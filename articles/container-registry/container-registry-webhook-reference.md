---
title: Informazioni di riferimento sullo schema dei webhook per Registro Azure Container
description: Informazioni di riferimento sul payload JSON della richiesta di webhook per Registro Azure Container.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 12/02/2017
ms.author: danlep
ms.openlocfilehash: 42790905509e2ea8bbba87587ed01b1929221db5
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2019
ms.locfileid: "56329320"
---
# <a name="azure-container-registry-webhook-reference"></a>Informazioni di riferimento sui webhook per Registro Azure Container

È possibile [configurare webhook](container-registry-webhook.md) per il registro contenitori, che generano eventi quando vengono eseguite determinate azioni nel registro. Abilitare, ad esempio, webhook che vengono attivati per le operazioni `push` e `delete` dell'immagine del contenitore. Quando un webhook viene attivato, Registro Azure Container invia una richiesta HTTP o HTTPS contenente informazioni sull'evento a un endpoint specificato. L'endpoint può quindi elaborare il webhook e agire di conseguenza.

Le sezioni seguenti illustrano in dettaglio lo schema delle richieste dei webhook generate da eventi supportati. Le sezioni sugli eventi contengono lo schema del payload per il tipo di evento, un payload della richiesta di esempio e uno o più comandi di esempio che attiverebbero il webhook.

Per informazioni sulla configurazione di webhook per Registro Azure Container, vedere [Uso dei webhook di Registro Azure Container](container-registry-webhook.md).

## <a name="webhook-requests"></a>Richieste dei webhook

### <a name="http-request"></a>Richiesta HTTP

Un webhook attivato crea una richiesta HTTP `POST` per l'endpoint dell'URL specificato quando si è configurato il webhook.

### <a name="http-headers"></a>Intestazioni HTTP

Le richieste dei webhook includono un'intestazione `Content-Type` di tipo `application/json` se non è stata specificata un'intestazione personalizzata `Content-Type` per il webhook.

Non vengono aggiunte alla richiesta altre intestazioni oltre a quelle personalizzate eventualmente specificate per il webhook.

## <a name="push-event"></a>Evento push

Webhook attivato quando viene eseguito il push di un'immagine del contenitore in un repository.

### <a name="push-event-payload"></a>Payload dell'evento push

|Elemento|Type|DESCRIZIONE|
|-------------|----------|-----------|
|`id`|string|ID dell'evento del webhook.|
|`timestamp`|DateTime|Ora in cui è stato attivato l'evento del webhook.|
|`action`|string|Azione che ha attivato l'evento webhook.|
|[target](#target)|Tipo complesso|Destinazione dell'evento che ha attivato l'evento del webhook.|
|[request](#request)|Tipo complesso|Richiesta che ha generato l'evento del webhook.|

### <a name="target"></a>target

|Elemento|Type|DESCRIZIONE|
|------------------|----------|-----------|
|`mediaType`|string|Tipo MIME dell'oggetto di riferimento.|
|`size`|Int32|Numero di byte del contenuto. Uguale al campo Length.|
|`digest`|string|Digest del contenuto, definito dalla specifica API HTTP del registro contenitori V2.|
|`length`|Int32|Numero di byte del contenuto. Uguale al campo Size.|
|`repository`|string|Nome del repository.|
|`tag`|string|Nome del tag dell'immagine.|

### <a name="request"></a>richiesta

|Elemento|Type|DESCRIZIONE|
|------------------|----------|-----------|
|`id`|string|ID della richiesta che ha avviato l'evento.|
|`host`|string|Nome host accessibile esternamente dell'istanza del registro, specificato dall'intestazione host HTTP nelle richieste in ingresso.|
|`method`|string|Metodo di richiesta che ha generato l'evento.|
|`useragent`|string|Intestazione agente utente della richiesta.|

### <a name="payload-example-push-event"></a>Esempio di payload: evento push

```JSON
{
  "id": "cb8c3971-9adc-488b-bdd8-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:80f0d5c8786bb9e621a45ece0db56d11cdc624ad20da9fe62e9d25490f331d7d",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-86cd-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Comando dell'[interfaccia della riga di comando di Docker](https://docs.docker.com/engine/reference/commandline/cli/) di esempio che attiva il webhook dell'evento **push**:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="delete-event"></a>Eliminare un evento

Webhook attivato quando viene eliminato un repository o un manifesto. Non attivato quando viene eliminato un tag.

### <a name="delete-event-payload"></a>Payload dell'evento delete

|Elemento|Type|DESCRIZIONE|
|-------------|----------|-----------|
|`id`|string|ID dell'evento del webhook.|
|`timestamp`|DateTime|Ora in cui è stato attivato l'evento del webhook.|
|`action`|string|Azione che ha attivato l'evento webhook.|
|[target](#delete_target)|Tipo complesso|Destinazione dell'evento che ha attivato l'evento del webhook.|
|[request](#delete_request)|Tipo complesso|Richiesta che ha generato l'evento del webhook.|

### <a name="delete_target"></a> target

|Elemento|Type|DESCRIZIONE|
|------------------|----------|-----------|
|`mediaType`|string|Tipo MIME dell'oggetto di riferimento.|
|`digest`|string|Digest del contenuto, definito dalla specifica API HTTP del registro contenitori V2.|
|`repository`|string|Nome del repository.|

### <a name="delete_request"></a> request

|Elemento|Type|DESCRIZIONE|
|------------------|----------|-----------|
|`id`|string|ID della richiesta che ha avviato l'evento.|
|`host`|string|Nome host accessibile esternamente dell'istanza del registro, specificato dall'intestazione host HTTP nelle richieste in ingresso.|
|`method`|string|Metodo di richiesta che ha generato l'evento.|
|`useragent`|string|Intestazione agente utente della richiesta.|

### <a name="payload-example-delete-event"></a>Esempio di payload: evento delete

```JSON
{
    "id": "afc359ce-df7f-4e32-bdde-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:80f0d5c8786bb9e621a45ece0db56d11cdc624ad20da9fe62e9d25490f331d7d",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-807f-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

Comandi dell'[interfaccia della riga di comando di Azure](/cli/azure/acr) di esempio che attivano il webhook di un evento **delete**:

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="next-steps"></a>Passaggi successivi

[Uso dei webhook di Registro Azure Container](container-registry-webhook.md)