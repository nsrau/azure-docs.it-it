---
title: Come distribuire un modello usando un'immagine Docker personalizzata
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare un'immagine Docker personalizzata quando si distribuiscono i modelli di servizio di Azure Machine Learning. Quando si distribuisce un modello con training, viene creata un'immagine Docker per host l'immagine del server web e altri componenti necessari per eseguire il servizio. Mentre il servizio di Azure Machine Learning fornisce un'immagine predefinita per l'utente, è anche possibile usare la propria immagine.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/05/2019
ms.openlocfilehash: 02bc8835ddb163d81f389e13b21b88adca55cb2f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082625"
---
# <a name="deploy-a-model-using-a-custom-docker-image"></a>Distribuire un modello usando un'immagine Docker personalizzata

Informazioni su come usare un'immagine Docker personalizzata durante la distribuzione di modelli con Training con il servizio di Azure Machine Learning.

Quando si distribuisce un modello con Training in un servizio web o un dispositivo IoT Edge, viene creata un'immagine Docker. Questa immagine contiene il modello di ambiente conda e gli asset necessari per usare il modello. Contiene anche un server web per gestire le richieste in ingresso durante la distribuzione come un servizio web e i componenti necessari per lavorare con l'IoT Hub di Azure.

Servizio Azure Machine Learning fornisce un'immagine Docker predefinita in modo che non devi preoccuparti di crearne uno. È anche possibile usare un'immagine personalizzata che viene creata come una _immagine di base_. Un'immagine di base viene usata come punto di partenza quando viene creata un'immagine per una distribuzione. Fornisce il sistema operativo e i componenti sottostanti. Il processo di distribuzione aggiunge quindi i componenti aggiuntivi, ad esempio il modello di ambiente conda e altri asset, l'immagine prima di distribuirlo.

In genere, si crea un'immagine personalizzata quando si desidera controllare versioni del componente o di risparmiare tempo durante la distribuzione. Ad esempio, potrebbe voler standardizzare una versione specifica di Python, Conda o altro componente. È anche possibile installare il software richiesto dal modello, in cui il processo di installazione richiede molto tempo. Installazione del software durante la creazione dell'immagine di base significa che non è necessario installarlo per ogni distribuzione.

> [!IMPORTANT]
> Quando si distribuisce un modello, è possibile eseguire l'override di componenti di base, ad esempio server web o componenti di IoT Edge. Questi componenti forniscono un ambiente di lavoro noti che viene testato e supportato da Microsoft.

> [!WARNING]
> Microsoft potrebbe non essere in grado di risolvere i problemi causati da un'immagine personalizzata. Se si verificano problemi, potrebbe chiesto loro di usare l'immagine predefinita o una delle immagini forniti da Microsoft per vedere se il problema è specifico per l'immagine.

Questo documento è suddiviso in due sezioni:

* Creare un'immagine personalizzata: Fornisce informazioni per gli amministratori e DevOps sulla creazione di un'immagine personalizzata e la configurazione dell'autenticazione a un registro contenitori di Azure usando il comando di Azure e l'interfaccia della riga di comando di Machine Learning.
* Usare un'immagine personalizzata: Fornisce informazioni per i data Scientist e DevOps/MLOps sull'uso di immagini personalizzate quando si distribuisce un modello con training dal SDK per Python o della riga di comando di Machine Learning.

## <a name="prerequisites"></a>Prerequisiti

* Un gruppo di lavoro del servizio Azure Machine Learning. Per altre informazioni, vedere la [creare un'area di lavoro](setup-create-workspace.md) articolo.
* Azure Machine Learning SDK. Per altre informazioni, vedere la sezione SDK Python del [creare un'area di lavoro](setup-create-workspace.md#sdk) articolo.
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Un' [registro contenitori di Azure](/azure/container-registry) o altro registro Docker che è accessibile su internet.
* I passaggi in questo documento presuppongono che abbia familiarità con la creazione e utilizzo un' __configurazione di inferenza__ oggetto come parte della distribuzione del modello. Per altre informazioni, vedere la sezione "prepararsi per la distribuzione" del [dove eseguire la distribuzione e in che modo](how-to-deploy-and-where.md#prepare-to-deploy).

## <a name="create-a-custom-image"></a>Creare un'immagine personalizzata

Le informazioni contenute in questa sezione si presuppongono che si usa registro contenitori di Azure per archiviare le immagini Docker. Attenersi alla procedura seguente quando si pianifica di creare immagini personalizzate per il servizio di Azure Machine Learning:

* Si userà il registro contenitori di Azure creato per l'area di lavoro del servizio di Azure Machine Learning o un registro contenitori di Azure autonomo?

    Quando l'utilizzo di immagini archiviate nel __Registro di contenitori per l'area di lavoro__, non è necessario eseguire l'autenticazione al Registro di sistema. L'autenticazione è gestita dall'area di lavoro.

    > [!TIP]
    > Il Registro di contenitori per l'area di lavoro viene creato alla prima di eseguire il training o distribuire un modello usando l'area di lavoro. Se è stata creata una nuova area di lavoro, ma non sottoposto a training o creato un modello, non sarà presente alcun registro contenitori di Azure per l'area di lavoro.

    Per informazioni su come recuperare il nome del registro contenitori di Azure per l'area di lavoro, vedere la [nome del registro contenitori di Get](#getname) sezione di questo articolo.

    Quando l'utilizzo di immagini archiviate un __registro contenitori di autonomo__, sarà necessario configurare un'entità servizio che ha almeno l'accesso in lettura. È quindi fornire l'ID dell'entità servizio (nome utente) e la password per tutti gli utenti che usano immagini dal Registro di sistema. L'eccezione è se si rendono il registro contenitori pubblicamente accessibile.

    Per informazioni sulla creazione di un registro contenitori privato di Azure, vedere [creare un registro contenitori privato](/azure/container-registry/container-registery-get-started-azure-cli).

    Per informazioni sull'uso delle entità servizio con registro contenitori di Azure, vedere [autenticazione al registro contenitori di Azure con entità servizio](/azure/container-registry/container-registry-auth-service-principal).

* Informazioni di immagine e registro contenitori di Azure: Fornire il nome dell'immagine a tutti gli utenti che è necessario usarlo. Ad esempio, un'immagine denominata `myimage`, archiviato in un registro di sistema denominato `myregistry`, viene fatto riferimento come `myregistry.azurecr.io/myimage` quando si usa l'immagine per la distribuzione del modello

* Requisiti dell'immagine: Servizio Azure Machine Learning supporta solo le immagini Docker che forniscono i componenti software seguenti:

    * Ubuntu 16.04 o versione successiva.
    * 4\.5 conda. & o versione successiva.
    * Python 3.5. & o 3.6. #.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Ottenere informazioni del Registro di sistema del contenitore

In questa sezione, informazioni su come ottenere il nome del registro contenitori di Azure per l'area di lavoro del servizio di Azure Machine Learning.

> [!TIP]
> Il Registro di contenitori per l'area di lavoro viene creato alla prima di eseguire il training o distribuire un modello usando l'area di lavoro. Se è stata creata una nuova area di lavoro, ma non sottoposto a training o creato un modello, non sarà presente alcun registro contenitori di Azure per l'area di lavoro.

Se è già stato sottoposto a training o distribuiti i modelli usando il servizio di Azure Machine Learning, dell'area di lavoro è stato creato un registro contenitori. Per trovare il nome del registro contenitori, procedere come segue:

1. Aprire un prompt dei comandi o una nuova shell e usare il comando seguente per eseguire l'autenticazione alla sottoscrizione di Azure:

    ```azurecli-interactive
    az login
    ```

    Seguire le istruzioni per eseguire l'autenticazione alla sottoscrizione.

2. Usare il comando seguente per elencare il Registro di contenitori per l'area di lavoro. Sostituire `<myworkspace>` con il nome dell'area di lavoro del servizio Azure Machine Learning. Sostituire `<resourcegroup>` con il gruppo di risorse di Azure che contiene l'area di lavoro:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    Le informazioni restituite sono simili al testo seguente:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    Il `<registry_name>` valore è il nome del registro contenitori di Azure per l'area di lavoro.

### <a name="build-a-custom-image"></a>Creare un'immagine personalizzata

I passaggi descritti in questa procedura dettagliata sezione Creazione di un'immagine Docker personalizzata in Registro contenitori di Azure.

1. Creare un nuovo file di testo denominato `Dockerfile`e usare il testo seguente come contenuto:

    ```text
    FROM ubuntu:16.04

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-4.5.12-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y python=3.6 && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. Da una shell o il prompt dei comandi, usare quanto segue per l'autenticazione al registro contenitori di Azure. Sostituire il `<registry_name>` con il nome del registro contenitori di cui si desidera archiviare l'immagine in:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Per caricare il documento Dockerfile e compilare la soluzione, usare il comando seguente. Sostituire `<registry_name>` con il nome del registro contenitori di cui si desidera archiviare l'immagine in:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    Durante il processo di compilazione, viene inviato nel flusso di informazioni per eseguire il backup per la riga di comando. Se la compilazione ha esito positivo, viene visualizzato un messaggio simile al testo seguente:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Per altre informazioni sulla creazione di immagini con un registro contenitori di Azure, vedere [compilare ed eseguire un'immagine del contenitore usando le attività del Registro di sistema di contenitore di Azure](/docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli.md)

Per altre informazioni sul caricamento di immagini esistenti a un registro contenitori di Azure, vedere [Push la prima immagine in un registro contenitori Docker privati](/azure/container-registry/container-registry-get-started-docker-cli.md).

## <a name="use-a-custom-image"></a>Usare un'immagine personalizzata

Per usare un'immagine personalizzata, è necessario quanto segue:

* Il __nome dell'immagine__. Ad esempio, `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` è il percorso di un'immagine Docker di base forniti da Microsoft.
* Se l'immagine si trova in una __repository privato__, sono necessarie le informazioni seguenti:

    * Il Registro di sistema __indirizzo__. Ad esempio: `myregistry.azureecr.io`.
    * Un'entità servizio __nomeutente__ e __password__ che abbia accesso in lettura al Registro di sistema.

    Se non si hanno queste informazioni, rivolgersi all'amministratore per registro contenitori di Azure che contiene l'immagine.

### <a name="publicly-available-images"></a>Immagini disponibili pubblicamente

Microsoft fornisce diverse immagini docker in un repository accessibile pubblicamente, che può essere usato con i passaggi descritti in questa sezione:

| Image | Descrizione |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Immagine di base per il servizio di Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0` | Contiene il runtime ONNX. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7` | Contiene i componenti CUDA e ONNX runtime. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03` | Contiene TensorRT e ONNX runtime. |

> [!TIP]
> Poiché queste immagini sono disponibili pubblicamente, è necessario fornire un indirizzo, nome utente o password quando vengono utilizzati.

> [!IMPORTANT]
> Le immagini di Microsoft che usano CUDA o TensorRT devono essere utilizzate solo nei servizi di Microsoft Azure.

> [!TIP]
>__Se viene eseguito il training del modello nel calcolo di Azure Machine Learning__, usando __1.0.22 versione o versioni successive__ di Azure Machine Learning SDK, viene creata un'immagine durante il training. Per individuare il nome di questa immagine, usare `run.properties["AzureML.DerivedImageName"]`. Nell'esempio seguente viene illustrato come utilizzare questa immagine:
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Usare un'immagine con il SDK di Azure Machine Learning

Per usare un'immagine personalizzata, impostare il `base_image` proprietà del [oggetto di configurazione di inferenza](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) all'indirizzo dell'immagine:

```python
# use an image from a registry named 'myregistry'
inference_config.base_image = "myregistry.azurecr.io/myimage:v1"
```

Questo formato funziona per entrambe le immagini archiviate nel registro contenitori di Azure per i registri di contenitore e dell'area di lavoro che sono accessibili pubblicamente. Ad esempio, il codice seguente usa un'immagine predefinita fornita da Microsoft:

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

Usare un'immagine da un __registro contenitori privato__ che non è presente nell'area di lavoro, è necessario specificare l'indirizzo del repository e un nome utente e password:

```python
# Use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

### <a name="use-an-image-with-the-machine-learning-cli"></a>Usare un'immagine con l'interfaccia CLI di Machine Learning

> [!IMPORTANT]
> Attualmente la CLI di Machine Learning può usare le immagini dal registro contenitori di Azure per l'area di lavoro o il repository accessibili pubblicamente. Non può usare le immagini da registri privati autonomo.

Quando si distribuisce un modello tramite la CLI di Machine Learning, è fornire un file di configurazione di inferenza che fa riferimento all'immagine personalizzata. Il documento JSON seguente viene illustrato come fare riferimento a un'immagine in un registro contenitori pubblici:

```json
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "infenv.yml",
   "extraDockerfileSteps": null,
   "sourceDirectory": null,
   "enableGpu": false,
   "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
   "baseImageRegistry": "mcr.microsoft.com"
}
```

Questo file viene usato con il `az ml model deploy` comando. Il `--ic` parametro viene usato per specificare il file di configurazione di inferenza.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Per altre informazioni sulla distribuzione di un modello tramite la CLI di Machine Learning, vedere la sezione "registrazione del modello, profilatura e la distribuzione" del [estensione dell'interfaccia della riga per il servizio di Azure Machine Learning](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) articolo.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle [dove eseguire la distribuzione e in che modo](how-to-deploy-and-where.md).
* Informazioni su come [eseguire il training e distribuire modelli di machine learning con le pipeline di Azure](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops).