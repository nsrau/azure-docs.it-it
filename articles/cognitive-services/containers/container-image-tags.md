---
title: Tag delle immagini di contenitori in Servizi cognitivi
titleSuffix: Azure Cognitive Services
description: Elenco completo di tutti i tag dell'immagine del contenitore di servizi cognitivi.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/17/2020
ms.author: aahi
ms.openlocfilehash: ba51776942ad28fc8d4b0db7dd2d0e162e5322b5
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94743338"
---
# <a name="azure-cognitive-services-container-image-tags-and-release-notes"></a>Tag di immagine del contenitore di servizi cognitivi di Azure e note sulla versione

Servizi cognitivi di Azure offre molte immagini del contenitore. I registri dei contenitori e i repository corrispondenti variano tra le immagini del contenitore. Ogni nome dell'immagine del contenitore offre più tag. Un tag dell'immagine del contenitore è un meccanismo di controllo delle versioni dell'immagine del contenitore. Questo articolo è destinato a essere usato come riferimento completo per l'elenco di tutte le immagini del contenitore di servizi cognitivi e dei relativi tag disponibili.

> [!TIP]
> Quando si usa [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) , prestare particolare attenzione alle maiuscole e minuscole del registro contenitori, al repository, al nome dell'immagine del contenitore e al tag corrispondente, poiché fanno **distinzione tra maiuscole** e minuscole.

## <a name="anomaly-detector"></a>Rilevamento anomalie

L'immagine del contenitore del [rilevatore di anomalie][ad-containers] è disponibile nel `mcr.microsoft.com` Syndicate del registro contenitori. Si trova all'interno del `azure-cognitive-services/decision` repository ed è denominato `anomaly-detector` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` .

Questa immagine del contenitore include i seguenti tag disponibili. È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/decision/anomaly-detector/tags/list)versione.

# <a name="latest-version"></a>[Versione più recente](#tab/current)

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013560003-amd64-preview` |      |

# <a name="previous-versions"></a>[Versioni precedenti](#tab/previous)

| Tag immagine                    | Note |
|-------------------------------|:------|
| `1.1.012300001-amd64-preview` |       |

---

## <a name="read-ocr-optical-character-recognition"></a>Lettura OCR (riconoscimento ottico di caratteri)

Il [visione artificiale][cv-containers] l'immagine del contenitore OCR è disponibile nel `mcr.microsoft.com` Syndicate del registro contenitori. Si trova all'interno del `azure-cognitive-services` repository ed è denominato `read` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/vision/read` .

Questa immagine del contenitore include i seguenti tag disponibili. È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/vision/read/tags/list)versione.

# <a name="latest-version"></a>[Versione più recente](#tab/current)

Note sulla versione per `v2.0.013250001-amd64-preview` :

* Ridurre ulteriormente l'utilizzo della memoria per il contenitore.
* La cache esterna è necessaria per la configurazione di più POD. Ad esempio, configurare Redis per la memorizzazione nella cache.
* Corretti i risultati mancanti quando la cache Redis è configurata e `ResultExpirationPeriod` è impostata su 0.
* Rimuovere la limitazione delle dimensioni del corpo della richiesta di 26MB. Il contenitore è ora in grado di accettare >file 26MB.
* Aggiungere il timestamp e la versione di build alla registrazione della console.

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `2.0.013250001-amd64-preview` |  |

# <a name="previous-versions"></a>[Versioni precedenti](#tab/previous)

Note sulla versione per `1.1.013050001-amd64-preview`

* Aggiunta `ReadEngineConfig:ResultExpirationPeriod` della configurazione di inizializzazione del contenitore per specificare quando il sistema deve pulire i risultati del riconoscimento. 
    * L'impostazione è in ore e il valore predefinito è 48 ore.
    * L'impostazione può ridurre l'utilizzo della memoria per l'archiviazione dei risultati, soprattutto quando si usa l'archiviazione in memoria del contenitore.
    * Esempio 1. ReadEngineConfig: ResultExpirationPeriod = 1, il sistema cancellerà il risultato del riconoscimento 1 ora dopo il processo.
    * Se questa configurazione è impostata su 0, il sistema cancellerà il risultato del riconoscimento dopo che il risultato è stato recuperato.

* Correzione di un errore interno del server 500 quando al sistema viene passato un formato di immagine non valido. Verrà ora restituito un errore 400:

    ```json
    {
        "error": {
        "code": "InvalidImageSize",
        "message": "Image must be between 1024 and 209715200 bytes."
        }
    }
    ```

| Tag immagine                    | Note |
|-------------------------------|:------|
| `1.1.013050001-amd64-preview` |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

---


## <a name="form-recognizer"></a>Riconoscimento modulo

L'immagine del contenitore del [riconoscitore del modulo][fr-containers] è disponibile nel `mcr.microsoft.com` Syndication del registro contenitori. Si trova all'interno del `azure-cognitive-services/custom-form` repository ed è denominato `labeltool` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool` .

Questa immagine del contenitore include i seguenti tag disponibili. È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/custom-form/labeltool/tags/list)versione.

# <a name="latest-version"></a>[Versione più recente](#tab/current)

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |


# <a name="previous-versions"></a>[Versioni precedenti](#tab/previous)

| Tag immagine                    | Note |
|-------------------------------|:------|
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

---

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

L'immagine del contenitore [Luis][lu-containers] si trova nel `mcr.microsoft.com` Syndicate del registro contenitori. Si trova all'interno del `azure-cognitive-services/language` repository ed è denominato `luis` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/language/luis` .

Questa immagine del contenitore include i seguenti tag disponibili. È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/language/luis/tags/list)versione.

# <a name="latest-version"></a>[Versione più recente](#tab/current)

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012280003-amd64-preview` |       |


# <a name="previous-version"></a>[Versione precedente](#tab/previous)

| Tag immagine                    | Note |
|-------------------------------|:------|
| `1.1.012130003-amd64-preview` |       |

---

## <a name="custom-speech-to-text"></a>Da Riconoscimento vocale personalizzato a testo

L'immagine del contenitore [da riconoscimento vocale personalizzato a testo][sp-cstt] è disponibile nel Syndicate del `mcr.microsoft.com` Registro contenitori. Si trova all'interno del `azure-cognitive-services/speechservices/` repository ed è denominato `custom-speech-to-text` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` . È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-speech-to-text/tags/list)versione.


# <a name="latest-version"></a>[Versione più recente](#tab/current)

Nota sulla versione per `2.6.0-amd64` :

**Funzionalità**
* Supporto per l'phrase V2 
* Gli elenchi di frasi sono supportati nelle impostazioni locali seguenti:
    * it-au
    * en-CA
    * en-GB
    * it-in
    * en-NZ
    * it-it
    * zh-cn
* Supporto per il download personalizzato del modello di base. 
    * Usare `BaseModelLocale=<locale>` con il `docker run` comando
* Migrazione completa a .NET 3,1

**Correzioni**
* È stato risolto un problema per cui il Punteggio di confidenza era sempre 1 in modalità di programmazione
* Migrazione all'API TextAnalytics 3,0

Si noti che a causa degli elenchi di frasi inclusi, le dimensioni di questa immagine del contenitore sono aumentate.

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `2.6.0-amd64`                 |       |


# <a name="previous-version"></a>[Versione precedente](#tab/previous)

Nota sulla versione per `2.5.0-amd64` :

**Funzionalità**
* Supporto della pronuncia personalizzata nei modelli personalizzati
* Supportare Azure e il Cloud Azure per enti pubblici degli Stati Uniti

**Correzioni**
* Correzione del problema di esecuzione come utente non radice in modalità di Diarizzazione

| Tag immagine                    | Note               |
|-------------------------------|:--------------------|
| `2.5.0-amd64`                 |   1 ° versione GA    |

---

## <a name="custom-text-to-speech"></a>Sintesi vocale personalizzata

L'immagine del contenitore [di sintesi vocale personalizzato][sp-ctts] è disponibile nel `mcr.microsoft.com` Syndicate del registro contenitori. Si trova all'interno del `azure-cognitive-services/speechservices/` repository ed è denominato `custom-text-to-speech` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` . È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-text-to-speech/tags/list)versione.


# <a name="latest-version"></a>[Versione più recente](#tab/current)

Nota sulla versione per `1.8.0-amd64` :

**Funzionalità**
* Migrazione completa a .NET 3,1

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.8.0-amd64`                 |       |


# <a name="previous-version"></a>[Versione precedente](#tab/previous)

Nota sulla versione per `1.7.0-amd64` :

**Funzionalità**
* Migrazione parziale a .NET 3,1

| Tag immagine                    | Note               |
|-------------------------------|:--------------------|
| `1.7.0-amd64`                 |   1 ° versione GA    |

---

## <a name="speech-to-text"></a>Riconoscimento vocale

L'immagine del contenitore [di sintesi vocale][sp-stt] è reperibile nel Syndicate del `mcr.microsoft.com` Registro contenitori. Si trova all'interno del `azure-cognitive-services/speechservices/` repository ed è denominato `speech-to-text` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` . È possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/speech-to-text/tags/list)versione.

Poiché il 2.5.0 di riconoscimento vocale v, le immagini sono supportate nell'area *Virginia del governo degli Stati Uniti* . Usare l'endpoint di fatturazione di Virginia per il *governo degli Stati Uniti* e le chiavi API quando si usa questa area.

# <a name="latest-version"></a>[Versione più recente](#tab/current)

Nota sulla versione per `2.6.0-amd64-<locale>` :

**Funzionalità**
* Aggiornamento ai modelli più recenti e migrazione completa a .NET 3,1
* Supporto per l'phrase V2
* Gli elenchi di frasi sono supportati nelle impostazioni locali seguenti:
    * it-au
    * en-CA
    * en-GB
    * it-in
    * en-NZ
    * it-it
    * zh-cn
* Supporto per le nuove impostazioni locali `cs-CZ` 
    * Le lettere maiuscole e punteggiatura non sono attualmente supportate.

**Correzioni**
* Corregge un problema per cui i punteggi di confidenza sono sempre 1 in modalità di pubblicazione
* Migrated use the TextAnalytics 3,0 API

Si noti che a causa degli elenchi di frasi inclusi, le dimensioni di questa immagine del contenitore sono aumentate. 

| Tag immagine                    | Note                                                                                                | 
|-------------------------------|:-----------------------------------------------------------------------------------------------------|
| `latest`                      | Immagine del contenitore con le `en-US` impostazioni locali.                                                             |
| `2.6.0-amd64-<locale>`        | Sostituire `<locale>` con una delle impostazioni locali disponibili, elencate di seguito. Ad esempio `2.6.0-amd64-en-us`. |

Per questo contenitore sono disponibili le impostazioni locali seguenti.

| Impostazioni locali per v 2.6.0           | Note                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Immagine del contenitore con le `ar-AE` impostazioni locali. |
| `ar-eg`                     | Immagine del contenitore con le `ar-EG` impostazioni locali. |
| `ar-kw`                     | Immagine del contenitore con le `ar-KW` impostazioni locali. |
| `ar-qa`                     | Immagine del contenitore con le `ar-QA` impostazioni locali. |
| `ar-sa`                     | Immagine del contenitore con le `ar-SA` impostazioni locali. |
| `ca-es`                     | Immagine del contenitore con le `ca-ES` impostazioni locali. |
| `cs-cz`                     | Immagine del contenitore con le `cs-CZ` impostazioni locali. |
| `da-dk`                     | Immagine del contenitore con le `da-DK` impostazioni locali. |
| `de-de`                     | Immagine del contenitore con le `de-DE` impostazioni locali. |
| `en-au`                     | Immagine del contenitore con le `en-AU` impostazioni locali. |
| `en-ca`                     | Immagine del contenitore con le `en-CA` impostazioni locali. |
| `en-gb`                     | Immagine del contenitore con le `en-GB` impostazioni locali. |
| `en-in`                     | Immagine del contenitore con le `en-IN` impostazioni locali. |
| `en-nz`                     | Immagine del contenitore con le `en-NZ` impostazioni locali. |
| `en-us`                     | Immagine del contenitore con le `en-US` impostazioni locali. |
| `es-es`                     | Immagine del contenitore con le `es-ES` impostazioni locali. |
| `es-mx`                     | Immagine del contenitore con le `es-MX` impostazioni locali. |
| `fi-fi`                     | Immagine del contenitore con le `fi-FI` impostazioni locali. |
| `fr-ca`                     | Immagine del contenitore con le `fr-CA` impostazioni locali. |
| `fr-fr`                     | Immagine del contenitore con le `fr-FR` impostazioni locali. |
| `gu-in`                     | Immagine del contenitore con le `gu-IN` impostazioni locali. |
| `hi-in`                     | Immagine del contenitore con le `hi-IN` impostazioni locali. |
| `it-it`                     | Immagine del contenitore con le `it-IT` impostazioni locali. |
| `ja-jp`                     | Immagine del contenitore con le `ja-JP` impostazioni locali. |
| `ko-kr`                     | Immagine del contenitore con le `ko-KR` impostazioni locali. |
| `mr-in`                     | Immagine del contenitore con le `mr-IN` impostazioni locali. |
| `nb-no`                     | Immagine del contenitore con le `nb-NO` impostazioni locali. |
| `nl-nl`                     | Immagine del contenitore con le `nl-NL` impostazioni locali. |
| `pl-pl`                     | Immagine del contenitore con le `pl-PL` impostazioni locali. |
| `pt-br`                     | Immagine del contenitore con le `pt-BR` impostazioni locali. |
| `pt-pt`                     | Immagine del contenitore con le `pt-PT` impostazioni locali. |
| `ru-ru`                     | Immagine del contenitore con le `ru-RU` impostazioni locali. |
| `sv-se`                     | Immagine del contenitore con le `sv-SE` impostazioni locali. |
| `ta-in`                     | Immagine del contenitore con le `ta-IN` impostazioni locali. |
| `te-in`                     | Immagine del contenitore con le `te-IN` impostazioni locali. |
| `th-th`                     | Immagine del contenitore con le `th-TH` impostazioni locali. |
| `tr-tr`                     | Immagine del contenitore con le `tr-TR` impostazioni locali. |
| `zh-cn`                     | Immagine del contenitore con le `zh-CN` impostazioni locali. |
| `zh-hk`                     | Immagine del contenitore con le `zh-HK` impostazioni locali. |
| `zh-tw`                     | Immagine del contenitore con le `zh-TW` impostazioni locali. |


# <a name="previous-version"></a>[Versione precedente](#tab/previous)

Nota sulla versione per `2.5.0-amd64-<locale>` :

**Funzionalità**
* Supporto per il cloud di Azure per il governo degli Stati Uniti

**Correzioni**
* Corregge un problema relativo all'esecuzione come utente non root in modalità di pubblicazione

| Tag immagine                  | Note                                    |
|-----------------------------|:-----------------------------------------|
| `2.5.0-amd64-<locale>`      | Sostituire `<locale>` con una delle impostazioni locali disponibili, elencate di seguito. Ad esempio `2.5.0-amd64-en-us`.  |

Per questo contenitore sono disponibili le impostazioni locali seguenti.

| Impostazioni locali per v 2.5.0           | Note                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Immagine del contenitore con le `ar-AE` impostazioni locali. |
| `ar-eg`                     | Immagine del contenitore con le `ar-EG` impostazioni locali. |
| `ar-kw`                     | Immagine del contenitore con le `ar-KW` impostazioni locali. |
| `ar-qa`                     | Immagine del contenitore con le `ar-QA` impostazioni locali. |
| `ar-sa`                     | Immagine del contenitore con le `ar-SA` impostazioni locali. |
| `ca-es`                     | Immagine del contenitore con le `ca-ES` impostazioni locali. |
| `da-dk`                     | Immagine del contenitore con le `da-DK` impostazioni locali. |
| `de-de`                     | Immagine del contenitore con le `de-DE` impostazioni locali. |
| `en-au`                     | Immagine del contenitore con le `en-AU` impostazioni locali. |
| `en-ca`                     | Immagine del contenitore con le `en-CA` impostazioni locali. |
| `en-gb`                     | Immagine del contenitore con le `en-GB` impostazioni locali. |
| `en-in`                     | Immagine del contenitore con le `en-IN` impostazioni locali. |
| `en-nz`                     | Immagine del contenitore con le `en-NZ` impostazioni locali. |
| `en-us`                     | Immagine del contenitore con le `en-US` impostazioni locali. |
| `es-es`                     | Immagine del contenitore con le `es-ES` impostazioni locali. |
| `es-mx`                     | Immagine del contenitore con le `es-MX` impostazioni locali. |
| `fi-fi`                     | Immagine del contenitore con le `fi-FI` impostazioni locali. |
| `fr-ca`                     | Immagine del contenitore con le `fr-CA` impostazioni locali. |
| `fr-fr`                     | Immagine del contenitore con le `fr-FR` impostazioni locali. |
| `gu-in`                     | Immagine del contenitore con le `gu-IN` impostazioni locali. |
| `hi-in`                     | Immagine del contenitore con le `hi-IN` impostazioni locali. |
| `it-it`                     | Immagine del contenitore con le `it-IT` impostazioni locali. |
| `ja-jp`                     | Immagine del contenitore con le `ja-JP` impostazioni locali. |
| `ko-kr`                     | Immagine del contenitore con le `ko-KR` impostazioni locali. |
| `mr-in`                     | Immagine del contenitore con le `mr-IN` impostazioni locali. |
| `nb-no`                     | Immagine del contenitore con le `nb-NO` impostazioni locali. |
| `nl-nl`                     | Immagine del contenitore con le `nl-NL` impostazioni locali. |
| `pl-pl`                     | Immagine del contenitore con le `pl-PL` impostazioni locali. |
| `pt-br`                     | Immagine del contenitore con le `pt-BR` impostazioni locali. |
| `pt-pt`                     | Immagine del contenitore con le `pt-PT` impostazioni locali. |
| `ru-ru`                     | Immagine del contenitore con le `ru-RU` impostazioni locali. |
| `sv-se`                     | Immagine del contenitore con le `sv-SE` impostazioni locali. |
| `ta-in`                     | Immagine del contenitore con le `ta-IN` impostazioni locali. |
| `te-in`                     | Immagine del contenitore con le `te-IN` impostazioni locali. |
| `th-th`                     | Immagine del contenitore con le `th-TH` impostazioni locali. |
| `tr-tr`                     | Immagine del contenitore con le `tr-TR` impostazioni locali. |
| `zh-cn`                     | Immagine del contenitore con le `zh-CN` impostazioni locali. |
| `zh-hk`                     | Immagine del contenitore con le `zh-HK` impostazioni locali. |
| `zh-tw`                     | Immagine del contenitore con le `zh-TW` impostazioni locali. |

---

## <a name="text-to-speech"></a>Sintesi vocale

L'immagine del contenitore [da sintesi vocale][sp-tts] è disponibile nel `mcr.microsoft.com` Syndicate del registro contenitori. Si trova all'interno del `azure-cognitive-services/speechservices/` repository ed è denominato `text-to-speech` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` .

Questa immagine del contenitore include i seguenti tag disponibili. È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/text-to-speech/tags/list)versione.


# <a name="latest-version"></a>[Versione più recente](#tab/current)

Nota sulla versione per `1.8.0-amd64-<locale-and-voice>` :

**Funzionalità**
* Migrazione completa a .NET 3,1

| Tag immagine                                  | Note                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `latest`                                    | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-AriaRUS` voce.                                            | 
| `1.8.0-amd64-<locale-and-voice>`            | Sostituire `<locale>` con una delle impostazioni locali disponibili, elencate di seguito. Ad esempio `1.8.0-amd64-en-us-ariarus`.  |


| Impostazioni locali per v 1.8.0                          | Note                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | Immagine del contenitore con le `ar-EG` impostazioni locali e la `ar-EG-Hoda` voce.            |
| `ar-sa-naayf`                               | Immagine del contenitore con le `ar-SA` impostazioni locali e la `ar-SA-Naayf` voce.           |
| `bg-bg-ivan`                                | Immagine del contenitore con le `bg-BG` impostazioni locali e la `bg-BG-Ivan` voce.            |
| `ca-es-herenarus`                           | Immagine del contenitore con le `ca-ES` impostazioni locali e la `ca-ES-HerenaRUS` voce.       |
| `cs-cz-jakub`                               | Immagine del contenitore con le `cs-CZ` impostazioni locali e la `cs-CZ-Jakub` voce.           |
| `da-dk-hellerus`                            | Immagine del contenitore con le `da-DK` impostazioni locali e la `da-DK-HelleRUS` voce.        |
| `de-at-michael`                             | Immagine del contenitore con le `de-AT` impostazioni locali e la `de-AT-Michael` voce.         |
| `de-ch-karsten`                             | Immagine del contenitore con le `de-CH` impostazioni locali e la `de-CH-Karsten` voce.         |
| `de-de-hedda`                               | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-Hedda` voce.           |
| `de-de-heddarus`                            | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-Hedda` voce.           |
| `de-de-stefan-apollo`                       | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-Stefan-Apollo` voce.   |
| `el-gr-stefanos`                            | Immagine del contenitore con le `el-GR` impostazioni locali e la `el-GR-Stefanos` voce.        |
| `en-au-catherine`                           | Immagine del contenitore con le `en-AU` impostazioni locali e la `en-AU-Catherine` voce.       |
| `en-au-hayleyrus`                           | Immagine del contenitore con le `en-AU` impostazioni locali e la `en-AU-HayleyRUS` voce.       |
| `en-ca-heatherrus`                          | Immagine del contenitore con le `en-CA` impostazioni locali e la `en-CA-HeatherRUS` voce.      |
| `en-ca-linda`                               | Immagine del contenitore con le `en-CA` impostazioni locali e la `en-CA-Linda` voce.           |
| `en-gb-george-apollo`                       | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-George-Apollo` voce.   |
| `en-gb-hazelrus`                            | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-HazelRUS` voce.        |
| `en-gb-susan-apollo`                        | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-Susan-Apollo` voce.    |
| `en-ie-sean`                                | Immagine del contenitore con le `en-IE` impostazioni locali e la `en-IE-Sean` voce.            |
| `en-in-heera-apollo`                        | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-Heera-Apollo` voce.    |
| `en-in-priyarus`                            | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-PriyaRUS` voce.        |
| `en-in-ravi-apollo`                         | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-Ravi-Apollo` voce.     |
| `en-us-benjaminrus`                         | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-BenjaminRUS` voce.     |
| `en-us-guy24krus`                           | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-Guy24kRUS` voce.       |
| `en-us-aria24krus`                          | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-Aria24kRUS` voce.      |
| `en-us-ariarus`                             | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-AriaRUS` voce.         |
| `en-us-zirarus`                             | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-ZiraRUS` voce.         |
| `es-es-helenarus`                           | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-HelenaRUS` voce.       |
| `es-es-laura-apollo`                        | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-Laura-Apollo` voce.    |
| `es-es-pablo-apollo`                        | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-Pablo-Apollo` voce.    |
| `es-mx-hildarus`                            | Immagine del contenitore con le `es-MX` impostazioni locali e la `es-MX-HildaRUS` voce.        |
| `es-mx-raul-apollo`                         | Immagine del contenitore con le `es-MX` impostazioni locali e la `es-MX-Raul-Apollo` voce.     |
| `fi-fi-heidirus`                            | Immagine del contenitore con le `fi-FI` impostazioni locali e la `fi-FI-HeidiRUS` voce.        |
| `fr-ca-caroline`                            | Immagine del contenitore con le `fr-CA` impostazioni locali e la `fr-CA-Caroline` voce.        |
| `fr-ca-harmonierus`                         | Immagine del contenitore con le `fr-CA` impostazioni locali e la `fr-CA-HarmonieRUS` voce.     |
| `fr-ch-guillaume`                           | Immagine del contenitore con le `fr-CH` impostazioni locali e la `fr-CH-Guillaume` voce.       |
| `fr-fr-hortenserus`                         | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-HortenseRUS` voce.     |
| `fr-fr-julie-apollo`                        | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-Julie-Apollo` voce.    |
| `fr-fr-paul-apollo`                         | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-Paul-Apollo` voce.     |
| `he-il-asaf`                                | Immagine del contenitore con le `he-IL` impostazioni locali e la `he-IL-Asaf` voce.            |
| `hi-in-hemant`                              | Immagine del contenitore con le `hi-IN` impostazioni locali e la `hi-IN-Hemant` voce.          |
| `hi-in-kalpana-apollo`                      | Immagine del contenitore con le `hi-IN` impostazioni locali e la `hi-IN-Kalpana-Apollo` voce.  |
| `hi-in-kalpana`                             | Immagine del contenitore con le `hi-IN` impostazioni locali e la `hi-IN-Kalpana` voce.         |
| `hr-hr-matej`                               | Immagine del contenitore con le `hr-HR` impostazioni locali e la `hr-HR-Matej` voce.           |
| `hu-hu-szabolcs`                            | Immagine del contenitore con le `hu-HU` impostazioni locali e la `hu-HU-Szabolcs` voce.        |
| `id-id-andika`                              | Immagine del contenitore con le `id-ID` impostazioni locali e la `id-ID-Andika` voce.          |
| `it-it-cosimo-apollo`                       | Immagine del contenitore con le `it-IT` impostazioni locali e la `it-IT-Cosimo-Apollo` voce.   |
| `it-it-luciarus`                            | Immagine del contenitore con le `it-IT` impostazioni locali e la `it-IT-LuciaRUS` voce.        |
| `ja-jp-ayumi-apollo`                        | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-Ayumi-Apollo` voce.    |
| `ja-jp-harukarus`                           | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-HarukaRUS` voce.       |
| `ja-jp-ichiro-apollo`                       | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-Ichiro-Apollo` voce.   |
| `ko-kr-heamirus`                            | Immagine del contenitore con le `ko-KR` impostazioni locali e la `ko-KR-HeamiRUS` voce.        |
| `ms-my-rizwan`                              | Immagine del contenitore con le `ms-MY` impostazioni locali e la `ms-MY-Rizwan` voce.          |
| `nb-no-huldarus`                            | Immagine del contenitore con le `nb-NO` impostazioni locali e la `nb-NO-HuldaRUS` voce.        |
| `nl-nl-hannarus`                            | Immagine del contenitore con le `nl-NL` impostazioni locali e la `nl-NL-HannaRUS` voce.        |
| `pl-pl-paulinarus`                          | Immagine del contenitore con le `pl-PL` impostazioni locali e la `pl-PL-PaulinaRUS` voce.      |
| `pt-br-daniel-apollo`                       | Immagine del contenitore con le `pt-BR` impostazioni locali e la `pt-BR-Daniel-Apollo` voce.   |
| `pt-br-heloisarus`                          | Immagine del contenitore con le `pt-BR` impostazioni locali e la `pt-BR-HeloisaRUS` voce.      |
| `pt-pt-heliarus`                            | Immagine del contenitore con le `pt-PT` impostazioni locali e la `pt-PT-HeliaRUS` voce.        |
| `ro-ro-andrei`                              | Immagine del contenitore con le `ro-RO` impostazioni locali e la `ro-RO-Andrei` voce.          |
| `ru-ru-ekaterinarus`                        | Immagine del contenitore con le `ru-RU` impostazioni locali e la `ru-RU-EkaterinaRUS` voce.    |
| `ru-ru-irina-apollo`                        | Immagine del contenitore con le `ru-RU` impostazioni locali e la `ru-RU-Irina-Apollo` voce.    |
| `ru-ru-pavel-apollo`                        | Immagine del contenitore con le `ru-RU` impostazioni locali e la `ru-RU-Pavel-Apollo` voce.    |
| `sk-sk-filip`                               | Immagine del contenitore con le `sk-SK` impostazioni locali e la `sk-SK-Filip` voce.           |
| `sl-si-lado`                                | Immagine del contenitore con le `sl-SI` impostazioni locali e la `sl-SI-Lado` voce.            |
| `sv-se-hedvigrus`                           | Immagine del contenitore con le `sv-SE` impostazioni locali e la `sv-SE-HedvigRUS` voce.       |
| `ta-in-valluvar`                            | Immagine del contenitore con le `ta-IN` impostazioni locali e la `ta-IN-Valluvar` voce.        |
| `te-in-chitra`                              | Immagine del contenitore con le `te-IN` impostazioni locali e la `te-IN-Chitra` voce.          |
| `th-th-pattara`                             | Immagine del contenitore con le `th-TH` impostazioni locali e la `th-TH-Pattara` voce.         |
| `tr-tr-sedarus`                             | Immagine del contenitore con le `tr-TR` impostazioni locali e la `tr-TR-SedaRUS` voce.         |
| `vi-vn-an`                                  | Immagine del contenitore con le `vi-VN` impostazioni locali e la `vi-VN-An` voce.              |
| `zh-cn-huihuirus`                           | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-HuihuiRUS` voce.       |
| `zh-cn-kangkang-apollo`                     | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-Kangkang-Apollo` voce. |
| `zh-cn-yaoyao-apollo`                       | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-Yaoyao-Apollo` voce.   |
| `zh-hk-danny-apollo`                        | Immagine del contenitore con le `zh-HK` impostazioni locali e la `zh-HK-Danny-Apollo` voce.    |
| `zh-hk-tracy-apollo`                        | Immagine del contenitore con le `zh-HK` impostazioni locali e la `zh-HK-Tracy-Apollo` voce.    |
| `zh-hk-tracyrus`                            | Immagine del contenitore con le `zh-HK` impostazioni locali e la `zh-HK-TracyRUS` voce.        |
| `zh-tw-hanhanrus`                           | Immagine del contenitore con le `zh-TW` impostazioni locali e la `zh-TW-HanHanRUS` voce.       |
| `zh-tw-yating-apollo`                       | Immagine del contenitore con le `zh-TW` impostazioni locali e la `zh-TW-Yating-Apollo` voce.   |
| `zh-tw-zhiwei-apollo`                       | Immagine del contenitore con le `zh-TW` impostazioni locali e la `zh-TW-Zhiwei-Apollo` voce.   |


# <a name="previous-version"></a>[Versione precedente](#tab/previous)

Nota sulla versione per `1.7.0-amd64-<locale-and-voice>` :

**Funzionalità**
* Componenti aggiornati a .NET 3,1

| Tag immagine                                  | Note                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `1.7.0-amd64-<locale-and-voice>`            | 1 ° versione GA. Sostituire `<locale>` con una delle impostazioni locali disponibili, elencate di seguito. Ad esempio `1.7.0-amd64-en-us-ariarus`.  |


| Impostazioni locali per v 1.7.0                          | Note                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | Immagine del contenitore con le `ar-EG` impostazioni locali e la `ar-EG-Hoda` voce.            |
| `ar-sa-naayf`                               | Immagine del contenitore con le `ar-SA` impostazioni locali e la `ar-SA-Naayf` voce.           |
| `bg-bg-ivan`                                | Immagine del contenitore con le `bg-BG` impostazioni locali e la `bg-BG-Ivan` voce.            |
| `ca-es-herenarus`                           | Immagine del contenitore con le `ca-ES` impostazioni locali e la `ca-ES-HerenaRUS` voce.       |
| `cs-cz-jakub`                               | Immagine del contenitore con le `cs-CZ` impostazioni locali e la `cs-CZ-Jakub` voce.           |
| `da-dk-hellerus`                            | Immagine del contenitore con le `da-DK` impostazioni locali e la `da-DK-HelleRUS` voce.        |
| `de-at-michael`                             | Immagine del contenitore con le `de-AT` impostazioni locali e la `de-AT-Michael` voce.         |
| `de-ch-karsten`                             | Immagine del contenitore con le `de-CH` impostazioni locali e la `de-CH-Karsten` voce.         |
| `de-de-hedda`                               | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-Hedda` voce.           |
| `de-de-heddarus`                            | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-Hedda` voce.           |
| `de-de-stefan-apollo`                       | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-Stefan-Apollo` voce.   |
| `el-gr-stefanos`                            | Immagine del contenitore con le `el-GR` impostazioni locali e la `el-GR-Stefanos` voce.        |
| `en-au-catherine`                           | Immagine del contenitore con le `en-AU` impostazioni locali e la `en-AU-Catherine` voce.       |
| `en-au-hayleyrus`                           | Immagine del contenitore con le `en-AU` impostazioni locali e la `en-AU-HayleyRUS` voce.       |
| `en-ca-heatherrus`                          | Immagine del contenitore con le `en-CA` impostazioni locali e la `en-CA-HeatherRUS` voce.      |
| `en-ca-linda`                               | Immagine del contenitore con le `en-CA` impostazioni locali e la `en-CA-Linda` voce.           |
| `en-gb-george-apollo`                       | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-George-Apollo` voce.   |
| `en-gb-hazelrus`                            | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-HazelRUS` voce.        |
| `en-gb-susan-apollo`                        | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-Susan-Apollo` voce.    |
| `en-ie-sean`                                | Immagine del contenitore con le `en-IE` impostazioni locali e la `en-IE-Sean` voce.            |
| `en-in-heera-apollo`                        | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-Heera-Apollo` voce.    |
| `en-in-priyarus`                            | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-PriyaRUS` voce.        |
| `en-in-ravi-apollo`                         | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-Ravi-Apollo` voce.     |
| `en-us-benjaminrus`                         | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-BenjaminRUS` voce.     |
| `en-us-guy24krus`                           | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-Guy24kRUS` voce.       |
| `en-us-aria24krus`                          | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-Aria24kRUS` voce.      |
| `en-us-ariarus`                             | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-AriaRUS` voce.         |
| `en-us-zirarus`                             | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-ZiraRUS` voce.         |
| `es-es-helenarus`                           | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-HelenaRUS` voce.       |
| `es-es-laura-apollo`                        | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-Laura-Apollo` voce.    |
| `es-es-pablo-apollo`                        | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-Pablo-Apollo` voce.    |
| `es-mx-hildarus`                            | Immagine del contenitore con le `es-MX` impostazioni locali e la `es-MX-HildaRUS` voce.        |
| `es-mx-raul-apollo`                         | Immagine del contenitore con le `es-MX` impostazioni locali e la `es-MX-Raul-Apollo` voce.     |
| `fi-fi-heidirus`                            | Immagine del contenitore con le `fi-FI` impostazioni locali e la `fi-FI-HeidiRUS` voce.        |
| `fr-ca-caroline`                            | Immagine del contenitore con le `fr-CA` impostazioni locali e la `fr-CA-Caroline` voce.        |
| `fr-ca-harmonierus`                         | Immagine del contenitore con le `fr-CA` impostazioni locali e la `fr-CA-HarmonieRUS` voce.     |
| `fr-ch-guillaume`                           | Immagine del contenitore con le `fr-CH` impostazioni locali e la `fr-CH-Guillaume` voce.       |
| `fr-fr-hortenserus`                         | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-HortenseRUS` voce.     |
| `fr-fr-julie-apollo`                        | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-Julie-Apollo` voce.    |
| `fr-fr-paul-apollo`                         | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-Paul-Apollo` voce.     |
| `he-il-asaf`                                | Immagine del contenitore con le `he-IL` impostazioni locali e la `he-IL-Asaf` voce.            |
| `hi-in-hemant`                              | Immagine del contenitore con le `hi-IN` impostazioni locali e la `hi-IN-Hemant` voce.          |
| `hi-in-kalpana-apollo`                      | Immagine del contenitore con le `hi-IN` impostazioni locali e la `hi-IN-Kalpana-Apollo` voce.  |
| `hi-in-kalpana`                             | Immagine del contenitore con le `hi-IN` impostazioni locali e la `hi-IN-Kalpana` voce.         |
| `hr-hr-matej`                               | Immagine del contenitore con le `hr-HR` impostazioni locali e la `hr-HR-Matej` voce.           |
| `hu-hu-szabolcs`                            | Immagine del contenitore con le `hu-HU` impostazioni locali e la `hu-HU-Szabolcs` voce.        |
| `id-id-andika`                              | Immagine del contenitore con le `id-ID` impostazioni locali e la `id-ID-Andika` voce.          |
| `it-it-cosimo-apollo`                       | Immagine del contenitore con le `it-IT` impostazioni locali e la `it-IT-Cosimo-Apollo` voce.   |
| `it-it-luciarus`                            | Immagine del contenitore con le `it-IT` impostazioni locali e la `it-IT-LuciaRUS` voce.        |
| `ja-jp-ayumi-apollo`                        | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-Ayumi-Apollo` voce.    |
| `ja-jp-harukarus`                           | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-HarukaRUS` voce.       |
| `ja-jp-ichiro-apollo`                       | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-Ichiro-Apollo` voce.   |
| `ko-kr-heamirus`                            | Immagine del contenitore con le `ko-KR` impostazioni locali e la `ko-KR-HeamiRUS` voce.        |
| `ms-my-rizwan`                              | Immagine del contenitore con le `ms-MY` impostazioni locali e la `ms-MY-Rizwan` voce.          |
| `nb-no-huldarus`                            | Immagine del contenitore con le `nb-NO` impostazioni locali e la `nb-NO-HuldaRUS` voce.        |
| `nl-nl-hannarus`                            | Immagine del contenitore con le `nl-NL` impostazioni locali e la `nl-NL-HannaRUS` voce.        |
| `pl-pl-paulinarus`                          | Immagine del contenitore con le `pl-PL` impostazioni locali e la `pl-PL-PaulinaRUS` voce.      |
| `pt-br-daniel-apollo`                       | Immagine del contenitore con le `pt-BR` impostazioni locali e la `pt-BR-Daniel-Apollo` voce.   |
| `pt-br-heloisarus`                          | Immagine del contenitore con le `pt-BR` impostazioni locali e la `pt-BR-HeloisaRUS` voce.      |
| `pt-pt-heliarus`                            | Immagine del contenitore con le `pt-PT` impostazioni locali e la `pt-PT-HeliaRUS` voce.        |
| `ro-ro-andrei`                              | Immagine del contenitore con le `ro-RO` impostazioni locali e la `ro-RO-Andrei` voce.          |
| `ru-ru-ekaterinarus`                        | Immagine del contenitore con le `ru-RU` impostazioni locali e la `ru-RU-EkaterinaRUS` voce.    |
| `ru-ru-irina-apollo`                        | Immagine del contenitore con le `ru-RU` impostazioni locali e la `ru-RU-Irina-Apollo` voce.    |
| `ru-ru-pavel-apollo`                        | Immagine del contenitore con le `ru-RU` impostazioni locali e la `ru-RU-Pavel-Apollo` voce.    |
| `sk-sk-filip`                               | Immagine del contenitore con le `sk-SK` impostazioni locali e la `sk-SK-Filip` voce.           |
| `sl-si-lado`                                | Immagine del contenitore con le `sl-SI` impostazioni locali e la `sl-SI-Lado` voce.            |
| `sv-se-hedvigrus`                           | Immagine del contenitore con le `sv-SE` impostazioni locali e la `sv-SE-HedvigRUS` voce.       |
| `ta-in-valluvar`                            | Immagine del contenitore con le `ta-IN` impostazioni locali e la `ta-IN-Valluvar` voce.        |
| `te-in-chitra`                              | Immagine del contenitore con le `te-IN` impostazioni locali e la `te-IN-Chitra` voce.          |
| `th-th-pattara`                             | Immagine del contenitore con le `th-TH` impostazioni locali e la `th-TH-Pattara` voce.         |
| `tr-tr-sedarus`                             | Immagine del contenitore con le `tr-TR` impostazioni locali e la `tr-TR-SedaRUS` voce.         |
| `vi-vn-an`                                  | Immagine del contenitore con le `vi-VN` impostazioni locali e la `vi-VN-An` voce.              |
| `zh-cn-huihuirus`                           | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-HuihuiRUS` voce.       |
| `zh-cn-kangkang-apollo`                     | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-Kangkang-Apollo` voce. |
| `zh-cn-yaoyao-apollo`                       | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-Yaoyao-Apollo` voce.   |
| `zh-hk-danny-apollo`                        | Immagine del contenitore con le `zh-HK` impostazioni locali e la `zh-HK-Danny-Apollo` voce.    |
| `zh-hk-tracy-apollo`                        | Immagine del contenitore con le `zh-HK` impostazioni locali e la `zh-HK-Tracy-Apollo` voce.    |
| `zh-hk-tracyrus`                            | Immagine del contenitore con le `zh-HK` impostazioni locali e la `zh-HK-TracyRUS` voce.        |
| `zh-tw-hanhanrus`                           | Immagine del contenitore con le `zh-TW` impostazioni locali e la `zh-TW-HanHanRUS` voce.       |
| `zh-tw-yating-apollo`                       | Immagine del contenitore con le `zh-TW` impostazioni locali e la `zh-TW-Yating-Apollo` voce.   |
| `zh-tw-zhiwei-apollo`                       | Immagine del contenitore con le `zh-TW` impostazioni locali e la `zh-TW-Zhiwei-Apollo` voce.   |

---

## <a name="neural-text-to-speech"></a>Sintesi vocale neurale

L'immagine del contenitore [di sintesi vocale neurale][sp-ntts] è disponibile nel Syndicate del `mcr.microsoft.com` Registro contenitori. Si trova all'interno del `azure-cognitive-services/speechservices/` repository ed è denominato `neural-text-to-speech` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` .

Questa immagine del contenitore include i seguenti tag disponibili. È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/neural-text-to-speech/tags/list)versione.


# <a name="latest-version"></a>[Versione più recente](#tab/current)

Note sulla versione per `v1.3.0` :
* Il contenitore da testo a voce neurale è ora disponibile a livello generale. 

| Tag immagine                                  | Note                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-AriaNeural` voce.      |
| `1.3.0-amd64-<locale-and-voice>`    | Sostituire `<locale>` con una delle impostazioni locali disponibili, elencate di seguito. Ad esempio `1.3.0-amd64-en-us-arianeural`. |


| v 1.3.0 impostazioni locali e voci           | Note                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `de-de-katjaneural`                 | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-KatjaNeural` voce.     |
| `en-au-natashaneural`               | Immagine del contenitore con le `en-AU` impostazioni locali e la `en-AU-NatashaNeural` voce.   |
| `en-ca-claraneural`                 | Immagine del contenitore con le `en-CA` impostazioni locali e la `en-CA-ClaraNeural` voce.     |
| `en-gb-libbyneural`                 | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-LibbyNeural` voce.     |
| `en-gb-mianeural`                   | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-MiaNeural` voce.       |
| `en-us-arianeural`                  | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-AriaNeural` voce.      |
| `en-us-guyneural`                   | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-GuyNeural` voce.       |
| `es-es-elviraneural`                | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-ElviraNeural` voce.    |
| `es-mx-dalianeural`                 | Immagine del contenitore con le `es-MX` impostazioni locali e la `es-MX-DaliaNeural` voce.     |
| `fr-ca-sylvieneural`                | Immagine del contenitore con le `fr-CA` impostazioni locali e la `fr-CA-SylvieNeural` voce.    |
| `fr-fr-deniseneural`                | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-DeniseNeural` voce.    |
| `it-it-elsaneural`                  | Immagine del contenitore con le `it-IT` impostazioni locali e la `it-IT-ElsaNeural` voce.      |
| `ja-jp-nanamineural`                | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-NanamiNeural` voce.    |
| `ko-kr-sunhineural`                 | Immagine del contenitore con le `ko-KR` impostazioni locali e la `ko-KR-SunHiNeural` voce.     |
| `pt-br-franciscaneural`             | Immagine del contenitore con le `pt-BR` impostazioni locali e la `pt-BR-FranciscaNeural` voce. |
| `zh-cn-xiaoxiaoneural`              | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-XiaoxiaoNeural` voce.  |

# <a name="previous-version"></a>[Versione precedente](#tab/previous)

| Tag immagine                                  | Note                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-AriaNeural` voce.      |
| `1.2.0-amd64-<locale-and-voice>-preview`    | Sostituire `<locale>` con una delle impostazioni locali disponibili, elencate di seguito. Ad esempio `1.2.0-amd64-en-us-arianeural-preview`. |


| v 1.2.0 Anteprima impostazioni locali e voci           | Note                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-AriaNeural` voce.      |
| `de-de-katjaneural-preview`                 | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-KatjaNeural` voce.     |
| `en-au-natashaneural-preview`               | Immagine del contenitore con le `en-AU` impostazioni locali e la `en-AU-NatashaNeural` voce.   |
| `en-ca-claraneural-preview`                 | Immagine del contenitore con le `en-CA` impostazioni locali e la `en-CA-ClaraNeural` voce.     |
| `en-gb-libbyneural-preview`                 | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-LibbyNeural` voce.     |
| `en-gb-mianeural-preview`                   | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-MiaNeural` voce.       |
| `en-us-arianeural-preview`                  | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-AriaNeural` voce.      |
| `en-us-guyneural-preview`                   | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-GuyNeural` voce.       |
| `es-es-elviraneural-preview`                | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-ElviraNeural` voce.    |
| `es-mx-dalianeural-preview`                 | Immagine del contenitore con le `es-MX` impostazioni locali e la `es-MX-DaliaNeural` voce.     |
| `fr-ca-sylvieneural-preview`                | Immagine del contenitore con le `fr-CA` impostazioni locali e la `fr-CA-SylvieNeural` voce.    |
| `fr-fr-deniseneural-preview`                | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-DeniseNeural` voce.    |
| `it-it-elsaneural-preview`                  | Immagine del contenitore con le `it-IT` impostazioni locali e la `it-IT-ElsaNeural` voce.      |
| `ja-jp-nanamineural-preview`                | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-NanamiNeural` voce.    |
| `ko-kr-sunhineural-preview`                 | Immagine del contenitore con le `ko-KR` impostazioni locali e la `ko-KR-SunHiNeural` voce.     |
| `pt-br-franciscaneural-preview`             | Immagine del contenitore con le `pt-BR` impostazioni locali e la `pt-BR-FranciscaNeural` voce. |
| `zh-cn-xiaoxiaoneural-preview`              | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-XiaoxiaoNeural` voce.  |

---

## <a name="speech-language-detection"></a>Rilevamento della lingua parlata

L'immagine del contenitore del [rilevamento della lingua vocale][sp-lid] è reperibile nel `mcr.microsoft.com` Syndicate del registro contenitori. Si trova all'interno del `azure-cognitive-services/speechservices/` repository ed è denominato `language-detection` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` .

Questa immagine del contenitore include i seguenti tag disponibili. È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/language-detection/tags/list)versione.

| Tag immagine                                  | Note                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                       |      |
| `1.1.0-amd64-preview`                       |      |

## <a name="key-phrase-extraction"></a>Estrazione frasi chiave

è possibile trovare l'immagine del contenitore nel `mcr.microsoft.com` Syndicate del registro contenitori. Si trova all'interno del `azure-cognitive-services/textanalytics/` repository ed è denominato `keyphrase` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` .

Questa immagine del contenitore include i seguenti tag disponibili. È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/keyphrase/tags/list)versione.

# <a name="latest-version"></a>[Versione più recente](#tab/current)


| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` |       |

# <a name="previous-versions"></a>[Versioni precedenti](#tab/previous)

| Tag immagine                    | Note |
|-------------------------------|:------|
| `1.1.012840001-amd64` |       |
| `1.1.012830001-amd64`    |       |

---

## <a name="text-language-detection"></a>Rilevamento della lingua del testo

Il [rilevamento lingua][ta-la] immagine del contenitore è disponibile nel `mcr.microsoft.com` Syndicate del registro contenitori. Si trova all'interno del `azure-cognitive-services/textanalytics/` repository ed è denominato `language` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/textanalytics/language`


Questa immagine del contenitore include i seguenti tag disponibili. È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/language/tags/list)versione.

# <a name="latest-versions"></a>[Versioni più recenti](#tab/current)

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` | |
   

# <a name="previous-versions"></a>[Versioni precedenti](#tab/previous)


| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012840001-amd64` |   |
| `1.1.012830001-amd64` |   |

---

## <a name="sentiment-analysis"></a>Analisi del sentiment

Il [analisi del sentiment][ta-se] immagine del contenitore è disponibile nel `mcr.microsoft.com` Syndicate del registro contenitori. Si trova all'interno del `azure-cognitive-services/textanalytics/` repository ed è denominato `sentiment` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment`

Questa immagine del contenitore include i seguenti tag disponibili. È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/sentiment/tags/list)versione.

| Tag immagine | Note                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0-en`   | Analisi del sentiment V3 (Inglese)               |
| `3.0-es`   | Analisi del sentiment V3 (Spagnolo)               |
| `3.0-fr`   | Analisi del sentiment V3 (Francese)                |
| `3.0-it`   | Analisi del sentiment V3 (Italiano)               |
| `3.0-de`   | Analisi del sentiment V3 (tedesco)                |
| `3.0-zh`   | Analisi del sentiment V3 (cinese semplificato)  |
| `3.0-zht`  | Analisi del sentiment V3 (cinese tradizionale) |
| `3.0-ja`   | Analisi del sentiment V3 (giapponese)              |
| `3.0-pt`   | Analisi del sentiment V3 (Portoghese)            |
| `3.0-nl`   | Analisi del sentiment V3 (Olandese)                 |
| `2.1`    | Analisi del sentiment v2      |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[sp-ntts]: ../speech-service/speech-container-howto.md?tabs=ntts
[sp-lid]: ../speech-service/speech-container-howto.md?tabs=lid
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
