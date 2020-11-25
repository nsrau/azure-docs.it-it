---
title: Machine Learning con Apache Spark
description: Questo articolo fornisce una panoramica concettuale delle funzionalità di machine learning e data science disponibili tramite Apache Spark in Azure sinapsi Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 11/13/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: ced78955c71f37a5a6f5231f61e5327428834f00
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919528"
---
# <a name="machine-learning-with-apache-spark"></a>Machine Learning con Apache Spark

Apache Spark in Azure sinapsi Analytics consente l'apprendimento automatico con Big Data, offrendo la possibilità di ottenere informazioni preziose da grandi quantità di dati strutturati, non strutturati e in rapida evoluzione. 

Questa sezione include una panoramica ed esercitazioni per i flussi di lavoro di Machine Learning, tra cui analisi esplorativa dei dati, progettazione di funzionalità, training del modello, Punteggio del modello e distribuzione.  

## <a name="synapse-runtime"></a>Runtime sinapsi 
Il runtime di sinapsi è un ambiente curato ottimizzato per data science e machine learning. Il runtime di sinapsi offre una gamma di librerie open source comuni e compilazioni in Azure Machine Learning SDK per impostazione predefinita. Il runtime di sinapsi include anche molte librerie esterne, tra cui PyTorch, Scikit-learn, XGBoost e altro ancora.

Per altre informazioni sulle librerie disponibili e le versioni correlate, vedere il [runtime di Azure sinapsi Analytics](../spark/apache-spark-version-support.md)pubblicato.

## <a name="exploratory-data-analysis"></a>Analisi esplorativa dei dati
Quando si usa Apache Spark in Azure sinapsi Analytics, sono disponibili diverse opzioni predefinite che consentono di visualizzare i dati, tra cui le opzioni del grafico del notebook di sinapsi, l'accesso alle librerie open source più diffuse, ad esempio Seaborn e Matplotlib, nonché l'integrazione con sinapsi SQL e Power BI.

Per altre informazioni sulle opzioni di visualizzazione dei dati e di analisi dei dati, vedere l'articolo su come [visualizzare i dati usando i notebook di sinapsi di Azure](../spark/apache-spark-data-visualization.md).

## <a name="feature-engineering"></a>Progettazione delle funzioni
Per impostazione predefinita, il runtime di sinapsi include un set di librerie comunemente usate per la progettazione di funzionalità. Per i set di impostazioni di grandi dimensioni, è possibile usare Spark SQL, MLlib e Koala per la progettazione delle funzionalità. Per i set di impostazioni più piccoli, le librerie di terze parti come NumPy, Pandas e Scikit-learn forniscono anche metodi utili per questi scenari.

## <a name="train-models"></a>Eseguire il training dei modelli
Sono disponibili diverse opzioni per il training di modelli di Machine Learning con Azure Spark in Azure sinapsi Analytics: Apache Spark MLlib, Azure Machine Learning e diverse altre librerie open source. 

Per altre informazioni sulle funzionalità di Machine Learning, vedere l'articolo su come eseguire il [training dei modelli in Azure sinapsi Analytics](../spark/apache-spark-machine-learning-training.md).

### <a name="sparkml-and-mllib"></a>Spark e MLlib
Le funzionalità di calcolo distribuite in memoria rendono Spark uno strumento valido per l'esecuzione di algoritmi iterativi utilizzati in calcoli grafici e di Machine Learning. ```spark.ml``` fornisce un set uniforme di API di alto livello che consentono agli utenti di creare e ottimizzare le pipeline di machine learning. Per ulteriori informazioni su ```spark.ml``` , vedere la guida alla [programmazione di Apache Spark ml](https://spark.apache.org/docs/1.2.2/ml-guide.html).

### <a name="azure-machine-learning-automl"></a>Azure Machine Learning AutoML
[Azure Machine Learning AutoML](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) (Automatic Machine Learning) consente di automatizzare il processo di sviluppo di modelli di machine learning. Consente a data scientist, analisti e sviluppatori di creare modelli di Machine Learning con scalabilità, efficienza e produttività elevate, garantendo al tempo stesso la qualità del modello. I componenti per l'esecuzione di Azure Machine Learning SDK AutoML sono compilati direttamente nel runtime di sinapsi.

### <a name="open-source-libraries"></a>Librerie open source
Ogni pool di Apache Spark in Azure sinapsi Analytics è dotato di un set di librerie di Machine Learning pre-caricate e diffuse.  Alcune delle librerie di Machine Learning pertinenti incluse per impostazione predefinita includono:

- [Scikit-learn](https://scikit-learn.org/stable/index.html) è una delle librerie di apprendimento automatico a nodo singolo più diffuse per gli algoritmi di ml classici. Scikit-learn supporta la maggior parte degli algoritmi di apprendimento supervisionati e non supervisionati e può essere usato anche per data mining e analisi dei dati.
  
- [XGBoost](https://xgboost.readthedocs.io/en/latest/) è una libreria di apprendimento automatico diffusa che contiene algoritmi ottimizzati per il training di alberi delle decisioni e foreste casuali. 
  
- [PyTorch](https://pytorch.org/)  &  [Tensorflow](https://www.tensorflow.org/) sono librerie di apprendimento avanzato Python avanzate. All'interno di un pool di Apache Spark in Azure sinapsi Analytics, è possibile usare queste librerie per compilare modelli a computer singolo impostando il numero di esecutori nel pool su zero. Anche se Apache Spark non funziona in questa configurazione, si tratta di un modo semplice e conveniente per creare modelli a computer singolo.

## <a name="track-model-development"></a>Tenere traccia dello sviluppo di modelli
[MLFlow](https://www.mlflow.org/) è una libreria open source per la gestione del ciclo di vita degli esperimenti di machine learning. Il rilevamento MLFlow è un componente di MLflow che registra e tiene traccia delle metriche di esecuzione del training e degli elementi del modello. Per altre informazioni su come usare il rilevamento del MLFlow tramite analisi delle sinapsi di Azure e Azure Machine Learning, vedere questa esercitazione sull' [uso di MLFlow](https://docs.microsoft.com/azure/machine-learning/how-to-use-mlflow).

## <a name="model-scoring"></a>Punteggio del modello
Il Punteggio del modello o l'inferenza è la fase in cui viene usato un modello per eseguire stime. Per il punteggio del modello con SparkML o MLLib, è possibile usare i metodi Spark nativi per eseguire l'inferenza direttamente in un dataframe di Spark. Per altre librerie e tipi di modelli open source, è anche possibile creare una funzione definita dall'utente Spark per scalare l'inferenza su set di dati di grandi dimensioni. Per i set di impostazioni di dimensioni ridotte, è inoltre possibile utilizzare i metodi di inferenza del modello nativi forniti dalla libreria.

## <a name="register-and-serve-models"></a>Registrare e gestire i modelli
La registrazione di un modello consente di archiviare, eseguire la versione e tenere traccia dei metadati relativi ai modelli nell'area di lavoro. Al termine del training del modello, è possibile registrare il modello nel registro di sistema del [modello Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment#register-package-and-deploy-models-from-anywhere). Una volta registrati, i modelli ONNX possono essere usati anche per [arricchire i dati](../machine-learning/tutorial-sql-pool-model-scoring-wizard.md) archiviati in pool SQL dedicati.

## <a name="next-steps"></a>Passaggi successivi
Per iniziare a usare Machine Learning in Azure sinapsi Analytics, assicurarsi di consultare le esercitazioni seguenti:
- [Analizzare i dati con i notebook della sinapsi di Azure](../spark/apache-spark-data-visualization-tutorial.md)

- [Eseguire il training di un modello di apprendimento automatico con AutoML](../spark/apache-spark-azure-machine-learning-tutorial.md)

- [Eseguire il training di un modello di apprendimento automatico con Apache Spark MLlib](../spark/apache-spark-machine-learning-mllib-notebook.md)
  