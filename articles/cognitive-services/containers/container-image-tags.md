---
title: Tag delle immagini di contenitori in Servizi cognitivi
titleSuffix: Azure Cognitive Services
description: Elenco completo di tutti i tag dell'immagine del contenitore di servizi cognitivi.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: cabc3d2a0f8eb3a75938d1768bb0085aab528391
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83584604"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Tag dell'immagine del contenitore di servizi cognitivi di Azure

Servizi cognitivi di Azure offre molte immagini del contenitore. I registri dei contenitori e i repository corrispondenti variano tra le immagini del contenitore. Ogni nome dell'immagine del contenitore offre più tag. Un tag dell'immagine del contenitore è un meccanismo di controllo delle versioni dell'immagine del contenitore. Questo articolo è destinato a essere usato come riferimento completo per l'elenco di tutte le immagini del contenitore di servizi cognitivi e dei relativi tag disponibili.

> [!TIP]
> Quando si usa [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) , prestare particolare attenzione alle maiuscole e minuscole del registro contenitori, al repository, al nome dell'immagine del contenitore e al tag corrispondente, poiché fanno **distinzione tra maiuscole**e minuscole.

## <a name="anomaly-detector"></a>Rilevamento anomalie

L'immagine del contenitore del [rilevatore di anomalie][ad-containers] è disponibile nel `mcr.microsoft.com` Syndicate del registro contenitori. Si trova all'interno del `azure-cognitive-services` repository ed è denominato `anomaly-detector` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` .

Questa immagine del contenitore include i tag seguenti:

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |

## <a name="computer-vision"></a>Visione artificiale

È possibile trovare l'immagine del contenitore [visione artificiale][cv-containers] nel `containerpreview.azurecr.io` Registro contenitori. Si trova all'interno del `microsoft` repository ed è denominato `cognitive-services-read` . Il nome completo dell'immagine del contenitore è, `containerpreview.azurecr.io/microsoft/cognitive-services-read` .

Questa immagine del contenitore include i tag seguenti:

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Viso

L'immagine del contenitore [viso][fa-containers] è reperibile nel `containerpreview.azurecr.io` Registro contenitori. Si trova all'interno del `microsoft` repository ed è denominato `cognitive-services-face` . Il nome completo dell'immagine del contenitore è, `containerpreview.azurecr.io/microsoft/cognitive-services-face` .

Questa immagine del contenitore include i tag seguenti:

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

L'immagine del contenitore del [riconoscitore del modulo][fr-containers] è disponibile nel `containerpreview.azurecr.io` Registro contenitori. Si trova all'interno del `microsoft` repository ed è denominato `cognitive-services-form-recognizer` . Il nome completo dell'immagine del contenitore è, `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` .

Questa immagine del contenitore include i tag seguenti:

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

L'immagine del contenitore [Luis][lu-containers] si trova nel `mcr.microsoft.com` Syndicate del registro contenitori. Si trova all'interno del `azure-cognitive-services` repository ed è denominato `luis` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/luis` .

Questa immagine del contenitore include i tag seguenti:

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

## <a name="custom-speech-to-text"></a>Da Riconoscimento vocale personalizzato a testo

È possibile trovare l'immagine del contenitore [da riconoscimento vocale personalizzato a testo][sp-cstt] nel `containerpreview.azurecr.io` Registro contenitori. Si trova all'interno del `microsoft` repository ed è denominato `cognitive-services-custom-speech-to-text` . Il nome completo dell'immagine del contenitore è, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` .

Questa immagine del contenitore include i tag seguenti:

| Tag immagine            | Note |
|-----------------------|:------|
| `latest`              |       |
| `2.2.0-amd64-preview` |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>Sintesi vocale personalizzata

È possibile trovare l'immagine del contenitore sintesi [vocale personalizzato][sp-ctts] nel `containerpreview.azurecr.io` Registro contenitori. Si trova all'interno del `microsoft` repository ed è denominato `cognitive-services-custom-text-to-speech` . Il nome completo dell'immagine del contenitore è, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` .

Questa immagine del contenitore include i tag seguenti:

| Tag immagine            | Note |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>Riconoscimento vocale

L'immagine del contenitore [di riconoscimento vocale][sp-stt] è reperibile nel `containerpreview.azurecr.io` Registro contenitori. Si trova all'interno del `microsoft` repository ed è denominato `cognitive-services-speech-to-text` . Il nome completo dell'immagine del contenitore è, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` .

Questa immagine del contenitore include i tag seguenti:

| Tag immagine                  | Note                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | Immagine del contenitore con le `en-US` impostazioni locali. |
| `2.2.0-amd64-ar-ae-preview` | Immagine del contenitore con le `ar-AE` impostazioni locali. |
| `2.2.0-amd64-ar-eg-preview` | Immagine del contenitore con le `ar-EG` impostazioni locali. |
| `2.2.0-amd64-ar-kw-preview` | Immagine del contenitore con le `ar-KW` impostazioni locali. |
| `2.2.0-amd64-ar-qa-preview` | Immagine del contenitore con le `ar-QA` impostazioni locali. |
| `2.2.0-amd64-ar-sa-preview` | Immagine del contenitore con le `ar-SA` impostazioni locali. |
| `2.2.0-amd64-ca-es-preview` | Immagine del contenitore con le `ca-ES` impostazioni locali. |
| `2.2.0-amd64-da-dk-preview` | Immagine del contenitore con le `da-DK` impostazioni locali. |
| `2.2.0-amd64-de-de-preview` | Immagine del contenitore con le `de-DE` impostazioni locali. |
| `2.2.0-amd64-en-au-preview` | Immagine del contenitore con le `en-AU` impostazioni locali. |
| `2.2.0-amd64-en-ca-preview` | Immagine del contenitore con le `en-CA` impostazioni locali. |
| `2.2.0-amd64-en-gb-preview` | Immagine del contenitore con le `en-GB` impostazioni locali. |
| `2.2.0-amd64-en-in-preview` | Immagine del contenitore con le `en-IN` impostazioni locali. |
| `2.2.0-amd64-en-nz-preview` | Immagine del contenitore con le `en-NZ` impostazioni locali. |
| `2.2.0-amd64-en-us-preview` | Immagine del contenitore con le `en-US` impostazioni locali. |
| `2.2.0-amd64-es-es-preview` | Immagine del contenitore con le `es-ES` impostazioni locali. |
| `2.2.0-amd64-es-mx-preview` | Immagine del contenitore con le `es-MX` impostazioni locali. |
| `2.2.0-amd64-fi-fi-preview` | Immagine del contenitore con le `fi-FI` impostazioni locali. |
| `2.2.0-amd64-fr-ca-preview` | Immagine del contenitore con le `fr-CA` impostazioni locali. |
| `2.2.0-amd64-fr-fr-preview` | Immagine del contenitore con le `fr-FR` impostazioni locali. |
| `2.2.0-amd64-gu-in-preview` | Immagine del contenitore con le `gu-IN` impostazioni locali. |
| `2.2.0-amd64-hi-in-preview` | Immagine del contenitore con le `hi-IN` impostazioni locali. |
| `2.2.0-amd64-it-it-preview` | Immagine del contenitore con le `it-IT` impostazioni locali. |
| `2.2.0-amd64-ja-jp-preview` | Immagine del contenitore con le `ja-JP` impostazioni locali. |
| `2.2.0-amd64-ko-kr-preview` | Immagine del contenitore con le `ko-KR` impostazioni locali. |
| `2.2.0-amd64-mr-in-preview` | Immagine del contenitore con le `mr-IN` impostazioni locali. |
| `2.2.0-amd64-nb-no-preview` | Immagine del contenitore con le `nb-NO` impostazioni locali. |
| `2.2.0-amd64-nl-nl-preview` | Immagine del contenitore con le `nl-NL` impostazioni locali. |
| `2.2.0-amd64-pl-pl-preview` | Immagine del contenitore con le `pl-PL` impostazioni locali. |
| `2.2.0-amd64-pt-br-preview` | Immagine del contenitore con le `pt-BR` impostazioni locali. |
| `2.2.0-amd64-pt-pt-preview` | Immagine del contenitore con le `pt-PT` impostazioni locali. |
| `2.2.0-amd64-ru-ru-preview` | Immagine del contenitore con le `ru-RU` impostazioni locali. |
| `2.2.0-amd64-sv-se-preview` | Immagine del contenitore con le `sv-SE` impostazioni locali. |
| `2.2.0-amd64-ta-in-preview` | Immagine del contenitore con le `ta-IN` impostazioni locali. |
| `2.2.0-amd64-te-in-preview` | Immagine del contenitore con le `te-IN` impostazioni locali. |
| `2.2.0-amd64-th-th-preview` | Immagine del contenitore con le `th-TH` impostazioni locali. |
| `2.2.0-amd64-tr-tr-preview` | Immagine del contenitore con le `tr-TR` impostazioni locali. |
| `2.2.0-amd64-zh-cn-preview` | Immagine del contenitore con le `zh-CN` impostazioni locali. |
| `2.2.0-amd64-zh-hk-preview` | Immagine del contenitore con le `zh-HK` impostazioni locali. |
| `2.2.0-amd64-zh-tw-preview` | Immagine del contenitore con le `zh-TW` impostazioni locali. |
| `2.1.1-amd64-en-us-preview` | Immagine del contenitore con le `en-US` impostazioni locali. |
| `2.1.1-amd64-ar-ae-preview` | Immagine del contenitore con le `ar-AE` impostazioni locali. |
| `2.1.1-amd64-ar-eg-preview` | Immagine del contenitore con le `ar-EG` impostazioni locali. |
| `2.1.1-amd64-ar-kw-preview` | Immagine del contenitore con le `ar-KW` impostazioni locali. |
| `2.1.1-amd64-ar-qa-preview` | Immagine del contenitore con le `ar-QA` impostazioni locali. |
| `2.1.1-amd64-ar-sa-preview` | Immagine del contenitore con le `ar-SA` impostazioni locali. |
| `2.1.1-amd64-ca-es-preview` | Immagine del contenitore con le `ca-ES` impostazioni locali. |
| `2.1.1-amd64-da-dk-preview` | Immagine del contenitore con le `da-DK` impostazioni locali. |
| `2.1.1-amd64-de-de-preview` | Immagine del contenitore con le `de-DE` impostazioni locali. |
| `2.1.1-amd64-en-au-preview` | Immagine del contenitore con le `en-AU` impostazioni locali. |
| `2.1.1-amd64-en-ca-preview` | Immagine del contenitore con le `en-CA` impostazioni locali. |
| `2.1.1-amd64-en-gb-preview` | Immagine del contenitore con le `en-GB` impostazioni locali. |
| `2.1.1-amd64-en-in-preview` | Immagine del contenitore con le `en-IN` impostazioni locali. |
| `2.1.1-amd64-en-nz-preview` | Immagine del contenitore con le `en-NZ` impostazioni locali. |
| `2.1.1-amd64-en-us-preview` | Immagine del contenitore con le `en-US` impostazioni locali. |
| `2.1.1-amd64-es-es-preview` | Immagine del contenitore con le `es-ES` impostazioni locali. |
| `2.1.1-amd64-es-mx-preview` | Immagine del contenitore con le `es-MX` impostazioni locali. |
| `2.1.1-amd64-fi-fi-preview` | Immagine del contenitore con le `fi-FI` impostazioni locali. |
| `2.1.1-amd64-fr-ca-preview` | Immagine del contenitore con le `fr-CA` impostazioni locali. |
| `2.1.1-amd64-fr-fr-preview` | Immagine del contenitore con le `fr-FR` impostazioni locali. |
| `2.1.1-amd64-gu-in-preview` | Immagine del contenitore con le `gu-IN` impostazioni locali. |
| `2.1.1-amd64-hi-in-preview` | Immagine del contenitore con le `hi-IN` impostazioni locali. |
| `2.1.1-amd64-it-it-preview` | Immagine del contenitore con le `it-IT` impostazioni locali. |
| `2.1.1-amd64-ja-jp-preview` | Immagine del contenitore con le `ja-JP` impostazioni locali. |
| `2.1.1-amd64-ko-kr-preview` | Immagine del contenitore con le `ko-KR` impostazioni locali. |
| `2.1.1-amd64-mr-in-preview` | Immagine del contenitore con le `mr-IN` impostazioni locali. |
| `2.1.1-amd64-nb-no-preview` | Immagine del contenitore con le `nb-NO` impostazioni locali. |
| `2.1.1-amd64-nl-nl-preview` | Immagine del contenitore con le `nl-NL` impostazioni locali. |
| `2.1.1-amd64-pl-pl-preview` | Immagine del contenitore con le `pl-PL` impostazioni locali. |
| `2.1.1-amd64-pt-br-preview` | Immagine del contenitore con le `pt-BR` impostazioni locali. |
| `2.1.1-amd64-pt-pt-preview` | Immagine del contenitore con le `pt-PT` impostazioni locali. |
| `2.1.1-amd64-ru-ru-preview` | Immagine del contenitore con le `ru-RU` impostazioni locali. |
| `2.1.1-amd64-sv-se-preview` | Immagine del contenitore con le `sv-SE` impostazioni locali. |
| `2.1.1-amd64-ta-in-preview` | Immagine del contenitore con le `ta-IN` impostazioni locali. |
| `2.1.1-amd64-te-in-preview` | Immagine del contenitore con le `te-IN` impostazioni locali. |
| `2.1.1-amd64-th-th-preview` | Immagine del contenitore con le `th-TH` impostazioni locali. |
| `2.1.1-amd64-tr-tr-preview` | Immagine del contenitore con le `tr-TR` impostazioni locali. |
| `2.1.1-amd64-zh-cn-preview` | Immagine del contenitore con le `zh-CN` impostazioni locali. |
| `2.1.1-amd64-zh-hk-preview` | Immagine del contenitore con le `zh-HK` impostazioni locali. |
| `2.1.1-amd64-zh-tw-preview` | Immagine del contenitore con le `zh-TW` impostazioni locali. |
| `2.1.0-amd64-ar-ae-preview` | Immagine del contenitore con le `ar-AE` impostazioni locali. |
| `2.1.0-amd64-ar-eg-preview` | Immagine del contenitore con le `ar-EG` impostazioni locali. |
| `2.1.0-amd64-ar-kw-preview` | Immagine del contenitore con le `ar-KW` impostazioni locali. |
| `2.1.0-amd64-ar-qa-preview` | Immagine del contenitore con le `ar-QA` impostazioni locali. |
| `2.1.0-amd64-ar-sa-preview` | Immagine del contenitore con le `ar-SA` impostazioni locali. |
| `2.1.0-amd64-ca-es-preview` | Immagine del contenitore con le `ca-ES` impostazioni locali. |
| `2.1.0-amd64-da-dk-preview` | Immagine del contenitore con le `da-DK` impostazioni locali. |
| `2.1.0-amd64-de-de-preview` | Immagine del contenitore con le `de-DE` impostazioni locali. |
| `2.1.0-amd64-en-au-preview` | Immagine del contenitore con le `en-AU` impostazioni locali. |
| `2.1.0-amd64-en-ca-preview` | Immagine del contenitore con le `en-CA` impostazioni locali. |
| `2.1.0-amd64-en-gb-preview` | Immagine del contenitore con le `en-GB` impostazioni locali. |
| `2.1.0-amd64-en-in-preview` | Immagine del contenitore con le `en-IN` impostazioni locali. |
| `2.1.0-amd64-en-nz-preview` | Immagine del contenitore con le `en-NZ` impostazioni locali. |
| `2.1.0-amd64-en-us-preview` | Immagine del contenitore con le `en-US` impostazioni locali. |
| `2.1.0-amd64-es-es-preview` | Immagine del contenitore con le `es-ES` impostazioni locali. |
| `2.1.0-amd64-es-mx-preview` | Immagine del contenitore con le `es-MX` impostazioni locali. |
| `2.1.0-amd64-fi-fi-preview` | Immagine del contenitore con le `fi-FI` impostazioni locali. |
| `2.1.0-amd64-fr-ca-preview` | Immagine del contenitore con le `fr-CA` impostazioni locali. |
| `2.1.0-amd64-fr-fr-preview` | Immagine del contenitore con le `fr-FR` impostazioni locali. |
| `2.1.0-amd64-gu-in-preview` | Immagine del contenitore con le `gu-IN` impostazioni locali. |
| `2.1.0-amd64-hi-in-preview` | Immagine del contenitore con le `hi-IN` impostazioni locali. |
| `2.1.0-amd64-it-it-preview` | Immagine del contenitore con le `it-IT` impostazioni locali. |
| `2.1.0-amd64-ja-jp-preview` | Immagine del contenitore con le `ja-JP` impostazioni locali. |
| `2.1.0-amd64-ko-kr-preview` | Immagine del contenitore con le `ko-KR` impostazioni locali. |
| `2.1.0-amd64-mr-in-preview` | Immagine del contenitore con le `mr-IN` impostazioni locali. |
| `2.1.0-amd64-nb-no-preview` | Immagine del contenitore con le `nb-NO` impostazioni locali. |
| `2.1.0-amd64-nl-nl-preview` | Immagine del contenitore con le `nl-NL` impostazioni locali. |
| `2.1.0-amd64-pl-pl-preview` | Immagine del contenitore con le `pl-PL` impostazioni locali. |
| `2.1.0-amd64-pt-br-preview` | Immagine del contenitore con le `pt-BR` impostazioni locali. |
| `2.1.0-amd64-pt-pt-preview` | Immagine del contenitore con le `pt-PT` impostazioni locali. |
| `2.1.0-amd64-ru-ru-preview` | Immagine del contenitore con le `ru-RU` impostazioni locali. |
| `2.1.0-amd64-sv-se-preview` | Immagine del contenitore con le `sv-SE` impostazioni locali. |
| `2.1.0-amd64-ta-in-preview` | Immagine del contenitore con le `ta-IN` impostazioni locali. |
| `2.1.0-amd64-te-in-preview` | Immagine del contenitore con le `te-IN` impostazioni locali. |
| `2.1.0-amd64-th-th-preview` | Immagine del contenitore con le `th-TH` impostazioni locali. |
| `2.1.0-amd64-tr-tr-preview` | Immagine del contenitore con le `tr-TR` impostazioni locali. |
| `2.1.0-amd64-zh-cn-preview` | Immagine del contenitore con le `zh-CN` impostazioni locali. |
| `2.1.0-amd64-zh-hk-preview` | Immagine del contenitore con le `zh-HK` impostazioni locali. |
| `2.1.0-amd64-zh-tw-preview` | Immagine del contenitore con le `zh-TW` impostazioni locali. |
| `2.0.3-amd64-ja-jp-preview` | Immagine del contenitore con le `ja-JP` impostazioni locali. |
| `2.0.2-amd64-ar-ae-preview` | Immagine del contenitore con le `ar-AE` impostazioni locali. |
| `2.0.2-amd64-ar-eg-preview` | Immagine del contenitore con le `ar-EG` impostazioni locali. |
| `2.0.2-amd64-ar-kw-preview` | Immagine del contenitore con le `ar-KW` impostazioni locali. |
| `2.0.2-amd64-ar-qa-preview` | Immagine del contenitore con le `ar-QA` impostazioni locali. |
| `2.0.2-amd64-ar-sa-preview` | Immagine del contenitore con le `ar-SA` impostazioni locali. |
| `2.0.2-amd64-ca-es-preview` | Immagine del contenitore con le `ca-ES` impostazioni locali. |
| `2.0.2-amd64-da-dk-preview` | Immagine del contenitore con le `da-DK` impostazioni locali. |
| `2.0.2-amd64-de-de-preview` | Immagine del contenitore con le `de-DE` impostazioni locali. |
| `2.0.2-amd64-en-au-preview` | Immagine del contenitore con le `en-AU` impostazioni locali. |
| `2.0.2-amd64-en-ca-preview` | Immagine del contenitore con le `en-CA` impostazioni locali. |
| `2.0.2-amd64-en-gb-preview` | Immagine del contenitore con le `en-GB` impostazioni locali. |
| `2.0.2-amd64-en-in-preview` | Immagine del contenitore con le `en-IN` impostazioni locali. |
| `2.0.2-amd64-en-nz-preview` | Immagine del contenitore con le `en-NZ` impostazioni locali. |
| `2.0.2-amd64-en-us-preview` | Immagine del contenitore con le `en-US` impostazioni locali. |
| `2.0.2-amd64-es-es-preview` | Immagine del contenitore con le `es-ES` impostazioni locali. |
| `2.0.2-amd64-es-mx-preview` | Immagine del contenitore con le `es-MX` impostazioni locali. |
| `2.0.2-amd64-fi-fi-preview` | Immagine del contenitore con le `fi-FI` impostazioni locali. |
| `2.0.2-amd64-fr-ca-preview` | Immagine del contenitore con le `fr-CA` impostazioni locali. |
| `2.0.2-amd64-fr-fr-preview` | Immagine del contenitore con le `fr-FR` impostazioni locali. |
| `2.0.2-amd64-gu-in-preview` | Immagine del contenitore con le `gu-IN` impostazioni locali. |
| `2.0.2-amd64-hi-in-preview` | Immagine del contenitore con le `hi-IN` impostazioni locali. |
| `2.0.2-amd64-it-it-preview` | Immagine del contenitore con le `it-IT` impostazioni locali. |
| `2.0.2-amd64-ja-jp-preview` | Immagine del contenitore con le `ja-JP` impostazioni locali. |
| `2.0.2-amd64-ko-kr-preview` | Immagine del contenitore con le `ko-KR` impostazioni locali. |
| `2.0.2-amd64-mr-in-preview` | Immagine del contenitore con le `mr-IN` impostazioni locali. |
| `2.0.2-amd64-nb-no-preview` | Immagine del contenitore con le `nb-NO` impostazioni locali. |
| `2.0.2-amd64-nl-nl-preview` | Immagine del contenitore con le `nl-NL` impostazioni locali. |
| `2.0.2-amd64-pl-pl-preview` | Immagine del contenitore con le `pl-PL` impostazioni locali. |
| `2.0.2-amd64-pt-br-preview` | Immagine del contenitore con le `pt-BR` impostazioni locali. |
| `2.0.2-amd64-pt-pt-preview` | Immagine del contenitore con le `pt-PT` impostazioni locali. |
| `2.0.2-amd64-ru-ru-preview` | Immagine del contenitore con le `ru-RU` impostazioni locali. |
| `2.0.2-amd64-sv-se-preview` | Immagine del contenitore con le `sv-SE` impostazioni locali. |
| `2.0.2-amd64-ta-in-preview` | Immagine del contenitore con le `ta-IN` impostazioni locali. |
| `2.0.2-amd64-te-in-preview` | Immagine del contenitore con le `te-IN` impostazioni locali. |
| `2.0.2-amd64-th-th-preview` | Immagine del contenitore con le `th-TH` impostazioni locali. |
| `2.0.2-amd64-tr-tr-preview` | Immagine del contenitore con le `tr-TR` impostazioni locali. |
| `2.0.2-amd64-zh-cn-preview` | Immagine del contenitore con le `zh-CN` impostazioni locali. |
| `2.0.2-amd64-zh-hk-preview` | Immagine del contenitore con le `zh-HK` impostazioni locali. |
| `2.0.2-amd64-zh-tw-preview` | Immagine del contenitore con le `zh-TW` impostazioni locali. |
| `2.0.1-amd64-ar-ae-preview` | Immagine del contenitore con le `ar-AE` impostazioni locali. |
| `2.0.1-amd64-ar-eg-preview` | Immagine del contenitore con le `ar-EG` impostazioni locali. |
| `2.0.1-amd64-ar-kw-preview` | Immagine del contenitore con le `ar-KW` impostazioni locali. |
| `2.0.1-amd64-ar-qa-preview` | Immagine del contenitore con le `ar-QA` impostazioni locali. |
| `2.0.1-amd64-ar-sa-preview` | Immagine del contenitore con le `ar-SA` impostazioni locali. |
| `2.0.1-amd64-ca-es-preview` | Immagine del contenitore con le `ca-ES` impostazioni locali. |
| `2.0.1-amd64-da-dk-preview` | Immagine del contenitore con le `da-DK` impostazioni locali. |
| `2.0.1-amd64-de-de-preview` | Immagine del contenitore con le `de-DE` impostazioni locali. |
| `2.0.1-amd64-en-au-preview` | Immagine del contenitore con le `en-AU` impostazioni locali. |
| `2.0.1-amd64-en-ca-preview` | Immagine del contenitore con le `en-CA` impostazioni locali. |
| `2.0.1-amd64-en-gb-preview` | Immagine del contenitore con le `en-GB` impostazioni locali. |
| `2.0.1-amd64-en-in-preview` | Immagine del contenitore con le `en-IN` impostazioni locali. |
| `2.0.1-amd64-en-nz-preview` | Immagine del contenitore con le `en-NZ` impostazioni locali. |
| `2.0.1-amd64-en-us-preview` | Immagine del contenitore con le `en-US` impostazioni locali. |
| `2.0.1-amd64-es-es-preview` | Immagine del contenitore con le `es-ES` impostazioni locali. |
| `2.0.1-amd64-es-mx-preview` | Immagine del contenitore con le `es-MX` impostazioni locali. |
| `2.0.1-amd64-fi-fi-preview` | Immagine del contenitore con le `fi-FI` impostazioni locali. |
| `2.0.1-amd64-fr-ca-preview` | Immagine del contenitore con le `fr-CA` impostazioni locali. |
| `2.0.1-amd64-fr-fr-preview` | Immagine del contenitore con le `fr-FR` impostazioni locali. |
| `2.0.1-amd64-gu-in-preview` | Immagine del contenitore con le `gu-IN` impostazioni locali. |
| `2.0.1-amd64-hi-in-preview` | Immagine del contenitore con le `hi-IN` impostazioni locali. |
| `2.0.1-amd64-it-it-preview` | Immagine del contenitore con le `it-IT` impostazioni locali. |
| `2.0.1-amd64-ja-jp-preview` | Immagine del contenitore con le `ja-JP` impostazioni locali. |
| `2.0.1-amd64-ko-kr-preview` | Immagine del contenitore con le `ko-KR` impostazioni locali. |
| `2.0.1-amd64-mr-in-preview` | Immagine del contenitore con le `mr-IN` impostazioni locali. |
| `2.0.1-amd64-nb-no-preview` | Immagine del contenitore con le `nb-NO` impostazioni locali. |
| `2.0.1-amd64-nl-nl-preview` | Immagine del contenitore con le `nl-NL` impostazioni locali. |
| `2.0.1-amd64-pl-pl-preview` | Immagine del contenitore con le `pl-PL` impostazioni locali. |
| `2.0.1-amd64-pt-br-preview` | Immagine del contenitore con le `pt-BR` impostazioni locali. |
| `2.0.1-amd64-pt-pt-preview` | Immagine del contenitore con le `pt-PT` impostazioni locali. |
| `2.0.1-amd64-ru-ru-preview` | Immagine del contenitore con le `ru-RU` impostazioni locali. |
| `2.0.1-amd64-sv-se-preview` | Immagine del contenitore con le `sv-SE` impostazioni locali. |
| `2.0.1-amd64-ta-in-preview` | Immagine del contenitore con le `ta-IN` impostazioni locali. |
| `2.0.1-amd64-te-in-preview` | Immagine del contenitore con le `te-IN` impostazioni locali. |
| `2.0.1-amd64-th-th-preview` | Immagine del contenitore con le `th-TH` impostazioni locali. |
| `2.0.1-amd64-tr-tr-preview` | Immagine del contenitore con le `tr-TR` impostazioni locali. |
| `2.0.1-amd64-zh-cn-preview` | Immagine del contenitore con le `zh-CN` impostazioni locali. |
| `2.0.1-amd64-zh-hk-preview` | Immagine del contenitore con le `zh-HK` impostazioni locali. |
| `2.0.1-amd64-zh-tw-preview` | Immagine del contenitore con le `zh-TW` impostazioni locali. |
| `2.0.0-amd64-ar-eg-preview` | Immagine del contenitore con le `ar-EG` impostazioni locali. |
| `2.0.0-amd64-ca-es-preview` | Immagine del contenitore con le `ca-ES` impostazioni locali. |
| `2.0.0-amd64-da-dk-preview` | Immagine del contenitore con le `da-DK` impostazioni locali. |
| `2.0.0-amd64-de-de-preview` | Immagine del contenitore con le `de-DE` impostazioni locali. |
| `2.0.0-amd64-en-au-preview` | Immagine del contenitore con le `en-AU` impostazioni locali. |
| `2.0.0-amd64-en-ca-preview` | Immagine del contenitore con le `en-CA` impostazioni locali. |
| `2.0.0-amd64-en-gb-preview` | Immagine del contenitore con le `en-GB` impostazioni locali. |
| `2.0.0-amd64-en-in-preview` | Immagine del contenitore con le `en-IN` impostazioni locali. |
| `2.0.0-amd64-en-nz-preview` | Immagine del contenitore con le `en-NZ` impostazioni locali. |
| `2.0.0-amd64-en-us-preview` | Immagine del contenitore con le `en-US` impostazioni locali. |
| `2.0.0-amd64-es-es-preview` | Immagine del contenitore con le `es-ES` impostazioni locali. |
| `2.0.0-amd64-es-mx-preview` | Immagine del contenitore con le `es-MX` impostazioni locali. |
| `2.0.0-amd64-fi-fi-preview` | Immagine del contenitore con le `fi-FI` impostazioni locali. |
| `2.0.0-amd64-fr-ca-preview` | Immagine del contenitore con le `fr-CA` impostazioni locali. |
| `2.0.0-amd64-fr-fr-preview` | Immagine del contenitore con le `fr-FR` impostazioni locali. |
| `2.0.0-amd64-hi-in-preview` | Immagine del contenitore con le `hi-IN` impostazioni locali. |
| `2.0.0-amd64-it-it-preview` | Immagine del contenitore con le `it-IT` impostazioni locali. |
| `2.0.0-amd64-ja-jp-preview` | Immagine del contenitore con le `ja-JP` impostazioni locali. |
| `2.0.0-amd64-ko-kr-preview` | Immagine del contenitore con le `ko-KR` impostazioni locali. |
| `2.0.0-amd64-nb-no-preview` | Immagine del contenitore con le `nb-NO` impostazioni locali. |
| `2.0.0-amd64-nl-nl-preview` | Immagine del contenitore con le `nl-NL` impostazioni locali. |
| `2.0.0-amd64-pl-pl-preview` | Immagine del contenitore con le `pl-PL` impostazioni locali. |
| `2.0.0-amd64-pt-br-preview` | Immagine del contenitore con le `pt-BR` impostazioni locali. |
| `2.0.0-amd64-pt-pt-preview` | Immagine del contenitore con le `pt-PT` impostazioni locali. |
| `2.0.0-amd64-ru-ru-preview` | Immagine del contenitore con le `ru-RU` impostazioni locali. |
| `2.0.0-amd64-sv-se-preview` | Immagine del contenitore con le `sv-SE` impostazioni locali. |
| `2.0.0-amd64-th-th-preview` | Immagine del contenitore con le `th-TH` impostazioni locali. |
| `2.0.0-amd64-tr-tr-preview` | Immagine del contenitore con le `tr-TR` impostazioni locali. |
| `2.0.0-amd64-zh-cn-preview` | Immagine del contenitore con le `zh-CN` impostazioni locali. |
| `2.0.0-amd64-zh-hk-preview` | Immagine del contenitore con le `zh-HK` impostazioni locali. |
| `2.0.0-amd64-zh-tw-preview` | Immagine del contenitore con le `zh-TW` impostazioni locali. |
| `1.2.0-amd64-de-de-preview` | Immagine del contenitore con le `de-DE` impostazioni locali. |
| `1.2.0-amd64-en-au-preview` | Immagine del contenitore con le `en-AU` impostazioni locali. |
| `1.2.0-amd64-en-ca-preview` | Immagine del contenitore con le `en-CA` impostazioni locali. |
| `1.2.0-amd64-en-gb-preview` | Immagine del contenitore con le `en-GB` impostazioni locali. |
| `1.2.0-amd64-en-in-preview` | Immagine del contenitore con le `en-IN` impostazioni locali. |
| `1.2.0-amd64-en-us-preview` | Immagine del contenitore con le `en-US` impostazioni locali. |
| `1.2.0-amd64-es-es-preview` | Immagine del contenitore con le `es-ES` impostazioni locali. |
| `1.2.0-amd64-es-mx-preview` | Immagine del contenitore con le `es-MX` impostazioni locali. |
| `1.2.0-amd64-fr-ca-preview` | Immagine del contenitore con le `fr-CA` impostazioni locali. |
| `1.2.0-amd64-fr-fr-preview` | Immagine del contenitore con le `fr-FR` impostazioni locali. |
| `1.2.0-amd64-it-it-preview` | Immagine del contenitore con le `it-IT` impostazioni locali. |
| `1.2.0-amd64-ja-jp-preview` | Immagine del contenitore con le `ja-JP` impostazioni locali. |
| `1.2.0-amd64-pt-br-preview` | Immagine del contenitore con le `pt-BR` impostazioni locali. |
| `1.2.0-amd64-zh-cn-preview` | Immagine del contenitore con le `zh-CN` impostazioni locali. |
| `1.1.3-amd64-de-de-preview` | Immagine del contenitore con le `de-DE` impostazioni locali. |
| `1.1.3-amd64-en-au-preview` | Immagine del contenitore con le `en-AU` impostazioni locali. |
| `1.1.3-amd64-en-ca-preview` | Immagine del contenitore con le `en-CA` impostazioni locali. |
| `1.1.3-amd64-en-gb-preview` | Immagine del contenitore con le `en-GB` impostazioni locali. |
| `1.1.3-amd64-en-in-preview` | Immagine del contenitore con le `en-IN` impostazioni locali. |
| `1.1.3-amd64-en-us-preview` | Immagine del contenitore con le `en-US` impostazioni locali. |
| `1.1.3-amd64-es-es-preview` | Immagine del contenitore con le `es-ES` impostazioni locali. |
| `1.1.3-amd64-es-mx-preview` | Immagine del contenitore con le `es-MX` impostazioni locali. |
| `1.1.3-amd64-fr-ca-preview` | Immagine del contenitore con le `fr-CA` impostazioni locali. |
| `1.1.3-amd64-fr-fr-preview` | Immagine del contenitore con le `fr-FR` impostazioni locali. |
| `1.1.3-amd64-it-it-preview` | Immagine del contenitore con le `it-IT` impostazioni locali. |
| `1.1.3-amd64-ja-jp-preview` | Immagine del contenitore con le `ja-JP` impostazioni locali. |
| `1.1.3-amd64-pt-br-preview` | Immagine del contenitore con le `pt-BR` impostazioni locali. |
| `1.1.3-amd64-zh-cn-preview` | Immagine del contenitore con le `zh-CN` impostazioni locali. |
| `1.1.2-amd64-de-de-preview` | Immagine del contenitore con le `de-DE` impostazioni locali. |
| `1.1.2-amd64-en-au-preview` | Immagine del contenitore con le `en-AU` impostazioni locali. |
| `1.1.2-amd64-en-ca-preview` | Immagine del contenitore con le `en-CA` impostazioni locali. |
| `1.1.2-amd64-en-gb-preview` | Immagine del contenitore con le `en-GB` impostazioni locali. |
| `1.1.2-amd64-en-in-preview` | Immagine del contenitore con le `en-IN` impostazioni locali. |
| `1.1.2-amd64-en-us-preview` | Immagine del contenitore con le `en-US` impostazioni locali. |
| `1.1.2-amd64-es-es-preview` | Immagine del contenitore con le `es-ES` impostazioni locali. |
| `1.1.2-amd64-es-mx-preview` | Immagine del contenitore con le `es-MX` impostazioni locali. |
| `1.1.2-amd64-fr-ca-preview` | Immagine del contenitore con le `fr-CA` impostazioni locali. |
| `1.1.2-amd64-fr-fr-preview` | Immagine del contenitore con le `fr-FR` impostazioni locali. |
| `1.1.2-amd64-it-it-preview` | Immagine del contenitore con le `it-IT` impostazioni locali. |
| `1.1.2-amd64-ja-jp-preview` | Immagine del contenitore con le `ja-JP` impostazioni locali. |
| `1.1.2-amd64-pt-br-preview` | Immagine del contenitore con le `pt-BR` impostazioni locali. |
| `1.1.2-amd64-zh-cn-preview` | Immagine del contenitore con le `zh-CN` impostazioni locali. |
| `1.1.1-amd64-de-de-preview` | Immagine del contenitore con le `de-DE` impostazioni locali. |
| `1.1.1-amd64-en-au-preview` | Immagine del contenitore con le `en-AU` impostazioni locali. |
| `1.1.1-amd64-en-ca-preview` | Immagine del contenitore con le `en-CA` impostazioni locali. |
| `1.1.1-amd64-en-gb-preview` | Immagine del contenitore con le `en-GB` impostazioni locali. |
| `1.1.1-amd64-en-in-preview` | Immagine del contenitore con le `en-IN` impostazioni locali. |
| `1.1.1-amd64-en-us-preview` | Immagine del contenitore con le `en-US` impostazioni locali. |
| `1.1.1-amd64-es-es-preview` | Immagine del contenitore con le `es-ES` impostazioni locali. |
| `1.1.1-amd64-es-mx-preview` | Immagine del contenitore con le `es-MX` impostazioni locali. |
| `1.1.1-amd64-fr-ca-preview` | Immagine del contenitore con le `fr-CA` impostazioni locali. |
| `1.1.1-amd64-fr-fr-preview` | Immagine del contenitore con le `fr-FR` impostazioni locali. |
| `1.1.1-amd64-it-it-preview` | Immagine del contenitore con le `it-IT` impostazioni locali. |
| `1.1.1-amd64-ja-jp-preview` | Immagine del contenitore con le `ja-JP` impostazioni locali. |
| `1.1.1-amd64-pt-br-preview` | Immagine del contenitore con le `pt-BR` impostazioni locali. |
| `1.1.1-amd64-zh-cn-preview` | Immagine del contenitore con le `zh-CN` impostazioni locali. |
| `1.1.0-amd64-de-de-preview` | Immagine del contenitore con le `de-DE` impostazioni locali. |
| `1.1.0-amd64-en-au-preview` | Immagine del contenitore con le `en-AU` impostazioni locali. |
| `1.1.0-amd64-en-ca-preview` | Immagine del contenitore con le `en-CA` impostazioni locali. |
| `1.1.0-amd64-en-gb-preview` | Immagine del contenitore con le `en-GB` impostazioni locali. |
| `1.1.0-amd64-en-in-preview` | Immagine del contenitore con le `en-IN` impostazioni locali. |
| `1.1.0-amd64-en-us-preview` | Immagine del contenitore con le `en-US` impostazioni locali. |
| `1.1.0-amd64-es-es-preview` | Immagine del contenitore con le `es-ES` impostazioni locali. |
| `1.1.0-amd64-es-mx-preview` | Immagine del contenitore con le `es-MX` impostazioni locali. |
| `1.1.0-amd64-fr-ca-preview` | Immagine del contenitore con le `fr-CA` impostazioni locali. |
| `1.1.0-amd64-fr-fr-preview` | Immagine del contenitore con le `fr-FR` impostazioni locali. |
| `1.1.0-amd64-it-it-preview` | Immagine del contenitore con le `it-IT` impostazioni locali. |
| `1.1.0-amd64-ja-jp-preview` | Immagine del contenitore con le `ja-JP` impostazioni locali. |
| `1.1.0-amd64-pt-br-preview` | Immagine del contenitore con le `pt-BR` impostazioni locali. |
| `1.1.0-amd64-zh-cn-preview` | Immagine del contenitore con le `zh-CN` impostazioni locali. |
| `1.0.0-amd64-de-de-preview` | Immagine del contenitore con le `de-DE` impostazioni locali. |
| `1.0.0-amd64-en-au-preview` | Immagine del contenitore con le `en-AU` impostazioni locali. |
| `1.0.0-amd64-en-ca-preview` | Immagine del contenitore con le `en-CA` impostazioni locali. |
| `1.0.0-amd64-en-gb-preview` | Immagine del contenitore con le `en-GB` impostazioni locali. |
| `1.0.0-amd64-en-in-preview` | Immagine del contenitore con le `en-IN` impostazioni locali. |
| `1.0.0-amd64-en-us-preview` | Immagine del contenitore con le `en-US` impostazioni locali. |
| `1.0.0-amd64-es-es-preview` | Immagine del contenitore con le `es-ES` impostazioni locali. |
| `1.0.0-amd64-es-mx-preview` | Immagine del contenitore con le `es-MX` impostazioni locali. |
| `1.0.0-amd64-fr-ca-preview` | Immagine del contenitore con le `fr-CA` impostazioni locali. |
| `1.0.0-amd64-fr-fr-preview` | Immagine del contenitore con le `fr-FR` impostazioni locali. |
| `1.0.0-amd64-it-it-preview` | Immagine del contenitore con le `it-IT` impostazioni locali. |
| `1.0.0-amd64-ja-jp-preview` | Immagine del contenitore con le `ja-JP` impostazioni locali. |
| `1.0.0-amd64-pt-br-preview` | Immagine del contenitore con le `pt-BR` impostazioni locali. |
| `1.0.0-amd64-zh-cn-preview` | Immagine del contenitore con le `zh-CN` impostazioni locali. |

## <a name="text-to-speech"></a>Sintesi vocale

L'immagine del contenitore [da sintesi vocale][sp-tts] è reperibile nel `containerpreview.azurecr.io` Registro contenitori. Si trova all'interno del `microsoft` repository ed è denominato `cognitive-services-text-to-speech` . Il nome completo dell'immagine del contenitore è, `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` .

Questa immagine del contenitore include i tag seguenti:

| Tag immagine                                  | Note                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-JessaRUS` voce.        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | Immagine del contenitore con le `ar-EG` impostazioni locali e la `ar-EG-Hoda` voce.            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | Immagine del contenitore con le `ar-SA` impostazioni locali e la `ar-SA-Naayf` voce.           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | Immagine del contenitore con le `bg-BG` impostazioni locali e la `bg-BG-Ivan` voce.            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | Immagine del contenitore con le `ca-ES` impostazioni locali e la `ca-ES-HerenaRUS` voce.       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | Immagine del contenitore con le `cs-CZ` impostazioni locali e la `cs-CZ-Jakub` voce.           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | Immagine del contenitore con le `da-DK` impostazioni locali e la `da-DK-HelleRUS` voce.        |
| `1.3.0-amd64-de-at-michael-preview`         | Immagine del contenitore con le `de-AT` impostazioni locali e la `de-AT-Michael` voce.         |
| `1.3.0-amd64-de-ch-karsten-preview`         | Immagine del contenitore con le `de-CH` impostazioni locali e la `de-CH-Karsten` voce.         |
| `1.3.0-amd64-de-de-hedda-preview`           | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-Hedda` voce.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-Hedda` voce.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-HeddaRUS` voce.        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-Stefan-Apollo` voce.   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | Immagine del contenitore con le `el-GR` impostazioni locali e la `el-GR-Stefanos` voce.        |
| `1.3.0-amd64-en-au-catherine-preview`       | Immagine del contenitore con le `en-AU` impostazioni locali e la `en-AU-Catherine` voce.       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | Immagine del contenitore con le `en-AU` impostazioni locali e la `en-AU-HayleyRUS` voce.       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | Immagine del contenitore con le `en-CA` impostazioni locali e la `en-CA-HeatherRUS` voce.      |
| `1.3.0-amd64-en-ca-linda-preview`           | Immagine del contenitore con le `en-CA` impostazioni locali e la `en-CA-Linda` voce.           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-George-Apollo` voce.   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-HazelRUS` voce.        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-Susan-Apollo` voce.    |
| `1.3.0-amd64-en-ie-sean-preview`            | Immagine del contenitore con le `en-IE` impostazioni locali e la `en-IE-Sean` voce.            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-Heera-Apollo` voce.    |
| `1.3.0-amd64-en-in-priyarus-preview`        | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-PriyaRUS` voce.        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-Ravi-Apollo` voce.     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-BenjaminRUS` voce.     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-Guy24kRUS` voce.       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-Jessa24kRUS` voce.     |
| `1.3.0-amd64-en-us-jessarus-preview`        | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-JessaRUS` voce.        |
| `1.3.0-amd64-en-us-zirarus-preview`         | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-ZiraRUS` voce.         |
| `1.3.0-amd64-es-es-helenarus-preview`       | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-HelenaRUS` voce.       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-Laura-Apollo` voce.    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-Pablo-Apollo` voce.    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | Immagine del contenitore con le `es-MX` impostazioni locali e la `es-MX-HildaRUS` voce.        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | Immagine del contenitore con le `es-MX` impostazioni locali e la `es-MX-Raul-Apollo` voce.     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | Immagine del contenitore con le `fi-FI` impostazioni locali e la `fi-FI-HeidiRUS` voce.        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | Immagine del contenitore con le `fr-CA` impostazioni locali e la `fr-CA-Caroline` voce.        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | Immagine del contenitore con le `fr-CA` impostazioni locali e la `fr-CA-HarmonieRUS` voce.     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | Immagine del contenitore con le `fr-CH` impostazioni locali e la `fr-CH-Guillaume` voce.       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-HortenseRUS` voce.     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-Julie-Apollo` voce.    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-Paul-Apollo` voce.     |
| `1.3.0-amd64-he-il-asaf-preview`            | Immagine del contenitore con le `he-IL` impostazioni locali e la `he-IL-Asaf` voce.            |
| `1.3.0-amd64-hi-in-hemant-preview`          | Immagine del contenitore con le `hi-IN` impostazioni locali e la `hi-IN-Hemant` voce.          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Immagine del contenitore con le `hi-IN` impostazioni locali e la `hi-IN-Kalpana-Apollo` voce.  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Immagine del contenitore con le `hi-IN` impostazioni locali e la `hi-IN-Kalpana` voce.         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | Immagine del contenitore con le `hi-IN` impostazioni locali e la `hi-IN-Kalpana` voce.         |
| `1.3.0-amd64-hr-hr-matej-preview`           | Immagine del contenitore con le `hr-HR` impostazioni locali e la `hr-HR-Matej` voce.           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | Immagine del contenitore con le `hu-HU` impostazioni locali e la `hu-HU-Szabolcs` voce.        |
| `1.3.0-amd64-id-id-andika-preview`          | Immagine del contenitore con le `id-ID` impostazioni locali e la `id-ID-Andika` voce.          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | Immagine del contenitore con le `it-IT` impostazioni locali e la `it-IT-Cosimo-Apollo` voce.   |
| `1.3.0-amd64-it-it-luciarus-preview`        | Immagine del contenitore con le `it-IT` impostazioni locali e la `it-IT-LuciaRUS` voce.        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-Ayumi-Apollo` voce.    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-HarukaRUS` voce.       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-Ichiro-Apollo` voce.   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | Immagine del contenitore con le `ko-KR` impostazioni locali e la `ko-KR-HeamiRUS` voce.        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | Immagine del contenitore con le `ms-MY` impostazioni locali e la `ms-MY-Rizwan` voce.          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | Immagine del contenitore con le `nb-NO` impostazioni locali e la `nb-NO-HuldaRUS` voce.        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | Immagine del contenitore con le `nl-NL` impostazioni locali e la `nl-NL-HannaRUS` voce.        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | Immagine del contenitore con le `pl-PL` impostazioni locali e la `pl-PL-PaulinaRUS` voce.      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | Immagine del contenitore con le `pt-BR` impostazioni locali e la `pt-BR-Daniel-Apollo` voce.   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | Immagine del contenitore con le `pt-BR` impostazioni locali e la `pt-BR-HeloisaRUS` voce.      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | Immagine del contenitore con le `pt-PT` impostazioni locali e la `pt-PT-HeliaRUS` voce.        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | Immagine del contenitore con le `ro-RO` impostazioni locali e la `ro-RO-Andrei` voce.          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | Immagine del contenitore con le `ru-RU` impostazioni locali e la `ru-RU-EkaterinaRUS` voce.    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | Immagine del contenitore con le `ru-RU` impostazioni locali e la `ru-RU-Irina-Apollo` voce.    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | Immagine del contenitore con le `ru-RU` impostazioni locali e la `ru-RU-Pavel-Apollo` voce.    |
| `1.3.0-amd64-sk-sk-filip-preview`           | Immagine del contenitore con le `sk-SK` impostazioni locali e la `sk-SK-Filip` voce.           |
| `1.3.0-amd64-sl-si-lado-preview`            | Immagine del contenitore con le `sl-SI` impostazioni locali e la `sl-SI-Lado` voce.            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | Immagine del contenitore con le `sv-SE` impostazioni locali e la `sv-SE-HedvigRUS` voce.       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | Immagine del contenitore con le `ta-IN` impostazioni locali e la `ta-IN-Valluvar` voce.        |
| `1.3.0-amd64-te-in-chitra-preview`          | Immagine del contenitore con le `te-IN` impostazioni locali e la `te-IN-Chitra` voce.          |
| `1.3.0-amd64-th-th-pattara-preview`         | Immagine del contenitore con le `th-TH` impostazioni locali e la `th-TH-Pattara` voce.         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | Immagine del contenitore con le `tr-TR` impostazioni locali e la `tr-TR-SedaRUS` voce.         |
| `1.3.0-amd64-vi-vn-an-preview`              | Immagine del contenitore con le `vi-VN` impostazioni locali e la `vi-VN-An` voce.              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-HuihuiRUS` voce.       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-Kangkang-Apollo` voce. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-Yaoyao-Apollo` voce.   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | Immagine del contenitore con le `zh-HK` impostazioni locali e la `zh-HK-Danny-Apollo` voce.    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | Immagine del contenitore con le `zh-HK` impostazioni locali e la `zh-HK-Tracy-Apollo` voce.    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | Immagine del contenitore con le `zh-HK` impostazioni locali e la `zh-HK-TracyRUS` voce.        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | Immagine del contenitore con le `zh-TW` impostazioni locali e la `zh-TW-HanHanRUS` voce.       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | Immagine del contenitore con le `zh-TW` impostazioni locali e la `zh-TW-Yating-Apollo` voce.   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | Immagine del contenitore con le `zh-TW` impostazioni locali e la `zh-TW-Zhiwei-Apollo` voce.   |
| `1.2.0-amd64-de-de-heddarus-preview`        | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-Hedda` voce.           |
| `1.2.0-amd64-de-de-heddarus-preview`        | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-HeddaRUS` voce.        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-Stefan-Apollo` voce.   |
| `1.2.0-amd64-en-au-catherine-preview`       | Immagine del contenitore con le `en-AU` impostazioni locali e la `en-AU-Catherine` voce.       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | Immagine del contenitore con le `en-AU` impostazioni locali e la `en-AU-HayleyRUS` voce.       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-George-Apollo` voce.   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-HazelRUS` voce.        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-Susan-Apollo` voce.    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-Heera-Apollo` voce.    |
| `1.2.0-amd64-en-in-priyarus-preview`        | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-PriyaRUS` voce.        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-Ravi-Apollo` voce.     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-BenjaminRUS` voce.     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-Guy24kRUS` voce.       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-Jessa24kRUS` voce.     |
| `1.2.0-amd64-en-us-jessarus-preview`        | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-JessaRUS` voce.        |
| `1.2.0-amd64-en-us-zirarus-preview`         | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-ZiraRUS` voce.         |
| `1.2.0-amd64-es-es-helenarus-preview`       | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-HelenaRUS` voce.       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-Laura-Apollo` voce.    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-Pablo-Apollo` voce.    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | Immagine del contenitore con le `es-MX` impostazioni locali e la `es-MX-HildaRUS` voce.        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | Immagine del contenitore con le `es-MX` impostazioni locali e la `es-MX-Raul-Apollo` voce.     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | Immagine del contenitore con le `fr-CA` impostazioni locali e la `fr-CA-Caroline` voce.        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | Immagine del contenitore con le `fr-CA` impostazioni locali e la `fr-CA-HarmonieRUS` voce.     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-HortenseRUS` voce.     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-Julie-Apollo` voce.    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-Paul-Apollo` voce.     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | Immagine del contenitore con le `it-IT` impostazioni locali e la `it-IT-Cosimo-Apollo` voce.   |
| `1.2.0-amd64-it-it-luciarus-preview`        | Immagine del contenitore con le `it-IT` impostazioni locali e la `it-IT-LuciaRUS` voce.        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-Ayumi-Apollo` voce.    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-HarukaRUS` voce.       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-Ichiro-Apollo` voce.   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | Immagine del contenitore con le `ko-KR` impostazioni locali e la `ko-KR-HeamiRUS` voce.        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | Immagine del contenitore con le `pt-BR` impostazioni locali e la `pt-BR-Daniel-Apollo` voce.   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | Immagine del contenitore con le `pt-BR` impostazioni locali e la `pt-BR-HeloisaRUS` voce.      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-HuihuiRUS` voce.       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-Kangkang-Apollo` voce. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-Yaoyao-Apollo` voce.   |
| `1.1.0-amd64-de-de-hedda-preview`           | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-Hedda` voce.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-Hedda` voce.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-HeddaRUS` voce.        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-Stefan-Apollo` voce.   |
| `1.1.0-amd64-en-au-catherine-preview`       | Immagine del contenitore con le `en-AU` impostazioni locali e la `en-AU-Catherine` voce.       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | Immagine del contenitore con le `en-AU` impostazioni locali e la `en-AU-HayleyRUS` voce.       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-George-Apollo` voce.   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-HazelRUS` voce.        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-Susan-Apollo` voce.    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-Heera-Apollo` voce.    |
| `1.1.0-amd64-en-in-priyarus-preview`        | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-PriyaRUS` voce.        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-Ravi-Apollo` voce.     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-BenjaminRUS` voce.     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-Guy24kRUS` voce.       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-Jessa24kRUS` voce.     |
| `1.1.0-amd64-en-us-jessarus-preview`        | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-JessaRUS` voce.        |
| `1.1.0-amd64-en-us-zirarus-preview`         | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-ZiraRUS` voce.         |
| `1.1.0-amd64-es-es-helenarus-preview`       | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-HelenaRUS` voce.       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-Laura-Apollo` voce.    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-Pablo-Apollo` voce.    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | Immagine del contenitore con le `es-MX` impostazioni locali e la `es-MX-HildaRUS` voce.        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | Immagine del contenitore con le `es-MX` impostazioni locali e la `es-MX-Raul-Apollo` voce.     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | Immagine del contenitore con le `fr-CA` impostazioni locali e la `fr-CA-Caroline` voce.        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | Immagine del contenitore con le `fr-CA` impostazioni locali e la `fr-CA-HarmonieRUS` voce.     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-HortenseRUS` voce.     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-Julie-Apollo` voce.    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-Paul-Apollo` voce.     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | Immagine del contenitore con le `it-IT` impostazioni locali e la `it-IT-Cosimo-Apollo` voce.   |
| `1.1.0-amd64-it-it-luciarus-preview`        | Immagine del contenitore con le `it-IT` impostazioni locali e la `it-IT-LuciaRUS` voce.        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-Ayumi-Apollo` voce.    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-HarukaRUS` voce.       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-Ichiro-Apollo` voce.   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | Immagine del contenitore con le `ko-KR` impostazioni locali e la `ko-KR-HeamiRUS` voce.        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | Immagine del contenitore con le `pt-BR` impostazioni locali e la `pt-BR-Daniel-Apollo` voce.   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | Immagine del contenitore con le `pt-BR` impostazioni locali e la `pt-BR-HeloisaRUS` voce.      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-HuihuiRUS` voce.       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-Kangkang-Apollo` voce. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-Yaoyao-Apollo` voce.   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-BenjaminRUS` voce.     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-Guy24kRUS` voce.       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-Jessa24kRUS` voce.     |
| `1.0.0-amd64-en-us-jessarus-preview`        | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-JessaRUS` voce.        |
| `1.0.0-amd64-en-us-zirarus-preview`         | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-ZiraRUS` voce.         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-HuihuiRUS` voce.       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-Kangkang-Apollo` voce. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-Yaoyao-Apollo` voce.   |

## <a name="key-phrase-extraction"></a>Estrazione frasi chiave

Il [estrazione frasi chiave][ta-kp] immagine del contenitore è disponibile nel `mcr.microsoft.com` Syndicate del registro contenitori. Si trova all'interno del `azure-cognitive-services` repository ed è denominato `keyphrase` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/keyphrase` .

Questa immagine del contenitore include i tag seguenti:

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>Rilevamento lingua

Il [rilevamento lingua][ta-la] immagine del contenitore è disponibile nel `mcr.microsoft.com` Syndicate del registro contenitori. Si trova all'interno del `azure-cognitive-services` repository ed è denominato `language` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/language` .

Questa immagine del contenitore include i tag seguenti:

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>Analisi del sentiment

Il [analisi del sentiment][ta-se] immagine del contenitore è disponibile nel `mcr.microsoft.com` Syndicate del registro contenitori. Si trova all'interno del `azure-cognitive-services` repository ed è denominato `sentiment` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/sentiment` .

Questa immagine del contenitore include i tag seguenti:

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
| `1.1.009301-amd64-preview`    | Analisi del sentiment v2      |
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
