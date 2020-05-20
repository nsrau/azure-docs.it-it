---
title: Supporto linguistico-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Il servizio di riconoscimento vocale supporta numerose lingue per la conversione di sintesi vocale e sintesi vocale, oltre alla traduzione vocale. Questo articolo fornisce un elenco completo del supporto linguistico per funzionalità del servizio.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: trbye
ms.custom: seodec18
ms.openlocfilehash: 28c5c1258c0733cd23f7c4ad3c901360461d5bdc
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682148"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Supporto della lingua e della voce per il servizio di riconoscimento vocale

Il supporto della lingua varia in base alla funzionalità del servizio vocale. Nelle tabelle seguenti viene riepilogato il supporto delle lingue per le offerte del servizio [riconoscimento](#speech-to-text) [vocale, sintesi vocale](#text-to-speech)e [traduzione vocale](#speech-translation) .

## <a name="speech-to-text"></a>Riconoscimento vocale

Sia Microsoft Speech SDK che l'API REST supportano le seguenti lingue (impostazioni locali). Per migliorare l'accuratezza, la personalizzazione viene offerta per un subset di lingue tramite il caricamento di trascrizioni audio e con etichetta umana o testo correlato: frasi. La personalizzazione della pronuncia viene offerta tramite il caricamento del testo correlato: pronuncia. Altre informazioni sulla personalizzazione sono disponibili [qui](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Impostazioni locali  | Linguaggio                          | Supportato | Personalizzazioni                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Arabo (UAE)                      | Sì       | No                                                |
| `ar-BH` | Arabo (Bahrain), standard moderno | Sì       | Modello linguistico                                    |
| `ar-EG` | Arabo (Egitto)                    | Sì       | Modello linguistico                                    |
| `ar-IL` | Arabo (Israele)                   | Sì       | No                                                |
| `ar-KW` | Arabo (Kuwait)                   | Sì       | No                                                |
| `ar-PS` | Arabo (Palestina)                | Sì       | No                                                |
| `ar-QA` | Arabo (Qatar)                    | Sì       | No                                                |
| `ar-SA` | Arabo (Arabia Saudita)             | Sì       | No                                                |
| `ar-SY` | Arabo (Siria)                    | Sì       | Modello linguistico                                    |
| `ca-ES` | Catalano                           | Sì       | Modello linguistico                                    |
| `da-DK` | Danese (Danimarca)                  | Sì       | Modello linguistico                                    |
| `de-DE` | Tedesco (Germania)                  | Sì       | Modello acustico<br>Modello linguistico<br>Pronuncia |
| `en-AU` | Inglese (Australia)               | Sì       | Modello acustico<br>Modello linguistico                  |
| `en-CA` | Inglese (Canada)                  | Sì       | Modello acustico<br>Modello linguistico                  |
| `en-GB` | Inglese (Regno Unito)          | Sì       | Modello acustico<br>Modello linguistico<br>Pronuncia |
| `en-IN` | Inglese (India)                   | Sì       | Modello acustico<br>Modello linguistico                  |
| `en-NZ` | Inglese (Nuova Zelanda)             | Sì       | Modello acustico<br>Modello linguistico                  |
| `en-US` | Inglese (Stati Uniti)           | Sì       | Modello acustico<br>Modello linguistico<br>Pronuncia |
| `es-ES` | Spagnolo (Spagna)                   | Sì       | Modello acustico<br>Modello linguistico                  |
| `es-MX` | Spagnolo (Messico)                  | Sì       | Modello acustico<br>Modello linguistico                  |
| `fi-FI` | Finlandese (Finlandia)                 | Sì       | Modello linguistico                                    |
| `fr-CA` | Francese (Canada)                   | Sì       | Modello acustico<br>Modello linguistico                  |
| `fr-FR` | Francese (Francia)                   | Sì       | Modello acustico<br>Modello linguistico<br>Pronuncia |
| `gu-IN` | Gujarati (indiano)                 | Sì       | Modello linguistico                                    |
| `hi-IN` | Hindi (India)                     | Sì       | Modello acustico<br>Modello linguistico                  |
| `it-IT` | Italiano (Italia)                   | Sì       | Modello acustico<br>Modello linguistico<br>Pronuncia |
| `ja-JP` | Giapponese (Giappone)                  | Sì       | Modello linguistico                                    |
| `ko-KR` | Coreano (Corea)                    | Sì       | Modello linguistico                                    |
| `mr-IN` | Marathi (India)                   | Sì       | Modello linguistico                                    |
| `nb-NO` | Norvegese (Bokmål) (Norvegia)       | Sì       | Modello linguistico                                    |
| `nl-NL` | Olandese (Paesi Bassi)               | Sì       | Modello linguistico                                    |
| `pl-PL` | Polacco (Polonia)                   | Sì       | Modello linguistico                                    |
| `pt-BR` | Portoghese (Brasile)               | Sì       | Modello acustico<br>Modello linguistico<br>Pronuncia |
| `pt-PT` | Portoghese (Portogallo)             | Sì       | Modello linguistico                                    |
| `ru-RU` | Russo (Russia)                  | Sì       | Modello acustico<br>Modello linguistico                  |
| `sv-SE` | Svedese (Svezia)                  | Sì       | Modello linguistico                                    |
| `ta-IN` | Tamil (India)                     | Sì       | Modello linguistico                                    |
| `te-IN` | Telugu (India)                    | Sì       | No                                                |
| `th-TH` | Thai (Thailandia)                   | Sì       | No                                                |
| `tr-TR` | Turco (Turchia)                  | Sì       | No                                                |
| `zh-CN` | Cinese (mandarino, semplificato)    | Sì       | Modello acustico<br>Modello linguistico                  |
| `zh-HK` | Cinese (cantonese, tradizionale)  | Sì       | Modello linguistico                                    |
| `zh-TW` | Cinese (mandarino taiwanese)      | Sì       | Modello linguistico                                    |

## <a name="text-to-speech"></a>Sintesi vocale

Sia Microsoft Speech SDK che le API REST supportano queste voci, ognuna delle quali supporta una lingua e un dialetto specifici, identificati dalle impostazioni locali. È anche possibile ottenere un elenco completo di lingue e voci supportate per ogni area/endpoint specifico tramite l' [API Voices/list](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> I prezzi variano per voci standard, personalizzate e neurali. Per informazioni aggiuntive, visitare la pagina [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="neural-voices"></a>Voci neurali

La sintesi vocale neurale è un nuovo tipo di sintesi vocale basata su reti neurali profonde. Quando si usa una voce neurale, è praticamente impossibile distinguere la sintesi vocale dalle registrazioni umane.

Le voci neurali possono essere usate per rendere più naturali e accattivanti le interazioni con chatbot e gli assistenti vocali, convertire i testi digitali, ad esempio gli e-book in Audiolibri e migliorare i sistemi di navigazione in auto. Con la prosodia naturale simile al linguaggio umano e l'articolazione chiara delle parole, le voci neurali riducono in modo significativo le difficoltà di ascolto durante l'interazione degli utenti con i sistemi di intelligenza artificiale.

Per informazioni sulla disponibilità a livello di area, vedere [Aree](regions.md#standard-and-neural-voices).

|Impostazioni locali  | Linguaggio            | Sesso | Nome voce  | Supporto bi-lingue | Supporto dello stile |
|--|--|--|--|--|--|
| `de-DE` | Tedesco (Germania)    | Female | "de-DE-KatjaNeural" | Sì. Inglese (Stati Uniti) | Generale |
| `en-AU` | Inglese (Australia) | Female |  "en-AU-NatashaNeural" | No | Generale |
| `en-CA` | Inglese (Canada)    | Female |  "en-CA-ClaraNeural"| No | Generale |
| `en-GB` | Inglese (Regno Unito)        | Female |  "en-GB-LibbyNeural"| No | Generale |
|  |      | Female |  "en-GB-MiaNeural" | No | Generale |
| `en-US` | Inglese (Stati Uniti)        | Female |  "en-US-AriaNeural"| No | Generale, più stili vocali disponibili |
|  |      | Male   | "en-US-GuyNeural" | No | Generale |
| `es-ES` | Spagnolo (Spagna)     | Female |  "es-ES-ElviraNeural"| Sì. Inglese (Stati Uniti) | Generale |
| `es-MX` | Spagnolo (Messico)    | Female |  "es-MX-DaliaNeural" | Sì. Inglese (Stati Uniti) | Generale |
| `fr-CA` | Francese (Canada)     | Female |  "fr-CA-SylvieNeural" | Sì. Inglese (Stati Uniti) | Generale |
| `fr-FR` | Francese (Francia)     | Female | "fr-FR-DeniseNeural"  | Sì. Inglese (Stati Uniti) | Generale |
| `it-IT` | Italiano (Italia)     | Female |  "it-IT-ElsaNeural"  | Sì. Inglese (Stati Uniti) | Generale |
| `ja-JP` | Giapponese            | Female |  "ja-JP-NanamiNeural" | Sì. Inglese (Stati Uniti) | Generale |
| `ko-KR` | Coreano              | Female |  "ko-KR-SunHiNeural" | Sì. Inglese (Stati Uniti) | Generale |
| `nb-NO` | Norvegese           | Female | "nb-NO-IselinNeural" | No | Generale |
| `pt-BR` | Portoghese (Brasile) | Female |  "PT-BR-FranciscaNeural" | No | Generale |
| `tr-TR` | Turco             | Female | "tr-TR-EmelNeural" | No | Generale |
| `zh-CN` | Cinese (mandarino, semplificato)  | Female |  "zh-CN-XiaoxiaoNeural"  | Sì. Inglese (Stati Uniti) | Generale, più stili vocali disponibili |
|  |  | Female |  "zh-CN-XiaoyouNeural"  | Sì. Inglese (Stati Uniti) | Kid Voice, ottimizzato per la narrazione di storie |
|  |  | Male |  "zh-CN-YunyangNeural"  | Sì. Inglese (Stati Uniti) | Ottimizzato per la lettura di notizie, più stili vocali disponibili |
|  |  | Male |  "zh-CN-YunyeNeural"  | No | Ottimizzato per la narrazione di storie |

> [!IMPORTANT]
> La `en-US-JessaNeural` voce è stata modificata in `en-US-AriaNeural` . Se si usa "Jessa" prima, convertirlo in "aria".

Per informazioni su come configurare e modificare le voci neurali, vedere [linguaggio di markup sintesi vocale](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> È possibile continuare a usare il mapping del nome completo del servizio, ad esempio "Microsoft Server Speech Sintesi vocale Voice (en-US, AriaNeural)" nelle richieste di sintesi vocale.

### <a name="standard-voices"></a>Voci standard

Sono disponibili più di 75 voci standard in oltre 45 lingue e impostazioni locali, che consentono di convertire il testo in contenuto vocale sintetizzato. Per informazioni sulla disponibilità a livello di area, vedere [Aree](regions.md#standard-and-neural-voices).

| Impostazioni locali | Linguaggio | Sesso | Nome voce |
|--|--|--|--|
| <sup>1</sup>`ar-EG` | Arabo (Egitto) | Female | "ar-EG-un". |
| `ar-SA` | Arabo (Arabia Saudita) | Male | "ar-SA-Naayf" |
| `bg-BG` | Bulgaro | Male |  "BG-BG-Ivan" |
| `ca-ES` | Catalano (Spagna) | Female |  "ca-ES-HerenaRUS" |
| `cs-CZ` | Ceco | Male | "CS-CZ-Jakub" |
| `da-DK` | Danese | Female |  "da-DK-HelleRus" |
| `de-AT` | Tedesco (Austria) | Male | "de-AT-Michael" |
| `de-CH` | Tedesco (Svizzera) | Male |  "de-CH-Karsten" |
| `de-DE` | Tedesco (Germania) | Female |  "de-DE-Hedda" |
|  |  | Female | "de-DE-HeddaRUS" |
|  |  | Male |  "de-DE-Stefan-Apollo" |
| `el-GR` | Greco | Male | "El-GR-Stefanos" |
| `en-AU` | Inglese (Australia) | Female |  "en-AU-Catherine" |
|  |  | Female |  "en-AU-HayleyRUS" |
| `en-CA` | Inglese (Canada) | Female |  "en-CA-Linda" |
|  |  | Female |  "en-CA-HeatherRUS" |
| `en-GB` | Inglese (Regno Unito) | Female |  "en-GB-Susan-Apollo" |
|  |  | Female |  "en-GB-HazelRUS" |
|  |  | Male |  "en-GB-George-Apollo" |
| `en-IE` | Inglese (Irlanda) | Male | "en-IE-Sean" |
| `en-IN` | Inglese (India) | Female | "en-IN-si-Apollo" |
|  |  | Female |  "en-IN-PriyaRUS" |
|  |  | Male |  "en-IN-Ravi-Apollo" |
| `en-US` | Inglese (Stati Uniti) | Female |  "en-US-ZiraRUS" |
|  |  | Female | "en-US-AriaRUS" |
|  |  | Male | "en-US-BenjaminRUS" |
|  |  | Male |  "en-US-Guy24kRUS" |
| `es-ES` | Spagnolo (Spagna) | Female |  "es-ES-Laura-Apollo" |
|  |  | Female | "es-ES-HelenaRUS" |
|  |  | Male | "es-ES-Pablo-Apollo" |
| `es-MX` | Spagnolo (Messico) | Female |  "es-MX-HildaRUS" |
|  |  | Male | "es-MX-Raul-Apollo" |
| `fi-FI` | Finlandese | Female | "fi-FI-HeidiRUS" |
| `fr-CA` | Francese (Canada) | Female | "fr-CA-Caroline" |
|  |  | Female | "fr-CA-HarmonieRUS" |
| `fr-CH` | Francese (Svizzera) | Male | "fr-CH-Guillaume" |
| `fr-FR` | Francese (Francia) | Female |  "fr-FR-Julie-Apollo" |
|  |  | Female |"fr-FR-HortenseRUS" |
|  |  | Male |  "fr-FR-Paul-Apollo" |
| `he-IL` | Ebraico (Israele) | Male |  "he-IL-la |
| `hi-IN` | Hindi (India) | Female | "Hi-IN-non-Apollo" |
|  |  | Female |  "Hi-IN-le" |
|  |  | Male |  "Hi-IN-," |
| `hr-HR` | Croato | Male | "HR-HR-Matej" |
| `hu-HU` | Ungherese | Male |  "hu-HU-se" |
| `id-ID` | Indonesiano | Male | "ID-ID-Andika" |
| `it-IT` | Italiano | Male |  "it-IT-Cosimo-Apollo" |
|  |  | Female |  "it-IT-LuciaRUS" |
| `ja-JP` | Giapponese | Female |  "ja-JP-Ayumi-Apollo" |
|  |  | Male | "ja-JP-Ichiro-Apollo" |
|  |  | Female |  "ja-JP-HarukaRUS" |
| `ko-KR` | Coreano | Female | "ko-KR-HeamiRUS" |
| `ms-MY` | Malese | Male |  "MS-MY-per" |
| `nb-NO` | Norvegese | Female |  "nb-NO-HuldaRUS" |
| `nl-NL` | Olandese | Female |  "nl-NL-HannaRUS" |
| `pl-PL` | Polacco | Female |  "pl-PL-PaulinaRUS" |
| `pt-BR` | Portoghese (Brasile) | Female | "PT-BR-HeloisaRUS" |
|  |  | Male |  "PT-BR-Daniel-Apollo" |
| `pt-PT` | Portoghese (Portogallo) | Female | "PT-PT-HeliaRUS" |
| `ro-RO` | Romeno | Male | "ro-RO-Andrei" |
| `ru-RU` | Russo | Female |  "ur-ur-Irina-Apollo" |
|  |  | Male | "ur-ur-Pavel-Apollo" |
|  |  | Female |  ur-ur-EkaterinaRUS |
| `sk-SK` | Slovacco | Male | "sk-SK-Filip" |
| `sl-SI` | Sloveno | Male |  "SL-SI-lado" |
| `sv-SE` | Svedese | Female | "sv-SE-HedvigRUS" |
| `ta-IN` | Tamil (India) | Male |  "TA-IN-Valluvar" |
| `te-IN` | Telugu (India) | Female |  "te-IN-Chitle" |
| `th-TH` | Thai | Male |  "th-TH-Pattara" |
| `tr-TR` | Turco (Turchia) | Female | "tr-TR-SedaRUS" |
| `vi-VN` | Vietnamita | Male |  "vi-VN-an" |
| `zh-CN` | Cinese (mandarino, semplificato) | Female |  "zh-CN-HuihuiRUS" |
|  |  | Female | "zh-CN-Yaoyao-Apollo" |
|  |  | Male | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Cinese (cantonese, tradizionale) | Female |  "zh-HK-Tracy-Apollo" |
|  |  | Female | "zh-HK-TracyRUS" |
|  |  | Male |  "zh-HK-Danny-Apollo" |
| `zh-TW` | Cinese (mandarino taiwanese) | Female |  "zh-TW-Yating-Apollo" |
|  |  | Female | "zh-TW-HanHanRUS" |
|  |  | Male |  "zh-TW-Zhiwei-Apollo" |

**1** *ar-EG supporta l'arabo standard moderno (MSA).*

> [!IMPORTANT]
> La `en-US-Jessa` voce è stata modificata in `en-US-Aria` . Se si usa "Jessa" prima, convertirlo in "aria".

> [!TIP]
> È possibile continuare a usare il mapping del nome completo del servizio, ad esempio "Microsoft Server Speech Sintesi vocale Voice (en-US, AriaRUS)" nelle richieste di sintesi vocale.

### <a name="customization"></a>Personalizzazione

La personalizzazione vocale è disponibile per `de-DE` , `en-GB` ,, `en-IN` `en-US` , `es-MX` , `fr-FR` , `it-IT` , `pt-BR` e `zh-CN` . Selezionare le impostazioni locali corrette che corrispondono ai dati di training per il training di un modello vocale personalizzato. Se, ad esempio, i dati di registrazione sono pronunciati in inglese con un accento britannico, selezionare `en-GB` .

> [!NOTE]
> Non è supportato il training del modello bilingue in una voce personalizzata, ad eccezione della lingua inglese cinese (BI). Per eseguire il training di una voce cinese in lingua inglese, selezionare "lingua inglese per il cinese". Il training vocale in tutte le impostazioni locali inizia con un set di dati di 2000 + espressioni, ad eccezione di e, in `en-US` `zh-CN` cui è possibile iniziare con qualsiasi dimensione dei dati di training.

## <a name="speech-translation"></a>Traduzione vocale

L'API **Traduzione vocale** supporta lingue diverse per la traduzione vocale e con riconoscimento vocale. La lingua di origine deve essere sempre presente nella tabella della lingua di riconoscimento vocale. Le lingue di destinazione disponibili variano a seconda del fatto che siano parlate o scritte. È possibile tradurre la voce in ingresso in più di [60 lingue](https://www.microsoft.com/translator/business/languages/). Per la [sintesi vocale](language-support.md#text-languages)è disponibile un subset di linguaggi.

### <a name="text-languages"></a>Lingue per il testo

| Lingua per il testo           | Codice lingua |
|:------------------------|:-------------:|
| Afrikaans               | `af`          |
| Arabo                  | `ar`          |
| Bengalese                  | `bn`          |
| Bosniaco (latino)         | `bs`          |
| Bulgaro               | `bg`          |
| Cantonese (tradizionale) | `yue`         |
| Catalano                 | `ca`          |
| Cinese semplificato      | `zh-Hans`     |
| Cinese tradizionale     | `zh-Hant`     |
| Croato                | `hr`          |
| Ceco                   | `cs`          |
| Danese                  | `da`          |
| Olandese                   | `nl`          |
| Inglese                 | `en`          |
| Estone                | `et`          |
| Figiano                  | `fj`          |
| Filippino                | `fil`         |
| Finlandese                 | `fi`          |
| Francese                  | `fr`          |
| Tedesco                  | `de`          |
| Greco                   | `el`          |
| Gujarati                | `gu`          |
| Creolo haitiano          | `ht`          |
| Ebraico                  | `he`          |
| Hindi                   | `hi`          |
| Hmong Daw               | `mww`         |
| Ungherese               | `hu`          |
| Indonesiano              | `id`          |
| Irlandese                   | `ga`          |
| Italiano                 | `it`          |
| Giapponese                | `ja`          |
| Kannada                 | `kn`          |
| Kiswahili               | `sw`          |
| Klingon                 | `tlh`         |
| Klingon (plqaD)         | `tlh-Qaak`    |
| Coreano                  | `ko`          |
| Lettone                 | `lv`          |
| Lituano              | `lt`          |
| Malgascio                | `mg`          |
| Malese                   | `ms`          |
| Malayalam               | `ml`          |
| Maltese                 | `mt`          |
| Maori                   | `mi`          |
| Marathi                 | `mr`          |
| Norvegese               | `nb`          |
| Persiano                 | `fa`          |
| Polacco                  | `pl`          |
| Portoghese (Brasile)     | `pt-br`       |
| Portoghese (Portogallo)   | `pt-pt`       |
| Punjabi                 | `pa`          |
| Querétaro Otomi         | `otq`         |
| Romeno                | `ro`          |
| Russo                 | `ru`          |
| Samoano                  | `sm`          |
| Serbo (alfabeto cirillico)      | `sr-Cyrl`     |
| Serbo (alfabeto latino)         | `sr-Latn`     |
| Slovacco                  | `sk`          |
| Sloveno               | `sl`          |
| Spagnolo                 | `es`          |
| Svedese                 | `sv`          |
| Tahitiano                | `ty`          |
| Tamil                   | `ta`          |
| Telugu                  | `te`          |
| Thai                    | `th`          |
| Tongano                  | `to`          |
| Turco                 | `tr`          |
| Ucraino               | `uk`          |
| Urdu                    | `ur`          |
| Vietnamita              | `vi`          |
| Gallese                   | `cy`          |
| Yucatec Maya            | `yua`         |

## <a name="next-steps"></a>Passaggi successivi

* [Ricevi la tua sottoscrizione di valutazione del servizio vocale](https://azure.microsoft.com/try/cognitive-services/)
* [Informazioni sul riconoscimento vocale in C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
