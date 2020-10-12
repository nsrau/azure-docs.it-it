---
title: Reazione a app Azure eventi chiave-valore di configurazione
description: Usare griglia di eventi di Azure per sottoscrivere gli eventi di configurazione dell'app, che consentono alle applicazioni di reagire alle modifiche apportate ai valori chiave senza la necessità di codice complesso.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 02/20/2020
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: ae3417f991c0d810d8946cdaf358218ebbe4f6a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88590031"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reazione agli eventi di configurazione app Azure

App Azure eventi di configurazione consentono alle applicazioni di reagire alle modifiche apportate ai valori chiave. Questa operazione viene eseguita senza la necessità di codice complesso o servizi di polling costosi e inefficienti. Al contrario, gli eventi vengono inviati tramite [griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/) ai sottoscrittori, ad esempio funzioni di [Azure](https://azure.microsoft.com/services/functions/), app per la [logica di Azure](https://azure.microsoft.com/services/logic-apps/)o anche al listener HTTP personalizzato. In modo critico, paghi solo per ciò che usi.

Gli eventi di configurazione app Azure vengono inviati alla griglia di eventi di Azure, che offre servizi di recapito affidabili per le applicazioni tramite criteri di ripetizione avanzata e recapito dei messaggi non recapitabili. Per altre informazioni, vedere [recapito dei messaggi di griglia di eventi e riprovare](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

Gli scenari comuni di eventi di configurazione delle app includono l'aggiornamento della configurazione dell'applicazione, l'attivazione di distribuzioni o qualsiasi flusso di lavoro orientato alla configurazione. Quando le modifiche non sono frequenti, ma lo scenario richiede tempi di risposta immediata, un'architettura basata su eventi può essere particolarmente efficiente.

Per un esempio rapido, vedere [Route app Azure Configuration Events to an Custom Web endpoint-CLI](./howto-app-configuration-event.md) . 

![Modello di Griglia di eventi di Azure](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Eventi di configurazione app Azure disponibili
Griglia di eventi usa le [sottoscrizioni di eventi](../event-grid/concepts.md#event-subscriptions) per instradare i messaggi di evento ai sottoscrittori. App Azure le sottoscrizioni di eventi di configurazione possono includere due tipi di eventi:  

> |Nome evento|Descrizione|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Generato quando un valore chiave viene creato o sostituito|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Generato quando un valore chiave viene eliminato|

## <a name="event-schema"></a>Schema di eventi
App Azure eventi di configurazione contengono tutte le informazioni necessarie per rispondere alle modifiche apportate ai dati. È possibile identificare un evento di configurazione dell'app perché la proprietà eventType inizia con "Microsoft. AppConfiguration". Informazioni aggiuntive sull'utilizzo delle proprietà degli eventi di Griglia di eventi sono disponibili nello [schema di eventi di Griglia di eventi](../event-grid/event-schema.md).  

> |Proprietà|Type|Descrizione|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |argomento|string|ID Azure Resource Manager completo della configurazione dell'app che genera l'evento.|
> |subject|string|URI del valore della chiave che rappresenta l'oggetto dell'evento.|
> |eventTime|string|Data/ora in cui è stato generato l'evento, in formato ISO 8601.|
> |eventType|string|"Microsoft. AppConfiguration. KeyValueModified" o "Microsoft. AppConfiguration. KeyValueDeleted".|
> |ID|string|Identificatore univoco di questo evento.|
> |dataVersion|string|Versione dello schema dell'oggetto dati.|
> |metadataVersion|string|Versione dello schema delle proprietà di primo livello.|
> |data|object|Raccolta di dati evento specifici della configurazione di app Azure|
> |Data. Key|string|Chiave del valore della chiave che è stato modificato o eliminato.|
> |Data. Label|string|Etichetta, se presente, del valore della chiave che è stato modificato o eliminato.|
> |Data. ETag|string|Per `KeyValueModified` l'ETag del nuovo valore di chiave. Per `KeyValueDeleted` l'ETag del valore della chiave che è stato eliminato.|

Di seguito è riportato un esempio di un evento KeyValueModified:
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

Per ulteriori informazioni, vedere [app Azure schema degli eventi di configurazione](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Procedure consigliate per l'utilizzo di eventi
Le applicazioni che gestiscono gli eventi di configurazione delle app devono seguire le seguenti procedure consigliate:
> [!div class="checklist"]
> * È possibile configurare più sottoscrizioni per indirizzare gli eventi allo stesso gestore eventi, quindi non presupporre che gli eventi provengano da un'origine particolare. Al contrario, controllare l'argomento del messaggio per assicurarsi che l'istanza di configurazione dell'app invii l'evento.
> * Controllare eventType e non presupporre che tutti gli eventi ricevuti saranno quelli previsti.
> * Usare i campi eTag per capire se le informazioni sugli oggetti sono ancora aggiornate.  
> * Usare i campi di Sequencer per comprendere l'ordine degli eventi in un oggetto specifico.
> * Usare il campo oggetto per accedere al valore della chiave che è stato modificato.


## <a name="next-steps"></a>Passaggi successivi

Scopri di più su griglia di eventi e assegna app Azure eventi di configurazione a un tentativo:

- [Informazioni sulla griglia di eventi](../event-grid/overview.md)
- [Indirizzare gli eventi di configurazione app Azure a un endpoint Web personalizzato](./howto-app-configuration-event.md)