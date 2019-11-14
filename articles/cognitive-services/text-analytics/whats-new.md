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
ms.date: 11/04/2019
ms.author: aahi
ms.openlocfilehash: 15beb8d3e326f04f1ae61c26f00e9428d95f6bc4
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031366"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Novità dell'API Analisi del testo

Il API Analisi del testo viene aggiornato su base continuativa. Per rimanere sempre aggiornati sui recenti sviluppi, in questo articolo vengono fornite informazioni sulle nuove versioni e funzionalità.

## <a name="named-entity-recognition-v3-public-preview---october-2019"></a>Versione di anteprima pubblica denominata Entity Recognition V3-ottobre 2019

La versione successiva di Named Entity Recognition (NER) è ora disponibile per l'anteprima pubblica e fornisce funzionalità di rilevamento e categorizzazione espanse delle entità trovate nel testo. Fornisce:

* Riconoscimento dei nuovi tipi di entità seguenti:
    * Numero di telefono
    * Indirizzo IP

* Un [nuovo endpoint](https://cognitiveusw2ppe.portal.azure-api.net/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) per il riconoscimento dei tipi di entità di informazioni personali (solo in inglese)
* Separare gli endpoint per il [riconoscimento delle entità]( https://cognitiveusw2ppe.portal.azure-api.net/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) e il collegamento delle [entità]( https://cognitiveusw2ppe.portal.azure-api.net/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

Il collegamento di entità supporta inglese e spagnolo. Il supporto del linguaggio NER varia in base al tipo di entità. 

> [!div class="nextstepaction"]
> [Altre informazioni su Named Entity Recognition V3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-v3-public-preview)

## <a name="sentiment-analysis-v3-public-preview---october-2019"></a>Anteprima pubblica di Analisi del sentiment V3-2019 ottobre

La [prossima versione di analisi del sentiment](https://cognitiveusw2ppe.portal.azure-api.net/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) è ora disponibile per l'anteprima pubblica e offre miglioramenti significativi nell'accuratezza e nei dettagli della categorizzazione e del punteggio dell'API. Fornisce inoltre:

* Assegnazione automatica di etichette per diversi sentimenti nel testo.
* Analisi dei sentimenti e output a livello di documento e di frase. 

Supporta inglese (`en`), giapponese (`ja`), cinese semplificato (`zh-Hans`), cinese tradizionale (`zh-Hant`), francese (`fr`), Italiano (`it`), spagnolo (`es`), olandese (`nl`), portoghese (`pt`) e tedesco (`de`) ed è disponibile nelle aree geografiche seguenti: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`e `West US 2`. 

> [!div class="nextstepaction"]
> [Altre informazioni su Analisi del sentiment V3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-v3-public-preview)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sull'API Analisi del testo](overview.md)  
* [Esempi di scenari utente](text-analytics-user-scenarios.md)
* [Analisi del sentiment](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rilevamento della lingua](how-tos/text-analytics-how-to-language-detection.md)
* [Riconoscimento delle entità](how-tos/text-analytics-how-to-entity-linking.md)
* [Estrazione delle frasi chiave](how-tos/text-analytics-how-to-keyword-extraction.md)
