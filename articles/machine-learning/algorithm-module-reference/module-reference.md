---
title: Informazioni di riferimento su algoritmi e moduli
titleSuffix: Azure Machine Learning service
description: Informazioni sui moduli disponibili nell'interfaccia visual Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 8825f1dc3b66a5c4981ba25a90813aec63975b1f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145141"
---
# <a name="algorithm--module-reference-overview"></a>Cenni preliminari di riferimento su algoritmi e moduli

Questo contenuto di riferimento fornisce le informazioni tecniche generali in ogni computer i moduli disponibili nell'interfaccia di visual (anteprima) del servizio Azure Machine Learning e algoritmi di apprendimento.

Ogni modulo rappresenta un set di codice che può eseguire in modo indipendente ed eseguire una di apprendimento automatico attività, dati gli input necessari. Un modulo potrebbe contenere un algoritmo specifico, o eseguire un'attività che è importante per machine learning, come sostituzione di valori mancanti o analisi statistiche.

> [!TIP]
> In qualsiasi esperimento dell'interfaccia visiva grafica, è possibile ottenere informazioni su un modulo specifico. Selezionare il modulo, quindi selezionare il **ulteriore assistenza** clic sul collegamento nella **Guida rapida** riquadro.

## <a name="modules"></a>Moduli

I moduli sono organizzati per funzionalità:

| Funzionalità | DESCRIZIONE | Modulo |
| --- |--- | ---- |
| Conversioni dei formati di dati | Convertire i dati tra diversi formati di file usati in machine learning, | [Converti in CSV](convert-to-csv.md) |
| Dati di input e output | Spostare dati da origini cloud nell'esperimento. Scrivere i dati intermedi o risultati in archiviazione di Azure, un database SQL o Hive, durante l'esecuzione di un esperimento o usare l'archiviazione cloud di scambiare dati tra gli esperimenti.  | [Importazione dei dati](import-data.md)<br/>[Esportazione dei dati](export-data.md)<br/>[Immettere manualmente i dati](enter-data-manually.md) |
| Trasformazione dei dati | Operazioni sui dati che sono univoci a machine learning, ad esempio normalizzazione o il binning dei dati, selezione delle funzioni e riduzione della dimensionalità.| [Selezionare le colonne nel set di dati](select-columns-in-dataset.md) <br/> [Modificare i metadati](edit-metadata.md) <br/> [Pulisci dati mancanti](clean-missing-data.md) <br/> [Aggiunta di colonne](add-columns.md) <br/> [Aggiungi righe](add-rows.md) <br/> [Rimuovere le righe Duplicate](remove-duplicate-rows.md) <br/> [Divisione dei dati](split-data.md) <br/> [Normalizzare i dati](normalize-data.md) <br/> [Partition and Sample](partition-and-sample.md) |
| Modulo Python | Scrivere il codice e incorporarlo in un modulo di integrazione di Python contestualmente all'esperimento. | [Esegui Script Python](execute-python-script.md)   <br/> [Creare modelli di Python](create-python-model.md)
|  | **Gli algoritmi di apprendimento**: | |
| classificazione | Prevedere una classe.  Scegli dal file binario (due classi) o algoritmi multiclasse.| [Multiclass Decision Forest](multiclass-decision-forest.md) <br/> [Regressione logistica multiclasse](multiclass-logistic-regression.md)  <br/> [Multiclass Neural Network](multiclass-neural-network.md)  <br/>  [Two-Class Logistic Regression](two-class-logistic-regression.md)  <br/>[Two-Class Averaged Perceptron](two-class-averaged-perceptron.md) <br/> [Two-Class&nbsp;Boosted&nbsp;decisione&nbsp;albero](two-class-boosted-decision-tree.md)  <br/> [Two-Class Decision Forest](two-class-decision-forest.md)  <br/> [Two-Class Neural Network](two-class-neural-network.md)  <br/> [Due&#8209;classe&nbsp;supporto&nbsp;vettore&nbsp;Machine](two-class-support-vector-machine.md) 
| Clustering | Raggruppare i dati.| [Clustering K-Means](k-means-clustering.md)
| Regressione | Stimare un valore. | [Regressione lineare](linear-regression.md)  <br/> [Regressione rete neurale](neural-network-regression.md)  <br/> [Regressione tramite foresta delle decisioni](decision-forest-regression.md)  <br/> [Boosted&nbsp;decisione&nbsp;albero&nbsp;regressione](boosted-decision-tree-regression.md)
|  | **Compilare e valutare i modelli**: | |
| Eseguire il training   | Dati tramite l'algoritmo di esecuzione. | [Train Model](train-model.md)  <br/> [Train Clustering Model](train-clustering-model.md)    |
| Valutazione del modello | Misurare l'accuratezza del modello con training. |  [Valutazione del modello](evaluate-model.md)
| Score | Ottenere le stime dal modello che appena sottoposto a training. | [Applicare la trasformazione](apply-transformation.md)<br/>[Assegnare&nbsp;Data&nbsp;a&nbsp;cluster](assign-data-to-clusters.md) <br/>[Score Model](score-model.md)

## <a name="error-messages"></a>messaggi di errore

Scopri le [messaggi di errore e i codici di eccezione](machine-learning-module-error-codes.md) potrebbero verificarsi usando i moduli dell'interfaccia visiva grafica del servizio di Azure Machine Learning.
