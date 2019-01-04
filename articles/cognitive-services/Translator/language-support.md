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
ms.openlocfilehash: 0b1187083c14fc7c536f6a32f3a41957f53f299b
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2018
ms.locfileid: "52679716"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Supporto lingua e area geografica per l'API Traduzione testuale

L'API Traduzione testuale supporta le lingue seguenti per la traduzione da testo a testo. La traduzione automatica neurale è il nuovo standard per traduzioni automatiche di alta qualità supportate dall'intelligenza artificiale ed è disponibile per impostazione predefinita quando si usa la versione V3 dell'API Traduzione testuale, se è disponibile un sistema neurale. 

[Altre informazioni sull'uso della traduzione automatica](https://www.microsoft.com/translator/mt.aspx)

**API Traduzione V2**

> [!NOTE]
> La versione 2 è stata deprecata il 30 aprile 2018 e verrà sospesa il 30 aprile 2019.

* Solo sistema statistico: nessun sistema neurale è disponibile per questa lingua.
* Sistema neurale disponibile: è disponibile un sistema neurale. Usare il parametro `category=generalnn` per accedere al sistema neurale.
* Sistema neurale come predefinito: il sistema neurale è il sistema di traduzione predefinito. Usare il parametro `category=smt` per accedere al sistema statistico per l'uso con l'Hub di Microsoft Translator.
* Solo sistema neurale: è disponibile solo la traduzione neurale.

**API Traduzione V3** l'API Traduzione V3 è neurale per impostazione predefinita e i sistemi statistici sono disponibili solo quando non è presente alcun sistema neurale. Il traduttore personalizzato è utilizzabile solo con i linguaggi neurali. 

|Linguaggio|  Codice lingua|  API V2| API V3|
|:-----|:-----:|:-----|:-----|
|Afrikaans| `af`    |Solo sistema statistico|  Neurale|
|Arabo|    `ar`    |Sistema neurale disponibile|  Neurale|
|Arabo levantino| `apc`   |Sistema neurale disponibile|  Neurale|
|Bengalese|    `bn`    |Sistema neurale disponibile|  Neurale|
|Bosniaco (latino)|   `bs`    |Solo sistema statistico|  Statistica|
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
|Persiano|   `fa`    |Solo sistema statistico|  Statistica|
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
|Tahitiano|  `ty`    |Solo sistema statistico|  Statistica|
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
