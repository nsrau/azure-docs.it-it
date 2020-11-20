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
ms.custom: references_regions
ms.openlocfilehash: a430333f57b3734e5977db5c104c8f6d37bfd3c4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949089"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Lingue e voci supportate per il servizio Voce

Il supporto per la lingua varia in base alla funzionalità del servizio Voce. Nelle tabelle seguenti viene riepilogato il supporto per la lingua per le offerte di servizio [Riconoscimento vocale](#speech-to-text), [Sintesi vocale](#text-to-speech) e [Traduzione vocale](#speech-translation).

## <a name="speech-to-text"></a>Riconoscimento vocale

Sia Microsoft Speech SDK che l'API REST supportano le lingue seguenti (impostazioni locali). 

Per migliorare l'accuratezza, è possibile personalizzare un subset di lingue caricando **Audio + Human-labeled Transcripts** (Trascrizioni audio o con etichette umane) oppure **Related Text: Sentences** (Testo correlato: Frasi). Per altre informazioni sulla personalizzazione, vedere [Introduzione a Riconoscimento vocale personalizzato](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Linguaggio                          | Impostazioni locali (BCP-47) | Personalizzazioni                                   |
|-----------------------------------|--------|--------------------------------------------------|
|Arabo (Bahrein), standard moderno  |`ar-BH` | Modello linguistico                                   |
|Arabo (Egitto)                     |`ar-EG` | Modello linguistico                                   |
|Arabo (Iraq)                      |`ar-IQ` | Modello linguistico                                   |
|Arabo (Giordania)                    |`ar-JO` | Modello linguistico                                   |
|Arabo (Kuwait)                    |`ar-KW` | Modello linguistico                                   |
|Arabo (Libano)                   |`ar-LB` | Modello linguistico                                   |
|Arabo (Oman)                      |`ar-OM` | Modello linguistico                                   |
|Arabo (Qatar)                     |`ar-QA` | Modello linguistico                                   |
|Arabo (Arabia Saudita)              |`ar-SA` | Modello linguistico                                   |
|Arabo (Siria)                     |`ar-SY` | Modello linguistico                                   |
|Arabo (Emirati Arabi Uniti)      |`ar-AE` | Modello linguistico                                   |
|Bulgaro (Bulgaria)               |`bg-BG` | Modello linguistico                                   |
|Catalano (Spagna)                    |`ca-ES` | Modello linguistico                                   |
|Cinese (cantonese, tradizionale)   |`zh-HK` | Modello linguistico                                   |
|Cinese (mandarino, semplificato)     |`zh-CN` | Modello acustico<br>Modello linguistico                 |
|Cinese (mandarino taiwanese)       |`zh-TW` | Modello linguistico                                   |
|Croato (Croazia)                 |`hr-HR` | Modello linguistico                                   |
|Ceco (Repubblica Ceca)             |`cs-CZ` | Modello linguistico                                   |
|Danese (Danimarca)                   |`da-DK` | Modello linguistico                                   |
|Olandese (Paesi Bassi)                |`nl-NL` | Modello linguistico                                   |
|Inglese (Australia)                |`en-AU` | Modello acustico<br>Modello linguistico                 |
|Inglese (Canada)                   |`en-CA` | Modello acustico<br>Modello linguistico                 |
|Inglese (Hong Kong)                |`en-HK` | Modello linguistico                                   |
|Inglese (India)                    |`en-IN` | Modello acustico<br>Modello linguistico                 |
|Inglese (Irlanda)                  |`en-IE` | Modello linguistico                                   |
|Inglese (Nuova Zelanda)              |`en-NZ` | Modello acustico<br>Modello linguistico                 |
|Inglese (Filippine)              |`en-PH` | Modello linguistico                                   |
|Inglese (Singapore)                |`en-SG` | Modello linguistico                                   |
|Inglese (Sud Africa)             |`en-ZA` | Modello linguistico                                   |
|Inglese (Regno Unito)           |`en-GB` | Modello acustico<br>Modello linguistico<br>Pronuncia|
|Inglese (Stati Uniti)            |`en-US` | Modello acustico<br>Modello linguistico<br>Pronuncia|
|Estone (Estonia)                  |`et-EE` | Modello linguistico                                   |
|Finlandese (Finlandia)                  |`fi-FI` | Modello linguistico                                   |
|Francese (Canada)                    |`fr-CA` | Modello acustico<br>Modello linguistico                 |
|Francese (Francia)                    |`fr-FR` | Modello acustico<br>Modello linguistico<br>Pronuncia|
|Tedesco (Germania)                   |`de-DE` | Modello acustico<br>Modello linguistico<br>Pronuncia|
|Greco (Grecia)                     |`el-GR` | Modello linguistico                                   |
|Gujarati (India)                  |`gu-IN` | Modello linguistico                                   |
|Hindi (India)                      |`hi-IN` | Modello acustico<br>Modello linguistico                 |
|Ungherese (Ungheria)                |`hu-HU` | Modello linguistico                                   |
|Irlandese (Irlanda)                     |`ga-IE` | Modello linguistico                                   |
|Italiano (Italia)                    |`it-IT` | Modello acustico<br>Modello linguistico<br>Pronuncia|
|Giapponese (Giappone)                   |`ja-JP` | Modello linguistico                                   |
|Coreano (Corea)                     |`ko-KR` | Modello linguistico                                   |
|Lettone (Lettonia)                   |`lv-LV` | Modello linguistico                                   |
|Lituano (Lituania)             |`lt-LT` | Modello linguistico                                   |
|Maltese (Malta)                     |`mt-MT` | Modello linguistico                                   |
|Marathi (India)                    |`mr-IN` | Modello linguistico                                   |
|Norvegese (Bokmål, Norvegia)         |`nb-NO` | Modello linguistico                                   |
|Polacco (Polonia)                    |`pl-PL` | Modello linguistico                                   |
|Portoghese (Brasile)                |`pt-BR` | Modello acustico<br>Modello linguistico<br>Pronuncia|
|Portoghese (Portogallo)              |`pt-PT` | Modello linguistico                                   |
|Romeno (Romania)                 |`ro-RO` | Modello linguistico                                   |
|Russo (Russia)                   |`ru-RU` | Modello acustico<br>Modello linguistico                 |
|Slovacco (Slovacchia)                  |`sk-SK` | Modello linguistico                                   |
|Sloveno (Slovenia)               |`sl-SI` | Modello linguistico                                   |
|Spagnolo (Argentina)                |`es-AR` | Modello linguistico                                   |
|Spagnolo (Bolivia)                  |`es-BO` | Modello linguistico                                   |
|Spagnolo (Cile)                    |`es-CL` | Modello linguistico                                   |
|Spagnolo (Colombia)                 |`es-CO` | Modello linguistico                                   |
|Spagnolo (Costa Rica)               |`es-CR` | Modello linguistico                                   |
|Spagnolo (Cuba)                     |`es-CU` | Modello linguistico                                   |
|Spagnolo (Repubblica Dominicana)       |`es-DO` | Modello linguistico                                   |
|Spagnolo (Ecuador)                  |`es-EC` | Modello linguistico                                   |
|Spagnolo (El Salvador)              |`es-SV` | Modello linguistico                                   |
|Spagnolo (Guatemala)                |`es-GT` | Modello linguistico                                   |
|Spagnolo (Honduras)                 |`es-HN` | Modello linguistico                                   |
|Spagnolo (Messico)                   |`es-MX` | Modello acustico<br>Modello linguistico                 |
|Spagnolo (Nicaragua)                |`es-NI` | Modello linguistico                                   |
|Spagnolo (Panama)                   |`es-PA` | Modello linguistico                                   |
|Spagnolo (Paraguay)                 |`es-PY` | Modello linguistico                                   |
|Spagnolo (Perù)                     |`es-PE` | Modello linguistico                                   |
|Spagnolo (Porto Rico)              |`es-PR` | Modello linguistico                                   |
|Spagnolo (Spagna)                    |`es-ES` | Modello acustico<br>Modello linguistico                 |
|Spagnolo (Uruguay)                  |`es-UY` | Modello linguistico                                   |
|Spagnolo (Stati Uniti)                      |`es-US` | Modello linguistico                                   |
|Spagnolo (Venezuela)                |`es-VE` | Modello linguistico                                   |
|Svedese (Svezia)                   |`sv-SE` | Modello linguistico                                   |
|Tamil (India)                      |`ta-IN` | Modello linguistico                                   |
|Telugu (India)                     |`te-IN` | Modello linguistico                                   |
|Thai (Thailandia)                    |`th-TH` | Modello linguistico                                   |
|Turco (Turchia)                   |`tr-TR` | Modello linguistico                                   |

## <a name="text-to-speech"></a>Sintesi vocale

Sia Microsoft Speech SDK che le API REST supportano le voci seguenti, ognuna delle quali supporta una lingua e un dialetto specifici, identificate dalle impostazioni locali. È anche possibile ottenere un elenco completo di lingue e voci supportate per ogni area/endpoint specifico tramite l'API [voices/list](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> I prezzi variano per voci standard, personalizzate e neurali. Per informazioni aggiuntive, visitare la pagina [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="neural-voices"></a>Voci neurali

La sintesi vocale neurale è un nuovo tipo di sintesi vocale basata su reti neurali profonde. Quando si usa una voce neurale, è praticamente impossibile distinguere la sintesi vocale dalle registrazioni umane.

Le voci neurali possono essere usate per rendere più naturali e coinvolgenti le interazioni con chatbot e assistenti vocali, per convertire testo digitale, come gli e-book, in audiolibri e per migliorare i sistemi dei navigatori per le automobili. Con la prosodia naturale simile al linguaggio umano e l'articolazione chiara delle parole, le voci neurali riducono in modo significativo le difficoltà di ascolto durante l'interazione degli utenti con i sistemi di intelligenza artificiale.

| Linguaggio | Impostazioni locali | Sesso | Nome della voce | Supporto dello stile |
|---|---|---|---|---|
| Arabo (Egitto) | `ar-EG` | Female | `ar-EG-SalmaNeural` | Generale |
| Arabo (Arabia Saudita) | `ar-SA` | Female | `ar-SA-ZariyahNeural` | Generale |
| Bulgaro (Bulgaro) | `bg-BG` | Female | `bg-BG-KalinaNeural` <sup>Nuovo</sup> | Generale |
| Catalano (Spagna) | `ca-ES` | Female | `ca-ES-AlbaNeural` | Generale |
| Cinese (cantonese, tradizionale) | `zh-HK` | Female | `zh-HK-HiuGaaiNeural` | Generale |
| Cinese (mandarino, semplificato) | `zh-CN` | Female | `zh-CN-XiaoxiaoNeural` | Generale, più stili vocali disponibili [con SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Cinese (mandarino, semplificato) | `zh-CN` | Female | `zh-CN-XiaoyouNeural` | Voce infantile, ottimizzata per la narrazione di storie |
| Cinese (mandarino, semplificato) | `zh-CN` | Male   | `zh-CN-YunyangNeural` | Ottimizzato per la lettura di notizie,<br /> più stili vocali disponibili [con SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Cinese (mandarino, semplificato) | `zh-CN` | Male   | `zh-CN-YunyeNeural` | Ottimizzata per la narrazione di storie |
| Cinese (mandarino taiwanese) | `zh-TW` | Female | `zh-TW-HsiaoYuNeural` | Generale |
| Croato (Croazia) | `hr-HR` | Female | `hr-HR-GabrijelaNeural` <sup>Nuovo</sup> | Generale |
| Ceco (Ceco) | `cs-CZ` | Female | `cs-CZ-VlastaNeural` <sup>Nuovo</sup>    | Generale |
| Danese (Danimarca) | `da-DK` | Female | `da-DK-ChristelNeural` | Generale |
| Olandese (Paesi Bassi) | `nl-NL` | Female | `nl-NL-ColetteNeural` | Generale |
| Inglese (Australia) | `en-AU` | Female | `en-AU-NatashaNeural` | Generale |
| Inglese (Australia) | `en-AU` | Male   | `en-AU-WilliamNeural` <sup>Nuovo</sup>  | Generale |
| Inglese (Canada) | `en-CA` | Female | `en-CA-ClaraNeural` | Generale |
| Inglese (India) | `en-IN` | Female | `en-IN-NeerjaNeural` | Generale |
| Inglese (Irlanda) | `en-IE` | Female | `en-IE-EmilyNeural` <sup>Nuovo</sup> | Generale |
| Inglese (Regno Unito) | `en-GB` | Female | `en-GB-LibbyNeural` | Generale |
| Inglese (Regno Unito) | `en-GB` | Female | `en-GB-MiaNeural` | Generale |
| Inglese (Regno Unito) | `en-GB` | Male | `en-GB-RyanNeural` <sup>Nuovo</sup> | Generale |
| Inglese (Stati Uniti) | `en-US` | Female | `en-US-AriaNeural` | Generale, più stili vocali disponibili [con SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Inglese (Stati Uniti) | `en-US` | Male | `en-US-GuyNeural` | Generale |
| Inglese (Stati Uniti) | `en-US` | Female | `en-US-JennyNeural` <sup>Nuovo</sup> | Generale, più stili vocali disponibili [con SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Finlandese (Finlandia) | `fi-FI` | Female | `fi-FI-NooraNeural` | Generale |
| Francese (Canada) | `fr-CA` | Female | `fr-CA-SylvieNeural` | Generale |
| Francese (Canada) | `fr-CA` | Male | `fr-CA-JeanNeural` <sup>Nuovo</sup> | Generale |
| Francese (Francia) | `fr-FR` | Female | `fr-FR-DeniseNeural` | Generale |
| Francese (Francia) | `fr-FR` | Male | `fr-FR-HenriNeural` <sup>Nuovo</sup> | Generale |
| Francese (Svizzera) | `fr-CH` | Female | `fr-CH-ArianeNeural` <sup>Nuovo</sup> | Generale |
| Tedesco (Austria) | `de-AT` | Female | `de-AT-IngridNeural` <sup>Nuovo</sup> | Generale |
| Tedesco (Germania) | `de-DE` | Female | `de-DE-KatjaNeural` | Generale |
| Tedesco (Germania) | `de-DE` | Male | `de-DE-ConradNeural` <sup>Nuovo</sup> | Generale |
| Tedesco (Svizzera) | `de-CH` | Female | `de-CH-LeniNeural` <sup>Nuovo</sup> | Generale |
| Greco (Grecia) | `el-GR` | Female | `el-GR-AthinaNeural` <sup>Nuovo</sup> | Generale |
| Ebraico (Israele) | `he-IL` | Female | `he-IL-HilaNeural` <sup>Nuovo</sup> | Generale |
| Hindi (India) | `hi-IN` | Female | `hi-IN-SwaraNeural` | Generale |
| Ungherese (Ungheria) | `hu-HU` | Female | `hu-HU-NoemiNeural` <sup>Nuovo</sup> | Generale |
| Indonesiano (Indonesia) | `id-ID` | Male | `id-ID-ArdiNeural` <sup>Nuovo</sup> | Generale |
| Italiano (Italia) | `it-IT` | Female | `it-IT-ElsaNeural` | Generale |
| Italiano (Italia) | `it-IT` | Female | `it-IT-IsabellaNeural` <sup>Nuovo</sup> | Generale |
| Italiano (Italia) | `it-IT` | Male | `it-IT-DiegoNeural` <sup>Nuovo</sup> | Generale |
| Giapponese (Giappone) | `ja-JP` | Female | `ja-JP-NanamiNeural` | Generale |
| Giapponese (Giappone) | `ja-JP` | Male | `ja-JP-KeitaNeural` <sup>Nuovo</sup> | Generale |
| Coreano (Corea) | `ko-KR` | Female | `ko-KR-SunHiNeural` | Generale |
| Coreano (Corea) | `ko-KR` | Male | `ko-KR-InJoonNeural` <sup>Nuovo</sup> | Generale |
| Malese (Malaysia) | `ms-MY` | Female | `ms-MY-YasminNeural` <sup>Nuovo</sup> | Generale |
| Norvegese (Bokmål, Norvegia) | `nb-NO` | Female | `nb-NO-IselinNeural` | Generale |
| Polacco (Polonia) | `pl-PL` | Female | `pl-PL-ZofiaNeural` | Generale |
| Portoghese (Brasile) | `pt-BR` | Female | `pt-BR-FranciscaNeural` | Generale, più stili vocali disponibili [con SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Portoghese (Brasile) | `pt-BR` | Male | `pt-BR-AntonioNeural` <sup>Nuovo</sup> | Generale |
| Portoghese (Portogallo) | `pt-PT` | Female | `pt-PT-FernandaNeural` | Generale |
| Romeno (Romania) | `ro-RO` | Female | `ro-RO-AlinaNeural` <sup>Nuovo</sup> | Generale |
| Russo (Russia) | `ru-RU` | Female | `ru-RU-DariyaNeural` | Generale |
| Slovacco (Slovacchia) | `sk-SK` | Female | `sk-SK-ViktoriaNeural` <sup>Nuovo</sup> | Generale |
| Sloveno (Slovenia) | `sl-SI` | Female | `sl-SI-PetraNeural` <sup>Nuovo</sup> | Generale |
| Spagnolo (Messico) | `es-MX` | Female | `es-MX-DaliaNeural` | Generale |
| Spagnolo (Messico) | `es-MX` | Male | `es-MX-JorgeNeural` <sup>Nuovo</sup> | Generale |
| Spagnolo (Spagna) | `es-ES` | Female | `es-ES-ElviraNeural` | Generale |
| Spagnolo (Spagna) | `es-ES` | Male | `es-ES-AlvaroNeural` <sup>Nuovo</sup> | Generale |
| Svedese (Svezia) | `sv-SE` | Female | `sv-SE-HilleviNeural` | Generale |
| Tamil (India) | `ta-IN` | Female | `ta-IN-PallaviNeural` <sup>Nuovo</sup> | Generale |
| Telugu (India) | `te-IN` | Female | `te-IN-ShrutiNeural` <sup>Nuovo</sup> | Generale |
| Thai (Thailandia) | `th-TH` | Female | `th-TH-AcharaNeural` | Generale |
| Thai (Thailandia) | `th-TH` | Female | `th-TH-PremwadeeNeural` <sup>Nuovo</sup> | Generale |
| Turco (Turchia) | `tr-TR` | Female | `tr-TR-EmelNeural` | Generale |
| Vietnamita (Vietnam) | `vi-VN` <sup>Nuovo</sup> | Female | `vi-VN-HoaiMyNeural` | Generale|

#### <a name="neural-voices-in-preview"></a>Voci neurali in anteprima

Sotto le voci neurali sono disponibili in anteprima pubblica. 

| Linguaggio                         | Impostazioni locali  | Sesso | Nome della voce                             | Supporto dello stile |
|----------------------------------|---------|--------|----------------------------------------|---------------|
| Cinese (mandarino, semplificato) | `zh-CN` | Male   | `zh-CN-YunxiNeural` <sup>Nuovo</sup> | Generale, più stili disponibili [con SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Cinese (mandarino, semplificato) | `zh-CN` | Female | `zh-CN-XiaohanNeural` <sup>Nuovo</sup> | Generale, più stili disponibili [con SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Cinese (mandarino, semplificato) | `zh-CN` | Female | `zh-CN-XiaoxuanNeural` <sup>Nuovo</sup> | Generale, più Riproduci ruoli e [stili disponibili con SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Cinese (mandarino, semplificato) | `zh-CN` | Female | `zh-CN-XiaomoNeural` <sup>Nuovo</sup> | Generale, più Riproduci ruoli e [stili disponibili con SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Cinese (mandarino, semplificato) | `zh-CN` | Female | `zh-CN-XiaoruiNeural` <sup>Nuovo</sup> | Voce Senior, più stili disponibili [con SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Estone (Estonia) | `et-EE` | Female | `et-EE-AnuNeural` <sup>Nuovo</sup> | Generale |
| Gaelico (Irlanda) | `ga-IE` | Female | `ga-IE-OrlaNeural` <sup>Nuovo</sup> | Generale |
| Lituano (Lituania) | `lt-LT` | Female | `lt-LT-OnaNeural` <sup>Nuovo</sup> | Generale |
| Lettone (Lettonia) | `lv-LV` | Female | `lv-LV-EveritaNeural` <sup>Nuovo</sup> | Generale |
| Maltese (Malta) | `mt-MT` | Female | `mt-MT-GraceNeural` <sup>Nuovo</sup> | Generale |

> [!IMPORTANT]
> Le voci nell'anteprima pubblica sono disponibili solo in 3 aree del servizio: Stati Uniti orientali, Europa occidentale e Asia sudorientale.

Per informazioni sulla disponibilità a livello di area, vedere [Aree](regions.md#standard-and-neural-voices).

Per informazioni su come configurare e modificare le voci neurali, ad esempio gli stili di pronuncia, vedere [linguaggio di markup sintesi vocale](speech-synthesis-markup.md#adjust-speaking-styles).

> [!IMPORTANT]
> La voce `en-US-JessaNeural` è stata modificata in `en-US-AriaNeural`. Se in precedenza si usava "Jessa", convertirla in "Aria".

> [!TIP]
> Nelle richieste di sintesi vocale è possibile continuare a usare il mapping del nome completo del servizio, ad esempio "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)".

### <a name="standard-voices"></a>Voci standard

Sono disponibili più di 75 voci standard in oltre 45 lingue e impostazioni locali, che consentono di convertire il testo in contenuto vocale sintetizzato. Per informazioni sulla disponibilità a livello di area, vedere [Aree](regions.md#standard-and-neural-voices).

| Linguaggio | Impostazioni locali (BCP-47) | Sesso | Nome della voce |
|--|--|--|--|
| Arabo (arabo) | `ar-EG` | Female | `ar-EG-Hoda`|
| Arabo (Arabia Saudita) | `ar-SA` | Male | `ar-SA-Naayf`|
| Bulgaro (Bulgaria) | `bg-BG` | Male | `bg-BG-Ivan`|
| Catalano (Spagna) | `ca-ES` | Female | `ca-ES-HerenaRUS`|
| Cinese (cantonese, tradizionale) | `zh-HK` | Male | `zh-HK-Danny`|
| Cinese (cantonese, tradizionale) | `zh-HK` | Female | `zh-HK-TracyRUS`|
| Cinese (mandarino, semplificato) | `zh-CN` | Female | `zh-CN-HuihuiRUS`|
| Cinese (mandarino, semplificato) | `zh-CN` | Male | `zh-CN-Kangkang`|
| Cinese (mandarino, semplificato) | `zh-CN` | Female | `zh-CN-Yaoyao`|
| Cinese (mandarino taiwanese) |  `zh-TW` | Female | `zh-TW-HanHanRUS`|
| Cinese (mandarino taiwanese) |  `zh-TW` | Female | `zh-TW-Yating`|
| Cinese (mandarino taiwanese) |  `zh-TW` | Male | `zh-TW-Zhiwei`|
| Croato (Croazia) | `hr-HR` | Male | `hr-HR-Matej`|
| Ceco (Repubblica Ceca) | `cs-CZ` | Male | `cs-CZ-Jakub`|
| Danese (Danimarca) | `da-DK` | Female | `da-DK-HelleRUS`|
| Olandese (Paesi Bassi) | `nl-NL` | Female | `nl-NL-HannaRUS`|
| Inglese (Australia) | `en-AU` | Female | `en-AU-Catherine`|
| Inglese (Australia) | `en-AU` | Female | `en-AU-HayleyRUS`|
| Inglese (Canada) | `en-CA` | Female | `en-CA-HeatherRUS`|
| Inglese (Canada) | `en-CA` | Female | `en-CA-Linda`|
| Inglese (India) | `en-IN` | Female | `en-IN-Heera`|
| Inglese (India) | `en-IN` | Female | `en-IN-PriyaRUS`|
| Inglese (India) | `en-IN` | Male | `en-IN-Ravi`|
| Inglese (Irlanda) | `en-IE` | Male | `en-IE-Sean`|
| Inglese (Regno Unito) | `en-GB` | Male | `en-GB-George`|
| Inglese (Regno Unito) | `en-GB` | Female | `en-GB-HazelRUS`|
| Inglese (Regno Unito) | `en-GB` | Female | `en-GB-Susan`|
| Inglese (Stati Uniti) | `en-US` | Male | `en-US-BenjaminRUS`|
| Inglese (Stati Uniti) | `en-US` | Male | `en-US-GuyRUS`|
| Inglese (Stati Uniti) | `en-US` | Female | `en-US-JessaRUS`|
| Inglese (Stati Uniti) | `en-US` | Female | `en-US-ZiraRUS`|
| Finlandese (Finlandia) | `fi-FI` | Female | `fi-FI-HeidiRUS`|
| Francese (Canada) | `fr-CA` | Female | `fr-CA-Caroline`|
| Francese (Canada) | `fr-CA` | Female | `fr-CA-HarmonieRUS`|
| Francese (Francia) | `fr-FR` | Female | `fr-FR-HortenseRUS`|
| Francese (Francia) | `fr-FR` | Female | `fr-FR-Julie`|
| Francese (Francia) | `fr-FR` | Male | `fr-FR-Paul`|
| Francese (Svizzera) | `fr-CH` | Male | `fr-CH-Guillaume`|
| Tedesco (Austria) | `de-AT` | Male | `de-AT-Michael`|
| Tedesco (Germania) | `de-DE` | Female | `de-DE-HeddaRUS`|
| Tedesco (Germania) | `de-DE` | Male | `de-DE-Stefan`|
| Tedesco (Svizzera) | `de-CH` | Male | `de-CH-Karsten`|
| Greco (Grecia) | `el-GR` | Male | `el-GR-Stefanos`|
| Ebraico (Israele) | `he-IL` | Male | `he-IL-Asaf`|
| Hindi (India) | `hi-IN` | Male | `hi-IN-Hemant`|
| Hindi (India) | `hi-IN` | Female | `hi-IN-Kalpana`|
| Ungherese (Ungheria) | `hu-HU` | Male | `hu-HU-Szabolcs`|
| Indonesiano (Indonesia) | `id-ID` | Male | `id-ID-Andika`|
| Italiano (Italia) | `it-IT` | Male | `it-IT-Cosimo`|
| Italiano (Italia) | `it-IT` | Female | `it-IT-LuciaRUS`|
| Giapponese (Giappone) | `ja-JP` | Female | `ja-JP-Ayumi`|
| Giapponese (Giappone) | `ja-JP` | Female | `ja-JP-HarukaRUS`|
| Giapponese (Giappone) | `ja-JP` | Male | `ja-JP-Ichiro`|
| Coreano (Corea) | `ko-KR` | Female | `ko-KR-HeamiRUS`|
| Malese (Malaysia) | `ms-MY` | Male | `ms-MY-Rizwan`|
| Norvegese (Bokmål, Norvegia) | `nb-NO` | Female | `nb-NO-HuldaRUS`|
| Polacco (Polonia) | `pl-PL` | Female | `pl-PL-PaulinaRUS`|
| Portoghese (Brasile) | `pt-BR` | Male | `pt-BR-Daniel`|
| Portoghese (Brasile) | `pt-BR` | Female | `pt-BR-HeloisaRUS`|
| Portoghese (Portogallo) | `pt-PT` | Female | `pt-PT-HeliaRUS`|
| Romeno (Romania) | `ro-RO` | Male | `ro-RO-Andrei`|
| Russo (Russia) | `ru-RU` | Female | `ru-RU-EkaterinaRUS`|
| Russo (Russia) | `ru-RU` | Female | `ru-RU-Irina`|
| Russo (Russia) | `ru-RU` | Male | `ru-RU-Pavel`|
| Slovacco (Slovacchia) | `sk-SK` | Male | `sk-SK-Filip`|
| Sloveno (Slovenia) | `sl-SI` | Male | `sl-SI-Lado`|
| Spagnolo (Messico) | `es-MX` | Female | `es-MX-HildaRUS`|
| Spagnolo (Messico) | `es-MX` | Male | `es-MX-Raul`|
| Spagnolo (Spagna) | `es-ES` | Female | `es-ES-HelenaRUS`|
| Spagnolo (Spagna) | `es-ES` | Female | `es-ES-Laura`|
| Spagnolo (Spagna) | `es-ES` | Male | `es-ES-Pablo`|
| Svedese (Svezia) | `sv-SE` | Female | `sv-SE-HedvigRUS`|
| Tamil (India) | `ta-IN` | Male | `ta-IN-Valluvar`|
| Telugu (India) | `te-IN` | Female | `te-IN-Chitra`|
| Thai (Thailandia) | `th-TH` | Male | `th-TH-Pattara`|
| Turco (Turchia) | `tr-TR` | Female | `tr-TR-SedaRUS`|
| Vietnamita (Vietnam) | `vi-VN` | Male | `vi-VN-An` |

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
| Klingon                 | `tlh-Latn`    |
| Klingon (plqaD)         | `tlh-Piqd`    |
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

| Linguaggio | Impostazioni locali (BCP-47) | Verifica dipendente dal testo | Verifica indipendente dal testo | Identificazione indipendente dal testo |
|----|----|----|----|----|
|Inglese (Stati Uniti)  |  en-US  |  sì  |  sì  |  sì |
|Cinese (mandarino, semplificato) | zh-CN     |     n/d |     sì |     sì|
|Inglese (Australia)     | en-AU     | n/d     | sì     | sì|
|Inglese (Canada)     | en-CA     | n/d |     sì |     sì|
|Inglese (Regno Unito)     | en-GB     | n/d     | sì     | sì|
|Francese (Canada)     | fr-CA     | n/d     | sì |     sì|
|Francese (Francia)     | fr-FR     | n/d     | sì     | sì|
|Tedesco (Germania)     | de-DE     | n/d     | sì     | sì|
|Italiano | it-IT     |     n/d     | sì |     sì|
|Giapponese     | ja-JP | n/d     | sì     | sì|
|Portoghese (Brasile) | pt-BR |     n/d |     sì |     sì|
|Spagnolo (Messico)     | es-MX     | n/d |     sì |     sì|
|Spagnolo (Spagna)     | es-ES | n/d     | sì |     sì|

## <a name="next-steps"></a>Passaggi successivi

* [Creare un account Azure gratuito](https://azure.microsoft.com/free/cognitive-services/)
* [Informazioni sul riconoscimento vocale in C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
