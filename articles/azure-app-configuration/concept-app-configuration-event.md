---
title: Reazione a eventi chiave-valore di configurazione delle App di Azure | Microsoft Docs
description: Usare griglia di eventi di Azure per sottoscrivere gli eventi di configurazione dell'App.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 05/30/2019
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: 601124aef37d2b285db71130f5c63b3620c7768f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735647"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reazione a eventi di configurazione delle App di Azure

Eventi di configurazione delle App di Azure consentono alle applicazioni di reagire alle modifiche in valori di chiave. Questa operazione viene eseguita senza la necessità di usare codice complesso o servizi di polling costosi e inefficienti. Gli eventi vengono invece inviati attraverso [Griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/) ai sottoscrittori, ad esempio [Funzioni di Azure](https://azure.microsoft.com/services/functions/), [App per la logica di Azure](https://azure.microsoft.com/services/logic-apps/) o anche al listener http personalizzato in uso, e si paga solo ciò che si usa.

Eventi di configurazione delle App di Azure vengono inviati a griglia di eventi di Azure che fornisce servizi di distribuzione affidabile per le applicazioni attraverso rich ripetere i criteri e il recapito non recapitabilità. Per altre informazioni, vedere [recapito dei messaggi di griglia di eventi e di ripetizione dei tentativi](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

Scenari di eventi di configurazione di app comuni includono l'aggiornamento di configurazione dell'applicazione, attivare le distribuzioni o qualsiasi flusso di lavoro orientato alla configurazione. Quando le modifiche non sono frequenti, ma lo scenario richiede tempi di risposta immediata, un'architettura basata su eventi può essere particolarmente efficiente.

Dare un'occhiata [gli eventi di configurazione di App di Azure di Route a una classe personalizzata web endpoint - CLI](./howto-app-configuration-event.md) per un esempio rapido. 

![Modello di Griglia di eventi di Azure](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Eventi di configurazione delle App di Azure disponibili
Griglia di eventi usa le [sottoscrizioni di eventi](../event-grid/concepts.md#event-subscriptions) per instradare i messaggi di evento ai sottoscrittori. Le sottoscrizioni di eventi di configurazione dell'App Azure possono includere due tipi di eventi:  

> |Nome evento|Descrizione|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Generato quando viene creato o sostituito un valore di chiave|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Generato quando viene eliminato un chiave-valore|

## <a name="event-schema"></a>Schema di eventi
Eventi di configurazione delle App di Azure contengono tutte le informazioni che necessarie per rispondere alle modifiche nei dati. È possibile identificare un evento di configurazione app perché la proprietà eventType inizia con "Microsoft.AppConfiguration". Informazioni aggiuntive sull'utilizzo delle proprietà degli eventi di Griglia di eventi sono disponibili nello [schema di eventi di Griglia di eventi](../event-grid/event-schema.md).  

> |Proprietà|Type|DESCRIZIONE|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |topic|string|Id Azure Resource Manager completo della configurazione di app che genera l'evento.|
> |subject|string|L'URI della chiave-valore che rappresenta l'oggetto dell'evento.|
> |eventTime|string|Data/ora che è stato generato l'evento, in formato ISO 8601.|
> |eventType|string|"Microsoft.AppConfiguration.KeyValueModified" o "Microsoft.AppConfiguration.KeyValueDeleted".|
> |ID|string|Identificatore univoco di questo evento.|
> |dataVersion|string|Versione dello schema dell'oggetto dati.|
> |metadataVersion|string|Versione dello schema delle proprietà di primo livello.|
> |data|object|Raccolta di dati di eventi specifici di configurazione delle App di Azure|
> |data.key|string|La chiave del valore di chiave che è stata modificata o eliminata.|
> |data.label|string|L'etichetta, se presente, del valore di chiave che è stato modificato o eliminato.|
> |data.etag|string|Per `KeyValueModified` il valore etag del nuovo chiave-valore. Per `KeyValueDeleted` il valore etag del valore di chiave che è stato eliminato.|

Ecco un esempio di un evento KeyValueModified:
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

Per altre informazioni, vedere [dello schema di eventi di configurazione dell'App Azure](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Procedure consigliate per l'utilizzo di eventi
Le applicazioni che gestiscono gli eventi di configurazione di app devono seguire alcune procedure consigliate:
> [!div class="checklist"]
> * Più sottoscrizioni possono essere configurate per instradare gli eventi allo stesso gestore eventi, è importante non presupporre gli eventi provengono da una determinata origine, ma per controllare l'argomento del messaggio per assicurarsi che provengano dalla configurazione dell'app che sono previsti.
> * Controllare anche che il tipo di evento che si verifica, eventType, sia un oggetto che si è preparati a elaborare e non presupporre che tutti gli eventi che si ricevono siano dei tipi previsti.
> * Dal momento che i messaggi non arrivano in ordine e giungono con un po' di ritardo, usare i campi etag per verificare se le informazioni disponibili sugli oggetti sono ancora aggiornate.  È possibile usare anche i campi del sequencer per comprendere l'ordine degli eventi relativi a un oggetto specifico.
> * Usare il campo oggetto per accedere al valore di chiave che è stato modificato.


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su griglia di eventi e provare gli eventi di configurazione delle App di Azure:

- [Informazioni sulla griglia di eventi](../event-grid/overview.md)
- [Indirizzare gli eventi di configurazione delle App di Azure a un endpoint web personalizzato](./howto-app-configuration-event.md)