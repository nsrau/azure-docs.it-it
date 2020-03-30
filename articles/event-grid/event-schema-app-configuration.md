---
title: Schema dell'evento di configurazione dell'app di Azure Event Grid AzureAzure Event Grid Azure Azure Azure Event Grid Azure App Configuration event
description: Descrive le proprietà fornite per gli eventi di configurazione delle app di Azure con Griglia di eventi di AzureDescribes the properties that are provided for Azure App Configuration events with Azure Event Grid
services: event-grid
author: jimmyca
ms.service: event-grid
ms.topic: reference
ms.date: 05/30/2019
ms.author: jimmyca
ms.openlocfilehash: fe0274f723692eea3cfd25cc0e9e146b35dce2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66735782"
---
# <a name="azure-event-grid-event-schema-for-azure-app-configuration"></a>Azure Event Grid event schema for Azure App Configuration

Questo articolo fornisce le proprietà e lo schema per gli eventi di configurazione delle app di Azure.This article provides the properties and schema for Azure App Configuration events. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).

Per un elenco di script ed esercitazioni di esempio, vedere Origine evento Configurazione app di Azure.For a list of sample scripts and tutorials, see [Azure App Configuration event source.](event-sources.md#app-configuration)

## <a name="available-event-types"></a>Tipi di evento disponibili

Configurazione app di Azure genera i tipi di evento seguenti:Azure App Configuration emits the following event types:

| Tipo di evento | Descrizione |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModified | Generato quando un key-value viene creato o sostituito. |
| Microsoft.AppConfiguration.KeyValueDeleted | Generato quando viene eliminata una chiave-valore. |

## <a name="example-event"></a>Evento di esempio

Nell'esempio seguente viene illustrato lo schema di un evento di modifica chiave-valore:The following example shows the schema of a key-value modified event: 

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

Lo schema per un evento eliminato chiave-valore è simile:The schema for a key-value deleted event is similar: 

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
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```
 
## <a name="event-properties"></a>Proprietà degli eventi

Un evento presenta i seguenti dati di primo livello:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| argomento | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |
| subject | string | Percorso dell'oggetto dell'evento definito dall'autore. |
| eventType | string | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| id | string | Identificatore univoco dell'evento. |
| data | object | Dati dell'evento di configurazione dell'app. |
| dataVersion | string | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | string | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| Key | string | Chiave della chiave-valore modificata o eliminata. |
| label | string | Etichetta, se presente, della chiave-valore che è stata modificata o eliminata. |
| etag | string | Per `KeyValueModified` l'etag del nuovo valore-chiave. Per `KeyValueDeleted` l'etag della chiave-valore che è stato eliminato. |
 
## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
* Per un'introduzione all'uso degli eventi di configurazione delle app di Azure, vedere Instradare gli eventi di configurazione delle app di Azure - Interfaccia della riga di comando di Azure.For an introduction to working with Azure App [Configuration events, see Route Azure App Configuration events - Azure CLI.](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) 