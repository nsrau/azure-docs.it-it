---
title: Rilevamento MLflow per esperimenti ML
titleSuffix: Azure Machine Learning
description: Configurare MLflow con Azure Machine Learning per registrare le metriche e gli artefatti da modelli ML creati in cluster Databricks, nell'ambiente locale o nell'ambiente della macchina virtuale.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 95567a177635dc7d7ed03404487e62c76db8bdac
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779113"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>Monitorare le metriche dei modelli con MLflow e Azure Machine Learning (anteprima)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come abilitare l'URI di rilevamento e l'API di registrazione di MLflow, un'operazione collettivamente nota come [Rilevamento MLflow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), per connettere gli esperimenti di MLflow e Azure Machine Learning. In questo modo è possibile rilevare e registrare le metriche e gli artefatti degli esperimenti nell'[area di lavoro di Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces). Se si usa già Rilevamento MLflow per gli esperimenti, l'area di lavoro offre una posizione centralizzata, sicura e scalabile per archiviare le metriche e i modelli di training.

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

[MLflow](https://www.mlflow.org) è una libreria open source per la gestione del ciclo di vita degli esperimenti di machine learning. Rilevamento MLflow è un componente di MLflow che registra e tiene traccia delle metriche di esecuzione del training e degli elementi del modello, indipendentemente dall'ambiente dell'esperimento, localmente nel computer, in una destinazione di calcolo remota, in una macchina virtuale o in un cluster di Azure Databricks. 

Il diagramma seguente illustra il rilevamento delle metriche di esecuzione di un esperimento e l'archiviazione degli elementi del modello nell'area di lavoro di Azure Machine Learning.

![MLflow con diagramma di Azure Machine Learning](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Le informazioni contenute in questo documento sono destinate principalmente a data scientist e sviluppatori che desiderano monitorare il processo di training del modello. Gli amministratori interessati al monitoraggio dell'uso delle risorse e degli eventi da Azure Machine Learning, come ad esempio quote, esecuzioni di training o distribuzioni del modello completate, possono consultare la sezione [Monitoraggio di Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Client di MLflow e Azure Machine Learning a confronto

 La tabella seguente riepiloga i diversi client che possono usare Azure Machine Learning e le rispettive funzionalità.

 Rilevamento di MLflow offre funzionalità di registrazione delle metriche e di archiviazione degli artefatti disponibili in caso contrario solo tramite l'[SDK Python di Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).


| | Rilevamento&nbsp;MLflow <!--& Deployment--> | Python SDK di Azure Machine Learning |  Interfaccia della riga di comando di Azure Machine Learning | Azure Machine Learning Studio|
|---|---|---|---|---|
| Gestire l'area di lavoro |   | ✓ | ✓ | ✓ |
| Usare archivi dati  |   | ✓ | ✓ | |
| Metriche di log      | ✓ | ✓ |   | |
| Caricare artefatti | ✓ | ✓ |   | |
| Visualizzare le metriche     | ✓ | ✓ | ✓ | ✓ |
| Gestire il calcolo   |   | ✓ | ✓ | ✓ |

<!--| Deploy models    | ✓ | ✓ | ✓ | ✓ |
|Monitor model performance||✓|  |   |
| Detect data drift |   | ✓ |   | ✓ |
-->
## <a name="prerequisites"></a>Prerequisiti

* [Installare MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Installare l'SDK di Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) nel computer locale  L'SDK fornisce la connettività perché MLflow possa accedere all'area di lavoro.
* [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Rilevare le esecuzioni locali

Rilevamento MLflow con Azure Machine Learning consente di archiviare le metriche e gli artefatti registrati dalle esecuzioni locali nell'area di lavoro di Azure Machine Learning.

Installare il pacchetto `azureml-mlflow` per usare Rilevamento MLflow con Azure Machine Learning sugli esperimenti eseguiti localmente in un Jupyter Notebook o in un editor di codice.

```shell
pip install azureml-mlflow
```

>[!NOTE]
>Lo spazio dei nomi azureml.contrib cambia di frequente perché Microsoft è attualmente impegnata a migliorare il servizio. Qualsiasi elemento in questo spazio dei nomi deve essere pertanto considerato come anteprima e non è completamente supportato da Microsoft.

Importare le classi `mlflow` e [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) per accedere all'URI di Rilevamento MLflow e configurare l'area di lavoro.

Nel codice seguente, il metodo `get_mlflow_tracking_uri()` assegna un indirizzo URI di rilevamento univoco all'area di lavoro, `ws`, mentre `set_tracking_uri()` punta l'URI di Rilevamento MLflow a tale indirizzo.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>L'URI di rilevamento è valido fino a un massimo di un'ora. Se si riavvia lo script dopo un periodo di inattività, usare l'API get_mlflow_tracking_uri per ottenere un nuovo URI.

Impostare il nome dell'esperimento MLflow con `set_experiment()` e avviare l'esecuzione del training con `start_run()`. Usare quindi `log_metric()` per attivare l'API di registrazione MLflow e iniziare la registrazione delle metriche di esecuzione del training.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Rilevare le esecuzioni remote

Rilevamento MLflow con Azure Machine Learning consente di archiviare le metriche e gli artefatti registrati dalle esecuzioni remote nell'area di lavoro di Azure Machine Learning.

Le esecuzioni remote consentono di eseguire il training dei modelli su calcoli più potenti, ad esempio macchine virtuali abilitate per la GPU o cluster dell'ambiente di calcolo di Machine Learning. Per informazioni sulle diverse opzioni di calcolo, vedere la sezione [Configurare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md).

Configurare l'ambiente di esecuzione di calcolo e training con la classe [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py). Includere i pacchetti PIP `mlflow` e `azureml-mlflow` nella sezione [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) dell'ambiente. Creare quindi [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) con il calcolo remoto come destinazione di calcolo.

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

Nello script di training, importare `mlflow` per usare le API di registrazione MLflow e avviare la registrazione delle metriche di esecuzione.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Con questa configurazione di esecuzione di calcolo e training, usare il metodo `Experiment.submit('train.py')` per inviare un'esecuzione. Questo metodo imposta automaticamente l'URI di Rilevamento MLflow e indirizza la registrazione da MLflow all'area di lavoro.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Rilevare le esecuzioni di Azure Databricks

Rilevamento MLflow con Azure Machine Learning consente di archiviare le metriche e gli artefatti registrati dalle esecuzioni di Azure Databricks nell'area di lavoro di Azure Machine Learning.

Per eseguire esperimenti di MLflow con Azure Databricks, è necessario innanzitutto creare [un'area di lavoro e un cluster di Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Nel cluster, assicurarsi di installare la libreria *azureml-mlflow* da PyPi, per assicurarsi che il cluster abbia accesso alle funzioni e alle classi necessarie.

Da qui, importare il notebook dell'esperimento, collegarlo al cluster di Azure Databricks ed eseguire l'esperimento. 

### <a name="install-libraries"></a>Installare le librerie

Per installare librerie nel cluster, passare alla scheda **Librerie** e fare clic su **Installa nuova**

 ![MLflow con diagramma di Azure Machine Learning](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

Nel campo **Pacchetto**, digitare azureml-mlflow e quindi fare clic su Installa. Ripetere questo passaggio, se necessario, per installare altri pacchetti aggiuntivi nel cluster per l'esperimento.

 ![MLflow con diagramma di Azure Machine Learning](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Configurare il notebook e l'area di lavoro

Una volta configurato il cluster, importare il notebook dell'esperimento, aprirlo e collegarvi il cluster.

Il codice seguente deve trovarsi nel notebook dell'esperimento. Questo codice consente di ottenere i dettagli della sottoscrizione di Azure per creare un'istanza dell'area di lavoro. Questo codice presuppone che si disponga di un gruppo di risorse esistente e di un'area di lavoro di Azure Machine Learning. In caso contrario, è possibile [crearli](how-to-manage-workspace.md). 

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

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Connettere le aree di lavoro di Azure Databricks e Azure Machine Learning

Nel [portale di Azure](https://ms.portal.azure.com) è possibile collegare l'area di lavoro di Azure Databricks (ADB) a un'area di lavoro di Azure Machine Learning nuova o esistente. A tale scopo, passare all'area di lavoro di ADB e selezionare il pulsante **Collega area di lavoro di Azure Machine Learning** in basso a destra. Il collegamento delle aree di lavoro consente di tenere traccia dei dati dell'esperimento nell'area di lavoro di Azure Machine Learning. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>Collegare Rilevamento MLflow all'area di lavoro

Dopo avere creato un'istanza dell'area di lavoro, impostare l'URI di Rilevamento MLflow. In questo modo è possibile collegare Rilevamento MLflow all'area di lavoro di Azure Machine Learning. Dopo il collegamento, tutti gli esperimenti verranno visualizzati nel servizio di rilevamento di Azure Machine Learning gestito.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>Impostare direttamente Rilevamento MLflow nel notebook

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

Nello script di training, importare MLflow per usare le API di registrazione MLflow e avviare la registrazione delle metriche di esecuzione. L'esempio seguente registra la metrica di perdita per il periodo. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>Automatizzare l'impostazione di Rilevamento MLflow

Anziché impostare manualmente l'URI di rilevamento in ogni successiva sessione del notebook dell'esperimento nei cluster, eseguire questa operazione automaticamente usando questo [script di inizializzazione del cluster di rilevamento di Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md).

Una volta configurato correttamente, è possibile visualizzare i dati di Rilevamento MLflow nell'API REST di Azure Machine Learning, in tutti i client e in Azure Databricks tramite l'interfaccia utente di MLflow o il client MLflow.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Visualizzare le metriche e gli artefatti nell'area di lavoro

Le metriche e gli artefatti dalla registrazione MLflow vengono conservati nell'area di lavoro. Per visualizzarli in qualsiasi momento, passare all'area di lavoro e individuare l'esperimento in base al nome presente nell'area di lavoro in [Azure Machine Learning Studio](https://ml.azure.com).  In alternativa, eseguire il comando seguente. 

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

### Deploy the model

Use the Azure Machine Learning SDK to deploy the model as a web service.

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

Then, register and deploy the model by using the Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```
#### Deploy to AKS

To deploy to AKS, first create an AKS cluster. Create an AKS cluster using the [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) method. It may take 20-25 minutes to create a new cluster.

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

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')


Then, deploy the image by using the Azure Machine Learning SDK [deploy()](Then, register and deploy the model by using the Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice
(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
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

I [notebook MLflow con Azure Machine Learning](https://aka.ms/azureml-mlflow-examples) dimostrano e ampliano le nozioni presentate in questo articolo.

## <a name="next-steps"></a>Passaggi successivi
* [Gestire i modelli](concept-model-management-and-deployment.md).
* Monitorare i modelli di produzione per la [deriva dei dati](how-to-monitor-data-drift.md).
