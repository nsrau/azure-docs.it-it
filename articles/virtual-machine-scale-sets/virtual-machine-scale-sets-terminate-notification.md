---
title: Notifica di notifica di notifica per le istanze del set di scalabilità delle macchine virtuali di AzureTerminate notification for Azure virtual machine scale set instances
description: Informazioni su come abilitare la notifica di terminazione per le istanze del set di scalabilità di macchine virtuali di AzureLearn how to enable termination notification for Azure virtual machine scale set instances
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 6023e9bf7539b79446d0135ba731b61be166dd6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250751"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances"></a>Notifica di notifica di notifica per le istanze del set di scalabilità delle macchine virtuali di AzureTerminate notification for Azure virtual machine scale set instances
Le istanze del set di scalabilità possono acconsentire esplicitamente alla ricezione delle notifiche di terminazione dell'istanza e impostare un timeout di ritardo predefinito per l'operazione di terminazione. La notifica di chiusura viene inviata tramite il servizio metadati di Azure - [Eventi pianificati](../virtual-machines/windows/scheduled-events.md), che fornisce notifiche e ritardo delle operazioni di impatto, ad esempio riavvii e ridistribuzione. La soluzione aggiunge un altro evento – Terminate – all'elenco degli eventi pianificati e il ritardo associato dell'evento di terminazione dipenderà dal limite di ritardo specificato dagli utenti nelle configurazioni del modello del set di scalabilità.

Una volta registrate nella funzionalità, le istanze del set di scalabilità non devono attendere la scadenza del timeout specificato prima dell'eliminazione dell'istanza. Dopo aver ricevuto una notifica Terminate, l'istanza può scegliere di essere eliminata in qualsiasi momento prima della scadenza del timeout di terminazione.

## <a name="enable-terminate-notifications"></a>Abilitare le notifiche di terminazione
Esistono diversi modi per abilitare le notifiche di terminazione nelle istanze del set di scalabilità, come descritto negli esempi seguenti.

### <a name="azure-portal"></a>Portale di Azure

I passaggi seguenti consentono di terminare la notifica durante la creazione di un nuovo set di scalabilità. 

1. Passare a Set di **scalabilità macchine virtuali**.
1. Selezionare **: Aggiungi** per creare un nuovo set di scalabilità.
1. Passare alla scheda **Gestione.** 
1. Individuare la sezione **Terminazione istanza.**
1. Per **Notifica di chiusura istanza**, selezionare **Attivato**.
1. Per **Ritardo di terminazione (minuti),** impostare il timeout predefinito desiderato.
1. Al termine della creazione del nuovo set di scalabilità, selezionare il pulsante **Rivedi e crea.** 

> [!NOTE]
> Non è possibile impostare le notifiche di terminazione nei set di scalabilità esistenti nel portale di AzureYou are unable to set terminate notifications on existing scale sets in Azure portal

### <a name="rest-api"></a>API REST

Nell'esempio seguente viene abilitata la notifica di terminazione nel modello del set di scalabilità.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "virtualMachineProfile": {
            "scheduledEventsProfile": {
                "terminateNotificationProfile": {
                    "notBeforeTimeout":"PT5M",
                    "enable":true
                }
            }
        }
    }        
}

```

Il blocco precedente specifica un ritardo di timeout di 5 minuti (come indicato da *PT5M*) per qualsiasi operazione di terminazione su tutte le istanze nel set di scalabilità. Il campo *notBeforeTimeout* può assumere qualsiasi valore compreso tra 5 e 15 minuti in formato ISO 8601. È possibile modificare il timeout predefinito per l'operazione di terminazione modificando la proprietà *notBeforeTimeout* in *terminateNotificationProfile* descritta in precedenza.

Dopo aver abilitato *scheduledEventsProfile* sul modello del set di scalabilità e aver impostato *il metodo notBeforeTimeout*, aggiornare le singole istanze al [modello più recente](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) per riflettere le modifiche.

> [!NOTE]
>Le notifiche di terminazione nelle istanze del set di scalabilità possono essere abilitate solo con la versione API 2019-03-01 e successive

### <a name="azure-powershell"></a>Azure PowerShell
Quando si crea un nuovo set di scalabilità, è possibile abilitare le notifiche di terminazione sul set di scalabilità utilizzando il cmdlet [New-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig)

Questo script di esempio illustra la creazione di un set di scalabilità e delle risorse associate usando il file di configurazione: Creare un set di scalabilità di [macchine virtuali completo.](./scripts/powershell-sample-create-complete-scale-set.md) È possibile fornire la notifica di terminazione di configure aggiungendo i parametri *TerminateScheduledEvents* e *TerminateScheduledEventNotBeforeTimeoutInMinutes* all'oggetto di configurazione per la creazione del set di scalabilità. Nell'esempio seguente viene abilitata la funzionalità con un timeout di ritardo di 10 minuti.

```azurepowershell-interactive
New-AzVmssConfig `
  -Location "VMSSLocation" `
  -SkuCapacity 2 `
  -SkuName "Standard_DS2" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 10
```

Utilizzare il cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) per abilitare le notifiche di terminazione in un set di scalabilità esistente.

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
L'esempio precedente abilita le notifiche di terminazione in un set di scalabilità esistente e imposta un timeout di 15 minuti per l'evento di terminazione.

Dopo aver abilitato gli eventi pianificati nel modello del set di scalabilità e aver impostato il timeout, aggiornare le singole istanze al [modello più recente](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) per riflettere le modifiche.

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

L'esempio seguente è per abilitare la notifica di terminazione durante la creazione di un nuovo set di scalabilità.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --terminate-notification-time 10
```

L'esempio precedente crea innanzitutto un gruppo di risorse, quindi crea un nuovo set di scalabilità con le notifiche di terminazione abilitate per un timeout predefinito di 10 minuti.

L'esempio seguente è per abilitare la notifica di terminazione in un set di scalabilità esistente.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-terminate-notification true \
  --terminate-notification-time 10
```

## <a name="get-terminate-notifications"></a>Ottenere le notifiche di terminazioneGet Terminate notifications

Le notifiche di terminazione vengono recapitate tramite [eventi pianificati,](../virtual-machines/windows/scheduled-events.md)ovvero un servizio metadati di Azure.Terminate notifications are delivered through Scheduled Events , which is an Azure Metadata Service. Il Servizio metadati di Azure presenta informazioni sulle macchine virtuali in esecuzione usando un endpoint REST accessibile dall'interno della macchina virtuale. Le informazioni sono disponibili tramite un IP non instradabile in modo che non vengano esposte all'esterno della macchina virtuale.

Eventi pianificati è abilitato per il set di scalabilità la prima volta che si effettua una richiesta di eventi. Ci si può aspettare una risposta ritardata nella prima chiamata fino a due minuti. Eseguire periodicamente una query sull'endpoint per rilevare gli eventi di manutenzione imminenti e lo stato delle attività di manutenzione in corso.

Eventi pianificati è disabilitato per il set di scalabilità se le istanze del set di scalabilità non effettuano una richiesta per 24 ore.

### <a name="endpoint-discovery"></a>Individuazione degli endpointEndpoint discovery
Per le macchine virtuali abilitate per La rete virtuale, il servizio metadati è disponibile da un IP statico non instradabile, 169.254.169.254.254.254.For VNET enabled VNET, the Metadata Service is available from a static non-routable IP, 169.254.169.254.

L'endpoint completo per la versione più recente degli eventi pianificati è:
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="query-response"></a>Risposta alla query
Una risposta contiene una serie di eventi pianificati. Una serie vuota indica che al momento non sono presenti eventi pianificati.

Nel caso in cui siano presenti eventi pianificati, la risposta contiene una matrice di eventi. Per un evento "Terminate", la risposta avrà il seguente aspetto:
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
*DocumentIncarnation* è un ETag e fornisce un modo semplice per verificare se il payload di eventi è stato modificato dall'ultima query.

Per ulteriori informazioni su ognuno dei campi precedenti, vedere la documentazione relativa agli eventi pianificati per [Windows](../virtual-machines/windows/scheduled-events.md#event-properties) e [Linux](../virtual-machines/linux/scheduled-events.md#event-properties).

### <a name="respond-to-events"></a>Rispondere agli eventi
Una volta appreso di un evento imminente e completato la logica per l'arresto normale, è possibile approvare l'evento in sospeso effettuando una chiamata POST al servizio metadati con EventId. La chiamata POST indica ad Azure che può continuare con l'eliminazione della macchina virtuale.

Di seguito è riportato il json previsto nel corpo della richiesta POST. La richiesta deve contenere un elenco di StartRequests.The request should contain a list of StartRequests. Ogni StartRequest contiene il EventId per l'evento che si desidera accelerare:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

Verificare che ogni macchina virtuale nel set di scalabilità approvi solo l'EventID rilevante solo per tale macchina virtuale. Una macchina virtuale può ottenere il proprio nome di macchina virtuale [tramite i metadati dell'istanza.](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name) Questo nome assume il formato "'nome-set-scala'_'id-istanza'" e verrà visualizzato nella sezione 'Risorse' della risposta alla query descritta in precedenza.

È anche possibile fare riferimento a script di esempio per l'esecuzione di query e la risposta agli eventi utilizzando [PowerShell](../virtual-machines/windows/scheduled-events.md#powershell-sample) e [Python](../virtual-machines/linux/scheduled-events.md#python-sample).

## <a name="tips-and-best-practices"></a>Suggerimenti e procedure consigliate
-   Termina notifiche solo su operazioni 'delete': tutte le operazioni di eliminazione (eliminazione manuale o scalabilità inattiva automaticamente) genereranno eventi Terminate se il set di scalabilità è *scheduledEventsProfile* abilitato. Altre operazioni come reboot, reimage, redeploy e stop/deallocate non generano eventi Terminate. Le notifiche di terminazione non possono essere abilitate per le macchine virtuali con priorità bassa.
-   Nessuna attesa obbligatoria per il timeout: è possibile avviare l'operazione di terminazione in qualsiasi momento dopo la ricezione dell'evento e prima della scadenza dell'ora *NotBefore* dell'evento.
-   Eliminazione obbligatoria al timeout: non è disponibile alcuna capacità di estendere il valore di timeout dopo la generazione di un evento. Dopo la scadenza del timeout, l'evento di terminazione in sospeso verrà elaborato e la macchina virtuale verrà eliminata.
-   Valore di timeout modificabile: è possibile modificare il valore di timeout in qualsiasi momento prima dell'eliminazione di un'istanza, modificando la proprietà *notBeforeTimeout* nel modello del set di scalabilità e aggiornando le istanze della macchina virtuale al modello più recente.
-   Approvare tutte le eliminazioni in sospeso: se è presente un'eliminazione in sospeso in VM_1 che non è stata approvata e hai approvato un altro evento di terminazione in VM_2, VM_2 non viene eliminato fino a quando l'evento di terminazione per VM_1 viene approvato o non è trascorso il timeout. Dopo aver approvato l'evento di terminazione per VM_1, vengono eliminati sia VM_1 che VM_2.
-   Approva tutte le eliminazioni simultanee: estensione dell'esempio precedente, se VM_1 e VM_2 hanno lo stesso tempo *NotBefore,* entrambi gli eventi di terminazione devono essere approvati o nessuna macchina virtuale viene eliminata prima della scadenza del timeout.

## <a name="troubleshoot"></a>Risolvere i problemi
### <a name="failure-to-enable-scheduledeventsprofile"></a>Impossibile abilitare scheduledEventsProfile
Se viene visualizzato un errore 'BadRequest' con un messaggio di errore che indica "Impossibile trovare il membro 'scheduledEventsProfile' nell'oggetto di tipo 'VirtualMachineProfile',controllare la versione dell'API utilizzata per le operazioni del set di scalabilità. È necessaria la versione API di calcolo **2019-03-01** o successiva. 

### <a name="failure-to-get-terminate-events"></a>Impossibile ottenere gli eventi Terminate
Se non si ottengono eventi **Terminate** tramite eventi pianificati, controllare la versione dell'API utilizzata per ottenere gli eventi. Per gli eventi Terminate è necessaria la versione **2019-01-01** o successiva dell'API del servizio metadati.
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>Evento Getting Terminate con un'ora NotBefore non corretta  
Dopo aver abilitato *scheduledEventsProfile* sul modello del set di scalabilità e aver impostato *il metodo notBeforeTimeout*, aggiornare le singole istanze al [modello più recente](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) per riflettere le modifiche.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [distribuire l'applicazione](virtual-machine-scale-sets-deploy-app.md) nei set di scalabilità di macchine virtuali.
