---
title: Lingue supportate - API Traduzione vocale
titlesuffix: Azure Cognitive Services
description: Informazioni sulle lingue supportate dall'API Traduzione vocale.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 3/5/2018
ms.author: v-jansko
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 513fd93f148a8a133901a5dbb60722fbc4a7d9e7
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866260"
---
# <a name="languages-supported-by-the-translator-speech-api"></a>Lingue supportate dall'API Traduzione vocale

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Di seguito sono elencate le lingue supportate per la traduzione vocale. Se per la traduzione vocale sono supportate entrambe le lingue, sono disponibili i servizi di traduzione da contenuto vocale a contenuto vocale o da contenuto vocale a testo. Se la lingua di destinazione non è supportata per la traduzione vocale, è disponibile solo la traduzione da contenuto vocale a testo.

| Lingua per la sintesi vocale    |
|:----------- |
| Arabo (standard moderno)      |
| Portoghese (Brasile)     |
| Cinese (mandarino)      |
| Inglese      |
| Francese      |
| Tedesco      |
| Italiano      |
| Giapponese      |
| Russo      |
| Spagnolo      |

L'API Traduzione vocale supporta le lingue seguenti come lingue di destinazione per la traduzione da contenuto vocale a testo.

| Lingua per il testo    | Codice lingua |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabo       | `ar`          |
| Bengalese      | `bn`          |
| Bosniaco (latino)      | `bs`          |
| Bulgaro      | `bg`          |
| Cantonese (tradizionale)      | `yue`          |
| Catalano      | `ca`          |
| Cinese semplificato      | `zh-Hans`          |
| Cinese tradizionale      | `zh-Hant`          |
| Croato      | `hr`          |
| Ceco      | `cs`          |
| Danese      | `da`          |
| Olandese      | `nl`          |
| Inglese      | `en`          |
| Estone      | `et`          |
| Figiano      | `fj`          |
| Filippino      | `fil`          |
| Finlandese      | `fi`          |
| Francese      | `fr`          |
| Tedesco      | `de`          |
| Greco      | `el`          |
| Creolo haitiano      | `ht`          |
| Ebraico      | `he`          |
| Hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| Ungherese      | `hu`          |
|Islandese|`is`          |
| Indonesiano      | `id`          |
| Italiano      | `it`          |
| Giapponese      | `ja`          |
| Kiswahili      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| Coreano      | `ko`          |
| Lettone      | `lv`          |
| Lituano      | `lt`          |
| Malgascio      | `mg`          |
| Malese      | `ms`          |
| Maltese      | `mt`          |
| Norvegese      | `nb`          |
| Persiano      | `fa`          |
| Polacco      | `pl`          |
| Portoghese      | `pt`          |
| Querétaro Otomi      | `otq`          |
| Rumeno      | `ro`          |
| Russo      | `ru`          |
| Samoano      | `sm`          |
| Serbo (alfabeto cirillico)      | `sr-Cyrl`          |
| Serbo (alfabeto latino)      | `sr-Latn`          |
| Slovacco     | `sk`          |
| Sloveno      | `sl`          |
| Spagnolo      | `es`          |
| Svedese      | `sv`          |
| Tahitiano      | `ty`          |
| Tamil      | `ta`          |
| Thai      | `th`          |
| Tongano      | `to`          |
| Turco      | `tr`          |
| Ucraino      | `uk`          |
| Urdu      | `ur`          |
| Vietnamita      | `vi`          |
| Gallese      | `cy`          |
| Yucatec Maya      | `yua`          |

## <a name="access-the-list-programmatically"></a>Accedere all'elenco a livello di codice

È possibile accedere all'elenco di lingue supportate a livello di codice usando la risorsa Languages. L'elenco fornice il codice della lingua e il nome della lingua in inglese o in qualsiasi altra lingua supportata. L'elenco viene aggiornato automaticamente dal servizio Traduzione vocale quando vengono rese disponibili nuove lingue.

La risorsa Languages restituisce l'elenco di lingue supportate per riconoscimento vocale, testo e sintesi vocale. La risorsa Languages non richiede l'autenticazione.

[Vedere le informazioni di riferimento sull'API per provare il metodo per le lingue](languages-reference.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Accedere all'elenco nel sito Web Microsoft Translator

Per una rapida panoramica delle lingue, il sito Web Microsoft Translator offre l'elenco di tutte le lingue supportate dalle API Traduzione testuale e Traduzione vocale. L'elenco non include informazioni specifiche per sviluppatori, ad esempio i codici delle lingue.

[Visualizzare l'elenco delle lingue](https://www.microsoft.com/translator/languages.aspx)
