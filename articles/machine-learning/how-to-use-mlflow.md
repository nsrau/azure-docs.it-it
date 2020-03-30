---
title: Monitoraggio del flusso di lavoro MLflow per esperimenti ML
titleSuffix: Azure Machine Learning
description: Configurare MLflow con Azure Machine Learning per registrare metriche ed elementi dai modelli di Machine Learning creati nei cluster Databricks, nell'ambiente locale o nell'ambiente VM.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: dce7db9fc508c70d79be62a7e97b3bf52a316b22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76983699"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>Tenere traccia delle metriche dei modelli con MLflow e Azure Machine Learning (anteprima)Track models metrics with MLflow and Azure Machine Learning (preview)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come abilitare l'URI di rilevamento di MLflow e l'API di registrazione, collettivamente nota come [rilevamento di MLflow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), per connettere gli esperimenti MLflow e Azure Machine Learning. In questo modo è possibile tenere traccia e registrare le metriche e gli elementi dell'esperimento nell'area di lavoro di [Azure Machine Learning.](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces) Se si usa già MLflow Tracking per gli esperimenti, l'area di lavoro fornisce una posizione centralizzata, sicura e scalabile per archiviare le metriche e i modelli di training.

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

[MLflow](https://www.mlflow.org) è una libreria open source per la gestione del ciclo di vita degli esperimenti di apprendimento automatico. Il rilevamento MLFlow è un componente di MLflow che registra e tiene traccia delle metriche di training e degli elementi del modello, indipendentemente dall'ambiente dell'esperimento, in locale nel computer, in una destinazione di calcolo remota, in una macchina virtuale o in un cluster di Azure Databricks. 

Il diagramma seguente illustra che con Il rilevamento del flusso di Machine Learning si tiene traccia delle metriche di esecuzione di un esperimento e si archiviano gli elementi del modello nell'area di lavoro di Azure Machine Learning.The following diagram illustrates that with MLflow Tracking, you track an experiment's run metrics and store model artifacts in your Azure Machine Learning workspace.

![mlflow con diagramma di apprendimento automatico azzurro](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Le informazioni contenute in questo documento sono principalmente per i data scientist e gli sviluppatori che desiderano monitorare il processo di training del modello. Se si è un amministratore interessato a monitorare l'utilizzo delle risorse e gli eventi da Azure Machine Learning, ad esempio quote, esecuzioni di training completate o distribuzioni di modelli completate, vedere Monitoraggio di Azure Machine Learning.If you are an administrator interested in monitoring resource usage and events from Azure Machine Learning, such as quotas, completed training runs, or completed model deployments, see [Monitoring Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Confrontare i client MLflow e Azure Machine Learning

 Nella tabella seguente sono riepilogati i diversi client che possono usare Azure Machine Learning e le rispettive funzionalità di funzionalità.

 MLflow Tracking offre funzionalità di registrazione delle metriche e archiviazione degli artefatti che sono disponibili solo in caso contrario tramite [Azure Machine Learning Python SDK.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)


| | Monitoraggio del&nbsp;flusso ML <!--& Deployment--> | Azure Machine Learning Python SDK |  Interfaccia della riga di comando di Azure Machine Learning | Azure Machine Learning Studio|
|---|---|---|---|---|
| Gestire l'area di lavoro |   | ✓ | ✓ | ✓ |
| Utilizzare gli archivi dati  |   | ✓ | ✓ | |
| Metriche di log      | ✓ | ✓ |   | |
| Caricare gli elementi | ✓ | ✓ |   | |
| Visualizzare le metriche     | ✓ | ✓ | ✓ | ✓ |
| Gestire il calcolo   |   | ✓ | ✓ | ✓ |

<!--| Deploy models    | ✓ | ✓ | ✓ | ✓ |
|Monitor model performance||✓|  |   |
| Detect data drift |   | ✓ |   | ✓ |
-->
## <a name="prerequisites"></a>Prerequisiti

* [Installare MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Installare Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) nel computer locale L'SDK fornisce la connettività per MLflow per accedere all'area di lavoro.
* [Creare un'area di](how-to-manage-workspace.md)lavoro di Azure Machine Learning.

## <a name="track-local-runs"></a>Tenere traccia delle corse locali

MLflow Tracking con Azure Machine Learning consente di archiviare le metriche e gli elementi registrati dalle esecuzioni locali nell'area di lavoro di Azure Machine Learning.MLflow Tracking with Azure Machine Learning lets you store the logged metrics and artifacts from your local runs into your Azure Machine Learning workspace.

Installare `azureml-mlflow` il pacchetto per usare MLflow Tracking con Azure Machine Learning negli esperimenti eseguiti in locale in un notebook Jupyter o nell'editor di codice.

```shell
pip install azureml-mlflow
```

>[!NOTE]
>Lo spazio dei nomi azureml.contrib cambia frequentemente, mentre si lavora per migliorare il servizio. Qualsiasi elemento in questo spazio dei nomi deve essere pertanto considerato come anteprima e non è completamente supportato da Microsoft.

Importare `mlflow` le [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) classi e per accedere all'URI di rilevamento di MLflow e configurare l'area di lavoro.

Nel codice seguente, `get_mlflow_tracking_uri()` il metodo assegna un indirizzo URI `ws`di `set_tracking_uri()` rilevamento univoco all'area di lavoro , e punta l'URI di rilevamento MLflow a tale indirizzo.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>L'URI di rilevamento è valido fino a un'ora o meno. Se si riavvia lo script dopo un certo tempo di inattività, usare l'API di get_mlflow_tracking_uri per ottenere un nuovo URI.

Impostare il nome dell'esperimento MLflow `set_experiment()` con e iniziare l'esecuzione di corsi di formazione con `start_run()`. Quindi `log_metric()` usare per attivare l'API di registrazione MLflow e iniziare a registrare le metriche di esecuzione del training.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Tenere traccia delle condutture remote

MLflow Tracking con Azure Machine Learning consente di archiviare le metriche e gli elementi registrati dal telecomando eseguito nell'area di lavoro di Azure Machine Learning.MLflow Tracking with Azure Machine Learning lets you store the logged metrics and artifacts from your remote runs into your Azure Machine Learning workspace.

Le esecuzioni remote consentono di eseguire il training dei modelli su calcoli più potenti, ad esempio macchine virtuali abilitate per GPU o cluster Machine Learning Compute.Remote runs let you train your models on more powerful computes, such as GPU enabled virtual machines, or Machine Learning Compute clusters. Per informazioni sulle diverse opzioni di calcolo, [vedere Impostare le destinazioni di calcolo per il training del modello.](how-to-set-up-training-targets.md)

Configurare l'ambiente di [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) esecuzione del calcolo e del training con la classe . Includere `mlflow` `azureml-mlflow` pacchetti pip nella [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) sezione dell'ambiente. Costruire [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) quindi con il calcolo remoto come destinazione di calcolo.

```Python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-mlflow'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

Nello script di `mlflow` training importare per usare le API di registrazione di MLflow e iniziare a registrare le metriche di esecuzione.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Con questa configurazione di elaborazione `Experiment.submit('train.py')` e training, usare il metodo per inviare un'esecuzione. Questo metodo imposta automaticamente l'URI di rilevamento MLflow e indirizza la registrazione da MLflow all'area di lavoro.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Tenere traccia delle esecuzioni di Azure DatabricksTrack Azure Databricks runs

Il rilevamento di MLflow con Azure Machine Learning consente di archiviare le metriche e gli elementi registrati dai databrick di Azure eseguiti nell'area di lavoro di Azure Machine Learning.MLflow Tracking with Azure Machine Learning lets you store the logged metrics and artifacts from your Azure Databricks runs in your Azure Machine Learning workspace.

Per eseguire gli esperimenti Mlflow con Azure Databricks, è innanzitutto necessario creare un'area di lavoro e un cluster di [Azure Databricks.](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) Nel cluster assicurarsi di installare la libreria *azureml-mlflow* da PyPi, per assicurarsi che il cluster abbia accesso alle funzioni e alle classi necessarie.

A questo posto, importare il blocco appunti dell'esperimento, collegarlo al cluster Azure Databricks ed eseguire l'esperimento. 

### <a name="install-libraries"></a>Installare le librerie

Per installare le librerie nel cluster, accedere alla scheda **Librerie** e fare clic su **Installa nuovo**

 ![mlflow con diagramma di apprendimento automatico azzurro](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

Nel campo **Pacchetto** digitare azureml-mlflow e quindi fare clic su Installa. Ripetere questo passaggio in base alle esigenze per installare altri pacchetti aggiuntivi nel cluster per l'esperimento.

 ![mlflow con diagramma di apprendimento automatico azzurro](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Configurare il blocco appunti e l'area di lavoro

Dopo aver configurato il cluster, importare il blocco appunti dell'esperimento, aprirlo e collegarvi il cluster.

Il codice seguente deve essere presente nel blocco appunti dell'esperimento. Questo codice ottiene i dettagli della sottoscrizione di Azure per creare un'istanza dell'area di lavoro. Questo codice presuppone la disponibilità di un gruppo di risorse esistente e di un'area di lavoro di Azure Machine Learning, altrimenti è possibile [crearle](how-to-manage-workspace.md). 

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

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Connetti le aree di lavoro di Azure Databricks e Azure Machine Learning

Nel [portale di Azure](https://ms.portal.azure.com)è possibile collegare l'area di lavoro di Azure Databricks (ADB) a un'area di lavoro di Azure Machine Learning nuova o esistente. A tale scopo, passare all'area di lavoro ADB e selezionare il pulsante **Collega area di lavoro di Azure Machine Learning** in basso a destra. Il collegamento delle aree di lavoro consente di tenere traccia dei dati dell'esperimento nell'area di lavoro di Azure Machine Learning.Linking your workspaces enables you to track your experiment data in the Azure Machine Learning workspace. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>Collegare il rilevamento MLflow all'area di lavoro

Dopo aver creato un'istanza dell'area di lavoro, impostare l'URI di rilevamento MLflow.After you instantiate your workspace, set the MLflow tracking URI. In questo modo, si collega il rilevamento MLflow all'area di lavoro di Azure Machine Learning.By so, you link the MLflow tracking to Azure Machine Learning workspace. Dopo il collegamento, tutti gli esperimenti verranno effettuati nel servizio di monitoraggio di Azure Machine Learning gestito.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>Impostare direttamente MLflow Tracking nel notebook

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

Nello script di training importare mlflow per usare le API di registrazione di MLflow e iniziare a registrare le metriche di esecuzione. Nell'esempio seguente viene logdata la metrica di perdita di epoca. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>Automatizza l'impostazione del rilevamento MLflow

Anziché impostare manualmente l'URI di rilevamento in ogni sessione del blocco appunti dell'esperimento successiva nei cluster, eseguire questa operazione automaticamente usando questo script Init del cluster di rilevamento di [Azure.Machine Learning Tracking Cluster Init .](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md)

Se configurato correttamente, è possibile visualizzare i dati di rilevamento di MLflow nell'API REST di Azure Machine Learning e in tutti i client e in Azure Databricktt tramite l'interfaccia utente MLflow o tramite il client MLflow.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Visualizzare metriche ed elementi nell'area di lavoro

Le metriche e gli elementi della registrazione di MLflow vengono mantenuti nell'area di lavoro. Per visualizzarli in qualsiasi momento, passare all'area di lavoro e trovare l'esperimento per nome nell'area di lavoro in [Azure Machine Learning Studio.](https://ml.azure.com)  Oppure eseguire il codice seguente. 

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

 -->

 ## <a name="example-notebooks"></a>Notebook di esempio

[MLflow con blocchi appunti](https://aka.ms/azureml-mlflow-examples) di Azure ML illustrano ed espandono i concetti presentati in questo articolo.

## <a name="next-steps"></a>Passaggi successivi
* [Gestisci i tuoi modelli](concept-model-management-and-deployment.md).
* Monitorare i modelli di produzione per [la deriva dei dati](how-to-monitor-data-drift.md).
