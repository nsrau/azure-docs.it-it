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
ms.date: 12/13/2019
ms.openlocfilehash: 280851b2fea0b8100a7d0f8ec8105109a41c8c83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283927"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Che cos'è un'istanza di calcolo di Azure Machine Learning?

Un'istanza di calcolo di Azure Machine Learning (anteprima) è una workstation basata su cloud completamente gestita per i data scientist. 

Le istanze di calcolo semplificano l'introduzione allo sviluppo di Azure Machine Learning, oltre a fornire funzionalità di gestione e preparazione aziendale per gli amministratori IT.  

Usare un'istanza di elaborazione come ambiente di sviluppo completamente configurato e gestito nel cloud.

Le istanze di calcolo vengono in genere utilizzate come ambienti di sviluppo.  Possono anche essere utilizzati come destinazione di calcolo per il training e l'inferenza per lo sviluppo e il test.  Per le attività di grandi dimensioni, un cluster di [calcolo](how-to-set-up-training-targets.md#amlcompute) di Azure Machine Learning con funzionalità di scalabilità multinodo è una scelta di destinazione di calcolo migliore.


## <a name="why-use-a-compute-instance"></a>Perché usare un'istanza di calcolo?

Un'istanza di calcolo è una workstation basata su cloud completamente gestita ottimizzata per l'ambiente di sviluppo dell'apprendimento automatico. offrendo i seguenti vantaggi:

|Vantaggi principali||
|----|----|
|Produttività|Gli scienziati dei dati possono creare e distribuire modelli utilizzando notebook integrati e i seguenti strumenti nel browser Web:<br/>- Jupyter<br/>- JupyterLab<br/>- Studio|
|Gestione & sicura|Riduci il tuo ingombro di sicurezza e aggiungi la conformità ai requisiti di sicurezza aziendali. Le istanze di calcolo forniscono criteri di gestione affidabili e configurazioni di rete sicure, ad esempio:Compute instances provide robust management policies and secure networking configurations such as:<br/><br/>- Provisioning automatico dai modelli di Resource Manager o Azure Machine Learning SDK<br/>- [Controllo degli accessi in base al ruolo](/azure/role-based-access-control/overview)<br/>- [Supporto per reti virtuali](how-to-enable-virtual-network.md#compute-instance)<br/>- Criteri SSH per abilitare/disabilitare l'accesso SSH|
|Preconfigurato&nbsp;&nbsp;o ML|Risparmia tempo sulle attività di configurazione con pacchetti ML preconfigurati e aggiornati, framework di deep learning, driver GPU.|
|Completamente personalizzabile|Ampio supporto per i tipi di macchine virtuali di Azure, tra cui GPU e personalizzazioni persistenti di basso livello, ad esempio l'installazione di pacchetti e driver, semplificano gli scenari avanzati. |

## <a name="tools-and-environments"></a><a name="contents"></a>Strumenti e ambienti

L'istanza di calcolo di Azure Machine Learning consente di creare, addestrare e distribuire modelli in un'esperienza notebook completamente integrata nell'area di lavoro.


Questi strumenti e ambienti vengono installati nell'istanza di calcolo:These tools and environments are installed on the compute instance: 

|Strumenti generali & ambienti|Dettagli|
|----|:----:|
|Driver|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Libreria Intel MPI||
|Interfaccia della riga di comando di Azure ||
|Esempi di Azure Machine LearningAzure Machine Learning samples ||
|Motore EDAT di Azure Machine LearningAzure Machine Learning EDAT engine ||
|Docker||
|Nginx||
|NCCL 2.0 ||
|Protobuf|| 

|**Ambienti** di & degli strumenti R|Dettagli|
|----|:----:|
|Edizione Open Source del server RStudio||
|Kernel R||
|Azure Machine Learning SDK for R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>Esempi di SDK|

|**Strumenti PYTHON** & ambienti|Dettagli|
|----|----|
|Anaconda Python||
|Jupyter ed estensioni||
|Jupyterlab ed estensioni||
|Visual Studio Code ||
[Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>da PyPI|`azureml-sdk[notebooks,contrib,automl,explain]`</br>`azureml-contrib-datadrift`</br>`azureml-telemetry`</br>`azureml-tensorboard`</br>`azureml-contrib-opendatasets`</br>`azureml-opendatasets`</br>`azureml-contrib-reinforcementlearning`</br>`azureml-mlflow`</br>`azureml-contrib-interpret` |
|Altri pacchetti PyPI|`jupytext`</br>`jupyterlab-git`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Pacchetti Conda|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Pacchetti di deep learning|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|Pacchetti ONNX|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Esempi di Azure Machine Learning Python & R SDKAzure Machine Learning Python & R SDK samples||

I pacchetti Python sono tutti installati nell'ambiente **Python 3.6 - AzureML.Python** packages are all installed in the Python 3.6 - AzureML environment.  

Le istanze di calcolo vengono in genere utilizzate come ambienti di sviluppo.  Possono anche essere utilizzati come destinazione di calcolo per il training e l'inferenza per lo sviluppo e il test.  Per le attività di grandi dimensioni, un cluster di [calcolo](how-to-set-up-training-targets.md#amlcompute) di Azure Machine Learning con funzionalità di scalabilità multinodo è una scelta di destinazione di calcolo migliore.

### <a name="installing-packages"></a>Installazione di pacchetti

È possibile installare i pacchetti direttamente in un notebook Jupyter o Rstudio:

* RStudio Utilizzare la scheda **Pacchetti** in basso a destra o la scheda **Console** in alto a sinistra.  
* Python: Aggiungi il codice di installazione ed eseguilo in una cella del notebook Jupyter.

In alternativa, è possibile accedere a una finestra del terminale in uno dei seguenti modi:

* RStudio: selezionare la scheda **Terminale** in alto a sinistra.
* Jupyter Lab: seleziona il riquadro **Terminale** sotto l'intestazione **Altro** nella scheda Launcher.
* Jupyter: seleziona **Nuovo>Terminale** in alto a destra nella scheda File.
* SSH alla macchina.  Installare quindi i pacchetti Python nell'ambiente **Python 3.6 - AzureML.Then** install Python packages into the Python 3.6 - AzureML environment.  Installare i pacchetti R nell'ambiente **R.Install** R packages into the R environment.

## <a name="accessing-files"></a>Accesso ai file

I blocchi appunti e gli script R vengono archiviati nell'account di archiviazione predefinito dell'area di lavoro nella condivisione file di Azure.Notebooks and R scripts are stored in the default storage account of your workspace in Azure file share.  Questi file si trovano nella directory "File utente". Questa archiviazione semplifica la condivisione dei blocchi appunti tra istanze di calcolo. L'account di archiviazione mantiene inoltre i blocchi appunti conservati in modo sicuro quando si arresta o si elimina un'istanza di calcolo.

L'account di condivisione file di Azure dell'area di lavoro viene montato come unità nell'istanza di calcolo. Questa unità è la directory di lavoro predefinita per Jupyter, Jupyter Labs e RStudio.

I file nella condivisione file sono accessibili da tutte le istanze di calcolo nella stessa area di lavoro. Tutte le modifiche apportate a questi file nell'istanza di calcolo verranno rese persistenti nella condivisione file.

È anche possibile clonare gli esempi più recenti di Azure Machine Learning nella cartella nella directory dei file utente nella condivisione file dell'area di lavoro.

La scrittura di file di piccole dimensioni può essere più lenta sulle unità di rete rispetto alla scrittura nella macchina virtuale stessa.  Se si scrivono molti file di piccole dimensioni, provare a `/tmp` utilizzare una directory direttamente nell'istanza di calcolo, ad esempio una directory. Si noti che questi file non saranno accessibili da altre istanze di calcolo nell'area di lavoro.

## <a name="managing-a-compute-instance"></a>Gestione di un'istanza di calcoloManaging a compute instance

Nell'area di lavoro di Azure Machine Learning Studio selezionare **Calcola**, quindi **Calcolo istanza** nella parte superiore.

![Gestire un'istanza di calcoloManage a compute instance](./media/concept-compute-instance/manage-compute-instance.png)

È possibile effettuare le azioni seguenti:

* Creare un'istanza di calcolo. Specificare il nome, il tipo di macchina virtuale di Azure, incluse le GPU (si noti che il tipo di macchina virtuale non può essere modificato dopo la creazione), abilitare/disabilitare l'accesso SSH e configurare le impostazioni della rete virtuale facoltativamente. È anche possibile creare un'istanza direttamente da blocchi appunti integrati, portale di Azure, modello di Resource Manager o Azure Machine Learning SDK. La quota di core dedicati per area che si applica alla creazione di istanze di calcolo è unificata e condivisa con la quota del cluster di calcolo di Azure Machine Learning.The dedicated cores per region quota which applies to compute instance creation is unified and shared with Azure Machine Learning compute cluster quota.
* Aggiornare la scheda delle istanze di calcolo
* Avviare, arrestare e riavviare un'istanza di calcoloStart, stop and restart a compute instance
* Eliminare un'istanza di calcoloDelete a compute instance

Per ogni istanza di calcolo nell'area di lavoro è possibile:For each compute instance in your workspace you can:

* Accedere a Jupyter, JupyterLab, RStudio nell'istanza di calcoloAccess Jupyter, JupyterLab, RStudio on the compute instance
* SSH nell'istanza di calcolo. L'accesso SSH è disabilitato per impostazione predefinita, ma può essere abilitato al momento della creazione dell'istanza di calcolo. L'accesso SSH è tramite il meccanismo di chiave pubblica/privata. La scheda fornisce i dettagli per la connessione SSH, ad esempio l'indirizzo IP, il nome utente e il numero di porta.
* Ottenere informazioni dettagliate su un'istanza di calcolo specifica, ad esempio l'indirizzo IP e l'area.

[Il controllo degli accessi](/azure/role-based-access-control/overview) in base al ruolo consente di controllare quali utenti nell'area di lavoro possono creare, eliminare, avviare, arrestare, riavviare un'istanza di calcolo. Tutti gli utenti nel ruolo di collaboratore e proprietario dell'area di lavoro possono creare, eliminare, avviare, arrestare e riavviare istanze di calcolo nell'area di lavoro. Tuttavia, solo il creatore di un'istanza di calcolo specifica può accedere a Jupyter, JupyterLab e RStudio su tale istanza di calcolo. Il creatore dell'istanza di calcolo ha l'istanza di calcolo dedicata, ha accesso root e può eseguire il terminale tramite Jupyter. L'istanza di calcolo avrà l'accesso singolo utente dell'utente creatore e tutte le azioni utilizzeranno l'identità dell'utente per il controllo degli accessi in base al ruolo e l'attribuzione delle esecuzioni dell'esperimento. L'accesso SSH viene controllato tramite il meccanismo di chiave pubblica/privata.

È anche possibile creare un'istanza
* Direttamente dall'esperienza dei notebook integrati
* Nel portale di Azure
* Dal modello di Azure Resource Manager
* Con Azure Machine Learning SDK

La quota di core dedicati per area, che si applica alla creazione di istanze di calcolo, è unificata e condivisa con la quota del cluster di formazione di Azure Machine Learning.The dedicated cores per region quota, which applies to compute instance creation is unified and shared with Azure Machine Learning training cluster quota. 

## <a name="compute-target"></a>Destinazione di calcolo

Le istanze di calcolo possono essere usate come destinazione di [calcolo](concept-compute-target.md#train) di training in modo simile ai cluster di formazione di elaborazione di Azure Machine Learning.Compute instances can be used as a training compute target similar to Azure Machine Learning compute training clusters. Effettuare il provisioning di una macchina virtuale Multi-GPU per eseguire processi di formazione distribuiti utilizzando TensorFlow/PyTorch estimators. È anche possibile creare una configurazione di esecuzione e usarla per eseguire l'esperimento nell'istanza di calcolo. È possibile usare l'istanza di calcolo come destinazione di distribuzione dell'inferenza locale per gli scenari di test/debugging.

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Che cosa è successo alla macchina virtuale del blocco appunti?

Le istanze di calcolo sostituiscono la macchina virtuale del blocco appunti.  

Tutti i file del blocco appunti archiviati nella condivisione file dell'area di lavoro e i dati negli archivi dati dell'area di lavoro saranno accessibili da un'istanza di calcolo. Tuttavia, tutti i pacchetti personalizzati installati in precedenza in una macchina virtuale del blocco appunti dovranno essere reinstallati nell'istanza di calcolo. Le limitazioni delle quote che si applicano alla creazione di cluster di calcolo verranno applicate anche alla creazione di istanze di calcolo. 

Non è possibile creare nuove macchine virtuali del blocco appunti. Tuttavia, è comunque possibile accedere e usare le macchine virtuali del blocco appunti create, con funzionalità complete. Le istanze di calcolo possono essere create nella stessa area di lavoro delle macchine virtuali del blocco appunti esistenti. 


## <a name="next-steps"></a>Passaggi successivi

 * [Esercitazione: Eseguire il training del primo modello](tutorial-1st-experiment-sdk-train.md) di ML viene illustrato come usare un'istanza di calcolo con un blocco appunti integrato.
