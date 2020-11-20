---
title: Repository e immagini del contenitore
services: cognitive-services
author: aahill
manager: nitinme
description: Due tabelle che rappresentano i registri dei contenitori, i repository e i nomi delle immagini per tutte le offerte di servizi cognitivi.
ms.service: cognitive-services
ms.topic: include
ms.date: 09/03/2020
ms.author: aahi
ms.openlocfilehash: 2c2ebe9b419100163ae55c1be85dd1464904e841
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94979690"
---
### <a name="container-repositories-and-images"></a>Repository e immagini del contenitore

Le tabelle seguenti sono un elenco delle immagini di contenitori disponibili offerte da servizi cognitivi di Azure. Per un elenco completo di tutti i nomi delle immagini del contenitore disponibili e dei relativi tag disponibili, vedere [tag dell'immagine del contenitore di servizi cognitivi](../container-image-tags.md). 

#### <a name="generally-available"></a>Disponibile a livello generale 

Microsoft Container Registry (il Consorzio Microsoft) pubblica tutti i contenitori disponibili a livello generale per servizi cognitivi. I contenitori sono anche disponibili direttamente dall' [Hub Docker](https://hub.docker.com/_/microsoft-azure-cognitive-services).

**LUIS**

| Contenitore | Container Registry/repository/nome dell'immagine |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/language/luis` |

Per ulteriori informazioni [, vedere come eseguire e installare i contenitori Luis](../../LUIS/luis-container-howto.md) .

**Analisi del testo**

| Contenitore | Container Registry/repository/nome dell'immagine |
|--|--|
| Analisi del sentiment V3 (Inglese) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en` |
| Analisi del sentiment V3 (Spagnolo) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-es` |
| Analisi del sentiment V3 (Francese) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-fr` |
| Analisi del sentiment V3 (Italiano) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-it` |
| Analisi del sentiment V3 (tedesco) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-de` |
| Analisi del sentiment V3 (cinese semplificato) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zh` |
| Analisi del sentiment V3 (cinese tradizionale) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zht` |
| Analisi del sentiment V3 (giapponese) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-ja` |
| Analisi del sentiment V3 (Portoghese) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-pt` |
| Analisi del sentiment V3 (Olandese) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-nl` |

Per ulteriori informazioni, vedere [come eseguire e installare contenitori analisi del testo](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) .

**Rilevamento anomalie** 

| Contenitore | Container Registry/repository/nome dell'immagine |
|--|--|
| Rilevamento anomalie | `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` |

Per altre informazioni [, vedere How to run and install Anomaly Detector Containers](../../anomaly-detector/anomaly-detector-container-howto.md) .

**Servizio di riconoscimento vocale**

> [!NOTE]
> Per usare i contenitori di riconoscimento vocale, sarà necessario completare un [modulo di richiesta online](https://aka.ms/csgate).

| Contenitore | Container Registry/repository/nome dell'immagine |
|--|--|
| [Riconoscimento vocale](../../speech-service/speech-container-howto.md?tab=stt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` |
| [Da Riconoscimento vocale personalizzato a testo](../../speech-service/speech-container-howto.md?tab=cstt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` |
| [Sintesi vocale](../../speech-service/speech-container-howto.md?tab=tts) | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` |

#### <a name="ungated-preview"></a>Anteprima "non gestita" 

I contenitori di anteprima seguenti sono disponibili pubblicamente. Microsoft Container Registry (il Consorzio) ha tutti i contenitori non gated disponibili pubblicamente per servizi cognitivi. I contenitori sono anche disponibili direttamente dall' [Hub Docker](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Service | Contenitore | Container Registry/repository/nome dell'immagine |
|--|--|--|
| [Analisi del testo](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Estrazione frasi chiave | `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` |
| [Analisi del testo](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Rilevamento lingua | `mcr.microsoft.com/azure-cognitive-services/textanalytics/language` |


#### <a name="gated-preview"></a>Anteprima "gestita"

In precedenza, i contenitori di anteprima gestita erano ospitati nel `containerpreview.azurecr.io` repository. A partire dal 22 settembre 2020, i contenitori (eccetto Analisi del testo per l'integrità) sono ospitati in Microsoft Container Registry (e il download non richiede l'uso del comando Docker login. Per usare il contenitore è necessario:

1. Completare un [modulo di richiesta](https://aka.ms/csgate) con l'ID sottoscrizione di Azure e lo scenario utente. 
2. Al momento dell'approvazione, scaricare il contenitore da. 
3. Usare la chiave e l'endpoint da una risorsa di Azure appropriata per autenticare il contenitore in fase di esecuzione. 

| Service | Contenitore | Container Registry/repository/nome dell'immagine |
|--|--|--|
| [Visione artificiale](../../Computer-vision/computer-vision-how-to-install-containers.md) | Lettura v 2.0 | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| [Visione artificiale](../../Computer-vision/computer-vision-how-to-install-containers.md) | Leggi v 3.1 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |
| [Visione artificiale](../../computer-vision/spatial-analysis-container.md) | Analisi spaziale | `mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis` |
| [API servizio Voce](../../speech-service/speech-container-howto.md?tab=ctts) | Sintesi vocale personalizzata | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` |
| [API servizio Voce](../../speech-service/speech-container-howto.md?tab=lid) | Rilevamento lingua | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` |
| [API servizio Voce](../../speech-service/speech-container-howto.md?tab=ntts) | Sintesi vocale neurale | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` |
| [Analisi del testo per la sanità](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health) | Analisi del testo per la sanità | `containerpreview.azurecr.io/microsoft/cognitive-services-healthcare` |