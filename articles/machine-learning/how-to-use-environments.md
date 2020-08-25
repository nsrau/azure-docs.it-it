---
title: Usare gli ambienti software
titleSuffix: Azure Machine Learning
description: Creare e gestire gli ambienti per il training e la distribuzione dei modelli. Gestire i pacchetti Python e altre impostazioni per l'ambiente.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 07/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: a08575ac118f38361d82198ccc86a09f7b2558d6
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783740"
---
# <a name="create--use-software-environments-in-azure-machine-learning"></a>Creare & usare gli ambienti software in Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come creare e gestire [ambienti](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)Azure Machine Learning. Usare gli ambienti per tenere traccia e riprodurre le dipendenze del software dei progetti man mano che si evolvono.

Gestione delle dipendenze software è un'attività comune per gli sviluppatori. Si vuole garantire che le compilazioni siano riproducibili senza una configurazione manuale completa del software. Il Azure Machine Learning `Environment` classe rappresenta le soluzioni di sviluppo locale come PIP e conda e lo sviluppo cloud distribuito tramite le funzionalità di Docker.

Gli esempi in questo articolo illustrano come:

* Creare un ambiente e specificare le dipendenze del pacchetto.
* Recuperare e aggiornare gli ambienti.
* Usare un ambiente per il training.
* Usare un ambiente per la distribuzione del servizio Web.

Per una panoramica di alto livello del funzionamento degli ambienti in Azure Machine Learning, vedere [che cosa sono gli ambienti ml?](concept-environments.md) Per informazioni sulla configurazione degli ambienti di sviluppo, vedere [qui](how-to-configure-environment.md).

## <a name="prerequisites"></a>Prerequisiti

* [SDK Azure Machine Learning per Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Area di lavoro Azure Machine Learning](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>Creare un ambiente

Le sezioni seguenti illustrano i diversi modi in cui è possibile creare un ambiente per gli esperimenti.

### <a name="use-a-curated-environment"></a>Usare un ambiente curato

Gli ambienti curati contengono raccolte di pacchetti Python e sono disponibili nell'area di lavoro per impostazione predefinita. Questi ambienti sono supportati da immagini Docker memorizzate nella cache che riduce il costo di preparazione dell'esecuzione. Per iniziare, è possibile selezionare uno di questi ambienti con la cura più diffusi: 

* L'ambiente _AzureML-minimal_ contiene un set minimo di pacchetti per abilitare il rilevamento e il caricamento delle risorse. È possibile usarlo come punto di partenza per il proprio ambiente.

* L'ambiente _AzureML-tutorial_ contiene pacchetti di Data Science comuni. Questi pacchetti includono Scikit-learn, Pandas, matplotlib e un set più ampio di pacchetti azureml-SDK.

Per un elenco degli ambienti curati, vedere l' [articolo ambienti curati](resource-curated-environments.md).

Usare il `Environment.get` metodo per selezionare uno degli ambienti curati:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

È possibile elencare gli ambienti curati e i relativi pacchetti usando il codice seguente:

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Non avviare il proprio nome di ambiente con il prefisso _AzureML_ . Questo prefisso è riservato per gli ambienti curati.


### <a name="instantiate-an-environment-object"></a>Creare un'istanza di un oggetto Environment

Per creare manualmente un ambiente, importare la `Environment` classe da SDK. Usare quindi il codice seguente per creare un'istanza di un oggetto Environment.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

Se si definisce un ambiente personalizzato, è necessario elencare `azureml-defaults` con version >= 1.0.45 come dipendenza PIP. Questo pacchetto contiene la funzionalità necessaria per ospitare il modello come servizio Web.

### <a name="use-conda-and-pip-specification-files"></a>Usare i file di specifica conda e PIP

È possibile creare un ambiente da una specifica conda o da un file di requisiti PIP. Usare il [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) metodo o il metodo [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) . Nell'argomento del metodo includere il nome dell'ambiente e il percorso del file desiderato. 

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")                                          
```

### <a name="use-existing-environments"></a>USA ambienti esistenti

Se si dispone di un ambiente conda esistente nel computer locale, è possibile usare il servizio per creare un oggetto ambiente. Utilizzando questa strategia, è possibile riutilizzare l'ambiente interattivo locale in esecuzioni remote.

Il codice seguente crea un oggetto Environment dall'ambiente conda esistente `mycondaenv` . Usa il [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) metodo.

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

Una definizione di ambiente può essere salvata in una directory in un formato facilmente modificabile con il [`save_to_directory()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#save-to-directory-path--overwrite-false-) metodo. Una volta modificato, è possibile creare un'istanza di un nuovo ambiente caricando file dalla directory.

```python
myenv = Environment.save_to_directory(path = "path-to-destination-directory", overwrite = False)
# modify the environment definition
newenv = Environment.load_from_directory(path = "path-to-source-directory")
```

### <a name="create-environments-automatically"></a>Crea ambienti automaticamente

Creare automaticamente un ambiente inviando un'esecuzione di training. Inviare l'esecuzione usando il `submit()` metodo. Quando si invia un'esecuzione di training, la compilazione del nuovo ambiente può richiedere diversi minuti. La durata della compilazione dipende dalle dimensioni delle dipendenze richieste. 

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

Analogamente, se si usa un [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) oggetto per il training, è possibile inviare direttamente l'istanza di Estimator come esecuzione senza specificare un ambiente. L' `Estimator` oggetto incapsula già l'ambiente e la destinazione di calcolo.

## <a name="add-packages-to-an-environment"></a>Aggiungere pacchetti a un ambiente

Aggiungere i pacchetti a un ambiente usando Conda, pip o file wheel privati. Specificare ogni dipendenza del pacchetto utilizzando la [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) classe. Aggiungerlo all'ambiente `PythonSection` .

### <a name="conda-and-pip-packages"></a>Pacchetti conda e PIP

Se un pacchetto è disponibile in un repository conda, è consigliabile usare l'installazione conda anziché l'installazione PIP. I pacchetti conda vengono in genere forniti con file binari predefiniti che rendono l'installazione più affidabile.

L'esempio seguente esegue aggiunte nell'ambiente. Aggiunge la versione 1.17.0 di `numpy`. Viene inoltre aggiunto il `pillow` pacchetto, `myenv` . Vengono usati rispettivamente il metodo [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) e il metodo [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-).

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

È anche possibile aggiungere variabili di ambiente all'ambiente. Che diventano quindi disponibili usando OS. Environ. Get nello script di training.

```python
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

>[!IMPORTANT]
> Se si usa la stessa definizione di ambiente per un'altra esecuzione, il servizio Azure Machine Learning riutilizza l'immagine memorizzata nella cache dell'ambiente. Se si crea un ambiente con una dipendenza del pacchetto sbloccata, ad esempio ```numpy``` , tale ambiente continuerà a utilizzare la versione del pacchetto installata _al momento della creazione dell'ambiente_. Inoltre, qualsiasi ambiente futuro con definizione di corrispondenza continuerà a utilizzare la versione precedente. Per altre informazioni, vedere [Building, Caching e riuso dell'ambiente](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse).

### <a name="private-python-packages"></a>Pacchetti Python privati

Per usare i pacchetti Python in modo privato e sicuro senza esporli alla rete Internet pubblica, vedere l'articolo [come usare i pacchetti Python privati](how-to-use-private-python-packages.md).

## <a name="manage-environments"></a>Gestire gli ambienti

Gestire gli ambienti in modo che sia possibile aggiornarli, monitorarli e riutilizzarli tra destinazioni di calcolo e altri utenti dell'area di lavoro.

### <a name="register-environments"></a>Registrare gli ambienti

L'ambiente viene registrato automaticamente con l'area di lavoro quando si invia un'esecuzione o si distribuisce un servizio Web. È anche possibile registrare manualmente l'ambiente usando il [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) metodo. Questa operazione rende l'ambiente in un'entità rilevata e con versione nel cloud. L'entità può essere condivisa tra gli utenti dell'area di lavoro.

Il codice seguente registra l' `myenv` ambiente nell' `ws` area di lavoro.

```python
myenv.register(workspace=ws)
```

Quando si usa l'ambiente per la prima volta nel training o nella distribuzione, questo viene registrato con l'area di lavoro. Viene quindi compilato e distribuito nella destinazione di calcolo. Il servizio memorizza nella cache gli ambienti. Il riutilizzo di un ambiente memorizzato nella cache richiede molto meno tempo rispetto all'utilizzo di un nuovo servizio o di un nuovo aggiornamento.

### <a name="get-existing-environments"></a>Ottenere gli ambienti esistenti

La `Environment` classe offre metodi che consentono di recuperare gli ambienti esistenti nell'area di lavoro. È possibile recuperare gli ambienti in base al nome, come elenco o in base a una specifica esecuzione di training. Queste informazioni sono utili per la risoluzione dei problemi, il controllo e la riproducibilità.

#### <a name="view-a-list-of-environments"></a>Visualizzare un elenco di ambienti

Visualizzare gli ambienti nell'area di lavoro usando la [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) classe. Selezionare quindi un ambiente da riutilizzare.

#### <a name="get-an-environment-by-name"></a>Ottenere un ambiente in base al nome

È anche possibile ottenere un ambiente specifico in base al nome e alla versione. Il codice seguente usa il [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) metodo per recuperare la versione `1` dell' `myenv` ambiente nell' `ws` area di lavoro.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Eseguire il training di un ambiente di esecuzione specifico

Per ottenere l'ambiente utilizzato per un'esecuzione specifica al termine del training, utilizzare il [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) metodo nella `Run` classe.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Aggiornare un ambiente esistente

Si consiglia di modificare un ambiente esistente, ad esempio aggiungendo un pacchetto python. La compilazione di una nuova versione dell'ambiente verrà quindi creata quando si invia un'esecuzione, si distribuisce un modello o si registra manualmente l'ambiente. Il controllo delle versioni consente di visualizzare le modifiche dell'ambiente nel tempo. 

Per aggiornare una versione del pacchetto python in un ambiente esistente, specificare il numero di versione per il pacchetto. Se non si usa il numero di versione esatto, Azure Machine Learning riutilizzerà l'ambiente esistente con le versioni originali del pacchetto.

### <a name="debug-the-image-build"></a>Eseguire il debug della compilazione dell'immagine

Nell'esempio seguente viene usato il [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace--image-build-compute-none-) metodo per creare manualmente un ambiente come immagine docker. Monitora i log di output dalla compilazione dell'immagine usando [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) . L'immagine compilata viene quindi visualizzata nell'istanza di Azure Container Registry dell'area di lavoro. Queste informazioni sono utili per il debug.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

È utile per prima cosa creare immagini localmente usando il [`build_local()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-local-workspace--platform-none----kwargs-) metodo. L'impostazione del parametro facoltativo `pushImageToWorkspaceAcr = True` comporterà il push dell'immagine risultante nel registro contenitori dell'area di lavoro di Azure ml. 

> [!WARNING]
>  La modifica dell'ordine delle dipendenze o dei canali in un ambiente determinerà un nuovo ambiente e richiederà una nuova compilazione dell'immagine.

## <a name="enable-docker"></a>Abilita Docker

Il contenitore Docker fornisce un modo efficiente per incapsulare le dipendenze. Quando si Abilita Docker, Azure ML compila un'immagine Docker e crea un ambiente Python all'interno del contenitore, in base alle specifiche. Le immagini Docker vengono memorizzate nella cache e riutilizzate: la prima esecuzione in un nuovo ambiente richiede in genere più tempo durante la compilazione dell'immagine.

La [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) della classe Azure Machine Learning `Environment` consente di personalizzare e controllare in modo accurato il sistema operativo guest in cui si esegue il training. La `arguments` variabile può essere usata per specificare argomenti aggiuntivi da passare al comando Docker Run.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Per impostazione predefinita, l'immagine Docker appena creata viene visualizzata nel registro contenitori associato all'area di lavoro.  Il nome del repository ha il formato *azureml/ \<uuid\> azureml_*. La parte dell'identificatore univoco (*UUID*) del nome corrisponde a un hash calcolato dalla configurazione dell'ambiente. Questa corrispondenza consente al servizio di determinare se un'immagine per l'ambiente specificato esiste già per il riutilizzo.

### <a name="use-a-custom-docker-image-or-dockerfile"></a>Usare un'immagine Docker personalizzata o Dockerfile 
Il servizio usa automaticamente una delle [Immagini di base](https://github.com/Azure/AzureML-Containers)basate su Ubuntu Linux. Installa i pacchetti Python specificati. L'immagine di base ha versioni CPU e GPU. Azure Machine Learning rileva automaticamente la versione da utilizzare. È anche possibile usare un'immagine di [base Docker personalizzata](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image#create-a-custom-base-image).

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

>[!IMPORTANT]
> Azure Machine Learning supporta solo le immagini Docker che forniscono il software seguente:
> * Ubuntu 16,04 o versione successiva.
> * Conda 4.5. # o successiva.
> * Python 3.5. #, 3.6. # o 3.7. #.

È anche possibile specificare un Dockerfile personalizzato. È più semplice iniziare da una delle immagini di base Azure Machine Learning usando ```FROM``` il comando Docker e quindi aggiungere passaggi personalizzati. Usare questo approccio se è necessario installare pacchetti non Python come dipendenze. Ricordarsi di impostare l'immagine di base su None.

```python
# Specify docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = "./Dockerfile"
```

### <a name="specify-your-own-python-interpreter"></a>Specificare un interprete Python personalizzato

In alcune situazioni, l'immagine di base personalizzata potrebbe già contenere un ambiente Python con i pacchetti che si vuole usare.

Per usare i pacchetti installati e disabilitare conda, impostare il parametro `Environment.python.user_managed_dependencies = True` . Verificare che l'immagine di base contenga un interprete Python e che i pacchetti siano necessari per lo script di training.

Ad esempio, per eseguire in un ambiente Miniconda di base in cui è installato il pacchetto NumPy, specificare prima di tutto un Dockerfile con un passaggio per installare il pacchetto. Impostare quindi le dipendenze gestite dall'utente su `True` . 

È anche possibile specificare un percorso di un interprete Python specifico all'interno dell'immagine, impostando la `Environment.python.interpreter_path` variabile.

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

> [!WARNING]
> Se si installano alcune dipendenze Python nell'immagine Docker e si dimentica di impostare user_managed_dependencies = true, tali pacchetti non sono presenti nell'ambiente di esecuzione, causando così errori di Runtime. Per impostazione predefinita, Azure ML compilerà un ambiente conda con le dipendenze specificate e eseguirà l'esecuzione in tale ambiente invece di usare le librerie Python installate nell'immagine di base.

### <a name="retrieve-image-details"></a>Recuperare i dettagli dell'immagine

Per un ambiente registrato, è possibile recuperare i dettagli dell'immagine usando il codice seguente, dove `details` è un'istanza di [DockerImageDetails](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockerimagedetails?view=azure-ml-py) (AzureML Python SDK >= 1,11) e fornisce tutte le informazioni sull'immagine dell'ambiente, ad esempio dockerfile, Registry e image name.

```python
details = environment.get_image_details()
```

## <a name="use-environments-for-training"></a>Usare gli ambienti per il training

Per inviare un'esecuzione di training, è necessario combinare l'ambiente, la [destinazione di calcolo](concept-compute-target.md) e lo script Python di training in una configurazione di esecuzione. Questa configurazione è un oggetto wrapper usato per l'invio di esecuzioni.

Quando si invia un'esecuzione di training, lo sviluppo  di un nuovo ambiente può richiedere diversi minuti. La durata dipende dalle dimensioni delle dipendenze richieste. Gli ambienti vengono memorizzati nella cache dal servizio. Finché la definizione dell'ambiente rimane invariata, la configurazione completa verrà eseguita una sola volta.

Nell'esempio di esecuzione dello script locale seguente viene illustrato [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) come utilizzare come oggetto wrapper.

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
> Per disabilitare la cronologia di esecuzione o eseguire snapshot, usare l'impostazione in `ScriptRunConfig.run_config.history` .

Se non si specifica l'ambiente nella configurazione di esecuzione, il servizio crea un ambiente predefinito quando si invia l'esecuzione.

### <a name="use-an-estimator-for-training"></a>Usa un estimatore per il training

Se si usa un [estimatore](how-to-train-ml-models.md) per il training, è possibile inviare direttamente l'istanza di Estimator. Include già l'ambiente e la destinazione di calcolo.

Nel codice seguente viene utilizzato un estimatore per un'esecuzione di training a nodo singolo. Viene eseguito in un calcolo remoto per un `scikit-learn` modello. Si presuppone che in precedenza siano stati creati un oggetto di destinazione di calcolo, `compute_target` , e un oggetto archivio dati, `ds` .

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

## <a name="use-environments-for-web-service-deployment"></a>Usare gli ambienti per la distribuzione del servizio Web

È possibile usare gli ambienti quando si distribuisce il modello come servizio Web. Questa funzionalità consente un flusso di lavoro riproducibile e connesso. In questo flusso di lavoro è possibile eseguire il training, il test e la distribuzione del modello usando le stesse librerie sia nel calcolo di training che nel calcolo di inferenza.

Per distribuire un servizio Web, combinare l'ambiente, il calcolo di inferenza, lo script di assegnazione di punteggi e il modello registrato nell'oggetto di distribuzione, [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-). Per ulteriori informazioni, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

In questo esempio si presuppone che sia stata completata un'esecuzione di training. A questo punto si vuole distribuire il modello in istanze di contenitore di Azure. Quando si compila il servizio Web, i file di modello e di assegnazione dei punteggi vengono montati sull'immagine e lo stack di inferenza Azure Machine Learning viene aggiunto all'immagine.

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

## <a name="notebooks"></a>Notebook

Questo [articolo](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks#add-new-kernels) fornisce informazioni su come installare un ambiente conda come un kernel in un notebook.

[Distribuire un modello usando un'immagine di base Docker personalizzata](how-to-deploy-custom-docker-image.md) illustra come distribuire un modello usando un'immagine di base Docker personalizzata.

Questo [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) illustra come distribuire un modello Spark come servizio Web.

## <a name="create-and-manage-environments-with-the-cli"></a>Creare e gestire gli ambienti con l'interfaccia della riga di comando

L' [interfaccia](reference-azure-machine-learning-cli.md) della riga di comando di Azure Machine Learning rispecchia la maggior parte delle funzionalità di Python SDK. È possibile usarlo per creare e gestire gli ambienti. I comandi illustrati in questa sezione illustrano le funzionalità di base.

Il comando seguente imposta come impalcature i file per una definizione di ambiente predefinita nella directory specificata. Questi file sono file JSON. Funzionano come la classe corrispondente nell'SDK. È possibile utilizzare i file per creare nuovi ambienti con impostazioni personalizzate. 

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

Scaricare un ambiente registrato usando il comando seguente.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Passaggi successivi

* Per usare una destinazione di calcolo gestita per eseguire il training di un modello, vedere [esercitazione: eseguire il training di un modello](tutorial-train-models-with-aml.md).
* Quando si dispone di un modello [sottoposto a training, scoprire come e dove distribuire i modelli](how-to-deploy-and-where.md).
* Visualizzare il [ `Environment` riferimento all'SDK di classe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
