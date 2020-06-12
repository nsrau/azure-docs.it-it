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
ms.openlocfilehash: 8c03df8fb0cd8f5f092450ebe4c66266d2ff4293
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/24/2020
ms.locfileid: "83816353"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Che cos'è un'istanza di calcolo di Azure Machine Learning?

Un'istanza di calcolo di Azure Machine Learning (anteprima) è una workstation basata su cloud completamente gestita per i data scientist. 

Le istanze di calcolo semplificano l'avvio dello sviluppo di Azure Machine Learning e offrono funzionalità di gestione e amministrazione aziendali agli amministratori IT.  

Usare un'istanza di calcolo come ambiente di sviluppo completamente configurato e gestito nel cloud.

Le istanze di calcolo vengono in genere usate come ambienti di sviluppo,  ma possono essere usate anche come destinazione di calcolo per il training e l'inferenza per operazioni di sviluppo e test.  Per attività di grandi dimensioni, un [cluster di elaborazione di Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) con funzionalità di scalabilità a più nodi rappresenta una scelta migliore per la destinazione di calcolo.


## <a name="why-use-a-compute-instance"></a>Perché usare un'istanza di calcolo?

Un'istanza di calcolo è una workstation basata su cloud completamente gestita, ottimizzata per l'ambiente di sviluppo di apprendimento automatico e in grado di offrire i vantaggi indicati di seguito.

|Vantaggi principali||
|----|----|
|Produttività|I data scientist possono creare e distribuire modelli usando notebook integrati e gli strumenti seguenti nel Web browser:<br/>-  Jupyter<br/>-  JupyterLab<br/>-  RStudio|
|Gestione e sicurezza|Riduzione del footprint per la sicurezza e aggiunta di conformità ai requisiti di sicurezza aziendali. Le istanze di calcolo offrono criteri di gestione affidabili e configurazioni di rete sicure, ad esempio:<br/><br/>- Provisioning automatico da modelli di Resource Manager o di Azure Machine Learning SDK<br/>- [Controllo degli accessi in base al ruolo](/azure/role-based-access-control/overview)<br/>- [Supporto della rete virtuale](how-to-enable-virtual-network.md#compute-instance)<br/>- Criteri SSH per abilitare/disabilitare l'accesso SSH|
|Pacchetti preconfigurati&nbsp;o&nbsp;AML|Risparmio di tempo nelle attività di configurazione con pacchetti AML preconfigurati e aggiornati, framework di Deep Learning e driver GPU.|
|Personalizzazione completa|Ampio supporto per i tipi di macchine virtuali di Azure, tra cui GPU e personalizzazione di basso livello persistente, come l'installazione di pacchetti e driver, che semplifica gli scenari avanzati. |

## <a name="tools-and-environments"></a><a name="contents"></a>Strumenti e ambienti

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
|RStudio Server Open Source Edition||
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

Le istanze di calcolo vengono in genere usate come ambienti di sviluppo,  ma possono essere usate anche come destinazione di calcolo per il training e l'inferenza per operazioni di sviluppo e test.  Per attività di grandi dimensioni, un [cluster di elaborazione di Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) con funzionalità di scalabilità a più nodi rappresenta una scelta migliore per la destinazione di calcolo.

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

L'account di condivisione file di Azure dell'area di lavoro viene montato come unità nell'istanza di calcolo. Tale unità è la directory di lavoro predefinita per Jupyter, Jupyter Lab e RStudio.

I file nella condivisione file sono accessibili da tutte le istanze di calcolo presenti nella stessa area di lavoro. Tutte le modifiche apportate ai file nell'istanza di calcolo vengono salvate in modo affidabile nella condivisione file.

È anche possibile clonare gli ultimi esempi di Azure Machine Learning nella cartella della directory dei file utente nella condivisione file dell'area di lavoro.

La scrittura di file di piccole dimensioni può essere più lenta nelle unità di rete rispetto alla scrittura nella macchina virtuale stessa.  Se si scrivono molti file di piccole dimensioni, provare a usare una directory direttamente nell'istanza di calcolo, ad esempio una directory `/tmp`. Si noti che questi file non saranno accessibili dalle altre istanze di calcolo presenti nell'area di lavoro.

## <a name="managing-a-compute-instance"></a>Gestione di un'istanza di calcolo

Nell'area di lavoro in Azure Machine Learning Studio selezionare **Calcolo**, quindi selezionare **Istanza di calcolo** nella parte superiore.

![Gestire un'istanza di calcolo](./media/concept-compute-instance/manage-compute-instance.png)

È possibile eseguire queste operazione:

* Creare un'istanza di calcolo. Specificare il nome, il tipo di macchina virtuale di Azure, incluse le GPU (si noti che il tipo di macchina virtuale non può essere modificato dopo la creazione), abilitare/disabilitare l'accesso SSH e configurare le impostazioni della rete virtuale (facoltativo). È anche possibile creare un'istanza direttamente da notebook integrati, nel portale di Azure, in un modello di Resource Manager o in Azure Machine Learning SDK. La quota di core dedicati per area che si applica alla creazione dell'istanza di calcolo è unificata e condivisa con la quota di cluster di calcolo di Azure Machine Learning.
* Aggiornare la scheda delle istanze di calcolo.
* Avviare, arrestare e riavviare un'istanza di calcolo. Arrestare la macchina virtuale quando non è in uso per ridurre i costi e quindi riavviarla quando è necessario.
* Eliminare un'istanza di calcolo.

Per ogni istanza di calcolo nell'area di lavoro è possibile eseguire queste operazioni:

* Accedere a Jupyter, JupyterLab, RStudio nell'istanza di calcolo
* Accedere con SSH all'istanza di calcolo. L'accesso SSH è disabilitato per impostazione predefinita, ma può essere abilitato al momento della creazione dell'istanza di calcolo. L'accesso SSH avviene tramite il meccanismo di chiave pubblica/privata. Nella scheda sono disponibili i dettagli per la connessione SSH, ad esempio indirizzo IP, nome utente e numero di porta.
* Ottenere informazioni dettagliate su un'istanza di calcolo specifica, ad esempio l'indirizzo IP e l'area.

Il [controllo degli accessi in base al ruolo](/azure/role-based-access-control/overview) consente di controllare quali utenti nell'area di lavoro possono creare, eliminare, avviare, arrestare e riavviare un'istanza di calcolo. Tutti gli utenti con il ruolo collaboratore e proprietario dell'area di lavoro possono creare, eliminare, avviare, arrestare e riavviare le istanze di calcolo nell'area di lavoro. Solo il creatore di un'istanza di calcolo specifica, tuttavia, può accedere a Jupyter, JupyterLab e RStudio in tale istanza di calcolo. Il creatore dell'istanza di calcolo dispone dell'istanza di calcolo dedicata e dell'accesso alla cartella radice ed è in grado di accedere al terminale con Jupyter. All'istanza di calcolo è associato un account di accesso utente singolo del creatore e tutte le azioni usano l'identità dell'utente per il controllo degli accessi in base al ruolo e l'attribuzione delle esecuzioni dell'esperimento. L'accesso SSH viene controllato tramite il meccanismo di chiave pubblica/privata.

È anche possibile creare un'istanza nei modi seguenti:
* Direttamente nei notebook integrati
* Nel portale di Azure
* Nel modello di Azure Resource Manager
* Con Azure Machine Learning SDK

La quota di core dedicati per area che si applica alla creazione dell'istanza di calcolo è unificata e condivisa con la quota di cluster di training di Azure Machine Learning. 

## <a name="compute-target"></a>Destinazione del calcolo

Le istanze di calcolo possono essere usate come una [destinazione di calcolo di training](concept-compute-target.md#train) simile ai cluster di training di calcolo di Azure Machine Learning. Effettuare il provisioning di una macchina virtuale con più GPU per eseguire processi di training distribuiti tramite gli estimatori TensorFlow/PyTorch. È anche possibile creare una configurazione di esecuzione e usarla per eseguire l'esperimento nell'istanza di calcolo. È possibile usare l'istanza di calcolo come destinazione di distribuzione di inferenza locale per gli scenari di test/debug.

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Cosa è successo alla macchina virtuale del notebook?

Le istanze di calcolo sostituiscono la macchina virtuale del notebook.  

Tutti i file del notebook archiviati nella condivisione file dell'area di lavoro e i dati negli archivi dati dell'area di lavoro sono accessibili da un'istanza di calcolo. Eventuali pacchetti personalizzati installati in precedenza in una macchina virtuale del notebook, tuttavia, devono essere reinstallati nell'istanza di calcolo. Le limitazioni di quota che si applicano alla creazione dei cluster di calcolo vengono applicate anche alla creazione dell'istanza di calcolo. 

Non è possibile creare nuove macchine virtuali del notebook. È comunque possibile accedere e usare le macchine virtuali del notebook create con funzionalità complete. È possibile creare istanze di calcolo nella stessa area di lavoro delle macchine virtuali del notebook esistenti. 


## <a name="next-steps"></a>Passaggi successivi

 * In [Esercitazione: Eseguire il training del primo modello di Machine Learning](tutorial-1st-experiment-sdk-train.md) viene illustrato come usare un'istanza di calcolo con un notebook integrato.
