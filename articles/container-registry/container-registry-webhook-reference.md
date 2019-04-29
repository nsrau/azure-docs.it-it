---
title: Informazioni di riferimento sullo schema dei webhook per Registro Azure Container
description: Informazioni di riferimento sul payload JSON della richiesta di webhook per Registro Azure Container.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/05/2019
ms.author: danlep
ms.openlocfilehash: 4c0845b9cf5194ecbd0ab813997e17e070840f44
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331342"
---
# <a name="azure-container-registry-webhook-reference"></a>Informazioni di riferimento sui webhook per Registro Azure Container

È possibile [configurare webhook](container-registry-webhook.md) per il registro contenitori, che generano eventi quando vengono eseguite determinate azioni nel registro. Ad esempio, abilitare webhook che vengono attivati quando un'immagine del contenitore o un grafico Helm viene eseguito il push in un registro di sistema o eliminato. Quando un webhook viene attivato, Registro Azure Container invia una richiesta HTTP o HTTPS contenente informazioni sull'evento a un endpoint specificato. L'endpoint può quindi elaborare il webhook e agire di conseguenza.

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

### <a name="request"></a>request

|Elemento|Type|DESCRIZIONE|
|------------------|----------|-----------|
|`id`|string|ID della richiesta che ha avviato l'evento.|
|`host`|string|Nome host accessibile esternamente dell'istanza del registro, specificato dall'intestazione host HTTP nelle richieste in ingresso.|
|`method`|string|Metodo di richiesta che ha generato l'evento.|
|`useragent`|string|Intestazione agente utente della richiesta.|

### <a name="payload-example-image-push-event"></a>Esempio di payload: evento push di immagini

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

Riportato [CLI di Docker](https://docs.docker.com/engine/reference/commandline/cli/) comandi che attiva l'immagine **push** evento webhook:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>Evento push grafico

Webhook attivato quando viene eseguito il push di un grafico di Helm in un repository.

### <a name="chart-push-event-payload"></a>Payload dell'evento grafico push

|Elemento|Type|DESCRIZIONE|
|-------------|----------|-----------|
|`id`|string|ID dell'evento del webhook.|
|`timestamp`|DateTime|Ora in cui è stato attivato l'evento del webhook.|
|`action`|string|Azione che ha attivato l'evento webhook.|
|[target](#helm_target)|Tipo complesso|Destinazione dell'evento che ha attivato l'evento del webhook.|

### <a name="helm_target"></a>target

|Elemento|Type|DESCRIZIONE|
|------------------|----------|-----------|
|`mediaType`|string|Tipo MIME dell'oggetto di riferimento.|
|`size`|Int32|Numero di byte del contenuto.|
|`digest`|string|Digest del contenuto, definito dalla specifica API HTTP del registro contenitori V2.|
|`repository`|string|Nome del repository.|
|`tag`|string|Nome del tag del grafico.|
|`name`|string|Il nome del grafico.|
|`version`|string|La versione del grafico.|

### <a name="payload-example-chart-push-event"></a>Esempio di payload: evento push grafico

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

Esempio [Azure CLI](/cli/azure/acr) comandi che attiva il **chart_push** evento webhook:

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>Eliminare un evento

Webhook attivato quando un repository di immagini o manifesto viene eliminato. Non attivato quando viene eliminato un tag.

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

### <a name="payload-example-image-delete-event"></a>Esempio di payload: evento di eliminazione di immagini

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

## <a name="chart-delete-event"></a>Evento di eliminazione del grafico

Webhook attivato quando viene eliminato un repository o un grafico Helm. 

### <a name="chart-delete-event-payload"></a>Payload dell'evento grafico delete

|Elemento|Type|DESCRIZIONE|
|-------------|----------|-----------|
|`id`|string|ID dell'evento del webhook.|
|`timestamp`|DateTime|Ora in cui è stato attivato l'evento del webhook.|
|`action`|string|Azione che ha attivato l'evento webhook.|
|[target](#chart_delete_target)|Tipo complesso|Destinazione dell'evento che ha attivato l'evento del webhook.|

### <a name="chart_delete_target"></a> target

|Elemento|Type|DESCRIZIONE|
|------------------|----------|-----------|
|`mediaType`|string|Tipo MIME dell'oggetto di riferimento.|
|`size`|Int32|Numero di byte del contenuto.|
|`digest`|string|Digest del contenuto, definito dalla specifica API HTTP del registro contenitori V2.|
|`repository`|string|Nome del repository.|
|`tag`|string|Nome del tag del grafico.|
|`name`|string|Il nome del grafico.|
|`version`|string|La versione del grafico.|

### <a name="payload-example-chart-delete-event"></a>Esempio di payload: evento di eliminazione del grafico

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

Esempio [Azure CLI](/cli/azure/acr) comandi che attiva il **chart_delete** evento webhook:

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>Passaggi successivi

[Uso dei webhook di Registro Azure Container](container-registry-webhook.md)