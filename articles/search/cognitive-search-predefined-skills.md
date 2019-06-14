---
title: Estrazione di dati incorporata, linguaggio naturale, elaborazione delle immagini - Ricerca di Azure
description: Estrazione dei dati, linguaggio naturale, competenze cognitive di elaborazione delle immagini, semantica e struttura per il contenuto non elaborato in una pipeline di Ricerca di Azure.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7925f3aef4123fddd3a96c6e62971b881ae4cbc3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65021865"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>Competenze predefinite per l'arricchimento del contenuto (Ricerca di Azure)

Questo articolo illustra i concetti relativi alle competenze cognitive accluse a Ricerca di Azure. Una *competenza cognitiva* è un'operazione che trasforma in qualche modo il contenuto. Spesso si tratta di un componente che consente di estrarre dati o dedurre la struttura e pertanto aumenta il riconoscimento dei dati di input. L'output è quasi sempre basato su testo. Un *set di competenze* è una raccolta di competenze che definiscono la pipeline di arricchimento. 

> [!NOTE]
> Se si espande l'ambito aumentando la frequenza di elaborazione, aggiungendo più documenti oppure aggiungendo altri algoritmi di intelligenza artificiale, sarà necessario [collegare una risorsa fatturabile di Servizi cognitivi](cognitive-search-attach-cognitive-services.md). Gli addebiti si accumulano quando si chiamano le API in Servizi cognitivi e per l'estrazione di immagini come parte della fase di individuazione di documenti in Ricerca di Azure. Non sono previsti addebiti per l'estrazione di testo dai documenti.
>
> L'esecuzione delle competenze predefinite viene addebitata secondo gli attuali [prezzi con pagamento in base al consumo dei Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/). I prezzi per l'estrazione delle immagini sono descritti nella [pagina dei prezzi di Ricerca di Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="predefined-skills"></a>Competenze predefinite

Diverse competenze sono flessibili in relazione a ciò che usano o producono. In generale, la maggior parte delle competenze sono basate su modelli con training preliminare, ovvero di cui non è possibile eseguire il training usando i propri dati di training. Nella tabella seguente sono enumerate e descritte le competenze fornite da Microsoft. 

| Competenza | Descrizione |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Questa competenza usa un modello con training preliminare per rilevare le frasi importanti in base alla posizione del termine, alle regole linguistiche, alla prossimità ad altri termini e a quanto il termine è inusuale all'interno dei dati di origine. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Questa competenza usa un modello con training preliminare per rilevare la lingua usata (un ID lingua per ogni documento). Quando vengono usate più lingue all'interno dei segmenti dello stesso testo, l'output è l'identificatore LCID della lingua usata prevalentemente.|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | Consolida il testo da una raccolta di campi in un unico campo.  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Questa competenza usa un modello con training preliminare per stabilire le entità per un set fisso di categorie: persone, posizione, organizzazione, posta elettronica, URL, campi datetime. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Questa competenza usa un modello con training preliminare per assegnare un punteggio positivo o negativo della valutazione in base al record. Il valore è compreso tra 0 e 1. Si verificano punteggi neutri per entrambi i casi null quando la valutazione non può essere rilevata e per il testo che viene considerato neutro.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Suddivide il testo in più pagine in modo che sia possibile arricchire o aumentare il contenuto in modo incrementale. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Questa competenza usa un algoritmo di rilevamento dell'immagine per identificare il contenuto di un'immagine e generare una descrizione di testo. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Riconoscimento ottico dei caratteri. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Esegue il mapping di output a un tipo complesso (un tipo di dati multiparte, che può essere usato per un nome completo, un indirizzo su più righe o una combinazione di cognome e ID personale.) |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | Consente l'estendibilità della pipeline di ricerca cognitiva effettuando una chiamata HTTP in un'API Web personalizzata |


Per materiale sussidiario sulla creazione di una [competenza personalizzata](cognitive-search-custom-skill-web-api.md), consultare [Come definire un'interfaccia personalizzata](cognitive-search-custom-skill-interface.md) ed [Esempio: creazione di una competenza personalizzata](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Vedere anche

+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Definizione dell'interfaccia delle competenze personalizzate](cognitive-search-custom-skill-interface.md)
+ [Esercitazione: indicizzazione arricchita con ricerca cognitiva](cognitive-search-tutorial-blob.md)
