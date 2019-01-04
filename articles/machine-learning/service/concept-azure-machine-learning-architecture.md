---
title: 'Machine Learning nel cloud: termini e architettura'
titleSuffix: Azure Machine Learning service
description: Informazioni su architettura, terminologia e concetti del servizio Azure Machine Learning. Si otterranno informazioni anche sul flusso di lavoro generale dell'uso del servizio e sui servizi di Azure usati dal servizio Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: haining
author: hning86
ms.reviewer: larryfr
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 3966d4b27f0e3d42f47d84fb5c9f5c8519a27b6c
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184730"
---
# <a name="how-the-azure-machine-learning-service-works-architecture-and-concepts"></a>Informazioni sul funzionamento del servizio Azure Machine Learning: architettura e concetti

In questo documento vengono descritti l'architettura e i concetti del servizio Azure Machine Learning. Il diagramma seguente illustra i componenti principali del servizio e il flusso di lavoro generale durante il suo uso: 

[![Architettura e flusso di lavoro del servizio di Azure Machine Learning ](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

Il flusso di lavoro segue in genere questi passaggi:

1. Sviluppare script di training per l'apprendimento automatico in __Python__.
1. Creare e configurare una __destinazione di calcolo__.
1. __Inviare gli script__ alla destinazione di calcolo configurata per l'esecuzione in tale ambiente. Durante il training, la destinazione di calcolo archivia i record di esecuzione in un __archivio dati__. Qui, i record vengono salvati in un __esperimento__.
1. __Eseguire una query sull'esperimento__ per le metriche registrate dalle esecuzioni correnti e precedenti. Se le metriche non indicano un risultato desiderato, tornare al passaggio 1 ed eseguire l'iterazione sugli script.
1. Una volta trovata un'esecuzione soddisfacente, registrare il modello persistente nel __registro di modello__.
1. Sviluppare uno script di punteggio.
1. __Creare un'immagine__ e registrarla nel __registro delle immagini__. 
1. __Distribuire l'immagine__ come un __servizio Web__ in Azure.


> [!NOTE]
> Sebbene in questo documento vengano definiti termini e concetti usati da Azure Machine Learning, non vengono definiti quelli relativi alla piattaforma di Azure. Per altre informazioni sulla terminologia della piattaforma Azure, vedere il [glossario di Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="workspace"></a>Area di lavoro

L'area di lavoro è la risorsa di primo livello per il servizio Azure Machine Learning. Fornisce una posizione centralizzata per lavorare con tutti gli artefatti creati durante l'uso del servizio di Azure Machine Learning.

L'area di lavoro mantiene un elenco di destinazioni di calcolo che può essere usato per il training del modello. Inoltre, mantiene una cronologia di esecuzioni di training che include i registri, le metriche, gli output e uno snapshot degli script. Queste informazioni consentono di determinare il training che produce il modello migliore.

I modelli vengono registrati con l'area di lavoro. Il modello registrato e gli script di punteggio sono usati per creare un'immagine. L'immagine può quindi essere distribuita nelle istanze di contenitore di Azure, nel servizio Kubernetes di Azure o in una matrice FPGA come un endpoint HTTP basato su REST. Può anche essere distribuita in un dispositivo Azure IoT Edge come modulo.

È possibile creare più aree di lavoro, ciascuna delle quali può essere condivisa da più utenti. Quando si condivide un'area di lavoro, è possibile controllarne l'accesso assegnando agli utenti i ruoli seguenti:

* Proprietario
* Collaboratore
* Reader

Quando si crea una nuova area di lavoro, vengono create automaticamente diverse risorse di Azure usate dall'area di lavoro:

* [Registro contenitori di Azure](https://azure.microsoft.com/services/container-registry/): registra contenitori Docker che vengono usati durante il training e durante la distribuzione di un modello.
* [Archiviazione di Azure](https://azure.microsoft.com/services/storage/): usata come archivio dati predefinito per l'area di lavoro.
* [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): archivia le informazioni di monitoraggio sui modelli.
* [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): conserva segreti usati dalle destinazioni di calcolo e altre informazioni riservate richieste dall'area di lavoro.

> [!NOTE]
> In alternativa alla creazione di nuove versioni è possibile usare i servizi di Azure esistenti. 

Il diagramma seguente rappresenta una tassonomia dell'area di lavoro:

[![Tassonomia dell'area di lavoro](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.svg)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

## <a name="model"></a>Modello

Nella forma più semplice, un modello è un frammento di codice che accetta un input e produce output. La creazione di un modello di Machine Learning prevede di selezionare un algoritmo, fornirgli dei dati e ottimizzare gli iperparametri. Il training è un processo iterativo che produce un modello con training, che racchiude ciò che il modello ha appreso durante il processo di training.

Un modello è prodotto da un'esecuzione in Azure Machine Learning. È anche possibile usare un modello il cui training sia stato eseguito all'esterno di Azure Machine Learning. È possibile registrare un modello in un'area di lavoro del servizio di Azure Machine Learning.

Il servizio di Azure Machine Learning è indipendente dal framework. Durante la creazione di un modello è possibile usare un qualsiasi framework di apprendimento automatico comune, ad esempio scikit-learn, xgboost, PyTorch, TensorFlow, Chainer e CNTK.

Per un esempio di training di un modello, vedere il documento [Avvio rapido: Creare un'area di lavoro del servizio Azure Machine Learning](quickstart-get-started.md).

### <a name="model-registry"></a>Registro di modello

Il registro di modello tiene traccia di tutti i modelli nell'area di lavoro del servizio di Azure Machine Learning. 

I modelli vengono identificati dal nome e dalla versione. Ogni volta che si registra un modello con lo stesso nome di uno esistente, il registro presuppone che si tratti di una nuova versione. La versione viene incrementata e il nuovo modello registrato con il nome.

Al momento della registrazione del modello è possibile specificare altri tag di metadati e quindi usarli durante la ricerca di modelli.

Non è possibile eliminare i modelli attualmente in uso da un'immagine.

Per un esempio di registrazione di un modello, vedere il documento [Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="image"></a>Immagine

Le immagini consentono di distribuire in modo affidabile un modello e tutti i componenti necessari per il suo utilizzo. Un'immagine contiene i seguenti elementi:

* Un modello.
* Uno script di punteggio o applicazione. Questo script viene usato per passare l'input al modello e per restituire l'output del modello.
* Dipendenze necessarie per il modello o lo script di punteggio/applicazione.  Ad esempio, è possibile includere un file di ambiente Conda che elenca le dipendenze dei pacchetti Python.

Esistono due tipi di immagini che possono essere create tramite Azure Machine Learning:

* Immagine FPGA: usata durante la distribuzione in un dispositivo FPGA (Field-Programmable Gate Array) nel cloud di Azure.
* Immagine Docker: usata durante la distribuzione in destinazioni di calcolo diverse da FPGA. Ad esempio, le istanze di contenitore di Azure e il servizio Kubernetes di Azure.

Per un esempio di creazione di un'immagine, vedere il documento [Distribuire un modello di classificazione delle immagini nell'istanza di contenitore di Azure](tutorial-deploy-models-with-aml.md).

### <a name="image-registry"></a>Registro delle immagini

Il registro delle immagini tiene traccia delle immagini create dai modelli. È possibile specificare altri tag di metadati durante la creazione dell'immagine. I tag dei metadati vengono archiviati nel registro delle immagini e consentono l'esecuzione di query per trovare l'immagine.

## <a name="deployment"></a>Distribuzione

La distribuzione è la creazione di un'istanza dell'immagine in un servizio Web che può essere ospitato nel cloud o in un modulo IoT per le distribuzioni integrate nei dispositivi. 

### <a name="web-service"></a>Servizio Web

Un servizio Web distribuito può usare le istanze di contenitore di Azure, il servizio Kubernetes di Azure o le matrici FPGA.
Il servizio viene creato da un'immagine che racchiude il modello, lo script e i file associati. L'immagine presenta un endpoint HTTP con bilanciamento del carico, che riceve le richieste di punteggio inviate al servizio Web.

Azure aiuta a monitorare la distribuzione del servizio Web raccogliendo i dati di telemetria di Application Insights e, se questa funzionalità è stata abilitata, i dati di telemetria del modello. I dati di telemetria sono accessibili solo per l'utente e archiviati in Application Insights e nelle istanze dell'account di archiviazione.

Se è stata abilitata la scalabilità automatica, la distribuzione verrà ridimensionata automaticamente da Azure.

Per un esempio di distribuzione di un modello come servizio Web, vedere il documento [Distribuire un modello di classificazione delle immagini nell'istanza di contenitore di Azure](tutorial-deploy-models-with-aml.md).

### <a name="iot-module"></a>Modulo IoT

Un modulo IoT distribuito è un contenitore Docker che include il modello, lo script/associazione associato e qualsiasi altra dipendenza. Questi moduli vengono distribuiti tramite Azure IoT Edge nei dispositivi perimetrali. 

Se il monitoraggio è abilitato, Azure raccoglie i dati di telemetria dal modello all'interno del modulo Azure IoT Edge. I dati di telemetria sono accessibili solo per l'utente e archiviati nell'istanza dell'account di archiviazione.

Azure IoT Edge garantirà che il modulo è in esecuzione e monitorerà il dispositivo che lo ospita.

## <a name="datastore"></a>Archivio dati

Un archivio dati è un'astrazione dell'archiviazione su un account di archiviazione di Azure. L'archivio dati può usare un contenitore BLOB o una condivisione file di Azure come risorsa di archiviazione back-end. Ogni area di lavoro presenta un archivio dati predefinito, ma è possibile registrare altri archivi dati. 

Usare l'API di Python SDK o l'interfaccia della riga di comando di Azure Machine Learning per archiviare e recuperare i file dall'archivio dati. 

## <a name="run"></a>Esecuzione

Un'esecuzione è un record contenente le seguenti informazioni:

* Metadati sull'esecuzione (timestamp, durata e così via)
* Metriche registrate dallo script
* File di output raccolti automaticamente dall'esperimento o caricati in modo esplicito dall'utente.
* Uno snapshot della directory che contiene gli script, precedente all'esecuzione

Un'esecuzione viene generata quando si invia uno script per eseguire il training di un modello. Un'esecuzione può avere zero o più esecuzioni figlio. L'esecuzione di primo livello potrebbe pertanto avere due esecuzioni figlio, ognuna delle quali può avere le proprie esecuzioni figlio.

Per un esempio di visualizzazione delle esecuzioni generate dal training di un modello, vedere il documento [Avvio rapido: Introduzione al servizio Azure Machine Learning](quickstart-get-started.md).

## <a name="experiment"></a>Esperimento

Un esperimento è un raggruppamento di più esecuzioni da uno script specifico. Appartiene sempre a un'area di lavoro. Quando si invia un'esecuzione, si fornisce un nome dell'esperimento. Le informazioni per l'esecuzione vengono archiviate in tale esperimento. Se si invia un'esecuzione e si specifica un nome dell'esperimento che non esiste, viene creato automaticamente un nuovo esperimento con lo stesso nome.

Per un esempio d'uso di un esperimento, vedere il documento [Avvio rapido: Introduzione al servizio Azure Machine Learning](quickstart-get-started.md).

## <a name="pipeline"></a>Pipeline

Le pipeline di Machine Learning consentono di creare e gestire i flussi di lavoro in cui sono unite le varie fasi dell'apprendimento automatico. Ad esempio, una pipeline può includere le fasi di preparazione dei dati, training del modello, distribuzione del modello e inferenza. Ogni fase può includere più passaggi, ciascuno dei quali può essere eseguito in modo automatico in varie destinazioni di calcolo.

Per altre informazioni sulle pipeline di apprendimento automatico, vedere l'articolo [Pipeline e Azure Machine Learning](concept-ml-pipelines.md).

## <a name="compute-target"></a>Destinazione di calcolo

Una destinazione di calcolo è la risorsa di calcolo usata per eseguire lo script di training o per ospitare la distribuzione del servizio. Le destinazioni di calcolo supportate sono: 

| Destinazione di calcolo | Formazione | Distribuzione |
| ---- |:----:|:----:|
| Il computer locale | ✓ | &nbsp; |
| Ambiente di calcolo di Azure Machine Learning | ✓ | &nbsp; |
| Una VM Linux in Azure</br>(ad esempio, Data Science Virtual Machine) | ✓ | &nbsp; |
| Azure Databricks | ✓ | &nbsp; | &nbsp; |
| Azure Data Lake Analytics. | ✓ | &nbsp; |
| Apache Spark per HDInsight | ✓ | &nbsp; |
| Istanza di contenitore di Azure | &nbsp; | ✓ |
| Azure Kubernetes Service | &nbsp; | ✓ |
| Azure IoT Edge | &nbsp; | ✓ |
| Project Brainwave</br>(Field-programmable Gate Array) | &nbsp; | ✓ |

Le destinazioni di calcolo sono allegate a un'area di lavoro. Le destinazioni di calcolo diverse dal computer locale vengono condivise dagli utenti dell'area di lavoro.

### <a name="managed-and-unmanaged-compute-targets"></a>Destinazioni di calcolo gestite e non gestite

Le destinazione di calcolo **gestite** vengono create e gestite dal servizio Azure Machine Learning. Queste destinazioni sono ottimizzate per i carichi di lavoro di Machine Learning. L'__ambiente di calcolo di Azure Machine Learning__ è al momento (4 dicembre 2018) l'unica destinazione di calcolo gestita. È possibile che in futuro vengano aggiunte altre destinazioni di calcolo gestite. Le istanze dell'ambiente di calcolo di Machine Learning possono essere create direttamente tramite l'area di lavoro usando il portale di Azure, l'SDK di Azure Machine Learning o l'interfaccia della riga di comando di Azure. Tutte le altre destinazioni di calcolo devono essere create al di fuori dell'area di lavoro e quindi collegate a tale area.

Le destinazione di calcolo **non gestite** non vengono gestite dal servizio Azure Machine Learning. Può essere necessario crearle all'esterno di Azure Machine Learning e quindi collegarle all'area di lavoro prima dell'uso. Queste destinazioni di calcolo possono richiedere passaggi aggiuntivi per mantenere o migliorare le prestazioni per i carichi di lavoro di Machine Learning.

Per informazioni sulla selezione di una destinazione di calcolo per il training, vedere il documento [Selezionare e usare una destinazione di calcolo per eseguire il training del modello](how-to-set-up-training-targets.md).

Per informazioni sulla selezione di una destinazione di calcolo per la distribuzione, vedere il documento [Distribuire modelli con il servizio Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="run-configuration"></a>Configurazione di esecuzione

Una configurazione di esecuzione è un set di istruzioni che definisce come uno script deve essere eseguito in una destinazione di calcolo specificata. Include un'ampia serie di definizioni di comportamento, ad esempio se usare un ambiente Python esistente oppure un ambiente Conda compilato dalla specifica.

Una configurazione di esecuzione può essere salvata in modo permanente in un file all'interno della directory che contiene lo script di training oppure costruita come un oggetto in memoria e usata per inviare un'esecuzione.

Per degli esempi di configurazioni di esecuzione, vedere il documento [Selezionare e usare una destinazione di calcolo per eseguire il training del modello](how-to-set-up-training-targets.md).

## <a name="training-script"></a>Script di training

Per eseguire il training di un modello si specifica la directory che contiene lo script di training e i file associati. Si specifica inoltre un nome per l'esperimento, che viene usato per archiviare le informazioni raccolte durante il training. Durante il training l'intera directory viene copiata nell'ambiente di training (destinazione di calcolo) e lo script specificato per la configurazione di esecuzione viene avviato. Uno snapshot della directory viene archiviato anche nell'esperimento nell'area di lavoro.

Per un esempio, vedere [Creare un'area di lavoro con Python](quickstart-get-started.md).

## <a name="logging"></a>Registrazione

Quando si sviluppa la soluzione, usare il Python SDK Python di Azure Machine Learning nello script di Python per registrare metriche arbitrarie. Dopo l'esecuzione, eseguire query sulle metriche per determinare se l'esecuzione ha prodotto il modello che si desidera distribuire. 

## <a name="snapshot"></a>Snapshot

Quando si invia un'esecuzione, Azure Machine Learning consente di comprimere la directory che contiene lo script come file ZIP e lo invia alla destinazione di calcolo. Il file ZIP viene quindi espanso; lo script viene eseguito in questa posizione. Azure Machine Learning archivia inoltre il file ZIP come snapshot come parte del record di esecuzione. Chiunque abbia accesso all'area di lavoro può esplorare un record di esecuzione e scaricare lo snapshot.

## <a name="activity"></a>Attività

Un'attività rappresenta un'operazione a esecuzione prolungata. Le operazioni seguenti sono esempi di attività:

* Creazione o eliminazione di una destinazione di calcolo
* Esecuzione di uno script su una destinazione di calcolo

Le attività possono fornire notifiche tramite l'SDK o l'interfaccia utente Web in modo da poter facilmente monitorare l'avanzamento di queste operazioni.

## <a name="next-steps"></a>Passaggi successivi

Usare i collegamenti seguenti per iniziare a usare Azure Machine Learning:

* [Informazioni sul servizio Azure Machine Learning](overview-what-is-azure-ml.md)
* [Avvio rapido: Creare un'area di lavoro con Python](quickstart-get-started.md)
* [Esercitazione: Eseguire il training di un modello](tutorial-train-models-with-aml.md)
