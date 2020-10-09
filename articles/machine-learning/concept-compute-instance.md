---
title: Che cos'è un'istanza di calcolo di Azure Machine Learning?
titleSuffix: Azure Machine Learning
description: Informazioni sull'istanza di calcolo di Azure Machine Learning, una workstation basata su cloud completamente gestita.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 10/02/2020
ms.openlocfilehash: f32783b18b5454164567910aa369739d025b8be0
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826897"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Che cos'è un'istanza di calcolo di Azure Machine Learning?

Un'istanza di calcolo Azure Machine Learning è una workstation gestita basata sul cloud per i data scientist.

Le istanze di calcolo semplificano l'avvio dello sviluppo di Azure Machine Learning e offrono funzionalità di gestione e amministrazione aziendali agli amministratori IT.  

Usa un'istanza di calcolo come ambiente di sviluppo completamente configurato e gestito nel cloud per Machine Learning. Possono anche essere usati come destinazione di calcolo per il training e l'inferenza a scopo di sviluppo e test.  

Per il training del modello di livello di produzione, usare un [cluster di calcolo Azure Machine Learning](how-to-create-attach-compute-cluster.md) con funzionalità di scalabilità a più nodi. Per la distribuzione del modello di livello di produzione, usare il [cluster di servizi Kubernetes di Azure](how-to-deploy-azure-kubernetes-service.md).

## <a name="why-use-a-compute-instance"></a>Perché usare un'istanza di calcolo?

Un'istanza di calcolo è una workstation basata su cloud completamente gestita, ottimizzata per l'ambiente di sviluppo di machine learning. e in grado di offrire i vantaggi indicati di seguito.

|Vantaggi principali|Descrizione|
|----|----|
|Produttività|È possibile compilare e distribuire modelli usando notebook integrati e gli strumenti seguenti in Azure Machine Learning Studio:<br/>-  Jupyter<br/>-  JupyterLab<br/>-RStudio (anteprima)<br/>L'istanza di calcolo è completamente integrata con Azure Machine Learning area di lavoro e studio. È possibile condividere notebook e dati con altri data scientist nell'area di lavoro.<br/> È anche possibile usare [vs code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630) con le istanze di calcolo.
|Gestione e sicurezza|Riduzione del footprint per la sicurezza e aggiunta di conformità ai requisiti di sicurezza aziendali. Le istanze di calcolo offrono criteri di gestione affidabili e configurazioni di rete sicure, ad esempio:<br/><br/>-Provisioning automatico da modelli di Gestione risorse o SDK Azure Machine Learning<br/>- [Controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](/azure/role-based-access-control/overview)<br/>- [Supporto della rete virtuale](how-to-enable-virtual-network.md#compute-instance)<br/>- Criteri SSH per abilitare/disabilitare l'accesso SSH<br/>TLS 1,2 abilitato |
|Preconfigurato &nbsp; per &nbsp; ml|Risparmio di tempo nelle attività di configurazione con pacchetti AML preconfigurati e aggiornati, framework di Deep Learning e driver GPU.|
|Personalizzazione completa|Ampio supporto per i tipi di macchine virtuali di Azure, tra cui GPU e personalizzazione di basso livello persistente, come l'installazione di pacchetti e driver, che semplifica gli scenari avanzati. |

È possibile [creare un'istanza di calcolo](how-to-create-manage-compute-instance.md?tabs=python#create) autonomamente oppure un amministratore può [creare un'istanza di calcolo](how-to-create-manage-compute-instance.md?tabs=python#create-on-behalf-of-preview).

## <a name="tools-and-environments"></a><a name="contents"></a>Strumenti e ambienti

> [!IMPORTANT]
> Gli elementi contrassegnati (anteprima) in questo articolo sono attualmente in anteprima pubblica.
> La versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

L'istanza di calcolo di Azure Machine Learning consente di creare, eseguire il training e distribuire modelli in un'esperienza di notebook completamente integrata nell'area di lavoro.

È possibile eseguire notebook Jupyter in [vs code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630) usando l'istanza di calcolo come server remoto senza necessità di SSH. È anche possibile abilitare l'integrazione VS Code tramite l' [estensione SSH remota](https://devblogs.microsoft.com/python/enhance-your-azure-machine-learning-experience-with-the-vs-code-extension/).

È possibile [installare i pacchetti](how-to-create-manage-compute-instance.md#install-packages) e [aggiungere i kernel](how-to-create-manage-compute-instance.md#add-new-kernels) all'istanza di calcolo.  

Gli strumenti e gli ambienti seguenti sono già installati nell'istanza di calcolo: 

|Strumenti generali e ambienti|Dettagli|
|----|:----:|
|Driver|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI library||
|Interfaccia della riga di comando di Azure ||
|Esempi di Azure Machine Learning ||
|Docker||
|Nginx||
|NCCL 2.0 ||
|Protobuf|| 

|Strumenti e ambienti **R**|Dettagli|
|----|:----:|
|Edizione Open Source del server RStudio (anteprima)||
|Kernel R||
|Azure Machine Learning SDK per R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>Esempi di SDK|

|Strumenti e ambienti **PYTHON**|Dettagli|
|----|----|
|Anaconda Python||
|Jupyter ed estensioni||
|Jupyterlab ed estensioni||
[Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)</br>da PyPI|Include la maggior parte dei pacchetti aggiuntivi di azureml.  Per visualizzare l'elenco completo, [aprire una finestra del terminale nell'istanza di calcolo in uso](how-to-run-jupyter-notebooks.md#terminal) e avviare l'esecuzione <br/> `conda list -n azureml_py36 azureml*` |
|Altri pacchetti PyPI|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Pacchetti Conda|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Pacchetti Deep Learning|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|Pacchetti ONNX|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Esempi di Azure Machine Learning Python e R SDK||

Tutti i pacchetti Python sono installati nell'ambiente **Python 3.6 - AzureML**.  

## <a name="accessing-files"></a>Accesso ai file

I notebook e gli script R vengono archiviati nell'account di archiviazione predefinito dell'area di lavoro nella condivisione file di Azure.  Tali file si trovano nella directory dei file dell'utente. Questo tipo di archiviazione semplifica la condivisione di notebook tra le istanze di calcolo. L'account di archiviazione consente inoltre di mantenere i notebook in modo sicuro quando si arresta o si elimina un'istanza di calcolo.

L'account di condivisione file di Azure dell'area di lavoro viene montato come unità nell'istanza di calcolo. Tale unità è la directory di lavoro predefinita per Jupyter, Jupyter Lab e RStudio. Ciò significa che i notebook e altri file creati in Jupyter, JupyterLab o RStudio vengono archiviati automaticamente nella condivisione file e disponibili per l'uso anche in altre istanze di calcolo.

I file nella condivisione file sono accessibili da tutte le istanze di calcolo presenti nella stessa area di lavoro. Tutte le modifiche apportate ai file nell'istanza di calcolo vengono salvate in modo affidabile nella condivisione file.

È anche possibile clonare gli ultimi esempi di Azure Machine Learning nella cartella della directory dei file utente nella condivisione file dell'area di lavoro.

La scrittura di file di piccole dimensioni può essere più lenta nelle unità di rete rispetto alla scrittura nel disco locale dell'istanza di calcolo.  Se si scrivono molti file di piccole dimensioni, provare a usare una directory direttamente nell'istanza di calcolo, ad esempio una directory `/tmp`. Si noti che questi file non saranno accessibili da altre istanze di calcolo. 

È possibile usare la `/tmp` Directory nell'istanza di calcolo per i dati temporanei.  Tuttavia, non scrivere file di grandi dimensioni di dati nel disco del sistema operativo dell'istanza di calcolo.  Usare invece gli [archivi dati](concept-azure-machine-learning-architecture.md#datasets-and-datastores) . Se è stata installata l'estensione JupyterLab git, può anche causare un rallentamento delle prestazioni dell'istanza di calcolo.

## <a name="managing-a-compute-instance"></a>Gestione di un'istanza di calcolo

Nell'area di lavoro in Azure Machine Learning Studio selezionare **Calcolo**, quindi selezionare **Istanza di calcolo** nella parte superiore.

![Gestire un'istanza di calcolo](./media/concept-compute-instance/manage-compute-instance.png)

È possibile eseguire queste operazione:

* [Creare un'istanza di calcolo](#create). 
* Aggiornare la scheda istanze di calcolo.
* Avviare, arrestare e riavviare un'istanza di calcolo.  Si paga per l'istanza ogni volta che viene eseguito. Arrestare l'istanza di calcolo quando non viene usata per ridurre i costi. L'arresto di un'istanza di calcolo la dealloca. e quindi riavviarla quando è necessario.
* Eliminare un'istanza di calcolo.
* Filtrare l'elenco delle istanze di calcolo in modo da visualizzare solo quelle create.

Per ogni istanza di calcolo nell'area di lavoro che è possibile usare, è possibile:

* Accedere a Jupyter, JupyterLab, RStudio nell'istanza di calcolo
* Accedere con SSH all'istanza di calcolo. L'accesso SSH è disabilitato per impostazione predefinita, ma può essere abilitato al momento della creazione dell'istanza di calcolo. L'accesso SSH avviene tramite il meccanismo di chiave pubblica/privata. Nella scheda sono disponibili i dettagli per la connessione SSH, ad esempio indirizzo IP, nome utente e numero di porta.
* Ottenere informazioni dettagliate su un'istanza di calcolo specifica, ad esempio l'indirizzo IP e l'area.

Il [controllo degli accessi in base al ruolo](/azure/role-based-access-control/overview) consente di controllare quali utenti nell'area di lavoro possono creare, eliminare, avviare, arrestare e riavviare un'istanza di calcolo. Tutti gli utenti con il ruolo collaboratore e proprietario dell'area di lavoro possono creare, eliminare, avviare, arrestare e riavviare le istanze di calcolo nell'area di lavoro. Tuttavia, solo l'autore di un'istanza di calcolo specifica o l'utente assegnato se è stato creato per loro conto, può accedere a Jupyter, JupyterLab e RStudio in tale istanza di calcolo. Un'istanza di calcolo è dedicata a un singolo utente che ha accesso alla radice e può eseguire il terminale in tramite Jupyter/JupyterLab/RStudio. L'istanza di calcolo disporrà di un accesso utente singolo e tutte le azioni useranno l'identità dell'utente per il RBAC e l'attribuzione delle esecuzioni dell'esperimento. L'accesso SSH viene controllato tramite il meccanismo di chiave pubblica/privata.

Queste azioni possono essere controllate da RBAC:
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *Microsoft. MachineLearningServices/Workspaces/Computes/Start/Action*
* *Microsoft. MachineLearningServices/Workspaces/Computes/Stop/Action*
* *Microsoft. MachineLearningServices/Workspaces/Computes/restart/Action*

### <a name="create-a-compute-instance"></a><a name="create"></a>Creare un'istanza di ambiente di calcolo

Nell'area di lavoro in Azure Machine Learning Studio [creare una nuova istanza di calcolo](how-to-create-attach-compute-studio.md#compute-instance) dalla sezione **calcolo** o nella sezione **notebook** quando si è pronti per eseguire uno dei notebook. 

È anche possibile creare un'istanza nei modi seguenti:
* Direttamente dall' [esperienza dei notebook integrati](tutorial-1st-experiment-sdk-setup.md#azure)
* Nel portale di Azure
* Da Azure Resource Manager modello. Per un modello di esempio, vedere la pagina relativa alla [creazione di un modello di istanza di calcolo Azure Machine Learning](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).
* Con [Azure Machine Learning SDK](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/machine-learning/concept-compute-instance.md)
* Dall'estensione dell'interfaccia della riga [di comando per Azure Machine Learning](reference-azure-machine-learning-cli.md#computeinstance)

I core dedicati per area per la quota della famiglia di VM e la quota di area totale, applicabile alla creazione dell'istanza di calcolo, sono unificati e condivisi con Azure Machine Learning quota del cluster di calcolo di training. Se si arresta l'istanza di calcolo, la quota non viene rilasciata per garantire che sia possibile riavviare l'istanza di calcolo.


### <a name="create-on-behalf-of-preview"></a>Crea per conto di (anteprima)

In qualità di amministratore, è possibile creare un'istanza di calcolo per conto di un data scientist a cui assegnare l'istanza:
* [Azure Resource Manager modello](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).  Per informazioni dettagliate su come trovare TenantID e ObjectID necessari in questo modello, vedere [trovare ID oggetto Identity per la configurazione dell'autenticazione](../healthcare-apis/find-identity-object-ids.md).  È anche possibile trovare questi valori nel portale Azure Active Directory.
* API REST

Per i data scientist per cui si crea l'istanza di calcolo sono necessarie le autorizzazioni RBAC seguenti: 
* *Microsoft. MachineLearningServices/Workspaces/Computes/Start/Action*
* *Microsoft. MachineLearningServices/Workspaces/Computes/Stop/Action*
* *Microsoft. MachineLearningServices/Workspaces/Computes/restart/Action*
* *Microsoft. MachineLearningServices/Workspaces/Computes/applicationaccess/Action*

Il data scientist può avviare, arrestare e riavviare l'istanza di calcolo. Possono usare l'istanza di calcolo per:
* Jupyter
* JupyterLab
* RStudio
* Notebook integrati

## <a name="compute-target"></a>Destinazione del calcolo

Le istanze di calcolo possono essere usate come una [destinazione di calcolo di training](concept-compute-target.md#train) simile ai cluster di training di calcolo di Azure Machine Learning. 

Un'istanza di calcolo:
* Dispone di una coda di processi.
* Esegue processi in modo sicuro in un ambiente di rete virtuale, senza richiedere alle aziende di aprire la porta SSH. Il processo viene eseguito in un ambiente in contenitori e inserisce le dipendenze del modello in un contenitore docker.
* Può eseguire più processi di piccole dimensioni in parallelo (anteprima).  Due processi per core possono essere eseguiti in parallelo mentre il resto dei processi viene accodato.
* Supporta processi di training distribuiti con più GPU a nodo singolo

È possibile usare l'istanza di calcolo come destinazione di distribuzione di inferenza locale per gli scenari di test/debug.


## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Cosa è successo alla macchina virtuale del notebook?

Le istanze di calcolo sostituiscono la macchina virtuale del notebook.  

Tutti i file del notebook archiviati nella condivisione file dell'area di lavoro e i dati negli archivi dati dell'area di lavoro sono accessibili da un'istanza di calcolo. Tuttavia, eventuali pacchetti personalizzati installati in precedenza in una macchina virtuale notebook dovranno essere reinstallati nell'istanza di calcolo. Le limitazioni della quota, che si applicano alla creazione di cluster di calcolo, verranno applicate anche alla creazione dell'istanza di calcolo.

Non è possibile creare nuove macchine virtuali del notebook. È comunque possibile accedere e usare le macchine virtuali del notebook create con funzionalità complete. È possibile creare istanze di calcolo nella stessa area di lavoro delle macchine virtuali del notebook esistenti.


## <a name="next-steps"></a>Passaggi successivi

* [Creare e gestire un'istanza di calcolo](how-to-create-manage-compute-instance.md)
* In [Esercitazione: Eseguire il training del primo modello di Machine Learning](tutorial-1st-experiment-sdk-train.md) viene illustrato come usare un'istanza di calcolo con un notebook integrato.
