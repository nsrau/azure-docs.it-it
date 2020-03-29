---
title: Tag delle immagini di contenitori in Servizi cognitivi
titleSuffix: Azure Cognitive Services
description: Elenco completo di tutti i tag di immagine del contenitore del servizio cognitivo.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: eed2223dbfeee307b552cdd010530f27c379f5fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219445"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Tag immagine del contenitore di Servizi cognitivi di AzureAzure Cognitive Services container image tags

Servizi cognitivi di Azure offre molte immagini di contenitori. I registri contenitore e i repository corrispondenti variano tra le immagini del contenitore. Ogni nome di immagine contenitore offre più tag. Un tag immagine contenitore è un meccanismo di controllo delle versioni dell'immagine contenitore. Questo articolo deve essere usato come riferimento completo per elencare tutte le immagini del contenitore di Servizi cognitivi e i relativi tag disponibili.

> [!TIP]
> Quando [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)si utilizza , prestare particolare attenzione alla combinazione di maiuscole e minuscole del registro contenitori, del repository, del nome dell'immagine del contenitore e del tag corrispondente, in quanto fanno **distinzione tra maiuscole e minuscole.**

## <a name="anomaly-detector"></a>Rilevamento anomalie

L'immagine del contenitore [Anomaly Detector][ad-containers] è disponibile nel Registro di sistema del `containerpreview.azurecr.io` contenitore. Si trova all'interno `microsoft` del `cognitive-services-anomaly-detector`repository e si chiama . Il nome completo dell'immagine del contenitore è, `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`.

Questa immagine contenitore ha i seguenti tag disponibili:

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008500001-amd64-preview` |       |

## <a name="computer-vision"></a>Visione artificiale

L'immagine del contenitore Visione `containerpreview.azurecr.io` [artificiale][cv-containers] è disponibile nel Registro di sistema del contenitore. Si trova all'interno `microsoft` del `cognitive-services-read`repository e si chiama . Il nome completo dell'immagine del contenitore è, `containerpreview.azurecr.io/microsoft/cognitive-services-read`.

Questa immagine contenitore ha i seguenti tag disponibili:

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Viso

L'immagine del contenitore `containerpreview.azurecr.io` [Face][fa-containers] è disponibile nel registro contenitori. Si trova all'interno `microsoft` del `cognitive-services-face`repository e si chiama . Il nome completo dell'immagine del contenitore è, `containerpreview.azurecr.io/microsoft/cognitive-services-face`.

Questa immagine contenitore ha i seguenti tag disponibili:

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |
| `1.1.006490002-amd64-preview` |       |
| `1.0.005940002-amd64-preview` |       |
| `1.0.005550001-amd64-preview` |       |

## <a name="form-recognizer"></a>Riconoscimento modulo

L'immagine del contenitore Form `containerpreview.azurecr.io` [Recognizer][fr-containers] è disponibile nel Registro di sistema del contenitore. Si trova all'interno `microsoft` del `cognitive-services-form-recognizer`repository e si chiama . Il nome completo dell'immagine del contenitore è, `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`.

Questa immagine contenitore ha i seguenti tag disponibili:

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

L'immagine del contenitore [LUIS][lu-containers] è disponibile nel sindacato del Registro di sistema del `mcr.microsoft.com` contenitore. Si trova all'interno `azure-cognitive-services` del `luis`repository e si chiama . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/luis`.

Questa immagine contenitore ha i seguenti tag disponibili:

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.010330004-amd64-preview` |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.008010002-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.007020001-amd64-preview` |       |

## <a name="custom-speech-to-text"></a>Sintesi vocale personalizzata

L'immagine personalizzata del contenitore [Speech-to-text][sp-cstt] è disponibile nel Registro di sistema del `containerpreview.azurecr.io` contenitore. Si trova all'interno `microsoft` del `cognitive-services-custom-speech-to-text`repository e si chiama . Il nome completo dell'immagine del contenitore è, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`.

Questa immagine contenitore ha i seguenti tag disponibili:

| Tag immagine            | Note |
|-----------------------|:------|
| `latest`              |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>Sintesi vocale personalizzata

L'immagine del contenitore [Custom Text-to-speech][sp-ctts] è disponibile nel Registro di sistema del `containerpreview.azurecr.io` contenitore. Si trova all'interno `microsoft` del `cognitive-services-custom-text-to-speech`repository e si chiama . Il nome completo dell'immagine del contenitore è, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`.

Questa immagine contenitore ha i seguenti tag disponibili:

| Tag immagine            | Note |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>Riconoscimento vocale

L'immagine del contenitore [di sintesi][sp-stt] vocale è disponibile nel Registro di sistema del `containerpreview.azurecr.io` contenitore. Si trova all'interno `microsoft` del `cognitive-services-speech-to-text`repository e si chiama . Il nome completo dell'immagine del contenitore è, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`.

Questa immagine contenitore ha i seguenti tag disponibili:

| Tag immagine                  | Note                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | Immagine contenitore `en-US` con le impostazioni locali. |
| `2.1.1-amd64-ar-ae-preview` | Immagine contenitore `ar-AE` con le impostazioni locali. |
| `2.1.1-amd64-ar-eg-preview` | Immagine contenitore `ar-EG` con le impostazioni locali. |
| `2.1.1-amd64-ar-kw-preview` | Immagine contenitore `ar-KW` con le impostazioni locali. |
| `2.1.1-amd64-ar-qa-preview` | Immagine contenitore `ar-QA` con le impostazioni locali. |
| `2.1.1-amd64-ar-sa-preview` | Immagine contenitore `ar-SA` con le impostazioni locali. |
| `2.1.1-amd64-ca-es-preview` | Immagine contenitore `ca-ES` con le impostazioni locali. |
| `2.1.1-amd64-da-dk-preview` | Immagine contenitore `da-DK` con le impostazioni locali. |
| `2.1.1-amd64-de-de-preview` | Immagine contenitore `de-DE` con le impostazioni locali. |
| `2.1.1-amd64-en-au-preview` | Immagine contenitore `en-AU` con le impostazioni locali. |
| `2.1.1-amd64-en-ca-preview` | Immagine contenitore `en-CA` con le impostazioni locali. |
| `2.1.1-amd64-en-gb-preview` | Immagine contenitore `en-GB` con le impostazioni locali. |
| `2.1.1-amd64-en-in-preview` | Immagine contenitore `en-IN` con le impostazioni locali. |
| `2.1.1-amd64-en-nz-preview` | Immagine contenitore `en-NZ` con le impostazioni locali. |
| `2.1.1-amd64-en-us-preview` | Immagine contenitore `en-US` con le impostazioni locali. |
| `2.1.1-amd64-es-es-preview` | Immagine contenitore `es-ES` con le impostazioni locali. |
| `2.1.1-amd64-es-mx-preview` | Immagine contenitore `es-MX` con le impostazioni locali. |
| `2.1.1-amd64-fi-fi-preview` | Immagine contenitore `fi-FI` con le impostazioni locali. |
| `2.1.1-amd64-fr-ca-preview` | Immagine contenitore `fr-CA` con le impostazioni locali. |
| `2.1.1-amd64-fr-fr-preview` | Immagine contenitore `fr-FR` con le impostazioni locali. |
| `2.1.1-amd64-gu-in-preview` | Immagine contenitore `gu-IN` con le impostazioni locali. |
| `2.1.1-amd64-hi-in-preview` | Immagine contenitore `hi-IN` con le impostazioni locali. |
| `2.1.1-amd64-it-it-preview` | Immagine contenitore `it-IT` con le impostazioni locali. |
| `2.1.1-amd64-ja-jp-preview` | Immagine contenitore `ja-JP` con le impostazioni locali. |
| `2.1.1-amd64-ko-kr-preview` | Immagine contenitore `ko-KR` con le impostazioni locali. |
| `2.1.1-amd64-mr-in-preview` | Immagine contenitore `mr-IN` con le impostazioni locali. |
| `2.1.1-amd64-nb-no-preview` | Immagine contenitore `nb-NO` con le impostazioni locali. |
| `2.1.1-amd64-nl-nl-preview` | Immagine contenitore `nl-NL` con le impostazioni locali. |
| `2.1.1-amd64-pl-pl-preview` | Immagine contenitore `pl-PL` con le impostazioni locali. |
| `2.1.1-amd64-pt-br-preview` | Immagine contenitore `pt-BR` con le impostazioni locali. |
| `2.1.1-amd64-pt-pt-preview` | Immagine contenitore `pt-PT` con le impostazioni locali. |
| `2.1.1-amd64-ru-ru-preview` | Immagine contenitore `ru-RU` con le impostazioni locali. |
| `2.1.1-amd64-sv-se-preview` | Immagine contenitore `sv-SE` con le impostazioni locali. |
| `2.1.1-amd64-ta-in-preview` | Immagine contenitore `ta-IN` con le impostazioni locali. |
| `2.1.1-amd64-te-in-preview` | Immagine contenitore `te-IN` con le impostazioni locali. |
| `2.1.1-amd64-th-th-preview` | Immagine contenitore `th-TH` con le impostazioni locali. |
| `2.1.1-amd64-tr-tr-preview` | Immagine contenitore `tr-TR` con le impostazioni locali. |
| `2.1.1-amd64-zh-cn-preview` | Immagine contenitore `zh-CN` con le impostazioni locali. |
| `2.1.1-amd64-zh-hk-preview` | Immagine contenitore `zh-HK` con le impostazioni locali. |
| `2.1.1-amd64-zh-tw-preview` | Immagine contenitore `zh-TW` con le impostazioni locali. |
| `2.1.0-amd64-ar-ae-preview` | Immagine contenitore `ar-AE` con le impostazioni locali. |
| `2.1.0-amd64-ar-eg-preview` | Immagine contenitore `ar-EG` con le impostazioni locali. |
| `2.1.0-amd64-ar-kw-preview` | Immagine contenitore `ar-KW` con le impostazioni locali. |
| `2.1.0-amd64-ar-qa-preview` | Immagine contenitore `ar-QA` con le impostazioni locali. |
| `2.1.0-amd64-ar-sa-preview` | Immagine contenitore `ar-SA` con le impostazioni locali. |
| `2.1.0-amd64-ca-es-preview` | Immagine contenitore `ca-ES` con le impostazioni locali. |
| `2.1.0-amd64-da-dk-preview` | Immagine contenitore `da-DK` con le impostazioni locali. |
| `2.1.0-amd64-de-de-preview` | Immagine contenitore `de-DE` con le impostazioni locali. |
| `2.1.0-amd64-en-au-preview` | Immagine contenitore `en-AU` con le impostazioni locali. |
| `2.1.0-amd64-en-ca-preview` | Immagine contenitore `en-CA` con le impostazioni locali. |
| `2.1.0-amd64-en-gb-preview` | Immagine contenitore `en-GB` con le impostazioni locali. |
| `2.1.0-amd64-en-in-preview` | Immagine contenitore `en-IN` con le impostazioni locali. |
| `2.1.0-amd64-en-nz-preview` | Immagine contenitore `en-NZ` con le impostazioni locali. |
| `2.1.0-amd64-en-us-preview` | Immagine contenitore `en-US` con le impostazioni locali. |
| `2.1.0-amd64-es-es-preview` | Immagine contenitore `es-ES` con le impostazioni locali. |
| `2.1.0-amd64-es-mx-preview` | Immagine contenitore `es-MX` con le impostazioni locali. |
| `2.1.0-amd64-fi-fi-preview` | Immagine contenitore `fi-FI` con le impostazioni locali. |
| `2.1.0-amd64-fr-ca-preview` | Immagine contenitore `fr-CA` con le impostazioni locali. |
| `2.1.0-amd64-fr-fr-preview` | Immagine contenitore `fr-FR` con le impostazioni locali. |
| `2.1.0-amd64-gu-in-preview` | Immagine contenitore `gu-IN` con le impostazioni locali. |
| `2.1.0-amd64-hi-in-preview` | Immagine contenitore `hi-IN` con le impostazioni locali. |
| `2.1.0-amd64-it-it-preview` | Immagine contenitore `it-IT` con le impostazioni locali. |
| `2.1.0-amd64-ja-jp-preview` | Immagine contenitore `ja-JP` con le impostazioni locali. |
| `2.1.0-amd64-ko-kr-preview` | Immagine contenitore `ko-KR` con le impostazioni locali. |
| `2.1.0-amd64-mr-in-preview` | Immagine contenitore `mr-IN` con le impostazioni locali. |
| `2.1.0-amd64-nb-no-preview` | Immagine contenitore `nb-NO` con le impostazioni locali. |
| `2.1.0-amd64-nl-nl-preview` | Immagine contenitore `nl-NL` con le impostazioni locali. |
| `2.1.0-amd64-pl-pl-preview` | Immagine contenitore `pl-PL` con le impostazioni locali. |
| `2.1.0-amd64-pt-br-preview` | Immagine contenitore `pt-BR` con le impostazioni locali. |
| `2.1.0-amd64-pt-pt-preview` | Immagine contenitore `pt-PT` con le impostazioni locali. |
| `2.1.0-amd64-ru-ru-preview` | Immagine contenitore `ru-RU` con le impostazioni locali. |
| `2.1.0-amd64-sv-se-preview` | Immagine contenitore `sv-SE` con le impostazioni locali. |
| `2.1.0-amd64-ta-in-preview` | Immagine contenitore `ta-IN` con le impostazioni locali. |
| `2.1.0-amd64-te-in-preview` | Immagine contenitore `te-IN` con le impostazioni locali. |
| `2.1.0-amd64-th-th-preview` | Immagine contenitore `th-TH` con le impostazioni locali. |
| `2.1.0-amd64-tr-tr-preview` | Immagine contenitore `tr-TR` con le impostazioni locali. |
| `2.1.0-amd64-zh-cn-preview` | Immagine contenitore `zh-CN` con le impostazioni locali. |
| `2.1.0-amd64-zh-hk-preview` | Immagine contenitore `zh-HK` con le impostazioni locali. |
| `2.1.0-amd64-zh-tw-preview` | Immagine contenitore `zh-TW` con le impostazioni locali. |
| `2.0.3-amd64-ja-jp-preview` | Immagine contenitore `ja-JP` con le impostazioni locali. |
| `2.0.2-amd64-ar-ae-preview` | Immagine contenitore `ar-AE` con le impostazioni locali. |
| `2.0.2-amd64-ar-eg-preview` | Immagine contenitore `ar-EG` con le impostazioni locali. |
| `2.0.2-amd64-ar-kw-preview` | Immagine contenitore `ar-KW` con le impostazioni locali. |
| `2.0.2-amd64-ar-qa-preview` | Immagine contenitore `ar-QA` con le impostazioni locali. |
| `2.0.2-amd64-ar-sa-preview` | Immagine contenitore `ar-SA` con le impostazioni locali. |
| `2.0.2-amd64-ca-es-preview` | Immagine contenitore `ca-ES` con le impostazioni locali. |
| `2.0.2-amd64-da-dk-preview` | Immagine contenitore `da-DK` con le impostazioni locali. |
| `2.0.2-amd64-de-de-preview` | Immagine contenitore `de-DE` con le impostazioni locali. |
| `2.0.2-amd64-en-au-preview` | Immagine contenitore `en-AU` con le impostazioni locali. |
| `2.0.2-amd64-en-ca-preview` | Immagine contenitore `en-CA` con le impostazioni locali. |
| `2.0.2-amd64-en-gb-preview` | Immagine contenitore `en-GB` con le impostazioni locali. |
| `2.0.2-amd64-en-in-preview` | Immagine contenitore `en-IN` con le impostazioni locali. |
| `2.0.2-amd64-en-nz-preview` | Immagine contenitore `en-NZ` con le impostazioni locali. |
| `2.0.2-amd64-en-us-preview` | Immagine contenitore `en-US` con le impostazioni locali. |
| `2.0.2-amd64-es-es-preview` | Immagine contenitore `es-ES` con le impostazioni locali. |
| `2.0.2-amd64-es-mx-preview` | Immagine contenitore `es-MX` con le impostazioni locali. |
| `2.0.2-amd64-fi-fi-preview` | Immagine contenitore `fi-FI` con le impostazioni locali. |
| `2.0.2-amd64-fr-ca-preview` | Immagine contenitore `fr-CA` con le impostazioni locali. |
| `2.0.2-amd64-fr-fr-preview` | Immagine contenitore `fr-FR` con le impostazioni locali. |
| `2.0.2-amd64-gu-in-preview` | Immagine contenitore `gu-IN` con le impostazioni locali. |
| `2.0.2-amd64-hi-in-preview` | Immagine contenitore `hi-IN` con le impostazioni locali. |
| `2.0.2-amd64-it-it-preview` | Immagine contenitore `it-IT` con le impostazioni locali. |
| `2.0.2-amd64-ja-jp-preview` | Immagine contenitore `ja-JP` con le impostazioni locali. |
| `2.0.2-amd64-ko-kr-preview` | Immagine contenitore `ko-KR` con le impostazioni locali. |
| `2.0.2-amd64-mr-in-preview` | Immagine contenitore `mr-IN` con le impostazioni locali. |
| `2.0.2-amd64-nb-no-preview` | Immagine contenitore `nb-NO` con le impostazioni locali. |
| `2.0.2-amd64-nl-nl-preview` | Immagine contenitore `nl-NL` con le impostazioni locali. |
| `2.0.2-amd64-pl-pl-preview` | Immagine contenitore `pl-PL` con le impostazioni locali. |
| `2.0.2-amd64-pt-br-preview` | Immagine contenitore `pt-BR` con le impostazioni locali. |
| `2.0.2-amd64-pt-pt-preview` | Immagine contenitore `pt-PT` con le impostazioni locali. |
| `2.0.2-amd64-ru-ru-preview` | Immagine contenitore `ru-RU` con le impostazioni locali. |
| `2.0.2-amd64-sv-se-preview` | Immagine contenitore `sv-SE` con le impostazioni locali. |
| `2.0.2-amd64-ta-in-preview` | Immagine contenitore `ta-IN` con le impostazioni locali. |
| `2.0.2-amd64-te-in-preview` | Immagine contenitore `te-IN` con le impostazioni locali. |
| `2.0.2-amd64-th-th-preview` | Immagine contenitore `th-TH` con le impostazioni locali. |
| `2.0.2-amd64-tr-tr-preview` | Immagine contenitore `tr-TR` con le impostazioni locali. |
| `2.0.2-amd64-zh-cn-preview` | Immagine contenitore `zh-CN` con le impostazioni locali. |
| `2.0.2-amd64-zh-hk-preview` | Immagine contenitore `zh-HK` con le impostazioni locali. |
| `2.0.2-amd64-zh-tw-preview` | Immagine contenitore `zh-TW` con le impostazioni locali. |
| `2.0.1-amd64-ar-ae-preview` | Immagine contenitore `ar-AE` con le impostazioni locali. |
| `2.0.1-amd64-ar-eg-preview` | Immagine contenitore `ar-EG` con le impostazioni locali. |
| `2.0.1-amd64-ar-kw-preview` | Immagine contenitore `ar-KW` con le impostazioni locali. |
| `2.0.1-amd64-ar-qa-preview` | Immagine contenitore `ar-QA` con le impostazioni locali. |
| `2.0.1-amd64-ar-sa-preview` | Immagine contenitore `ar-SA` con le impostazioni locali. |
| `2.0.1-amd64-ca-es-preview` | Immagine contenitore `ca-ES` con le impostazioni locali. |
| `2.0.1-amd64-da-dk-preview` | Immagine contenitore `da-DK` con le impostazioni locali. |
| `2.0.1-amd64-de-de-preview` | Immagine contenitore `de-DE` con le impostazioni locali. |
| `2.0.1-amd64-en-au-preview` | Immagine contenitore `en-AU` con le impostazioni locali. |
| `2.0.1-amd64-en-ca-preview` | Immagine contenitore `en-CA` con le impostazioni locali. |
| `2.0.1-amd64-en-gb-preview` | Immagine contenitore `en-GB` con le impostazioni locali. |
| `2.0.1-amd64-en-in-preview` | Immagine contenitore `en-IN` con le impostazioni locali. |
| `2.0.1-amd64-en-nz-preview` | Immagine contenitore `en-NZ` con le impostazioni locali. |
| `2.0.1-amd64-en-us-preview` | Immagine contenitore `en-US` con le impostazioni locali. |
| `2.0.1-amd64-es-es-preview` | Immagine contenitore `es-ES` con le impostazioni locali. |
| `2.0.1-amd64-es-mx-preview` | Immagine contenitore `es-MX` con le impostazioni locali. |
| `2.0.1-amd64-fi-fi-preview` | Immagine contenitore `fi-FI` con le impostazioni locali. |
| `2.0.1-amd64-fr-ca-preview` | Immagine contenitore `fr-CA` con le impostazioni locali. |
| `2.0.1-amd64-fr-fr-preview` | Immagine contenitore `fr-FR` con le impostazioni locali. |
| `2.0.1-amd64-gu-in-preview` | Immagine contenitore `gu-IN` con le impostazioni locali. |
| `2.0.1-amd64-hi-in-preview` | Immagine contenitore `hi-IN` con le impostazioni locali. |
| `2.0.1-amd64-it-it-preview` | Immagine contenitore `it-IT` con le impostazioni locali. |
| `2.0.1-amd64-ja-jp-preview` | Immagine contenitore `ja-JP` con le impostazioni locali. |
| `2.0.1-amd64-ko-kr-preview` | Immagine contenitore `ko-KR` con le impostazioni locali. |
| `2.0.1-amd64-mr-in-preview` | Immagine contenitore `mr-IN` con le impostazioni locali. |
| `2.0.1-amd64-nb-no-preview` | Immagine contenitore `nb-NO` con le impostazioni locali. |
| `2.0.1-amd64-nl-nl-preview` | Immagine contenitore `nl-NL` con le impostazioni locali. |
| `2.0.1-amd64-pl-pl-preview` | Immagine contenitore `pl-PL` con le impostazioni locali. |
| `2.0.1-amd64-pt-br-preview` | Immagine contenitore `pt-BR` con le impostazioni locali. |
| `2.0.1-amd64-pt-pt-preview` | Immagine contenitore `pt-PT` con le impostazioni locali. |
| `2.0.1-amd64-ru-ru-preview` | Immagine contenitore `ru-RU` con le impostazioni locali. |
| `2.0.1-amd64-sv-se-preview` | Immagine contenitore `sv-SE` con le impostazioni locali. |
| `2.0.1-amd64-ta-in-preview` | Immagine contenitore `ta-IN` con le impostazioni locali. |
| `2.0.1-amd64-te-in-preview` | Immagine contenitore `te-IN` con le impostazioni locali. |
| `2.0.1-amd64-th-th-preview` | Immagine contenitore `th-TH` con le impostazioni locali. |
| `2.0.1-amd64-tr-tr-preview` | Immagine contenitore `tr-TR` con le impostazioni locali. |
| `2.0.1-amd64-zh-cn-preview` | Immagine contenitore `zh-CN` con le impostazioni locali. |
| `2.0.1-amd64-zh-hk-preview` | Immagine contenitore `zh-HK` con le impostazioni locali. |
| `2.0.1-amd64-zh-tw-preview` | Immagine contenitore `zh-TW` con le impostazioni locali. |
| `2.0.0-amd64-ar-eg-preview` | Immagine contenitore `ar-EG` con le impostazioni locali. |
| `2.0.0-amd64-ca-es-preview` | Immagine contenitore `ca-ES` con le impostazioni locali. |
| `2.0.0-amd64-da-dk-preview` | Immagine contenitore `da-DK` con le impostazioni locali. |
| `2.0.0-amd64-de-de-preview` | Immagine contenitore `de-DE` con le impostazioni locali. |
| `2.0.0-amd64-en-au-preview` | Immagine contenitore `en-AU` con le impostazioni locali. |
| `2.0.0-amd64-en-ca-preview` | Immagine contenitore `en-CA` con le impostazioni locali. |
| `2.0.0-amd64-en-gb-preview` | Immagine contenitore `en-GB` con le impostazioni locali. |
| `2.0.0-amd64-en-in-preview` | Immagine contenitore `en-IN` con le impostazioni locali. |
| `2.0.0-amd64-en-nz-preview` | Immagine contenitore `en-NZ` con le impostazioni locali. |
| `2.0.0-amd64-en-us-preview` | Immagine contenitore `en-US` con le impostazioni locali. |
| `2.0.0-amd64-es-es-preview` | Immagine contenitore `es-ES` con le impostazioni locali. |
| `2.0.0-amd64-es-mx-preview` | Immagine contenitore `es-MX` con le impostazioni locali. |
| `2.0.0-amd64-fi-fi-preview` | Immagine contenitore `fi-FI` con le impostazioni locali. |
| `2.0.0-amd64-fr-ca-preview` | Immagine contenitore `fr-CA` con le impostazioni locali. |
| `2.0.0-amd64-fr-fr-preview` | Immagine contenitore `fr-FR` con le impostazioni locali. |
| `2.0.0-amd64-hi-in-preview` | Immagine contenitore `hi-IN` con le impostazioni locali. |
| `2.0.0-amd64-it-it-preview` | Immagine contenitore `it-IT` con le impostazioni locali. |
| `2.0.0-amd64-ja-jp-preview` | Immagine contenitore `ja-JP` con le impostazioni locali. |
| `2.0.0-amd64-ko-kr-preview` | Immagine contenitore `ko-KR` con le impostazioni locali. |
| `2.0.0-amd64-nb-no-preview` | Immagine contenitore `nb-NO` con le impostazioni locali. |
| `2.0.0-amd64-nl-nl-preview` | Immagine contenitore `nl-NL` con le impostazioni locali. |
| `2.0.0-amd64-pl-pl-preview` | Immagine contenitore `pl-PL` con le impostazioni locali. |
| `2.0.0-amd64-pt-br-preview` | Immagine contenitore `pt-BR` con le impostazioni locali. |
| `2.0.0-amd64-pt-pt-preview` | Immagine contenitore `pt-PT` con le impostazioni locali. |
| `2.0.0-amd64-ru-ru-preview` | Immagine contenitore `ru-RU` con le impostazioni locali. |
| `2.0.0-amd64-sv-se-preview` | Immagine contenitore `sv-SE` con le impostazioni locali. |
| `2.0.0-amd64-th-th-preview` | Immagine contenitore `th-TH` con le impostazioni locali. |
| `2.0.0-amd64-tr-tr-preview` | Immagine contenitore `tr-TR` con le impostazioni locali. |
| `2.0.0-amd64-zh-cn-preview` | Immagine contenitore `zh-CN` con le impostazioni locali. |
| `2.0.0-amd64-zh-hk-preview` | Immagine contenitore `zh-HK` con le impostazioni locali. |
| `2.0.0-amd64-zh-tw-preview` | Immagine contenitore `zh-TW` con le impostazioni locali. |
| `1.2.0-amd64-de-de-preview` | Immagine contenitore `de-DE` con le impostazioni locali. |
| `1.2.0-amd64-en-au-preview` | Immagine contenitore `en-AU` con le impostazioni locali. |
| `1.2.0-amd64-en-ca-preview` | Immagine contenitore `en-CA` con le impostazioni locali. |
| `1.2.0-amd64-en-gb-preview` | Immagine contenitore `en-GB` con le impostazioni locali. |
| `1.2.0-amd64-en-in-preview` | Immagine contenitore `en-IN` con le impostazioni locali. |
| `1.2.0-amd64-en-us-preview` | Immagine contenitore `en-US` con le impostazioni locali. |
| `1.2.0-amd64-es-es-preview` | Immagine contenitore `es-ES` con le impostazioni locali. |
| `1.2.0-amd64-es-mx-preview` | Immagine contenitore `es-MX` con le impostazioni locali. |
| `1.2.0-amd64-fr-ca-preview` | Immagine contenitore `fr-CA` con le impostazioni locali. |
| `1.2.0-amd64-fr-fr-preview` | Immagine contenitore `fr-FR` con le impostazioni locali. |
| `1.2.0-amd64-it-it-preview` | Immagine contenitore `it-IT` con le impostazioni locali. |
| `1.2.0-amd64-ja-jp-preview` | Immagine contenitore `ja-JP` con le impostazioni locali. |
| `1.2.0-amd64-pt-br-preview` | Immagine contenitore `pt-BR` con le impostazioni locali. |
| `1.2.0-amd64-zh-cn-preview` | Immagine contenitore `zh-CN` con le impostazioni locali. |
| `1.1.3-amd64-de-de-preview` | Immagine contenitore `de-DE` con le impostazioni locali. |
| `1.1.3-amd64-en-au-preview` | Immagine contenitore `en-AU` con le impostazioni locali. |
| `1.1.3-amd64-en-ca-preview` | Immagine contenitore `en-CA` con le impostazioni locali. |
| `1.1.3-amd64-en-gb-preview` | Immagine contenitore `en-GB` con le impostazioni locali. |
| `1.1.3-amd64-en-in-preview` | Immagine contenitore `en-IN` con le impostazioni locali. |
| `1.1.3-amd64-en-us-preview` | Immagine contenitore `en-US` con le impostazioni locali. |
| `1.1.3-amd64-es-es-preview` | Immagine contenitore `es-ES` con le impostazioni locali. |
| `1.1.3-amd64-es-mx-preview` | Immagine contenitore `es-MX` con le impostazioni locali. |
| `1.1.3-amd64-fr-ca-preview` | Immagine contenitore `fr-CA` con le impostazioni locali. |
| `1.1.3-amd64-fr-fr-preview` | Immagine contenitore `fr-FR` con le impostazioni locali. |
| `1.1.3-amd64-it-it-preview` | Immagine contenitore `it-IT` con le impostazioni locali. |
| `1.1.3-amd64-ja-jp-preview` | Immagine contenitore `ja-JP` con le impostazioni locali. |
| `1.1.3-amd64-pt-br-preview` | Immagine contenitore `pt-BR` con le impostazioni locali. |
| `1.1.3-amd64-zh-cn-preview` | Immagine contenitore `zh-CN` con le impostazioni locali. |
| `1.1.2-amd64-de-de-preview` | Immagine contenitore `de-DE` con le impostazioni locali. |
| `1.1.2-amd64-en-au-preview` | Immagine contenitore `en-AU` con le impostazioni locali. |
| `1.1.2-amd64-en-ca-preview` | Immagine contenitore `en-CA` con le impostazioni locali. |
| `1.1.2-amd64-en-gb-preview` | Immagine contenitore `en-GB` con le impostazioni locali. |
| `1.1.2-amd64-en-in-preview` | Immagine contenitore `en-IN` con le impostazioni locali. |
| `1.1.2-amd64-en-us-preview` | Immagine contenitore `en-US` con le impostazioni locali. |
| `1.1.2-amd64-es-es-preview` | Immagine contenitore `es-ES` con le impostazioni locali. |
| `1.1.2-amd64-es-mx-preview` | Immagine contenitore `es-MX` con le impostazioni locali. |
| `1.1.2-amd64-fr-ca-preview` | Immagine contenitore `fr-CA` con le impostazioni locali. |
| `1.1.2-amd64-fr-fr-preview` | Immagine contenitore `fr-FR` con le impostazioni locali. |
| `1.1.2-amd64-it-it-preview` | Immagine contenitore `it-IT` con le impostazioni locali. |
| `1.1.2-amd64-ja-jp-preview` | Immagine contenitore `ja-JP` con le impostazioni locali. |
| `1.1.2-amd64-pt-br-preview` | Immagine contenitore `pt-BR` con le impostazioni locali. |
| `1.1.2-amd64-zh-cn-preview` | Immagine contenitore `zh-CN` con le impostazioni locali. |
| `1.1.1-amd64-de-de-preview` | Immagine contenitore `de-DE` con le impostazioni locali. |
| `1.1.1-amd64-en-au-preview` | Immagine contenitore `en-AU` con le impostazioni locali. |
| `1.1.1-amd64-en-ca-preview` | Immagine contenitore `en-CA` con le impostazioni locali. |
| `1.1.1-amd64-en-gb-preview` | Immagine contenitore `en-GB` con le impostazioni locali. |
| `1.1.1-amd64-en-in-preview` | Immagine contenitore `en-IN` con le impostazioni locali. |
| `1.1.1-amd64-en-us-preview` | Immagine contenitore `en-US` con le impostazioni locali. |
| `1.1.1-amd64-es-es-preview` | Immagine contenitore `es-ES` con le impostazioni locali. |
| `1.1.1-amd64-es-mx-preview` | Immagine contenitore `es-MX` con le impostazioni locali. |
| `1.1.1-amd64-fr-ca-preview` | Immagine contenitore `fr-CA` con le impostazioni locali. |
| `1.1.1-amd64-fr-fr-preview` | Immagine contenitore `fr-FR` con le impostazioni locali. |
| `1.1.1-amd64-it-it-preview` | Immagine contenitore `it-IT` con le impostazioni locali. |
| `1.1.1-amd64-ja-jp-preview` | Immagine contenitore `ja-JP` con le impostazioni locali. |
| `1.1.1-amd64-pt-br-preview` | Immagine contenitore `pt-BR` con le impostazioni locali. |
| `1.1.1-amd64-zh-cn-preview` | Immagine contenitore `zh-CN` con le impostazioni locali. |
| `1.1.0-amd64-de-de-preview` | Immagine contenitore `de-DE` con le impostazioni locali. |
| `1.1.0-amd64-en-au-preview` | Immagine contenitore `en-AU` con le impostazioni locali. |
| `1.1.0-amd64-en-ca-preview` | Immagine contenitore `en-CA` con le impostazioni locali. |
| `1.1.0-amd64-en-gb-preview` | Immagine contenitore `en-GB` con le impostazioni locali. |
| `1.1.0-amd64-en-in-preview` | Immagine contenitore `en-IN` con le impostazioni locali. |
| `1.1.0-amd64-en-us-preview` | Immagine contenitore `en-US` con le impostazioni locali. |
| `1.1.0-amd64-es-es-preview` | Immagine contenitore `es-ES` con le impostazioni locali. |
| `1.1.0-amd64-es-mx-preview` | Immagine contenitore `es-MX` con le impostazioni locali. |
| `1.1.0-amd64-fr-ca-preview` | Immagine contenitore `fr-CA` con le impostazioni locali. |
| `1.1.0-amd64-fr-fr-preview` | Immagine contenitore `fr-FR` con le impostazioni locali. |
| `1.1.0-amd64-it-it-preview` | Immagine contenitore `it-IT` con le impostazioni locali. |
| `1.1.0-amd64-ja-jp-preview` | Immagine contenitore `ja-JP` con le impostazioni locali. |
| `1.1.0-amd64-pt-br-preview` | Immagine contenitore `pt-BR` con le impostazioni locali. |
| `1.1.0-amd64-zh-cn-preview` | Immagine contenitore `zh-CN` con le impostazioni locali. |
| `1.0.0-amd64-de-de-preview` | Immagine contenitore `de-DE` con le impostazioni locali. |
| `1.0.0-amd64-en-au-preview` | Immagine contenitore `en-AU` con le impostazioni locali. |
| `1.0.0-amd64-en-ca-preview` | Immagine contenitore `en-CA` con le impostazioni locali. |
| `1.0.0-amd64-en-gb-preview` | Immagine contenitore `en-GB` con le impostazioni locali. |
| `1.0.0-amd64-en-in-preview` | Immagine contenitore `en-IN` con le impostazioni locali. |
| `1.0.0-amd64-en-us-preview` | Immagine contenitore `en-US` con le impostazioni locali. |
| `1.0.0-amd64-es-es-preview` | Immagine contenitore `es-ES` con le impostazioni locali. |
| `1.0.0-amd64-es-mx-preview` | Immagine contenitore `es-MX` con le impostazioni locali. |
| `1.0.0-amd64-fr-ca-preview` | Immagine contenitore `fr-CA` con le impostazioni locali. |
| `1.0.0-amd64-fr-fr-preview` | Immagine contenitore `fr-FR` con le impostazioni locali. |
| `1.0.0-amd64-it-it-preview` | Immagine contenitore `it-IT` con le impostazioni locali. |
| `1.0.0-amd64-ja-jp-preview` | Immagine contenitore `ja-JP` con le impostazioni locali. |
| `1.0.0-amd64-pt-br-preview` | Immagine contenitore `pt-BR` con le impostazioni locali. |
| `1.0.0-amd64-zh-cn-preview` | Immagine contenitore `zh-CN` con le impostazioni locali. |

## <a name="text-to-speech"></a>Sintesi vocale

L'immagine del contenitore [sintesi][sp-tts] vocale `containerpreview.azurecr.io` è disponibile nel Registro di sistema del contenitore. Si trova all'interno `microsoft` del `cognitive-services-text-to-speech`repository e si chiama . Il nome completo dell'immagine del contenitore è, `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`.

Questa immagine contenitore ha i seguenti tag disponibili:

| Tag immagine                                  | Note                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Immagine contenitore `en-US` con `en-US-JessaRUS` le impostazioni locali e la voce.        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | Immagine contenitore `ar-EG` con `ar-EG-Hoda` le impostazioni locali e la voce.            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | Immagine contenitore `ar-SA` con `ar-SA-Naayf` le impostazioni locali e la voce.           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | Immagine contenitore `bg-BG` con `bg-BG-Ivan` le impostazioni locali e la voce.            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | Immagine contenitore `ca-ES` con `ca-ES-HerenaRUS` le impostazioni locali e la voce.       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | Immagine contenitore `cs-CZ` con `cs-CZ-Jakub` le impostazioni locali e la voce.           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | Immagine contenitore `da-DK` con `da-DK-HelleRUS` le impostazioni locali e la voce.        |
| `1.3.0-amd64-de-at-michael-preview`         | Immagine contenitore `de-AT` con `de-AT-Michael` le impostazioni locali e la voce.         |
| `1.3.0-amd64-de-ch-karsten-preview`         | Immagine contenitore `de-CH` con `de-CH-Karsten` le impostazioni locali e la voce.         |
| `1.3.0-amd64-de-de-hedda-preview`           | Immagine contenitore `de-DE` con `de-DE-Hedda` le impostazioni locali e la voce.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Immagine contenitore `de-DE` con `de-DE-Hedda` le impostazioni locali e la voce.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Immagine contenitore `de-DE` con `de-DE-HeddaRUS` le impostazioni locali e la voce.        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | Immagine contenitore `de-DE` con `de-DE-Stefan-Apollo` le impostazioni locali e la voce.   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | Immagine contenitore `el-GR` con `el-GR-Stefanos` le impostazioni locali e la voce.        |
| `1.3.0-amd64-en-au-catherine-preview`       | Immagine contenitore `en-AU` con `en-AU-Catherine` le impostazioni locali e la voce.       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | Immagine contenitore `en-AU` con `en-AU-HayleyRUS` le impostazioni locali e la voce.       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | Immagine contenitore `en-CA` con `en-CA-HeatherRUS` le impostazioni locali e la voce.      |
| `1.3.0-amd64-en-ca-linda-preview`           | Immagine contenitore `en-CA` con `en-CA-Linda` le impostazioni locali e la voce.           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | Immagine contenitore `en-GB` con `en-GB-George-Apollo` le impostazioni locali e la voce.   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | Immagine contenitore `en-GB` con `en-GB-HazelRUS` le impostazioni locali e la voce.        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | Immagine contenitore `en-GB` con `en-GB-Susan-Apollo` le impostazioni locali e la voce.    |
| `1.3.0-amd64-en-ie-sean-preview`            | Immagine contenitore `en-IE` con `en-IE-Sean` le impostazioni locali e la voce.            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | Immagine contenitore `en-IN` con `en-IN-Heera-Apollo` le impostazioni locali e la voce.    |
| `1.3.0-amd64-en-in-priyarus-preview`        | Immagine contenitore `en-IN` con `en-IN-PriyaRUS` le impostazioni locali e la voce.        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | Immagine contenitore `en-IN` con `en-IN-Ravi-Apollo` le impostazioni locali e la voce.     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | Immagine contenitore `en-US` con `en-US-BenjaminRUS` le impostazioni locali e la voce.     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | Immagine contenitore `en-US` con `en-US-Guy24kRUS` le impostazioni locali e la voce.       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | Immagine contenitore `en-US` con `en-US-Jessa24kRUS` le impostazioni locali e la voce.     |
| `1.3.0-amd64-en-us-jessarus-preview`        | Immagine contenitore `en-US` con `en-US-JessaRUS` le impostazioni locali e la voce.        |
| `1.3.0-amd64-en-us-zirarus-preview`         | Immagine contenitore `en-US` con `en-US-ZiraRUS` le impostazioni locali e la voce.         |
| `1.3.0-amd64-es-es-helenarus-preview`       | Immagine contenitore `es-ES` con `es-ES-HelenaRUS` le impostazioni locali e la voce.       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | Immagine contenitore `es-ES` con `es-ES-Laura-Apollo` le impostazioni locali e la voce.    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | Immagine contenitore `es-ES` con `es-ES-Pablo-Apollo` le impostazioni locali e la voce.    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | Immagine contenitore `es-MX` con `es-MX-HildaRUS` le impostazioni locali e la voce.        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | Immagine contenitore `es-MX` con `es-MX-Raul-Apollo` le impostazioni locali e la voce.     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | Immagine contenitore `fi-FI` con `fi-FI-HeidiRUS` le impostazioni locali e la voce.        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | Immagine contenitore `fr-CA` con `fr-CA-Caroline` le impostazioni locali e la voce.        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | Immagine contenitore `fr-CA` con `fr-CA-HarmonieRUS` le impostazioni locali e la voce.     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | Immagine contenitore `fr-CH` con `fr-CH-Guillaume` le impostazioni locali e la voce.       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | Immagine contenitore `fr-FR` con `fr-FR-HortenseRUS` le impostazioni locali e la voce.     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | Immagine contenitore `fr-FR` con `fr-FR-Julie-Apollo` le impostazioni locali e la voce.    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | Immagine contenitore `fr-FR` con `fr-FR-Paul-Apollo` le impostazioni locali e la voce.     |
| `1.3.0-amd64-he-il-asaf-preview`            | Immagine contenitore `he-IL` con `he-IL-Asaf` le impostazioni locali e la voce.            |
| `1.3.0-amd64-hi-in-hemant-preview`          | Immagine contenitore `hi-IN` con `hi-IN-Hemant` le impostazioni locali e la voce.          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Immagine contenitore `hi-IN` con `hi-IN-Kalpana-Apollo` le impostazioni locali e la voce.  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Immagine contenitore `hi-IN` con `hi-IN-Kalpana` le impostazioni locali e la voce.         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | Immagine contenitore `hi-IN` con `hi-IN-Kalpana` le impostazioni locali e la voce.         |
| `1.3.0-amd64-hr-hr-matej-preview`           | Immagine contenitore `hr-HR` con `hr-HR-Matej` le impostazioni locali e la voce.           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | Immagine contenitore `hu-HU` con `hu-HU-Szabolcs` le impostazioni locali e la voce.        |
| `1.3.0-amd64-id-id-andika-preview`          | Immagine contenitore `id-ID` con `id-ID-Andika` le impostazioni locali e la voce.          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | Immagine contenitore `it-IT` con `it-IT-Cosimo-Apollo` le impostazioni locali e la voce.   |
| `1.3.0-amd64-it-it-luciarus-preview`        | Immagine contenitore `it-IT` con `it-IT-LuciaRUS` le impostazioni locali e la voce.        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | Immagine contenitore `ja-JP` con `ja-JP-Ayumi-Apollo` le impostazioni locali e la voce.    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | Immagine contenitore `ja-JP` con `ja-JP-HarukaRUS` le impostazioni locali e la voce.       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | Immagine contenitore `ja-JP` con `ja-JP-Ichiro-Apollo` le impostazioni locali e la voce.   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | Immagine contenitore `ko-KR` con `ko-KR-HeamiRUS` le impostazioni locali e la voce.        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | Immagine contenitore `ms-MY` con `ms-MY-Rizwan` le impostazioni locali e la voce.          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | Immagine contenitore `nb-NO` con `nb-NO-HuldaRUS` le impostazioni locali e la voce.        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | Immagine contenitore `nl-NL` con `nl-NL-HannaRUS` le impostazioni locali e la voce.        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | Immagine contenitore `pl-PL` con `pl-PL-PaulinaRUS` le impostazioni locali e la voce.      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | Immagine contenitore `pt-BR` con `pt-BR-Daniel-Apollo` le impostazioni locali e la voce.   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | Immagine contenitore `pt-BR` con `pt-BR-HeloisaRUS` le impostazioni locali e la voce.      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | Immagine contenitore `pt-PT` con `pt-PT-HeliaRUS` le impostazioni locali e la voce.        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | Immagine contenitore `ro-RO` con `ro-RO-Andrei` le impostazioni locali e la voce.          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | Immagine contenitore `ru-RU` con `ru-RU-EkaterinaRUS` le impostazioni locali e la voce.    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | Immagine contenitore `ru-RU` con `ru-RU-Irina-Apollo` le impostazioni locali e la voce.    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | Immagine contenitore `ru-RU` con `ru-RU-Pavel-Apollo` le impostazioni locali e la voce.    |
| `1.3.0-amd64-sk-sk-filip-preview`           | Immagine contenitore `sk-SK` con `sk-SK-Filip` le impostazioni locali e la voce.           |
| `1.3.0-amd64-sl-si-lado-preview`            | Immagine contenitore `sl-SI` con `sl-SI-Lado` le impostazioni locali e la voce.            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | Immagine contenitore `sv-SE` con `sv-SE-HedvigRUS` le impostazioni locali e la voce.       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | Immagine contenitore `ta-IN` con `ta-IN-Valluvar` le impostazioni locali e la voce.        |
| `1.3.0-amd64-te-in-chitra-preview`          | Immagine contenitore `te-IN` con `te-IN-Chitra` le impostazioni locali e la voce.          |
| `1.3.0-amd64-th-th-pattara-preview`         | Immagine contenitore `th-TH` con `th-TH-Pattara` le impostazioni locali e la voce.         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | Immagine contenitore `tr-TR` con `tr-TR-SedaRUS` le impostazioni locali e la voce.         |
| `1.3.0-amd64-vi-vn-an-preview`              | Immagine contenitore `vi-VN` con `vi-VN-An` le impostazioni locali e la voce.              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | Immagine contenitore `zh-CN` con `zh-CN-HuihuiRUS` le impostazioni locali e la voce.       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | Immagine contenitore `zh-CN` con `zh-CN-Kangkang-Apollo` le impostazioni locali e la voce. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | Immagine contenitore `zh-CN` con `zh-CN-Yaoyao-Apollo` le impostazioni locali e la voce.   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | Immagine contenitore `zh-HK` con `zh-HK-Danny-Apollo` le impostazioni locali e la voce.    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | Immagine contenitore `zh-HK` con `zh-HK-Tracy-Apollo` le impostazioni locali e la voce.    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | Immagine contenitore `zh-HK` con `zh-HK-TracyRUS` le impostazioni locali e la voce.        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | Immagine contenitore `zh-TW` con `zh-TW-HanHanRUS` le impostazioni locali e la voce.       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | Immagine contenitore `zh-TW` con `zh-TW-Yating-Apollo` le impostazioni locali e la voce.   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | Immagine contenitore `zh-TW` con `zh-TW-Zhiwei-Apollo` le impostazioni locali e la voce.   |
| `1.2.0-amd64-de-de-heddarus-preview`        | Immagine contenitore `de-DE` con `de-DE-Hedda` le impostazioni locali e la voce.           |
| `1.2.0-amd64-de-de-heddarus-preview`        | Immagine contenitore `de-DE` con `de-DE-HeddaRUS` le impostazioni locali e la voce.        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | Immagine contenitore `de-DE` con `de-DE-Stefan-Apollo` le impostazioni locali e la voce.   |
| `1.2.0-amd64-en-au-catherine-preview`       | Immagine contenitore `en-AU` con `en-AU-Catherine` le impostazioni locali e la voce.       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | Immagine contenitore `en-AU` con `en-AU-HayleyRUS` le impostazioni locali e la voce.       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | Immagine contenitore `en-GB` con `en-GB-George-Apollo` le impostazioni locali e la voce.   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | Immagine contenitore `en-GB` con `en-GB-HazelRUS` le impostazioni locali e la voce.        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | Immagine contenitore `en-GB` con `en-GB-Susan-Apollo` le impostazioni locali e la voce.    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | Immagine contenitore `en-IN` con `en-IN-Heera-Apollo` le impostazioni locali e la voce.    |
| `1.2.0-amd64-en-in-priyarus-preview`        | Immagine contenitore `en-IN` con `en-IN-PriyaRUS` le impostazioni locali e la voce.        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | Immagine contenitore `en-IN` con `en-IN-Ravi-Apollo` le impostazioni locali e la voce.     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | Immagine contenitore `en-US` con `en-US-BenjaminRUS` le impostazioni locali e la voce.     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | Immagine contenitore `en-US` con `en-US-Guy24kRUS` le impostazioni locali e la voce.       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | Immagine contenitore `en-US` con `en-US-Jessa24kRUS` le impostazioni locali e la voce.     |
| `1.2.0-amd64-en-us-jessarus-preview`        | Immagine contenitore `en-US` con `en-US-JessaRUS` le impostazioni locali e la voce.        |
| `1.2.0-amd64-en-us-zirarus-preview`         | Immagine contenitore `en-US` con `en-US-ZiraRUS` le impostazioni locali e la voce.         |
| `1.2.0-amd64-es-es-helenarus-preview`       | Immagine contenitore `es-ES` con `es-ES-HelenaRUS` le impostazioni locali e la voce.       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | Immagine contenitore `es-ES` con `es-ES-Laura-Apollo` le impostazioni locali e la voce.    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | Immagine contenitore `es-ES` con `es-ES-Pablo-Apollo` le impostazioni locali e la voce.    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | Immagine contenitore `es-MX` con `es-MX-HildaRUS` le impostazioni locali e la voce.        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | Immagine contenitore `es-MX` con `es-MX-Raul-Apollo` le impostazioni locali e la voce.     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | Immagine contenitore `fr-CA` con `fr-CA-Caroline` le impostazioni locali e la voce.        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | Immagine contenitore `fr-CA` con `fr-CA-HarmonieRUS` le impostazioni locali e la voce.     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | Immagine contenitore `fr-FR` con `fr-FR-HortenseRUS` le impostazioni locali e la voce.     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | Immagine contenitore `fr-FR` con `fr-FR-Julie-Apollo` le impostazioni locali e la voce.    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | Immagine contenitore `fr-FR` con `fr-FR-Paul-Apollo` le impostazioni locali e la voce.     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | Immagine contenitore `it-IT` con `it-IT-Cosimo-Apollo` le impostazioni locali e la voce.   |
| `1.2.0-amd64-it-it-luciarus-preview`        | Immagine contenitore `it-IT` con `it-IT-LuciaRUS` le impostazioni locali e la voce.        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | Immagine contenitore `ja-JP` con `ja-JP-Ayumi-Apollo` le impostazioni locali e la voce.    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | Immagine contenitore `ja-JP` con `ja-JP-HarukaRUS` le impostazioni locali e la voce.       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | Immagine contenitore `ja-JP` con `ja-JP-Ichiro-Apollo` le impostazioni locali e la voce.   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | Immagine contenitore `ko-KR` con `ko-KR-HeamiRUS` le impostazioni locali e la voce.        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | Immagine contenitore `pt-BR` con `pt-BR-Daniel-Apollo` le impostazioni locali e la voce.   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | Immagine contenitore `pt-BR` con `pt-BR-HeloisaRUS` le impostazioni locali e la voce.      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | Immagine contenitore `zh-CN` con `zh-CN-HuihuiRUS` le impostazioni locali e la voce.       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | Immagine contenitore `zh-CN` con `zh-CN-Kangkang-Apollo` le impostazioni locali e la voce. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | Immagine contenitore `zh-CN` con `zh-CN-Yaoyao-Apollo` le impostazioni locali e la voce.   |
| `1.1.0-amd64-de-de-hedda-preview`           | Immagine contenitore `de-DE` con `de-DE-Hedda` le impostazioni locali e la voce.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Immagine contenitore `de-DE` con `de-DE-Hedda` le impostazioni locali e la voce.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Immagine contenitore `de-DE` con `de-DE-HeddaRUS` le impostazioni locali e la voce.        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | Immagine contenitore `de-DE` con `de-DE-Stefan-Apollo` le impostazioni locali e la voce.   |
| `1.1.0-amd64-en-au-catherine-preview`       | Immagine contenitore `en-AU` con `en-AU-Catherine` le impostazioni locali e la voce.       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | Immagine contenitore `en-AU` con `en-AU-HayleyRUS` le impostazioni locali e la voce.       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | Immagine contenitore `en-GB` con `en-GB-George-Apollo` le impostazioni locali e la voce.   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | Immagine contenitore `en-GB` con `en-GB-HazelRUS` le impostazioni locali e la voce.        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | Immagine contenitore `en-GB` con `en-GB-Susan-Apollo` le impostazioni locali e la voce.    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | Immagine contenitore `en-IN` con `en-IN-Heera-Apollo` le impostazioni locali e la voce.    |
| `1.1.0-amd64-en-in-priyarus-preview`        | Immagine contenitore `en-IN` con `en-IN-PriyaRUS` le impostazioni locali e la voce.        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | Immagine contenitore `en-IN` con `en-IN-Ravi-Apollo` le impostazioni locali e la voce.     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | Immagine contenitore `en-US` con `en-US-BenjaminRUS` le impostazioni locali e la voce.     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | Immagine contenitore `en-US` con `en-US-Guy24kRUS` le impostazioni locali e la voce.       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | Immagine contenitore `en-US` con `en-US-Jessa24kRUS` le impostazioni locali e la voce.     |
| `1.1.0-amd64-en-us-jessarus-preview`        | Immagine contenitore `en-US` con `en-US-JessaRUS` le impostazioni locali e la voce.        |
| `1.1.0-amd64-en-us-zirarus-preview`         | Immagine contenitore `en-US` con `en-US-ZiraRUS` le impostazioni locali e la voce.         |
| `1.1.0-amd64-es-es-helenarus-preview`       | Immagine contenitore `es-ES` con `es-ES-HelenaRUS` le impostazioni locali e la voce.       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | Immagine contenitore `es-ES` con `es-ES-Laura-Apollo` le impostazioni locali e la voce.    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | Immagine contenitore `es-ES` con `es-ES-Pablo-Apollo` le impostazioni locali e la voce.    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | Immagine contenitore `es-MX` con `es-MX-HildaRUS` le impostazioni locali e la voce.        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | Immagine contenitore `es-MX` con `es-MX-Raul-Apollo` le impostazioni locali e la voce.     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | Immagine contenitore `fr-CA` con `fr-CA-Caroline` le impostazioni locali e la voce.        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | Immagine contenitore `fr-CA` con `fr-CA-HarmonieRUS` le impostazioni locali e la voce.     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | Immagine contenitore `fr-FR` con `fr-FR-HortenseRUS` le impostazioni locali e la voce.     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | Immagine contenitore `fr-FR` con `fr-FR-Julie-Apollo` le impostazioni locali e la voce.    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | Immagine contenitore `fr-FR` con `fr-FR-Paul-Apollo` le impostazioni locali e la voce.     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | Immagine contenitore `it-IT` con `it-IT-Cosimo-Apollo` le impostazioni locali e la voce.   |
| `1.1.0-amd64-it-it-luciarus-preview`        | Immagine contenitore `it-IT` con `it-IT-LuciaRUS` le impostazioni locali e la voce.        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | Immagine contenitore `ja-JP` con `ja-JP-Ayumi-Apollo` le impostazioni locali e la voce.    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | Immagine contenitore `ja-JP` con `ja-JP-HarukaRUS` le impostazioni locali e la voce.       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | Immagine contenitore `ja-JP` con `ja-JP-Ichiro-Apollo` le impostazioni locali e la voce.   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | Immagine contenitore `ko-KR` con `ko-KR-HeamiRUS` le impostazioni locali e la voce.        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | Immagine contenitore `pt-BR` con `pt-BR-Daniel-Apollo` le impostazioni locali e la voce.   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | Immagine contenitore `pt-BR` con `pt-BR-HeloisaRUS` le impostazioni locali e la voce.      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | Immagine contenitore `zh-CN` con `zh-CN-HuihuiRUS` le impostazioni locali e la voce.       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | Immagine contenitore `zh-CN` con `zh-CN-Kangkang-Apollo` le impostazioni locali e la voce. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | Immagine contenitore `zh-CN` con `zh-CN-Yaoyao-Apollo` le impostazioni locali e la voce.   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | Immagine contenitore `en-US` con `en-US-BenjaminRUS` le impostazioni locali e la voce.     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | Immagine contenitore `en-US` con `en-US-Guy24kRUS` le impostazioni locali e la voce.       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | Immagine contenitore `en-US` con `en-US-Jessa24kRUS` le impostazioni locali e la voce.     |
| `1.0.0-amd64-en-us-jessarus-preview`        | Immagine contenitore `en-US` con `en-US-JessaRUS` le impostazioni locali e la voce.        |
| `1.0.0-amd64-en-us-zirarus-preview`         | Immagine contenitore `en-US` con `en-US-ZiraRUS` le impostazioni locali e la voce.         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | Immagine contenitore `zh-CN` con `zh-CN-HuihuiRUS` le impostazioni locali e la voce.       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | Immagine contenitore `zh-CN` con `zh-CN-Kangkang-Apollo` le impostazioni locali e la voce. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | Immagine contenitore `zh-CN` con `zh-CN-Yaoyao-Apollo` le impostazioni locali e la voce.   |

## <a name="key-phrase-extraction"></a>Estrazione frasi chiave

L'immagine del contenitore Key `mcr.microsoft.com` Phrase [Extraction][ta-kp] è disponibile nel sindacato del Registro di sistema del contenitore. Si trova all'interno `azure-cognitive-services` del `keyphrase`repository e si chiama . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/keyphrase`.

Questa immagine contenitore ha i seguenti tag disponibili:

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>Rilevamento lingua

L'immagine del contenitore [Rilevamento lingua][ta-la] è disponibile nel sindacato del Registro di sistema del `mcr.microsoft.com` contenitore. Si trova all'interno `azure-cognitive-services` del `language`repository e si chiama . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/language`.

Questa immagine contenitore ha i seguenti tag disponibili:

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>Analisi del sentiment

L'immagine del contenitore Sentiment `mcr.microsoft.com` [Analysis][ta-se] è disponibile nel sindacato del Registro di sistema del contenitore. Si trova all'interno `azure-cognitive-services` del `sentiment`repository e si chiama . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/sentiment`.

Questa immagine contenitore ha i seguenti tag disponibili:

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
