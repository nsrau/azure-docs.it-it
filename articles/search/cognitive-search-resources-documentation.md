---
title: Risorse della documentazione di ricerca cognitiva- Ricerca di Azure
description: Un elenco annotato di articoli, esercitazioni, esempi e post di blog relativi ai carichi di lavoro della ricerca cognitiva in Ricerca di Azure.
services: search
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 609b5d990cffce10733f6fc82e6b1032ad0f06bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60951797"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>Risorse della documentazione per i carichi di lavoro della ricerca cognitiva

La ricerca cognitiva, ora in anteprima pubblica, è un nuovo livello di arricchimento nell'indicizzazione di Ricerca di Azure che consente di trovare informazioni latenti in origini non di testo e testo indifferenziato, trasformandolo in contenuto full-text disponibile per la ricerca in Ricerca di Azure.

Gli articoli seguenti rappresentano la documentazione completa per la ricerca cognitiva.

## <a name="getting-started"></a>Introduzione
+ [Che cos'è la ricerca cognitiva?](cognitive-search-concept-intro.md)
+ [Avvio rapido: Provare la ricerca cognitiva nel portale](cognitive-search-quickstart-blob.md)
+ [Esercitazione: Informazioni sulle API di ricerca cognitiva](cognitive-search-tutorial-blob.md)
+ [Esempio: creare una competenza personalizzata](cognitive-search-create-custom-skill-example.md)

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
  + [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ [Anteprima API REST](search-api-2017-11-11-preview.md)
  + [Creare un insieme di competenze (api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Creare un indicizzatore (api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Vedere anche 

+ [API REST Ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indicizzatori in Ricerca di Azure](search-indexer-overview.md)
+ [Che cos'è la Ricerca di Azure?](search-what-is-azure-search.md)
