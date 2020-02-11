---
title: Supporto per la lingua - API Traduzione testuale
titleSuffix: Azure Cognitive Services
description: Il API Traduzione testuale supporta le seguenti lingue per la traduzione da testo a testo tramite Neural Machine Translation (NMT).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: ec5c1295f883bb37ad5f016bc3346c134863e6bd
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118822"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Supporto lingua e area geografica per l'API Traduzione testuale

L'API Traduzione testuale supporta le lingue seguenti per la traduzione da testo a testo. La traduzione automatica neurale è il nuovo standard per traduzioni automatiche di alta qualità supportate dall'intelligenza artificiale ed è disponibile per impostazione predefinita quando si usa la versione V3 dell'API Traduzione testuale, se è disponibile un sistema neurale.

[Altre informazioni sull'uso della traduzione automatica](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Conversione

**API Traduzione V2**

> [!NOTE]
> V2 è stato deprecato il 30 aprile 2018. Eseguire la migrazione delle applicazioni a V3 per sfruttare i vantaggi delle nuove funzionalità disponibili esclusivamente in V3.

* Solo statistica: non è disponibile alcun sistema neurale per questa lingua.
* Neural available: è disponibile un sistema neurale. Usare il parametro `category=generalnn` per accedere al sistema neurale.
* Impostazione predefinita neurale: neurale è il sistema di conversione predefinito. Usare il parametro `category=smt` per accedere al sistema statistico per l'uso con l'Hub di Microsoft Translator.
* Solo neurale: è disponibile solo la traduzione neurale.

**API Traduzione V3** l'API Traduzione V3 è neurale per impostazione predefinita e i sistemi statistici sono disponibili solo quando non è presente alcun sistema neurale.

> [!NOTE]
> Attualmente, un sottoinsieme di linguaggi neurali è disponibile nel convertitore personalizzato e l'aggiunta di altri. [Visualizza le lingue attualmente disponibili nel convertitore personalizzato](#customization).

|Lingua:|  Codice lingua|  API V3|
|:-----|:-----:|:-----|
|Afrikaans| `af`|   Neurale|
|Arabo|    `ar`    |   Neurale|
|Bangla|    `bn`    |   Neurale|
|Bosniaco (latino)|   `bs`    |   Neurale|
|Bulgaro| `bg`    |   Neurale|
|Cantonese (tradizionale)|   `yue`|  Statistiche|
|Catalano|   `ca`    |   Statistiche|
|Cinese semplificato|    `zh-Hans`|Neurale|
|Cinese tradizionale|   `zh-Hant`       |Neurale|
|Croato|  `hr`    |Neurale|
|Ceco| `cs`    |   Neurale|
|Danese|    `da`        |Neurale|
|Olandese| `nl`|   Neurale|
|Inglese|   `en`    |   Neurale|
|Estone|  `et`    |   Neurale|
|Figiano|    `fj`    |   Statistiche|
|Filippino|  `fil`   |   Statistiche|
|Finlandese|   `fi`    |   Neurale|
|Francese|    `fr`    |   Neurale|
|Tedesco|    `de`    |   Neurale|
|Greco| `el`    |   Neurale|
|Creolo haitiano|    `ht`        |Statistiche|
|Ebraico |`he`   |Neurale
|Hindi| `hi`    |   Neurale|
|Hmong Daw| `mww`   |   Statistiche|
|Ungherese| `hu`    |   Neurale|
|Islandese| `is`    |   Neurale|
|Indonesiano|    `id`    |   Statistiche|
|Irlandese | `ga`| Neurale
|Italiano|   `it`    |   Neurale|
|Giapponese|  `ja`    |   Neurale|
|Kannada|`kn`| Neurale
|Kiswahili| `sw`    |   Statistiche|
|Klingon|   `tlh`   |   Statistiche|
|Klingon (plqaD)|   `tlh-Qaak`  |   Statistiche|
|Coreano |`ko`   |   Neurale|
|Lettone|   `lv`    |   Neurale|
|Lituano|    `lt`    |   Neurale|
|Malgascio|  `mg`    |   Statistiche|
|Malese| `ms`        |Statistiche|
|Malayalam| `ml` | Neurale
|Maltese|   `mt`    |   Statistiche|
|Maori| `mi`  | Neurale|
|Norvegese| `nb`    |   Neurale|
|Persiano|   `fa`    |   Neurale|
|Polacco|    `pl`    |   Neurale|
|Portoghese (Brasile)|   `pt-br` |   Neurale|
|Portoghese (Portogallo)| `pt-pt` | Neurale
|Punjabi|`pa`|Neurale
|Querétaro Otomi|   `otq`   |   Statistiche|
|Rumeno|  `ro`    |   Neurale|
|Russo|   `ru`    |   Neurale|
|Samoano|    `sm`    |   Statistiche|
|Serbo (alfabeto cirillico)|    `sr-Cyrl`|  Statistiche|
|Serbo (alfabeto latino)|   `sr-Latn`       |Statistiche|
|Slovacco|    `sk`    |   Neurale|
|Sloveno| `sl`    |   Neurale|
|Spagnolo|   `es`    |   Neurale|
|Svedese|   `sv`    |Neurale|
|Tahitiano|  `ty`    |Statistiche|
|Tamil| `ta`    |   Neurale|
|Telugu|    `te`    |   Neurale|
|Thai|  `th`    |   Neurale|
|Tongano|    `to`    |   Statistiche|
|Turco|   `tr`        |Neurale|
|Ucraino| `uk`    |   Neurale|
|Urdu|  `ur`    |   Statistiche|
|Vietnamita|    `vi`    |   Neurale|
|Gallese| `cy`    |   Neurale|
|Yucatec Maya|  `yua`   |   Statistiche|

> [!NOTE]
> Il codice lingua `pt` per impostazione predefinita `pt-br`, portoghese (Brasile).

## <a name="transliteration"></a>Traslitterazione

Il metodo Transliterate supporta le lingue seguenti. Nella colonna "Verso/Da" il simbolo "<-->" indica che la lingua può essere traslitterata da o verso entrambi gli alfabeti elencati. Il simbolo "-->" indica che la lingua può essere traslitterata solo da un alfabeto all'altro.

| Lingua:    | Codice lingua | Script | Verso/Da | Script|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabo | `ar` | Arabo `Arab` | <--> | Latino `Latn` |
|Bangla  | `bn` | Bengalese `Beng` | <--> | Latino `Latn` |
| Cinese (semplificato) | `zh-Hans` | Cinese semplificato `Hans`| <--> | Latino `Latn` |
| Cinese (semplificato) | `zh-Hans` | Cinese semplificato `Hans`| <--> | Cinese tradizionale `Hant`|
| Cinese (tradizionale) | `zh-Hant` | Cinese tradizionale `Hant`| <--> | Latino `Latn` |
| Cinese (tradizionale) | `zh-Hant` | Cinese tradizionale `Hant`| <--> | Cinese semplificato `Hans` |
| Gujarati | `gu`  | Gujarati `Gujr` | --> | Latino `Latn` |
| Ebraico | `he` | Ebraico `Hebr` | <--> | Latino `Latn` |
| Hindi | `hi` | Devanagari `Deva` | <--> | Latino `Latn` |
| Giapponese | `ja` | Giapponese `Jpan` | <--> | Latino `Latn` |
| Kannada | `kn` | Kannada `Knda` | --> | Latino `Latn` |
| Malayalam | `ml` | Malayalam `Mlym` | --> | Latino `Latn` |
| Marathi | `mr` | Devanagari `Deva` | --> | Latino `Latn` |
| Oriya | `or` | Oriya `Orya` | <--> | Latino `Latn` |
| Punjabi | `pa` | Gurmukhi `Guru`  | <--> | Latino `Latn`  |
| Serbo (alfabeto cirillico) | `sr-Cyrl` | Cirillico `Cyrl`  | --> | Latino `Latn` |
| Serbo (alfabeto latino) | `sr-Latn` | Latino `Latn` | --> | Cirillico `Cyrl`|
| Tamil | `ta` | Tamil `Taml` | --> | Latino `Latn` |
| Telugu | `te` | Telugu `Telu` | --> | Latino `Latn` |
| Thai | `th` | Thai `Thai` | <--> | Latino `Latn` |

## <a name="dictionary"></a>Dizionario

Il dizionario supporta le lingue seguenti verso o dalla lingua inglese tramite i metodi Lookup ed Examples.

| Lingua:    | Codice lingua |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabo       | `ar`          |
| Bangla      | `bn`          |
| Bosniaco (latino)      | `bs`          |
| Bulgaro      | `bg`          |
| Catalano      | `ca`          |
| Cinese semplificato      | `zh-Hans`          |
| Croato      | `hr`          |
| Ceco      | `cs`          |
| Danese      | `da`          |
| Olandese      | `nl`          |
| Estone      | `et`          |
| Finlandese      | `fi`          |
| Francese      | `fr`          |
| Tedesco      | `de`          |
| Greco      | `el`          |
| Creolo haitiano      | `ht`          |
| Ebraico      | `he`          |
| Hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| Ungherese      | `hu`          |
| Islandese    | `is`  |
| Indonesiano      | `id`          |
| Italiano      | `it`          |
| Giapponese      | `ja`          |
| Kiswahili      | `sw`          |
| Klingon      | `tlh`          |
| Coreano      | `ko`          |
| Lettone      | `lv`          |
| Lituano      | `lt`          |
| Malese      | `ms`          |
| Maltese      | `mt`          |
| Norvegese      | `nb`          |
| Persiano      | `fa`          |
| Polacco      | `pl`          |
| Portoghese (Brasile)     | `pt-br`          |
| Rumeno      | `ro`          |
| Russo      | `ru`          |
| Serbo (alfabeto latino)      | `sr-Latn`          |
| Slovacco     | `sk`          |
| Sloveno      | `sl`          |
| Spagnolo      | `es`          |
| Svedese      | `sv`          |
| Tamil      | `ta`          |
| Thai      | `th`          |
| Turco      | `tr`          |
| Ucraino      | `uk`          |
| Urdu      | `ur`          |
| Vietnamita      | `vi`          |
| Gallese      | `cy`          |

## <a name="detect"></a>Rileva

API Traduzione testuale rileva tutte le lingue disponibili per la conversione e la traslitterazione.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Accedere all'elenco di lingue dell'API Traduzione testuale a livello di codice

È possibile recuperare l'elenco delle lingue supportate per l'API Traduzione testuale v3.0 usando il metodo Languages. È possibile visualizzare l'elenco in base alla funzionalità, al codice della lingua, nonché al nome della lingua in inglese o in qualsiasi altra lingua supportata. L'elenco viene aggiornato automaticamente dal servizio Microsoft Translator quando diventano disponibili nuove lingue.

[Visualizzare la documentazione di riferimento dell'operazione Languages](reference/v3-0-languages.md)

## <a name="customization"></a>Personalizzazione

Le lingue seguenti sono disponibili per la personalizzazione da o verso l'inglese utilizzando il [convertitore personalizzato](https://aka.ms/CustomTranslator).

| Lingua:    | Codice lingua |
|:----------- |:-------------:|
| Arabo       | `ar`          |
| Bangla      | `bn`          |
| Bosniaco (latino)      | `bs`          |
| Bulgaro      | `bg`          |
| Cinese semplificato      | `zh-Hans`          |
|Cinese tradizionale|   `zh-Hant`   |
| Croato      | `hr`          |
| Ceco      | `cs`          |
| Danese      | `da`          |
| Olandese      | `nl`          |
| Inglese    | `en`     |
| Estone      | `et`          |
| Finlandese      | `fi`          |
| Francese      | `fr`          |
| Tedesco      | `de`          |
| Greco      | `el`          |
| Ebraico      | `he`          |
| Hindi      | `hi`          |
| Ungherese      | `hu`          |
| Islandese | `is` |
| Indonesiano|   `id`    |
| Irlandese | `ga`  |
| Italiano      | `it`          |
| Giapponese      | `ja`          |
| Kiswahili|    `sw`    |
| Coreano      | `ko`          |
| Lettone      | `lv`          |
| Lituano      | `lt`          |
| Malgascio| `mg`    |
| Maori| `mi`  |
| Norvegese      | `nb`          |
| Persiano      | `fa`          |
| Polacco      | `pl`          |
| Portoghese (Brasile) | `pt-br` |
| Rumeno      | `ro`          |
| Russo      | `ru`          |
| Samoano|   `sm`    |
| Serbo (alfabeto latino)      | `sr-Latn`          |
| Slovacco     | `sk`          |
| Sloveno      | `sl`          |
| Spagnolo      | `es`          |
| Svedese      | `sv`          |
| Thai      | `th`          |
| Turco      | `tr`          |
| Ucraino      | `uk`          |
| Vietnamita      | `vi`          |
| Gallese | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Accedere all'elenco nel sito Web Microsoft Translator

Per una rapida panoramica delle lingue, il sito Web Microsoft Translator offre l'elenco di tutte le lingue supportate dalle API Traduzione testuale e Traduzione vocale. L'elenco non include informazioni specifiche per sviluppatori, ad esempio i codici delle lingue.

[Visualizzare l'elenco delle lingue](https://www.microsoft.com/translator/languages.aspx)
