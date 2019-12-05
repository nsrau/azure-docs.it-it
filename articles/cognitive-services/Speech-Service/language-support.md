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
ms.date: 11/21/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 8db70ccbabfc165dbe3f7b9a61dbe5023a87f708
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815373"
---
# <a name="language-and-region-support-for-the-speech-service"></a>Supporto di lingue e aree per il servizio di riconoscimento vocale

Il supporto della lingua varia in base alla funzionalità del servizio vocale. Nelle tabelle seguenti viene riepilogato il supporto delle lingue per le offerte del servizio [riconoscimento](#speech-to-text) [vocale, sintesi vocale](#text-to-speech)e [traduzione vocale](#speech-translation) .

## <a name="speech-to-text"></a>Riconoscimento vocale

Sia Microsoft Speech SDK che l'API REST supportano le seguenti lingue (impostazioni locali). Per migliorare l'accuratezza, la personalizzazione viene offerta per un subset di lingue tramite il caricamento di trascrizioni audio e con etichetta umana o testo correlato: frasi.  La personalizzazione della pronuncia è attualmente disponibile solo per `en-US` e `de-DE`. Altre informazioni sulla personalizzazione sono disponibili [qui](how-to-custom-speech.md).

 Impostazioni locali | Linguaggio | Supportato | Personalizzabile
------|------------|-----------|-------------
`ar-EG` | Arabo (Egitto), standard moderno | SÌ | SÌ
`ar-SA` | Arabo (Arabia Saudita) | SÌ | SÌ
`ar-AE` | Arabo (UAE) | SÌ | SÌ
`ar-KW` | Arabo (Kuwait) | SÌ | SÌ
`ar-QA` | Arabo (Qatar) | SÌ | SÌ
`ca-ES` | Catalano | SÌ | No
`da-DK` | Danese (Danimarca) | SÌ | No
`de-DE` | Tedesco (Germania) | SÌ | SÌ
`en-AU` | Inglese (Australia) | SÌ | SÌ
`en-CA` | Inglese (Canada) | SÌ | SÌ
`en-GB` | Inglese (Regno Unito) | SÌ | SÌ
`en-IN` | English (India) | SÌ | SÌ
`en-NZ` | Inglese (Nuova Zelanda) | SÌ | SÌ
`en-US` | Inglese (Stati Uniti) | SÌ | SÌ
`es-ES` | Spagnolo (Spagna) | SÌ | SÌ
`es-MX` | Spagnolo (Messico) | SÌ | SÌ
`fi-FI` | Finlandese (Finlandia) | SÌ | No
`fr-CA` | Francese (Canada) | SÌ | SÌ
`fr-FR` | Francese (Francia) | SÌ | SÌ
`gu-IN` | Gujarati (indiano) | SÌ | SÌ
`hi-IN` | Hindi (India) | SÌ | SÌ
`it-IT` | Italiano (Italia) | SÌ | SÌ
`ja-JP` | Giapponese (Giappone) | SÌ | SÌ
`ko-KR` | Coreano (Corea) | SÌ | SÌ
`mr-IN` | Marathi (India) | SÌ | SÌ
`nb-NO` | Norvegese (Bokmål) (Norvegia) | SÌ | No
`nl-NL` | Olandese (Paesi Bassi) | SÌ | SÌ
`pl-PL` | Polacco (Polonia) | SÌ | No
`pt-BR` | Portoghese (Brasile) | SÌ | SÌ
`pt-PT` | Portoghese (Portogallo) | SÌ | SÌ
`ru-RU` | Russo (Russia) | SÌ | SÌ
`sv-SE` | Svedese (Svezia) | SÌ | No
`ta-IN` | Tamil (India) | SÌ | SÌ
`te-IN` | Telugu (India) | SÌ | SÌ
`zh-CN` | Cinese (mandarino, semplificato) | SÌ | SÌ
`zh-HK` | Cinese (cantonese, tradizionale) | SÌ | SÌ
`zh-TW` | Cinese (mandarino taiwanese) | SÌ | SÌ
`th-TH` | Thailandese (Thailandia) | SÌ | No
`tr-TR` | Turchia | SÌ | SÌ

## <a name="text-to-speech"></a>Sintesi vocale

Sia Microsoft Speech SDK che le API REST supportano queste voci, ognuna delle quali supporta una lingua e un dialetto specifici, identificati dalle impostazioni locali.

> [!IMPORTANT]
> I prezzi variano per voci standard, personalizzate e neurali. Per informazioni aggiuntive, visitare la pagina [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="neural-voices"></a>Voci neurali

La sintesi vocale neurale è un nuovo tipo di sintesi vocale basata su reti neurali profonde. Quando si usa una voce neurale, è praticamente impossibile distinguere la sintesi vocale dalle registrazioni umane.

Le voci neurali possono essere usate per rendere più naturali e accattivanti le interazioni con chatbot e gli assistenti vocali, convertire i testi digitali, ad esempio gli e-book in Audiolibri e migliorare i sistemi di navigazione in auto. Con la prosodia naturale simile al linguaggio umano e l'articolazione chiara delle parole, le voci neurali riducono in modo significativo le difficoltà di ascolto durante l'interazione degli utenti con i sistemi di intelligenza artificiale.

Per un elenco completo delle voci neurali con informazioni sulla disponibilità a livello di area, vedere [aree](regions.md#standard-and-neural-voices).

Impostazioni locali | Linguaggio | Genere | Mapping completo del nome del servizio | Nome breve vocale
--------|----------|--------|---------|------------
`de-DE` | Tedesco (Germania) | Femminile | "Voce Sintesi vocale vocale di Microsoft Server (de-DE, KatjaNeural)" | "de-DE-KatjaNeural"
`en-US` | English (US) | Maschile | "Microsoft Server Speech Text to Speech Voice (en-US, GuyNeural)" | "en-US-GuyNeural"
`en-US` | English (US) | Femminile | "Microsoft Server Speech Text to Speech Voice (en-US, JessaNeural)" | "en-US-JessaNeural"
`it-IT` | Italiano (Italia) | Femminile |"Voce Sintesi vocale vocale di Microsoft Server (it-IT, ElsaNeural)" | "it-IT-ElsaNeural"
`zh-CN` | Cinese (Terraferma) | Femminile | "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> È possibile usare il mapping del nome completo del servizio o il nome della voce breve nelle richieste di sintesi vocale.

### <a name="standard-voices"></a>Voci standard

Sono disponibili più di 75 voci standard in oltre 45 lingue e impostazioni locali, che consentono di convertire il testo in contenuto vocale sintetizzato. Per informazioni sulla disponibilità a livello di area, vedere [Aree](regions.md#standard-and-neural-voices).

Impostazioni locali | Linguaggio | Genere | Mapping completo del nome del servizio | Nome breve
-------|----------|---------|----------|----------
<sup>&dagger;</sup>`ar-EG` | Arabo (Egitto) | Femminile | "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)" | "ar-EG-un".
`ar-SA` | Arabo (Arabia Saudita) | Maschile | "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)" | "ar-SA-Naayf"
`bg-BG` | Bulgaro | Maschile | "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)" | "BG-BG-Ivan"
`ca-ES` | Catalano (Spagna) | Femminile | "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)" | "ca-ES-HerenaRUS"
`cs-CZ` | Ceco | Maschile | "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)" | "CS-CZ-Jakub"
`da-DK` | Danese | Femminile | "Microsoft Server Speech Text to Speech Voice (da-DK, HelleRUS)" | "da-DK-HelleRUS"
`de-AT` | Tedesco (Austria) | Maschile | "Microsoft Server Speech Text to Speech Voice (de-AT, Michael)" | "de-AT-Michael"
`de-CH` | Tedesco (Svizzera) | Maschile | "Microsoft Server Speech Text to Speech Voice (de-CH, Karsten)" | "de-CH-Karsten"
`de-DE` | Tedesco (Germania) | Femminile | "Microsoft Server Speech Text to Speech Voice (de-DE, Hedda)" | "de-DE-Hedda"
| | | Femminile | "Microsoft Server Speech Text to Speech Voice (de-DE, HeddaRUS)" | "de-DE-HeddaRUS"
| | | Maschile | "Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo"
`el-GR` | Greco | Maschile | "Microsoft Server Speech Text to Speech Voice (el-GR, Stefanos)" | "El-GR-Stefanos"
`en-AU` | Inglese (Australia) | Femminile | "Microsoft Server Speech Text to Speech Voice (en-AU, Catherine)" | "en-AU-Catherine"
| | | Femminile | "Microsoft Server Speech Text to Speech Voice (en-AU, HayleyRUS)" | "en-AU-HayleyRUS"
`en-CA` | Inglese (Canada) | Femminile | "Microsoft Server Speech Text to Speech Voice (en-CA, Linda)" | "en-CA-Linda"
| | | Femminile | "Microsoft Server Speech Text to Speech Voice (en-CA, HeatherRUS)" | "en-CA-HeatherRUS"
`en-GB` | English (UK) | Femminile | "Microsoft Server Speech Text to Speech Voice (en-GB, Susan, Apollo)" | "en-GB-Susan-Apollo"
| | | Femminile | "Microsoft Server Speech Text to Speech Voice (en-GB, HazelRUS)" | "en-GB-HazelRUS"
| | | Maschile | "Microsoft Server Speech Text to Speech Voice (en-GB, George, Apollo)" | "en-GB-George-Apollo"
`en-IE` | Inglese (Irlanda) | Maschile | "Microsoft Server Speech Text to Speech Voice (en-IE, Sean)" | "en-IE-Sean"
`en-IN` | English (India) | Femminile | "Microsoft Server Speech Text to Speech Voice (en-IN, Heera, Apollo)" | "en-IN-si-Apollo"
| | | Femminile | "Microsoft Server Speech Text to Speech Voice (en-IN, PriyaRUS)" | "en-IN-PriyaRUS"
| | | Maschile | "Microsoft Server Speech Text to Speech Voice (en-IN, Ravi, Apollo)" | "en-IN-Ravi-Apollo"
`en-US` | English (US) | Femminile | "Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)" | "en-US-ZiraRUS"
| | | Femminile | "Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)" | "en-US-JessaRUS"
| | | Maschile | "Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)" | "en-US-BenjaminRUS"
| | | Femminile | "Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)" | "en-US-Jessa24kRUS"
| | | Maschile | "Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)" | "en-US-Guy24kRUS"
`es-ES` | Spagnolo (Spagna) |Femminile | "Microsoft Server Speech Text to Speech Voice (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo"
| | | Femminile | "Microsoft Server Speech Text to Speech Voice (es-ES, HelenaRUS)" | "es-ES-HelenaRUS"
| | | Maschile | "Microsoft Server Speech Text to Speech Voice (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo"
`es-MX` | Spagnolo (Messico) | Femminile | "Microsoft Server Speech Text to Speech Voice (es-MX, HildaRUS)" | "es-MX-HildaRUS"
| | | Maschile | "Microsoft Server Speech Text to Speech Voice (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo"
`fi-FI` | Finlandese | Femminile | "Microsoft Server Speech Text to Speech Voice (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS"
`fr-CA` | Francese (Canada) |Femminile | "Microsoft Server Speech Text to Speech Voice (fr-CA, Caroline)" | "fr-CA-Caroline"
| | | Femminile | "Microsoft Server Speech Text to Speech Voice (fr-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS"
`fr-CH` | Francese (Svizzera)| Maschile | "Microsoft Server Speech Text to Speech Voice (fr-CH, Guillaume)" | "fr-CH-Guillaume"
`fr-FR` | Francese (Francia)| Femminile | "Microsoft Server Speech Text to Speech Voice (fr-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo"
| | | Femminile | "Microsoft Server Speech Text to Speech Voice (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS"
| | | Maschile | "Microsoft Server Speech Text to Speech Voice (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo"
`he-IL` | Ebraico (Israele) | Maschile| "Microsoft Server Speech Text to Speech Voice (he-IL, Asaf)" | "he-IL-la
`hi-IN` | Hindi (India) | Femminile | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana, Apollo)" | "hi-IN-Kalpana-Apollo"
| | | Femminile | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana)" | "hi-IN-Kalpana"
| | | Maschile | "Microsoft Server Speech Text to Speech Voice (hi-IN, Hemant)" | "Hi-IN-,"
`hr-HR` | Croato | Maschile | "Microsoft Server Speech Text to Speech Voice (hr-HR, Matej)" | "HR-HR-Matej"
`hu-HU` | Ungherese | Maschile | "Microsoft Server Speech Text to Speech Voice (hu-HU, Szabolcs)" | "hu-HU-se"
`id-ID` | Indonesiano| Maschile | "Microsoft Server Speech Text to Speech Voice (id-ID, Andika)" | "ID-ID-Andika"
`it-IT` | Italiano | Maschile | "Microsoft Server Speech Text to Speech Voice (it-IT, Cosimo, Apollo)" | "it-IT-Cosimo-Apollo"
| | | Femminile | "Microsoft Server Speech Text to Speech Voice (it-IT, LuciaRUS)" | "it-IT-LuciaRUS"
`ja-JP` | Giapponese | Femminile | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo"
| | | Maschile | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo"
| | | Femminile | "Microsoft Server Speech Text to Speech Voice (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS"
`ko-KR` | Coreano | Femminile | "Microsoft Server Speech Text to Speech Voice (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS"
`ms-MY` | Malese | Maschile | "Microsoft Server Speech Text to Speech Voice (ms-MY, Rizwan)" | "MS-MY-per"
`nb-NO` | Norvegese | Femminile | "Microsoft Server Speech Text to Speech Voice (nb-NO, HuldaRUS)" | "nb-NO-HuldaRUS"
`nl-NL` | Olandese | Femminile | "Microsoft Server Speech Text to Speech Voice (nl-NL, HannaRUS)" | "nl-NL-HannaRUS"
`pl-PL` | Polacco | Femminile | "Microsoft Server Speech Text to Speech Voice (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS"
`pt-BR` | Portoghese (Brasile) | Femminile | "Microsoft Server Speech Text to Speech Voice (pt-BR, HeloisaRUS)" | "PT-BR-HeloisaRUS"
| | | Maschile |"Microsoft Server Speech Text to Speech Voice (pt-BR, Daniel, Apollo)" | "PT-BR-Daniel-Apollo"
`pt-PT` | Portoghese (Portogallo) | Femminile | "Microsoft Server Speech Text to Speech Voice (pt-PT, HeliaRUS)" | "PT-PT-HeliaRUS"
`ro-RO` | Rumeno | Maschile | "Microsoft Server Speech Text to Speech Voice (ro-RO, Andrei)" | "ro-RO-Andrei"
`ru-RU` |Russo| Femminile | "Microsoft Server Speech Text to Speech Voice (ru-RU, Irina, Apollo)" | "ur-ur-Irina-Apollo"
| | | Maschile | "Microsoft Server Speech Text to Speech Voice (ru-RU, Pavel, Apollo)" | "ur-ur-Pavel-Apollo"
| | | Femminile | "Microsoft Server Speech Text to Speech Voice (ru-RU, EkaterinaRUS)" | ur-ur-EkaterinaRUS
`sk-SK` | Slovacco | Maschile | "Microsoft Server Speech Text to Speech Voice (sk-SK, Filip)" | "sk-SK-Filip"
`sl-SI` | Sloveno | Maschile | "Microsoft Server Speech Text to Speech Voice (sl-SI, Lado)" | "sl-SI-Lado"
`sv-SE` | Svedese | Femminile | "Microsoft Server Speech Text to Speech Voice (sv-SE, HedvigRUS)" | "sv-SE-HedvigRUS"
`ta-IN` | Tamil (India) | Maschile | "Microsoft Server Speech Text to Speech Voice (ta-IN, Valluvar)" | "TA-IN-Valluvar"
`te-IN` | Telugu (India) | Femminile | "Microsoft Server Speech Text to Speech Voice (te-IN, Chitra)" | "te-IN-Chitle"
`th-TH` | Thai | Maschile | "Microsoft Server Speech Text to Speech Voice (th-TH, Pattara)" | "th-TH-Pattara"
`tr-TR` | Turco | Femminile | "Microsoft Server Speech Text to Speech Voice (tr-TR, SedaRUS)" | "tr-TR-SedaRUS"
`vi-VN` | Vietnamita | Maschile | "Microsoft Server Speech Text to Speech Voice (vi-VN, An)" | "vi-VN-An"
`zh-CN` | Cinese (Terraferma) | Femminile | "Microsoft Server Speech Text to Speech Voice (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS"
| | | Femminile | "Microsoft Server Speech Text to Speech Voice (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo"
| | | Maschile | "Microsoft Server Speech Text to Speech Voice (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo"
`zh-HK` | Cinese (Hong Kong) | Femminile | "Microsoft Server Speech Text to Speech Voice (zh-HK, Tracy, Apollo)" | "zh-HK-Tracy-Apollo"
| | | Femminile | "Microsoft Server Speech Text to Speech Voice (zh-HK, TracyRUS)" | "zh-HK-TracyRUS"
| | | Maschile | "Microsoft Server Speech Text to Speech Voice (zh-HK, Danny, Apollo)" | "zh-HK-Danny-Apollo"
`zh-TW` | Cinese (Taiwan) | Femminile | "Microsoft Server Speech Text to Speech Voice (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo"
| | | Femminile | "Microsoft Server Speech Text to Speech Voice (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS"
| | | Maschile | "Microsoft Server Speech Text to Speech Voice (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo"

&dagger; *ar-EG supporta lo standard moderno della lingua araba.*

> [!NOTE]
> È possibile usare il mapping del nome completo del servizio o il nome della voce breve nelle richieste di sintesi vocale.

### <a name="customization"></a>Personalizzazione

La personalizzazione vocale è disponibile per `de-DE`, `en-GB`, `en-IN`, `en-US`, `es-MX`, `fr-FR`, `it-IT`, `pt-BR`e `zh-CN`. Selezionare le impostazioni locali corrette che corrispondono ai dati di training per il training di un modello vocale personalizzato. Se, ad esempio, i dati di registrazione sono pronunciati in inglese con un accento britannico, selezionare `en-GB`.

> [!NOTE]
> Non è supportato il training del modello bilingue in una voce personalizzata, ad eccezione della lingua inglese cinese (BI). Per eseguire il training di una voce cinese in lingua inglese, selezionare "lingua inglese per il cinese". Il training vocale in tutte le impostazioni locali inizia con un set di dati di 2000 + espressioni, ad eccezione del `en-US` e `zh-CN` in cui è possibile iniziare con qualsiasi dimensione dei dati di training.

## <a name="speech-translation"></a>Traduzione vocale

L'API **Traduzione vocale** supporta lingue diverse per la traduzione vocale e con riconoscimento vocale. La lingua di origine deve essere sempre presente nella tabella della lingua di riconoscimento vocale. Le lingue di destinazione disponibili variano a seconda del fatto che siano parlate o scritte. È possibile tradurre la voce in ingresso in più di [60 lingue](https://www.microsoft.com/translator/business/languages/). Per la [sintesi vocale](language-support.md#text-languages)è disponibile un subset di linguaggi.

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
| Italiano      | `en`          |
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
| Telugu      | `te`          |
| Thai      | `th`          |
| Tongano      | `to`          |
| Turco      | `tr`          |
| Ucraino      | `uk`          |
| Urdu      | `ur`          |
| Vietnamita      | `vi`          |
| Gallese      | `cy`          |
| Yucatec Maya      | `yua`          |


## <a name="next-steps"></a>Passaggi successivi

* [Ricevi la tua sottoscrizione di valutazione del servizio vocale](https://azure.microsoft.com/try/cognitive-services/)
* [Informazioni sul riconoscimento vocale in C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
