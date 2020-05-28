---
title: Supporto per la lingua-Translator
titleSuffix: Azure Cognitive Services
description: Il convertitore di servizi cognitivi supporta le seguenti lingue per la traduzione da testo a testo tramite la traduzione di Neural Machine (NMT).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 359079ad73fcf162fb742afe74c1c1de5896c35c
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996075"
---
# <a name="language-and-region-support-for-translator"></a>Supporto per lingua e area geografica per Translator

Il convertitore supporta le seguenti lingue per la traduzione da testo a testo. La conversione di macchine neurali (NMT) è il nuovo standard per le traduzioni di macchine basate su intelligenza artificiale di alta qualità ed è disponibile come impostazione predefinita con V3 di Translator quando è disponibile un sistema neurale.

[Altre informazioni sull'uso della traduzione automatica](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Traduzione

**Traduttore V2**

> [!NOTE]
> V2 è stato deprecato il 30 aprile 2018. Eseguire la migrazione delle applicazioni a V3 per sfruttare i vantaggi delle nuove funzionalità disponibili esclusivamente in V3.

* Solo statistica: non è disponibile alcun sistema neurale per questa lingua.
* Neural available: è disponibile un sistema neurale. Usare il parametro `category=generalnn` per accedere al sistema neurale.
* Impostazione predefinita neurale: neurale è il sistema di conversione predefinito. Usare il parametro `category=smt` per accedere al sistema statistico per l'uso con l'Hub di Microsoft Translator.
* Solo neurale: è disponibile solo la traduzione neurale.

**Convertitore V3** Il convertitore V3 è neurale per impostazione predefinita e i sistemi statistici sono disponibili solo se non esiste alcun sistema neurale.

> [!NOTE]
> Attualmente, un sottoinsieme di linguaggi neurali è disponibile nel convertitore personalizzato e l'aggiunta di altri. [Visualizza le lingue attualmente disponibili nel convertitore personalizzato](#customization).

|Linguaggio|    Codice lingua|    API V3|
|:-----|:-----:|:-----|
|Afrikaans|    `af`|    Neurale|
|Arabo|    `ar`    |    Neurale|
|Bengalese|    `bn`    |    Neurale|
|Bosniaco (latino)|    `bs`    |    Neurale|
|Bulgaro|    `bg`    |    Neurale|
|Cantonese (tradizionale)|    `yue`|    Statistica|
|Catalano|    `ca`    |    Statistica|
|Cinese semplificato|    `zh-Hans`|Neurale|
|Cinese tradizionale|    `zh-Hant`        |Neurale|
|Croato|    `hr`    |Neurale|
|Ceco|    `cs`    |    Neurale|
|Danese|    `da`        |Neurale|
|Olandese|    `nl`|    Neurale|
|Inglese|    `en`    |    Neurale|
|Estone|    `et`    |    Neurale|
|Figiano|    `fj`    |    Statistica|
|Filippino|    `fil`    |    Statistica|
|Finlandese|    `fi`    |    Neurale|
|Francese|    `fr`    |    Neurale|
|Tedesco|    `de`    |    Neurale|
|Greco|    `el`    |    Neurale|
|Gujarati|    `gu`    |    Neurale|
|Creolo haitiano|    `ht`        |Statistica|
|Ebraico    |`he`    |Neurale
|Hindi|    `hi`    |    Neurale|
|Hmong Daw|    `mww`    |    Statistica|
|Ungherese|    `hu`    |    Neurale|
|Islandese|    `is`    |    Neurale|
|Indonesiano|    `id`    |    Statistica|
|Irlandese | `ga`| Neurale
|Italiano|    `it`    |    Neurale|
|Giapponese|    `ja`    |    Neurale|
|Kannada|`kn`| Neurale
|Kiswahili|    `sw`    |    Statistica|
|Klingon|    `tlh`    |    Statistica|
|Klingon (plqaD)|    `tlh-Qaak`    |    Statistica|
|Coreano    |`ko`    |    Neurale|
|Lettone|    `lv`    |    Neurale|
|Lituano|    `lt`    |    Neurale|
|Malgascio|    `mg`    |    Statistica|
|Malese|    `ms`        |Statistica|
|Malayalam| `ml` | Neurale
|Maltese|    `mt`    |    Statistica|
|Maori| `mi`  | Neurale|
|Marathi| `mr`  | Neurale|
|Norvegese|    `nb`    |    Neurale|
|Persiano|    `fa`    |    Neurale|
|Polacco|    `pl`    |    Neurale|
|Portoghese (Brasile)|    `pt-br`    |    Neurale|
|Portoghese (Portogallo)| `pt-pt` | Neurale
|Punjabi|`pa`|Neurale
|Querétaro Otomi|    `otq`    |    Statistica|
|Romeno|    `ro`    |    Neurale|
|Russo|    `ru`    |    Neurale|
|Samoano|    `sm`    |    Statistica|
|Serbo (alfabeto cirillico)|    `sr-Cyrl`|    Statistica|
|Serbo (alfabeto latino)|    `sr-Latn`        |Statistica|
|Slovacco|    `sk`    |    Neurale|
|Sloveno|    `sl`    |    Neurale|
|Spagnolo|    `es`    |    Neurale|
|Svedese|    `sv`    |Neurale|
|Tahitiano|    `ty`    |Statistica|
|Tamil|    `ta`    |    Neurale|
|Telugu|    `te`    |    Neurale|
|Thai|    `th`    |    Neurale|
|Tongano|    `to`    |    Statistica|
|Turco|    `tr`        |Neurale|
|Ucraino|    `uk`    |    Neurale|
|Urdu|    `ur`    |    Statistica|
|Vietnamita|    `vi`    |    Neurale|
|Gallese|    `cy`    |    Neurale|
|Yucatec Maya|    `yua`    |    Statistica|

> [!NOTE]
> Il codice lingua `pt` predefinito è `pt-br` , portoghese (Brasile).

## <a name="transliteration"></a>Traslitterazione

Il metodo Transliterate supporta le lingue seguenti. Nella colonna "Verso/Da" il simbolo "<-->" indica che la lingua può essere traslitterata da o verso entrambi gli alfabeti elencati. Il simbolo "-->" indica che la lingua può essere traslitterata solo da un alfabeto all'altro.

| Linguaggio    | Codice lingua | Script | A/da | Script|
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
| Thai | `th` | Thai `Thai` | --> | Latino `Latn` |

## <a name="dictionary"></a>Dizionario

Il dizionario supporta le lingue seguenti verso o dalla lingua inglese tramite i metodi Lookup ed Examples.

| Linguaggio    | Codice lingua |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabo       | `ar`          |
| Bengalese      | `bn`          |
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
| Romeno      | `ro`          |
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

Translator rileva tutte le lingue disponibili per la conversione e la traslitterazione.


## <a name="access-the-translator-language-list-programmatically"></a>Accesso all'elenco di lingue di conversione a livello di codice

È possibile recuperare un elenco di lingue supportate per Translator v 3.0 usando il metodo languages. È possibile visualizzare l'elenco in base alla funzionalità, al codice della lingua, nonché al nome della lingua in inglese o in qualsiasi altra lingua supportata. L'elenco viene aggiornato automaticamente dal servizio Microsoft Translator quando diventano disponibili nuove lingue.

[Visualizzare la documentazione di riferimento dell'operazione Languages](reference/v3-0-languages.md)

## <a name="customization"></a>Personalizzazione

Le lingue seguenti sono disponibili per la personalizzazione da o verso l'inglese utilizzando il [convertitore personalizzato](https://aka.ms/CustomTranslator).

| Linguaggio    | Codice lingua |
|:----------- |:-------------:|
| Arabo       | `ar`          |
| Bengalese      | `bn`          |
| Bosniaco (latino)      | `bs`          |
| Bulgaro      | `bg`          |
| Cinese semplificato      | `zh-Hans`          |
|Cinese tradizionale|    `zh-Hant`    |
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
| Indonesiano|    `id`    |
| Irlandese | `ga`    |
| Italiano      | `it`          |
| Giapponese      | `ja`          |
| Kiswahili|    `sw`    |
| Coreano      | `ko`          |
| Lettone      | `lv`          |
| Lituano      | `lt`          |
| Malgascio|    `mg`    |
| Maori| `mi`  |
| Norvegese      | `nb`          |
| Persiano      | `fa`          |
| Polacco      | `pl`          |
| Portoghese (Brasile) | `pt-br` |
| Romeno      | `ro`          |
| Russo      | `ru`          |
| Samoano|    `sm`    |
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

Per una rapida panoramica delle lingue, il sito Web Microsoft Translator Mostra tutte le lingue supportate dalle API di traduzione e sintesi vocale. L'elenco non include informazioni specifiche per sviluppatori, ad esempio i codici delle lingue.

[Visualizzare l'elenco delle lingue](https://www.microsoft.com/translator/languages.aspx)
