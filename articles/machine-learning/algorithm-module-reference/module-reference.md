---
title: Informazioni di riferimento su algoritmi e moduli
description: Informazioni sui moduli di Azure Machine Learning designer che è possibile usare per creare progetti di Machine Learning personalizzati.
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: 998f9ef7bddfd988dc0af8183fb9c000bf8c79e7
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421295"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer"></a>Guida di riferimento al modulo & dell'algoritmo per Azure Machine Learning Designer

Questo contenuto di riferimento fornisce lo sfondo tecnico su ognuno degli algoritmi e dei moduli di machine learning disponibili in Azure Machine Learning Designer.

Ogni modulo rappresenta un set di codice che può essere eseguito in modo indipendente e può eseguire un'attività di Machine Learning, a condizione che vengano specificati i necessari valori di input. Un modulo può contenere un particolare algoritmo o eseguire un'attività di particolare rilevanza nell'apprendimento automatico, ad esempio la sostituzione di valori mancanti o l'analisi statistica.

Per assistenza nella scelta degli algoritmi, vedere: 
* [Come selezionare gli algoritmi](../how-to-select-algorithms.md)
* [Scheda di riferimento rapido sugli algoritmi di Azure Machine Learning](../algorithm-cheat-sheet.md)

> [!TIP]
> In qualsiasi pipeline della finestra di progettazione è possibile ottenere informazioni su uno specifico modulo. Selezionare il collegamento **Altre informazioni** nella scheda del modulo quando si passa il mouse sul modulo nell'elenco dei moduli o nel riquadro destro del modulo.

## <a name="data-preparation-modules"></a>Moduli di preparazione dei dati


| Funzionalità | Descrizione | Modulo |
| --- |--- | --- |
| Dati di input e di output | Spostano i dati da origini cloud nella pipeline. Scrivere i risultati o i dati intermedi in archiviazione di Azure, nel database SQL o in hive, durante l'esecuzione di una pipeline o usare l'archiviazione cloud per scambiare dati tra pipeline.  | [Enter Data Manually](enter-data-manually.md) (Immissione manuale dei dati) <br/> [Esportazione dei dati](export-data.md) <br/> [Importazione dei dati](import-data.md) |
| Trasformazioni dei dati | Operazioni su dati specifici per l'apprendimento automatico, ad esempio la normalizzazione o il binning dei dati, la riduzione della dimensionalità e la conversione dei dati tra vari formati di file.| [Aggiungi colonne](add-columns.md) <br/> [Add Rows](add-rows.md) (Aggiungi righe) <br/> [Apply Math Operation](apply-math-operation.md) (Applica operazione matematica) <br/> [Apply SQL Transformation](apply-sql-transformation.md) (Applica trasformazione SQL) <br/> [Clean Missing Data](clean-missing-data.md) (Pulisci dati mancanti) <br/> [Clip Values](clip-values.md) (Ritaglia valori) <br/> [Convert to CSV](convert-to-csv.md) (Converti in CSV) <br/> [Convert to Dataset](convert-to-dataset.md) (Converti in set di dati) <br/> [Convert to Indicator Values](convert-to-indicator-values.md) (Converti in valori indicatore) <br/> [Edit Metadata](edit-metadata.md) (Modifica metadati) <br/> [Group Data into Bins](group-data-into-bins.md) (Raggruppa dati in contenitori) <br/> [Join Data](join-data.md) (Unisci dati) <br/> [Normalize Data](normalize-data.md) (Normalizza dati) <br/> [Partition and Sample](partition-and-sample.md) (Crea partizioni ed esegui campionamenti)  <br/> [Remove Duplicate Rows](remove-duplicate-rows.md) (Rimuovi righe duplicate) <br/> [SMOTE](smote.md) <br/> [Select Columns Transform](select-columns-transform.md) (Seleziona trasformazione colonne) <br/> [Select Columns in Dataset](select-columns-in-dataset.md) (Seleziona colonne nel set di dati) <br/> [Dividere dati](split-data.md) |
| Selezione caratteristiche | Selezionano un subset di funzionalità utili e pertinenti da usare per la creazione di un modello di analisi. | [Filter Based Feature Selection](filter-based-feature-selection.md) (Selezione caratteristiche basata su filtro) <br/> [Permutation Feature Importance](permutation-feature-importance.md) (Importanza caratteristica permutazione) |
| Funzioni di statistiche | Offrono un'ampia gamma di metodi statistici correlati al data science. | [Summarize Data](summarize-data.md) (Riepiloga dati)|

## <a name="machine-learning-algorithms"></a>Algoritmi di apprendimento automatico

| Funzionalità | Descrizione | Modulo |
| --- |--- | --- |
| Regressione | Prevede un valore. | [Boosted Decision Tree Regression](boosted-decision-tree-regression.md) (Regressione albero delle decisioni con boosting) <br/> [Decision Forest Regression](decision-forest-regression.md) (Regressione foresta delle decisioni) <br/> [Regressione quantile della foresta rapida](fast-forest-quantile-regression.md)  <br/> [Linear Regression](linear-regression.md) (Regressione lineare)  <br/> [Neural Network Regression](neural-network-regression.md) (Regressione rete neurale)  <br/> [Regressione di Poisson](poisson-regression.md)  <br/>|
| Clustering | Raggruppa i dati.| [Clustering K-Means](k-means-clustering.md)
| Classificazione | Prevedono una classe.  Scegliere tra algoritmi binari (a due classi) o multiclasse.| [Albero delle decisioni incrementato a più classi](multiclass-boosted-decision-tree.md) <br/> [Foresta delle decisioni multiclasse](multiclass-decision-forest.md) <br/> [Regressione logistica multiclasse](multiclass-logistic-regression.md)  <br/> [Rete neurale multiclasse](multiclass-neural-network.md) <br/> [One vs. All Multiclass](one-vs-all-multiclass.md) (Uno contro tutti multiclasse) <br/> [Percettrone medio a due classi](two-class-averaged-perceptron.md) <br/>  [Albero delle decisioni incrementato a due classi](two-class-boosted-decision-tree.md)  <br/> [Foresta delle decisioni a due classi](two-class-decision-forest.md) <br/>  [Regressione logistica a due classi](two-class-logistic-regression.md) <br/> [Rete neurale a due classi](two-class-neural-network.md) <br/> [Two Class Support Vector Machine](two-class-support-vector-machine.md) (Macchina a vettori di supporto a due classi) | 

## <a name="modules-for-building-and-evaluating-models"></a>Moduli per la creazione e la valutazione dei modelli

| Funzionalità | Descrizione | Modulo |
| --- |--- | --- |
| Training del modello | Eseguono i dati tramite l'algoritmo. |  [Train Clustering Model](train-clustering-model.md) (Training del modello di clustering) <br/> [Eseguire il training del modello](train-model.md) <br/> [Training del modello Pytorch](train-pytorch-model.md) <br/> [Tune Model Hyperparameters](tune-model-hyperparameters.md) (Ottimizza iperparametri del modello) |
| Valutazione e assegnazione di punteggi al modello | Misurano l'accuratezza del modello sottoposto a training. | [Applica trasformazione](apply-transformation.md) <br/> [Assign Data to Clusters](assign-data-to-clusters.md) (Assegna dati ai cluster) <br/> [Cross Validate Model](cross-validate-model.md) (Convalida incrociata modello) <br/> [Evaluate Model](evaluate-model.md) (Valuta modello) <br/> [Score Image Model](score-image-model.md) <br/> [Score Model](score-model.md) (Punteggio modello) |
| Linguaggio di Python | Scrivono codice e lo incorporano in un modulo per integrare Python con la pipeline. | [Create Python Model](create-python-model.md) (Crea modello Python) <br/> [Execute Python Script](execute-python-script.md) (Esegui script Python) |
| Linguaggio di R | Scrivono codice e lo incorporano in un modulo per integrare R con la pipeline. | [Execute R Script](execute-r-script.md) (Esegui script R) |
| Text Analytics | Forniscono strumenti di calcolo specializzati per usare testo strutturato e non strutturato. |  [Convert Word to Vector](convert-word-to-vector.md) (Converti parola in vettore) <br/> [Extract N Gram Features from Text](extract-n-gram-features-from-text.md) (Estrai caratteristiche n-gramma da testo) <br/> [Hashing di funzioni](feature-hashing.md) <br/> [Preprocess Text](preprocess-text.md) (Preelabora il testo) <br/> [Latent Dirichlet Allocation](latent-dirichlet-allocation.md) (Allocazione Dirichlet latente) <br/> [Assegnare punteggi al modello Vowpal Wabbit](score-vowpal-wabbit-model.md) <br/> [Eseguire il training del modello Vowpal Wabbit](train-vowpal-wabbit-model.md)|
| Visione artificiale | Moduli correlati alla pre-elaborazione dei dati di immagini e al riconoscimento delle immagini. |  [Apply Image Transformation](apply-image-transformation.md) <br/> [Convert to Image Directory](convert-to-image-directory.md) <br/> [Init Image Transformation](init-image-transformation.md) <br/> [Split Image Directory](split-image-directory.md) <br/> [DenseNet](densenet.md) <br/> [ResNet](resnet.md) |
| Recommendation | Creano modelli di raccomandazione. | [Evaluate Recommender](evaluate-recommender.md) (Valuta modelo di raccomandazione) <br/> [Score SVD Recommender](score-svd-recommender.md) (Punteggio modulo di raccomandazione SVD) <br/> [Score Wide and Deep Recommender](score-wide-and-deep-recommender.md)<br/> [Train SVD Recommender](train-SVD-recommender.md) (Training modulo di raccomandazione SVD) <br/> [Train Wide and Deep Recommender](train-wide-and-deep-recommender.md)|
| Anomaly Detection | Creano modelli di rilevamento delle anomalie. | [PCA-Based Anomaly Detection](pca-based-anomaly-detection.md) (Rilevamento anomalie basato su PCA) <br/> [Train Anomaly Detection Model](train-anomaly-detection-model.md) (Training modello di rilevamento anomalie) |


## <a name="web-service"></a>Servizio Web

Vedere le informazioni sui [moduli del servizio Web](web-service-input-output.md) necessari per l'inferenza in tempo reale nella finestra di progettazione di Azure Machine Learning.

## <a name="error-messages"></a>messaggi di errore

Vedere le informazioni su [messaggi di errore e codici di eccezione](designer-error-codes.md) che possono verificarsi usando moduli della finestra di progettazione di Azure Machine Learning.

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Creare un modello nella finestra di progettazione per la previsione dei prezzi delle automobili](../tutorial-designer-automobile-price-train-score.md)
