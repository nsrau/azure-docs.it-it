---
title: Distribuire modelli con un'immagine Docker personalizzata
titleSuffix: Azure Machine Learning
description: Informazioni su come usare un'immagine di base Docker personalizzata quando si distribuiscono i modelli di Azure Machine Learning. Mentre Azure Machine Learning fornisce un'immagine di base predefinita, è anche possibile usare un'immagine di base personalizzata.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 76eed22052b8c9fe2cc849e68dd926ef2c85208a
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87843216"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Distribuire un modello usando un'immagine di base Docker personalizzata
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Informazioni su come usare un'immagine di base Docker personalizzata quando si distribuiscono modelli con training con Azure Machine Learning.

Quando si distribuisce un modello sottoposto a training in un servizio Web o in un dispositivo IoT Edge, viene creato un pacchetto che contiene un server Web per la gestione delle richieste in ingresso.

Azure Machine Learning fornisce un'immagine di base Docker predefinita, pertanto non è necessario preoccuparsi di crearne una. È anche possibile usare Azure Machine Learning __ambienti__ per selezionare un'immagine di base specifica oppure usare un oggetto personalizzato fornito dall'utente.

Un'immagine di base viene usata come punto di partenza per la creazione di un'immagine per una distribuzione. Fornisce il sistema operativo e i componenti sottostanti. Il processo di distribuzione aggiunge quindi componenti aggiuntivi, ad esempio il modello, l'ambiente conda e altre risorse, all'immagine prima di distribuirla.

In genere, si crea un'immagine di base personalizzata quando si vuole usare Docker per gestire le dipendenze, mantenere un controllo più rigoroso sulle versioni dei componenti o risparmiare tempo durante la distribuzione. Ad esempio, potrebbe essere necessario standardizzare una versione specifica di Python, conda o un altro componente. Potrebbe inoltre essere necessario installare il software richiesto dal modello, in cui il processo di installazione richiede molto tempo. L'installazione del software durante la creazione dell'immagine di base significa che non è necessario installarla per ogni distribuzione.

> [!IMPORTANT]
> Quando si distribuisce un modello, non è possibile eseguire l'override di componenti di base come il server Web o i componenti IoT Edge. Questi componenti forniscono un ambiente funzionante noto testato e supportato da Microsoft.

> [!WARNING]
> Microsoft potrebbe non essere in grado di risolvere i problemi causati da un'immagine personalizzata. Se si verificano problemi, è possibile che venga richiesto di usare l'immagine predefinita o una delle immagini fornite da Microsoft per verificare se il problema è specifico per l'immagine.

Questo documento è suddiviso in due sezioni:

* Creare un'immagine di base personalizzata: fornisce informazioni agli amministratori e DevOps sulla creazione di un'immagine personalizzata e sulla configurazione dell'autenticazione in un Container Registry di Azure tramite l'interfaccia della riga di comando di Azure e Machine Learning CLI.
* Distribuire un modello usando un'immagine di base personalizzata: fornisce informazioni ai data scientist e ai tecnici DevOps/ML sull'uso di immagini personalizzate quando si distribuisce un modello sottoposto a training da Python SDK o ML CLI.

## <a name="prerequisites"></a>Prerequisiti

* Gruppo di lavoro Azure Machine Learning. Per altre informazioni, vedere l'articolo [creare un'area di lavoro](how-to-manage-workspace.md) .
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). 
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Estensione dell'interfaccia della riga di comando per Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Un [container Registry di Azure](/azure/container-registry) o un altro registro Docker accessibile su Internet.
* I passaggi descritti in questo documento presuppongono che si abbia familiarità con la creazione e l'uso di un oggetto di __configurazione dell'inferenza__ come parte della distribuzione del modello. Per ulteriori informazioni, vedere [la pagina relativa alla posizione di distribuzione e](how-to-deploy-and-where.md).

## <a name="create-a-custom-base-image"></a>Creare un'immagine di base personalizzata

Le informazioni contenute in questa sezione presuppongono che si usi un Container Registry di Azure per archiviare le immagini docker. Quando si pianifica la creazione di immagini personalizzate per Azure Machine Learning, utilizzare il seguente elenco di controllo:

* Si userà il Container Registry di Azure creato per l'area di lavoro Azure Machine Learning o un Container Registry Azure autonomo?

    Quando si usano immagini archiviate nel __Registro contenitori per l'area di lavoro__, non è necessario eseguire l'autenticazione al registro di sistema. L'autenticazione viene gestita dall'area di lavoro.

    > [!WARNING]
    > Il Container Registry di Azure per l'area di lavoro viene __creato la prima volta che si esegue il training o si distribuisce un modello__ usando l'area di lavoro. Se è stata creata una nuova area di lavoro, ma non è stato eseguito il training o la creazione di un modello, non esiste alcun Container Registry di Azure per l'area di lavoro.

    Per informazioni sul recupero del nome del Container Registry di Azure per l'area di lavoro, vedere la sezione [ottenere il nome del registro contenitori](#getname) di questo articolo.

    Quando si usano immagini archiviate in un __Registro contenitori autonomo__, è necessario configurare un'entità servizio che disponga almeno dell'accesso in lettura. Specificare quindi l'ID dell'entità servizio (username) e la password per chiunque usi immagini dal registro di sistema. L'eccezione si verifica se si rende accessibile pubblicamente il registro contenitori.

    Per informazioni sulla creazione di un Container Registry di Azure privato, vedere [creare un registro contenitori privato](/azure/container-registry/container-registry-get-started-azure-cli).

    Per informazioni sull'uso delle entità servizio con Container Registry di Azure, vedere [autenticazione container Registry di Azure con entità servizio](/azure/container-registry/container-registry-auth-service-principal).

* Azure Container Registry e le informazioni sull'immagine: specificare il nome dell'immagine per chiunque debba usarlo. Si fa riferimento ad esempio a un'immagine denominata `myimage` , archiviata in un registro denominato `myregistry` , `myregistry.azurecr.io/myimage` quando si usa l'immagine per la distribuzione del modello

* Requisiti dell'immagine: Azure Machine Learning supporta solo le immagini Docker che forniscono il software seguente:

    * Ubuntu 16,04 o versione successiva.
    * Conda 4.5. # o successiva.
    * Python 3.5. #, 3.6. # o 3.7. #.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Ottenere informazioni sul registro contenitori

In questa sezione viene illustrato come ottenere il nome del Container Registry di Azure per l'area di lavoro di Azure Machine Learning.

> [!WARNING]
> Il Container Registry di Azure per l'area di lavoro viene __creato la prima volta che si esegue il training o si distribuisce un modello__ usando l'area di lavoro. Se è stata creata una nuova area di lavoro, ma non è stato eseguito il training o la creazione di un modello, non esiste alcun Container Registry di Azure per l'area di lavoro.

Se sono già stati sottoposti a training o distribuiti modelli con Azure Machine Learning, è stato creato un registro contenitori per l'area di lavoro. Per trovare il nome del registro contenitori, attenersi alla procedura seguente:

1. Aprire una nuova shell o un prompt dei comandi e usare il comando seguente per eseguire l'autenticazione alla sottoscrizione di Azure:

    ```azurecli-interactive
    az login
    ```

    Seguire le istruzioni per l'autenticazione alla sottoscrizione.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. Usare il comando seguente per elencare il registro contenitori per l'area di lavoro. Sostituire `<myworkspace>` con il nome dell'area di lavoro Azure Machine Learning. Sostituire `<resourcegroup>` con il gruppo di risorse di Azure che contiene l'area di lavoro:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    Le informazioni restituite sono simili al testo seguente:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    Il `<registry_name>` valore è il nome del container Registry di Azure per l'area di lavoro.

### <a name="build-a-custom-base-image"></a>Creare un'immagine di base personalizzata

I passaggi descritti in questa sezione illustrano come creare un'immagine Docker personalizzata nel Container Registry di Azure.

1. Creare un nuovo file di testo denominato `Dockerfile` e usare il testo seguente come contenuto:

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.5.12
    ARG PYTHON_VERSION=3.6

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. Da una shell o da un prompt dei comandi, usare il comando seguente per eseguire l'autenticazione nel Container Registry di Azure. Sostituire `<registry_name>` con il nome del registro contenitori in cui si vuole archiviare l'immagine:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Per caricare il Dockerfile e compilarlo, usare il comando seguente. Sostituire `<registry_name>` con il nome del registro contenitori in cui si vuole archiviare l'immagine:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > In questo esempio viene applicato un tag di `:v1` all'immagine. Se non viene specificato alcun tag, viene applicato un tag di `:latest` .

    Durante il processo di compilazione, le informazioni vengono trasmesse nuovamente alla riga di comando. Se la compilazione ha esito positivo, viene visualizzato un messaggio simile al testo seguente:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Per altre informazioni sulla creazione di immagini con una Container Registry di Azure, vedere [compilare ed eseguire un'immagine del contenitore con le attività di container Registry di Azure](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli)

Per altre informazioni sul caricamento di immagini esistenti in un Container Registry di Azure, vedere [eseguire il push della prima immagine in un registro contenitori Docker privato](/azure/container-registry/container-registry-get-started-docker-cli).

## <a name="use-a-custom-base-image"></a>Usare un'immagine di base personalizzata

Per usare un'immagine personalizzata, sono necessarie le informazioni seguenti:

* __Nome dell'immagine__. Ad esempio, `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest` è il percorso di un'immagine Docker di base fornita da Microsoft.

    > [!IMPORTANT]
    > Per le immagini personalizzate create, assicurarsi di includere tutti i tag usati con l'immagine. Ad esempio, se l'immagine è stata creata con un tag specifico, ad esempio `:v1` . Se durante la creazione dell'immagine non è stato usato un tag specifico, `:latest` è stato applicato un tag di.

* Se l'immagine si trova in un __repository privato__, sono necessarie le informazioni seguenti:

    * __Indirizzo__del registro di sistema. Ad esempio: `myregistry.azureecr.io`.
    * __Nome utente__ e __password__ di un'entità servizio con accesso in lettura al registro di sistema.

    Se non si dispone di queste informazioni, rivolgersi all'amministratore per il Container Registry di Azure che contiene l'immagine.

### <a name="publicly-available-base-images"></a>Immagini di base disponibili pubblicamente

Microsoft fornisce diverse immagini Docker in un repository accessibile pubblicamente, che può essere usato con i passaggi descritti in questa sezione:

| Immagine | Description |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Immagine di base per Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | Contiene il runtime ONNX per l'inferenza della CPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | Contiene il runtime ONNX e CUDA per GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | Contiene ONNX Runtime e TensorRT per GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm ` | Contiene ONNX Runtime e OpenVINO per Intel <sup></sup> Vision Accelerator Design basati su Movidius<sup>TM</sup> MyriadX VPUs |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Contiene ONNX Runtime e OpenVINO per Intel <sup></sup> Movidius<sup>TM</sup> USB Sticks |

Per ulteriori informazioni sulle immagini di base del runtime di ONNX, vedere la [sezione ONNX Runtime dockerfile](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) nel repository GitHub.

> [!TIP]
> Poiché queste immagini sono disponibili pubblicamente, non è necessario fornire un indirizzo, un nome utente o una password quando vengono usate.

Per altre informazioni, vedere [Azure Machine Learning](https://github.com/Azure/AzureML-Containers) repository di contenitori su GitHub.

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Usare un'immagine con l'SDK Azure Machine Learning

Per usare un'immagine archiviata nel **container Registry di Azure per l'area di lavoro**o un **Registro contenitori accessibile pubblicamente**, impostare gli attributi di [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) seguenti:

+ `docker.enabled=True`
+ `docker.base_image`: Impostare sul Registro di sistema e il percorso dell'immagine.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest"
```

Per usare un'immagine da un __Registro contenitori privato__ che non si trova nell'area di lavoro, è necessario usare `docker.base_image_registry` per specificare l'indirizzo del repository e un nome utente e una password:

```python
# Set the container registry information
myenv.docker.base_image_registry.address = "myregistry.azurecr.io"
myenv.docker.base_image_registry.username = "username"
myenv.docker.base_image_registry.password = "password"

myenv.inferencing_stack_version = "latest"  # This will install the inference specific apt packages.

# Define the packages needed by the model and scripts
from azureml.core.conda_dependencies import CondaDependencies
conda_dep = CondaDependencies()
# you must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
myenv.python.conda_dependencies=conda_dep
```

È necessario aggiungere azureml-defaults con Version >= 1.0.45 come dipendenza PIP. Questo pacchetto contiene le funzionalità necessarie per ospitare il modello come servizio Web. È anche necessario impostare inferencing_stack_version proprietà nell'ambiente su "più recente", in modo da installare pacchetti apt specifici necessari per il servizio Web. 

Dopo aver definito l'ambiente, usarlo con un oggetto [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) per definire l'ambiente di inferenza in cui verrà eseguito il modello e il servizio Web.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

A questo punto, è possibile continuare con la distribuzione. Il frammento di codice seguente, ad esempio, distribuisce un servizio Web in locale usando la configurazione dell'inferenza e l'immagine personalizzata:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Per altre informazioni sulla distribuzione, vedere [distribuire modelli con Azure Machine Learning](how-to-deploy-and-where.md).

Per altre informazioni sulla personalizzazione dell'ambiente Python, vedere [Creare e gestire ambienti per il training e la distribuzione](how-to-use-environments.md). 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Usare un'immagine con l'interfaccia della riga di comando Machine Learning

> [!IMPORTANT]
> Attualmente l'interfaccia della riga di comando di Machine Learning può usare le immagini della Container Registry di Azure per l'area di lavoro o i repository accessibili pubblicamente. Non può usare immagini di registri privati autonomi.

Prima di distribuire un modello usando l'interfaccia della riga di comando di Machine Learning, creare un [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) che usa l'immagine personalizzata. Creare quindi un file di configurazione dell'inferenza che fa riferimento all'ambiente. È anche possibile definire l'ambiente direttamente nel file di configurazione dell'inferenza. Il documento JSON seguente illustra come fare riferimento a un'immagine in un registro contenitori pubblico. In questo esempio, l'ambiente viene definito inline:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Questo file viene usato con il `az ml model deploy` comando. Il `--ic` parametro viene usato per specificare il file di configurazione dell'inferenza.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Per altre informazioni sulla distribuzione di un modello tramite l'interfaccia della riga di comando di ML, vedere la sezione relativa alla registrazione, profilatura e distribuzione del modello dell' [estensione CLI per Azure Machine Learning](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) articolo.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [dove eseguire la distribuzione e su come](how-to-deploy-and-where.md).
* Informazioni su come eseguire il [training e distribuire modelli di apprendimento automatico usando Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops).
