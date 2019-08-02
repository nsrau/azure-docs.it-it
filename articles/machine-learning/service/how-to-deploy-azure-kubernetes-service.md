---
title: Come distribuire i modelli nel servizio Azure Kubernetes
titleSuffix: Azure Machine Learning service
description: Informazioni su come distribuire i modelli di servizio Azure Machine Learning come servizio Web usando il servizio Azure Kubernetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.openlocfilehash: deb6482c0419a5872ccf86f0014adbecc7be6c9d
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68694397"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Distribuire un modello in un cluster del servizio Kubernetes di Azure

Informazioni su come usare il servizio Azure Machine Learning per distribuire un modello come servizio Web in Azure Kubernetes Service (AKS). Il servizio Azure Kubernetes è ideale per distribuzioni di produzione su vasta scala. Usare il servizio Azure Kubernetes se sono necessarie una o più delle funzionalità seguenti:

- __Tempo di risposta rapido__.
- __Scalabilità__ automatica del servizio distribuito.
- Opzioni di __accelerazione hardware__ quali GPU e FPGA (Field-Programmable Gate Array).

> [!IMPORTANT]
> Il ridimensionamento di cluster non viene fornito tramite l'SDK Azure Machine Learning. Per altre informazioni sul ridimensionamento dei nodi in un cluster AKS, vedere [ridimensionare il numero di nodi in un cluster AKS](../../aks/scale-cluster.md).

Quando si esegue la distribuzione nel servizio Azure Kubernetes, viene distribuito in un cluster AKS __connesso all'area di lavoro__. Esistono due modi per connettere un cluster AKS all'area di lavoro:

* Creare il cluster AKS usando Azure Machine Learning Service SDK, l'interfaccia della riga di comando di Machine Learning o il portale di Azure. Questo processo connette automaticamente il cluster all'area di lavoro.
* Alleghi un cluster AKS esistente all'area di lavoro del servizio Azure Machine Learning. È possibile collegare un cluster usando Azure Machine Learning Service SDK, Machine Learning CLI o il portale di Azure.

> [!IMPORTANT]
> Il processo di creazione o allegato è un'attività una sola volta. Quando un cluster AKS è connesso all'area di lavoro, è possibile usarlo per le distribuzioni. È possibile scollegare o eliminare il cluster AKS se non è più necessario. Una volta Detatched o eliminato, non sarà più possibile eseguire la distribuzione nel cluster.

## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro del servizio Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro del servizio Azure Machine Learning](setup-create-workspace.md).

- Un modello di apprendimento automatico registrato nell'area di lavoro. Se non si dispone di un modello registrato, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

- Estensione dell'interfaccia della riga [di comando di Azure per il servizio Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://aka.ms/aml-sdk)o l' [estensione di Visual Studio code Azure Machine Learning](how-to-vscode-tools.md).

- I frammenti di codice __Python__ in questo articolo presuppongono che siano impostate le variabili seguenti:

    * `ws`-Impostare sull'area di lavoro.
    * `model`: Impostare sul modello registrato.
    * `inference_config`-Impostare sulla configurazione di inferenza per il modello.

    Per ulteriori informazioni sull'impostazione di queste variabili, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

- I frammenti di codice dell' __interfaccia__ della riga di comando in questo articolo `inferenceconfig.json` presuppongono che sia stato creato un documento. Per ulteriori informazioni sulla creazione di questo documento, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

## <a name="create-a-new-aks-cluster"></a>Creare un nuovo cluster AKS

**Tempo stimato**: circa 20 minuti.

La creazione o il fissaggio di un cluster AKS è un processo di tipo One-Time per l'area di lavoro. È possibile riutilizzare questo cluster per più distribuzioni. Se si elimina il cluster o il gruppo di risorse che lo contiene, è necessario creare un nuovo cluster la volta successiva che è necessario distribuire. È possibile collegare più cluster AKS all'area di lavoro.

Se si vuole creare un cluster AKS per __lo sviluppo__, la __convalida__e il __test__ anziché l'ambiente di produzione, è possibile specificare lo __scopo del cluster__ per il __test__di sviluppo.

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
> Per [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), se si selezionano valori personalizzati per agent_count e vm_size, è necessario verificare che il valore di agent_count moltiplicato per il valore di vm_size sia maggiore o uguale a 12 CPU virtuali. Ad esempio, se come vm_size si usa "Standard_D3_v2", che dispone di 4 CPU virtuali, per agent_count è consigliabile selezionare 3 o un valore superiore.
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
> Il cluster AKS esistente può trovarsi in un'area di Azure rispetto all'area di lavoro del servizio Azure Machine Learning.

> [!WARNING]
> Quando si connette un cluster AKS a un'area di lavoro, è possibile definire il modo in cui si userà il `cluster_purpose` cluster impostando il parametro.
>
> Se non si imposta il `cluster_purpose` parametro, o non si imposta `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, il cluster deve avere almeno 12 CPU virtuali disponibili.
>
> Se si imposta `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, il cluster non deve avere 12 CPU virtuali. Un cluster configurato per lo sviluppo/test non sarà tuttavia adatto per il traffico a livello di produzione e potrebbe aumentare i tempi di inferenza.

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

Per distribuire un modello nel servizio Azure Kubernetes, creare una __configurazione di distribuzione__ che descriva le risorse di calcolo necessarie. Ad esempio, numero di core e memoria. È inoltre necessaria una __configurazione__di inferenza, che descrive l'ambiente necessario per ospitare il modello e il servizio Web. Per ulteriori informazioni sulla creazione della configurazione di inferenza, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

### <a name="using-the-sdk"></a>Uso dell'SDK

```python
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

## <a name="using-vs-code"></a>Utilizzo di VS Code

Per informazioni sull'uso di VS Code, vedere [Deploy to AKS by the vs code Extension](how-to-vscode-tools.md#deploy-and-manage-models).

> [!IMPORTANT] 
> Per la distribuzione tramite VS Code è necessario che il cluster AKS venga creato o collegato all'area di lavoro in anticipo.

## <a name="update-the-web-service"></a>Aggiornare il servizio Web

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md)
* [Risoluzione dei problemi di distribuzione](how-to-troubleshoot-deployment.md)
* [Proteggere i servizi Web di Azure Machine Learning con SSL](how-to-secure-web-service.md)
* [Usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Monitorare i modelli di Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
