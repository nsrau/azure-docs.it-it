---
title: Come usare i notebook di Jupyter in Azure Machine Learning Workbench | Microsoft Docs
description: Guida per l'uso della funzionalità Jupyter Notebook integrata in Azure Machine Learning Workbench
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/09/2017
ms.openlocfilehash: c21b7096f689efedacd6e7d55d83912d35dff803
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="use-jupyter-notebooks-in-azure-machine-learning-workbench"></a>Usare i notebook di Jupyter in Azure Machine Learning Workbench

Azure Machine Learning Workbench supporta l'esecuzione di esperimenti di data science interattivi grazie all'integrazione con Jupyter Notebook. Questo articolo descrive come sfruttare al meglio questa funzionalità per aumentare la velocità e la qualità degli esperimenti di data science interattivi.

## <a name="prerequisites"></a>Prerequisiti
- [Creare account di Azure Machine Learning e installare Azure Machine Learning Workbench](quickstart-installation.md).
- Acquisire familiarità con [Jupyter Notebook](http://jupyter.org/). Questo articolo non contiene informazioni per imparare a usare Jupyter.

## <a name="jupyter-notebook-architecture"></a>Architettura di Jupyter Notebook
A livello generale, l'architettura di Jupyter Notebook include tre componenti, ognuno dei quali può essere eseguito in ambienti di calcolo diversi:

- **Client**: riceve l'input dell'utente e mostra l'output restituito.
- **Server**: server Web che ospita i file di notebook, con estensione ipynb.
- **Kernel**: ambiente di runtime in cui vengono eseguite le celle dei notebook.

Per altre informazioni, vedere la [documentazione ufficiale di Jupyter](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html). Il diagramma seguente illustra la corrispondenza tra l'architettura dei componenti client, server e kernel e i componenti di Azure Machine Learning:

![Architettura di Jupyter Notebook](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-machine-learning-workbench-notebooks"></a>Kernel nei notebook di Azure Machine Learning Workbench
In Azure Machine Learning Workbench è possibile accedere a diversi kernel definendo le configurazioni di esecuzione e le destinazioni di calcolo nella cartella `aml_config` del progetto. L'aggiunta di una nuova destinazione di calcolo tramite il comando `az ml computetarget attach` equivale all'aggiunta di un nuovo kernel.

>[!NOTE]
>Per altre informazioni sulle configurazioni di esecuzione e le destinazioni di calcolo, vedere [Configurazione del servizio Sperimentazione di Azure Machine Learning](experimentation-service-configuration.md).

### <a name="kernel-naming-convention"></a>Convenzione di denominazione dei kernel
Azure Machine Learning Workbench genera kernel Jupyter personalizzati, denominati in base al formato seguente: *\<nome del progetto> \<nome della configurazione di esecuzione>*. Se ad esempio si ha una configurazione di esecuzione _docker-python_ in un progetto _myIris_, Azure Machine Learning rende disponibile un kernel denominato *myIris docker-python*. Il kernel in esecuzione viene impostato nel sottomenu **Change kernel** (Modifica kernel) del menu **Kernel** di Jupyter Notebook. Il nome del kernel in esecuzione viene visualizzato all'estremità destra della barra dei menu.
 
Azure Machine Learning Workbench supporta attualmente i tipi di kernel seguenti.

### <a name="local-python-kernel"></a>Kernel Python in locale
Il kernel Python supporta l'esecuzione nei computer locali. È integrato con il supporto della cronologia di esecuzione di Azure Machine Learning. Il nome del kernel è in genere *nome_progetto local*.

>[!NOTE]
>Non usare il kernel Python 3. È un kernel autonomo fornito da Jupyter per impostazione predefinita e non integrato con le funzionalità di Azure Machine Learning. Ad esempio, le funzioni magic `%azureml` di Jupyter restituiscono errori di tipo "non trovato". 

### <a name="python-kernel-in-docker-local-or-remote"></a>Kernel Python in Docker, locale o remoto
Questo kernel Python viene eseguito in un contenitore Docker nel computer locale o in una macchina virtuale Linux remota. Il nome del kernel è in genere *nome_progetto docker*. Nel file associato `docker.runconfig` il campo `Framework` è impostato su `Python`.

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>Kernel PySpark in Docker, locale o remoto
Questo kernel PySpark esegue gli script in un contesto Spark in esecuzione all'interno del contenitore Docker, sul computer locale o in una macchina virtuale Linux remota. Il nome del kernel è in genere *nome_progetto docker*. Nel file associato `docker.runconfig` il campo `Framework` è impostato su `PySpark`.

### <a name="pyspark-kernel-in-an-azure-hdinsight-cluster"></a>Kernel PySpark in un cluster Azure HDInsight
Questo kernel viene eseguito nel cluster Azure HDInsight remoto collegato come destinazione di calcolo del progetto. Il nome del kernel è in genere *nome_progetto nome_hdi*. 

>[!IMPORTANT]
>Nel file `.compute` relativo alla destinazione di calcolo HDI, per usare questo kernel è necessario modificare il valore del campo `yarnDeployMode`, il cui valore predefinito è `cluster`, impostandolo su `client`. 

## <a name="start-a-jupyter-server-from-azure-machine-learning-workbench"></a>Avviare un server Jupyter da Azure Machine Learning Workbench
In Azure Machine Learning Workbench è possibile accedere ai notebook tramite la scheda **Notebooks** (Notebook). Il progetto di esempio di _classificazione di Iris_ include un notebook di esempio `iris.ipynb`.

![Scheda dei notebook](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

Quando si apre un notebook in Azure Machine Learning Workbench, questo viene visualizzato nella rispettiva scheda del documento in **modalità di anteprima**. Si tratta di una visualizzazione di sola lettura che non richiede un server Jupyter e un kernel in esecuzione.

![Anteprima del notebook](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

Selezionare il pulsante **Start Notebook Server** (Avvia server Notebook) per avviare il server Jupyter e passare alla **modalità di modifica** del notebook. Nell'applicazione Workbench appare la nota interfaccia utente di Jupyter Notebook. È ora possibile impostare un kernel dal menu **Kernel** e avviare la sessione interattiva del notebook. 

>[!NOTE]
>Con i kernel non locali, il primo avvio può richiedere un paio di minuti. È possibile eseguire il comando `az ml experiment prepare` dalla finestra dell'interfaccia della riga di comando per preparare la destinazione di calcolo in modo da rendere più veloce il successivo avvio del kernel.

![Modalità di modifica](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

Questa è un'esperienza di Jupyter Notebook completamente interattiva. In questa finestra sono supportate tutte le scelte rapide da tastiera e le normali operazioni relative ai notebook, fatta eccezione per alcune operazioni sui file che possono essere eseguite tramite le schede **Notebooks** (Notebook) e **File** dell'applicazione Workbench.

## <a name="start-a-jupyter-server-from-the-command-line"></a>Avviare un server Jupyter dalla riga di comando
È anche possibile avviare una sessione di notebook eseguendo il comando `az ml notebook start` nella finestra della riga di comando:
```
$ az ml notebook start
[I 10:14:25.455 NotebookApp] The port 8888 is already in use, trying another port.
[I 10:14:25.464 NotebookApp] Serving notebooks from local directory: /Users/johnpelak/Desktop/IrisDemo
[I 10:14:25.465 NotebookApp] 0 active kernels 
[I 10:14:25.465 NotebookApp] The Jupyter Notebook is running at: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.465 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 10:14:25.466 NotebookApp] 
    
Copy and paste this URL into your browser when you connect for the first time, to login with a token: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.759 NotebookApp] Accepting one-time-token-authenticated connection from ::1
[I 10:16:52.970 NotebookApp] Kernel started: 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
[I 10:16:53.854 NotebookApp] Adapting to protocol v5.1 for kernel 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
```
Viene aperto automaticamente il browser predefinito con il server Jupyter che punta alla directory radice del progetto. È anche possibile usare l'URL e il token visualizzati nella finestra dell'interfaccia della riga di comando per aprire altre finestre del browser in locale. 

![Dashboard del progetto](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

È ora possibile selezionare un file di notebook `.ipynb`, aprirlo, impostare il kernel, se non è stato impostato, e avviare la sessione interattiva.

![Dashboard del progetto](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="use-magic-commands-to-manage-experiments"></a>Usare i comandi magic per la gestione degli esperimenti

È possibile usare i [comandi magic](http://ipython.readthedocs.io/en/stable/interactive/magics.html) nelle celle del notebook per tenere traccia della cronologia di esecuzione e salvare gli output, ad esempio modelli o set di dati.

Per tenere traccia delle singole esecuzioni di cella del notebook, usare il comando magic `%azureml history on`. Quando la cronologia è attiva, ogni esecuzione di cella viene visualizzata come singola voce nella cronologia di esecuzione:

```
%azureml history on
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()
logger.log("Cell","Load Data")
```

Per disattivare il rilevamento delle esecuzioni di cella, usare il comando magic `%azureml history off`.

È possibile usare il comando magic `%azureml upload` per salvare i file del modello e dei dati dell'esecuzione. Gli oggetti salvati sono riportati come output nella visualizzazione della cronologia di esecuzione:

```
modelpath = os.path.join("outputs","model.pkl")
with open(modelpath,"wb") as f:
    pickle.dump(model,f)
%azureml upload outputs/model.pkl
```

>[!NOTE]
>Gli output devono essere salvati in una cartella denominata *outputs*.

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni su come usare Jupyter Notebook, vedere la [documentazione ufficiale di Jupyter](http://jupyter-notebook.readthedocs.io/en/latest/).    
- Per acquisire una conoscenza più approfondita dell'ambiente di esecuzione di Sperimentazione di Azure Machine Learning, vedere [Configurazione del servizio Sperimentazione di Azure Machine Learning](experimentation-service-configuration.md).

