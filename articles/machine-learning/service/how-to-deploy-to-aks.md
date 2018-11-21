---
title: Distribuire modelli in Kubernetes dal servizio Azure Machine Learning | Microsoft Docs
description: Informazioni su come distribuire un modello dal servizio Azure Machine Learning al servizio Kubernetes di Azure. Il modello viene distribuito come servizio Web. Il servizio Kubernetes di Azure è ideale per carichi di lavoro di produzione su vasta scala.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
manager: cgronlun
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: efaaa196220213877283040120d6c2eeed86dbf3
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2018
ms.locfileid: "51346321"
---
# <a name="how-to-deploy-models-from-azure-machine-learning-service-to-azure-kubernetes-service"></a>Come distribuire modelli dal servizio Azure Machine Learning al servizio Kubernetes di Azure

Per scenari di produzione su vasta scala è possibile distribuire il modello al servizio Kubernetes di Azure (AKS). Azure Machine Learning può usare un cluster AKS esistente o un nuovo cluster creato durante la distribuzione. Il modello viene distribuito nel servizio Kubernetes di Azure come servizio Web.

La distribuzione nel servizio Kubernetes di Azure offre scalabilità automatica, registrazione, raccolta dei dati del modello e tempi di risposta rapidi per il servizio Web.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Un'area di lavoro del servizio Azure Machine Learning, una directory locale contenente gli script e Azure Machine Learning SDK per Python installato. Informazioni su come ottenere questi prerequisiti usando il documento [Come configurare un ambiente di sviluppo](how-to-configure-environment.md).

- Un modello di Machine Learning addestrato. Se non si dispone di un modello, vedere l'esercitazione su come [eseguire il training del modello di classificazione delle immagini](tutorial-train-models-with-aml.md).

## <a name="initialize-the-workspace"></a>Inizializzare l'area di lavoro

Per inizializzare l'area di lavoro, caricare il file `config.json` che contiene le informazioni sull'area di lavoro.

```python
from azureml.coreazureml import Workspace

# Load existing workspace from the config file info.
ws = Workspace.from_config()
```

## <a name="register-the-model"></a>Registrare il modello

Per registrare un modello esistente, specificare il percorso, la descrizione e i tag del modello.

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl", # this points to a local file
                        model_name = "best_model", # this is the name the model is registered as
                        tags = {"data": "diabetes", "type": "regression"},
                        description = "Ridge regression model to predict diabetes",
                        workspace = ws)
```

## <a name="create-a-docker-image"></a>Creare un'immagine Docker

Il servizio Kubernetes di Azure usa immagini Docker. Per creare l'immagine, seguire questa procedura:

1. Per configurare l'immagine, è necessario creare uno script di assegnazione punteggio e un file di ambiente. Per un esempio di creazione dello script e del file di ambiente, vedere le sezioni seguenti dell'esempio sulla classificazione delle immagini:

    * [Creare uno script di assegnazione punteggio (score.py)](tutorial-deploy-models-with-aml.md#create-scoring-script)

        > [!IMPORTANT]
        > Lo script di assegnazione dei punteggi riceve i dati inviati dai client e li passa al modello per l'assegnazione dei punteggi. Documentare la struttura dei data prevista dallo script e dal modello. Questa documentazione semplifica alcune operazioni durante la compilazione di un client che usa il servizio web.

    * [Creare un file di ambiente (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file) 

   Nell'esempio seguente si usano questi file per configurare l'immagine:

    ```python
    from azureml.core.image import ContainerImage

    # Image configuration
    image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                        runtime = "python",
                                                        conda_file = "myenv.yml",
                                                        description = "Image with ridge regression model",
                                                        tags = {"data": "diabetes", "type": "regression"}
                                                        )
    ```

1. Per creare l'immagine, usare la configurazione del modello e l'immagine. Il completamento di questa operazione potrebbe richiedere circa 5 minuti:

    ```python
    image = ContainerImage.create(name = "myimage1",
                                    # this is the model object
                                    models = [model],
                                    image_config = image_config,
                                    workspace = ws)

    # Wait for the create process to complete
    image.wait_for_creation(show_output = True)
    ```

## <a name="create-the-aks-cluster"></a>Creare il cluster AKS

Il frammento di codice seguente illustra come creare il cluster AKS. Il completamento di questo processo richiede circa 20 minuti:

> [!IMPORTANT]
> La creazione del cluster AKS si esegue una sola volta per l'area di lavoro. Dopo aver creato il cluster, è possibile riutilizzarlo per più distribuzioni. Se si elimina il cluster o il gruppo di risorse che lo contiene, per eseguire la distribuzione successiva sarà necessario creare un nuovo cluster.


```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1' 
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                    name = aks_name, 
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

### <a name="attach-existing-aks-cluster-optional"></a>Collegare un cluster AKS esistente (facoltativo)

Se si dispone di un cluster AKS esistente nella sottoscrizione di Azure, è possibile usarlo per distribuire l'immagine. Il frammento di codice seguente illustra come collegare un cluster all'area di lavoro. 

> [!IMPORTANT]
> È supportato solo AKS versione 1.11.2.

```python
# Get the resource id from https://porta..azure.com -> Find your resource group -> click on the Kubernetes service -> Properties
resource_id = '/subscriptions/<your subscription id>/resourcegroups/<your resource group>/providers/Microsoft.ContainerService/managedClusters/<your aks service name>'

# Set to the name of the cluster
cluster_name='my-existing-aks' 

# Attatch the cluster to your workgroup
aks_target = AksCompute.attach(workspace=ws, name=cluster_name, resource_id=resource_id)

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

## <a name="deploy-your-web-service"></a>Distribuire il servizio Web

Il frammento di codice seguente illustra come distribuire l'immagine nel cluster AKS:

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
aks_service = Webservice.deploy_from_image(workspace = ws, 
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!TIP]
> Se si verificano errori durante la distribuzione, usare `aks_service.get_logs()` per visualizzare i log del servizio AKS. Le informazioni registrate potrebbero indicare la causa dell'errore.

## <a name="test-the-web-service"></a>Testare il servizio Web

Usare `aks_service.run()` per testare il servizio Web. Il frammento di codice seguente illustra come passare i dati al servizio e visualizzare la stima:

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = aks_service.run(input_data = test_sample)
print(prediction)
```

## <a name="cleanup"></a>Pulizia

Per eliminare il servizio, l'immagine e il modello, usare il frammento di codice seguente:

```python
aks_service.delete()
image.delete()
model.delete()
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [usare un modello di ML distribuito come un servizio Web](how-to-consume-web-service.md).