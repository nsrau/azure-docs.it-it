---
title: Schema di eventi del Registro contenitori della Griglia di eventi di Azure
description: Descrive le proprietà disponibili per gli eventi del Registro contenitori con Griglia di eventi di Azure
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 08/13/2018
ms.author: tomfitz
ms.openlocfilehash: d18a6718e4c29f3d04639644dc752b0733f15ba8
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2018
ms.locfileid: "42141768"
---
# <a name="azure-event-grid-event-schema-for-container-registry"></a>Schema di eventi di Griglia di eventi di Azure per il Registro contenitori

Questo articolo illustra le proprietà e lo schema per gli eventi del Registro contenitori. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).

## <a name="available-event-types"></a>Tipi di evento disponibili

L'archiviazione BLOB genera i tipi di eventi seguenti:

| Tipo evento | DESCRIZIONE |
| ---------- | ----------- |
| Microsoft.ContainerRegistry.ImagePushed | Generato quando viene eseguito il push di un'immagine. |
| Microsoft.ContainerRegistry.ImagePushed | Generato quando un'immagine viene eliminata. |

## <a name="example-event"></a>Evento di esempio

L'esempio seguente illustra lo schema di esecuzione del push di un'immagine: 

```json
[{
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld:v1",
  "eventType": "ImagePushed",
  "eventTime": "2018-04-25T21:39:47.6549614Z",
  "data": {
    "id": "31c51664-e5bd-416a-a5df-e5206bc47ed0",
    "timestamp": "2018-04-25T21:39:47.276585742Z",
    "action": "push",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 3023,
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "length": 3023,
      "repository": "aci-helloworld",
      "tag": "v1"
    },
    "request": {
      "id": "7c66f28b-de19-40a4-821c-6f5f6c0003a4",
      "host": "demo.azurecr.io",
      "method": "PUT",
      "useragent": "docker/18.03.0-ce go/go1.9.4 git-commit/0520e24 os/windows arch/amd64 UpstreamClient(Docker-Client/18.03.0-ce \\\\(windows\\\\))"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Lo schema per l'eliminazione di un'immagine è simile:

```json
[{
  "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld",
  "eventType": "ImageDeleted",
  "eventTime": "2018-04-26T17:56:01.8211268Z",
  "data": {
    "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
    "timestamp": "2018-04-26T17:56:00.996603117Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "repository": "aci-helloworld"
    },
    "request": {
      "id": "aeda5b99-4197-409f-b8a8-ff539edb7de2",
      "host": "demo.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Proprietà degli eventi

Un evento presenta i seguenti dati di primo livello:

| Proprietà | type | DESCRIZIONE |
| -------- | ---- | ----------- |
| argomento | stringa | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |
| subject | stringa | Percorso dell'oggetto dell'evento definito dall'autore. |
| eventType | stringa | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | stringa | Ora di generazione dell'evento in base all'ora UTC del provider. |
| id | stringa | Identificatore univoco dell'evento. |
| data | object | Dati relativi all'evento di archiviazione BLOB. |
| dataVersion | stringa | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | stringa | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | type | DESCRIZIONE |
| -------- | ---- | ----------- |
| id | stringa | L'ID evento. |
| timestamp | stringa | L'ora in cui si è verificato l'evento. |
| action | stringa | L'azione che comprende l'evento specificato. |
| target | object | L'URL di destinazione dell'evento. |
| richiesta | object | La richiesta che ha generato l'evento. |

Di seguito sono elencate le proprietà dell'oggetto di destinazione:

| Proprietà | type | DESCRIZIONE |
| -------- | ---- | ----------- |
| mediaType | stringa | Tipo MIME dell'oggetto di riferimento. |
| size | numero intero | Numero di byte del contenuto. Uguale al campo Length. |
| digest | stringa | Digest del contenuto, definito dalla specifica API HTTP del registro contenitori V2. |
| length | numero intero | Numero di byte del contenuto. Uguale al campo Size. |
| repository | stringa | Nome del repository. |
| tag | stringa | Nome tag. |

Di seguito sono elencate le proprietà dell'oggetto della richiesta:

| Proprietà | type | DESCRIZIONE |
| -------- | ---- | ----------- |
| id | stringa | ID della richiesta che ha avviato l'evento. |
| indirizzo | stringa | L'IP o nome host e possibilmente la porta della connessione client che ha avviato l'evento. Questo valore è il RemoteAddr. dalla richiesta http standard. |
| host | stringa | Nome host accessibile esternamente dell'istanza del registro, specificato dall'intestazione host http nelle richieste in ingresso. |
| statico | stringa | Metodo di richiesta che ha generato l'evento. |
| userAgent | stringa | Intestazione agente utente della richiesta. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
