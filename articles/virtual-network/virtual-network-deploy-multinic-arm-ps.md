---
title: "Creare una macchina virtuale con più schede di interfaccia di rete - Azure PowerShell | Documentazione Microsoft"
description: "Informazioni su come creare una VM con più schede di interfaccia di rete mediante PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 88880483-8f9e-4eeb-b783-64b8613407d9
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: ae6c92d4f005b389de2d65c30113580190914b42
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-vm-with-multiple-nics-using-powershell"></a>Creare una macchina virtuale con più schede di interfaccia di rete usando PowerShell

> [!div class="op_single_selector"]
- [PowerShell](virtual-network-deploy-multinic-arm-ps.md)
- [Interfaccia della riga di comando di Azure](virtual-network-deploy-multinic-arm-cli.md)
- [Modello](virtual-network-deploy-multinic-arm-template.md)
- [PowerShell (Classic)](virtual-network-deploy-multinic-classic-ps.md) (PowerShell (classico))
- [Interfaccia della riga di comando di Azure (versione classica)](virtual-network-deploy-multinic-classic-cli.md)

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../resource-manager-deployment-model.md).  Questo articolo illustra il modello di distribuzione Resource Manager che Microsoft consiglia di usare per le distribuzioni più recenti in sostituzione del [modello di distribuzione classica](virtual-network-deploy-multinic-classic-ps.md).
>

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

La procedura seguente usa un gruppo di risorse denominato *IaaSStory* per i server Web e un gruppo di risorse denominato *IaaSStory-BackEnd* per i server di database.

## <a name="prerequisites"></a>Prerequisiti
Prima di creare i server di database, è necessario creare il gruppo di risorse *IaaSStory* con tutte le risorse richieste per questo scenario. Per creare le risorse, seguire questa procedura:

1. Passare alla [pagina del modello](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Nella pagina del modello, a destra del **gruppo di risorse padre**, fare clic su **Distribuisci in Azure**.
3. Se necessario, modificare i valori dei parametri, quindi seguire i passaggi nel portale di anteprima di Azure per distribuire il gruppo di risorse.

> [!IMPORTANT]
> Assicurarsi che i nomi degli account di archiviazione siano univoci. In Azure non sono infatti ammessi nomi di account di archiviazione duplicati.
> 

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>Creare le macchine virtuali di back-end
Le macchine virtuali di back-end dipendono dalla creazione delle risorse seguenti:

* **Account di archiviazione per i dischi dati**. Per migliorare le prestazioni, i dischi dati sui server di database utilizzano la tecnologia SSD (Solid State Drive), che richiede un account di archiviazione premium. Verificare che la posizione di Azure distribuita supporti l'archiviazione premium.
* **Schede di rete**. Ogni macchina virtuale ha due schede di rete, una per l'accesso al database e una per la gestione.
* **Set di disponibilità**. Tutti i server di database vengono aggiunti a un singolo set di disponibilità, per garantire che almeno una delle macchine virtuali sia attiva e in esecuzione durante la manutenzione.  

### <a name="step-1---start-your-script"></a>Passaggio 1 - avviare lo script
È possibile scaricare lo script di PowerShell completo utilizzato [qui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/arm/virtual-network-deploy-multinic-arm-ps.ps1). Attenersi alla procedura seguente per modificare lo script da usare nell'ambiente.

1. Modificare i valori delle variabili indicate di seguito in base al gruppo di risorse esistente distribuito in precedenza in [Prerequisiti](#Prerequisites).

    ```powershell
    $existingRGName        = "IaaSStory"
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    $remoteAccessNSGName   = "NSG-RemoteAccess"
    $stdStorageAccountName = "wtestvnetstoragestd"
    ```

2. Modificare i valori delle variabili indicate di seguito in base ai valori che si desidera usare per la distribuzione di back-end.

    ```powershell
    $backendRGName         = "IaaSStory-Backend"
    $prmStorageAccountName = "wtestvnetstorageprm"
    $avSetName             = "ASDB"
    $vmSize                = "Standard_DS3"
    $publisher             = "MicrosoftSQLServer"
    $offer                 = "SQL2014SP1-WS2012R2"
    $sku                   = "Standard"
    $version               = "latest"
    $vmNamePrefix          = "DB"
    $osDiskPrefix          = "osdiskdb"
    $dataDiskPrefix        = "datadisk"
    $diskSize               = "120"    
    $nicNamePrefix         = "NICDB"
    $ipAddressPrefix       = "192.168.2."
    $numberOfVMs           = 2
    ```
3. Recuperare le risorse esistenti necessarie per la distribuzione.

    ```powershell
    $vnet                  = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $existingRGName
    $backendSubnet         = $vnet.Subnets|?{$_.Name -eq $backendSubnetName}
    $remoteAccessNSG       = Get-AzureRmNetworkSecurityGroup -Name $remoteAccessNSGName -ResourceGroupName $existingRGName
    $stdStorageAccount     = Get-AzureRmStorageAccount -Name $stdStorageAccountName -ResourceGroupName $existingRGName
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Passaggio 2 - Creare le risorse necessarie per le macchine virtuali
È necessario creare un nuovo gruppo di risorse, un account di archiviazione per i dischi dati e un set di disponibilità per tutte le macchine virtuali. Sono inoltre necessarie credenziali dell'account amministratore locale per ciascuna macchina virtuale. Per creare queste risorse, eseguire questi passaggi.

1. Creare un nuovo gruppo di risorse.

    ```powershell
    New-AzureRmResourceGroup -Name $backendRGName -Location $location
    ```
2. Creare un nuovo account di archiviazione premium nel gruppo di risorse creato in precedenza.

    ```powershell
    $prmStorageAccount = New-AzureRmStorageAccount -Name $prmStorageAccountName `
    -ResourceGroupName $backendRGName -Type Premium_LRS -Location $location
    ```
3. Creare un nuovo set di disponibilità.

    ```powershell
    $avSet = New-AzureRmAvailabilitySet -Name $avSetName -ResourceGroupName $backendRGName -Location $location
    ```
4. Ottenere le credenziali dell'account amministratore locale per ciascuna macchina virtuale.

    ```powershell
    $cred = Get-Credential -Message "Type the name and password for the local administrator account."
    ```

### <a name="step-3---create-the-nics-and-back-end-vms"></a>Passaggio 3 - Creare le schede di rete e le macchine virtuali di back-end
È necessario utilizzare un ciclo per creare tutte le macchine virtuali che si desidera e creare le schede di rete e le macchine virtuali necessarie all'interno del ciclo. Per creare le schede di rete e le macchine virtuali, eseguire questa procedura.

1. Avviare un ciclo `for` per ripetere i comandi per la creazione di una macchina virtuale e di due schede di rete per il numero di volte necessario, in base al valore della variabile `$numberOfVMs`.
   
    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. Creare la scheda di rete utilizzata per l'accesso al database.

    ```powershell
    $nic1Name = $nicNamePrefix + $suffixNumber + "-DA"
    $ipAddress1 = $ipAddressPrefix + ($suffixNumber + 3)
    $nic1 = New-AzureRmNetworkInterface -Name $nic1Name -ResourceGroupName $backendRGName `
    -Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress1
    ```

3. Creare la scheda di rete utilizzata per l'accesso remoto. Si noti che a questa scheda di rete è associato un NSG.

    ```powershell
    $nic2Name = $nicNamePrefix + $suffixNumber + "-RA"
    $ipAddress2 = $ipAddressPrefix + (53 + $suffixNumber)
    $nic2 = New-AzureRmNetworkInterface -Name $nic2Name -ResourceGroupName $backendRGName `
    -Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress2 `
    -NetworkSecurityGroupId $remoteAccessNSG.Id
    ```

4. Creare l'oggetto `vmConfig` .

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id
    ```

5. Creare due dischi dati per ciascuna macchina virtuale. Si noti che i dischi dati si trovano nell'account di archiviazione premium creato in precedenza.

    ```powershell
    $dataDisk1Name = $vmName + "-" + $osDiskPrefix + "-1"
    $data1VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk1Name + ".vhd"
    Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk1Name -DiskSizeInGB $diskSize `
    -VhdUri $data1VhdUri -CreateOption empty -Lun 0

    $dataDisk2Name = $vmName + "-" + $dataDiskPrefix + "-2"
    $data2VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk2Name + ".vhd"
    Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk2Name -DiskSizeInGB $diskSize `
    -VhdUri $data2VhdUri -CreateOption empty -Lun 1
    ```

6. Configurare il sistema operativo e l'immagine da utilizzare per la macchina virtuale.

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher -Offer $offer -Skus $sku -Version $version
    ```

7. Aggiungere le due schede di rete create in precedenza per l'oggetto `vmConfig` .

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic1.Id -Primary
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic2.Id
    ```

8. Creare il disco del sistema operativo e la macchina virtuale. Si noti il simbolo `}` al termine del ciclo `for`.

    ```powershell
    $osDiskName = $vmName + "-" + $osDiskSuffix
    $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
    $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
    New-AzureRmVM -VM $vmConfig -ResourceGroupName $backendRGName -Location $location
    }
    ```

### <a name="step-4---run-the-script"></a>Passaggio 4 - Eseguire lo script.
Una volta scaricato e modificato lo script in base alle esigenze, eseguire lo script per creare macchine virtuali del database di back-end con più schede di rete.

1. Salvare lo script ed eseguirlo dal prompt dei comandi **PowerShell** o **PowerShell ISE**. Verrà visualizzato l'output iniziale, come mostrato di seguito:

        ResourceGroupName : IaaSStory-Backend
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
            Actions  NotActions
            =======  ==========
                *                  

        ResourceId        : /subscriptions/[Subscription ID]/resourceGroups/IaaSStory-Backend

2. Dopo qualche minuto, inserire le credenziali nella casella di richiesta e fare clic su **OK**. L'output seguente rappresenta una singola macchina virtuale. Si noti che per il completamento dell'intero processo sono stati necessari 8 minuti.

        ResourceGroupName            :
        Id                           :
        Name                         : DB2
        Type                         :
        Location                     :
        Tags                         :
        TagsText                     : null
        AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
        AvailabilitySetReferenceText :     {
                                     "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Compute/availabilitySets/ASDB"
                                    }
        Extensions                   :
        ExtensionsText               : null
        HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
        HardwareProfileText          : {
                                        "VirtualMachineSize": "Standard_DS3"
                                       }
        InstanceView                 :
        InstanceViewText             : null
        NetworkProfile               :
        NetworkProfileText           : null
        OSProfile                    :
        OSProfileText                : null
        Plan                         :
        PlanText                     : null
        ProvisioningState            :
        StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
        StorageProfileText           : {
                                         "DataDisks": [
                                           {
                                             "Lun": 0,
                                             "Caching": null,
                                             "CreateOption": "empty",
                                             "DiskSizeGB": 127,
                                             "Name": "DB2-datadisk-1",
                                             "SourceImage": null,
                                             "VirtualHardDisk": {
                                               "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
                                             }
                                           }
                                         ],
                                         "ImageReference": null,
                                         "OSDisk": null
                                       }
        DataDiskNames                : {DB2-datadisk-1}
        NetworkInterfaceIDs          :
        RequestId                    :
        StatusCode                   : 0

        ResourceGroupName            :
        Id                           :
        Name                         : DB2
        Type                         :
        Location                     :
        Tags                         :
        TagsText                     : null
        AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
        AvailabilitySetReferenceText : {
                                         "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
                                       Microsoft.Compute/availabilitySets/ASDB"
                                       }
        Extensions                   :
        ExtensionsText               : null
        HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
        HardwareProfileText          : {
                                         "VirtualMachineSize": "Standard_DS3"
                                       }
        InstanceView                 :
        InstanceViewText             : null
        NetworkProfile               :
        NetworkProfileText           : null
        OSProfile                    :
        OSProfileText                : null
        Plan                         :
        PlanText                     : null
        ProvisioningState            :
        StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
        StorageProfileText           : {
                                         "DataDisks": [
                                           {
                                             "Lun": 0,
                                             "Caching": null,
                                             "CreateOption": "empty",
                                             "DiskSizeGB": 127,
                                             "Name": "DB2-datadisk-1",
                                             "SourceImage": null,
                                             "VirtualHardDisk": {
                                               "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
                                             }
                                           },
                                           {
                                             "Lun": 1,
                                             "Caching": null,
                                             "CreateOption": "empty",
                                             "DiskSizeGB": 127,
                                             "Name": "DB2-datadisk-2",
                                             "SourceImage": null,
                                             "VirtualHardDisk": {
                                               "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-2.vhd"
                                             }
                                           }
                                         ],
                                         "ImageReference": null,
                                         "OSDisk": null
                                       }
        DataDiskNames                : {DB2-datadisk-1, DB2-datadisk-2}
        NetworkInterfaceIDs          :
        RequestId                    :
        StatusCode                   : 0
        EndTime             : [Date] [Time]
        Error               :
        Output              :
        StartTime           : [Date] [Time]
        Status              : Succeeded
        TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        StatusCode          : OK

