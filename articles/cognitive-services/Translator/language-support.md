---
title: Supporto per la lingua - API Traduzione testuale
titleSuffix: Azure Cognitive Services
description: Elenco di lingue naturali supportate dall'API Traduzione testuale.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 09/25/2018
ms.author: v-jansko
ms.openlocfilehash: 222e37e38772b82e9d9849e3a955b865d43d3c63
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957393"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Supporto lingua e area geografica per l'API Traduzione testuale

L'API Traduzione testuale supporta le lingue seguenti per la traduzione da testo a testo. La traduzione automatica neurale è il nuovo standard per traduzioni automatiche di alta qualità supportate dall'intelligenza artificiale ed è disponibile per impostazione predefinita quando si usa la versione V3 dell'API Traduzione testuale, se è disponibile un sistema neurale. La traduzione automatica neurale è disponibile nella versione V2 usando la categoria "generalnn".

[Altre informazioni sull'uso della traduzione automatica](https://www.microsoft.com/translator/mt.aspx)

| Linguaggio    | Tipo di traduzione |Codice lingua |
|:----------- |:-------:|:-------------:|
| Afrikaans      | Statistica |`af`          |
| Arabo      | Neurale | `ar`          |
| Arabo levantino    | Neurale | `apc`
| Bengalese      | Neurale |`bn`          |
| Bosniaco (alfabeto latino)      | Statistica |`bs`          |
| Bulgaro     |  Neurale |`bg`          |
| Cantonese (tradizionale)      | Statistica |`yue`          |
| Catalano      | Statistica |`ca`          |
| Cinese semplificato        |  Neurale |`zh-Hans`          |
| Cinese tradizionale        |  Neurale |`zh-Hant`          |
| Croato      | Neurale |`hr`          |
| Ceco        |  Neurale |`cs`          |
| Danese        |  Neurale |`da`          |
| Olandese        |  Neurale |`nl`          |
| Inglese       |  Neurale |`en`          |
| Estone      | Neurale |`et`          |
| Figiano      | Statistica |`fj`          |
| Filippino      | Statistica |`fil`          |
| Finlandese      | Neurale |`fi`          |
| Francese        |  Neurale |`fr`          |
| Tedesco       |  Neurale |`de`          |
| Greco      | Neurale |`el`          |
| Creolo haitiano      | Statistica |`ht`          |
| Ebraico      | Neurale |`he`          |
| Hindi        |  Neurale |`hi`          |
| Hmong Daw      | Statistica |`mww`          |
| Ungherese      | Neurale |`hu`          |
| Islandese      |  Neurale |`is`           |
| Indonesiano      | Statistica |`id`          |
| Italiano        |  Neurale |`it`          |
| Giapponese        |  Neurale |`ja`          |
| Swahili      | Statistica |`sw`          |
| Klingon      | Statistica |`tlh`          |
| Klingon (plqaD)      | Statistica |`tlh-Qaak`          |
| Coreano        |  Neurale |`ko`          |
| Lettone      | Neurale |`lv`          |
| Lituano      | Neurale |`lt`          |
| Malgascio      | Statistica |`mg`          |
| Malese      | Statistica |`ms`          |
| Maltese      | Statistica |`mt`          |
| Norvegese        |  Neurale |`nb`          |
| Persiano      | Statistica |`fa`          |
| Polacco        |  Neurale |`pl`          |
| Portoghese        |  Neurale |`pt`          |
| Querétaro Otomi      | Statistica |`otq`          |
| Rumeno        |  Neurale |`ro`          |
| Russo        |  Neurale |`ru`          |
| Samoano      | Statistica |`sm`          |
| Serbo (alfabeto cirillico)      | Statistica |`sr-Cyrl`          |
| Serbo (alfabeto latino)      | Statistica |`sr-Latn`          |
| Slovacco     | Neurale |`sk`          |
| Sloveno      | Neurale |`sl`          |
| Spagnolo        |  Neurale |`es`          |
| Svedese        |  Neurale |`sv`          |
| Taitiano      | Statistica |`ty`          |
| Tamil      | Statistica |`ta`          |
| Telugu   | Neurale   | `te` |
| Thai      | Neurale |`th`          |
| Tongano      | Statistica |`to`          |
| Turco       |  Neurale |`tr`          |
| Ucraino      | Neurale |`uk`          |
| Urdu      | Statistica |`ur`          |
| Vietnamita      | Neurale |`vi`          |
| Gallese      | Neurale |`cy`          |
| Yucatec Maya      | Statistica |`yua`          |

## <a name="transliteration"></a>Traslitterazione

Il metodo Transliterate supporta le lingue seguenti. Nella colonna "Verso/Da" il simbolo "<-->" indica che la lingua può essere traslitterata da o verso entrambi gli alfabeti elencati. Il simbolo "-->" indica che la lingua può essere traslitterata solo da un alfabeto all'altro.

| Linguaggio    | Codice lingua | Script | Verso/Da | Script|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabo | ar | Arabo | <--> | Latino |
|Bengalese  | bn | Bengalese | <--> | Latino |
| Cinese (semplificato) | zh-Hans | Cinese semplificato | <--> | Latino |
| Cinese (semplificato) | zh-Hans | Cinese semplificato | <--> | Cinese tradizionale |
| Cinese (tradizionale) | zh-Hant | Cinese tradizionale | <--> | Latino |
| Cinese (tradizionale) | zh-Hant | Cinese tradizionale | <--> | Cinese semplificato |
| Gujarati | gu  | Gujarati | --> | Latino |
| Ebraico | he | Ebraico | <--> | Latino |
| Hindi | hi | Devanagari | <--> | Latino |
| Giapponese | ja | Giapponese | <--> | Latino |
| Kannada | kn | Kannada | --> | Latino |
| Malayalam | ml | Malayalam | --> | Latino |
| Marathi | mr | Devanagari | --> | Latino |
| Oriya | oppure | Oriya | <--> | Latino |
| Punjabi | pa | Gurmukhi | <--> | Latino  |
| Serbo (alfabeto cirillico) | sr-Cyrl | Cirillico  | --> | Latino |
| Serbo (alfabeto latino) | sr-Latn | Latino | --> | Cirillico |
| Tamil | ta | Tamil | --> | Latino |
| Telugu | te | Telugu | --> | Latino |
| Thai | th | Thai | <--> | Latino |

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

## <a name="languages-detected-by-the-detect-method"></a>Lingue rilevate dal metodo Detect

Il metodo Detect è in grado di rilevare le lingue seguenti. Detect può rilevare lingue non tradotte da Microsoft Translator.

| Linguaggio    |
|:----------- |
| Afrikaans |
| Albanese |
| Arabo |
| Basco |
| Bielorusso |
| Bulgaro |
| Catalano |
| Cinese |
| Cinese (semplificato) |
| Cinese (tradizionale) |
| Croato |
| Ceco |
| Danese |
| Olandese |
| Inglese |
| Esperanto |
| Estone |
| Finlandese |
| Francese |
| Galiziano |
| Tedesco |
| Greco |
| Creolo haitiano |
| Ebraico |
| Hindi |
| Ungherese |
| Islandese |
| Indonesiano |
| Irlandese |
| Italiano |
| Giapponese |
| Coreano |
| Curdo (paesi arabi) |
| Curdo (Latino) |
| Latino |
| Lettone |
| Lituano |
| Macedone |
| Malese |
| Maltese |
| Norvegese |
| Norvegese (Nynorsk) |
| Pashto |
| Persiano |
| Polacco |
| Portoghese |
| Rumeno |
| Russo |
| Serbo (alfabeto cirillico) |
| Serbo (alfabeto latino) |
| Slovacco |
| Sloveno |
| Somalo |
| Spagnolo |
| Swahili |
| Svedese |
| Tagalog |
| Telugu |
| Thai |
| Turco |
| Ucraino |
| Urdu |
| Uzbeco (alfabeto cirillico) |
| Uzbeco (alfabeto latino) |
| Vietnamita |
| Gallese |
| Yiddish |

## <a name="access-the-list-programmatically"></a>Accedere all'elenco a livello di codice

È possibile accedere all'elenco di lingue supportate a livello di codice usando l'operazione Languages dell'API Traduzione testuale V3.0. È possibile visualizzare l'elenco in base alla funzionalità, al codice della lingua, nonché al nome della lingua in inglese o in qualsiasi altra lingua supportata. L'elenco viene aggiornato automaticamente dal servizio Microsoft Translator quando vengono rese disponibili nuove lingue.

[Visualizzare la documentazione di riferimento dell'operazione Languages](reference/v3-0-languages.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Accedere all'elenco nel sito Web di Microsoft Translator

Per una rapida panoramica delle lingue, il sito Web di Microsoft Translator offre l'elenco di tutte le lingue supportate dalle API Traduzione testuale e Traduzione vocale. L'elenco non include informazioni specifiche per sviluppatori, ad esempio i codici delle lingue.

[Visualizzare l'elenco delle lingue](https://www.microsoft.com/translator/languages.aspx)
