---
title: Architettura e nozioni chiave
titleSuffix: Azure Machine Learning
description: Informazioni sull'architettura, i termini e i concetti che costituiscono Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: d7bad24510f74a7fadd74328e24ea22855e6fe02
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750843"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Modalità di funzionamento di Azure Machine Learning: Architettura e concetti

Informazioni sull'architettura e i concetti per Azure Machine Learning.

> [!NOTE]
> Sebbene in questo articolo vengano definiti termini e concetti usati da Azure Machine Learning, non vengono definiti quelli relativi alla piattaforma di Azure. Per altre informazioni sulla terminologia della piattaforma Azure, vedere il [glossario di Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="workspace"></a><a name="workspace"></a> Area

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="Architettura Azure Machine Learning":::

Un' [area di lavoro di Machine Learning](concept-workspace.md) è la risorsa di livello superiore per Azure Machine Learning.  L'area di lavoro è la posizione centralizzata per:
* Gestire le risorse usate per il training e la distribuzione di modelli, ad esempio i [calcoli](#compute-instance)
* Archiviare gli asset creati quando si usa Azure Machine Learning, tra cui:
  * [Ambienti](#environments)
  * [Esecuzioni](#runs)
  * [Pipeline](#ml-pipelines)
  * [Set di dati](#datasets-and-datastores)
  * [Modelli](#models)
  * [Endpoints](#endpoints)

Un'area di lavoro include altre risorse di Azure usate dall'area di lavoro:

+ [Azure container Registry](https://azure.microsoft.com/services/container-registry/): registra i contenitori Docker usati durante il training e quando si distribuisce un modello. Per ridurre al minimo i costi, il registro contenitori di contenitori viene **caricato Lazy** fino a quando non vengono create immagini di distribuzione
+ [Account di archiviazione di Azure](https://azure.microsoft.com/services/storage/): viene usato come archivio dati predefinito per l'area di lavoro.  I notebook di Jupyter usati con le istanze di calcolo Azure Machine Learning vengono archiviati anche qui.
+ [Applicazione Azure Insights](https://azure.microsoft.com/services/application-insights/): archivia le informazioni di monitoraggio relative ai modelli.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): archivia i segreti usati dalle destinazioni di calcolo e altre informazioni riservate necessarie per l'area di lavoro.

È possibile condividere un'area di lavoro con altri utenti.

## <a name="studio"></a>Studio

[Azure Machine Learning Studio](https://ml.azure.com) fornisce una visualizzazione Web di tutti gli elementi nell'area di lavoro.  Questo portale consente anche di accedere agli strumenti interattivi che fanno parte di Azure Machine Learning:

+ [Progettazione Azure Machine Learning (anteprima)](concept-designer.md) per eseguire i passaggi del flusso di lavoro senza scrivere codice
+ Esperienza Web per [Machine Learning automatizzato](concept-automated-ml.md)
+ [Assegnazione di etichette ai progetti](how-to-create-labeling-projects.md) per creare, gestire e monitorare i progetti per etichettare i dati

##  <a name="computes"></a>Calcola

<a name="compute-targets"></a> Una [destinazione di calcolo](concept-compute-target.md) è il computer o il set di computer in cui si esegue lo script di training o si ospita la distribuzione del servizio. Questo percorso può essere il computer locale o una risorsa di calcolo remota.

Azure Machine Learning introduce due risorse di calcolo basate sul cloud completamente gestite configurate per le attività di Machine Learning:

* <a name="compute-instance"></a>**Istanza di calcolo** ([Computeinstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance?view=azure-ml-py)): un'istanza di calcolo è una macchina virtuale (VM) che include più strumenti e ambienti installati per Machine Learning. Usare un'istanza di calcolo come workstation di sviluppo per avviare l'esecuzione di notebook di esempio senza che sia necessaria alcuna installazione. Può essere usato anche come destinazione di calcolo per il training e l'inferenza dei processi.
* **Cluster di calcolo** ([Amlcompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py)): cluster di macchine virtuali con funzionalità di scalabilità a più nodi. Viene scalato automaticamente quando viene inviato un processo. Più adatto per le destinazioni di calcolo per processi e produzione di grandi dimensioni. Usare come destinazione di calcolo per il training o per la distribuzione di sviluppo/test.

Per ulteriori informazioni sulle destinazioni di calcolo di training, vedere [training di destinazioni di calcolo](concept-compute-target.md#train).  Per ulteriori informazioni sulle destinazioni di calcolo della distribuzione, vedere [destinazioni di distribuzione](concept-compute-target.md#deploy).

## <a name="datasets-and-datastores"></a>Set di dati e archivi dati

[**Azure Machine Learning DataSet**](concept-data.md#datasets)  semplificano l'accesso e l'utilizzo dei dati. I set di dati gestiscono i dati in diversi scenari, come il training di modelli e la creazione di pipeline. L'SDK di Azure Machine Learning permette di accedere alle risorse di archiviazione sottostanti, esplorare i dati e gestire il ciclo di vita delle diverse definizioni dei dati.

I set di dati offrono alcuni metodi per lavorare con i dati nei formati più diffusi, ad esempio l'uso di `from_delimited_files()` o `to_pandas_dataframe()`.

Per altre informazioni, vedere [Creare e registrare set di dati di Azure Machine Learning](how-to-create-register-datasets.md).  Per altri esempi d'uso dei set di dati, vedere i [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial).

Un [**archivio dati**](concept-data.md#datastores) è un'astrazione di archiviazione in un account di archiviazione di Azure. Ogni area di lavoro presenta un archivio dati predefinito, ma è possibile registrare altri archivi dati. Usare l'API di Python SDK o l'interfaccia della riga di comando di Azure Machine Learning per archiviare e recuperare i file dall'archivio dati. 

## <a name="models"></a>Modelli

Nella forma più semplice, un modello è un frammento di codice che accetta un input e produce output. La creazione di un modello di apprendimento automatico comporta la selezione di un algoritmo, la relativa fornitura di dati e l' [ottimizzazione degli iperparametri](how-to-tune-hyperparameters.md). Il training è un processo iterativo che produce un modello con training, che racchiude ciò che il modello ha appreso durante il processo di training.

Un modello viene generato da un' [esecuzione](#runs) di un [esperimento](#experiments) in Azure Machine Learning. È anche possibile usare un modello il cui training sia stato eseguito all'esterno di Azure Machine Learning. Si [registra quindi il modello](#register-model) nell'area di lavoro.

Azure Machine Learning è indipendente dal framework. Durante la creazione di un modello, è possibile usare un qualsiasi framework di apprendimento automatico comune, ad esempio Scikit-learn, XGBoost, PyTorch, TensorFlow e Chainer.

Per un esempio di training di un modello con Scikit-learn, vedere Esercitazione: eseguire il training di [un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="model-registry"></a><a name="register-model"></a> Registro modelli
[Area di lavoro](#workspace)  >  **Registro modelli**

Il **Registro di sistema del modello** consente di tenere traccia di tutti i modelli nell'area di lavoro Azure Machine Learning.

I modelli vengono identificati dal nome e dalla versione. Ogni volta che si registra un modello con lo stesso nome di uno esistente, il registro presuppone che si tratti di una nuova versione. La versione viene incrementata e il nuovo modello registrato con lo stesso nome.

Quando si registra il modello, è possibile specificare altri tag di metadati e quindi usarli durante la ricerca di modelli.

> [!TIP]
> Un modello registrato è un contenitore logico per uno o più file che costituiscono il modello. Se, ad esempio, si dispone di un modello archiviato in più file, è possibile registrarli come singolo modello nell'area di lavoro di Azure Machine Learning. Dopo la registrazione, è possibile scaricare o distribuire il modello registrato e ricevere tutti i file registrati.

Non è possibile eliminare un modello registrato in uso da parte di una distribuzione attiva.

Per un esempio di registrazione di un modello, vedere [Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md).


### <a name="environments"></a>Ambienti

[Area di lavoro](#workspace)  >  **Ambienti**

Un [ambiente](concept-environments.md) è l'incapsulamento dell'ambiente in cui si verifica il training o il punteggio del modello di machine learning. L'ambiente specifica i pacchetti Python, le variabili di ambiente e le impostazioni software per gli script di training e di assegnazione dei punteggi.

Per esempi di codice, vedere la sezione "gestire gli ambienti" di [come usare gli ambienti](how-to-use-environments.md#manage-environments).

### <a name="experiments"></a>Sperimentazioni

[Area di lavoro](#workspace)  >  **Esperimenti**

Un esperimento è un raggruppamento di più esecuzioni da uno script specifico. Appartiene sempre a un'area di lavoro. Quando si invia un'esecuzione, si fornisce un nome dell'esperimento. Le informazioni per l'esecuzione vengono archiviate in tale esperimento. Se il nome non esiste quando si invia un esperimento, viene creato automaticamente un nuovo esperimento.

Per un esempio d'uso di un esperimento, vedere [Esercitazione: Eseguire il training del primo modello](tutorial-1st-experiment-sdk-train.md).

### <a name="runs"></a>Esecuzioni

[Area di lavoro](#workspace)  >  [Esperimenti](#experiments)  >  **Esegui**

Per esecuzione si intende una singola esecuzione di uno script di training. Di norma, un esperimento contiene più esecuzioni.

Azure Machine Learning registra tutte le esecuzioni e archivia le informazioni seguenti nell'esperimento:

* Metadati sull'esecuzione (timestamp, durata e così via)
* Metriche registrate dallo script
* File di output raccolti automaticamente dall'esperimento o caricati in modo esplicito dall'utente
* Uno snapshot della directory che contiene gli script, precedente all'esecuzione

Un'esecuzione viene generata quando si invia uno script per eseguire il training di un modello. Un'esecuzione può avere zero o più esecuzioni figlio. L'esecuzione di primo livello, ad esempio, potrebbe avere due esecuzioni figlio, ognuna delle quali può avere a sua volte le proprie esecuzioni figlio.

### <a name="run-configurations"></a>Configurazioni di esecuzione

[Area di lavoro](#workspace)  >  [Esperimenti](#experiments)  >  [Esegui](#runs)  >  **Configurazione di esecuzione**

Una configurazione di esecuzione è un set di istruzioni che definisce come uno script deve essere eseguito in una destinazione di calcolo specificata. La configurazione include un'ampia serie di definizioni di comportamento, ad esempio se usare un ambiente Python esistente oppure un ambiente Conda compilato a partire da una specifica.

Una configurazione di esecuzione può essere resa permanente in un file all'interno della directory che contiene lo script di training.   Oppure può essere costruito come oggetto in memoria e utilizzato per inviare un'esecuzione.

Per degli esempi di configurazioni di esecuzione, vedere [Selezionare e usare una destinazione di calcolo per eseguire il training del modello](how-to-set-up-training-targets.md).

### <a name="estimators"></a>Oggetti estimator

Per facilitare il training dei modelli con i framework più diffusi, la classe di stima consente di costruire facilmente configurazioni di esecuzione. È possibile creare e usare un oggetto [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) generico per inviare script di training che usano qualsiasi framework di apprendimento scelto, ad esempio scikit-learn.

Per le attività PyTorch, TensorFlow e Chainer, Azure Machine Learning fornisce anche le stime rispettivamente per [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py), che semplificano l'uso di questi framework.

Per altre informazioni, vedere gli articoli seguenti:

* [Eseguire il training di modelli di Machine Learning mediante stime](how-to-train-ml-models.md).
* [Eseguire il training di modelli di Deep Learning Pytorch su larga scala con Azure Machine Learning](how-to-train-pytorch.md).
* [Eseguire il training di modelli di Deep Learning TensorFlow su larga scala con Azure Machine Learning e registrarli](how-to-train-tensorflow.md).
* [Eseguire il training di modelli di Deep Learning Chainer su larga scala con Azure Machine Learning e registrarli](how-to-train-ml-models.md).

### <a name="snapshots"></a>Snapshot

[Area di lavoro](#workspace)  >  [Esperimenti](#experiments)  >  [Esegui](#runs)  >  **Snapshot**

Quando si invia un'esecuzione, Azure Machine Learning consente di comprimere la directory che contiene lo script come file ZIP e lo invia alla destinazione di calcolo. Il file ZIP viene quindi estratto e lo script eseguito in questa posizione. Azure Machine Learning archivia inoltre il file ZIP come snapshot come parte del record di esecuzione. Chiunque abbia accesso all'area di lavoro può esplorare un record di esecuzione e scaricare lo snapshot.


### <a name="logging"></a>Registrazione

Quando si sviluppa la soluzione, usare Python SDK di Azure Machine Learning nello script di Python per registrare metriche arbitrarie. Dopo l'esecuzione, eseguire query sulle metriche per determinare se l'esecuzione ha prodotto il modello che si desidera distribuire.


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Rilevamento e integrazione di Git

Quando si avvia un'esecuzione di training in cui la directory di origine è un repository Git locale, le informazioni sul repository vengono archiviate nella cronologia di esecuzione. Questa operazione funziona con le esecuzioni inviate usando una stima, una pipeline di Machine Learning o un'esecuzione di script. Funziona anche per le esecuzioni inviate dall'SDK o dall'interfaccia della riga di comando di Machine Learning.

Per altre informazioni, vedere [Integrazione di Git con Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="deployment"></a>Distribuzione

Si distribuisce un [modello registrato](#register-model) come endpoint del servizio. Sono necessari i componenti seguenti:

* **Ambiente**. Questo ambiente incapsula le dipendenze necessarie per eseguire il modello per l'inferenza.
* **Codice**di assegnazione dei punteggi. Questo script accetta le richieste, assegna un punteggio alle richieste usando il modello e restituisce i risultati.
* **Configurazione di inferenza**. La configurazione dell'inferenza specifica l'ambiente, lo script di ingresso e altri componenti necessari per eseguire il modello come servizio.

Per altre informazioni su questi componenti, vedere [distribuire modelli con Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="endpoints"></a>Endpoint

[Area di lavoro](#workspace)  >  **Endpoint**

Un endpoint è la creazione di un'istanza del modello in un servizio Web che può essere ospitato nel cloud o in un modulo IoT per le distribuzioni integrate nei dispositivi.

#### <a name="web-service-endpoint"></a>Endpoint servizio Web

Quando si distribuisce un modello come servizio Web, l'endpoint può essere distribuito in istanze di Azure Container, servizio Azure Kubernetes o FPGA. Il servizio viene creato dal modello, lo script e i file associati. Questi vengono inseriti in un'immagine del contenitore di base che contiene l'ambiente di esecuzione per il modello. L'immagine presenta un endpoint HTTP con bilanciamento del carico che riceve le richieste di punteggio inviate al servizio Web.

È possibile abilitare la telemetria Application Insights o i dati di telemetria del modello per monitorare il servizio Web. I dati di telemetria sono accessibili solo all'utente.  Viene archiviato nelle istanze di Application Insights e dell'account di archiviazione.

Se è stata abilitata la scalabilità automatica, la distribuzione verrà ridimensionata automaticamente da Azure.

Per un esempio di distribuzione di un modello come servizio Web, vedere [Distribuire un modello di classificazione delle immagini in Istanze di Azure Container](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-endpoints"></a>Endpoint del modulo IoT

Un endpoint del modulo IoT distribuito è un contenitore Docker che include il modello, lo script/associazione associato e qualsiasi altra dipendenza. Questi moduli vengono distribuiti tramite Azure IoT Edge nei dispositivi perimetrali.

Se il monitoraggio è abilitato, Azure raccoglie i dati di telemetria dal modello all'interno del modulo Azure IoT Edge. I dati di telemetria sono accessibili solo per l'utente e archiviati nell'istanza dell'account di archiviazione.

Azure IoT Edge garantisce che il modulo sia in esecuzione e monitora il dispositivo che lo ospita.
. 
## <a name="automation"></a>Automazione

### <a name="azure-machine-learning-cli"></a>Interfaccia della riga di comando di Azure Machine Learning 

L'interfaccia della riga di comando di [Azure Machine Learning](reference-azure-machine-learning-cli.md) è un'estensione dell'interfaccia della riga di comando di Azure, un'interfaccia della riga di comando multipiattaforma per la piattaforma Azure. Questa estensione fornisce i comandi per automatizzare le attività di machine learning.

### <a name="ml-pipelines"></a>Pipeline di Machine Learning

Si usano le [pipeline di Machine Learning](concept-ml-pipelines.md) per creare e gestire i flussi di lavoro che uniscono le fasi di machine learning. Ad esempio, una pipeline può includere le fasi di preparazione dei dati, training del modello, distribuzione del modello e inferenza/punteggio. Ogni fase può includere più passaggi, ciascuno dei quali può essere eseguito in modo automatico in varie destinazioni di calcolo. 

I passaggi della pipeline sono riutilizzabili e possono essere eseguiti senza rieseguire i passaggi precedenti se l'output di questi passaggi non è stato modificato. Ad esempio, è possibile ripetere il training di un modello senza eseguire nuovamente costosi passaggi di preparazione dei dati se questi non sono stati modificati. Le pipeline consentono anche ai data scientist di collaborare mentre lavorano su aree separate di un flusso di lavoro di apprendimento automatico.

## <a name="interacting-with-machine-learning"></a>Interazione con Machine Learning

> [!IMPORTANT]
> Gli strumenti contrassegnati (anteprima) di seguito sono attualmente disponibili in anteprima pubblica.
> La versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+  Interagire con il servizio in qualsiasi ambiente Python con l'[SDK Azure Machine Learning per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Interagisci con il servizio in qualsiasi ambiente R con [Azure Machine Learning SDK per r](https://azure.github.io/azureml-sdk-for-r/reference/index.html) (anteprima).
+ Usare la [finestra di progettazione di Azure Machine Learning (anteprima)](concept-designer.md) per eseguire i passaggi del flusso di lavoro senza scrivere alcun codice. Per usare la finestra di progettazione è necessaria un' [area di lavoro aziendale](concept-workspace.md#upgrade).
+ Usare [Azure Machine Learning interfaccia](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) della riga di comando per l'automazione.
+ L’[acceleratore di soluzione molti modelli](https://aka.ms/many-models) (anteprima) si basa su Azure Machine Learning consente di eseguire il training, utilizzare e gestire centinaia o addirittura migliaia di modelli di Machine Learning.

## <a name="next-steps"></a>Passaggi successivi

Per un'introduzione ad Azure Machine Learning, vedere:

* [Cos'è Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Creare un'area di lavoro di Machine Learning di Azure](how-to-manage-workspace.md)
* [Esercitazione (parte 1): Eseguire il training di un modello](tutorial-train-models-with-aml.md)
