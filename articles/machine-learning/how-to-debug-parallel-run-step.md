---
title: Eseguire il debug e risolvere i problemi di ParallelRunStep
titleSuffix: Azure Machine Learning
description: Eseguire il debug e risolvere i problemi di ParallelRunStep nelle pipeline di Machine Learning in Azure Machine Learning SDK per Python. Informazioni sui problemi più comuni per lo sviluppo con pipeline e suggerimenti per eseguire il debug degli script prima e durante l'esecuzione remota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: troubleshooting
ms.reviewer: jmartens, larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 09/23/2020
ms.openlocfilehash: 7866f2dcaebe396759eb7f6315c457bfce307723
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91315576"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Eseguire il debug e risolvere i problemi di ParallelRunStep


In questo articolo viene descritto come eseguire il debug e risolvere i problemi della classe [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py&preserve-view=true) da [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true).

## <a name="testing-scripts-locally"></a>Test degli script in locale

Vedere la sezione [Test degli script in locale](how-to-debug-pipelines.md#debug-scripts-locally) per le pipeline di Machine Learning. Poiché ParallelRunStep viene eseguita come fase nelle pipeline di Machine Learning, la stessa risposta si applica a entrambi i casi.

## <a name="debugging-scripts-from-remote-context"></a>Debug degli script dal contesto remoto

La transizione dal debug di uno script di assegnazione del punteggio in locale al debug di uno script di assegnazione del punteggio in una pipeline effettiva può risultare difficile. Per informazioni sull'individuazione dei log nel portale, vedere la [sezione sulle pipeline di Machine Learning in Debug degli script dal contesto remoto](how-to-debug-pipelines.md). Le informazioni contenute nella sezione si applicano anche a ParallelRunStep.

Ad esempio, il file di log `70_driver_log.txt` contiene le informazioni del controller che avvia il codice ParallelRunStep.

A causa della natura distribuita dei processi ParallelRunStep, sono presenti log di origini diverse. Tuttavia, vengono creati due file consolidati che offrono informazioni di alto livello:

- `~/logs/job_progress_overview.txt`: questo file offre informazioni di alto livello sul numero di mini-batch (chiamati anche attività) creati e il numero di mini-batch elaborati. A questo scopo, mostra il risultato del processo. Se il processo non è riuscito, verrà visualizzato il messaggio di errore e il punto in cui avviare la risoluzione dei problemi.

- `~/logs/sys/master_role.txt`: Questo file fornisce la visualizzazione del nodo principale, nota anche come agente di orchestrazione, del processo in esecuzione. Include la creazione di attività, il monitoraggio dello stato di avanzamento e il risultato dell'esecuzione.

I log generati dallo script di immissione usando l'helper EntryScript e le istruzioni print sono disponibili nei file seguenti:

- `~/logs/user/entry_script_log/<ip_address>/<process_name>.log.txt`: Questi file sono i log scritti da entry_script usando l'helper EntryScript.

- `~/logs/user/stdout/<ip_address>/<process_name>.stdout.txt`: Questi file sono i log da stdout (ad esempio, l'istruzione Print) del entry_script.

- `~/logs/user/stderr/<ip_address>/<process_name>.stderr.txt`: Questi file sono i log di stderr della entry_script.

Per una comprensione immediata degli errori nello script, è disponibile il file:

- `~/logs/user/error.txt`: questo file tenta di riepilogare gli errori nello script.

Per altre informazioni sugli errori nello script, è disponibile il file:

- `~/logs/user/error/`: Contiene le analisi dello stack complete delle eccezioni generate durante il caricamento e l'esecuzione dello script di immissione.

Quando è necessaria una conoscenza completa del modo in cui ogni nodo ha eseguito lo script del punteggio, esaminare i singoli log dei processi per ogni nodo. I log dei processi sono disponibili nella cartella `sys/node`, raggruppati in base ai nodi di lavoro:

- `~/logs/sys/node/<ip_address>/<process_name>.txt`: Questo file fornisce informazioni dettagliate su ogni mini-batch quando viene selezionato o completato da un thread di lavoro. Per ogni mini-batch, il file include:

    - Indirizzo IP e PID del processo di lavoro. 
    - Il numero totale di elementi, il numero di elementi elaborati correttamente e il numero di elementi non elaborati.
    - L'ora di inizio, la durata, il tempo di elaborazione e l'ora del metodo run.

È anche possibile trovare informazioni sull'utilizzo delle risorse dei processi per ogni ruolo di lavoro. Queste informazioni sono in formato CSV e si trovano in `~/logs/sys/perf/<ip_address>/node_resource_usage.csv`. Le informazioni su ogni processo sono disponibili in `~logs/sys/perf/<ip_address>/processes_resource_usage.csv` .

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Come registrare dallo script utente personale da un contesto remoto?
ParallelRunStep può eseguire più processi in un nodo basato su process_count_per_node. Per organizzare i log da ogni processo nel nodo e combinare l'istruzione Print and log, è consigliabile usare ParallelRunStep logger, come illustrato di seguito. Si ottiene un logger da EntryScript e si fa in modo che i log vengano visualizzati nella cartella **logs/utente** nel portale.

**Script di immissione di esempio che usa il logger:**
```python
from azureml_user.parallel_run import EntryScript

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

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Come passare un input laterale, ad esempio uno o più file contenenti una tabella di ricerca, a tutti i ruoli di lavoro?

L'utente può passare i dati di riferimento allo script usando side_inputs parametro di ParalleRunStep. Tutti i set di impostazioni specificati come side_inputs verranno montati in ogni nodo di lavoro. L'utente può ottenere la posizione del montaggio passando un argomento.

Costruire un [set](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py&preserve-view=true) di dati contenente i dati di riferimento e registrarli con l'area di lavoro. Passarlo al parametro `side_inputs` di `ParallelRunStep`. Inoltre, è possibile aggiungere il percorso nella `arguments` sezione per accedere facilmente al percorso montato:

```python
label_config = label_ds.as_named_input("labels_input")
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

Successivamente, è possibile accedervi nello script di inferenza, ad esempio nel metodo init(), come illustrato di seguito:

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

### <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>Come usare i set di dati di input con l'autenticazione basata su entità servizio

L'utente può passare set di dati di input con l'autenticazione basata su entità servizio usata nell'area di lavoro. L'uso di tale set di dati in ParallelRunStep richiede la registrazione del set di dati per la costruzione della configurazione ParallelRunStep.

```python
service_principal = ServicePrincipalAuthentication(
    tenant_id="***",
    service_principal_id="***",
    service_principal_password="***")
 
ws = Workspace(
    subscription_id="***",
    resource_group="***",
    workspace_name="***",
    auth=service_principal
    )
 
default_blob_store = ws.get_default_datastore() # or Datastore(ws, '***datastore-name***') 
ds = Dataset.File.from_files(default_blob_store, '**path***')
registered_ds = ds.register(ws, '***dataset-name***', create_new_version=True)
```

## <a name="next-steps"></a>Passaggi successivi

* Vedere le informazioni di riferimento su SDK per il pacchetto [azureml-pipeline-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py&preserve-view=true) . Visualizzare la [documentazione](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?view=azure-ml-py&preserve-view=true) di riferimento per la classe ParallelRunStep.

* Seguire l' [esercitazione avanzata](tutorial-pipeline-batch-scoring-classification.md) sull'uso di pipeline con ParallelRunStep. Nell'esercitazione viene illustrato come passare un altro file come input laterale. 
