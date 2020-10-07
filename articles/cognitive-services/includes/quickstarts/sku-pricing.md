---
title: SKU e prezzi dei Servizi cognitivi
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: cb066ecc1dc11985c0ccb0fa687a15d6b038b9fa
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89321617"
---
Vedere l'elenco degli SKU e le informazioni sui prezzi di seguito. 

#### <a name="multi-service"></a>Multi-servizio

| Servizio                    | Tipo                      |
|----------------------------|---------------------------|
| Più servizi. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/).            | `CognitiveServices`     |


#### <a name="vision"></a>Visione

| Servizio                    | Tipo                      |
|----------------------------|---------------------------|
| Visione artificiale            | `ComputerVision`          |
| Visione personalizzata - Previsione | `CustomVision.Prediction` |
| Visione personalizzata - Training   | `CustomVision.Training`   |
| Viso                       | `Face`                    |
| Riconoscimento modulo            | `FormRecognizer`          |
| Riconoscimento input penna             | `InkRecognizer`           |

#### <a name="search"></a>Ricerca

| Servizio            | Tipo                  |
|--------------------|-----------------------|
| Suggerimenti automatici Bing   | `Bing.Autosuggest.v7` |
| Ricerca personalizzata Bing | `Bing.CustomSearch`   |
| Ricerca entità Bing | `Bing.EntitySearch`   |
| Ricerca Bing        | `Bing.Search.v7`      |
| Controllo ortografico Bing   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>Voce

| Servizio            | Tipo                 |
|--------------------|----------------------|
| Servizi Voce    | `SpeechServices`     |
| Riconoscimento vocale | `SpeakerRecognition` |

#### <a name="language"></a>Linguaggio

| Servizio            | Tipo                |
|--------------------|---------------------|
| Comprensione modulo | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Text Analytics     | `TextAnalytics`     |
| Traduzione testuale   | `TextTranslation`   |

#### <a name="decision"></a>Decisione

| Servizio           | Tipo               |
|-------------------|--------------------|
| Rilevamento anomalie  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizza esperienze      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>Piani tariffari e fatturazione

I piani tariffari (e l'importo fatturato) si basano sul numero di transazioni inviate usando le informazioni di autenticazione. Ogni piano tariffario specifica:
* Il numero massimo di transazioni consentite al secondo.
* Le funzionalità del servizio abilitate all'interno del piano tariffario.
* Il costo per un numero predefinito di transazioni. Il superamento di questo numero determinerà un addebito aggiuntivo, come specificato nei [dettagli dei prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) per il servizio.

> [!NOTE]
> Molti servizi cognitivi prevedono un livello gratuito che è possibile usare per provarli. Per usufruire del livello gratuito, usare `F0` come SKU per la risorsa.