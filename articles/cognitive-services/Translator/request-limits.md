---
title: Limiti delle richieste - API Traduzione testuale
titleSuffix: Azure Cognitive Services
description: Questo articolo elenca i limiti delle richieste per l'API Traduzione testuale. Sono previsti addebiti in base al numero di caratteri, non alla frequenza delle richieste, con un limite di 5000 caratteri per ogni richiesta. I limiti di caratteri sono basati sulla sottoscrizione, con il livello F0 limitato a due milioni di caratteri all'ora.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: 97b0b6256b7aaf7b42565fe9453fb87a0c414569
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60605225"
---
# <a name="request-limits-for-translator-text"></a>Limiti delle richieste per Traduzione testuale

Questo articolo elenca i limiti delle richieste per l'API Traduzione testuale. I servizi includono la traduzione, la traslitterazione, il rilevamento della lunghezza delle frasi, il rilevamento della lingua e le traduzioni alternative.

## <a name="character-limits-per-request"></a>Limiti di caratteri per richiesta

Ogni richiesta non può superare i 5000 caratteri. Sono previsti addebiti in base ai caratteri e non al numero di richieste. È consigliabile inviare richieste più brevi e lasciare in attesa alcune richieste in un dato momento.

Non è previsto alcun limite per il numero di richieste in attesa all'API Traduzione testuale.

## <a name="character-limits-per-hour"></a>Limiti di caratteri all'ora

Il limite di caratteri all'ora si basa sul livello della sottoscrizione di Traduzione testuale. Se si raggiungono o superano questi limiti, probabilmente verrà restituita una risposta per segnalare che la quota è stata superata:

| Livello | Limite di caratteri |
|------|-----------------|
| F0 | 2 milioni di caratteri all'ora |
| S1 | 40 milioni di caratteri all'ora |
| S2 | 40 milioni di caratteri all'ora |
| S3 | 120 milioni di caratteri all'ora |
| S4 | 200 milioni di caratteri all'ora |

Questi limiti riguardano esclusivamente i sistemi generici di Microsoft. I sistemi di traduzione personalizzati che usano Microsoft Translator Hub hanno un limite di 1800 caratteri al secondo.

## <a name="latency"></a>Latenza

L'API traduzione testuale ha una latenza massima di 15 secondi usando i modelli standard. Conversione mediante modelli personalizzati con una latenza massima di 25 secondi. Entro questo tempo si riceverà un risultato o una risposta di timeout. Le risposte vengono in genere restituite in un tempo compreso tra 150 e 300 millisecondi. Tempi di risposta variano in base alla dimensione della coppia di richiesta e della lingua. Se non si riceve una traduzione o una [risposta di errore](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) entro tale intervallo di tempo, è consigliabile verificare la connessione di rete e riprovare.

## <a name="sentence-length-limits"></a>Limiti di lunghezza delle frasi

Quando si usa la funzione [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence), lunghezza delle frasi è limitata a 275 caratteri. Sono previste eccezioni per queste lingue:

| Lingua | Codice | Limite di caratteri |
|----------|------|-----------------|
| Cinese | zh | 132 |
| Tedesco | de | 290 |
| Italiano | it | 280 |
| Giapponese | ja | 150 |
| Portoghese | pt | 290 |
| Spagnolo | es | 280 |
| Italiano | it | 280 |
| Thai | ° | 258 |

> [!NOTE]
> Questo limite non si applica alle traduzioni.

## <a name="next-steps"></a>Passaggi successivi

* [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Disponibilità internazionale](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [Informazioni di riferimento sull'API Traduzione testuale v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
