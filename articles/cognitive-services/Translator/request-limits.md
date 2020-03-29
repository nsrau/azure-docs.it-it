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
ms.date: 03/17/2020
ms.author: swmachan
ms.openlocfilehash: 8d26efec2783d6f121c319e46b1b505b6e1b1e09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498942"
---
# <a name="request-limits-for-translator-text"></a>Limiti delle richieste per Traduzione testuale

Questo articolo elenca i limiti delle richieste per l'API Traduzione testuale. I servizi includono la traduzione, la traslitterazione, il rilevamento della lunghezza delle frasi, il rilevamento della lingua e le traduzioni alternative.

## <a name="character-and-array-limits-per-request"></a>Limiti di caratteri e matrici per richiesta

Ogni richiesta di traduzione è limitata a 5.000 caratteri, in tutte le lingue di destinazione in cui stai traducendo. Ad esempio, l'invio di una richiesta di traduzione di 1.500 caratteri per la traduzione in 3 lingue diverse comporta una dimensione della richiesta di 1.500x3 x 4.500 caratteri, che soddisfa il limite di richieste. Sono previsti addebiti in base ai caratteri e non al numero di richieste. Si consiglia di inviare richieste più brevi.

Nella tabella seguente sono elencati i limiti di caratteri e elementi della matrice per ogni operazione dell'API Translator Text.

| Operazione | Dimensione massima dell'elemento della matriceMaximum Size of array Element |   Numero massimo di elementi di matrice |  Dimensione massima richiesta (caratteri) |
|:----|:----|:----|:----|
| Translate | 5.000 | 100   | 5.000 |
| Transliterate | 5.000 | 10    | 5.000 |
| Detect | 10,000 | 100 |   50.000 |
| BreakSentence | 10,000    | 100 | 50.000 |
| Ricerca nel dizionario| 100 |  10  | 1.000 |
| Esempi di dizionari | 100 per il testo e 100 per la traduzione (200 in totale)| 10|   2.000 |

## <a name="character-limits-per-hour"></a>Limiti di caratteri all'ora

Il limite di caratteri all'ora si basa sul livello della sottoscrizione di Traduzione testuale. 

La quota oraria dovrebbe essere consumata uniformemente nel corso dell'ora. Ad esempio, al limite di 2 milioni di caratteri all'ora, i caratteri devono essere utilizzati non più velocemente di circa 33.300 caratteri per finestra scorrevole al minuto (2 milioni di caratteri divisi per 60 minuti).

Se raggiungi o superi questi limiti o invii una parte troppo grande della quota in un breve periodo di tempo, probabilmente riceverai una risposta fuori quota. Non esistono limiti alle richieste simultanee.

| Livello | Limite di caratteri |
|------|-----------------|
| F0 | 2 milioni di caratteri all'ora |
| S1 | 40 milioni di caratteri all'ora |
| S2 / C2 | 40 milioni di caratteri all'ora |
| S3 / C3 | 120 milioni di caratteri all'ora |
| S4 / C4 | 200 milioni di caratteri all'ora |

I limiti per [le sottoscrizioni multiservizio](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) sono gli stessi del livello S1.

Questi limiti sono limitati ai modelli di traduzione standard di Microsoft.These limits are restricted to Microsoft's standard translation models. I modelli di traduzione personalizzati che utilizzano Custom Translator sono limitati a 1.800 caratteri al secondo.

## <a name="latency"></a>Latenza

L'API Translator Text ha una latenza massima di 15 secondi usando modelli standard e 120 secondi quando usi modelli personalizzati. In genere, le risposte per il *testo entro 100 caratteri* vengono restituite in 150 millisecondi a 300 millisecondi. I modelli di traduttore personalizzati hanno caratteristiche di latenza simili in base alla frequenza delle richieste sostenuta e possono avere una latenza più elevata quando la frequenza delle richieste è intermittente. I tempi di risposta variano in base alle dimensioni della coppia di richiesta e lingua. Se non ricevi una traduzione o una risposta di [errore](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) entro tale intervallo di tempo, controlla il codice, la connessione di rete e riprova. 

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
* [Disponibilità a livello di area](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [Informazioni di riferimento sull'API Traduzione testuale v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
