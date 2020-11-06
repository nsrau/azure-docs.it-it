---
title: API REST di configurazione app Azure-intestazioni
description: Pagine di riferimento per le intestazioni usate con l'API REST di configurazione app Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 5e1f92c68004d0197391ab72df775913c0940fec
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424319"
---
# <a name="headers"></a>Intestazioni

Questo articolo fornisce i collegamenti alle pagine di riferimento per le intestazioni usate con l'API REST di configurazione app Azure.

## <a name="request-headers"></a>Intestazioni richiesta

Nella tabella seguente vengono descritte le intestazioni di richiesta comuni utilizzate nella configurazione app Azure.

| Intestazione | Descrizione | Esempio |
|--|--|--|
| **Autorizzazione** | Utilizzato per [autenticare](./rest-api-authentication-index.md) una richiesta al servizio. Vedere la [sezione 14,8](https://tools.ietf.org/html/rfc2616#section-14.8) | `Authorization: HMAC-SHA256 Credential=<Credential>&SignedHeaders=Host;x-ms-date;x-ms-content-sha256&Signature=<Signature>` |
| **Accettare** | Informa il server del tipo di supporto che il client accetterà in una risposta HTTP. Vedere la [sezione 14,1](https://tools.ietf.org/html/rfc2616#section-14.1) | `Accept: application/vnd.microsoft.appconfig.kv+json;` |
| **Accept-DateTime** | Richiede al server di restituire il contenuto come una rappresentazione dello stato precedente. Il valore di questa intestazione è il valore DateTime richiesto di tale stato. Vedere [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) | `Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **Content-Type** | Contiene il tipo di supporto del contenuto all'interno del corpo della richiesta HTTP. Vedere la [sezione 14,17](https://tools.ietf.org/html/rfc2616#section-14.17) | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **Data** | Data e ora in cui è stata emessa la richiesta HTTP. Questa intestazione viene utilizzata nell' [autenticazione HMAC](./rest-api-authentication-hmac.md). Vedere la [sezione 14,18](https://tools.ietf.org/html/rfc2616#section-14.18) | `Date: Fri, 11 May 2018 18:48:36 GMT` |
| **Host** | Specifica il tenant per il quale è stata eseguita la richiesta. Questa intestazione viene utilizzata nell' [autenticazione HMAC](./rest-api-authentication-hmac.md). Vedere la [sezione 14,23](https://tools.ietf.org/html/rfc2616#section-14.23) | `Host: contoso.azconfig.io` |
| **If-Match** | Utilizzato per rendere condizionale una richiesta HTTP. Questa richiesta deve avere esito positivo solo se l'ETag della risorsa di destinazione corrisponde al valore di questa intestazione. Il valore "*" corrisponde a qualsiasi ETag. Vedere la [sezione 14,24](https://tools.ietf.org/html/rfc2616#section-14.24) | `If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **If-None-Match** | Utilizzato per rendere condizionale una richiesta HTTP. Questa richiesta deve avere esito positivo solo se l'ETag della risorsa di destinazione non corrisponde al valore di questa intestazione. Il valore "*" corrisponde a qualsiasi ETag. Vedere la [sezione 14,26](https://tools.ietf.org/html/rfc2616#section-14.26) | `If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Sync-token** | Usato per abilitare la coerenza in tempo reale durante una sequenza di richieste. | `Sync-Token: jtqGc1I4=MDoyOA==;sn=28` |
| **x-ms-client-request-id** | ID univoco fornito dal client utilizzato per tenere traccia del round trip di una richiesta. | `x-ms-client-request-id: 00000000-0000-0000-0000-000000000000` |
| **x-ms-content-SHA256** | Un digest SHA256 del corpo della richiesta HTTP. Questa intestazione viene utilizzata nell' [autenticazione HMAC](./rest-api-authentication-hmac.md). | `x-ms-content-sha256: 47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU=` |
| **x-ms-date** | Questa intestazione può essere impostata e utilizzata al posto dell' `Date` intestazione se non è possibile accedere all'intestazione Data. Questa intestazione viene utilizzata nell' [autenticazione HMAC](./rest-api-authentication-hmac.md). | `x-ms-date: Fri, 11 May 2018 18:48:36 GMT` |
| **x-ms-return-client-request-id** | Utilizzato in combinazione con l' `x-ms-client-request-id` intestazione. Se il valore di questa intestazione è' true ', al server verrà richiesto di restituire il valore dell' `x-ms-client-request-id` intestazione della richiesta. | `x-ms-return-client-request-id: true` |

## <a name="response-headers"></a>Intestazioni della risposta

Nelle risposte del server possono essere incluse le intestazioni HTTP seguenti.

| Intestazione | Descrizione | Esempio |
|--|--|--|
| **Content-Type** | Contiene il tipo di supporto del contenuto all'interno del corpo della risposta HTTP. Vedere la [sezione 14,17](https://tools.ietf.org/html/rfc2616#section-14.17) | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **ETag** | Token opaco che rappresenta lo stato di una risorsa specificata. Può essere usato nelle operazioni condizionali. Vedere la [sezione 14,19](https://tools.ietf.org/html/rfc2616#section-14.19) | `ETag: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Ultima modifica** | Descrive la data dell'Ultima modifica della risorsa richiesta. Formattato come [Data http](https://tools.ietf.org/html/rfc2616#section-3.3.1). Vedere la [sezione 14,29](https://tools.ietf.org/html/rfc2616#section-14.29) | `Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT` |
| **Collegamento** | Fornisce collegamenti a risorse correlate alla risposta. Questa intestazione viene utilizzata per il paging utilizzando il collegamento _successivo_ . Vedere [RFC 5988](https://tools.ietf.org/html/rfc5988) | `Link: </kv?after={token}>; rel="next"` |
| **Memento-DateTime** | Indica che il contenuto contenuto in una risposta rappresenta uno stato precedente. Il valore di questa intestazione è il valore DateTime di tale stato. Vedere [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) | `Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **Retry-After-MS** | Fornisce un periodo suggerito (in millisecondi) affinché il client attenda prima di ritentare una richiesta non riuscita. | `retry-after-ms: 10` |
| **x-ms-request-id** | ID univoco generato dal server utilizzato per tenere traccia della richiesta all'interno del servizio. | `x-ms-request-id: 00000000-0000-0000-0000-000000000000` |
| **WWW-Authenticate** | Utilizzato per richiedere l'autenticazione dei client e fornire un motivo per il motivo per cui un tentativo di autenticazione non è riuscito. Vedere la [sezione 14,47](https://tools.ietf.org/html/rfc2616#section-14.47) | `WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Signature"` |
