---
title: Machine Learning in Azure Synapse Analytics
description: Panoramica delle funzionalità di Machine Learning in Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 5e422b5292f77150e2755cb5963e2cd95d148654
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91939104"
---
# <a name="machine-learning-capabilities-in-azure-synapse-analytics-workspaces-preview"></a>Funzionalità di Machine Learning in Azure Synapse Analytics (aree di lavoro in anteprima)

[!INCLUDE [preview](../includes/note-preview.md)]

Azure Synapse Analytics offre diverse funzionalità di Machine Learning. Questo articolo presenta una panoramica dell'applicazione di funzionalità di Machine Learning nel contesto di Azure Synapse.

In questa panoramica vengono illustrate le diverse funzionalità di Synapse correlate al Machine Learning, dalla prospettiva di un processo di data science.

Probabilmente si ha già familiarità con l'aspetto di un tipico processo di data science. Si tratta di un processo noto, che viene adottato nella maggior parte dei progetti di Machine Learning.

A un livello generale, il processo include i passaggi seguenti:
* (Comprensione del business)
* Acquisizione e comprensione dei dati
* Modellazione
* Distribuzione di modelli e assegnazione di punteggi

Questo articolo descrive le funzionalità di Machine Learning di Azure Synapse in diversi motori di analisi, dal punto di vista di un processo di data science. Per ogni passaggio del processo di data science, è riportato un riepilogo delle funzionalità di Azure Synapse che possono essere utili.

## <a name="azure-synapse-machine-learning-capabilities"></a>Funzionalità di Machine Learning in Azure Synapse

### <a name="data-acquisition-and-understanding"></a>Acquisizione e comprensione dei dati

La maggior parte dei progetti di Machine Learning presuppone l'esecuzione di passaggi prestabiliti, uno dei quali consiste nell'accesso e nella comprensione dei dati.

#### <a name="data-source-and-pipelines"></a>Origine dati e pipeline

Grazie ad [Azure Data Factory](/azure/data-factory/introduction), un componente integrato in modo nativo di Azure Synapse, è disponibile un potente set di strumenti per l'inserimento dati e le pipeline di orchestrazione dei dati. Questo set consente di creare facilmente pipeline di dati per accedere ai dati e trasformarli in un formato che può essere utilizzato per il Machine Learning. In [questo articolo](/azure/data-factory/concepts-pipelines-activities?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) sono disponibili altre informazioni sulle pipeline di dati in Synapse. 

#### <a name="data-preparation-and-explorationvisualization"></a>Preparazione ed esplorazione/visualizzazione dei dati

Una parte importante del processo di Machine Learning consiste nel comprendere i dati tramite l'esplorazione e le visualizzazioni.

A seconda della posizione di archiviazione dei dati, Synapse offre un set di strumenti diversi per esplorarli e prepararli per l'analisi e il Machine Learning. Uno dei modi più rapidi per iniziare l'esplorazione dei dati consiste nell'uso di pool di Apache Spark o di pool serverless di Synapse SQL direttamente sui dati presenti nel data lake.

* [Apache Spark per Azure Synapse](../spark/apache-spark-overview.md) offre funzionalità per la trasformazione, la preparazione e l'esplorazione dei dati su larga scala. Per i pool di Spark sono disponibili strumenti quali PySpark/Python, Scala e .NET per l'elaborazione dei dati su larga scala. Grazie alle potenti librerie di visualizzazione, è possibile migliorare l'esperienza di esplorazione per una più facile comprensione dei dati. [Altre informazioni su come esplorare e visualizzare i dati in Synapse usando Spark](../get-started-analyze-spark.md).

* [I pool serverless di Synapse SQL](../sql/on-demand-workspace-overview.md) consentono di esplorare i dati usando TSQL direttamente sul data lake. Per questi pool sono inoltre disponibili alcune visualizzazioni predefinite in Synapse Studio. [Altre informazioni su come esplorare i dati con i pool serverless di Synapse SQL](../get-started-analyze-sql-on-demand.md).

### <a name="modeling"></a>Modellazione

In Azure Synapse è possibile eseguire il training di modelli di Machine Learning nei pool di Apache Spark con strumenti quali PySpark/Python, Scala o .NET.

#### <a name="train-models-on-spark-pools-with-mllib"></a>Eseguire il training dei modelli nei pool di Spark con MLlib

È possibile eseguire il training dei modelli di Machine Learning con il supporto di diversi algoritmi e librerie. [Spark MLlib](http://spark.apache.org/docs/latest/ml-guide.html) offre algoritmi di Machine Learning scalabili che consentono di risolvere i problemi di Machine Learning più classici. [Questa esercitazione](../spark/apache-spark-machine-learning-mllib-notebook.md) illustra come eseguire il training di un modello usando MLlib in Synapse.

Oltre a MLlib, per lo sviluppo di modelli è possibile usare le librerie più diffuse, ad esempio [Scikit Learn](https://scikit-learn.org/stable/). Per informazioni dettagliate su come installare le librerie nei pool di Spark in Synapse, vedere [Gestire le librerie per Apache Spark in Azure Synapse Analytics](../spark/apache-spark-azure-portal-add-libraries.md).

#### <a name="train-models-with-azure-machine-learning-automl"></a>Eseguire il training dei modelli con Azure Machine Learning AutoML

Un altro modo per eseguire il training dei modelli di Machine Learning, che non richiede particolari conoscenze delle tecnologie di Machine Learning, è offerto dall'uso di AutoML. [AutoML](/azure/machine-learning/concept-automated-ml) è una funzionalità che esegue automaticamente il training di un set di modelli di Machine Learning e consente all'utente di selezionare il modello migliore in base a metriche specifiche. Grazie a una perfetta integrazione con Azure Machine Learning da Azure Synapse Notebooks, gli utenti possono sfruttare facilmente AutoML in Synapse con l'autenticazione passthrough di Azure Active Directory.  Con questo tipo di autenticazione è sufficiente puntare all'area di lavoro di Azure Machine Learning senza dover immettere credenziali. In questa [esercitazione su AutoML](../spark/apache-spark-azure-machine-learning-tutorial.md) viene descritto come eseguire il training di modelli di Azure Machine Learning AutoML nei pool di Spark in Synapse.

### <a name="model-deployment-and-scoring"></a>Distribuzione di modelli e assegnazione di punteggi

I modelli che sono stati sottoposti a training con Azure Synapse o con altre tecnologie possono essere usati facilmente per l'assegnazione di punteggi. Attualmente in Synapse sono disponibili due modi per eseguire l'assegnazione di punteggi in batch.

* È possibile usare la [funzione TSQL PREDICT](../sql-data-warehouse/sql-data-warehouse-predict.md) nei pool di Synapse SQL per eseguire stime direttamente nella posizione in cui si trovano i dati. Questa funzione potente e scalabile consente di arricchire i dati senza spostarli dal data warehouse. [In Synapse Studio è stata introdotta una nuova esperienza guidata per i modelli di Machine Learning](https://aka.ms/synapse-ml-ui) in cui è possibile distribuire un modello ONNX dal registro dei modelli di Azure Machine Learning nei pool di Synapse SQL per l'assegnazione di punteggi in batch tramite la funzione PREDICT.

* Un'altra opzione per l'assegnazione di punteggi in batch ai modelli di Machine Learning in Azure Synapse consiste nell'uso di pool di Apache Spark per Azure Synapse. A seconda delle librerie usate per il training dei modelli, per l'assegnazione di punteggi in batch è possibile usare un'esperienza basata su codice.

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Azure Synapse Analytics](../get-started.md)
* [Creare un'area di lavoro](../get-started-create-workspace.md)
* [Avvio rapido: Creare un nuovo servizio collegato di Azure Machine Learning in Synapse](quickstart-integrate-azure-machine-learning.md)
* [Esercitazione: Procedura guidata di assegnazione di punteggi al modello di Machine Learning per pool SQL](tutorial-sql-pool-model-scoring-wizard.md)
