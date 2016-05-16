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
	ms.date="04/26/2016"
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

    Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
    UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
    VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
    ProvisioningState     : Succeeded
    OverProvision         :
    Id                    : /subscriptions/{subscription-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                  : myvmss1
    Type                  : Microsoft.Compute/virtualMachineScaleSets
    Location              : centralus
    Tags                  :

In questo comando sostituire *resource group name* con il nome del gruppo di risorse che include il set di scalabilità di macchine virtuali, *scale set name* con il nome del set di scalabilità di macchine virtuali e *#* con l'ID dell'istanza della macchina virtuale di cui si vogliono ottenere informazioni, quindi eseguire il comando:

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
        
Verrà visualizzata una schermata simile alla seguente:

    InstanceId         : 1
    Sku                : Microsoft.Azure.Management.Compute.Models.Sku
    LatestModelApplied : True
    InstanceView       :
    HardwareProfile    :
    StorageProfile     : Microsoft.Azure.Management.Compute.Models.StorageProfile
    OsProfile          : Microsoft.Azure.Management.Compute.Models.OSProfile
    NetworkProfile     : Microsoft.Azure.Management.Compute.Models.NetworkProfile
    DiagnosticsProfile :
    AvailabilitySet    :
    ProvisioningState  : Succeeded
    LicenseType        :
    Plan               :
    Resources          :
    Id                 : /subscriptions/{subscription-id}/resourceGroups/myrg1/providers/Microsoft.
                         Compute/virtualMachineScaleSets/myvmss1/virtualMachines/1
    Name               : myvmss1_1
    Type               : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location           : centralus
    Tags               :
        
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

<!---HONumber=AcomDC_0504_2016-->