---
title: "Gestire i set di scalabilità di macchine virtuali con Azure PowerShell | Microsoft Docs"
description: "Cmdlet comuni di Azure PowerShell per la gestione dei set di scalabilità di macchine virtuali, ad esempio per l'avvio e l'arresto di un'istanza o la modifica della capacità del set di scalabilità."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: iainfou
ms.openlocfilehash: 39836b207a84911d4749da8a084779d93949846b
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2017
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Gestire un set di scalabilità di macchine virtuali con Azure PowerShell
Nel ciclo di vita del set di scalabilità di una macchina virtuale potrebbe essere necessario eseguire una o più attività di gestione. Si potrebbe anche voler creare script per automatizzare le attività di ciclo di vita. Questo articolo descrive alcuni dei cmdlet comuni di Azure PowerShell che consentono di eseguire queste attività.

Per completare queste attività di gestione è necessario il modulo Azure PowerShell più recente. Per informazioni su come installare e usare la versione più recente, vedere [Introduzione ad Azure PowerShell](/powershell/azure/get-started-azureps). Se necessario, è possibile [creare un set di scalabilità di macchine virtuali nel portale di Azure](virtual-machine-scale-sets-portal-create.md).


## <a name="view-information-about-a-scale-set"></a>Visualizzare informazioni su un set di scalabilità
Per visualizzare informazioni generali su un set di scalabilità, usare [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss). Nell'esempio seguente si ottengono informazioni su un set di scalabilità denominato *myScaleSet* nel gruppo di risorse *myResourceGroup*. Immettere i nomi personalizzati nel modo seguente:

```powershell
Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Visualizzare le macchine virtuali in un set di scalabilità
Per visualizzare l'elenco delle istanze di VM in un set di scalabilità, usare [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). L'esempio seguente elenca tutte le istanze di VM presenti nel set di scalabilità denominato *myScaleSet* e nel gruppo di risorse *myResourceGroup*. Specificare valori personalizzati per questi nomi:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Per visualizzare altre informazioni su un'istanza di VM specifica, aggiungere il parametro `-InstanceId` a [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) e specificare un'istanza da visualizzare. L'esempio seguente visualizza informazioni su un'istanza di VM *0* nel set di scalabilità denominato *myScaleSet* e nel gruppo di risorse *myResourceGroup*. Immettere i nomi personalizzati nel modo seguente:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>Modificare la capacità di un set di scalabilità
Con i comandi precedenti vengono visualizzate informazioni sul set di scalabilità e sulle istanze di VM. Per aumentare o diminuire il numero di istanze in un set di scalabilità è possibile modificare la capacità. Il set di scalabilità crea o rimuove automaticamente il numero necessario di VM, quindi configura le VM per la ricezione del traffico dell'applicazione.

Creare prima un oggetto set di scalabilità con [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss), quindi specificare un nuovo valore per `sku.capacity`. Per applicare la modifica della capacità, usare [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). L'esempio seguente aggiorna *myScaleSet* nel gruppo di risorse *myResourceGroup* a una capacità di *5* istanze. Specificare i valori personalizzati nel modo seguente:

```powershell
# Get current scale set
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet $vmss 
```

Sono necessari alcuni minuti per aggiornare la capacità del set di scalabilità. Quando si riduce la capacità di un set di scalabilità, vengono rimosse prima le macchine virtuali con l'ID istanza più elevato.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Arrestare e avviare VM in un set di scalabilità
Per arrestare una o più VM in un set di scalabilità, usare [Stop-AzureRmVmss](/powershell/module/azurerm.compute/stop-azurermvmss). Il parametro `-InstanceId` consente di specificare una o più VM da arrestare. Se non si specifica un ID istanza, vengono arrestate tutte le VM del set di scalabilità. Per arrestare più VM, separare gli ID istanza con una virgola.

L'esempio seguente arresta l'istanza *0* nel set di scalabilità denominato *myScaleSet* e nel gruppo di risorse *myResourceGroup*. Specificare i valori personalizzati nel modo seguente:

```powershell
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Per impostazione predefinita, le VM arrestate vengono deallocate e quindi non generano costi di calcolo. Se si vuole che la VM resti nello stato di provisioning quando viene arrestata, aggiungere il parametro `-StayProvisioned` al comando precedente. Le VM arrestate che rimangono nello stato di provisioning generano costi di calcolo.


### <a name="start-vms-in-a-scale-set"></a>Avviare VM in un set di scalabilità
Per avviare una o più VM in un set di scalabilità, usare [Start-AzureRmVmss](/powershell/module/azurerm.compute/start-azurermvmss). Il parametro `-InstanceId` consente di specificare una o più VM da avviare. Se non si specifica un ID istanza, vengono avviate tutte le VM del set di scalabilità. Per avviare più VM, separare gli ID istanza con una virgola.

L'esempio seguente avvia l'istanza *0* nel set di scalabilità denominato *myScaleSet* e nel gruppo di risorse *myResourceGroup*. Specificare i valori personalizzati nel modo seguente:

```powershell
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Riavviare VM in un set di scalabilità
Per riavviare una o più VM in un set di scalabilità, usare [Restart-AzureRmVmss](/powershell/module/azurerm.compute/restart-azurermvmss). Il parametro `-InstanceId` consente di specificare una o più VM da riavviare. Se non si specifica un ID istanza, vengono riavviate tutte le VM del set di scalabilità. Per riavviare più VM, separare gli ID istanza con una virgola.

L'esempio seguente riavvia l'istanza *0* nel set di scalabilità denominato *myScaleSet* e nel gruppo di risorse *myResourceGroup*. Specificare i valori personalizzati nel modo seguente:

```powershell
Restart-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Rimuovere VM da un set di scalabilità
Per rimuovere una o più VM in un set di scalabilità, usare [Remove-AzureRmVmss](/powershell/module/azurerm.compute/remove-azurermvmss). Il parametro `-InstanceId` consente di specificare una o più VM da rimuovere. Se non si specifica un ID istanza, vengono rimosse tutte le VM del set di scalabilità. Per rimuovere più VM, separare gli ID istanza con una virgola.

L'esempio seguente rimuove l'istanza *0* nel set di scalabilità denominato *myScaleSet* e nel gruppo di risorse *myResourceGroup*. Specificare i valori personalizzati nel modo seguente:

```powershell
Remove-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Passaggi successivi
Altre attività comuni per i set di scalabilità includono la [distribuzione di un'applicazione](virtual-machine-scale-sets-deploy-app.md) e l'[aggiornamento delle istanze di VM](virtual-machine-scale-sets-upgrade-scale-set.md). È anche possibile usare Azure PowerShell per [configurare regole di scalabilità automatica](virtual-machine-scale-sets-autoscale-overview.md).