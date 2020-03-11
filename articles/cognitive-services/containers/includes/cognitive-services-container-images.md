---
title: Repository e immagini del contenitore
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Due tabelle che rappresentano i registri dei contenitori, i repository e i nomi delle immagini per tutte le offerte di servizi cognitivi.
ms.service: cognitive-services
ms.topic: include
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 55a3bb5f894d3ab753cfec64687abc9c7cae53cb
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082687"
---
### <a name="container-repositories-and-images"></a>Repository e immagini del contenitore

Le tabelle seguenti sono un elenco delle immagini di contenitori disponibili offerte da servizi cognitivi di Azure. Per un elenco completo di tutti i nomi delle immagini del contenitore disponibili e dei relativi tag disponibili, vedere [tag dell'immagine del contenitore di servizi cognitivi](../container-image-tags.md). Attualmente non sono disponibili contenitori di servizi cognitivi a livello generale (GA). Per il momento, fino a quando non vengono resi disponibili altri annunci, i contenitori sono disponibili come *Anteprima*pubblica o non *gestita* .

 - *Public ungated*: i contenitori sono disponibili pubblicamente senza un meccanismo di controllo.
 - *Public gated Preview*: i contenitori sono disponibili pubblicamente, ma richiedono prima una richiesta formale per accedere al registro contenitori.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Pubblico "non gated" (registro contenitori: `mcr.microsoft.com`)

Microsoft Container Registry (il Consorzio) pubblica tutti i contenitori "non controllati" disponibili pubblicamente per servizi cognitivi. I contenitori sono anche disponibili direttamente dall' [Hub Docker](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Service | Contenitore | Container Registry/repository/nome dell'immagine |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Analisi del testo](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Estrazione frasi chiave | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Analisi del testo](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Rilevamento lingua | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Analisi del testo](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Analisi del sentiment | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Anteprima pubblica "gestita" (registro contenitori: `containerpreview.azurecr.io`)

Il registro di anteprima del contenitore ospita tutti i contenitori "controllati" disponibili pubblicamente per servizi cognitivi. Questi contenitori richiedono una richiesta formale per accedervi tramite il registro contenitori.

| Service | Contenitore | Container Registry/repository/nome dell'immagine |
|--|--|--|
| [Rilevamento anomalie](../../anomaly-detector/anomaly-detector-container-howto.md) | Rilevamento anomalie | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Visione artificiale](../../Computer-vision/computer-vision-how-to-install-containers.md) | Lettura | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Viso](../../face/face-how-to-install-containers.md) | Viso | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Riconoscimento moduli](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Riconoscimento modulo | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [API servizio Voce](../../speech-service/speech-container-howto.md?tab=stt) | Riconoscimento vocale | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [API servizio Voce](../../speech-service/speech-container-howto.md?tab=cstt) | Da Riconoscimento vocale personalizzato a testo | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [API servizio Voce](../../speech-service/speech-container-howto.md?tab=tts) | Sintesi vocale | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [API servizio Voce](../../speech-service/speech-container-howto.md?tab=ctts) | Sintesi vocale personalizzata | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
