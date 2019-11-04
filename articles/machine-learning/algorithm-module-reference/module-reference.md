---
title: Informazioni di riferimento su algoritmi e moduli
description: Informazioni sui moduli disponibili in Azure Machine Learning Designer (anteprima)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 2da567a8f5ebd0161e41bf5a0aeb83b0d3b4ba4c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466078"
---
# <a name="algorithm--module-reference-overview"></a>Panoramica di riferimento al modulo & di algoritmi

Questo contenuto di riferimento fornisce lo sfondo tecnico su ognuno degli algoritmi e dei moduli di machine learning disponibili in Azure Machine Learning Designer (anteprima).

Ogni modulo rappresenta un set di codice che può essere eseguito in modo indipendente ed eseguire un'attività di Machine Learning, in base agli input necessari. Un modulo può contenere un particolare algoritmo o eseguire un'attività di particolare rilevanza nell'apprendimento automatico, ad esempio la sostituzione di valori mancanti o l'analisi statistica.

> [!TIP]
> In qualsiasi pipeline della finestra di progettazione è possibile ottenere informazioni su un modulo specifico. Selezionare il modulo e quindi fare clic sul collegamento **more help** (altre informazioni) nel riquadro **Quick Help** (Guida rapida).

## <a name="modules"></a>Moduli

I moduli sono organizzati per funzionalità:

| Funzionalità | Description | Modulo |
| --- |--- | ---- |
| Input e output dei dati | Spostare i dati dalle origini cloud alla pipeline. Scrivere i risultati o i dati intermedi in archiviazione di Azure, un database SQL o hive, durante l'esecuzione di una pipeline, oppure usare l'archiviazione cloud per scambiare dati tra pipeline.  | [Importazione dei dati](import-data.md) <br/> [Immissione manuale dei dati](enter-data-manually.md) <br/>[Esportazione dei dati](export-data.md) |
| Trasformazione dei dati | Operazioni sui dati che sono univoche per Machine Learning, ad esempio la normalizzazione o la suddivisione in contenitori dei dati, la riduzione della dimensionalità e la conversione dei dati tra diversi formati di file.| [Aggiungi colonne](add-columns.md) <br/> [Aggiungi righe](add-rows.md) <br/> [Pulisci i dati mancanti](clean-missing-data.md) <br/> [Converti in CSV](convert-to-csv.md) <br/> [Converti in DataSet](convert-to-dataset.md) <br/> [Modificare i metadati](edit-metadata.md) <br/> [Unisci dati](join-data.md) <br/> [Normalizza i dati](normalize-data.md) <br/> [Partition ed Sample](partition-and-sample.md) <br/> [Rimuovi righe duplicate](remove-duplicate-rows.md) <br/> [Selezione trasformazione colonne](select-columns-transform.md) <br/> [Selezionare le colonne nel set di dati](select-columns-in-dataset.md) <br/> [COLPÌ](smote.md) <br/> [Suddividere i dati](split-data.md) |
| Selezione delle funzioni | Consente di selezionare un subset di funzionalità utili rilevanti da utilizzare per la compilazione di un modello analitico. | [Selezione delle funzioni basata su filtro](filter-based-feature-selection.md) <br/> [Importanza della funzionalità di permutazione](permutation-feature-importance.md) |
| Moduli Python e R | Scrivere codice e incorporarlo in un modulo per integrare Python e R con la pipeline. | [Creare un modello Python](create-python-model.md) <br/> [Esegui script Python](execute-python-script.md)   <br/>  [Esegui script R](execute-r-script.md)
| Analisi del testo | Fornire strumenti di calcolo specializzati per lavorare con testo strutturato e non strutturato. | [Estrai le funzionalità di N grammi dal testo](extract-n-gram-features-from-text.md) <br/> [Hashing delle funzionalità](feature-hashing.md) <br/> [Pre-elabora testo](preprocess-text.md) |
|  | **Algoritmi di Machine Learning**: | |
| Classificazione | Stimare una classe.  Scegliere tra algoritmi binari (a due classi) o multiclasse.| [Foresta delle decisioni multiclasse](multiclass-decision-forest.md) <br/> [Albero delle decisioni con boosting multiclasse](multiclass-boosted-decision-tree.md) <br/> [Regressione logistica multiclasse](multiclass-logistic-regression.md)  <br/> [Rete neurale multiclasse](multiclass-neural-network.md) <br/> [Una o tutte le multiclasse](one-vs-all-multiclass.md) <br/>  [Regressione logistica a due classi](two-class-logistic-regression.md)  <br/>[Perceptron media a due classi](two-class-averaged-perceptron.md) <br/> [Albero delle decisioni con boosting a due classi](two-class-boosted-decision-tree.md)  <br/> [Foresta delle decisioni a due classi](two-class-decision-forest.md)  <br/> [Rete neurale a due classi](two-class-neural-network.md) <br/> [Macchina a vettori di supporto per due classi](two-class-support-vector-machine.md) | 
| Clustering | Raggruppare i dati.| [Clustering K-means](k-means-clustering.md)
| regressione | Stimare un valore. | [Regressione dell'albero delle decisioni con boosting](boosted-decision-tree-regression.md) <br/> [Regressione della foresta delle decisioni](decision-forest-regression.md) <br/> [Regressione lineare](linear-regression.md)  <br/> [Regressione di rete neurale](neural-network-regression.md)  <br/> |
| Recommender | Compilare i modelli di raccomandazione. | [Valuta la raccomandazione](evaluate-recommender.md) <br/> [Punteggio SVD Recommender](score-svd-recommender.md) <br/> [Train SVD Recommender](train-SVD-recommender.md) |
|  | **Compilare e valutare i modelli**: | |
| Fai il training   | Eseguire i dati tramite l'algoritmo. | [Modello Cross Validate](cross-validate-model.md) <br/> [Train Model](train-model.md)  <br/> [Training del modello di clustering](train-clustering-model.md) <br/>  [Ottimizzazione degli iperparametri del modello](tune-model-hyperparameters.md) |
| Valutare il modello | Misurare l'accuratezza del modello sottoposto a training. |  [Valuta modello](evaluate-model.md) |
| Punteggio | Ottenere le stime dal modello appena sottoposto a training. | [Applica trasformazione](apply-transformation.md)<br/>[Assegnare i dati ai cluster](assign-data-to-clusters.md) <br/>[Modello di Punteggio](score-model.md) |
| Funzioni di statistiche | Fornire un'ampia gamma di metodi numerici correlati a data science. | [Applica operazione matematica](apply-math-operation.md) <br/> [Riepiloga dati](summarize-data.md)|

## <a name="error-messages"></a>messaggi di errore

Informazioni sui [messaggi di errore e i codici di eccezione](machine-learning-module-error-codes.md) che possono verificarsi usando i moduli in Azure Machine Learning Designer.
