---
title: Come distribuire i modelli nel servizio Azure Kubernetes
titleSuffix: Azure Machine Learning
description: Informazioni su come distribuire i modelli di Azure Machine Learning come servizio Web usando il servizio Azure Kubernetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.openlocfilehash: c32560f7bb182ac347e9e5a71b53b57cf80fac38
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034619"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Distribuire un modello in un cluster del servizio Kubernetes di Azure

Informazioni su come usare Azure Machine Learning per distribuire un modello come servizio Web in Azure Kubernetes Service (AKS). Il servizio Azure Kubernetes è ideale per distribuzioni di produzione su vasta scala. Usare il servizio Azure Kubernetes se sono necessarie una o più delle funzionalità seguenti:

- __Tempo di risposta rapido__.
- __Scalabilità__ automatica del servizio distribuito.
- Opzioni di __accelerazione hardware__ quali GPU e FPGA (Field-Programmable Gate Array).

> [!IMPORTANT]
> Il ridimensionamento di cluster non viene fornito tramite l'SDK Azure Machine Learning. Per altre informazioni sul ridimensionamento dei nodi in un cluster AKS, vedere [ridimensionare il numero di nodi in un cluster AKS](../../aks/scale-cluster.md).

Quando si esegue la distribuzione nel servizio Azure Kubernetes, viene distribuito in un cluster AKS __connesso all'area di lavoro__. Esistono due modi per connettere un cluster AKS all'area di lavoro:

* Creare il cluster AKS usando il Azure Machine Learning SDK, l'interfaccia della riga di comando di Machine Learning, la [portale di Azure](https://portal.azure.com) o la [pagina di destinazione dell'area di lavoro (anteprima)](https://ml.azure.com). Questo processo connette automaticamente il cluster all'area di lavoro.
* Alleghi un cluster AKS esistente all'area di lavoro Azure Machine Learning. È possibile collegare un cluster usando il Azure Machine Learning SDK, l'interfaccia della riga di comando Machine Learning o il portale di Azure.

> [!IMPORTANT]
> Il processo di creazione o allegato è un'attività una sola volta. Quando un cluster AKS è connesso all'area di lavoro, è possibile usarlo per le distribuzioni. È possibile scollegare o eliminare il cluster AKS se non è più necessario. Una volta Detatched o eliminato, non sarà più possibile eseguire la distribuzione nel cluster.

## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md).

- Un modello di apprendimento automatico registrato nell'area di lavoro. Se non si dispone di un modello registrato, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

- Estensione dell'interfaccia della riga [di comando di Azure per il servizio Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)o l' [estensione di Visual Studio code Azure Machine Learning](how-to-vscode-tools.md).

- I frammenti di codice __Python__ in questo articolo presuppongono che siano impostate le variabili seguenti:

    * `ws`-Impostare sull'area di lavoro.
    * `model`: Impostare sul modello registrato.
    * `inference_config`-Impostare sulla configurazione di inferenza per il modello.

    Per ulteriori informazioni sull'impostazione di queste variabili, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

- I frammenti di codice dell' __interfaccia__ della riga di comando in questo articolo `inferenceconfig.json` presuppongono che sia stato creato un documento. Per ulteriori informazioni sulla creazione di questo documento, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

## <a name="create-a-new-aks-cluster"></a>Creare un nuovo cluster AKS

**Tempo stimato**: circa 20 minuti.

La creazione o il fissaggio di un cluster AKS è un processo di tipo One-Time per l'area di lavoro. È possibile riutilizzare questo cluster per più distribuzioni. Se si elimina il cluster o il gruppo di risorse che lo contiene, è necessario creare un nuovo cluster la volta successiva che è necessario distribuire. È possibile collegare più cluster AKS all'area di lavoro.

> [!TIP]
> Se si vuole proteggere il cluster AKS con una rete virtuale di Azure, è necessario creare prima la rete virtuale. Per altre informazioni, vedere la pagina relativa a [sperimentazione e inferenza sicure con rete virtuale di Azure](how-to-enable-virtual-network.md#aksvnet).

Se si vuole creare un cluster AKS per __lo sviluppo__, la __convalida__e il __test__ anziché l'ambiente di produzione, è possibile specificare lo __scopo del cluster__ per il __test__di sviluppo.

> [!WARNING]
> Se si imposta `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, il cluster creato non è adatto per il traffico a livello di produzione e può aumentare i tempi di inferenza. Anche i cluster di sviluppo/test non garantiscono la tolleranza di errore. Si consiglia almeno 2 CPU virtuali per i cluster di sviluppo/test.

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
> Per [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), se si scelgono valori personalizzati `agent_count` per `vm_size`e e `cluster_purpose` `DEV_TEST` nonè`agent_count` , è necessario assicurarsi che moltiplicato per siamaggioreougualea12CPUvirtuali.`vm_size` Se, ad esempio, si usa `vm_size` un di "Standard_D3_v2", che ha 4 CPU virtuali, è necessario scegliere una `agent_count` di 3 o una versione successiva.
>
> Il Azure Machine Learning SDK non fornisce il supporto per il ridimensionamento di un cluster AKS. Per ridimensionare i nodi del cluster, usare l'interfaccia utente per il cluster AKS nell'portale di Azure. È possibile modificare solo il numero di nodi, non le dimensioni della macchina virtuale del cluster.

Per ulteriori informazioni sulle classi, i metodi e i parametri utilizzati in questo esempio, vedere i documenti di riferimento seguenti:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.provisioning_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget. wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Uso dell'interfaccia della riga di comando**

```azurecli
az ml computetarget create aks -n myaks
```

Per ulteriori informazioni, vedere [AZ ml computetarget create Ask](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) Reference.

## <a name="attach-an-existing-aks-cluster"></a>Alleghi un cluster AKS esistente

**Tempo stimato:** Circa 5 minuti.

Se è già presente un cluster AKS nella sottoscrizione di Azure ed è la versione 1.12. # #, è possibile usarlo per distribuire l'immagine.

> [!TIP]
> Il cluster AKS esistente può trovarsi in un'area di Azure rispetto all'area di lavoro Azure Machine Learning.
>
> Se si vuole proteggere il cluster AKS con una rete virtuale di Azure, è necessario creare prima la rete virtuale. Per altre informazioni, vedere la pagina relativa a [sperimentazione e inferenza sicure con rete virtuale di Azure](how-to-enable-virtual-network.md#aksvnet).

> [!WARNING]
> Quando si connette un cluster AKS a un'area di lavoro, è possibile definire il modo in cui si userà il `cluster_purpose` cluster impostando il parametro.
>
> Se non si imposta il `cluster_purpose` parametro, o non si imposta `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, il cluster deve avere almeno 12 CPU virtuali disponibili.
>
> Se si imposta `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, il cluster non deve avere 12 CPU virtuali. Per lo sviluppo e il test sono consigliate almeno 2 CPU virtuali. Tuttavia, un cluster configurato per lo sviluppo/test non è adatto per il traffico a livello di produzione e può aumentare i tempi di inferenza. Anche i cluster di sviluppo/test non garantiscono la tolleranza di errore.

Per altre informazioni sulla creazione di un cluster AKS con l'interfaccia della riga di comando di Azure o il portale, vedere gli articoli seguenti:

* [Creare un cluster AKS (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Creare un cluster AKS (portale)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Gli esempi seguenti illustrano come alleghi un cluster AKS 1.12. # # esistente all'area di lavoro:

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

Per ulteriori informazioni sulle classi, i metodi e i parametri utilizzati in questo esempio, vedere i documenti di riferimento seguenti:

* [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute. Connetti](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**Uso dell'interfaccia della riga di comando**

Per alleghi un cluster esistente usando l'interfaccia della riga di comando, è necessario ottenere l'ID risorsa del cluster esistente. Per ottenere questo valore, utilizzare il comando seguente. Sostituire `myexistingcluster` con il nome del cluster AKS. Sostituire `myresourcegroup` con il gruppo di risorse che contiene il cluster:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Il comando restituisce un valore simile al testo seguente:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Per alleghi il cluster esistente all'area di lavoro, usare il comando seguente. Sostituire `aksresourceid` con il valore restituito dal comando precedente. Sostituire `myresourcegroup` con il gruppo di risorse che contiene l'area di lavoro. Sostituire `myworkspace` con il nome dell'area di lavoro.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Per ulteriori informazioni, vedere la Guida di riferimento al comando [AZ ml computetarget per l'associazione AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) .

## <a name="deploy-to-aks"></a>Distribuire in servizio Azure Kubernetes

Per distribuire un modello nel servizio Azure Kubernetes, creare una __configurazione di distribuzione__ che descriva le risorse di calcolo necessarie. Ad esempio, numero di core e memoria. È inoltre necessaria una __configurazione di inferenza__, che descrive l'ambiente necessario per ospitare il modello e il servizio Web. Per ulteriori informazioni sulla creazione della configurazione di inferenza, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

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

Per ulteriori informazioni sulle classi, i metodi e i parametri utilizzati in questo esempio, vedere i documenti di riferimento seguenti:

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Model. deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)
* [WebService. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Uso dell'interfaccia della riga di comando

Per eseguire la distribuzione usando l'interfaccia della riga di comando, usare il comando seguente. Sostituire `myaks` con il nome della destinazione di calcolo AKS. Sostituire `mymodel:1` con il nome e la versione del modello registrato. Sostituire `myservice` con il nome da assegnare al servizio:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aks-deploy-config.md)]

Per ulteriori informazioni, vedere il riferimento [AZ ml Model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) . 

### <a name="using-vs-code"></a>Uso di VS Code

Per informazioni sull'uso di VS Code, vedere [Deploy to AKS by the vs code Extension](how-to-vscode-tools.md#deploy-and-manage-models).

> [!IMPORTANT] 
> Per la distribuzione tramite VS Code è necessario che il cluster AKS venga creato o collegato all'area di lavoro in anticipo.

## <a name="web-service-authentication"></a>Autenticazione del servizio Web

Quando si esegue la distribuzione nel servizio Azure Kubernetes, l'autenticazione __basata su chiavi__ è abilitata per impostazione predefinita. È anche possibile abilitare l'autenticazione __basata su token__ . Per l'autenticazione basata su token è necessario che i client usino un account Azure Active Directory per richiedere un token di autenticazione, usato per eseguire richieste al servizio distribuito.

Per __disabilitare__ l'autenticazione, impostare `auth_enabled=False` il parametro durante la creazione della configurazione di distribuzione. Nell'esempio seguente viene disabilitata l'autenticazione tramite l'SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Per informazioni sull'autenticazione da un'applicazione client, vedere la pagina relativa all' [utilizzo di un modello di Azure machine learning distribuito come servizio Web](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Autenticazione con chiavi

Se è abilitata l'autenticazione della chiave, è `get_keys` possibile usare il metodo per recuperare una chiave di autenticazione primaria e secondaria:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se è necessario rigenerare una chiave, usare[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Autenticazione con token

Per abilitare l'autenticazione del token, `token_auth_enabled=True` impostare il parametro durante la creazione o l'aggiornamento di una distribuzione. Nell'esempio seguente viene abilitata l'autenticazione del token tramite l'SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Se l'autenticazione basata su token è abilitata, `get_token` è possibile usare il metodo per recuperare un token JWT e l'ora di scadenza del token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Sarà necessario richiedere un nuovo token dopo l' `refresh_by` ora del token.
>
> Microsoft consiglia di creare l'area di lavoro di Azure Machine Learning nella stessa area del cluster del servizio Azure Kubernetes. Per eseguire l'autenticazione con un token, il servizio Web effettuerà una chiamata all'area in cui viene creata l'area di lavoro Azure Machine Learning. Se l'area dell'area di lavoro non è disponibile, non sarà possibile recuperare un token per il servizio Web, anche se il cluster si trova in un'area diversa da quella dell'area di lavoro. In questo modo, l'autenticazione basata su token risulta non disponibile fino a quando l'area dell'area di lavoro non sarà nuovamente disponibile. Inoltre, maggiore è la distanza tra l'area del cluster e l'area dell'area di lavoro, più tempo sarà necessario per recuperare un token.

## <a name="update-the-web-service"></a>Aggiornare il servizio Web

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Sperimentazione e inferenza sicure in una rete virtuale](how-to-enable-virtual-network.md)
* [Come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md)
* [Risoluzione dei problemi di distribuzione](how-to-troubleshoot-deployment.md)
* [Proteggere i servizi Web di Azure Machine Learning con SSL](how-to-secure-web-service.md)
* [Usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Monitorare i modelli di Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
