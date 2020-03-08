---
title: Informazioni di riferimento su algoritmi e moduli
description: Informazioni sui moduli disponibili in Azure Machine Learning Designer (anteprima)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 02/22/2020
ms.openlocfilehash: 334cf69db70e8a1e0c2994c492f15ff44505af0d
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673380"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer-preview"></a>Guida di riferimento al modulo & dell'algoritmo per Azure Machine Learning Designer (anteprima)

Questo contenuto di riferimento fornisce lo sfondo tecnico su ognuno degli algoritmi e dei moduli di machine learning disponibili in Azure Machine Learning Designer (anteprima).

Ogni modulo rappresenta un set di codice che può essere eseguito in modo indipendente ed eseguire un'attività di Machine Learning, in base agli input necessari. Un modulo può contenere un particolare algoritmo o eseguire un'attività di particolare rilevanza nell'apprendimento automatico, ad esempio la sostituzione di valori mancanti o l'analisi statistica.

Per informazioni sulla scelta degli algoritmi, vedere 
* [Come selezionare gli algoritmi](../how-to-select-algorithms.md)
* [Foglio informativo sugli algoritmi di Azure Machine Learning](../algorithm-cheat-sheet.md)

> [!TIP]
> In qualsiasi pipeline della finestra di progettazione è possibile ottenere informazioni su un modulo specifico. Selezionare il modulo e quindi fare clic sul collegamento **more help** (altre informazioni) nel riquadro **Quick Help** (Guida rapida).

## <a name="data-preparation-modules"></a>Moduli di preparazione dei dati


| Funzionalità | Descrizione | Modulo |
| --- |--- | --- |
| Input e output dei dati | Spostare i dati dalle origini cloud alla pipeline. Scrivere i risultati o i dati intermedi in archiviazione di Azure, un database SQL o hive, durante l'esecuzione di una pipeline, oppure usare l'archiviazione cloud per scambiare dati tra pipeline.  | [Immissione manuale dei dati](enter-data-manually.md) <br/> [Esportazione dei dati](export-data.md) <br/> [Importazione dei dati](import-data.md) |
| Trasformazione dei dati | Operazioni sui dati che sono univoche per Machine Learning, ad esempio la normalizzazione o la suddivisione in contenitori dei dati, la riduzione della dimensionalità e la conversione dei dati tra diversi formati di file.| [Aggiungi colonne](add-columns.md) <br/> [Aggiungi righe](add-rows.md) <br/> [Applica operazione matematica](apply-math-operation.md) <br/> [Applica trasformazione SQL](apply-sql-transformation.md) <br/> [Pulisci i dati mancanti](clean-missing-data.md) <br/> [Ritaglia valori](clip-values.md) <br/> [Converti in CSV](convert-to-csv.md) <br/> [Converti in DataSet](convert-to-dataset.md) <br/> [Converti in valori indicatore](convert-to-indicator-values.md) <br/> [Modificare i metadati](edit-metadata.md) <br/> [Unisci dati](join-data.md) <br/> [Normalizza i dati](normalize-data.md) <br/> [Partition ed Sample](partition-and-sample.md)  <br/> [Rimuovi righe duplicate](remove-duplicate-rows.md) <br/> [COLPÌ](smote.md) <br/> [Selezione trasformazione colonne](select-columns-transform.md) <br/> [Selezionare le colonne nel set di dati](select-columns-in-dataset.md) <br/> [Dividere dati](split-data.md) |
| Selezione caratteristiche | Consente di selezionare un subset di funzionalità utili rilevanti da utilizzare per la compilazione di un modello analitico. | [Selezione delle funzioni basata su filtro](filter-based-feature-selection.md) <br/> [Importanza della funzionalità di permutazione](permutation-feature-importance.md) |
| Funzioni di statistiche | Fornire un'ampia gamma di metodi statistici correlati a data science. | [Riepiloga dati](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Algoritmi di apprendimento automatico

| Funzionalità | Descrizione | Modulo |
| --- |--- | --- |
| Regressione | Stimare un valore. | [Regressione dell'albero delle decisioni con boosting](boosted-decision-tree-regression.md) <br/> [Regressione della foresta delle decisioni](decision-forest-regression.md) <br/> [Regressione lineare](linear-regression.md)  <br/> [Regressione di rete neurale](neural-network-regression.md)  <br/> |
| Clustering | Raggruppare i dati.| [Clustering K-means](k-means-clustering.md)
| Classificazione | Stimare una classe.  Scegliere tra algoritmi binari (a due classi) o multiclasse.| [Albero delle decisioni con boosting multiclasse](multiclass-boosted-decision-tree.md) <br/> [Foresta delle decisioni multiclasse](multiclass-decision-forest.md) <br/> [Regressione logistica multiclasse](multiclass-logistic-regression.md)  <br/> [Rete neurale multiclasse](multiclass-neural-network.md) <br/> [Una o tutte le multiclasse](one-vs-all-multiclass.md) <br/> [Perceptron media a due classi](two-class-averaged-perceptron.md) <br/>  [Albero delle decisioni incrementato a due classi](two-class-boosted-decision-tree.md)  <br/> [Foresta delle decisioni a due classi](two-class-decision-forest.md) <br/>  [Regressione logistica a due classi](two-class-logistic-regression.md) <br/> [Rete neurale a due classi](two-class-neural-network.md) <br/> [Macchina a vettori di supporto per due classi](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Moduli per la compilazione e la valutazione dei modelli

| Funzionalità | Descrizione | Modulo |
| --- |--- | --- |
| Training dei modelli | Eseguire i dati tramite l'algoritmo. |  [Training del modello di clustering](train-clustering-model.md) <br/> [Eseguire il training del modello](train-model.md)  <br/> [Ottimizzazione degli iperparametri del modello](tune-model-hyperparameters.md) |
| Valutazione e punteggio del modello | Misurare l'accuratezza del modello sottoposto a training. | [Applica trasformazione](apply-transformation.md) <br/> [Assegnare i dati ai cluster](assign-data-to-clusters.md) <br/> [Modello Cross Validate](cross-validate-model.md) <br/> [Valuta modello](evaluate-model.md) <br/> [Modello di Punteggio](score-model.md) |
| Linguaggio di programmazione di Python | Scrivere il codice e incorporarlo in un modulo per integrare Python con la pipeline. | [Creare un modello Python](create-python-model.md) <br/> [Esegui script Python](execute-python-script.md) |
| Linguaggio R | Scrivere codice e incorporarlo in un modulo per integrare R con la pipeline. | [Esegui script R](execute-r-script.md) |
| Text Analytics | Fornire strumenti di calcolo specializzati per lavorare con testo strutturato e non strutturato. | [Estrai le funzionalità di N grammi dal testo](extract-n-gram-features-from-text.md) <br/> [Hashing delle funzionalità](feature-hashing.md) <br/> [Pre-elabora testo](preprocess-text.md) |
| Recommendation | Compilare i modelli di raccomandazione. | [Valuta la raccomandazione](evaluate-recommender.md) <br/> [Punteggio SVD Recommender](score-svd-recommender.md) <br/> [Train SVD Recommender](train-SVD-recommender.md) |


## <a name="error-messages"></a>messaggi di errore

Informazioni sui [messaggi di errore e i codici di eccezione](designer-error-codes.md) che possono verificarsi usando i moduli in Azure Machine Learning Designer.

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: creare un modello in progettazione per stimare i prezzi automatici](../tutorial-designer-automobile-price-train-score.md)
