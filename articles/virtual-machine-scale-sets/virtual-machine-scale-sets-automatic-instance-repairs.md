---
title: Ripristino automatico delle istanze con i set di scalabilità di macchine virtuali di Azure
description: Informazioni su come configurare i criteri di riparazione automatica per le istanze di macchine virtuali in un set di scalabilità
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: avverma
ms.openlocfilehash: f335b0fb3396103c321d740bcf6d125e60e95086
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274814"
---
# <a name="preview-automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Anteprima: riparazioni automatiche dell'istanza per i set di scalabilità di macchine virtuali di Azure

L'abilitazione della riparazione automatica delle istanze per i set di scalabilità di macchine virtuali di Azure consente di ottenere la disponibilità elevata per le applicazioni mantenendo un set di istanze integre Se un'istanza del set di scalabilità risulta non integra come segnalato dall' [estensione dell'integrità dell'applicazione](./virtual-machine-scale-sets-health-extension.md) o dai [Probe di integrità](../load-balancer/load-balancer-custom-probe-overview.md)del servizio di bilanciamento del carico, questa funzionalità esegue automaticamente il ripristino dell'istanza eliminando l'istanza non integra e creandone una nuova per sostituirla.

> [!NOTE]
> Questa funzionalità di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Requisiti per l'utilizzo di ripristini automatici delle istanze

**Acconsenti esplicitamente all'anteprima delle riparazioni automatiche dell'istanza**

Usare l'API REST o Azure PowerShell per acconsentire esplicitamente all'anteprima per la riparazione automatica dell'istanza. Questa procedura consente di registrare la sottoscrizione per la funzionalità di anteprima. Si noti che si tratta solo di un'installazione necessaria solo una volta per l'utilizzo di questa funzionalità. Se la sottoscrizione è già registrata per l'anteprima automatica delle riparazioni dell'istanza, non è necessario eseguire di nuovo la registrazione. 

Uso dell'API REST 

1. Eseguire la registrazione per la funzionalità usando le [funzionalità-Register](/rest/api/resources/features/register) 

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview/register?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registering"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

2. Attendere alcuni minuti per la modifica *dello stato* in *registrato*. Per confermare questa operazione, è possibile usare l'API seguente.

```
GET on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registered"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

3. Una volta che lo *stato è stato* modificato in *registrato*, eseguire il comando seguente.

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2015-12-01'
```

Uso di Azure PowerShell

1. Eseguire la registrazione per la funzionalità usando il cmdlet [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) seguito da [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature)

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute

Register-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
```

2. Attendere alcuni minuti per la modifica di *RegistrationState* in *registrato*. Per confermare questa operazione, è possibile usare il cmdlet seguente.

```azurepowershell-interactive
Get-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
 ```

 La risposta deve essere la seguente.

| FeatureName                           | ProviderName            | RegistrationState       |
|---------------------------------------|-------------------------|-------------------------|
| RepairVMScaleSetInstancesPreview      | Microsoft.Compute       | Registrato              |

3. Dopo la modifica di *RegistrationState* in *registered*, eseguire il cmdlet seguente.

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute
```

**Abilitare il monitoraggio dell'integrità delle applicazioni per set di scalabilità**

Il set di scalabilità deve avere il monitoraggio dell'integrità dell'applicazione per le istanze abilitate. Questa operazione può essere eseguita usando l' [estensione di integrità dell'applicazione](./virtual-machine-scale-sets-health-extension.md) o i [Probe di integrità](../load-balancer/load-balancer-custom-probe-overview.md)del servizio di bilanciamento del carico. È possibile abilitare solo uno di questi alla volta. L'estensione per l'integrità dell'applicazione o il servizio di bilanciamento del carico esegue il ping dell'endpoint dell'applicazione configurato nelle istanze di macchina virtuale per determinare lo stato di integrità dell'applicazione. Questo stato di integrità viene usato dall'agente di orchestrazione del set di scalabilità per monitorare l'integrità dell'istanza ed eseguire le riparazioni quando richiesto.

**Configurare l'endpoint per fornire lo stato di integrità**

Prima di abilitare i criteri di ripristino automatico dell'istanza, verificare che le istanze del set di scalabilità dispongano dell'endpoint applicazione configurato per generare lo stato di integrità dell'applicazione. Quando un'istanza restituisce lo stato 200 (OK) nell'endpoint dell'applicazione, l'istanza viene contrassegnata come "integro". In tutti gli altri casi, l'istanza viene contrassegnata come "non integro", inclusi gli scenari seguenti:

- Quando all'interno delle istanze di macchina virtuale non è configurato alcun endpoint applicazione per fornire lo stato di integrità dell'applicazione
- Quando l'endpoint dell'applicazione non è configurato correttamente
- Quando l'endpoint dell'applicazione non è raggiungibile

Per le istanze contrassegnate come "non integre", le riparazioni automatiche vengono attivate dal set di scalabilità. Verificare che l'endpoint dell'applicazione sia configurato correttamente prima di abilitare i criteri di riparazione automatica per evitare le riparazioni accidentali delle istanze, mentre l'endpoint viene configurato.

**Abilita singolo gruppo di posizionamento**

Questa versione di anteprima è attualmente disponibile solo per i set di scalabilità distribuiti come singolo gruppo di posizionamento. La proprietà *singlePlacementGroup* deve essere impostata su *true* per il set di scalabilità per l'utilizzo della funzionalità di ripristino automatico delle istanze. Altre informazioni sui [gruppi di posizionamento](./virtual-machine-scale-sets-placement-groups.md#placement-groups).

**Versione API**

Il criterio di riparazione automatica è supportato per l'API di calcolo versione 2018-10-01 o successiva.

**Restrizioni per lo spostamento di risorse o sottoscrizioni**

Come parte di questa versione di anteprima, gli spostamenti di risorse o sottoscrizioni non sono attualmente supportati per i set di scalabilità quando è abilitato il criterio di riparazione automatica.

**Restrizione per i set di scalabilità di Service Fabric**

Questa funzionalità di anteprima non è attualmente supportata per i set di scalabilità di Service Fabric.

## <a name="how-do-automatic-instance-repairs-work"></a>Come funzionano le riparazioni automatiche delle istanze?

La funzionalità di ripristino automatico delle istanze si basa sul monitoraggio dello stato delle singole istanze in un set di scalabilità. Le istanze di VM in un set di scalabilità possono essere configurate per emettere lo stato di integrità dell'applicazione usando l' [estensione dell'integrità dell'applicazione](./virtual-machine-scale-sets-health-extension.md) o i [Probe di integrità del bilanciamento del carico](../load-balancer/load-balancer-custom-probe-overview.md) Se un'istanza risulta non integra, il set di scalabilità esegue un'azione di ripristino eliminando l'istanza non integra e creandone una nuova per sostituirla. Questa funzionalità può essere abilitata nel modello di set di scalabilità di macchine virtuali usando l'oggetto *automaticRepairsPolicy* .

### <a name="batching"></a>Creazione di batch

Le operazioni di ripristino automatico dell'istanza vengono eseguite in batch. In un determinato momento, non più del 5% delle istanze nel set di scalabilità viene ripristinato tramite i criteri di riparazione automatici. In questo modo è possibile evitare l'eliminazione e la ricreazione simultanee di un numero elevato di istanze, se non è stato trovato nello stesso momento.

### <a name="grace-period"></a>Periodo di tolleranza

Quando un'istanza passa attraverso un'operazione di modifica dello stato a causa di un'azione PUT, PATCH o POST eseguita sul set di scalabilità (ad esempio, la ricreazione dell'immagine, la ridistribuzione, l'aggiornamento e così via), qualsiasi azione di ripristino sull'istanza viene eseguita solo dopo l'attesa del periodo di tolleranza. Il periodo di tolleranza è la quantità di tempo per consentire all'istanza di tornare allo stato integro. Il periodo di tolleranza inizia dopo il completamento della modifica dello stato. Questo consente di evitare operazioni di ripristino prematura o accidentale. Il periodo di tolleranza viene rispettato per tutte le istanze appena create nel set di scalabilità (incluso quello creato come risultato dell'operazione di ripristino). Il periodo di tolleranza viene specificato in minuti nel formato ISO 8601 e può essere impostato utilizzando la proprietà *automaticRepairsPolicy. gracePeriod*. Il periodo di tolleranza può essere compreso tra 30 minuti e 90 minuti e il valore predefinito è 30 minuti.

Il processo di ripristino automatico delle istanze funziona nel modo seguente:

1. I [Probe](../load-balancer/load-balancer-custom-probe-overview.md) di integrità [dell'](./virtual-machine-scale-sets-health-extension.md) applicazione o del servizio di bilanciamento del carico effettuano il ping dell'endpoint dell'applicazione in ogni macchina virtuale nel set di scalabilità per ottenere lo stato di integrità dell'applicazione per ogni istanza.
2. Se l'endpoint risponde con lo stato 200 (OK), l'istanza viene contrassegnata come "integro". In tutti gli altri casi (incluso se l'endpoint non è raggiungibile), l'istanza viene contrassegnata come "non integro".
3. Quando un'istanza viene rilevata come non integra, il set di scalabilità attiva un'azione di ripristino eliminando l'istanza non integra e creandone una nuova per sostituirla.
4. Le riparazioni dell'istanza vengono eseguite in batch. In un determinato momento, non vengono ripristinate più del 5% delle istanze totali del set di scalabilità. Se un set di scalabilità ha meno di 20 istanze, le riparazioni vengono eseguite per un'istanza non integra alla volta.
5. Il processo precedente continua fino a quando non viene ripristinata tutte le istanze non integre del set di scalabilità.

## <a name="instance-protection-and-automatic-repairs"></a>Protezione delle istanze e riparazioni automatiche

Se un'istanza di un set di scalabilità è protetta applicando il *[criterio di protezione da azioni del set di scalabilità](./virtual-machine-scale-sets-instance-protection.md#protect-from-scale-set-actions)* , le riparazioni automatiche non vengono eseguite su tale istanza.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Abilitazione dei criteri di riparazione automatica quando si crea un nuovo set di scalabilità

Per abilitare i criteri di riparazione automatica durante la creazione di un nuovo set di scalabilità, assicurarsi che siano soddisfatti tutti i [requisiti](#requirements-for-using-automatic-instance-repairs) per la scelta di questa funzionalità. L'endpoint dell'applicazione deve essere configurato correttamente per le istanze del set di scalabilità, in modo da evitare l'attivazione di riparazioni accidentali durante la configurazione dell'endpoint. Per i set di scalabilità appena creati, le riparazioni di istanze vengono eseguite solo dopo l'attesa della durata del periodo di tolleranza. Per abilitare la correzione automatica dell'istanza in un set di scalabilità, usare l'oggetto *automaticRepairsPolicy* nel modello del set di scalabilità di macchine virtuali.

### <a name="rest-api"></a>API REST

Nell'esempio seguente viene illustrato come abilitare la correzione automatica dell'istanza in un modello di set di scalabilità. Usare l'API versione 2018-10-01 o successiva.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

La funzionalità di ripristino automatico delle istanze può essere abilitata durante la creazione di un nuovo set di scalabilità tramite il cmdlet [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) . Questo script di esempio illustra la creazione di un set di scalabilità e delle risorse associate usando il file di configurazione: [creare un set di scalabilità di macchine virtuali completo](./scripts/powershell-sample-create-complete-scale-set.md). È possibile configurare i criteri di ripristino automatico delle istanze aggiungendo i parametri *EnableAutomaticRepair* e *AutomaticRepairGracePeriod* all'oggetto di configurazione per la creazione del set di scalabilità. Nell'esempio seguente viene abilitata la funzionalità con un periodo di tolleranza di 30 minuti.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Abilitazione dei criteri di riparazione automatica quando si aggiorna un set di scalabilità esistente

Prima di abilitare i criteri di riparazione automatica in un set di scalabilità esistente, assicurarsi che siano soddisfatti tutti i [requisiti](#requirements-for-using-automatic-instance-repairs) per la scelta di questa funzionalità. L'endpoint dell'applicazione deve essere configurato correttamente per le istanze del set di scalabilità, in modo da evitare l'attivazione di riparazioni accidentali durante la configurazione dell'endpoint. Per abilitare la correzione automatica dell'istanza in un set di scalabilità, usare l'oggetto *automaticRepairsPolicy* nel modello del set di scalabilità di macchine virtuali.

### <a name="rest-api"></a>API REST

Nell'esempio seguente viene abilitato il criterio con un periodo di tolleranza di 40 minuti. Usare l'API versione 2018-10-01 o successiva.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Usare il cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) per modificare la configurazione della funzionalità di ripristino automatico delle istanze in un set di scalabilità esistente. Nell'esempio seguente viene aggiornato il periodo di tolleranza a 40 minuti.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

## <a name="troubleshoot"></a>Risolvere problemi

**Errore di abilitazione dei criteri di riparazione automatica**

Se viene visualizzato un errore "richiesta non valida" con un messaggio che indica che "non è stato possibile trovare il membro ' automaticRepairsPolicy ' nell'oggetto di tipo ' Properties '", controllare la versione dell'API usata per il set di scalabilità di macchine virtuali. Per questa funzionalità è necessaria l'API versione 2018-10-01 o successiva.

**L'istanza non viene ripristinata anche quando è abilitato il criterio**

L'istanza potrebbe essere nel periodo di tolleranza. Si tratta dell'intervallo di tempo di attesa dopo qualsiasi modifica dello stato nell'istanza prima di eseguire le riparazioni. Questo consente di evitare eventuali riparazioni premature o accidentali. L'azione di ripristino dovrebbe verificarsi una volta completato il periodo di tolleranza per l'istanza.

**Visualizzazione dello stato di integrità dell'applicazione per istanze del set di scalabilità**

È possibile usare l' [API visualizzazione dell'istanza](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) per le istanze in un set di scalabilità di macchine virtuali per visualizzare lo stato di integrità dell'applicazione. Con Azure PowerShell, è possibile usare il cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) con il flag *-InstanceView* . Lo stato di integrità dell'applicazione viene fornito sotto la proprietà *vmHealth*.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare l' [estensione integrità dell'applicazione](./virtual-machine-scale-sets-health-extension.md) o i [Probe di integrità](../load-balancer/load-balancer-custom-probe-overview.md) del servizio di bilanciamento del carico per i set di scalabilità.
