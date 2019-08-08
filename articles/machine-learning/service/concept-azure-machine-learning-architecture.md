---
title: Architettura & concetti chiave
titleSuffix: Azure Machine Learning service
description: Informazioni sull'architettura, i termini, i concetti e il flusso di lavoro che costituiscono il servizio Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 07/12/2019
ms.custom: seodec18
ms.openlocfilehash: e6f6c41e5de4f4a053748dfb08dc57e8acac32e5
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848230"
---
# <a name="how-azure-machine-learning-service-works-architecture-and-concepts"></a>Come funziona il servizio Azure Machine Learning: Architettura e concetti

Informazioni sull'architettura, i concetti e il flusso di lavoro per il servizio Azure Machine Learning. Il diagramma seguente illustra i componenti principali del servizio e il flusso di lavoro generale durante il suo uso:

![Architettura e flusso di lavoro del servizio Azure Machine Learning](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Flusso di lavoro

Il flusso di lavoro del modello di Machine Learning segue generalmente questa sequenza:

1. **Treno**
    + Sviluppare script di training di Machine Learning in **Python** o con l'interfaccia visiva.
    + Creare e configurare una **destinazione di calcolo**.
    + **Inviare gli script** alla destinazione di calcolo configurata per l'esecuzione in tale ambiente. Durante il training, gli script possono leggere o scrivere nell'**archivio dati**. I record di esecuzione inoltre vengono salvati come **esecuzioni** nell'**area di lavoro** e raggruppati negli **esperimenti**.

1. **Pacchetto** : dopo che è stata rilevata un'esecuzione soddisfacente, registrare il modello permanente nel **Registro di sistema del modello**.

1.  - Convalidare**la query nell'esperimento per le** metriche registrate dalle esecuzioni correnti e passate. Se le metriche non indicano un risultato desiderato, tornare al passaggio 1 ed eseguire l'iterazione sugli script.

1. **Distribuisci** : sviluppa uno script di assegnazione dei punteggi che usa il modello e **distribuisce il modello** come **servizio Web** in Azure o in un **dispositivo IOT Edge**.

1. **Monitoraggio** : monitorare la **tendenza dei dati** tra il set di dati di training e i dati di inferenza di un modello distribuito. Quando necessario, eseguire il loopback al passaggio 1 per ripetere il training del modello con i nuovi dati di training.

## <a name="tools-for-azure-machine-learning"></a>Strumenti per Azure Machine Learning

Usare questi strumenti per Azure Machine Learning:

+  Interagire con il servizio in qualsiasi ambiente Python con [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Automatizzare le attività di Machine Learning con l'interfaccia della riga di comando [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli).
+ Scrivere codice in Visual Studio Code con [Azure Machine Learning estensione vs code](how-to-vscode-tools.md)
+ Usare l' [interfaccia visiva (anteprima) per il servizio Azure Machine Learning](ui-concept-visual-interface.md) per eseguire i passaggi del flusso di lavoro senza scrivere codice.

## <a name="glossary-of-concepts"></a>Glossario dei concetti

+ <a href="#workspaces">Area</a>
+ <a href="#experiments">Sperimentazioni</a>
+ <a href="#models">Modelli</a>
+ <a href="#run-configurations">Configurazione di esecuzione</a>
+ <a href="#datasets-and-datastores">DataSet & archivi dati</a>
+ <a href="#compute-targets">Destinazioni di calcolo</a>
+ <a href="#training-scripts">Script di training</a>
+ <a href="#runs">Run</a>
+ <a href="#github-tracking-and-integration">Rilevamento git</a>
+ <a href="#snapshots">Snapshot</a>
+ <a href="#activities">Attività</a>
+ <a href="#images">Immagine</a>
+ <a href="#deployment">Distribuzione</a>
+ <a href="#web-service-deployments">Servizi Web</a>
+ <a href="#iot-module-deployments">Moduli delle cose</a>
+ <a href="#ml-pipelines">Pipeline ML</a>
+ <a href="#logging">registrazione</a>

> [!NOTE]
> Sebbene in questo articolo vengano definiti termini e concetti usati dal servizio Azure Machine Learning, non vengono definiti quelli relativi alla piattaforma di Azure. Per altre informazioni sulla terminologia della piattaforma Azure, vedere il [glossario di Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).


### <a name="workspaces"></a>Aree di lavoro

[L'area di lavoro](concept-workspace.md) è la risorsa di primo livello per il servizio Azure Machine Learning. Fornisce una posizione centralizzata per lavorare con tutti gli artefatti creati durante l'uso del servizio Azure Machine Learning.

Nel seguente diagramma viene illustrata una tassonomia dell'area di lavoro:

[![Tassonomia dell'area di lavoro](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

Per ulteriori informazioni sulle aree di lavoro, vedere [che cos'è un'area di lavoro Azure Machine Learning?](concept-workspace.md).

### <a name="experiments"></a>Esperimenti

Un esperimento è un raggruppamento di più esecuzioni da uno script specifico. Appartiene sempre a un'area di lavoro. Quando si invia un'esecuzione, si fornisce un nome dell'esperimento. Le informazioni per l'esecuzione vengono archiviate in tale esperimento. Se si invia un'esecuzione e si specifica un nome dell'esperimento che non esiste, viene creato automaticamente un nuovo esperimento il nuovo nome specificato.

Per un esempio dell'uso di un esperimento, [vedere Esercitazione: Eseguire il training del](tutorial-1st-experiment-sdk-train.md)primo modello.

### <a name="models"></a>Modelli

Nella forma più semplice, un modello è un frammento di codice che accetta un input e produce output. La creazione di un modello di Machine Learning prevede di selezionare un algoritmo, fornirgli dei dati e ottimizzare gli iperparametri. Il training è un processo iterativo che produce un modello con training, che racchiude ciò che il modello ha appreso durante il processo di training.

Un modello è prodotto da un'esecuzione in Azure Machine Learning. È anche possibile usare un modello il cui training sia stato eseguito all'esterno di Azure Machine Learning. È possibile registrare un modello in un'area di lavoro del servizio Azure Machine Learning.

Il servizio Azure Machine Learning è indipendente dal framework. Quando si crea un modello, è possibile usare qualsiasi framework di Machine Learning comune, ad esempio Scikit-learn, XGBoost, PyTorch, TensorFlow e Chainer.

Per un esempio di training di un modello, [vedere Esercitazione: Eseguire il training di un modello di classificazione delle immagini con il servizio Azure Machine Learning](tutorial-train-models-with-aml.md).

Il **Registro di sistema del modello** tiene traccia di tutti i modelli nell'area di lavoro del servizio Azure Machine Learning.

I modelli vengono identificati dal nome e dalla versione. Ogni volta che si registra un modello con lo stesso nome di uno esistente, il registro presuppone che si tratti di una nuova versione. La versione viene incrementata e il nuovo modello registrato con lo stesso nome.

Quando si registra il modello, è possibile specificare altri tag di metadati e quindi usarli durante la ricerca di modelli.

> [!TIP]
> Un modello registrato è un contenitore logico per uno o più file che costituiscono il modello. Se, ad esempio, si dispone di un modello archiviato in più file, è possibile registrarli come singolo modello nell'area di lavoro Azure Machine Learning. Dopo la registrazione, è possibile scaricare o distribuire il modello registrato e ricevere tutti i file registrati.

Non è possibile eliminare un modello registrato utilizzato da una distribuzione attiva.

Per un esempio di registrazione di un modello, vedere [Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="run-configurations"></a>Configurazioni di esecuzione

Una configurazione di esecuzione è un set di istruzioni che definisce come uno script deve essere eseguito in una destinazione di calcolo specificata. La configurazione include un'ampia serie di definizioni di comportamento, ad esempio se usare un ambiente Python esistente oppure un ambiente Conda compilato a partire da una specifica.

Una configurazione di esecuzione può essere salvata in modo permanente in un file all'interno della directory che contiene lo script di training, oppure costruita come un oggetto in memoria e usata per inviare un'esecuzione.

Per degli esempi di configurazioni di esecuzione, vedere [Selezionare e usare una destinazione di calcolo per eseguire il training del modello](how-to-set-up-training-targets.md).

### <a name="datasets-and-datastores"></a>DataSet e archivi dati

**Set di impostazioni Azure Machine Learning** (anteprima) semplifica l'accesso e l'utilizzo dei dati. I set di dati gestiscono i dati in diversi scenari, come il training del modello e la creazione della pipeline. Con Azure Machine Learning SDK è possibile accedere all'archiviazione sottostante, esplorare e preparare i dati, gestire il ciclo di vita delle diverse definizioni del set di dati e confrontare i set di dati usati nel training e nell'ambiente di produzione.

DataSets fornisce metodi per l'utilizzo dei dati nei formati più diffusi, ad `from_delimited_files()` esempio `to_pandas_dataframe()`l'utilizzo di o.

Per altre informazioni, vedere [creare e registrare Azure Machine Learning set](how-to-create-register-datasets.md)di dati.  Per altri esempi di uso dei set di impostazioni, vedere i [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/work-with-data/datasets).

Un **archivio dati** è un'astrazione di archiviazione in un account di archiviazione di Azure. L'archivio dati può usare un contenitore BLOB o una condivisione file di Azure come risorsa di archiviazione back-end. Ogni area di lavoro presenta un archivio dati predefinito, ma è possibile registrare altri archivi dati. Usare l'API di Python SDK o l'interfaccia della riga di comando di Azure Machine Learning per archiviare e recuperare i file dall'archivio dati.

### <a name="compute-targets"></a>Destinazioni di calcolo

Una [destinazione di calcolo](concept-compute-target.md) consente di specificare la risorsa di calcolo in cui si esegue lo script di training o si ospita la distribuzione del servizio. Questo percorso può essere il computer locale o una risorsa di calcolo basata sul cloud. Le destinazioni di calcolo facilitano la modifica dell'ambiente di calcolo senza modificare il codice.

Altre informazioni sulle [destinazioni di calcolo disponibili per il training e la distribuzione](concept-compute-target.md).

### <a name="training-scripts"></a>Script di training

Per eseguire il training di un modello si specifica la directory che contiene lo script di training e i file associati. Si specifica inoltre un nome per l'esperimento, che viene usato per archiviare le informazioni raccolte durante il training. Durante il training l'intera directory viene copiata nell'ambiente di training (destinazione di calcolo) e lo script specificato per la configurazione di esecuzione viene avviato. Uno snapshot della directory viene archiviato anche nell'esperimento nell'area di lavoro.

Per un esempio, vedere [Esercitazione: Eseguire il training di un modello di classificazione delle immagini con il servizio Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="runs"></a>Esecuzioni

Un'esecuzione è un record contenente le seguenti informazioni:

* Metadati sull'esecuzione (timestamp, durata e così via)
* Metriche registrate dallo script
* File di output raccolti automaticamente dall'esperimento o caricati in modo esplicito dall'utente
* Uno snapshot della directory che contiene gli script, precedente all'esecuzione

Un'esecuzione viene generata quando si invia uno script per eseguire il training di un modello. Un'esecuzione può avere zero o più esecuzioni figlio. L'esecuzione di primo livello, ad esempio, potrebbe avere due esecuzioni figlio, ognuna delle quali può avere a sua volte le proprie esecuzioni figlio.


### <a name="github-tracking-and-integration"></a>Rilevamento e integrazione di GitHub

Quando si avvia un'esecuzione di training in cui la directory di origine è un repository git locale, le informazioni sul repository vengono archiviate nella cronologia di esecuzione. Ad esempio, l'ID commit corrente per il repository viene registrato come parte della cronologia. Funziona con le esecuzioni inviate usando un estimatore, una pipeline ML o un'esecuzione di script. Funziona anche per le esecuzioni inviate dall'SDK o Machine Learning interfaccia della riga di comando.

### <a name="snapshots"></a>Snapshot

Quando si invia un'esecuzione, Azure Machine Learning consente di comprimere la directory che contiene lo script come file ZIP e lo invia alla destinazione di calcolo. Il file ZIP viene quindi estratto e lo script eseguito in questa posizione. Azure Machine Learning archivia inoltre il file ZIP come snapshot come parte del record di esecuzione. Chiunque abbia accesso all'area di lavoro può esplorare un record di esecuzione e scaricare lo snapshot.

> [!NOTE]
> Per evitare che i file non necessari vengano inclusi nello snapshot, creare un file ignore (con estensione gitignore o amlignore). Inserire questo file nella directory snapshot e aggiungere i nomi file da ignorare al suo interno. Il file con estensione amlignore utilizza la stessa [sintassi e gli stessi criteri del file. gitignore](https://git-scm.com/docs/gitignore). Se sono presenti entrambi i file, il file con estensione amlignore avrà la precedenza.

### <a name="activities"></a>Attività

Un'attività rappresenta un'operazione a esecuzione prolungata. Le operazioni seguenti sono esempi di attività:

* Creazione o eliminazione di una destinazione di calcolo
* Esecuzione di uno script su una destinazione di calcolo

Le attività possono fornire notifiche tramite l'SDK o l'interfaccia utente Web in modo da poter facilmente monitorare l'avanzamento di queste operazioni.

### <a name="images"></a>Immagini

Le immagini consentono di distribuire in modo affidabile un modello e tutti i componenti necessari per il suo utilizzo. Un'immagine contiene i seguenti elementi:

* Un modello.
* Uno script di punteggio o applicazione. Questo script viene usato per passare l'input al modello e per restituire l'output del modello.
* Le dipendenze necessarie per il modello, lo script di punteggio o l'applicazione. Ad esempio, è possibile includere un file di ambiente Conda che elenca le dipendenze dei pacchetti Python.

Azure Machine Learning può creare due tipi di immagine:

* **Immagine FPGA**: usata durante la distribuzione in un dispositivo FPGA (Field-Programmable Gate Array) in Azure.
* **Immagine docker**: usata durante la distribuzione in destinazioni di calcolo diverse da FPGA. Alcuni esempi sono le Istanze di Azure Container e il servizio Azure Kubernetes.

Il servizio Azure Machine Learning fornisce un'immagine di base, che viene utilizzata per impostazione predefinita. È anche possibile fornire immagini personalizzate.

### <a name="image-registry"></a>Registro delle immagini

Le immagini vengono catalogate nel **Registro immagini** dell'area di lavoro. È possibile specificare tag di metadati aggiuntivi quando si crea l'immagine, in modo che sia possibile eseguire una query per trovare l'immagine in un secondo momento.

Per un esempio di creazione di un'immagine, vedere [Distribuire un modello di classificazione delle immagini in Istanze di Azure Container](tutorial-deploy-models-with-aml.md).

Per un esempio di distribuzione di un modello usando un'immagine personalizzata, vedere [come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md).

### <a name="deployment"></a>Distribuzione

Una distribuzione è una creazione di istanze del modello in un servizio Web che può essere ospitato nel cloud o un modulo per le distribuzioni di dispositivi integrati.

#### <a name="web-service-deployments"></a>Distribuzioni di servizi Web

Un servizio Web distribuito può usare le Istanze di Azure Container, il servizio Azure Kubernetes o le FPGA. Il servizio viene creato dal modello, dallo script e dai file associati. Questi vengono incapsulati in un'immagine, che fornisce l'ambiente di runtime per il servizio Web. L'immagine presenta un endpoint HTTP con bilanciamento del carico che riceve le richieste di punteggio inviate al servizio Web.

Azure aiuta a monitorare la distribuzione del servizio Web raccogliendo i dati di telemetria di Application Insights o, se questa funzionalità è stata abilitata, i dati di telemetria del modello. I dati di telemetria sono accessibili solo per l'utente e archiviati in Application Insights e nelle istanze dell'account di archiviazione.

Se è stata abilitata la scalabilità automatica, la distribuzione verrà ridimensionata automaticamente da Azure.

Per un esempio di distribuzione di un modello come servizio Web, vedere [Distribuire un modello di classificazione delle immagini in Istanze di Azure Container](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-deployments"></a>Distribuzioni del modulo Internet

Un modulo IoT distribuito è un contenitore Docker che include il modello, lo script/associazione associato e qualsiasi altra dipendenza. Questi moduli vengono distribuiti usando Azure IoT Edge nei dispositivi perimetrali.

Se il monitoraggio è abilitato, Azure raccoglie i dati di telemetria dal modello all'interno del modulo Azure IoT Edge. I dati di telemetria sono accessibili solo per l'utente e archiviati nell'istanza dell'account di archiviazione.

Azure IoT Edge garantisce che il modulo sia in esecuzione e monitora il dispositivo che lo ospita.

### <a name="ml-pipelines"></a>Pipeline di Machine Learning

Le pipeline di Machine Learning consentono di creare e gestire i flussi di lavoro in cui sono unite le varie fasi dell'apprendimento automatico. Una pipeline, ad esempio, può includere la preparazione dei dati, il training del modello, la distribuzione del modello e le fasi di inferenza/punteggio. Ogni fase può includere più passaggi, ciascuno dei quali può essere eseguito in modo automatico in varie destinazioni di calcolo. 

I passaggi della pipeline sono riutilizzabili e possono essere eseguiti senza eseguire nuovamente i passaggi successivi se l'output del passaggio non è stato modificato. Ad esempio, è possibile ripetere il training di un modello senza rieseguire i passaggi di preparazione dei dati costosi se i dati non sono stati modificati. Le pipeline consentono inoltre ai data scientist di collaborare mentre lavorano su aree separate di un flusso di lavoro di machine learning.

Per altre informazioni sulle pipeline di apprendimento automatico, vedere [Pipeline e Azure Machine Learning](concept-ml-pipelines.md).

### <a name="logging"></a>Registrazione

Quando si sviluppa la soluzione, usare Python SDK di Azure Machine Learning nello script di Python per registrare metriche arbitrarie. Dopo l'esecuzione, eseguire query sulle metriche per determinare se l'esecuzione ha prodotto il modello che si desidera distribuire.

### <a name="next-steps"></a>Passaggi successivi

Per iniziare con il servizio Azure Machine Learning, vedere:

* [Informazioni sul servizio Azure Machine Learning.](overview-what-is-azure-ml.md)
* [Creare un'area di lavoro del servizio Azure Machine Learning](how-to-manage-workspace.md)
* [Esercitazione (parte 1): Eseguire il training di un modello](tutorial-train-models-with-aml.md)
