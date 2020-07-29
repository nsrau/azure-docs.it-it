---
title: Protezione delle istanze per il set di scalabilità di macchine virtuali di Azure
description: Informazioni su come proteggere le istanze del set di scalabilità di macchine virtuali di Azure da operazioni di riduzione e scalabilità del set.
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: 8c4944da8ffcaa75e6448483918a29809c32830b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124058"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Protezione delle istanze per il set di scalabilità di macchine virtuali di Azure

I set di scalabilità di macchine virtuali di Azure consentono una migliore elasticità per i carichi di lavoro sfruttando la [scalabilità automatica](virtual-machine-scale-sets-autoscale-overview.md), in modo che sia possibile configurare quando effettuare lo scale-out o la riduzione per l’infrastruttura. I set di scalabilità consentono inoltre di gestire, configurare e aggiornare in modo centralizzato un numero elevato di macchine virtuali tramite diverse impostazioni dei [criteri di aggiornamento](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model). È possibile configurare un aggiornamento del modello del set di scalabilità. Se il criterio di aggiornamento è stato impostato come automatico o continuo, la nuova configurazione viene applicata automaticamente a ogni istanza del set di scalabilità.

Quando l'applicazione elabora il traffico, possono verificarsi situazioni in cui si desidera che istanze specifiche vengano trattate in modo diverso rispetto al resto delle istanze del set di scalabilità. Ad esempio, alcune istanze nel set di scalabilità potrebbero eseguire operazioni con esecuzione prolungata ed è possibile che non si desideri che queste istanze vengano ridimensionate fino al completamento delle operazioni. È anche possibile che si desideri specializzare alcune istanze del set di scalabilità per eseguire attività aggiuntive o diverse da quelle degli altri membri del set di scalabilità. È necessario che le macchine virtuali "speciali" non vengano modificate con le altre istanze nel set di scalabilità. La protezione dell'istanza fornisce controlli aggiuntivi per gestire questi e altri scenari per l'applicazione.

Questo articolo descrive come applicare e usare le diverse funzionalità di protezione dell'istanza con le istanze del set di scalabilità.

## <a name="types-of-instance-protection"></a>Tipi di protezione delle istanze
I set di scalabilità offrono due tipi di funzionalità di protezione delle istanze:

-   **Proteggi dalla riduzione**
    - Attivata tramite la proprietà **protectFromScaleIn** nell’istanza del set di scalabilità
    - Protegge l'istanza dalla riduzione attivata dalla scalabilità automatica
    - Le operazioni dell'istanza avviate dall'utente (inclusa l'eliminazione dell'istanza) **non vengono bloccate**
    - Le operazioni avviate nel set di scalabilità (aggiornamento, ricreazione dell'immagine, deallocazione e così via) **non vengono bloccate**

-   **Proteggi dalle azioni del set di scalabilità**
    - Attivata tramite la proprietà **protectFromScaleSetActions** nell’istanza del set di scalabilità
    - Protegge l'istanza dalla riduzione attivata dalla scalabilità automatica
    - Protegge le istanze dalle operazioni avviate nel set di scalabilità (aggiornamento, ricreazione dell'immagine, deallocazione e così via)
    - Le operazioni dell'istanza avviate dall'utente (inclusa l'eliminazione dell'istanza) **non vengono bloccate**
    - L'eliminazione del set di scalabilità completo **non viene bloccata**

## <a name="protect-from-scale-in"></a>Proteggi dalla riduzione
La protezione dell'istanza può essere applicata alle istanze del set di scalabilità dopo la creazione delle istanze. La protezione viene applicata e modificata solo per il [modello di istanza](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) e non per il [modello del set di scalabilità](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Esistono diversi modi per applicare la protezione dalla riduzione alle istanze del set di scalabilità, come descritto negli esempi seguenti.

### <a name="azure-portal"></a>Portale di Azure

È possibile applicare la protezione dalla riduzione a un'istanza del set di scalabilità tramite il portale di Azure. Non è possibile modificare più di un'istanza alla volta. Ripetere questi passaggi per ogni istanza da proteggere.
 
1. Passare a un set di scalabilità di macchine virtuali esistente.
1. Selezionare **Istanze** dal menu a sinistra nelle **Impostazioni**.
1. Selezionare il nome dell’istanza da proteggere.
1. Selezionare la scheda **Criteri di protezione**.
1. Nel pannello **Criteri di protezione** selezionare l'opzione **Proteggi da riduzione**.
1. Selezionare **Salva**. 

### <a name="rest-api"></a>API REST

Nell'esempio seguente viene applicata la protezione con scalabilità da riduzione in un'istanza del set di scalabilità.

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
>La protezione dell'istanza è supportata solo con la versione 2019-03-01 dell’API e versioni successive.

### <a name="azure-powershell"></a>Azure PowerShell

Usare il cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) per applicare la protezione dalla riduzione all'istanza del set di scalabilità.

Nell'esempio seguente viene applicata la protezione da riduzione in un'istanza del set di scalabilità con ID istanza 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

Usare [az vmss update](/cli/azure/vmss#az-vmss-update) per applicare la protezione da riduzione all'istanza del set di scalabilità.

Nell'esempio seguente viene applicata la protezione da riduzione in un'istanza del set di scalabilità con ID istanza 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Proteggi dalle azioni del set di scalabilità
La protezione dell'istanza può essere applicata alle istanze del set di scalabilità dopo la loro creazione. La protezione viene applicata e modificata solo per il [modello di istanza](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) e non per il [modello del set di scalabilità](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

La protezione di un'istanza dalle azioni del set di scalabilità protegge l'istanza anche dalla riduzione attivata dalla scalabilità automatica.

Esistono diversi modi per applicare la protezione dalle azioni del set di scalabilità nelle istanze del set di scalabilità, come descritto negli esempi seguenti.

### <a name="azure-portal"></a>Portale di Azure

È possibile applicare la protezione dalle azioni del set di scalabilità tramite il portale di Azure. Non è possibile modificare più di un'istanza alla volta. Ripetere questi passaggi per ogni istanza da proteggere.
 
1. Passare a un set di scalabilità di macchine virtuali esistente.
1. Selezionare **Istanze** dal menu a sinistra nelle **Impostazioni**.
1. Selezionare il nome dell’istanza da proteggere.
1. Selezionare la scheda **Criteri di protezione**.
1. Nel pannello **Criteri di protezione** selezionare l'opzione **Proteggi dalle azioni del set di scalabilità**.
1. Selezionare **Salva**. 

### <a name="rest-api"></a>API REST

Nell'esempio seguente viene applicata la protezione dalle azioni del set di scalabilità a un'istanza del set di scalabilità.

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
>La protezione dell'istanza è supportata solo con la versione 2019-03-01 dell’API e versioni successive.</br>
La protezione di un'istanza dalle azioni del set di scalabilità protegge l'istanza anche dalla riduzione attivata dalla scalabilità automatica. Non è possibile specificare "protectFromScaleIn" come false quando si imposta "protectFromScaleSetActions" come true

### <a name="azure-powershell"></a>Azure PowerShell

Usare il cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) per applicare la protezione dalle azioni del set di scalabilità all'istanza del set di scalabilità.

Nell'esempio seguente viene applicata la protezione dalle azioni del set di scalabilità a un'istanza del set di scalabilità con ID istanza 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

Usare il cmdlet [az vmss update](/cli/azure/vmss#az-vmss-update) per applicare la protezione dalle azioni del set di scalabilità all'istanza del set di scalabilità.

Nell'esempio seguente viene applicata la protezione dalle azioni del set di scalabilità a un'istanza del set di scalabilità con ID istanza 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Risolvere problemi
### <a name="no-protectionpolicy-on-scale-set-model"></a>Nessun protectionPolicy per il modello del set di scalabilità
La protezione dell'istanza è applicabile solo alle istanze del set di scalabilità e non al modello del set di scalabilità.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Nessun protectionPolicy per il modello di istanza del set di scalabilità
Per impostazione predefinita, i criteri di protezione non vengono applicati a un'istanza al momento della creazione.

La protezione dell'istanza può essere applicata alle istanze del set di scalabilità dopo la loro creazione.

### <a name="not-able-to-apply-instance-protection"></a>Non è possibile applicare la protezione dell'istanza
La protezione dell'istanza è supportata solo con la versione 2019-03-01 dell’API e versioni successive. Controllare la versione dell'API in uso e aggiornarla come necessario. Potrebbe essere necessario aggiornare anche PowerShell o l'interfaccia della riga di comando alla versione più recente.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [distribuire l'applicazione](virtual-machine-scale-sets-deploy-app.md) nei set di scalabilità di macchine virtuali.
