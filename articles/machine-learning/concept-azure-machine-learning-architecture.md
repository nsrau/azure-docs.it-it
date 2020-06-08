---
title: Architettura e nozioni chiave
titleSuffix: Azure Machine Learning
description: Informazioni su architettura, terminologia, nozioni e flussi di lavoro di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 05/13/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: 49c23774fe16c24ba90daa02cdda1688b79b12d3
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683048"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Modalità di funzionamento di Azure Machine Learning: Architettura e concetti

Informazioni su architettura, nozioni e flussi di lavoro di Azure Machine Learning. Il diagramma seguente illustra i componenti principali del servizio e il flusso di lavoro generale durante il suo uso:

![Architettura e flusso di lavoro di Azure Machine Learning](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Flusso di lavoro

Il flusso di lavoro del modello di Machine Learning, in genere, segue questa sequenza:

1. **Esecuzione del training**
    + Sviluppare script di training di Machine Learning in **Python**, **R** o con Progettazione grafica.
    + Creare e configurare una **destinazione di calcolo**.
    + **Inviare gli script** a una destinazione di calcolo configurata per l'esecuzione in tale ambiente. Durante il training, gli script possono leggere o scrivere in **archivi dati**. I log e l'output generati durante il training vengono salvati come **esecuzioni** nell'**area di lavoro** e raggruppati in **esperimenti**.

1. **Pacchetto** - Una volta trovata un'esecuzione soddisfacente, registrare il modello persistente nel **registro di modello**.

1. **Convalidare** - **Eseguire una query sull'esperimento** per le metriche registrate dalle esecuzioni correnti e precedenti. Se le metriche non indicano un risultato desiderato, tornare al passaggio 1 ed eseguire l'iterazione sugli script.

1. **Distribuire** - Sviluppare uno script di assegnazione dei punteggi che usa il modello e **Distribuire il modello** come **servizio Web** in Azure o su un **dispositivo IoT Edge**.

1. **Monitorare** - Monitorare la **deriva dei dati** tra il set di dati di training e i dati di inferenza di un modello distribuito. Laddove necessario, eseguire il loopback al passaggio 1 per ripetere il training del modello con i nuovi dati di training.

## <a name="tools-for-azure-machine-learning"></a>Strumenti per Azure Machine Learning

Usare i seguenti strumenti per Azure Machine Learning:

+  Interagire con il servizio in qualsiasi ambiente Python con l'[SDK Azure Machine Learning per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Interagire con il servizio in qualsiasi ambiente R con l'[SDK Azure Machine Learning per R](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Automatizzare le attività di apprendimento automatico con l'[interfaccia della riga di comando di Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli).
+ Usare la [finestra di progettazione di Azure Machine Learning (anteprima)](concept-designer.md) per eseguire i passaggi del flusso di lavoro senza scrivere alcun codice.
+ L'[acceleratore di soluzione molti modelli](https://aka.ms/many-models) (anteprima) si basa su Azure Machine Learning consente di eseguire il training, utilizzare e gestire centinaia o addirittura migliaia di modelli di Machine Learning.

> [!NOTE]
> Sebbene in questo articolo vengano definiti termini e concetti usati da Azure Machine Learning, non vengono definiti quelli relativi alla piattaforma di Azure. Per altre informazioni sulla terminologia della piattaforma Azure, vedere il [glossario di Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="glossary"></a>Glossario

* [Attività](#activities)
* [Area di lavoro](#workspaces)
    * [Sperimentazioni](#experiments)
        * [Esegui](#runs) 
            * [Configurazione di esecuzione](#run-configurations)
            * [Snapshot](#snapshots)
            * [Rilevamento Git](#github-tracking-and-integration)
            * [Logging](#logging)
    * [Pipeline di Machine Learning](#ml-pipelines)
    * [Modelli](#models)
        * [Ambienti](#environments)
        * [Script di training](#training-scripts)
        * [Oggetti estimator](#estimators)
    * [Endpoints](#endpoints)
        * [Servizio Web](#web-service-endpoint)
        * [Moduli IoT](#iot-module-endpoints)
    * [Set di dati e archivi dati](#datasets-and-datastores)
    * [Destinazioni di calcolo](#compute-targets)

### <a name="activities"></a>attività

Un'attività rappresenta un'operazione a esecuzione prolungata. Le operazioni seguenti sono esempi di attività:

* Creazione o eliminazione di una destinazione di calcolo
* Esecuzione di uno script su una destinazione di calcolo

Le attività possono fornire notifiche tramite l'SDK o l'interfaccia utente Web in modo da poter facilmente monitorare l'avanzamento di queste operazioni.

### <a name="workspaces"></a>Aree di lavoro

[L'area di lavoro](concept-workspace.md) è la risorsa di primo livello per Azure Machine Learning. Fornisce una posizione centralizzata per lavorare con tutti gli artefatti creati durante l'uso di Azure Machine Learning. È possibile condividere un'area di lavoro con altri utenti. Per una descrizione dettagliata delle aree di lavoro, vedere [Che cos'è un'area di lavoro di Azure Machine Learning?](concept-workspace.md).

### <a name="experiments"></a>Sperimentazioni

Un esperimento è un raggruppamento di più esecuzioni da uno script specifico. Appartiene sempre a un'area di lavoro. Quando si invia un'esecuzione, si fornisce un nome dell'esperimento. Le informazioni per l'esecuzione vengono archiviate in tale esperimento. Se si invia un'esecuzione e si specifica un nome dell'esperimento che non esiste, viene creato automaticamente un nuovo esperimento il nuovo nome specificato.

Per un esempio d'uso di un esperimento, vedere [Esercitazione: Eseguire il training del primo modello](tutorial-1st-experiment-sdk-train.md).

### <a name="runs"></a>Esecuzioni

Per esecuzione si intende una singola esecuzione di uno script di training. Di norma, un esperimento contiene più esecuzioni.

Azure Machine Learning registra tutte le esecuzioni e archivia le informazioni seguenti nell'esperimento:

* Metadati sull'esecuzione (timestamp, durata e così via)
* Metriche registrate dallo script
* File di output raccolti automaticamente dall'esperimento o caricati in modo esplicito dall'utente
* Uno snapshot della directory che contiene gli script, precedente all'esecuzione

Un'esecuzione viene generata quando si invia uno script per eseguire il training di un modello. Un'esecuzione può avere zero o più esecuzioni figlio. L'esecuzione di primo livello, ad esempio, potrebbe avere due esecuzioni figlio, ognuna delle quali può avere a sua volte le proprie esecuzioni figlio.

### <a name="run-configurations"></a>Configurazioni di esecuzione

Una configurazione di esecuzione è un set di istruzioni che definisce come uno script deve essere eseguito in una destinazione di calcolo specificata. La configurazione include un'ampia serie di definizioni di comportamento, ad esempio se usare un ambiente Python esistente oppure un ambiente Conda compilato a partire da una specifica.

Una configurazione di esecuzione può essere salvata in modo permanente in un file all'interno della directory che contiene lo script di training, oppure costruita come un oggetto in memoria e usata per inviare un'esecuzione.

Per degli esempi di configurazioni di esecuzione, vedere [Selezionare e usare una destinazione di calcolo per eseguire il training del modello](how-to-set-up-training-targets.md).

### <a name="snapshots"></a>Snapshot

Quando si invia un'esecuzione, Azure Machine Learning consente di comprimere la directory che contiene lo script come file ZIP e lo invia alla destinazione di calcolo. Il file ZIP viene quindi estratto e lo script eseguito in questa posizione. Azure Machine Learning archivia inoltre il file ZIP come snapshot come parte del record di esecuzione. Chiunque abbia accesso all'area di lavoro può esplorare un record di esecuzione e scaricare lo snapshot.

> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="github-tracking-and-integration"></a>Rilevamento e integrazione di GitHub

Quando si avvia un'esecuzione di training in cui la directory di origine è un repository Git locale, le informazioni sul repository vengono archiviate nella cronologia di esecuzione. Questa operazione funziona con le esecuzioni inviate usando una stima, una pipeline di Machine Learning o un'esecuzione di script. Funziona anche per le esecuzioni inviate dall'SDK o dall'interfaccia della riga di comando di Machine Learning.

Per altre informazioni, vedere [Integrazione di Git con Azure Machine Learning](concept-train-model-git-integration.md).

### <a name="logging"></a>Registrazione

Quando si sviluppa la soluzione, usare Python SDK di Azure Machine Learning nello script di Python per registrare metriche arbitrarie. Dopo l'esecuzione, eseguire query sulle metriche per determinare se l'esecuzione ha prodotto il modello che si desidera distribuire.

### <a name="ml-pipelines"></a>Pipeline di Machine Learning

Le pipeline di Machine Learning consentono di creare e gestire i flussi di lavoro in cui sono unite le varie fasi dell'apprendimento automatico. Ad esempio, una pipeline può includere le fasi di preparazione dei dati, training del modello, distribuzione del modello e inferenza/punteggio. Ogni fase può includere più passaggi, ciascuno dei quali può essere eseguito in modo automatico in varie destinazioni di calcolo. 

I passaggi della pipeline sono riutilizzabili e possono essere eseguiti senza rieseguire i passaggi precedenti se l'output di questi passaggi non è stato modificato. Ad esempio, è possibile ripetere il training di un modello senza eseguire nuovamente costosi passaggi di preparazione dei dati se questi non sono stati modificati. Le pipeline consentono anche ai data scientist di collaborare mentre lavorano su aree separate di un flusso di lavoro di apprendimento automatico.

Per altre informazioni sulle pipeline di apprendimento automatico, vedere [Pipeline e Azure Machine Learning](concept-ml-pipelines.md).

### <a name="models"></a>Modelli

Nella forma più semplice, un modello è un frammento di codice che accetta un input e produce output. La creazione di un modello di Machine Learning prevede di selezionare un algoritmo, fornirgli dei dati e ottimizzare gli iperparametri. Il training è un processo iterativo che produce un modello con training, che racchiude ciò che il modello ha appreso durante il processo di training.

Un modello è prodotto da un'esecuzione in Azure Machine Learning. È anche possibile usare un modello il cui training sia stato eseguito all'esterno di Azure Machine Learning. È possibile registrare un modello in un'area di lavoro di Azure Machine Learning.

Azure Machine Learning è indipendente dal framework. Durante la creazione di un modello, è possibile usare un qualsiasi framework di apprendimento automatico comune, ad esempio Scikit-learn, XGBoost, PyTorch, TensorFlow e Chainer.

Per un esempio di training di un modello usando Scikit-learn e una stima, vedere [Esercitazione: Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md).

Il **registro di modello** tiene traccia di tutti i modelli nell'area di lavoro di Azure Machine Learning.

I modelli vengono identificati dal nome e dalla versione. Ogni volta che si registra un modello con lo stesso nome di uno esistente, il registro presuppone che si tratti di una nuova versione. La versione viene incrementata e il nuovo modello registrato con lo stesso nome.

Quando si registra il modello, è possibile specificare altri tag di metadati e quindi usarli durante la ricerca di modelli.

> [!TIP]
> Un modello registrato è un contenitore logico per uno o più file che costituiscono il modello. Se, ad esempio, si dispone di un modello archiviato in più file, è possibile registrarli come singolo modello nell'area di lavoro di Azure Machine Learning. Dopo la registrazione, è possibile scaricare o distribuire il modello registrato e ricevere tutti i file registrati.

Non è possibile eliminare un modello registrato in uso da parte di una distribuzione attiva.

Per un esempio di registrazione di un modello, vedere [Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="environments"></a>Ambienti

Gli ambienti di Azure Machine Learning vengono usati per specificare la configurazione (Docker/Python/Spark o altro) usata per creare un ambiente riproducibile per la preparazione dei dati, il training e la gestione di modelli. Gli ambienti sono entità gestite e con versione all'interno dell'area di lavoro di Azure Machine Learning che consentono flussi di lavoro riproducibili, controllabili e portabili in diverse destinazioni di calcolo.

È possibile usare un oggetto ambiente nel calcolo locale per sviluppare lo script di training, riutilizzare lo stesso ambiente nell'ambiente di calcolo di Machine Learning per il training dei modelli su larga scala e persino distribuire il modello con lo stesso ambiente. 

Informazioni su [come creare e gestire un ambiente di Azure Machine Learning riutilizzabile](how-to-use-environments.md) per il training e l'inferenza.

### <a name="training-scripts"></a>Script di training

Per eseguire il training di un modello si specifica la directory che contiene lo script di training e i file associati. Si specifica inoltre un nome per l'esperimento, che viene usato per archiviare le informazioni raccolte durante il training. Durante il training l'intera directory viene copiata nell'ambiente di training (destinazione di calcolo) e lo script specificato per la configurazione di esecuzione viene avviato. Uno snapshot della directory viene archiviato anche nell'esperimento nell'area di lavoro.

Per un esempio, vedere [Esercitazione: Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="estimators"></a>Oggetti estimator

Per facilitare il training dei modelli con i framework più diffusi, la classe di stima consente di costruire facilmente configurazioni di esecuzione. È possibile creare e usare un oggetto [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) generico per inviare script di training che usano qualsiasi framework di apprendimento scelto, ad esempio scikit-learn.

Per le attività PyTorch, TensorFlow e Chainer, Azure Machine Learning fornisce anche le stime rispettivamente per [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py), che semplificano l'uso di questi framework.

Per altre informazioni, vedere gli articoli seguenti:

* [Eseguire il training di modelli di Machine Learning mediante stime](how-to-train-ml-models.md).
* [Eseguire il training di modelli di Deep Learning Pytorch su larga scala con Azure Machine Learning](how-to-train-pytorch.md).
* [Eseguire il training di modelli di Deep Learning TensorFlow su larga scala con Azure Machine Learning e registrarli](how-to-train-tensorflow.md).
* [Eseguire il training di modelli di Deep Learning Chainer su larga scala con Azure Machine Learning e registrarli](how-to-train-ml-models.md).

### <a name="endpoints"></a>Endpoint

Un endpoint è la creazione di un'istanza del modello in un servizio Web che può essere ospitato nel cloud o in un modulo IoT per le distribuzioni integrate nei dispositivi.

#### <a name="web-service-endpoint"></a>Endpoint servizio Web

Quando si distribuisce un modello come servizio Web, l'endpoint può essere distribuito in istanze di Azure Container, servizio Azure Kubernetes o FPGA. Il servizio viene creato dal modello, lo script e i file associati. Questi vengono inseriti in un'immagine del contenitore di base che contiene l'ambiente di esecuzione per il modello. L'immagine presenta un endpoint HTTP con bilanciamento del carico che riceve le richieste di punteggio inviate al servizio Web.

Azure aiuta a monitorare il servizio Web raccogliendo i dati di telemetria di Application Insights o, se questa funzionalità è stata abilitata, i dati di telemetria del modello. I dati di telemetria sono accessibili solo per l'utente e archiviati in Application Insights e nelle istanze dell'account di archiviazione.

Se è stata abilitata la scalabilità automatica, la distribuzione verrà ridimensionata automaticamente da Azure.

Per un esempio di distribuzione di un modello come servizio Web, vedere [Distribuire un modello di classificazione delle immagini in Istanze di Azure Container](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-endpoints"></a>Endpoint del modulo IoT

Un endpoint del modulo IoT distribuito è un contenitore Docker che include il modello, lo script/associazione associato e qualsiasi altra dipendenza. Questi moduli vengono distribuiti tramite Azure IoT Edge nei dispositivi perimetrali.

Se il monitoraggio è abilitato, Azure raccoglie i dati di telemetria dal modello all'interno del modulo Azure IoT Edge. I dati di telemetria sono accessibili solo per l'utente e archiviati nell'istanza dell'account di archiviazione.

Azure IoT Edge garantisce che il modulo sia in esecuzione e monitora il dispositivo che lo ospita.


### <a name="compute-instance-preview"></a><a name="compute-instance"></a>Istanza di calcolo (anteprima)

Un'**istanza di calcolo di Azure Machine Learning** (in precedenza macchina virtuale Notebook) è una workstation basata su cloud completamente gestita che include più strumenti e ambienti installati per l'apprendimento automatico. Le istanze di calcolo possono essere usate come destinazione di calcolo per il training e l'inferenza dei processi. Per le attività di grandi dimensioni, [i cluster di elaborazione di Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) con funzionalità di scalabilità a più nodi rappresentano una scelta migliore per la destinazione di calcolo.

Altre informazioni sulle [istanze di calcolo](concept-compute-instance.md).

### <a name="datasets-and-datastores"></a>Set di dati e archivi dati

I **set di dati di Azure Machine Learning** (anteprima) semplificano l'accesso e l'uso dei dati. I set di dati gestiscono i dati in diversi scenari, come il training di modelli e la creazione di pipeline. L'SDK di Azure Machine Learning permette di accedere alle risorse di archiviazione sottostanti, esplorare i dati e gestire il ciclo di vita delle diverse definizioni dei dati.

I set di dati offrono alcuni metodi per lavorare con i dati nei formati più diffusi, ad esempio l'uso di `from_delimited_files()` o `to_pandas_dataframe()`.

Per altre informazioni, vedere [Creare e registrare set di dati di Azure Machine Learning](how-to-create-register-datasets.md).  Per altri esempi d'uso dei set di dati, vedere i [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial).

Un **archivio dati** è un'astrazione delle risorse di archiviazione su un account di archiviazione di Azure. L'archivio dati può usare un contenitore BLOB o una condivisione file di Azure come risorsa di archiviazione back-end. Ogni area di lavoro presenta un archivio dati predefinito, ma è possibile registrare altri archivi dati. Usare l'API di Python SDK o l'interfaccia della riga di comando di Azure Machine Learning per archiviare e recuperare i file dall'archivio dati.

### <a name="compute-targets"></a>Destinazioni di calcolo

Una [destinazione di calcolo](concept-compute-target.md) permette di specificare la risorsa di calcolo usata per eseguire lo script di training o per ospitare la distribuzione del servizio. Questo percorso può essere il computer locale o una risorsa di calcolo basata sul cloud.

Altre informazioni sulle [destinazioni di calcolo disponibili per il training e la distribuzione](concept-compute-target.md).

### <a name="next-steps"></a>Passaggi successivi

Per un'introduzione ad Azure Machine Learning, vedere:

* [Cos'è Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Creare un'area di lavoro di Machine Learning di Azure](how-to-manage-workspace.md)
* [Esercitazione (parte 1): Eseguire il training di un modello](tutorial-train-models-with-aml.md)
