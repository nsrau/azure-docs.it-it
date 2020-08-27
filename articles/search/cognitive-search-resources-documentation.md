---
title: Collegamenti alla documentazione per l'arricchimento tramite intelligenza artificiale
titleSuffix: Azure Cognitive Search
description: Elenco con annotazioni di articoli, esercitazioni, esempi e post di blog relativi ai carichi di lavoro di arricchimento tramite intelligenza artificiale in Ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: eaafb9536a8667417c0abf4ab570609f2db290bc
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935348"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Risorse di documentazione per l'arricchimento tramite intelligenza artificiale in Ricerca cognitiva di Azure

L'arricchimento di intelligenza artificiale è un componente aggiuntivo per l'indicizzazione basata su indicizzatore che consente di trovare informazioni latenti in origini non di testo e testo non differenziato, trasformarlo in contenuto ricercabile full-text in Azure ricerca cognitiva. 

Per l'elaborazione incorporata, i modelli di intelligenza artificiale pre-sottoposti a training in Servizi cognitivi vengono chiamati internamente per eseguire le analisi. È anche possibile integrare modelli personalizzati usando Azure Machine Learning, funzioni di Azure o altri approcci.

Di seguito è riportato un elenco consolidato della documentazione relativa all'arricchimento di intelligenza artificiale.

## <a name="concepts"></a>Concetti

+ [Arricchimenti AI](cognitive-search-concept-intro.md)
+ [Set di competenze](cognitive-search-working-with-skillsets.md)
+ [Sessioni di debug](cognitive-search-debug-session.md)
+ [Archivi conoscenze](knowledge-store-concept-intro.md)
+ [Proiezioni](knowledge-store-projection-overview.md)
+ [Arricchimento incrementale (riutilizzo di un documento arricchito memorizzato nella cache)](cognitive-search-incremental-indexing-conceptual.md)

## <a name="hands-on-walkthroughs"></a>Procedure dettagliate

+ [Guida introduttiva: creare un di competenze cognitive nell'portale di Azure](cognitive-search-quickstart-blob.md)
+ [Esercitazione: indicizzazione arricchita con intelligenza artificiale](cognitive-search-tutorial-blob.md)
+ [Esercitazione: diagnostica, ripristino e commit delle modifiche apportate alle tue competenze con le sessioni di debug](cognitive-search-tutorial-debug-sessions.md)

## <a name="knowledge-stores"></a>Archivi conoscenze

+ [Guida introduttiva: creare un archivio informazioni nell'portale di Azure](knowledge-store-create-portal.md)
+ [Creare un archivio conoscenze con REST e Postman](knowledge-store-create-rest.md)
+ [Visualizzare un archivio conoscenze con Storage Explorer](knowledge-store-view-storage-explorer.md)
+ [Connettere un archivio conoscenze con Power BI](knowledge-store-connect-power-bi.md)
+ [Esempi di proiezione (come modellare ed esportare gli arricchimenti)](knowledge-store-projections-examples.md)

## <a name="custom-skills-advanced"></a>Competenze personalizzate (avanzate)

+ [Come definire un'interfaccia di competenze personalizzata](cognitive-search-custom-skill-interface.md)
+ [Esempio: creare una competenza personalizzata usando funzioni di Azure (e API Ricerca entità Bing)](cognitive-search-create-custom-skill-example.md)
+ [Esempio: creare una competenza personalizzata usando Python](cognitive-search-custom-skill-python.md)
+ [Esempio: creare una competenza personalizzata usando il sistema di riconoscimento del modulo](cognitive-search-custom-skill-form.md) 
+ [Esempio: creare una competenza personalizzata usando Azure Machine Learning](cognitive-search-tutorial-aml-custom-skill.md) 

## <a name="how-to-guidance"></a>Guida alle procedure

+ [Collegare una risorsa Servizi cognitivi](cognitive-search-attach-cognitive-services.md)
+ [Definire un insieme di competenze](cognitive-search-defining-skillset.md)
+ [annotazioni di riferimento in un skillt](cognitive-search-concept-annotations-syntax.md)
+ [Eseguire il mapping di campi a un indice](cognitive-search-output-field-mapping.md)
+ [Elaborare ed estrarre informazioni dalle immagini](cognitive-search-concept-image-scenarios.md)
+ [Configurare la memorizzazione nella cache per l'arricchimento incrementale](search-howto-incremental-index.md)
+ [Come ricompilare un indice di Ricerca cognitiva di Azure](search-howto-reindex.md)
+ [Suggerimenti per la progettazione](cognitive-search-concept-troubleshooting.md)
+ [Errori e avvisi comuni](cognitive-search-common-errors-warnings.md)

## <a name="skills-reference"></a>Informazioni di riferimento sulle competenze

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Competenze personalizzate
  + [Microsoft. Skills. Custom. AmlSkill](cognitive-search-aml-skill.md)
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Competenze deprecate](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

## <a name="apis"></a>API

+ [REST API](/rest/api/searchservice/)
  + [Crea competenze (API-Version = 2020-06-30)](/rest/api/searchservice/create-skillset)
  + [Crea indicizzatore (API-Version = 2020-06-30)](/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Vedere anche

+ [API REST di Ricerca cognitiva di Azure](/rest/api/searchservice/)
+ [Indicizzatori in Ricerca cognitiva di Azure](search-indexer-overview.md)
+ [Che cos'è la ricerca cognitiva di Azure?](search-what-is-azure-search.md)