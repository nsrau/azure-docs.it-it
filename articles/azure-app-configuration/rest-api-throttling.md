---
title: API REST di configurazione app Azure-limitazione
description: Pagine di riferimento per comprendere la limitazione quando si usa l'API REST di configurazione di app Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 26464138b7739bb476d9d0dd1078194473ecd23e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424270"
---
# <a name="throttling"></a>Limitazione

Gli archivi di configurazione sono limitati alle richieste che possono essere utilizzate. Tutte le richieste che superano una quota assegnata per un archivio di configurazione riceveranno una risposta HTTP 429 (numero eccessivo di richieste).

La limitazione è divisa in criteri di quota diversi:

- **Totale richieste** -numero totale di richieste
- **Larghezza di banda totale** -dati in uscita in byte
- **Archiviazione** -dimensioni di archiviazione totali dei dati utente in byte

## <a name="handling-throttled-responses"></a>Gestione delle risposte limitate

Quando viene raggiunto il limite di velocità per una determinata quota, il server risponderà ad altre richieste del tipo con un codice di stato _429_ . La risposta _429_ conterrà un'intestazione _Retry-After-MS_ che fornisce al client un tempo di attesa suggerito (in millisecondi) per consentire il rifornimento della quota della richiesta.

```http
HTTP/1.1 429 (Too Many Requests)
retry-after-ms: 10
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/too-many-requests",
  "title": "Resource utilization has surpassed the assigned quota",
  "policy": "Total Requests",
  "status": 429
}
```

Nell'esempio precedente il client ha superato la quota consentita ed è consigliabile rallentare e attendere 10 millisecondi prima di provare altre richieste. I client devono considerare anche backoff progressivi.

## <a name="other-retry"></a>Altro tentativo

Il servizio può identificare situazioni diverse dalla limitazione che richiedono un nuovo tentativo client (ad esempio, il servizio 503 non è disponibile). In tutti questi casi, `retry-after-ms` verrà fornita l'intestazione della risposta. Per aumentare l'affidabilità, è consigliabile che il client segua l'intervallo suggerito ed esegua un nuovo tentativo.

```http
HTTP/1.1 503 Service Unavailable
retry-after-ms: 787
```
