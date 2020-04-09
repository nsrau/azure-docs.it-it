---
title: Archivi contenitori e immagini
services: cognitive-services
author: aahill
manager: nitinme
description: Due tabelle che rappresentano i registri contenitore, i repository e i nomi delle immagini per tutte le offerte di servizi cognitivi.
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a854a090af908da691e9b26f5b0714c6560fc0ba
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876829"
---
### <a name="container-repositories-and-images"></a>Archivi contenitori e immagini

The tables below are a listing of the available container images offered by Azure Cognitive Services. Per un elenco completo di tutti i nomi di immagini del contenitore disponibili e dei relativi tag disponibili, vedere [Tag immagine del contenitore Servizi cognitivi.](../container-image-tags.md) Attualmente, non sono disponibili contenitori di servizi cognitivi (GA). Per il momento, fino a quando non vengono fatti ulteriori annunci: i contenitori sono disponibili come *Public Ungated* o *Public Gated Preview*.

 - *Pubblico Ungated*: i contenitori sono disponibili pubblicamente senza un meccanismo di gating.
 - *Public Gated Preview*: i contenitori sono disponibili pubblicamente, ma prima richiedono una richiesta formale per accedere al registro contenitori.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Pubblico "Ungated" (registro dei contenitori: `mcr.microsoft.com`)

Microsoft Container Registry (MCR) pubblicamente disponibile contenitori "ungated" per servizi cognitivi. I contenitori sono disponibili anche direttamente [dall'hub Docker](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Service | Contenitore | Registro contenitori / Repository / Nome immagine |
|--|--|--|
| [Luis](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cogni'ive-services/luis` |
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Estrazione frasi chiave | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Rilevamento lingua | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Analisi del sentiment | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Anteprima pubblica "Gated" (registro contenitori: `containerpreview.azurecr.io`)

Il Registro di sistema di Container Preview ospita tutti i contenitori "gated" disponibili pubblicamente per i servizi cognitivi. Questi contenitori richiedono una richiesta formale per accedervi tramite il registro dei contenitori.

| Service | Contenitore | Registro contenitori / Repository / Nome immagine |
|--|--|--|
| [Rilevatore di anomalie](../../anomaly-detector/anomaly-detector-container-howto.md) | Rilevamento anomalie | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Visione artificiale](../../Computer-vision/computer-vision-how-to-install-containers.md) | Lettura | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Viso](../../face/face-how-to-install-containers.md) | Viso | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Riconoscitore di moduli](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Riconoscimento modulo | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [API servizio Voce](../../speech-service/speech-container-howto.md?tab=stt) | Riconoscimento vocale | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [API servizio Voce](../../speech-service/speech-container-howto.md?tab=cstt) | Sintesi vocale personalizzata | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [API servizio Voce](../../speech-service/speech-container-howto.md?tab=tts) | Sintesi vocale | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [API servizio Voce](../../speech-service/speech-container-howto.md?tab=ctts) | Sintesi vocale personalizzata | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
