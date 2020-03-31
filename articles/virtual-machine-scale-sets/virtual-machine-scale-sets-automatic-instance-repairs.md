---
title: Riparazioni automatiche delle istanze con set di scalabilità di macchine virtuali di AzureAutomatic instance repairs with Azure virtual machine scale sets
description: Informazioni su come configurare i criteri di ripristino automatico per le istanze di macchine virtuali in un set di scalabilitàLearn how to configure automatic repairs policy for VM instances in a scale set
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274814"
---
# <a name="preview-automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Anteprima: Riparazioni automatiche delle istanze per i set di scalabilità delle macchine virtuali di AzurePreview: Automatic instance repairs for Azure virtual machine scale sets

L'abilitazione delle riparazioni automatiche delle istanze per i set di scalabilità delle macchine virtuali di Azure consente di ottenere un'elevata disponibilità per le applicazioni mantenendo un set di istanze integre. Se un'istanza nel set di scalabilità risulta non integra come riportato [dall'estensione dell'integrità dell'applicazione](./virtual-machine-scale-sets-health-extension.md) o dai probe di integrità del [bilanciamento](../load-balancer/load-balancer-custom-probe-overview.md)del carico, questa funzionalità esegue automaticamente il ripristino dell'istanza eliminando l'istanza non integra e creandouna nuova per sostituirla.

> [!NOTE]
> Questa funzionalità di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Requisiti per l'utilizzo delle riparazioni automatiche delle istanze

**Opt-in per l'anteprima di riparazioni automatiche dell'istanza**

Usare l'API REST o Azure PowerShell per attivare l'anteprima delle riparazioni automatiche dell'istanza. Questi passaggi registreranno l'abbonamento per la funzionalità di anteprima. Si noti che si tratta solo di una configurazione una tantera necessaria per l'utilizzo di questa funzionalità. Se la sottoscrizione è già registrata per l'anteprima delle riparazioni automatiche delle istanze, non è necessario registrarsi nuovamente. 

Uso dell'API REST 

1. Registrati per la funzione utilizzando [Le funzioni - Registrati](/rest/api/resources/features/register) 

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

2. Attendere alcuni minuti che lo *Stato* cambi *su Registrato*. È possibile utilizzare la seguente API per confermarlo.

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

3. Una volta che *lo Stato* è stato modificato in *Registrato*, eseguire quanto segue.

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2015-12-01'
```

Uso di Azure PowerShell

1. Eseguire la registrazione per la funzionalità utilizzando il cmdlet [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) seguito da [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature)

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute

Register-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
```

2. Attendere alcuni minuti che *RegistrationState* cambi su *Registered*. È possibile utilizzare il cmdlet seguente per confermarlo.

```azurepowershell-interactive
Get-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
 ```

 La risposta dovrebbe essere la seguente.

| NomeFunzionalità                           | ProviderName            | RegistrazioneStato       |
|---------------------------------------|-------------------------|-------------------------|
| RepairVMScaleSetInstancesPreview      | Microsoft.Compute       | Registrato              |

3. Una volta che il *RegistrationState* per passare a *Registered*, quindi eseguire il seguente cmdlet.

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute
```

**Abilitare il monitoraggio dell'integrità delle applicazioni per il set di scalabilitàEnable application health monitoring for scale set**

Il set di scalabilità deve avere il monitoraggio dell'integrità dell'applicazione per le istanze abilitate. Questa operazione può essere eseguita usando [l'estensione dell'integrità dell'applicazione](./virtual-machine-scale-sets-health-extension.md) o i probe di integrità del servizio di bilanciamento del [carico.](../load-balancer/load-balancer-custom-probe-overview.md) Solo uno di questi può essere abilitato alla volta. L'estensione per l'integrità dell'applicazione o il servizio di bilanciamento del carico esamina il ping dell'endpoint dell'applicazione configurato nelle istanze della macchina virtuale per determinare lo stato di integrità dell'applicazione. Questo stato di integrità viene utilizzato dall'agente di orchestrazione del set di scalabilità per monitorare l'integrità dell'istanza ed eseguire riparazioni quando necessario.

**Configurare l'endpoint per fornire lo stato di integritàConfigure endpoint to provide health status**

Prima di abilitare i criteri di riparazione automatica delle istanze, verificare che per le istanze del set di scalabilità sia configurato l'endpoint dell'applicazione per generare lo stato di integrità dell'applicazione. Quando un'istanza restituisce lo stato 200 (OK) su questo endpoint dell'applicazione, l'istanza viene contrassegnata come "Integro". In tutti gli altri casi, l'istanza è contrassegnata come "Non integro", inclusi gli scenari seguenti:In all other cases, the instance is marked "Unhealthy", including the following scenarios:

- Quando non è presente alcun endpoint applicazione configurato all'interno delle istanze della macchina virtuale per fornire lo stato di integrità dell'applicazioneWhen there is no application endpoint configured inside the virtual machine instances to provide application health status
- Quando l'endpoint dell'applicazione non è configurato correttamente
- Quando l'endpoint dell'applicazione non è raggiungibile

Per le istanze contrassegnate come "Non integre", le riparazioni automatiche vengono attivate dal set di scalabilità. Verificare che l'endpoint dell'applicazione sia configurato correttamente prima di abilitare i criteri di riparazione automatica per evitare il ripristino indesiderato delle istanze, mentre l'endpoint viene configurato.

**Abilita singolo gruppo di posizionamento**

Questa anteprima è attualmente disponibile solo per i set di scalabilità distribuiti come singolo gruppo di posizionamento. La proprietà *singlePlacementGroup* deve essere impostata su *true* per impostare la scalabilità per utilizzare la funzionalità di ripristino automatico delle istanze. Ulteriori informazioni sui gruppi di [posizionamento](./virtual-machine-scale-sets-placement-groups.md#placement-groups).

**Versione API**

I criteri di riparazione automatica sono supportati per la versione 2018-10-01 dell'API di calcolo.

**Restrizioni sugli spostamenti di risorse o sottoscrizioni**

Come parte di questa anteprima, gli spostamenti di risorse o sottoscrizioni non sono attualmente supportati per i set di scalabilità quando è abilitato il criterio di riparazione automatica.

**Restrizione per i set di scalabilità dell'infrastruttura del servizio**

Questa funzionalità di anteprima non è attualmente supportata per i set di scalabilità dell'infrastruttura del servizio.

## <a name="how-do-automatic-instance-repairs-work"></a>Come funzionano le riparazioni automatiche delle istanze?

La funzionalità di ripristino automatico delle istanze si basa sul monitoraggio dell'integrità delle singole istanze in un set di scalabilità. Le istanze di macchine virtuali in un set di scalabilità possono essere configurate per generare lo stato di integrità dell'applicazione usando [l'estensione Integrità applicazioni](./virtual-machine-scale-sets-health-extension.md) o i probe di integrità del [bilanciamento del carico.](../load-balancer/load-balancer-custom-probe-overview.md) Se un'istanza risulta non integra, il set di scalabilità esegue un'azione di ripristino eliminando l'istanza non integra e creandone una nuova per sostituirla. Questa funzionalità può essere abilitata nel modello di set di scalabilità delle macchine virtuali usando l'oggetto *automaticRepairsPolicy.This* feature can be enabled in the virtual machine scale set model by using the automaticRepairsPolicy object.

### <a name="batching"></a>Creazione di batch

Le operazioni di ripristino automatico delle istanze vengono eseguite in batch. In qualsiasi momento, non più del 5% delle istanze nel set di scalabilità vengono riparate tramite i criteri di riparazione automatica. Ciò consente di evitare l'eliminazione simultanea e la ricreazione di un numero elevato di istanze se risulta non integro allo stesso tempo.

### <a name="grace-period"></a>Periodo di tolleranza

Quando un'istanza passa attraverso un'operazione di modifica dello stato a causa di un'azione PUT, PATCH o POST eseguita sul set di scalabilità (ad esempio ricreare l'immagine, ridistribuire, aggiornare e così via), qualsiasi azione di ripristino su tale istanza viene eseguita solo dopo aver atteso il periodo di tolleranza. Periodo di tolleranza è la quantità di tempo per consentire all'istanza di tornare allo stato integro. Il periodo di tolleranza inizia dopo il completamento della modifica dello stato. Ciò consente di evitare eventuali operazioni di riparazione prematura o accidentale. Il periodo di tolleranza viene rispettato per qualsiasi istanza appena creata nel set di scalabilità (inclusa quella creata come risultato dell'operazione di ripristino). Il periodo di tolleranza viene specificato in minuti in formato ISO 8601 e può essere impostato utilizzando la proprietà *automaticRepairsPolicy.gracePeriod*. Il periodo di tolleranza può essere compreso tra 30 minuti e 90 minuti e ha un valore predefinito di 30 minuti.

Il processo di riparazione automatica dell'istanza funziona come segue:

1. [L'estensione Dell'integrità dell'applicazione](./virtual-machine-scale-sets-health-extension.md) o i probe di integrità del servizio di [bilanciamento](../load-balancer/load-balancer-custom-probe-overview.md) del carico eseguire il ping dell'endpoint dell'applicazione all'interno di ogni macchina virtuale nel set di scalabilità per ottenere lo stato di integrità dell'applicazione per ogni istanza.
2. Se l'endpoint risponde con lo stato 200 (OK), l'istanza viene contrassegnata come "Integro". In tutti gli altri casi (anche se l'endpoint non è raggiungibile), l'istanza è contrassegnata come "Non integro".
3. Quando un'istanza risulta non integra, il set di scalabilità attiva un'azione di ripristino eliminando l'istanza non integra e creandone una nuova per sostituirla.
4. Le riparazioni delle istanze vengono eseguite in batch. In qualsiasi momento, non più del 5% delle istanze totali nel set di scalabilità vengono riparate. Se un set di scalabilità include meno di 20 istanze, le riparazioni vengono eseguite per un'istanza non integra alla volta.
5. Il processo precedente continua fino a quando non vengono riparate tutte le istanze non integre nel set di scalabilità.

## <a name="instance-protection-and-automatic-repairs"></a>Protezione dell'istanza e riparazioni automatiche

Se un'istanza in un set di scalabilità è protetta applicando il criterio di *[protezione Proteggi azioni di tipo Proteggi da scale,](./virtual-machine-scale-sets-instance-protection.md#protect-from-scale-set-actions)* i ririparazioni automatici non vengono eseguiti su tale istanza.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Abilitazione dei criteri di riparazione automatica durante la creazione di un nuovo set di scalabilitàEnabling automatic repairs policy when creating a new scale set

Per abilitare i criteri di riparazione automatiche durante la creazione di un nuovo set di scalabilità, assicurarsi che siano soddisfatti tutti i [requisiti](#requirements-for-using-automatic-instance-repairs) per l'attivazione di questa funzionalità. L'endpoint dell'applicazione deve essere configurato correttamente per le istanze del set di scalabilità per evitare l'attivazione di riparazioni indesiderate durante la configurazione dell'endpoint. Per i set di scalabilità appena creati, tutte le riparazioni di istanze vengono eseguite solo dopo aver atteso la durata del periodo di tolleranza. Per abilitare il ripristino automatico dell'istanza in un set di scalabilità, usare l'oggetto *automaticRepairsPolicy* nel modello del set di scalabilità delle macchine virtuali.

### <a name="rest-api"></a>API REST

Nell'esempio seguente viene illustrato come abilitare il ripristino automatico dell'istanza in un modello di set di scalabilità. Usare la versione API 2018-10-01 o successiva.

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

La funzionalità di ripristino automatico delle istanze può essere abilitata durante la creazione di un nuovo set di scalabilità utilizzando il cmdlet [New-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig) Questo script di esempio illustra la creazione di un set di scalabilità e delle risorse associate usando il file di configurazione: Creare un set di scalabilità di [macchine virtuali completo.](./scripts/powershell-sample-create-complete-scale-set.md) È possibile configurare i criteri di riparazione automatica delle istanze aggiungendo i parametri *EnableAutomaticRepair* e *AutomaticRepairGracePeriod* all'oggetto di configurazione per la creazione del set di scalabilità. Nell'esempio seguente viene abilitata la funzionalità con un periodo di tolleranza di 30 minuti.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Abilitazione dei criteri di riparazione automatica durante l'aggiornamento di un set di scalabilità esistenteEnabling automatic repairs policy when updating an existing scale set

Prima di abilitare i criteri di riparazione automatiche in un set di scalabilità esistente, assicurarsi che siano soddisfatti tutti i [requisiti](#requirements-for-using-automatic-instance-repairs) per l'attivazione di questa funzionalità. L'endpoint dell'applicazione deve essere configurato correttamente per le istanze del set di scalabilità per evitare l'attivazione di riparazioni indesiderate durante la configurazione dell'endpoint. Per abilitare il ripristino automatico dell'istanza in un set di scalabilità, usare l'oggetto *automaticRepairsPolicy* nel modello del set di scalabilità delle macchine virtuali.

### <a name="rest-api"></a>API REST

Nell'esempio seguente viene abilitato il criterio con un periodo di prova di 40 minuti. Usare la versione API 2018-10-01 o successiva.

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

Utilizzare il cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) per modificare la configurazione della funzionalità di ripristino automatico delle istanze in un set di scalabilità esistente. Nell'esempio seguente il periodo di prova viene aggiornato a 40 minuti.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

## <a name="troubleshoot"></a>Risolvere i problemi

**Mancata abilitazione dei criteri di riparazione automatica**

Se viene visualizzato un errore 'BadRequest' con un messaggio che indica "Impossibile trovare il membro 'automaticRepairsPolicy' nell'oggetto di tipo 'proprietà'", controllare la versione dell'API utilizzata per il set di scalabilità della macchina virtuale. Per questa funzionalità è necessaria la versione API 2018-10-01 o successiva.

**Istanza non viene ripristinata anche quando è abilitato il criterio**

L'istanza potrebbe essere nel periodo di tolleranza. Si tratta della quantità di tempo di attesa dopo qualsiasi modifica dello stato nell'istanza prima di eseguire le operazioni di ripristino. Questo per evitare eventuali riparazioni premature o accidentali. L'azione di ripristino deve essere eseguita una volta completato il periodo di tolleranza per l'istanza.

**Visualizzazione dello stato di integrità dell'applicazione per le istanze del set di scalabilitàViewing application health status for scale set instances**

È possibile usare [l'API Get Instance View](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) per le istanze in un set di scalabilità della macchina virtuale per visualizzare lo stato di integrità dell'applicazione. Con Azure PowerShell, è possibile usare il cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) con il flag *-InstanceView.With* Azure PowerShell, you can use the cmdlet Get-AzVmssVM with the -InstanceView flag. Lo stato di integrità dell'applicazione viene fornito nella proprietà *vmHealth*.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare [l'estensione dell'integrità dell'applicazione](./virtual-machine-scale-sets-health-extension.md) o i probe di integrità del servizio di [bilanciamento del carico](../load-balancer/load-balancer-custom-probe-overview.md) per i set di scalabilità.
