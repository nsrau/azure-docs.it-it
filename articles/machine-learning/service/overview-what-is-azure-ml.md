---
title: Informazioni su Azure Machine Learning | Microsoft Docs
description: Illustra i concetti di base dell'apprendimento automatico nel cloud, descrive per che cosa può essere usato e definisce i termini relativi all'apprendimento automatico. Panoramica di Azure Machine Learning, una soluzione integrata di data science end-to-end per data scientist professionali per lo sviluppo, la sperimentazione e la distribuzione di applicazioni per analisi avanzate su scala cloud.
services: machine-learning
author: mwinkle
ms.author: mwinkle
manager: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.topic: get-started-article
ms.date: 09/21/2017
ms.openlocfilehash: 868647780bafe9117a15891264a38535edff7250
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2018
---
# <a name="what-is-machine-learning"></a>Informazioni su Machine Learning

Machine Learning è una tecnica di analisi scientifica dei dati che consente ai computer di usare i dati esistenti per prevedere comportamenti, tendenze e risultati futuri. Con Machine Learning, i computer apprendono senza essere programmati in modo esplicito.

Queste previsioni o stime di Machine Learning possono rendere più intelligenti le app e i dispositivi. Quando si effettuano acquisti online, l'apprendimento automatico consente di consigliare altri prodotti che potrebbero interessare in base a ciò che si è acquistato. Quando si usa la carta di credito, l'apprendimento automatico confronta la transazione con un database di transazioni e consente di rilevare eventuali frodi. Quando il robot aspirapolvere aspira la polvere in una stanza, l'apprendimento automatico gli consente di decidere se il lavoro è stato completato.

## <a name="what-is-azure-machine-learning"></a>Informazioni su Azure Machine Learning
Azure Machine Learning è una soluzione integrata per data science end-to-end e analisi avanzate. Consente ai data scientist di preparare i dati, sviluppare esperimenti e distribuire modelli su scala cloud.

I componenti principali di Azure Machine Learning sono i seguenti:
- Azure Machine Learning Workbench
- Servizio Sperimentazione di Azure Machine Learning
- Servizio Gestione modelli di Azure Machine Learning
- Microsoft Machine Learning Libraries per Apache Spark (MMLSpark Library)
- Visual Studio Code Tools for AI

Queste applicazioni e servizi consentono di accelerare in modo significativo lo sviluppo e la distribuzione di progetti di data science. 

![Concetti relativi ad Azure Machine Learning](./media/overview-what-is-azure-ml/aml-concepts.png)


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
- [Data Preparation User Guide](../desktop-workbench/data-prep-user-guide.md) (Guida dell'utente per la preparazione dati)
- [Using Git with Azure Machine Learning](../desktop-workbench/using-git-ml-project.md) (Uso di Git con Azure Machine Learning)
- [Using Jupyter Notebook in Azure Machine Learning](../desktop-workbench/how-to-use-jupyter-notebooks.md) (Uso di Jupyter Notebook in Azure Machine Learning)
- Roaming e condivisione
- [Run History Guide](../desktop-workbench/how-to-use-run-history-model-metrics.md) (Guida alla cronologia di esecuzione)
- [IDE Integration](../desktop-workbench/how-to-configure-your-ide.md) (Integrazione con l'IDE)

## <a name="azure-machine-learning-experimentation-service"></a>Servizio Sperimentazione di Azure Machine Learning
Il servizio Sperimentazione gestisce l'esecuzione di esperimenti di Machine Learning. Supporta anche Workbench fornendo funzionalità di gestione progetti, integrazione con Git, il controllo degli accessi, il roaming e la condivisione. 

Tramite semplici operazioni di configurazione, è possibile eseguire esperimenti in una vasta gamma di opzioni di ambienti di calcolo:

- Locale nativo
- Contenitore Docker locale
- Contenitore Docker in una VM remota
- Cluster Spark con aumento delle prestazioni in Azure

Il servizio Sperimentazione costruisce ambienti virtuali per assicurare che lo script possa essere eseguito in isolamento con risultati riproducibili. Registra le informazioni sulla cronologia di esecuzione e presenta visivamente la cronologia agli utenti. È possibile selezionare con facilità il modello migliore tra le esecuzioni degli esperimenti. 

Per altre informazioni, vedere [Experimentation Service Configuration](../desktop-workbench/experimentation-service-configuration.md) (Configurazione del servizio Sperimentazione).

## <a name="azure-machine-learning-model-management-service"></a>Servizio Gestione modelli di Azure Machine Learning

Il servizio Gestione modelli consente a data scientist e team di dev-ops di distribuire modelli predittivi in un'ampia gamma di ambienti. Le versioni e la derivazione dei modelli vengono verificate dalle esecuzioni di training alle distribuzioni. I modelli vengono archiviati, registrati e gestiti sul cloud. 

Usando semplici comandi dell'interfaccia della riga di comando è possibile creare contenitori per il modello, assegnare punteggi agli script e alle dipendenze nelle immagini Docker. Queste immagini sono registrate nel registro Docker specifico ospitato in Azure (Registro contenitori di Azure). Possono essere distribuite in modo affidabile nelle destinazioni seguenti:

- Computer locali
- Server locali
- Cloud
- Dispositivi perimetrali IoT

Kubernetes in esecuzione nel servizio contenitore di Azure viene usato per le distribuzioni su scala cloud. I dati di telemetria relativi all'esecuzione dei modelli vengono acquisiti in AppInsights per l'analisi visiva. 

Per altre informazioni sul servizio Gestione modelli, vedere [Model Management Overview](../desktop-workbench/model-management-overview.md) (Panoramica di Gestione modelli).


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

Per altre informazioni, vedere [Using MMLSpark in Azure Machine Learning](../desktop-workbench/how-to-use-mmlspark.md) (Uso di MMLSpark in Azure Machine Learning).

## <a name="visual-studio-code-tools-for-ai"></a>Visual Studio Code Tools for AI
Visual Studio Code Tools for AI è un'estensione di Visual Studio Code per la compilazione, il test e la distribuzioni di soluzioni per Deep Learning e intelligenza artificiale. Include molti punti di integrazione con Azure Machine Learning, tra cui:
- Visualizzazione della cronologia di esecuzione che mostra le prestazioni delle esecuzioni di training e delle metriche registrate.
- Visualizzazione Raccolta che consente agli utenti di esplorare e avviare nuovi progetti con Microsoft Cognitive Toolkit, TensorFlow e molti altri framework per Deep Learning. 
- Visualizzazione di esplorazione per la selezione di destinazioni di calcolo per l'esecuzione degli script.
 

## <a name="what-are-the-machine-learning-options-from-microsoft"></a>Informazioni sulle opzioni di Machine Learning fornite da Microsoft
Oltre ad Azure Machine Learning, sono disponibili diverse opzioni in Azure per la compilazione, la distribuzione e la gestione di modelli di Machine Learning. [Altre informazioni in proposito sono disponibili qui.](../desktop-workbench/overview-more-machine-learning.md)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>Passaggi successivi
* [Install and create Azure Machine Learning (Installare e creare istanze di Azure Machine Learning)](quickstart-installation.md)
