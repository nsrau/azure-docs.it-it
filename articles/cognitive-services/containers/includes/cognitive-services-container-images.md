---
title: Repository e immagini del contenitore
services: cognitive-services
author: aahill
manager: nitinme
description: Due tabelle che rappresentano i registri dei contenitori, i repository e i nomi delle immagini per tutte le offerte di servizi cognitivi.
ms.service: cognitive-services
ms.topic: include
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: ff039d6d5879e036aecc63b46359d84673f84a0e
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89424642"
---
### <a name="container-repositories-and-images"></a>Repository e immagini del contenitore

Le tabelle seguenti sono un elenco delle immagini di contenitori disponibili offerte da servizi cognitivi di Azure. Per un elenco completo di tutti i nomi delle immagini del contenitore disponibili e dei relativi tag disponibili, vedere [tag dell'immagine del contenitore di servizi cognitivi](../container-image-tags.md). 

#### <a name="generally-available"></a>Disponibile a livello generale 

Microsoft Container Registry (il Consorzio Microsoft) pubblica tutti i contenitori disponibili a livello generale per servizi cognitivi. I contenitori sono anche disponibili direttamente dall' [Hub Docker](https://hub.docker.com/_/microsoft-azure-cognitive-services).

#### <a name="luis"></a>[LUIS](#tab/luis)

| Contenitore LUIS | Container Registry/repository/nome dell'immagine |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |

Per ulteriori informazioni [, vedere come eseguire e installare i contenitori Luis](../../LUIS/luis-container-howto.md) .

#### <a name="text-analytics"></a>[Analisi del testo](#tab/text-analytics)

| Contenitore di Analisi del testo | Container Registry/repository/nome dell'immagine |
|--|--|
| Analisi del sentiment V3 (Inglese) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en` |
| Analisi del sentiment V3 (Spagnolo) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-es` |
| Analisi del sentiment V3 (Francese) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-fr` |
| Analisi del sentiment V3 (Italiano) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-it` |
| Analisi del sentiment V3 (tedesco) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-de` |
| Analisi del sentiment V3 (cinese semplificato) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zh` |
| Analisi del sentiment V3 (cinese tradizionale) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zht` |
| Analisi del sentiment V3 (giapponese) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-ja` |
| Analisi del sentiment V3 (Portoghese) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-pt` |
| Analisi del sentiment V3 (Olandese) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-nl` |

Per ulteriori informazioni, vedere [come eseguire e installare contenitori analisi del testo](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) .

---

#### <a name="public-ungated-preview-container-registry-mcrmicrosoftcom"></a>Anteprima pubblica "ungated" (registro contenitori: `mcr.microsoft.com` )

I contenitori di anteprima seguenti sono disponibili pubblicamente. Microsoft Container Registry (il Consorzio) ha tutti i contenitori non gated disponibili pubblicamente per servizi cognitivi. I contenitori sono anche disponibili direttamente dall' [Hub Docker](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Servizio | Contenitore | Container Registry/repository/nome dell'immagine |
|--|--|--|
| [Analisi del testo](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Estrazione frasi chiave | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Analisi del testo](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Rilevamento lingua | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Rilevamento anomalie](../../anomaly-detector/anomaly-detector-container-howto.md) | Rilevamento anomalie | `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Anteprima pubblica "gestita" (registro contenitori: `containerpreview.azurecr.io` )

I seguenti contenitori di anteprima gestita sono ospitati nel registro di anteprima del contenitore e richiedono l'accesso a un'applicazione. Per ulteriori informazioni, vedere gli articoli sui contenitori seguenti.

| Servizio | Contenitore | Container Registry/repository/nome dell'immagine |
|--|--|--|
| [Visione artificiale](../../Computer-vision/computer-vision-how-to-install-containers.md) | Lettura | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Viso](../../face/face-how-to-install-containers.md) | Viso | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Riconoscimento moduli](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Riconoscimento modulo | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [API servizio Voce](../../speech-service/speech-container-howto.md?tab=stt) | Riconoscimento vocale | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [API servizio Voce](../../speech-service/speech-container-howto.md?tab=cstt) | Da Riconoscimento vocale personalizzato a testo | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [API servizio Voce](../../speech-service/speech-container-howto.md?tab=tts) | Sintesi vocale | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [API servizio Voce](../../speech-service/speech-container-howto.md?tab=ctts) | Sintesi vocale personalizzata | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
| [API servizio Voce](../../speech-service/speech-container-howto.md?tab=ntts) | Sintesi vocale neurale | `containerpreview.azurecr.io/microsoft/cognitive-services-neural-text-to-speech` |
| [Analisi del testo per l'integrità](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health) | Analisi del testo per la sanità | `containerpreview.azurecr.io/microsoft/cognitive-services-healthcare` |
