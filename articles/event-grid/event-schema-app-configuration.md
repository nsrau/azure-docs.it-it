---
title: Schema di eventi configurazione di App Azure di griglia di eventi di Azure
description: Vengono descritte le proprietà che sono disponibili per gli eventi di configurazione delle App di Azure con griglia di eventi di Azure
services: event-grid
author: jimmyca
ms.service: event-grid
ms.topic: reference
ms.date: 05/30/2019
ms.author: jimmyca
ms.openlocfilehash: fe0274f723692eea3cfd25cc0e9e146b35dce2ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735782"
---
# <a name="azure-event-grid-event-schema-for-azure-app-configuration"></a>Schema di eventi di griglia di eventi Azure per la configurazione di App di Azure

Questo articolo illustra le proprietà e schema per gli eventi di configurazione delle App di Azure. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).

Per un elenco di esercitazioni e gli script di esempio, vedere [origine evento di configurazione dell'App Azure](event-sources.md#app-configuration).

## <a name="available-event-types"></a>Tipi di evento disponibili

Configurazione di App di Azure genera i tipi di evento seguenti:

| Tipo evento | Descrizione |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModified | Generato quando viene creato o sostituito un valore di chiave. |
| Microsoft.AppConfiguration.KeyValueDeleted | Generato quando viene eliminato un valore di chiave. |

## <a name="example-event"></a>Evento di esempio

Nell'esempio seguente mostra lo schema di un evento di modificato chiave-valore: 

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

Lo schema per un evento deleted chiave-valore è simile: 

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

| Proprietà | Type | DESCRIZIONE |
| -------- | ---- | ----------- |
| topic | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |
| subject | string | Percorso dell'oggetto dell'evento definito dall'autore. |
| eventType | string | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| id | string | Identificatore univoco dell'evento. |
| data | object | Dati di eventi di configurazione App. |
| dataVersion | string | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | string | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| key | string | La chiave del valore di chiave che è stata modificata o eliminata. |
| label | string | L'etichetta, se presente, del valore di chiave che è stato modificato o eliminato. |
| etag | string | Per `KeyValueModified` il valore etag del nuovo chiave-valore. Per `KeyValueDeleted` il valore etag del valore di chiave che è stato eliminato. |
 
## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
* Per un'introduzione all'uso degli eventi di configurazione delle App di Azure, vedere [eventi Route configurazione di App di Azure - CLI Azure](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 