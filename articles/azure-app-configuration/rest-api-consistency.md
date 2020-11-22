---
title: API REST di configurazione app Azure-coerenza
description: Pagine di riferimento per garantire la coerenza in tempo reale usando l'API REST di configurazione app Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: db9553c2c9c79a6beb9c66d0cb1a1a60435b2abd
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2020
ms.locfileid: "95253338"
---
# <a name="real-time-consistency"></a>Coerenza in tempo reale

A causa della natura di alcuni sistemi distribuiti, la coerenza in tempo reale tra le richieste è difficile da applicare in modo implicito. Una soluzione consiste nel consentire il supporto del protocollo sotto forma di più token di sincronizzazione. I token di sincronizzazione sono facoltativi.

## <a name="initial-request"></a>Richiesta iniziale

Per garantire la coerenza in tempo reale tra istanze e richieste client diverse, usare `Sync-Token` intestazioni di richiesta e risposta facoltative.

Sintassi:

```http
Sync-Token: <id>=<value>;sn=<sn>
```

|Parametro|Descrizione|
|--|--|
| `<id>` | ID token (opaco) |
| `<value>` | Valore del token (opaco). Consente la stringa con codifica Base64. |
| `<sn>` | Numero di sequenza del token (versione). Superiore indica una versione più recente dello stesso token. Consente una migliore concorrenza e la memorizzazione nella cache del client. Il client può scegliere di usare solo l'ultima versione del token, perché le versioni dei token sono inclusivi. Questo parametro non è obbligatorio per le richieste. |

## <a name="response"></a>Risposta

Il servizio fornisce un' `Sync-Token` intestazione con ogni risposta.

```http
Sync-Token: jtqGc1I4=MDoyOA==;sn=28
```

## <a name="subsequent-requests"></a>Richieste successive

Qualsiasi richiesta successiva garantisce una risposta coerente in tempo reale rispetto al fornito `Sync-Token` .

```http
Sync-Token: <id>=<value>
```

Se si omette l' `Sync-Token` intestazione dalla richiesta, il servizio può rispondere con i dati memorizzati nella cache durante un breve periodo di tempo (fino a pochi secondi), prima che venga risolto internamente. Questo comportamento può causare letture incoerenti se le modifiche sono state apportate immediatamente prima della lettura.

## <a name="multiple-sync-tokens"></a>Più token di sincronizzazione

Il server potrebbe rispondere con più token di sincronizzazione per una singola richiesta. Per garantire la coerenza in tempo reale per la richiesta successiva, il client deve rispondere con tutti i token di sincronizzazione ricevuti. Più valori di intestazione devono essere separati da virgole.

```http
Sync-Token: <token1-id>=<value>,<token2-id>=<value>
```
