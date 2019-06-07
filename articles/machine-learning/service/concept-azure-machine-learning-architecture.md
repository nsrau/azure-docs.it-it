---
title: Architettura e concetti chiave
titleSuffix: Azure Machine Learning service
description: Scopri l'architettura, condizioni, concetti e del flusso di lavoro che costituiscono il servizio di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 04/15/2019
ms.custom: seodec18
ms.openlocfilehash: f369f899d4a383205ad124e4fcd8dabf9f92f63f
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66753183"
---
# <a name="how-azure-machine-learning-service-works-architecture-and-concepts"></a>Come funziona il servizio Azure Machine Learning: Architettura e concetti

Informazioni sui concetti, architettura e flusso di lavoro per il servizio di Azure Machine Learning. Il diagramma seguente illustra i componenti principali del servizio e il flusso di lavoro generale durante il suo uso:

[![Architettura e flusso di lavoro del servizio Azure Machine Learning](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

## <a name="workflow"></a>Flusso di lavoro

Il flusso di lavoro di machine learning in genere segue questa sequenza:

1. Sviluppare script di training di apprendimento **Python** o con l'interfaccia visiva.
1. Creare e configurare una **destinazione di calcolo**.
1. **Inviare gli script** alla destinazione di calcolo configurata per l'esecuzione in tale ambiente. Durante il training, gli script possono leggere o scrivere nell'**archivio dati**. I record di esecuzione inoltre vengono salvati come **esecuzioni** nell'**area di lavoro** e raggruppati negli **esperimenti**.
1. **Eseguire una query sull'esperimento** per le metriche registrate dalle esecuzioni correnti e precedenti. Se le metriche non indicano un risultato desiderato, tornare al passaggio 1 ed eseguire l'iterazione sugli script.
1. Una volta trovata un'esecuzione soddisfacente, registrare il modello persistente nel **registro di modello**.
1. Sviluppare uno script di assegnazione dei punteggi che utilizza il modello e **distribuire il modello** come una **servizio web** in Azure o in un **dispositivo IoT Edge**.

Eseguire questi passaggi con una qualsiasi delle operazioni seguenti:
+ [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ [Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)
+ [Estensione di Azure Machine Learning Visual Studio Code](how-to-vscode-tools.md)
+  Il [interfaccia visiva (anteprima) per il servizio di Azure Machine Learning](ui-concept-visual-interface.md)

> [!NOTE]
> Sebbene in questo articolo vengano definiti termini e concetti usati dal servizio Azure Machine Learning, non vengono definiti quelli relativi alla piattaforma di Azure. Per altre informazioni sulla terminologia della piattaforma Azure, vedere il [glossario di Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="workspace"></a>Area di lavoro

[L'area di lavoro](concept-workspace.md) è la risorsa di primo livello per il servizio di Azure Machine Learning. Fornisce una posizione centralizzata per lavorare con tutti gli artefatti creati durante l'uso del servizio Azure Machine Learning.

Nel seguente diagramma viene illustrata una tassonomia dell'area di lavoro:

[![Tassonomia dell'area di lavoro](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

Per altre informazioni sulle aree di lavoro, vedere [che cos'è un'area di lavoro di Azure Machine Learning?](concept-workspace.md).

## <a name="experiment"></a>Esperimento

Un esperimento è un raggruppamento di più esecuzioni da uno script specifico. Appartiene sempre a un'area di lavoro. Quando si invia un'esecuzione, si fornisce un nome dell'esperimento. Le informazioni per l'esecuzione vengono archiviate in tale esperimento. Se si invia un'esecuzione e si specifica un nome dell'esperimento che non esiste, viene creato automaticamente un nuovo esperimento il nuovo nome specificato.

Per un esempio d'uso di un esperimento, vedere [Avvio rapido: Introduzione al servizio Azure Machine Learning](quickstart-run-cloud-notebook.md).

## <a name="model"></a>Modello

Nella forma più semplice, un modello è un frammento di codice che accetta un input e produce output. La creazione di un modello di Machine Learning prevede di selezionare un algoritmo, fornirgli dei dati e ottimizzare gli iperparametri. Il training è un processo iterativo che produce un modello con training, che racchiude ciò che il modello ha appreso durante il processo di training.

Un modello è prodotto da un'esecuzione in Azure Machine Learning. È anche possibile usare un modello il cui training sia stato eseguito all'esterno di Azure Machine Learning. È possibile registrare un modello in un'area di lavoro del servizio Azure Machine Learning.

Il servizio Azure Machine Learning è indipendente dal framework. Quando si crea un modello, è possibile usare qualsiasi framework di apprendimento comuni, come Scikit-learn, XGBoost, PyTorch, TensorFlow e Chainer.

Per un esempio di training di un modello, vedere [esercitazione: Eseguire il training di un modello di classificazione delle immagini con il servizio Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="model-registry"></a>Registro di modello

Il registro di modello tiene traccia di tutti i modelli nell'area di lavoro del servizio di Azure Machine Learning.

I modelli vengono identificati dal nome e dalla versione. Ogni volta che si registra un modello con lo stesso nome di uno esistente, il registro presuppone che si tratti di una nuova versione. La versione viene incrementata e il nuovo modello registrato con lo stesso nome.

Quando si registra il modello, è possibile specificare altri tag di metadati e quindi usarli durante la ricerca di modelli.

Non è possibile eliminare i modelli che sono utilizzati da una distribuzione attiva.

Per un esempio di registrazione di un modello, vedere [Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="run-configuration"></a>Configurazione di esecuzione

Una configurazione di esecuzione è un set di istruzioni che definisce come uno script deve essere eseguito in una destinazione di calcolo specificata. La configurazione include un'ampia serie di definizioni di comportamento, ad esempio se usare un ambiente Python esistente oppure un ambiente Conda compilato a partire da una specifica.

Una configurazione di esecuzione può essere salvata in modo permanente in un file all'interno della directory che contiene lo script di training, oppure costruita come un oggetto in memoria e usata per inviare un'esecuzione.

Per degli esempi di configurazioni di esecuzione, vedere [Selezionare e usare una destinazione di calcolo per eseguire il training del modello](how-to-set-up-training-targets.md).

## <a name="dataset"></a>Set di dati

Azure Machine Learning i set di dati (anteprima) rendono più semplice accedere e usare i dati. Set di dati di gestire i dati in diversi scenari, ad esempio training modello e la creazione di pipeline. Usa il SDK di Azure Machine Learning, è possibile accedere all'archiviazione sottostante, esplorare e preparare i dati, gestire il ciclo di vita delle diverse definizioni di set di dati e confrontare tra set di dati usati nel training e in fase di produzione.

I set di dati fornisce i metodi per l'utilizzo di dati in formati comuni, ad esempio usando `from_delimited_files()` o `to_pandas_dataframe()`.

Per altre informazioni, vedere [crea e registra il set di dati di Azure Machine Learning](how-to-create-register-datasets.md).

Per un esempio dell'uso di set di dati, vedere la [notebook di esempio](https://aka.ms/dataset-tutorial).

## <a name="datastore"></a>Archivio dati

Un archivio dati è un'astrazione dell'archiviazione su un account di archiviazione di Azure. L'archivio dati può usare un contenitore BLOB o una condivisione file di Azure come risorsa di archiviazione back-end. Ogni area di lavoro presenta un archivio dati predefinito, ma è possibile registrare altri archivi dati.

Usare l'API di Python SDK o l'interfaccia della riga di comando di Azure Machine Learning per archiviare e recuperare i file dall'archivio dati.

## <a name="compute-target"></a>Destinazione del calcolo

Oggetto [destinazione di calcolo](concept-compute-target.md) ti permette di specificare la risorsa di calcolo in cui si esegue lo script di training o di un host la distribuzione del servizio. Questo percorso può essere nel computer locale o una risorsa di calcolo basati sul cloud. Le destinazioni di calcolo rendono più semplice modificare l'ambiente di calcolo senza modificare il codice. 

## <a name="training-script"></a>Script di training

Per eseguire il training di un modello si specifica la directory che contiene lo script di training e i file associati. Si specifica inoltre un nome per l'esperimento, che viene usato per archiviare le informazioni raccolte durante il training. Durante il training l'intera directory viene copiata nell'ambiente di training (destinazione di calcolo) e lo script specificato per la configurazione di esecuzione viene avviato. Uno snapshot della directory viene archiviato anche nell'esperimento nell'area di lavoro.

Per un esempio, vedere [Esercitazione: Eseguire il training di un modello di classificazione delle immagini con il servizio Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="run"></a>Esegui

Un'esecuzione è un record contenente le seguenti informazioni:

* Metadati sull'esecuzione (timestamp, durata e così via)
* Metriche registrate dallo script
* File di output raccolti automaticamente dall'esperimento o caricati in modo esplicito dall'utente
* Uno snapshot della directory che contiene gli script, precedente all'esecuzione

Un'esecuzione viene generata quando si invia uno script per eseguire il training di un modello. Un'esecuzione può avere zero o più esecuzioni figlio. L'esecuzione di primo livello, ad esempio, potrebbe avere due esecuzioni figlio, ognuna delle quali può avere a sua volte le proprie esecuzioni figlio.

Per un esempio di visualizzazione delle esecuzioni generate dal training di un modello, vedere [Avvio rapido: Introduzione al servizio Azure Machine Learning](quickstart-run-cloud-notebook.md).

## <a name="github-tracking-and-integration"></a>Integrazione e il rilevamento di GitHub

Quando si avvia un'esecuzione in cui la directory di origine è un repository Git locale di training, informazioni sul repository vengono archiviate nella cronologia di esecuzione. Ad esempio, l'ID commit corrente per il repository viene registrato come parte della cronologia. Funziona con esecuzioni inviate tramite un Estimatore, pipeline di Machine Learning o dall'esecuzione dello script. Funziona anche per le esecuzioni sottomesso dal SDK o l'interfaccia della riga di comando di Machine Learning.

## <a name="snapshot"></a>Snapshot

Quando si invia un'esecuzione, Azure Machine Learning consente di comprimere la directory che contiene lo script come file ZIP e lo invia alla destinazione di calcolo. Il file ZIP viene quindi estratto e lo script eseguito in questa posizione. Azure Machine Learning archivia inoltre il file ZIP come snapshot come parte del record di esecuzione. Chiunque abbia accesso all'area di lavoro può esplorare un record di esecuzione e scaricare lo snapshot.

> [!NOTE]
> Per impedire che viene incluso nello snapshot di file non necessari, creare un file ignorato (con estensione gitignore o .amlignore). Inserire questo file della cartella Snapshot e aggiungere i nomi di file da ignorare in esso. Il file .amlignore Usa lo stesso [sintassi e i modelli del file con estensione gitignore](https://git-scm.com/docs/gitignore). Se entrambi i file esistono, il file .amlignore ha la precedenza.

## <a name="activity"></a>Attività

Un'attività rappresenta un'operazione a esecuzione prolungata. Le operazioni seguenti sono esempi di attività:

* Creazione o eliminazione di una destinazione di calcolo
* Esecuzione di uno script su una destinazione di calcolo

Le attività possono fornire notifiche tramite l'SDK o l'interfaccia utente Web in modo da poter facilmente monitorare l'avanzamento di queste operazioni.

## <a name="image"></a>Image

Le immagini consentono di distribuire in modo affidabile un modello e tutti i componenti necessari per il suo utilizzo. Un'immagine contiene i seguenti elementi:

* Un modello.
* Uno script di punteggio o applicazione. Questo script viene usato per passare l'input al modello e per restituire l'output del modello.
* Le dipendenze necessarie per il modello, lo script di punteggio o l'applicazione. Ad esempio, è possibile includere un file di ambiente Conda che elenca le dipendenze dei pacchetti Python.

Azure Machine Learning può creare due tipi di immagine:

* **Immagine FPGA**: usata durante la distribuzione in un dispositivo FPGA (Field-Programmable Gate Array) in Azure.
* **Immagine docker**: usata durante la distribuzione in destinazioni di calcolo diverse da FPGA. Alcuni esempi sono le Istanze di Azure Container e il servizio Azure Kubernetes.

Il servizio di Azure Machine Learning fornisce un'immagine di base, che viene usata per impostazione predefinita. È anche possibile fornire le tue immagini personalizzate.

Per un esempio di creazione di un'immagine, vedere [Distribuire un modello di classificazione delle immagini in Istanze di Azure Container](tutorial-deploy-models-with-aml.md).

### <a name="image-registry"></a>Registro delle immagini

Il registro delle immagini tiene traccia delle immagini create dai modelli. È possibile specificare altri tag di metadati durante la creazione dell'immagine. I tag dei metadati vengono archiviati nel registro delle immagini e consentono l'esecuzione di query per trovare l'immagine.

## <a name="deployment"></a>Distribuzione

Creazione di un'istanza del modello in un servizio web che può essere ospitato nel cloud o in un modulo di IoT per le distribuzioni di dispositivo integrato è una distribuzione.

### <a name="web-service"></a>Servizio Web

Un servizio Web distribuito può usare le Istanze di Azure Container, il servizio Azure Kubernetes o le FPGA. Si crea il servizio dal modello, script e i file associati. Questi sono incapsulati in un'immagine, che fornisce l'ambiente di runtime per il servizio web. L'immagine presenta un endpoint HTTP con bilanciamento del carico che riceve le richieste di punteggio inviate al servizio Web.

Azure aiuta a monitorare la distribuzione del servizio Web raccogliendo i dati di telemetria di Application Insights o, se questa funzionalità è stata abilitata, i dati di telemetria del modello. I dati di telemetria sono accessibili solo per l'utente e archiviati in Application Insights e nelle istanze dell'account di archiviazione.

Se è stata abilitata la scalabilità automatica, la distribuzione verrà ridimensionata automaticamente da Azure.

Per un esempio di distribuzione di un modello come servizio Web, vedere [Distribuire un modello di classificazione delle immagini in Istanze di Azure Container](tutorial-deploy-models-with-aml.md).

### <a name="iot-module"></a>Modulo IoT

Un modulo IoT distribuito è un contenitore Docker che include il modello, lo script/associazione associato e qualsiasi altra dipendenza. Questi moduli si distribuisce usando Azure IoT Edge nei dispositivi perimetrali.

Se il monitoraggio è abilitato, Azure raccoglie i dati di telemetria dal modello all'interno del modulo Azure IoT Edge. I dati di telemetria sono accessibili solo per l'utente e archiviati nell'istanza dell'account di archiviazione.

Azure IoT Edge garantisce che il modulo sia in esecuzione e monitora il dispositivo che lo ospita.

## <a name="pipeline"></a>Pipeline

Le pipeline di Machine Learning consentono di creare e gestire i flussi di lavoro in cui sono unite le varie fasi dell'apprendimento automatico. Ad esempio, una pipeline potrebbe includere la preparazione dei dati, training del modello, la distribuzione del modello e fasi inferenza/di assegnazione dei punteggi. Ogni fase può includere più passaggi, ciascuno dei quali può essere eseguito in modo automatico in varie destinazioni di calcolo.

Per altre informazioni sulle pipeline di apprendimento automatico, vedere [Pipeline e Azure Machine Learning](concept-ml-pipelines.md).

## <a name="logging"></a>Registrazione

Quando si sviluppa la soluzione, usare Python SDK di Azure Machine Learning nello script di Python per registrare metriche arbitrarie. Dopo l'esecuzione, eseguire query sulle metriche per determinare se l'esecuzione ha prodotto il modello che si desidera distribuire.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare con il servizio Azure Machine Learning, vedere:

* [Informazioni sul servizio Azure Machine Learning.](overview-what-is-azure-ml.md)
* [Creare un'area di lavoro del servizio di Azure Machine Learning](setup-create-workspace.md)
* [Esercitazione: Eseguire il training di un modello](tutorial-train-models-with-aml.md)
* [Creare un'area di lavoro con un modello di Resource Manager](how-to-create-workspace-template.md)
