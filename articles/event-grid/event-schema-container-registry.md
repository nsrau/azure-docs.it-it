---
title: Schema di eventi del Registro contenitori della Griglia di eventi di Azure
description: Vengono descritte le proprietà che sono disponibili per gli eventi di registro contenitori con griglia di eventi di Azure
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 03/12/2019
ms.author: spelluru
ms.openlocfilehash: c5998ff428c4b6f4c1f7a4087c6ccb27d93773eb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60345465"
---
# <a name="azure-event-grid-event-schema-for-container-registry"></a>Schema di eventi di Griglia di eventi di Azure per il Registro contenitori

Questo articolo illustra le proprietà e lo schema per gli eventi del Registro contenitori. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).

## <a name="available-event-types"></a>Tipi di evento disponibili

Registro contenitori di Azure genera i tipi di evento seguenti:

| Tipo evento | DESCRIZIONE |
| ---------- | ----------- |
| Microsoft.ContainerRegistry.ImagePushed | Generato quando viene eseguito il push di un'immagine. |
| Microsoft.ContainerRegistry.ImagePushed | Generato quando un'immagine viene eliminata. |
| Microsoft.ContainerRegistry.ChartPushed | Generato quando viene eseguito il push di un grafico Helm. |
| Microsoft.ContainerRegistry.ChartDeleted | Generato quando viene eliminato un grafico Helm. |

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

Lo schema per un grafico il push di eventi è simile allo schema per un evento push creati da un'immagine, ma non include un oggetto della richiesta:

```json
[{
  "id": "ea3a9c28-5b17-40f6-a500-3f02b6829277",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartPushed",
  "eventTime": "2019-03-12T22:16:31.5164086Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:16:31.0087496+00:00",
    "action":"chart_push",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Lo schema per un evento grafico eliminato è simile allo schema per un evento eliminato con immagine, ma non include un oggetto della richiesta:

```json
[{
  "id": "39136b3a-1a7e-416f-a09e-5c85d5402fca",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartDeleted",
  "eventTime": "019-03-12T22:42:08.7034064Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:42:08.3783775+00:00",
    "action":"chart_delete",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
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
| data | object | Dati relativi all'evento di archiviazione BLOB. |
| dataVersion | string | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | string | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| id | string | L'ID evento. |
| timestamp | string | L'ora in cui si è verificato l'evento. |
| action | string | L'azione che comprende l'evento specificato. |
| target | object | L'URL di destinazione dell'evento. |
| request | object | La richiesta che ha generato l'evento. |

Di seguito sono elencate le proprietà dell'oggetto di destinazione:

| Proprietà | Type | DESCRIZIONE |
| -------- | ---- | ----------- |
| mediaType | string | Tipo MIME dell'oggetto di riferimento. |
| size | numero intero | Numero di byte del contenuto. Uguale al campo Length. |
| digest | string | Digest del contenuto, definito dalla specifica API HTTP del registro contenitori V2. |
| length | numero intero | Numero di byte del contenuto. Uguale al campo Size. |
| repository | string | Nome del repository. |
| tag | string | Nome tag. |
| name | string | Il nome del grafico. |
| version | string | La versione del grafico. |

Di seguito sono elencate le proprietà dell'oggetto della richiesta:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| id | string | ID della richiesta che ha avviato l'evento. |
| indirizzo | string | L'IP o nome host e possibilmente la porta della connessione client che ha avviato l'evento. Questo valore è il RemoteAddr. dalla richiesta http standard. |
| host | string | Nome host accessibile esternamente dell'istanza del registro, specificato dall'intestazione host http nelle richieste in ingresso. |
| statico | string | Metodo di richiesta che ha generato l'evento. |
| useragent | string | Intestazione agente utente della richiesta. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
