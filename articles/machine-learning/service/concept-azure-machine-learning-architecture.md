---
title: 'Apprendimento automatico nel cloud: Termini e architettura'
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
ms.openlocfilehash: 4d201669bf627cf9b591958f3372760c0c990db9
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/28/2018
ms.locfileid: "53808095"
---
# <a name="how-azure-machine-learning-service-works-architecture-and-concepts"></a>Come funziona il servizio Azure Machine Learning: Architettura e concetti

In questo articolo vengono descritti l'architettura e i concetti del servizio Azure Machine Learning. Il diagramma seguente illustra i componenti principali del servizio e il flusso di lavoro generale durante il suo uso: 

[![Architettura e flusso di lavoro del servizio Azure Machine Learning](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

Il flusso di lavoro, in genere, segue questa sequenza:

1. Sviluppare script di training per l'apprendimento automatico in **Python**.
1. Creare e configurare una **destinazione di calcolo**.
1. **Inviare gli script** alla destinazione di calcolo configurata per l'esecuzione in tale ambiente. Durante il training, la destinazione di calcolo archivia i record di esecuzione in un **archivio dati**. Qui, i record vengono salvati in un **esperimento**.
1. **Eseguire una query sull'esperimento** per le metriche registrate dalle esecuzioni correnti e precedenti. Se le metriche non indicano un risultato desiderato, tornare al passaggio 1 ed eseguire l'iterazione sugli script.
1. Una volta trovata un'esecuzione soddisfacente, registrare il modello persistente nel **registro di modello**.
1. Sviluppare uno script di punteggio.
1. **Creare un'immagine** e registrarla nel **registro delle immagini**. 
1. **Distribuire l'immagine** come un **servizio Web** in Azure.


> [!NOTE]
> Sebbene in questo articolo vengano definiti termini e concetti usati dal servizio Azure Machine Learning, non vengono definiti quelli relativi alla piattaforma di Azure. Per altre informazioni sulla terminologia della piattaforma Azure, vedere il [glossario di Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="workspace"></a>Area di lavoro

L'area di lavoro è la risorsa di primo livello per il servizio Azure Machine Learning. Fornisce una posizione centralizzata per lavorare con tutti gli artefatti creati durante l'uso del servizio Azure Machine Learning.

L'area di lavoro mantiene un elenco di destinazioni di calcolo che può essere usato per il training del modello. Inoltre, mantiene una cronologia di esecuzioni di training che include i registri, le metriche, gli output e uno snapshot degli script. Queste informazioni consentono di determinare il training che produce il modello migliore.

I modelli vengono registrati con l'area di lavoro. Il modello registrato e gli script di punteggio sono usati per creare un'immagine. È possibile quindi distribuire l'immagine nelle Istanze di Azure Container, nel servizio Azure Kubernetes o in una matrice FPGA come un endpoint HTTP basato su REST. È inoltre possibile distribuirla in un dispositivo Azure IoT Edge come modulo.

È possibile creare più aree di lavoro, ciascuna delle quali può essere condivisa da più utenti. Quando si condivide un'area di lavoro, è possibile controllarne l'accesso assegnando agli utenti i ruoli seguenti:

* Proprietario
* Collaboratore
* Reader

Quando si crea una nuova area di lavoro, vengono create automaticamente diverse risorse di Azure usate dall'area di lavoro:

* [Registro Azure Container](https://azure.microsoft.com/services/container-registry/): Registra i contenitori docker usati durante il training e quando si distribuisce un modello.
* [Account di archiviazione di Azure](https://azure.microsoft.com/services/storage/): usato come archivio dati predefinito per l'area di lavoro.
* [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): archivia le informazioni di monitoraggio sui modelli.
* [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): conserva segreti usati dalle destinazioni di calcolo e altre informazioni riservate richieste dall'area di lavoro.

> [!NOTE]
> Oltre alla creazione di nuove versioni, è possibile usare i servizi di Azure esistenti. 

Nel seguente diagramma viene illustrata una tassonomia dell'area di lavoro:

[![Tassonomia dell'area di lavoro](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.svg)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

## <a name="model"></a>Modello

Nella forma più semplice, un modello è un frammento di codice che accetta un input e produce output. La creazione di un modello di Machine Learning prevede di selezionare un algoritmo, fornirgli dei dati e ottimizzare gli iperparametri. Il training è un processo iterativo che produce un modello con training, che racchiude ciò che il modello ha appreso durante il processo di training.

Un modello è prodotto da un'esecuzione in Azure Machine Learning. È anche possibile usare un modello il cui training sia stato eseguito all'esterno di Azure Machine Learning. È possibile registrare un modello in un'area di lavoro del servizio Azure Machine Learning.

Il servizio Azure Machine Learning è indipendente dal framework. Quando si crea un modello, è possibile usare un qualsiasi framework di apprendimento automatico comune, ad esempio Scikit-learn, XGBoost, PyTorch, TensorFlow, Chainer e Microsoft Cognitive Toolkit (precedentemente noto come CNTK).

Per un esempio di training di un modello, vedere [Avvio rapido: Creare un'area di lavoro del servizio Machine Learning](quickstart-get-started.md).

### <a name="model-registry"></a>Registro di modello

Il registro di modello tiene traccia di tutti i modelli nell'area di lavoro del servizio di Azure Machine Learning. 

I modelli vengono identificati dal nome e dalla versione. Ogni volta che si registra un modello con lo stesso nome di uno esistente, il registro presuppone che si tratti di una nuova versione. La versione viene incrementata e il nuovo modello registrato con lo stesso nome.

Quando si registra il modello, è possibile specificare altri tag di metadati e quindi usarli durante la ricerca di modelli.

Non è possibile eliminare i modelli attualmente in uso da un'immagine.

Per un esempio di registrazione di un modello, vedere [Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="image"></a>Image

Le immagini consentono di distribuire in modo affidabile un modello e tutti i componenti necessari per il suo utilizzo. Un'immagine contiene i seguenti elementi:

* Un modello.
* Uno script di punteggio o applicazione. Questo script viene usato per passare l'input al modello e per restituire l'output del modello.
* Le dipendenze necessarie per il modello, lo script di punteggio o l'applicazione. Ad esempio, è possibile includere un file di ambiente Conda che elenca le dipendenze dei pacchetti Python.

Azure Machine Learning può creare due tipi di immagine:

* **Immagine FPGA**: usata durante la distribuzione in un dispositivo FPGA (Field-Programmable Gate Array) in Azure.
* **Immagine docker**: usata durante la distribuzione in destinazioni di calcolo diverse da FPGA. Alcuni esempi sono le Istanze di Azure Container e il servizio Azure Kubernetes.

Per un esempio di creazione di un'immagine, vedere [Distribuire un modello di classificazione delle immagini in Istanze di Azure Container](tutorial-deploy-models-with-aml.md).

### <a name="image-registry"></a>Registro delle immagini

Il registro delle immagini tiene traccia delle immagini create dai modelli. È possibile specificare altri tag di metadati durante la creazione dell'immagine. I tag dei metadati vengono archiviati nel registro delle immagini e consentono l'esecuzione di query per trovare l'immagine.

## <a name="deployment"></a>Distribuzione

La distribuzione è la creazione di un'istanza dell'immagine in un servizio Web che può essere ospitato nel cloud o in un modulo IoT per le distribuzioni integrate nei dispositivi. 

### <a name="web-service"></a>Servizio Web

Un servizio Web distribuito può usare le Istanze di Azure Container, il servizio Azure Kubernetes o le FPGA. Il servizio viene creato da un'immagine che racchiude il modello, lo script e i file associati. L'immagine presenta un endpoint HTTP con bilanciamento del carico che riceve le richieste di punteggio inviate al servizio Web.

Azure aiuta a monitorare la distribuzione del servizio Web raccogliendo i dati di telemetria di Application Insights o, se questa funzionalità è stata abilitata, i dati di telemetria del modello. I dati di telemetria sono accessibili solo per l'utente e archiviati in Application Insights e nelle istanze dell'account di archiviazione.

Se è stata abilitata la scalabilità automatica, la distribuzione verrà ridimensionata automaticamente da Azure.

Per un esempio di distribuzione di un modello come servizio Web, vedere [Distribuire un modello di classificazione delle immagini in Istanze di Azure Container](tutorial-deploy-models-with-aml.md).

### <a name="iot-module"></a>Modulo IoT

Un modulo IoT distribuito è un contenitore Docker che include il modello, lo script/associazione associato e qualsiasi altra dipendenza. Questi moduli vengono distribuiti tramite Azure IoT Edge nei dispositivi perimetrali. 

Se il monitoraggio è abilitato, Azure raccoglie i dati di telemetria dal modello all'interno del modulo Azure IoT Edge. I dati di telemetria sono accessibili solo per l'utente e archiviati nell'istanza dell'account di archiviazione.

Azure IoT Edge garantisce che il modulo sia in esecuzione e monitora il dispositivo che lo ospita.

## <a name="datastore"></a>Archivio dati

Un archivio dati è un'astrazione dell'archiviazione su un account di archiviazione di Azure. L'archivio dati può usare un contenitore BLOB o una condivisione file di Azure come risorsa di archiviazione back-end. Ogni area di lavoro presenta un archivio dati predefinito, ma è possibile registrare altri archivi dati. 

Usare l'API di Python SDK o l'interfaccia della riga di comando di Azure Machine Learning per archiviare e recuperare i file dall'archivio dati. 

## <a name="run"></a>Esegui

Un'esecuzione è un record contenente le seguenti informazioni:

* Metadati sull'esecuzione (timestamp, durata e così via)
* Metriche registrate dallo script
* File di output raccolti automaticamente dall'esperimento o caricati in modo esplicito dall'utente
* Uno snapshot della directory che contiene gli script, precedente all'esecuzione

Un'esecuzione viene generata quando si invia uno script per eseguire il training di un modello. Un'esecuzione può avere zero o più esecuzioni figlio. L'esecuzione di primo livello, ad esempio, potrebbe avere due esecuzioni figlio, ognuna delle quali può avere a sua volte le proprie esecuzioni figlio.

Per un esempio di visualizzazione delle esecuzioni generate dal training di un modello, vedere [Avvio rapido: Introduzione al servizio Azure Machine Learning](quickstart-get-started.md).

## <a name="experiment"></a>Esperimento

Un esperimento è un raggruppamento di più esecuzioni da uno script specifico. Appartiene sempre a un'area di lavoro. Quando si invia un'esecuzione, si fornisce un nome dell'esperimento. Le informazioni per l'esecuzione vengono archiviate in tale esperimento. Se si invia un'esecuzione e si specifica un nome dell'esperimento che non esiste, viene creato automaticamente un nuovo esperimento il nuovo nome specificato.

Per un esempio d'uso di un esperimento, vedere [Avvio rapido: Introduzione al servizio Azure Machine Learning](quickstart-get-started.md).

## <a name="pipeline"></a>Pipeline

Le pipeline di Machine Learning consentono di creare e gestire i flussi di lavoro in cui sono unite le varie fasi dell'apprendimento automatico. Ad esempio, una pipeline può includere le fasi di preparazione dei dati, training del modello, distribuzione del modello e inferenza. Ogni fase può includere più passaggi, ciascuno dei quali può essere eseguito in modo automatico in varie destinazioni di calcolo.

Per altre informazioni sulle pipeline di apprendimento automatico, vedere [Pipeline e Azure Machine Learning](concept-ml-pipelines.md).

## <a name="compute-target"></a>Destinazione del calcolo

Una destinazione di calcolo è la risorsa di calcolo usata per eseguire lo script di training o per ospitare la distribuzione del servizio. Le destinazioni di calcolo supportate sono: 

| Destinazione del calcolo | Formazione | Distribuzione |
| ---- |:----:|:----:|
| Il computer locale | ✓ | &nbsp; |
| Ambiente di calcolo di Azure Machine Learning | ✓ | &nbsp; |
| Una VM Linux in Azure</br>(ad esempio, Data Science Virtual Machine) | ✓ | &nbsp; |
| Azure Databricks | ✓ | &nbsp; | &nbsp; |
| Azure Data Lake Analytics. | ✓ | &nbsp; |
| Apache Spark per HDInsight | ✓ | &nbsp; |
| Istanze di contenitore di Azure | &nbsp; | ✓ |
| Servizio Azure Kubernetes | &nbsp; | ✓ |
| Azure IoT Edge | &nbsp; | ✓ |
| Project Brainwave</br>(Field-programmable Gate Array) | &nbsp; | ✓ |

Le destinazioni di calcolo sono allegate a un'area di lavoro. Le destinazioni di calcolo diverse dal computer locale vengono condivise dagli utenti dell'area di lavoro.

### <a name="managed-and-unmanaged-compute-targets"></a>Destinazioni di calcolo gestite e non gestite

* **Gestite**: destinazioni di calcolo create e gestite dal servizio Azure Machine Learning. Queste destinazioni sono ottimizzate per i carichi di lavoro di apprendimento automatico. L'ambiente di calcolo di Azure Machine Learning è l'unica destinazione di calcolo gestita alla data di questo documento (4 dicembre 2018). È possibile che in futuro vengano aggiunte altre destinazioni di calcolo gestite. 

    È possibile creare istanze dell'ambiente di calcolo di apprendimento automatico direttamente tramite l'area di lavoro usando il portale di Azure, l'SDK di Azure Machine Learning o l'interfaccia della riga di comando di Azure. Tutte le altre destinazioni di calcolo devono essere create al di fuori dell'area di lavoro e quindi collegate a tale area.

* **Non gestite**: destinazioni di calcolo *non* gestite dal servizio Azure Machine Learning. Può essere necessario crearle all'esterno di Azure Machine Learning e quindi collegarle all'area di lavoro prima dell'uso. Le destinazioni di calcolo non gestite possono richiedere passaggi aggiuntivi per mantenere o migliorare le prestazioni per i carichi di lavoro di apprendimento automatico.

Per informazioni sulla selezione di una destinazione di calcolo per il training, vedere [Selezionare e usare una destinazione di calcolo per eseguire il training del modello](how-to-set-up-training-targets.md).

Per informazioni sulla selezione di una destinazione di calcolo per la distribuzione, vedere [Distribuire modelli con il servizio Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="run-configuration"></a>Configurazione di esecuzione

Una configurazione di esecuzione è un set di istruzioni che definisce come uno script deve essere eseguito in una destinazione di calcolo specificata. La configurazione include un'ampia serie di definizioni di comportamento, ad esempio se usare un ambiente Python esistente oppure un ambiente Conda compilato a partire da una specifica.

Una configurazione di esecuzione può essere salvata in modo permanente in un file all'interno della directory che contiene lo script di training, oppure costruita come un oggetto in memoria e usata per inviare un'esecuzione.

Per degli esempi di configurazioni di esecuzione, vedere [Selezionare e usare una destinazione di calcolo per eseguire il training del modello](how-to-set-up-training-targets.md).

## <a name="training-script"></a>Script di training

Per eseguire il training di un modello si specifica la directory che contiene lo script di training e i file associati. Si specifica inoltre un nome per l'esperimento, che viene usato per archiviare le informazioni raccolte durante il training. Durante il training l'intera directory viene copiata nell'ambiente di training (destinazione di calcolo) e lo script specificato per la configurazione di esecuzione viene avviato. Uno snapshot della directory viene archiviato anche nell'esperimento nell'area di lavoro.

Per un esempio, vedere [Creare un'area di lavoro con Python](quickstart-get-started.md).

## <a name="logging"></a>Registrazione

Quando si sviluppa la soluzione, usare Python SDK di Azure Machine Learning nello script di Python per registrare metriche arbitrarie. Dopo l'esecuzione, eseguire query sulle metriche per determinare se l'esecuzione ha prodotto il modello che si desidera distribuire. 

## <a name="snapshot"></a>Snapshot

Quando si invia un'esecuzione, Azure Machine Learning consente di comprimere la directory che contiene lo script come file ZIP e lo invia alla destinazione di calcolo. Il file ZIP viene quindi estratto e lo script eseguito in questa posizione. Azure Machine Learning archivia inoltre il file ZIP come snapshot come parte del record di esecuzione. Chiunque abbia accesso all'area di lavoro può esplorare un record di esecuzione e scaricare lo snapshot.

## <a name="activity"></a>Attività

Un'attività rappresenta un'operazione a esecuzione prolungata. Le operazioni seguenti sono esempi di attività:

* Creazione o eliminazione di una destinazione di calcolo
* Esecuzione di uno script su una destinazione di calcolo

Le attività possono fornire notifiche tramite l'SDK o l'interfaccia utente Web in modo da poter facilmente monitorare l'avanzamento di queste operazioni.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare con il servizio Azure Machine Learning, vedere:

* [Informazioni sul servizio Azure Machine Learning.](overview-what-is-azure-ml.md)
* [Avvio rapido: Creare un'area di lavoro con Python](quickstart-get-started.md)
* [Esercitazione: Eseguire il training di un modello](tutorial-train-models-with-aml.md)
