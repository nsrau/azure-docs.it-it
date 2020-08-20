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
ms.date: 07/27/2020
ms.openlocfilehash: c72777bf2a4415a7f773f82a21a121f5e58f2ec0
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88651916"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Che cos'è un'istanza di calcolo di Azure Machine Learning?

Un'istanza di calcolo Azure Machine Learning è una workstation gestita basata sul cloud per i data scientist.

Le istanze di calcolo semplificano l'avvio dello sviluppo di Azure Machine Learning e offrono funzionalità di gestione e amministrazione aziendali agli amministratori IT.  

Usa un'istanza di calcolo come ambiente di sviluppo completamente configurato e gestito nel cloud per Machine Learning. Possono anche essere usati come destinazione di calcolo per il training e l'inferenza a scopo di sviluppo e test.  

Per il training del modello di livello di produzione usare un [cluster di calcolo Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) con funzionalità di scalabilità a più nodi. Per la distribuzione del modello di livello di produzione, usare il [cluster di servizi Kubernetes di Azure](how-to-deploy-azure-kubernetes-service.md).

## <a name="why-use-a-compute-instance"></a>Perché usare un'istanza di calcolo?

Un'istanza di calcolo è una workstation basata su cloud completamente gestita, ottimizzata per l'ambiente di sviluppo di apprendimento automatico e in grado di offrire i vantaggi indicati di seguito.

|Vantaggi principali|Descrizione|
|----|----|
|Produttività|È possibile compilare e distribuire modelli usando notebook integrati e gli strumenti seguenti in Azure Machine Learning Studio:<br/>-  Jupyter<br/>-  JupyterLab<br/>-RStudio (anteprima)<br/>L'istanza di calcolo è completamente integrata con Azure Machine Learning area di lavoro e studio. È possibile condividere notebook e dati con altri data scientist nell'area di lavoro. È anche possibile configurare VS Code lo sviluppo remoto tramite [SSH](how-to-set-up-vs-code-remote.md) |
|Gestione e sicurezza|Riduzione del footprint per la sicurezza e aggiunta di conformità ai requisiti di sicurezza aziendali. Le istanze di calcolo offrono criteri di gestione affidabili e configurazioni di rete sicure, ad esempio:<br/><br/>- Provisioning automatico da modelli di Resource Manager o di Azure Machine Learning SDK<br/>- [Controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](/azure/role-based-access-control/overview)<br/>- [Supporto della rete virtuale](how-to-enable-virtual-network.md#compute-instance)<br/>- Criteri SSH per abilitare/disabilitare l'accesso SSH<br/>TLS 1,2 abilitato |
|Preconfigurato &nbsp; per &nbsp; ml|Risparmio di tempo nelle attività di configurazione con pacchetti AML preconfigurati e aggiornati, framework di Deep Learning e driver GPU.|
|Personalizzazione completa|Ampio supporto per i tipi di macchine virtuali di Azure, tra cui GPU e personalizzazione di basso livello persistente, come l'installazione di pacchetti e driver, che semplifica gli scenari avanzati. |

## <a name="tools-and-environments"></a><a name="contents"></a>Strumenti e ambienti

> [!IMPORTANT]
> Gli strumenti contrassegnati (anteprima) di seguito sono attualmente disponibili in anteprima pubblica.
> La versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

L'istanza di calcolo di Azure Machine Learning consente di creare, eseguire il training e distribuire modelli in un'esperienza di notebook completamente integrata nell'area di lavoro.

Tali strumenti e ambienti sono installati nell'istanza di calcolo. 

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
[Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>da PyPI|Include la maggior parte dei pacchetti aggiuntivi di azureml.  Per visualizzare l'elenco completo, [aprire una finestra del terminale nell'istanza di calcolo in uso](how-to-run-jupyter-notebooks.md#terminal) e avviare l'esecuzione <br/> `conda list -n azureml_py36 azureml*` |
|Altri pacchetti PyPI|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Pacchetti Conda|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Pacchetti Deep Learning|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|Pacchetti ONNX|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Esempi di Azure Machine Learning Python e R SDK||

Tutti i pacchetti Python sono installati nell'ambiente **Python 3.6 - AzureML**.  

### <a name="installing-packages"></a>Installazione di pacchetti

È possibile installare i pacchetti direttamente in un notebook di Jupyter o Rstudio:

* RStudio: usare la scheda **Packages** (Pacchetti) in basso a sinistra o la scheda **Console** in alto a sinistra.  
* Python: aggiungere il codice di installazione e avviare l'esecuzione in una cella del notebook Jupyter.

In alternativa, è possibile accedere a una finestra del terminale in uno dei modi seguenti.

* RStudio: selezionare la scheda **Terminal** (Terminale) in alto a sinistra.
* Jupyter Lab:  selezionare il riquadro **Terminal** (Terminale) nell'intestazione **Other** (Altro) nella scheda Launcher (Utilità di avvio).
* Jupyter:  selezionare **New>Terminal** (Nuovo>Terminale) in alto a destra nella scheda File.
* Accedere con SSH al computer  e quindi installare i pacchetti Python nell'ambiente **Python 3.6 - AzureML**.  Installare i pacchetti R nell'ambiente **R**.

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
* Filtrare l'elenco delle istanze di calcolo con quelle create.  Queste sono le istanze di calcolo a cui è possibile accedere.

Per ogni istanza di calcolo nell'area di lavoro a cui si ha accesso, è possibile:

* Accedere a Jupyter, JupyterLab, RStudio nell'istanza di calcolo
* Accedere con SSH all'istanza di calcolo. L'accesso SSH è disabilitato per impostazione predefinita, ma può essere abilitato al momento della creazione dell'istanza di calcolo. L'accesso SSH avviene tramite il meccanismo di chiave pubblica/privata. Nella scheda sono disponibili i dettagli per la connessione SSH, ad esempio indirizzo IP, nome utente e numero di porta.
* Ottenere informazioni dettagliate su un'istanza di calcolo specifica, ad esempio l'indirizzo IP e l'area.

Il [controllo degli accessi in base al ruolo](/azure/role-based-access-control/overview) consente di controllare quali utenti nell'area di lavoro possono creare, eliminare, avviare, arrestare e riavviare un'istanza di calcolo. Tutti gli utenti con il ruolo collaboratore e proprietario dell'area di lavoro possono creare, eliminare, avviare, arrestare e riavviare le istanze di calcolo nell'area di lavoro. Solo il creatore di un'istanza di calcolo specifica, tuttavia, può accedere a Jupyter, JupyterLab e RStudio in tale istanza di calcolo. L'autore dell'istanza di calcolo dispone dell'istanza di calcolo dedicata, dispone dell'accesso alla radice e può eseguire il terminale tramite Jupyter/JupyterLab/RStudio. All'istanza di calcolo è associato un account di accesso utente singolo del creatore e tutte le azioni usano l'identità dell'utente per il controllo degli accessi in base al ruolo e l'attribuzione delle esecuzioni dell'esperimento. L'accesso SSH viene controllato tramite il meccanismo di chiave pubblica/privata.

Queste azioni possono essere controllate da RBAC:
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *Microsoft. MachineLearningServices/Workspaces/Computes/Start/Action*
* *Microsoft. MachineLearningServices/Workspaces/Computes/Stop/Action*
* *Microsoft. MachineLearningServices/Workspaces/Computes/restart/Action*

### <a name="create-a-compute-instance"></a><a name="create"></a>Creare un'istanza di calcolo

Nell'area di lavoro in Azure Machine Learning Studio creare una nuova istanza di calcolo dalla sezione **calcolo** o nella sezione **notebook** quando si è pronti per eseguire uno dei notebook.

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="Creare una nuova istanza di calcolo":::


|Campo  |Descrizione  |
|---------|---------|
|Nome del calcolo     |  <li>Il nome è obbligatorio e deve avere una lunghezza compresa tra 3 e 24 caratteri.</li><li>I caratteri validi sono lettere maiuscole e minuscole, cifre e il  **-** carattere.</li><li>Il nome deve iniziare con una lettera</li><li>Il nome deve essere univoco in tutti i calcoli esistenti all'interno di un'area di Azure. Se il nome scelto non è univoco, verrà visualizzato un avviso</li><li>Se **-**  viene usato il carattere, deve essere seguito da almeno una lettera in un secondo momento nel nome</li>     |
|Tipo di macchina virtuale |  Scegliere CPU o GPU. Questo tipo non può essere modificato dopo la creazione     |
|Dimensioni della macchina virtuale     |  Le dimensioni delle macchine virtuali supportate possono essere limitate nella propria area. Controllare l' [elenco di disponibilità](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Abilitare/disabilitare l'accesso SSH     |   L'accesso SSH è disabilitato per impostazione predefinita.  L'accesso SSH non può essere. modificato dopo la creazione. Assicurarsi di abilitare l'accesso se si prevede di eseguire il debug in modo interattivo con [vs code remoto](how-to-set-up-vs-code-remote.md)   |
|Impostazioni avanzate     |  Facoltativa. Configurare una rete virtuale. Specificare il **gruppo di risorse**, la **rete virtuale**e la **subnet** per creare l'istanza di calcolo all'interno di una rete virtuale di Azure (VNET). Per ulteriori informazioni, vedere i [requisiti di rete](how-to-enable-virtual-network.md#compute-instance) per vnet.        |

È anche possibile creare un'istanza nei modi seguenti:
* Direttamente dall' [esperienza dei notebook integrati](tutorial-1st-experiment-sdk-setup.md#azure)
* Nel portale di Azure
* Da Azure Resource Manager modello. Per un modello di esempio, vedere la pagina relativa alla [creazione di un modello di istanza di calcolo Azure Machine Learning](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).
* Con Azure Machine Learning SDK
* Dall'estensione dell'interfaccia della riga [di comando per Azure Machine Learning](reference-azure-machine-learning-cli.md#computeinstance)

I core dedicati per area per ogni quota della famiglia di VM e la quota regionale totale, applicabile alla creazione dell'istanza di calcolo. è unificato e condiviso con Azure Machine Learning quota del cluster di calcolo del training. Se si arresta l'istanza di calcolo, la quota non viene rilasciata per garantire che sia possibile riavviare l'istanza di calcolo.

## <a name="compute-target"></a>Destinazione del calcolo

Le istanze di calcolo possono essere usate come [destinazione di calcolo di training](concept-compute-target.md#train) simile a Azure Machine Learning cluster di calcolo. 

Un'istanza di calcolo:
* Dispone di una coda di processi.
* Esegue processi in modo sicuro in un ambiente di rete virtuale, senza richiedere alle aziende di aprire la porta SSH. Il processo viene eseguito in un ambiente in contenitori e inserisce le dipendenze del modello in un contenitore docker.
* Può eseguire più processi di piccole dimensioni in parallelo (anteprima).  Due processi per core possono essere eseguiti in parallelo mentre il resto dei processi viene accodato.
* Supporta processi di training distribuiti con più GPU a nodo singolo

È possibile usare l'istanza di calcolo come destinazione di distribuzione di inferenza locale per gli scenari di test/debug.

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Cosa è successo alla macchina virtuale del notebook?

Le istanze di calcolo sostituiscono la macchina virtuale del notebook.  

Tutti i file del notebook archiviati nella condivisione file dell'area di lavoro e i dati negli archivi dati dell'area di lavoro sono accessibili da un'istanza di calcolo. Eventuali pacchetti personalizzati installati in precedenza in una macchina virtuale del notebook, tuttavia, devono essere reinstallati nell'istanza di calcolo. Le limitazioni di quota che si applicano alla creazione dei cluster di calcolo vengono applicate anche alla creazione dell'istanza di calcolo.

Non è possibile creare nuove macchine virtuali del notebook. È comunque possibile accedere e usare le macchine virtuali del notebook create con funzionalità complete. È possibile creare istanze di calcolo nella stessa area di lavoro delle macchine virtuali del notebook esistenti.


## <a name="next-steps"></a>Passaggi successivi

 * In [Esercitazione: Eseguire il training del primo modello di Machine Learning](tutorial-1st-experiment-sdk-train.md) viene illustrato come usare un'istanza di calcolo con un notebook integrato.
