<properties
	pageTitle="Creare un set di scalabilità di macchine virtuali | Microsoft Azure"
	description="Creare un set di scalabilità di macchine virtuali tramite PowerShell"
	services="virtual-machine-scale-sets"
    documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="davidmu"/>

# Creare un set di scalabilità di macchine virtuali Windows tramite Azure PowerShell

Questa procedura per la creazione di un set di scalabilità di macchine virtuali di Azure segue un approccio basato sul completamento di valori predefiniti. Per altre informazioni, vedere [Panoramica dei set di scalabilità di macchine virtuali](virtual-machine-scale-sets-overview.md).

L'esecuzione della procedura illustrata in questo articolo richiede circa 30 minuti.

## Passaggio 1: installare Azure PowerShell

Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione che si vuole usare e accedere all'account di Azure, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

## Passaggio 2: Creare le risorse

Creare le risorse necessarie per il nuovo set di scalabilità di macchine virtuali.

### Gruppo di risorse

Un set di scalabilità di macchine virtuali deve trovarsi all'interno di un gruppo di risorse.

1.  Ottenere un elenco di località disponibili e i servizi supportati:

        Get-AzureLocation | Sort Name | Select Name, AvailableServices

    Verrà visualizzata una schermata analoga alla seguente

        Name                AvailableServices
        ----                -----------------
        Australia East      {Compute, Storage, PersistentVMRole, HighMemory}
        Australia Southeast {Compute, Storage, PersistentVMRole, HighMemory}
        Brazil South        {Compute, Storage, PersistentVMRole, HighMemory}
        Central India       {Compute, Storage, PersistentVMRole, HighMemory}
        Central US          {Compute, Storage, PersistentVMRole, HighMemory}
        East Asia           {Compute, Storage, PersistentVMRole, HighMemory}
        East US             {Compute, Storage, PersistentVMRole, HighMemory}
        East US 2           {Compute, Storage, PersistentVMRole, HighMemory}
        Japan East          {Compute, Storage, PersistentVMRole, HighMemory}
        Japan West          {Compute, Storage, PersistentVMRole, HighMemory}
        North Central US    {Compute, Storage, PersistentVMRole, HighMemory}
        North Europe        {Compute, Storage, PersistentVMRole, HighMemory}
        South Central US    {Compute, Storage, PersistentVMRole, HighMemory}
        South India         {Compute, Storage, PersistentVMRole, HighMemory}
        Southeast Asia      {Compute, Storage, PersistentVMRole, HighMemory}
        West Europe         {Compute, Storage, PersistentVMRole, HighMemory}
        West India          {Compute, Storage, PersistentVMRole, HighMemory}
        West US             {Compute, Storage, PersistentVMRole, HighMemory}

2. Selezionare la località più adatta alle proprie esigenze, sostituire il valore di **$locName** con il nome di questa località e quindi creare la variabile:

        $locName = "location name from the list, such as Central US"

3. Sostituire il valore di **$rgName** con il nome che si vuole usare per il nuovo gruppo di risorse e quindi creare la variabile:

        $rgName = "resource group name"
        
4. Creare il gruppo di risorse:
    
        New-AzureRmResourceGroup -Name $rgName -Location $locName

    Verrà visualizzata una schermata analoga alla seguente:

        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### Account di archiviazione

Le macchine virtuali create in un set di scalabilità richiedono un account di archiviazione per l'archiviazione dei dischi associati.

1. Sostituire il valore di **saName** con il nome che si vuole usare per l'account di archiviazione e quindi creare la variabile: 

        $saName = "storage account name"
        
2. Verificare che il nome selezionato sia univoco:
    
        Test-AzureName -Storage $saName

    Se la risposta è **False**, il nome proposto è univoco.

3. Sostituire il valore di **$saType** con il tipo di account di archiviazione e quindi creare la variabile:

        $saType = "storage account type"
        
    I valori possibili sono: Standard\_LRS, Standard\_GRS, Standard\_RAGRS o Premium\_LRS.
        
4. Creare l'account:
    
        New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

    Verrà visualizzata una schermata analoga alla seguente:

        ResourceGroupName   : myrg1
        StorageAccountName  : myst1
        Id                  : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft
	                    	.Storage/storageAccounts/myst1
        Location            : centralus
        AccountType         : StandardLRS
        CreationTime        : 3/15/2016 4:51:52 PM
        CustomDomain        :
        LastGeoFailoverTime :
        PrimaryEndpoints    : Microsoft.Azure.Management.Storage.Models.Endpoints
        PrimaryLocation     : centralus
        ProvisioningState   : Succeeded
        SecondaryEndpoints  :
        SecondaryLocation   :
        StatusOfPrimary     : Available
        StatusOfSecondary   :
        Tags                : {}
        Context             : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext

### Rete virtuale

Per le macchine virtuali nel set di scalabilità è necessaria una rete virtuale.

1. Sostituire il valore di **$subName** con il nome che si vuole usare per la subnet della rete virtuale e quindi creare la variabile: 

        $subName = "subnet name"
        
2. Creare la configurazione della subnet:
    
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subName -AddressPrefix 10.0.0.0/24
        
    Il prefisso dell'indirizzo potrebbe essere diverso nella rete virtuale.

3. Sostituire il valore di **$netName** con il nome che si vuole usare per la rete virtuale e quindi creare la variabile:

        $netName = "virtual network name"
        
4. Creare la rete virtuale:
    
        $vnet = New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### Indirizzo IP pubblico

Prima di creare un'interfaccia di rete, è necessario creare un indirizzo IP pubblico.

1. Sostituire il valore di **$domName** con l'etichetta di nome di dominio che si vuole usare con l'indirizzo IP pubblico e quindi creare la variabile:  

        $domName = "domain name label"
        
    L'etichetta può contenere solo lettere, numeri e trattini. L'ultimo carattere deve essere una lettera o un numero.
    
2. Verificare che il nome sia univoco:
    
        Test-AzureRmDnsAvailability -DomainQualifiedName $domName -Location $locName

    Se la risposta è **True**, il nome proposto è univoco.

3. Sostituire il valore di **$pipName** con il nome che si vuole usare per l'indirizzo IP pubblico e quindi creare la variabile.

        $pipName = "public ip address name"
        
4. Creare l'indirizzo IP pubblico:
    
        $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic -DomainNameLabel $domName

### Interfaccia di rete

Ora che l'indirizzo IP pubblico è disponibile, è possibile creare l'interfaccia di rete.

1. Sostituire il valore di **$nicName** con il nome che si vuole usare per l'interfaccia di rete e quindi creare la variabile: 

        $nicName = "network interface name"
        
2. Creare l'interfaccia di rete:
    
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### Configurazione del set di scalabilità

Sono disponibili tutte le risorse necessarie per la configurazione del set di scalabilità, si procederà quindi alla sua creazione.

1. Sostituire il valore di **$ipName** con il nome che si vuole usare per la configurazione IP e quindi creare la variabile: 

        $ipName = "IP configuration name"
        
2. Creare la configurazione IP:

        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

2. Sostituire il valore di **$vmssConfig** con il nome che si vuole usare per la configurazione del set di scalabilità e quindi creare la variabile:

        $vmssConfig = "Scale set configuration name"
        
3. Creare la configurazione per il set di scalabilità:

        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0" -UpgradePolicyMode "manual"
        
    Questo esempio mostra un set di scalabilità creato con 3 macchine virtuali. Per altre informazioni sulla capacità dei set di scalabilità, vedere [Panoramica dei set di scalabilità di macchine virtuali](virtual-machine-scale-sets-overview.md). Questo passaggio prevede anche l'impostazione delle dimensioni, indicata come SkuName, delle macchine virtuali nel set. Vedere la pagina relativa alle [dimensioni delle macchine virtuali](..\virtual-machines\virtual-machines-windows-sizes.md) per individuare dimensioni in grado di soddisfare le proprie esigenze.
    
4. Aggiungere la configurazione dell'interfaccia di rete alla configurazione del set di scalabilità:
        
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssConfig -Primary $true -IPConfiguration $ipConfig
        
    Verrà visualizzata una schermata analoga alla seguente:

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     :
        OverProvision         :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags                  :

#### Profilo del sistema operativo

1. Sostituire il valore di **$computerName** con il prefisso del nome del computer che si vuole usare e quindi creare la variabile: 

        $computerName = "computer name prefix"
        
2. Sostituire il valore di **$adminName** con il nome dell'account amministratore nelle macchine virtuali e quindi creare la variabile:

        $adminName = "administrator account name"
        
3. Sostituire il valore di **$adminPassword** con la password dell'account e quindi creare la variabile:

        $adminPassword = "password for administrator accounts"
        
4. Creare il profilo del sistema operativo:

        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

#### Profilo di archiviazione

1. Sostituire il valore di **$storageProfile** con il nome che si vuole usare per il profilo di archiviazione e quindi creare la variabile:  

        $storeProfile = "storage profile name"
        
2. Creare le variabili che definiscono l'immagine da usare:
      
        $imagePublisher = "MicrosoftWindowsServer"
        $imageOffer = "WindowsServer"
        $imageSku = "2012-R2-Datacenter"
        
    Vedere [Navigate and select Azure virtual machine images with Windows PowerShell and the Azure CLI](..\virtual-machines\virtual-machines-windows-cli-ps-findimage.md) (Esplorare e selezionare immagini di macchine virtuali di Azure con Windows PowerShell e l'interfaccia della riga di comando di Azure) per trovare le informazioni su altre immagini da usare.
        
3. Sostituire il valore di **$vhdContainer** con il percorso in cui sono archiviati i dischi rigidi virtuali, ad esempio "https://mystorage.blob.core.windows.net/vhds", quindi creare la variabile:
       
        $vhdContainer = "URI of storage container"
        
4. Creare il profilo di archiviazione:

        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storeProfile -VhdContainer $vhdContainer -OsDiskCreateOption "FromImage" -OsDiskCaching "None"  

### Set di scalabilità di macchine virtuali

Infine, è possibile creare il set di scalabilità.

1. Sostituire il valore di **$vmssName** con il nome del set di scalabilità di macchine virtuali e quindi creare la variabile:

        $vmssName = "scale set name"
        
2. Creare il set di scalabilità:

        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss

    Per indicare la riuscita della distribuzione verrà visualizzata una schermata simile a quella che segue:

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     : Updating
        OverProvision         :
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microso
                               ft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags                  :

## Passaggio 3: Esplorare le risorse

Per esplorare il set di scalabilità di macchine virtuali appena creato, usare queste risorse:

- Portale di Azure: tramite il portale è disponibile una quantità limitata di informazioni.
- [Esplora risorse di Azure](https://resources.azure.com/): si tratta dello strumento migliore per esaminare lo stato corrente del set di scalabilità.
- Azure PowerShell: per ottenere informazioni, usare il comando seguente:

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

## Passaggi successivi

- Gestire il set di scalabilità appena creato usando le informazioni contenute in [Manage virtual machines in a Virtual Machine Scale Set](virtual-machine-scale-sets-windows-manage.md) (Gestire macchine virtuali in un set di scalabilità di macchine virtuali).
- Prendere in considerazione la possibilità di impostare il ridimensionamento automatico del set di scalabilità usando le informazioni contenute in [Automatic scaling and virtual machine scale sets](virtual-machine-scale-sets-autoscale-overview.md) (Ridimensionamento automatico e set di scalabilità di macchine virtuali)
- Altre informazioni sul ridimensionamento verticale sono disponibili in [Ridimensionamento automatico verticale con set di scalabilità di macchine virtuali](virtual-machine-scale-sets-vertical-scale-reprovision.md)

<!---HONumber=AcomDC_0504_2016-->