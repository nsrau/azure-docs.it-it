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
ms.openlocfilehash: 850d7b9732ad547d502b042e7eaad5d3b471030e
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926001"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Tag dell'immagine del contenitore di servizi cognitivi di Azure

Servizi cognitivi di Azure offre molte immagini del contenitore. I registri dei contenitori e i repository corrispondenti variano tra le immagini del contenitore. Ogni nome dell'immagine del contenitore offre più tag. Un tag dell'immagine del contenitore è un meccanismo di controllo delle versioni dell'immagine del contenitore. Questo articolo è destinato a essere usato come riferimento completo per l'elenco di tutte le immagini del contenitore di servizi cognitivi e dei relativi tag disponibili.

> [!TIP]
> Quando si [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)USA, prestare particolare attenzione alle maiuscole e minuscole del registro contenitori, al repository, al nome dell'immagine del contenitore e al tag corrispondente, poiché fanno **distinzione tra maiuscole**e minuscole.

## <a name="anomaly-detector"></a>Rilevamento anomalie

È possibile trovare l'immagine del contenitore del [rilevatore di anomalie][ad-containers] nel registro `containerpreview.azurecr.io` contenitori. Si trova all'interno del `microsoft` repository ed è denominato. `cognitive-services-anomaly-detector` Il nome completo dell'immagine del contenitore è `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`,.

Questa immagine del contenitore include i tag seguenti:

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008500001-amd64-preview` |       |

## <a name="computer-vision"></a>Visione artificiale

È possibile trovare l'immagine del contenitore [visione artificiale][cv-containers] nel `containerpreview.azurecr.io` registro contenitori. Si trova all'interno del `microsoft` repository ed è denominato. `cognitive-services-read` Il nome completo dell'immagine del contenitore è `containerpreview.azurecr.io/microsoft/cognitive-services-read`,.

Questa immagine del contenitore include i tag seguenti:

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Viso

L'immagine del contenitore [viso][fa-containers] è reperibile nel `containerpreview.azurecr.io` registro contenitori. Si trova all'interno del `microsoft` repository ed è denominato. `cognitive-services-face` Il nome completo dell'immagine del contenitore è `containerpreview.azurecr.io/microsoft/cognitive-services-face`,.

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

L'immagine del contenitore del [riconoscitore del modulo][fr-containers] è `containerpreview.azurecr.io` disponibile nel registro contenitori. Si trova all'interno del `microsoft` repository ed è denominato. `cognitive-services-form-recognizer` Il nome completo dell'immagine del contenitore è `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`,.

Questa immagine del contenitore include i tag seguenti:

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

L'immagine del contenitore [Luis][lu-containers] si trova nel Syndicate `mcr.microsoft.com` del registro contenitori. Si trova all'interno del `azure-cognitive-services` repository ed è denominato. `luis` Il nome completo dell'immagine del contenitore è `mcr.microsoft.com/azure-cognitive-services/luis`,.

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

È possibile trovare l'immagine del contenitore [da riconoscimento vocale personalizzato a testo][sp-cstt] nel registro `containerpreview.azurecr.io` contenitori. Si trova all'interno del `microsoft` repository ed è denominato. `cognitive-services-custom-speech-to-text` Il nome completo dell'immagine del contenitore è `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`,.

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

È possibile trovare l'immagine del contenitore sintesi [vocale personalizzato][sp-ctts] nel registro `containerpreview.azurecr.io` contenitori. Si trova all'interno del `microsoft` repository ed è denominato. `cognitive-services-custom-text-to-speech` Il nome completo dell'immagine del contenitore è `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`,.

Questa immagine del contenitore include i tag seguenti:

| Tag immagine            | Note |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>Riconoscimento vocale

L'immagine del contenitore [di riconoscimento vocale][sp-stt] è reperibile nel registro `containerpreview.azurecr.io` contenitori. Si trova all'interno del `microsoft` repository ed è denominato. `cognitive-services-speech-to-text` Il nome completo dell'immagine del contenitore è `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`,.

Questa immagine del contenitore include i tag seguenti:

| Tag immagine                  | Note                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | Immagine del contenitore con `en-US` le impostazioni locali. |
| `2.2.0-amd64-ar-ae-preview` | Immagine del contenitore con `ar-AE` le impostazioni locali. |
| `2.2.0-amd64-ar-eg-preview` | Immagine del contenitore con `ar-EG` le impostazioni locali. |
| `2.2.0-amd64-ar-kw-preview` | Immagine del contenitore con `ar-KW` le impostazioni locali. |
| `2.2.0-amd64-ar-qa-preview` | Immagine del contenitore con `ar-QA` le impostazioni locali. |
| `2.2.0-amd64-ar-sa-preview` | Immagine del contenitore con `ar-SA` le impostazioni locali. |
| `2.2.0-amd64-ca-es-preview` | Immagine del contenitore con `ca-ES` le impostazioni locali. |
| `2.2.0-amd64-da-dk-preview` | Immagine del contenitore con `da-DK` le impostazioni locali. |
| `2.2.0-amd64-de-de-preview` | Immagine del contenitore con `de-DE` le impostazioni locali. |
| `2.2.0-amd64-en-au-preview` | Immagine del contenitore con `en-AU` le impostazioni locali. |
| `2.2.0-amd64-en-ca-preview` | Immagine del contenitore con `en-CA` le impostazioni locali. |
| `2.2.0-amd64-en-gb-preview` | Immagine del contenitore con `en-GB` le impostazioni locali. |
| `2.2.0-amd64-en-in-preview` | Immagine del contenitore con `en-IN` le impostazioni locali. |
| `2.2.0-amd64-en-nz-preview` | Immagine del contenitore con `en-NZ` le impostazioni locali. |
| `2.2.0-amd64-en-us-preview` | Immagine del contenitore con `en-US` le impostazioni locali. |
| `2.2.0-amd64-es-es-preview` | Immagine del contenitore con `es-ES` le impostazioni locali. |
| `2.2.0-amd64-es-mx-preview` | Immagine del contenitore con `es-MX` le impostazioni locali. |
| `2.2.0-amd64-fi-fi-preview` | Immagine del contenitore con `fi-FI` le impostazioni locali. |
| `2.2.0-amd64-fr-ca-preview` | Immagine del contenitore con `fr-CA` le impostazioni locali. |
| `2.2.0-amd64-fr-fr-preview` | Immagine del contenitore con `fr-FR` le impostazioni locali. |
| `2.2.0-amd64-gu-in-preview` | Immagine del contenitore con `gu-IN` le impostazioni locali. |
| `2.2.0-amd64-hi-in-preview` | Immagine del contenitore con `hi-IN` le impostazioni locali. |
| `2.2.0-amd64-it-it-preview` | Immagine del contenitore con `it-IT` le impostazioni locali. |
| `2.2.0-amd64-ja-jp-preview` | Immagine del contenitore con `ja-JP` le impostazioni locali. |
| `2.2.0-amd64-ko-kr-preview` | Immagine del contenitore con `ko-KR` le impostazioni locali. |
| `2.2.0-amd64-mr-in-preview` | Immagine del contenitore con `mr-IN` le impostazioni locali. |
| `2.2.0-amd64-nb-no-preview` | Immagine del contenitore con `nb-NO` le impostazioni locali. |
| `2.2.0-amd64-nl-nl-preview` | Immagine del contenitore con `nl-NL` le impostazioni locali. |
| `2.2.0-amd64-pl-pl-preview` | Immagine del contenitore con `pl-PL` le impostazioni locali. |
| `2.2.0-amd64-pt-br-preview` | Immagine del contenitore con `pt-BR` le impostazioni locali. |
| `2.2.0-amd64-pt-pt-preview` | Immagine del contenitore con `pt-PT` le impostazioni locali. |
| `2.2.0-amd64-ru-ru-preview` | Immagine del contenitore con `ru-RU` le impostazioni locali. |
| `2.2.0-amd64-sv-se-preview` | Immagine del contenitore con `sv-SE` le impostazioni locali. |
| `2.2.0-amd64-ta-in-preview` | Immagine del contenitore con `ta-IN` le impostazioni locali. |
| `2.2.0-amd64-te-in-preview` | Immagine del contenitore con `te-IN` le impostazioni locali. |
| `2.2.0-amd64-th-th-preview` | Immagine del contenitore con `th-TH` le impostazioni locali. |
| `2.2.0-amd64-tr-tr-preview` | Immagine del contenitore con `tr-TR` le impostazioni locali. |
| `2.2.0-amd64-zh-cn-preview` | Immagine del contenitore con `zh-CN` le impostazioni locali. |
| `2.2.0-amd64-zh-hk-preview` | Immagine del contenitore con `zh-HK` le impostazioni locali. |
| `2.2.0-amd64-zh-tw-preview` | Immagine del contenitore con `zh-TW` le impostazioni locali. |
| `2.1.1-amd64-en-us-preview` | Immagine del contenitore con `en-US` le impostazioni locali. |
| `2.1.1-amd64-ar-ae-preview` | Immagine del contenitore con `ar-AE` le impostazioni locali. |
| `2.1.1-amd64-ar-eg-preview` | Immagine del contenitore con `ar-EG` le impostazioni locali. |
| `2.1.1-amd64-ar-kw-preview` | Immagine del contenitore con `ar-KW` le impostazioni locali. |
| `2.1.1-amd64-ar-qa-preview` | Immagine del contenitore con `ar-QA` le impostazioni locali. |
| `2.1.1-amd64-ar-sa-preview` | Immagine del contenitore con `ar-SA` le impostazioni locali. |
| `2.1.1-amd64-ca-es-preview` | Immagine del contenitore con `ca-ES` le impostazioni locali. |
| `2.1.1-amd64-da-dk-preview` | Immagine del contenitore con `da-DK` le impostazioni locali. |
| `2.1.1-amd64-de-de-preview` | Immagine del contenitore con `de-DE` le impostazioni locali. |
| `2.1.1-amd64-en-au-preview` | Immagine del contenitore con `en-AU` le impostazioni locali. |
| `2.1.1-amd64-en-ca-preview` | Immagine del contenitore con `en-CA` le impostazioni locali. |
| `2.1.1-amd64-en-gb-preview` | Immagine del contenitore con `en-GB` le impostazioni locali. |
| `2.1.1-amd64-en-in-preview` | Immagine del contenitore con `en-IN` le impostazioni locali. |
| `2.1.1-amd64-en-nz-preview` | Immagine del contenitore con `en-NZ` le impostazioni locali. |
| `2.1.1-amd64-en-us-preview` | Immagine del contenitore con `en-US` le impostazioni locali. |
| `2.1.1-amd64-es-es-preview` | Immagine del contenitore con `es-ES` le impostazioni locali. |
| `2.1.1-amd64-es-mx-preview` | Immagine del contenitore con `es-MX` le impostazioni locali. |
| `2.1.1-amd64-fi-fi-preview` | Immagine del contenitore con `fi-FI` le impostazioni locali. |
| `2.1.1-amd64-fr-ca-preview` | Immagine del contenitore con `fr-CA` le impostazioni locali. |
| `2.1.1-amd64-fr-fr-preview` | Immagine del contenitore con `fr-FR` le impostazioni locali. |
| `2.1.1-amd64-gu-in-preview` | Immagine del contenitore con `gu-IN` le impostazioni locali. |
| `2.1.1-amd64-hi-in-preview` | Immagine del contenitore con `hi-IN` le impostazioni locali. |
| `2.1.1-amd64-it-it-preview` | Immagine del contenitore con `it-IT` le impostazioni locali. |
| `2.1.1-amd64-ja-jp-preview` | Immagine del contenitore con `ja-JP` le impostazioni locali. |
| `2.1.1-amd64-ko-kr-preview` | Immagine del contenitore con `ko-KR` le impostazioni locali. |
| `2.1.1-amd64-mr-in-preview` | Immagine del contenitore con `mr-IN` le impostazioni locali. |
| `2.1.1-amd64-nb-no-preview` | Immagine del contenitore con `nb-NO` le impostazioni locali. |
| `2.1.1-amd64-nl-nl-preview` | Immagine del contenitore con `nl-NL` le impostazioni locali. |
| `2.1.1-amd64-pl-pl-preview` | Immagine del contenitore con `pl-PL` le impostazioni locali. |
| `2.1.1-amd64-pt-br-preview` | Immagine del contenitore con `pt-BR` le impostazioni locali. |
| `2.1.1-amd64-pt-pt-preview` | Immagine del contenitore con `pt-PT` le impostazioni locali. |
| `2.1.1-amd64-ru-ru-preview` | Immagine del contenitore con `ru-RU` le impostazioni locali. |
| `2.1.1-amd64-sv-se-preview` | Immagine del contenitore con `sv-SE` le impostazioni locali. |
| `2.1.1-amd64-ta-in-preview` | Immagine del contenitore con `ta-IN` le impostazioni locali. |
| `2.1.1-amd64-te-in-preview` | Immagine del contenitore con `te-IN` le impostazioni locali. |
| `2.1.1-amd64-th-th-preview` | Immagine del contenitore con `th-TH` le impostazioni locali. |
| `2.1.1-amd64-tr-tr-preview` | Immagine del contenitore con `tr-TR` le impostazioni locali. |
| `2.1.1-amd64-zh-cn-preview` | Immagine del contenitore con `zh-CN` le impostazioni locali. |
| `2.1.1-amd64-zh-hk-preview` | Immagine del contenitore con `zh-HK` le impostazioni locali. |
| `2.1.1-amd64-zh-tw-preview` | Immagine del contenitore con `zh-TW` le impostazioni locali. |
| `2.1.0-amd64-ar-ae-preview` | Immagine del contenitore con `ar-AE` le impostazioni locali. |
| `2.1.0-amd64-ar-eg-preview` | Immagine del contenitore con `ar-EG` le impostazioni locali. |
| `2.1.0-amd64-ar-kw-preview` | Immagine del contenitore con `ar-KW` le impostazioni locali. |
| `2.1.0-amd64-ar-qa-preview` | Immagine del contenitore con `ar-QA` le impostazioni locali. |
| `2.1.0-amd64-ar-sa-preview` | Immagine del contenitore con `ar-SA` le impostazioni locali. |
| `2.1.0-amd64-ca-es-preview` | Immagine del contenitore con `ca-ES` le impostazioni locali. |
| `2.1.0-amd64-da-dk-preview` | Immagine del contenitore con `da-DK` le impostazioni locali. |
| `2.1.0-amd64-de-de-preview` | Immagine del contenitore con `de-DE` le impostazioni locali. |
| `2.1.0-amd64-en-au-preview` | Immagine del contenitore con `en-AU` le impostazioni locali. |
| `2.1.0-amd64-en-ca-preview` | Immagine del contenitore con `en-CA` le impostazioni locali. |
| `2.1.0-amd64-en-gb-preview` | Immagine del contenitore con `en-GB` le impostazioni locali. |
| `2.1.0-amd64-en-in-preview` | Immagine del contenitore con `en-IN` le impostazioni locali. |
| `2.1.0-amd64-en-nz-preview` | Immagine del contenitore con `en-NZ` le impostazioni locali. |
| `2.1.0-amd64-en-us-preview` | Immagine del contenitore con `en-US` le impostazioni locali. |
| `2.1.0-amd64-es-es-preview` | Immagine del contenitore con `es-ES` le impostazioni locali. |
| `2.1.0-amd64-es-mx-preview` | Immagine del contenitore con `es-MX` le impostazioni locali. |
| `2.1.0-amd64-fi-fi-preview` | Immagine del contenitore con `fi-FI` le impostazioni locali. |
| `2.1.0-amd64-fr-ca-preview` | Immagine del contenitore con `fr-CA` le impostazioni locali. |
| `2.1.0-amd64-fr-fr-preview` | Immagine del contenitore con `fr-FR` le impostazioni locali. |
| `2.1.0-amd64-gu-in-preview` | Immagine del contenitore con `gu-IN` le impostazioni locali. |
| `2.1.0-amd64-hi-in-preview` | Immagine del contenitore con `hi-IN` le impostazioni locali. |
| `2.1.0-amd64-it-it-preview` | Immagine del contenitore con `it-IT` le impostazioni locali. |
| `2.1.0-amd64-ja-jp-preview` | Immagine del contenitore con `ja-JP` le impostazioni locali. |
| `2.1.0-amd64-ko-kr-preview` | Immagine del contenitore con `ko-KR` le impostazioni locali. |
| `2.1.0-amd64-mr-in-preview` | Immagine del contenitore con `mr-IN` le impostazioni locali. |
| `2.1.0-amd64-nb-no-preview` | Immagine del contenitore con `nb-NO` le impostazioni locali. |
| `2.1.0-amd64-nl-nl-preview` | Immagine del contenitore con `nl-NL` le impostazioni locali. |
| `2.1.0-amd64-pl-pl-preview` | Immagine del contenitore con `pl-PL` le impostazioni locali. |
| `2.1.0-amd64-pt-br-preview` | Immagine del contenitore con `pt-BR` le impostazioni locali. |
| `2.1.0-amd64-pt-pt-preview` | Immagine del contenitore con `pt-PT` le impostazioni locali. |
| `2.1.0-amd64-ru-ru-preview` | Immagine del contenitore con `ru-RU` le impostazioni locali. |
| `2.1.0-amd64-sv-se-preview` | Immagine del contenitore con `sv-SE` le impostazioni locali. |
| `2.1.0-amd64-ta-in-preview` | Immagine del contenitore con `ta-IN` le impostazioni locali. |
| `2.1.0-amd64-te-in-preview` | Immagine del contenitore con `te-IN` le impostazioni locali. |
| `2.1.0-amd64-th-th-preview` | Immagine del contenitore con `th-TH` le impostazioni locali. |
| `2.1.0-amd64-tr-tr-preview` | Immagine del contenitore con `tr-TR` le impostazioni locali. |
| `2.1.0-amd64-zh-cn-preview` | Immagine del contenitore con `zh-CN` le impostazioni locali. |
| `2.1.0-amd64-zh-hk-preview` | Immagine del contenitore con `zh-HK` le impostazioni locali. |
| `2.1.0-amd64-zh-tw-preview` | Immagine del contenitore con `zh-TW` le impostazioni locali. |
| `2.0.3-amd64-ja-jp-preview` | Immagine del contenitore con `ja-JP` le impostazioni locali. |
| `2.0.2-amd64-ar-ae-preview` | Immagine del contenitore con `ar-AE` le impostazioni locali. |
| `2.0.2-amd64-ar-eg-preview` | Immagine del contenitore con `ar-EG` le impostazioni locali. |
| `2.0.2-amd64-ar-kw-preview` | Immagine del contenitore con `ar-KW` le impostazioni locali. |
| `2.0.2-amd64-ar-qa-preview` | Immagine del contenitore con `ar-QA` le impostazioni locali. |
| `2.0.2-amd64-ar-sa-preview` | Immagine del contenitore con `ar-SA` le impostazioni locali. |
| `2.0.2-amd64-ca-es-preview` | Immagine del contenitore con `ca-ES` le impostazioni locali. |
| `2.0.2-amd64-da-dk-preview` | Immagine del contenitore con `da-DK` le impostazioni locali. |
| `2.0.2-amd64-de-de-preview` | Immagine del contenitore con `de-DE` le impostazioni locali. |
| `2.0.2-amd64-en-au-preview` | Immagine del contenitore con `en-AU` le impostazioni locali. |
| `2.0.2-amd64-en-ca-preview` | Immagine del contenitore con `en-CA` le impostazioni locali. |
| `2.0.2-amd64-en-gb-preview` | Immagine del contenitore con `en-GB` le impostazioni locali. |
| `2.0.2-amd64-en-in-preview` | Immagine del contenitore con `en-IN` le impostazioni locali. |
| `2.0.2-amd64-en-nz-preview` | Immagine del contenitore con `en-NZ` le impostazioni locali. |
| `2.0.2-amd64-en-us-preview` | Immagine del contenitore con `en-US` le impostazioni locali. |
| `2.0.2-amd64-es-es-preview` | Immagine del contenitore con `es-ES` le impostazioni locali. |
| `2.0.2-amd64-es-mx-preview` | Immagine del contenitore con `es-MX` le impostazioni locali. |
| `2.0.2-amd64-fi-fi-preview` | Immagine del contenitore con `fi-FI` le impostazioni locali. |
| `2.0.2-amd64-fr-ca-preview` | Immagine del contenitore con `fr-CA` le impostazioni locali. |
| `2.0.2-amd64-fr-fr-preview` | Immagine del contenitore con `fr-FR` le impostazioni locali. |
| `2.0.2-amd64-gu-in-preview` | Immagine del contenitore con `gu-IN` le impostazioni locali. |
| `2.0.2-amd64-hi-in-preview` | Immagine del contenitore con `hi-IN` le impostazioni locali. |
| `2.0.2-amd64-it-it-preview` | Immagine del contenitore con `it-IT` le impostazioni locali. |
| `2.0.2-amd64-ja-jp-preview` | Immagine del contenitore con `ja-JP` le impostazioni locali. |
| `2.0.2-amd64-ko-kr-preview` | Immagine del contenitore con `ko-KR` le impostazioni locali. |
| `2.0.2-amd64-mr-in-preview` | Immagine del contenitore con `mr-IN` le impostazioni locali. |
| `2.0.2-amd64-nb-no-preview` | Immagine del contenitore con `nb-NO` le impostazioni locali. |
| `2.0.2-amd64-nl-nl-preview` | Immagine del contenitore con `nl-NL` le impostazioni locali. |
| `2.0.2-amd64-pl-pl-preview` | Immagine del contenitore con `pl-PL` le impostazioni locali. |
| `2.0.2-amd64-pt-br-preview` | Immagine del contenitore con `pt-BR` le impostazioni locali. |
| `2.0.2-amd64-pt-pt-preview` | Immagine del contenitore con `pt-PT` le impostazioni locali. |
| `2.0.2-amd64-ru-ru-preview` | Immagine del contenitore con `ru-RU` le impostazioni locali. |
| `2.0.2-amd64-sv-se-preview` | Immagine del contenitore con `sv-SE` le impostazioni locali. |
| `2.0.2-amd64-ta-in-preview` | Immagine del contenitore con `ta-IN` le impostazioni locali. |
| `2.0.2-amd64-te-in-preview` | Immagine del contenitore con `te-IN` le impostazioni locali. |
| `2.0.2-amd64-th-th-preview` | Immagine del contenitore con `th-TH` le impostazioni locali. |
| `2.0.2-amd64-tr-tr-preview` | Immagine del contenitore con `tr-TR` le impostazioni locali. |
| `2.0.2-amd64-zh-cn-preview` | Immagine del contenitore con `zh-CN` le impostazioni locali. |
| `2.0.2-amd64-zh-hk-preview` | Immagine del contenitore con `zh-HK` le impostazioni locali. |
| `2.0.2-amd64-zh-tw-preview` | Immagine del contenitore con `zh-TW` le impostazioni locali. |
| `2.0.1-amd64-ar-ae-preview` | Immagine del contenitore con `ar-AE` le impostazioni locali. |
| `2.0.1-amd64-ar-eg-preview` | Immagine del contenitore con `ar-EG` le impostazioni locali. |
| `2.0.1-amd64-ar-kw-preview` | Immagine del contenitore con `ar-KW` le impostazioni locali. |
| `2.0.1-amd64-ar-qa-preview` | Immagine del contenitore con `ar-QA` le impostazioni locali. |
| `2.0.1-amd64-ar-sa-preview` | Immagine del contenitore con `ar-SA` le impostazioni locali. |
| `2.0.1-amd64-ca-es-preview` | Immagine del contenitore con `ca-ES` le impostazioni locali. |
| `2.0.1-amd64-da-dk-preview` | Immagine del contenitore con `da-DK` le impostazioni locali. |
| `2.0.1-amd64-de-de-preview` | Immagine del contenitore con `de-DE` le impostazioni locali. |
| `2.0.1-amd64-en-au-preview` | Immagine del contenitore con `en-AU` le impostazioni locali. |
| `2.0.1-amd64-en-ca-preview` | Immagine del contenitore con `en-CA` le impostazioni locali. |
| `2.0.1-amd64-en-gb-preview` | Immagine del contenitore con `en-GB` le impostazioni locali. |
| `2.0.1-amd64-en-in-preview` | Immagine del contenitore con `en-IN` le impostazioni locali. |
| `2.0.1-amd64-en-nz-preview` | Immagine del contenitore con `en-NZ` le impostazioni locali. |
| `2.0.1-amd64-en-us-preview` | Immagine del contenitore con `en-US` le impostazioni locali. |
| `2.0.1-amd64-es-es-preview` | Immagine del contenitore con `es-ES` le impostazioni locali. |
| `2.0.1-amd64-es-mx-preview` | Immagine del contenitore con `es-MX` le impostazioni locali. |
| `2.0.1-amd64-fi-fi-preview` | Immagine del contenitore con `fi-FI` le impostazioni locali. |
| `2.0.1-amd64-fr-ca-preview` | Immagine del contenitore con `fr-CA` le impostazioni locali. |
| `2.0.1-amd64-fr-fr-preview` | Immagine del contenitore con `fr-FR` le impostazioni locali. |
| `2.0.1-amd64-gu-in-preview` | Immagine del contenitore con `gu-IN` le impostazioni locali. |
| `2.0.1-amd64-hi-in-preview` | Immagine del contenitore con `hi-IN` le impostazioni locali. |
| `2.0.1-amd64-it-it-preview` | Immagine del contenitore con `it-IT` le impostazioni locali. |
| `2.0.1-amd64-ja-jp-preview` | Immagine del contenitore con `ja-JP` le impostazioni locali. |
| `2.0.1-amd64-ko-kr-preview` | Immagine del contenitore con `ko-KR` le impostazioni locali. |
| `2.0.1-amd64-mr-in-preview` | Immagine del contenitore con `mr-IN` le impostazioni locali. |
| `2.0.1-amd64-nb-no-preview` | Immagine del contenitore con `nb-NO` le impostazioni locali. |
| `2.0.1-amd64-nl-nl-preview` | Immagine del contenitore con `nl-NL` le impostazioni locali. |
| `2.0.1-amd64-pl-pl-preview` | Immagine del contenitore con `pl-PL` le impostazioni locali. |
| `2.0.1-amd64-pt-br-preview` | Immagine del contenitore con `pt-BR` le impostazioni locali. |
| `2.0.1-amd64-pt-pt-preview` | Immagine del contenitore con `pt-PT` le impostazioni locali. |
| `2.0.1-amd64-ru-ru-preview` | Immagine del contenitore con `ru-RU` le impostazioni locali. |
| `2.0.1-amd64-sv-se-preview` | Immagine del contenitore con `sv-SE` le impostazioni locali. |
| `2.0.1-amd64-ta-in-preview` | Immagine del contenitore con `ta-IN` le impostazioni locali. |
| `2.0.1-amd64-te-in-preview` | Immagine del contenitore con `te-IN` le impostazioni locali. |
| `2.0.1-amd64-th-th-preview` | Immagine del contenitore con `th-TH` le impostazioni locali. |
| `2.0.1-amd64-tr-tr-preview` | Immagine del contenitore con `tr-TR` le impostazioni locali. |
| `2.0.1-amd64-zh-cn-preview` | Immagine del contenitore con `zh-CN` le impostazioni locali. |
| `2.0.1-amd64-zh-hk-preview` | Immagine del contenitore con `zh-HK` le impostazioni locali. |
| `2.0.1-amd64-zh-tw-preview` | Immagine del contenitore con `zh-TW` le impostazioni locali. |
| `2.0.0-amd64-ar-eg-preview` | Immagine del contenitore con `ar-EG` le impostazioni locali. |
| `2.0.0-amd64-ca-es-preview` | Immagine del contenitore con `ca-ES` le impostazioni locali. |
| `2.0.0-amd64-da-dk-preview` | Immagine del contenitore con `da-DK` le impostazioni locali. |
| `2.0.0-amd64-de-de-preview` | Immagine del contenitore con `de-DE` le impostazioni locali. |
| `2.0.0-amd64-en-au-preview` | Immagine del contenitore con `en-AU` le impostazioni locali. |
| `2.0.0-amd64-en-ca-preview` | Immagine del contenitore con `en-CA` le impostazioni locali. |
| `2.0.0-amd64-en-gb-preview` | Immagine del contenitore con `en-GB` le impostazioni locali. |
| `2.0.0-amd64-en-in-preview` | Immagine del contenitore con `en-IN` le impostazioni locali. |
| `2.0.0-amd64-en-nz-preview` | Immagine del contenitore con `en-NZ` le impostazioni locali. |
| `2.0.0-amd64-en-us-preview` | Immagine del contenitore con `en-US` le impostazioni locali. |
| `2.0.0-amd64-es-es-preview` | Immagine del contenitore con `es-ES` le impostazioni locali. |
| `2.0.0-amd64-es-mx-preview` | Immagine del contenitore con `es-MX` le impostazioni locali. |
| `2.0.0-amd64-fi-fi-preview` | Immagine del contenitore con `fi-FI` le impostazioni locali. |
| `2.0.0-amd64-fr-ca-preview` | Immagine del contenitore con `fr-CA` le impostazioni locali. |
| `2.0.0-amd64-fr-fr-preview` | Immagine del contenitore con `fr-FR` le impostazioni locali. |
| `2.0.0-amd64-hi-in-preview` | Immagine del contenitore con `hi-IN` le impostazioni locali. |
| `2.0.0-amd64-it-it-preview` | Immagine del contenitore con `it-IT` le impostazioni locali. |
| `2.0.0-amd64-ja-jp-preview` | Immagine del contenitore con `ja-JP` le impostazioni locali. |
| `2.0.0-amd64-ko-kr-preview` | Immagine del contenitore con `ko-KR` le impostazioni locali. |
| `2.0.0-amd64-nb-no-preview` | Immagine del contenitore con `nb-NO` le impostazioni locali. |
| `2.0.0-amd64-nl-nl-preview` | Immagine del contenitore con `nl-NL` le impostazioni locali. |
| `2.0.0-amd64-pl-pl-preview` | Immagine del contenitore con `pl-PL` le impostazioni locali. |
| `2.0.0-amd64-pt-br-preview` | Immagine del contenitore con `pt-BR` le impostazioni locali. |
| `2.0.0-amd64-pt-pt-preview` | Immagine del contenitore con `pt-PT` le impostazioni locali. |
| `2.0.0-amd64-ru-ru-preview` | Immagine del contenitore con `ru-RU` le impostazioni locali. |
| `2.0.0-amd64-sv-se-preview` | Immagine del contenitore con `sv-SE` le impostazioni locali. |
| `2.0.0-amd64-th-th-preview` | Immagine del contenitore con `th-TH` le impostazioni locali. |
| `2.0.0-amd64-tr-tr-preview` | Immagine del contenitore con `tr-TR` le impostazioni locali. |
| `2.0.0-amd64-zh-cn-preview` | Immagine del contenitore con `zh-CN` le impostazioni locali. |
| `2.0.0-amd64-zh-hk-preview` | Immagine del contenitore con `zh-HK` le impostazioni locali. |
| `2.0.0-amd64-zh-tw-preview` | Immagine del contenitore con `zh-TW` le impostazioni locali. |
| `1.2.0-amd64-de-de-preview` | Immagine del contenitore con `de-DE` le impostazioni locali. |
| `1.2.0-amd64-en-au-preview` | Immagine del contenitore con `en-AU` le impostazioni locali. |
| `1.2.0-amd64-en-ca-preview` | Immagine del contenitore con `en-CA` le impostazioni locali. |
| `1.2.0-amd64-en-gb-preview` | Immagine del contenitore con `en-GB` le impostazioni locali. |
| `1.2.0-amd64-en-in-preview` | Immagine del contenitore con `en-IN` le impostazioni locali. |
| `1.2.0-amd64-en-us-preview` | Immagine del contenitore con `en-US` le impostazioni locali. |
| `1.2.0-amd64-es-es-preview` | Immagine del contenitore con `es-ES` le impostazioni locali. |
| `1.2.0-amd64-es-mx-preview` | Immagine del contenitore con `es-MX` le impostazioni locali. |
| `1.2.0-amd64-fr-ca-preview` | Immagine del contenitore con `fr-CA` le impostazioni locali. |
| `1.2.0-amd64-fr-fr-preview` | Immagine del contenitore con `fr-FR` le impostazioni locali. |
| `1.2.0-amd64-it-it-preview` | Immagine del contenitore con `it-IT` le impostazioni locali. |
| `1.2.0-amd64-ja-jp-preview` | Immagine del contenitore con `ja-JP` le impostazioni locali. |
| `1.2.0-amd64-pt-br-preview` | Immagine del contenitore con `pt-BR` le impostazioni locali. |
| `1.2.0-amd64-zh-cn-preview` | Immagine del contenitore con `zh-CN` le impostazioni locali. |
| `1.1.3-amd64-de-de-preview` | Immagine del contenitore con `de-DE` le impostazioni locali. |
| `1.1.3-amd64-en-au-preview` | Immagine del contenitore con `en-AU` le impostazioni locali. |
| `1.1.3-amd64-en-ca-preview` | Immagine del contenitore con `en-CA` le impostazioni locali. |
| `1.1.3-amd64-en-gb-preview` | Immagine del contenitore con `en-GB` le impostazioni locali. |
| `1.1.3-amd64-en-in-preview` | Immagine del contenitore con `en-IN` le impostazioni locali. |
| `1.1.3-amd64-en-us-preview` | Immagine del contenitore con `en-US` le impostazioni locali. |
| `1.1.3-amd64-es-es-preview` | Immagine del contenitore con `es-ES` le impostazioni locali. |
| `1.1.3-amd64-es-mx-preview` | Immagine del contenitore con `es-MX` le impostazioni locali. |
| `1.1.3-amd64-fr-ca-preview` | Immagine del contenitore con `fr-CA` le impostazioni locali. |
| `1.1.3-amd64-fr-fr-preview` | Immagine del contenitore con `fr-FR` le impostazioni locali. |
| `1.1.3-amd64-it-it-preview` | Immagine del contenitore con `it-IT` le impostazioni locali. |
| `1.1.3-amd64-ja-jp-preview` | Immagine del contenitore con `ja-JP` le impostazioni locali. |
| `1.1.3-amd64-pt-br-preview` | Immagine del contenitore con `pt-BR` le impostazioni locali. |
| `1.1.3-amd64-zh-cn-preview` | Immagine del contenitore con `zh-CN` le impostazioni locali. |
| `1.1.2-amd64-de-de-preview` | Immagine del contenitore con `de-DE` le impostazioni locali. |
| `1.1.2-amd64-en-au-preview` | Immagine del contenitore con `en-AU` le impostazioni locali. |
| `1.1.2-amd64-en-ca-preview` | Immagine del contenitore con `en-CA` le impostazioni locali. |
| `1.1.2-amd64-en-gb-preview` | Immagine del contenitore con `en-GB` le impostazioni locali. |
| `1.1.2-amd64-en-in-preview` | Immagine del contenitore con `en-IN` le impostazioni locali. |
| `1.1.2-amd64-en-us-preview` | Immagine del contenitore con `en-US` le impostazioni locali. |
| `1.1.2-amd64-es-es-preview` | Immagine del contenitore con `es-ES` le impostazioni locali. |
| `1.1.2-amd64-es-mx-preview` | Immagine del contenitore con `es-MX` le impostazioni locali. |
| `1.1.2-amd64-fr-ca-preview` | Immagine del contenitore con `fr-CA` le impostazioni locali. |
| `1.1.2-amd64-fr-fr-preview` | Immagine del contenitore con `fr-FR` le impostazioni locali. |
| `1.1.2-amd64-it-it-preview` | Immagine del contenitore con `it-IT` le impostazioni locali. |
| `1.1.2-amd64-ja-jp-preview` | Immagine del contenitore con `ja-JP` le impostazioni locali. |
| `1.1.2-amd64-pt-br-preview` | Immagine del contenitore con `pt-BR` le impostazioni locali. |
| `1.1.2-amd64-zh-cn-preview` | Immagine del contenitore con `zh-CN` le impostazioni locali. |
| `1.1.1-amd64-de-de-preview` | Immagine del contenitore con `de-DE` le impostazioni locali. |
| `1.1.1-amd64-en-au-preview` | Immagine del contenitore con `en-AU` le impostazioni locali. |
| `1.1.1-amd64-en-ca-preview` | Immagine del contenitore con `en-CA` le impostazioni locali. |
| `1.1.1-amd64-en-gb-preview` | Immagine del contenitore con `en-GB` le impostazioni locali. |
| `1.1.1-amd64-en-in-preview` | Immagine del contenitore con `en-IN` le impostazioni locali. |
| `1.1.1-amd64-en-us-preview` | Immagine del contenitore con `en-US` le impostazioni locali. |
| `1.1.1-amd64-es-es-preview` | Immagine del contenitore con `es-ES` le impostazioni locali. |
| `1.1.1-amd64-es-mx-preview` | Immagine del contenitore con `es-MX` le impostazioni locali. |
| `1.1.1-amd64-fr-ca-preview` | Immagine del contenitore con `fr-CA` le impostazioni locali. |
| `1.1.1-amd64-fr-fr-preview` | Immagine del contenitore con `fr-FR` le impostazioni locali. |
| `1.1.1-amd64-it-it-preview` | Immagine del contenitore con `it-IT` le impostazioni locali. |
| `1.1.1-amd64-ja-jp-preview` | Immagine del contenitore con `ja-JP` le impostazioni locali. |
| `1.1.1-amd64-pt-br-preview` | Immagine del contenitore con `pt-BR` le impostazioni locali. |
| `1.1.1-amd64-zh-cn-preview` | Immagine del contenitore con `zh-CN` le impostazioni locali. |
| `1.1.0-amd64-de-de-preview` | Immagine del contenitore con `de-DE` le impostazioni locali. |
| `1.1.0-amd64-en-au-preview` | Immagine del contenitore con `en-AU` le impostazioni locali. |
| `1.1.0-amd64-en-ca-preview` | Immagine del contenitore con `en-CA` le impostazioni locali. |
| `1.1.0-amd64-en-gb-preview` | Immagine del contenitore con `en-GB` le impostazioni locali. |
| `1.1.0-amd64-en-in-preview` | Immagine del contenitore con `en-IN` le impostazioni locali. |
| `1.1.0-amd64-en-us-preview` | Immagine del contenitore con `en-US` le impostazioni locali. |
| `1.1.0-amd64-es-es-preview` | Immagine del contenitore con `es-ES` le impostazioni locali. |
| `1.1.0-amd64-es-mx-preview` | Immagine del contenitore con `es-MX` le impostazioni locali. |
| `1.1.0-amd64-fr-ca-preview` | Immagine del contenitore con `fr-CA` le impostazioni locali. |
| `1.1.0-amd64-fr-fr-preview` | Immagine del contenitore con `fr-FR` le impostazioni locali. |
| `1.1.0-amd64-it-it-preview` | Immagine del contenitore con `it-IT` le impostazioni locali. |
| `1.1.0-amd64-ja-jp-preview` | Immagine del contenitore con `ja-JP` le impostazioni locali. |
| `1.1.0-amd64-pt-br-preview` | Immagine del contenitore con `pt-BR` le impostazioni locali. |
| `1.1.0-amd64-zh-cn-preview` | Immagine del contenitore con `zh-CN` le impostazioni locali. |
| `1.0.0-amd64-de-de-preview` | Immagine del contenitore con `de-DE` le impostazioni locali. |
| `1.0.0-amd64-en-au-preview` | Immagine del contenitore con `en-AU` le impostazioni locali. |
| `1.0.0-amd64-en-ca-preview` | Immagine del contenitore con `en-CA` le impostazioni locali. |
| `1.0.0-amd64-en-gb-preview` | Immagine del contenitore con `en-GB` le impostazioni locali. |
| `1.0.0-amd64-en-in-preview` | Immagine del contenitore con `en-IN` le impostazioni locali. |
| `1.0.0-amd64-en-us-preview` | Immagine del contenitore con `en-US` le impostazioni locali. |
| `1.0.0-amd64-es-es-preview` | Immagine del contenitore con `es-ES` le impostazioni locali. |
| `1.0.0-amd64-es-mx-preview` | Immagine del contenitore con `es-MX` le impostazioni locali. |
| `1.0.0-amd64-fr-ca-preview` | Immagine del contenitore con `fr-CA` le impostazioni locali. |
| `1.0.0-amd64-fr-fr-preview` | Immagine del contenitore con `fr-FR` le impostazioni locali. |
| `1.0.0-amd64-it-it-preview` | Immagine del contenitore con `it-IT` le impostazioni locali. |
| `1.0.0-amd64-ja-jp-preview` | Immagine del contenitore con `ja-JP` le impostazioni locali. |
| `1.0.0-amd64-pt-br-preview` | Immagine del contenitore con `pt-BR` le impostazioni locali. |
| `1.0.0-amd64-zh-cn-preview` | Immagine del contenitore con `zh-CN` le impostazioni locali. |

## <a name="text-to-speech"></a>Sintesi vocale

L'immagine del contenitore [da sintesi vocale][sp-tts] è reperibile nel registro `containerpreview.azurecr.io` contenitori. Si trova all'interno del `microsoft` repository ed è denominato. `cognitive-services-text-to-speech` Il nome completo dell'immagine del contenitore è `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`,.

Questa immagine del contenitore include i tag seguenti:

| Tag immagine                                  | Note                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Immagine del contenitore con `en-US` le impostazioni locali `en-US-JessaRUS` e la voce.        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | Immagine del contenitore con `ar-EG` le impostazioni locali `ar-EG-Hoda` e la voce.            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | Immagine del contenitore con `ar-SA` le impostazioni locali `ar-SA-Naayf` e la voce.           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | Immagine del contenitore con `bg-BG` le impostazioni locali `bg-BG-Ivan` e la voce.            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | Immagine del contenitore con `ca-ES` le impostazioni locali `ca-ES-HerenaRUS` e la voce.       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | Immagine del contenitore con `cs-CZ` le impostazioni locali `cs-CZ-Jakub` e la voce.           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | Immagine del contenitore con `da-DK` le impostazioni locali `da-DK-HelleRUS` e la voce.        |
| `1.3.0-amd64-de-at-michael-preview`         | Immagine del contenitore con `de-AT` le impostazioni locali `de-AT-Michael` e la voce.         |
| `1.3.0-amd64-de-ch-karsten-preview`         | Immagine del contenitore con `de-CH` le impostazioni locali `de-CH-Karsten` e la voce.         |
| `1.3.0-amd64-de-de-hedda-preview`           | Immagine del contenitore con `de-DE` le impostazioni locali `de-DE-Hedda` e la voce.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Immagine del contenitore con `de-DE` le impostazioni locali `de-DE-Hedda` e la voce.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Immagine del contenitore con `de-DE` le impostazioni locali `de-DE-HeddaRUS` e la voce.        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | Immagine del contenitore con `de-DE` le impostazioni locali `de-DE-Stefan-Apollo` e la voce.   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | Immagine del contenitore con `el-GR` le impostazioni locali `el-GR-Stefanos` e la voce.        |
| `1.3.0-amd64-en-au-catherine-preview`       | Immagine del contenitore con `en-AU` le impostazioni locali `en-AU-Catherine` e la voce.       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | Immagine del contenitore con `en-AU` le impostazioni locali `en-AU-HayleyRUS` e la voce.       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | Immagine del contenitore con `en-CA` le impostazioni locali `en-CA-HeatherRUS` e la voce.      |
| `1.3.0-amd64-en-ca-linda-preview`           | Immagine del contenitore con `en-CA` le impostazioni locali `en-CA-Linda` e la voce.           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | Immagine del contenitore con `en-GB` le impostazioni locali `en-GB-George-Apollo` e la voce.   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | Immagine del contenitore con `en-GB` le impostazioni locali `en-GB-HazelRUS` e la voce.        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | Immagine del contenitore con `en-GB` le impostazioni locali `en-GB-Susan-Apollo` e la voce.    |
| `1.3.0-amd64-en-ie-sean-preview`            | Immagine del contenitore con `en-IE` le impostazioni locali `en-IE-Sean` e la voce.            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | Immagine del contenitore con `en-IN` le impostazioni locali `en-IN-Heera-Apollo` e la voce.    |
| `1.3.0-amd64-en-in-priyarus-preview`        | Immagine del contenitore con `en-IN` le impostazioni locali `en-IN-PriyaRUS` e la voce.        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | Immagine del contenitore con `en-IN` le impostazioni locali `en-IN-Ravi-Apollo` e la voce.     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | Immagine del contenitore con `en-US` le impostazioni locali `en-US-BenjaminRUS` e la voce.     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | Immagine del contenitore con `en-US` le impostazioni locali `en-US-Guy24kRUS` e la voce.       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | Immagine del contenitore con `en-US` le impostazioni locali `en-US-Jessa24kRUS` e la voce.     |
| `1.3.0-amd64-en-us-jessarus-preview`        | Immagine del contenitore con `en-US` le impostazioni locali `en-US-JessaRUS` e la voce.        |
| `1.3.0-amd64-en-us-zirarus-preview`         | Immagine del contenitore con `en-US` le impostazioni locali `en-US-ZiraRUS` e la voce.         |
| `1.3.0-amd64-es-es-helenarus-preview`       | Immagine del contenitore con `es-ES` le impostazioni locali `es-ES-HelenaRUS` e la voce.       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | Immagine del contenitore con `es-ES` le impostazioni locali `es-ES-Laura-Apollo` e la voce.    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | Immagine del contenitore con `es-ES` le impostazioni locali `es-ES-Pablo-Apollo` e la voce.    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | Immagine del contenitore con `es-MX` le impostazioni locali `es-MX-HildaRUS` e la voce.        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | Immagine del contenitore con `es-MX` le impostazioni locali `es-MX-Raul-Apollo` e la voce.     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | Immagine del contenitore con `fi-FI` le impostazioni locali `fi-FI-HeidiRUS` e la voce.        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | Immagine del contenitore con `fr-CA` le impostazioni locali `fr-CA-Caroline` e la voce.        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | Immagine del contenitore con `fr-CA` le impostazioni locali `fr-CA-HarmonieRUS` e la voce.     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | Immagine del contenitore con `fr-CH` le impostazioni locali `fr-CH-Guillaume` e la voce.       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | Immagine del contenitore con `fr-FR` le impostazioni locali `fr-FR-HortenseRUS` e la voce.     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | Immagine del contenitore con `fr-FR` le impostazioni locali `fr-FR-Julie-Apollo` e la voce.    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | Immagine del contenitore con `fr-FR` le impostazioni locali `fr-FR-Paul-Apollo` e la voce.     |
| `1.3.0-amd64-he-il-asaf-preview`            | Immagine del contenitore con `he-IL` le impostazioni locali `he-IL-Asaf` e la voce.            |
| `1.3.0-amd64-hi-in-hemant-preview`          | Immagine del contenitore con `hi-IN` le impostazioni locali `hi-IN-Hemant` e la voce.          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Immagine del contenitore con `hi-IN` le impostazioni locali `hi-IN-Kalpana-Apollo` e la voce.  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Immagine del contenitore con `hi-IN` le impostazioni locali `hi-IN-Kalpana` e la voce.         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | Immagine del contenitore con `hi-IN` le impostazioni locali `hi-IN-Kalpana` e la voce.         |
| `1.3.0-amd64-hr-hr-matej-preview`           | Immagine del contenitore con `hr-HR` le impostazioni locali `hr-HR-Matej` e la voce.           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | Immagine del contenitore con `hu-HU` le impostazioni locali `hu-HU-Szabolcs` e la voce.        |
| `1.3.0-amd64-id-id-andika-preview`          | Immagine del contenitore con `id-ID` le impostazioni locali `id-ID-Andika` e la voce.          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | Immagine del contenitore con `it-IT` le impostazioni locali `it-IT-Cosimo-Apollo` e la voce.   |
| `1.3.0-amd64-it-it-luciarus-preview`        | Immagine del contenitore con `it-IT` le impostazioni locali `it-IT-LuciaRUS` e la voce.        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | Immagine del contenitore con `ja-JP` le impostazioni locali `ja-JP-Ayumi-Apollo` e la voce.    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | Immagine del contenitore con `ja-JP` le impostazioni locali `ja-JP-HarukaRUS` e la voce.       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | Immagine del contenitore con `ja-JP` le impostazioni locali `ja-JP-Ichiro-Apollo` e la voce.   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | Immagine del contenitore con `ko-KR` le impostazioni locali `ko-KR-HeamiRUS` e la voce.        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | Immagine del contenitore con `ms-MY` le impostazioni locali `ms-MY-Rizwan` e la voce.          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | Immagine del contenitore con `nb-NO` le impostazioni locali `nb-NO-HuldaRUS` e la voce.        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | Immagine del contenitore con `nl-NL` le impostazioni locali `nl-NL-HannaRUS` e la voce.        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | Immagine del contenitore con `pl-PL` le impostazioni locali `pl-PL-PaulinaRUS` e la voce.      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | Immagine del contenitore con `pt-BR` le impostazioni locali `pt-BR-Daniel-Apollo` e la voce.   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | Immagine del contenitore con `pt-BR` le impostazioni locali `pt-BR-HeloisaRUS` e la voce.      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | Immagine del contenitore con `pt-PT` le impostazioni locali `pt-PT-HeliaRUS` e la voce.        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | Immagine del contenitore con `ro-RO` le impostazioni locali `ro-RO-Andrei` e la voce.          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | Immagine del contenitore con `ru-RU` le impostazioni locali `ru-RU-EkaterinaRUS` e la voce.    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | Immagine del contenitore con `ru-RU` le impostazioni locali `ru-RU-Irina-Apollo` e la voce.    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | Immagine del contenitore con `ru-RU` le impostazioni locali `ru-RU-Pavel-Apollo` e la voce.    |
| `1.3.0-amd64-sk-sk-filip-preview`           | Immagine del contenitore con `sk-SK` le impostazioni locali `sk-SK-Filip` e la voce.           |
| `1.3.0-amd64-sl-si-lado-preview`            | Immagine del contenitore con `sl-SI` le impostazioni locali `sl-SI-Lado` e la voce.            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | Immagine del contenitore con `sv-SE` le impostazioni locali `sv-SE-HedvigRUS` e la voce.       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | Immagine del contenitore con `ta-IN` le impostazioni locali `ta-IN-Valluvar` e la voce.        |
| `1.3.0-amd64-te-in-chitra-preview`          | Immagine del contenitore con `te-IN` le impostazioni locali `te-IN-Chitra` e la voce.          |
| `1.3.0-amd64-th-th-pattara-preview`         | Immagine del contenitore con `th-TH` le impostazioni locali `th-TH-Pattara` e la voce.         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | Immagine del contenitore con `tr-TR` le impostazioni locali `tr-TR-SedaRUS` e la voce.         |
| `1.3.0-amd64-vi-vn-an-preview`              | Immagine del contenitore con `vi-VN` le impostazioni locali `vi-VN-An` e la voce.              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | Immagine del contenitore con `zh-CN` le impostazioni locali `zh-CN-HuihuiRUS` e la voce.       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | Immagine del contenitore con `zh-CN` le impostazioni locali `zh-CN-Kangkang-Apollo` e la voce. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | Immagine del contenitore con `zh-CN` le impostazioni locali `zh-CN-Yaoyao-Apollo` e la voce.   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | Immagine del contenitore con `zh-HK` le impostazioni locali `zh-HK-Danny-Apollo` e la voce.    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | Immagine del contenitore con `zh-HK` le impostazioni locali `zh-HK-Tracy-Apollo` e la voce.    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | Immagine del contenitore con `zh-HK` le impostazioni locali `zh-HK-TracyRUS` e la voce.        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | Immagine del contenitore con `zh-TW` le impostazioni locali `zh-TW-HanHanRUS` e la voce.       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | Immagine del contenitore con `zh-TW` le impostazioni locali `zh-TW-Yating-Apollo` e la voce.   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | Immagine del contenitore con `zh-TW` le impostazioni locali `zh-TW-Zhiwei-Apollo` e la voce.   |
| `1.2.0-amd64-de-de-heddarus-preview`        | Immagine del contenitore con `de-DE` le impostazioni locali `de-DE-Hedda` e la voce.           |
| `1.2.0-amd64-de-de-heddarus-preview`        | Immagine del contenitore con `de-DE` le impostazioni locali `de-DE-HeddaRUS` e la voce.        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | Immagine del contenitore con `de-DE` le impostazioni locali `de-DE-Stefan-Apollo` e la voce.   |
| `1.2.0-amd64-en-au-catherine-preview`       | Immagine del contenitore con `en-AU` le impostazioni locali `en-AU-Catherine` e la voce.       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | Immagine del contenitore con `en-AU` le impostazioni locali `en-AU-HayleyRUS` e la voce.       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | Immagine del contenitore con `en-GB` le impostazioni locali `en-GB-George-Apollo` e la voce.   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | Immagine del contenitore con `en-GB` le impostazioni locali `en-GB-HazelRUS` e la voce.        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | Immagine del contenitore con `en-GB` le impostazioni locali `en-GB-Susan-Apollo` e la voce.    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | Immagine del contenitore con `en-IN` le impostazioni locali `en-IN-Heera-Apollo` e la voce.    |
| `1.2.0-amd64-en-in-priyarus-preview`        | Immagine del contenitore con `en-IN` le impostazioni locali `en-IN-PriyaRUS` e la voce.        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | Immagine del contenitore con `en-IN` le impostazioni locali `en-IN-Ravi-Apollo` e la voce.     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | Immagine del contenitore con `en-US` le impostazioni locali `en-US-BenjaminRUS` e la voce.     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | Immagine del contenitore con `en-US` le impostazioni locali `en-US-Guy24kRUS` e la voce.       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | Immagine del contenitore con `en-US` le impostazioni locali `en-US-Jessa24kRUS` e la voce.     |
| `1.2.0-amd64-en-us-jessarus-preview`        | Immagine del contenitore con `en-US` le impostazioni locali `en-US-JessaRUS` e la voce.        |
| `1.2.0-amd64-en-us-zirarus-preview`         | Immagine del contenitore con `en-US` le impostazioni locali `en-US-ZiraRUS` e la voce.         |
| `1.2.0-amd64-es-es-helenarus-preview`       | Immagine del contenitore con `es-ES` le impostazioni locali `es-ES-HelenaRUS` e la voce.       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | Immagine del contenitore con `es-ES` le impostazioni locali `es-ES-Laura-Apollo` e la voce.    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | Immagine del contenitore con `es-ES` le impostazioni locali `es-ES-Pablo-Apollo` e la voce.    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | Immagine del contenitore con `es-MX` le impostazioni locali `es-MX-HildaRUS` e la voce.        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | Immagine del contenitore con `es-MX` le impostazioni locali `es-MX-Raul-Apollo` e la voce.     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | Immagine del contenitore con `fr-CA` le impostazioni locali `fr-CA-Caroline` e la voce.        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | Immagine del contenitore con `fr-CA` le impostazioni locali `fr-CA-HarmonieRUS` e la voce.     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | Immagine del contenitore con `fr-FR` le impostazioni locali `fr-FR-HortenseRUS` e la voce.     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | Immagine del contenitore con `fr-FR` le impostazioni locali `fr-FR-Julie-Apollo` e la voce.    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | Immagine del contenitore con `fr-FR` le impostazioni locali `fr-FR-Paul-Apollo` e la voce.     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | Immagine del contenitore con `it-IT` le impostazioni locali `it-IT-Cosimo-Apollo` e la voce.   |
| `1.2.0-amd64-it-it-luciarus-preview`        | Immagine del contenitore con `it-IT` le impostazioni locali `it-IT-LuciaRUS` e la voce.        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | Immagine del contenitore con `ja-JP` le impostazioni locali `ja-JP-Ayumi-Apollo` e la voce.    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | Immagine del contenitore con `ja-JP` le impostazioni locali `ja-JP-HarukaRUS` e la voce.       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | Immagine del contenitore con `ja-JP` le impostazioni locali `ja-JP-Ichiro-Apollo` e la voce.   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | Immagine del contenitore con `ko-KR` le impostazioni locali `ko-KR-HeamiRUS` e la voce.        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | Immagine del contenitore con `pt-BR` le impostazioni locali `pt-BR-Daniel-Apollo` e la voce.   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | Immagine del contenitore con `pt-BR` le impostazioni locali `pt-BR-HeloisaRUS` e la voce.      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | Immagine del contenitore con `zh-CN` le impostazioni locali `zh-CN-HuihuiRUS` e la voce.       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | Immagine del contenitore con `zh-CN` le impostazioni locali `zh-CN-Kangkang-Apollo` e la voce. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | Immagine del contenitore con `zh-CN` le impostazioni locali `zh-CN-Yaoyao-Apollo` e la voce.   |
| `1.1.0-amd64-de-de-hedda-preview`           | Immagine del contenitore con `de-DE` le impostazioni locali `de-DE-Hedda` e la voce.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Immagine del contenitore con `de-DE` le impostazioni locali `de-DE-Hedda` e la voce.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Immagine del contenitore con `de-DE` le impostazioni locali `de-DE-HeddaRUS` e la voce.        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | Immagine del contenitore con `de-DE` le impostazioni locali `de-DE-Stefan-Apollo` e la voce.   |
| `1.1.0-amd64-en-au-catherine-preview`       | Immagine del contenitore con `en-AU` le impostazioni locali `en-AU-Catherine` e la voce.       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | Immagine del contenitore con `en-AU` le impostazioni locali `en-AU-HayleyRUS` e la voce.       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | Immagine del contenitore con `en-GB` le impostazioni locali `en-GB-George-Apollo` e la voce.   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | Immagine del contenitore con `en-GB` le impostazioni locali `en-GB-HazelRUS` e la voce.        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | Immagine del contenitore con `en-GB` le impostazioni locali `en-GB-Susan-Apollo` e la voce.    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | Immagine del contenitore con `en-IN` le impostazioni locali `en-IN-Heera-Apollo` e la voce.    |
| `1.1.0-amd64-en-in-priyarus-preview`        | Immagine del contenitore con `en-IN` le impostazioni locali `en-IN-PriyaRUS` e la voce.        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | Immagine del contenitore con `en-IN` le impostazioni locali `en-IN-Ravi-Apollo` e la voce.     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | Immagine del contenitore con `en-US` le impostazioni locali `en-US-BenjaminRUS` e la voce.     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | Immagine del contenitore con `en-US` le impostazioni locali `en-US-Guy24kRUS` e la voce.       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | Immagine del contenitore con `en-US` le impostazioni locali `en-US-Jessa24kRUS` e la voce.     |
| `1.1.0-amd64-en-us-jessarus-preview`        | Immagine del contenitore con `en-US` le impostazioni locali `en-US-JessaRUS` e la voce.        |
| `1.1.0-amd64-en-us-zirarus-preview`         | Immagine del contenitore con `en-US` le impostazioni locali `en-US-ZiraRUS` e la voce.         |
| `1.1.0-amd64-es-es-helenarus-preview`       | Immagine del contenitore con `es-ES` le impostazioni locali `es-ES-HelenaRUS` e la voce.       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | Immagine del contenitore con `es-ES` le impostazioni locali `es-ES-Laura-Apollo` e la voce.    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | Immagine del contenitore con `es-ES` le impostazioni locali `es-ES-Pablo-Apollo` e la voce.    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | Immagine del contenitore con `es-MX` le impostazioni locali `es-MX-HildaRUS` e la voce.        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | Immagine del contenitore con `es-MX` le impostazioni locali `es-MX-Raul-Apollo` e la voce.     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | Immagine del contenitore con `fr-CA` le impostazioni locali `fr-CA-Caroline` e la voce.        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | Immagine del contenitore con `fr-CA` le impostazioni locali `fr-CA-HarmonieRUS` e la voce.     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | Immagine del contenitore con `fr-FR` le impostazioni locali `fr-FR-HortenseRUS` e la voce.     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | Immagine del contenitore con `fr-FR` le impostazioni locali `fr-FR-Julie-Apollo` e la voce.    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | Immagine del contenitore con `fr-FR` le impostazioni locali `fr-FR-Paul-Apollo` e la voce.     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | Immagine del contenitore con `it-IT` le impostazioni locali `it-IT-Cosimo-Apollo` e la voce.   |
| `1.1.0-amd64-it-it-luciarus-preview`        | Immagine del contenitore con `it-IT` le impostazioni locali `it-IT-LuciaRUS` e la voce.        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | Immagine del contenitore con `ja-JP` le impostazioni locali `ja-JP-Ayumi-Apollo` e la voce.    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | Immagine del contenitore con `ja-JP` le impostazioni locali `ja-JP-HarukaRUS` e la voce.       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | Immagine del contenitore con `ja-JP` le impostazioni locali `ja-JP-Ichiro-Apollo` e la voce.   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | Immagine del contenitore con `ko-KR` le impostazioni locali `ko-KR-HeamiRUS` e la voce.        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | Immagine del contenitore con `pt-BR` le impostazioni locali `pt-BR-Daniel-Apollo` e la voce.   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | Immagine del contenitore con `pt-BR` le impostazioni locali `pt-BR-HeloisaRUS` e la voce.      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | Immagine del contenitore con `zh-CN` le impostazioni locali `zh-CN-HuihuiRUS` e la voce.       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | Immagine del contenitore con `zh-CN` le impostazioni locali `zh-CN-Kangkang-Apollo` e la voce. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | Immagine del contenitore con `zh-CN` le impostazioni locali `zh-CN-Yaoyao-Apollo` e la voce.   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | Immagine del contenitore con `en-US` le impostazioni locali `en-US-BenjaminRUS` e la voce.     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | Immagine del contenitore con `en-US` le impostazioni locali `en-US-Guy24kRUS` e la voce.       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | Immagine del contenitore con `en-US` le impostazioni locali `en-US-Jessa24kRUS` e la voce.     |
| `1.0.0-amd64-en-us-jessarus-preview`        | Immagine del contenitore con `en-US` le impostazioni locali `en-US-JessaRUS` e la voce.        |
| `1.0.0-amd64-en-us-zirarus-preview`         | Immagine del contenitore con `en-US` le impostazioni locali `en-US-ZiraRUS` e la voce.         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | Immagine del contenitore con `zh-CN` le impostazioni locali `zh-CN-HuihuiRUS` e la voce.       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | Immagine del contenitore con `zh-CN` le impostazioni locali `zh-CN-Kangkang-Apollo` e la voce. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | Immagine del contenitore con `zh-CN` le impostazioni locali `zh-CN-Yaoyao-Apollo` e la voce.   |

## <a name="key-phrase-extraction"></a>Estrazione frasi chiave

Il [estrazione frasi chiave][ta-kp] immagine del contenitore è disponibile nel Syndicate `mcr.microsoft.com` del registro contenitori. Si trova all'interno del `azure-cognitive-services` repository ed è denominato. `keyphrase` Il nome completo dell'immagine del contenitore è `mcr.microsoft.com/azure-cognitive-services/keyphrase`,.

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

Il [rilevamento lingua][ta-la] immagine del contenitore è disponibile nel Syndicate `mcr.microsoft.com` del registro contenitori. Si trova all'interno del `azure-cognitive-services` repository ed è denominato. `language` Il nome completo dell'immagine del contenitore è `mcr.microsoft.com/azure-cognitive-services/language`,.

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

Il [analisi del sentiment][ta-se] immagine del contenitore è disponibile nel Syndicate `mcr.microsoft.com` del registro contenitori. Si trova all'interno del `azure-cognitive-services` repository ed è denominato. `sentiment` Il nome completo dell'immagine del contenitore è `mcr.microsoft.com/azure-cognitive-services/sentiment`,.

Questa immagine del contenitore include i tag seguenti:

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
