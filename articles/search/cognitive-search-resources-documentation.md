---
title: Risorse della documentazione di ricerca cognitiva- Ricerca di Azure
description: Un elenco annotato di articoli, esercitazioni, esempi e post di blog relativi ai carichi di lavoro della ricerca cognitiva in Ricerca di Azure.
services: search
manager: nitinme
author: HeidiSteen
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.subservice: cognitive-search
ms.openlocfilehash: 88e9db6526733960045e9a7f49e1f73fcaca8cf6
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639097"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>Risorse della documentazione per i carichi di lavoro della ricerca cognitiva

La ricerca cognitiva, ora disponibile a livello generale, è un nuovo livello di arricchimento nell'indicizzazione di ricerca di Azure che consente di trovare informazioni latenti in origini non di testo e testo non differenziato, trasformarlo in contenuto ricercabile full-text in ricerca di Azure.

Gli articoli seguenti rappresentano la documentazione completa per la ricerca cognitiva.

## <a name="getting-started"></a>Introduzione
+ [Che cos'è la ricerca cognitiva?](cognitive-search-concept-intro.md)
+ [Avvio rapido: Provare la ricerca cognitiva nel portale](cognitive-search-quickstart-blob.md)
+ [Esercitazione: Informazioni sulle API di ricerca cognitiva](cognitive-search-tutorial-blob.md)
+ [Esempio: Creare una competenza personalizzata per la ricerca cognitiva](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Guida alle procedure
+ [Come definire un insieme di competenze](cognitive-search-defining-skillset.md)
+ [Come fare riferimento alle annotazioni in un insieme di competenze](cognitive-search-concept-annotations-syntax.md)
+ [Come eseguire la mappatura dei campi a un indice](cognitive-search-output-field-mapping.md)
+ [Come elaborare ed estrarre informazioni dalle immagini](cognitive-search-concept-image-scenarios.md)
+ [Come ricompilare un indice di Ricerca di Azure](search-howto-reindex.md)
+ [Come definire un'interfaccia di competenze personalizzata](cognitive-search-custom-skill-interface.md)
+ [Suggerimenti per la risoluzione dei problemi](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Riferimenti

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
  + [Microsoft. Skills. Text. KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft. Skills. Text. TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft. Skills. util. ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Competenze personalizzate
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Competenze deprecate](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [API REST](https://docs.microsoft.com/rest/api/searchservice/)
  + [Creare un set di competenze (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Creare un indicizzatore (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Vedere anche

+ [API REST Ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indicizzatori in Ricerca di Azure](search-indexer-overview.md)
+ [Che cos'è la Ricerca di Azure?](search-what-is-azure-search.md)
