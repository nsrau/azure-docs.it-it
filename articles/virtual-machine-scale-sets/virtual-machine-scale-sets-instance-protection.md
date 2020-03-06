---
title: Protezione dell'istanza per le istanze del set di scalabilità di macchine virtuali di Azure
description: Informazioni su come proteggere le istanze del set di scalabilità di macchine virtuali di Azure dalle operazioni di scalabilità e impostazione del set di scalabilità.
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 021faad28fb575c4ffeb4d895ad451d8cd82b1a5
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78392911"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Protezione dell'istanza per le istanze del set di scalabilità di macchine virtuali di Azure

I set di scalabilità di macchine virtuali di Azure consentono una migliore elasticità per i carichi di lavoro tramite la [scalabilità](virtual-machine-scale-sets-autoscale-overview.md)automatica, per consentirti di configurare la scalabilità orizzontale dell'infrastruttura e il ridimensionamento. I set di scalabilità consentono inoltre di gestire, configurare e aggiornare in modo centralizzato un numero elevato di macchine virtuali tramite diverse impostazioni dei [criteri di aggiornamento](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) . È possibile configurare un aggiornamento sul modello del set di scalabilità e la nuova configurazione viene applicata automaticamente a ogni istanza del set di scalabilità se il criterio di aggiornamento è stato impostato su automatico o in sequenza.

Quando l'applicazione elabora il traffico, possono essere presenti situazioni in cui si desidera che istanze specifiche vengano trattate in modo diverso rispetto al resto dell'istanza del set di scalabilità. Ad esempio, alcune istanze nel set di scalabilità possono eseguire operazioni a esecuzione prolungata e non si vuole che queste istanze vengano ridimensionate fino al completamento delle operazioni. È anche possibile specializzare alcune istanze nel set di scalabilità per eseguire attività aggiuntive o diverse da quelle degli altri membri del set di scalabilità. È necessario che le macchine virtuali "speciali" non vengano modificate con le altre istanze nel set di scalabilità. La protezione dell'istanza fornisce i controlli aggiuntivi per abilitare questi e altri scenari per l'applicazione.

Questo articolo descrive come applicare e usare le diverse funzionalità di protezione dell'istanza con le istanze del set di scalabilità.

## <a name="types-of-instance-protection"></a>Tipi di protezione dell'istanza
I set di scalabilità forniscono due tipi di funzionalità di protezione dell'istanza:

-   **Proteggi dalla scalabilità**
    - Abilitata tramite la proprietà **protectFromScaleIn** nell'istanza del set di scalabilità
    - Protegge l'istanza dalla scalabilità avviata con scalabilità automatica
    - Le operazioni dell'istanza avviate dall'utente (inclusa l'eliminazione dell'istanza) **non sono bloccate**
    - Le operazioni avviate nel set di scalabilità (aggiornamento, ricreazione dell'immagine, deallocazione e così via) **non sono bloccate**

-   **Proteggi da azioni del set di scalabilità**
    - Abilitata tramite la proprietà **protectFromScaleSetActions** nell'istanza del set di scalabilità
    - Protegge l'istanza dalla scalabilità avviata con scalabilità automatica
    - Protegge l'istanza dalle operazioni avviate nel set di scalabilità, ad esempio l'aggiornamento, la ricreazione dell'immagine, la deallocazione e così via.
    - Le operazioni dell'istanza avviate dall'utente (inclusa l'eliminazione dell'istanza) **non sono bloccate**
    - L'eliminazione del set di scalabilità completo **non è bloccata**

## <a name="protect-from-scale-in"></a>Proteggi dalla scalabilità
La protezione dell'istanza può essere applicata alle istanze del set di scalabilità dopo la creazione delle istanze. La protezione viene applicata e modificata solo sul [modello di istanza](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) e non sul [modello del set di scalabilità](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Esistono diversi modi per applicare la protezione con scalabilità in istanze del set di scalabilità, come descritto negli esempi seguenti.

### <a name="azure-portal"></a>Portale di Azure

È possibile applicare la protezione con scalabilità orizzontale tramite la portale di Azure a un'istanza nel set di scalabilità. Non è possibile modificare più di un'istanza alla volta. Ripetere i passaggi per ogni istanza che si desidera proteggere.
 
1. Passare a un set di scalabilità di macchine virtuali esistente.
1. Selezionare **istanze** dal menu a sinistra, in **Impostazioni**.
1. Selezionare il nome dell'istanza che si desidera proteggere.
1. Selezionare la scheda **criteri di protezione** .
1. Nel pannello **criteri di protezione** selezionare l'opzione **Proteggi da scalabilità** .
1. Selezionare **Salva**. 

### <a name="rest-api"></a>API REST

Nell'esempio seguente viene applicata la protezione con scalabilità in un'istanza del set di scalabilità.

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
>La protezione dell'istanza è supportata solo con l'API versione 2019-03-01 e successive

### <a name="azure-powershell"></a>Azure PowerShell

Usare il cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) per applicare la protezione con scalabilità orizzontale all'istanza del set di scalabilità.

Nell'esempio seguente viene applicata la protezione con scalabilità in un'istanza del set di scalabilità con ID istanza 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

Usare il comando [AZ vmss Update](/cli/azure/vmss#az-vmss-update) per applicare la protezione con scalabilità orizzontale all'istanza del set di scalabilità.

Nell'esempio seguente viene applicata la protezione con scalabilità in un'istanza del set di scalabilità con ID istanza 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Proteggi da azioni del set di scalabilità
La protezione dell'istanza può essere applicata alle istanze del set di scalabilità dopo la creazione delle istanze. La protezione viene applicata e modificata solo sul [modello di istanza](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) e non sul [modello del set di scalabilità](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

La protezione di un'istanza dalle azioni del set di scalabilità protegge anche l'istanza dalla scalabilità avviata con scalabilità automatica.

Esistono diversi modi per applicare la protezione delle azioni dei set di scalabilità nelle istanze del set di scalabilità, come descritto negli esempi seguenti.

### <a name="azure-portal"></a>Portale di Azure

È possibile applicare la protezione dalle azioni del set di scalabilità tramite il portale di Azure a un'istanza nel set di scalabilità. Non è possibile modificare più di un'istanza alla volta. Ripetere i passaggi per ogni istanza che si desidera proteggere.
 
1. Passare a un set di scalabilità di macchine virtuali esistente.
1. Selezionare **istanze** dal menu a sinistra, in **Impostazioni**.
1. Selezionare il nome dell'istanza che si desidera proteggere.
1. Selezionare la scheda **criteri di protezione** .
1. Nel pannello **criteri di protezione** selezionare l'opzione **Proteggi da azioni set di scalabilità** .
1. Selezionare **Salva**. 

### <a name="rest-api"></a>API REST

Nell'esempio seguente viene applicata la protezione dalle azioni del set di scalabilità a un'istanza nel set di scalabilità.

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
>La protezione dell'istanza è supportata solo con l'API versione 2019-03-01 e successive.</br>
La protezione di un'istanza dalle azioni del set di scalabilità protegge anche l'istanza dalla scalabilità avviata con scalabilità automatica. Non è possibile specificare "protectFromScaleIn": false quando si imposta "protectFromScaleSetActions": true

### <a name="azure-powershell"></a>Azure PowerShell

Usare il cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) per applicare la protezione dalle azioni del set di scalabilità all'istanza del set di scalabilità.

Nell'esempio seguente viene applicata la protezione dalle azioni del set di scalabilità a un'istanza nel set di scalabilità con ID istanza 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

Usare il comando [AZ vmss Update](/cli/azure/vmss#az-vmss-update) per applicare la protezione dalle azioni del set di scalabilità all'istanza del set di scalabilità.

Nell'esempio seguente viene applicata la protezione dalle azioni del set di scalabilità a un'istanza nel set di scalabilità con ID istanza 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Risolvere problemi
### <a name="no-protectionpolicy-on-scale-set-model"></a>Nessun protectionPolicy sul modello del set di scalabilità
La protezione dell'istanza è applicabile solo alle istanze del set di scalabilità e non al modello del set di scalabilità.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Nessun protectionPolicy sul modello di istanza del set di scalabilità
Per impostazione predefinita, i criteri di protezione non vengono applicati a un'istanza di al momento della creazione.

È possibile applicare la protezione dell'istanza alle istanze del set di scalabilità dopo la creazione delle istanze.

### <a name="not-able-to-apply-instance-protection"></a>Non è possibile applicare la protezione dell'istanza
La protezione dell'istanza è supportata solo con l'API versione 2019-03-01 e successive. Controllare la versione dell'API in uso e aggiornarla secondo le esigenze. Potrebbe anche essere necessario aggiornare PowerShell o l'interfaccia della riga di comando alla versione più recente.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [distribuire l'applicazione](virtual-machine-scale-sets-deploy-app.md) nei set di scalabilità di macchine virtuali.
