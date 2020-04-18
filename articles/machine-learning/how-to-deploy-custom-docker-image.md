---
title: Distribuire modelli con un'immagine Docker personalizzataDeploy models with custom Docker image
titleSuffix: Azure Machine Learning
description: Informazioni su come usare un'immagine di base Docker personalizzata quando si distribuiscono i modelli di Azure Machine Learning.Learn how to use a custom Docker base image when deploying your Azure Machine Learning models. Mentre Azure Machine Learning offre un'immagine di base predefinita, è anche possibile usare la propria immagine di base.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/16/2020
ms.openlocfilehash: a237beb72e35a236e353c58db520a8d611fdfdcd
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618001"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Distribuire un modello usando un'immagine di base Docker personalizzataDeploy a model using a custom Docker base image
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Informazioni su come usare un'immagine di base Docker personalizzata quando si distribuiscono modelli con training con Azure Machine Learning.Learn how to use a custom Docker base image when deploying trained models with Azure Machine Learning.

Quando si distribuisce un modello sottoposto a training a un servizio Web o a un dispositivo IoT Edge, viene creato un pacchetto che contiene un server Web per gestire le richieste in ingresso.

Azure Machine Learning offre un'immagine di base Docker predefinita in modo da non doversi preoccupare di crearne una. È anche possibile usare __gli ambienti__ di Azure Machine Learning per selezionare un'immagine di base specifica o usarne un'immagine personalizzata.

Un'immagine di base viene utilizzata come punto di partenza quando viene creata un'immagine per una distribuzione. Fornisce il sistema operativo e i componenti sottostanti. Il processo di distribuzione aggiunge quindi componenti aggiuntivi, ad esempio il modello, l'ambiente conda e altri asset, all'immagine prima della distribuzione.

In genere, si crea un'immagine di base personalizzata quando si desidera utilizzare Docker per gestire le dipendenze, mantenere un controllo più rigoroso sulle versioni dei componenti o risparmiare tempo durante la distribuzione. Ad esempio, è possibile standardizzare su una versione specifica di Python, Conda o un altro componente. È anche possibile installare il software richiesto dal modello, in cui il processo di installazione richiede molto tempo. L'installazione del software durante la creazione dell'immagine di base significa che non è necessario installarlo per ogni distribuzione.

> [!IMPORTANT]
> Quando si distribuisce un modello, non è possibile eseguire l'override dei componenti di base, ad esempio il server Web o i componenti IoT Edge. Questi componenti forniscono un ambiente di lavoro noto che viene testato e supportato da Microsoft.These components provide a known working environment that is tested and supported by Microsoft.

> [!WARNING]
> Microsoft potrebbe non essere in grado di risolvere i problemi causati da un'immagine personalizzata. Se si verificano problemi, potrebbe essere richiesto di utilizzare l'immagine predefinita o una delle immagini fornite da Microsoft per verificare se il problema è specifico dell'immagine.

Questo documento è suddiviso in due sezioni:

* Creare un'immagine di base personalizzata: fornisce informazioni agli amministratori e a DevOps sulla creazione di un'immagine personalizzata e sulla configurazione dell'autenticazione in un Registro contenitori di Azure usando l'interfaccia della riga di comando di Azure e l'interfaccia della riga di comando di Machine Learning.Create a custom base image: Provides information to admins and DevOps on creating a custom image and configuring authentication to an Azure Container Registry using the Azure CLI and Machine Learning CLI.
* Distribuire un modello usando un'immagine di base personalizzata: fornisce informazioni agli scienziati dei dati e ai tecnici DevOps /ML sull'uso di immagini personalizzate durante la distribuzione di un modello sottoposto a training da Python SDK o ML CLI.

## <a name="prerequisites"></a>Prerequisiti

* Un gruppo di lavoro di Azure Machine Learning.An Azure Machine Learning workgroup. Per altre informazioni, vedere l'articolo [Creare un'area di lavoro.](how-to-manage-workspace.md)
* Azure [Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). 
* [L'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)di Azure .
* [L'estensione CLI per Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Un Registro di sistema [del contenitore](/azure/container-registry) di Azure o un altro Registro di sistema Docker accessibile su Internet.An Azure Container Registry or other Docker registry that is accessible on the internet.
* I passaggi descritti in questo documento presuppongono che si abbia familiarità con la creazione e l'utilizzo di un oggetto di configurazione di __inferenza__ come parte della distribuzione del modello. Per ulteriori informazioni, vedere la sezione "preparazione alla distribuzione" in [Dove eseguire la distribuzione e come](how-to-deploy-and-where.md#prepare-to-deploy).

## <a name="create-a-custom-base-image"></a>Creare un'immagine di base personalizzataCreate a custom base image

Le informazioni contenute in questa sezione presuppongono che si stia usando un Registro di sistema del contenitore di Azure per archiviare le immagini Docker.The information in this section assumes that you are using an Azure Container Registry to store Docker images. Usare l'elenco di controllo seguente quando si pianifica la creazione di immagini personalizzate per Azure Machine Learning:Use the following checklist when planning to create custom images for Azure Machine Learning:

* Verrà usato il Registro di sistema del contenitore di Azure creato per l'area di lavoro di Azure Machine Learning o un Registro di contenitori di Azure autonomo?

    Quando si utilizzano immagini archiviate nel Registro di sistema del __contenitore per l'area di lavoro__, non è necessario eseguire l'autenticazione nel Registro di sistema. L'autenticazione viene gestita dall'area di lavoro.

    > [!WARNING]
    > Il Registro di sistema del contenitore di Azure per __l'area__ di lavoro viene creato la prima volta che si esegue il training o la distribuzione di un modello usando l'area di lavoro. Se è stata creata una nuova area di lavoro, ma non è stato eseguito il training o un modello, non esisterà alcun Registro contenitori di Azure per l'area di lavoro.

    Per informazioni sul recupero del nome del Registro di sistema del contenitore di Azure per l'area di lavoro, vedere la sezione Ottenere il [nome del Registro](#getname) di sistema del contenitore di questo articolo.

    Quando si utilizzano immagini archiviate in un __registro contenitori autonomo__, è necessario configurare un'entità servizio con almeno accesso in lettura. È quindi possibile fornire l'ID dell'entità servizio (nome utente) e la password a chiunque utilizzi le immagini del Registro di sistema. L'eccezione è se si rende il registro contenitori accessibile pubblicamente.

    Per informazioni sulla creazione di un Registro di sistema del contenitore di Azure privato, vedere [Creare un registro contenitori privato.](/azure/container-registry/container-registry-get-started-azure-cli)

    Per informazioni sull'uso delle entità servizio con il Registro di sistema del contenitore di Azure, vedere [Autenticazione del Registro](/azure/container-registry/container-registry-auth-service-principal)di sistema del contenitore di Azure con entità servizio.

* Informazioni sul Registro di sistema e sull'immagine del contenitore di Azure: fornire il nome dell'immagine a chiunque debba usarla. Ad esempio, a `myimage`un'immagine denominata , memorizzata in un Registro di sistema denominato `myregistry`, viene fatto riferimento come `myregistry.azurecr.io/myimage` quando si utilizza l'immagine per la distribuzione del modello

* Requisiti per le immagini: Azure Machine Learning supporta solo immagini Docker che forniscono il software seguente:Image requirements: Azure Machine Learning only supports Docker images that provide the following software:

    * Ubuntu 16.04 o superiore.
    * Conda 4.5.
    * Python 3.5. o 3.6.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Ottenere informazioni sul Registro di sistema del contenitoreGet container registry information

In questa sezione viene illustrato come ottenere il nome del Registro di sistema del contenitore di Azure per l'area di lavoro di Azure Machine Learning.In this section, learn how to get the name of the Azure Container Registry for your Azure Machine Learning workspace.

> [!WARNING]
> Il Registro di sistema del contenitore di Azure per __l'area__ di lavoro viene creato la prima volta che si esegue il training o la distribuzione di un modello usando l'area di lavoro. Se è stata creata una nuova area di lavoro, ma non è stato eseguito il training o un modello, non esisterà alcun Registro contenitori di Azure per l'area di lavoro.

Se i modelli sono già stati addestrati o distribuiti usando Azure Machine Learning, è stato creato un registro contenitori per l'area di lavoro. Per trovare il nome di questo registro contenitori, attenersi alla seguente procedura:

1. Aprire una nuova shell o prompt dei comandi e usare il comando seguente per eseguire l'autenticazione nella sottoscrizione di Azure:Open a new shell or command-prompt and use the following command to authenticate to your Azure subscription:

    ```azurecli-interactive
    az login
    ```

    Seguire le istruzioni visualizzate per eseguire l'autenticazione nella sottoscrizione.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. Utilizzare il comando seguente per elencare il registro contenitori per l'area di lavoro. Sostituire `<myworkspace>` con il nome dell'area di lavoro di Azure Machine Learning.Replace with your Azure Machine Learning workspace name. Sostituire `<resourcegroup>` con il gruppo di risorse di Azure che contiene l'area di lavoro:Replace with the Azure resource group that contains your workspace:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    Le informazioni restituite sono simili al testo seguente:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    Il `<registry_name>` valore è il nome del Registro di sistema del contenitore di Azure per l'area di lavoro.

### <a name="build-a-custom-base-image"></a>Creare un'immagine di base personalizzata

I passaggi descritti in questa sezione illustrano come creare un'immagine Docker personalizzata nel Registro di sistema del contenitore di Azure.The steps in this section walk-through creating a custom Docker image in your Azure Container Registry.

1. Creare un nuovo `Dockerfile`file di testo denominato , e utilizzare il testo seguente come contenuto:

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

2. Da una shell o da un prompt dei comandi, usare quanto segue per eseguire l'autenticazione nel Registro di sistema del contenitore di Azure.From a shell or command-prompt, use the following to authenticate to the Azure Container Registry. Sostituire `<registry_name>` il con il nome del registro contenitori in cui si desidera archiviare l'immagine:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Per caricare il Dockerfile e compilarlo, utilizzare il comando seguente. Sostituire `<registry_name>` con il nome del registro contenitori in cui si desidera archiviare l'immagine:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > In questo esempio, `:v1` all'immagine viene applicato un tag di . Se non viene fornito alcun `:latest` tag, viene applicato un tag di .

    Durante il processo di compilazione, le informazioni vengono trasmesse alla riga di comando. Se la compilazione ha esito positivo, viene visualizzato un messaggio simile al testo seguente:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Per altre informazioni sulla creazione di immagini con un Registro di sistema del contenitore di Azure, vedere [Creare ed eseguire un'immagine](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli) del contenitore usando le attività del Registro di sistema del contenitore di AzureFor more information on building images with an Azure Container Registry, see Build and run a container image using Azure Container Registry Tasks

Per altre informazioni sul caricamento di immagini esistenti in un Registro di sistema del contenitore di Azure, vedere Eseguire il push della prima immagine in un registro del [contenitore Docker privato.](/azure/container-registry/container-registry-get-started-docker-cli)

## <a name="use-a-custom-base-image"></a>Usare un'immagine di base personalizzataUse a custom base image

Per utilizzare un'immagine personalizzata, sono necessarie le seguenti informazioni:

* Il __nome dell'immagine__. Ad esempio, è il percorso di un'immagine Docker di base fornita da Microsoft.For example, `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` is the path to a basic Docker Image provided by Microsoft.

    > [!IMPORTANT]
    > Per le immagini personalizzate che hai creato, assicurati di includere tutti i tag utilizzati con l'immagine. Ad esempio, se l'immagine è stata `:v1`creata con un tag specifico, ad esempio . Se non avete utilizzato un tag specifico durante `:latest` la creazione dell'immagine, è stato applicato un tag di.

* Se l'immagine si trova in un __repository privato,__ sono necessarie le seguenti informazioni:

    * __L'indirizzo__del Registro di sistema . Ad esempio: `myregistry.azureecr.io`.
    * Nome __utente__ e __password__ dell'entità servizio con accesso in lettura al Registro di sistema.

    Se non si dispone di queste informazioni, rivolgersi all'amministratore del Registro di sistema del contenitore di Azure che contiene l'immagine.

### <a name="publicly-available-base-images"></a>Immagini di base disponibili pubblicamente

Microsoft fornisce diverse immagini docker in un repository accessibile pubblicamente, che può essere utilizzato con la procedura descritta in questa sezione:

| Immagine | Descrizione |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Immagine di base per Azure Machine LearningBasic image for Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | Contiene il runtime ONNX per l'inferenza della CPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | Contiene il runtime ONNX e la CUDA per GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | Contiene ONNX Runtime e TensorRT per GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm ` | Contiene ONNX Runtime e<sup> </sup> OpenVINO per Intel Vision Accelerator Design basati su VPUs Movidius<sup>TM</sup> MyriadX |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Contiene ONNX Runtime e<sup> </sup> OpenVINO per le chiavette USB Intel Movidius<sup>TM</sup> |

Per altre informazioni sulle immagini di base di ONNX Runtime, vedere la sezione dockerfile di ONNX Runtime nel repository GitHub.For more information about the ONNX Runtime base images see the [ONNX Runtime dockerfile section](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) in the GitHub repo.

> [!TIP]
> Poiché queste immagini sono disponibili pubblicamente, non è necessario fornire un indirizzo, un nome utente o una password quando vengono usate.

Per altre informazioni, vedere [Contenitori](https://github.com/Azure/AzureML-Containers)di Azure Machine Learning .

> [!TIP]
>__Se il modello viene eseguito il training in Azure Machine Learning Compute__, usando la versione __1.0.22 o successiva__ di Azure Machine Learning SDK, viene creata un'immagine durante il training. Per scoprire il nome di `run.properties["AzureML.DerivedImageName"]`questa immagine, utilizzare . Nell'esempio seguente viene illustrato come utilizzare questa immagine:The following example demonstrates how to use this image:
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Usare un'immagine con Azure Machine Learning SDKUse an image with the Azure Machine Learning SDK

Per usare un'immagine archiviata nel Registro di sistema del contenitore di **Azure per l'area di lavoro**o in un **registro contenitori accessibile pubblicamente,** impostare gli attributi [di ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) seguenti:

+ `docker.enabled=True`
+ `docker.base_image`: consente di impostare il Registro di sistema e il percorso dell'immagine.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

Per utilizzare un'immagine da un __registro di contenitori privati__ che non si trova nell'area di lavoro, è necessario utilizzare `docker.base_image_registry` per specificare l'indirizzo del repository e un nome utente e una password:

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

È necessario aggiungere i valori predefiniti di azureml con la versione >1.0.45 come dipendenza pip. Questo pacchetto contiene le funzionalità necessarie per ospitare il modello come servizio Web. È inoltre necessario impostare inferencing_stack_version proprietà nell'ambiente su "latest", verranno installati pacchetti apt specifici necessari per il servizio Web. 

Dopo aver definito l'ambiente, utilizzarlo con un [oggetto InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) per definire l'ambiente di inferenza in cui verranno eseguiti il modello e il servizio Web.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

A questo punto, è possibile continuare con la distribuzione. Ad esempio, il frammento di codice seguente distribuirebbe un servizio Web in locale usando la configurazione di inferenza e l'immagine personalizzata:For example, the following code snippet would deploy a web service locally using the inference configuration and custom image:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Per altre informazioni sulla distribuzione, vedere Distribuire modelli con Azure Machine Learning.For more information on deployment, see [Deploy models with Azure Machine Learning.](how-to-deploy-and-where.md)

Per ulteriori informazioni sulla personalizzazione dell'ambiente Python, vedere [Creare e gestire ambienti per la formazione e](how-to-use-environments.md)la distribuzione . 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Usare un'immagine con l'interfaccia della riga di comando di Machine LearningUse an image with the Machine Learning CLI

> [!IMPORTANT]
> Attualmente l'interfaccia della riga di comando di Machine Learning può usare le immagini dal Registro di sistema dei contenitori di Azure per l'area di lavoro o per i repository accessibili pubblicamente. Non può utilizzare immagini da registri privati autonomi.

Prima di distribuire un modello usando l'interfaccia della riga di comando di Machine Learning, creare un [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) che usa l'immagine personalizzata. Creare quindi un file di configurazione di inferenza che faccia riferimento all'ambiente. È inoltre possibile definire l'ambiente direttamente nel file di configurazione dell'inferenza. Il documento JSON seguente illustra come fare riferimento a un'immagine in un registro contenitori pubblici. In questo esempio, l'ambiente è definito inline:In this example, the environment is defined inline:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
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

Questo file viene `az ml model deploy` utilizzato con il comando. Il `--ic` parametro viene utilizzato per specificare il file di configurazione dell'inferenza.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Per altre informazioni sulla distribuzione di un modello tramite l'interfaccia della riga di comando di ML, vedere la sezione "registrazione, profilatura e distribuzione del modello" [dell'articolo Estensione dell'interfaccia](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) della riga di comando per Azure Machine Learning.For more information on deploying a model using the ML CLI, see the "model registration, profiling, and deployment" section of the CLI extension for Azure Machine Learning article.

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni su [Dove distribuire e su come](how-to-deploy-and-where.md).
* Informazioni su come eseguire il training e distribuire modelli di Machine Learning usando Le pipeline di Azure.Learn how to Train and [deploy machine learning models using Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops).
