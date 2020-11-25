---
title: Eseguire il training dei modelli di Machine Learning con Apache Spark
description: Usare Apache Spark in Azure sinapsi Analytics per eseguire il training di modelli di Machine Learning
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 09/13/2020
ms.openlocfilehash: 8250f7154cfb2739d065980284da67a200680d89
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919724"
---
# <a name="train-machine-learning-models"></a>Eseguire il training di modelli di Machine Learning
Apache Spark in Azure sinapsi Analytics consente l'apprendimento automatico con Big Data, offrendo la possibilità di ottenere informazioni preziose da grandi quantità di dati strutturati, non strutturati e in rapida evoluzione. Sono disponibili diverse opzioni per il training di modelli di Machine Learning con Azure Spark in Azure sinapsi Analytics: Apache Spark MLlib, Azure Machine Learning e diverse altre librerie open source. 

## <a name="apache-sparkml-and-mllib"></a>Apache SparkML e MLlib
Apache Spark in Azure Synapse Analytics è una delle implementazioni Microsoft di Apache Spark nel cloud. Fornisce un Framework di elaborazione parallela dei dati unificato e open source che supporta l'elaborazione in memoria per migliorare Big Data analisi. Il motore di elaborazione Spark è costruito per la velocità, la semplicità d'uso e le analisi sofisticate. Le funzionalità di calcolo distribuite in memoria rendono Spark uno strumento valido per l'esecuzione di algoritmi iterativi utilizzati in calcoli grafici e di Machine Learning. 

Sono disponibili due librerie di apprendimento automatico scalabili che integrano funzionalità di modellazione algoritmica nell'ambiente distribuito: MLlib e SparkML. MLlib contiene l'API originale basata su RDD. SparkML è un pacchetto più recente che contiene un'API di livello superiore basata su Spark DataFrames per la costruzione di pipeline di apprendimento automatico. SparkML non supporta ancora tutte le funzionalità di MLlib, ma sostituisce MLlib come libreria standard di Machine Learning di Spark.

> [!NOTE]
> 
> Per altre informazioni sulla creazione di un modello SparkML, seguire questa [esercitazione](../spark/apache-spark-azure-machine-learning-tutorial.md).

## <a name="popular-libraries"></a>Librerie più diffuse
Ogni pool di Apache Spark in Azure sinapsi Analytics è dotato di un set di librerie di Machine Learning pre-caricate e diffuse. Queste librerie forniscono codice riutilizzabile che può essere utile includere nei programmi o nei progetti. Alcune delle librerie di Machine Learning pertinenti incluse per impostazione predefinita includono:
- [Scikit-learn](https://scikit-learn.org/stable/index.html) è una delle librerie di apprendimento automatico a nodo singolo più diffuse per gli algoritmi di ml classici. Scikit-learn supporta la maggior parte degli algoritmi di apprendimento supervisionati e non supervisionati e può essere usato anche per data mining e analisi dei dati.
  
- [XGBoost](https://xgboost.readthedocs.io/en/latest/) è una libreria di apprendimento automatico diffusa che contiene algoritmi ottimizzati per il training di alberi delle decisioni e foreste casuali. 
  
- [PyTorch](https://pytorch.org/)  &  [Tensorflow](https://www.tensorflow.org/) sono librerie di apprendimento avanzato Python avanzate. All'interno di un pool di Apache Spark in Azure sinapsi Analytics, è possibile usare queste librerie per compilare modelli a computer singolo impostando il numero di esecutori nel pool su zero. Anche se Apache Spark non funziona in questa configurazione, si tratta di un modo semplice e conveniente per creare modelli a computer singolo.

È possibile ottenere altre informazioni sulle librerie disponibili e le versioni correlate visualizzando il [runtime di Azure sinapsi Analytics](../spark/apache-spark-version-support.md)pubblicato.

## <a name="mmlspark"></a>MMLSpark
La libreria di apprendimento automatico Microsoft per Apache Spark è [MMLSpark](https://github.com/Azure/mmlspark). Questa libreria è progettata per migliorare la produttività dei data scientist in Spark, aumentare la frequenza di sperimentazione e sfruttare le tecniche di apprendimento automatico all'avanguardia, tra cui l'apprendimento avanzato, su set di dati di grandi dimensioni. 

MMLSpark offre un livello superiore rispetto alle API di basso livello di SparkML per la creazione di modelli di apprendimento automatico scalabili, tra cui l'indicizzazione di stringhe, l'applicazione forzata ai dati di un layout previsto dagli algoritmi di apprendimento automatico e l'assemblaggio di vettori di funzionalità. La libreria MMLSpark semplifica queste e altre attività comuni per la creazione di modelli in PySpark.

## <a name="automated-ml-in-azure-machine-learning"></a>ML automatizzato in Azure Machine Learning 
Azure Machine Learning offre un ambiente basato sul cloud che consente di sottoporre a training, distribuire, automatizzare, gestire e monitorare i modelli di Machine Learning. Automated Machine Learning (AutoML) in Azure Machine Learning accetta le impostazioni di configurazione e i dati di training e scorre automaticamente le combinazioni di diversi metodi di normalizzazione/standardizzazione delle funzionalità, modelli e impostazioni iperparametri per arrivare al modello migliore. 

Quando si usa AutoML in Azure sinapsi Analytics, è possibile sfruttare l'integrazione completa tra i diversi servizi per semplificare l'autenticazione & il training del modello. 

> [!NOTE]
> 
> Per altre informazioni sulla creazione di un esperimento di Azure Machine Learning AutoML, seguire questa [esercitazione](./spark/../apache-spark-azure-machine-learning-tutorial.md).

## <a name="azure-cognitive-services"></a>Servizi cognitivi di Azure
[Servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/what-are-cognitive-services) offre funzionalità di machine learning per risolvere i problemi generali, ad esempio l'analisi del testo per i sentimenti emotivi o l'analisi delle immagini per riconoscere oggetti o visi. Per usare questi servizi, non è necessario avere conoscenze specifiche di Machine Learning o data science. Un servizio cognitivo fornisce parte o tutti i componenti in una soluzione di apprendimento automatico: dati, algoritmo e modello sottoposto a training. Questi servizi sono destinati a richiedere una conoscenza generale dei dati senza che sia necessaria un'esperienza di apprendimento automatico o data science. È possibile sfruttare automaticamente questi servizi cognitivi già sottoposti a training in Azure sinapsi Analytics.

## <a name="next-steps"></a>Passaggi successivi
Questo articolo offre una panoramica delle diverse opzioni per il training dei modelli di machine learning all'interno di Apache Spark pool in Azure sinapsi Analytics. Per altre informazioni sul training del modello, vedere l'esercitazione seguente:

- Eseguire esperimenti di Machine Learning automatici usando Azure Machine Learning e Azure sinapsi Analytics: [esercitazione su AutoML](./spark/../apache-spark-autoscale.md) 
- Eseguire esperimenti di SparkML: [esercitazione su Apache SparkML](../spark/apache-spark-azure-machine-learning-tutorial.md)
- Visualizzare le librerie predefinite: [runtime di Azure sinapsi Analytics](../spark/apache-spark-version-support.md)