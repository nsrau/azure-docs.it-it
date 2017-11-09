---
title: "Come usare le unità GPU per Azure Machine Learning | Microsoft Docs"
description: "Questo articolo descrive come usare l'unità di elaborazione grafica (GPU) per il training delle reti neurali profonde in Azure Machine Learning Workbench."
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 2501113fe75f20602059927a4e0a50cecd86b187
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2017
---
# <a name="how-to-use-gpu-in-azure-machine-learning"></a>Come usare le unità GPU in Azure Machine Learning
L'unità di elaborazione grafica (GPU) è ampiamente usata per elaborare le attività di calcolo complesse che si hanno in genere durante il training di alcuni modelli di reti neurali profonde. Usando le unità GPU è possibile ridurre significativamente la durata del training dei modelli. Questo documento illustra come configurare Azure Machine Learning Workbench affinché usi la [macchina virtuale per data science ](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) dotata di GPU come destinazione dell'esecuzione. 

## <a name="prerequisites"></a>Prerequisiti
- Per proseguire con questa guida è necessario innanzitutto [installare Azure Machine Learning Workbench](quickstart-installation.md).
- È necessario accedere a computer dotati di GPU NVidia.
    - È possibile eseguire gli script direttamente sul computer locale (Windows o Mac OS) con GPU.
    - È anche possibile eseguire gli script in un contenitore Docker in un computer con GPU.

## <a name="execute-in-local-environment-with-gpus"></a>Esecuzione in ambiente _local_ con GPU
È possibile installare Azure Machine Learning Workbench in un computer dotato di GPU ed avviare l'esecuzione in un ambiente _local_. Può trattarsi di:
- Un computer fisico Windows o Mac OS.
- Una macchina virtuale Windows, ad esempio una macchina virtuale per data science il cui provisioning è eseguito con modelli di macchine virtuali di Azure Serie NC.

In questo caso non sono necessarie configurazioni speciali di Azure Machine Learning Workbench. Assicurarsi di disporre di tutti i driver, i toolkit e le librerie di machine learning necessarie, con supporto per GPU e installate localmente. Avviare l'esecuzione nell'ambiente _local_ in cui il runtime di Python possa accedere direttamente all'hardware GPU locale.

1. Aprire Azure Machine Learning Workbench. Passare a **File** e **aprire il prompt dei comandi**. 
2. Dalla riga di comando, installare un framework di apprendimento avanzato con supporto per GPU, come Microsoft Cognitive Toolkit, TensorFlow e così via, ad esempio:

```batch
REM install latest TensorFlow with GPU support
C:\MyProj> pip install tensorflow-gpu

REM install Microsoft Cognitive Toolkit 2.1 (1-bit SGD) with GPU support on Windows
C:\MyProj> pip install https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-win_amd64.whl
```

3. Scrivere il codice Python che usa le librerie di apprendimento avanzato.
4. Scegliere _local_ come ambiente di calcolo ed eseguire il codice Python.

```batch
REM execute Python script in local environment
C:\MyProj> az ml experiment submit -c local my-deep-learning-script.py
```

## <a name="execute-in-docker-environment-on-linux-vm-with-gpus"></a>Esecuzione in ambiente _docker_ su macchina virtuale Linux con GPU
Azure Machine Learning Workbench supporta anche l'esecuzione in Docker in una macchina virtuale Linux di Azure. È un'ottima opportunità per eseguire processi con calcoli complessi in un hardware virtuale potente, il cui costo è limitato al consumo perché può essere disattivato al termine delle operazioni. Sebbene in linea di principio sia possibile usare le unità GPU in qualsiasi macchina virtuale Linux, la macchina virtuale per data science basata su Ubuntu è fornita con i driver CUDA e le librerie pre-installate necessarie, agevolando così la configurazione. Seguire questa procedura:

### <a name="create-a-ubuntu-based-linux-data-science-virtual-machine-in-azure"></a>In Azure, creare una macchina virtuale per data science Linux basata su Ubuntu.
1. Aprire il Web browser e quindi passare al [portale di Azure](https://portal.azure.com).

2. Selezionare **+ Nuova** a sinistra del portale.

3. Nel marketplace, cercare "Data Science Virtual Machine for Linux (Ubuntu)".

4. Fare clic su **Crea** per creare una macchina virtuale per data science di Ubuntu.

5. Compilare il modulo **Basic** con le informazioni obbligatorie.
Durante la selezione del percorso della macchina virtuale, si noti che le macchine virtuali GPU sono disponibili solo in determinate aree di Azure, ad esempio, **Stati Uniti centro-meridionali**. Vedere i [prodotti disponibili in base all'area](https://azure.microsoft.com/en-us/regions/services/).
Fare clic su OK per salvare le informazioni nel modulo **Basic**.

6. Scegliere le dimensioni della macchina virtuale. Selezionare una delle dimensioni per macchine virtuali con prefisso NC, dotate di chip GPU NVidia.  Se necessario, fare clic su **Visualizza tutto** per visualizzare l'elenco completo. Altre informazioni sulle [macchine virtuali di Azure dotate di GPU](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes-gpu).

7. Completare le impostazioni rimanenti ed esaminare le informazioni sull'acquisto. Fare clic su Acquista per creare la macchina virtuale. Prendere nota dell'indirizzo IP allocato alla macchina virtuale. 

### <a name="create-a-new-project-in-azure-ml-workbench"></a>Creare un nuovo progetto in Azure Machine Learning Workbench 
È possibile usare l'esempio _Classifying MNIST using TensorFlow_ oppure l'esempio _Classifying MNIST dataset with CNTK_.

### <a name="create-a-new-compute-target"></a>Creare una nuova destinazione di calcolo
Avviare la riga di comando da Azure Machine Learning Workbench. Immettere il comando seguente. Sostituire il testo segnaposto dell'esempio riportato di seguito con i valori personalizzati relativi a nome, indirizzo IP, nome utente e password. 

```batch
C:\MyProj> az ml computetarget attach --name "my_dsvm" --address "my_dsvm_ip_address" --username "my_name" --password "my_password" --type remotedocker
```

### <a name="configure-azure-ml-workbench-to-access-gpu"></a>Configurare Azure Machine Learning Workbench per l'accesso a GPU
Tornare al progetto e aprire **Visualizzazione file**e fare clic sul pulsante **Aggiorna**. Vengono visualizzati due nuovi file di configurazione, `my_dsvm.compute` e `my_dsvm.runconfig`.
 
Aprire il file `my_dsvm.compute`. Modificare `baseDockerImage` in `microsoft/mmlspark:plus-gpu-0.7.9` e aggiungere una nuova riga `nvidiaDocker: true`. Il file presenterà quindi le due righe seguenti:
 
```yaml
...
baseDockerImage: microsoft/mmlspark:plus-gpu-0.7.91
nvidiaDocker: true
```
 
Aprire quindi `my_dsvm.runconfig`, modificare il valore di `Framework` da `PySpark` a `Python`. Se questa riga non viene visualizzata, aggiungerla, perché in caso contrario il valore predefinito sarebbe `PySpark`.

```yaml
"Framework": "Python"
```
### <a name="reference-deep-learning-framework"></a>Framework di riferimento per l'apprendimento avanzato 
Aprire il file `conda_dependencies.yml` e verificare che sia in uso la versione GPU dei pacchetti Python del framework di apprendimento avanzato. I progetti di esempio elencano le versioni della CPU, pertanto è necessario apportare questa modifica.

Esempio per TensorFlow: 
```
name: project_environment
dependencies:
  - python=3.5.2
  # latest TensorFlow library with GPU support
  - tensorflow-gpu
```

Esempio per Microsoft Cognitive Toolkit:
```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip: 
    # use the Linux build of Microsoft Cognitive Toolkit 2.1 with GPU support
    - https://cntk.ai/PythonWheel/GPU/cntk-2.1-cp35-cp35m-linux_x86_64.whl
```

È anche possibile usare la versione 1 bit-SGD di Microsoft Cognitive Toolkit, che include miglioramenti delle prestazioni nelle macchine virtuali con più GPU. Si noti il [requisito di licenza per 1 bit-SGD](https://docs.microsoft.com/en-us/cognitive-toolkit/cntk-1bit-sgd-license).

```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip:    
    # use the Linux build of the Microsoft Cognitive Toolkit 2.1 with 1-bit SGD and GPU support
    - https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-linux_x86_64.whl
```

### <a name="execute"></a>Esegui
È ora possibile eseguire gli script Python in Azure Machine Learning Workbench usando il contesto `my_dsvm` oppure avviandoli dalla riga di comando:
 
```batch
C:\myProj> az ml experiment submit -c my_dsvm my_tensorflow_or_cntk_script.py
```
 
Per verificare che la GPU sia in uso, controllare che l'output di esecuzione sia simile al seguente:

```
name: Tesla K80
major: 3 minor: 7 memoryClockRate (GHz) 0.8235
pciBusID 06c3:00:00.0
Total memory: 11.17GiB
Free memory: 11.11GiB
```

Congratulazioni. Ora lo script sfrutta tutte le potenzialità delle unità GPU grazie al contenitore Docker.

## <a name="next-steps"></a>Passaggi successivi
Vedere un esempio di utilizzo dell'unità GPU per accelerare il training della rete neurale profonda nella raccolta di Azure Machine Learning.
