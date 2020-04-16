---
title: Configurazione app di Azure come origine griglia di eventiAzure App Configuration as Event Grid source
description: Questo articolo descrive come usare Configurazione app di Azure come origine eventi Griglia di eventi. Fornisce lo schema e i collegamenti agli articoli di esercitazione e procedura.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: adb548ef8531698a2cb075fbc742bb20a02a434b
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393420"
---
# <a name="azure-app-configuration-as-an-event-grid-source"></a>Configurazione delle app di Azure come origine griglia di eventiAzure App Configuration as an Event Grid source
Questo articolo fornisce le proprietà e lo schema per gli eventi di configurazione delle app di Azure.This article provides the properties and schema for Azure App Configuration events. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md). Fornisce inoltre un elenco di guide introduttive ed esercitazioni per usare Configurazione app di Azure come origine eventi.

## <a name="event-grid-event-schema"></a>Schema di eventi di Griglia di eventi

### <a name="available-event-types"></a>Tipi di evento disponibili

Configurazione app di Azure genera i tipi di evento seguenti:Azure App Configuration emits the following event types:

| Tipo di evento | Descrizione |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModified | Generato quando un key-value viene creato o sostituito. |
| Microsoft.AppConfiguration.KeyValueDeleted | Generato quando viene eliminata una chiave-valore. |

### <a name="example-event"></a>Evento di esempio

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
 
### <a name="event-properties"></a>Proprietà degli eventi

Un evento presenta i seguenti dati di primo livello:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| argomento | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |
| subject | string | Percorso dell'oggetto dell'evento definito dall'autore. |
| eventType | string | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| ID | string | Identificatore univoco dell'evento. |
| data | object | Dati dell'evento di configurazione dell'app. |
| dataVersion | string | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | string | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| Key | string | Chiave della chiave-valore modificata o eliminata. |
| label | string | Etichetta, se presente, della chiave-valore che è stata modificata o eliminata. |
| etag | string | Per `KeyValueModified` l'etag del nuovo valore-chiave. Per `KeyValueDeleted` l'etag della chiave-valore che è stato eliminato. |

## <a name="tutorials-and-how-tos"></a>Esercitazioni ed es.

|Titolo | Descrizione |
|---------|---------|
| [Reagire agli eventi di configurazione delle app di Azure usando Griglia di eventiReact to Azure App Configuration events by using Event Grid](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Panoramica dell'integrazione della configurazione delle app di Azure con Griglia di eventi. |
| [Guida introduttiva: indirizzare gli eventi di configurazione dell'app di Azure a un endpoint Web personalizzato con l'interfaccia della riga di comando di AzureQuickstart: route Azure App Configuration events to a custom web endpoint with Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Viene illustrato come usare l'interfaccia della riga di comando di Azure per inviare eventi di configurazione delle app di Azure a un WebHook.Shows how to use Azure CLI to send Azure App Configuration events to a WebHook. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
* Per un'introduzione all'uso degli eventi di configurazione delle app di Azure, vedere Instradare gli eventi di configurazione delle app di Azure - Interfaccia della riga di comando di Azure.For an introduction to working with Azure App [Configuration events, see Route Azure App Configuration events - Azure CLI.](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) 