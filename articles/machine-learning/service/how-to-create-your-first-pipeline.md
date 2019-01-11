---
title: Creare, eseguire e monitorare pipeline di Machine Learning
titleSuffix: Azure Machine Learning service
description: Creare ed eseguire una pipeline di Machine Learning con l'SDK di Azure Machine Learning per Python. Le pipeline consentono di creare e gestire i flussi di lavoro in cui sono unite le varie fasi del processo di Machine Learning. Queste fasi includono la preparazione dei dati, il training del modello, la distribuzione del modello e l'inferenza.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6c6472b824eefdd1954f3645c69090d1fb5455de
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2018
ms.locfileid: "53754459"
---
# <a name="create-and-run-a-machine-learning-pipeline-by-using-azure-machine-learning-sdk"></a>Creare ed eseguire una pipeline di Machine Learning con l'SDK di Azure Machine Learning

Questo articolo illustra come creare, pubblicare, eseguire e monitorare una [pipeline di Machine Learning](concept-ml-pipelines.md) usando l'[SDK di Azure Machine Learning](https://aka.ms/aml-sdk).  Le pipeline consentono di creare e gestire i flussi di lavoro in cui sono unite le varie fasi di Machine Learning. Ogni fase di una pipeline, ad esempio la preparazione dei dati e il training del modello, può includere uno o più passaggi.

Le pipeline create sono visibili ai membri dell'[area di lavoro](how-to-manage-workspace.md) del servizio Azure Machine Learning. 

Le pipeline usano destinazioni di calcolo remote per il calcolo e l'archiviazione dei dati intermedi e finali associati. Le pipeline possono eseguire operazioni di lettura e scrittura dei dati da e verso le posizioni di [Archiviazione di Azure](https://docs.microsoft.com/azure/storage/) supportate.

>[!Note]
>Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento del servizio Azure Machine Learning](http://aka.ms/AMLFree).

## <a name="prerequisites"></a>Prerequisiti

* [Configurare il proprio ambiente di sviluppo](how-to-configure-environment.md) per installare l'SDK di Azure Machine Learning.

* Creare un'[area di lavoro di Azure Machine Learning](how-to-configure-environment.md#workspace) che conterrà tutte le risorse della pipeline. 

 ```python
 ws = Workspace.create(
     name = '<workspace-name>',
     subscription_id = '<subscription-id>',
     resource_group = '<resource-group>',
     location = '<workspace_region>',
     exist_ok = True)
 ```

## <a name="set-up-machine-learning-resources"></a>Configurare le risorse di Machine Learning

Creare le risorse necessarie per eseguire una pipeline:

* Configurare un archivio dati che verrà usato per accedere ai dati necessari nei passaggi della pipeline.

* Configurare un oggetto `DataReference` in modo da puntare ai dati che si trovano in un archivio dati o che sono accessibili da tale archivio.

* Configurare le [destinazioni di calcolo](concept-azure-machine-learning-architecture.md#compute-target) in cui verranno eseguiti i passaggi della pipeline.

### <a name="set-up-a-datastore"></a>Configurare un archivio dati
Un archivio dati contiene i dati a cui accede la pipeline. Ogni area di lavoro ha un archivio dati predefinito. È possibile registrare altri archivi dati. 

Quando si crea la propria area di lavoro, [File di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)e [Archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) vengono collegati all'area per impostazione predefinita. File di Azure è l'archivio dati predefinito per un'area di lavoro, ma come archivio dati è possibile usare anche la risorsa di archiviazione BLOB. Per altre informazioni, vedere [Decidere quando usare BLOB di Azure, File di Azure o Dischi di Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore (Azure file storage)
def_data_store = ws.get_default_datastore() 

# The above call is equivalent to this 
def_data_store = Datastore(ws, "workspacefilestore")

# Get blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")
```

Caricare le directory o i file di dati nell'archivio dati per renderli accessibili dalle pipeline. Questo esempio usa la versione di archiviazione BLOB dell'archivio dati:

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups", 
    overwrite=True)
```

Una pipeline è costituita da uno o più passaggi. Un passaggio è un'unità di esecuzione in una destinazione di calcolo. I passaggi possono utilizzare origini dati e generare dati "intermedi". Un passaggio può creare dati, ad esempio un modello, una directory con modello e file dipendenti o dati temporanei. Questi dati sono quindi disponibili per altri passaggi successivi nella pipeline.

### <a name="configure-data-reference"></a>Configurare un riferimento ai dati

Si è appena creata un'origine dati a cui si può fare riferimento in una pipeline come input per un passaggio. Un'origine dati in una pipeline è rappresentata da un oggetto [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference). L'oggetto `DataReference` punta ai dati che si trovano in un archivio dati o che sono accessibili da tale archivio.

```python
blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

I dati intermedi (o output di un passaggio) sono rappresentati da un oggetto [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py). `output_data1` viene generato come output di un passaggio e usato come input di uno o più passaggi successivi. `PipelineData` introduce una dipendenza dei dati tra i vari passaggi e crea un ordine di esecuzione implicito nella pipeline.

```python
output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

### <a name="set-up-compute"></a>Configurare l'ambiente di calcolo

In Azure Machine Learning il termine *ambiente di calcolo* (o *destinazione di calcolo*) si riferisce ai computer o ai cluster che eseguono i passaggi di calcolo nella pipeline di Machine Learning. È ad esempio possibile creare un ambiente di calcolo di Azure Machine Learning per eseguire i passaggi.

```python
compute_name = "aml-compute"
 if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size, # NC6 is GPU-enabled
                                                                min_nodes = 1, 
                                                                max_nodes = 4)
     # create the compute target
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # Can poll for a minimum number of nodes and for a specific timeout. 
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

## <a name="construct-your-pipeline-steps"></a>Creare i passaggi della pipeline

A questo punto è possibile definire un passaggio della pipeline. Con l'SDK di Azure Machine Learning sono disponibili numerosi passaggi predefiniti. Il passaggio più semplice è `PythonScriptStep`, che esegue uno script Python in una destinazione di calcolo specificata.

```python
trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", processed_data1],
    inputs=[blob_input_data],
    outputs=[processed_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Dopo la definizione dei passaggi, si crea la pipeline usando alcuni o tutti i passaggi definiti.

>[!NOTE]
>Quando si definiscono i passaggi o si crea la pipeline, nel servizio Azure Machine Learning non viene eseguito alcun caricamento di file o dati.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

## <a name="submit-the-pipeline"></a>Inviare la pipeline

Quando si invia la pipeline, il servizio Azure Machine Learning controlla le dipendenze per ogni passaggio e carica uno snapshot della directory di origine specificata. Se la directory di origine non è specificata, viene caricata la directory locale corrente.

```python
# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
```

Quando si esegue una pipeline per la prima volta, Azure Machine Learning:

* Scarica lo snapshot del progetto nella destinazione di calcolo dalla risorsa di archiviazione BLOB associata all'area di lavoro.
* Crea un'immagine Docker corrispondente a ogni passaggio nella pipeline.
* Scarica l'immagine Docker per ogni passaggio nella destinazione di calcolo dal registro contenitori.
* Monta l'archivio dati se in un passaggio è specificato un oggetto `DataReference`. Se il montaggio non è supportato, in alternativa i dati vengono copiati nella destinazione di calcolo.
* Esegue il passaggio nella destinazione di calcolo specificata nella definizione del passaggio. 
* Crea gli artefatti, ad esempio i log, stdout e stderr, le metriche e l'output specificati dal passaggio. Questi artefatti vengono quindi caricati e conservati nell'archivio dati predefinito dell'utente.

![Diagramma dell'esecuzione di un esperimento come pipeline](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

## <a name="publish-a-pipeline"></a>Pubblicare una pipeline

È possibile pubblicare una pipeline in modo da eseguirla in un secondo momento con input diversi. Per consentire all'endpoint REST di una pipeline già pubblicata di accettare parametri, è necessario impostare parametri per la pipeline prima della pubblicazione. 

1. Per creare un parametro per la pipeline, usare un oggetto [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) con un valore predefinito.

 ```python
 pipeline_param = PipelineParameter(
     name="pipeline_arg", 
     default_value=10)
 ```

2. Aggiungere l'oggetto `PipelineParameter` come parametro a uno dei passaggi della pipeline, come indicato di seguito:

 ```python
 compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],    
     target=compute_target, 
     source_directory=project_folder)
 ```

3. Pubblicare la pipeline che accetterà un parametro quando richiamata.

```python
published_pipeline1 = pipeline1.publish(
    name="My_Published_Pipeline", 
    description="My Published Pipeline Description")
```

## <a name="run-a-published-pipeline"></a>Eseguire una pipeline pubblicata

Tutte le pipeline pubblicate hanno un endpoint REST che richiama l'esecuzione della pipeline da sistemi esterni, ad esempio client non Python. Questo endpoint consente la "ripetibilità gestita" in scenari di ripetizione del training e assegnazione di punteggi in batch.

Per richiamare l'esecuzione della pipeline precedente, è necessario un token di intestazione di autenticazione di Azure Active Directory, come descritto nell'articolo sulla [classe AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py).

```python
response = requests.post(published_pipeline1.endpoint, 
    headers=aad_token, 
    json={"ExperimentName": "My_Pipeline",
        "ParameterAssignments": {"pipeline_arg": 20}})
```
## <a name="view-results"></a>Visualizzare i risultati

Visualizzare l'elenco di tutte le pipeline e i relativi dettagli di esecuzione:
1. Accedere al [portale di Azure](https://portal.azure.com/).  

1. [Visualizzare l'area di lavoro](how-to-manage-workspace.md#view-a-workspace) per trovare l'elenco delle pipeline.
 ![elenco delle pipeline di Machine Learning](./media/how-to-create-your-first-pipeline/list_of_pipelines.png)
 
1. Selezionare una pipeline specifica per visualizzare i risultati dell'esecuzione.

## <a name="next-steps"></a>Passaggi successivi
- Usare [questi notebook di Jupyter in GitHub](https://aka.ms/aml-pipeline-readme) per esplorare più in dettaglio le pipeline di Machine Learning.
- Leggere la guida di riferimento dell'SDK per i pacchetti [azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e [azureml-pipelines-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py).

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
