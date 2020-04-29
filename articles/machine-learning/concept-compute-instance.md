---
title: Che cos'è un'istanza di calcolo Azure Machine Learning?
titleSuffix: Azure Machine Learning
description: Informazioni sull'istanza di calcolo Azure Machine Learning, una workstation basata su cloud completamente gestita.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 280851b2fea0b8100a7d0f8ec8105109a41c8c83
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79283927"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Che cos'è un'istanza di calcolo Azure Machine Learning?

Un'istanza di calcolo Azure Machine Learning (anteprima) è una workstation basata su cloud completamente gestita per i data scientist. 

Le istanze di calcolo consentono di iniziare a usare in modo semplice Azure Machine Learning lo sviluppo, nonché di fornire funzionalità di gestione e conformità a livello aziendale per gli amministratori IT.  

Usare un'istanza di calcolo come ambiente di sviluppo completamente configurato e gestito nel cloud.

Le istanze di calcolo vengono in genere usate come ambienti di sviluppo.  Possono anche essere usati come destinazione di calcolo per il training e l'inferenza per lo sviluppo e il test.  Per le attività di grandi dimensioni, un [cluster di calcolo Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) con funzionalità di scalabilità a più nodi rappresenta una scelta migliore per la destinazione di calcolo.


## <a name="why-use-a-compute-instance"></a>Perché usare un'istanza di calcolo?

Un'istanza di calcolo è una workstation basata su cloud completamente gestita, ottimizzata per l'ambiente di sviluppo di machine learning. offrendo i seguenti vantaggi:

|Vantaggi principali||
|----|----|
|Produttività|I data scientist possono creare e distribuire modelli usando notebook integrati e gli strumenti seguenti nel Web browser:<br/>-Jupyter<br/>-JupyterLab<br/>-RStudio|
|Gestito & protetto|Ridurre il footprint di sicurezza e aggiungere la conformità ai requisiti di sicurezza aziendali. Le istanze di calcolo forniscono criteri di gestione affidabili e configurazioni di rete sicure, ad esempio:<br/><br/>-Provisioning automatico da modelli di Gestione risorse o SDK Azure Machine Learning<br/>- [Controllo degli accessi in base al ruolo (RBAC)](/azure/role-based-access-control/overview)<br/>- [Supporto per reti virtuali](how-to-enable-virtual-network.md#compute-instance)<br/>-Criterio SSH per abilitare/disabilitare l'accesso SSH|
|Preconfigurata&nbsp;o&nbsp;ml|Risparmia tempo nelle attività di configurazione con pacchetti ML pre-configurati e aggiornati, Framework per l'apprendimento avanzato, driver GPU.|
|Completamente personalizzabile|Un ampio supporto per i tipi di VM di Azure, tra cui GPU e personalizzazione di basso livello, come l'installazione di pacchetti e driver, rende gli scenari avanzati una brezza. |

## <a name="tools-and-environments"></a><a name="contents"></a>Strumenti e ambienti

Azure Machine Learning istanza di calcolo consente di creare, eseguire il training e distribuire modelli in un'esperienza di notebook completamente integrata nell'area di lavoro.


Questi strumenti e ambienti sono installati nell'istanza di calcolo: 

|Strumenti generali & ambienti|Dettagli|
|----|:----:|
|Driver|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Libreria Intel MPI||
|Interfaccia della riga di comando di Azure ||
|Esempi di Azure Machine Learning ||
|Motore EDAT Azure Machine Learning ||
|Docker||
|Nginx||
|NCCL 2,0 ||
|Protobuf|| 

|Strumenti **R** & ambienti|Dettagli|
|----|:----:|
|Edizione Open Source del server RStudio||
|Kernel R||
|SDK di Azure Machine Learning per R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>Esempi di SDK|

|Strumenti **PYTHON** & ambienti|Dettagli|
|----|----|
|Anaconda Python||
|Jupyter ed estensioni||
|Jupyterlab ed estensioni||
|Visual Studio Code ||
[SDK di Azure Machine Learning per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>da PyPI|`azureml-sdk[notebooks,contrib,automl,explain]`</br>`azureml-contrib-datadrift`</br>`azureml-telemetry`</br>`azureml-tensorboard`</br>`azureml-contrib-opendatasets`</br>`azureml-opendatasets`</br>`azureml-contrib-reinforcementlearning`</br>`azureml-mlflow`</br>`azureml-contrib-interpret` |
|Altri pacchetti PyPI|`jupytext`</br>`jupyterlab-git`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Pacchetti conda|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Pacchetti per Deep Learning|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|Pacchetti ONNX|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Esempi di Azure Machine Learning Python & R SDK||

Tutti i pacchetti Python sono installati nell'ambiente **python 3,6-AzureML** .  

Le istanze di calcolo vengono in genere usate come ambienti di sviluppo.  Possono anche essere usati come destinazione di calcolo per il training e l'inferenza per lo sviluppo e il test.  Per le attività di grandi dimensioni, un [cluster di calcolo Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) con funzionalità di scalabilità a più nodi rappresenta una scelta migliore per la destinazione di calcolo.

### <a name="installing-packages"></a>Installazione di pacchetti

È possibile installare i pacchetti direttamente in un notebook di Jupyter o rstudio:

* RStudio usare la scheda **pacchetti** in basso a destra o la scheda **console** in alto a sinistra.  
* Python: aggiungere il codice di installazione ed eseguire in una cella Jupyter notebook.

In alternativa, è possibile accedere a una finestra del terminale in uno dei modi seguenti:

* RStudio: selezionare la scheda **terminale** in alto a sinistra.
* Jupyter Lab: selezionare il riquadro **terminale** sotto l' **altra** intestazione nella scheda utilità di avvio.
* Jupyter: selezionare **nuovo>terminale** in alto a destra nella scheda file.
* Eseguire SSH nel computer.  Installare quindi i pacchetti Python nell'ambiente **python 3,6-AzureML** .  Installare i pacchetti R nell'ambiente **r** .

## <a name="accessing-files"></a>Accesso ai file

I notebook e gli script R vengono archiviati nell'account di archiviazione predefinito dell'area di lavoro nella condivisione file di Azure.  Questi file si trovano nella directory "User Files". Questa risorsa di archiviazione semplifica la condivisione di notebook tra le istanze di calcolo. L'account di archiviazione consente inoltre di mantenere i notebook in modo sicuro quando si arresta o si elimina un'istanza di calcolo.

L'account di condivisione file di Azure dell'area di lavoro viene montato come unità nell'istanza di calcolo. Questa unità è la directory di lavoro predefinita per Jupyter, Jupyter Labs e RStudio.

I file nella condivisione file sono accessibili da tutte le istanze di calcolo nella stessa area di lavoro. Tutte le modifiche apportate a questi file nell'istanza di calcolo verranno salvate in modo affidabile nella condivisione file.

È anche possibile clonare gli ultimi Azure Machine Learning esempi nella cartella nella directory dei file utente nella condivisione file dell'area di lavoro.

La scrittura di file di piccole dimensioni può essere più lenta nelle unità di rete rispetto alla scrittura nella macchina virtuale stessa.  Se si stanno scrivendo molti file di piccole dimensioni, provare a usare una directory direttamente nell'istanza di calcolo, ad `/tmp` esempio una directory. Si noti che questi file non saranno accessibili dalle altre istanze di calcolo nell'area di lavoro.

## <a name="managing-a-compute-instance"></a>Gestione di un'istanza di calcolo

Nell'area di lavoro in Azure Machine Learning Studio selezionare **calcolo**, quindi selezionare l' **istanza di calcolo** nella parte superiore.

![Gestire un'istanza di calcolo](./media/concept-compute-instance/manage-compute-instance.png)

È possibile effettuare le azioni seguenti:

* Creare un'istanza di calcolo. Specificare il nome, il tipo di macchina virtuale di Azure, incluse le GPU (si noti che il tipo di macchina virtuale non può essere modificato dopo la creazione), abilitare/disabilitare l'accesso SSH e configurare le impostazioni della rete virtuale facoltativamente. È anche possibile creare un'istanza direttamente da notebook integrati, portale di Azure, Gestione risorse modello o Azure Machine Learning SDK. La quota di core dedicati per area che si applica alla creazione dell'istanza di calcolo è unificata e condivisa con Azure Machine Learning quota del cluster di calcolo.
* Aggiornare la scheda istanze di calcolo
* Avviare, arrestare e riavviare un'istanza di calcolo
* Eliminare un'istanza di calcolo

Per ogni istanza di calcolo nell'area di lavoro è possibile:

* Accedere a Jupyter, JupyterLab, RStudio nell'istanza di calcolo
* SSH nell'istanza di calcolo. L'accesso SSH è disabilitato per impostazione predefinita, ma può essere abilitato al momento della creazione dell'istanza di calcolo. L'accesso SSH avviene tramite il meccanismo di chiave pubblica/privata. La scheda fornirà i dettagli per la connessione SSH, ad esempio indirizzo IP, nome utente e numero di porta.
* Ottenere informazioni dettagliate su un'istanza di calcolo specifica, ad esempio l'indirizzo IP e l'area.

[RBAC](/azure/role-based-access-control/overview) consente di controllare quali utenti nell'area di lavoro possono creare, eliminare, avviare, arrestare e riavviare un'istanza di calcolo. Tutti gli utenti del ruolo Collaboratore e proprietario dell'area di lavoro possono creare, eliminare, avviare, arrestare e riavviare le istanze di calcolo nell'area di lavoro. Tuttavia, solo l'autore di un'istanza di calcolo specifica può accedere a Jupyter, JupyterLab e RStudio in tale istanza di calcolo. L'autore dell'istanza di calcolo dispone dell'istanza di calcolo dedicata, dispone dell'accesso alla radice ed è in grado di eseguire il terminale con Jupyter. L'istanza di calcolo avrà un account di accesso utente singolo dell'utente Creator e tutte le azioni useranno l'identità dell'utente per RBAC e l'attribuzione delle esecuzioni dell'esperimento. L'accesso SSH viene controllato tramite il meccanismo di chiave pubblica/privata.

È anche possibile creare un'istanza
* Direttamente dall'esperienza dei notebook integrati
* Nel portale di Azure
* Dal modello di Azure Resource Manager
* Con Azure Machine Learning SDK

La quota di core dedicati per area, applicabile alla creazione dell'istanza di calcolo, è unificata e condivisa con Azure Machine Learning quota del cluster di training. 

## <a name="compute-target"></a>Destinazione di calcolo

Le istanze di calcolo possono essere usate come [destinazione di calcolo di training](concept-compute-target.md#train) in modo analogo ai cluster di training di calcolo di Azure Machine Learning. Effettuare il provisioning di una macchina virtuale con più GPU per eseguire processi di training distribuiti usando gli estimatori TensorFlow/PyTorch. È anche possibile creare una configurazione di esecuzione e usarla per eseguire l'esperimento nell'istanza di calcolo. È possibile usare l'istanza di calcolo come destinazione di distribuzione di inferenza locale per gli scenari di test/debug.

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Cosa è successo alla VM notebook?

Le istanze di calcolo sostituiscono la VM del notebook.  

Tutti i file del notebook archiviati nella condivisione file dell'area di lavoro e i dati negli archivi dati dell'area di lavoro saranno accessibili da un'istanza di calcolo. Tuttavia, eventuali pacchetti personalizzati installati in precedenza in una macchina virtuale notebook dovranno essere reinstallati nell'istanza di calcolo. Le limitazioni di quota che si applicano alla creazione dei cluster di calcolo verranno applicate anche alla creazione dell'istanza di calcolo. 

Non è possibile creare nuove macchine virtuali del notebook. Tuttavia, è comunque possibile accedere e usare le macchine virtuali notebook create con funzionalità complete. È possibile creare istanze di calcolo nella stessa area di lavoro delle macchine virtuali del notebook esistenti. 


## <a name="next-steps"></a>Passaggi successivi

 * [Esercitazione: eseguire il training del primo modello ml](tutorial-1st-experiment-sdk-train.md) Mostra come usare un'istanza di calcolo con un notebook integrato.
