<properties 
	pageTitle="Configurazione di una farm Intranet di SharePoint in un cloud ibrido per l'esecuzione di test" 
	description="Informazioni su come creare una farm Intranet di SharePoint in un ambiente cloud ibrido per lo sviluppo o il test IT professionale." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="josephd"/>


# Configurazione di una farm Intranet di SharePoint in un cloud ibrido per l'esecuzione di test

I passaggi in questo argomento illustrano la creazione di un ambiente cloud ibrido per il testing di una farm Intranet di SharePoint ospitata in Microsoft Azure. Di seguito è riportata la configurazione risultante.

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_3.png)
 
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

Se non si dispone di una sottoscrizione Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/). Se si dispone di un abbonamento MSDN, vedere [Benefici di Azure per sottoscrittori MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Fase 1: Configurare l'ambiente cloud ibrido

Usare le istruzioni riportate in [Configurazione di un ambiente cloud ibrido per l'esecuzione di test](virtual-networks-setup-hybrid-cloud-environment-testing.md) . Poiché l'ambiente di test non richiede la presenza del server APP1 nella subnet Corpnet, è possibile arrestarlo per il momento.

Questa è la configurazione corrente.

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_1.png)
 
## Fase 2: Configurare il computer SQL server (SQL1).

Dal portale di gestione di Azure, avviare il computer DC2, se necessario.

Creare innanzitutto una connessione desktop remoto a DC2 usando le credenziali CORP\User1.

Successivamente, creare un account di amministratore di farm SharePoint. Aprire un prompt di Windows PowerShell a livello di amministratore su DC2 ed eseguire questo comando.

	New-ADUser -SamAccountName SPFarmAdmin -AccountPassword (read-host "Set user password" -assecurestring) -name "SPFarmAdmin" -enabled $true -ChangePasswordAtLogon $false

Quando viene richiesto di fornire la password dell'account SPFarmAdmin, digitare una password complessa e annotarla in un luogo sicuro.

Successivamente, creare una macchina virtuale di Azure per SQL1 con questi comandi al prompt dei comandi Azure PowerShell nel computer locale.

	$storageacct="<Name of the storage account for your TestVNET virtual network>"
	Set-AzureStorageAccount -StorageAccountName $storageacct
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Standard on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$LocalAdminName="<A local administrator account name>" 
	$LocalAdminPW="<The password for the local administrator account>"
	$User1Password="<The password for the CORP\User1 account>"
	$vm1=New-AzureVMConfig -Name SQL1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $LocalAdminName -Password $LocalAdminPW -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $User1Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 100 -DiskLabel SQLFiles -LUN 0 -HostCaching None
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Successivamente, connettersi alla nuova macchina virtuale SQL1 *using the local administrator account*.

1.	Nel riquadro sinistro del portale di gestione di Azure fare clic su **Macchine virtuali**, quindi fare clic su **In esecuzione** nella colonna Stato per SQL1.
2.	Nella barra delle applicazioni fare clic su **Connetti**. 
3.	Quando viene richiesto di aprire SQL1.rdp, fare clic su **Apri**.
4.	Quando viene visualizzata una finestra di messaggio di Connessione Desktop remoto, fare clic su **Connetti**.
5.	Quando vengono richieste le credenziali, usare le seguenti:
	- Nome: **SQL1**[Nome dell'account amministratore locale]
	- Password: [Password dell'account amministratore locale]
6.	Quando viene visualizzata una finestra di messaggio di Connessione Desktop remoto che si riferisce ai certificati, fare clic su **Sì**.

Configurare le regole di Windows Firewall per consentire il traffico per il test di connettività di base e SQL Server. Da un prompt dei comandi di Windows PowerShell a livello di amministratore in SQL1 eseguire questi comandi.

	New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Il comando ping dovrebbe restituire quattro risposte dall'indirizzo IP 10.0.0.1.

Quindi, aggiungere il disco dati aggiuntivo come nuovo volume con la lettera di unità F:.

1.	Nel riquadro sinistro di Server Manager fare clic su **Servizi file e archiviazione**, quindi scegliere **Dischi**.
2.	Nel riquadro del contenuto nel gruppo **Dischi** fare clic su **disco 2** (con la **Partizione** impostata su **Sconosciuto**).
3.	Fare clic su **Attività**, quindi su **Nuovo volume**.
4.	Nella pagina Operazioni preliminari della creazione guidata nuovo volume fare clic su **Avanti**.
5.	Nella pagina Selezionare il server e il disco fare clic su **Disco 2**, quindi fare clic su **avanti**. Quando richiesto, fare clic su **OK**.
6.	Nella pagina Specifica dimensioni del volume fare clic su **Avanti**.
7.	Nella pagina Assegnare a una lettera di unità o cartella fare clic su **Avanti**.
8.	Nella pagina Selezionare le impostazioni del file system fare clic su **Avanti**.
9.	Nella pagina Conferma selezioni, fare clic su **Crea**.
10.	Al termine, fare clic su **Chiudi**.

Eseguire questi comandi al prompt dei comandi di Windows PowerShell in SQL1:

	md f:\Data
	md f:\Log
	md f:\Backup

Configurare quindi SQL Server 2014 in modo che usi l'unità F: per i nuovi database e per le autorizzazioni dell'account utente.

1.	Dalla schermata Start digitare **SQL Server Management**, quindi fare clic su **SQL Server 2014 Management Studio**.
2.	In **Connetti al server** fare clic su **Connetti**.
3.	Nel riquadro dell'albero Esplora oggetti fare clic con il pulsante destro del mouse su **SQL1**, quindi fare clic su **Proprietà**.
4.	Nella finestra **Proprietà server** fare clic su **Impostazioni database**.
5.	Individuare i **percorsi predefiniti del database** e impostare i valori seguenti: 
	- Per **Dati** digitare il percorso **f:\Data**.
	- Per **Log** digitare il percorso **f:\Log**.
	- Per **Backup** digitare il percorso **f:\Backup**.
	- Si noti che solo i nuovi database useranno questi percorsi.
6.	Fare clic su **OK** per chiudere la finestra.
7.	Nel riquadro dell'albero **Esplora oggetti** aprire **Sicurezza**.
8.	Fare clic con il pulsante destro del mouse su **Account di accesso**, quindi scegliere **Nuovo account accesso**.
9.	In **Nome account di accesso** digitare **CORP\User1**.
10.	Nella pagina **Ruoli server** fare clic su **sysadmin**, quindi su **OK**.
11.	Nel riquadro dell'albero di **Esplora oggetti**, fare doppio clic su **Account di accesso**, quindi fare clic su **Nuovo account di accesso**.
12.	Nella pagina **Generale** digitare **CORP\SPFarmAdmin** in **Nome account di accesso**.
13.	Nella pagina **Ruoli server** selezionare **dbcreator**, quindi fare clic su **OK**.
14.	Chiudere Microsoft SQL Server Management Studio.

Questa è la configurazione corrente.

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_2.png)

 
## Fase 3: Configurare il server di SharePoint (SP1)

Innanzitutto, creare una macchina virtuale di Azure per SP1 con questi comandi al prompt dei comandi Azure PowerShell nel computer locale.

	$image= Get-AzureVMImage | where { $_.Label -eq "SharePoint Server 2013 Trial" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$LocalAdminName="<A local administrator account name>" 
	$LocalAdminPW="<The password for the local administrator account>"
	$User1Password="<The password for the CORP\User1 account>"
	$vm1=New-AzureVMConfig -Name SP1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $LocalAdminName -Password $LocalAdminPW -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $User1Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Successivamente, connettersi alla macchina virtuale SP1 con le credenziali CORP\User1.

Configurare quindi una regola di Windows Firewall per permettere il traffico per il test della connettività di base. Da un prompt dei comandi Windows PowerShell a livello di amministratore in SP1, eseguire questi comandi.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Il comando ping dovrebbe restituire quattro risposte dall'indirizzo IP 10.0.0.1.

Configurare quindi SP1 per una nuova farm di SharePoint e un sito del team predefinito.

1.	Dalla schermata Start digitare **Prodotti SharePoint 2013**, quindi fare clic su **Configurazione guidata prodotti di SharePoint 2013**. Quando viene richiesto di consentire al programma di apportare modifiche al computer, fare clic su **Sì**.
2.	Nella pagina iniziale della pagina dei prodotti SharePoint, fare clic su **Avanti**. 
3.	Nella finestra di dialogo che informa che potrebbero essere necessario riavviare alcuni servizi durante la configurazione, fare clic su **Sì**.
4.	Nella pagina Connect to a server farm, fare clic su **Create a new server farm**, quindi fare clic su **Next**.
5.	Nella pagina Specify Configuration Database Settings digitare **sql1.corp.contoso.com** in **Database server**, digitare **CORP\SPFarmAdmin** in **User name**, digitare la password dell'account SPFarmAdmin in **Password**, quindi fare clic su **Next**.
6.	Nella pagina Specify Farm Security Settings, digitare **P@ssphrase** nei campi **Passphrase** e **Confirm passphrase**, quindi fare clic su **Next**.
7.	Nella pagina Configure SharePoint Central Administration Web Application fare clic su **Next**.
8.	Nella pagina the Completing the SharePoint Products Configuration Wizard fare clic su **Next**. La configurazione guidata dei prodotti SharePoint potrebbe richiedere alcuni minuti.
9.	Nella pagina Configuration Successful fare clic su **Finish**. Dopo il completamento, viene avviato Internet Explorer con una scheda denominata Initial Farm Configuration Wizard.
10.	Nella finestra di dialogo **Help Make SharePoint Better** fare clic su **No, I don't wish to participate**, quindi fare clic su **OK**.
11.	Per **How do you want to configure your SharePoint farm?**, fare clic su **Start the Wizard**.
12.	Nella pagina Configure your SharePoint farm, in **Service account**, fare clic su **Use existing managed account**.
13.	In **Services**, deselezionare tutte le caselle di controllo eccetto la casella accanto a **State Service**, quindi fare clic su **Next**. È possibile che venga visualizzata la pagina Working on it per alcuni secondi prima del completamento.
14.	Nella pagina Create Site Collection, in **Title and description**, digitare **Contoso Corporation** in **Title**, specificare l'URL **http://sp1**/, quindi fare clic su **OK**. È possibile che venga visualizzata la pagina Working on it per alcuni secondi prima del completamento. Questo passaggio crea un sito del team all'URL http://sp1.
15.	Nella pagina This completes the Farm Configuration Wizard, fare clic su **Finish**. Nella scheda di Internet Explorer viene visualizzato il sito SharePoint 2013 Central Administration.
16.	Accedere al computer CLIENT1 con le credenziali dell'account CORP\User1, quindi avviare Internet Explorer.
17.	Nella barra degli indirizzi digitare **http://sp1/** e quindi premere INVIO. Verrà visualizzato il sito del team di SharePoint per Contoso Corporation. Il rendering del sito potrebbe richiedere alcuni minuti.

Questa è la configurazione corrente.

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_3.png)
 
La farm Intranet di SharePoint in un ambiente cloud ibrido è ora pronta per il test.

## Risorse aggiuntive

[SharePoint nei servizi infrastruttura di Azure](http://msdn.microsoft.com/library/azure/dn275955.aspx)

[Farm di SharePoint Server](../virtual-machines/virtual-machines-sharepoint-farm-azure-preview.md)

[Configurazione di un ambiente cloud ibrido per l'esecuzione di test](virtual-networks-setup-hybrid-cloud-environment-testing.md)

[Configurazione di un'applicazione LOB basata sul Web in un cloud ibrido per l'esecuzione di test](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Configurazione della sincronizzazione della directory (DirSync) di Office 365 in un cloud ibrido per l'esecuzione di test](virtual-networks-setup-dirsync-hybrid-cloud-testing.md)

[Configurazione di un ambiente cloud ibrido simulato per l'esecuzione di test](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)
<!--HONumber=47-->
 