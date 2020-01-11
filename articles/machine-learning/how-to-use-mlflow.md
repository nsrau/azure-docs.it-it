---
title: Rilevamento MLflow per esperimenti ML
titleSuffix: Azure Machine Learning
description: Configurare MLflow con Azure Machine Learning per registrare le metriche & artefatti e distribuire i modelli da databricks, dall'ambiente locale o dall'ambiente di macchina virtuale.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 09/23/2019
ms.custom: seodec18
ms.openlocfilehash: 47d4c1de12823eaf0aae5beeff776d50f8f5a6a7
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896376"
---
# <a name="track-metrics-and-deploy-models-with-mlflow-and-azure-machine-learning-preview"></a>Rilevare le metriche e distribuire i modelli con MLflow e Azure Machine Learning (anteprima)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come abilitare l'URI di rilevamento e l'API di registrazione di MLflow, collettivamente nota come [MLflow Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), con Azure Machine Learning. In questo modo è possibile:

+ Rilevare e registrare le metriche e gli artefatti dell'esperimento nell' [area di lavoro Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces). Se si usa già il rilevamento MLflow per gli esperimenti, l'area di lavoro offre una posizione centralizzata, sicura e scalabile per archiviare le metriche e i modelli di training.

+ Distribuisci gli esperimenti MLflow come servizio Web Azure Machine Learning. Distribuendo come servizio Web, è possibile applicare le funzionalità di monitoraggio Azure Machine Learning e rilevamento della tendenza dei dati ai modelli di produzione. 

[MLflow](https://www.mlflow.org) è una libreria open source per la gestione del ciclo di vita degli esperimenti di machine learning. Il rilevamento MLFlow è un componente di MLflow che registra e tiene traccia delle metriche di esecuzione del training e degli elementi del modello, indipendentemente dall'ambiente dell'esperimento, in una destinazione di calcolo remota, in una macchina virtuale, in locale nel computer o in un cluster Azure Databricks.

Il diagramma seguente illustra il rilevamento delle metriche di esecuzione di un esperimento e l'archiviazione degli elementi del modello nell'area di lavoro Azure Machine Learning.

![mlflow con diagramma di Azure Machine Learning](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Le informazioni contenute in questo documento sono destinate principalmente a data scientist e sviluppatori che desiderano monitorare il processo di training del modello. Se si è un amministratore interessato al monitoraggio dell'utilizzo delle risorse e degli eventi di Azure Machine Learning, ad esempio le quote, le esecuzioni di training completate o le distribuzioni del modello completate, vedere [monitoraggio Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Confrontare i client MLflow e Azure Machine Learning

 La tabella seguente riepiloga i diversi client che possono usare Azure Machine Learning e le rispettive funzionalità di funzione.

 Il rilevamento MLflow offre funzionalità di registrazione delle metriche e di archiviazione degli artefatti che sono disponibili solo in caso contrario tramite il [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).


| | MLflow rilevamento & distribuzione | SDK Azure Machine Learning Python |  Interfaccia della riga di comando di Azure Machine Learning | Azure Machine Learning Studio|
|---|---|---|---|---|
| Gestire l'area di lavoro |   | ✓ | ✓ | ✓ |
| Usare gli archivi dati  |   | ✓ | ✓ | |
| Metrica log      | ✓ | ✓ |   | |
| Carica artefatti | ✓ | ✓ |   | |
| Visualizzare le metriche     | ✓ | ✓ | ✓ | ✓ |
| Gestire il calcolo   |   | ✓ | ✓ | ✓ |
| Distribuire i modelli    | ✓ | ✓ | ✓ | ✓ |
|Monitorare le prestazioni del modello||✓|  |   |
| Rilevare la deriva dei dati |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Prerequisiti

* [Installare MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Installare Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) nel computer locale. l'SDK fornisce la connettività per MLflow per accedere all'area di lavoro.
* [Creare un area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Rileva esecuzioni locali

Il rilevamento MLflow con Azure Machine Learning consente di archiviare le metriche registrate e gli artefatti dalle esecuzioni locali nell'area di lavoro Azure Machine Learning.

Installare il pacchetto di `azureml-contrib-run` per usare il rilevamento MLflow con Azure Machine Learning sugli esperimenti eseguiti localmente in un Jupyter Notebook o un editor di codice.

```shell
pip install azureml-mlflow
```

>[!NOTE]
>Lo spazio dei nomi azureml. contrib viene modificato di frequente, in quanto è possibile migliorare il servizio. Qualsiasi elemento in questo spazio dei nomi deve essere pertanto considerato come anteprima e non è completamente supportato da Microsoft.

Importare le classi `mlflow` e [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) per accedere all'URI di rilevamento di MLflow e configurare l'area di lavoro.

Nel codice seguente il metodo `get_mlflow_tracking_uri()` assegna un indirizzo URI di rilevamento univoco all'area di lavoro, `ws`e `set_tracking_uri()` punta l'URI di rilevamento MLflow a tale indirizzo.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>L'URI di rilevamento è valido fino a un'ora o meno. Se si riavvia lo script dopo un periodo di inattività, usare l'API get_mlflow_tracking_uri per ottenere un nuovo URI.

Impostare il nome dell'esperimento MLflow con `set_experiment()` e avviare l'esecuzione del training con `start_run()`. Usare quindi `log_metric()` per attivare l'API di registrazione MLflow e iniziare la registrazione delle metriche di esecuzione del training.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Rileva esecuzioni remote

Il rilevamento MLflow con Azure Machine Learning consente di archiviare le metriche registrate e gli artefatti dalle esecuzioni remote nell'area di lavoro Azure Machine Learning.

Le esecuzioni remote consentono di eseguire il training dei modelli su calcoli più potenti, ad esempio macchine virtuali abilitate per la GPU o cluster ambiente di calcolo di Machine Learning. Per informazioni sulle diverse opzioni di calcolo, vedere [configurare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md) .

Configurare l'ambiente di esecuzione di calcolo e training con la classe [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) . Includere `mlflow` e `azure-contrib-run` pacchetti PIP nella sezione [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) dell'ambiente. Costruire quindi [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) con il calcolo remoto come destinazione di calcolo.

```Python
from azureml.core.environment import Environment
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

Con questa configurazione di esecuzione di calcolo e training, usare il metodo `Experiment.submit('train.py')` per inviare un'esecuzione. Questo metodo imposta automaticamente l'URI di rilevamento MLflow e indirizza la registrazione da MLflow all'area di lavoro.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Rileva esecuzioni Azure Databricks

Il rilevamento MLflow con Azure Machine Learning consente di archiviare le metriche registrate e gli artefatti dalle esecuzioni di databricks nell'area di lavoro Azure Machine Learning.

Per eseguire gli esperimenti Mlflow con Azure Databricks, è prima di tutto necessario creare un' [area di lavoro Azure Databricks e un cluster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

Nel cluster, assicurarsi di installare la libreria *azureml-mlflow* da pypi, per assicurarsi che il cluster abbia accesso alle funzioni e alle classi necessarie.
Da qui importare il notebook dell'esperimento, collegarvi il cluster ed eseguire l'esperimento. 

### <a name="install-libraries"></a>Installare le librerie

Per installare le librerie nel cluster, passare alla scheda **librerie** e fare clic su **Installa nuovo**

 ![mlflow con diagramma di Azure Machine Learning](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

Nel campo **pacchetto** Digitare azureml-mlflow e quindi fare clic su Installa. Ripetere questo passaggio, se necessario, per installare altri pacchetti aggiuntivi nel cluster per l'esperimento.

 ![mlflow con diagramma di Azure Machine Learning](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Configurare il notebook e l'area di lavoro

Una volta configurato il cluster, importare il notebook dell'esperimento, aprirlo e collegarvi il cluster.

Il codice seguente deve trovarsi nel notebook dell'esperimento. Questo codice consente di ottenere i dettagli della sottoscrizione di Azure per creare un'istanza dell'area di lavoro. Questo codice presuppone che si disponga di un gruppo di risorse esistente e di un'area di lavoro Azure Machine Learning, in caso contrario è possibile [crearli](how-to-manage-workspace.md). 

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

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Connettere le aree di lavoro Azure Databricks e Azure Machine Learning

Nella [portale di Azure](https://ms.portal.azure.com)è possibile collegare l'area di lavoro di Azure DATABRICKS (ADB) a un'area di lavoro Azure Machine Learning nuova o esistente. A tale scopo, passare all'area di lavoro ADB e selezionare il pulsante **collega Azure machine learning area di lavoro** in basso a destra. Il collegamento delle aree di lavoro consente di tenere traccia dei dati dell'esperimento nell'area di lavoro Azure Machine Learning. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>Collegare il monitoraggio MLflow all'area di lavoro

Dopo avere creato un'istanza dell'area di lavoro, impostare l'URI di rilevamento MLflow. In questo modo è possibile collegare il rilevamento MLflow all'area di lavoro Azure Machine Learning. Dopo il collegamento, tutti gli esperimenti si troveranno nel servizio di rilevamento Azure Machine Learning gestito.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>Imposta direttamente il rilevamento MLflow nel notebook

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

Nello script di training importare mlflow per usare le API di registrazione di MLflow e avviare la registrazione delle metriche di esecuzione. L'esempio seguente registra la metrica di perdita di Epoch. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>Automatizzare l'impostazione del rilevamento MLflow

Anziché impostare manualmente l'URI di rilevamento in ogni successiva sessione del notebook dell'esperimento nei cluster, eseguire questa operazione automaticamente usando lo [script di inizializzazione del cluster di rilevamento Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md).

Una volta configurata correttamente, è possibile visualizzare i dati di rilevamento MLflow nell'API REST di Azure Machine Learning e in tutti i client e in Azure Databricks tramite l'interfaccia utente di MLflow o usando il client MLflow.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Visualizzare le metriche e gli artefatti nell'area di lavoro

Le metriche e gli artefatti dalla registrazione MLflow vengono conservati nell'area di lavoro. Per visualizzarli in qualsiasi momento, passare all'area di lavoro e trovare l'esperimento in base al nome nell'area di lavoro in [Azure Machine Learning Studio](https://ml.azure.com).  In alternativa, eseguire il codice seguente. 

```python
run.get_metrics()
ws.get_details()
```

<!-- ## Deploy MLflow models as a web service

Deploying your MLflow experiments as an Azure Machine Learning web service allows you to leverage the Azure Machine Learning model management and data drift detection capabilities and apply them to your production models.

The following diagram demonstrates that with the MLflow deploy API you can deploy your existing MLflow models as an Azure Machine Learning web service, despite their frameworks--PyTorch, Tensorflow, scikit-learn, ONNX, etc., and manage your production models in your workspace.

![mlflow with azure machine learning diagram](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### Log your model

Before you can deploy, be sure that your model is saved so you can reference it and its path location for deployment. In your training script, there should be code similar to the following [mlflow.sklearn.log_model()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) method, that saves your model to the specified outputs directory. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Include the `conda_env` parameter to pass a dictionary representation of the dependencies and environment this model should be run in.

### Retrieve model from previous run

To retrieve the run, you need the run ID and the path in run history of where the model was saved. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### Create Docker image

The `mlflow.azureml.build_image()` function builds a Docker image from the saved model in a framework-aware manner. It automatically creates the framework-specific inferencing wrapper code and specifies package dependencies for you. Specify the model path, your workspace, run ID and other parameters.

The following code builds a docker image using *runs:/<run.id>/model* as the model_uri path for a Scikit-learn experiment.

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
The creation of the Docker image can take several minutes. 

### Deploy the Docker image 

After the image is created, use the Azure Machine Learning SDK to deploy the image as a web service.

First, specify the deployment configuration. Azure Container Instance (ACI) is a suitable choice for a quick dev-test deployment, while Azure Kubernetes Service (AKS) is suitable for scalable production deployments.

#### Deploy to ACI

Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### Deploy to AKS

To deploy to AKS, first create an AKS cluster and bring over the Docker image you want to deploy. For this example, bring over the previously created image from the ACI deployment.

To get the image from the previous ACI deployment use the [Image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py) class. 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

Create an AKS cluster using the [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) method. It may take 20-25 minutes to create a new cluster.

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
Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import Webservice, AksWebservice
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
```

The service deployment can take several minutes.

## Clean up resources

If you don't plan to use the logged metrics and artifacts in your workspace, the ability to delete them individually is currently unavailable. Instead, delete the resource group that contains the storage account and workspace, so you don't incur any charges:

1. In the Azure portal, select **Resource groups** on the far left.

   ![Delete in the Azure portal](./media/how-to-use-mlflow/delete-resources.png)

1. From the list, select the resource group you created.

1. Select **Delete resource group**.

1. Enter the resource group name. Then select **Delete**.


## Example notebooks

The [MLflow with Azure ML notebooks](https://aka.ms/azureml-mlflow-examples) demonstrate and expand upon concepts presented in this article.

## Next steps
* [Manage your models](concept-model-management-and-deployment.md).
* Monitor your production models for [data drift](how-to-monitor-data-drift.md).
 -->