---
title: YAML della pipeline Machine Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come definire una pipeline di Machine Learning usando un file YAML. Le definizioni di pipeline YAML vengono usate con l'estensione di machine learning per l'interfaccia della riga di comando di Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: sanpil
author: sanpil
ms.date: 11/11/2019
ms.openlocfilehash: 474a184b24ca3318a33adb89b25640939a814474
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929503"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Definire le pipeline di Machine Learning in YAML

Informazioni su come definire le pipeline di Machine Learning in [YAML](https://yaml.org/). Quando si usa l'estensione Machine Learning per l'interfaccia della riga di comando di Azure, molti comandi correlati alla pipeline prevedono un file YAML che definisce la pipeline.

La tabella seguente elenca gli elementi che non sono attualmente supportati quando si definisce una pipeline in YAML:

| Tipo di passaggio | Supportato? |
| ----- | :-----: |
| PythonScriptStep | Sì |
| AdlaStep | Sì |
| AzureBatchStep | Sì |
| DatabricksStep | Sì |
| DataTransferStep | Sì |
| AutoMLStep | No |
| HyperDriveStep | No |
| ModuleStep | No |
| MPIStep | No |
| EstimatorStep | No |

## <a name="pipeline-definition"></a>Definizione della pipeline

Una definizione di pipeline usa le chiavi seguenti, che corrispondono alla classe [Pipelines](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py) :

| Chiave YAML | DESCRIZIONE |
| ----- | ----- |
| `name` | Descrizione della pipeline. |
| `parameters` | Parametro/i alla pipeline. |
| `data_reference` | Definisce la modalità e la posizione in cui i dati devono essere resi disponibili in un'esecuzione. |
| `default_compute` | Destinazione di calcolo predefinita in cui vengono eseguiti tutti i passaggi della pipeline. |
| `steps` | Passaggi utilizzati nella pipeline. |

## <a name="parameters"></a>parametri

La sezione `parameters` usa le chiavi seguenti, che corrispondono alla classe [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py) :

| Chiave YAML | DESCRIZIONE |
| ---- | ---- |
| `type` | Tipo di valore del parametro. I tipi validi sono `string`, `int`, `float`, `bool`o `datapath`. |
| `default` | Valore predefinito. |

Ogni parametro è denominato. Il frammento di codice YAML seguente, ad esempio, definisce tre parametri denominati `NumIterationsParameter`, `DataPathParameter`e `NodeCountParameter`:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        NumIterationsParameter:
            type: int
            default: 40
        DataPathParameter:
            type: datapath
            default:
                datastore: workspaceblobstore
                path_on_datastore: sample2.txt
        NodeCountParameter:
            type: int
            default: 4
```

## <a name="data-reference"></a>Riferimento ai dati

La sezione `data_references` usa le chiavi seguenti, che corrispondono a [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py):

| Chiave YAML | DESCRIZIONE |
| ----- | ----- |
| `datastore` | Archivio dati a cui fare riferimento. |
| `path_on_datastore` | Percorso relativo nell'archivio di backup per il riferimento ai dati. |

Ogni riferimento ai dati è contenuto in una chiave. Il frammento di codice YAML seguente, ad esempio, definisce un riferimento ai dati archiviato nella chiave denominata `employee_data`:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
```

## <a name="steps"></a>Passi

I passaggi definiscono un ambiente di calcolo, insieme ai file da eseguire nell'ambiente. Per definire il tipo di un passaggio, usare la chiave di `type`:

| Tipo di passaggio | DESCRIZIONE |
| ----- | ----- |
| `AdlaStep` | Esegue uno script U-SQL con Azure Data Lake Analytics. Corrisponde alla classe [AdlaStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?view=azure-ml-py) . |
| `AzureBatchStep` | Esegue processi utilizzando Azure Batch. Corrisponde alla classe [AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?view=azure-ml-py) . |
| `DatabricsStep` | Aggiunge un notebook di databricks, uno script Python o un file JAR. Corrisponde alla classe [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?view=azure-ml-py) . |
| `DataTransferStep` | Trasferisce i dati tra le opzioni di archiviazione. Corrisponde alla classe [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) . |
| `PythonScriptStep` | Esegue uno script Python. Corrisponde alla classe [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) . |

### <a name="adla-step"></a>Passaggio Anna

| Chiave YAML | DESCRIZIONE |
| ----- | ----- |
| `script_name` | Nome dello script U-SQL (relativo alla `source_directory`). |
| `compute_target` | La destinazione di calcolo Azure Data Lake da usare per questo passaggio. |
| `parameters` | [Parametri](#parameters) della pipeline. |
| `inputs` | Gli input possono essere [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)o [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Gli output possono essere [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) o [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | Directory che contiene lo script, gli assembly e così via. |
| `priority` | Valore di priorità da usare per il processo corrente. |
| `params` | Dizionario di coppie nome-valore. |
| `degree_of_parallelism` | Grado di parallelismo da utilizzare per questo processo. |
| `runtime_version` | Versione di runtime del motore di Data Lake Analytics. |
| `allow_reuse` | Determina se il passaggio deve riutilizzare i risultati precedenti quando viene eseguito di nuovo con le stesse impostazioni. |

L'esempio seguente contiene una definizione di passaggio Anna:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
    default_compute: adlacomp
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AdlaStep"
            name: "MyAdlaStep"
            script_name: "sample_script.usql"
            source_directory: "D:\\scripts\\Adla"
            inputs:
                employee_data:
                    source: employee_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: adftestadla
                    bind_mode: mount
```

### <a name="azure-batch-step"></a>Passaggio Azure Batch

| Chiave YAML | DESCRIZIONE |
| ----- | ----- |
| `compute_target` | La destinazione di calcolo Azure Batch da usare per questo passaggio. |
| `inputs` | Gli input possono essere [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)o [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Gli output possono essere [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) o [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | Directory che contiene i file binari del modulo, eseguibili, assembly e così via. |
| `executable` | Nome del comando/eseguibile che verrà eseguito come parte di questo processo. |
| `create_pool` | Flag booleano per indicare se creare il pool prima di eseguire il processo. |
| `delete_batch_job_after_finish` | Flag booleano per indicare se eliminare il processo dall'account batch dopo che è stato terminato. |
| `delete_batch_pool_after_finish` | Flag booleano per indicare se eliminare il pool al termine del processo. |
| `is_positive_exit_code_failure` | Flag booleano per indicare se il processo ha esito negativo se l'attività termina con un codice positivo. |
| `vm_image_urn` | Se `create_pool` è `True`e la macchina virtuale usa `VirtualMachineConfiguration`. |
| `pool_id` | ID del pool in cui viene eseguito il processo. |
| `allow_reuse` | Determina se il passaggio deve riutilizzare i risultati precedenti quando viene eseguito di nuovo con le stesse impostazioni. |

L'esempio seguente contiene una definizione del passaggio Azure Batch:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        input:
            datastore: workspaceblobstore
            path_on_datastore: "input.txt"
    default_compute: testbatch
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AzureBatchStep"
            name: "MyAzureBatchStep"
            pool_id: "MyPoolName"
            create_pool: true
            executable: "azurebatch.cmd"
            source_directory: "D:\\scripts\\AureBatch"
            allow_reuse: false
            inputs:
                input:
                    source: input
            outputs:
                output:
                    destination: output
                    datastore: workspaceblobstore
```

### <a name="databricks-step"></a>Passaggio di databricks

| Chiave YAML | DESCRIZIONE |
| ----- | ----- |
| `compute_target` | La destinazione di calcolo Azure Databricks da usare per questo passaggio. |
| `inputs` | Gli input possono essere [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)o [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Gli output possono essere [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) o [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `run_name` | Nome in databricks per questa esecuzione. |
| `source_directory` | Directory che contiene lo script e altri file. |
| `num_workers` | Numero statico di ruoli di lavoro per il cluster di esecuzione di databricks. |
| `runconfig` | Percorso di un file di `.runconfig`. Questo file è una rappresentazione YAML della classe [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) . Per altre informazioni sulla struttura di questo file, vedere [runconfigschema. JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Determina se il passaggio deve riutilizzare i risultati precedenti quando viene eseguito di nuovo con le stesse impostazioni. |

L'esempio seguente contiene un passaggio di databricks:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "dbtest"
    default_compute: mydatabricks
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DatabricksStep"
            name: "MyDatabrickStep"
            run_name: "DatabricksRun"
            python_script_name: "train-db-local.py"
            source_directory: "D:\\scripts\\Databricks"
            num_workers: 1
            allow_reuse: true
            inputs:
                blob_test_data:
                    source: blob_test_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="data-transfer-step"></a>Passaggio trasferimento dati

| Chiave YAML | DESCRIZIONE |
| ----- | ----- |
| `compute_target` | La destinazione di calcolo Azure Data Factory da usare per questo passaggio. |
| `source_data_reference` | Connessione di input utilizzata come origine delle operazioni di trasferimento dei dati. I valori supportati sono [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)o [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `destination_data_reference` | Connessione di input che funge da destinazione delle operazioni di trasferimento dei dati. I valori supportati sono [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) e [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `allow_reuse` | Determina se il passaggio deve riutilizzare i risultati precedenti quando viene eseguito di nuovo con le stesse impostazioni. |

L'esempio seguente contiene un passaggio di trasferimento dei dati:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "testdata"
    default_compute: adftest
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DataTransferStep"
            name: "MyDataTransferStep"
            adla_compute_name: adftest
            source_data_reference:
                adls_test_data:
                    source: adls_test_data
            destination_data_reference:
                blob_test_data:
                    source: blob_test_data
```

### <a name="python-script-step"></a>Passaggio di script Python

| Chiave YAML | DESCRIZIONE |
| ----- | ----- |
| `compute_target` | Destinazione di calcolo da usare per questo passaggio. La destinazione di calcolo può essere un Azure Machine Learning calcolo, una macchina virtuale, ad esempio il Data Science VM, o HDInsight. |
| `inputs` | Gli input possono essere [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)o [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Gli output possono essere [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) o [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `script_name` | Nome dello script Python (relativo a `source_directory`). |
| `source_directory` | Directory che contiene lo script, l'ambiente conda e così via. |
| `runconfig` | Percorso di un file di `.runconfig`. Questo file è una rappresentazione YAML della classe [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) . Per altre informazioni sulla struttura di questo file, vedere [runconfig. JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Determina se il passaggio deve riutilizzare i risultati precedenti quando viene eseguito di nuovo con le stesse impostazioni. |

L'esempio seguente contiene un passaggio di script Python:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        DataReference1:
            datastore: workspaceblobstore
            path_on_datastore: testfolder/sample.txt
    default_compute: cpu-cluster
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "PythonScriptStep"
            name: "MyPythonScriptStep"
            script_name: "train.py"
            allow_reuse: True
            source_directory: "D:\\scripts\\PythonScript"
            inputs:
                InputData:
                    source: DataReference1
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

## <a name="schedules"></a>Pianificazioni

Quando si definisce la pianificazione per una pipeline, può essere attivata da un archivio dati o ricorrente in base a un intervallo di tempo. Di seguito sono riportate le chiavi usate per definire una pianificazione:

| Chiave YAML | DESCRIZIONE |
| ----- | ----- |
| `description` | Descrizione della pianificazione. |
| `recurrence` | Contiene le impostazioni di ricorrenza, se la pianificazione è ricorrente. |
| `pipeline_parameters` | Tutti i parametri richiesti dalla pipeline. |
| `wait_for_provisioning` | Indica se attendere il completamento del provisioning della pianificazione. |
| `wait_timeout` | Numero di secondi di attesa prima del timeout. |
| `datastore_name` | Archivio dati da monitorare per i BLOB modificati/aggiunti. |
| `polling_interval` | Quanto tempo, in minuti, tra il polling per i BLOB modificati/aggiunti. Valore predefinito: 5 minuti. Supportato solo per le pianificazioni dell'archivio dati. |
| `data_path_parameter_name` | Nome del parametro della pipeline del percorso dati da impostare con il percorso BLOB modificato. Supportato solo per le pianificazioni dell'archivio dati. |
| `continue_on_step_failure` | Indica se continuare l'esecuzione di altri passaggi nel PipelineRun inviato in caso di esito negativo di un passaggio. Se specificato, eseguirà l'override dell'impostazione `continue_on_step_failure` della pipeline.
| `path_on_datastore` | Facoltativa. Percorso nell'archivio dati da monitorare per i BLOB modificati/aggiunti. Il percorso è sotto il contenitore per l'archivio dati, quindi il percorso effettivo di monitoraggio della pianificazione è contenitore/`path_on_datastore`. Se è None, viene monitorato il contenitore dell'archivio dati. Le aggiunte o le modifiche apportate in una sottocartella del `path_on_datastore` non vengono monitorate. Supportato solo per le pianificazioni dell'archivio dati. |

L'esempio seguente contiene la definizione per una pianificazione attivata da un archivio dati:

```yaml
Schedule: 
      description: "Test create with datastore" 
      recurrence: ~ 
      pipeline_parameters: {} 
      wait_for_provisioning: True 
      wait_timeout: 3600 
      datastore_name: "workspaceblobstore" 
      polling_interval: 5 
      data_path_parameter_name: "input_data" 
      continue_on_step_failure: None 
      path_on_datastore: "file/path" 
```

Quando si definisce una **pianificazione ricorrente**, usare le chiavi seguenti in `recurrence`:

| Chiave YAML | DESCRIZIONE |
| ----- | ----- |
| `frequency` | Frequenza con cui si ripete la pianificazione. I valori validi sono `"Minute"`, `"Hour"`, `"Day"`, `"Week"`o `"Month"`. |
| `interval` | Frequenza con cui viene attivata la pianificazione. Il valore integer è il numero di unità di tempo di attesa fino a quando la pianificazione non viene riattivata. |
| `start_time` | Ora di inizio per la pianificazione. Il formato della stringa del valore è `YYYY-MM-DDThh:mm:ss`. Se non viene specificata l'ora di inizio, il primo carico di lavoro viene eseguito immediatamente e i carichi di lavoro futuri vengono eseguiti in base alla pianificazione. Se l'ora di inizio è nel passato, il primo carico di lavoro viene eseguito al successivo tempo di esecuzione calcolato. |
| `time_zone` | Fuso orario per l'ora di inizio. Se non viene specificato alcun fuso orario, viene utilizzata l'ora UTC. |
| `hours` | Se `frequency` è `"Day"` o `"Week"`, è possibile specificare uno o più numeri interi da 0 a 23, separati da virgole, come ore del giorno in cui deve essere eseguita la pipeline. È possibile utilizzare solo `time_of_day` o `hours` e `minutes`. |
| `minutes` | Se `frequency` è `"Day"` o `"Week"`, è possibile specificare uno o più numeri interi compresi tra 0 e 59, separati da virgole, come minuti dell'ora in cui deve essere eseguita la pipeline. È possibile utilizzare solo `time_of_day` o `hours` e `minutes`. |
| `time_of_day` | Se `frequency` è `"Day"` o `"Week"`, è possibile specificare un'ora del giorno in cui eseguire la pianificazione. Il formato della stringa del valore è `hh:mm`. È possibile utilizzare solo `time_of_day` o `hours` e `minutes`. |
| `week_days` | Se `frequency` è `"Week"`, è possibile specificare uno o più giorni, separati da virgole, quando la pianificazione deve essere eseguita. I valori validi sono `"Monday"`, `"Tuesday"`, `"Wednesday"`, `"Thursday"`, `"Friday"`, `"Saturday"`e `"Sunday"`. |

L'esempio seguente contiene la definizione per una pianificazione ricorrente:

```yaml
Schedule: 
    description: "Test create with recurrence" 
    recurrence: 
        frequency: Week # Can be "Minute", "Hour", "Day", "Week", or "Month". 
        interval: 1 # how often fires 
        start_time: 2019-06-07T10:50:00 
        time_zone: UTC 
        hours: 
        - 1 
        minutes: 
        - 0 
        time_of_day: null 
        week_days: 
        - Friday 
    pipeline_parameters: 
        'a': 1 
    wait_for_provisioning: True 
    wait_timeout: 3600 
    datastore_name: ~ 
    polling_interval: ~ 
    data_path_parameter_name: ~ 
    continue_on_step_failure: None 
    path_on_datastore: ~ 
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [usare l'estensione CLI per Azure Machine Learning](reference-azure-machine-learning-cli.md).