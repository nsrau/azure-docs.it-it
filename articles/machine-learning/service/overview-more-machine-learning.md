---
title: Altri prodotti di apprendimento automatico Microsoft in Azure Machine Learning | Documentazione Microsoft
description: Oltre ad Azure Machine Learning, Microsoft offre varie altre opzioni per la compilazione, la distribuzione e la gestione di modelli di Machine Learning.
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: garyericson, mldocs
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/11/2018
ms.openlocfilehash: d3cb1a9f10d82eb1df585ae03adf4130888bd84c
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832901"
---
# <a name="other-machine-learning-products-and-services-from-microsoft"></a>Altri prodotti di apprendimento automatico e servizi Microsoft

Oltre ad [Azure Machine Learning](overview-what-is-azure-ml.md), Microsoft offre varie altre opzioni per la compilazione, la distribuzione e la gestione di modelli di Machine Learning. 
* SQL Server Machine Learning Services
* Microsoft Machine Learning Server
* Macchina virtuale di data science di Azure
* Spark MLLib in HDInsight
* Servizio Batch AI Training
* Microsoft Cognitive Toolkit (CNTK)
* Servizi cognitivi di Azure


## <a name="sql-server-machine-learning-services"></a>SQL Server Machine Learning Services
I [Servizi Microsoft Machine Learning di SQL Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) consentono di eseguire, eseguire il training e distribuire modelli di Machine Learning tramite R o Python. È possibile usare dati in locale e in database di SQL Server. 

Usare i Servizi Microsoft Machine Learning quando è necessario eseguire il training o distribuire modelli in locale o in Microsoft SQL Server. I modelli creati con i Servizi Machine Learning possono essere distribuiti usando Gestione modelli di Azure Machine Learning. 

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server 
[Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone) è un server aziendale per l'hosting e la gestione di carichi di lavoro paralleli e distribuiti di processi R e Python. Microsoft Machine Learning Server viene eseguito su Linux, Windows, Hadoop e Apache Spark. È anche disponibile in [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/). Fornisce un motore di esecuzione per le soluzione create tramite [pacchetti di Microsoft Machine Learning](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package) ed estende R e Python open source con il supporto per gli scenari seguenti:

- Analisi ad alte prestazioni
- Analisi statistiche
- Machine learning
- Set di dati di grandi dimensioni

Le funzionalità aggiuntive vengono fornite tramite pacchetti proprietari installati insieme al server. Per lo sviluppo è possibile usare IDE come [R Tools per Visual Studio](https://www.visualstudio.com/vs/rtvs/) e [Python Tools for Visual Studio](https://www.visualstudio.com/vs/python/).

Usare Microsoft Machine Learning Server quando è necessario eseguire queste operazioni:

- Creare e distribuire modelli creati con R e Python in un server
- Distribuire training R e Python su larga scala in un cluster Hadoop o Spark

## <a name="azure-data-science-virtual-machine"></a>Macchina virtuale di data science di Azure
La [macchina virtuale di data science](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) è un ambiente VM personalizzato nel cloud di Microsoft Azure, creato in modo specifico per l'analisi scientifica dei dati. Include diversi strumenti comuni per l'analisi scientifica dei dati e altri strumenti preinstallati e preconfigurati per implementare rapidamente la creazione di applicazioni intelligenti per l'analisi avanzata. La macchina virtuale di data science è disponibile nelle versioni 2016 e 2012 di Windows Server, in una VM Linux su Ubuntu 16.04 LTS  e nelle distribuzioni basate su OpenLogic CentOS 7.4. 

Usare la macchina virtuale di data science quando è necessario eseguire oppure ospitare processi in un singolo nodo. o se è necessario aumentare in modo remoto le prestazioni di elaborazione in un singolo computer. La macchina virtuale di data science è supportata come destinazione per Sperimentazione di Azure Machine Learning Experimentation e Gestione modelli di Azure Machine Learning. 

## <a name="spark-mllib-in-hdinsight"></a>Spark MLLib in HDInsight
[Spark MLLib in HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-ipython-notebook-machine-learning) consente di creare modelli come parte dei processi Spark in esecuzione su Big Data. Spark consente di trasformare e preparare con facilità i dati e quindi di aumentare la creazione di modelli in un singolo processo. I modelli creati tramite Spark MLLib possono essere distribuiti, gestiti e monitorati tramite Gestione modelli di Azure Machine Learning. Le esecuzioni di training possono essere inviate e gestite con Sperimentazione di Azure Machine Learning. Spark può essere usato anche per aumentare le prestazioni dei processi di preparazione dei dati creati in Machine Learning Workbench. 

Usare Spark quando è necessario aumentare le prestazioni dell'elaborazione dei dati e creare modelli come parte di una pipeline di dati. È possibile creare processi Spark in Scala, Java, Python o R. 

## <a name="batch-ai-training"></a>Batch AI Training 
[Azure Batch AI Training](https://aka.ms/batchaitraining) consente di sperimentare in parallelo con i modelli di intelligenza artificiale, usando qualsiasi framework, e quindi esegue il training dei modelli su larga scala in GPU cluster. Descrivere i requisiti del processo e la configurazione da eseguire. Il resto della procedura verrà gestito automaticamente. 

Batch AI Training consente di aumentare le prestazioni dei processi di Deep Learning in GPU cluster, usando frameworks quali:

- Cognitive Toolkit
- Caffe
- Chainer
- TensorFlow

Gestione modelli di Azure Machine Learning può essere usato per accettare modelli da Batch AI Training e quindi distribuirli, gestirli e monitorarli.  Batch AI Training verrà integrato in Sperimentazione di Azure Machine Learning in futuro. 

## <a name="microsoft-cognitive-toolkit-cntk"></a>Microsoft Cognitive Toolkit (CNTK)
[Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) è un toolkit unificato per Deep Learning che descrive le reti neurali come passaggi di calcolo in un grafo orientato. In questo grafo orientato i nodi foglia rappresentano i valori di input o i parametri di rete, mentre gli altri nodi rappresentano operazioni della matrice sui relativi input. Cognitive Toolkit consente di realizzare e combinare con facilità i tipi di modello più diffusi, ad esempio DNN di inoltro del feed, reti convoluzionali (CNN) e reti ricorrenti (RNN/LSTM). Implementa l'apprendimento basato su discesa del gradiente stocastico (SGD, backpropagation degli errori) con la differenziazione automatica e la parallelizzazione tra più GPU e server.

Usare Cognitive Toolkit quando si vuole creare un modello tramite Deep Learning.  Cognitive Toolkit può essere usato in tutti i servizi indicati in precedenza.

## <a name="azure-cognitive-services"></a>Servizi cognitivi di Azure
[Servizi cognitivi di Azure](https://docs.microsoft.com/azure/#pivot=products&panel=ai) è un set di circa 30 API che consente di creare app che usano metodi di comunicazione naturali. Queste API consentono alle app di vedere, sentire, parlare, comprendere e interpretare le esigenze degli utenti con poche righe di codice. È possibile aggiungere con facilità funzionalità intelligenti alle app, ad esempio: 

- Rilevamento di emozioni e sentiment
- Riconoscimento visivo e vocale
- Comprensione del linguaggio (LUIS, Language Understanding)
- Conoscenza e ricerca

I Servizi cognitivi possono essere usati per sviluppare app in diversi dispositivi e diverse piattaforme. Le API vengono migliorate continuamente e sono facili da installare. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>Passaggi successivi

Leggere la panoramica di [Azure Machine Learning](overview-what-is-azure-ml.md).
