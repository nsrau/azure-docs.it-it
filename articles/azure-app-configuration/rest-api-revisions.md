---
title: API REST di configurazione app Azure-revisioni chiave-valore
description: Pagine di riferimento per l'uso di revisioni chiave-valore tramite l'API REST di configurazione app Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 668345da8bb89412f7b1dd36975c5bed6f229580
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2020
ms.locfileid: "95246385"
---
# <a name="key-value-revisions"></a>Revisioni chiave-valore

Una *revisione chiave-valore* definisce la rappresentazione cronologica di una risorsa chiave-valore. Le revisioni scadono dopo 7 giorni per gli archivi di livello gratuito o 30 giorni per gli archivi di livello standard. Le revisioni supportano l' `List` operazione.

Per tutte le operazioni, ``key`` è un parametro facoltativo. Se omesso, implica qualsiasi chiave.

Per tutte le operazioni, ``label`` è un parametro facoltativo. Se omesso, implica qualsiasi etichetta.

Questo articolo si applica alla versione API 1,0.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-revisions"></a>Elenca revisioni

```http
GET /revisions?label=*&api-version={api-version} HTTP/1.1
```

**Risposte**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8"
Accept-Ranges: items
```

```json
{
    "items": [
        {
          "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
          "key": "{key}",
          "label": "{label}",
          "content_type": null,
          "value": "example value",
          "last_modified": "2017-12-05T02:41:26.4874615+00:00",
          "tags": []
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Paginazione

Il risultato viene impaginato se il numero di elementi restituiti supera il limite di risposta. Seguire l' ``Link`` intestazione di risposta facoltativa e usare ``rel="next"`` per la navigazione. In alternativa, il contenuto fornisce un collegamento successivo sotto forma di ``@nextLink`` Proprietà.

```http
GET /revisions?api-version={api-version} HTTP/1.1
```

**Risposta**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
Accept-Ranges: items
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

## <a name="list-subset-of-revisions"></a>Elenco subset di revisioni

Usare l' `Range` intestazione della richiesta. La risposta contiene un'intestazione `Content-Range`. Se il server non è in grado di soddisfare l'intervallo richiesto, risponde con HTTP `416` ( `RangeNotSatisfiable` ).

```http
GET /revisions?api-version={api-version} HTTP/1.1
Range: items=0-2
```

**Response**.

```http
HTTP/1.1 206 Partial Content
Content-Type: application/vnd.microsoft.appconfig.revs+json; charset=utf-8
Content-Range: items 0-2/80
```

## <a name="filtering"></a>Filtro

È supportata una combinazione di `key` `label` filtri e.
Usare i parametri facoltativi `key` e della `label` stringa di query.

```http
GET /revisions?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>Filtri supportati

|Filtro della chiave|Effetto|
|--|--|
|`key` è omesso oppure `key=*`|Corrisponde a **qualsiasi** chiave|
|`key=abc`|Corrisponde a una chiave denominata  **ABC**|
|`key=abc*`|Corrisponde ai nomi delle chiavi che iniziano con **ABC**|
|`key=*abc`|Corrisponde ai nomi delle chiavi che terminano con **ABC**|
|`key=*abc*`|Corrisponde ai nomi delle chiavi che contengono **ABC**|
|`key=abc,xyz`|Corrisponde ai nomi delle chiavi **ABC** o **XYZ** (limitato a 5 CSV)|

|Filtro etichette|Effetto|
|--|--|
|`label` è omesso oppure `label=`|Corrisponde alla voce senza etichetta|
|`label=*`|Corrisponde a **qualsiasi** etichetta|
|`label=prod`|Corrisponde all'etichetta **Prod**|
|`label=prod*`|Corrisponde alle etichette che iniziano con **Prod**|
|`label=*prod`|Corrisponde alle etichette che terminano con **Prod**|
|`label=*prod*`|Corrisponde alle etichette che contengono **Prod**|
|`label=prod,test`|Corrisponde alle etichette **Prod** o **test** (limitato a 5 CSV)|

### <a name="reserved-characters"></a>Caratteri riservati

I caratteri riservati sono:

`*`, `\`, `,`

Se un carattere riservato è parte del valore, deve essere preceduto da un carattere di escape usando `\{Reserved Character}` . I caratteri non riservati possono anche essere preceduti da un carattere di escape.

### <a name="filter-validation"></a>Convalida filtro

Se si verifica un errore di convalida del filtro, la risposta è HTTP `400` con i dettagli dell'errore:

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

### <a name="examples"></a>Esempio

- Tutto:

    ```http
    GET /revisions
    ```

- Elementi in cui il nome della chiave inizia con **ABC**:

    ```http
    GET /revisions?key=abc*&api-version={api-version}
    ```

- Gli elementi in cui il nome della chiave è **ABC** o **XYZ** e le etichette contengono **Prod**:

    ```http
    GET /revisions?key=abc,xyz&label=*prod*&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Campi specifici della richiesta

Usare il parametro facoltativo della `$select` stringa di query e fornire un elenco delimitato da virgole dei campi richiesti. Se il `$select` parametro viene omesso, la risposta conterrà il set predefinito.

```http
GET /revisions?$select=value,label,last_modified&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Accesso basato sul tempo

Ottenere una rappresentazione del risultato in un momento precedente. Per altre informazioni, vedere [Framework http per l'accesso Time-Based agli Stati delle risorse--Memento](https://tools.ietf.org/html/rfc7089#section-2.1), sezione 2.1.1.

```http
GET /revisions?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Risposta**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.revs+json"
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
