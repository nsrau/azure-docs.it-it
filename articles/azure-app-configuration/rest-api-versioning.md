---
title: API REST di configurazione app Azure-controllo delle versioni
description: Pagine di riferimento per il controllo delle versioni tramite l'API REST di configurazione app Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 3a7f50b26d59501d2be3a0147fe89919819b50e6
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2020
ms.locfileid: "95246368"
---
# <a name="versioning"></a>Controllo delle versioni

Ogni richiesta client deve fornire una versione API esplicita come parametro della stringa di query. Ad esempio: `https://{myconfig}.azconfig.io/kv?api-version=1.0`.

`api-version` è espresso in formato SemVer (Major. minor). La negoziazione di intervallo o di versione non è supportata.

Questo articolo si applica alla versione API 1,0.

Di seguito viene illustrato un riepilogo delle possibili risposte di errore restituite dal server quando non è possibile trovare una corrispondenza per la versione dell'API richiesta.

## <a name="api-version-unspecified"></a>Versione API non specificata

Questo errore si verifica quando un client effettua una richiesta senza fornire una versione API.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "API version is not specified",
  "name": "api-version",
  "detail": "An API version is required, but was not specified.",
  "status": 400
}
```

## <a name="unsupported-api-version"></a>Versione API non supportata

Questo errore si verifica quando una versione API richiesta dal client non corrisponde ad alcuna versione API supportata dal server.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Unsupported API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

## <a name="invalid-api-version"></a>Versione API non valida

Questo errore si verifica quando un client effettua una richiesta con una versione API, ma il valore non è valido o non può essere analizzato dal server.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8  
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

## <a name="ambiguous-api-version"></a>Versione API ambigua

Questo errore si verifica quando un client richiede una versione API ambigua per il server (ad esempio, più valori diversi).

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Ambiguous API version",
  "name": "api-version",
  "detail": "The following API versions were requested: {comma separated api versions}. At most, only a single API version may be specified. Please update the intended API version and retry the request.",
  "status": 400
}
```
