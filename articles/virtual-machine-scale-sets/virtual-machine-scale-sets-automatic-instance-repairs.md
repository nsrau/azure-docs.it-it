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
ms.openlocfilehash: 8e73ef75b3313656b45d29270d9996c3ad17c630
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538070"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Riparazioni automatiche delle istanze per i set di scalabilità delle macchine virtuali di AzureAutomatic instance repairs for Azure virtual machine scale sets

L'abilitazione delle riparazioni automatiche delle istanze per i set di scalabilità delle macchine virtuali di Azure consente di ottenere un'elevata disponibilità per le applicazioni mantenendo un set di istanze integre. Se un'istanza nel set di scalabilità risulta non integra come riportato [dall'estensione dell'integrità dell'applicazione](./virtual-machine-scale-sets-health-extension.md) o dai probe di integrità del [bilanciamento](../load-balancer/load-balancer-custom-probe-overview.md)del carico, questa funzionalità esegue automaticamente il ripristino dell'istanza eliminando l'istanza non integra e creandouna nuova per sostituirla.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Requisiti per l'utilizzo delle riparazioni automatiche delle istanze

**Abilitare il monitoraggio dell'integrità delle applicazioni per il set di scalabilitàEnable application health monitoring for scale set**

Il set di scalabilità deve avere il monitoraggio dell'integrità dell'applicazione per le istanze abilitate. Questa operazione può essere eseguita usando [l'estensione dell'integrità dell'applicazione](./virtual-machine-scale-sets-health-extension.md) o i probe di integrità del servizio di bilanciamento del [carico.](../load-balancer/load-balancer-custom-probe-overview.md) Solo uno di questi può essere abilitato alla volta. L'estensione per l'integrità dell'applicazione o il servizio di bilanciamento del carico esamina il ping dell'endpoint dell'applicazione configurato nelle istanze della macchina virtuale per determinare lo stato di integrità dell'applicazione. Questo stato di integrità viene utilizzato dall'agente di orchestrazione del set di scalabilità per monitorare l'integrità dell'istanza ed eseguire riparazioni quando necessario.

**Configurare l'endpoint per fornire lo stato di integritàConfigure endpoint to provide health status**

Prima di abilitare i criteri di riparazione automatica delle istanze, verificare che per le istanze del set di scalabilità sia configurato l'endpoint dell'applicazione per generare lo stato di integrità dell'applicazione. Quando un'istanza restituisce lo stato 200 (OK) su questo endpoint dell'applicazione, l'istanza viene contrassegnata come "Integro". In tutti gli altri casi, l'istanza è contrassegnata come "Non integro", inclusi gli scenari seguenti:In all other cases, the instance is marked "Unhealthy", including the following scenarios:

- Quando non è presente alcun endpoint applicazione configurato all'interno delle istanze della macchina virtuale per fornire lo stato di integrità dell'applicazioneWhen there is no application endpoint configured inside the virtual machine instances to provide application health status
- Quando l'endpoint dell'applicazione non è configurato correttamente
- Quando l'endpoint dell'applicazione non è raggiungibile

Per le istanze contrassegnate come "Non integre", le riparazioni automatiche vengono attivate dal set di scalabilità. Verificare che l'endpoint dell'applicazione sia configurato correttamente prima di abilitare i criteri di riparazione automatica per evitare il ripristino indesiderato delle istanze, mentre l'endpoint viene configurato.

**Abilita singolo gruppo di posizionamento**

Questa funzionalità è attualmente disponibile solo per i set di scalabilità distribuiti come singolo gruppo di posizionamento. La proprietà *singlePlacementGroup* deve essere impostata su *true* per impostare la scalabilità per utilizzare la funzionalità di ripristino automatico delle istanze. Ulteriori informazioni sui gruppi di [posizionamento](./virtual-machine-scale-sets-placement-groups.md#placement-groups).

**Versione API**

I criteri di riparazione automatica sono supportati per la versione 2018-10-01 dell'API di calcolo.

**Restrizioni sugli spostamenti di risorse o sottoscrizioni**

Gli spostamenti di risorse o sottoscrizioni non sono attualmente supportati per i set di scalabilità quando è abilitata la funzionalità di riparazione automatica.

**Restrizione per i set di scalabilità dell'infrastruttura del servizio**

Questa funzionalità non è attualmente supportata per i set di scalabilità dell'infrastruttura del servizio.

## <a name="how-do-automatic-instance-repairs-work"></a>Come funzionano le riparazioni automatiche delle istanze?

La funzionalità di ripristino automatico delle istanze si basa sul monitoraggio dell'integrità delle singole istanze in un set di scalabilità. Le istanze di macchine virtuali in un set di scalabilità possono essere configurate per generare lo stato di integrità dell'applicazione usando [l'estensione Integrità applicazioni](./virtual-machine-scale-sets-health-extension.md) o i probe di integrità del [bilanciamento del carico.](../load-balancer/load-balancer-custom-probe-overview.md) Se un'istanza risulta non integra, il set di scalabilità esegue un'azione di ripristino eliminando l'istanza non integra e creandone una nuova per sostituirla. L'ultimo modello di set di scalabilità della macchina virtuale viene usato per creare la nuova istanza. Questa funzionalità può essere abilitata nel modello di set di scalabilità delle macchine virtuali usando l'oggetto *automaticRepairsPolicy.This* feature can be enabled in the virtual machine scale set model by using the automaticRepairsPolicy object.

### <a name="batching"></a>Creazione di batch

Le operazioni di ripristino automatico delle istanze vengono eseguite in batch. In qualsiasi momento, non più del 5% delle istanze nel set di scalabilità vengono riparate tramite i criteri di riparazione automatica. Ciò consente di evitare l'eliminazione simultanea e la ricreazione di un numero elevato di istanze se risulta non integro allo stesso tempo.

### <a name="grace-period"></a>Periodo di tolleranza

Quando un'istanza passa attraverso un'operazione di modifica dello stato a causa di un'azione PUT, PATCH o POST eseguita sul set di scalabilità (ad esempio ricreare l'immagine, ridistribuire, aggiornare e così via), qualsiasi azione di ripristino su tale istanza viene eseguita solo dopo aver atteso il periodo di tolleranza. Periodo di tolleranza è la quantità di tempo per consentire all'istanza di tornare allo stato integro. Il periodo di tolleranza inizia dopo il completamento della modifica dello stato. Ciò consente di evitare eventuali operazioni di riparazione prematura o accidentale. Il periodo di tolleranza viene rispettato per qualsiasi istanza appena creata nel set di scalabilità (inclusa quella creata come risultato dell'operazione di ripristino). Il periodo di tolleranza viene specificato in minuti in formato ISO 8601 e può essere impostato utilizzando la proprietà *automaticRepairsPolicy.gracePeriod*. Il periodo di tolleranza può essere compreso tra 30 minuti e 90 minuti e ha un valore predefinito di 30 minuti.

### <a name="suspension-of-repairs"></a>Sospensione delle riparazioni 

I set di scalabilità delle macchine virtuali consentono di sospendere temporaneamente le riparazioni automatiche delle istanze, se necessario. Il *serviceState* per le riparazioni automatiche nella proprietà *orchestrationServices* nella visualizzazione istanza del set di scalabilità della macchina virtuale mostra lo stato corrente delle riparazioni automatiche. Quando un set di scalabilità viene scelto per le riparazioni automatiche, il valore del parametro *serviceState* è impostato su *Running*. Quando le riparazioni automatiche vengono sospese per un set di scalabilità, il parametro *serviceState* viene impostato su *Suspended*. Se *automaticRepairsPolicy* è definito in un set di scalabilità ma la funzionalità di ripristino automatico non è abilitata, il parametro *serviceState* è impostato su *Non in esecuzione*.

Se le istanze appena create per la sostituzione di quelle non integre in un set di scalabilità continuano a rimanere non integre anche dopo aver eseguito ripetutamente operazioni di ripristino, come misura di sicurezza la piattaforma aggiorna *serviceState* per le riparazioni automatiche in *Suspended*. È possibile riprendere le riparazioni automatiche impostando il valore di *serviceState* per le riparazioni automatiche su *Running*. Le istruzioni dettagliate sono disponibili nella sezione sulla [visualizzazione e l'aggiornamento dello stato di assistenza dei criteri di riparazione automatica](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) per il set di scalabilità. 

Il processo di riparazione automatica dell'istanza funziona come segue:

1. [L'estensione Dell'integrità dell'applicazione](./virtual-machine-scale-sets-health-extension.md) o i probe di integrità del servizio di [bilanciamento](../load-balancer/load-balancer-custom-probe-overview.md) del carico eseguire il ping dell'endpoint dell'applicazione all'interno di ogni macchina virtuale nel set di scalabilità per ottenere lo stato di integrità dell'applicazione per ogni istanza.
2. Se l'endpoint risponde con lo stato 200 (OK), l'istanza viene contrassegnata come "Integro". In tutti gli altri casi (anche se l'endpoint non è raggiungibile), l'istanza è contrassegnata come "Non integro".
3. Quando un'istanza risulta non integra, il set di scalabilità attiva un'azione di ripristino eliminando l'istanza non integra e creandone una nuova per sostituirla.
4. Le riparazioni delle istanze vengono eseguite in batch. In qualsiasi momento, non più del 5% delle istanze totali nel set di scalabilità vengono riparate. Se un set di scalabilità include meno di 20 istanze, le riparazioni vengono eseguite per un'istanza non integra alla volta.
5. Il processo precedente continua fino a quando non vengono riparate tutte le istanze non integre nel set di scalabilità.

## <a name="instance-protection-and-automatic-repairs"></a>Protezione dell'istanza e riparazioni automatiche

Se un'istanza in un set di scalabilità è protetta applicando uno dei criteri di [protezione,](./virtual-machine-scale-sets-instance-protection.md)i ririparazioni automatici non vengono eseguiti su tale istanza. Questo vale per entrambi i criteri di protezione: *proteggere dalla scalabilità verticale* e proteggere dalle azioni *di scalabilità orizzontale.* 

## <a name="terminatenotificationandautomaticrepairs"></a>Notifica di terminazione e riparazioni automatiche

Se la funzionalità di notifica di [terminazione](./virtual-machine-scale-sets-terminate-notification.md) è abilitata in un set di scalabilità, durante l'operazione di ripristino automatico, l'eliminazione di un'istanza non integra segue la configurazione di terminazione della notifica. Una notifica di terminazione viene inviata tramite il servizio metadati di Azure, ovvero eventi pianificati, e l'eliminazione dell'istanza viene ritardata per la durata del timeout di ritardo configurato. Tuttavia, la creazione di una nuova istanza per sostituire quella non integra non attende il completamento del timeout di ritardo.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Abilitazione dei criteri di riparazione automatica durante la creazione di un nuovo set di scalabilitàEnabling automatic repairs policy when creating a new scale set

Per abilitare i criteri di riparazione automatiche durante la creazione di un nuovo set di scalabilità, assicurarsi che siano soddisfatti tutti i [requisiti](#requirements-for-using-automatic-instance-repairs) per l'attivazione di questa funzionalità. L'endpoint dell'applicazione deve essere configurato correttamente per le istanze del set di scalabilità per evitare l'attivazione di riparazioni indesiderate durante la configurazione dell'endpoint. Per i set di scalabilità appena creati, tutte le riparazioni di istanze vengono eseguite solo dopo aver atteso la durata del periodo di tolleranza. Per abilitare il ripristino automatico dell'istanza in un set di scalabilità, usare l'oggetto *automaticRepairsPolicy* nel modello del set di scalabilità delle macchine virtuali.

### <a name="azure-portal"></a>Portale di Azure
 
La procedura seguente consente di abilitare i criteri di riparazione automatica durante la creazione di un nuovo set di scalabilità.
 
1. Passare a Set di **scalabilità macchine virtuali**.
1. Selezionare **: Aggiungi** per creare un nuovo set di scalabilità.
1. Vai alla scheda **Integrità.** 
1. Individuare la sezione **Integrità.**
1. Abilitare l'opzione **Monitora integrità applicazione.**
1. Individuare la sezione **Criteri di riparazione automatica.**
1. **Attivare** l'opzione **Riparazioni automatiche.**
1. In **Periodo di tolleranza (min)** specificare il periodo di tolleranza in minuti, i valori consentiti sono compresi tra 30 e 90 minuti. 
1. Al termine della creazione del nuovo set di scalabilità, selezionare il pulsante **Rivedi e crea.**

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

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

Nell'esempio seguente vengono abilitati i criteri di riparazione automatica durante la creazione di un nuovo set di scalabilità utilizzando *[az vmss create](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)*. Creare innanzitutto un gruppo di risorse, quindi creare un nuovo set di scalabilità con il periodo di tolleranza dei criteri di riparazione automatica impostato su 30 minuti.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --load-balancer <existingLoadBalancer> \
  --health-probe <existingHealthProbeUnderLoaderBalancer> \
  --automatic-repairs-period 30
```

L'esempio precedente usa un servizio di bilanciamento del carico esistente e un probe di integrità per il monitoraggio dello stato di integrità delle istanze dell'applicazione. Se invece si preferisce utilizzare un'estensione per l'integrità dell'applicazione per il monitoraggio, è possibile creare un set di scalabilità, configurare l'estensione di integrità dell'applicazione e quindi abilitare i criteri di ripristino automatico delle istanze utilizzando *l'aggiornamento di az vmss*, come illustrato nella sezione successiva.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Abilitazione dei criteri di riparazione automatica durante l'aggiornamento di un set di scalabilità esistenteEnabling automatic repairs policy when updating an existing scale set

Prima di abilitare i criteri di riparazione automatiche in un set di scalabilità esistente, assicurarsi che siano soddisfatti tutti i [requisiti](#requirements-for-using-automatic-instance-repairs) per l'attivazione di questa funzionalità. L'endpoint dell'applicazione deve essere configurato correttamente per le istanze del set di scalabilità per evitare l'attivazione di riparazioni indesiderate durante la configurazione dell'endpoint. Per abilitare il ripristino automatico dell'istanza in un set di scalabilità, usare l'oggetto *automaticRepairsPolicy* nel modello del set di scalabilità delle macchine virtuali.

Dopo aver aggiornato il modello di un set di scalabilità esistente, assicurarsi che il modello più recente venga applicato a tutte le istanze della scala. Fare riferimento alle istruzioni su come portare le [macchine virtuali aggiornate con l'ultimo modello di set di scalabilità.](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

### <a name="azure-portal"></a>Portale di Azure

È possibile modificare i criteri di riparazione automatica di un set di scalabilità esistente tramite il portale di Azure.You can modify the automatic repairs policy of an existing scale set through the Azure portal. 
 
1. Passare a un set di scalabilità di macchine virtuali esistente.
1. In **Impostazioni** nel menu a sinistra selezionare **Integrità e ripristino**.
1. Abilitare l'opzione **Monitora integrità applicazione.**
1. Individuare la sezione **Criteri di riparazione automatica.**
1. **Attivare** l'opzione **Riparazioni automatiche.**
1. In **Periodo di tolleranza (min)** specificare il periodo di tolleranza in minuti, i valori consentiti sono compresi tra 30 e 90 minuti. 
1. Al termine, selezionare **Salva**. 

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

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

Di seguito è riportato un esempio per l'aggiornamento dei criteri di riparazione automatica delle istanze di un set di scalabilità esistente, utilizzando *[az vmss update](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-update)*.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>Visualizzazione e aggiornamento dello stato di servizio dei criteri di riparazione automatica delle istanze

### <a name="rest-api"></a>API REST 

Usare [Ottieni visualizzazione istanza](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview) con la versione API 2019-12-01 per il set di scalabilità delle macchine virtuali per visualizzare *serviceState* per le riparazioni automatiche nella proprietà *orchestrationServices*. 

```http
GET '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version=2019-12-01'
```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Running"
    }
  ]
}
```

Usare *l'API setOrchestrationServiceState* con la versione API 2019-12-01 o successiva in un set di scalabilità di macchine virtuali per impostare lo stato delle riparazioni automatiche. Una volta che il set di scalabilità è attivato dalla funzionalità di riparazione automatica, è possibile utilizzare questa API per sospendere o riprendere le riparazioni automatiche per il set di scalabilità. 

 ```http
 POST '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/setOrchestrationServiceState?api-version=2019-12-01'
 ```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Suspend"
    }
  ]
}
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure 

Utilizzare il cmdlet [get-instance-view](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-get-instance-view) per visualizzare *serviceState* per il ripristino automatico delle istanze. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

Utilizzare il cmdlet [set-orchestration-service-state](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-set-orchestration-service-state) per aggiornare *serviceState* per il ripristino automatico delle istanze. Dopo aver scelto il set di scalabilità per la funzionalità di ripristino automatico, è possibile utilizzare questo cmdlet per sospendere o riprendere le riparazioni automatiche per il set di scalabilità. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

Utilizzare il cmdlet [Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss?view=azps-3.7.0) con il parametro *InstanceView* per visualizzare *ServiceState* per le riparazioni automatiche delle istanze.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Utilizzare il cmdlet Set-AzVmssOrchestrationServiceState per aggiornare *serviceState* per il ripristino automatico dell'istanza. Dopo aver scelto il set di scalabilità per la funzionalità di ripristino automatico, è possibile utilizzare questo cmdlet per sospendere o riprendere le riparazioni automatiche per il set di scalabilità.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>Risolvere problemi

**Mancata abilitazione dei criteri di riparazione automatica**

Se viene visualizzato un errore 'BadRequest' con un messaggio che indica "Impossibile trovare il membro 'automaticRepairsPolicy' nell'oggetto di tipo 'proprietà'", controllare la versione dell'API utilizzata per il set di scalabilità della macchina virtuale. Per questa funzionalità è necessaria la versione API 2018-10-01 o successiva.

**Istanza non viene ripristinata anche quando è abilitato il criterio**

L'istanza potrebbe essere nel periodo di tolleranza. Si tratta della quantità di tempo di attesa dopo qualsiasi modifica dello stato nell'istanza prima di eseguire le operazioni di ripristino. Questo per evitare eventuali riparazioni premature o accidentali. L'azione di ripristino deve essere eseguita una volta completato il periodo di tolleranza per l'istanza.

**Visualizzazione dello stato di integrità dell'applicazione per le istanze del set di scalabilitàViewing application health status for scale set instances**

È possibile usare [l'API Get Instance View](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) per le istanze in un set di scalabilità della macchina virtuale per visualizzare lo stato di integrità dell'applicazione. Con Azure PowerShell, è possibile usare il cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) con il flag *-InstanceView.With* Azure PowerShell, you can use the cmdlet Get-AzVmssVM with the -InstanceView flag. Lo stato di integrità dell'applicazione viene fornito nella proprietà *vmHealth*.

Nel portale di Azure è possibile visualizzare anche lo stato di integrità. Passare a un set di scalabilità esistente, selezionare **Istanze** dal menu a sinistra e osservare la colonna **Stato di** integrità per lo stato di integrità di ogni istanza del set di scalabilità. 

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare [l'estensione dell'integrità dell'applicazione](./virtual-machine-scale-sets-health-extension.md) o i probe di integrità del servizio di [bilanciamento del carico](../load-balancer/load-balancer-custom-probe-overview.md) per i set di scalabilità.
