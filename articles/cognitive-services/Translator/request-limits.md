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
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: c19e39918aa64730a35a27fcdadd70800f47f4fa
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514648"
---
# <a name="request-limits-for-translator-text"></a>Limiti delle richieste per Traduzione testuale

Questo articolo elenca i limiti delle richieste per l'API Traduzione testuale. I servizi includono la traduzione, la traslitterazione, il rilevamento della lunghezza delle frasi, il rilevamento della lingua e le traduzioni alternative.

## <a name="character-and-array-limits-per-request"></a>Limiti di carattere e la matrice per ogni richiesta

Ogni richiesta di traslazione è limitato a 5.000 caratteri. Sono previsti addebiti in base ai caratteri e non al numero di richieste. È consigliabile inviare le richieste più breve.

La seguente tabella elenchi matrice elemento e il carattere i limiti per ogni operazione dell'API traduzione testuale.

| Operazione | Dimensione massima dell'elemento di matrice |   Numero massimo di elementi della matrice |  Dimensioni massime di richiesta (caratteri) |
|:----|:----|:----|:----|
| Translate | 5.000 | 100   | 5.000 |
| Transliterate | 5.000 | 10    | 5.000 |
| Detect | 10,000 | 100 |   50,000 |
| BreakSentence | 10,000    | 100 | 5,0000 |
| Ricerca nel dizionario| 100 |  10  | 1.000 |
| Esempi di dizionari | 100 per il testo e 100 per la conversione (200 totale)| 10|   2.000 |

## <a name="character-limits-per-hour"></a>Limiti di caratteri all'ora

Il limite di caratteri all'ora si basa sul livello della sottoscrizione di Traduzione testuale. La quota oraria deve essere utilizzata in modo uniforme in tutta l'ora. Se si raggiungono o superano questi limiti o troppo grande di una parte della quota di trasmissione in un breve periodo di tempo, probabilmente si riceverà un timeout della risposta di quota.

| Livello | Limite di caratteri |
|------|-----------------|
| F0 | 2 milioni di caratteri all'ora |
| S1 | 40 milioni di caratteri all'ora |
| S2 / C2 | 40 milioni di caratteri all'ora |
| S3 / C3 | 120 milioni di caratteri all'ora |
| S4 / C4 | 200 milioni di caratteri all'ora |

Limiti della [multiservizio sottoscrizioni](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) corrispondono a livello S1.

Questi limiti sono limitati ai modelli di conversione standard di Microsoft. I modelli di conversione personalizzata che utilizzano Microsoft Translator personalizzati sono limitati ai caratteri 1.800 al secondo.

## <a name="latency"></a>Latenza

L'API traduzione testuale ha una latenza massima di 15 secondi usando i modelli standard. Conversione mediante modelli personalizzati con una latenza massima di 25 secondi. Entro questo tempo si riceverà un risultato o una risposta di timeout. Le risposte vengono in genere restituite in un tempo compreso tra 150 e 300 millisecondi. Tempi di risposta variano in base alla dimensione della coppia di richiesta e della lingua. Se non si riceve una traduzione o una [risposta di errore](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) entro tale intervallo di tempo, è consigliabile verificare la connessione di rete e riprovare.

## <a name="sentence-length-limits"></a>Limiti di lunghezza delle frasi

Quando si usa la funzione [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence), lunghezza delle frasi è limitata a 275 caratteri. Sono previste eccezioni per queste lingue:

| Linguaggio | Codice | Limite di caratteri |
|----------|------|-----------------|
| Cinese | zh | 132 |
| Tedesco | de | 290 |
| Italiano | it | 280 |
| Giapponese | ja | 150 |
| Portoghese | pt | 290 |
| Spagnolo | es | 280 |
| Italiano | it | 280 |
| Thai | th | 258 |

> [!NOTE]
> Questo limite non si applica alle traduzioni.

## <a name="next-steps"></a>Passaggi successivi

* [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Disponibilità internazionale](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [Informazioni di riferimento sull'API Traduzione testuale v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
