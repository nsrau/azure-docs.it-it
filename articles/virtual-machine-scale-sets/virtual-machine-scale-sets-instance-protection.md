---
title: Istanze del set di protezione dell'istanza per la scalabilità di macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come impedire operazioni di riduzione del numero e set di scalabilità di istanze di set di scalabilità di macchine virtuali di Azure.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: manayar
ms.openlocfilehash: 6c271c2c9feb1520951b2a8e301da4878970d60a
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259423"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances-preview"></a>Protezione dell'istanza per la scalabilità di macchine virtuali di Azure imposta le istanze (anteprima)
Set di scalabilità di macchine virtuali di Azure Abilita elasticità migliori per i carichi di lavoro attraverso [scalabilità automatica](virtual-machine-scale-sets-autoscale-overview.md), pertanto è possibile configurare quando l'infrastruttura di scale-out e quando lo scale-in. Set di scalabilità consentono inoltre di gestire in modo centralizzato, configurare e aggiornare un numero elevato di macchine virtuali tramite diversi [criteri di aggiornamento](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) impostazioni. È possibile configurare un aggiornamento nel modello del set di scalabilità e la nuova configurazione viene applicata automaticamente a ogni istanza di set di scalabilità se è stato impostato il criterio di aggiornamento automatico o in corso.

Come l'applicazione elabora il traffico, possono esserci situazioni in cui si desidera che le istanze specifiche di trattare in modo diverso rispetto al resto della scala impostare l'istanza. Ad esempio, alcune istanze del set di scalabilità è stato possibile eseguire operazioni a esecuzione prolungata e non si desidera che queste istanze per essere ridimensionati fino al completamento di operazioni. Si potrebbero anche avere specializzati poche istanze nel set di scalabilità per eseguire attività aggiuntive o diverse rispetto agli altri membri del set di scalabilità. Si richiedono queste macchine virtuali 'speciale' non deve essere modificato con le altre istanze del set di scalabilità. Protezione dell'istanza fornisce i controlli aggiuntivi per abilitare questi e altri scenari per l'applicazione.

Questo articolo descrive come è possibile applicare e usare le funzionalità di protezione diversi istanza con istanze del set di scalabilità.

> [!NOTE]
>Protezione dell'istanza è attualmente in anteprima pubblica. Per usare la funzionalità di anteprima pubblica descritta di seguito non è necessaria alcuna procedura di consenso esplicito. L'anteprima di protezione istanza è supportato solo con l'API versione 2019-03-01 e versioni successive.

## <a name="types-of-instance-protection"></a>Tipi di protezione dell'istanza
Set di scalabilità offrono due tipi di funzionalità di protezione istanza:

-   **Proteggere da ridimensionare**
    - Abilitati tramite **protectFromScaleIn** istanza del set di proprietà sulla scala
    - Protegge l'istanza di scale-in ha avviata la scalabilità automatica
    - Operazioni avviate dall'utente istanza (tra cui l'eliminazione di istanza) sono **non bloccato**
    - Operazioni avviate nel set di scalabilità (eseguire l'aggiornamento, ricreare l'immagine, deallocare, e così via) sono **non bloccato**

-   **Protezione dalle azioni del set di scalabilità**
    - Abilitati tramite **protectFromScaleSetActions** istanza del set di proprietà sulla scala
    - Protegge l'istanza di scale-in ha avviata la scalabilità automatica
    - Protegge l'istanza da operazioni avviate nel set di scalabilità (ad esempio l'aggiornamento, ricreare l'immagine, deallocare, ecc.)
    - Operazioni avviate dall'utente istanza (tra cui l'eliminazione di istanza) sono **non bloccato**
    - L'eliminazione del set di scalabilità completa è **non bloccato**

## <a name="protect-from-scale-in"></a>Proteggere da ridimensionare
Protezione dell'istanza può essere applicata per istanze del set di scalabilità dopo che le istanze vengono create. Protezione viene applicata e modificata solo nel [modello di istanza](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) e non per il [modello del set di scalabilità](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Esistono diversi modi per applicare la protezione di riduzione del numero su istanze del set di scalabilità come descritto in dettaglio negli esempi seguenti.

### <a name="rest-api"></a>API REST

L'esempio seguente applica protezione scale-in a un'istanza del set di scalabilità.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true
    }
  }        
}

```

> [!NOTE]
>Protezione dell'istanza è supportata solo con l'API versione 2019-03-01 e versioni successive

### <a name="azure-powershell"></a>Azure PowerShell

Usare la [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) istanza del set di cmdlet per applicare la protezione di scale-in per la scalabilità.

L'esempio seguente applica protezione scale-in a un'istanza del set di scalabilità con l'ID dell'istanza 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

Uso [az vmss update](/cli/azure/vmss#az-vmss-update) per applicare la protezione di scale-in per l'istanza del set di scalabilità.

L'esempio seguente applica protezione scale-in a un'istanza del set di scalabilità con l'ID dell'istanza 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Protezione dalle azioni del set di scalabilità
Protezione dell'istanza può essere applicata per istanze del set di scalabilità dopo che le istanze vengono create. Protezione viene applicata e modificata solo nel [modello di istanza](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) e non per il [modello del set di scalabilità](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

La protezione dalle azioni del set di scalabilità di un'istanza protegge anche l'istanza di scale-in di scalabilità automatica ha avviata.

Esistono diversi modi per applicare scalabilità nastavit protezione azioni di scalabilità di istanze del set come descritto in dettaglio negli esempi seguenti.

### <a name="rest-api"></a>API REST

Nell'esempio seguente applica la protezione dalle azioni del set di scalabilità a un'istanza del set di scalabilità.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vMScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true,
      "protectFromScaleSetActions": true
    }
  }        
}

```

> [!NOTE]
>Protezione dell'istanza è supportata solo con l'API versione 2019-03-01 e versioni successive.</br>
La protezione dalle azioni del set di scalabilità di un'istanza protegge anche l'istanza di scale-in di scalabilità automatica ha avviata. Non è possibile specificare "protectFromScaleIn": false durante l'impostazione "protectFromScaleSetActions": true

### <a name="azure-powershell"></a>Azure PowerShell

Usare la [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) cmdlet per applicare la protezione dalla scala set di azioni per l'istanza del set di scalabilità.

Nell'esempio seguente applica la protezione dalle azioni del set di scalabilità a un'istanza del set di scalabilità con l'ID dell'istanza 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

Uso [az vmss update](/cli/azure/vmss#az-vmss-update) per applicare la protezione dalle azioni del set di scalabilità per l'istanza del set di scalabilità.

Nell'esempio seguente applica la protezione dalle azioni del set di scalabilità a un'istanza del set di scalabilità con l'ID dell'istanza 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Risolvere problemi
### <a name="no-protectionpolicy-on-scale-set-model"></a>Nessun protectionPolicy nel modello del set di scalabilità
Protezione dell'istanza è applicabile solo su istanze del set di scalabilità e non nel modello del set di scalabilità.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Nessun protectionPolicy nel modello di istanza di set di scalabilità
Per impostazione predefinita, i criteri di protezione non viene applicato a un'istanza al momento della creazione.

È possibile applicare la protezione di istanza per istanze del set di scalabilità dopo che le istanze vengono create.

### <a name="not-able-to-apply-instance-protection"></a>Non è possibile applicare la protezione di istanza
Protezione dell'istanza è supportata solo con l'API versione 2019-03-01 e versioni successive. Verificare la versione dell'API in uso e aggiornare in base alle esigenze. È anche necessario aggiornare alla versione più recente di PowerShell o CLI.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [distribuire l'applicazione](virtual-machine-scale-sets-deploy-app.md) nei set di scalabilità di macchine virtuali.
