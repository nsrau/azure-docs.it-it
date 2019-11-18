---
title: Creare ambienti ML riutilizzabili
titleSuffix: Azure Machine Learning
description: Creare e gestire gli ambienti per il training e la distribuzione dei modelli. Gestire i pacchetti Python e altre impostazioni per l'ambiente.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: b9b58c9b5f32d6ca714ac3ac940b91643fa8020c
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123557"
---
# <a name="reuse-environments-for-training--deployment-with-azure-machine-learning"></a>Riutilizza gli ambienti per il Training & la distribuzione con Azure Machine Learning.
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo viene illustrato come creare e gestire [ambienti](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) di Azure Machine Learning in modo da tenere traccia e riprodurre le dipendenze del software dei progetti man mano che si evolvono.

Gestione delle dipendenze software è un'attività comune per gli sviluppatori. Si desidera essere in grado di garantire che le compilazioni siano riproducibili senza una grande quantità di configurazione software manuale. Con le soluzioni per lo sviluppo locale, ad esempio PIP e conda, la classe degli ambienti Azure Machine Learning fornisce una soluzione per lo sviluppo cloud locale e distribuito.

Gli esempi in questo articolo illustrano come:

* Creare un ambiente e specificare le dipendenze del pacchetto
* Recuperare e aggiornare gli ambienti
* USA ambiente per il training
* USA ambiente per la distribuzione del servizio Web

## <a name="what-are-environments"></a>Che cosa sono gli ambienti

Gli ambienti specificano i pacchetti Python, le variabili di ambiente e le impostazioni software per gli script di training e di assegnazione dei punteggi e i tempi di esecuzione (Python, Spark o Docker). Sono entità gestite e con versione all'interno dell'area di lavoro Azure Machine Learning che consentono flussi di lavoro riproducibili, controllabili e portatili di Machine Learning tra destinazioni di calcolo diverse.

È possibile usare un oggetto ambiente nel calcolo locale per sviluppare lo script di training, riutilizzare lo stesso ambiente in Azure Machine Learning calcolo per il training dei modelli su larga scala e persino distribuire il modello con lo stesso ambiente.

Nell'esempio seguente viene illustrato che lo stesso oggetto ambiente può essere utilizzato sia nella configurazione di esecuzione per il training sia nella configurazione dell'inferenza e della distribuzione per le distribuzioni di servizi Web.

![Diagramma dell'ambiente nel flusso di lavoro di Machine Learning](./media/how-to-use-environments/ml-environment.png)

### <a name="types-of-environments"></a>Tipi di ambienti

Gli ambienti possono essere suddivisi in tre categorie: **curato**, **gestito dall'utente** e **gestito dal sistema**.

Per impostazione predefinita, gli ambienti curati sono forniti da Azure Machine Learning e sono disponibili nell'area di lavoro. Contengono raccolte di pacchetti e impostazioni Python che consentono di iniziare a usare diversi framework di machine learning. 

Per un ambiente gestito dall'utente è necessario configurare l'ambiente e installare ogni pacchetto richiesto dallo script di training nella destinazione di calcolo. Conda non controllerà l'ambiente e non istallerà alcun elemento. 

Gli ambienti gestiti dal sistema vengono usati quando si vuole [conda](https://conda.io/docs/) per gestire l'ambiente Python e le dipendenze di script. Per impostazione predefinita, il servizio presuppone questo tipo di ambiente, a causa dell'utilità delle destinazioni di calcolo remote che non possono essere configurate manualmente.

## <a name="prerequisites"></a>prerequisiti

* SDK Azure Machine Learning per Python [installato](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* [Area di lavoro Azure Machine Learning](how-to-manage-workspace.md).


## <a name="create-an-environment"></a>Creare un ambiente

Esistono diversi modi per creare un ambiente per gli esperimenti.

### <a name="use-curated-environment"></a>USA ambiente curato

È possibile selezionare uno degli ambienti curati per iniziare. 

* L'ambiente __AzureML-minimal__ contiene un set minimo di pacchetti per abilitare il rilevamento e il caricamento delle risorse. È possibile usarlo come punto di partenza per il proprio ambiente.

* L'ambiente __AzureML-tutorial__ contiene pacchetti di Data Science comuni, ad esempio Scikit-learn, Pandas e matplotlib e un set più ampio di pacchetti AzureML-SDK.

Gli ambienti curati sono supportati da immagini Docker memorizzate nella cache, riducendo il costo di preparazione dell'esecuzione.

Usare il metodo __Environment. Get__ per selezionare uno degli ambienti curati:

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
>  Non avviare il proprio nome di ambiente con il prefisso _AzureML_ . È riservata agli ambienti curati.

### <a name="instantiate-an-environment-object"></a>Creare un'istanza di un oggetto Environment

Per creare manualmente un ambiente, importare la classe Environment dall'SDK e creare un'istanza di un oggetto Environment con il codice seguente.

```python
from azureml.core import Environment
Environment(name="myenv")
```

### <a name="conda-and-pip-specification-files"></a>File delle specifiche conda e PIP

È anche possibile creare un ambiente da una specifica conda o da un file di requisiti PIP.
Usare il metodo [from_conda_specification ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) o il [from_pip_requirements ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) e includere il nome dell'ambiente e il percorso del file desiderato nell'argomento del metodo.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

#From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="existing-conda-environment"></a>Ambiente conda esistente

Se si dispone di un ambiente conda esistente nel computer locale, il servizio offre una soluzione per la creazione di un oggetto ambiente. In questo modo è possibile riutilizzare l'ambiente interattivo locale nelle esecuzioni remote.

Il codice seguente crea un oggetto ambiente fuori dall'ambiente conda esistente `mycondaenv` con il metodo [from_existing_conda_environment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) .

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="automatically-create-environments"></a>Crea automaticamente ambienti

Creare automaticamente un ambiente inviando un'esecuzione di training con il metodo Submit (). Quando si invia un'esecuzione di training, la compilazione del nuovo ambiente può richiedere diversi minuti, a seconda delle dimensioni delle dipendenze richieste. 

Se non si specifica un ambiente nella configurazione di esecuzione prima di inviare l'esecuzione, viene creato automaticamente un ambiente predefinito.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attaches training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Shows each step of run 
run.wait_for_completion(show_output=True)
```

Analogamente, se si usa un oggetto [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) per il training, è possibile inviare l'istanza di Estimator direttamente come esecuzione senza dover specificare un ambiente. L'oggetto `Estimator` incapsula già l'ambiente e la destinazione di calcolo.


## <a name="add-packages-to-an-environment"></a>Aggiungere pacchetti a un ambiente

Aggiungere i pacchetti in un ambiente con i file conda, PIP o della rotellina privata. Specificare ogni dipendenza del pacchetto usando la [classe CondaDependency](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)e aggiungerla al PythonSection dell'ambiente.

### <a name="conda-and-pip-packages"></a>Pacchetti conda e PIP

Se un pacchetto è disponibile in un repository conda del pacchetto, è consigliabile usare l'installazione di conda rispetto a PIP. Il motivo è che i pacchetti conda sono in genere dotati di file binari predefiniti che rendono l'installazione più affidabile.

Nell'esempio seguente vengono aggiunti `scikit-learn`, in particolare la versione 0.21.3 e `pillow` pacchetto all'ambiente, `myenv` rispettivamente con i metodi [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) e [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) .

```python
from azureml.core import Environment
from azureml.core.environment import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs scikit-learn version 0.21.3 conda package
conda_dep.add_conda_package("scikit-learn==0.21.3")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

### <a name="private-wheel-files"></a>File della rotellina privata

È possibile usare i file della rotellina di PIP privati prima di caricarli nell'archiviazione dell'area di lavoro usando il metodo statico [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) , quindi acquisire l'URL di archiviazione e passare l'URL al metodo `add_pip_package()`

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>Gestione degli ambienti

Consente di gestire gli ambienti per aggiornarli, monitorarli e riutilizzarli tra destinazioni di calcolo e altri utenti dell'area di lavoro.

### <a name="register-environments"></a>Registrare gli ambienti

L'ambiente viene registrato automaticamente con l'area di lavoro quando si invia un'esecuzione o si distribuisce un servizio Web. È anche possibile registrare manualmente l'ambiente usando il metodo [Register ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) . Questa operazione rende l'ambiente in un'entità rilevata e con versione nel cloud e può essere condivisa tra gli utenti dell'area di lavoro.

Il codice seguente registra l'ambiente, `myenv`, nell'area di lavoro, `ws`.

```python
myenv.register(workspace=ws)
```

Quando viene usato per la prima volta, nel training o nella distribuzione, l'ambiente viene registrato con l'area di lavoro, compilato e distribuito nella destinazione di calcolo. Gli ambienti vengono memorizzati nella cache dal servizio. Il riutilizzo di un ambiente memorizzato nella cache richiede molto meno tempo rispetto all'utilizzo di un nuovo servizio o di un nuovo aggiornamento.

### <a name="get-existing-environments"></a>Ottenere gli ambienti esistenti

La classe Environment offre metodi che consentono di recuperare gli ambienti esistenti nell'area di lavoro in base al nome, in base a un elenco o a una specifica esecuzione di training a scopo di risoluzione dei problemi o controllo, nonché la riproducibilità.

#### <a name="view-list-of-environments"></a>Visualizza l'elenco degli ambienti

Visualizzare gli ambienti nell'area di lavoro con [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-), quindi selezionarne uno da riutilizzare.

#### <a name="get-environment-by-name"></a>Ottenere l'ambiente in base al nome

È anche possibile ottenere un ambiente specifico in base al nome e alla versione.
Il codice seguente usa il metodo [Get ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) per recuperare la versione `1` dell'ambiente `myenv` nell'area di lavoro `ws`.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="training-run-specific-environment"></a>Ambiente specifico di esecuzione della formazione

Per ottenere l'ambiente usato per un'esecuzione specifica al termine del training, usare il metodo [get_environment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) nella classe Run.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Aggiornare un ambiente esistente

Se si apportano modifiche a un ambiente esistente, ad esempio si aggiunge un pacchetto python, viene creata una nuova versione di un ambiente quando si invia l'esecuzione, si distribuisce un modello o si registra manualmente l'ambiente. Il controllo delle versioni consente di visualizzare le modifiche apportate all'ambiente nel tempo.

Per aggiornare una versione del pacchetto python di un ambiente esistente, specificare il numero di versione esatto per il pacchetto. In caso contrario, il Azure Machine Learning riutilizzerà l'ambiente esistente con le versioni del pacchetto da quando è stato creato l'ambiente.

### <a name="debug-the-image-build"></a>Eseguire il debug della compilazione dell'immagine

Questo esempio usa il metodo [Build ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-) per creare manualmente un ambiente come immagine Docker e monitora i log di output dalla compilazione dell'immagine usando [wait_for_completion ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-). L'immagine compilata viene quindi visualizzata sotto l'area di lavoro Azure Container Registry, che risulta utile per il debug.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="docker-and-environments"></a>Docker e ambienti

 Il [DockerSection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) della classe Azure Machine Learning `Environments` consente di personalizzare e controllare in dettaglio il sistema operativo guest in cui viene eseguita l'esecuzione del training.

Quando si `enable` Docker, il servizio compila un'immagine Docker e crea un ambiente Python con le specifiche all'interno del contenitore docker. In questo modo si forniscono isolamento e riproducibilità aggiuntive per le esecuzioni di training.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Una volta compilata, l'immagine Docker viene visualizzata nella Container Registry di Azure associata all'area di lavoro, per impostazione predefinita.  Il nome del repository ha il formato *azureml/azureml_\<uuid\>* . La parte identificatore univoco (*UUID*) corrisponde a un hash calcolato dalla configurazione dell'ambiente. Ciò consente al servizio di determinare se un'immagine corrispondente all'ambiente specificato esiste già per il riutilizzo.

Inoltre, il servizio usa automaticamente una delle [Immagini di base](https://github.com/Azure/AzureML-Containers)basate su Ubuntu Linux e installa i pacchetti Python specificati. L'immagine di base dispone di versioni CPU e GPU. Azure Machine Learning rileva automaticamente la versione da utilizzare.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

> [!NOTE]
> Se si specifica `environment.python.user_managed_dependencies=False` quando si usa un'immagine Docker personalizzata, il servizio compilerà un ambiente conda all'interno dell'immagine ed eseguirà l'esecuzione in tale ambiente, invece di usare le librerie Python installate nell'immagine di base. Impostare il parametro su `True` per utilizzare i pacchetti installati.

## <a name="using-environments-for-training"></a>Uso di ambienti per il training

Per inviare un'esecuzione di training, è necessario combinare l'ambiente, [calcolare la destinazione](concept-compute-target.md) e il training di script Python in una configurazione di esecuzione. oggetto wrapper utilizzato per l'invio di esecuzioni.

Quando si invia un'esecuzione di training, la compilazione di un nuovo ambiente può richiedere diversi minuti, a seconda delle dimensioni delle dipendenze richieste. Gli ambienti vengono memorizzati nella cache dal servizio, quindi, finché la definizione dell'ambiente rimane invariata, il tempo di installazione completo viene eseguito una sola volta.

Nell'esempio di esecuzione dello script locale seguente viene illustrato come usare [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) come oggetto wrapper.

```python
from azureml.core import Environment, ScriptRunConfig, Experiment

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
> Per disabilitare la cronologia di esecuzione o eseguire snapshot, usare l'impostazione in `ScriptRunConfig.run_config.history`.

Se non si specifica l'ambiente nella configurazione di esecuzione, il servizio creerà automaticamente un ambiente predefinito quando si invia l'esecuzione.

### <a name="train-with-an-estimator"></a>Eseguire il training con un Estimator

Se si usa un [estimatore](how-to-train-ml-models.md) per il training, è sufficiente inviare direttamente l'istanza di Estimator, perché include già l'ambiente e la destinazione di calcolo.

Il codice seguente usa un estimatore per un training a nodo singolo eseguito in un calcolo remoto per un modello Scikit-learn e presuppone che un oggetto di destinazione di calcolo creato in precedenza, `compute_target` e un oggetto archivio dati, `ds`.

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

## <a name="using-environments-for-web-service-deployment"></a>Uso di ambienti per la distribuzione di servizi Web

È possibile usare gli ambienti quando si distribuisce il modello come servizio Web. Questo consente un flusso di lavoro riproducibile e connesso in cui è possibile eseguire il training, il test e la distribuzione del modello usando esattamente le stesse librerie nei calcoli di training e di inferenza.

Per distribuire un servizio Web, combinare l'ambiente, il calcolo inferenza, lo script di assegnazione dei punteggi e il modello registrato nell'oggetto di distribuzione, [Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-). Ulteriori informazioni sulla [distribuzione dei servizi Web](how-to-deploy-and-where.md).

In questo esempio si supponga di avere completato un'esecuzione di training e di voler distribuire il modello in un'istanza di contenitore di Azure. Quando si compila il servizio Web, i file di modello e di assegnazione dei punteggi vengono montati sull'immagine e l'Azure Machine Learning stack di inferenza viene aggiunto all'immagine.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>Notebook di esempio

Questo [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) si espande in base ai concetti e ai metodi illustrati in questo articolo.

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: eseguire il training di un modello](tutorial-train-models-with-aml.md) usa una destinazione di calcolo gestita per eseguire il training di un modello.
* Dopo aver creato un modello con training, consultare le informazioni su [come e dove distribuire i modelli](how-to-deploy-and-where.md).
* Visualizzare il riferimento all'SDK della [classe Environment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) .
