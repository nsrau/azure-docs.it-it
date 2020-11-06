---
title: API REST di configurazione app Azure-etichette
description: Pagine di riferimento per l'uso delle etichette con l'API REST di configurazione app Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: e6fcc8399c1dbc36a5b2e915c726f1c2496ee2f9
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424130"
---
# <a name="labels"></a>Etichette

API-Version: 1,0

La risorsa **Label** viene definita nel modo seguente:

```json
{
      "name": [string]             // Name of the label
}
```

Supporta le operazioni seguenti:

- Elenco

Per tutte le operazioni ``name`` è un parametro di filtro facoltativo. Se omesso, implica **qualsiasi** etichetta.

## <a name="prerequisites"></a>Prerequisiti

- È necessario autenticare tutte le richieste HTTP. Vedere la sezione [autenticazione](./rest-api-authentication-index.md) .
- Tutte le richieste HTTP devono fornire esplicitamente `api-version` . Vedere la sezione sul [controllo delle versioni](./rest-api-versioning.md) .

## <a name="list-labels"></a>Elenca etichette

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**Risposte**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{label-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Paginazione

Il risultato viene impaginato se il numero di elementi restituiti supera il limite di risposta. Seguire le `Link` intestazioni di risposta facoltative e usare `rel="next"` per la navigazione. In alternativa, il contenuto fornisce un collegamento successivo sotto forma di `@nextLink` Proprietà. Il collegamento successivo contiene il `api-version` parametro.

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**Risposta**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8
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

## <a name="filtering"></a>Filtro

Il filtro in base a `name` è supportato.

```http
GET /labels?name={label-name}&api-version={api-version}
```

### <a name="supported-filters"></a>Filtri supportati

|Filtro della chiave|Effetto|
|--|--|
|`name` è omesso oppure `name=*`|Corrisponde a **qualsiasi** etichetta|
|`name=abc`|Corrisponde a un'etichetta denominata  **ABC**|
|`name=abc*`|Corrisponde ai nomi delle etichette che iniziano con **ABC**|
|`name=abc,xyz`|Corrisponde ai nomi di etichetta **ABC** o **XYZ** (limitato a 5 CSV)|

### <a name="reserved-characters"></a>Caratteri riservati

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
  "title": "Invalid request parameter 'name'",
  "name": "name",
  "detail": "name(2): Invalid character",
  "status": 400
}
```

### <a name="examples"></a>Esempi

- Tutti

    ```http
    GET /labels?api-version={api-version}
    ```

- Il nome dell'etichetta inizia con **ABC**

    ```http
    GET  /labels?name=abc*&api-version={api-version}
    ```

- Il nome dell'etichetta può essere **ABC** o **XYZ**

    ```http
    GET /labels?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Campi specifici della richiesta

Usare il `$select` parametro facoltativo della stringa di query e fornire un elenco delimitato da virgole dei campi richiesti. Se il `$select` parametro viene omesso, la risposta conterrà il set predefinito.

```http
GET /labels?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Accesso Time-Based

Ottenere una rappresentazione del risultato in un momento precedente. Vedere la sezione [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)

```http
GET /labels&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Risposta**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json"
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
