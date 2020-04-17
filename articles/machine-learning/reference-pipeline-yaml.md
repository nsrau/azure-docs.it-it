---
title: Machine Learning pipeline YAML
titleSuffix: Azure Machine Learning
description: Informazioni su come definire una pipeline di Machine Learning usando un file YAML.Learn how to define a machine learning pipeline using a YAML file. YAML pipeline definitions are used with the machine learning extension for the Azure CLI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: sanpil
author: sanpil
ms.date: 11/11/2019
ms.openlocfilehash: 40e6d7f3d9c28708c5adec26ddc3c0463e75adc0
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529706"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Definire pipeline di apprendimento automatico in YAMLDefine machine learning pipelines in YAML

Informazioni su come definire le pipeline di Machine Learning in [YAML](https://yaml.org/). Quando si usa l'estensione di apprendimento automatico per l'interfaccia della riga di comando di Azure, molti dei comandi correlati alla pipeline prevedono un file YAML che definisce la pipeline.

Nella tabella seguente sono elencati gli elementi che sono e non sono attualmente supportati quando si definisce una pipeline in YAML:

| Tipo di passaggio | Supportata |
| ----- | :-----: |
| PythonScriptStep (Scrittura su cui Python) | Sì |
| AdlaStep | Sì |
| Controllo di AzureBatchStep | Sì |
| DatabricksStep | Sì |
| DataTransferStep | Sì |
| Passaggio automatico | No |
| HyperDriveStep | No |
| ModuloStep | Sì |
| MpIStep | No |
| EstimatorStep | No |

## <a name="pipeline-definition"></a>Definizione della pipeline

Una definizione di pipeline utilizza le chiavi seguenti, che corrispondono alla classe [Pipelines:A pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py) definition uses the following keys, which correspond to the Pipelines class:

| Tasto YAML | Descrizione |
| ----- | ----- |
| `name` | Descrizione della pipeline. |
| `parameters` | Parametri alla pipeline. |
| `data_reference` | Definisce come e dove i dati devono essere resi disponibili in un'esecuzione. |
| `default_compute` | Destinazione di calcolo predefinita in cui vengono eseguiti tutti i passaggi della pipeline. |
| `steps` | I passaggi utilizzati nella pipeline. |

## <a name="parameters"></a>Parametri

La `parameters` sezione utilizza le chiavi seguenti, che corrispondono alla classe [PipelineParameter:The](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py) section uses the following keys, which correspond to the PipelineParameter class:

| Tasto YAML | Descrizione |
| ---- | ---- |
| `type` | Tipo del valore del parametro. I tipi `string` `int`validi `float` `bool`sono `datapath`, , , , o . |
| `default` | Valore predefinito. |

Ogni parametro è denominato. Ad esempio, il seguente frammento YAML definisce tre parametri denominati `NumIterationsParameter`, `DataPathParameter`, e `NodeCountParameter`:

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

## <a name="data-reference"></a>Riferimenti ai dati

La `data_references` sezione utilizza i seguenti tasti, che corrispondono al [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py):

| Tasto YAML | Descrizione |
| ----- | ----- |
| `datastore` | Archivio dati a cui fare riferimento. |
| `path_on_datastore` | Percorso relativo nell'archivio di backup per il riferimento ai dati. |

Ogni riferimento ai dati è contenuto in una chiave. Ad esempio, il frammento YAML seguente definisce `employee_data`un riferimento ai dati archiviato nella chiave denominata :

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

## <a name="steps"></a>Passaggi

I passaggi definiscono un ambiente di calcolo, insieme ai file da eseguire nell'ambiente. Per definire il tipo di `type` un passaggio, utilizzare la chiave:

| Tipo di passaggio | Descrizione |
| ----- | ----- |
| `AdlaStep` | Esegue uno script U-SQL con Azure Data Lake Analytics. Corrisponde alla classe [AdlaStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?view=azure-ml-py) |
| `AzureBatchStep` | Esegue i processi usando Azure Batch.Runs jobs using Azure Batch. Corrisponde alla classe [AzureBatchStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?view=azure-ml-py) |
| `DatabricsStep` | Aggiunge un blocco appunti Databricks, uno script Python o un JAR. Corrisponde alla classe [DatabricksStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?view=azure-ml-py) |
| `DataTransferStep` | Trasferisce i dati tra le opzioni di archiviazione. Corrisponde alla classe [DataTransferStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) |
| `PythonScriptStep` | Esegue uno script Python. Corrisponde alla classe [PythonScriptStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) |

### <a name="adla-step"></a>Passaggio ADLA

| Tasto YAML | Descrizione |
| ----- | ----- |
| `script_name` | Nome dello script U-SQL (relativo al `source_directory`). |
| `compute_target` | Destinazione di calcolo di Azure Data Lake da usare per questo passaggio. |
| `parameters` | [Parametri](#parameters) per la pipeline. |
| `inputs` | Gli input possono essere [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)o [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Gli output possono essere [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) o [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | Directory che contiene lo script, gli assembly e così via. |
| `priority` | Valore di priorità da utilizzare per il processo corrente. |
| `params` | Dizionario di coppie nome-valore. |
| `degree_of_parallelism` | Il grado di parallelismo da utilizzare per questo lavoro. |
| `runtime_version` | Versione di runtime del motore di Data Lake Analytics. |
| `allow_reuse` | Determina se il passaggio deve riutilizzare i risultati precedenti quando viene eseguito di nuovo con le stesse impostazioni. |

L'esempio seguente contiene una definizione di passaggio ADLA:The following example contains an ADLA Step definition:

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

### <a name="azure-batch-step"></a>Passaggio Batch di AzureAzure Batch step

| Tasto YAML | Descrizione |
| ----- | ----- |
| `compute_target` | Destinazione di calcolo di Azure Batch da usare per questo passaggio. |
| `inputs` | Gli input possono essere [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)o [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Gli output possono essere [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) o [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | Directory che contiene i file binari del modulo, l'eseguibile, gli assembly e così via. |
| `executable` | Nome del comando/eseguibile che verrà eseguito come parte di questo processo. |
| `create_pool` | Flag booleano per indicare se creare il pool prima di eseguire il processo. |
| `delete_batch_job_after_finish` | Flag booleano per indicare se eliminare il processo dall'account Batch al termine. |
| `delete_batch_pool_after_finish` | Flag booleano per indicare se eliminare il pool al termine del processo. |
| `is_positive_exit_code_failure` | Flag booleano per indicare se il processo ha esito negativo se l'attività viene chiusa con un codice positivo. |
| `vm_image_urn` | Se `create_pool` `True`è e `VirtualMachineConfiguration`VM utilizza . |
| `pool_id` | ID del pool in cui verrà eseguito il processo. |
| `allow_reuse` | Determina se il passaggio deve riutilizzare i risultati precedenti quando viene eseguito di nuovo con le stesse impostazioni. |

L'esempio seguente contiene una definizione del passaggio Batch di Azure:The following example contains an Azure Batch step definition:

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

### <a name="databricks-step"></a>Fase Databricks

| Tasto YAML | Descrizione |
| ----- | ----- |
| `compute_target` | La destinazione di calcolo di Azure Databricks da usare per questo passaggio. |
| `inputs` | Gli input possono essere [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)o [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Gli output possono essere [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) o [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `run_name` | Nome in Databricks per questa corsa. |
| `source_directory` | Directory che contiene lo script e altri file. |
| `num_workers` | Numero statico di lavoratori per il cluster Databricks. |
| `runconfig` | Percorso di `.runconfig` un file. Questo file è una rappresentazione YAML della classe [RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) Per ulteriori informazioni sulla struttura di questo file, vedere [runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Determina se il passaggio deve riutilizzare i risultati precedenti quando viene eseguito di nuovo con le stesse impostazioni. |

L'esempio seguente contiene un passaggio Databricks:The following example contains a Databricks step:

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

### <a name="data-transfer-step"></a>Fase di trasferimento dei dati

| Tasto YAML | Descrizione |
| ----- | ----- |
| `compute_target` | La destinazione di calcolo di Azure Data Factory da usare per questo passaggio. |
| `source_data_reference` | Connessione di input che funge da origine delle operazioni di trasferimento dati. I valori supportati sono [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)o [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `destination_data_reference` | Connessione di input che funge da destinazione delle operazioni di trasferimento dati. I valori supportati sono [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) e [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `allow_reuse` | Determina se il passaggio deve riutilizzare i risultati precedenti quando viene eseguito di nuovo con le stesse impostazioni. |

L'esempio seguente contiene un passaggio di trasferimento dati:The following example contains a data transfer step:

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

### <a name="python-script-step"></a>Istruzione di script Python

| Tasto YAML | Descrizione |
| ----- | ----- |
| `inputs` | Gli input possono essere [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)o [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Gli output possono essere [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) o [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `script_name` | Nome dello script Python (relativo a `source_directory`). |
| `source_directory` | Directory che contiene lo script, l'ambiente Conda e così via. |
| `runconfig` | Percorso di `.runconfig` un file. Questo file è una rappresentazione YAML della classe [RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) Per ulteriori informazioni sulla struttura di questo file, vedere [runconfig.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Determina se il passaggio deve riutilizzare i risultati precedenti quando viene eseguito di nuovo con le stesse impostazioni. |

L'esempio seguente contiene un'istruzione di script Python:The following example contains a Python script step:

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

### <a name="pipeline-with-multiple-steps"></a>Pipeline con più passaggiPipeline with multiple steps 

| Tasto YAML | Descrizione |
| ----- | ----- |
| `steps` | Sequenza di una o più definizioni PipelineStep. Si noti che `destination` il `outputs` di un `inputs` passaggio è diventato le chiavi per il .| 

```yaml
pipeline:
    name: SamplePipelineFromYAML
    description: Sample multistep YAML pipeline
    data_references:
        TitanicDS:
            dataset_name: 'titanic_ds'
            bind_mode: download
    default_compute: cpu-cluster
    steps:
        Dataprep:
            type: "PythonScriptStep"
            name: "DataPrep Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "prep.py"
            arguments:
            - '--train_path'
            - output:train_path
            - '--test_path'
            - output:test_path
            allow_reuse: True
            inputs:
                titanic_ds:
                    source: TitanicDS
                    bind_mode: download
            outputs:
                train_path:
                    destination: train_csv
                    datastore: workspaceblobstore
                test_path:
                    destination: test_csv
        Training:
            type: "PythonScriptStep"
            name: "Training Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "train.py"
            arguments:
            - "--train_path"
            - input:train_path
            - "--test_path"
            - input:test_path
            inputs:
                train_path:
                    source: train_csv
                    bind_mode: download
                test_path:
                    source: test_csv
                    bind_mode: download

```

## <a name="schedules"></a>Pianificazioni

Quando si definisce la pianificazione per una pipeline, può essere attivata dall'archivio dati o ricorrente in base a un intervallo di tempo. Di seguito sono riportate le chiavi utilizzate per definire una pianificazione:

| Tasto YAML | Descrizione |
| ----- | ----- |
| `description` | Descrizione della pianificazione. |
| `recurrence` | Contiene le impostazioni di ricorrenza, se la pianificazione è ricorrente. |
| `pipeline_parameters` | Tutti i parametri richiesti dalla pipeline. |
| `wait_for_provisioning` | Se attendere il completamento del provisioning della pianificazione. |
| `wait_timeout` | Numero di secondi di attesa prima del timeout. |
| `datastore_name` | Archivio dati da monitorare per i BLOB modificati/aggiunti. |
| `polling_interval` | Durata, in minuti, tra il polling dei BLOB modificati/aggiunti. Valore predefinito: 5 minuti. Supportato solo per le pianificazioni dell'archivio dati. |
| `data_path_parameter_name` | Nome del parametro della pipeline del percorso dati da impostare con il percorso BLOB modificato. Supportato solo per le pianificazioni dell'archivio dati. |
| `continue_on_step_failure` | Se continuare l'esecuzione di altri passaggi in PipelineRun inviato se un passaggio ha esito negativo. Se specificato, eseguirà l'override dell'impostazione `continue_on_step_failure` della pipeline.
| `path_on_datastore` | Facoltativa. Percorso nell'archivio dati da monitorare per i BLOB modificati/aggiunti. Il percorso si trova sotto il contenitore per l'archivio`path_on_datastore`dati, pertanto il percorso effettivo monitorato dal monitoraggio della pianificazione è container/ . In caso contrario, viene monitorato il contenitore dell'archivio dati. Le aggiunte/modifiche effettuate in `path_on_datastore` una sottocartella del Supportato solo per le pianificazioni dell'archivio dati. |

L'esempio seguente contiene la definizione per una pianificazione attivata dall'archivio dati:The following example contains the definition for a datastore-triggered schedule:

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

Quando si definisce una pianificazione `recurrence` **ricorrente,** utilizzare le seguenti chiavi in:

| Tasto YAML | Descrizione |
| ----- | ----- |
| `frequency` | Frequenza di ricorrenze della pianificazione. I valori `"Minute"` `"Hour"`validi `"Day"` `"Week"`sono `"Month"`, , , o . |
| `interval` | Frequenza di incendi del programma. Il valore intero è il numero di unità di tempo da attendere fino a quando la pianificazione viene riattivata. |
| `start_time` | Ora di inizio della pianificazione. Il formato stringa del `YYYY-MM-DDThh:mm:ss`valore è . Se non viene fornita alcuna ora di inizio, il primo carico di lavoro viene eseguito immediatamente e i carichi di lavoro futuri vengono eseguiti in base alla pianificazione. Se l'ora di inizio è nel passato, il primo carico di lavoro viene eseguito alla successiva fase di esecuzione calcolata. |
| `time_zone` | Fuso orario per l'ora di inizio. Se non viene fornito alcun fuso orario, viene utilizzato UTC. |
| `hours` | Se `frequency` `"Day"` è `"Week"`o , è possibile specificare uno o più numeri interi da 0 a 23, separati da virgole, come ore del giorno in cui deve essere eseguita la pipeline. Solo `time_of_day` `hours` o `minutes` e può essere utilizzato. |
| `minutes` | Se `frequency` `"Day"` è `"Week"`o , è possibile specificare uno o più numeri interi da 0 a 59, separati da virgole, come minuti dell'ora in cui deve essere eseguita la pipeline. Solo `time_of_day` `hours` o `minutes` e può essere utilizzato. |
| `time_of_day` | Se `frequency` `"Day"` è `"Week"`o , è possibile specificare un'ora del giorno per l'esecuzione della pianificazione. Il formato stringa del `hh:mm`valore è . Solo `time_of_day` `hours` o `minutes` e può essere utilizzato. |
| `week_days` | Se `frequency` `"Week"`è , è possibile specificare uno o più giorni, separati da virgole, in cui deve essere eseguita la pianificazione. I valori `"Monday"` `"Tuesday"`validi `"Wednesday"` `"Thursday"`sono `"Friday"` `"Saturday"`, `"Sunday"`, , , , e . |

L'esempio seguente contiene la definizione per una pianificazione ricorrente:The following example contains the definition for a recurring schedule:

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

Informazioni su come [usare l'estensione DELL'interfaccia della riga](reference-azure-machine-learning-cli.md)di comando per Azure Machine Learning.
