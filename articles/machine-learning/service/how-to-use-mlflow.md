---
title: Usare MLflow con
titleSuffix: Azure Machine Learning service
description: Configurare MLflow con Azure Machine Learning per registrare le metriche & artefatti e distribuire i modelli da databricks, dall'ambiente locale o dall'ambiente di macchina virtuale.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 08/07/2019
ms.custom: seodec18
ms.openlocfilehash: c7bed307373289c6a6f76117fa07ee2ee3242bfc
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860451"
---
# <a name="track-metrics-and-deploy-models-with-mlflow-and-azure-machine-learning-service-preview"></a>Rilevare le metriche e distribuire i modelli con MLflow e il servizio Azure Machine Learning (anteprima)

Questo articolo illustra come abilitare l'URI di rilevamento e l'API di registrazione di MLflow, collettivamente nota come [MLflow Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), con Azure Machine Learning Service. In questo modo è possibile:

+ Tenere traccia e registrare le metriche e gli artefatti dell'esperimento nell' [area di lavoro del servizio Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspaces). Se si usa già il rilevamento MLflow per gli esperimenti, l'area di lavoro offre una posizione centralizzata, sicura e scalabile per archiviare le metriche e i modelli di training.

+ Distribuisci gli esperimenti MLflow come servizio Web Azure Machine Learning. Distribuendo come servizio Web, è possibile applicare le funzionalità di monitoraggio Azure Machine Learning e rilevamento della tendenza dei dati ai modelli di produzione. 

[MLflow](https://www.mlflow.org) è una libreria open source per la gestione del ciclo di vita degli esperimenti di machine learning. Il rilevamento MLFlow è un componente di MLflow che registra e tiene traccia delle metriche di esecuzione del training e degli elementi del modello, indipendentemente dall'ambiente dell'esperimento, in locale, in una macchina virtuale, in un cluster di calcolo remoto, anche in Azure Databricks.

Il diagramma seguente illustra che con il rilevamento del MLflow, è possibile eseguire qualsiasi esperimento, sia che si tratti di una destinazione di calcolo remota in una macchina virtuale, in locale nel computer o in un cluster Azure Databricks, e tenere traccia delle metriche di esecuzione e archiviare gli artefatti del modello nell'area di lavoro Azure Machine Learning.

![mlflow con diagramma di Azure Machine Learning](media/how-to-use-mlflow/mlflow-diagram-track.png)

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Confrontare i client MLflow e Azure Machine Learning

 La tabella seguente riepiloga i diversi client che possono usare Azure Machine Learning servizio e le rispettive funzionalità di funzione.

 Il rilevamento MLflow offre funzionalità di registrazione delle metriche e di archiviazione degli artefatti che sono disponibili solo in caso contrario tramite il [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).


| | MLflow rilevamento & distribuzione | SDK Azure Machine Learning Python |  Interfaccia della riga di comando di Azure Machine Learning | Portale di Azure o pagina di destinazione dell'area di lavoro (anteprima)|
|---|---|---|---|---|
| Gestire l'area di lavoro |   | ✓ | ✓ | ✓ |
| Usare gli archivi dati  |   | ✓ | ✓ | |
| Metrica log      | ✓ | ✓ |   | |
| Carica artefatti | ✓ | ✓ |   | |
| Visualizza metriche     | ✓ | ✓ | ✓ | ✓ |
| Gestire il calcolo   |   | ✓ | ✓ | ✓ |
| Distribuire i modelli    | ✓ | ✓ | ✓ | ✓ |
|Monitorare le prestazioni del modello||✓|  |   |
| Rilevare la deriva dei dati |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Prerequisiti

* [Installare MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Installare Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) nel computer locale. l'SDK fornisce la connettività per MLflow per accedere all'area di lavoro.
* [Creare un area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Rileva esecuzioni locali

Il rilevamento MLflow con il servizio Azure Machine Learning consente di archiviare le metriche e gli artefatti registrati dalle esecuzioni locali nell'area di lavoro Azure Machine Learning.

Installare il `azureml-contrib-run` pacchetto per usare il rilevamento MLflow con Azure Machine Learning negli esperimenti eseguiti localmente in un Jupyter notebook o in un editor di codice.

```shell
pip install azureml-contrib-run
```

>[!NOTE]
>Lo spazio dei nomi azureml. contrib viene modificato di frequente, in quanto è possibile migliorare il servizio. Qualsiasi elemento in questo spazio dei nomi deve essere pertanto considerato come anteprima e non è completamente supportato da Microsoft.

Importare le `mlflow` classi [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) e per accedere all'URI di rilevamento di MLflow e configurare l'area di lavoro.

Nel codice seguente il `get_mlflow_tracking_uri()` metodo assegna un indirizzo URI di rilevamento univoco all'area di lavoro, `ws`e `set_tracking_uri()` punta l'URI di rilevamento MLflow a tale indirizzo.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>L'URI di rilevamento è valido fino a un'ora o meno. Se si riavvia lo script dopo un periodo di inattività, usare l'API get_mlflow_tracking_uri per ottenere un nuovo URI.

Impostare il nome dell'esperimento MLflow `set_experiment()` con e avviare l'esecuzione del `start_run()`training con. Usare `log_metric()` quindi per attivare l'API di registrazione MLflow e iniziare la registrazione delle metriche di esecuzione del training.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Rileva esecuzioni remote

Il rilevamento MLflow con Azure Machine Learning servizio consente di archiviare le metriche e gli artefatti registrati dalle esecuzioni remote nell'area di lavoro Azure Machine Learning.

Le esecuzioni remote consentono di eseguire il training dei modelli su calcoli più potenti, ad esempio macchine virtuali abilitate per la GPU o cluster ambiente di calcolo di Machine Learning. Per informazioni sulle diverse opzioni di calcolo, vedere [configurare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md) .

Configurare l'ambiente di esecuzione di calcolo e training con [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) la classe. Includere `mlflow`i pacchetti [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) PIP nella sezione dell'ambiente. `azure-contrib-run` Costruire [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) quindi con il calcolo remoto come destinazione di calcolo.

```Python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-contrib-run'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

Nello script di training importare `mlflow` per usare le API di registrazione MLflow e avviare la registrazione delle metriche di esecuzione.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Con questa configurazione di esecuzione di calcolo e training, usare `Experiment.submit('train.py')` il metodo per inviare un'esecuzione. In questo modo viene impostato automaticamente l'URI di rilevamento MLflow e viene indirizzata la registrazione da MLflow all'area di lavoro.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Rileva esecuzioni Azure Databricks

Il rilevamento MLflow con Azure Machine Learning servizio consente di archiviare le metriche e gli artefatti registrati dalle esecuzioni di databricks nell'area di lavoro Azure Machine Learning.

Per eseguire gli esperimenti Mlflow con Azure Databricks, è prima di tutto necessario creare un' [area di lavoro Azure Databricks e un cluster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

Nel cluster, assicurarsi di installare la libreria *azureml-mlflow* da pypi, per assicurarsi che il cluster abbia accesso alle funzioni e alle classi necessarie.

### <a name="install-libraries"></a>Installare le librerie

Per installare le librerie nel cluster, passare alla scheda **librerie** e fare clic su **Installa nuovo**

 ![mlflow con diagramma di Azure Machine Learning](media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

Nel campo **pacchetto** Digitare azureml-mlflow e quindi fare clic su Installa. Ripetere questo passaggio, se necessario, per installare altri pacchetti aggiuntivi nel cluster per l'esperimento.

 ![mlflow con diagramma di Azure Machine Learning](media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Configurare il notebook e l'area di lavoro

Una volta configurato il cluster, importare il notebook dell'esperimento, aprirlo e collegarvi il cluster.

Il codice seguente deve trovarsi nel notebook dell'esperimento. Questa operazione ottiene i dettagli della sottoscrizione di Azure per creare un'istanza dell'area di lavoro. Si presuppone che si disponga di un gruppo di risorse e di un'area di lavoro Azure Machine Learning, in caso contrario è possibile [crearli](how-to-manage-workspace.md). 

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace
from azureml.mlflow import get_portal_url

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)

```
### <a name="link-mlflow-tracking-to-your-workspace"></a>Collegare il monitoraggio MLflow all'area di lavoro
Dopo avere creato un'istanza dell'area di lavoro, impostare l'URI di rilevamento MLflow. In questo modo è possibile collegare il rilevamento MLflow all'area di lavoro Azure Machine Learning. Al termine di questa operazione, tutti gli esperimenti si troveranno nel servizio di rilevamento Azure Machine Learning gestito.

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

Nello script di training importare mlflow per usare le API di registrazione di MLflow e avviare la registrazione delle metriche di esecuzione. L'esempio seguente registra la metrica di perdita di Epoch. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Visualizzare le metriche e gli artefatti nell'area di lavoro

Le metriche e gli artefatti dalla registrazione MLflow vengono conservati nell'area di lavoro. Per visualizzarli in qualsiasi momento, passare all'area di lavoro e trovare l'esperimento per nome nel [portale di Azure](https://portal.azure.com) o nella [pagina di destinazione dell'area di lavoro (anteprima)](https://ml.azure.com).  In alternativa, eseguire il codice seguente. 

```python
run.get_metrics()
ws.get_details()
```

## <a name="deploy-mlflow-models-as-a-web-service"></a>Distribuire i modelli MLflow come servizio Web

La distribuzione degli esperimenti MLflow come servizio Web Azure Machine Learning consente di sfruttare le funzionalità di gestione del modello di Azure Machine Learning e di rilevamento della deriva dei dati e di applicarle ai modelli di produzione.

Il diagramma seguente illustra che con l'API di distribuzione MLflow è possibile distribuire i modelli MLflow esistenti come un servizio Web Azure Machine Learning, nonostante i relativi Framework, PyTorch, Tensorflow, Scikit-learn, ONNX e così via, e gestire i modelli di produzione in area di lavoro.

![mlflow con diagramma di Azure Machine Learning](media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### <a name="log-your-model"></a>Registrare il modello

Prima di poter distribuire, assicurarsi che il modello sia salvato in modo da potervi fare riferimento e il percorso del percorso per la distribuzione. Nello script di training dovrebbe essere presente codice simile al metodo [mlflow. sklearn. log _model ()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) seguente che salva il modello nella directory degli output specificata. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Includere il `conda_env` parametro per passare una rappresentazione del dizionario delle dipendenze e dell'ambiente in cui deve essere eseguito questo modello.

### <a name="retrieve-model-from-previous-run"></a>Recupera modello dall'esecuzione precedente

Per recuperare l'esecuzione desiderata sono necessari l'ID esecuzione e il percorso nella cronologia di esecuzione in cui è stato salvato il modello. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### <a name="create-docker-image"></a>Creare un'immagine Docker

La `mlflow.azureml.build_image()` funzione compila un'immagine Docker dal modello salvato in modo compatibile con il Framework. Crea automaticamente il codice wrapper per l'inferenza specifico del Framework e specifica le dipendenze del pacchetto. Specificare il percorso del modello, l'area di lavoro, l'ID esecuzione e altri parametri.

Il codice seguente crea un'immagine Docker usando le *esecuzioni:/< Run. id >/Model* come percorso model_uri per un esperimento Scikit-learn.

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
La creazione dell'immagine Docker può richiedere diversi minuti. 

### <a name="deploy-the-docker-image"></a>Distribuire l'immagine Docker 

Dopo aver creato l'immagine, usare l'SDK Azure Machine Learning per distribuire l'immagine come servizio Web.

Specificare prima di tutto la configurazione della distribuzione. Istanza di contenitore di Azure è una scelta ideale per una rapida distribuzione di sviluppo e test, mentre Azure Kubernetes Service (AKS) è adatto per distribuzioni di produzione scalabili.

#### <a name="deploy-to-aci"></a>Distribuire in ACI

Configurare la configurazione della distribuzione con il metodo [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . È anche possibile aggiungere tag e descrizioni per tenere traccia del servizio Web.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Quindi, distribuire l'immagine usando il metodo [deploy_from_image ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-) di Azure Machine Learning SDK. 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### <a name="deploy-to-aks"></a>Distribuire in servizio Azure Kubernetes

Per eseguire la distribuzione in AKS, è necessario creare un cluster AKS e importare l'immagine Docker che si vuole distribuire. Per questo esempio, importare l'immagine creata in precedenza dalla distribuzione di ACI.

Per ottenere l'immagine dalla distribuzione di ACI precedente, usare la classe [Image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py) . 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

Creazione di un calcolo AKS la creazione di un nuovo cluster può richiedere 20-25 minuti

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow' 

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
Configurare la configurazione della distribuzione con il metodo [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . È anche possibile aggiungere tag e descrizioni per tenere traccia del servizio Web.

```python
from azureml.core.webservice import Webservice, AksWebservice
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

Quindi, distribuire l'immagine usando il metodo [deploy_from_image ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-) di Azure Machine Learning SDK. 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
```

La distribuzione del servizio può richiedere diversi minuti.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si prevede di usare le metriche registrate e gli artefatti nell'area di lavoro, la possibilità di eliminarli singolarmente non è attualmente disponibile. Eliminare invece il gruppo di risorse che contiene l'account di archiviazione e l'area di lavoro, in modo che non vengano addebitati costi:

1. Nel portale di Azure fare clic su **Gruppi di risorse** all'estrema sinistra.

   ![Eseguire l'eliminazione nel portale di Azure](media/how-to-use-mlflow/delete-resources.png)

1. Nell'elenco selezionare il gruppo di risorse creato.

1. Selezionare **Elimina gruppo di risorse**.

1. Immettere il nome del gruppo di risorse. Selezionare **Elimina**.


## <a name="example-notebooks"></a>Notebook di esempio

I [notebook di MLflow con Azure ml](https://aka.ms/azureml-mlflow-examples) dimostrano e si espandono sui concetti presentati in questo articolo.

## <a name="next-steps"></a>Passaggi successivi
* [Gestire i modelli](concept-model-management-and-deployment.md).
* Monitorare i modelli di produzione per la [deriva dei dati](how-to-monitor-data-drift.md).
