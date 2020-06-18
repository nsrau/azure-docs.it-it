---
title: Supporto per la lingua - Servizio Voce
titleSuffix: Azure Cognitive Services
description: Il servizio Voce supporta numerose lingue per la conversione della voce in testo scritto e la sintesi vocale, insieme a funzionalità di traduzione vocale. Questo articolo fornisce un elenco completo delle lingue supportate per ogni funzionalità del servizio.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: trbye
ms.custom: seodec18
ms.openlocfilehash: 3e32c7d7da918eb58de47d8fc9b7688a189cb022
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84783096"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Lingue e voci supportate per il servizio Voce

Il supporto per la lingua varia in base alla funzionalità del servizio Voce. Nelle tabelle seguenti viene riepilogato il supporto per la lingua per le offerte di servizio [Riconoscimento vocale](#speech-to-text), [Sintesi vocale](#text-to-speech) e [Traduzione vocale](#speech-translation).

## <a name="speech-to-text"></a>Riconoscimento vocale

Sia Microsoft Speech SDK che l'API REST supportano le lingue seguenti (impostazioni locali). 

Per migliorare l'accuratezza, è possibile personalizzare un subset di lingue caricando **Audio + Human-labeled Transcripts** (Trascrizioni audio o con etichette umane) oppure **Related Text: Sentences** (Testo correlato: Frasi). Per altre informazioni sulla personalizzazione, vedere [Introduzione a Riconoscimento vocale personalizzato](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Impostazioni locali  | Linguaggio                          | Supportato | Personalizzazioni                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Arabo (Emirati Arabi Uniti)                      | Sì       | No                                                |
| `ar-BH` | Arabo (Bahrein), standard moderno | Sì       | Modello linguistico                                    |
| `ar-EG` | Arabo (Egitto)                    | Sì       | Modello linguistico                                    |
| `ar-IL` | Arabo (Israele)                   | Sì       | No                                                |
| `ar-JO` | Arabo (Giordania)                   | Sì       | No                                                |
| `ar-KW` | Arabo (Kuwait)                   | Sì       | No                                                |
| `ar-LB` | Arabo (Libano)                  | Sì       | No                                                |
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
| `gu-IN` | Gujarati (India)                 | Sì       | Modello linguistico                                    |
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
| `te-IN` | Telugu (India)                    | Sì       | Modello linguistico                                    |
| `th-TH` | Thai (Thailandia)                   | Sì       | No                                                |
| `tr-TR` | Turco (Turchia)                  | Sì       | Modello linguistico                                    |
| `zh-CN` | Cinese (mandarino, semplificato)    | Sì       | Modello acustico<br>Modello linguistico                  |
| `zh-HK` | Cinese (cantonese, tradizionale)  | Sì       | Modello linguistico                                    |
| `zh-TW` | Cinese (mandarino taiwanese)      | Sì       | Modello linguistico                                    |

## <a name="text-to-speech"></a>Sintesi vocale

Sia Microsoft Speech SDK che le API REST supportano le voci seguenti, ognuna delle quali supporta una lingua e un dialetto specifici, identificate dalle impostazioni locali. È anche possibile ottenere un elenco completo di lingue e voci supportate per ogni area/endpoint specifico tramite l'API [voices/list](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> I prezzi variano per voci standard, personalizzate e neurali. Per informazioni aggiuntive, visitare la pagina [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="neural-voices"></a>Voci neurali

La sintesi vocale neurale è un nuovo tipo di sintesi vocale basata su reti neurali profonde. Quando si usa una voce neurale, è praticamente impossibile distinguere la sintesi vocale dalle registrazioni umane.

Le voci neurali possono essere usate per rendere più naturali e coinvolgenti le interazioni con chatbot e assistenti vocali, per convertire testo digitale, come gli e-book, in audiolibri e per migliorare i sistemi dei navigatori per le automobili. Con la prosodia naturale simile al linguaggio umano e l'articolazione chiara delle parole, le voci neurali riducono in modo significativo le difficoltà di ascolto durante l'interazione degli utenti con i sistemi di intelligenza artificiale.

Per informazioni sulla disponibilità a livello di area, vedere [Aree](regions.md#standard-and-neural-voices).

|Impostazioni locali  | Linguaggio            | Sesso | Nome della voce | Supporto dello stile |
|--|--|--|--|--|
| `de-DE` | Tedesco (Germania)                | Female | "de-DE-KatjaNeural"      | Generale |
| `en-AU` | Inglese (Australia)             | Female | "en-AU-NatashaNeural"    | Generale |
| `en-CA` | Inglese (Canada)                | Female | "en-CA-ClaraNeural"      | Generale |
| `en-GB` | Inglese (Regno Unito)                    | Female | "en-GB-LibbyNeural"      | Generale |
|         |                                 | Female | "en-GB-MiaNeural"        | Generale |
| `en-US` | Inglese (Stati Uniti)                    | Female | "en-US-AriaNeural"       | Generale, più stili vocali disponibili |
|         |                                 | Male   | "en-US-GuyNeural"        | Generale |
| `es-ES` | Spagnolo (Spagna)                 | Female | "es-ES-ElviraNeural"     | Generale |
| `es-MX` | Spagnolo (Messico)                | Female | "es-MX-DaliaNeural"      | Generale |
| `fr-CA` | Francese (Canada)                 | Female | "fr-CA-SylvieNeural"     | Generale |
| `fr-FR` | Francese (Francia)                 | Female | "fr-FR-DeniseNeural"     | Generale |
| `it-IT` | Italiano (Italia)                 | Female | "it-IT-ElsaNeural"       | Generale |
| `ja-JP` | Giapponese                        | Female | "ja-JP-NanamiNeural"     | Generale |
| `ko-KR` | Coreano                          | Female | "ko-KR-SunHiNeural"      | Generale |
| `nb-NO` | Norvegese                       | Female | "nb-NO-IselinNeural"     | Generale |
| `pt-BR` | Portoghese (Brasile)             | Female | "pt-BR-FranciscaNeural"  | Generale |
| `tr-TR` | Turco                         | Female | "tr-TR-EmelNeural"       | Generale |
| `zh-CN` | Cinese (mandarino, semplificato)  | Female | "zh-CN-XiaoxiaoNeural"   | Generale, più stili vocali disponibili |
|         |                                 | Female | "zh-CN-XiaoyouNeural"    | Voce infantile, ottimizzata per la narrazione di storie |
|         |                                 | Male   | "zh-CN-YunyangNeural"    | Ottimizzata per la lettura di notizie, più stili vocali disponibili |
|         |                                 | Male   | "zh-CN-YunyeNeural"      | Ottimizzata per la narrazione di storie |

> [!IMPORTANT]
> La voce `en-US-JessaNeural` è stata modificata in `en-US-AriaNeural`. Se in precedenza si usava "Jessa", convertirla in "Aria".

Per informazioni su come configurare e modificare le voci neurali, vedere [Speech Synthesis Markup Language](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> Nelle richieste di sintesi vocale è possibile continuare a usare il mapping del nome completo del servizio, ad esempio "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)".

### <a name="standard-voices"></a>Voci standard

Sono disponibili più di 75 voci standard in oltre 45 lingue e impostazioni locali, che consentono di convertire il testo in contenuto vocale sintetizzato. Per informazioni sulla disponibilità a livello di area, vedere [Aree](regions.md#standard-and-neural-voices).

| Impostazioni locali | Linguaggio | Sesso | Nome della voce |
|--|--|--|--|
| <sup>1</sup>`ar-EG` | Arabo (Egitto) | Female | "ar-EG-Hoda" |
| `ar-SA` | Arabo (Arabia Saudita) | Male | "ar-SA-Naayf" |
| `bg-BG` | Bulgaro | Male |  "bg-BG-Ivan" |
| `ca-ES` | Catalano | Female |  "ca-ES-HerenaRUS" |
| `cs-CZ` | Ceco | Male | "cs-CZ-Jakub" |
| `da-DK` | Danese | Female |  "da-DK-HelleRUS" |
| `de-AT` | Tedesco (Austria) | Male | "de-AT-Michael" |
| `de-CH` | Tedesco (Svizzera) | Male |  "de-CH-Karsten" |
| `de-DE` | Tedesco (Germania) | Female |  "de-DE-Hedda" |
|  |  | Female | "de-DE-HeddaRUS" |
|  |  | Male |  "de-DE-Stefan-Apollo" |
| `el-GR` | Greco | Male | "el-GR-Stefanos" |
| `en-AU` | Inglese (Australia) | Female |  "en-AU-Catherine" |
|  |  | Female |  "en-AU-HayleyRUS" |
| `en-CA` | Inglese (Canada) | Female |  "en-CA-Linda" |
|  |  | Female |  "en-CA-HeatherRUS" |
| `en-GB` | Inglese (Regno Unito) | Female |  "en-GB-Susan-Apollo" |
|  |  | Female |  "en-GB-HazelRUS" |
|  |  | Male |  "en-GB-George-Apollo" |
| `en-IE` | Inglese (Irlanda) | Male | "en-IE-Sean" |
| `en-IN` | Inglese (India) | Female | "en-IN-Heera-Apollo" |
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
| `he-IL` | Ebraico (Israele) | Male |  "he-IL-Asaf" |
| `hi-IN` | Hindi (India) | Female | "hi-IN-Kalpana-Apollo" |
|  |  | Female |  "hi-IN-Kalpana" |
|  |  | Male |  "hi-IN-Hemant" |
| `hr-HR` | Croato | Male | "hr-HR-Matej" |
| `hu-HU` | Ungherese | Male |  "hu-HU-Szabolcs" |
| `id-ID` | Indonesiano | Male | "id-ID-Andika" |
| `it-IT` | Italiano | Male |  "it-IT-Cosimo-Apollo" |
|  |  | Female |  "it-IT-LuciaRUS" |
| `ja-JP` | Giapponese | Female |  "ja-JP-Ayumi-Apollo" |
|  |  | Male | "ja-JP-Ichiro-Apollo" |
|  |  | Female |  "ja-JP-HarukaRUS" |
| `ko-KR` | Coreano | Female | "ko-KR-HeamiRUS" |
| `ms-MY` | Malese | Male |  "ms-MY-Rizwan" |
| `nb-NO` | Norvegese | Female |  "nb-NO-HuldaRUS" |
| `nl-NL` | Olandese | Female |  "nl-NL-HannaRUS" |
| `pl-PL` | Polacco | Female |  "pl-PL-PaulinaRUS" |
| `pt-BR` | Portoghese (Brasile) | Female | "pt-BR-HeloisaRUS" |
|  |  | Male |  "pt-BR-Daniel-Apollo" |
| `pt-PT` | Portoghese (Portogallo) | Female | "pt-PT-HeliaRUS" |
| `ro-RO` | Rumeno | Male | "ro-RO-Andrei" |
| `ru-RU` | Russo | Female |  "ru-RU-Irina-Apollo" |
|  |  | Male | "ru-RU-Pavel-Apollo" |
|  |  | Female |  ru-RU-EkaterinaRUS |
| `sk-SK` | Slovacco | Male | "sk-SK-Filip" |
| `sl-SI` | Sloveno | Male |  "sl-SI-Lado" |
| `sv-SE` | Svedese | Female | "sv-SE-HedvigRUS" |
| `ta-IN` | Tamil (India) | Male |  "ta-IN-Valluvar" |
| `te-IN` | Telugu (India) | Female |  "te-IN-Chitra" |
| `th-TH` | Thai | Male |  "th-TH-Pattara" |
| `tr-TR` | Turco (Turchia) | Female | "tr-TR-SedaRUS" |
| `vi-VN` | Vietnamita | Male |  "vi-VN-An" |
| `zh-CN` | Cinese (mandarino, semplificato) | Female |  "zh-CN-HuihuiRUS" |
|  |  | Female | "zh-CN-Yaoyao-Apollo" |
|  |  | Male | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Cinese (cantonese, tradizionale) | Female |  "zh-HK-Tracy-Apollo" |
|  |  | Female | "zh-HK-TracyRUS" |
|  |  | Male |  "zh-HK-Danny-Apollo" |
| `zh-TW` | Cinese (mandarino taiwanese) | Female |  "zh-TW-Yating-Apollo" |
|  |  | Female | "zh-TW-HanHanRUS" |
|  |  | Male |  "zh-TW-Zhiwei-Apollo" |

**1** *ar-EG supporta lo standard moderno della lingua araba.*

> [!IMPORTANT]
> La voce `en-US-Jessa` è stata modificata in `en-US-Aria`. Se in precedenza si usava "Jessa", convertirla in "Aria".

> [!TIP]
> Nelle richieste di sintesi vocale è possibile continuare a usare il mapping del nome completo del servizio, ad esempio "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)".

### <a name="customization"></a>Personalizzazione

La personalizzazione della voce è disponibile per `de-DE`, `en-GB`, `en-IN`, `en-US`, `es-MX`, `fr-FR`, `it-IT`, `pt-BR` e `zh-CN`. Selezionare le impostazioni locali corrette che corrispondono ai dati di training disponibili per il training di un modello vocale personalizzato. Se, ad esempio, i dati della registrazione sono pronunciati in inglese con un accento britannico, selezionare `en-GB`.

> [!NOTE]
> Il training del modello bilingue nella voce personalizzata non è supportato, ad eccezione del modello bilingue inglese-cinese. Per eseguire il training di una voce cinese in grado di parlare anche in inglese, selezionare "Chinese-English bilingual". Il training vocale in tutte le impostazioni locali inizia con un set di dati che include oltre 2000, ad eccezione dei set `en-US` e `zh-CN`, in cui è possibile iniziare dati di training di qualsiasi dimensione.

## <a name="speech-translation"></a>Traduzione vocale

L'API **Traduzione vocale** supporta lingue diverse per la traduzione vocale e con riconoscimento vocale. La lingua di origine deve essere sempre inclusa nella tabella delle lingue per il riconoscimento vocale. Le lingue di destinazione disponibili variano a seconda del fatto che siano parlate o scritte. È possibile tradurre la voce in ingresso in più di [60 lingue](https://www.microsoft.com/translator/business/languages/). Un subset di queste lingue è disponibile per la [sintesi vocale](language-support.md#text-languages).

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
| Rumeno                | `ro`          |
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

## <a name="speaker-recognition"></a>Riconoscimento del parlante

Vedere la tabella seguente per le lingue supportate per le diverse API di Riconoscimento del parlante. Per altre informazioni su Riconoscimento del parlante, vedere la [panoramica](speaker-recognition-overview.md).

| Impostazioni locali | Linguaggio | Verifica dipendente dal testo | Verifica indipendente dal testo | Identificazione indipendente dal testo |
|----|----|----|----|----|
| it-IT | Inglese (Stati Uniti) | sì | sì | sì |
|zh-CN  |Cinese (mandarino, semplificato)|    n/d|    sì|    sì|
|de-DE  |Tedesco (Germania)   |n/d    |sì    |sì|
|en-GB  |Inglese (Regno Unito)   |n/d    |sì    |sì|
|fr-FR  |Francese (Francia)    |n/d    |sì    |sì|
|en-AU  |Inglese (Australia)    |n/d    |sì    |sì|
|en-CA  |Inglese (Canada)   |n/d|   sì|    sì|
|fr-CA  |Francese (Canada)    |n/d    |sì|   sì|
|it-IT  |Italiano|   n/d |sì|   sì|
|es-ES| Spagnolo (Spagna) |n/d    |sì|   sì|
|es-MX  |Spagnolo (Messico)   |n/d|   sì|    sì|
|ja-JP| Giapponese    |n/d    |sì    |sì|
|pt-BR| Portoghese (Brasile)|    n/d|    sì|    sì|
|ko-KR| Coreano  |n/d    |sì|   sì|

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di prova al servizio Voce](https://azure.microsoft.com/try/cognitive-services/)
* [Informazioni sul riconoscimento vocale in C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
