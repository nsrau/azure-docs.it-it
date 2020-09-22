---
title: 'Esercitazione: Eseguire lo script di Python "Hello World"'
titleSuffix: Azure Machine Learning
description: La seconda parte della serie introduttiva su Azure ML mostra come inviare al cloud un semplice script di Python "Hello World".
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 98f4c30d03763e070b1bdc32a5e6e099556916ab
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929494"
---
# <a name="tutorial-run-hello-world-python-script-part-2-of-4"></a>Esercitazione: Eseguire lo script di Python "Hello World" (parte 2 di 4)

In questa esercitazione viene illustrato come usare Azure Machine Learning Python SDK per inviare ed eseguire uno script di Python "Hello World".

Questa esercitazione è la **seconda di una serie in quattro parti** in cui vengono illustrati i concetti fondamentali su Azure Machine Learning e vengono completate attività di Machine Learning basate su processi in Azure. Questa esercitazione si basa sull'attività completata nella [parte 1 dell'esercitazione: Configurare il computer locale per Azure Machine Learning](
tutorial-1st-experiment-sdk-setup-local.md).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare ed eseguire in locale uno script di Python "Hello World"
> * Creare uno script di controllo di Python per inviare "Hello world" ad Azure Machine Learning
> * Comprendere i concetti di Azure Machine Learning nello script di controllo
> * Inviare ed eseguire "Hello World"
> * Visualizzare l'output del codice nel cloud

## <a name="prerequisites"></a>Prerequisiti

- Completare la [parte 1 dell'esercitazione relativa alla configurazione del computer locale](tutorial-1st-experiment-sdk-setup-local.md) se non si ha già un'area di lavoro di Azure Machine Learning.
- Conoscenze di base del linguaggio Python e dei flussi di lavoro di Machine Learning.
- Ambiente di sviluppo locale, inclusi tra gli altri Visual Studio Code, Jupyter o PyCharm.
- Python (versione 3.5-3.7).

## <a name="create-and-run-a-python-script-locally"></a>Creare ed eseguire in locale uno script di Python

Nella directory `tutorial` creare una nuova sottodirectory denominata `src` in cui archiviare il codice che si vuole eseguire in un cluster di elaborazione di Azure Machine Learning. Nella sottodirectory `src` creare lo script di Python `hello.py`:

```python
# src/hello.py
print("Hello world!")
```

La struttura di directory del progetto sarà ora simile alla seguente:

```Bash
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
└──01-create-workspace.py
└──02-create-compute.py
```

### <a name="test-your-script-locally"></a>Testare lo script in locale

È possibile eseguire il codice in locale, con il vantaggio di poter eseguire il debug interattivo del codice, usando l'ambiente di sviluppo integrato preferito o tramite un terminale:

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

## <a name="create-a-control-script"></a>Creare uno script di controllo

Uno *script di controllo* consente di eseguire lo script `hello.py` nel cloud.  Lo script di controllo consente di controllare come e dove viene eseguito il codice di Machine Learning.  

Nella directory dell'esercitazione creare un nuovo file di Python denominato `03-run-hello.py` e copiare e incollare in tale file il codice seguente:

```python
# tutorial/03-run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

### <a name="understand-the-code"></a>Informazioni sul codice

Ecco come funziona lo script di controllo:

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      [Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) si connette all'area di lavoro di Azure Machine Learning in modo che sia possibile comunicare con le risorse di Azure Machine Learning.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      [Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py&preserve-view=true) consente di organizzare in modo semplice più esecuzioni sotto un singolo nome. Più avanti si vedrà come gli esperimenti consentano di confrontare facilmente le metriche di decine di esecuzioni.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) esegue il wrapping del codice `hello.py` e lo passa all'area di lavoro. Come indicato dal nome, è possibile usare questa classe per _configurare_ la modalità di _esecuzione_ dello _script_ in Azure Machine Learning. Specifica anche la destinazione di calcolo su cui verrà eseguito lo script.  In questo codice la destinazione è il cluster di elaborazione creato nell'[esercitazione relativa alla configurazione](tutorial-1st-experiment-sdk-setup-local.md).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       Invia lo script. Questo invio è denominato [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py&preserve-view=true).  Un oggetto run incapsula una singola esecuzione del codice. Usare un oggetto run per monitorare lo stato dello script, acquisire l'output, analizzare i risultati, visualizzare le metriche e altro ancora.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        L'oggetto `run` fornisce un handle sull'esecuzione del codice. Monitorarne lo stato da Azure Machine Learning Studio con l'URL stampato dallo script di Python.  
   :::column-end:::
:::row-end:::

## <a name="submit-and-run-your-code-in-the-cloud"></a>Inviare ed eseguire il codice nel cloud

Eseguire lo script di controllo, che a propria volta esegue `hello.py` nel cluster di elaborazione creato nell'[esercitazione relativa alla configurazione](tutorial-1st-experiment-sdk-setup-local.md).

```bash
python 03-run-hello.py
```

## <a name="monitor-your-code-in-the-cloud-using-studio"></a>Monitorare il codice nel cloud usando Studio

L'output conterrà un collegamento ad Azure Machine Learning Studio simile al seguente: `https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>`.

Seguire il collegamento e passare alla scheda **Outputs + logs** (Output + log), dove è possibile vedere un file `70_driver_log.txt` simile al seguente:

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

Nella riga 8 verrà visualizzato l'output "Hello World!".

Il file `70_driver_log.txt` contiene l'output standard di un'esecuzione. Questo file può essere utile quando si esegue il debug di esecuzioni remote nel cloud.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato eseguito in Azure un semplice script "hello world". Si è visto come connettersi all'area di lavoro di Azure Machine Learning, creare un esperimento e inviare al cloud il codice `hello.py`.

Nell'esercitazione successiva si approfondiranno queste informazioni eseguendo uno script più interessante di `print("Hello world!")`.

> [!div class="nextstepaction"]
> [Esercitazione: training di un modello](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> Per completare questa serie di esercitazioni e non procedere al passaggio successivo, ricordare di [pulire le risorse](tutorial-1st-experiment-bring-data.md#clean-up-resources)
