---
title: Novità dell'API Analisi del testo
titleSuffix: Text Analytics - Azure Cognitive Services
description: Questo articolo fornisce informazioni sulle nuove versioni e sulle nuove funzionalità per l'analisi del testo di Servizi cognitivi di Azure.This article provides you with information about new releases and features for the Azure Cognitive Services Text Analytics.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 162e60ac8d33dc5d1951a58b0a9643b668608d7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77188811"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Novità dell'API Analisi del testo

L'API Analisi del testo viene aggiornata su base continuativa. Per rimanere aggiornati con gli sviluppi recenti, in questo articolo vengono fornite informazioni sulle nuove versioni e funzionalità.

## <a name="february-2020"></a>Febbraio 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Supporto SDK per l'anteprima pubblica dell'API di analisi del testo v3

Come parte della versione unificata di [Azure SDK](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290), l'API di analisi del testo v3 SDK è ora disponibile come anteprima pubblica per i linguaggi di programmazione seguenti:
   * [C #](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)

> [!div class="nextstepaction"]
> [Ulteriori informazioni sull'SDK dell'API di analisi del testo v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Anteprima pubblica di Named Entity Recognition v3

Tipi di entità aggiuntivi sono ora disponibili nel servizio di anteprima pubblico NER (Named Entity Recognition) v3 quando si espande il rilevamento delle entità di informazioni generali e personali presenti nel testo. Questo aggiornamento introduce la [versione](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2020-02-01`del modello , che include:

* Riconoscimento dei seguenti tipi di entità generali (solo in inglese):
    * PersonaTipo
    * Prodotto
    * Event
    * Entità geopolitica (GPE) come sottotipo in Posizione
    * Competenza

* Riconoscimento dei seguenti tipi di entità di informazioni personali (solo in inglese):
    * Persona
    * Organization
    * Età come sottotipo in Quantità
    * Data come sottotipo in DateTime
    * Email 
    * Numero di telefono (solo USA)
    * URL
    * Indirizzo IP

> [!div class="nextstepaction"]
> [Ulteriori informazioni su Named Entity Recognition v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>Ottobre 2019

#### <a name="named-entity-recognition-ner"></a>Riconoscimento delle entità denominate (NER)

* Un [nuovo endpoint](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) per il riconoscimento dei tipi di entità informazioni personali (solo in inglese)

* Endpoint separati per il [riconoscimento](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) delle entità e il [collegamento di entità.](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking)

* [Versione modello](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2019-10-01`, che include:
    * Rilevamento e categorizzazione espansi delle entità trovate nel testo. 
    * Riconoscimento dei seguenti nuovi tipi di entità:
        * Numero di telefono
        * Indirizzo IP

Il collegamento di entità supporta l'inglese e lo spagnolo. Il supporto del linguaggio NER varia in base al tipo di entità.

#### <a name="sentiment-analysis-v3-public-preview"></a>Anteprima pubblica di Analisi del sentiment versione 3

* Un [nuovo endpoint](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) per l'analisi del sentiment.
* [Versione modello](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) `2019-10-01`, che include:

    * Miglioramenti significativi nella precisione e nei dettagli della categorizzazione e del punteggio del testo dell'API.
    * Etichettatura automatica per diversi sentimenti nel testo.
    * Analisi del sentiment e output a livello di documento e frase. 

Supporta l'inglese`en`(`ja`), giapponese`zh-Hans`( ),`zh-Hant`cinese semplificato (`it`),`es`cinese tradizionale`nl`(`pt`), francese`de` `Australia East`( `Central Canada` `Central US` `East Asia` `East US` `East US 2` `North Europe` `Southeast Asia` `South Central US` `UK South``fr`), italiano ( ), spagnolo ( ), olandese `West Europe`( `West US 2`), portoghese ( ) e tedesco ( ) ed è disponibile nelle seguenti regioni: , , , , , , , , , , e . 

> [!div class="nextstepaction"]
> [Ulteriori informazioni su Sentiment Analysis v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sull'API Analisi del testo](overview.md)  
* [Esempi di scenari utente](text-analytics-user-scenarios.md)
* [Analisi del sentiment](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rilevamento della lingua](how-tos/text-analytics-how-to-language-detection.md)
* [Riconoscimento delle entità](how-tos/text-analytics-how-to-entity-linking.md)
* [Estrazione di frasi chiave](how-tos/text-analytics-how-to-keyword-extraction.md)
