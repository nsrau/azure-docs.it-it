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
ms.openlocfilehash: 6602eb4bacdc3b6382c1b6873a465cdfc0632693
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029341"
---
# <a name="algorithm--module-reference-overview"></a>Cenni preliminari di riferimento su algoritmi e moduli

Questo contenuto di riferimento fornisce le informazioni tecniche generali in ogni computer i moduli disponibili nell'interfaccia di visual (anteprima) del servizio Azure Machine Learning e algoritmi di apprendimento. 

Ogni modulo rappresenta un set di codice che può eseguire in modo indipendente ed eseguire una di apprendimento automatico attività, dati gli input necessari. Un modulo potrebbe contenere un algoritmo specifico, o eseguire un'attività che è importante per machine learning, come sostituzione di valori mancanti o analisi statistiche. 

> [!TIP]
> In qualsiasi esperimento dell'interfaccia visiva grafica, è possibile ottenere informazioni su un modulo specifico. Selezionare il modulo, quindi selezionare il **ulteriore assistenza** clic sul collegamento nella **Guida rapida** riquadro.

I moduli sono organizzati per funzionalità:

**Conversioni dei formati di dati**

  + [Converti in CSV ](convert-to-csv.md)

**I dati di input e output moduli** svolgere il lavoro dello spostamento dei dati da origini cloud nell'esperimento. È possibile scrivere i dati intermedi o risultati in archiviazione di Azure, un database SQL o Hive, durante l'esecuzione di un esperimento o usare l'archiviazione cloud di scambiare dati tra gli esperimenti.  

  + [Importazione dei dati](import-data.md)

  + [Esportazione dei dati](export-data.md)

  + [Immettere manualmente i dati](enter-data-manually.md)


**I moduli di trasformazione dati** supportano le operazioni sui dati che sono univoci a machine learning, ad esempio normalizzazione o il binning dei dati, selezione delle funzioni e riduzione della dimensionalità.

  + [Selezionare le colonne nel set di dati](select-columns-in-dataset.md)

  + [Modificare i metadati](edit-metadata.md)

  + [Pulisci dati mancanti](clean-missing-data.md)

  + [Aggiunta di colonne](add-columns.md)

  + [Aggiungi righe](add-rows.md)

  + [Rimuovere le righe Duplicate](remove-duplicate-rows.md)

  + [Divisione dei dati](split-data.md)

  + [Normalizzare i dati](normalize-data.md)

  + [Partition and Sample](partition-and-sample.md)


**Gli algoritmi di apprendimento** , ad esempio clustering, macchina a vettori di supporto o reti neurali, sono disponibili all'interno dei singoli moduli che consentono di personalizzare l'attività di apprendimento automatico con i parametri appropriati.  
  + [Score Model](score-model.md)

  + [Assegna i dati ai cluster ](assign-data-to-clusters.md)

  + [Train Model](train-model.md)

  + [Train Clustering Model](train-clustering-model.md)

  + [Valutazione del modello](evaluate-model.md)

  + [Applicare la trasformazione](apply-transformation.md)

  + [Regressione lineare](linear-regression.md)

  + [Regressione rete neurale](neural-network-regression.md)

  + [Regressione tramite foresta delle decisioni](decision-forest-regression.md)

  + [Regressione di albero delle decisioni con Boosting](boosted-decision-tree-regression.md)

  + [Albero delle decisioni con Boosting a due classi](two-class-boosted-decision-tree.md)

  + [Two-Class Logistic Regression](two-class-logistic-regression.md)

  + [Regressione logistica multiclasse](multiclass-logistic-regression.md)

  + [Multiclass Neural Network](multiclass-neural-network.md)

  + [Multiclass Decision Forest](multiclass-decision-forest.md)

  + [Two-Class Averaged Perceptron](two-class-averaged-perceptron.md)

  + [Two-Class Decision Forest](two-class-decision-forest.md)

  + [Two-Class Neural Network](two-class-neural-network.md)

  + [Two-Class Support Vector Machine](two-class-support-vector-machine.md)
  
  + [Clustering K-Means](k-means-clustering.md)


**Modulo Python** rende più semplice eseguire una funzione personalizzata. Si scrive il codice e incorporarlo in un modulo, per l'integrazione di Python con un servizio di esperimento.
  + [Esegui Script Python](execute-python-script.md)

  + [Creare modelli di Python](create-python-model.md)


