---
title: "Creare un set di scalabilità di macchine virtuali tramite PowerShell | Microsoft Docs"
description: "Creare un set di scalabilità di macchine virtuali tramite PowerShell"
services: virtual-machine-scale-sets
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7bb03323-8bcc-4ee4-9a3e-144ca6d644e2
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/18/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 6fa8b353c47091c9b71173e98021117245cc6694


---
# <a name="create-a-windows-virtual-machine-scale-set-using-azure-powershell"></a>Creare un set di scalabilità di macchine virtuali Windows tramite Azure PowerShell
Questa procedura per la creazione di un set di scalabilità di macchine virtuali di Azure segue un approccio basato sul completamento di valori predefiniti. Per altre informazioni, vedere [Panoramica dei set di scalabilità di macchine virtuali](virtual-machine-scale-sets-overview.md) .

L'esecuzione della procedura illustrata in questo articolo richiede circa 30 minuti.

## <a name="step-1-install-azure-powershell"></a>Passaggio 1: installare Azure PowerShell
Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione e accedere all'account, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

## <a name="step-2-create-resources"></a>Passaggio 2: Creare le risorse
Creare le risorse necessarie per il nuovo set di scalabilità.

### <a name="resource-group"></a>Resource group
Un set di scalabilità di macchine virtuali deve trovarsi all'interno di un gruppo di risorse.

1. Ottenere un elenco di località disponibili in cui è possibile posizionare le risorse:
   
        Get-AzureLocation | Sort Name | Select Name
2. Selezionare la località più adatta alle proprie esigenze, sostituire il valore di **$locName** con il nome di questa località e quindi creare la variabile:
   
        $locName = "location name from the list, such as Central US"
3. Sostituire il valore di **$rgName** con il nome che si vuole usare per il nuovo gruppo di risorse e quindi creare la variabile: 
   
        $rgName = "resource group name"
4. Creare il gruppo di risorse:
   
        New-AzureRmResourceGroup -Name $rgName -Location $locName
   
    L'output sarà simile all'esempio seguente:
   
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### <a name="storage-account"></a>Account di archiviazione
Un account di archiviazione viene usato da una macchina virtuale per archiviare il disco del sistema operativo e i dati di diagnostica usati per la scalabilità. Se possibile, è consigliabile avere un account di archiviazione per ogni macchina virtuale creata in un set di scalabilità. Se questo non è possibile, pianificare un massimo di 20 macchine virtuali per ogni account di archiviazione. L'esempio riportato in questo articolo presenta la creazione di tre account di archiviazione per tre macchine virtuali.

1. Sostituire il valore di **$stName** con un nome per l'account di archiviazione. Testare l'univocità del nome. 
   
        $saName = "storage account name"
        Get-AzureRmStorageAccountNameAvailability $saName
   
    Se la risposta è **True**, il nome proposto è univoco.
2. Sostituire il valore di **$saType** con il tipo di account di archiviazione e quindi creare la variabile:  
   
        $saType = "storage account type"
   
    I valori possibili sono: Standard_LRS, Standard_GRS, Standard_RAGRS o Premium_LRS.
3. Creare l'account:
   
        New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName
   
    L'output sarà simile all'esempio seguente:
   
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
4. Ripetere i passaggi da 1 a 4 per creare tre account di archiviazione, ad esempio myst1, myst2 e myst3.

### <a name="virtual-network"></a>rete virtuale
Per le macchine virtuali nel set di scalabilità è necessaria una rete virtuale.

1. Sostituire il valore di **$subnetName** con il nome da usare per la subnet nella rete virtuale e creare la variabile: 
   
        $subnetName = "subnet name"
2. Creare la configurazione della subnet:
   
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   
    Il prefisso dell'indirizzo potrebbe essere diverso nella rete virtuale.
3. Sostituire il valore di **$netName** con il nome da usare per la rete virtuale e quindi creare la variabile: 
   
        $netName = "virtual network name"
4. Creare la rete virtuale:
   
        $vnet = New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="configuration-of-the-scale-set"></a>Configurazione del set di scalabilità
Sono disponibili tutte le risorse necessarie per la configurazione del set di scalabilità, si procederà quindi alla sua creazione.  

1. Sostituire il valore di **$ipName** con il nome da usare per la configurazione IP e quindi creare la variabile: 
   
        $ipName = "IP configuration name"
2. Creare la configurazione IP:
   
        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id
3. Sostituire il valore di **$vmssConfig** con il nome da usare per la configurazione del set di scalabilità e quindi creare la variabile:   
   
        $vmssConfig = "Scale set configuration name"
4. Creare la configurazione per il set di scalabilità:
   
        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0" -UpgradePolicyMode "manual"
   
    Questo esempio presenta un set di scalabilità creato con tre macchine virtuali. Per altre informazioni sulla capacità dei set di scalabilità, vedere [Panoramica dei set di scalabilità di macchine virtuali](virtual-machine-scale-sets-overview.md) . Questo passaggio prevede anche l'impostazione delle dimensioni, indicata come SkuName, delle macchine virtuali nel set. Per determinare la dimensione più adatta alle esigenze, vedere [Dimensioni delle macchine virtuali in Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
5. Aggiungere la configurazione dell'interfaccia di rete alla configurazione del set di scalabilità:
   
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssConfig -Primary $true -IPConfiguration $ipConfig
   
    L'output sarà simile all'esempio seguente:
   
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

#### <a name="operating-system-profile"></a>Profilo del sistema operativo
1. Sostituire il valore di **$computerName** con il prefisso del nome computer da usare e quindi creare la variabile: 
   
        $computerName = "computer name prefix"
2. Sostituire il valore di **$adminName** con il nome dell'account amministratore nelle macchine virtuali e quindi creare la variabile:
   
        $adminName = "administrator account name"
3. Sostituire il valore di **$adminPassword** con la password dell'account e quindi creare la variabile:
   
        $adminPassword = "password for administrator accounts"
4. Creare il profilo del sistema operativo:
   
        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

#### <a name="storage-profile"></a>Profilo di archiviazione
1. Sostituire il valore di **$storageProfile** con il nome da usare per il profilo di archiviazione e quindi creare la variabile:  
   
        $storageProfile = "storage profile name"
2. Creare le variabili che definiscono l'immagine da usare:  
   
        $imagePublisher = "MicrosoftWindowsServer"
        $imageOffer = "WindowsServer"
        $imageSku = "2012-R2-Datacenter"
   
    Per informazioni su altre immagini da usare, vedere [Esplorare e selezionare immagini di macchine virtuali Windows in Azure con PowerShell o l'interfaccia della riga di comando](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
3. Sostituire il valore di **$vhdContainers** con un elenco contenente i percorsi di archiviazione dei dischi rigidi virtuali, ad esempio "https://mystorage.blob.core.windows.net/vhds" e quindi creare la variabile:
   
        $vhdContainers = @("https://myst1.blob.core.windows.net/vhds","https://myst2.blob.core.windows.net/vhds","https://myst3.blob.core.windows.net/vhds")
4. Creare il profilo di archiviazione:
   
        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storageProfile -VhdContainer $vhdContainers -OsDiskCreateOption "FromImage" -OsDiskCaching "None"  

### <a name="virtual-machine-scale-set"></a>Set di scalabilità di macchine virtuali
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

## <a name="step-3-explore-resources"></a>Passaggio 3: Esplorare le risorse
Per esplorare il set di scalabilità di macchine virtuali appena creato, usare queste risorse:

* Portale di Azure: tramite il portale è disponibile una quantità limitata di informazioni.
* [Esplora risorse di Azure](https://resources.azure.com/): si tratta dello strumento migliore per esaminare lo stato corrente del set di scalabilità.
* Azure PowerShell: per ottenere informazioni, usare il comando seguente.
  
        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
  
        Or 
  
        Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

## <a name="next-steps"></a>Passaggi successivi
* Per gestire il set di scalabilità appena creato, usare le informazioni disponibili in [Gestire le macchine virtuali in un set di scalabilità di macchine virtuali](virtual-machine-scale-sets-windows-manage.md)
* È consigliabile impostare il ridimensionamento automatico del set di scalabilità. A tale scopo, usare le informazioni disponibili in [Ridimensionamento automatico e set di scalabilità di macchine virtuali](virtual-machine-scale-sets-autoscale-overview.md)
* Altre informazioni sull'aumento delle prestazioni sono disponibili in [Scalabilità automatica verticale con set di scalabilità di macchine virtuali](virtual-machine-scale-sets-vertical-scale-reprovision.md)




<!--HONumber=Nov16_HO2-->


