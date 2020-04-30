---
title: Estensione dell'interfaccia della riga di comando
titleSuffix: Azure Machine Learning
description: Informazioni sull'estensione dell'interfaccia della riga di comando di Azure Machine Learning per l'interfaccia della riga di comando di Azure. L'interfaccia della riga di comando di Azure è un'utilità della riga di comando multipiattaforma che consente di usare le risorse nel cloud di Azure. L'estensione Machine Learning consente di usare Azure Machine Learning. L'interfaccia della riga di comando di ML crea e gestisce risorse quali l'area di lavoro, gli archivi dati, i set di dati, le pipeline, i modelli e le distribuzioni.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 16f9080487af95e7de5c5f8c91fd5c8d356b7bde
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81618061"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>Usare l'estensione CLI per Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

L'interfaccia della riga di comando di Azure Machine Learning è un'estensione dell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), un'interfaccia della riga di comando multipiattaforma per la piattaforma Azure. Questa estensione fornisce i comandi per lavorare con Azure Machine Learning. Consente di automatizzare le attività di machine learning. L'elenco seguente fornisce alcune azioni di esempio che è possibile eseguire con l'estensione CLI:

+ Eseguire gli esperimenti per creare modelli di apprendimento automatico

+ Registrare modelli di Machine Learning per l'uso da parte dei clienti

+ Creare un pacchetto, distribuire e monitorare il ciclo di vita dei modelli di Machine Learning

L'interfaccia della riga di comando non sostituisce Azure Machine Learning SDK. Si tratta di uno strumento complementare ottimizzato per la gestione di attività con parametri molto adatti all'automazione.

## <a name="prerequisites"></a>Prerequisiti

* Per usare l'interfaccia della riga di comando, è necessario avere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Per usare i comandi dell'interfaccia della riga di comando in questo documento dall' **ambiente locale**, è necessaria l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Se si usa il [Azure cloud Shell](https://azure.microsoft.com//features/cloud-shell/), l'interfaccia della riga di comando è accessibile tramite il browser e vive nel cloud.

## <a name="full-reference-docs"></a>Documentazione di riferimento completa

Trovare la [documentazione completa di riferimento per l'estensione Azure-CLI-ml dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest).

## <a name="connect-the-cli-to-your-azure-subscription"></a>Connettere l'interfaccia della riga di comando alla sottoscrizione di Azure

> [!IMPORTANT]
> Se si usa il Azure Cloud Shell, è possibile ignorare questa sezione. Cloud Shell esegue automaticamente l'autenticazione con l'account usato per accedere alla sottoscrizione di Azure.

Sono disponibili diversi modi per eseguire l'autenticazione alla sottoscrizione di Azure dall'interfaccia della riga di comando. Il livello più semplice consiste nell'eseguire l'autenticazione interattiva tramite un browser. Per eseguire l'autenticazione in modo interattivo, aprire una riga di comando o un terminale e usare il comando seguente:

```azurecli-interactive
az login
```

Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso. In caso contrario, è necessario aprire un browser e seguire le istruzioni nella riga di comando. Le istruzioni prevedono [https://aka.ms/devicelogin](https://aka.ms/devicelogin) l'esplorazione e l'immissione di un codice di autorizzazione.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

Per altri metodi di autenticazione, vedere [accedere con l'interfaccia](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)della riga di comando di Azure.

## <a name="install-the-extension"></a>Installare l'estensione

Per installare l'estensione dell'interfaccia della riga di comando di Azure Machine Learning, usare il comando seguente:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> È possibile trovare i file di esempio che è possibile usare con i [comandi riportati di seguito.](https://aka.ms/azml-deploy-cloud)

Quando richiesto, selezionare `y` per installare l'estensione.

Per verificare che l'estensione sia stata installata, usare il comando seguente per visualizzare un elenco di sottocomandi specifici di ML:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>Aggiornare l'estensione

Per aggiornare l'estensione dell'interfaccia della riga di comando Machine Learning, usare il comando seguente:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>Rimuovere l'estensione

Per rimuovere l'estensione dell'interfaccia della riga di comando, usare il comando seguente:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Gestione delle risorse

I comandi seguenti illustrano come usare l'interfaccia della riga di comando per gestire le risorse usate da Azure Machine Learning.

+ Se non ne è già presente uno, creare un gruppo di risorse:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Creare un'area di lavoro Azure Machine Learning:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    > [!TIP]
    > Questo comando crea un'area di lavoro Basic Edition. Per creare un'area di lavoro aziendale, `--sku enterprise` usare l'opzione `az ml workspace create` con il comando. Per ulteriori informazioni sulle edizioni di Azure Machine Learning, vedere [che cos'è Azure Machine Learning](overview-what-is-azure-ml.md#sku).

    Per ulteriori informazioni, vedere [AZ ml Workspace create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create).

+ Alleghi la configurazione di un'area di lavoro a una cartella per abilitare la consapevolezza contestuale CLI.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Questo comando crea una `.azureml` sottodirectory che contiene i file dell'ambiente runconfig e conda di esempio. Contiene anche un `config.json` file usato per comunicare con l'area di lavoro Azure Machine Learning.

    Per ulteriori informazioni, vedere [AZ ml Folder Connetti](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Alleghi un contenitore BLOB di Azure come archivio dati.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Per altre informazioni, vedere [AZ ml datastore allegato-BLOB](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Caricare i file in un archivio dati.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Per altre informazioni, vedere [AZ ml datastore upload](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload).

+ Alleghi un cluster AKS come destinazione di calcolo.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Per altre informazioni, vedere [AZ ml computetarget allegato AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ Creare una nuova destinazione AMLcompute.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    Per altre informazioni, vedere [AZ ml computetarget create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

## <a name="run-experiments"></a><a id="experiments"></a>Eseguire esperimenti

* Avviare un'esecuzione dell'esperimento. Quando si usa questo comando, specificare il nome del file runconfig (il testo precedente \*a. runconfig se si sta osservando la file System) rispetto al parametro-c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > Il `az ml folder attach` comando crea una `.azureml` sottodirectory che contiene due file runconfig di esempio. 
    >
    > Se si dispone di uno script Python che crea un oggetto di configurazione di esecuzione a livello di codice, è possibile usare [runconfig. Save ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) per salvarlo come file runconfig.
    >
    > Lo schema runconfig completo si trova in questo [file JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). Lo schema è autodocumentato tramite la `description` chiave di ogni oggetto. Sono inoltre disponibili enumerazioni per i valori possibili e un frammento di modello alla fine.

    Per ulteriori informazioni, vedere [AZ ml Run Submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

* Visualizzare un elenco di esperimenti:

    ```azurecli-interactive
    az ml experiment list
    ```

    Per altre informazioni, vedere [AZ ml Experiment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="dataset-management"></a>Gestione DataSet

I comandi seguenti illustrano come usare i set di impostazioni nei Azure Machine Learning:

+ Registrare un set di dati:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Per informazioni sul formato del file JSON usato per definire il set di dati, usare `az ml dataset register --show-template`.

    Per ulteriori informazioni, vedere [AZ ml DataSet Register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Archiviare un set di dati attivo o deprecato:

    ```azurecli-interactive
    az ml dataset archive -n dataset-name
    ```

    Per altre informazioni, vedere [AZ ml DataSet Archive](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Deprecare un set di dati:

    ```azurecli-interactive
    az ml dataset deprecate -d replacement-dataset-id -n dataset-to-deprecate
    ```

    Per ulteriori informazioni, vedere [AZ ml DataSet deprecate](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Elencare tutti i set di impostazioni in un'area di lavoro:

    ```azurecli-interactive
    az ml dataset list
    ```

    Per altre informazioni, vedere [AZ ml DataSet list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Ottenere i dettagli di un set di dati:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Per altre informazioni, vedere [AZ ml DataSet Show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Riattivare un set di dati archiviato o deprecato:

    ```azurecli-interactive
    az ml dataset reactivate -n dataset-name
    ```

    Per ulteriori informazioni, vedere [AZ ml DataSet reactivate](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Annullare la registrazione di un set di dati:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Per ulteriori informazioni, vedere [AZ ml DataSet Unregister](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

## <a name="environment-management"></a>Gestione dell'ambiente

I comandi seguenti illustrano come creare, registrare ed elencare Azure Machine Learning [ambienti](how-to-configure-environment.md) per l'area di lavoro:

+ Creare file di impalcatura per un ambiente:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Per altre informazioni, vedere [AZ ml Environment patibolo](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold).

+ Registrare un ambiente:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Per ulteriori informazioni, vedere [AZ ml Environment Register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register).

+ Elencare gli ambienti registrati:

    ```azurecli-interactive
    az ml environment list
    ```

    Per ulteriori informazioni, vedere [AZ ml Environment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list).

+ Scaricare un ambiente registrato:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Per altre informazioni, vedere [AZ ml Environment download](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download).

### <a name="environment-configuration-schema"></a>Schema di configurazione dell'ambiente

Se è stato usato `az ml environment scaffold` il comando, viene generato un `azureml_environment.json` file modello che può essere modificato e usato per creare configurazioni di ambiente personalizzate con l'interfaccia della riga di comando. L'oggetto di livello principale viene mappato liberamente [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) alla classe in Python SDK. 

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

La tabella seguente illustra in dettaglio ogni campo di primo livello nel file JSON, il tipo e una descrizione. Se un tipo di oggetto è collegato a una classe di Python SDK, esiste una corrispondenza 1:1 tra ogni campo JSON e il nome della variabile pubblica nella classe Python. In alcuni casi è possibile eseguire il mapping del campo a un argomento del costruttore anziché a una variabile di classe. Ad esempio, il `environmentVariables` campo viene mappato `environment_variables` alla variabile nella [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) classe.

| Campo JSON | Type | Descrizione |
|---|---|---|
| `name` | `string` | Nome dell'ambiente. Non avviare il nome con **Microsoft** o **AzureML**. |
| `version` | `string` | Versione dell'ambiente. |
| `environmentVariables` | `{string: string}` | Mappa hash dei nomi e dei valori delle variabili di ambiente. |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py) | Oggetto che definisce l'ambiente e l'interprete Python da usare nella risorsa di calcolo di destinazione. |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) | Definisce le impostazioni per personalizzare l'immagine Docker compilata in base alle specifiche dell'ambiente. |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py) | La sezione Configura le impostazioni di Spark. Viene usato solo quando Framework è impostato su PySpark. |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py) | Configura le dipendenze della libreria databricks. |
| `inferencingStackVersion` | `string` | Specifica la versione dello stack di inferenza aggiunta all'immagine. Per evitare di aggiungere uno stack di inferenza, lasciare questo `null`campo. Valore valido: "Latest". |

## <a name="ml-pipeline-management"></a>Gestione pipeline ML

I comandi seguenti illustrano come usare le pipeline di Machine Learning:

+ Creare una pipeline di Machine Learning:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Per ulteriori informazioni, vedere [AZ ml pipeline create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create).

    Per altre informazioni sul file YAML della pipeline, vedere [definire pipeline di Machine Learning in YAML](reference-pipeline-yaml.md).

+ Eseguire una pipeline:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Per ulteriori informazioni, vedere [AZ ml Run Submit-pipeline](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    Per altre informazioni sul file YAML della pipeline, vedere [definire pipeline di Machine Learning in YAML](reference-pipeline-yaml.md).

+ Pianificare una pipeline:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Per ulteriori informazioni, vedere [AZ ml pipeline create-Schedule](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule).

    Per altre informazioni sul file YAML della pianificazione della pipeline, vedere [definire pipeline di Machine Learning in YAML](reference-pipeline-yaml.md#schedules).

## <a name="model-registration-profiling-deployment"></a>Registrazione del modello, profilatura, distribuzione

I comandi seguenti illustrano come registrare un modello con training e quindi distribuirlo come servizio di produzione:

+ Registrare un modello con Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Per ulteriori informazioni, vedere [AZ ml Model Register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).

+ **Facoltativo** Profilare il modello per ottenere valori di CPU e memoria ottimali per la distribuzione.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Per altre informazioni, vedere [AZ ml Model Profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

+ Distribuire il modello in AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Per altre informazioni sullo schema del file di configurazione dell'inferenza, vedere [schema di configurazione di inferenza](#inferenceconfig).
    
    Per ulteriori informazioni sullo schema del file di configurazione della distribuzione, vedere [schema di configurazione della distribuzione](#deploymentconfig).

    Per altre informazioni, vedere [AZ ml Model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Schema di configurazione dell'inferenza

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Schema di configurazione della distribuzione

### <a name="local-deployment-configuration-schema"></a>Schema di configurazione della distribuzione locale

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Schema di configurazione della distribuzione dell'istanza di contenitore di Azure 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Schema di configurazione della distribuzione del servizio Kubernetes di Azure

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Riferimento ai comandi per l'estensione CLI Machine Learning](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).

* [Eseguire il training e distribuire modelli di apprendimento automatico usando Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
