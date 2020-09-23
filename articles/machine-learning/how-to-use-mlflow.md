---
title: Rilevamento MLflow per esperimenti ML
titleSuffix: Azure Machine Learning
description: Configurare MLflow con Azure Machine Learning per registrare le metriche e gli artefatti dai modelli ML e distribuire i modelli ML come servizio Web.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 116faae1bc0a93ce2007fcf809a8c96475289036
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90897162"
---
# <a name="track-model-metrics-and-deploy-ml-models-with-mlflow-and-azure-machine-learning-preview"></a>Monitorare le metriche del modello e distribuire modelli di Machine Learning con MLflow e Azure Machine Learning (anteprima)

Questo articolo illustra come abilitare l'URI di rilevamento e l'API di registrazione di MLflow, un'operazione collettivamente nota come [Rilevamento MLflow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), per connettere gli esperimenti di MLflow e Azure Machine Learning. 

Con Azure Machine Learning supporto nativo per MLflow è possibile,

+ Rilevare e registrare le metriche e gli artefatti dell'esperimento nell' [area di lavoro Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces). Se si usa già Rilevamento MLflow per gli esperimenti, l'area di lavoro offre una posizione centralizzata, sicura e scalabile per archiviare le metriche e i modelli di training.

+ Inviare processi di training con i progetti MLflow con il supporto di Azure Machine Learning back-end (anteprima). È possibile inviare processi localmente con Azure Machine Learning rilevamento o eseguire la migrazione delle esecuzioni nel cloud, ad esempio tramite un [Azure Machine Learning calcolo](https://docs.microsoft.com/azure/machine-learning/how-to-create-attach-compute-sdk#amlcompute).

+ Tenere traccia e gestire i modelli in MLflow e nel registro di sistema del modello Azure Machine Learning.

+ Distribuisci gli esperimenti MLflow come servizio Web Azure Machine Learning. Distribuendo come servizio Web, è possibile applicare le funzionalità di monitoraggio Azure Machine Learning e rilevamento della tendenza dei dati ai modelli di produzione. 

[MLflow](https://www.mlflow.org) è una libreria open source per la gestione del ciclo di vita degli esperimenti di machine learning. Rilevamento MLflow è un componente di MLflow che registra e tiene traccia delle metriche di esecuzione del training e degli elementi del modello, indipendentemente dall'ambiente dell'esperimento, localmente nel computer, in una destinazione di calcolo remota, in una macchina virtuale o in un cluster di Azure Databricks. 

>[!NOTE]
> Come libreria open source, MLflow cambia di frequente. Di conseguenza, le funzionalità rese disponibili tramite l'integrazione Azure Machine Learning e MLflow devono essere considerate come anteprima e non sono completamente supportate da Microsoft.

Il diagramma seguente illustra il rilevamento delle metriche di esecuzione di un esperimento e l'archiviazione degli elementi del modello nell'area di lavoro di Azure Machine Learning.

![MLflow con diagramma di Azure Machine Learning](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Le informazioni contenute in questo documento sono destinate principalmente a data scientist e sviluppatori che desiderano monitorare il processo di training del modello. Gli amministratori interessati al monitoraggio dell'uso delle risorse e degli eventi da Azure Machine Learning, come ad esempio quote, esecuzioni di training o distribuzioni del modello completate, possono consultare la sezione [Monitoraggio di Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Client di MLflow e Azure Machine Learning a confronto

 La tabella seguente riepiloga i diversi client che possono usare Azure Machine Learning e le rispettive funzionalità.

 Rilevamento di MLflow offre funzionalità di registrazione delle metriche e di archiviazione degli artefatti disponibili in caso contrario solo tramite l'[SDK Python di Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true).

| Funzionalità | MLflow rilevamento & distribuzione | Python SDK di Azure Machine Learning |  Interfaccia della riga di comando di Azure Machine Learning | Azure Machine Learning Studio|
|---|---|---|---|---|
| Gestire l'area di lavoro |   | ✓ | ✓ | ✓ |
| Usare archivi dati  |   | ✓ | ✓ | |
| Metriche di log      | ✓ | ✓ |   | |
| Caricare artefatti | ✓ | ✓ |   | |
| Visualizzare le metriche     | ✓ | ✓ | ✓ | ✓ |
| Gestire il calcolo   |   | ✓ | ✓ | ✓ |
| Distribuire i modelli    | ✓ | ✓ | ✓ | ✓ |
|Monitorare le prestazioni del modello||✓|  |   |
| Rilevare la deriva dei dati |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Prerequisiti

* [Installare MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Installare l'SDK di Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) nel computer locale  L'SDK fornisce la connettività perché MLflow possa accedere all'area di lavoro.
* [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Rilevare le esecuzioni locali

Rilevamento MLflow con Azure Machine Learning consente di archiviare le metriche e gli artefatti registrati dalle esecuzioni locali nell'area di lavoro di Azure Machine Learning.

Installare il pacchetto `azureml-mlflow` per usare Rilevamento MLflow con Azure Machine Learning sugli esperimenti eseguiti localmente in un Jupyter Notebook o in un editor di codice.

```shell
pip install azureml-mlflow
```

Importare le classi `mlflow` e [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py&preserve-view=true) per accedere all'URI di Rilevamento MLflow e configurare l'area di lavoro.

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

Le esecuzioni remote consentono di eseguire il training dei modelli su calcoli più potenti, ad esempio macchine virtuali abilitate per la GPU o cluster dell'ambiente di calcolo di Machine Learning. Per informazioni sulle diverse opzioni di calcolo, vedere [usare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md) .

Configurare l'ambiente di esecuzione di calcolo e training con la classe [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true). Includere i pacchetti PIP `mlflow` e `azureml-mlflow` nella sezione [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py&preserve-view=true) dell'ambiente. Creare quindi [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py&preserve-view=true) con il calcolo remoto come destinazione di calcolo.

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

Con questa configurazione di esecuzione di calcolo e training, usare il metodo `Experiment.submit()` per inviare un'esecuzione. Questo metodo imposta automaticamente l'URI di Rilevamento MLflow e indirizza la registrazione da MLflow all'area di lavoro.

```Python
run = exp.submit(src)
```

## <a name="train-with-mlflow-projects"></a>Eseguire il training con progetti MLflow

I [progetti MLflow](https://mlflow.org/docs/latest/projects.html) consentono di organizzare e descrivere il codice per permettere ad altri data scientist o a strumenti automatici di eseguirlo. I progetti MLflow con Azure Machine Learning consentono di tenere traccia e gestire le esecuzioni di training nell'area di lavoro. 

Questo esempio illustra come inviare progetti MLflow localmente con Azure Machine Learning Tracking.

Installare il `azureml-mlflow` pacchetto per usare il rilevamento MLflow con Azure Machine Learning negli esperimenti localmente. Gli esperimenti possono essere eseguiti tramite un notebook Jupyter o un editor di codice.

```shell
pip install azureml-mlflow
```

Importare le classi `mlflow` e [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) per accedere all'URI di Rilevamento MLflow e configurare l'area di lavoro.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

Impostare il nome dell'esperimento MLflow con `set_experiment()` e avviare l'esecuzione del training con `start_run()`. Quindi, usare `log_metric()` per attivare l'API di registrazione MLflow e iniziare la registrazione delle metriche di esecuzione del training.

```Python
experiment_name = 'experiment-with-mlflow-projects'
mlflow.set_experiment(experiment_name)
```

Creare l'oggetto di configurazione back-end per archiviare le informazioni necessarie per l'integrazione, ad esempio la destinazione di calcolo e il tipo di ambiente gestito da usare.

```python
backend_config = {"USE_CONDA": False}
```
Aggiungere il `azureml-mlflow` pacchetto come dipendenza pip al file di configurazione dell'ambiente per tenere traccia delle metriche e degli elementi chiave nell'area di lavoro. 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```
Inviare l'esecuzione locale e assicurarsi di impostare il parametro `backend = "azureml" ` . Con questa impostazione è possibile inviare le esecuzioni in locale e ottenere il supporto aggiunto per il rilevamento automatico degli output, i file di log, gli snapshot e gli errori stampati nell'area di lavoro. 

Visualizzare le esecuzioni e le metriche in [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md). 


```python
local_env_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    use_conda=False,
                                    backend_config = backend_config, 
                                    )

```

## <a name="track-azure-databricks-runs"></a>Rilevare le esecuzioni di Azure Databricks

Il rilevamento MLflow con Azure Machine Learning consente di archiviare le metriche e gli artefatti registrati dal Azure Databricks viene eseguito in tutte e tre le aree seguenti: 

* Area di lavoro di Azure Machine Learning
* Area di lavoro di Azure Databricks.
* MLflow

Per eseguire esperimenti di MLflow con Azure Databricks, è necessario innanzitutto creare [un'area di lavoro e un cluster di Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Nel cluster installare la libreria *azureml-mlflow* da pypi per assicurarsi che il cluster abbia accesso alle funzioni e alle classi necessarie.

> [!NOTE]
> Il `azureml.core` pacchetto include `azureml-mlflow` . Se è già stato installato `azureml.core` , è possibile ignorare il `azureml-mlflow` passaggio di installazione. 

Da qui, importare il notebook dell'esperimento, collegarlo al cluster di Azure Databricks ed eseguire l'esperimento. 

### <a name="install-libraries"></a>Installare le librerie

Per installare librerie nel cluster, passare alla scheda **Librerie** e fare clic su **Installa nuova**

 ![mlflow con Azure databricks](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

Nel campo **Pacchetto**, digitare azureml-mlflow e quindi fare clic su Installa. Ripetere questo passaggio, se necessario, per installare altri pacchetti aggiuntivi nel cluster per l'esperimento.

 ![Libreria mlflow di installazione del database di Azure](./media/how-to-use-mlflow/install-libraries.png)

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

### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Connettere le aree di lavoro di Azure Databricks e Azure Machine Learning

Nel [portale di Azure](https://ms.portal.azure.com) è possibile collegare l'area di lavoro di Azure Databricks (ADB) a un'area di lavoro di Azure Machine Learning nuova o esistente. A tale scopo, passare all'area di lavoro di ADB e selezionare il pulsante **Collega area di lavoro di Azure Machine Learning** in basso a destra. Il collegamento delle aree di lavoro consente di tenere traccia dei dati dell'esperimento nell'area di lavoro di Azure Machine Learning. 

### <a name="mlflow-tracking-in-your-workspaces"></a>Rilevamento MLflow nelle aree di lavoro

Dopo aver creato un'istanza dell'area di lavoro, il rilevamento MLflow viene impostato automaticamente in modo da essere rilevato in tutte le posizioni seguenti:

* Area di lavoro Azure Machine Learning collegata.
* L'area di lavoro ADB originale. 
* MLflow. 

Tutti gli esperimenti si troveranno nel servizio di rilevamento Azure Machine Learning gestito.

#### <a name="set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace"></a>Impostare il rilevamento di MLflow per tenere traccia solo nell'area di lavoro Azure Machine Learning

Se si preferisce gestire gli esperimenti rilevati in una posizione centralizzata, è possibile impostare il rilevamento MLflow per tenere traccia **solo** nell'area di lavoro Azure Machine Learning. 


```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

Nello script di training, importare `mlflow` per usare le API di registrazione MLflow e avviare la registrazione delle metriche di esecuzione. L'esempio seguente registra la metrica di perdita per il periodo. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Visualizzare le metriche e gli artefatti nell'area di lavoro

Le metriche e gli artefatti dalla registrazione MLflow vengono conservati nell'area di lavoro. Per visualizzarli in qualsiasi momento, passare all'area di lavoro e individuare l'esperimento in base al nome presente nell'area di lavoro in [Azure Machine Learning Studio](https://ml.azure.com).  In alternativa, eseguire il comando seguente. 

```python
run.get_metrics()
ws.get_details()
```

## <a name="manage-models"></a>Gestire i modelli 

Registrare e tenere traccia dei modelli con il registro di sistema del [modello Azure Machine Learning](concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere) che supporta il registro di sistema del modello MLflow. Azure Machine Learning modelli sono allineati con lo schema del modello MLflow, semplificando l'esportazione e l'importazione di questi modelli in flussi di lavoro diversi. I metadati correlati a MLflow, ad esempio l'ID esecuzione, vengono contrassegnati anche con il modello registrato per la tracciabilità. Gli utenti possono inviare le esecuzioni di training, registrare e distribuire i modelli prodotti dalle esecuzioni di MLflow. 

Se si vuole distribuire e registrare il modello pronto per la produzione in un unico passaggio, vedere [distribuire e registrare i modelli di MLflow](#deploy-and-register-mlflow-models).

Per registrare e visualizzare un modello da un'esecuzione, attenersi alla procedura seguente:

1. Al termine dell'esecuzione, chiamare il `register_model()` metodo.

    ```python
    # the model folder produced from the run is registered. This includes the MLmodel file, model.pkl and the conda.yaml.
    run.register_model(model_name = 'my-model', model_path = 'model')
    ```

1. Visualizzare il modello registrato nell'area di lavoro con [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md).

    Nell'esempio seguente il modello registrato `my-model` include metadati di rilevamento MLflow con tag. 

    ![registrato-mlflow-Model](./media/how-to-use-mlflow/registered-mlflow-model.png)

1. Selezionare la scheda **elementi** per visualizzare tutti i file di modello allineati allo schema del modello MLflow (conda. YAML, MLmodel, Model. PKL).

    ![schema modello](./media/how-to-use-mlflow/mlflow-model-schema.png)

1. Selezionare MLmodel per visualizzare il file MLmodel generato dall'esecuzione.

    ![MLmodel-schema](./media/how-to-use-mlflow/mlmodel-view.png)



## <a name="deploy-and-register-mlflow-models"></a>Distribuire e registrare i modelli MLflow 

La distribuzione degli esperimenti MLflow come servizio Web Azure Machine Learning consente di sfruttare e applicare le funzionalità di gestione dei modelli di Azure Machine Learning e di rilevamento della deriva ai modelli di produzione.

A tale scopo, è necessario

1. Registrare il modello.
1. Determinare la configurazione di distribuzione che si vuole usare per lo scenario.

    1. [Istanza di contenitore di Azure](#deploy-to-aci) è una scelta ideale per una rapida distribuzione di sviluppo e test.
    1. [Azure Kubernetes Service (AKS)](#deploy-to-aks) è adatto per distribuzioni di produzione scalabili.

Il diagramma seguente illustra che con l'API di distribuzione MLflow è possibile distribuire i modelli MLflow esistenti come un servizio Web Azure Machine Learning, nonostante i relativi Framework, PyTorch, Tensorflow, Scikit-learn, ONNX e così via, e gestire i modelli di produzione nell'area di lavoro.

![ distribuire modelli mlflow con Azure Machine Learning](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)


### <a name="deploy-to-aci"></a>Distribuire in ACI

Configurare la configurazione della distribuzione con il metodo [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . È anche possibile aggiungere tag e descrizioni per tenere traccia del servizio Web.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Set the model path to the model folder created by your run
model_path = "model"

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Quindi, registrare e distribuire il modello in un unico passaggio con il metodo di [distribuzione](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) di Azure Machine Learning SDK. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

### <a name="deploy-to-aks"></a>Distribuire in servizio Azure Kubernetes

Per eseguire la distribuzione in AKS, creare prima un cluster AKS. Creare un cluster AKS usando il metodo [ComputeTarget. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#&preserve-view=truecreate-workspace--name--provisioning-configuration-) . La creazione di un nuovo cluster può richiedere 20-25 minuti.

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
Configurare la configurazione della distribuzione con il metodo [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . È anche possibile aggiungere tag e descrizioni per tenere traccia del servizio Web.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

Quindi, registrare e distribuire il modello in un unico passaggio con Azure Machine Learning SDK [Deploy ()] (quindi, registrare e distribuire il modello usando il metodo di [distribuzione](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) di Azure Machine Learning SDK. 

```python

# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice

# set the model path 
model_path = "model"

(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

La distribuzione del servizio può richiedere diversi minuti.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si prevede di usare le metriche registrate e gli artefatti nell'area di lavoro, la possibilità di eliminarli singolarmente non è attualmente disponibile. Eliminare invece il gruppo di risorse che contiene l'account di archiviazione e l'area di lavoro, in modo che non vengano addebitati costi:

1. Nel portale di Azure fare clic su **Gruppi di risorse** all'estrema sinistra.

   ![Eseguire l'eliminazione nel portale di Azure](./media/how-to-use-mlflow/delete-resources.png)

1. Nell'elenco selezionare il gruppo di risorse creato.

1. Selezionare **Elimina gruppo di risorse**.

1. Immettere il nome del gruppo di risorse. Selezionare **Elimina**.

## <a name="example-notebooks"></a>Notebook di esempio

I [notebook MLflow con Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) dimostrano e ampliano le nozioni presentate in questo articolo.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire i modelli](concept-model-management-and-deployment.md).
* Monitorare i modelli di produzione per la [deriva dei dati](how-to-monitor-data-drift.md).
