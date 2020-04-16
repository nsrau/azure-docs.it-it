---
title: Informazioni di riferimento su algoritmi e moduli
description: Informazioni sui moduli disponibili nella finestra di progettazione di Azure Machine Learning (anteprima)Learn about the modules available in Azure Machine Learning designer (preview)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 02/22/2020
ms.openlocfilehash: 7fcfc7c15f94d7b569629e53534d731126172260
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399052"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer-preview"></a>Riferimento & modulo per la finestra di progettazione di Azure Machine Learning (anteprima)Algorithm to module reference for Azure Machine Learning designer (preview)

Questo contenuto di riferimento fornisce il background tecnico in ognuno degli algoritmi e dei moduli di Machine Learning disponibili nella finestra di progettazione di Azure Machine Learning (anteprima).

Ogni modulo rappresenta un set di codice che può essere eseguito in modo indipendente ed eseguire un'attività di apprendimento automatico, dati gli input necessari. Un modulo può contenere un particolare algoritmo o eseguire un'attività di particolare rilevanza nell'apprendimento automatico, ad esempio la sostituzione di valori mancanti o l'analisi statistica.

Per informazioni sulla scelta degli algoritmi, vedere 
* [Come selezionare gli algoritmi](../how-to-select-algorithms.md)
* [Azure Machine Learning Algorithm Cheat Sheet](../algorithm-cheat-sheet.md)

> [!TIP]
> In qualsiasi pipeline nella finestra di progettazione, è possibile ottenere informazioni su un modulo specifico. Selezionare il modulo e quindi fare clic sul collegamento **more help** (altre informazioni) nel riquadro **Quick Help** (Guida rapida).

## <a name="data-preparation-modules"></a>Moduli di preparazione dei dati


| Funzionalità | Descrizione | Modulo |
| --- |--- | --- |
| Input e output dei dati | Spostare i dati dalle origini cloud nella pipeline. Scrivere i risultati o i dati intermedi in Archiviazione di Azure, un database SQL o Hive, durante l'esecuzione di una pipeline o usare l'archiviazione cloud per scambiare dati tra pipeline.  | [Immettere i dati manualmente](enter-data-manually.md) <br/> [Esportazione dei dati](export-data.md) <br/> [Importazione dei dati](import-data.md) |
| Trasformazione dei dati | Operazioni su dati specifici dell'apprendimento automatico, ad esempio la normalizzazione o il binning dei dati, la riduzione della dimensionalità e la conversione dei dati tra vari formati di file.| [Aggiungere colonne](add-columns.md) <br/> [Aggiungi righe](add-rows.md) <br/> [Applicare operazioni matematiche](apply-math-operation.md) <br/> [Applicare la trasformazione SQL](apply-sql-transformation.md) <br/> [Pulire i dati mancanti](clean-missing-data.md) <br/> [Ritagliare valori](clip-values.md) <br/> [Eseguire la conversione in CSV](convert-to-csv.md) <br/> [Eseguire la conversione in set di dati](convert-to-dataset.md) <br/> [Eseguire la conversione in valori di indicatori](convert-to-indicator-values.md) <br/> [Modificare i metadati](edit-metadata.md) <br/> [Unire dati](join-data.md) <br/> [Normalizzare i dati](normalize-data.md) <br/> [Creare partizioni ed eseguire campionamenti](partition-and-sample.md)  <br/> [Rimuovere righe duplicate](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Select Columns Transform](select-columns-transform.md) <br/> [Select Columns in Dataset](select-columns-in-dataset.md) <br/> [Dividere dati](split-data.md) |
| Selezione caratteristiche | Selezionare un sottoinsieme di funzioni utili e pertinenti da utilizzare nella creazione di un modello analitico. | [Filter Based Feature Selection](filter-based-feature-selection.md) <br/> [Permutation Feature Importance](permutation-feature-importance.md) |
| Funzioni di statistiche | Fornire un'ampia gamma di metodi statistici relativi all'analisi scientifica dei dati. | [Riepilogare i dati](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Algoritmi di apprendimento automatico

| Funzionalità | Descrizione | Modulo |
| --- |--- | --- |
| Regressione | Prevedere un valore. | [Regressione albero delle decisioni incrementato](boosted-decision-tree-regression.md) <br/> [Regressione foresta delle decisioni](decision-forest-regression.md) <br/> [Regressione lineare](linear-regression.md)  <br/> [Regressione rete neurale](neural-network-regression.md)  <br/> |
| Clustering | Raggruppare i dati.| [Clustering K-means](k-means-clustering.md)
| Classificazione | Prevedi una lezione.  Scegliere tra algoritmi binari (a due classi) o multiclasse.| [Albero delle decisioni incrementato a più classi](multiclass-boosted-decision-tree.md) <br/> [Foresta delle decisioni multiclasse](multiclass-decision-forest.md) <br/> [Regressione logistica multiclasse](multiclass-logistic-regression.md)  <br/> [Rete neurale multiclasse](multiclass-neural-network.md) <br/> [Uno contro tutti i multiclassi](one-vs-all-multiclass.md) <br/> [Percettrone medio a due classi](two-class-averaged-perceptron.md) <br/>  [Albero delle decisioni incrementato a due classi](two-class-boosted-decision-tree.md)  <br/> [Foresta delle decisioni a due classi](two-class-decision-forest.md) <br/>  [Regressione logistica a due classi](two-class-logistic-regression.md) <br/> [Rete neurale a due classi](two-class-neural-network.md) <br/> [Two-Class Support Vector Machine](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Moduli per la creazione e la valutazione di modelli

| Funzionalità | Descrizione | Modulo |
| --- |--- | --- |
| Training dei modelli | Eseguire i dati tramite l'algoritmo. |  [Eseguire il training del modello di clustering](train-clustering-model.md) <br/> [Modello di treno](train-model.md)  <br/> [Tune Model Hyperparameters](tune-model-hyperparameters.md) |
| Punteggio e valutazione del modello | Misurare l'accuratezza del modello sottoposto a training. | [Applicare la trasformazione](apply-transformation.md) <br/> [Assegnazione di dati ai clusterAssign Data to Clusters](assign-data-to-clusters.md) <br/> [Cross Validate Model](cross-validate-model.md) <br/> [Valutare il modello](evaluate-model.md) <br/> [Assegnare il punteggio al modello](score-model.md) |
| Linguaggio di programmazione di Python | Scrivere codice e incorporarlo in un modulo per integrare Python con la pipeline. | [Creare il modello Python](create-python-model.md) <br/> [Eseguire lo script Python](execute-python-script.md) |
| Linguaggio R | Scrivere codice e incorporarlo in un modulo per integrare R con la pipeline. | [Execute R Script](execute-r-script.md) |
| Text Analytics | Fornire strumenti di calcolo specializzati per l'utilizzo di testo strutturato e non strutturato. | [Extract N Gram Features from Text](extract-n-gram-features-from-text.md) <br/> [Feature Hashing](feature-hashing.md) <br/> [Preprocess Text](preprocess-text.md) <br/> [Allocazione latente del dirichlet](latent-dirichlet-allocation.md) |
| Recommendation | Creare modelli di raccomandazione. | [Evaluate Recommender](evaluate-recommender.md) <br/> [Score SVD Recommender](score-svd-recommender.md) <br/> [Train SVD Recommender](train-SVD-recommender.md) |
| Anomaly Detection | Creare modelli di rilevamento delle anomalie. | [Rilevamento anomalie basato su PCA](pca-based-anomaly-detection.md) <br/> [Train Anomaly Detection Model](train-anomaly-detection-model.md) |


## <a name="error-messages"></a>messaggi di errore

Informazioni sui messaggi di errore e sui codici di [eccezione](designer-error-codes.md) che possono verificarsi usando i moduli nella finestra di progettazione di Azure Machine Learning.Learn about the error messages and exception codes you might encounter using modules in Azure Machine Learning designer.

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Creare un modello nella finestra di progettazione per prevedere i prezzi automaticiTutorial: Build a model in designer to predict auto prices](../tutorial-designer-automobile-price-train-score.md)
