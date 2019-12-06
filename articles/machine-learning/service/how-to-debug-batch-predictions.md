---
title: Debug e risoluzione dei problemi ParallelRunStep
titleSuffix: Azure Machine Learning
description: Eseguire il debug e risolvere i problemi delle pipeline di Machine Learning in Azure Machine Learning SDK per Python. Informazioni sui problemi più comuni per lo sviluppo con pipeline e suggerimenti per eseguire il debug degli script prima e durante l'esecuzione remota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 11/21/2019
ms.openlocfilehash: db17dca1a859807f4d92b4953ec0e2785a3a4160
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852328"
---
# <a name="debug-and-troubleshoot-using-parallelrun"></a>Eseguire il debug e risolvere i problemi usando ParallelRun
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come eseguire il debug e la risoluzione dei problemi della classe [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) da [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Testing degli script in locale

Vedere la [sezione script di test localmente](how-to-debug-pipelines.md#testing-scripts-locally) per le pipeline di machine learning. Il ParallelRunStep viene eseguito come passaggio nelle pipeline di ML, quindi la stessa risposta si applica a entrambe.

## <a name="debugging-scripts-from-remote-context"></a>Debug di script dal contesto remoto

La transizione dal debug di uno script di assegnazione dei punteggi in locale per eseguire il debug di uno script di assegnazione dei punteggi in una pipeline effettiva può essere difficile. Per informazioni sull'individuazione dei log nel portale, la [sezione pipeline di machine learning per il debug di script da un contesto remoto](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). Le informazioni contenute in questa sezione si applicano anche all'esecuzione di inferenza batch.

Ad esempio, il file di log `70_driver_log.txt` contiene anche: 

* Tutte le istruzioni stampate durante l'esecuzione dello script.
* Traccia dello stack dello script.

A causa della natura distribuita dei processi di inferenza batch, sono presenti log da diverse origini diverse. Tuttavia, vengono creati due file consolidati che forniscono informazioni di alto livello:

- `~/logs/overview.txt`: questo file fornisce informazioni di alto livello sul numero di mini-batch (noti anche come attività) creati fino a questo momento e il numero di mini-batch elaborati finora. A questo scopo, Mostra il risultato del processo. Se il processo non è riuscito, verrà visualizzato il messaggio di errore e il punto in cui avviare la risoluzione dei problemi.

- `~/logs/master.txt`: questo file fornisce la visualizzazione del nodo master, nota anche come agente di orchestrazione, del processo in esecuzione. Include la creazione di attività, il monitoraggio dello stato di avanzamento e il risultato dell'esecuzione.

Quando è necessaria una conoscenza completa del modo in cui ogni nodo ha eseguito lo script score, esaminare i singoli registri dei processi per ogni nodo. I log dei processi sono disponibili nella cartella `worker`, raggruppati in base ai nodi del ruolo di lavoro:

- `~/logs/worker/<ip_address>/Process-*.txt`: questo file fornisce informazioni dettagliate su ogni mini-batch quando viene selezionato o completato da un thread di lavoro. Per ogni mini-batch, questo file include:

    - Indirizzo IP e PID del processo di lavoro. 
    - Il numero totale di elementi e il numero di elementi elaborati correttamente. 
    - L'ora di inizio e di fine nei tempi di clock (`start1` e `end1`). 
    - L'ora di inizio e di fine nel tempo del processore (`start2` e `end2`). 

È inoltre possibile trovare informazioni sull'utilizzo delle risorse dei processi per ogni thread di lavoro. Queste informazioni sono in formato CSV e si trovano in `~/logs/performance/<ip_address>/`. Ad esempio, quando si verifica l'utilizzo delle risorse, esaminare i file seguenti:

- `process_resource_monitor_<ip>_<pid>.csv`: utilizzo delle risorse per il processo di lavoro. 
- `sys_resource_monitor_<ip>.csv`: log per nodo.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Ricerca per categorie log dallo script utente da un contesto remoto?
È possibile configurare un logger con i passaggi seguenti per fare in modo che i log vengano visualizzati nella cartella **logs/Users** nel portale:
1. Salvare la prima sezione di codice riportata di seguito in file entry_script_helper. py e inserire il file nella stessa cartella dello script di immissione. Questa classe ottiene il percorso all'interno di AmlCompute. Per il test locale, è possibile modificare `get_working_dir()` per restituire una cartella locale.
2. Configurare un logger nel metodo `init()` e quindi usarlo. La seconda sezione di codice riportata di seguito è un esempio. 

**entry_script_helper. py:**
```python
"""
This module provides helper features for entry script.
This file should be in Python search paths or in the same folder as the entry script.
"""
import os
import socket
import logging
import time
from multiprocessing import current_process
from azureml.core import Run


class EntryScriptHelper:
    """ A helper to provide common features for entry script."""

    LOG_CONFIGED = False

    def get_logger(self, name="EntryScript"):
        """ Return a logger.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        return logging.getLogger(name)

    def config(self, name="EntryScript", level="INFO"):
        """ Config a logger. This should be called in init() in score module.
            Config the logger one time if not configed.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        logger = logging.getLogger(name)

        formatter = logging.Formatter(
            "%(asctime)s|%(name)s|%(levelname)s|%(process)d|%(thread)d|%(funcName)s()|%(message)s"
        )
        formatter.converter = time.gmtime

        logger.setLevel(level)

        handler = logging.FileHandler(self.get_log_file_path())
        handler.setLevel(level)
        handler.setFormatter(formatter)
        logger.addHandler(handler)

        return logger

    def get_log_file_path(self):
        """ Get the log file path for users.
            Each process has its own log file, so there is not race issue among multiple processes.
        """
        ip_address = socket.gethostbyname(socket.gethostname())
        log_dir = os.path.join(self.get_log_dir(), "user", ip_address)
        os.makedirs(log_dir, exist_ok=True)
        return os.path.join(log_dir, f"{current_process().name}.txt")

    def get_log_dir(self):
        """ Return the folder for logs.
            Files and folders in it will be uploaded and show up in run detail page in the azure portal.
        """
        log_dir = os.path.join(self.get_working_dir(), "logs")
        os.makedirs(log_dir, exist_ok=True)
        return log_dir

    def get_working_dir(self):
        """ Return the working directory."""
        return os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), self.get_run().id)

    def get_temp_dir(self):
        """ Return local temp directory."""
        local_temp_dir = os.path.join(
            os.environ.get("AZ_BATCHAI_JOB_TEMP", ""), "azureml-bi", str(os.getpid())
        )
        os.makedirs(local_temp_dir, exist_ok=True)
        return local_temp_dir

    def get_run(self):
        """ Return the Run from the context."""
        return Run.get_context(allow_offline=False)

```

**Uno script di immissione di esempio che usa il logger:**
```python
"""
This is a sample scoring module.

This module provides a sample which passes the input back without any change.
"""
import os
import logging
from entry_script_helper import EntryScriptHelper

LOG_NAME = "score_file_list"


def init():
    """ Init """
    EntryScriptHelper().config(LOG_NAME)
    logger = logging.getLogger(LOG_NAME)
    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    logger.info(f"{__file__}.output_folder:{output_folder}")
    logger.info("init()")
    os.makedirs(output_folder, exist_ok=True)


def run(mini_batch):
    """ Accept and return the list back."""
    logger = logging.getLogger(LOG_NAME)
    logger.info(f"{__file__}, run({mini_batch})")

    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    for file_name in mini_batch:
        with open(file_name, "r") as file:
            lines = file.readlines()
        base_name = os.path.basename(file_name)
        name = os.path.join(output_folder, base_name)
        logger.info(f"{__file__}: {name}")
        with open(name, "w") as file:
            file.write(f"ouput file {name} from {__file__}:\n")
            for line in lines:
                file.write(line)

    return mini_batch
```

## <a name="next-steps"></a>Passaggi successivi

* Vedere le informazioni di riferimento su SDK per il pacchetto [azureml-contrib-pipeline-Step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) e la [documentazione](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) relativa alla classe ParallelRunStep.

* Seguire l' [esercitazione avanzata](tutorial-pipeline-batch-scoring-classification.md) sull'uso delle pipeline per il Punteggio batch.
