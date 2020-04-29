---
title: Novità della API Analisi del testo
titleSuffix: Text Analytics - Azure Cognitive Services
description: Questo articolo fornisce informazioni sulle nuove versioni e funzionalità per i servizi cognitivi di Azure Analisi del testo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 162e60ac8d33dc5d1951a58b0a9643b668608d7b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77188811"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Novità dell'API Analisi del testo

Il API Analisi del testo viene aggiornato su base continuativa. Per rimanere sempre aggiornati sui recenti sviluppi, in questo articolo vengono fornite informazioni sulle nuove versioni e funzionalità.

## <a name="february-2020"></a>Febbraio 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Supporto SDK per l'anteprima pubblica API Analisi del testo V3

Nell'ambito della [versione unificata di Azure SDK](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290), il API analisi del testo V3 SDK è ora disponibile come anteprima pubblica per i linguaggi di programmazione seguenti:
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (node. js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)

> [!div class="nextstepaction"]
> [Scopri di più su API Analisi del testo V3 SDK](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Anteprima pubblica di riconoscimento entità denominata V3

I tipi di entità aggiuntivi sono ora disponibili nel servizio di anteprima pubblica per il riconoscimento delle entità denominate (NER) v3 durante l'espansione del rilevamento di entità di informazioni generali e personali presenti nel testo. Questo aggiornamento introduce la [versione](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2020-02-01`del modello, che include:

* Riconoscimento dei seguenti tipi di entità generali (solo in inglese):
    * PersonType
    * Prodotto
    * Event
    * Entità geopolitica (GPE) come sottotipo in location
    * Competenza

* Riconoscimento dei seguenti tipi di entità di informazioni personali (solo in inglese):
    * Persona
    * Organization
    * Age come sottotipo sotto Quantity
    * Data come sottotipo sotto DateTime
    * Posta elettronica 
    * Numero di telefono (solo Stati Uniti)
    * URL
    * Indirizzo IP

> [!div class="nextstepaction"]
> [Altre informazioni su Named Entity Recognition V3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>Ottobre 2019

#### <a name="named-entity-recognition-ner"></a>Riconoscimento delle entità denominate (NER)

* Un [nuovo endpoint](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) per il riconoscimento dei tipi di entità di informazioni personali (solo in inglese)

* Separare gli endpoint per il [riconoscimento delle entità](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) e il collegamento delle [entità](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

* [Versione](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2019-10-01`del modello, che include:
    * Rilevamento espanso e categorizzazione di entità presenti nel testo. 
    * Riconoscimento dei nuovi tipi di entità seguenti:
        * Numero di telefono
        * Indirizzo IP

Il collegamento di entità supporta inglese e spagnolo. Il supporto del linguaggio NER varia in base al tipo di entità.

#### <a name="sentiment-analysis-v3-public-preview"></a>Anteprima pubblica di Analisi del sentiment versione 3

* Un [nuovo endpoint](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) per analizzare i sentimenti.
* [Versione](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) `2019-10-01`del modello, che include:

    * Miglioramenti significativi nell'accuratezza e nei dettagli della categorizzazione e del punteggio del testo dell'API.
    * Assegnazione automatica di etichette per diversi sentimenti nel testo.
    * Analisi dei sentimenti e output a livello di documento e di frase. 

Supporta inglese (`en`), giapponese (`ja`), cinese semplificato (`zh-Hans`), cinese tradizionale (`zh-Hant`), francese (`fr`), Italiano (`it`), spagnolo (`es`), olandese (`nl`), portoghese (`pt`) e tedesco (`de`) ed è disponibile nelle aree seguenti: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`e. `West US 2` 

> [!div class="nextstepaction"]
> [Altre informazioni su Analisi del sentiment V3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sull'API Analisi del testo](overview.md)  
* [Esempi di scenari utente](text-analytics-user-scenarios.md)
* [Analisi del sentiment](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rilevamento della lingua](how-tos/text-analytics-how-to-language-detection.md)
* [Riconoscimento entità](how-tos/text-analytics-how-to-entity-linking.md)
* [Estrazione delle frasi chiave](how-tos/text-analytics-how-to-keyword-extraction.md)
