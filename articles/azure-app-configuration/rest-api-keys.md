---
title: API REST di configurazione app Azure-chiavi
description: Pagine di riferimento per l'uso delle chiavi con l'API REST di configurazione app Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: f44ecdf571791d54a78d25dde514d57053b59160
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424316"
---
# <a name="keys"></a>Chiavi

API-Version: 1,0

La sintassi seguente rappresenta una risorsa chiave:

```http
{
    "name": [string]             // Name of the key
}
```

## <a name="operations"></a>Operazioni

Le risorse chiave supportano la seguente operazione:

- Elenco

Per tutte le operazioni `name` è un parametro di filtro facoltativo. Se omesso, implica *qualsiasi* chiave.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-keys"></a>Elenco delle chiavi

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**Risposte**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{key-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Paginazione

Il risultato viene impaginato se il numero di elementi restituiti supera il limite di risposta. Seguire le `Link` intestazioni di risposta facoltative e usare `rel="next"` per la navigazione. In alternativa, il contenuto fornisce un collegamento successivo sotto forma di `@nextLink` Proprietà. Il collegamento successivo contiene il `api-version` parametro.

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**Risposta**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8
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

Il filtro in base a ```name``` è supportato.

```http
GET /keys?name={key-name}&api-version={api-version}
```

Sono supportati i filtri seguenti:

|Filtro della chiave|Effetto|
|--|--|
|`name` è omesso oppure `name=*`|Corrisponde a **qualsiasi** chiave|
|`name=abc`|Corrisponde a una chiave denominata  **ABC**|
|`name=abc*`|Corrisponde ai nomi delle chiavi che iniziano con **abc**|
|`name=abc,xyz`|Corrisponde ai nomi delle chiavi **ABC** o **XYZ** (limitato a 5 CSV)|

Sono riservati i seguenti caratteri: `*` , `\` , `,`

Se un carattere riservato è parte del valore, deve essere preceduto da un carattere di escape usando `\{Reserved Character}` . I caratteri non riservati possono anche essere preceduti da un carattere di escape.

## <a name="filter-validation"></a>Convalida filtro

In caso di errore di convalida del filtro, la risposta è HTTP `400` con i dettagli dell'errore:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter 'name'",
  "name": "name",
  "detail": "name(2): Invalid character",
  "status": 400
}
```

## <a name="examples"></a>Esempi

- Tutti

    ```http
    GET /keys?api-version={api-version}
    ```

- Il nome della chiave inizia con **ABC**

    ```http
    GET  /keys?name=abc*&api-version={api-version}
    ```

- Il nome della chiave può essere **ABC** o **XYZ**

    ```http
    GET /keys?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Campi specifici della richiesta

Usare il parametro facoltativo della `$select` stringa di query e fornire un elenco delimitato da virgole dei campi richiesti. Se il `$select` parametro viene omesso, la risposta conterrà il set predefinito.

```http
GET /keys?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Accesso Time-Based

Ottenere una rappresentazione del risultato in un momento precedente. Vedere la sezione [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)

```http
GET /keys&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Risposta**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <relative uri>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```
