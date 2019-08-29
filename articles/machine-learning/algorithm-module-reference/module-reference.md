---
title: Informazioni di riferimento su algoritmi e moduli
titleSuffix: Azure Machine Learning service
description: Informazioni sui moduli disponibili nell'interfaccia visiva di Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 065931140894478caee9d4ea49dac49f2415716b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128646"
---
# <a name="algorithm--module-reference-overview"></a>Panoramica di riferimento al modulo & di algoritmi

Questo contenuto di riferimento fornisce lo sfondo tecnico su ognuno degli algoritmi e dei moduli di machine learning disponibili nell'interfaccia visiva (anteprima) del servizio Azure Machine Learning.

Ogni modulo rappresenta un set di codice che può essere eseguito in modo indipendente ed eseguire un'attività di Machine Learning, in base agli input necessari. Un modulo potrebbe contenere un particolare algoritmo o eseguire un'attività importante nell'apprendimento automatico, ad esempio la sostituzione di valori mancanti o l'analisi statistica.

> [!TIP]
> In qualsiasi esperimento nell'interfaccia visiva, è possibile ottenere informazioni su un modulo specifico. Selezionare il modulo, quindi selezionare il collegamento **altre** informazioni nel riquadro **Guida rapida** .

## <a name="modules"></a>Moduli

I moduli sono organizzati per funzionalità:

| Funzionalità | Descrizione | Modulo |
| --- |--- | ---- |
| Conversioni dei formati di dati | Convertire i dati tra i diversi formati di file usati in Machine Learning, | [Converti in CSV](convert-to-csv.md) |
| Input e output dei dati | Spostare i dati dalle origini cloud nell'esperimento. Scrivere i risultati o i dati intermedi in archiviazione di Azure, un database SQL o hive, durante l'esecuzione di un esperimento o usare l'archiviazione cloud per scambiare dati tra gli esperimenti.  | [Importazione dei dati](import-data.md)<br/>[Esportazione dei dati](export-data.md)<br/>[Immissione manuale dei dati](enter-data-manually.md) |
| Trasformazione dati | Operazioni sui dati che sono univoche per Machine Learning, ad esempio la normalizzazione o la suddivisione in contenitori dei dati, la selezione delle funzioni e la riduzione della dimensionalità.| [Selezionare le colonne nel set di dati](select-columns-in-dataset.md) <br/> [Modificare i metadati](edit-metadata.md) <br/> [Pulisci i dati mancanti](clean-missing-data.md) <br/> [Aggiungi colonne](add-columns.md) <br/> [Aggiungi righe](add-rows.md) <br/> [Rimuovi righe duplicate](remove-duplicate-rows.md) <br/> [Unisci dati](join-data.md) <br/> [Suddividere i dati](split-data.md) <br/> [Normalizza i dati](normalize-data.md) <br/> [Partition ed Sample](partition-and-sample.md) |
| Moduli Python e R | Scrivi codice e incorporalo in un modulo per integrare Python e R con l'esperimento. | [Esegui script Python](execute-python-script.md)   <br/> [Creare un modello Python](create-python-model.md) <br/> [Esegui script R](execute-r-script.md)
|  | **Algoritmi di Machine Learning**: | |
| classificazione | Stimare una classe.  Scegliere tra algoritmi binari (a due classi) o multiclasse.| [Foresta delle decisioni multiclasse](multiclass-decision-forest.md) <br/> [Albero delle decisioni con boosting multiclasse](multiclass-boosted-decision-tree.md) <br/> [Regressione logistica multiclasse](multiclass-logistic-regression.md)  <br/> [Rete neurale multiclasse](multiclass-neural-network.md)  <br/>  [Regressione logistica a due classi](two-class-logistic-regression.md)  <br/>[Perceptron media a due classi](two-class-averaged-perceptron.md) <br/> [Albero delle&nbsp;decisioni&nbsp;conboostingadueclassi&nbsp;](two-class-boosted-decision-tree.md)  <br/> [Foresta delle decisioni a due classi](two-class-decision-forest.md)  <br/> [Rete neurale a due classi](two-class-neural-network.md)  <br/> [Macchina&#8209;a&nbsp;vettori&nbsp;disupportoperdueclassi&nbsp;](two-class-support-vector-machine.md) 
| Clustering | Raggruppare i dati.| [Clustering K-means](k-means-clustering.md)
| Regressione | Stimare un valore. | [Regressione lineare](linear-regression.md)  <br/> [Regressione di rete neurale](neural-network-regression.md)  <br/> [Regressione della foresta delle decisioni](decision-forest-regression.md)  <br/> [Regressione dell'&nbsp;albero&nbsp;delle&nbsp;decisioni con boosting](boosted-decision-tree-regression.md)
|  | **Compilare e valutare i modelli**: | |
| Eseguire il training   | Eseguire i dati tramite l'algoritmo. | [Train Model](train-model.md)  <br/> [Training del modello di clustering](train-clustering-model.md)    |
| Valutare il modello | Misurare l'accuratezza del modello sottoposto a training. |  [Valuta modello](evaluate-model.md)
| Punteggio | Ottenere le stime dal modello appena sottoposto a training. | [Applica trasformazione](apply-transformation.md)<br/>[Assegnare&nbsp;i&nbsp;datiai&nbsp;cluster](assign-data-to-clusters.md) <br/>[Modello di Punteggio](score-model.md)

## <a name="error-messages"></a>messaggi di errore

Informazioni sui [messaggi di errore e i codici di eccezione](machine-learning-module-error-codes.md) che possono verificarsi usando i moduli nell'interfaccia visiva del servizio Azure Machine Learning.
