---
title: Protezione delle istanze per le istanze del set di scalabilità delle macchine virtuali di AzureInstance Protection for Azure virtual machine scale set instances
description: Informazioni su come proteggere le istanze del set di scalabilità delle macchine virtuali di Azure dalle operazioni di scalabilità verticale e di scalabilità orizzontale.
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 021faad28fb575c4ffeb4d895ad451d8cd82b1a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254118"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Protezione delle istanze per le istanze del set di scalabilità delle macchine virtuali di AzureInstance Protection for Azure virtual machine scale set instances

I set di scalabilità delle macchine virtuali di Azure consentono una migliore elasticità per i carichi di lavoro tramite [la scalabilità automatica,](virtual-machine-scale-sets-autoscale-overview.md)in modo da poter configurare la scalabilità orizzontale dell'infrastruttura e la scalabilità verticale. I set di scalabilità consentono inoltre di gestire, configurare e aggiornare centralmente un numero elevato di macchine virtuali tramite impostazioni dei criteri di [aggiornamento](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) diverse. È possibile configurare un aggiornamento nel modello del set di scalabilità e la nuova configurazione viene applicata automaticamente a ogni istanza del set di scalabilità se i criteri di aggiornamento sono stati impostati su Automatico o Roll.

Durante l'elaborazione del traffico da parte dell'applicazione, possono esserci situazioni in cui si desidera che istanze specifiche vengano trattate in modo diverso dal resto dell'istanza del set di scalabilità. Ad esempio, alcune istanze nel set di scalabilità potrebbero eseguire operazioni a esecuzione prolungata e non si desidera che queste istanze vengano ridimensionate fino al completamento delle operazioni. Potresti anche avere alcune istanze specializzate nel set di scalabilità per eseguire attività aggiuntive o diverse rispetto agli altri membri del set di scalabilità. È necessario che queste macchine virtuali 'speciali' non vengano modificate con le altre istanze nel set di scalabilità. La protezione dell'istanza fornisce i controlli aggiuntivi per abilitare questi e altri scenari per l'applicazione.

In questo articolo viene descritto come applicare e usare le diverse funzionalità di protezione delle istanze con le istanze del set di scalabilità.

## <a name="types-of-instance-protection"></a>Tipi di protezione dell'istanza
I set di scalabilità offrono due tipi di funzionalità di protezione delle istanze:Scale sets provide two types of instance protection capabilities:

-   **Proteggere dalla scalabilità verticale**
    - Abilitato tramite la proprietà **protectFromScaleIn** nell'istanza del set di scalabilità
    - Protegge l'istanza dalla scalabilità verticale di scalabilità automatica
    - Le operazioni dell'istanza avviate dall'utente (inclusa l'eliminazione dell'istanza) non sono **bloccate**
    - Le operazioni avviate sul set di scalabilità (aggiornamento, riimmagine, deallocazione e così via) non sono **bloccate**

-   **Proteggere dalle azioni del set di scalabilitàProtect from scale set actions**
    - Abilitato tramite la proprietà **protectFromScaleSetActions** nell'istanza del set di scalabilità
    - Protegge l'istanza dalla scalabilità verticale di scalabilità automatica
    - Protegge l'istanza dalle operazioni avviate nel set di scalabilità (ad esempio aggiornamento, ricreazione dell'immagine, deallocazione e così via).
    - Le operazioni dell'istanza avviate dall'utente (inclusa l'eliminazione dell'istanza) non sono **bloccate**
    - L'eliminazione dell'intero set di **scalabilità non** è bloccata

## <a name="protect-from-scale-in"></a>Proteggere dalla scalabilità verticale
La protezione dell'istanza può essere applicata alle istanze del set di scalabilità dopo la creazione delle istanze. La protezione viene applicata e modificata solo nel modello di [istanza](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) e non nel modello del set di [scalabilità.](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)

Esistono diversi modi per applicare la protezione della scalabilità orizzontale nelle istanze del set di scalabilità, come descritto negli esempi seguenti.

### <a name="azure-portal"></a>Portale di Azure

È possibile applicare la protezione con scalabilità orizzontale tramite il portale di Azure a un'istanza nel set di scalabilità. Non è possibile regolare più di un'istanza alla volta. Ripetere i passaggi per ogni istanza che si desidera proteggere.
 
1. Passare a un set di scalabilità di macchine virtuali esistente.
1. Selezionare **Istanze** dal menu a sinistra, in **Impostazioni**.
1. Selezionare il nome dell'istanza da proteggere.
1. Selezionare la scheda **Criterio di protezione.**
1. Nel pannello Criteri di **protezione** selezionare l'opzione **Proteggi da scalabilità verticale.**
1. Selezionare **Salva**. 

### <a name="rest-api"></a>API REST

Nell'esempio seguente viene applicata la protezione con scalabilità orizzontale a un'istanza nel set di scalabilità.

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
>La protezione dell'istanza è supportata solo con la versione API 2019-03-01 e versioni successive

### <a name="azure-powershell"></a>Azure PowerShell

Utilizzare il cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) per applicare la protezione con scalabilità orizzontale all'istanza del set di scalabilità.

L'esempio seguente applica la protezione con scalabilità orizzontale a un'istanza nel set di scalabilità con ID istanza 0.The following example applies scale-in protection to an instance in the scale set having instance ID 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

Usare [az vmss update](/cli/azure/vmss#az-vmss-update) per applicare la protezione con scalabilità orizzontale all'istanza del set di scalabilità.

L'esempio seguente applica la protezione con scalabilità orizzontale a un'istanza nel set di scalabilità con ID istanza 0.The following example applies scale-in protection to an instance in the scale set having instance ID 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Proteggere dalle azioni del set di scalabilitàProtect from scale set actions
La protezione dell'istanza può essere applicata alle istanze del set di scalabilità dopo la creazione delle istanze. La protezione viene applicata e modificata solo nel modello di [istanza](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) e non nel modello del set di [scalabilità.](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)

La protezione di un'istanza dalle azioni del set di scalabilità protegge anche l'istanza dalla scalabilità verticale di scalabilità automatica.

Esistono diversi modi per applicare la protezione delle azioni del set di scalabilità nelle istanze del set di scalabilità, come descritto negli esempi seguenti.

### <a name="azure-portal"></a>Portale di Azure

È possibile applicare la protezione dalle azioni del set di scalabilità tramite il portale di Azure a un'istanza nel set di scalabilità. Non è possibile regolare più di un'istanza alla volta. Ripetere i passaggi per ogni istanza che si desidera proteggere.
 
1. Passare a un set di scalabilità di macchine virtuali esistente.
1. Selezionare **Istanze** dal menu a sinistra, in **Impostazioni**.
1. Selezionare il nome dell'istanza da proteggere.
1. Selezionare la scheda **Criterio di protezione.**
1. Nel pannello Criteri di **protezione** selezionare l'opzione Proteggi da azioni set di **scalabilità.**
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
>La protezione dell'istanza è supportata solo con la versione API 2019-03-01 e successive.</br>
La protezione di un'istanza dalle azioni del set di scalabilità protegge anche l'istanza dalla scalabilità verticale di scalabilità automatica. Non è possibile specificare "protectFromScaleIn": false quando si imposta "protectFromScaleSetActions": true

### <a name="azure-powershell"></a>Azure PowerShell

Utilizzare il cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) per applicare la protezione dalle azioni del set di scalabilità all'istanza del set di scalabilità.

L'esempio seguente applica la protezione dalle azioni del set di scalabilità a un'istanza nel set di scalabilità con ID istanza 0.The following example applies protection from scale set actions to an instance in the scale set having instance ID 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

Usare [az vmss update](/cli/azure/vmss#az-vmss-update) per applicare la protezione dalle azioni del set di scalabilità all'istanza del set di scalabilità.

L'esempio seguente applica la protezione dalle azioni del set di scalabilità a un'istanza nel set di scalabilità con ID istanza 0.The following example applies protection from scale set actions to an instance in the scale set having instance ID 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Risolvere i problemi
### <a name="no-protectionpolicy-on-scale-set-model"></a>Nessuna protezionePolicy sul modello di set di scalabilitàNo protectionPolicy on scale set model
La protezione dell'istanza è applicabile solo nelle istanze del set di scalabilità e non nel modello del set di scalabilità.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Nessun protectionPolicy nel modello di istanza del set di scalabilitàNo protectionPolicy on scale set instance model
Per impostazione predefinita, i criteri di protezione non vengono applicati a un'istanza quando vengono creati.

È possibile applicare la protezione dell'istanza alle istanze del set di scalabilità dopo la creazione delle istanze.

### <a name="not-able-to-apply-instance-protection"></a>Impossibile applicare la protezione dell'istanza
La protezione dell'istanza è supportata solo con la versione API 2019-03-01 e successive. Controllare la versione dell'API in uso e aggiornare in base alle esigenze. Potrebbe anche essere necessario aggiornare PowerShell o l'interfaccia della riga di comando alla versione più recente.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [distribuire l'applicazione](virtual-machine-scale-sets-deploy-app.md) nei set di scalabilità di macchine virtuali.
