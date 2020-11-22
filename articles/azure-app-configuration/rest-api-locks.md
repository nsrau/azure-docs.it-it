---
title: API REST di configurazione app Azure-blocchi
description: Pagine di riferimento per l'utilizzo di blocchi chiave-valore tramite l'API REST di configurazione app Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 7e63b48f2119c48cd43717acee7b13b1701e0032
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2020
ms.locfileid: "95241268"
---
# <a name="locks"></a>Locks

Questa API (versione 1,0) fornisce la semantica di blocco e sblocco per la risorsa chiave-valore. Supporta le operazioni seguenti:

- Inserisci blocco
- Rimuovi blocco

Se presente, `label` deve essere un valore di etichetta esplicito (non un carattere jolly). Per tutte le operazioni, si tratta di un parametro facoltativo. Se omesso, non implica alcuna etichetta.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="lock-key-value"></a>Chiave-valore blocco

- Obbligatorio: ``{key}`` , ``{api-version}``  
- Facoltativo: ``label``

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

## <a name="unlock-key-value"></a>Chiave-valore di sblocco

- Obbligatorio: ``{key}`` , ``{api-version}``  
- Facoltativo: ``label``

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

## <a name="conditional-lock-and-unlock"></a>Blocco condizionale e sblocco

Per evitare race condition, utilizzare `If-Match` o le `If-None-Match` intestazioni della richiesta. L' `etag` argomento fa parte della rappresentazione della chiave. Se `If-Match` `If-None-Match` l'oggetto o viene omesso, l'operazione non è condizionale.

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
