---
title: Limiti delle richieste - API Traduzione testuale
titleSuffix: Azure Cognitive Services
description: Questo articolo elenca i limiti delle richieste per l'API Traduzione testuale. Sono previsti addebiti in base al numero di caratteri, non alla frequenza delle richieste, con un limite di 5000 caratteri per ogni richiesta. I limiti di caratteri sono basati sulla sottoscrizione, con il livello F0 limitato a due milioni di caratteri all'ora.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 79cf87cef4e58cf4e5a2039f30289d55038c8b92
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671957"
---
# <a name="request-limits-for-translator-text"></a>Limiti delle richieste per Traduzione testuale

Questo articolo elenca i limiti delle richieste per l'API Traduzione testuale. I servizi includono la traduzione, la traslitterazione, il rilevamento della lunghezza delle frasi, il rilevamento della lingua e le traduzioni alternative.

## <a name="character-and-array-limits-per-request"></a>Limiti di caratteri e matrici per ogni richiesta

Ogni richiesta di conversione è limitata a 5.000 caratteri. Sono previsti addebiti in base ai caratteri e non al numero di richieste. È consigliabile inviare richieste più brevi.

Nella tabella seguente sono elencati i limiti degli elementi e dei caratteri della matrice per ogni operazione del API Traduzione testuale.

| Operazione | Dimensione massima dell'elemento della matrice |   Numero massimo di elementi di matrice |  Dimensioni massime richiesta (caratteri) |
|:----|:----|:----|:----|
| Trasla | 5\.000 | 100   | 5\.000 |
| Transliterate | 5\.000 | 10    | 5\.000 |
| Rileva | 10,000 | 100 |   50,000 |
| BreakSentence | 10,000    | 100 | 50,000 |
| Ricerca nel dizionario| 100 |  10  | 1\.000 |
| Esempi di dizionari | 100 per il testo e 100 per la traduzione (200 totali)| 10|   2\.000 |

## <a name="character-limits-per-hour"></a>Limiti di caratteri all'ora

Il limite di caratteri all'ora si basa sul livello della sottoscrizione di Traduzione testuale. 

La quota oraria deve essere utilizzata in modo uniforme durante l'ora. Ad esempio, al limite del livello F0 di 2 milioni caratteri all'ora, i caratteri devono essere utilizzati non più velocemente di circa 33.300 caratteri al minuto finestra temporale scorrevole (2 milioni caratteri divisi per 60 minuti).

Se si raggiungono o superano questi limiti o si inviano dimensioni eccessive di una parte della quota in un breve periodo di tempo, è probabile che si riceva una risposta fuori quota. Non sono previsti limiti per le richieste simultanee.

| Livello | Limite di caratteri |
|------|-----------------|
| F0 | 2 milioni di caratteri all'ora |
| S1 | 40 milioni di caratteri all'ora |
| S2/C2 | 40 milioni di caratteri all'ora |
| S3/C3 | 120 milioni di caratteri all'ora |
| S4/C4 | 200 milioni di caratteri all'ora |

I limiti per le [sottoscrizioni multiservizio](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) corrispondono al livello S1.

Questi limiti sono limitati ai modelli di traduzione standard di Microsoft. I modelli di traduzione personalizzati che usano il convertitore personalizzato sono limitati a 1.800 caratteri al secondo.

## <a name="latency"></a>Latency

Il API Traduzione testuale ha una latenza massima di 15 secondi utilizzando i modelli standard. La conversione con modelli personalizzati ha una latenza massima di 25 secondi. Entro questo tempo si riceverà un risultato o una risposta di timeout. Le risposte vengono in genere restituite in un tempo compreso tra 150 e 300 millisecondi. I tempi di risposta variano in base alle dimensioni della coppia di richieste e lingue. Se non si riceve una traduzione o una [risposta di errore](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) entro questo intervallo di tempo, è consigliabile verificare la connessione di rete e riprovare.

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
