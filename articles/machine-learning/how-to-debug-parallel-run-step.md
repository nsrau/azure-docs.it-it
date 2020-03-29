---
title: Debug and troubleshoot ParallelRunStep
titleSuffix: Azure Machine Learning
description: Debug and troubleshoot ParallelRunStep in machine learning pipelines in the Azure Machine Learning SDK for Python. Informazioni sulle insidie comuni per lo sviluppo con le pipeline e suggerimenti per il debug degli script prima e durante l'esecuzione remota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122964"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Debug and troubleshoot ParallelRunStep
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo viene illustrato come eseguire il debug e risolvere i problemi relativi alla classe [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) da [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Test degli script in locale

Vedere la [sezione Test degli script in locale](how-to-debug-pipelines.md#testing-scripts-locally) per le pipeline di Machine Learning.See the Testing scripts locally section for machine learning pipelines. ParallelRunStep viene eseguito come passaggio nelle pipeline di ML in modo che la stessa risposta si applica a entrambi.

## <a name="debugging-scripts-from-remote-context"></a>Debug di script da un contesto remotoDebugging scripts from remote context

La transizione dal debug locale di uno script di assegnazione del punteggio al debug di uno script di assegnazione del punteggio in una pipeline effettiva può essere un passaggio difficile. Per informazioni su come trovare i log nel portale, la sezione delle pipeline di [Machine Learning sul debug di script da un contesto remoto](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). Le informazioni contenute in tale sezione si applicano anche a un'esecuzione di passaggi parallele.

Ad esempio, il `70_driver_log.txt` file di log contiene informazioni dal controller che avvia codice del passaggio di esecuzione parallela.

A causa della natura distribuita dei processi di esecuzione parallela, sono presenti log da diverse origini. Tuttavia, vengono creati due file consolidati che forniscono informazioni di alto livello:However, two consolidated files are created that provide high-level information:

- `~/logs/overview.txt`: questo file fornisce informazioni di alto livello sul numero di mini-batch (noti anche come attività) creati finora e sul numero di mini-batch elaborati finora. A questo scopo, mostra il risultato del lavoro. Se il processo non è riuscito, verrà visualizzato il messaggio di errore e dove avviare la risoluzione dei problemi.

- `~/logs/sys/master.txt`: questo file fornisce la visualizzazione del nodo master (nota anche come agente di orchestrazione) del processo in esecuzione. Include la creazione di attività, il monitoraggio dello stato di avanzamento, il risultato dell'esecuzione.

I log generati dallo script di immissione utilizzando EntryScript.logger e le istruzioni print si troveranno nei seguenti file:

- `~/logs/user/<ip_address>/Process-*.txt`: questo file contiene i log scritti da entry_script utilizzando EntryScript.logger. Contiene anche l'istruzione di stampa (stdout) di entry_script.

Quando è necessaria una conoscenza completa del modo in cui ogni nodo ha eseguito lo script di punteggio, esaminare i singoli log dei processi per ogni nodo. I log di processo `sys/worker` sono disponibili nella cartella, raggruppati per nodi di lavoro:

- `~/logs/sys/worker/<ip_address>/Process-*.txt`: questo file fornisce informazioni dettagliate su ogni mini-batch quando viene prelevato o completato da un lavoratore. Per ogni mini-batch, questo file include:

    - L'indirizzo IP e il PID del processo di lavoro. 
    - Numero totale di elementi, numero di elementi elaborati correttamente e numero di elementi non riusciti.
    - L'ora di inizio, la durata, il tempo di processo e l'ora del metodo di esecuzione.

È inoltre possibile trovare informazioni sull'utilizzo delle risorse dei processi per ogni lavoratore. Queste informazioni sono in formato `~/logs/sys/perf/<ip_address>/`CSV e si trovano in formato . Per un singolo nodo, i `~logs/sys/perf`file di processo saranno disponibili in . Ad esempio, quando si controlla l'utilizzo delle risorse, esaminare i file seguenti:For example, when checking for resource utilization, look at the following files:

- `Process-*.csv`: utilizzo delle risorse per processo di lavoro. 
- `sys.csv`: per log del nodo.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Come si accede dallo script utente da un contesto remoto?
È possibile ottenere un logger da EntryScript come illustrato nel codice di esempio seguente per visualizzare i log nei **log/cartella utente** nel portale.

**Uno script di immissione di esempio che usa il logger:A sample entry script using the logger:**
```python
from entry_script import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Come è possibile passare un input laterale, ad esempio un file o uno o più file contenenti una tabella di ricerca, a tutti i lavoratori?

Costruire un oggetto [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) contenente l'input laterale e la registrazione con l'area di lavoro. Dopo di che è possibile accedervi nello script di inferenza (ad esempio, nel metodo init()) come segue:

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>Passaggi successivi

* Vedere la guida di riferimento sdk per informazioni sul pacchetto [azureml-contrib-pipeline-step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) e la [documentazione](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) per la classe ParallelRunStep.See the SDK reference for help with the azureml-contrib-pipeline-step package and the documentation for ParallelRunStep class.

* Seguire [l'esercitazione avanzata](tutorial-pipeline-batch-scoring-classification.md) sull'uso delle pipeline con il passaggio di esecuzione parallela.
