---
title: Notifica di interruzione per le istanze del set di scalabilità di macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come abilitare la notifica di chiusura per le istanze del set di scalabilità di macchine virtuali di Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: shandilvarun
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/27/2019
ms.author: vashan
ms.openlocfilehash: d3d7f92b3803114321bc7420b5c4ba059aabcb9d
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705915"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances-preview"></a>Notifica di interruzione per le istanze del set di scalabilità di macchine virtuali di Azure (anteprima)
Le istanze del set di scalabilità possono acconsentire esplicitamente a ricevere notifiche di terminazione dell'istanza e impostare un timeout di ritardo predefinito per l'operazione di interruzione. La notifica di chiusura viene inviata tramite il servizio metadati di Azure- [eventi pianificati](../virtual-machines/windows/scheduled-events.md), che fornisce le notifiche per e ritardare le operazioni di cui si è interessati, ad esempio il riavvio e la ridistribuzione. La soluzione di anteprima aggiunge un altro evento, ovvero terminate, all'elenco di Eventi pianificati e il ritardo associato dell'evento di terminazione dipenderà dal limite di ritardo specificato dagli utenti nelle configurazioni del modello del set di scalabilità.

Una volta registrati nella funzionalità, le istanze del set di scalabilità non devono attendere la scadenza del timeout specificato prima che l'istanza venga eliminata. Dopo la ricezione di una notifica di terminazione, l'istanza può scegliere di essere eliminata in qualsiasi momento prima della scadenza del timeout di terminazione.

> [!IMPORTANT]
> La notifica di terminazione per le istanze del set di scalabilità è attualmente in anteprima pubblica. Per usare la funzionalità di anteprima pubblica descritta di seguito non è necessaria alcuna procedura di consenso esplicito.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-terminate-notifications"></a>Abilita notifiche di terminazione
Esistono diversi modi per abilitare le notifiche di terminazione sulle istanze del set di scalabilità, come descritto negli esempi seguenti.

### <a name="rest-api"></a>API REST

Nell'esempio seguente viene abilitata la notifica di terminazione sul modello del set di scalabilità.

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

Il blocco precedente specifica un ritardo di timeout di 5 minuti (come indicato da *PT5M*) per qualsiasi operazione di interruzione su tutte le istanze nel set di scalabilità. Il campo *notBeforeTimeout* può assumere qualsiasi valore compreso tra 5 e 15 minuti nel formato ISO 8601. È possibile modificare il timeout predefinito per l'operazione di terminazione modificando la proprietà *notBeforeTimeout* in *terminateNotificationProfile* descritta in precedenza.

Dopo aver abilitato *scheduledEventsProfile* sul modello del set di scalabilità e aver impostato il *notBeforeTimeout*, aggiornare le singole istanze al [modello più recente](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) in modo da riflettere le modifiche.

> [!NOTE]
>Le notifiche di terminazione sulle istanze del set di scalabilità possono essere abilitate solo con l'API versione 2019-03-01 e successive

### <a name="azure-powershell"></a>Azure PowerShell
Quando si crea un nuovo set di scalabilità, è possibile abilitare le notifiche di terminazione nel set di scalabilità usando il cmdlet [New-AzVmss](/powershell/module/az.compute/new-azvmss) .

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents
```

Nell'esempio precedente viene creato un nuovo set di scalabilità con notifiche di terminazione abilitate con un timeout predefinito di 5 minuti. Quando si crea un nuovo set di scalabilità, il parametro *TerminateScheduledEvents* non richiede un valore. Per modificare il valore di timeout, specificare il timeout desiderato tramite il parametro *TerminateScheduledEventNotBeforeTimeoutInMinutes* .

Usare il cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) per abilitare le notifiche di terminazione su un set di scalabilità esistente.

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
L'esempio precedente Abilita le notifiche di terminazione su un set di scalabilità esistente e imposta un timeout di 15 minuti per l'evento di terminazione.

Dopo aver abilitato gli eventi pianificati nel modello del set di scalabilità e aver impostato il timeout, aggiornare le singole istanze al [modello più recente](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) in modo da riflettere le modifiche.

## <a name="get-terminate-notifications"></a>Ricevi notifiche di terminazione

Le notifiche di terminazione vengono recapitate tramite [eventi pianificati](../virtual-machines/windows/scheduled-events.md), ovvero un servizio metadati di Azure. Il Servizio metadati di Azure presenta informazioni sulle macchine virtuali in esecuzione usando un endpoint REST accessibile dall'interno della macchina virtuale. Le informazioni sono disponibili tramite un indirizzo IP non instradabile, in modo che non venga esposto all'esterno della macchina virtuale.

Eventi pianificati è abilitato per il set di scalabilità la prima volta che si effettua una richiesta per gli eventi. È possibile prevedere una risposta ritardata nella prima chiamata fino a due minuti. Eseguire periodicamente una query sull'endpoint per rilevare gli eventi di manutenzione imminenti e lo stato delle attività di manutenzione in corso.

Eventi pianificati è disabilitato per il set di scalabilità se le istanze del set di scalabilità non effettuano una richiesta per 24 ore.

### <a name="endpoint-discovery"></a>Individuazione endpoint
Per le macchine virtuali abilitate per VNET, il servizio metadati è disponibile da un indirizzo IP statico non instradabile, 169.254.169.254.

L'endpoint completo per la versione più recente di Eventi pianificati per questa anteprima è:
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01 '

### <a name="query-response"></a>Risposta query
Una risposta contiene una serie di eventi pianificati. Una serie vuota indica che al momento non sono presenti eventi pianificati.

Nel caso in cui siano presenti eventi pianificati, la risposta contiene una matrice di eventi. Per un evento di terminazione, la risposta avrà un aspetto simile al seguente:
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
DocumentIncarnation è un ETag e fornisce un modo semplice per verificare se il payload degli eventi è stato modificato dall'ultima query.

Per ulteriori informazioni su ognuno dei campi precedenti, vedere la documentazione di Eventi pianificati per [Windows](../virtual-machines/windows/scheduled-events.md#event-properties) e [Linux](../virtual-machines/linux/scheduled-events.md#event-properties).

### <a name="respond-to-events"></a>Rispondere agli eventi
Dopo aver apprendere un evento imminente e aver completato la logica per l'arresto normale, è possibile approvare l'evento in attesa effettuando una chiamata POST al servizio metadati con l'ID evento. La chiamata POST indica ad Azure che può continuare con l'eliminazione della macchina virtuale.

Di seguito è riportato il codice JSON previsto nel corpo della richiesta POST. La richiesta deve contenere un elenco di StartRequests. Ogni StartRequest contiene gli EventId per l'evento che si vuole accelerare:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

Verificare che ogni macchina virtuale nel set di scalabilità approvi solo gli EventId pertinenti solo per tale macchina virtuale. Una macchina virtuale può ottenere il proprio nome di macchina virtuale [tramite i metadati dell'istanza](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name). Il nome assume il formato "{scale-set-name} _ {ID-istanza}" e verrà visualizzato nella sezione "risorse" della risposta alla query descritta in precedenza.

È anche possibile fare riferimento agli script di esempio per l'esecuzione di query e la risposta agli eventi usando [PowerShell](../virtual-machines/windows/scheduled-events.md#powershell-sample) e [Python](../virtual-machines/linux/scheduled-events.md#python-sample).

## <a name="tips-and-best-practices"></a>Suggerimenti e procedure consigliate
-   Termina le notifiche solo per le operazioni di eliminazione. tutte le operazioni di eliminazione (eliminazione manuale o scalabilità automatica avviate con scalabilità automatica) generano eventi di terminazione se per il set di scalabilità è abilitato *scheduledEventsProfile* . Altre operazioni, ad esempio il riavvio, la ricreazione dell'immagine, la ridistribuzione e l'arresto/deallocazione, non generano eventi di terminazione. Non è possibile abilitare le notifiche di terminazione per le macchine virtuali con priorità bassa.
-   Nessuna attesa obbligatoria per il timeout: è possibile avviare l'operazione di interruzione in qualsiasi momento dopo la ricezione dell'evento e prima della scadenza del tempo di *NotBefore* dell'evento.
-   Eliminazione obbligatoria al timeout: l'anteprima non fornisce alcuna funzionalità di estensione del valore di timeout dopo la generazione di un evento. Una volta scaduto il timeout, l'evento di terminazione in sospeso verrà elaborato e la macchina virtuale verrà eliminata.
-   Valore di timeout modificabile: è possibile modificare il valore di timeout in qualsiasi momento prima dell'eliminazione di un'istanza, modificando la proprietà *notBeforeTimeout* nel modello del set di scalabilità e aggiornando le istanze di VM al modello più recente.
-   Approva tutte le eliminazioni in sospeso: se è presente un'eliminazione in sospeso su VM_1 che non è approvata ed è stato approvato un altro evento di terminazione in VM_2, VM_2 non viene eliminato fino a quando non viene approvato l'evento di terminazione per VM_1 o il timeout è scaduto. Una volta approvata l'evento di terminazione per VM_1, verranno eliminati sia VM_1 che VM_2.
-   Approva tutte le eliminazioni simultanee: estendendo l'esempio precedente, se VM_1 e VM_2 hanno lo stesso tempo di *NotBefore* , entrambi gli eventi terminano devono essere approvati o nessuna macchina virtuale viene eliminata prima della scadenza del timeout.

## <a name="troubleshoot"></a>Risolvere problemi
### <a name="failure-to-enable-scheduledeventsprofile"></a>Errore di abilitazione di scheduledEventsProfile
Se viene visualizzato un errore "richiesta non valida" con un messaggio di errore che indica che "non è stato possibile trovare il membro ' scheduledEventsProfile ' nell'oggetto di tipo ' VirtualMachineProfile '", controllare la versione dell'API usata per le operazioni del set di scalabilità. Per questa anteprima è richiesta l'API di calcolo versione **2019-03-01** o successiva.

### <a name="failure-to-get-terminate-events"></a>Errore durante il tentativo di ottenere gli eventi di terminazione
Se non si ricevono eventi **terminati** tramite eventi pianificati, controllare la versione dell'API usata per ottenere gli eventi. Per gli eventi di terminazione è necessaria l'API del servizio metadati versione **2019-01-01** o successiva.
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01 '

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>Recupero dell'evento di terminazione con tempo NotBefore errato  
Dopo aver abilitato *scheduledEventsProfile* sul modello del set di scalabilità e aver impostato il *notBeforeTimeout*, aggiornare le singole istanze al [modello più recente](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) in modo da riflettere le modifiche.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [distribuire l'applicazione](virtual-machine-scale-sets-deploy-app.md) nei set di scalabilità di macchine virtuali.
