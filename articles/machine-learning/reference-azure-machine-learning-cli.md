---
title: Estensione CLI
titleSuffix: Azure Machine Learning
description: Informazioni sull'estensione dell'interfaccia della riga di comando di Azure Machine Learning per l'interfaccia della riga di comando di Azure. L'interfaccia della riga di comando di Azure è un'utilità della riga di comando multipiattaforma che consente di usare le risorse nel cloud di Azure. L'estensione Machine Learning consente di usare Azure Machine Learning.The Machine Learning extension enables you to work with Azure Machine Learning. L'interfaccia della riga di comando di ML crea e gestisce risorse quali area di lavoro, archivi dati, set di dati, pipeline, modelli e distribuzioni.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 471b26ebc4bd4aecb814ec43c7eba56e3d764fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402484"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>Usare l'estensione CLI per Azure Machine LearningUse the CLI extension for Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

L'interfaccia della riga di comando di Azure Machine Learning è un'estensione dell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), un'interfaccia della riga di comando multipiattaforma per la piattaforma Azure. Questa estensione fornisce comandi per l'uso con Azure Machine Learning.This extension provides commands for working with Azure Machine Learning. Consente di automatizzare le attività di apprendimento automatico. Nell'elenco seguente sono disponibili alcune azioni di esempio che è possibile eseguire con l'estensione CLI:The following list provides some example actions that you can do with the CLI extension:

+ Eseguire gli esperimenti per creare modelli di apprendimento automatico

+ Registrare modelli di Machine Learning per l'uso da parte dei clienti

+ Creare un pacchetto, distribuire e monitorare il ciclo di vita dei modelli di Machine Learning

L'interfaccia della riga di comando non sostituisce Azure Machine Learning SDK. Si tratta di uno strumento complementare che è ottimizzato per gestire attività altamente parametrizzate che si adattano bene all'automazione.

## <a name="prerequisites"></a>Prerequisiti

* Per usare l'interfaccia della riga di comando, è necessario avere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* [L'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)di Azure .

## <a name="full-reference-docs"></a>Documenti di riferimento completi

Trovare i documenti di [riferimento completi per l'estensione azure-cli-ml dell'interfaccia della riga di comando di Azure.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest)

## <a name="install-the-extension"></a>Installare l'estensione

Per installare l'estensione dell'interfaccia della riga di comando di Azure Machine Learning, usare il comando seguente:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> I file di esempio che è possibile utilizzare con i comandi riportati di seguito sono disponibili [qui](https://aka.ms/azml-deploy-cloud).

Quando richiesto, selezionare `y` per installare l'estensione.

Per verificare che l'estensione sia stata installata, usare il comando seguente per visualizzare un elenco di sottocomandi specifici di ML:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>Aggiornare l'estensione

Per aggiornare l'estensione dell'interfaccia della riga di comando di Machine Learning, usare il comando seguente:To update the Machine Learning CLI extension, use the following command:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>Rimuovere l'estensione

Per rimuovere l'estensione dell'interfaccia della riga di comando, usare il comando seguente:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Resource management

I comandi seguenti illustrano come usare l'interfaccia della riga di comando per gestire le risorse usate da Azure Machine Learning.

+ Se non si dispone già di uno, creare un gruppo di risorse:If you do not have one, create a resource group:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Creare un'area di lavoro di Azure Machine Learning:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    > [!TIP]
    > Questo comando crea un'area di lavoro edizione di base. Per creare un'area `--sku enterprise` di lavoro `az ml workspace create` dell'organizzazione, utilizzare l'opzione con il comando . Per altre informazioni sulle edizioni di Azure Machine Learning, vedere [Che cos'è Azure Machine Learning.](overview-what-is-azure-ml.md#sku)

    Per ulteriori informazioni, consultate [az ml workspace create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create).

+ Collegare una configurazione dell'area di lavoro a una cartella per abilitare il riconoscimento contestuale dell'interfaccia della riga di comando.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Questo comando `.azureml` crea una sottodirectory che contiene i file di ambiente runconfig e conda di esempio. Contiene inoltre `config.json` un file usato per comunicare con l'area di lavoro di Azure Machine Learning.It also contains a file that is used to communicate with your Azure Machine Learning workspace.

    Per ulteriori informazioni, vedere [az ml folder attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Collegare un contenitore BLOB di Azure come Archivio dati.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Per altre informazioni, vedere [az ml datastore attach-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Caricare file in un archivio dati.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Per ulteriori informazioni, vedere [az ml datastore upload](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload).

+ Collegare un cluster AKS come destinazione di calcolo.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Per altre informazioni, vedere [az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ Creare una nuova destinazione AMLcompute.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    Per ulteriori informazioni, vedere [az ml computetarget create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

## <a name="run-experiments"></a><a id="experiments"></a>Eseguire esperimenti

* Avviare un'esecuzione dell'esperimento. Quando si utilizza questo comando, specificare il nome \*del file runconfig (il testo prima di .runconfig se si sta esaminando il file system) rispetto al parametro -c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > Il `az ml folder attach` comando `.azureml` crea una sottodirectory, che contiene due file runconfig di esempio. 
    >
    > Se si dispone di uno script Python che crea un oggetto di configurazione di esecuzione a livello di codice, è possibile utilizzare [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) per salvarlo come file runconfig.
    >
    > Lo schema di runconfig completo è disponibile in questo [file JSON.](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) Lo schema è auto-documentazione attraverso la `description` chiave di ogni oggetto. Inoltre, sono presenti enumerazioni per i valori possibili e un frammento di modello alla fine.

    Per ulteriori informazioni, vedere [az ml run submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

* Visualizza un elenco di esperimenti:

    ```azurecli-interactive
    az ml experiment list
    ```

    Per ulteriori informazioni, consultate [Az ml experiment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="dataset-management"></a>Gestione dei set di dati

I comandi seguenti illustrano come usare i set di dati in Azure Machine Learning:The following commands demonstrate how to work with datasets in Azure Machine Learning:

+ Registrare un set di dati:Register a dataset:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Per informazioni sul formato del file JSON utilizzato `az ml dataset register --show-template`per definire il set di dati, utilizzare .

    Per ulteriori informazioni, vedere [az ml dataset register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Archiviare un set di dati attivo o deprecato:Archive an active or deprecated dataset:

    ```azurecli-interactive
    az ml dataset archive -n dataset-name
    ```

    Per ulteriori informazioni, vedere [az ml dataset archive](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Deprecare un set di dati:Deprecate a dataset:

    ```azurecli-interactive
    az ml dataset deprecate -d replacement-dataset-id -n dataset-to-deprecate
    ```

    Per ulteriori informazioni, vedere [az ml dataset deprecate](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Elencare tutti i set di dati in un'area di lavoro:List all datasets in a workspace:

    ```azurecli-interactive
    az ml dataset list
    ```

    Per ulteriori informazioni, vedere [az ml dataset list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Ottenere i dettagli di un set di dati:Get details of a dataset:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Per ulteriori informazioni, vedere [az ml dataset show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Riattivare un set di dati archiviato o deprecato:

    ```azurecli-interactive
    az ml dataset reactivate -n dataset-name
    ```

    Per ulteriori informazioni, consultate [az ml dataset reactivate](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Annullare la registrazione di un set di dati:Unregister a dataset:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Per ulteriori informazioni, vedere [az ml dataset unregister](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

## <a name="environment-management"></a>Gestione dell'ambiente

I comandi seguenti illustrano come creare, registrare ed elencare [gli ambienti](how-to-configure-environment.md) di Azure Machine Learning per l'area di lavoro:The following commands demonstrate how to create, register, and list Azure Machine Learning environments for your workspace:

+ Creare file di scaffolding per un ambiente:Create scaffolding files for an environment:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Per ulteriori informazioni, vedere [scaffolding dell'ambiente az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold).

+ Registrare un ambiente:Register an environment:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Per ulteriori informazioni, vedere [az ml environment register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register).

+ Elencare gli ambienti registrati:

    ```azurecli-interactive
    az ml environment list
    ```

    Per ulteriori informazioni, vedere [az ml environment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list).

+ Scaricare un ambiente registrato:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Per ulteriori informazioni, consultate [az ml environment download](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download).

### <a name="environment-configuration-schema"></a>Schema di configurazione dell'ambiente

Se è `az ml environment scaffold` stato utilizzato il `azureml_environment.json` comando, viene generato un file modello che può essere modificato e utilizzato per creare configurazioni di ambiente personalizzate con l'interfaccia della riga di comando. L'oggetto di primo livello [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) viene mappato liberamente alla classe in Python SDK. 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

La tabella seguente descrive in dettaglio ogni campo di primo livello nel file JSON, il tipo e una descrizione. Se un tipo di oggetto è collegato a una classe da Python SDK, esiste una corrispondenza 1:1 senza perdere tra ogni campo JSON e il nome della variabile pubblica nella classe Python. In alcuni casi il campo può eseguire il mapping a un argomento del costruttore anziché a una variabile di classe. Ad esempio, `environmentVariables` il campo `environment_variables` esegue [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) il mapping alla variabile nella classe.

| JSON | Type | Descrizione |
|---|---|---|
| `name` | `string` | Nome dell'ambiente. Non iniziare il nome con **Microsoft** o **AzureML.** |
| `version` | `string` | Versione dell'ambiente. |
| `environmentVariables` | `{string: string}` | Mapping hash di nomi e valori delle variabili di ambiente. |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py) | Oggetto che definisce l'ambiente Python e l'interprete da usare nella risorsa di calcolo di destinazione. |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) | Definisce le impostazioni per personalizzare l'immagine Docker compilata in base alle specifiche dell'ambiente. |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py) | La sezione configura le impostazioni di Spark. Viene utilizzato solo quando il framework è impostato su PySpark. |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py) | Configura le dipendenze della libreria Databricks. |
| `inferencingStackVersion` | `string` | Specifica la versione dello stack di inferenza aggiunta all'immagine. Per evitare di aggiungere uno stack `null`di inferenza, lasciare questo campo . Valore valido: "latest". |

## <a name="ml-pipeline-management"></a>Gestione pipeline ML

I comandi seguenti illustrano come usare le pipeline di Apprendimento automatico:The following commands demonstrate how to work with machine learning pipelines:

+ Creare una pipeline di apprendimento automatico:Create a machine learning pipeline:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Per ulteriori informazioni, vedere [az ml pipeline create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create).

    Per ulteriori informazioni sul file YAML della pipeline, vedere Definire pipeline di [Machine Learning in YAML](reference-pipeline-yaml.md).

+ Eseguire una pipeline:Run a pipeline:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Per ulteriori informazioni, vedere [az ml run submit-pipeline](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    Per ulteriori informazioni sul file YAML della pipeline, vedere Definire pipeline di [Machine Learning in YAML](reference-pipeline-yaml.md).

+ Pianificare una pipeline:Schedule a pipeline:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Per ulteriori informazioni, vedere [az ml pipeline create-schedule](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule).

    Per ulteriori informazioni sul file YAML di pianificazione della pipeline, vedere Definire pipeline di [Machine Learning in YAML](reference-pipeline-yaml.md#schedules).

## <a name="model-registration-profiling-deployment"></a>Registrazione del modello, profilatura, distribuzione

I comandi seguenti illustrano come registrare un modello con training e quindi distribuirlo come servizio di produzione:

+ Registrare un modello con Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Per ulteriori informazioni, vedere [az ml model register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).

+ **FACOLTATIVO** Profilare il modello per ottenere valori ottimali di CPU e memoria per la distribuzione.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Per ulteriori informazioni, vedere [az ml model profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

+ Distribuire il modello in AKSDeploy your model to AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Per ulteriori informazioni sullo schema del file di configurazione dell'inferenza, vedere Schema di [configurazione dell'inferenza](#inferenceconfig).
    
    Per ulteriori informazioni sullo schema del file di configurazione della distribuzione, vedere Schema di [configurazione della distribuzione](#deploymentconfig).

    Per ulteriori informazioni, vedere [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Schema di configurazione dell'inferenzaInference configuration schema

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Schema di configurazione della distribuzione

### <a name="local-deployment-configuration-schema"></a>Schema di configurazione della distribuzione locale

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Schema di configurazione della distribuzione dell'istanza del contenitore di AzureAzure Container Instance deployment configuration schema 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Schema di configurazione della distribuzione del servizio Azure KubernetesAzure Kubernetes Service deployment configuration schema

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Passaggi successivi

* Informazioni di riferimento sui comandi [per l'estensione dell'interfaccia della riga](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest)di comando di Machine Learning.

* [Eseguire il training e distribuire modelli di Machine Learning usando le pipeline di AzureTrain and deploy machine learning models using Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
