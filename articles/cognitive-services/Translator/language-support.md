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
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 5e96ced1bd87a0ed8be19bf4e07f7b0d9b319aad
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837338"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Supporto lingua e area geografica per l'API Traduzione testuale

L'API Traduzione testuale supporta le lingue seguenti per la traduzione da testo a testo. La traduzione automatica neurale è il nuovo standard per traduzioni automatiche di alta qualità supportate dall'intelligenza artificiale ed è disponibile per impostazione predefinita quando si usa la versione V3 dell'API Traduzione testuale, se è disponibile un sistema neurale.

[Altre informazioni sull'uso della traduzione automatica](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Traduzione

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

|Lingua|  Codice lingua|  API V2| API V3|
|:-----|:-----:|:-----|:-----|
|Afrikaans| `af`    |Solo sistema statistico|  Neurale|
|Arabo|    `ar`    |Sistema neurale disponibile|  Neurale|
|Bengalese|    `bn`    |Sistema neurale disponibile|  Neurale|
|Bosniaco (alfabeto latino)|   `bs`    |Sistema neurale disponibile|  Neurale|
|Bulgaro| `bg`    |Sistema neurale disponibile|  Neurale|
|Cantonese (tradizionale)|   `yue`   |Solo sistema statistico|  Statistica|
|Catalano|   `ca`    |Solo sistema statistico|  Statistica|
|Cinese semplificato|    `zh-Hans`   |Sistema neurale come predefinito |Neurale|
|Cinese tradizionale|   `zh-Hant`   |Sistema neurale come predefinito |Neurale|
|Croato|  `hr`    |Sistema neurale disponibile|  Neurale|
|Ceco| `cs`    |Sistema neurale disponibile|  Neurale|
|Danese|    `da`    |Sistema neurale disponibile   |Neurale|
|Olandese| `nl`    |Sistema neurale disponibile|  Neurale|
|Inglese|   `en`    |Sistema neurale disponibile|  Neurale|
|Estone|  `et`    |Sistema neurale disponibile|  Neurale|
|Figiano|    `fj`    |Solo sistema statistico|  Statistica|
|Filippino|  `fil`   |Solo sistema statistico|  Statistica|
|Finlandese|   `fi`    |Sistema neurale disponibile|  Neurale|
|Francese|    `fr`    |Sistema neurale disponibile|  Neurale|
|Tedesco|    `de`    |Sistema neurale disponibile|  Neurale|
|Greco| `el`    |Sistema neurale disponibile|  Neurale|
|Creolo haitiano|    `ht`    |Solo sistema statistico   |Statistica|
|Ebraico |`he`   |Sistema neurale disponibile   |Neurale|
|Hindi| `hi`    |Sistema neurale come predefinito|    Neurale|
|Hmong Daw| `mww`   |Solo sistema statistico|  Statistica|
|Ungherese| `hu`    |Sistema neurale disponibile|  Neurale|
|Islandese| `is`    |Solo sistema neurale|   Neurale|
|Indonesiano|    `id`    |Solo sistema statistico|  Statistica|
|Italiano|   `it`    |Sistema neurale disponibile|  Neurale|
|Giapponese|  `ja`    |Sistema neurale disponibile|  Neurale|
|Kiswahili| `sw`    |Solo sistema statistico|  Statistica|
|Klingon|   `tlh`   |Solo sistema statistico|  Statistica|
|Klingon (plqaD)|   `tlh-Qaak`  |Solo sistema statistico|  Statistica|
|Coreano |`ko`   |Sistema neurale disponibile|  Neurale|
|Lettone|   `lv`    |Sistema neurale disponibile|  Neurale|
|Lituano|    `lt`    |Sistema neurale disponibile|  Neurale|
|Malgascio|  `mg`    |Solo sistema statistico|  Statistica|
|Malese| `ms`    |Solo sistema statistico   |Statistica|
|Maltese|   `mt`    |Solo sistema statistico|  Statistica|
|Norvegese| `nb`    |Sistema neurale disponibile|  Neurale|
|Persiano|   `fa`    |Sistema neurale disponibile|  Neurale|
|Polacco|    `pl`    |Sistema neurale disponibile|  Neurale|
|Portoghese|    `pt`    |Sistema neurale disponibile|  Neurale|
|Querétaro Otomi|   `otq`   |Solo sistema statistico|  Statistica|
|Rumeno|  `ro`    |Sistema neurale disponibile|  Neurale|
|Russo|   `ru`    |Sistema neurale disponibile|  Neurale|
|Samoano|    `sm`    |Solo sistema statistico|  Statistica|
|Serbo (alfabeto cirillico)|    `sr-Cyrl`   |Solo sistema statistico|  Statistica|
|Serbo (alfabeto latino)|   `sr-Latn`   |Solo sistema statistico   |Statistica|
|Slovacco|    `sk`    |Sistema neurale disponibile|  Neurale|
|Sloveno| `sl`    |Sistema neurale disponibile|  Neurale|
|Spagnolo|   `es`    |Sistema neurale disponibile|  Neurale|
|Svedese|   `sv`    |Sistema neurale disponibile   |Neurale|
|Taitiano|  `ty`    |Solo sistema statistico|  Statistica|
|Tamil| `ta`    |Solo sistema statistico|  Statistica|
|Telugu|    `te`    |Solo sistema neurale|   Neurale|
|Thai|  `th`    |Sistema neurale disponibile|  Neurale|
|Tongano|    `to`    |Solo sistema statistico|  Statistica|
|Turco|   `tr`    |Sistema neurale disponibile   |Neurale|
|Ucraino| `uk`    |Sistema neurale disponibile|  Neurale|
|Urdu|  `ur`    |Solo sistema statistico|  Statistica|
|Vietnamita|    `vi`    |Sistema neurale disponibile|  Neurale|
|Gallese| `cy`    |Sistema neurale disponibile|  Neurale|
|Yucatec Maya|  `yua`   |Solo sistema statistico|  Statistica|

## <a name="transliteration"></a>Traslitterazione

Il metodo Transliterate supporta le lingue seguenti. Nella colonna "Verso/Da" il simbolo "<-->" indica che la lingua può essere traslitterata da o verso entrambi gli alfabeti elencati. Il simbolo "-->" indica che la lingua può essere traslitterata solo da un alfabeto all'altro.

| Lingua    | Codice lingua | Script | Verso/Da | Script|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabo | `ar` | Arabo `Arab` | <--> | Latino `Latn` |
|Bengalese  | `bn` | Bengalese `Beng` | <--> | Latino `Latn` |
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

| Lingua    | Codice lingua |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabo       | `ar`          |
| Bengalese      | `bn`          |
| Bosniaco (alfabeto latino)      | `bs`          |
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
| Portoghese      | `pt`          |
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

## <a name="detect"></a>Detect

API Traduzione testuale rileva tutte le lingue disponibili per la conversione e la traslitterazione.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Accedere all'elenco di lingue dell'API Traduzione testuale a livello di codice

È possibile recuperare l'elenco delle lingue supportate per l'API Traduzione testuale v3.0 usando il metodo Languages. È possibile visualizzare l'elenco in base alla funzionalità, al codice della lingua, nonché al nome della lingua in inglese o in qualsiasi altra lingua supportata. L'elenco viene aggiornato automaticamente dal servizio Microsoft Translator quando diventano disponibili nuove lingue.

[Visualizzare la documentazione di riferimento dell'operazione Languages](reference/v3-0-languages.md)

## <a name="customization"></a>Personalizzazione

Le lingue seguenti sono disponibili per la personalizzazione da o verso l'inglese utilizzando il [convertitore personalizzato](https://aka.ms/CustomTranslator).

| Lingua    | Codice lingua |
|:----------- |:-------------:|
| Arabo       | `ar`          |
| Bengalese      | `bn`          |
| Bosniaco (alfabeto latino)      | `bs`          |
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
|Kiswahili| `sw`    |
| Coreano      | `ko`          |
| Lettone      | `lv`          |
| Lituano      | `lt`          |
|Malgascio|  `mg`    |
| Norvegese      | `nb`          |
| Persiano      | `fa`          |
| Polacco      | `pl`          |
| Portoghese      | `pt`          |
| Rumeno      | `ro`          |
| Russo      | `ru`          |
|Samoano|    `sm`    |
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
