---
title: Supporto linguistico-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Il servizio di riconoscimento vocale supporta numerose lingue per la conversione di sintesi vocale e sintesi vocale, oltre alla traduzione vocale. Questo articolo fornisce un elenco completo del supporto linguistico per funzionalità del servizio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: abff902dc62ad7ae48f2ecedfbd52aeb96719093
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69970377"
---
# <a name="language-and-region-support-for-the-speech-services"></a>Supporto per lingua e area per i servizi di riconoscimento vocale

Le diverse funzioni dei servizi di riconoscimento vocale supportano lingue diverse. Le tabelle seguenti riepilogano il supporto della lingua.

## <a name="speech-to-text"></a>Riconoscimento vocale

Microsoft Speech Recognition SDK e l'API REST supportano le seguenti lingue (impostazioni locali). Per migliorare l'accuratezza, la personalizzazione viene offerta per un subset di lingue tramite il caricamento di trascrizioni audio e con etichetta umana o testo correlato: Frasi.  La personalizzazione della pronuncia è attualmente disponibile solo per en-US e de-DE. Altre informazioni sulla personalizzazione sono disponibili [qui](how-to-custom-speech.md).

  Codice | Linguaggio | Supportato | Personalizzabile
 ------|----------|---------------------|---------------------
 ar-EG | Arabo (Egitto), standard moderno | Sì | Yes
 ca-ES | Catalano | Yes | No
 da-DK | Danese (Danimarca) | Sì | No
 de-DE | Tedesco (Germania) | Sì | Sì
 en-AU | Inglese (Australia) | Sì | Sì
 en-CA | Inglese (Canada) | Yes | Sì
 en-GB | Inglese (Regno Unito) | Sì | Sì
 en-IN | Inglese (India) | Yes | Sì
 en-NZ | Inglese (Nuova Zelanda) | Sì | Sì
 en-US | Inglese (Stati Uniti) | Sì | Yes
 es-ES | Spagnolo (Spagna) | Sì | Yes
 es-MX | Spagnolo (Messico) | Sì | Sì
 fi-FI | Finlandese (Finlandia) | Yes | No
 fr-CA | Francese (Canada) | Yes | Sì
 fr-FR | Francese (Francia) | Sì | Sì
 hi-IN | Hindi (India) | Sì | Sì
 it-IT | Italiano (Italia) | Sì | Sì
 ja-JP | Giapponese (Giappone) | Yes | Sì
 ko-KR | Coreano (Corea) | Sì | Sì
 nb-NO | Norvegese (Bokmål) (Norvegia) | Sì | No
 nl-NL | Olandese (Paesi Bassi) | Sì | Yes
 pl-PL | Polacco (Polonia) | Sì | No
 pt-BR | Portoghese (Brasile) | Yes | Sì
 pt-PT | Portoghese (Portogallo) | Yes | Yes
 ru-RU | Russo (Russia) | Sì | Yes
 sv-SE | Svedese (Svezia) | Sì | No
 zh-CN | Cinese (mandarino, semplificato) | Sì | Sì
 zh-HK | Cinese (cantonese, tradizionale) | Sì | Sì
 zh-TW | Cinese (mandarino taiwanese) | Yes | Sì
 th-TH | Thailandese (Thailandia) | Sì | No 


## <a name="text-to-speech"></a>Sintesi vocale

L'API REST di sintesi vocale supporta le voci seguenti, ognuna delle quali supporta una lingua e una lingua regionale specifiche, identificate dalle impostazioni locali.

> [!IMPORTANT]
> I prezzi variano per voci standard, personalizzate e neurali. Per informazioni aggiuntive, visitare la pagina [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="neural-voices"></a>Voci neurali

La sintesi vocale neurale è un nuovo tipo di sintesi vocale basata su reti neurali profonde. Quando si usa una voce neurale, è praticamente impossibile distinguere la sintesi vocale dalle registrazioni umane.

Le voci neurali possono essere usate per rendere più naturali e coinvolgenti le interazioni con chatbot e assistenti virtuali, per convertire testo digitale, come gli e-book, in audiolibri e per migliorare i sistemi dei navigatori per le automobili. Con la prosodia naturale simile al linguaggio umano e l'articolazione chiara delle parole, le voci neurali riducono in modo significativo le difficoltà di ascolto durante l'interazione degli utenti con i sistemi di intelligenza artificiale.

Per un elenco completo delle voci neurali con informazioni sulla disponibilità a livello di area, vedere [aree](regions.md#standard-and-neural-voices).

Impostazioni locali | Linguaggio | Sesso | Mapping completo del nome del servizio | Nome breve vocale
--------|----------|--------|---------|------------
de-DE | Tedesco (Germania) | Femmina | "Voce Sintesi vocale vocale di Microsoft Server (de-DE, KatjaNeural)" | "de-DE-KatjaNeural"
en-US | Inglese (Stati Uniti) | Maschio | "Microsoft Server Speech Text to Speech Voice (en-US, GuyNeural)" | "en-US-GuyNeural"
en-US | Inglese (Stati Uniti) | Femmina | "Microsoft Server Speech Text to Speech Voice (en-US, JessaNeural)" | "en-US-JessaNeural"
it-IT | Italiano (Italia) | Femmina |"Voce Sintesi vocale vocale di Microsoft Server (it-IT, ElsaNeural)" | "it-IT-ElsaNeural"
zh-CN | Cinese (Terraferma) | Femmina | "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> È possibile usare il mapping del nome completo del servizio o il nome della voce breve nelle richieste di sintesi vocale.

### <a name="standard-voices"></a>Voci standard

Sono disponibili più di 75 voci standard in oltre 45 lingue e impostazioni locali, che consentono di convertire il testo in contenuto vocale sintetizzato. Per informazioni sulla disponibilità a livello di area, vedere [Aree](regions.md#standard-and-neural-voices).

Impostazioni locali | Linguaggio | Sesso | Mapping completo del nome del servizio | Nome breve vocale
-------|----------|---------|----------|----------
ar-EG\* | Arabo (Egitto) | Femmina | "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)" | "ar-EG-un".
ar-SA | Arabo (Arabia Saudita) | Maschio | "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)" | "ar-SA-Naayf"
bg-BG | Bulgaro | Maschio | "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)" | "BG-BG-Ivan"
ca-ES | Catalano (Spagna) | Femmina | "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)" | "ca-ES-HerenaRUS"
cs-CZ | Ceco | Maschio | "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)" | "CS-CZ-Jakub"
da-DK | Danese | Femmina | "Microsoft Server Speech Text to Speech Voice (da-DK, HelleRUS)" | "da-DK-HelleRUS"
de-AT | Tedesco (Austria) | Maschio | "Microsoft Server Speech Text to Speech Voice (de-AT, Michael)" | "de-AT-Michael"
de-CH | Tedesco (Svizzera) | Maschio | "Microsoft Server Speech Text to Speech Voice (de-CH, Karsten)" | "de-CH-Karsten"
de-DE | Tedesco (Germania) | Femmina | "Microsoft Server Speech Text to Speech Voice (de-DE, Hedda)" | "de-DE-Hedda"
| | | Femmina | "Microsoft Server Speech Text to Speech Voice (de-DE, HeddaRUS)" | "de-DE-HeddaRUS"
| | | Maschio | "Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo"
el-GR | Greco | Maschio | "Microsoft Server Speech Text to Speech Voice (el-GR, Stefanos)" | "El-GR-Stefanos"
en-AU | Inglese (Australia) | Femmina | "Microsoft Server Speech Text to Speech Voice (en-AU, Catherine)" | "en-AU-Catherine"
| | | Femmina | "Microsoft Server Speech Text to Speech Voice (en-AU, HayleyRUS)" | "en-AU-HayleyRUS"
en-CA | Inglese (Canada) | Femmina | "Microsoft Server Speech Text to Speech Voice (en-CA, Linda)" | "en-CA-Linda"
| | | Femmina | "Microsoft Server Speech Text to Speech Voice (en-CA, HeatherRUS)" | "en-CA-HeatherRUS"
en-GB | Inglese (Regno Unito) | Femmina | "Microsoft Server Speech Text to Speech Voice (en-GB, Susan, Apollo)" | "en-GB-Susan-Apollo"
| | | Femmina | "Microsoft Server Speech Text to Speech Voice (en-GB, HazelRUS)" | "en-GB-HazelRUS"
| | | Maschio | "Microsoft Server Speech Text to Speech Voice (en-GB, George, Apollo)" | "en-GB-George-Apollo"
en-IE | Inglese (Irlanda) | Maschio | "Microsoft Server Speech Text to Speech Voice (en-IE, Sean)" | "en-IE-Sean"
en-IN | Inglese (India) | Femmina | "Microsoft Server Speech Text to Speech Voice (en-IN, Heera, Apollo)" | "en-IN-si-Apollo"
| | | Femmina | "Microsoft Server Speech Text to Speech Voice (en-IN, PriyaRUS)" | "en-IN-PriyaRUS"
| | | Maschio | "Microsoft Server Speech Text to Speech Voice (en-IN, Ravi, Apollo)" | "en-IN-Ravi-Apollo"
en-US | Inglese (Stati Uniti) | Femmina | "Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)" | "en-US-ZiraRUS"
| | | Femmina | "Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)" | "en-US-JessaRUS"
| | | Maschio | "Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)" | "en-US-BenjaminRUS"
| | | Femmina | "Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)" | "en-US-Jessa24kRUS"
| | | Maschio | "Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)" | "en-US-Guy24kRUS"
es-ES | Spagnolo (Spagna) |Femmina | "Microsoft Server Speech Text to Speech Voice (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo"
| | | Femmina | "Microsoft Server Speech Text to Speech Voice (es-ES, HelenaRUS)" | "es-ES-HelenaRUS"
| | | Maschio | "Microsoft Server Speech Text to Speech Voice (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo"
es-MX | Spagnolo (Messico) | Femmina | "Microsoft Server Speech Text to Speech Voice (es-MX, HildaRUS)" | "es-MX-HildaRUS"
| | | Maschio | "Microsoft Server Speech Text to Speech Voice (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo"
fi-FI | Finlandese | Femmina | "Microsoft Server Speech Text to Speech Voice (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS"
fr-CA | Francese (Canada) |Femmina | "Microsoft Server Speech Text to Speech Voice (fr-CA, Caroline)" | "fr-CA-Caroline"
| | | Femmina | "Microsoft Server Speech Text to Speech Voice (fr-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS"
fr-CH | Francese (Svizzera)| Maschio | "Microsoft Server Speech Text to Speech Voice (fr-CH, Guillaume)" | "fr-CH-Guillaume"
fr-FR | Francese (Francia)| Femmina | "Microsoft Server Speech Text to Speech Voice (fr-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo"
| | | Femmina | "Microsoft Server Speech Text to Speech Voice (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS"
| | | Maschio | "Microsoft Server Speech Text to Speech Voice (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo"
he-IL| Ebraico (Israele) | Maschio| "Microsoft Server Speech Text to Speech Voice (he-IL, Asaf)" | "he-IL-la
hi-IN | Hindi (India) | Femmina | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana, Apollo)" | "hi-IN-Kalpana-Apollo"
| | |Femmina | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana)" | "hi-IN-Kalpana"
| | | Maschio | "Microsoft Server Speech Text to Speech Voice (hi-IN, Hemant)" | "Hi-IN-,"
hr-HR | Croato | Maschio | "Microsoft Server Speech Text to Speech Voice (hr-HR, Matej)" | "HR-HR-Matej"
hu-HU | Ungherese | Maschio | "Microsoft Server Speech Text to Speech Voice (hu-HU, Szabolcs)" | "hu-HU-se"
id-ID | Indonesiano| Maschio | "Microsoft Server Speech Text to Speech Voice (id-ID, Andika)" | "ID-ID-Andika"
it-IT | Italiano | Maschio | "Microsoft Server Speech Text to Speech Voice (it-IT, Cosimo, Apollo)" | "it-IT-Cosimo-Apollo"
| | | Femmina | "Microsoft Server Speech Text to Speech Voice (it-IT, LuciaRUS)" | "it-IT-LuciaRUS"
ja-JP | Giapponese | Femmina | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo"
| | | Maschio | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo"
| | | Femmina | "Microsoft Server Speech Text to Speech Voice (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS"
ko-KR | Coreano | Femmina | "Microsoft Server Speech Text to Speech Voice (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS"
ms-MY | Malese | Maschio | "Microsoft Server Speech Text to Speech Voice (ms-MY, Rizwan)" | "MS-MY-per"
nb-NO | Norvegese | Femmina | "Microsoft Server Speech Text to Speech Voice (nb-NO, HuldaRUS)" | "nb-NO-HuldaRUS"
nl-NL | Olandese | Femmina | "Microsoft Server Speech Text to Speech Voice (nl-NL, HannaRUS)" | "nl-NL-HannaRUS"
pl-PL | Polacco | Femmina | "Microsoft Server Speech Text to Speech Voice (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS"
pt-BR | Portoghese (Brasile) | Femmina | "Microsoft Server Speech Text to Speech Voice (pt-BR, HeloisaRUS)" | "PT-BR-HeloisaRUS"
| | | Maschio |"Microsoft Server Speech Text to Speech Voice (pt-BR, Daniel, Apollo)" | "PT-BR-Daniel-Apollo"
pt-PT | Portoghese (Portogallo) | Femmina | "Microsoft Server Speech Text to Speech Voice (pt-PT, HeliaRUS)" | "PT-PT-HeliaRUS"
ro-RO | Rumeno | Maschio | "Microsoft Server Speech Text to Speech Voice (ro-RO, Andrei)" | "ro-RO-Andrei"
ru-RU |Russo| Femmina | "Microsoft Server Speech Text to Speech Voice (ru-RU, Irina, Apollo)" | "ur-ur-Irina-Apollo"
| | | Maschio | "Microsoft Server Speech Text to Speech Voice (ru-RU, Pavel, Apollo)" | "ur-ur-Pavel-Apollo"
| | | Femmina | "Microsoft Server Speech Text to Speech Voice (ru-RU, EkaterinaRUS)" | ur-ur-EkaterinaRUS
sk-SK | Slovacco | Maschio | "Microsoft Server Speech Text to Speech Voice (sk-SK, Filip)" | "sk-SK-Filip"
sl-SI | Sloveno | Maschio | "Microsoft Server Speech Text to Speech Voice (sl-SI, Lado)" | "sl-SI-Lado"
sv-SE | Svedese | Femmina | "Microsoft Server Speech Text to Speech Voice (sv-SE, HedvigRUS)" | "sv-SE-HedvigRUS"
ta-IN | Tamil (India) | Maschio | "Microsoft Server Speech Text to Speech Voice (ta-IN, Valluvar)" | "TA-IN-Valluvar"
te-IN | Telugu (India) | Femmina | "Microsoft Server Speech Text to Speech Voice (te-IN, Chitra)" | "te-IN-Chitle"
th-TH | Thai | Maschio | "Microsoft Server Speech Text to Speech Voice (th-TH, Pattara)" | "th-TH-Pattara"
tr-TR | Turco | Femmina | "Microsoft Server Speech Text to Speech Voice (tr-TR, SedaRUS)" | "tr-TR-SedaRUS"
vi-VN | Vietnamita | Maschio | "Microsoft Server Speech Text to Speech Voice (vi-VN, An)" | "vi-VN-An"
zh-CN | Cinese (Terraferma) | Femmina | "Microsoft Server Speech Text to Speech Voice (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS"
| | | Femmina | "Microsoft Server Speech Text to Speech Voice (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo"
| | | Maschio | "Microsoft Server Speech Text to Speech Voice (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo"
zh-HK | Cinese (Hong Kong) | Femmina | "Microsoft Server Speech Text to Speech Voice (zh-HK, Tracy, Apollo)" | "zh-HK-Tracy-Apollo"
| | | Femmina | "Microsoft Server Speech Text to Speech Voice (zh-HK, TracyRUS)" | "zh-HK-TracyRUS"
| | | Maschio | "Microsoft Server Speech Text to Speech Voice (zh-HK, Danny, Apollo)" | "zh-HK-Danny-Apollo"
zh-TW | Cinese (Taiwan) | Femmina | "Microsoft Server Speech Text to Speech Voice (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo"
| | | Femmina | "Microsoft Server Speech Text to Speech Voice (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS"
| | | Maschio | "Microsoft Server Speech Text to Speech Voice (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo"

\* *ar-EG supporta lo standard moderno della lingua araba.*

> [!NOTE]
> È possibile usare il mapping del nome completo del servizio o il nome della voce breve nelle richieste di sintesi vocale.

### <a name="customization"></a>Personalizzazione

La personalizzazione vocale è disponibile per de-DE, en-GB, en-IN, en-US, es-MX, fr-FR, it-IT, PT-BR e zh-CN. Selezionare le impostazioni locali corrette che corrispondono ai dati di training per il training di un modello vocale personalizzato. Se, ad esempio, i dati di registrazione sono pronunciati in inglese con un accento britannico, selezionare en-GB.  

> [!NOTE]
> Non è supportato il training del modello bilingue in una voce personalizzata, ad eccezione della lingua inglese cinese (BI). Selezionare "cinese-inglese bilingue" per eseguire il training di una voce cinese che può pronunciare anche l'inglese. Il training vocale in tutte le impostazioni locali inizia con un set di dati di 2000 + espressioni, ad eccezione di en-US e zh-CN, in cui è possibile iniziare con qualsiasi dimensione dei dati di training.

## <a name="speech-translation"></a>Traduzione vocale

L'API **Traduzione vocale** supporta lingue diverse per la traduzione vocale e con riconoscimento vocale. La lingua di origine deve essere sempre inclusa nella tabella Lingua per il riconoscimento vocale. Le lingue di destinazione disponibili variano a seconda del fatto che siano parlate o scritte. È possibile tradurre la voce in ingresso in più di [60 lingue](https://www.microsoft.com/translator/business/languages/). Un subset di queste lingue è disponibile per la [sintesi vocale](language-support.md#text-languages).

### <a name="text-languages"></a>Lingue per il testo

| Lingua per il testo    | Codice lingua |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabo       | `ar`          |
| Bengali      | `bn`          |
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

* [Ottenere una sottoscrizione di prova gratuita al Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
* [Informazioni sul riconoscimento vocale in C#](quickstart-csharp-dotnet-windows.md)
