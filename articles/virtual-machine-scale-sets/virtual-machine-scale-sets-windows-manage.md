<properties
	pageTitle="Gestire VM in un set di scalabilità di macchine virtuali | Microsoft Azure"
	description="Gestire le macchine virtuali in un set di scalabilità di macchine virtuali tramite Azure PowerShell."
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
	ms.date="07/14/2016"
	ms.author="davidmu"/>

# Gestire le macchine virtuali in un set di scalabilità di macchine virtuali

Usare le attività descritte in questo articolo per gestire le risorse delle macchine virtuali del set di scalabilità.

Tutte le attività che implicano la gestione di una macchina virtuale in un set di scalabilità richiedono che si conosca l'ID dell'istanza della macchina virtuale che si vuole gestire. È possibile usare [Esplora risorse di Azure](https://resources.azure.com) per trovare l'ID dell'istanza di una macchina virtuale in un set di scalabilità. È anche possibile usare Esplora risorse per verificare lo stato delle attività da completare.

Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione che si vuole usare e accedere all'account Azure, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

## Visualizzare informazioni su un set di scalabilità di macchine virtuali

È possibile ottenere informazioni generali su un set di scalabilità, ovvero una visualizzazione delle istanze. In alternativa, è possibile ottenere informazioni più specifiche, ad esempio informazioni sulle risorse nel set.

In questo comando sostituire *resource group name* con il nome del gruppo di risorse che include il set di scalabilità di macchine virtuali e *scale set name* con il nome del set di scalabilità, quindi eseguire il comando:

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
    
In questo comando sostituire *resource group name* con il nome del gruppo di risorse che include il set di scalabilità di macchine virtuali, *scale set name* con il nome del set di scalabilità di macchine virtuali e *#* con l'ID dell'istanza della macchina virtuale di cui si vogliono ottenere informazioni, quindi eseguire il comando:

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
        
Verrà visualizzata una schermata simile alla seguente:

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
        
## Avviare una macchina virtuale in un set di scalabilità

In questo comando sostituire *resource group name* con il nome del gruppo di risorse che include il set di scalabilità di macchine virtuali, *scale set name* con il nome del set di scalabilità, sostituire *#* con l'ID della macchina virtuale che si vuole avviare, quindi eseguire il comando:

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

Per avviare tutte le macchine virtuali nel set, non usare il parametro - InstanceId.
    
## Arrestare una macchina virtuale in un set di scalabilità

In questo comando sostituire *resource group name* con il nome del gruppo di risorse che include il set di scalabilità di macchine virtuali, *scale set name* con il nome del set di scalabilità, sostituire *#* con l'ID della macchina virtuale che si vuole arrestare, quindi eseguire il comando:

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
    
Per arrestare una macchina virtuale e non deallocarla, usare il parametro -StayProvisioned. Per arrestare tutte le macchine virtuali nel set, non usare il parametro - InstanceId.
    
## Riavviare una macchina virtuale in un set di scalabilità

In questo comando sostituire *resource group name* con il nome del gruppo di risorse che include il set di scalabilità di macchine virtuali, *scale set name* con il nome del set di scalabilità, sostituire *#* con l'ID della macchina virtuale che si vuole riavviare, quindi eseguire il comando:

	Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
    
Per riavviare tutte le macchine virtuali nel set, non usare il parametro - InstanceId.

## Rimuovere una macchina virtuale da un set di scalabilità

In questo comando sostituire *resource group name* con il nome del gruppo di risorse che include il set di scalabilità di macchine virtuali, *scale set name* con il nome del set di scalabilità, sostituire *#* con l'ID della macchina virtuale che si vuole rimuovere dal set di scalabilità, quindi eseguire il comando:

	Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

Per rimuovere il set di scalabilità di macchine virtuali tutto in una volta, non usare il parametro - InstanceId.

<!---HONumber=AcomDC_0720_2016-->