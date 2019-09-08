---
title: Repository e immagini del contenitore
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Due tabelle che rappresentano i registri dei contenitori, i repository e i nomi delle immagini per tutte le offerte di servizi cognitivi.
ms.service: cognitive-services
ms.topic: include
ms.date: 09/06/2019
ms.author: dapine
ms.openlocfilehash: 600c01fc2a273141b06996369231c2389c5d6d57
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775864"
---
### <a name="container-repositories-and-images"></a>Repository e immagini del contenitore

Le tabelle seguenti sono un elenco completo delle immagini del contenitore disponibili offerte da servizi cognitivi di Azure.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Pubblico "non gated" (registro contenitori: `mcr.microsoft.com`)

Il Container Registry Microsoft ospita tutti i contenitori "ungated" disponibili pubblicamente per servizi cognitivi.

| Service | Contenitore | Container Registry/repository/nome dell'immagine |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Analisi del testo](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Estrazione frasi chiave | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Analisi del testo](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Rilevamento lingua | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Analisi del testo](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Analisi del sentiment | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Anteprima pubblica "gestita" (registro contenitori: `containerpreview.azurecr.io`)

Il registro di anteprima del contenitore ospita tutti i contenitori "controllati" disponibili pubblicamente per servizi cognitivi. Questi contenitori richiedono una richiesta formale di accesso per poterli utilizzare.

| Service | Contenitore | Container Registry/repository/nome dell'immagine |
|--|--|--|
| [Rilevamento anomalie](../../anomaly-detector/anomaly-detector-container-howto.md) | Rilevamento anomalie | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Visione artificiale](../../Computer-vision/computer-vision-how-to-install-containers.md) | Riconoscimento del testo | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text` |
| [Viso](../../face/face-how-to-install-containers.md) | Viso | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Riconoscimento moduli](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Riconoscimento modulo | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [API servizio Voce](../../speech-service/speech-container-howto.md) | Riconoscimento vocale | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [API servizio Voce](../../speech-service/speech-container-howto.md) | Sintesi vocale | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
