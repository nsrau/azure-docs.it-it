---
title: Installare e usare l'interfaccia della riga di comando di Azure Machine Learning
description: Informazioni su come installare e usare l'estensione dell'interfaccia della riga di comando di Azure Machine Learning per creare e gestire risorse quali l'area di lavoro, gli archivi dati, i set di dati, le pipeline, i modelli e le distribuzioni.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 06/22/2020
ms.custom: seodec18
ms.openlocfilehash: 490ee6e0dea5f5106d8027c2a4c9c7b09bb81fd0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91307960"
---
# <a name="install--use-the-cli-extension-for-azure-machine-learning"></a>Installare e usare l'estensione dell'interfaccia della riga di comando per Azure Machine Learning


L'interfaccia della riga di comando di Azure Machine Learning è un'estensione dell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest&preserve-view=true), un'interfaccia della riga di comando multipiattaforma per la piattaforma Azure. Questa estensione offre i comandi per lavorare con Azure Machine Learning. Consente di automatizzare le attività di Machine Learning. L'elenco seguente fornisce alcune azioni di esempio che è possibile eseguire con l'estensione dell'interfaccia della riga di comando:

+ Eseguire gli esperimenti per creare modelli di apprendimento automatico

+ Registrare modelli di Machine Learning per l'uso da parte dei clienti

+ Creare un pacchetto, distribuire e monitorare il ciclo di vita dei modelli di Machine Learning

L'interfaccia della riga di comando non sostituisce Azure Machine Learning SDK. È uno strumento complementare ottimizzato per gestire attività con un numero elevato di parametri che si adattano bene all'automazione.

## <a name="prerequisites"></a>Prerequisiti

* Per usare l'interfaccia della riga di comando, è necessario avere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Per usare i comandi dell'interfaccia della riga di comando in questo documento dall'**ambiente locale**, è necessaria l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

    Se si usa [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/), l'interfaccia della riga di comando è accessibile tramite il browser e si trova nel cloud.

## <a name="full-reference-docs"></a>Documentazione di riferimento completa

Trovare la [documentazione di riferimento completa per l'estensione azure-cli-ml dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest&preserve-view=true).

## <a name="connect-the-cli-to-your-azure-subscription"></a>Connettere l'interfaccia della riga di comando alla sottoscrizione di Azure

> [!IMPORTANT]
> Se si usa Azure Cloud Shell, è possibile ignorare questa sezione. Cloud Shell esegue automaticamente l'autenticazione con l'account usato per accedere alla sottoscrizione di Azure.

È possibile eseguire l'autenticazione della sottoscrizione di Azure dall'interfaccia della riga di comando in diversi modi. Quello più semplice consiste nell'eseguire l'autenticazione interattiva tramite un browser. Per eseguire l'autenticazione in modo interattivo, aprire una riga di comando o un terminale e usare il comando seguente:

```azurecli-interactive
az login
```

Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso. In caso contrario, è necessario aprire un browser e seguire le istruzioni nella riga di comando. Le istruzioni prevedono l'individuazione di [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e l'immissione di un codice di autorizzazione.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

Per altri metodi di autenticazione, vedere [Accedere con l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true).

## <a name="install-the-extension"></a>Installare l'estensione

Per installare l'estensione dell'interfaccia della riga di comando di Azure Machine Learning, usare il comando seguente:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> File di esempio utilizzabili con i comandi seguenti sono disponibili [qui](https://aka.ms/azml-deploy-cloud).

Quando richiesto, selezionare `y` per installare l'estensione.

Per verificare che l'estensione sia stata installata, usare il comando seguente per visualizzare un elenco di sottocomandi specifici di ML:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>Aggiornare l'estensione

Per aggiornare l'estensione dell'interfaccia della riga di comando di Azure Machine Learning, usare il comando seguente:

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

+ Se non è già disponibile un gruppo di risorse, crearne uno:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Creare un'area di lavoro di Azure Machine Learning:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    Per altre informazioni, vedere [az ml workspace create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-workspace-create).

+ Allegare una configurazione di un'area di lavoro a una cartella per abilitare la consapevolezza contestuale dell'interfaccia della riga di comando.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Questo comando crea una `.azureml` sottodirectory che contiene i file di ambiente runconfig e conda di esempio. Contiene anche un file `config.json` usato per comunicare con l'area di lavoro di Azure Machine Learning.

    Per altre informazioni, vedere [az ml folder attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-folder-attach).

+ Allegare un contenitore BLOB di Azure come archivio dati.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Per altre informazioni, vedere [az ml datastore attach-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Caricare file in un archivio dati.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Per altre informazioni, vedere [az ml datastore upload](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-datastore-upload).

+ Allegare un cluster AKS come destinazione di calcolo.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Per altre informazioni, vedere [az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

### <a name="compute-clusters"></a>Cluster di elaborazione

+ Creare un nuovo cluster di calcolo gestito.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```



+ Creare un nuovo cluster di calcolo gestito con identità gestita

  + Identità gestita assegnata dall'utente

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  + Identità gestita assegnata dal sistema

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
+ Aggiungere un'identità gestita a un cluster esistente:

    + Identità gestita assegnata dall'utente
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    + Identità gestita assegnata dal sistema

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

Per altre informazioni, vedere [az ml computetarget create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

<a id="computeinstance"></a>

### <a name="compute-instance"></a>Istanza di calcolo
Gestire le istanze di calcolo.  In tutti gli esempi riportati di seguito, il nome dell'istanza di calcolo è **CPU**

+ Creare un nuovo computeinstance.

    ```azurecli-interactive
    az ml computetarget create computeinstance  -n cpu -s "STANDARD_D3_V2" -v
    ```
    
    Per altre informazioni, vedere [AZ ml computetarget create computeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-create-computeinstance).

+ Arrestare un computeinstance.
    
    ```azurecli-interactive
    az ml computetarget stop computeinstance -n cpu -v
    ```
    
    Per ulteriori informazioni, vedere [AZ ml computetarget stop computeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-computeinstance-stop).
    
+ Avviare un computeinstance.
    
    ```azurecli-interactive
    az ml computetarget start computeinstance -n cpu -v
    ```
    
    Per altre informazioni, vedere [AZ ml computetarget Start computeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-computeinstance-start).
    
+ Riavviare un computeinstance.
    
    ```azurecli-interactive
    az ml computetarget restart computeinstance -n cpu -v
    ```
    
    Per altre informazioni, vedere [AZ ml computetarget restart computeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-computeinstance-restart).
    
+ Eliminare un computeinstance.
    
    ```azurecli-interactive
    az ml computetarget delete -n cpu -v
    ```
    
    Per ulteriori informazioni, vedere [AZ ml computetarget Delete computeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-delete).


## <a name="run-experiments"></a><a id="experiments"></a>Eseguire esperimenti

* Avviare un'esecuzione dell'esperimento. Quando si usa questo comando, specificare il nome del file runconfig (il testo prima di \*.runconfig se si sta esaminando il file system) rispetto al parametro -c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > Il comando `az ml folder attach` crea una sottodirectory `.azureml` che contiene due file runconfig di esempio. 
    >
    > Se si dispone di uno script Python che crea un oggetto di configurazione di esecuzione a livello di codice, è possibile usare [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py&preserve-view=true#&preserve-view=truesave-path-none--name-none--separate-environment-yaml-false-) per salvarlo come file runconfig.
    >
    > Lo schema runconfig completo è disponibile in questo [file JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). Lo schema è autoesplicativo tramite la chiave `description` di ogni oggetto. Sono inoltre disponibili enumerazioni per i valori possibili e un frammento di modello alla fine.

    Per altre informazioni, vedere [az ml run submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-run-submit-script).

* Visualizzare un elenco di esperimenti:

    ```azurecli-interactive
    az ml experiment list
    ```

    Per altre informazioni, vedere [az ml experiment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-experiment-list).

### <a name="hyperdrive-run"></a>Esecuzione di HyperDrive

È possibile usare HyperDrive con l'interfaccia della riga di comando di Azure per le esecuzioni di ottimizzazione dei parametri. Prima di tutto, creare un file di configurazione di HyperDrive nel formato seguente. Per informazioni dettagliate sui parametri di ottimizzazione degli iperparametri, vedere l'articolo [Ottimizzare gli iperparametri per il modello](how-to-tune-hyperparameters.md).

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Aggiungere questo file insieme ai file della configurazione di esecuzione. Inviare quindi un'esecuzione HyperDrive usando il codice seguente:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Prendere nota della sezione *arguments* in runconfig e dello *spazio dei parametri* nella configurazione di HyperDrive. Contengono gli argomenti della riga di comando da passare allo script di training. Il valore in runconfig rimane invariato per ogni iterazione, mentre l'intervallo nella configurazione di HyperDrive viene iterato. Non specificare lo stesso argomento in entrambi i file.

## <a name="dataset-management"></a>Gestione dei set di dati

I comandi seguenti illustrano come usare i set di dati in Azure Machine Learning:

+ Registrare un set di dati:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Per informazioni sul formato del file JSON usato per definire il set di dati, usare `az ml dataset register --show-template`.

    Per altre informazioni, vedere [az ml dataset register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-dataset-register).

+ Elencare tutti i set di dati in un'area di lavoro:

    ```azurecli-interactive
    az ml dataset list
    ```

    Per altre informazioni, vedere [az ml dataset list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-dataset-list).

+ Ottenere dettagli su un set di dati:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Per altre informazioni, vedere [az ml dataset show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-dataset-show).

+ Annullare la registrazione di un set di dati:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Per altre informazioni, vedere [az ml dataset unregister](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-dataset-archive).

## <a name="environment-management"></a>Gestione dell'ambiente

I comandi seguenti illustrano come creare, registrare ed elencare gli [ambienti](how-to-configure-environment.md) di Azure Machine Learning per l'area di lavoro in uso:

+ Creare file scaffolding per un ambiente:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Per altre informazioni, vedere [az ml environment scaffold](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-environment-scaffold).

+ Registrare un ambiente:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Per altre informazioni, vedere [az ml environment register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-environment-register).

+ Elencare ambienti registrati:

    ```azurecli-interactive
    az ml environment list
    ```

    Per altre informazioni, vedere [az ml environment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-environment-list).

+ Scaricare un ambiente registrato:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Per altre informazioni, vedere [az ml environment download](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-environment-download).

### <a name="environment-configuration-schema"></a>Schema di configurazione dell'ambiente

Se è stato usato il comando `az ml environment scaffold`, viene generato un file `azureml_environment.json` di modello che può essere modificato e usato per creare configurazioni personalizzate dell'ambiente con l'interfaccia della riga di comando. L'oggetto di livello principale viene mappato liberamente alla classe [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py&preserve-view=true) in Python SDK. 

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

La tabella seguente illustra in dettaglio ogni campo di primo livello nel file JSON, il tipo e una descrizione. Se un tipo di oggetto è collegato a una classe da Python SDK, esiste una corrispondenza 1:1 libera tra ogni campo JSON e il nome della variabile pubblica nella classe Python. In alcuni casi è possibile eseguire il mapping del campo a un argomento del costruttore anziché a una variabile della classe. Ad esempio, il campo `environmentVariables` viene mappato alla variabile `environment_variables` nella classe [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py&preserve-view=true).

| Campo JSON | Type | Descrizione |
|---|---|---|
| `name` | `string` | Nome dell'ambiente. Non avviare il nome con **Microsoft** o **AzureML**. |
| `version` | `string` | Versione dell'ambiente. |
| `environmentVariables` | `{string: string}` | Una mappa di hash dei nomi e dei valori delle variabili di ambiente. |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py&preserve-view=true)Hat definisce l'ambiente e l'interprete Python da usare nella risorsa di calcolo di destinazione. |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py&preserve-view=true) | Definisce le impostazioni per personalizzare l'immagine Docker compilata in base alle specifiche dell'ambiente. |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py&preserve-view=true) | La sezione configura le impostazioni di Spark. Si usa solo quando il framework è impostato su PySpark. |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py&preserve-view=true) | Configura le dipendenze della libreria Databricks. |
| `inferencingStackVersion` | `string` | Specifica la versione dello stack di inferenza aggiunta all'immagine. Per evitare di aggiungere uno stack di inferenza, lasciare questo campo `null`. Valore valido: "latest". |

## <a name="ml-pipeline-management"></a>Gestione pipeline ML

I comandi seguenti illustrano come usare le pipeline di Machine Learning:

+ Creare una pipeline di Machine Learning:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Per altre informazioni, vedere [az ml pipeline create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-pipeline-create).

    Per altre informazioni sul file YAML della pipeline, vedere [Definire le pipeline di Machine Learning in YAML](reference-pipeline-yaml.md).

+ Eseguire una pipeline:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Per altre informazioni, vedere [az ml run submit-pipeline](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    Per altre informazioni sul file YAML della pipeline, vedere [Definire le pipeline di Machine Learning in YAML](reference-pipeline-yaml.md).

+ Pianificare una pipeline:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Per altre informazioni, vedere [az ml pipeline create-schedule](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-pipeline-create-schedule).

    Per altre informazioni sul file YAML di programmazione della pipeline, vedere [Definire le pipeline di Machine Learning in YAML](reference-pipeline-yaml.md#schedules).

## <a name="model-registration-profiling-deployment"></a>Registrazione, profilatura e distribuzione di un modello

I comandi seguenti illustrano come registrare un modello con training e quindi distribuirlo come servizio di produzione:

+ Registrare un modello con Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Per altre informazioni, vedere [az ml model register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-model-register).

+ **FACOLTATIVO** Profilare il modello per ottenere valori ottimali per la CPU e la memoria per la distribuzione.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Per altre informazioni, vedere [az ml model profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-model-profile).

+ Distribuire il modello nel servizio Azure Kubernetes
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Per altre informazioni sullo schema di un file di configurazione dell'inferenza, vedere [Schema di configurazione dell'inferenza](#inferenceconfig).
    
    Per altre informazioni sullo schema di un file di configurazione della distribuzione, vedere [Schema di configurazione della distribuzione](#deploymentconfig).

    Per altre informazioni, vedere [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-model-deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Schema di configurazione dell'inferenza

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Schema di configurazione della distribuzione

### <a name="local-deployment-configuration-schema"></a>Schema di configurazione della distribuzione locale

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Schema di configurazione della distribuzione dell'istanza di contenitore di Azure 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Schema di configurazione della distribuzione rete del servizio Azure Kubernetes

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Riferimento ai comandi per l'estensione dell'interfaccia della riga di comando di Machine Learning](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest&preserve-view=true).

* [Eseguire il training e distribuire modelli di Machine Learning usando Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
