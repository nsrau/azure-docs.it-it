---
title: "Gestire VM in un set di scalabilità di macchine virtuali | Microsoft Docs"
description: "Gestire le macchine virtuali in un set di scalabilità di macchine virtuali tramite Azure PowerShell."
services: virtual-machine-scale-sets
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 812d5c977ace7176e81e3e875daaf8e643c95a46


---
# <a name="manage-virtual-machines-in-a-virtual-machine-scale-set"></a>Gestire le macchine virtuali in un set di scalabilità di macchine virtuali
Usare le attività descritte in questo articolo per gestire le macchine virtuali nel set di scalabilità.

La maggior parte delle attività che implicano la gestione di una macchina virtuale in un set di scalabilità richiede di conoscere l'ID dell'istanza della macchina virtuale che si vuole gestire. È possibile usare [Esplora risorse di Azure](https://resources.azure.com) per trovare l'ID dell'istanza di una macchina virtuale in un set di scalabilità. È anche possibile usare Esplora risorse per verificare lo stato delle attività da completare.

Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione e accedere all'account, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

## <a name="display-information-about-a-scale-set"></a>Visualizzare informazioni relative a un set di scalabilità
È possibile ottenere informazioni generali su un set di scalabilità, ovvero una visualizzazione delle istanze. In alternativa, è possibile ottenere informazioni più specifiche, ad esempio informazioni sulle risorse nel set di scalabilità.

Sostituire i valori tra virgolette con il nome del gruppo di risorse e il set di scalabilità, quindi eseguire il comando:

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

Verrà visualizzata una schermata simile alla seguente:

    Id                                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                                        : myvmss1
    Type                                        : Microsoft.Compute/virtualMachineScaleSets
    Location                                    : centralus
    Sku                                         :
      Name                                      : Standard_A0
      Tier                                      : Standard
      Capacity                                  : 3
    UpgradePolicy                               :
      Mode                                      : Manual
    VirtualMachineProfile                       :
      OsProfile                                 :
        ComputerNamePrefix                      : vmss1
        AdminUsername                           : admin1
        WindowsConfiguration                    :
          ProvisionVMAgent                      : True
          EnableAutomaticUpdates                : True
    StorageProfile                              :
      ImageReference                            :
        Publisher                               : MicrosoftWindowsServer
        Offer                                   : WindowsServer
        Sku                                     : 2012-R2-Datacenter
        Version                                 : latest
      OsDisk                                    :
        Name                                    : vmssosdisk
        Caching                                 : ReadOnly
        CreateOption                            : FromImage
        VhdContainers[0]                        : https://astore.blob.core.windows.net/vmss
        VhdContainers[1]                        : https://gstore.blob.core.windows.net/vmss
        VhdContainers[2]                        : https://mstore.blob.core.windows.net/vmss
        VhdContainers[3]                        : https://sstore.blob.core.windows.net/vmss
        VhdContainers[4]                        : https://ystore.blob.core.windows.net/vmss
    NetworkProfile                              :
      NetworkInterfaceConfigurations[0]         :
        Name                                    : mync1
        Primary                                 : True
        IpConfigurations[0]                     :
          Name                                  : ip1
          Subnet                                :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1
          LoadBalancerBackendAddressPools[0]    :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/backendAddressPools/bepool1
        LoadBalancerInboundNatPools[0]          :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/inboundNatPools/natpool1
    ExtensionProfile                            :
      Extensions[0]                             :
        Name                                    : Microsoft.Insights.VMDiagnosticsSettings
        Publisher                               : Microsoft.Azure.Diagnostics
        Type                                    : IaaSDiagnostics
        TypeHandlerVersion                      : 1.5
        AutoUpgradeMinorVersion                 : True
        Settings                                : {"xmlCfg":"...","storageAccount":"astore"}
    ProvisioningState                           : Succeeded

Sostituire i valori tra virgolette con il nome del gruppo di risorse e il set di scalabilità. Sostituire *#* con l'identificatore dell'istanza della macchina virtuale di cui si desidera ottenere informazioni, quindi eseguirlo:

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Verrà visualizzata una schermata simile a all'esempio seguente:

    Id                            : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/
                                    virtualMachineScaleSets/myvmss1/virtualMachines/0
    Name                          : myvmss1_0
    Type                          : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location                      : centralus
    InstanceId                    : 0
    Sku                           :
      Name                        : Standard_A0
      Tier                        : Standard
    LatestModelApplied            : True
    StorageProfile                :
      ImageReference              :
        Publisher                 : MicrosoftWindowsServer
        Offer                     : WindowsServer
        Sku                       : 2012-R2-Datacenter
        Version                   : 4.0.20160617
      OsDisk                      :
        OsType                    : Windows
        Name                      : vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8
        Vhd                       :
          Uri                     : https://astore.blob.core.windows.net/vmss/vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8.vhd
        Caching                   : ReadOnly
        CreateOption              : FromImage
    OsProfile                     :
      ComputerName                : myvmss1-0
      AdminUsername               : admin1
      WindowsConfiguration        :
        ProvisionVMAgent          : True
        EnableAutomaticUpdates    : True
    NetworkProfile                :
      NetworkInterfaces[0]        :
        Id                        : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/
                                    myvmss1/virtualMachines/0/networkInterfaces/mync1
    ProvisioningState             : Succeeded
    Resources[0]                  :
      Id                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachines/
                                    myvmss1_0/extensions/Microsoft.Insights.VMDiagnosticsSettings
      Name                        : Microsoft.Insights.VMDiagnosticsSettings
      Type                        : Microsoft.Compute/virtualMachines/extensions
      Location                    : centralus
      Publisher                   : Microsoft.Azure.Diagnostics
      VirtualMachineExtensionType : IaaSDiagnostics
      TypeHandlerVersion          : 1.5
      AutoUpgradeMinorVersion     : True
      Settings                    : {"xmlCfg":"...","storageAccount":"astore"}
      ProvisioningState           : Succeeded

## <a name="start-a-virtual-machine-in-a-scale-set"></a>Avviare una macchina virtuale in un set di scalabilità
Sostituire i valori tra virgolette con il nome del gruppo di risorse e il set di scalabilità. Sostituire *#* con l'identificatore della macchina virtuale che si desidera avviare, quindi eseguirlo:

    Start-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

In Esplora risorse è possibile vedere che lo stato dell'istanza è **running**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

È possibile avviare tutte le macchine virtuali nel set di scalabilità senza usare il parametro - InstanceId.

## <a name="stop-a-virtual-machine-in-a-scale-set"></a>Arrestare una macchina virtuale in un set di scalabilità
Sostituire i valori tra virgolette con il nome del gruppo di risorse e il set di scalabilità. Sostituire *#* con l'identificatore della macchina virtuale che si desidera arrestare, quindi eseguirlo:

    Stop-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

In Esplora risorse è possibile vedere che lo stato dell'istanza è **deallocated**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]

Per arrestare una macchina virtuale e non deallocarla, usare il parametro -StayProvisioned. È possibile arrestare tutte le macchine virtuali nel set senza usare il parametro - InstanceId.

## <a name="restart-a-virtual-machine-in-a-scale-set"></a>Riavviare una macchina virtuale in un set di scalabilità
Sostituire i valori tra virgolette con il nome del gruppo di risorse e il set di scalabilità. Sostituire *#* con l'identificatore della macchina virtuale che si desidera riavviare, quindi eseguirlo:

    Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

È possibile riavviare tutte le macchine virtuali nel set senza usare il parametro - InstanceId.

## <a name="remove-a-virtual-machine-from-a-scale-set"></a>Rimuovere una macchina virtuale da un set di scalabilità
Sostituire i valori tra virgolette con il nome del gruppo di risorse e il set di scalabilità. Sostituire *#* con l'identificatore della macchina virtuale che si desidera rimuovere, quindi eseguirlo:  

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

Per rimuovere il set di scalabilità di macchine virtuali tutto in una volta, non usare il parametro - InstanceId.

## <a name="change-the-capacity-of-a-scale-set"></a>Modificare la capacità di un set di scalabilità
È possibile aggiungere o rimuovere le macchine virtuali modificando la capacità del set. Ottenere il set di scalabilità che si desidera modificare, impostare il valore di capacità desiderato, quindi aggiornare il set di scalabilità con la nuova capacità. Nei comandi riportati sotto, sostituire i valori tra virgolette con il nome del gruppo di risorse e il set di scalabilità.

  $vmss = Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" $vmss.sku.capacity = 5 Update-AzureRmVmss -ResourceGroupName "resource group name" -Name "scale set name" -VirtualMachineScaleSet $vmss 

Se si rimuovono le macchine virtuali dal set di scalabilità, per prime verranno rimosse le macchine virtuali con gli ID più elevati.




<!--HONumber=Nov16_HO3-->


