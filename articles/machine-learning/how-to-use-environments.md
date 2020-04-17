---
title: Creare ambienti ML riutilizzabiliCreate reusable ML environments
titleSuffix: Azure Machine Learning
description: Creare e gestire ambienti per la formazione e la distribuzione dei modelli. Gestire i pacchetti Python e altre impostazioni per l'ambiente.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: dc30318027962247f7504b734385d7642550ec87
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536353"
---
# <a name="reuse-environments-for-training-and-deployment-by-using-azure-machine-learning"></a>Riutilizzare gli ambienti per la formazione e la distribuzione tramite Azure Machine LearningReuse environments for training and deployment by using Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo viene illustrato come creare e gestire [ambienti](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)di Azure Machine Learning. Utilizzare gli ambienti per tenere traccia e riprodurre le dipendenze software dei progetti man mano che si evolvono.

La gestione delle dipendenze software è un'attività comune per gli sviluppatori. Si desidera assicurarsi che le compilazioni siano riproducibili senza un'estesa configurazione software manuale. La classe `Environment` Azure Machine Learning tiene conto delle soluzioni di sviluppo locale, ad esempio pip e Conda, e fornisce una soluzione per lo sviluppo di cloud locale e distribuito.

Gli esempi in questo articolo illustrano come:The examples in this article show how to:

* Creare un ambiente e specificare le dipendenze del pacchetto.
* Recuperare e aggiornare gli ambienti.
* Usare un ambiente per la formazione.
* Usare un ambiente per la distribuzione del servizio Web.Use an environment for web service deployment.

Per una panoramica generale del funzionamento degli ambienti in Azure Machine Learning, vedere Che cosa sono gli [ambienti ML?](concept-environments.md).

## <a name="prerequisites"></a>Prerequisiti

* [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* Un'area di lavoro di [Azure Machine LearningAn Azure Machine Learning workspace](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>Creare un ambiente

Le sezioni seguenti illustrano i diversi modi in cui è possibile creare un ambiente per gli esperimenti.

### <a name="use-a-curated-environment"></a>Utilizzare un ambiente curato

È possibile selezionare uno degli ambienti curati per iniziare con: 

* L'ambiente _AzureML-Minimal_ contiene un set minimo di pacchetti per abilitare il monitoraggio e il caricamento di asset di esecuzione. È possibile utilizzarlo come punto di partenza per il proprio ambiente.

* L'ambiente _AzureML-Tutorial_ contiene pacchetti di analisi scientifica dei dati comuni. Questi pacchetti includono Scikit-Learn, Pandas, Matplotlib e un set più ampio di pacchetti azureml-sdk.

Gli ambienti curati sono supportati da immagini Docker memorizzate nella cache. Questo sostegno riduce i costi di preparazione della corsa.

Utilizzare `Environment.get` il metodo per selezionare uno degli ambienti ambiti:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

È possibile elencare gli ambienti curati e i relativi pacchetti usando il codice seguente:You can list the curated environments and their packages by using the following code:

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Non iniziare il proprio nome di ambiente con il prefisso _AzureML._ Questo prefisso è riservato agli ambienti curati.

### <a name="instantiate-an-environment-object"></a>Creare un'istanza di un oggetto ambiente

Per creare manualmente un `Environment` ambiente, importare la classe dall'SDK. Utilizzare quindi il codice seguente per creare un'istanza di un oggetto ambiente.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>Utilizzare i file di specifica Conda e pip

È anche possibile creare un ambiente da una specifica Conda o da un file dei requisiti pip. Utilizzare [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) il metodo [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) o il metodo . Nell'argomento del metodo includere il nome dell'ambiente e il percorso del file desiderato.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="use-existing-conda-environments"></a>Utilizzare ambienti Conda esistenti

Se si dispone di un ambiente Conda esistente nel computer locale, è possibile utilizzare il servizio per creare un oggetto ambiente. Utilizzando questa strategia, è possibile riutilizzare l'ambiente interattivo locale in esecuzioni remote.

Il codice seguente crea un oggetto ambiente `mycondaenv`dall'ambiente Conda esistente. Utilizza il [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) metodo.

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>Creare ambienti automaticamente

Creare automaticamente un ambiente inviando un'esecuzione di training. Inviare l'esecuzione `submit()` utilizzando il metodo . Quando si invia una corsa di formazione, la creazione del nuovo ambiente può richiedere alcuni minuti. La durata della compilazione dipende dalle dimensioni delle dipendenze richieste. 

Se non si specifica un ambiente nella configurazione di esecuzione prima di inviare l'esecuzione, viene creato automaticamente un ambiente predefinito.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attach training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Show each step of run 
run.wait_for_completion(show_output=True)
```

Analogamente, se [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) si utilizza un oggetto per il training, è possibile inviare direttamente l'istanza dello stimatore come esecuzione senza specificare un ambiente. L'oggetto `Estimator` incapsula già l'ambiente e la destinazione di calcolo.

## <a name="add-packages-to-an-environment"></a>Aggiungere pacchetti a un ambienteAdd packages to an environment

Aggiungere pacchetti a un ambiente usando Conda, pip o file privati wheel. Specificare ogni dipendenza del [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) pacchetto utilizzando la classe . Aggiungerlo al `PythonSection`file .

### <a name="conda-and-pip-packages"></a>Pacchetti Conda e pip

Se un pacchetto è disponibile in un repository di pacchetti Conda, è consigliabile utilizzare l'installazione di Conda anziché l'installazione di pip. I pacchetti Conda in genere sono dotati di file binari predefiniti che rendono l'installazione più affidabile.

Nell'esempio seguente vengono aggiunte all'ambiente. Aggiunge la versione 1.17.0 di `numpy`. Aggiunge anche `pillow` il `myenv`pacchetto, . Nell'esempio [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) vengono utilizzati rispettivamente il metodo e il [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) metodo .

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs numpy version 1.17.0 conda package
conda_dep.add_conda_package("numpy==1.17.0")

# Installs pillow package
conda_dep.add_pip_package("pillow")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

>[!IMPORTANT]
> Se si usa la stessa definizione di ambiente per un'altra esecuzione, il servizio Azure Machine Learning riutilizza l'immagine memorizzata nella cache dell'ambiente. Se si crea un ambiente con una dipendenza ```numpy```del pacchetto non bloccata, ad esempio , tale ambiente continuerà a utilizzare la versione del pacchetto installata _al momento della creazione dell'ambiente._ Inoltre, qualsiasi ambiente futuro con definizione corrispondente continuerà a utilizzare la versione precedente. Per ulteriori informazioni, consultate [Compilazione, memorizzazione nella cache e riutilizzo dell'ambiente.](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)

### <a name="private-wheel-files"></a>File di ruote private

È possibile utilizzare i file pip wheel privati caricandoli prima nell'area di archiviazione. È possibile caricarli [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) utilizzando un metodo statico. Quindi si acquisisce l'URL di `add_pip_package()` archiviazione e si passa l'URL al metodo.

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>Gestire gli ambienti

Gestire gli ambienti in modo che sia possibile aggiornarli, monitorarli e riutilizzarli tra destinazioni di elaborazione e con altri utenti dell'area di lavoro.

### <a name="register-environments"></a>Ambienti di registrazione

L'ambiente viene registrato automaticamente nell'area di lavoro quando si invia un'esecuzione o si distribuisce un servizio Web. È inoltre possibile registrare manualmente [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) l'ambiente utilizzando il metodo . Questa operazione rende l'ambiente in un'entità che viene rilevata e con controllo delle versioni nel cloud. L'entità può essere condivisa tra gli utenti dell'area di lavoro.

Il codice seguente `myenv` registra l'ambiente nell'area `ws` di lavoro.

```python
myenv.register(workspace=ws)
```

Quando si usa l'ambiente per la prima volta durante la formazione o la distribuzione, questo viene registrato nell'area di lavoro. Quindi viene compilato e distribuito nella destinazione di calcolo. Il servizio memorizza nella cache gli ambienti. Il riutilizzo di un ambiente memorizzato nella cache richiede molto meno tempo rispetto all'utilizzo di un nuovo servizio o di un servizio aggiornato.

### <a name="get-existing-environments"></a>Ottenere ambienti esistentiGet existing environments

La `Environment` classe offre metodi che consentono di recuperare gli ambienti esistenti nell'area di lavoro. È possibile recuperare gli ambienti in base al nome, come elenco o in base a un'esecuzione di training specifica. Queste informazioni sono utili per la risoluzione dei problemi, il controllo e la riproducibilità.

#### <a name="view-a-list-of-environments"></a>Visualizzare un elenco di ambienti

Visualizzare gli ambienti nell'area [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) di lavoro utilizzando la classe . Selezionare quindi un ambiente da riutilizzare.

#### <a name="get-an-environment-by-name"></a>Ottenere un ambiente in base al nomeGet an environment by name

È anche possibile ottenere un ambiente specifico in base al nome e alla versione. Il codice seguente [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) usa il `1` metodo `myenv` per `ws` recuperare la versione dell'ambiente nell'area di lavoro.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Eseguire il training di un ambiente specifico della corsa

Per ottenere l'ambiente utilizzato per un'esecuzione specifica al [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) termine dell'allenamento, usare il metodo nella `Run` classe .

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Aggiornare un ambiente esistente

Si supponga di modificare un ambiente esistente, ad esempio aggiungendo un pacchetto Python. Una nuova versione dell'ambiente viene quindi creata quando si invia un'esecuzione, si distribuisce un modello o si registra manualmente l'ambiente. Il controllo delle versioni consente di visualizzare le modifiche dell'ambiente nel tempo.

Per aggiornare una versione del pacchetto Python in un ambiente esistente, specificare il numero di versione per tale pacchetto. Se non si usa il numero di versione esatto, Azure Machine Learning riutilizzerà l'ambiente esistente con le versioni del pacchetto originale.

### <a name="debug-the-image-build"></a>Eseguire il debug della build dell'immagineDebug the image build

Nell'esempio seguente [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace--image-build-compute-none-) viene utilizzato il metodo per creare manualmente un ambiente come immagine Docker.The following example uses the method to manually create an environment as a Docker image. Esegue il monitoraggio dei log di [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)output dalla compilazione dell'immagine utilizzando . L'immagine compilata viene quindi visualizzata nell'istanza del Registro di sistema del contenitore di Azure dell'area di lavoro. Queste informazioni sono utili per il debug.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>Abilita finestra mobile

 La [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) classe Azure `Environment` Machine Learning consente di personalizzare e controllare con fine il sistema operativo guest in cui si esegue il training.

Quando si abilita Docker, il servizio compila un'immagine Docker.When you enable Docker, the service builds a Docker image. Crea anche un ambiente Python che usa le specifiche all'interno di tale contenitore Docker.Also creates a Python environment that uses your specifications within that Docker container. Questa funzionalità fornisce ulteriore isolamento e riproducibilità per le esecuzioni di formazione.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Per impostazione predefinita, l'immagine Docker appena compilata viene visualizzata nel registro contenitori associato all'area di lavoro.  Il nome del repository ha il formato *azureml/azureml_\<uuid\>*. La parte del nome dell'identificatore univoco (*uuid*) corrisponde a un hash calcolato dalla configurazione dell'ambiente. Questa corrispondenza consente al servizio di determinare se un'immagine per l'ambiente specificato esiste già per il riutilizzo.

Inoltre, il servizio utilizza automaticamente una delle immagini di [base](https://github.com/Azure/AzureML-Containers)basate su Linux di Ubuntu. Installa i pacchetti Python specificati. L'immagine di base ha versioni CPU e GPU. Azure Machine Learning rileva automaticamente la versione da usare.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

È inoltre possibile specificare un Dockerfile personalizzato. È più semplice iniziare da una delle immagini di ```FROM``` base di Azure Machine Learning usando il comando Docker e quindi aggiungere passaggi personalizzati. Utilizzare questo approccio se è necessario installare pacchetti non Python come dipendenze.

```python
# Specify docker steps as a string. Alternatively, load the string from a file.
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
```

### <a name="use-user-managed-dependencies"></a>Usare le dipendenze gestite dall'utenteUse user-managed dependencies

In alcune situazioni, l'immagine di base personalizzata potrebbe già contenere un ambiente Python con pacchetti che si desidera utilizzare.

Per impostazione predefinita, il servizio Azure Machine Learning creerà un ambiente Conda con dipendenze specificate ed eseguirà l'esecuzione in tale ambiente anziché usare le librerie Python installate nell'immagine di base. 

Per utilizzare i propri pacchetti `Environment.python.user_managed_dependencies = True`installati, impostare il parametro . Assicurarsi che l'immagine di base contenga un interprete Python e che disponga dei pacchetti necessari per lo script di training.

Ad esempio, per eseguire in un ambiente Miniconda di base in cui è installato il pacchetto NumPy, specificare innanzitutto un Dockerfile con un passaggio per installare il pacchetto. Impostare quindi le dipendenze `True`gestite dall'utente su . 

È inoltre possibile specificare un percorso a un interprete Python specifico all'interno dell'immagine, impostando la `Environment.python.interpreter_path` variabile.

```python
dockerfile = """
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN conda install numpy
"""

myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
myenv.python.user_managed_dependencies=True
myenv.python.interpreter_path = "/opt/miniconda/bin/python"
```

## <a name="use-environments-for-training"></a>Utilizzare gli ambienti per la formazione

Per inviare un'esecuzione di training, è necessario combinare l'ambiente, la destinazione di [calcolo](concept-compute-target.md)e lo script Python di training in una configurazione di esecuzione. Questa configurazione è un oggetto wrapper utilizzato per l'invio di esecuzioni.

Quando si invia una corsa di formazione, la creazione di un nuovo ambiente può richiedere alcuni minuti. La durata dipende dalle dimensioni delle dipendenze richieste. Gli ambienti vengono memorizzati nella cache dal servizio. Pertanto, finché la definizione dell'ambiente rimane invariata, il tempo di installazione completo viene eseguito una sola volta.

Nell'esempio di esecuzione dello script [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) locale seguente viene illustrato dove si utilizzerebbe l'oggetto wrapper.

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.environment import Environment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.run_config.target = "local"

# Attach environment to run config
runconfig.run_config.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> Per disabilitare la cronologia di esecuzione o `ScriptRunConfig.run_config.history`gli snapshot, utilizzare l'impostazione in .

Se non si specifica l'ambiente nella configurazione di esecuzione, il servizio crea un ambiente predefinito quando si invia l'esecuzione.

### <a name="use-an-estimator-for-training"></a>Utilizzare uno stimatore per la formazione

Se si utilizza uno [stimatore](how-to-train-ml-models.md) per la formazione, è possibile inviare direttamente l'istanza dello stimatore. Incapsula già l'ambiente e la destinazione di calcolo.

Il codice seguente usa uno stimatore per un'esecuzione di training a nodo singolo. Viene eseguito su un `scikit-learn` calcolo remoto per un modello. Si presuppone che in precedenza sia `compute_target`stato creato un `ds`oggetto di destinazione di calcolo, e un oggetto datastore, .

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])

# Submit the run 
run = experiment.submit(sk_est)
```

## <a name="use-environments-for-web-service-deployment"></a>Usare gli ambienti per la distribuzione del servizio WebUse environments for web service deployment

È possibile usare gli ambienti quando si distribuisce il modello come servizio Web.You can use environments when you deploy your model as a web service. Questa funzionalità consente un flusso di lavoro riproducibile e connesso. In questo flusso di lavoro è possibile eseguire il training, testare e distribuire il modello usando le stesse librerie sia nel training compute che nel calcolo dell'inferenza.

Per distribuire un servizio Web, combinare l'ambiente, il calcolo dell'inferenza, lo script di assegnazione del punteggio e il modello registrato nell'oggetto di distribuzione, [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-). Per ulteriori informazioni, vedere [Come e dove distribuire](how-to-deploy-and-where.md)i modelli .

In questo esempio, si supponga di aver completato un'esecuzione di training. A questo punto si vuole distribuire il modello in Istanze del contenitore di Azure.Now you want to deploy that model to Azure Container Instances. Quando si compila il servizio Web, il modello e i file di punteggio vengono montati nell'immagine e lo stack di inferenza di Azure Machine Learning viene aggiunto all'immagine.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference, & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>Notebook di esempio

Questo [blocco appunti di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) si espande sui concetti e sui metodi illustrati in questo articolo.

[Distribuire un modello usando un'immagine di base Docker personalizzata](how-to-deploy-custom-docker-image.md) viene illustrato come distribuire un modello utilizzando un'immagine di base Docker personalizzata.

In [questo blocco appunti di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) viene illustrato come distribuire un modello Spark come servizio Web.This example notebook demonstrates how to deploy a Spark model as a web service.

## <a name="create-and-manage-environments-with-the-cli"></a>Creare e gestire ambienti con l'interfaccia della riga di comando

[L'interfaccia della riga](reference-azure-machine-learning-cli.md) di comando di Azure Machine Learning rispecchia la maggior parte delle funzionalità di Python SDK. È possibile utilizzarlo per creare e gestire gli ambienti. I comandi descritti in questa sezione illustrano le funzionalità di base.

Il comando seguente esegue lo scaffolding dei file per una definizione di ambiente predefinita nella directory specificata. Questi file sono file JSON. Funzionano come la classe corrispondente nell'SDK. È possibile utilizzare i file per creare nuovi ambienti con impostazioni personalizzate. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

Eseguire il comando seguente per registrare un ambiente da una directory specificata.

```azurecli-interactive
az ml environment register -d myenvdir
```

Eseguire il comando seguente per elencare tutti gli ambienti registrati.

```azurecli-interactive
az ml environment list
```

Scaricare un ambiente registrato utilizzando il comando seguente.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Passaggi successivi

* Per utilizzare una destinazione di calcolo gestita per eseguire il training di un modello, vedere [Esercitazione: eseguire il training di un modello.](tutorial-train-models-with-aml.md)
* Dopo aver creato un modello di training, vedere [come e dove distribuire](how-to-deploy-and-where.md)i modelli .
* Visualizzare il [ `Environment` riferimento SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)della classe .
* Per ulteriori informazioni sui concetti e sui metodi descritti in questo articolo, vedere il [blocco appunti](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments)di esempio .
