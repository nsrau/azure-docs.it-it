---
title: API REST di configurazione app Azure-blocchi
description: Pagine di riferimento per l'utilizzo di blocchi chiave-valore tramite l'API REST di configurazione app Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 4949db646c54d75f60d29d3c631d0f4ee8d7c26e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424309"
---
# <a name="locks"></a>Locks

API-Version: 1,0

Questa API fornisce la semantica di blocco/sblocco per la risorsa chiave-valore. Supporta le operazioni seguenti:

- Inserisci blocco
- Rimuovi blocco

Se presente, `label` deve essere un valore di etichetta esplicito ( **non** un carattere jolly). Per tutte le operazioni è un parametro facoltativo. Se omesso, non implica alcuna etichetta.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="lock-key-value"></a>Blocca Key-Value

- **Obbligatorio:** ``{key}`` , ``{api-version}``  
- *Facoltativo:*``label``

```http
PUT /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**Risposte**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Se il valore della chiave non esiste, viene restituita la risposta seguente:

```http
HTTP/1.1 404 Not Found
```

## <a name="unlock-key-value"></a>Sblocca Key-Value

- **Obbligatorio:** ``{key}`` , ``{api-version}``  
- *Facoltativo:*``label``

```http
DELETE /locks/{key}?label={label}?api-version={api-version} HTTP/1.1
```

**Risposte**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Se il valore della chiave non esiste, viene restituita la risposta seguente:

```http
HTTP/1.1 404 Not Found
```

## <a name="conditional-lockunlock"></a>Blocco condizionale/sblocco

Per evitare race condition, utilizzare `If-Match` o le `If-None-Match` intestazioni della richiesta. L' `etag` argomento fa parte della rappresentazione della chiave. Se `If-Match` `If-None-Match` l'oggetto o viene omesso, l'operazione sarà non condizionale.

La richiesta seguente applica l'operazione solo se la rappresentazione chiave-valore corrente corrisponde all'oggetto specificato `etag` :

```http
PUT|DELETE /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

La richiesta seguente applica l'operazione solo se la rappresentazione corrente del valore chiave esiste, ma non corrisponde a quanto specificato `etag` :

```http
PUT|DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```
