---
title: Debug e risoluzione dei problemi ParallelRunStep
titleSuffix: Azure Machine Learning
description: Eseguire il debug e risolvere i problemi di ParallelRunStep nelle pipeline di Machine Learning in Azure Machine Learning SDK per Python. Informazioni sui problemi più comuni per lo sviluppo con pipeline e suggerimenti per eseguire il debug degli script prima e durante l'esecuzione remota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76122964"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Debug e risoluzione dei problemi ParallelRunStep
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come eseguire il debug e la risoluzione dei problemi della classe [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) da [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Testing degli script in locale

Vedere la [sezione script di test localmente](how-to-debug-pipelines.md#testing-scripts-locally) per le pipeline di machine learning. Il ParallelRunStep viene eseguito come passaggio nelle pipeline di ML, quindi la stessa risposta si applica a entrambe.

## <a name="debugging-scripts-from-remote-context"></a>Debug di script dal contesto remoto

La transizione dal debug di uno script di assegnazione dei punteggi in locale per eseguire il debug di uno script di assegnazione dei punteggi in una pipeline effettiva può essere difficile. Per informazioni sull'individuazione dei log nel portale, la [sezione pipeline di machine learning per il debug di script da un contesto remoto](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). Le informazioni contenute in questa sezione si applicano anche a un'esecuzione parallela dei passaggi.

Ad esempio, il file `70_driver_log.txt` di log contiene le informazioni del controller che avvia il codice del passaggio di esecuzione parallela.

A causa della natura distribuita dei processi di esecuzione parallela, sono presenti log da diverse origini diverse. Tuttavia, vengono creati due file consolidati che forniscono informazioni di alto livello:

- `~/logs/overview.txt`: Questo file fornisce informazioni di alto livello sul numero di mini-batch (noti anche come attività) creati fino a questo momento e il numero di mini-batch elaborati fino a questo momento. A questo scopo, Mostra il risultato del processo. Se il processo non è riuscito, verrà visualizzato il messaggio di errore e il punto in cui avviare la risoluzione dei problemi.

- `~/logs/sys/master.txt`: Questo file fornisce la visualizzazione del nodo master, nota anche come agente di orchestrazione, del processo in esecuzione. Include la creazione di attività, il monitoraggio dello stato di avanzamento e il risultato dell'esecuzione.

I log generati dallo script di immissione usando EntryScript. logger e le istruzioni Print verranno trovati nei file seguenti:

- `~/logs/user/<ip_address>/Process-*.txt`: Questo file contiene i log scritti da entry_script usando EntryScript. logger. Contiene anche l'istruzione Print (stdout) da entry_script.

Quando è necessaria una conoscenza completa del modo in cui ogni nodo ha eseguito lo script score, esaminare i singoli registri dei processi per ogni nodo. I log dei processi sono disponibili nella `sys/worker` cartella raggruppata in base ai nodi del ruolo di lavoro:

- `~/logs/sys/worker/<ip_address>/Process-*.txt`: Questo file fornisce informazioni dettagliate su ogni mini-batch quando viene selezionato o completato da un thread di lavoro. Per ogni mini-batch, questo file include:

    - Indirizzo IP e PID del processo di lavoro. 
    - Il numero totale di elementi, il numero di elementi elaborati correttamente e il numero di elementi non riusciti.
    - Ora di inizio, durata, ora del processo e tempo di esecuzione del metodo.

È inoltre possibile trovare informazioni sull'utilizzo delle risorse dei processi per ogni thread di lavoro. Queste informazioni sono in formato CSV e si trovano in `~/logs/sys/perf/<ip_address>/`. Per un singolo nodo, i file di processo saranno disponibili `~logs/sys/perf`con. Ad esempio, quando si verifica l'utilizzo delle risorse, esaminare i file seguenti:

- `Process-*.csv`: Per l'utilizzo delle risorse del processo di lavoro. 
- `sys.csv`: Log per nodo.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Ricerca per categorie log dallo script utente da un contesto remoto?
È possibile ottenere un logger da EntryScript, come illustrato nel codice di esempio seguente, per fare in modo che i log vengano visualizzati nella cartella **logs/User** nel portale.

**Uno script di immissione di esempio che usa il logger:**
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

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Come è possibile passare un input laterale, ad esempio un file o un file contenente una tabella di ricerca, a tutti i dipendenti?

Costruire un oggetto [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) contenente l'input laterale e registrarlo con l'area di lavoro. Successivamente, è possibile accedervi nello script di inferenza, ad esempio nel metodo init (), come indicato di seguito:

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>Passaggi successivi

* Vedere le informazioni di riferimento su SDK per il pacchetto [azureml-contrib-pipeline-Step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) e la [documentazione](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) relativa alla classe ParallelRunStep.

* Seguire l' [esercitazione avanzata](tutorial-pipeline-batch-scoring-classification.md) sull'uso di pipeline con il passaggio di esecuzione parallela.
