---
title: API REST di configurazione app Azure-chiave-valore
description: Pagine di riferimento per l'uso di valori di chiave tramite l'API REST di configurazione app Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: f89b3f2fa4805eeb2fd9f9d511c8f228b98139ac
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2020
ms.locfileid: "95241030"
---
# <a name="key-values"></a>Coppie chiave-valore

Un valore chiave è una risorsa identificata da una combinazione univoca di `key`  +  `label` . `label` è facoltativo. Per fare riferimento in modo esplicito a un valore chiave senza etichetta, usare "\ 0" (URL codificato come ``%00`` ). Vedere i dettagli per ogni operazione.

Questo articolo si applica alla versione API 1,0.

## <a name="operations"></a>Gestione operativa

- Recupero
- Elenca più
- Set
- Elimina

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="syntax"></a>Sintassi

```json
{
  "etag": [string],
  "key": [string],
  "label": [string, optional],
  "content_type": [string, optional],
  "value": [string],
  "last_modified": [datetime ISO 8601],
  "locked": [boolean],
  "tags": [object with string properties, optional]
}
```

## <a name="get-key-value"></a>Ottieni chiave-valore

Obbligatorio: ``{key}`` , ``{api-version}``  
Facoltativo: ``label`` (se omesso, implica un valore chiave senza etichetta).

```http
GET /kv/{key}?label={label}&api-version={api-version}
```

**Risposte**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26+00:00",
  "locked": "false",
  "tags": {
    "t1": "value1",
    "t2": "value2"
  }
}
```

Se la chiave non esiste, viene restituita la risposta seguente:

```http
HTTP/1.1 404 Not Found
```

## <a name="get-conditionally"></a>Get (in modo condizionale)

Per migliorare la memorizzazione nella cache del client, utilizzare `If-Match` o le `If-None-Match` intestazioni della richiesta. L' `etag` argomento fa parte della rappresentazione della chiave. Per ulteriori informazioni, vedere le [sezioni 14,24 e 14,26](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

La richiesta seguente recupera il valore della chiave solo se la rappresentazione corrente non corrisponde all'oggetto specificato `etag` :

```http
GET /kv/{key}?api-version={api-version} HTTP/1.1
Accept: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "{etag}"
```

**Risposte**

```http
HTTP/1.1 304 NotModified
```

oppure

```http
HTTP/1.1 200 OK
```

## <a name="list-key-values"></a>Elenca valori chiave

Facoltativo: ``key`` (se non specificato, implica qualsiasi chiave). Facoltativo: ``label`` (se non specificato, implica qualsiasi etichetta).

```http
GET /kv?label=*&api-version={api-version} HTTP/1.1
```

**Risposta**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8
```

Per altre opzioni, vedere la sezione "filtro" più avanti in questo articolo.

## <a name="pagination"></a>Paginazione

Il risultato viene impaginato se il numero di elementi restituiti supera il limite di risposta. Seguire le `Link` intestazioni di risposta facoltative e usare `rel="next"` per la navigazione.
In alternativa, il contenuto fornisce un collegamento successivo sotto forma di `@nextLink` Proprietà. L'URI collegato include l' `api-version` argomento.

```http
GET /kv?api-version={api-version} HTTP/1.1
```

**Risposta**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
Link: <{relative uri}>; rel="next"
```

```json
{
    "items": [
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="filtering"></a>Filtro

È supportata una combinazione di `key` `label` filtri e.
Usare i parametri facoltativi `key` e della `label` stringa di query.

```http
GET /kv?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>Filtri supportati

|Filtro della chiave|Effetto|
|--|--|
|`key` è omesso oppure `key=*`|Corrisponde a **qualsiasi** chiave|
|`key=abc`|Corrisponde a una chiave denominata **ABC**|
|`key=abc*`|Corrisponde ai nomi delle chiavi che iniziano con **ABC**|
|`key=abc,xyz`|Corrisponde ai nomi delle chiavi **ABC** o **XYZ** (limitato a 5 CSV)|

|Filtro etichette|Effetto|
|--|--|
|`label` è omesso oppure `label=*`|Corrisponde a **qualsiasi** etichetta|
|`label=%00`|Corrisponde a KV senza etichetta|
|`label=prod`|Corrisponde all'etichetta **Prod**|
|`label=prod*`|Corrisponde alle etichette che iniziano con **Prod**|
|`label=prod,test`|Corrisponde alle etichette **Prod** o **test** (limitato a 5 CSV)|

**_Caratteri riservati_* _

`_`, `\`, `,`

Se un carattere riservato è parte del valore, deve essere preceduto da un carattere di escape usando `\{Reserved Character}` . I caratteri non riservati possono anche essere preceduti da un carattere di escape.

***Convalida filtro** _

In caso di errore di convalida del filtro, la risposta è HTTP `400` con i dettagli dell'errore:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter '{filter}'",
  "name": "{filter}",
  "detail": "{filter}(2): Invalid character",
  "status": 400
}
```

_ *Esempi**

- Tutti

    ```http
    GET /kv?api-version={api-version}
    ```

- Il nome della chiave inizia con **ABC** e include tutte le etichette

    ```http
    GET /kv?key=abc*&label=*&api-version={api-version}
    ```

- Il nome della chiave inizia con **ABC** ed etichetta uguale a **V1** o **v2**

    ```http
    GET /kv?key=abc*&label=v1,v2&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Campi specifici della richiesta

Usare il parametro facoltativo della `$select` stringa di query e fornire un elenco delimitato da virgole dei campi richiesti. Se il `$select` parametro viene omesso, la risposta conterrà il set predefinito.

```http
GET /kv?$select=key,value&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Accesso basato sul tempo

Ottenere una rappresentazione del risultato in un momento precedente. Per ulteriori informazioni, vedere la sezione [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1). La paginazione è ancora supportata come definito in precedenza in questo articolo.

```http
GET /kv?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Risposta**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <{relative uri}>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```

## <a name="set-key"></a>Imposta chiave

- Obbligatorio: ``{key}``
- Facoltativo: ``label`` (se non è specificato o etichetta = %00, significa che il valore della chiave non è un'etichetta).

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
```

```json
{
  "value": "example value",         // optional
  "content_type": "user defined",   // optional
  "tags": {                         // optional
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

**Risposte**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": "user defined",
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26.4874615+00:00",
  "tags": {
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

Se l'elemento è bloccato, si riceverà la risposta seguente:

```http
HTTP/1.1 409 Conflict
Content-Type: application/problem+json; charset="utf-8"
```

```json
{
    "type": "https://azconfig.io/errors/key-locked",
    "title": "Modifing key '{key}' is not allowed",
    "name": "{key}",
    "detail": "The key is read-only. To allow modification unlock it first.",
    "status": "409"
}
```

## <a name="set-key-conditionally"></a>Imposta chiave (in modo condizionale)

Per evitare race condition, utilizzare `If-Match` o le `If-None-Match` intestazioni della richiesta. L' `etag` argomento fa parte della rappresentazione della chiave.
Se `If-Match` `If-None-Match` l'oggetto o viene omesso, l'operazione non è condizionale.

La risposta seguente aggiorna il valore solo se la rappresentazione corrente corrisponde all'oggetto specificato `etag` :

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

La risposta seguente aggiorna il valore solo se la rappresentazione corrente non corrisponde all'oggetto specificato `etag` :

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

La richiesta seguente aggiunge il valore solo se esiste già una rappresentazione:

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-Match: "*"
```

La richiesta seguente aggiunge il valore solo se non esiste già una rappresentazione:

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-None-Match: "*"
```

**Risposte**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

oppure

```http
HTTP/1.1 412 PreconditionFailed
```

## <a name="delete"></a>Elimina

- Obbligatorio: `{key}` , `{api-version}`
- Facoltativo: `{label}` (se non è specificato o etichetta = %00, significa che il valore della chiave non è un'etichetta).

```http
DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**Risposta:** Restituisce il valore chiave eliminato oppure None se il valore della chiave non esiste.

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

oppure

```http
HTTP/1.1 204 No Content
```

## <a name="delete-key-conditionally"></a>Elimina chiave (in modo condizionale)

Questa operazione è simile alla sezione "impostare la chiave (in modo condizionale)" più indietro in questo articolo.
