<properties 
	pageTitle="Ambienti di test farm SharePoint 2013 | Microsoft Azure" 
	description="Informazioni su come creare una farm Intranet di SharePoint Server 2013 a due livelli in un ambiente cloud ibrido per lo sviluppo o il test IT professionale." 
	services="virtual-machines-windows" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/01/2016" 
	ms.author="josephd"/>

# Configurazione di una farm Intranet di SharePoint in un cloud ibrido per l'esecuzione di test

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.

I passaggi in questo argomento illustrano la creazione di un ambiente cloud ibrido per il testing di una farm Intranet di SharePoint ospitata in Microsoft Azure. Di seguito è riportata la configurazione risultante.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sp/virtual-machines-windows-ps-hybrid-cloud-test-env-sp-ph3.png)
 
Questa configurazione simula un ambiente di produzione di SharePoint in Azure dal percorso su Internet. È costituita da:

- Una rete locale semplificata (la subnet Corpnet).
- Una rete virtuale cross-premise ospitata in Microsoft Azure (TestVNET).
- Una connessione VPN da sito a sito.
- Una farm di SharePoint a due livelli e un controller di dominio secondario nella rete virtuale TestVNET.

Questa configurazione rappresenta una base e un punto di partenza comune da cui è possibile:

- Sviluppare e testare applicazioni in una farm Intranet di SharePoint in un ambiente cloud ibrido.
- Eseguire il test di questo carico di lavoro IT basato sul cloud ibrido.

La configurazione di questo ambiente di test cloud ibrido prevede tre fasi principali:

1.	Configurare l'ambiente cloud ibrido per l'esecuzione di test.
2.	Configurare il computer SQL server (SQL1).
3.	Configurare il server di SharePoint (SP1).

Questo carico di lavoro richiede una sottoscrizione di Azure. Se si ha un abbonamento a MSDN o una sottoscrizione di Visual Studio, vedere [Credito Azure mensile per sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Fase 1: Impostare l'ambiente cloud ibrido

Utilizzare le istruzioni nell’argomento [Configurare l’ambiente cloud ibrido per il test](virtual-machines-windows-ps-hybrid-cloud-test-env-base.md). Poiché l'ambiente di test non richiede la presenza del server APP1 nella subnet Corpnet, è possibile arrestarlo per il momento.

Questa è la configurazione corrente.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sp/virtual-machines-windows-ps-hybrid-cloud-test-env-sp-ph1.png)

> [AZURE.NOTE] Per la fase 1, è possibile configurare anche l'[ambiente di test del cloud ibrido simulato](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md).
 
## Fase 2: Configurare il computer SQL server (SQL1)

Dal portale di Azure avviare il computer DC2, se necessario.

Dal portale di Azure connettersi a DC2 usando le credenziali CORP\\User1.

Successivamente, creare un account di amministratore di farm SharePoint. Aprire un prompt di Windows PowerShell a livello di amministratore su DC2 ed eseguire questo comando.

	New-ADUser -SamAccountName SPFarmAdmin -AccountPassword (read-host "Set user password" -assecurestring) -name "SPFarmAdmin" -enabled $true -ChangePasswordAtLogon $false

Quando viene richiesto di fornire la password dell'account SPFarmAdmin, digitare una password complessa e annotarla in un luogo sicuro.

Successivamente, creare una macchina virtuale di Azure per SQL1 con questi comandi al prompt dei comandi Azure PowerShell nel computer locale. Per usare questi comandi, inserire i valori delle variabili e rimuovere i caratteri < and >.

	$rgName="<your resource group name>"
	$locName="<the Azure location of your resource group>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
	$vm=New-AzureRMVMConfig -VMName SQL1 -VMSize Standard_A4
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-SQLDataDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name "Data" -DiskSizeInGB 100 -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the SQL Server computer." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName SQL1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Standard -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Al termine, usare il portale di Azure per connettersi a SQL1 con l'account amministratore locale.

Configurare le regole di Windows Firewall per consentire il traffico per il test di connettività di base e SQL Server. Da un prompt dei comandi di Windows PowerShell a livello di amministratore in SQL1 eseguire questi comandi.

	New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc2.corp.contoso.com

Il comando ping restituirà quattro risposte dall'indirizzo IP 192.168.0.4.

Quindi, aggiungere il disco dati aggiuntivo come nuovo volume con la lettera di unità F:.

1.	Nel riquadro sinistro di Server Manager fare clic su **Servizi file e archiviazione**, quindi scegliere **Dischi**.
2.	Nel riquadro del contenuto nel gruppo **Dischi** fare clic su **disco 2** (con la **Partizione** impostata su **Sconosciuto**).
3.	Fare clic su **Attività**, quindi su **Nuovo volume**.
4.	Nella pagina Operazioni preliminari della creazione guidata nuovo volume fare clic su **Avanti**.
5.	Nella pagina Selezionare il server e il disco fare clic su **Disco 2**, quindi fare clic su **Avanti**. Quando richiesto, fare clic su **OK**.
6.	Nella pagina Specifica dimensioni del volume fare clic su **Avanti**.
7.	Nella pagina Assegnare a una lettera di unità o cartella fare clic su **Avanti**.
8.	Nella pagina Selezionare le impostazioni del file system fare clic su **Avanti**.
9.	Nella pagina Conferma selezioni, fare clic su **Crea**.
10.	Al termine, fare clic su **Chiudi**.

Eseguire questi comandi al prompt dei comandi di Windows PowerShell in SQL1:

	md f:\Data
	md f:\Log
	md f:\Backup

Aggiungere SQL1 al dominio CORP di Active Directory con questi comandi al prompt di Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Usare l'account CORP\\User1 quando viene richiesto di specificare le credenziali dell'account di dominio per il comando **Add-Computer**.

Dopo il riavvio, usare il portale di Azure per connettersi a SQL1 *con l'account amministratore locale*.

Configurare quindi SQL Server 2014 in modo che usi l'unità F: per i nuovi database e per le autorizzazioni dell'account utente.

1.	Dalla schermata Start digitare **SQL Server Management**, quindi fare clic su **SQL Server 2014 Management Studio**.
2.	In **Connetti al Server**, fare clic su **Connetti**.
3.	Nel riquadro dell'albero Esplora oggetti, fare doppio clic su **SQL1**, quindi fare clic su **Proprietà**.
4.	Nella finestra **Proprietà server**, fare clic su **Impostazioni database**.
5.	Individuare **Percorsi predefiniti del Database** e impostare i valori seguenti: 
	- Per **Dati**, digitare il percorso **f:\\Data**.
	- Per **Log**, digitare il percorso **f:\\Log**.
	- Per **Backup**, digitare il percorso **f:\\Backup**.
	- Si noti che solo i nuovi database useranno questi percorsi.
6.	Fare clic su **OK** per chiudere la finestra.
7.	Nel riquadro dell'albero **Esplora oggetti** aprire **Sicurezza**.
8.	Fare clic con il pulsante destro del mouse su **Account di accesso**, quindi scegliere **Nuovo account di accesso**.
9.	In **Nome account di accesso**, digitare **CORP\\User1**.
10.	Nella pagina **Ruoli Server** fare clic su **sysadmin**, quindi su **OK**.
11.	Nel riquadro dell’albero **Esplora oggetti**, fare clic con il pulsante destro del mouse su **Account di accesso**, quindi fare clic su **Nuovo account di accesso**.
12.	Nella pagina **Generale**, in **Nome account di accesso**, digitare **CORP\\SPFarmAdmin**.
13.	Nella pagina **Ruoli server** selezionare **dbcreator**, quindi fare clic su **OK**.
14.	Chiudere Microsoft SQL Server Management Studio.

Questa è la configurazione corrente.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sp/virtual-machines-windows-ps-hybrid-cloud-test-env-sp-ph2.png)

## Fase 3: Configurare il server di SharePoint (SP1)

Innanzitutto, creare una macchina virtuale di Azure per SP1 con questi comandi al prompt dei comandi Azure PowerShell nel computer locale.

	$rgName="<your resource group name>"
	$locName="<the Azure location of your resource group>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name SP1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name SP1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
	$vm=New-AzureRMVMConfig -VMName SP1 -VMSize Standard_A3
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the SharePoint 2013 server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName SP1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SP1-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Usare quindi il portale di Azure per connettersi alla macchina virtuale SP1 con le credenziali dell'account amministratore locale.

Configurare quindi una regola di Windows Firewall per permettere il traffico per il test della connettività di base. Al prompt dei comandi di Windows PowerShell in SP1 eseguire questi comandi:

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc2.corp.contoso.com

Il comando ping restituirà quattro risposte dall'indirizzo IP 192.168.0.4.

Aggiungere SP1 al dominio CORP di Active Directory con questi comandi al prompt di Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Usare l'account CORP\\User1 quando viene richiesto di specificare le credenziali dell'account di dominio per il comando **Add-Computer**.

Dopo il riavvio, usare il portale di Azure per connettersi a SP1 con l'account CORP\\User1 e la password.

Configurare quindi SP1 per una nuova farm di SharePoint e un sito del team predefinito.

1.	Dalla schermata Start digitare **Prodotti SharePoint 2013**, quindi fare clic su **Configurazione guidata prodotti di SharePoint 2013**. Quando viene richiesto di consentire al programma di apportare modifiche al computer, fare clic su **Sì**.
2.	Nella pagina Prodotti SharePoint, fare clic su**Avanti**. 
3.	Nella finestra di dialogo che informa che potrebbe essere necessario riavviare alcuni servizi durante la configurazione, fare clic su **Sì**.
4.	Nella pagina Connetti a una server farm, fare clic su **Creare una nuova server farm**, quindi fare clic su **Avanti**.
5.	Nella pagina Specifica le impostazioni del database di configurazione, digitare **sql1.corp.contoso.com** in **Server del database**, digitare **CORP\\SPFarmAdmin** in **Nome utente**, digitare la password dell'account SPFarmAdmin in **Password**, quindi fare clic su **Avanti**.
6.	Nella pagina Specifica impostazioni sicurezza Farm digitare ****P@ssphrase** sia in **Passphrase** che **Conferma passphrase**, quindi fare clic su **Avanti**.
7.	Nella pagina Configurazione applicazione Web Amministrazione centrale SharePoint, fare clic su **Avanti**.
8.	Nella pagina Completamento della configurazione guidata ai prodotti SharePoint fare clic su **Avanti**. La configurazione guidata dei prodotti SharePoint potrebbe richiedere alcuni minuti.
9.	Nella pagina Configurazione completata, fare clic su **Fine**. Dopo il completamento, viene avviato Internet Explorer con una scheda denominata Initial Farm Configuration Wizard.
10.	Nella finestra di dialogo **Aiuta a migliorare SharePoint** fare clic su **No, non voglio partecipare**, quindi fare clic su **OK**.
11.	Per **Modalità con cui si desidera configurare la farm di SharePoint**, fare clic su **Avvia la procedura guidata**.
12.	Nella pagina Configura SharePoint farm in **Account del servizio**, fare clic su **Usa account gestito esistente**.
13.	In **Servizi**, deselezionare le caselle di controllo eccetto la casella accanto a **Stato servizio**, quindi fare clic su **Avanti**. È possibile che venga visualizzata la pagina Working on it per alcuni secondi prima del completamento.
14.	Nella pagina Crea raccolta siti in **Titolo e descrizione**, digita **Contoso Corporation** in **Titolo**, specificare l'URL **http://sp1**/, quindi fare clic su **OK**. È possibile che venga visualizzata la pagina Working on it per alcuni secondi prima del completamento. Questo passaggio crea un sito del team all'URL http://sp1.
15.	Nella pagina Completamento della configurazione guidata della farm, fare clic su **Termina**. Nella scheda di Internet Explorer viene visualizzato il sito SharePoint 2013 Central Administration.
16.	Accedere al computer CLIENT1 con le credenziali dell'account CORP\\User1, quindi avviare Internet Explorer.
17.	Nella barra degli Indirizzi digitare **http://sp1/** e quindi premere INVIO. Verrà visualizzato il sito del team di SharePoint per Contoso Corporation. Il rendering del sito potrebbe richiedere alcuni minuti.

Questa è la configurazione corrente.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sp/virtual-machines-windows-ps-hybrid-cloud-test-env-sp-ph3.png)
 
La farm Intranet di SharePoint in un ambiente cloud ibrido è ora pronta per il test.

## Passaggio successivo

- [Configurare](https://technet.microsoft.com/library/ee836142.aspx) la farm di SharePoint.

<!---HONumber=AcomDC_0518_2016-->