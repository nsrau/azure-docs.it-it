---
title: Registry webhook schema reference
description: Reference for JSON payload for webhook requests in an Azure container registry, which are generated when webhooks are enabled for artifact push or delete events
ms.topic: article
ms.date: 03/05/2019
ms.openlocfilehash: 8354ef9db24d5825238155ac567d5d829f9b0d7f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455966"
---
# <a name="azure-container-registry-webhook-reference"></a>Informazioni di riferimento sui webhook per Registro Azure Container

È possibile [configurare webhook](container-registry-webhook.md) per il registro contenitori, che generano eventi quando vengono eseguite determinate azioni nel registro. For example, enable webhooks that are triggered when a container image or Helm chart is pushed to a registry, or deleted. Quando un webhook viene attivato, Registro Azure Container invia una richiesta HTTP o HTTPS contenente informazioni sull'evento a un endpoint specificato. L'endpoint può quindi elaborare il webhook e agire di conseguenza.

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

|Elemento|Type|Description|
|-------------|----------|-----------|
|`id`|Stringa|ID dell'evento del webhook.|
|`timestamp`|Data e ora|Ora in cui è stato attivato l'evento del webhook.|
|`action`|Stringa|Azione che ha attivato l'evento webhook.|
|[target](#target)|Tipo complesso|Destinazione dell'evento che ha attivato l'evento del webhook.|
|[request](#request)|Tipo complesso|Richiesta che ha generato l'evento del webhook.|

### <a name="target"></a>target

|Elemento|Type|Description|
|------------------|----------|-----------|
|`mediaType`|Stringa|Tipo MIME dell'oggetto di riferimento.|
|`size`|Int32|Numero di byte del contenuto. Uguale al campo Length.|
|`digest`|Stringa|Digest del contenuto, definito dalla specifica API HTTP del registro contenitori V2.|
|`length`|Int32|Numero di byte del contenuto. Uguale al campo Size.|
|`repository`|Stringa|Nome del repository.|
|`tag`|Stringa|Nome del tag dell'immagine.|

### <a name="request"></a>request

|Elemento|Type|Description|
|------------------|----------|-----------|
|`id`|Stringa|ID della richiesta che ha avviato l'evento.|
|`host`|Stringa|Nome host accessibile esternamente dell'istanza del registro, specificato dall'intestazione host HTTP nelle richieste in ingresso.|
|`method`|Stringa|Metodo di richiesta che ha generato l'evento.|
|`useragent`|Stringa|Intestazione agente utente della richiesta.|

### <a name="payload-example-image-push-event"></a>Payload example: image push event

```JSON
{
  "id": "cb8c3971-9adc-488b-xxxx-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-xxxx-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Example [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) command that triggers the image **push** event webhook:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>Chart push event

Webhook triggered when a Helm chart is pushed to a repository.

### <a name="chart-push-event-payload"></a>Chart push event payload

|Elemento|Type|Description|
|-------------|----------|-----------|
|`id`|Stringa|ID dell'evento del webhook.|
|`timestamp`|Data e ora|Ora in cui è stato attivato l'evento del webhook.|
|`action`|Stringa|Azione che ha attivato l'evento webhook.|
|[target](#helm_target)|Tipo complesso|Destinazione dell'evento che ha attivato l'evento del webhook.|

### <a name="helm_target"></a>target

|Elemento|Type|Description|
|------------------|----------|-----------|
|`mediaType`|Stringa|Tipo MIME dell'oggetto di riferimento.|
|`size`|Int32|Numero di byte del contenuto.|
|`digest`|Stringa|Digest del contenuto, definito dalla specifica API HTTP del registro contenitori V2.|
|`repository`|Stringa|Nome del repository.|
|`tag`|Stringa|The chart tag name.|
|`name`|Stringa|The chart name.|
|`version`|Stringa|The chart version.|

### <a name="payload-example-chart-push-event"></a>Payload example: chart push event

```JSON
{
  "id": "6356e9e0-627f-4fed-xxxx-d9059b5143ac",
  "timestamp": "2019-03-05T23:45:31.2614267Z",
  "action": "chart_push",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Example [Azure CLI](/cli/azure/acr) command that triggers the **chart_push** event webhook:

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>Eliminare un evento

Webhook triggered when an image repository or manifest is deleted. Non attivato quando viene eliminato un tag.

### <a name="delete-event-payload"></a>Payload dell'evento delete

|Elemento|Type|Description|
|-------------|----------|-----------|
|`id`|Stringa|ID dell'evento del webhook.|
|`timestamp`|Data e ora|Ora in cui è stato attivato l'evento del webhook.|
|`action`|Stringa|Azione che ha attivato l'evento webhook.|
|[target](#delete_target)|Tipo complesso|Destinazione dell'evento che ha attivato l'evento del webhook.|
|[request](#delete_request)|Tipo complesso|Richiesta che ha generato l'evento del webhook.|

### <a name="delete_target"></a> target

|Elemento|Type|Description|
|------------------|----------|-----------|
|`mediaType`|Stringa|Tipo MIME dell'oggetto di riferimento.|
|`digest`|Stringa|Digest del contenuto, definito dalla specifica API HTTP del registro contenitori V2.|
|`repository`|Stringa|Nome del repository.|

### <a name="delete_request"></a> request

|Elemento|Type|Description|
|------------------|----------|-----------|
|`id`|Stringa|ID della richiesta che ha avviato l'evento.|
|`host`|Stringa|Nome host accessibile esternamente dell'istanza del registro, specificato dall'intestazione host HTTP nelle richieste in ingresso.|
|`method`|Stringa|Metodo di richiesta che ha generato l'evento.|
|`useragent`|Stringa|Intestazione agente utente della richiesta.|

### <a name="payload-example-image-delete-event"></a>Payload example: image delete event

```JSON
{
    "id": "afc359ce-df7f-4e32-xxxx-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-xxxx-7ca9ecf559b3",
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

## <a name="chart-delete-event"></a>Chart delete event

Webhook triggered when a Helm chart or repository is deleted. 

### <a name="chart-delete-event-payload"></a>Chart delete event payload

|Elemento|Type|Description|
|-------------|----------|-----------|
|`id`|Stringa|ID dell'evento del webhook.|
|`timestamp`|Data e ora|Ora in cui è stato attivato l'evento del webhook.|
|`action`|Stringa|Azione che ha attivato l'evento webhook.|
|[target](#chart_delete_target)|Tipo complesso|Destinazione dell'evento che ha attivato l'evento del webhook.|

### <a name="chart_delete_target"></a> target

|Elemento|Type|Description|
|------------------|----------|-----------|
|`mediaType`|Stringa|Tipo MIME dell'oggetto di riferimento.|
|`size`|Int32|Numero di byte del contenuto.|
|`digest`|Stringa|Digest del contenuto, definito dalla specifica API HTTP del registro contenitori V2.|
|`repository`|Stringa|Nome del repository.|
|`tag`|Stringa|The chart tag name.|
|`name`|Stringa|The chart name.|
|`version`|Stringa|The chart version.|

### <a name="payload-example-chart-delete-event"></a>Payload example: chart delete event

```JSON
{
  "id": "338a3ef7-ad68-4128-xxxx-fdd3af8e8f67",
  "timestamp": "2019-03-06T00:10:48.1270754Z",
  "action": "chart_delete",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Example [Azure CLI](/cli/azure/acr) command that triggers the **chart_delete** event webhook:

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>Passaggi successivi

[Uso dei webhook di Registro Azure Container](container-registry-webhook.md)