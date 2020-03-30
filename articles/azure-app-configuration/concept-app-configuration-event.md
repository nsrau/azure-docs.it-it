---
title: Reazione agli eventi chiave-valore di configurazione app di AzureReacting to Azure App Configuration key-value events
description: Usare Griglia di eventi di Azure per sottoscrivere gli eventi di configurazione delle app.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 02/20/2020
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: a4f61d147ba1abf73ada6360b8d0d965d8e063a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523799"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reazione agli eventi di configurazione delle app di AzureReacting to Azure App Configuration events

Gli eventi di Configurazione app di Azure consentono alle applicazioni di reagire alle modifiche nelle coppie chiave-valore. Questa operazione viene eseguita senza la necessità di codice complicato o servizi di polling costosi e inefficienti. Gli eventi vengono invece inviati tramite [Griglia di eventi](https://azure.microsoft.com/services/event-grid/) di Azure a sottoscrittori come Funzioni di [Azure,](https://azure.microsoft.com/services/functions/) [App per la logica](https://azure.microsoft.com/services/logic-apps/)di Azure o persino al listener http personalizzato. Criticamente, paghi solo per quello che usi.

Gli eventi di configurazione delle app di Azure vengono inviati alla griglia di eventi di Azure che fornisce servizi di recapito affidabili alle applicazioni tramite criteri di ripetizione dei tentativi avanzati e recapito di messaggi non recapitabili. Per ulteriori informazioni, vedere Recapito dei messaggi di Griglia di eventi [e riprovare.](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)

Gli scenari comuni di eventi di configurazione delle app includono l'aggiornamento della configurazione dell'applicazione, l'attivazione delle distribuzioni o qualsiasi flusso di lavoro orientato alla configurazione. Quando le modifiche non sono frequenti, ma lo scenario richiede tempi di risposta immediata, un'architettura basata su eventi può essere particolarmente efficiente.

Vedere Instradare gli eventi di [configurazione dell'app](./howto-app-configuration-event.md) di Azure a un endpoint Web personalizzato: CLI per un esempio rapido. 

![Modello di Griglia di eventi di Azure](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Eventi di configurazione delle app di Azure disponibiliAvailable Azure Azure App Configuration events
Griglia di eventi usa le [sottoscrizioni di eventi](../event-grid/concepts.md#event-subscriptions) per instradare i messaggi di evento ai sottoscrittori. Le sottoscrizioni di eventi di configurazione app di Azure possono includere due tipi di eventi:Azure App Configuration event subscriptions can include two types of events:  

> |Nome evento|Descrizione|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Generato quando un valore-chiave viene creato o sostituito|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Generato quando viene eliminata una chiave-valore|

## <a name="event-schema"></a>Schema di eventi
Gli eventi di configurazione delle app di Azure contengono tutte le informazioni necessarie per rispondere alle modifiche apportate ai dati. È possibile identificare un evento di configurazione dell'app perché la proprietà eventType inizia con "Microsoft.AppConfiguration". Informazioni aggiuntive sull'utilizzo delle proprietà degli eventi di Griglia di eventi sono disponibili nello [schema di eventi di Griglia di eventi](../event-grid/event-schema.md).  

> |Proprietà|Type|Descrizione|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |argomento|string|ID completo di Azure Resource Manager della configurazione dell'app che genera l'evento.|
> |subject|string|URI del valore-chiave che è l'oggetto dell'evento.|
> |eventTime|string|Data/ora in cui è stato generato l'evento, in formato ISO 8601.|
> |eventType|string|"Microsoft.AppConfiguration.KeyValueModified" o "Microsoft.AppConfiguration.KeyValueDeleted".|
> |ID|string|Identificatore univoco di questo evento.|
> |dataVersion|string|Versione dello schema dell'oggetto dati.|
> |metadataVersion|string|Versione dello schema delle proprietà di primo livello.|
> |data|object|Raccolta dei dati evento specifici di Configurazione app di Azure|
> |data.key (chiave)|string|Chiave della chiave-valore modificata o eliminata.|
> |data.label|string|Etichetta, se presente, della chiave-valore che è stata modificata o eliminata.|
> |data.etag|string|Per `KeyValueModified` l'etag del nuovo valore-chiave. Per `KeyValueDeleted` l'etag della chiave-valore che è stato eliminato.|

Di seguito è riportato un esempio di un evento KeyValueModified:Here is an example of a KeyValueModified event:
```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]

```

Per altre informazioni, vedere Schema degli eventi di configurazione delle app di Azure.For more information, see [Azure App Configuration events schema](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Procedure consigliate per l'utilizzo di eventi
Le applicazioni che gestiscono gli eventi di configurazione delle app devono seguire le procedure consigliate:Applications that handle App Configuration events should follow these recommended practices:
> [!div class="checklist"]
> * È possibile configurare più sottoscrizioni per instradare gli eventi allo stesso gestore eventi, pertanto non presupporre che gli eventi provengano da una determinata origine. Controllare invece l'argomento del messaggio per assicurarsi che l'istanza di configurazione app che invia l'evento.
> * Controllare eventType e non presupporre che tutti gli eventi ricevuti saranno i tipi previsti.
> * Utilizzare i campi etag per capire se le informazioni sugli oggetti sono ancora aggiornate.  
> * Utilizzare i campi del sequencer per comprendere l'ordine degli eventi su un oggetto specifico.
> * Utilizzare il campo dell'oggetto per accedere alla chiave-valore modificata.


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su Griglia di eventi e prova gli eventi di configurazione delle app di Azure:Learn more about Event Grid and give Azure App Configuration events a try:

- [Informazioni sulla griglia di eventi](../event-grid/overview.md)
- [Indirizzare gli eventi di configurazione dell'app di Azure a un endpoint Web personalizzatoRoute Azure App Configuration events to a custom web endpoint](./howto-app-configuration-event.md)