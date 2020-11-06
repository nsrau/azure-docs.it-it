---
title: API REST di configurazione app Azure-coerenza
description: Pagine di riferimento per garantire la coerenza in tempo reale usando l'API REST di configurazione app Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 4f11e6edcd4bc128f815db7e93b00b72bf990ea8
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424346"
---
# <a name="real-time-consistency"></a>Coerenza in tempo reale

A causa della natura di alcuni sistemi distribuiti, la coerenza in tempo reale tra le richieste è difficile da applicare in modo implicito. Una soluzione consiste nel consentire il supporto del protocollo sotto forma di più **token di sincronizzazione**. I token di sincronizzazione sono facoltativi.

## <a name="initial-request"></a>Richiesta iniziale

Per garantire la coerenza in tempo reale tra istanze e richieste client diverse, usare intestazioni di `Sync-Token` richiesta/risposta facoltative.

Sintassi:

```http
Sync-Token: <id>=<value>;sn=<sn>
```

|Parametro|Description|
|--|--|
| `<id>` | ID token (opaco) |
| `<value>` | Valore del token (opaco). Consente la stringa con codifica Base64 |
| `<sn>` | Numero di sequenza del token (versione). Superiore indica una versione più recente dello stesso token. Consente una migliore concorrenza e la memorizzazione nella cache del client. Il client può scegliere di usare solo l'ultima versione del token, perché le versioni dei token sono inclusivi. Non necessario per le richieste. |

## <a name="response"></a>Risposta

Il servizio fornisce un' `Sync-Token` intestazione con ogni risposta.

```http
Sync-Token: jtqGc1I4=MDoyOA==;sn=28
```

## <a name="subsequent-requests"></a>Richieste successive

Qualsiasi richiesta successiva garantisce una risposta coerente in **tempo reale** rispetto al fornito `Sync-Token` .

```http
Sync-Token: <id>=<value>
```

Se l' `Sync-Token` intestazione viene omessa dalla richiesta, è possibile che il servizio risponda con i dati memorizzati nella cache durante un breve periodo di tempo (fino a pochi secondi), prima che venga risolto internamente. Questo comportamento può causare letture incoerenti se si sono verificate modifiche immediatamente prima della lettura.

## <a name="multiple-sync-tokens"></a>Più token di sincronizzazione

Il server può rispondere con più token di sincronizzazione per una singola richiesta. Per garantire la coerenza in **tempo reale** per la richiesta successiva, il client deve rispondere con tutti i token di sincronizzazione ricevuti. Per RFC, più valori di intestazione devono essere separati da virgole.

```http
Sync-Token: <token1-id>=<value>,<token2-id>=<value>
```
