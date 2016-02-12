<properties
	pageTitle="Creare e configurare una macchina virtuale | Microsoft Azure"
	description="Creare e configurare una macchina virtuale di Azure con PowerShell e il modello di distribuzione di Gestione risorse."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/08/2015"
	ms.author="cynthn"/>

# Creare e preconfigurare una macchina virtuale Windows con Gestione risorse e Azure PowerShell

> [AZURE.SELECTOR]
- [Portal - Windows](virtual-machines-windows-tutorial.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md)
- [PowerShell - Template](virtual-machines-create-windows-powershell-resource-manager-template.md)
- [Portal - Linux](virtual-machines-linux-tutorial-portal-rm.md)
- [CLI](virtual-machines-linux-tutorial.md)

<br>



[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-ps-create-preconfigure-windows-vms.md).

Questi passaggi mostrano come costruire un set di comandi di Azure PowerShell per la creazione e la configurazione di una macchina virtuale di Azure. È possibile usare questo processo modulare per creare rapidamente un set di comandi per una nuova macchina virtuale basata su Windows ed espandere una distribuzione esistente. Può essere inoltre usato per creare più set di comandi che consentono di realizzare rapidamente un ambiente di sviluppo/test personalizzato o un ambiente IT professionale.

Questi passaggi seguono un approccio basato sul completamento di valori predefiniti per la creazione di set di comandi di Azure PowerShell. Questo approccio può essere utile se non si è esperti di PowerShell o per sapere semplicemente quali valori specificare per una corretta configurazione. Gli utenti esperti di PowerShell possono usare i comandi sostituendo le variabili (le righe che iniziano con "$") con i propri valori.

## Passaggio 1: installare Azure PowerShell

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## Passaggio 2: Impostare la sottoscrizione

In primo luogo, avviare un prompt di Azure PowerShell.

Accedere al proprio account.

	Login-AzureRmAccount

Ottenere il nome della sottoscrizione usando il comando seguente.

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

Impostare la sottoscrizione di Azure. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con i nomi corretti.

	$subscr="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current


## Passaggio 3: Creare le risorse

In questa sezione verrà illustrato come creare ogni risorsa per la nuova macchina virtuale.

### Gruppo di risorse


Le macchine virtuali create mediante il modello di distribuzione di Gestione risorse richiedono un gruppo di risorse. Se necessario, creare un nuovo gruppo di risorse per la nuova macchina virtuale. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con i nomi corretti.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureRmResourceGroup -Name $rgName -Location $locName

È possibile usare questo comando per elencare i gruppi di risorse esistenti.

	Get-AzureRmResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Per visualizzare un elenco dei percorsi di Azure in cui è possibile creare macchine virtuali basate su Gestione risorse.

	$loc=Get-AzureRmLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

### Account di archiviazione


Le macchine virtuali create con il modello di distribuzione di Gestione risorse richiedono un account di archiviazione basato su Gestione risorse. Se necessario, creare un nuovo account di archiviazione per la nuova macchina virtuale usando i comandi seguenti.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	$saName="<storage account name>"
	$saType="<storage account type, specify one: Standard_LRS, Standard_GRS, Standard_RAGRS, or Premium_LRS>"
	New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

Per l'account di archiviazione è necessario selezionare un nome univoco globale che contenga solo lettere minuscole e numeri. È possibile usare il comando seguente per elencare gli account di archiviazione esistenti.

	Get-AzureRmStorageAccount | Sort Name | Select Name

Per verificare che quello prescelto sia un nome univoco globale, è necessario eseguire il comando **Test-AzureName**.

	Test-AzureName -Storage <Proposed storage account name>

Se il comando Test-AzureName visualizza "False", il nome proposto è univoco.


### Etichetta di nome di dominio pubblico


Le macchine virtuali create con il modello di distribuzione di Gestione risorse possono usare un'etichetta di nome di dominio pubblico. L'etichetta può contenere solo lettere, numeri e trattini. Il primo e ultimo carattere devono essere una lettera o un numero.

Per verificare che l'etichetta di nome di dominio pubblico sia un'etichetta univoca globale, usare questi comandi.

	$domName="<domain name label to test>"
	$loc="<short name of an Azure location, for example, for West US, the short name is westus>"
	Test-AzureRmDnsAvailability -DomainQualifiedName $domName -Location $loc

Se DNSNameAvailability è "True", quello proposto è un nome univoco globale.

### Set di disponibilità


Se necessario, creare un nuovo set di disponibilità per la nuova macchina virtuale usando i comandi seguenti.

	$avName="<availability set name>"
	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

Usare il comando seguente per elencare i set di disponibilità esistenti.

	Get-AzureRmAvailabilitySet –ResourceGroupName $rgName | Sort Name | Select Name

### Regole NAT

Le macchine virtuali basate su Gestione Risorse possono essere configurate con le regole NAT in entrata per consentire il traffico in ingresso da Internet e possono trovarsi in un set con carico bilanciato. In entrambi i casi, è necessario specificare un'istanza del servizio di bilanciamento del carico e altre impostazioni. Per ulteriori informazioni, vedere[Creare un servizio di bilanciamento del carico tramite Gestione risorse di Azure](../load-balancer/load-balancer-arm-powershell.md).

Le macchine virtuali create con il modello di distribuzione di Gestione risorse richiedono una rete virtuale di Gestione risorse. Se necessario, creare una nuova rete virtuale basata su Gestione risorse con almeno una subnet per la nuova macchina virtuale. Di seguito è riportato un esempio di una nuova rete virtuale denominata **TestNet** con due subnet denominate **frontendSubnet** e **backendSubnet**.

	$rgName="LOBServers"
	$locName="West US"
	$frontendSubnet=New-AzureRmVirtualNetworkSubnetConfig -Name frontendSubnet -AddressPrefix 10.0.1.0/24
	$backendSubnet=New-AzureRmVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix 10.0.2.0/24
	New-AzureRmVirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $frontendSubnet,$backendSubnet

Usare i comandi seguenti per elencare le reti virtuali esistenti.

	$rgName="<resource group name>"
	Get-AzureRmVirtualNetwork -ResourceGroupName $rgName | Sort Name | Select Name

## Passaggio 4: Compilare il set di comandi

Aprire una nuova istanza dell'editor di testo preferito o l'ambiente PowerShell Integrated Scripting Environment (ISE) e copiare le linee seguenti per avviare il set di comandi. Specificare il nome del gruppo di risorse, il percorso di Azure e l'account di archiviazione relativi alla nuova macchina virtuale. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con i nomi corretti.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"

È necessario specificare il nome di una rete virtuale basata su Gestione risorse e il numero dell'indice di una subnet nella rete virtuale. Usare i comandi seguenti per elencare le subnet relative a una rete virtuale.

	$rgName="<resource group name>"
	$vnetName="<virtual network name>"
	Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName | Select Subnets

L'indice di subnet è il numero della subnet nella visualizzazione di questo comando, considerando una numerazione progressiva da sinistra a destra a partire da 0.

Per questo esempio:

	PS C:\> Get-AzureRmVirtualNetwork -Name TestNet -ResourceGroupName LOBServers | Select Subnets

	Subnets
	-------
	{frontendSubnet, backendSubnet}

L'indice di subnet per frontendSubnet è 0 e l'indice di subnet per backendSubnet è 1.

Copiare le righe seguenti nel set di comandi e specificare un nome di rete virtuale esistente e l'indice di subnet relativo alla macchina virtuale.

	$vnetName="<name of an existing virtual network>"
	$subnetIndex=<index of the subnet on which to create the NIC for the virtual machine>
	$vnet=Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

Successivamente, si crea una scheda di rete (NIC). Copiare una delle opzioni seguenti nel set di comandi e immettere le informazioni necessarie.

### Opzione 1: Specificare un nome NIC e assegnare un indirizzo IP pubblico

Copiare le righe seguenti nel set di comandi e specificare un nome per la scheda di interfaccia di rete.

	$nicName="<name of the NIC of the VM>"
	$pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

### Opzione 2: specificare un nome per la scheda di interfaccia di rete e un'etichetta di nome di dominio DNS.

Copiare le righe seguenti nel set di comandi e specificare un nome per la scheda di interfaccia di rete e l'etichetta del nome di dominio univoco globale.

	$nicName="<name of the NIC of the VM>"
	$domName="<domain name label>"
	$pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

### Opzione 3: Specificare un nome NIC e assegnare un indirizzo IP privato statico.

Copiare le righe seguenti nel set di comandi e specificare un nome per la scheda di interfaccia di rete.

	$nicName="<name of the NIC of the VM>"
	$staticIP="<available static IP address on the subnet>"
	$pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id -PrivateIpAddress $staticIP

### Opzione 4: Specificare un nome NIC e un'istanza del servizio di bilanciamento del carico per una regola NAT in entrata.

Per creare una scheda di interfaccia di rete e aggiungerla a un'istanza di servizio di bilanciamento del carico per una regola NAT in entrata, è necessario:

- Il nome di un'istanza del servizio di bilanciamento del carico creato in precedenza che dispone di una regola NAT in entrata per il traffico che viene inoltrato alla macchina virtuale.
- Il numero di indice del pool di indirizzi back-end dell'istanza del servizio di bilanciamento del carico da assegnare alla scheda di interfaccia di rete.
- Il numero di indice della regola NAT in entrata da assegnare alla scheda di interfaccia di rete.

Per informazioni su come creare un'istanza del servizio di bilanciamento del carico con le regole NAT in entrata, vedere l'argomento relativo alla [creazione di un servizio di bilanciamento del carico tramite Gestione risorse di Azure](../load-balancer/load-balancer-arm-powershell.md).

Copiare le righe seguenti nel set di comandi e specificare i nomi necessari e i numeri di indice.

	$nicName="<name of the NIC of the VM>"
	$lbName="<name of the load balancer instance>"
	$bePoolIndex=<index of the back end pool, starting at 0>
	$natRuleIndex=<index of the inbound NAT rule, starting at 0>
	$lb=Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgName
	$nic=New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -Subnet $vnet.Subnets[$subnetIndex].Id -LoadBalancerBackendAddressPool $lb.BackendAddressPools[$bePoolIndex] -LoadBalancerInboundNatRule $lb.InboundNatRules[$natRuleIndex]

La stringa $nicName deve essere univoca per il gruppo di risorse. Una procedura consigliata è quella di incorporare il nome della macchina virtuale nella stringa, ad esempio "LOB07-NIC".

### Opzione 5: Specificare un nome NIC e un'istanza del servizio di bilanciamento del carico per un set con carico bilanciato.

Per creare una scheda di interfaccia di rete e aggiungerla a un'istanza di servizio di bilanciamento del carico per un set con carico bilanciato, è necessario:

- Il nome di un'istanza del servizio di bilanciamento carico creato in precedenza che dispone di una regola per il traffico con carico bilanciato.
- Il numero di indice del pool di indirizzi back-end dell'istanza del servizio di bilanciamento del carico da assegnare alla scheda di interfaccia di rete.

Per informazioni su come creare un'istanza del servizio di bilanciamento del carico con le regole per il traffico con carico bilanciato, vedere l'argomento relativo alla [creazione di un servizio di bilanciamento del carico tramite Gestione risorse di Azure](../load-balancer/load-balancer-arm-powershell.md).

Copiare le righe seguenti nel set di comandi e specificare i nomi necessari e i numeri di indice.

	$nicName="<name of the NIC of the VM>"
	$lbName="<name of the load balancer instance>"
	$bePoolIndex=<index of the back end pool, starting at 0>
	$lb=Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgName
	$nic=New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -Subnet $vnet.Subnets[$subnetIndex].Id -LoadBalancerBackendAddressPool $lb.BackendAddressPools[$bePoolIndex]

Successivamente, creare un oggetto macchina virtuale locale e, facoltativamente, aggiungerlo a un set di disponibilità. Copiare una delle due opzioni seguenti nel set di comandi e immettere il nome, le dimensioni e il nome del set di disponibilità.

Opzione 1: specificare il nome e le dimensioni di una macchina virtuale.

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$vm=New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

Per determinare i possibili valori della stringa di dimensione della macchina virtuale, necessaria per l'opzione 1, usare i comandi seguenti.

	$locName="<Azure location of your resource group>"
	Get-AzureRmVMSize -Location $locName | Select Name

Opzione 2: specificare il nome e le dimensioni di una macchina virtuale e aggiungerla a un set di disponibilità.

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$avName="<availability set name>"
	$avSet=Get-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

Per determinare i possibili valori della stringa di dimensione della macchina virtuale, necessaria per l'opzione 2, usare i comandi seguenti.

	$rgName="<resource group name>"
	$avName="<availability set name>"
	Get-AzureRmVMSize -ResourceGroupName $rgName -AvailabilitySetName $avName | Select Name

> [AZURE.NOTE] Gestione risorse consente attualmente di aggiungere una sola macchina virtuale a un set di disponibilità mentre viene creato.

Per aggiungere un altro disco dati alla macchina virtuale, copiare le righe seguenti al set di comandi e specificare le impostazioni del disco.

	$diskSize=<size of the disk in GB>
	$diskLabel="<the label on the disk>"
	$diskName="<name identifier for the disk in Azure storage, such as 21050529-DISK02>"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureRmVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty

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
	$vm=Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRmVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

Copiare infine i seguenti comandi nel set di comandi e specificare l'identificatore di nome per il disco del sistema operativo della macchina virtuale.

	$diskName="<name identifier for the disk in Azure storage, such as OSDisk>"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"
	$vm=Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

## Passaggio 5: eseguire il set di comandi

Rivedere il set di comandi di Azure PowerShell creato nel passaggio 4 nell'editor di testo o in PowerShell ISE. Assicurarsi di aver specificato tutte le variabili e che siano presenti i valori corretti. Assicurarsi inoltre che siano stati rimossi tutti i caratteri < and >.

Se i comandi sono stati inseriti in un editor di testo, copiare il set negli Appunti, quindi fare clic con il pulsante destro del mouse sul prompt di Azure PowerShell. Il set di comandi verrà inviato come una serie di comandi di PowerShell e verrà creata la macchina virtuale di Azure. In alternativa, eseguire il set di comandi in Azure PowerShell ISE.

Se si desidera riutilizzare queste informazioni per creare altre macchine virtuali, è possibile salvare questo set di comandi come un file di script di PowerShell (*.ps1).

## Esempio

È necessario un set di comandi di PowerShell per creare una macchina virtuale aggiuntiva per un carico di lavoro line-of-business basato sul Web che:

- Viene inserito nel gruppo di risorse esistente LOBServers
- Usi l'immagine Windows Server 2012 R2 Datacenter
- Presenti il nome LOB07 e sia incluso nel set di disponibilità WEB\_AS esistente
- Disponga di una scheda di interfaccia di rete con un indirizzo IP pubblico nella subnet FrontEnd (indice subnet 0) della rete virtuale AZDatacenter esistente
- Disponga di un disco dati aggiuntivo di 200 GB

Ecco il set di comandi di Azure PowerShell per creare la macchina virtuale.

	# Set values for existing resource group and storage account names
	$rgName="LOBServers"
	$locName="West US"
	$saName="contosolobserverssa"

	# Set the existing virtual network and subnet index
	$vnetName="AZDatacenter"
	$subnetIndex=0
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	# Create the NIC
	$nicName="LOB07-NIC"
	$domName="contoso-vm-lob07"
	$pip=New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

	# Specify the name, size, and existing availability set
	$vmName="LOB07"
	$vmSize="Standard_A3"
	$avName="WEB_AS"
	$avSet=Get-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

	# Add a 200 GB additional data disk
	$diskSize=200
	$diskLabel="APPStorage"
	$diskName="21050529-DISK02"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureRmVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI -CreateOption empty

	# Specify the image and local administrator account, and then add the NIC
	$pubName="MicrosoftWindowsServer"
	$offerName="WindowsServer"
	$skuName="2012-R2-Datacenter"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm=Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRmVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	# Specify the OS disk name and create the VM
	$diskName="OSDisk"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	$vm=Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

## Risorse aggiuntive

[Provider di calcolo, rete e archiviazione in Gestione risorse di Microsoft Azure](virtual-machines-azurerm-versus-azuresm.md)

[Panoramica di Gestione risorse di Microsoft Azure](../resource-group-overview.md)

[Distribuire e gestire macchine virtuali di Azure usando modelli di Gestione risorse e PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

[Creare una macchina virtuale Windows con un modello di Gestione risorse e PowerShell](virtual-machines-create-windows-powershell-resource-manager-template.md)

[Come installare e configurare Azure PowerShell](powershell-install-configure.md)

<!---HONumber=AcomDC_0204_2016-->