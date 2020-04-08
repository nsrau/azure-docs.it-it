---
title: Come distribuire modelli nel servizio Azure KubernetesHow to deploy models to Azure Kubernetes Service
titleSuffix: Azure Machine Learning
description: Informazioni su come distribuire i modelli di Azure Machine Learning come servizio Web usando il servizio Azure Kubernetes.Learn how to deploy your Azure Machine Learning models as a web service using Azure Kubernetes Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 01/16/2020
ms.openlocfilehash: aec1b7f7bf60be34d21d52ca652a776cf3275fe8
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811761"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Distribuire un modello in un cluster di servizi Azure KubernetesDeploy a model to an Azure Kubernetes Service cluster
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Informazioni su come usare Azure Machine Learning per distribuire un modello come servizio Web nel servizio Azure Kubernetes.Learn how to use Azure Machine Learning to deploy a model as a web service on Azure Kubernetes Service (AKS). Il servizio Azure Kubernetes è utile per le distribuzioni di produzione su larga scala. Usare il servizio Azure Kubernetes se sono necessarie una o più delle funzionalità seguenti:Use Azure Kubernetes service if you need one or more of the following capabilities:

- __Tempi di risposta rapidi__.
- __Scalabilità automatica__ del servizio distribuito.
- __Opzioni di accelerazione hardware__ come GPU e FPGA (Field-programmable gate array).

> [!IMPORTANT]
> Il ridimensionamento del cluster non viene fornito tramite Azure Machine Learning SDK. Per altre informazioni sulla scalabilità dei nodi in un cluster AKS, vedere Ridimensionare il numero di nodi [in un cluster AKS](../aks/scale-cluster.md).

Quando si esegue la distribuzione nel servizio Azure Kubernetes, si esegue la distribuzione in un cluster AKS __connesso all'area di lavoro.__ Esistono due modi per connettere un cluster AKS all'area di lavoro:

* Creare il cluster AKS usando Azure Machine Learning SDK, l'interfaccia della riga di comando di Machine Learning o [lo studio di Azure Machine Learning.](https://ml.azure.com) Questo processo connette automaticamente il cluster all'area di lavoro.
* Collegare un cluster AKS esistente all'area di lavoro di Azure Machine Learning.Attach an existing AKS cluster to your Azure Machine Learning workspace. Un cluster può essere collegato usando Azure Machine Learning SDK, l'interfaccia della riga di comando di Machine Learning o Azure Machine Learning Studio.A cluster can be attached using the Azure Machine Learning SDK, Machine Learning CLI, or Azure Machine Learning studio.

> [!IMPORTANT]
> Il processo di creazione o allegato è un'attività unica. Dopo aver connesso un cluster AKS all'area di lavoro, è possibile utilizzarlo per le distribuzioni. È possibile scollegare o eliminare il cluster AKS se non è più necessario. Una volta scollegato o eliminato, non sarà più possibile eseguire la distribuzione nel cluster.

## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [Creare un'area](how-to-manage-workspace.md)di lavoro di Azure Machine Learning.For more information, see Create an Azure Machine Learning workspace .

- Un modello di apprendimento automatico registrato nell'area di lavoro. Se non si dispone di un modello registrato, vedere [Come e dove distribuire i modelli](how-to-deploy-and-where.md).

- [L'estensione dell'interfaccia della](reference-azure-machine-learning-cli.md)riga di comando di Azure per il servizio Machine Learning , [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)o l'estensione di codice di Visual Studio di Azure [Machine Learning](tutorial-setup-vscode-extension.md).

- I frammenti di codice __Python__ in questo articolo presuppongono che siano impostate le seguenti variabili:

    * `ws`- Impostare l'area di lavoro.
    * `model`- Impostare il modello registrato.
    * `inference_config`- Impostare la configurazione di inferenza per il modello.

    Per ulteriori informazioni sull'impostazione di queste variabili, vedere [Come e dove distribuire i modelli](how-to-deploy-and-where.md).

- I frammenti __CLI__ in questo articolo presuppongono che sia stato creato un `inferenceconfig.json` documento. Per ulteriori informazioni sulla creazione di questo documento, vedere [Come e dove distribuire i modelli](how-to-deploy-and-where.md).

## <a name="create-a-new-aks-cluster"></a>Creare un nuovo cluster AKSCreate a new AKS cluster

**Stima del tempo**: circa 20 minuti.

La creazione o il collegamento di un cluster AKS è un processo una tantera per l'area di lavoro. È possibile riutilizzare questo cluster per più distribuzioni. Se si elimina il cluster o il gruppo di risorse che lo contiene, è necessario creare un nuovo cluster alla successiva distribuzione. È possibile avere più cluster AKS collegati all'area di lavoro.

> [!TIP]
> Se si vuole proteggere il cluster AKS usando una rete virtuale di Azure, è innanzitutto necessario creare la rete virtuale. Per altre informazioni, vedere Secure experimentation e inference with Azure Virtual Network .For more information, see [Secure experimentation and inference with Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

Se si desidera creare un cluster AKS per __lo sviluppo__, la __convalida__e il __test__ anziché la produzione, è possibile specificare lo scopo del __cluster__ per il test di __sviluppo.__

> [!WARNING]
> Se si `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`imposta , il cluster creato non è adatto per il traffico a livello di produzione e può aumentare i tempi di inferenza. Anche i cluster di sviluppo/test non garantiscono la tolleranza di errore. È consigliabile utilizzare almeno 2 CPU virtuali per i cluster di sviluppo/test.

Gli esempi seguenti illustrano come creare un nuovo cluster AKS usando l'SDK e l'interfaccia della riga di comando:

**Uso dell'SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> Per [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), se si `agent_count` selezionano valori personalizzati per `vm_size`e , e `cluster_purpose` non `DEV_TEST`è , è necessario assicurarsi che `agent_count` moltiplicato per `vm_size` sia maggiore o uguale a 12 CPU virtuali. Ad esempio, se `vm_size` si utilizza un di "Standard_D3_v2", che dispone `agent_count` di 4 CPU virtuali, è necessario scegliere un 3 o superiore.
>
> Azure Machine Learning SDK non fornisce il supporto per la scalabilità di un cluster AKS. Per ridimensionare i nodi nel cluster, usare l'interfaccia utente per il cluster AKS in Azure Machine Learning Studio.To scale the nodes in the cluster, use the UI for your AKS cluster in the Azure Machine Learning studio. È possibile modificare solo il numero di nodi, non le dimensioni della macchina virtuale del cluster.

Per ulteriori informazioni sulle classi, i metodi e i parametri utilizzati in questo esempio, vedere i seguenti documenti di riferimento:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Uso dell'interfaccia della riga di comando**

```azurecli
az ml computetarget create aks -n myaks
```

Per altre informazioni, vedere il riferimento [az ml computetarget create aks.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks)

## <a name="attach-an-existing-aks-cluster"></a>Collegare un cluster AKS esistente

**Stima del tempo:** Circa 5 minuti.

Se nella sottoscrizione di Azure è già presente un cluster AKS che è versione 1.17 o inferiore, è possibile usarlo per distribuire l'immagine.

> [!TIP]
> Il cluster AKS esistente può trovarsi in un'area di Azure diversa dall'area di lavoro di Azure Machine Learning.The existing AKS cluster can be in a Azure region other than your Azure Machine Learning workspace.
>
> Se si vuole proteggere il cluster AKS usando una rete virtuale di Azure, è innanzitutto necessario creare la rete virtuale. Per altre informazioni, vedere Secure experimentation e inference with Azure Virtual Network .For more information, see [Secure experimentation and inference with Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

Quando si collega un cluster AKS a un'area di lavoro, `cluster_purpose` è possibile definire la modalità di utilizzo del cluster impostando il parametro.

Se non si `cluster_purpose` imposta il `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`parametro o si imposta , il cluster deve disporre di almeno 12 CPU virtuali.

Se si `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`imposta , non è necessario che il cluster disponga di 12 CPU virtuali. Consigliamo almeno 2 CPU virtuali per lo sviluppo/test. Tuttavia, un cluster configurato per sviluppo/test non è adatto per il traffico a livello di produzione e può aumentare i tempi di inferenza. Anche i cluster di sviluppo/test non garantiscono la tolleranza di errore.

> [!WARNING]
> Non creare più allegati simultanei allo stesso cluster AKS dall'area di lavoro. Ad esempio, collegare un cluster AKS a un'area di lavoro utilizzando due nomi diversi. Ogni nuovo allegato interromperà gli allegati esistenti precedenti.
>
> Se si desidera ricollegare un cluster AKS, ad esempio per modificare TLS o un'altra impostazione di configurazione del cluster, è necessario innanzitutto rimuovere l'allegato esistente utilizzando [AksCompute.detach()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--).

Per altre informazioni sulla creazione di un cluster AKS usando l'interfaccia della riga di comando o il portale di Azure, vedere gli articoli seguenti:For more information on creating an AKS cluster using the Azure CLI or portal, see the following articles:

* [Creare un cluster del servizio Azure Kubernetes (interfaccia della riga di comando)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Creare un cluster AKS (portale)Create an AKS cluster (portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Negli esempi seguenti viene illustrato come collegare un cluster AKS esistente all'area di lavoro:The following examples demonstrate how to attach an existing AKS cluster to your workspace:

**Uso dell'SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)
```

Per ulteriori informazioni sulle classi, i metodi e i parametri utilizzati in questo esempio, vedere i seguenti documenti di riferimento:

* [AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**Uso dell'interfaccia della riga di comando**

Per collegare un cluster esistente tramite l'interfaccia della riga di comando, è necessario ottenere l'ID risorsa del cluster esistente. Per ottenere questo valore, utilizzare il comando seguente. Sostituire `myexistingcluster` con il nome del cluster AKS. Sostituire `myresourcegroup` con il gruppo di risorse che contiene il cluster:Replace with the resource group that contains the cluster:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Il comando restituisce un valore simile al testo seguente:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Per collegare il cluster esistente all'area di lavoro, utilizzare il comando seguente. Sostituire `aksresourceid` con il valore restituito dal comando precedente. Sostituire `myresourcegroup` con il gruppo di risorse che contiene l'area di lavoro. Sostituire `myworkspace` con il nome dell'area di lavoro.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Per altre informazioni, vedere il riferimento [az ml computetarget attach aks.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

## <a name="deploy-to-aks"></a>Distribuire in servizio Azure Kubernetes

Per distribuire un modello nel servizio Azure Kubernetes, creare una configurazione di __distribuzione__ che descriva le risorse di calcolo necessarie. Ad esempio, numero di core e memoria. È inoltre necessaria una configurazione di __inferenza__, che descrive l'ambiente necessario per ospitare il modello e il servizio Web. Per ulteriori informazioni sulla creazione della configurazione di inferenza, vedere [Come e dove distribuire](how-to-deploy-and-where.md)i modelli .

### <a name="using-the-sdk"></a>Uso dell'SDK

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Per ulteriori informazioni sulle classi, i metodi e i parametri utilizzati in questo esempio, vedere i seguenti documenti di riferimento:

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Modello.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Uso dell'interfaccia della riga di comando

Per eseguire la distribuzione tramite l'interfaccia della riga di comando, utilizzare il comando seguente. Sostituire `myaks` con il nome della destinazione di calcolo AKS. Sostituire `mymodel:1` con il nome e la versione del modello registrato. Sostituire `myservice` con il nome per assegnare questo servizio:Replace with the name to give this service:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Per altre informazioni, vedere il riferimento alla distribuzione del [modello az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)

### <a name="using-vs-code"></a>Uso di VS Code

Per informazioni sull'utilizzo del codice VS, vedere [distribuire in AKS tramite l'estensione del codice VS](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> La distribuzione tramite codice VS richiede in anticipo che il cluster AKS venga creato o collegato all'area di lavoro.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Distribuire modelli in AKS usando l'implementazione controllata (anteprima)Deploy models to AKS using controlled rollout (preview)

Analizzare e promuovere le versioni dei modelli in modo controllato utilizzando gli endpoint. È possibile distribuire fino a sei versioni dietro un singolo endpoint. Gli endpoint forniscono le funzionalità seguenti:Endpoints provide the following capabilities:

* Configurare la __percentuale di traffico di assegnazione del punteggio inviato a ogni endpoint.__ Ad esempio, instradare il 20% del traffico all'endpoint "test" e l'80% alla "produzione".

    > [!NOTE]
    > Se non si tiene conto del 100% del traffico, la percentuale rimanente viene instradata alla versione __predefinita__ dell'endpoint. Ad esempio, se si configura la versione dell'endpoint 'test' per ottenere il 10% del traffico e 'prod' per il 30%, il restante 60% viene inviato alla versione dell'endpoint predefinita.
    >
    > La prima versione dell'endpoint creata viene configurata automaticamente come predefinita. È possibile modificare `is_default=True` questa impostazione durante la creazione o l'aggiornamento di una versione dell'endpoint.
     
* Contrassegnare una versione dell'endpoint come __controllo__ o __trattamento__. Ad esempio, la versione dell'endpoint di produzione corrente potrebbe essere il controllo, mentre i nuovi potenziali modelli vengono distribuiti come versioni di trattamento. Dopo aver valutato le prestazioni delle versioni di trattamento, se uno supera il controllo corrente, potrebbe essere promosso alla nuova produzione / controllo.

    > [!NOTE]
    > È possibile avere __un__ solo controllo. Può avere più trattamenti.

È possibile abilitare le informazioni dettagliate sulle app per visualizzare le metriche operative degli endpoint e delle versioni distribuite.

### <a name="create-an-endpoint"></a>Creare un endpoint
Quando si è pronti per distribuire i modelli, creare un endpoint di assegnazione del punteggio e distribuire la prima versione. Nell'esempio seguente viene illustrato come distribuire e creare l'endpoint utilizzando l'SDK. La prima distribuzione verrà definita come la versione predefinita, il che significa che il percentile del traffico non specificato in tutte le versioni passerà alla versione predefinita.  

> [!TIP]
> Nell'esempio seguente, la configurazione imposta la versione iniziale dell'endpoint per gestire il 20% del traffico. Poiché questo è il primo endpoint, è anche la versione predefinita. E dal momento che non abbiamo altre versioni per l'altro 80% del traffico, è instradato al default pure. Fino a quando non vengono distribuite altre versioni che accettano una percentuale di traffico, questa riceve in modo efficace il 100% del traffico.

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Aggiornare e aggiungere versioni a un endpointUpdate and add versions to an endpoint

Aggiungere un'altra versione all'endpoint e configurare il percentile del traffico di punteggio per la versione. Ci sono due tipi di versioni, un controllo e una versione di trattamento. Ci possono essere più versioni di trattamento per aiutare a confrontare con una singola versione di controllo.

> [!TIP]
> La seconda versione, creata dal frammento di codice seguente, accetta il 10% del traffico. La prima versione è configurata per il 20%, quindi solo il 30% del traffico è configurato per versioni specifiche. Il restante 70% viene inviato alla prima versione dell'endpoint, perché è anche la versione predefinita.

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
endpoint.wait_for_deployment(True)
```

Aggiornare le versioni esistenti o eliminarle in un endpoint. È possibile modificare il tipo predefinito della versione, il tipo di controllo e il percentile del traffico. Nell'esempio seguente, la seconda versione aumenta il traffico al 40% ed è ora l'impostazione predefinita.

> [!TIP]
> Dopo il frammento di codice seguente, la seconda versione è ora predefinita. Ora è configurato per il 40%, mentre la versione originale è ancora configurata per il 20%. Ciò significa che il 40% del traffico non è rappresentato dalle configurazioni di versione. Il traffico rimanente verrà instradato alla seconda versione, perché ora è predefinito. Riceve effettivamente l'80% del traffico.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```


## <a name="web-service-authentication"></a>Autenticazione del servizio Web

Quando si esegue la distribuzione nel servizio Azure Kubernetes, l'autenticazione __basata su chiave__ è abilitata per impostazione predefinita. È inoltre possibile abilitare l'autenticazione __basata su token.__ L'autenticazione basata su token richiede ai client di usare un account Azure Active Directory per richiedere un token di autenticazione, usato per effettuare richieste al servizio distribuito.

Per __disabilitare__ l'autenticazione, impostare il `auth_enabled=False` parametro durante la creazione della configurazione di distribuzione. L'esempio seguente disabilita l'autenticazione utilizzando l'SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Per informazioni sull'autenticazione da un'applicazione client, vedere [Consume an Azure Machine Learning model deployed as a web service](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Autenticazione con chiavi

Se l'autenticazione con chiave `get_keys` è abilitata, è possibile utilizzare il metodo per recuperare una chiave di autenticazione primaria e secondaria:If key authentication is enabled, you can use the method to retrieve a primary and secondary authentication key:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se è necessario rigenerare una chiave,[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Autenticazione con token

Per abilitare l'autenticazione token, impostare il `token_auth_enabled=True` parametro durante la creazione o l'aggiornamento di una distribuzione. L'esempio seguente abilita l'autenticazione token utilizzando l'SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Se l'autenticazione token è `get_token` abilitata, è possibile utilizzare il metodo per recuperare un token JWT e l'ora di scadenza del token:If token authentication is enabled, you can use the method to retrieve a JWT token and that token's expiration time:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Sarà necessario richiedere un nuovo token dopo `refresh_by` l'ora del token.
>
> Microsoft consiglia vivamente di creare l'area di lavoro di Azure Machine Learning nella stessa area del cluster di servizi Azure Kubernetes.Microsoft strongly recommends that you create your Azure Machine Learning workspace in the same region as your Azure Kubernetes Service cluster. Per eseguire l'autenticazione con un token, il servizio Web effettuerà una chiamata all'area in cui viene creata l'area di lavoro di Azure Machine Learning.To authenticate with a token, the web service will make a call to the region in which your Azure Machine Learning workspace is created. Se l'area di lavoro non è disponibile, non sarà possibile recuperare un token anche per il servizio Web, se il cluster si trova in un'area diversa rispetto all'area di lavoro. In questo modo l'autenticazione basata su token non è disponibile finché l'area dell'area di lavoro non è nuovamente disponibile. Inoltre, maggiore è la distanza tra l'area del cluster e l'area dell'area di lavoro, maggiore sarà il tempo necessario per recuperare un token.

## <a name="update-the-web-service"></a>Aggiornare il servizio Web

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Sperimentazione sicura e inferenza in una rete virtualeSecure experimentation and inference in a virtual network](how-to-enable-virtual-network.md)
* [Come distribuire un modello usando un'immagine Docker personalizzataHow to deploy a model using a custom Docker image](how-to-deploy-custom-docker-image.md)
* [Risoluzione dei problemi di distribuzione](how-to-troubleshoot-deployment.md)
* [Usare TLS per proteggere un servizio Web tramite Azure Machine LearningUse TLS to secure a web service through Azure Machine Learning](how-to-secure-web-service.md)
* [Usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Monitorare i modelli di Azure Machine Learning con Application InsightsMonitor your Azure Machine Learning models with Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
