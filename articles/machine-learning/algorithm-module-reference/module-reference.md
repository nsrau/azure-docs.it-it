---
title: Informazioni di riferimento su algoritmi e moduli
description: Informazioni sui moduli disponibili in Azure Machine Learning Designer (anteprima)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: c46c21b5662b924552f850f226a12c7350b4625a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81458200"
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
| Input e output dei dati | Spostare i dati dalle origini cloud alla pipeline. Scrivere i risultati o i dati intermedi in archiviazione di Azure, un database SQL o hive, durante l'esecuzione di una pipeline, oppure usare l'archiviazione cloud per scambiare dati tra pipeline.  | [Immettere i dati manualmente](enter-data-manually.md) <br/> [Esportazione dei dati](export-data.md) <br/> [Importazione dei dati](import-data.md) |
| Trasformazione dei dati | Operazioni sui dati che sono univoche per Machine Learning, ad esempio la normalizzazione o la suddivisione in contenitori dei dati, la riduzione della dimensionalità e la conversione dei dati tra diversi formati di file.| [Aggiungere colonne](add-columns.md) <br/> [Aggiungi righe](add-rows.md) <br/> [Applicare operazioni matematiche](apply-math-operation.md) <br/> [Applicare la trasformazione SQL](apply-sql-transformation.md) <br/> [Pulire i dati mancanti](clean-missing-data.md) <br/> [Ritagliare valori](clip-values.md) <br/> [Eseguire la conversione in CSV](convert-to-csv.md) <br/> [Eseguire la conversione in set di dati](convert-to-dataset.md) <br/> [Eseguire la conversione in valori di indicatori](convert-to-indicator-values.md) <br/> [Modificare i metadati](edit-metadata.md) <br/> [Unire dati](join-data.md) <br/> [Normalizzare i dati](normalize-data.md) <br/> [Creare partizioni ed eseguire campionamenti](partition-and-sample.md)  <br/> [Rimuovere righe duplicate](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Select Columns Transform](select-columns-transform.md) <br/> [Select Columns in Dataset](select-columns-in-dataset.md) <br/> [Dividere dati](split-data.md) |
| Selezione caratteristiche | Consente di selezionare un subset di funzionalità utili rilevanti da utilizzare per la compilazione di un modello analitico. | [Filter Based Feature Selection](filter-based-feature-selection.md) <br/> [Permutation Feature Importance](permutation-feature-importance.md) |
| Funzioni di statistiche | Fornire un'ampia gamma di metodi statistici correlati a data science. | [Riepilogare i dati](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Algoritmi di apprendimento automatico

| Funzionalità | Descrizione | Modulo |
| --- |--- | --- |
| Regressione | Stimare un valore. | [Regressione albero delle decisioni incrementato](boosted-decision-tree-regression.md) <br/> [Regressione foresta delle decisioni](decision-forest-regression.md) <br/> [Linear Regression](linear-regression.md)  <br/> [Regressione rete neurale](neural-network-regression.md)  <br/> |
| Clustering | Raggruppare i dati.| [Clustering K-means](k-means-clustering.md)
| Classificazione | Stimare una classe.  Scegliere tra algoritmi binari (a due classi) o multiclasse.| [Albero delle decisioni incrementato a più classi](multiclass-boosted-decision-tree.md) <br/> [Foresta delle decisioni multiclasse](multiclass-decision-forest.md) <br/> [Regressione logistica multiclasse](multiclass-logistic-regression.md)  <br/> [Rete neurale multiclasse](multiclass-neural-network.md) <br/> [One vs. All Multiclass](one-vs-all-multiclass.md) <br/> [Percettrone medio a due classi](two-class-averaged-perceptron.md) <br/>  [Two-Class Boosted Decision Tree](two-class-boosted-decision-tree.md)  <br/> [Foresta delle decisioni a due classi](two-class-decision-forest.md) <br/>  [Regressione logistica a due classi](two-class-logistic-regression.md) <br/> [Rete neurale a due classi](two-class-neural-network.md) <br/> [Two-Class Support Vector Machine](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Moduli per la compilazione e la valutazione dei modelli

| Funzionalità | Descrizione | Modulo |
| --- |--- | --- |
| Training dei modelli | Eseguire i dati tramite l'algoritmo. |  [Eseguire il training del modello di clustering](train-clustering-model.md) <br/> [Eseguire il training del modello](train-model.md)  <br/> [Tune Model Hyperparameters](tune-model-hyperparameters.md) |
| Valutazione e punteggio del modello | Misurare l'accuratezza del modello sottoposto a training. | [Applicare la trasformazione](apply-transformation.md) <br/> [Assign Data to Clusters](assign-data-to-clusters.md) <br/> [Cross Validate Model](cross-validate-model.md) <br/> [Valutare il modello](evaluate-model.md) <br/> [Assegnare il punteggio al modello](score-model.md) |
| Linguaggio di programmazione di Python | Scrivere il codice e incorporarlo in un modulo per integrare Python con la pipeline. | [Creare il modello Python](create-python-model.md) <br/> [Eseguire lo script Python](execute-python-script.md) |
| Linguaggio R | Scrivere codice e incorporarlo in un modulo per integrare R con la pipeline. | [Execute R Script](execute-r-script.md) |
| Text Analytics | Fornire strumenti di calcolo specializzati per lavorare con testo strutturato e non strutturato. | [Extract N Gram Features from Text](extract-n-gram-features-from-text.md) <br/> [Feature Hashing](feature-hashing.md) <br/> [Preprocess Text](preprocess-text.md) <br/> [Latent Dirichlet Allocation](latent-dirichlet-allocation.md) |
| Recommendation | Compilare i modelli di raccomandazione. | [Evaluate Recommender](evaluate-recommender.md) <br/> [Score SVD Recommender](score-svd-recommender.md) <br/> [Train SVD Recommender](train-SVD-recommender.md) |
| Anomaly Detection | Modelli di rilevamento delle anomalie di compilazione. | [Rilevamento anomalie basato su PCA](pca-based-anomaly-detection.md) <br/> [Eseguire il training del modello di rilevamento anomalie](train-anomaly-detection-model.md) |


## <a name="web-service"></a>Servizio Web

Informazioni sui [moduli del servizio Web](web-service-input-output.md) che sono necessari per l'inferenza in tempo reale in Azure Machine Learning Designer.

## <a name="error-messages"></a>Messaggi di errore

Informazioni sui [messaggi di errore e i codici di eccezione](designer-error-codes.md) che possono verificarsi usando i moduli in Azure Machine Learning Designer.

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: creare un modello in progettazione per stimare i prezzi automatici](../tutorial-designer-automobile-price-train-score.md)
