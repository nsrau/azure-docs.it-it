---
title: Architettura & concetti chiave
titleSuffix: Azure Machine Learning
description: Informazioni sull'architettura, i termini, i concetti e i flussi di lavoro che costituiscono Azure Machine Learning.Learn about the architecture, terms, concepts, and workflows that make up Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/17/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: 9f1d23f11cf73680a8861c9f1ac6cbd40ad497a4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257335"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Funzionamento di Azure Machine Learning: Architettura e concettiHow Azure Machine Learning works: Architecture and concepts

Informazioni sull'architettura, i concetti e il flusso di lavoro per Azure Machine Learning.Learn about the architecture, concepts, and workflow for Azure Machine Learning. Il diagramma seguente illustra i componenti principali del servizio e il flusso di lavoro generale durante il suo uso:

![Architettura e flusso di lavoro di Azure Machine Learning](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Flusso di lavoro

Il flusso di lavoro del modello di apprendimento automatico segue in genere questa sequenza:The machine learning model workflow generally follows this sequence:

1. **Eseguire il training**
    + Sviluppare script di training per l'apprendimento automatico in **Python**, **R**o con la finestra di progettazione visiva.
    + Creare e configurare una **destinazione di calcolo**.
    + **Inviare gli script** a una destinazione di calcolo configurata per l'esecuzione in tale ambiente. Durante il training, gli script possono leggere o scrivere nei **datastore**. I log e l'output prodotti durante il training vengono salvati come **esecuzioni** **nell'area di lavoro** e raggruppati in base a **esperimenti.**

1. **Pacchetto** - Dopo aver trovato un'esecuzione soddisfacente, registrare il modello persistente nel **Registro**di sistema del modello .

1. **Convalida** - **interroga l'esperimento** per le metriche registrate dalle esecuzioni correnti e passate. Se le metriche non indicano un risultato desiderato, tornare al passaggio 1 ed eseguire l'iterazione sugli script.

1. **Distribuisci:** sviluppare uno script di assegnazione dei punteggi che usa il modello e Distribuire il modello come servizio Web in Azure o in un **dispositivo perimetrale IoT.Deploy**- Develop a scoring script that uses the model and **Deploy the model** as a web **service** in Azure, or an IoT Edge device .

1. **Monitoraggio:** monitorare la deriva dei **dati** tra il set di dati di training e i dati di inferenza di un modello distribuito. Se necessario, tornare al passaggio 1 per rieseguire il training del modello con nuovi dati di training.

## <a name="tools-for-azure-machine-learning"></a>Strumenti per Azure Machine Learning

Usare questi strumenti per Azure Machine Learning:Use these tools for Azure Machine Learning:

+  Interagire con il servizio in qualsiasi ambiente Python con [Azure Machine Learning SDK per Python.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ Interagire con il servizio in qualsiasi ambiente R con [Azure Machine Learning SDK per R](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Automatizza le attività di apprendimento automatico con l'interfaccia della riga della riga di comando di [Azure Machine Learning.](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli)
+ Usare la finestra di progettazione [di Azure Machine Learning (anteprima)](concept-designer.md) per eseguire i passaggi del flusso di lavoro senza scrivere codice.


> [!NOTE]
> Anche se questo articolo definisce termini e concetti usati da Azure Machine Learning, non definisce termini e concetti per la piattaforma Azure.Although this article defines terms and concepts used by Azure Machine Learning, it does not define terms and concepts for the Azure platform. Per altre informazioni sulla terminologia della piattaforma Azure, vedere il [glossario di Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="glossary"></a>Glossario

* [Attività](#activities)
* [Area di lavoro](#workspaces)
    * [Sperimentazioni](#experiments)
        * [Correre](#runs) 
            * [Configurazione di esecuzione](#run-configurations)
            * [Snapshot](#snapshots)
            * [Tracciamento Git](#github-tracking-and-integration)
            * [Registrazione](#logging)
    * [Pipeline di apprendimento automatico](#ml-pipelines)
    * [Modelli](#models)
        * [Ambienti](#environments)
        * [Script di training](#training-scripts)
        * [Estimatori](#estimators)
    * [Endpoint](#endpoints)
        * [Servizio Web](#web-service-endpoint)
        * [Moduli IoT](#iot-module-endpoints)
    * [Data set di dati & datastore](#datasets-and-datastores)
    * [Obiettivi di calcolo](#compute-targets)

### <a name="activities"></a>attività

Un'attività rappresenta un'operazione a esecuzione prolungata. Le operazioni seguenti sono esempi di attività:

* Creazione o eliminazione di una destinazione di calcolo
* Esecuzione di uno script su una destinazione di calcolo

Le attività possono fornire notifiche tramite l'SDK o l'interfaccia utente Web in modo da poter facilmente monitorare l'avanzamento di queste operazioni.

### <a name="workspaces"></a>Aree di lavoro

[L'area di lavoro](concept-workspace.md) è la risorsa di primo livello per Azure Machine Learning.The workspace is the top-level resource for Azure Machine Learning. Fornisce una posizione centralizzata per lavorare con tutti gli elementi creati quando si usa Azure Machine Learning.It provides a centralized place to work with all the artifacts you create when you use Azure Machine Learning. È possibile condividere un'area di lavoro con altri utenti. Per una descrizione dettagliata delle aree di lavoro, vedere [Che cos'è un'area](concept-workspace.md)di lavoro di Azure Machine Learning? .

### <a name="experiments"></a>Sperimentazioni

Un esperimento è un raggruppamento di più esecuzioni da uno script specifico. Appartiene sempre a un'area di lavoro. Quando si invia un'esecuzione, si fornisce un nome dell'esperimento. Le informazioni per l'esecuzione vengono archiviate in tale esperimento. Se si invia un'esecuzione e si specifica un nome dell'esperimento che non esiste, viene creato automaticamente un nuovo esperimento il nuovo nome specificato.

Per un esempio di utilizzo di un esperimento, vedere [Esercitazione: eseguire il training del primo modello.](tutorial-1st-experiment-sdk-train.md)

### <a name="runs"></a>Esecuzioni

Un'esecuzione è una singola esecuzione di uno script di training. Un esperimento conterrà in genere più esecuzioni.

Azure Machine Learning registra tutte le esecuzioni e archivia le informazioni seguenti nell'esperimento:Azure Machine Learning records all runs and stores the following information in the experiment:

* Metadati sull'esecuzione (timestamp, durata e così via)
* Metriche registrate dallo script
* File di output raccolti automaticamente dall'esperimento o caricati in modo esplicito dall'utente
* Uno snapshot della directory che contiene gli script, precedente all'esecuzione

Un'esecuzione viene generata quando si invia uno script per eseguire il training di un modello. Un'esecuzione può avere zero o più esecuzioni figlio. L'esecuzione di primo livello, ad esempio, potrebbe avere due esecuzioni figlio, ognuna delle quali può avere a sua volte le proprie esecuzioni figlio.

### <a name="run-configurations"></a>Eseguire le configurazioni

Una configurazione di esecuzione è un set di istruzioni che definisce come uno script deve essere eseguito in una destinazione di calcolo specificata. La configurazione include un'ampia serie di definizioni di comportamento, ad esempio se usare un ambiente Python esistente oppure un ambiente Conda compilato a partire da una specifica.

Una configurazione di esecuzione può essere salvata in modo permanente in un file all'interno della directory che contiene lo script di training, oppure costruita come un oggetto in memoria e usata per inviare un'esecuzione.

Per degli esempi di configurazioni di esecuzione, vedere [Selezionare e usare una destinazione di calcolo per eseguire il training del modello](how-to-set-up-training-targets.md).

### <a name="snapshots"></a>Snapshot

Quando si invia un'esecuzione, Azure Machine Learning consente di comprimere la directory che contiene lo script come file ZIP e lo invia alla destinazione di calcolo. Il file ZIP viene quindi estratto e lo script eseguito in questa posizione. Azure Machine Learning archivia inoltre il file ZIP come snapshot come parte del record di esecuzione. Chiunque abbia accesso all'area di lavoro può esplorare un record di esecuzione e scaricare lo snapshot.

> [!NOTE]
> Per evitare che i file non necessari vengano inclusi nello snapshot, creare un file ignora (.gitignore o .amlignore). Inserire il file nella directory Snapshot e aggiungere i nomi dei file da ignorare. Il file .amlignore utilizza la stessa sintassi e gli stessi [modelli del file .gitignore](https://git-scm.com/docs/gitignore). Se entrambi i file esistono, il file con estensione amlignore ha la precedenza.

### <a name="github-tracking-and-integration"></a>Monitoraggio e integrazione di GitHub

Quando si avvia un'esecuzione di training in cui la directory di origine è un repository Git locale, le informazioni sul repository vengono archiviate nella cronologia di esecuzione. Questa operazione funziona con le esecuzioni inviate tramite uno stimatore, una pipeline di ML o un'esecuzione di script. Funziona anche per le esecuzioni inviate dall'SDK o dall'interfaccia della riga di comando di Machine Learning.

Per altre informazioni, vedere [Integrazione Git per Azure Machine Learning.For](concept-train-model-git-integration.md)more information, see Git integration for Azure Machine Learning .

### <a name="logging"></a>Registrazione

Quando si sviluppa la soluzione, usare Python SDK di Azure Machine Learning nello script di Python per registrare metriche arbitrarie. Dopo l'esecuzione, eseguire query sulle metriche per determinare se l'esecuzione ha prodotto il modello che si desidera distribuire.

### <a name="ml-pipelines"></a>Pipeline di Machine Learning

Le pipeline di Machine Learning consentono di creare e gestire i flussi di lavoro in cui sono unite le varie fasi dell'apprendimento automatico. Ad esempio, una pipeline potrebbe includere la preparazione dei dati, il training del modello, la distribuzione del modello e le fasi di inferenza/punteggio. Ogni fase può includere più passaggi, ciascuno dei quali può essere eseguito in modo automatico in varie destinazioni di calcolo. 

I passaggi della pipeline sono riutilizzabili e possono essere eseguiti senza rieseguire i passaggi precedenti se l'output di tali passaggi non è stato modificato. Ad esempio, è possibile rieseguire il training di un modello senza eseguire nuovamente costosi passaggi di preparazione dei dati se i dati non sono stati modificati. Le pipeline consentono inoltre ai data scientist di collaborare mentre lavorano su aree separate di un flusso di lavoro di apprendimento automatico.

Per altre informazioni sulle pipeline di apprendimento automatico, vedere [Pipeline e Azure Machine Learning](concept-ml-pipelines.md).

### <a name="models"></a>Modelli

Nella forma più semplice, un modello è un frammento di codice che accetta un input e produce output. La creazione di un modello di Machine Learning prevede di selezionare un algoritmo, fornirgli dei dati e ottimizzare gli iperparametri. Il training è un processo iterativo che produce un modello con training, che racchiude ciò che il modello ha appreso durante il processo di training.

Un modello è prodotto da un'esecuzione in Azure Machine Learning. È anche possibile usare un modello il cui training sia stato eseguito all'esterno di Azure Machine Learning. È possibile registrare un modello in un'area di lavoro di Azure Machine Learning.You can register a model in an Azure Machine Learning workspace.

Azure Machine Learning è indipendente dal framework. Quando si crea un modello, è possibile usare qualsiasi framework di apprendimento automatico popolare, ad esempio Scikit-learn, XGBoost, PyTorch, TensorFlow e Chainer.When you create a model, you can use any popular machine learning framework, such as Scikit-learn, XGBoost, PyTorch, TensorFlow, and Chainer.

Per un esempio di training di un modello usando Scikit-learn e uno stimatore, vedere [Esercitazione: eseguire](tutorial-train-models-with-aml.md)il training di un modello di classificazione delle immagini con Azure Machine Learning.For an example of training a model using Scikit-learn and an estimator, see Tutorial: Train an image classification model with Azure Machine Learning .

Il **registro modelli** tiene traccia di tutti i modelli nell'area di lavoro di Azure Machine Learning.The model registry keeps track of all the models in your Azure Machine Learning workspace.

I modelli vengono identificati dal nome e dalla versione. Ogni volta che si registra un modello con lo stesso nome di uno esistente, il registro presuppone che si tratti di una nuova versione. La versione viene incrementata e il nuovo modello registrato con lo stesso nome.

Quando si registra il modello, è possibile specificare altri tag di metadati e quindi usarli durante la ricerca di modelli.

> [!TIP]
> Un modello registrato è un contenitore logico per uno o più file che costituiscono il modello. Ad esempio, se si dispone di un modello archiviato in più file, è possibile registrarli come un singolo modello nell'area di lavoro di Azure Machine Learning.For example, if you have a model that is stored in multiple files, you can register them as a single model in your Azure Machine Learning workspace. Dopo la registrazione, è possibile scaricare o distribuire il modello registrato e ricevere tutti i file registrati.

Non è possibile eliminare un modello registrato utilizzato da una distribuzione attiva.

Per un esempio di registrazione di un modello, vedere [Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="environments"></a>Ambienti

Gli ambienti Azure ML vengono usati per specificare la configurazione (Docker / Python / Spark / ecc.) usata per creare un ambiente riproducibile per la preparazione dei dati, il training dei modelli e la gestione dei modelli. Sono entità gestite e con controllo delle versioni all'interno dell'area di lavoro di Azure Machine Learning che consentono flussi di lavoro di Machine Learning riproducibili, controllabili e portatili tra diversi obiettivi di calcolo.

È possibile usare un oggetto ambiente nel calcolo locale per sviluppare lo script di training, riutilizzare lo stesso ambiente in Azure Machine Learning Compute per il training del modello su larga scala e persino distribuire il modello con lo stesso ambiente. 

[Informazioni su come creare e gestire un ambiente ML riutilizzabile](how-to-use-environments.md) per la formazione e l'inferenza.

### <a name="training-scripts"></a>Script di training

Per eseguire il training di un modello si specifica la directory che contiene lo script di training e i file associati. Si specifica inoltre un nome per l'esperimento, che viene usato per archiviare le informazioni raccolte durante il training. Durante il training l'intera directory viene copiata nell'ambiente di training (destinazione di calcolo) e lo script specificato per la configurazione di esecuzione viene avviato. Uno snapshot della directory viene archiviato anche nell'esperimento nell'area di lavoro.

Per un esempio, vedere [Esercitazione: eseguire il training di un modello](tutorial-train-models-with-aml.md)di classificazione delle immagini con Azure Machine Learning.For an example, see Tutorial: Train an image classification model with Azure Machine Learning .

### <a name="estimators"></a>Estimatori

Per facilitare il training del modello con framework più diffusi, la classe di stima consente di costruire facilmente configurazioni di esecuzione. È possibile creare e usare un [Stimatore](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) generico per inviare script di formazione che utilizzano qualsiasi framework di apprendimento scelto (ad esempio scikit-learn).

Per le attività PyTorch, TensorFlow e Chainer, Azure Machine Learning fornisce anche gli strumenti di funzione [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) per semplificare l'utilizzo di questi framework.

Per altre informazioni, vedere gli articoli seguenti:

* [Treno modelli ML con stimatori](how-to-train-ml-models.md).
* [Addestrare](how-to-train-pytorch.md)i modelli di deep learning Pytorch su larga scala con Azure Machine Learning .
* [Addestrare e registrare i modelli TensorFlow su larga scala con Azure Machine Learning](how-to-train-tensorflow.md).
* [Eseguire il training e la registrazione di modelli Chainer su larga scala con Azure Machine Learning](how-to-train-ml-models.md).

### <a name="endpoints"></a>Endpoint

Un endpoint è un'istanza del modello in un servizio Web che può essere ospitato nel cloud o un modulo IoT per le distribuzioni di dispositivi integrati.

#### <a name="web-service-endpoint"></a>Endpoint del servizio Web

Quando si distribuisce un modello come servizio Web, l'endpoint può essere distribuito in istanze del contenitore di Azure, nel servizio Azure Kubernetes o in FPGA. Il servizio viene creato dal modello, dallo script e dai file associati. Questi vengono inseriti in un'immagine del contenitore di base che contiene l'ambiente di esecuzione per il modello. L'immagine presenta un endpoint HTTP con bilanciamento del carico che riceve le richieste di punteggio inviate al servizio Web.

Azure consente di monitorare il servizio Web raccogliendo dati di telemetria di Application Insights o dati di telemetria del modello, se si è scelto di abilitare questa funzionalità. I dati di telemetria sono accessibili solo per l'utente e archiviati in Application Insights e nelle istanze dell'account di archiviazione.

Se è stata abilitata la scalabilità automatica, la distribuzione verrà ridimensionata automaticamente da Azure.

Per un esempio di distribuzione di un modello come servizio Web, vedere Distribuire un modello di [classificazione delle immagini nelle istanze del contenitore](tutorial-deploy-models-with-aml.md)di Azure.For an example of deploying a model as a web service , see Deploy an image classification model in Azure Container Instances.

#### <a name="iot-module-endpoints"></a>Endpoint del modulo IoT

Un endpoint del modulo IoT distribuito è un contenitore Docker che include il modello e lo script o l'applicazione associati e le eventuali dipendenze aggiuntive. È possibile distribuire questi moduli usando Azure IoT Edge nei dispositivi perimetrali.

Se il monitoraggio è abilitato, Azure raccoglie i dati di telemetria dal modello all'interno del modulo Azure IoT Edge. I dati di telemetria sono accessibili solo per l'utente e archiviati nell'istanza dell'account di archiviazione.

Azure IoT Edge garantisce che il modulo sia in esecuzione e monitora il dispositivo che lo ospita.


### <a name="compute-instance-preview"></a><a name="compute-instance"></a>Istanza di calcolo (anteprima)Compute instance (preview)

Un'istanza di **calcolo** di Azure Machine Learning (precedentemente VM blocco appunti) è una workstation basata su cloud completamente gestita che include più strumenti e ambienti installati per l'apprendimento automatico. Le istanze di calcolo possono essere usate come destinazione di calcolo per i processi di training e inferenza. Per le attività di grandi dimensioni, i cluster di [calcolo](how-to-set-up-training-targets.md#amlcompute) di Azure Machine Learning con funzionalità di scalabilità multinodo sono una scelta di destinazione di calcolo migliore.

Ulteriori informazioni sulle istanze di [calcolo](concept-compute-instance.md).

### <a name="datasets-and-datastores"></a>Set di dati e datastore

**I set** di dati di Azure Machine Learning (anteprima) semplificano l'accesso e l'utilizzo dei dati. I set di dati gestiscono i dati in vari scenari, ad esempio il training del modello e la creazione di pipeline. Usando Azure Machine Learning SDK, è possibile accedere all'archiviazione sottostante, esplorare i dati e gestire il ciclo di vita di diverse definizioni di Dataset.Using the Azure Machine Learning SDK, you can access underlying storage, explore data, and manage the life cycle of different Dataset definitions.

I set di dati forniscono metodi per `from_delimited_files()` l'utilizzo dei dati in formati diffusi, ad esempio l'utilizzo o `to_pandas_dataframe()`.

Per altre informazioni, vedere Creare e registrare set di dati di Azure Machine Learning.For more information, see [Create and register Azure Machine Learning Datasets](how-to-create-register-datasets.md).  Per altri esempi sull'uso dei dataset, vedere [i blocchi appunti di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets).

Un **archivio dati** è un'astrazione di archiviazione su un account di archiviazione di Azure.A datastore is a storage abstraction over an Azure storage account. L'archivio dati può usare un contenitore BLOB o una condivisione file di Azure come risorsa di archiviazione back-end. Ogni area di lavoro presenta un archivio dati predefinito, ma è possibile registrare altri archivi dati. Usare l'API di Python SDK o l'interfaccia della riga di comando di Azure Machine Learning per archiviare e recuperare i file dall'archivio dati.

### <a name="compute-targets"></a>Destinazioni di calcolo

Una destinazione di [calcolo](concept-compute-target.md) consente di specificare la risorsa di calcolo in cui si esegue lo script di training o si ospita la distribuzione del servizio. Questo percorso può essere il computer locale o una risorsa di elaborazione basata su cloud.

Ulteriori informazioni sulle destinazioni di [calcolo disponibili per](concept-compute-target.md)la formazione e la distribuzione .

### <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare Azure Machine Learning, vedere:To get started with Azure Machine Learning, see:

* [Informazioni su Azure Machine Learning](overview-what-is-azure-ml.md)
* [Creare un'area di lavoro di Azure Machine LearningCreate an Azure Machine Learning workspace](how-to-manage-workspace.md)
* [Esercitazione (parte 1): Eseguire il training di un modelloTutorial (Part 1): Train a model](tutorial-train-models-with-aml.md)
