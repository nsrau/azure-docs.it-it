---
title: Supporto per la lingua - API servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: I servizi di riconoscimento vocale di Azure supportano numerose lingue per la conversione della voce in testo scritto e la sintesi vocale, insieme a funzionalità di traduzione vocale. Questo articolo fornisce un elenco completo delle lingue supportate per ogni servizio.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: bc85de9d2d94cd325095f5f6555105a724865005
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55213319"
---
# <a name="language-and-region-support-for-speech-service-api"></a>Supporto di lingua e area geografica per API servizio di riconoscimento vocale

Le diverse funzioni dei servizi di riconoscimento vocale supportano lingue diverse. Le tabelle seguenti riepilogano il supporto della lingua.

## <a name="speech-to-text"></a>Riconoscimento vocale

L'API Riconoscimento vocale Microsoft supporta le lingue seguenti. Livelli diversi di personalizzazione sono disponibili per ogni lingua.

  Codice | Linguaggio | [Adattamento acustico](how-to-customize-acoustic-models.md) | [Adattamento linguistico](how-to-customize-language-model.md) | [Adattamento della pronuncia](how-to-customize-pronunciation.md)
 ------|----------|---------------------|---------------------|-------------------------
 ar-EG | Arabo (Egitto), standard moderno | No  | Yes | No 
 ca-ES | Catalano (Spagna) | No  | No  | No 
 da-DK | Danese (Danimarca) | No  | No  | No 
 de-DE | Tedesco (Germania) | Yes | Sì | No 
 en-AU | Inglese (Australia) | No  | Yes | Yes
 en-CA | Inglese (Canada) | No  | Yes | Yes
 en-GB | Inglese (Regno Unito) | No  | Yes | Yes
 en-IN | Inglese (India) | Yes | Sì | Yes
 en-NZ | Inglese (Nuova Zelanda) | No  | Yes | Yes  
 en-US | Inglese (Stati Uniti) | Yes | Sì | Yes
 es-ES | Spagnolo (Spagna) | Yes | Sì | No 
 es-MX | Spagnolo (Messico) | No  | Yes | No 
 fi-FI | Finlandese (Finlandia) | No  | No  | No 
 fr-CA | Francese (Canada) | No  | Yes | No 
 fr-FR | Francese (Francia) | Yes | Sì | No 
 hi-IN | Hindi (India) | No  | Yes | No 
 it-IT | Italiano (Italia) | Yes | Sì | No 
 ja-JP | Giapponese (Giappone) | No  | Yes | No 
 ko-KR | Coreano (Corea) | No  | Yes | No 
 nb-NO | Norvegese (Bokmål) (Norvegia) | No  | No  | No 
 nl-NL | Olandese (Paesi Bassi) | No  | Yes | No 
 pl-PL | Polacco (Polonia) | No  | No  | No 
 pt-BR | Portoghese (Brasile) | Yes | Sì | No 
 pt-PT | Portoghese (Portogallo) | No  | Yes | No 
 ru-RU | Russo (Russia) | Yes | Sì | No 
 sv-SE | Svedese (Svezia) | No  | No  | No 
 zh-CN | Cinese (mandarino, semplificato) | Yes | Sì | No 
 zh-HK | Cinese (mandarino, tradizionale) | No  | Yes | No 
 zh-TW | Cinese (mandarino taiwanese) | No  | Yes | No 
 th-TH | Thailandese (Thailandia) | No  | No  | No 


## <a name="text-to-speech"></a>Sintesi vocale

L'API REST di sintesi vocale supporta le voci seguenti, ognuna delle quali supporta una lingua e una lingua regionale specifiche, identificate dalle impostazioni locali.

> [!IMPORTANT]
> I prezzi variano per voci standard, personalizzate e neurali. Per informazioni aggiuntive, visitare la pagina [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="neural-voices-preview"></a>Voci neurali (anteprima)

La sintesi vocale neurale è un nuovo tipo di sintesi vocale basata su reti neurali profonde. Quando si usa una voce neurale, è praticamente impossibile distinguere la sintesi vocale dalle registrazioni umane.

Le voci neurali possono essere usate per rendere più naturali e coinvolgenti le interazioni con chatbot e assistenti virtuali, per convertire testo digitale, come gli e-book, in audiolibri e per migliorare i sistemi dei navigatori per le automobili. Con la prosodia naturale simile al linguaggio umano e l'articolazione chiara delle parole, le voci neurali riducono in modo significativo le difficoltà di ascolto durante l'interazione degli utenti con i sistemi di intelligenza artificiale.

Per un elenco completo delle voci neurali con informazioni sulla disponibilità a livello di area, vedere [aree](regions.md#neural-voices).

| Impostazioni locali | Linguaggio | Sesso | Mapping nome del servizio|
|--------|----------|---------|--------------------|
| en-US | Inglese (Stati Uniti) | Maschio | "Microsoft Server Speech Text to Speech Voice (en-US, GuyNeural)" |
| en-US | Inglese (Stati Uniti) | Femmina | "Microsoft Server Speech Text to Speech Voice (en-US, JessaNeural)" |
| zh-CN | Cinese | Femmina | "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)" |

> [!IMPORTANT]
> Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural) è disponibile solo tramite l'endpoint dell'Asia sud-orientale: https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1.

### <a name="standard-voices"></a>Voci standard

Sono disponibili più di 75 voci standard in oltre 45 lingue e impostazioni locali, che consentono di convertire il testo in contenuto vocale sintetizzato. Per informazioni sulla disponibilità a livello di area, vedere [Aree](regions.md#standard-voices).

Impostazioni locali | Linguaggio | Sesso | Mapping nome del servizio
-------|----------|---------|--------------------
ar-EG\* | Arabo (Egitto) | Femmina | "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)"
ar-SA | Arabo (Arabia Saudita) | Maschio | "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)"
bg-BG | Bulgaro | Maschio | "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)"
ca-ES | Catalano (Spagna) | Femmina | "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)"
cs-CZ | Ceco | Maschio | "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)"
da-DK | Danese | Femmina | "Microsoft Server Speech Text to Speech Voice (da-DK, HelleRUS)"
de-AT | Tedesco (Austria) | Maschio | "Microsoft Server Speech Text to Speech Voice (de-AT, Michael)"
de-CH | Tedesco (Svizzera) | Maschio | "Microsoft Server Speech Text to Speech Voice (de-CH, Karsten)"
de-DE | Tedesco (Germania) | Femmina | "Microsoft Server Speech Text to Speech Voice (de-DE, Hedda)"
| | | Femmina | "Microsoft Server Speech Text to Speech Voice (de-DE, HeddaRUS)"
| | | Maschio | "Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)"
el-GR | Greco | Maschio | "Microsoft Server Speech Text to Speech Voice (el-GR, Stefanos)"
en-AU | Inglese (Australia) | Femmina | "Microsoft Server Speech Text to Speech Voice (en-AU, Catherine)"
| | | Femmina | "Microsoft Server Speech Text to Speech Voice (en-AU, HayleyRUS)"
en-CA | Inglese (Canada) | Femmina | "Microsoft Server Speech Text to Speech Voice (en-CA, Linda)"
| | | Femmina | "Microsoft Server Speech Text to Speech Voice (en-CA, HeatherRUS)"
en-GB | Inglese (Regno Unito) | Femmina | "Microsoft Server Speech Text to Speech Voice (en-GB, Susan, Apollo)"
| | |Femmina | "Microsoft Server Speech Text to Speech Voice (en-GB, HazelRUS)"
| | |Maschio | "Microsoft Server Speech Text to Speech Voice (en-GB, George, Apollo)"
en-IE | Inglese (Irlanda) |Maschio | "Microsoft Server Speech Text to Speech Voice (en-IE, Sean)"
en-IN | Inglese (India) | Femmina | "Microsoft Server Speech Text to Speech Voice (en-IN, Heera, Apollo)"
| | |Femmina | "Microsoft Server Speech Text to Speech Voice (en-IN, PriyaRUS)"
| | |Maschio | "Microsoft Server Speech Text to Speech Voice (en-IN, Ravi, Apollo)"
en-US | Inglese (Stati Uniti) |Femmina | "Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)"
| | |Femmina | "Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)"
| | |Maschio | "Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)"
| | |Femmina | "Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)"
| | |Maschio | "Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)"
es-ES | Spagnolo (Spagna) |Femmina | "Microsoft Server Speech Text to Speech Voice (es-ES, Laura, Apollo)"
| | |Femmina | "Microsoft Server Speech Text to Speech Voice (es-ES, HelenaRUS)"
| | |Maschio | "Microsoft Server Speech Text to Speech Voice (es-ES, Pablo, Apollo)"
es-MX | Spagnolo (Messico) | Femmina | "Microsoft Server Speech Text to Speech Voice (es-MX, HildaRUS)"
| | | Maschio | "Microsoft Server Speech Text to Speech Voice (es-MX, Raul, Apollo)"
fi-FI | Finlandese | Femmina | "Microsoft Server Speech Text to Speech Voice (fi-FI, HeidiRUS)"
fr-CA | Francese (Canada) |Femmina | "Microsoft Server Speech Text to Speech Voice (fr-CA, Caroline)"
| | | Femmina | "Microsoft Server Speech Text to Speech Voice (fr-CA, HarmonieRUS)"
fr-CH | Francese (Svizzera)|Maschio | "Microsoft Server Speech Text to Speech Voice (fr-CH, Guillaume)"
fr-FR | Francese (Francia)|Femmina | "Microsoft Server Speech Text to Speech Voice (fr-FR, Julie, Apollo)"
| | |Femmina | "Microsoft Server Speech Text to Speech Voice (fr-FR, HortenseRUS)"
| | |Maschio | "Microsoft Server Speech Text to Speech Voice (fr-FR, Paul, Apollo)"
he-IL| Ebraico (Israele) | Maschio| "Microsoft Server Speech Text to Speech Voice (he-IL, Asaf)"
hi-IN | Hindi (India) | Femmina | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana, Apollo)"
| | |Femmina | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana)"
| | | Maschio | "Microsoft Server Speech Text to Speech Voice (hi-IN, Hemant)"
hr-HR | Croato | Maschio | "Microsoft Server Speech Text to Speech Voice (hr-HR, Matej)"
hu-HU | Ungherese | Maschio | "Microsoft Server Speech Text to Speech Voice (hu-HU, Szabolcs)"
id-ID | Indonesiano| Maschio | "Microsoft Server Speech Text to Speech Voice (id-ID, Andika)"
it-IT | Italiano |Maschio | "Microsoft Server Speech Text to Speech Voice (it-IT, Cosimo, Apollo)"
| | |Femmina | "Microsoft Server Speech Text to Speech Voice (it-IT, LuciaRUS)"
ja-JP | Giapponese |Femmina | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ayumi, Apollo)"
| | |Maschio | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ichiro, Apollo)"
| | |Femmina | "Microsoft Server Speech Text to Speech Voice (ja-JP, HarukaRUS)"
ko-KR | Coreano |Femmina | "Microsoft Server Speech Text to Speech Voice (ko-KR, HeamiRUS)"
ms-MY | Malese | Maschio | "Microsoft Server Speech Text to Speech Voice (ms-MY, Rizwan)"
nb-NO | Norvegese | Femmina | "Microsoft Server Speech Text to Speech Voice (nb-NO, HuldaRUS)"
nl-NL | Olandese | Femmina | "Microsoft Server Speech Text to Speech Voice (nl-NL, HannaRUS)"
pl-PL | Polacco | Femmina | "Microsoft Server Speech Text to Speech Voice (pl-PL, PaulinaRUS)"
pt-BR | Portoghese (Brasile) | Femmina | "Microsoft Server Speech Text to Speech Voice (pt-BR, HeloisaRUS)"
| | |Maschio | "Microsoft Server Speech Text to Speech Voice (pt-BR, Daniel, Apollo)"
pt-PT | Portoghese (Portogallo) | Femmina | "Microsoft Server Speech Text to Speech Voice (pt-PT, HeliaRUS)"
ro-RO | Rumeno | Maschio | "Microsoft Server Speech Text to Speech Voice (ro-RO, Andrei)"
ru-RU |Russo| Femmina | "Microsoft Server Speech Text to Speech Voice (ru-RU, Irina, Apollo)"
| | |Maschio | "Microsoft Server Speech Text to Speech Voice (ru-RU, Pavel, Apollo)"
| | |Femmina | "Microsoft Server Speech Text to Speech Voice (ru-RU, EkaterinaRUS)"
sk-SK | Slovacco|Maschio | "Microsoft Server Speech Text to Speech Voice (sk-SK, Filip)"
sl-SI | Sloveno|Maschio | "Microsoft Server Speech Text to Speech Voice (sl-SI, Lado)"
sv-SE | Svedese|Femmina | "Microsoft Server Speech Text to Speech Voice (sv-SE, HedvigRUS)"
ta-IN | Tamil (India) |Maschio | "Microsoft Server Speech Text to Speech Voice (ta-IN, Valluvar)"
te-IN | Telugu (India) |Femmina | "Microsoft Server Speech Text to Speech Voice (te-IN, Chitra)"
th-TH | Thai|Maschio | "Microsoft Server Speech Text to Speech Voice (th-TH, Pattara)"
tr-TR |Turco| Femmina | "Microsoft Server Speech Text to Speech Voice (tr-TR, SedaRUS)"
vi-VN | Vietnamita|Maschio | "Microsoft Server Speech Text to Speech Voice (vi-VN, An)"
zh-CN | Cinese (Terraferma)|Femmina | "Microsoft Server Speech Text to Speech Voice (zh-CN, HuihuiRUS)"
| | |Femmina | "Microsoft Server Speech Text to Speech Voice (zh-CN, Yaoyao, Apollo)"
| | |Maschio | "Microsoft Server Speech Text to Speech Voice (zh-CN, Kangkang, Apollo)"
zh-HK | Cinese (Hong Kong)|Femmina | "Microsoft Server Speech Text to Speech Voice (zh-HK, Tracy, Apollo)"
| | |Femmina | "Microsoft Server Speech Text to Speech Voice (zh-HK, TracyRUS)"
| || Maschio | "Microsoft Server Speech Text to Speech Voice (zh-HK, Danny, Apollo)"
zh-TW | Cinese (Taiwan)|Femmina | "Microsoft Server Speech Text to Speech Voice (zh-TW, Yating, Apollo)"
| || Femmina | "Microsoft Server Speech Text to Speech Voice (zh-TW, HanHanRUS)"
| || Maschio | "Microsoft Server Speech Text to Speech Voice (zh-TW, Zhiwei, Apollo)"

\* *ar-EG supporta lo standard moderno della lingua araba.*

### <a name="customization"></a>Personalizzazione

La personalizzazione della voce è disponibile per l'inglese (Stati Uniti) (en-US), il cinese (zh-CN), il francese (fr-FR), il tedesco (de-DE) e l'italiano (it-IT).

> [!NOTE]
> Il training per la voce in francese, tedesco e italiano inizia con un set di dati di oltre 2000 espressioni. Anche i modelli bilingui in lingua cinese-inglese sono supportati con un set di dati iniziale di oltre 2000 espressioni.

## <a name="speech-translation"></a>Traduzione vocale

L'API **Traduzione vocale** supporta lingue diverse per la traduzione vocale e con riconoscimento vocale. La lingua di origine deve essere sempre inclusa nella tabella Lingua per il riconoscimento vocale. Le lingue di destinazione disponibili variano a seconda del fatto che siano parlate o scritte. È possibile tradurre la voce in ingresso in più di [60 lingue](https://www.microsoft.com/translator/business/languages/). Un subset di queste lingue è disponibile per la [sintesi vocale](language-support.md#text-languages).

### <a name="text-languages"></a>Lingue per il testo

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


## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
* [Informazioni sul riconoscimento vocale in C#](quickstart-csharp-dotnet-windows.md)
