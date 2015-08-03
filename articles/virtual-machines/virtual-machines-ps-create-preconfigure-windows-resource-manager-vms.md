<properties
	pageTitle="Creare e preconfigurare una macchina virtuale Windows con Gestione risorse e Azure PowerShell"
	description="Informazioni su come usare Azure PowerShell per creare e preconfigurare macchine virtuali basate su Windows e Gestione risorse in Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="kathydav"/>

# Creare e preconfigurare una macchina virtuale Windows con Gestione risorse e Azure PowerShell

Questi passaggi mostrano come costruire un set di comandi nella modalità Gestione risorse di Azure PowerShell per la creazione e la preconfigurazione di una macchina virtuale di Azure basata su Windows. È possibile usare questo processo modulare per creare rapidamente un set di comandi per una nuova macchina virtuale basata su Windows ed espandere una distribuzione esistente. Può essere inoltre usato per creare più set di comandi che consentono di realizzare rapidamente un ambiente di sviluppo/test personalizzato o un ambiente IT professionale.

Questi passaggi seguono un approccio basato sul completamento di valori predefiniti per la creazione di set di comandi di Azure PowerShell. Questo approccio può essere utile se non si è esperti di PowerShell o per sapere semplicemente quali valori specificare per una corretta configurazione. Gli utenti esperti di PowerShell possono usare i comandi sostituendo le variabili (le righe che iniziano con "$") con i propri valori.

[AZURE.INCLUDE [resource-manager-pointer-to-service-management](../../includes/resource-manager-pointer-to-service-management.md)]

- [Uso di Azure PowerShell per creare e preconfigurare macchine virtuali basate su Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)

## Passaggio 1: Installare Azure PowerShell

È anche necessario disporre di Azure PowerShell 0.9.0 o versione successiva. Se Azure PowerShell non è stato installato e configurato, fare clic [qui](powershell-install-configure.md) per le istruzioni.

Per verificare la versione di Azure PowerShell installata, usare il comando seguente al prompt di Azure PowerShell.

	Get-Module azure | format-table version

Di seguito è fornito un esempio.

	Version
	-------
	0.9.0

Se non si dispone della versione 0.9.0 o di una versione successiva, è necessario rimuovere Azure PowerShell tramite Programmi e funzionalità del Pannello di controllo e quindi installare la versione più recente. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md).

## Passaggio 2: Impostare la sottoscrizione

In primo luogo, eseguire un prompt di Azure PowerShell.

Impostare quindi la sottoscrizione di Azure eseguendo questi comandi al prompt di Azure PowerShell. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con i nomi corretti.

	$subscr="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

È possibile ottenere il nome della sottoscrizione corretto dalla visualizzazione del comando.

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

Successivamente, passare ad Azure PowerShell in modalità Gestione risorse.

	Switch-AzureMode AzureResourceManager

## Passaggio 3: Creare le risorse necessarie

Le macchine virtuali basate su Gestione risorse richiedono un gruppo di risorse. Se necessario, creare un nuovo gruppo di risorse per la nuova macchina virtuale con i comandi seguenti. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con i nomi corretti.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureResourceGroup -Name $rgName -Location $locName

Per determinare un nome di gruppo di risorse univoco, usare il comando seguente per elencare i gruppi di risorse esistenti.

	Get-AzureResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Per ottenere un elenco dei percorsi di Azure in cui è possibile creare macchine virtuali basate su Gestione risorse, usare i comandi seguenti.

	$loc=Get-AzureLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

Le macchine virtuali basate su Gestione risorse richiedono un account di archiviazione basato su Gestione risorse. Se necessario, creare un nuovo account di archiviazione per la nuova macchina virtuale usando i comandi seguenti.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	$saName="<storage account name>"
	$saType="<storage account type, specify one: Standard_LRS, Standard_GRS, Standard_RAGRS, or Premium_LRS>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

Per l'account di archiviazione è necessario selezionare un nome univoco globale che contenga solo lettere minuscole e numeri. È possibile usare il comando seguente per elencare gli account di archiviazione esistenti.

	Get-AzureStorageAccount | Sort Name | Select Name

Per verificare che quello prescelto sia un nome univoco globale, è necessario eseguire il comando **Test-AzureName** nella modalità di gestione del servizio Azure di PowerShell. Usare i comandi seguenti.

	Switch-AzureMode AzureServiceManagement
	Test-AzureName -Storage <Proposed storage account name>

Se il comando Test-AzureName visualizza "False", il nome proposto è univoco. Dopo aver stabilito un nome univoco, tornare alla modalità Gestione risorse di Azure PowerShell usando il comando seguente.

	Switch-AzureMode AzureResourceManager

Le macchine virtuali basate su Gestione risorse possono usare un'etichetta di nome di dominio pubblico che può contenere solo lettere, numeri e trattini. Il primo e l'ultimo carattere nel campo devono essere una lettera o un numero.

Per verificare che l'etichetta di nome di dominio pubblico sia un'etichetta univoca globale, usare questi comandi.

	$domName="<domain name label to test>"
	$loc="<short name of an Azure location, for example, for West US, the short name is westus>"
	Get-AzureCheckDnsAvailability -DomainQualifiedName $domName -Location $loc

Se DNSNameAvailability è "True", quello proposto è un nome univoco globale.

Le macchine virtuali basate su Gestione risorse possono trovarsi in un set di disponibilità basato su Gestione risorse. Se necessario, creare un nuovo set di disponibilità per la nuova macchina virtuale usando i comandi seguenti.

	$avName="<availability set name>"
	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

Usare il comando seguente per elencare i set di disponibilità esistenti.

	Get-AzureAvailabilitySet –ResourceGroupName $rgName | Sort Name | Select Name

Le macchine virtuali basate su Gestione risorse richiedono una rete virtuale basata su Gestione risorse. Se necessario, creare una nuova rete virtuale basata su Gestione risorse con almeno una subnet per la nuova macchina virtuale. Di seguito è riportato un esempio di una nuova rete virtuale con due subnet denominati frontendSubnet e backendSubnet.

	$rgName="LOBServers"
	$locName="West US"
	$frontendSubnet=New-AzureVirtualNetworkSubnetConfig -Name frontendSubnet -AddressPrefix 10.0.1.0/24
	$backendSubnet=New-AzureVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix 10.0.2.0/24
	New-AzurevirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $frontendSubnet,$backendSubnet

Usare i comandi seguenti per elencare le reti virtuali esistenti.

	$rgName="<resource group name>"
	Get-AzureVirtualNetwork -ResourceGroupName $rgName | Sort Name | Select Name

## Passaggio 4: Compilare il set di comandi

Aprire una nuova istanza dell'editor di testo preferito o l'ambiente PowerShell Integrated Scripting Environment (ISE) e copiare le linee seguenti per avviare il set di comandi. Specificare il nome del gruppo di risorse, il percorso di Azure e l'account di archiviazione relativi alla nuova macchina virtuale. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con i nomi corretti.

	Switch-AzureMode AzureResourceManager
	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"

È necessario specificare il nome di una rete virtuale basata su Gestione risorse e il numero dell'indice di una subnet nella rete virtuale. Usare i comandi seguenti per elencare le subnet relative a una rete virtuale.

	$rgName="<resource group name>"
	$vnetName="<virtual network name>"
	Get-AzureVirtualNetwork -Name $vnetName -ResourceGroupName $rgName | Select Subnets

L'indice di subnet è il numero della subnet nella visualizzazione di questo comando, considerando una numerazione progressiva da sinistra a destra a partire da 0.

Per questo esempio:

	PS C:\> Get-AzureVirtualNetwork -Name TestNet -ResourceGroupName LOBServers | Select Subnets

	Subnets
	-------
	{frontendSubnet, backendSubnet}

L'indice di subnet per frontendSubnet è 0 e l'indice di subnet per backendSubnet è 1.

Copiare le righe seguenti nel set di comandi e specificare un nome di rete virtuale esistente e l'indice di subnet relativo alla macchina virtuale.

	$vnetName="<name of an existing virtual network>"
	$subnetIndex=<index of the subnet on which to create the NIC for the virtual machine>
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName

Successivamente, creare una scheda di interfaccia di rete (NIC), richiedere un indirizzo IP pubblico e, facoltativamente, assegnare un'etichetta di nome di dominio DNS. Copiare una delle due opzioni seguenti nel set di comandi e immettere il nome della scheda di interfaccia di rete e l'etichetta del nome di dominio DNS.

Opzione 1: specificare un nome di scheda di interfaccia di rete.

Copiare le righe seguenti nel set di comandi e specificare un nome per la scheda di interfaccia di rete.

	$nicName="<name of the NIC of the VM>"
	$pip = New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

Opzione 2: specificare un nome per la scheda di interfaccia di rete e un'etichetta di nome di dominio DNS.

Copiare le righe seguenti nel set di comandi e specificare un nome per la scheda di interfaccia di rete e l'etichetta del nome di dominio univoco globale. Quando si creano macchine virtuali nella modalità di gestione del servizio di Azure PowerShell, questi passaggi vengono eseguiti automaticamente.

	$nicName="<name of the NIC of the VM>"
	$domName="<domain name label>"
	$pip = New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

Successivamente, creare un oggetto macchina virtuale locale e, facoltativamente, aggiungerlo a un set di disponibilità. Copiare una delle due opzioni seguenti nel set di comandi e immettere il nome, le dimensioni e il nome del set di disponibilità.

Opzione 1: specificare il nome e le dimensioni di una macchina virtuale.

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize

Per determinare i possibili valori della stringa di dimensione della macchina virtuale, necessaria per l'opzione 1, usare i comandi seguenti.

	$locName="<Azure location of your resource group>"
	Get-AzureVMSize -Location $locName | Select Name

Opzione 2: specificare il nome e le dimensioni di una macchina virtuale e aggiungerla a un set di disponibilità.

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$avName="<availability set name>"
	$avSet=Get-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

Per determinare i possibili valori della stringa di dimensione della macchina virtuale, necessaria per l'opzione 2, usare i comandi seguenti.

	$rgName="<resource group name>"
	$avName="<availability set name>"
	Get-AzureVMSize -ResourceGroupName $rgName -AvailabilitySetName $avName | Select Name

> [AZURE.NOTE]Gestione risorse consente attualmente di aggiungere una sola macchina virtuale a un set di disponibilità mentre viene creato.

Per aggiungere un altro disco dati alla macchina virtuale, copiare le righe seguenti al set di comandi e specificare le impostazioni del disco.

	$diskSize=<size of the disk in GB>
	$diskLabel="<the label on the disk>"
	$diskName="<name identifier for the disk in Azure storage, such as 21050529-DISK02>"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty

Successivamente, è necessario determinare l'entità di pubblicazione, l'offerta e la SKU dell'immagine per la macchina virtuale. Di seguito è riportata una tabella delle immagini basate su Windows più comuni.

|Nome entità di pubblicazione | Nome offerta | Nome SKU
|:---------------------------------|:-------------------------------------------|:---------------------------------|
|MicrosoftWindowsServer | WindowsServer | 2008 R2-SP1 |
|MicrosoftWindowsServer | WindowsServer | 2012-Datacenter |
|MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter |
|MicrosoftDynamicsNAV | DynamicsNAV | 2015 |
|MicrosoftSharePoint | MicrosoftSharePointServer | 2013 |
|MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-DW |
|MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-OLTP |
|MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials |
|MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 |

Se l'immagine di macchina virtuale necessaria non è inclusa nell'elenco, seguire queste [istruzioni](resource-groups-vm-searching.md#powershell) per determinare i nomi dell'entità di pubblicazione, dell'offerta e della SKU.

Copiare i comandi seguenti nel set di comandi e specificare i nomi dell'entità di pubblicazione, dell'offerta e della SKU.

	$pubName="<Image publisher name>"
	$offerName="<Image offer name>"
	$skuName="<Image SKU name>"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id

Copiare infine i seguenti comandi nel set di comandi e specificare l'identificatore di nome per il disco del sistema operativo della macchina virtuale.

	$diskName="<name identifier for the disk in Azure storage, such as OSDisk>"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

## Passaggio 5: Eseguire il set di comandi

Rivedere il set di comandi di Azure PowerShell compilato nell'editor di testo o in PowerShell ISE costituito da più blocchi di comandi (Passaggio 4). Assicurarsi di aver specificato tutte le variabili necessarie e che siano presenti i valori corretti. Assicurarsi inoltre che siano stati rimossi tutti i caratteri < and >.

Se i comandi sono stati inseriti in un editor di testo, copiare il set di comandi negli Appunti, quindi fare clic con il pulsante destro del mouse sul prompt aperto di Azure PowerShell. Il set di comandi verrà emesso come una serie di comandi di PowerShell e verrà creata la macchina virtuale di Azure. In alternativa, eseguire il set di comandi in Azure PowerShell ISE.

Se si ritiene in futuro di dover creare nuovamente questa macchina virtuale o una simile, è possibile salvare questo set di comandi come un file di script di PowerShell (estensione ps1).

## Esempio

È necessario un set di comandi di PowerShell per creare una macchina virtuale aggiuntiva per un carico di lavoro line-of-business basato sul Web che:

- Sia incluso nel gruppo di risorse LOBServers esistente
- Usi l'immagine Windows Server 2012 R2 Datacenter
- Presenti il nome LOB07 e sia incluso nel set di disponibilità WEB_AS esistente
- Disponga di una scheda di interfaccia di rete con un indirizzo IP pubblico nella subnet FrontEnd (indice subnet 0) della rete virtuale AZDatacenter esistente
- Disponga di un disco dati aggiuntivo di 200 GB

Di seguito è riportato il set di comandi di Azure PowerShell necessario per creare questa macchina virtuale, in base alla procedura descritta nel Passaggio 4.

	# Switch to the Resource Manager mode
	Switch-AzureMode AzureResourceManager

	# Set values for existing resource group and storage account names
	$rgName="LOBServers"
	$locName="West US"
	$saName="contosoLOBServersSA"

	# Set the existing virtual network and subnet index
	$vnetName="AZDatacenter"
	$subnetIndex=0
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	# Create the NIC
	$nicName="AzureInterface"
	$domName="contoso-vm-lob07"
	$pip=New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

	# Specify the name, size, and existing availability set
	$vmName="LOB07"
	$vmSize="Standard_A3"
	$avName="WEB_AS"
	$avSet=Get-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

	# Add a 200 GB additional data disk
	$diskSize=200
	$diskLabel="APPStorage"
	$diskName="21050529-DISK02"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI -CreateOption empty

	# Specify the image and local administrator account, and then add the NIC
	$pubName="MicrosoftWindowsServer"
	$offerName="WindowsServer"
	$skuName="2012-R2-Datacenter"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id

	# Specify the OS disk name and create the VM
	$diskName="OSDisk"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

## Risorse aggiuntive

[Provider di calcolo, rete e archiviazione in Gestione risorse di Microsoft Azure](virtual-machines-azurerm-versus-azuresm.md)

[Panoramica di Gestione risorse di Microsoft Azure](resource-group-overview.md)

[Distribuire e gestire macchine virtuali di Azure usando modelli di Gestione risorse e PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

[Creare una macchina virtuale Windows con un modello di Gestione risorse e PowerShell](virtual-machines-create-windows-powershell-resource-manager-template-simple)

[Come installare e configurare Azure PowerShell](install-configure-powershell.md)

<!---HONumber=July15_HO4-->