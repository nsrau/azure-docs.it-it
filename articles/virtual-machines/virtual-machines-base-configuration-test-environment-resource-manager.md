<properties
	pageTitle="Ambiente di Test di Configurazione di Base con Gestione risorse di Azure"
	description="Informazioni su come creare un ambiente di sviluppo e test semplice che simuli una Intranet semplificata in Microsoft Azure utilizzando Gestione Risorse."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/23/2015"
	ms.author="josephd"/>

# Ambiente di test di configurazione di base con Gestione risorse di Azure

In questo articolo vengono fornite le istruzioni dettagliate per creare l’ambiente di test della configurazione di base in una rete virtuale di Microsoft Azure, usando macchine virtuali create in Gestione Risorse.

È possibile utilizzare l'ambiente di test risultante:

- Per lo sviluppo e il testing di applicazioni.
- Come la configurazione iniziale di un ambiente di testing esteso di propria progettazione che include ulteriori macchine virtuali e servizi di Azure.

L'ambiente di test della configurazione di base è costituito dalla subnet Corpnet in una rete virtuale solo cloud denominata TestLab che simula una intranet semplificata, privata e connessa a Internet.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG04.png)

Contiene quanto segue:

- Una macchina virtuale di Azure su cui è in esecuzione Windows Server 2012 R2 denominato DC1, configurato come controller di dominio intranet e server DNS (Domain Name System).
- Una macchina virtuale di Azure su cui è in esecuzione Windows Server 2012 R2 denominata APP1, configurata come applicazione generica e server Web.
- Una macchina virtuale di Azure su cui è in esecuzione Windows Server 2012 R2 denominato CLIENT1 che verrà utilizzato come client intranet.

Questa configurazione consente a DC1, APP1, CLIENT1 e altri computer della subnet Corpnet di effettuarsi quanto segue:

- Connettersi a Internet per installare gli aggiornamenti, accedere alle risorse Internet in tempo reale e partecipare a tecnologie di cloud pubblico, ad esempio Microsoft Office 365 e altri servizi di Azure.
- Gestione remota mediante connessioni Desktop remoto dal computer connesso a Internet o alla rete dell'organizzazione.

Esistono quattro fasi per l'impostazione dell'ambiente di test della configurazione di base di Windows Server 2012 R2 in Azure.

1.	Creare la rete virtuale.
2.	Configurare DC1.
3.	Configurare APP1.
4.	Configurare CLIENT1.

Se non si dispone di una sottoscrizione Azure, è possibile effettuare l'iscrizione per ottenere una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/). Se si dispone di un abbonamento MSDN, vedere [Benefici di Azure per gli abbonati MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

> [AZURE.NOTE]Macchine virtuali in Azure comportano un costo monetario quando sono in esecuzione. Il costo viene addebitato sulla base della versione di valutazione gratuita, dell'abbonamento MSDN o della sottoscrizione a pagamento. Per ulteriori informazioni sui costi dell'esecuzione di macchine virtuali di Azure, vedere i [dettagli relativi ai prezzi delle macchine virtuali](http://azure.microsoft.com/pricing/details/virtual-machines/) e il [Calcolatore dei costi Azure](http://azure.microsoft.com/pricing/calculator/). Per contenere i costi, vedere [Riduzione dei costi di macchine virtuali in ambiente di test in Azure](#costs).

[AZURE.INCLUDE [resource-manager-pointer-to-service-management](../../includes/resource-manager-pointer-to-service-management.md)]

- [Ambiente di test di configurazione di base](virtual-machines-base-configuration-test-environment.md)


## Fase 1: creare la rete virtuale

Innanzitutto, se necessario, usare le istruzioni disponibili in [Come installare e configurare Azure PowerShell](../install-configure-powershell.md) per installare Azure PowerShell nel computer locale. Aprire un prompt dei comandi di Azure PowerShell.

Selezionare la sottoscrizione di Azure corretta con questi comandi. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con il nome corretto.

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

È possibile ottenere il nome della sottoscrizione corretto dalla proprietà SubscriptionName dell'output del comando **Get-AzureSubscription**.

Successivamente, passare ad Azure PowerShell in modalità Gestione risorse.

	Switch-AzureMode AzureResourceManager 

Successivamente, creare un nuovo gruppo di risorse per il testing della configurazione di Base. Per determinare un nome di gruppo di risorse univoco, usare il comando seguente per elencare i gruppi di risorse esistenti.

	Get-AzureResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Per ottenere un elenco dei percorsi di Azure in cui è possibile creare macchine virtuali basate su Gestione risorse, usare i comandi seguenti.

	$loc=Get-AzureLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

Creare il nuovo gruppo di risorse con questi comandi. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con i nomi corretti.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureResourceGroup -Name $rgName -Location $locName

Le macchine virtuali basate su Gestione risorse richiedono un account di archiviazione basato su Gestione risorse. Per l'account di archiviazione è necessario selezionare un nome univoco globale che contenga solo lettere minuscole e numeri. È possibile usare il comando seguente per elencare gli account di archiviazione esistenti.

	Get-AzureStorageAccount | Sort Name | Select Name

Per verificare che quello prescelto sia un nome univoco globale, è necessario eseguire il comando **Test-AzureName** nella modalità di gestione del servizio Azure di PowerShell. Usare i comandi seguenti.

	Switch-AzureMode AzureServiceManagement
	Test-AzureName -Storage <Proposed storage account name>

Se il comando Test-AzureName visualizza **False**, il nome proposto è univoco. Dopo aver stabilito un nome univoco, tornare alla modalità Gestione risorse di Azure PowerShell usando il comando seguente.

	Switch-AzureMode AzureResourceManager 

Creare un nuovo account di archiviazione per il nuovo ambiente di test usando i comandi seguenti.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<storage account name>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

Successivamente, creare innanzitutto la rete virtuale di Azure TestLab che ospiterà la subnet Corpnet della configurazione di base.

	$rgName="<name of your new resource group>"
	$locName="<Azure location name, such as West US>"
	$corpnetSubnet=New-AzureVirtualNetworkSubnetConfig -Name Corpnet -AddressPrefix 10.0.0.0/24
	New-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/8 -Subnet $corpnetSubnet –DNSServer 10.0.0.4

Questa è la configurazione corrente.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG01.png)

## Fase 2: configurare DC1.

DC1 è un controller di dominio per il dominio Servizi di dominio Active Directory corp.contoso.com e un server DNS per le macchine virtuali della rete virtuale TestLab.

Innanzitutto, specificare il nome del gruppo di risorse, il percorso Azure e il nome account di archiviazione e utilizzare questi comandi al prompt dei comandi di Azure PowerShell nel computer locale per creare una macchina virtuale di Azure per DC1.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzurePublicIpAddress -Name DC1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name DC1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 10.0.0.4
	$vm=New-AzureVMConfig -VMName DC1 -VMSize Standard_A1
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC1-TestLab-ADDSDisk.vhd"
	Add-AzureVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC1." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName DC1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC1-TestLab-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name DC1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

Successivamente, connettersi alla macchina virtuale DC1.

1.	Nel portale di anteprima di Azure, fare clic su**Sfoglia tutto**nel riquadro sinistro, fare clic su**Macchine virtuali**nell’elenco**Sfoglia**e quindi scegliere la macchina virtuale**DC1**.  
2.	Nel riquadro**DC1**, fare clic su**Connetti**.
3.	Quando richiesto, aprire il file DC1.rdp scaricato.
4.	Quando viene visualizzata una finestra di messaggio di Connessione Desktop remoto, fare clic su **Connetti**.
5.	Alla richiesta di credenziali, usare le seguenti:
- Nome: **DC1\** [Local administrator account name]
- Password: [Nome dell'account amministratore locale]
6.	Quando viene visualizzata una finestra di messaggio di Connessione Desktop remoto che si riferisce ai certificati, fare clic su **Sì**.

Quindi, aggiungere un altro disco dati come nuovo volume con la lettera di unità F:.

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

Configurare quindi DC1 come controller di dominio e server DNS per il dominio corp.contoso.com. Eseguire questi comandi in un prompt dei comandi di Windows PowerShell a livello di amministratore.

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSForest -DomainName corp.contoso.com -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Dopo il riavvio di DC1, riconnettersi alla macchina virtuale DC1.

1.	Nel portale di anteprima di Azure, fare clic su Sfoglia tutto nel riquadro sinistro, fare clic su Macchine virtuali nell’elenco Sfoglia elenco e quindi scegliere la macchina virtuale DC1.
2.	Nel riquadro DC1, fare clic su Connetti.
3.	Quando viene richiesto di aprire DC1.rdp, fare clic su **Apri**.
4.	Quando viene visualizzata una finestra di messaggio di Connessione Desktop remoto, fare clic su **Connetti**.
5.	Alla richiesta di credenziali, usare le seguenti:
- Nome:**CORP \**[Local administrator account name]
- Password: [Nome dell'account amministratore locale]
6.	Quando viene visualizzata una finestra di messaggio di Connessione Desktop remoto che si riferisce ai certificati, fare clic su **Sì**.

Successivamente, creare un account utente in Active Directory che verrà utilizzato quando si accede a computer membri del dominio CORP. In DC1 eseguire questi comandi in un prompt dei comandi di Windows PowerShell a livello di amministratore.

	New-ADUser -SamAccountName User1 -AccountPassword (read-host "Set user password" -assecurestring) -name "User1" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false
	Add-ADPrincipalGroupMembership -Identity "CN=User1,CN=Users,DC=corp,DC=contoso,DC=com" -MemberOf "CN=Enterprise Admins,CN=Users,DC=corp,DC=contoso,DC=com","CN=Domain Admins,CN=Users,DC=corp,DC=contoso,DC=com"

Si noti che non appena viene eseguito il comando, viene visualizzato una finestra in cui viene chiesto di immettere la password dell’account User1. Poiché questo account verrà utilizzato per le connessioni desktop remote per tutti i computer appartenenti al dominio CORP, scegliere una password complessa. Per verificarne il livello di complessità, vedere [Controllo password: utilizzo di password complesse](https://www.microsoft.com/security/pc-security/password-checker.aspx). Registrare la password dell'account User1 e archiviarlo in una posizione protetta.

Chiudere la sessione di Desktop remoto con DC1 e quindi riconnettersi utilizzando l'account CORP\\User1.

Successivamente, per consentire il traffico per lo strumento Ping, eseguire questo comando in un prompt dei comandi di Windows PowerShell a livello di amministratore.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True

Questa è la configurazione corrente.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG02.png)

## Fase 3: configurare APP1

APP1 fornisce servizi di condivisione file e Web.

Innanzitutto, specificare il nome del gruppo di risorse, il percorso Azure e il nome account di archiviazione e utilizzare questi comandi al prompt dei comandi di Azure PowerShell nel computer locale per creare una macchina virtuale di Azure per APP1.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzurePublicIpAddress -Name APP1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name APP1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$vm=New-AzureVMConfig -VMName APP1 -VMSize Standard_A1
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for APP1." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName APP1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/APP1-TestLab-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name APP1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

Successivamente, connettersi alla macchina virtuale APP1 utilizzando le credenziali di amministratore locale APP1 e aprire un prompt dei comandi di Windows PowerShell a livello di amministratore.

Per controllare la comunicazione di rete e di risoluzione dei nomi tra APP1 e DC1, eseguire il comando **ping dc1.corp.contoso.com** per verificare che vengano restituite quattro risposte.

Aggiungere la macchina virtuale APP1 al dominio CORP con questi comandi al prompt di Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Si noti che è necessario fornire le credenziali dell'account di dominio CORP\\User1 dopo aver immesso il comando Add-Computer.

Dopo il riavvio di APP1, connettersi utilizzando il nome dell'account CORP\\User1 e la password e quindi aprire un prompt dei comandi di Windows PowerShell a livello di amministratore.

Quindi, impostare APP1 come server Web con questo comando al prompt dei comandi di Windows PowerShell.

	Install-WindowsFeature Web-WebServer –IncludeManagementTools

Successivamente, creare una cartella condivisa e un file di testo all'interno della cartella in APP1 con questi comandi.

	New-Item -path c:\files -type directory
	Write-Output "This is a shared file." | out-file c:\files\example.txt
	New-SmbShare -name files -path c:\files -changeaccess CORP\User1

Questa è la configurazione corrente.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG03.png)

## Fase 4: configurare CLIENT1.

CLIENT1 agisce come un tipico, Tablet PC, computer desktop o portatile nella intranet di Contoso.

Innanzitutto, specificare il nome del gruppo di risorse, il percorso Azure e il nome account di archiviazione e utilizzare questi comandi al prompt dei comandi di Azure PowerShell nel computer locale per creare una macchina virtuale di Azure per CLIENT1.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzurePublicIpAddress -Name CLIENT1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name CLIENT1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$vm=New-AzureVMConfig -VMName CLIENT1 -VMSize Standard_A1
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for CLIENT1." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName CLIENT1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id	
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/CLIENT1-TestLab-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name CLIENT1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

Successivamente, connettersi alla macchina virtuale CLIENT1 utilizzando le credenziali di amministratore locale CLIENT1 e aprire un prompt dei comandi di Windows PowerShell a livello di amministratore.

Per controllare la comunicazione di rete e di risoluzione dei nomi tra CLIENT1 e DC1, eseguire il comando **ping dc1.corp.contoso.com** in un prompt dei comandi Windows PowerShell per verificare che vengano restituite quattro risposte.

Aggiungere la macchina virtuale CLIENT1 al dominio CORP con questi comandi al prompt di Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Si noti che è necessario fornire le credenziali dell'account di dominio CORP\\User1 dopo aver immesso il comando Add-Computer.

Dopo il riavvio di CLIENT1, connettersi utilizzando il nome dell'account CORP\\User1 e la password e quindi aprire un prompt dei comandi di Windows PowerShell a livello di amministratore.

Successivamente, verificare che sia possibile accedere al Web e alle risorse di condivisione file in APP1 da CLIENT1.

1.	In Server Manager, nel riquadro dell'albero, fare clic su **Server locale**.
2.	In **Proprietà per CLIENT1**, fare clic su **On** accanto a ** Configurazione sicurezza avanzata IE**.
3.	In ** Configurazione sicurezza avanzata IE**, fare clic su **Off** relativamente ad **Amministratori** e **Utenti**, quindi fare clic su **OK**.
4.	Dalla schermata Start, fare clic su **Internet Explorer**, quindi su **OK**.
5.	Nella barra degli indirizzi digitare ****http://app1.corp.contoso.com/**, quindi premere INVIO. Dovrebbe essere visualizzata la pagina Web di Internet Information Services predefinita per APP1.
6.	Sulla barra delle applicazioni desktop, fare clic sull'icona Esplora File.
7.	Nella barra degli indirizzi digitare **\\\\app1\\Files**, quindi premere INVIO.
8.	Dovrebbe essere visualizzata una finestra della cartella con il contenuto della cartella condivisa File.
9.	Nella finestra della cartella condivisa **File**, fare doppio clic sul file **Example.txt**. Viene visualizzato il contenuto del file di Example.txt.
10.	Chiudere il file **Example.txt in Blocco note** e le finestre della cartella condivisa **File**.

Questa sarà la configurazione finale.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG04.png)

La configurazione di base in Azure è ora pronta per lo sviluppo di applicazioni e il test o per ambienti di test aggiuntivi.

## Risorse aggiuntive

[Ambienti di test basati su cloud ibrido](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md)

[Ambiente di test di configurazione di base](virtual-machines-base-configuration-test-environment.md)


## <a id="costs"></a>Riduzione dei costi di macchine virtuali in ambiente di test in Azure

Per ridurre al minimo il costo di esecuzione delle macchine virtuali nell’ambiente di test, è possibile effettuare una delle seguenti operazioni:

- Creare l'ambiente di test ed eseguire il test e la dimostrazione necessari più rapidamente possibile. Al termine, eliminare le macchine virtuali dell’ambiente di test.
- Arrestare le macchine virtuali dell’ambiente di test in stato deallocato.

Per arrestare le macchine virtuali con Azure PowerShell, inserire il nome del gruppo di risorse ed eseguire questi comandi.

	$rgName="<your resource group name>"
	Stop-AzureVM -ResourceGroupName $rgName -Name "CLIENT1"
	Stop-AzureVM -ResourceGroupName $rgName -Name "APP1"
	Stop-AzureVM -ResourceGroupName $rgName -Name "DC1" -Force -StayProvisioned

Per assicurarsi che le macchine virtuali funzionino correttamente per l'avvio di tutti gli elementi dallo stato di arresto (deallocazione), è necessario avviarli nell'ordine seguente:

1.	DC1
2.	APP1
3.	CLIENT1

Per avviare le macchine virtuali in ordine con Azure PowerShell, inserire il nome del gruppo di risorse ed eseguire questi comandi.

	$rgName="<your resource group name>"
	Start-AzureVM -ResourceGroupName $rgName -Name "DC1"
	Start-AzureVM -ResourceGroupName $rgName -Name "APP1"
	Start-AzureVM -ResourceGroupName $rgName -Name "CLIENT1"

<!---HONumber=August15_HO6-->