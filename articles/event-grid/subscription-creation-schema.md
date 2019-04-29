---
title: Schema di sottoscrizione per Griglia di eventi di Azure
description: Descrive le proprietà per la sottoscrizione a un evento con Griglia di eventi di Azure.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/02/2019
ms.author: babanisa
ms.openlocfilehash: 9464ab89e08f53f61cb6f5a4b1e91da35b785af0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822792"
---
# <a name="event-grid-subscription-schema"></a>Schema di sottoscrizione per Griglia di eventi

Per creare una sottoscrizione di Griglia di eventi, si invia una richiesta all'operazione di sottoscrizione Crea evento. Utilizzare il seguente formato:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Ad esempio, per creare una sottoscrizione eventi per un account di archiviazione denominato `examplestorage` in un gruppo di risorse denominato `examplegroup`, usare il formato seguente:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Il nome della sottoscrizione eventi deve essere composto da 3 a 64 caratteri e solo da a-z, A-Z, 0-9, e "-". L'articolo descrive le proprietà e lo schema per il corpo della richiesta.
 
## <a name="event-subscription-properties"></a>Proprietà delle sottoscrizioni eventi

| Proprietà | Type | DESCRIZIONE |
| -------- | ---- | ----------- |
| destination | object | Oggetto che definisce l'endpoint. |
| filter | object | Campo facoltativo per il filtro dei tipi di eventi. |

### <a name="destination-object"></a>oggetto destination

| Proprietà | Type | DESCRIZIONE |
| -------- | ---- | ----------- |
| endpointType | string | Tipo di endpoint per la sottoscrizione (webhook/HTTP, hub eventi o coda). | 
| endpointUrl | string | URL di destinazione per gli eventi della sottoscrizione di eventi. | 

### <a name="filter-object"></a>oggetto filter

| Proprietà | Type | DESCRIZIONE |
| -------- | ---- | ----------- |
| includedEventTypes | array | Corrisponde se il tipo di evento nel messaggio di evento è una corrispondenza esatta a uno di questi nomi di tipo di evento. Genera un errore se il nome dell'evento non corrisponde ad alcuno dei nomi di tipo di evento registrati per l'origine evento. Il valore predefinito corrisponde a tutti i tipi di evento. |
| subjectBeginsWith | string | Filtro di corrispondenza del prefisso per il campo dell'oggetto nel messaggio dell'evento. La stringa predefinita o una stringa vuota corrisponde sempre. | 
| subjectEndsWith | string | Filtro di corrispondenza del suffisso per il campo dell'oggetto nel messaggio dell'evento. La stringa predefinita o una stringa vuota corrisponde sempre. |
| isSubjectCaseSensitive | string | Controlla la corrispondenza tra maiuscole e minuscole per i filtri. |


## <a name="example-subscription-schema"></a>Schema di sottoscrizione di esempio

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "Microsoft.Storage.BlobCreated", "Microsoft.Storage.BlobDeleted" ],
      "subjectBeginsWith": "blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md)