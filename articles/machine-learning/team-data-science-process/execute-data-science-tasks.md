---
title: "Eseguire attività di data science - Azure Machine Learning | Microsoft Docs"
description: "Informazioni su come un esperto di dati può eseguire un progetto di data science in modo tracciabile, collaborativo e con controllo della versione."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: bradsev;
ms.openlocfilehash: 7f3bf3bb5743bfb64489188d1016fb18d4967f79
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Eseguire attività di data science: esplorazione, modellazione e distribuzione

Le tipiche attività di data science includono esplorazione, modellazione e distribuzione. Questo articolo illustra come usare le utilità **IDEAR (Interactive Data Exploration, Analysis, and Reporting)** e **AMAR (Automated Modeling and Reporting)** per completare alcune attività comuni di data science, come l'esplorazione interattiva dei dati, l'analisi dei dati, la creazione di report e la creazione di modelli. Vengono inoltre illustrate le opzioni per la distribuzione di un modello in un ambiente di produzione usando una vasta gamma di toolkit e piattaforme di dati, ad esempio:

- [Azure Machine Learning](../preview/index.yml)
- [SQL-Server con Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/r-services#in-database-analytics-with-sql-server)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## 1. <a name='DataQualityReportUtility-1'></a> Esplorazione 

Un data scientist può esplorare i dati e creare report in diversi modi, usando le librerie e i pacchetti disponibili per Python (ad esempio matplotlib) o con R (ad esempio ggplot o reticolo). I data scientist possono personalizzare il codice in base alle esigenze di esplorazione dei dati per scenari specifici. Le esigenze quando si gestiscono i dati strutturati sono diverse rispetto a quelle per i dati non strutturati, ad esempio testo o immagini. 

Prodotti come Azure Machine Learning Workbench consentono anche la [preparazione avanzata dei dati](../preview/tutorial-bikeshare-dataprep.md) per la gestione e l'esplorazione, inclusa la creazione di caratteristiche. L'utente deve scegliere gli strumenti, le librerie e i pacchetti più adatti alle proprie esigenze. 

Il risultato finale alla fine di questa fase è un report di esplorazione dei dati. Il report deve fornire una vista abbastanza completa dei dati da usare per la modellazione e una valutazione del fatto che i dati siano o meno adatti per procedere con il passaggio di modellazione. L'utilità TDSP (Team Data Science Process) descritta nelle sezioni seguenti per l'esplorazione semi-automatica, la modellazione e la creazione di report offre anche esplorazione dei dati standard e report di modellazione. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Esplorazione interattiva dei dati, analisi e creazione di report con l'utilità IDEAR

Questa utilità basata su R Markdown o su notebook Python fornisce uno strumento flessibile e interattivo per valutare ed esplorare i set di dati. Gli utenti possono generare velocemente dei report dal set di dati con codifica minima. Gli utenti possono fare clic su pulsanti per esportare i risultati dell'esplorazione tramite lo strumento interattivo in un report finale, che può essere recapitato ai client o usato per prendere decisioni in merito alle variabili da includere nel passaggio di modellazione successivo.

In questo momento, lo strumento funziona solo in frame di dati in memoria. È necessario un file YAML per specificare i parametri del set di dati da esplorare. Per altre informazioni, vedere [IDEAR in TDSP Data Science Utilities](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils) (IDEAR nelle utilità di data science TDSP).


## 2. <a name='ModelingUtility-2'></a> Modellazione

Ci sono numerosi toolkit e pacchetti per il training dei modelli in diversi linguaggi. I data scientist devono poter usare quelli che preferiscono, a condizione che vengano soddisfatte le considerazioni sulle prestazioni relative all'accuratezza e alla latenza per i casi d'uso aziendali e gli scenari di produzione pertinenti.

La sezione successiva mostra come usare un'utilità TDSP basata su R per la modellazione semi-automatica. Questa utilità AMAR può essere usata per generare rapidamente modelli di base e i parametri che devono essere ottimizzati per fornire un modello con prestazioni migliori.
La sezione seguente relativa alla gestione dei modelli mostra come usare un sistema per registrare e gestire più modelli.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Training dei modelli: modellazione e creazione di report tramite l'utilità AMAR

L'[utilità AMAR (Automated Modeling and Reporting)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) fornisce uno strumento semi-automatico personalizzabile per eseguire la creazione di modelli con ottimizzazione degli iperparametri e per confrontare l'accuratezza di tali modelli. 

L'utilità di creazione dei modelli è un file R Markdown che può essere eseguito per produrre un output HTML autonomo con un sommario che semplifica la navigazione nelle diverse sezioni. All'esecuzione del file markdown (unione), vengono eseguiti tre algoritmi: regressione regolarizzata tramite il pacchetto glmnet, foresta casuale tramite il pacchetto randomForest e alberi con aumento priorità tramite il pacchetto xgboost. Ciascuno di questi algoritmi produce un modello di esecuzione del training. Viene confrontata poi l'accuratezza di questi modelli e vengono riportati i relativi tracciati di importanza della funzionalità. Attualmente, sono disponibili due utilità: una è per un'attività di classificazione binaria e una è per un'attività di regressione. Le differenze principali tra di esse è il modo in cui i parametri di controllo e la metrica di accuratezza vengono specificati per queste attività di apprendimento. 

Viene usato un file YAML per specificare:

- l'input di dati (un'origine SQL o un file di dati R) 
- quale quantità di dati viene usata per il training e quale quantità per il test
- quali algoritmi eseguire 
- la scelta dei parametri di controllo per l'ottimizzazione del modello:
    - convalida incrociata 
    - bootstrap
    - riduzione della convalida incrociata
- l'iperparametro imposta ogni algoritmo. 

Il numero di algoritmi, il numero di riduzioni per l'ottimizzazione, gli iperparametri e il numero di set di iperparametri per l'organizzazione possono anche essere modificati nel file Yaml per eseguire rapidamente i modelli. Ad esempio, possono essere eseguiti con un numero inferiore di riduzioni CV, un numero inferiore di set di parametri. Se autorizzato, possono essere eseguiti anche in modo più completo con un numero maggiore di convalide incrociate o un numero maggiore di set di parametri.

Per altre informazioni, vedere [Automated Modeling and Reporting Utility in TDSP Data Science Utilities](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) (Modellazione automatizzata e utilità di creazione di report nelle utilità data science di analisi scientifica di TDSP).

### <a name="model-management"></a>Gestione di modelli
Dopo aver creato più modelli, è in genere necessario usare un sistema per registrarli e gestirli. Solitamente, sono necessari una combinazione di script o API e un database back-end o un sistema di controllo delle versioni. Ecco alcune opzioni che è possibile prendere in considerazione per queste attività di gestione:

1. [Azure Machine Learning - servizio Gestione modelli](../preview/index.yml)
2. [ModelDB del MIT](https://mitdbg.github.io/modeldb/) 
3. [SQL Server come sistema di gestione dei modelli](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## 3. <a name='Deployment-3'></a> Distribuzione

La distribuzione nell'ambiente di produzione consente di usare attivamente un modello in un'azienda. Le stime effettuate da un modello distribuito possono essere usate per prendere decisioni aziendali.

### <a name="production-platforms"></a>Piattaforme di produzione
Ci sono diversi approcci e piattaforme per usare un modello in ambiente di produzione. Ecco alcune opzioni:


- [Distribuzione di modelli in Azure Machine Learning](../preview/model-management-overview.md)
- [Distribuzione di un modello in SQL-server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>
>
>NOTA: prima della distribuzione, è necessario assicurarsi che la latenza del punteggio del modello sia sufficientemente bassa per l'uso nell'ambiente di produzione.
>

Sono disponibili altri esempi in esercitazioni dettagliate che illustrano tutti i passaggi del processo per **scenari specifici**. Le esercitazioni sono elencate, con collegamenti e brevi descrizioni, nell'articolo [Procedure dettagliate di esempio](walkthroughs.md). Le esercitazioni spiegano come combinare strumenti cloud e locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente.

NOTA: per la distribuzione con Azure Machine Learning Studio, vedere [Distribuire un servizio Web di Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).

### <a name="ab-testing"></a>Test A/B
Quando nell'ambiente di produzione ci sono più modelli, può essere utile eseguire un [test A/B](https://en.wikipedia.org/wiki/A/B_testing) per confrontare le prestazioni dei modelli. 

 
## <a name="next-steps"></a>Passaggi successivi

L'articolo [Track progress of data science projects](track-progress.md) (Monitorare i progetti di data science) illustra in che modo un data scientist può monitorare l'avanzamento di un progetto di data science.
 


