---
title: Limiti delle richieste-Translator
titleSuffix: Azure Cognitive Services
description: Questo articolo elenca i limiti delle richieste per il traduttore. Sono previsti addebiti in base al numero di caratteri, non alla frequenza delle richieste, con un limite di 5000 caratteri per ogni richiesta. I limiti di caratteri sono basati sulla sottoscrizione, con il livello F0 limitato a due milioni di caratteri all'ora.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: swmachan
ms.openlocfilehash: dff69f00833cc3f8004befd330d144f4da0e332d
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566586"
---
# <a name="request-limits-for-translator"></a>Limiti delle richieste per il traduttore

Questo articolo fornisce limiti di limitazione per il traduttore. I servizi includono la traduzione, la traslitterazione, il rilevamento della lunghezza delle frasi, il rilevamento della lingua e le traduzioni alternative.

## <a name="character-and-array-limits-per-request"></a>Limiti di caratteri e matrici per ogni richiesta

Ogni richiesta di conversione è limitata a 10.000 caratteri, in tutte le lingue di destinazione in cui si esegue la conversione. Se, ad esempio, si invia una richiesta di conversione di 3.000 caratteri per tradurre in 3 lingue diverse, le dimensioni della richiesta sono pari a 3000x3 = 9.000 caratteri, che soddisfano il limite di richieste. Sono previsti addebiti in base ai caratteri e non al numero di richieste. È consigliabile inviare richieste più brevi.

Nella tabella seguente sono elencati i limiti degli elementi e dei caratteri della matrice per ogni operazione del traduttore.

| Operazione | Dimensione massima dell'elemento della matrice |    Numero massimo di elementi di matrice |    Dimensioni massime richiesta (caratteri) |
|:----|:----|:----|:----|
| Traduci | 10,000    | 100   | 10,000 |
| Transliterate | 5\.000 | 10    | 5\.000 |
| Detect | 50.000 | 100 |   50.000 |
| BreakSentence | 50.000    | 100 | 50.000 |
| Ricerca nel dizionario| 100 |  10  | 1\.000 |
| Esempi di dizionari | 100 per il testo e 100 per la traduzione (200 totali)| 10|   2\.000 |

## <a name="character-limits-per-hour"></a>Limiti di caratteri all'ora

Il limite di caratteri per ora è basato sul livello di sottoscrizione di Translator. 

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

## <a name="latency"></a>Latenza

Il convertitore ha una latenza massima di 15 secondi usando modelli standard e 120 secondi quando si usano modelli personalizzati. In genere, le risposte *per il testo entro 100 caratteri* vengono restituite in 150 millisecondi a 300 millisecondi. I modelli di conversione personalizzati hanno caratteristiche di latenza simili per la frequenza delle richieste sostenute e possono avere una latenza più elevata quando la frequenza delle richieste è intermittente. I tempi di risposta variano in base alle dimensioni della coppia di richieste e lingue. Se non si riceve una traduzione o una [risposta di errore](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) entro questo intervallo di tempo, verificare il codice, la connessione di rete e riprovare. 

## <a name="sentence-length-limits"></a>Limiti di lunghezza delle frasi

Quando si usa la funzione [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence), lunghezza delle frasi è limitata a 275 caratteri. Sono previste eccezioni per queste lingue:

| Linguaggio | Codice | Limite di caratteri |
|----------|------|-----------------|
| Cinese | zh | 166 |
| Tedesco | de | 800 |
| Italiano | it | 800 |
| Giapponese | ja | 166 |
| Portoghese | pt | 800 |
| Spagnolo | es | 800 |
| Thai | th | 180 |

> [!NOTE]
> Questo limite non si applica alle traduzioni.

## <a name="next-steps"></a>Passaggi successivi

* [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Disponibilità a livello di area](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [Informazioni di riferimento su Traduttore v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
