---
title: Come usare il blocco appunti Jupyter in Azure Machine Learning Workbench | Microsoft Docs
description: "Guida per l'uso della funzionalità del blocco appunti Jupyter in Azure Machine Learning Workbench"
services: machine-learning
author: jopela
ms.author: jopela
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 93850a7c9e3d9d69b0da22ebd0656ae40cee2e63
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="how-to-use-jupyter-notebook-in-azure-machine-learning-workbench"></a>Come usare il blocco appunti Jupyter in Azure Machine Learning Workbench

Azure Machine Learning Workbench supporta la sperimentazione interattiva del data science grazie all'integrazione del blocco appunti Jupyter. Questo articolo descrive come usare al meglio queste funzionalità per aumentare la velocità e la qualità degli esperimenti di data science interattivi.

## <a name="prerequisites"></a>Prerequisiti
- [Installare e creare Azure Machine Learning](/machine-learning/preview/quickstart-installation.md).
- Acquisire familiarità con il [blocco appunti Jupyter](http://jupyter.org/), dal momento che questo articolo non descrive come usarlo.

## <a name="jupyter-notebook-architecture"></a>Architettura del blocco appunti Jupyter
In generale, l'architettura del blocco appunti Jupyter include tre componenti, ognuna delle quali può essere eseguita in ambienti di calcolo diversi:

- **Client**: riceve l'input dell'utente e mostra l'output sottoposto a rendering
- **Server**: server Web che ospita i file del blocco appunti, ovvero i file .ipynb
- **Kernel**: l'ambiente di runtime in cui avviene l'effettiva esecuzione delle celle del blocco appunti

Per altre informazioni fare riferimento alla [documentazione ufficiale su Jupyter](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html). Di seguito è riportato un diagramma che illustra il mapping dell'architettura client, server e kernel nei componenti in Azure ML.

![architettura del blocco appunti](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-ml-workbench-notebook"></a>Kernel nel blocco appunti di Azure Machine Learning Workbench
In Azure Machine Learning Workbench è possibile accedere a diversi kernel configurando semplicemente le configurazioni di esecuzione e le destinazioni di calcolo nella cartella `aml_config` del progetto. Aggiungere una nuova destinazione di calcolo eseguendo il comando `az ml computetarget attach` corrisponde ad aggiungere un nuovo kernel.

>[!NOTE]
>Per altre informazioni sulle configurazioni di esecuzione e le destinazioni di calcolo rivedere l'articolo sulla [configurazione dell'esecuzione](experimentation-service-configuration.md).

### <a name="kernel-naming-convention"></a>Convenzione di denominazione del kernel
I kernel vengono in genere denominati nel formato "\<nome del progetto > \<nome della configurazione di esecuzione>". Ad esempio, se si dispone di una configurazione di esecuzione denominata _docker-python_ in un progetto denominato _myIris_, è possibile trovare un kernel denominato "myIris docker-python" nell'elenco dei kernel quando si apre un blocco appunti Jupyter.

Attualmente, il Workbench supporta i tipi seguenti di kernel.

### <a name="local-python-kernel"></a>Kernel Python in locale
Il kernel Python supporta l'esecuzione nel computer locale. È integrato con il supporto della cronologia di esecuzione di Azure Machine Learning. Il nome del kernel è in genere "my_project_name local".

### <a name="python-kernel-in-docker-local-or-remote"></a>Kernel Python in Docker, locale o remoto
Questo kernel Python viene eseguito in un contenitore Docker del computer locale o in una macchina virtuale Linux remota. Il nome del kernel è in genere "my_project docker". Il file associato `docker.runconfig` presenta l'impostazione `Python` per il campo `Framework`.

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>Kernel PySpark in Docker, locale o remoto
Il kernel PySpark esegue gli script in un contesto Spark in esecuzione all'interno del contenitore Docker, sul computer locale o in una macchina virtuale Linux remota. Il nome del kernel è in genere "my_project docker". Nel file associato `docker.runconfig` il campo `Framework` è impostato su `PySpark`.

### <a name="pyspark-kernel-on-hdinsight-cluster"></a>Kernel PySpark nel cluster di HDInsight
Questo kernel viene eseguito nel cluster HDInsight remoto collegato come destinazione di calcolo del progetto. Il nome del kernel è in genere "my_project my_hdi". 

>[!IMPORTANT]
>Nel file `.compute` della destinazione di calcolo HDI, per usare questo kernel è necessario modificare il valore del campo `yarnDeployMode`, il cui valore predefinito è `cluster`, impostandolo su `client`. 

## <a name="start-jupyter-server-from-the-workbench"></a>Avviare il server Jupyter dal Workbench
In Azure Machine Learning Workbench è possibile accedere ai blocchi appunti tramite la scheda **Blocchi appunti** del Workbench. Il progetto di esempio di _classificazione di Iris_ include un blocco appunti di esempio `iris.ipynb`.

![scheda Blocchi appunti](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

Quando si apre un blocco appunti in Azure Machine Learning Workbench, questo viene visualizzato nella rispettiva scheda del documento in **Modalità anteprima**. Si tratta di una visualizzazione di sola lettura che non richiede un server Jupyter e un kernel in esecuzione.

![anteprima del blocco appunti](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

Fare clic sul pulsante **Start Notebook Server** (Avvia il server del blocco appunti) per avviare il server Jupyter e passare alla **Modalità di modifica** del blocco appunti. La nota interfaccia utente del blocco appunti Jupyter appare nel Workbench. È ora possibile impostare un kernel dal menu **Kernel** e avviare la sessione interattiva del blocco appunti. 

>[!NOTE]
>Si noti che per i kernel non locali, l'avvio potrebbe richiedere un paio di minuti se lo si usa per la prima volta. È possibile eseguire il comando `az ml experiment prepare` dalla finestra dell'interfaccia della riga di comando per preparare la destinazione di calcolo in modo che il kernel possa avviarsi più velocemente dopo aver preparato la destinazione di calcolo.

![modalità di modifica](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

Si tratta di un'esperienza del blocco appunti Jupyter completamente interattiva. In questa finestra sono supportate tutte le normali operazioni del blocco appunti e i tasti di scelta rapida, fatta eccezione per alcune operazioni del file che possono essere effettuate tramite la scheda **Blocchi appunti** e la scheda **File** del Workbench.

## <a name="start-jupyter-server-from-command-line"></a>Avviare il server Jupyter dalla riga di comando
È anche possibile avviare una sessione del blocco appunti inviando il comando `az ml notebook start` nella finestra della riga di comando:
```
$ az ml notebook start
[I 10:14:25.455 NotebookApp] The port 8888 is already in use, trying another port.
[I 10:14:25.464 NotebookApp] Serving notebooks from local directory: /Users/johnpelak/Desktop/IrisDemo
[I 10:14:25.465 NotebookApp] 0 active kernels 
[I 10:14:25.465 NotebookApp] The Jupyter Notebook is running at: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.465 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 10:14:25.466 NotebookApp] 
    
    Copy/paste this URL into your browser when you connect for the first time,
    to login with a token:
        http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.759 NotebookApp] Accepting one-time-token-authenticated connection from ::1
[I 10:16:52.970 NotebookApp] Kernel started: 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
[I 10:16:53.854 NotebookApp] Adapting to protocol v5.1 for kernel 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
```
Il browser predefinito viene avviato automaticamente con il server Jupyter che punta alla directory principale del progetto. È anche possibile usare l'URL e il token visualizzati nella finestra dell'interfaccia della riga di comando per avviare altre finestre del browser in locale. 

![dashboard del progetto](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

È ora possibile fare clic su un file del blocco appunti `.ipynb`, aprirlo, impostare il kernel, se non è stato impostato, e avviare la sessione interattiva.

![dashboard del progetto](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni su come usare il blocco appunti Jupyter, visitare la pagina sulla [documentazione ufficiale di Jupyter](http://jupyter-notebook.readthedocs.io/en/latest/).    
- Per acquisire una conoscenza più approfondita dell'ambiente di esecuzione della sperimentazione di Azure ML, esaminare [Overview of Azure Machine Learning experiment service](experimentation-service-configuration.md) (Panoramica del servizio Sperimentazione di Azure Machine Learning).

