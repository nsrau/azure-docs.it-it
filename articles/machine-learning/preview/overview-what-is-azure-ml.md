---
title: Informazioni su Azure Machine Learning | Microsoft Docs
description: Panoramica di Sperimentazione e Gestione modelli di Azure Machine Learning, una soluzione integrata di data science end-to-end per data scientist professionali per lo sviluppo, la sperimentazione e la distribuzione di applicazioni per analisi avanzate su scala cloud.
services: machine-learning
author: haining
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: get-started-article
ms.date: 09/21/2017
ms.openlocfilehash: 5535433c478b989e255451a0bf882dfb8ba8f8fe
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="what-is-azure-machine-learning"></a>Informazioni su Azure Machine Learning

Azure Machine Learning è una soluzione integrata per data science end-to-end e analisi avanzate. Consente ai data scientist di preparare i dati, sviluppare esperimenti e distribuire modelli su scala cloud.

I componenti principali di Azure Machine Learning sono i seguenti:
- Azure Machine Learning Workbench
- Servizio Sperimentazione di Azure Machine Learning
- Servizio Gestione modelli di Azure Machine Learning
- Microsoft Machine Learning Libraries per Apache Spark (MMLSpark Library)
- Visual Studio Code Tools for AI

Queste applicazioni e servizi consentono di accelerare in modo significativo lo sviluppo e la distribuzione di progetti di data science. 

![Concetti relativi ad Azure Machine Learning](media/overview-what-is-azure-ml/aml-concepts.png)

## <a name="open-source-compatible"></a>Compatibilità con le soluzioni open source

Azure Machine Learning supporta completamente le tecnologie open source. È possibile usare decine di migliaia di pacchetti Python open source, ad esempio i framework di Machine Learning seguenti:

- [scikit-learn](http://scikit-learn.org/)
- [TensorFlow](https://www.tensorflow.org/)
- [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)
- [Spark ML](https://spark.apache.org/docs/2.1.1/ml-pipeline.html)

È possibile eseguire gli esperimenti in ambienti gestiti come i contenitori Docker e i cluster Spark. È anche possibile usare hardware avanzato come [macchine virtuali abilitate per GPU in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu) per accelerare l'esecuzione.

Azure Machine Learning è basato sulle tecnologie open source seguenti:

- [Notebook Jupyter](http://jupyter.org/)
- [Apache Spark](https://spark.apache.org/)
- [Docker](https://www.docker.com/)
- [Kubernetes](https://kubernetes.io/)
- [Python](https://www.python.org/)
- [Conda](https://conda.io/docs/)

Include anche le tecnologie open source Microsoft, come [Microsoft Machine Learning Library per Apache Spark](https://github.com/Azure/mmlspark) e Cognitive Toolkit.

È anche possibile sfruttare i vantaggi di alcune delle più avanzate tecnologie consolidata per Machine Learning sviluppate da Microsoft progettate per risolvere problemi su larga scala. Queste tecnologie sono state testate in molti prodotti Microsoft, ad esempio:

- Windows
- Bing
- Xbox
- Office
- SQL Server

Di seguito sono elencate alcune tecnologie di Microsoft Machine Learning incluse in Azure Machine Learning:

- [PROSE](https://microsoft.github.io/prose/) (PROgram Synthesis using Examples)
- [microsoftml](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)
- [revoscalepy](https://docs.microsoft.com/r-server/python-reference/revoscalepy/revoscalepy-package)

## <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench
Azure Machine Learning Workbench è un'applicazione desktop con strumenti da riga di comando, supportato su Windows e macOS. Consente di gestire soluzioni di Machine Learning durante l'intero ciclo di vita di data science:

- Inserimento e preparazione dei dati
- Sviluppo di modelli e gestione degli esperimenti
- Distribuzione di modelli in diversi ambienti di destinazione

Ecco le funzionalità principali offerti da Azure Machine Learning Workbench:

- Strumento di preparazione dei dati in grado di apprendere la logica di trasformazione dei dati in base esempi.
- Astrazione di origini dati accessibile tramite interfaccia utente e codice Python.
- Python SDK per richiamare pacchetti di preparazione dei dati con costruzione visiva.
- Servizio Jupyter Notebook incorporato e interfaccia utente client.
- Monitoraggio e gestione degli esperimenti tramite le visualizzazioni della cronologia di esecuzione.
- Controllo degli accessi in base al ruolo che consente la condivisione e la collaborazione tramite Azure Active Directory.
- Snapshot automatici del progetto per ogni esecuzione e controllo della versione esplicito abilitato dall'integrazione Git nativa. 
- Integrazione con gli IDE Python più diffusi.

Per altre informazioni, vedere gli articoli seguenti:
- [Data Preparation User Guide](data-prep-user-guide.md) (Guida dell'utente per la preparazione dati)
- [Using Git with Azure Machine Learning](using-git-ml-project.md) (Uso di Git con Azure Machine Learning)
- [Using Jupyter Notebook in Azure Machine Learning](how-to-use-jupyter-notebooks.md) (Uso di Jupyter Notebook in Azure Machine Learning)
- Roaming e condivisione
- [Run History Guide](how-to-use-run-history-model-metrics.md) (Guida alla cronologia di esecuzione)
- [IDE Integration](how-to-configure-your-IDE.md) (Integrazione con l'IDE)

## <a name="azure-machine-learning-experimentation-service"></a>Servizio Sperimentazione di Azure Machine Learning
Il servizio Sperimentazione gestisce l'esecuzione di esperimenti di Machine Learning. Supporta anche Workbench fornendo funzionalità di gestione progetti, integrazione con Git, il controllo degli accessi, il roaming e la condivisione. 

Tramite semplici operazioni di configurazione, è possibile eseguire esperimenti in una vasta gamma di opzioni di ambienti di calcolo:

- Locale nativo
- Contenitore Docker locale
- Contenitore Docker in una VM remota
- Cluster Spark con aumento delle prestazioni in Azure

Il servizio Sperimentazione costruisce ambienti virtuali per assicurare che lo script possa essere eseguito in isolamento con risultati riproducibili. Registra le informazioni sulla cronologia di esecuzione e presenta visivamente la cronologia agli utenti. È possibile selezionare con facilità il modello migliore tra le esecuzioni degli esperimenti. 

Per altre informazioni, vedere [Experimentation Service Configuration](experimentation-service-configuration.md) (Configurazione del servizio Sperimentazione).

## <a name="azure-machine-learning-model-management-service"></a>Servizio Gestione modelli di Azure Machine Learning

Il servizio Gestione modelli consente a data scientist e team di dev-ops di distribuire modelli predittivi in un'ampia gamma di ambienti. Le versioni e la derivazione dei modelli vengono verificate dalle esecuzioni di training alle distribuzioni. I modelli vengono archiviati, registrati e gestiti sul cloud. 

Usando semplici comandi dell'interfaccia della riga di comando è possibile creare contenitori per il modello, assegnare punteggi agli script e alle dipendenze nelle immagini Docker. Queste immagini sono registrate nel registro Docker specifico ospitato in Azure (Registro contenitori di Azure). Possono essere distribuite in modo affidabile nelle destinazioni seguenti:

- Computer locali
- Server locali
- Cloud
- Dispositivi perimetrali IoT

Kubernetes in esecuzione nel servizio contenitore di Azure viene usato per le distribuzioni su scala cloud. I dati di telemetria relativi all'esecuzione dei modelli vengono acquisiti in AppInsights per l'analisi visiva. 

Per altre informazioni sul servizio Gestione modelli, vedere [Model Management Overview](model-management-overview.md) (Panoramica di Gestione modelli).


## <a name="microsoft-machine-learning-library-for-apache-spark"></a>Microsoft Machine Learning Library per Apache Spark

[MMLSpark](https://github.com/Azure/mmlspark)(Microsoft Machine Learning Library per Apache Spark) è un pacchetto Spark open source che fornisce strumenti per Deep Learning e data science per Apache Spark. Integra le [pipeline di Spark Machine Learning](https://spark.apache.org/docs/2.1.1/ml-pipeline.html) con [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) e la libreria [OpenCV](http://opencv.org/). Consente di creare rapidamente modelli avanzati a scalabilità, predittivi e analitici per set di dati di immagini e di testo di grandi dimensioni. Ecco alcune delle caratteristiche principali:

- Inserimento semplificato di immagini da HDFS in Spark DataFrame
- Pre-elaborazione dei dati di immagine tramite trasformazioni da OpenCV
- Creazione di funzionalità dalle immagini tramite reti neurali profonde con pre-training usando Microsoft Cognitive Toolkit 
- Uso di LSTM bidirezionali con pre-training per l'estrazione di entità mediche
- Training di modelli di classificazione di immagini basati su DNN in VM GPU di serie N in Azure
- Creazione di funzionalità da dati di testo in formato libero tramite utili API basate su primitive in SparkML tramite un singolo convertitore
- Training semplificato dei modelli di classificazione e regressione tramite la creazione implicita di funzionalità dai dati
- Calcolo di un set avanzato di metriche di valutazione, incluse metriche per singole istanze

Per altre informazioni, vedere [Using MMLSpark in Azure Machine Learning](how-to-use-mmlspark.md) (Uso di MMLSpark in Azure Machine Learning).

## <a name="visual-studio-code-tools-for-ai"></a>Visual Studio Code Tools for AI
Visual Studio Code Tools for AI è un'estensione di Visual Studio Code per la compilazione, il test e la distribuzioni di soluzioni per Deep Learning e intelligenza artificiale. Include molti punti di integrazione con Azure Machine Learning, tra cui:
- Visualizzazione della cronologia di esecuzione che mostra le prestazioni delle esecuzioni di training e delle metriche registrate.
- Visualizzazione Raccolta che consente agli utenti di esplorare e avviare nuovi progetti con Microsoft Cognitive Toolkit, TensorFlow e molti altri framework per Deep Learning. 
- Visualizzazione di esplorazione per la selezione di destinazioni di calcolo per l'esecuzione degli script.
 

## <a name="what-are-the-machine-learning-options-from-microsoft"></a>Informazioni sulle opzioni di Machine Learning fornite da Microsoft
Oltre ad Azure Machine Learning, sono disponibili diverse opzioni in Azure per la compilazione, la distribuzione e la gestione di modelli di Machine Learning. 
* Servizi di Microsoft Machine Learning in SQL Server
* Microsoft Machine Learning Server
* Macchina virtuale di data science
* Spark MLLib in HDInsight
* Servizio Batch AI Training
* Microsoft Cognitive Toolkit
* Servizi cognitivi Microsoft


### <a name="microsoft-machine-learning-services-in-sql-server"></a>Servizi di Microsoft Machine Learning in SQL Server
I [Servizi Microsoft Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) consentono di eseguire, eseguire il training e distribuire modelli di Machine Learning tramite R o Python. È possibile usare dati in locale e in database di SQL Server. 

Usare i Servizi Microsoft Machine Learning quando è necessario eseguire il training o distribuire modelli in locale o in Microsoft SQL Server. I modelli creati con i Servizi Machine Learning possono essere distribuiti usando Gestione modelli di Azure Machine Learning. 

### <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server 
[Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone) è un server aziendale per l'hosting e la gestione di carichi di lavoro paralleli e distribuiti di processi R e Python. Microsoft Machine Learning Server viene eseguito su Linux, Windows, Hadoop e Apache Spark. È anche disponibile in [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/). Fornisce un motore di esecuzione per le soluzione create tramite [pacchetti di Microsoft Machine Learning](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package), ed estende R e Python open source con il supporto per gli scenari seguenti:

- Analisi a prestazioni elevate
- Analisi statistiche
- Machine Learning
- Set di dati di grandissime dimensioni

Le funzionalità con valore aggiunto vengono fornite tramite pacchetti proprietari installati insieme al server. Per lo sviluppo è possibile usare IDE come [R Tools per Visual Studio](https://www.visualstudio.com/vs/rtvs/) e [Python Tools for Visual Studio](https://www.visualstudio.com/vs/python/).

Usare Microsoft Machine Learning Server quando è necessario eseguire queste operazioni:

- Creare e distribuire modelli creati con R e Python in un server
- Distribuire training R e Python su larga scala in un cluster Hadoop o Spark

### <a name="data-science-virtual-machine"></a>Macchina virtuale di data science
La [macchina virtuale di data science](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) è un'immagine di VM personalizzata sul cloud di Microsoft Azure creata in modo specifico per data science. Include diversi strumenti comuni per l'analisi scientifica dei dati e altri strumenti preinstallati e preconfigurati per implementare rapidamente la creazione di applicazioni intelligenti per l'analisi avanzata. È disponibile in Windows Server e in Linux. L'edizione della DSVM per Windows è disponibile in Windows Server 2016 e Windows Server 2012. L'edizione della DSVM per Linux è disponibile in Ubuntu 16.04 LTS e nelle distribuzioni Linux basate su OpenLogic 7.2 CentOS. 

Usare la macchina virtuale di data science quando è necessario eseguire oppure ospitare processi in un singolo nodo o se è necessario aumentare in modo remoto le prestazioni di elaborazione in un singolo computer. La macchina virtuale di data science è supportata come destinazione per Sperimentazione di Azure Machine Learning Experimentation e Gestione modelli di Azure Machine Learning. 

### <a name="spark-mllib-in-hdinsight"></a>Spark MLLib in HDInsight
[Spark MLLib in HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-ipython-notebook-machine-learning) consente di creare modelli come parte dei processi Spark in esecuzione su Big Data. Spark consente di trasformare e preparare con facilità i dati e quindi di aumentare la creazione di modelli in un singolo processo. I modelli creati tramite Spark MLLib possono essere distribuiti, gestiti e monitorati tramite Gestione modelli di Azure Machine Learning. Le esecuzioni di training possono essere inviate e gestite con Sperimentazione di Azure Machine Learning. Spark può essere usato anche per aumentare le prestazioni dei processi di preparazione dei dati creati in Machine Learning Workbench. 

Usare Spark quando è necessario aumentare le prestazioni dell'elaborazione dei dati e creare modelli come parte di una pipeline di dati. È possibile creare processi Spark in Scala, Java, Python o R. 

### <a name="batch-ai-training"></a>Batch AI Training 
[Azure Batch AI Training](https://aka.ms/batchaitraining) consente di sperimentare in parallelo con i modelli di intelligenza artificiale, usando qualsiasi framework, e quindi esegue il training dei modelli su larga scala in GPU cluster. Descrivere i requisiti del processo e la configurazione da eseguire. Il resto della procedura verrà gestito automaticamente. 

Batch AI Training consente di aumentare le prestazioni dei processi di Deep Learning in GPU cluster, usando frameworks quali:

- Cognitive Toolkit
- Caffe
- Chainer
- TensorFlow

Gestione modelli di Azure Machine Learning può essere usato per accettare modelli da Batch AI Training e quindi distribuirli, gestirli e monitorarli.  Batch AI Training verrà integrato in Sperimentazione di Azure Machine Learning in futuro. 

### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit
[Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) è un toolkit unificato per Deep Learning che descrive le reti neurali come passaggi di calcolo in un grafo orientato. In questo grafo orientato i nodi foglia rappresentano i valori di input o i parametri di rete, mentre gli altri nodi rappresentano operazioni della matrice sui relativi input. Cognitive Toolkit consente di realizzare e combinare con facilità i tipi di modello più diffusi, ad esempio DNN di inoltro del feed, reti convoluzionali (CNN) e reti ricorrenti (RNN/LSTM). Implementa l'apprendimento basato su discesa del gradiente stocastico (SGD, backpropagation degli errori) con la differenziazione automatica e la parallelizzazione tra più GPU e server.

Usare Cognitive Toolkit quando si vuole creare un modello tramite Deep Learning.  Cognitive Toolkit può essere usato in tutti i servizi indicati in precedenza.

### <a name="microsoft-cognitive-services"></a>Servizi cognitivi Microsoft
Servizi cognitivi Microsoft è un set di 30 API che consente di creare app che usano metodi di comunicazione naturali. Queste API consentono alle app di vedere, sentire, parlare, comprendere e interpretare le esigenze degli utenti con poche righe di codice. È possibile aggiungere con facilità funzionalità intelligenti alle app, ad esempio: 

- Rilevamento di emozioni e sentiment
- Riconoscimento visivo e vocale
- Comprensione del linguaggio
- Conoscenza e ricerca

I Servizi cognitivi Microsoft possono essere usati per sviluppare app in diversi dispositivi e diverse piattaforme. Le API vengono migliorate continuamente e sono facili da installare. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>Passaggi successivi
* [Install and create Azure Machine Learning (Installare e creare istanze di Azure Machine Learning)](quickstart-installation.md)
