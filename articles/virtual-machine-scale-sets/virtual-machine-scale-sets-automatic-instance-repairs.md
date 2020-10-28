---
title: Ripristino automatico delle istanze con i set di scalabilità di macchine virtuali di Azure
description: Informazioni su come configurare i criteri di riparazione automatica per le istanze di macchine virtuali in un set di scalabilità
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 02/28/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 383895f2cb5983abd68bfca67d2c8361ee094ea1
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744848"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Ripristini automatici delle istanze per i set di scalabilità di macchine virtuali di Azure

L'abilitazione della riparazione automatica delle istanze per i set di scalabilità di macchine virtuali di Azure consente di ottenere la disponibilità elevata per le applicazioni mantenendo un set di istanze integre Se un'istanza del set di scalabilità risulta non integra come segnalato dall' [estensione dell'integrità dell'applicazione](./virtual-machine-scale-sets-health-extension.md) o dai [Probe di integrità](../load-balancer/load-balancer-custom-probe-overview.md)del servizio di bilanciamento del carico, questa funzionalità esegue automaticamente il ripristino dell'istanza eliminando l'istanza non integra e creandone una nuova per sostituirla.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Requisiti per l'utilizzo di ripristini automatici delle istanze

**Abilitare il monitoraggio dell'integrità delle applicazioni per set di scalabilità**

Il set di scalabilità deve avere il monitoraggio dell'integrità dell'applicazione per le istanze abilitate. Questa operazione può essere eseguita usando l' [estensione di integrità dell'applicazione](./virtual-machine-scale-sets-health-extension.md) o i [Probe di integrità](../load-balancer/load-balancer-custom-probe-overview.md)del servizio di bilanciamento del carico. È possibile abilitare solo uno di questi alla volta. L'estensione per l'integrità dell'applicazione o il servizio di bilanciamento del carico esegue il ping dell'endpoint dell'applicazione configurato nelle istanze di macchina virtuale per determinare lo stato di integrità dell'applicazione. Questo stato di integrità viene usato dall'agente di orchestrazione del set di scalabilità per monitorare l'integrità dell'istanza ed eseguire le riparazioni quando richiesto.

**Configurare l'endpoint per fornire lo stato di integrità**

Prima di abilitare i criteri di ripristino automatico dell'istanza, verificare che le istanze del set di scalabilità dispongano dell'endpoint applicazione configurato per generare lo stato di integrità dell'applicazione. Quando un'istanza restituisce lo stato 200 (OK) nell'endpoint dell'applicazione, l'istanza viene contrassegnata come "integro". In tutti gli altri casi, l'istanza viene contrassegnata come "non integro", inclusi gli scenari seguenti:

- Quando all'interno delle istanze di macchina virtuale non è configurato alcun endpoint applicazione per fornire lo stato di integrità dell'applicazione
- Quando l'endpoint dell'applicazione non è configurato correttamente
- Quando l'endpoint dell'applicazione non è raggiungibile

Per le istanze contrassegnate come "non integre", le riparazioni automatiche vengono attivate dal set di scalabilità. Verificare che l'endpoint dell'applicazione sia configurato correttamente prima di abilitare i criteri di riparazione automatica per evitare le riparazioni accidentali delle istanze, mentre l'endpoint viene configurato.

**Abilita singolo gruppo di posizionamento**

Questa funzionalità è attualmente disponibile solo per i set di scalabilità distribuiti come singolo gruppo di posizionamento. La proprietà *singlePlacementGroup* deve essere impostata su *true* per il set di scalabilità per l'utilizzo della funzionalità di ripristino automatico delle istanze. Altre informazioni sui [gruppi di posizionamento](./virtual-machine-scale-sets-placement-groups.md#placement-groups).

**Versione API**

Il criterio di riparazione automatica è supportato per l'API di calcolo versione 2018-10-01 o successiva.

**Restrizioni per lo spostamento di risorse o sottoscrizioni**

Lo spostamento di risorse o sottoscrizioni non è attualmente supportato per i set di scalabilità quando è abilitata la funzionalità di riparazione automatica.

**Restrizione per i set di scalabilità di Service Fabric**

Questa funzionalità non è attualmente supportata per i set di scalabilità di Service Fabric.

## <a name="how-do-automatic-instance-repairs-work"></a>Come funzionano le riparazioni automatiche delle istanze?

La funzionalità di ripristino automatico delle istanze si basa sul monitoraggio dello stato delle singole istanze in un set di scalabilità. Le istanze di VM in un set di scalabilità possono essere configurate per emettere lo stato di integrità dell'applicazione usando l' [estensione dell'integrità dell'applicazione](./virtual-machine-scale-sets-health-extension.md) o i [Probe di integrità del bilanciamento del carico](../load-balancer/load-balancer-custom-probe-overview.md) Se un'istanza risulta non integra, il set di scalabilità esegue un'azione di ripristino eliminando l'istanza non integra e creandone una nuova per sostituirla. Il modello di set di scalabilità di macchine virtuali più recente viene usato per creare la nuova istanza. Questa funzionalità può essere abilitata nel modello di set di scalabilità di macchine virtuali usando l'oggetto *automaticRepairsPolicy* .

### <a name="batching"></a>Creazione di batch

Le operazioni di ripristino automatico dell'istanza vengono eseguite in batch. In un determinato momento, non più del 5% delle istanze nel set di scalabilità viene ripristinato tramite i criteri di riparazione automatici. In questo modo è possibile evitare l'eliminazione e la ricreazione simultanee di un numero elevato di istanze, se non è stato trovato nello stesso momento.

### <a name="grace-period"></a>Periodo di tolleranza

Quando un'istanza passa attraverso un'operazione di modifica dello stato a causa di un'azione PUT, PATCH o POST eseguita sul set di scalabilità (ad esempio, la ricreazione dell'immagine, la ridistribuzione, l'aggiornamento e così via), qualsiasi azione di ripristino sull'istanza viene eseguita solo dopo l'attesa del periodo di tolleranza. Il periodo di tolleranza è la quantità di tempo per consentire all'istanza di tornare allo stato integro. Il periodo di tolleranza inizia dopo il completamento della modifica dello stato. Questo consente di evitare operazioni di ripristino prematura o accidentale. Il periodo di tolleranza viene rispettato per tutte le istanze appena create nel set di scalabilità (incluso quello creato come risultato dell'operazione di ripristino). Il periodo di tolleranza viene specificato in minuti nel formato ISO 8601 e può essere impostato utilizzando la proprietà *automaticRepairsPolicy. gracePeriod* . Il periodo di tolleranza può essere compreso tra 30 minuti e 90 minuti e il valore predefinito è 30 minuti.

### <a name="suspension-of-repairs"></a>Sospensione delle riparazioni 

I set di scalabilità di macchine virtuali offrono la possibilità di sospendere temporaneamente le riparazioni automatiche dell'istanza se necessario. Il *serviceState* per le riparazioni automatiche nella proprietà *orchestrationServices* nella visualizzazione dell'istanza del set di scalabilità di macchine virtuali Mostra lo stato corrente delle riparazioni automatiche. Quando un set di scalabilità viene scelto per la riparazione automatica, il valore del parametro *serviceState* è impostato su *in esecuzione* . Quando le riparazioni automatiche vengono sospese per un set di scalabilità, il parametro *serviceState* è impostato su *suspended* . Se *automaticRepairsPolicy* è definito in un set di scalabilità ma la funzionalità di riparazione automatica non è abilitata, il parametro *serviceState* è impostato su *non in esecuzione* .

Se le istanze appena create per la sostituzione di quelle non integre in un set di scalabilità continuano a rimanere non integre anche dopo aver eseguito ripetutamente le operazioni di ripristino, quindi, come misura di sicurezza, la piattaforma aggiorna la *serviceState* per le riparazioni automatiche da *sospendere* . È possibile riprendere le riparazioni automatiche impostando il valore di *serviceState* per le riparazioni automatiche in *esecuzione* . Le istruzioni dettagliate sono disponibili nella sezione sulla [visualizzazione e l'aggiornamento dello stato del servizio dei criteri di riparazione automatici](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) per il set di scalabilità. 

Il processo di ripristino automatico delle istanze funziona nel modo seguente:

1. I [Probe](../load-balancer/load-balancer-custom-probe-overview.md) di integrità [dell'](./virtual-machine-scale-sets-health-extension.md) applicazione o del servizio di bilanciamento del carico effettuano il ping dell'endpoint dell'applicazione in ogni macchina virtuale nel set di scalabilità per ottenere lo stato di integrità dell'applicazione per ogni istanza.
2. Se l'endpoint risponde con lo stato 200 (OK), l'istanza viene contrassegnata come "integro". In tutti gli altri casi (incluso se l'endpoint non è raggiungibile), l'istanza viene contrassegnata come "non integro".
3. Quando un'istanza viene rilevata come non integra, il set di scalabilità attiva un'azione di ripristino eliminando l'istanza non integra e creandone una nuova per sostituirla.
4. Le riparazioni dell'istanza vengono eseguite in batch. In un determinato momento, non vengono ripristinate più del 5% delle istanze totali del set di scalabilità. Se un set di scalabilità ha meno di 20 istanze, le riparazioni vengono eseguite per un'istanza non integra alla volta.
5. Il processo precedente continua fino a quando non viene ripristinata tutte le istanze non integre del set di scalabilità.

## <a name="instance-protection-and-automatic-repairs"></a>Protezione delle istanze e riparazioni automatiche

Se un'istanza di un set di scalabilità è protetta applicando uno dei [criteri di protezione](./virtual-machine-scale-sets-instance-protection.md), le riparazioni automatiche non vengono eseguite su tale istanza. Questo vale per entrambi i criteri di protezione: *Proteggi da scale-in* e *Proteggi dalle azioni di set di scalabilità* . 

## <a name="terminatenotificationandautomaticrepairs"></a>Interrompi notifica e riparazioni automatiche

Se la funzionalità di [notifica di terminazione](./virtual-machine-scale-sets-terminate-notification.md) è abilitata in un set di scalabilità, durante l'operazione di ripristino automatico, l'eliminazione di un'istanza non integra segue la configurazione della notifica di terminazione. Una notifica di terminazione viene inviata tramite il servizio metadati di Azure-eventi pianificati e l'eliminazione dell'istanza viene ritardata per la durata del timeout di ritardo configurato. Tuttavia, la creazione di una nuova istanza di per sostituire quello non integro non attende il completamento del timeout di ritardo.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Abilitazione dei criteri di riparazione automatica quando si crea un nuovo set di scalabilità

Per abilitare i criteri di riparazione automatica durante la creazione di un nuovo set di scalabilità, assicurarsi che siano soddisfatti tutti i [requisiti](#requirements-for-using-automatic-instance-repairs) per la scelta di questa funzionalità. L'endpoint dell'applicazione deve essere configurato correttamente per le istanze del set di scalabilità, in modo da evitare l'attivazione di riparazioni accidentali durante la configurazione dell'endpoint. Per i set di scalabilità appena creati, le riparazioni di istanze vengono eseguite solo dopo l'attesa della durata del periodo di tolleranza. Per abilitare la correzione automatica dell'istanza in un set di scalabilità, usare l'oggetto *automaticRepairsPolicy* nel modello del set di scalabilità di macchine virtuali.

È anche possibile usare questo [modello di avvio rapido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe) per distribuire un set di scalabilità di macchine virtuali con il probe di integrità del bilanciamento del carico e le riparazioni automatiche delle istanze abilitate con un periodo di tolleranza di 30 minuti

### <a name="azure-portal"></a>Portale di Azure
 
I passaggi seguenti abilitano i criteri di riparazione automatica quando si crea un nuovo set di scalabilità.
 
1. Passare a **set di scalabilità di macchine virtuali** .
1. Selezionare **+ Aggiungi** per creare un nuovo set di scalabilità.
1. Passare alla scheda **Health (integrità** ). 
1. Individuare la sezione relativa all' **integrità** .
1. Abilitare l'opzione **Monitoraggio integrità applicazione** .
1. Individuare la sezione **criteri di ripristino automatico** .
1. Attivare l'opzione **di** **Ripristino automatico** .
1. In **periodo di tolleranza (min)** specificare il periodo di tolleranza in minuti. i valori consentiti sono compresi tra 30 e 90 minuti. 
1. Al termine della creazione del nuovo set di scalabilità, selezionare il pulsante **Verifica + crea** .

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

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

Nell'esempio seguente vengono abilitati i criteri di riparazione automatica durante la creazione di un nuovo set di scalabilità con *[AZ vmss create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)* . Creare innanzitutto un gruppo di risorse, quindi creare un nuovo set di scalabilità con il periodo di tolleranza dei criteri di riparazione automatica impostato su 30 minuti.

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
  --automatic-repairs-grace-period 30
```

L'esempio precedente usa un servizio di bilanciamento del carico esistente e un probe di integrità per monitorare lo stato di integrità dell'applicazione delle istanze. Se invece si preferisce usare un'estensione per l'integrità dell'applicazione per il monitoraggio, è possibile creare un set di scalabilità, configurare l'estensione per l'integrità dell'applicazione e quindi abilitare i criteri di riparazione automatica dell'istanza usando il comando *AZ vmss Update* , come illustrato nella sezione successiva.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Abilitazione dei criteri di riparazione automatica quando si aggiorna un set di scalabilità esistente

Prima di abilitare i criteri di riparazione automatica in un set di scalabilità esistente, assicurarsi che siano soddisfatti tutti i [requisiti](#requirements-for-using-automatic-instance-repairs) per la scelta di questa funzionalità. L'endpoint dell'applicazione deve essere configurato correttamente per le istanze del set di scalabilità, in modo da evitare l'attivazione di riparazioni accidentali durante la configurazione dell'endpoint. Per abilitare la correzione automatica dell'istanza in un set di scalabilità, usare l'oggetto *automaticRepairsPolicy* nel modello del set di scalabilità di macchine virtuali.

Dopo aver aggiornato il modello di un set di scalabilità esistente, verificare che il modello più recente venga applicato a tutte le istanze della scala. Vedere le istruzioni su [come portare le VM aggiornate con il modello di set di scalabilità più recente](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-portal"></a>Portale di Azure

È possibile modificare i criteri di riparazione automatica di un set di scalabilità esistente tramite il portale di Azure. 
 
1. Passare a un set di scalabilità di macchine virtuali esistente.
1. In **Impostazioni** nel menu a sinistra selezionare **stato e ripristina** .
1. Abilitare l'opzione **Monitoraggio integrità applicazione** .
1. Individuare la sezione **criteri di ripristino automatico** .
1. Attivare l'opzione **di** **Ripristino automatico** .
1. In **periodo di tolleranza (min)** specificare il periodo di tolleranza in minuti. i valori consentiti sono compresi tra 30 e 90 minuti. 
1. Al termine, selezionare **Salva** . 

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

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

Di seguito è riportato un esempio per aggiornare i criteri di ripristino automatico delle istanze di un set di scalabilità esistente, usando *[AZ vmss Update](/cli/azure/vmss?view=azure-cli-latest#az-vmss-update)* .

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-grace-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>Visualizzazione e aggiornamento dello stato del servizio dei criteri di ripristino automatico delle istanze

### <a name="rest-api"></a>API REST 

Usare [ottenere la visualizzazione dell'istanza](/rest/api/compute/virtualmachinescalesets/getinstanceview) con l'API versione 2019-12-01 o successiva per il set di scalabilità di macchine virtuali per visualizzare la *serviceState* per le riparazioni automatiche nella proprietà *orchestrationServices* . 

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

Usare l'API *setOrchestrationServiceState* con api versione 2019-12-01 o successiva in un set di scalabilità di macchine virtuali per impostare lo stato delle riparazioni automatiche. Una volta che il set di scalabilità è stato scelto per la funzionalità di riparazione automatica, è possibile usare questa API per sospendere o riprendere le riparazioni automatiche per il set di scalabilità. 

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

Usare il cmdlet [Get-instance-View](/cli/azure/vmss?view=azure-cli-latest#az-vmss-get-instance-view) per visualizzare il *serviceState* per le riparazioni automatiche dell'istanza. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

Usare il cmdlet [set-Orchestration-Service-state](/cli/azure/vmss?view=azure-cli-latest#az-vmss-set-orchestration-service-state) per aggiornare *serviceState* per le riparazioni automatiche dell'istanza. Quando il set di scalabilità viene scelto per la funzionalità di ripristino automatico, è possibile usare questo cmdlet per sospendere o riprendere le riparazioni automatiche per il set di scalabilità. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

Usare il cmdlet [Get-AzVmss](/powershell/module/az.compute/get-azvmss?view=azps-3.7.0) con il parametro *InstanceView* per visualizzare il *ServiceState* per le riparazioni automatiche dell'istanza.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Usare Set-AzVmssOrchestrationServiceState cmdlet per aggiornare *serviceState* per le riparazioni automatiche dell'istanza. Una volta che il set di scalabilità è stato scelto per la funzionalità di ripristino automatico, è possibile usare questo cmdlet per sospendere o riprendere le riparazioni automatiche per il set di scalabilità.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>Risolvere problemi

**Errore di abilitazione dei criteri di riparazione automatica**

Se viene visualizzato un errore "richiesta non valida" con un messaggio che indica che "non è stato possibile trovare il membro ' automaticRepairsPolicy ' nell'oggetto di tipo ' Properties '", controllare la versione dell'API usata per il set di scalabilità di macchine virtuali. Per questa funzionalità è necessaria l'API versione 2018-10-01 o successiva.

**L'istanza non viene ripristinata anche quando è abilitato il criterio**

L'istanza potrebbe essere nel periodo di tolleranza. Si tratta dell'intervallo di tempo di attesa dopo qualsiasi modifica dello stato nell'istanza prima di eseguire le riparazioni. Questo consente di evitare eventuali riparazioni premature o accidentali. L'azione di ripristino dovrebbe verificarsi una volta completato il periodo di tolleranza per l'istanza.

**Visualizzazione dello stato di integrità dell'applicazione per istanze del set di scalabilità**

È possibile usare l' [API visualizzazione dell'istanza](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) per le istanze in un set di scalabilità di macchine virtuali per visualizzare lo stato di integrità dell'applicazione. Con Azure PowerShell, è possibile usare il cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) con il flag *-InstanceView* . Lo stato di integrità dell'applicazione viene fornito sotto la proprietà *vmHealth* .

Nella portale di Azure è possibile visualizzare anche lo stato di integrità. Passare a un set di scalabilità esistente, selezionare **istanze** dal menu a sinistra ed esaminare la colonna **stato** di integrità per lo stato di integrità di ogni istanza del set di scalabilità. 

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare l' [estensione integrità dell'applicazione](./virtual-machine-scale-sets-health-extension.md) o i [Probe di integrità](../load-balancer/load-balancer-custom-probe-overview.md) del servizio di bilanciamento del carico per i set di scalabilità.
