---
title: Creare e alleghi il servizio Azure Kubernetes
titleSuffix: Azure Machine Learning
description: Azure Kubernetes Service (AKS) può essere usato per distribuire un modello di machine learning come servizio Web. Informazioni su come creare un nuovo cluster AKS tramite Azure Machine Learning. Si apprenderà anche come alleghi un cluster AKS esistente all'area di lavoro Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/01/2020
ms.openlocfilehash: edd4cc28c6d59f1d6e0c9cabfd5855c72bd3fe73
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661837"
---
# <a name="create-and-attach-an-azure-kubernetes-service-cluster"></a>Creare e alleghi un cluster di servizi Kubernetes di Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning possibile distribuire modelli di apprendimento automatico sottoposti a training nel servizio Azure Kubernetes. Tuttavia, è necessario __creare__ prima di tutto un cluster Azure Kubernetes Service (AKS) dall'area di lavoro di Azure ml o __alleghi__ un cluster AKS esistente. Questo articolo fornisce informazioni sulla creazione e sul fissaggio di un cluster.

## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md).

- Estensione dell'interfaccia della riga [di comando di Azure per il servizio Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)o l' [estensione di Visual Studio code Azure Machine Learning](tutorial-setup-vscode-extension.md).

- Se si prevede di usare una rete virtuale di Azure per proteggere le comunicazioni tra l'area di lavoro di Azure ML e il cluster AKS, vedere l'articolo relativo all' [isolamento della rete durante il training & inferenza](how-to-enable-virtual-network.md) .

## <a name="limitations"></a>Limitazioni

- Se è necessario un **Load Balancer standard (SLB)** distribuito nel cluster anziché una Load Balancer di base (BLB), creare un cluster nel portale AKS/CLI/SDK e quindi **collegarlo** all'area di lavoro AML.

- Se si dispone di un criterio di Azure che limita la creazione di indirizzi IP pubblici, la creazione del cluster AKS avrà esito negativo. AKS richiede un indirizzo IP pubblico per il [traffico in uscita](/azure/aks/limit-egress-traffic). L'articolo sul traffico in uscita fornisce anche indicazioni per bloccare il traffico in uscita dal cluster tramite l'indirizzo IP pubblico, ad eccezione di alcuni nomi di dominio completi. Esistono due modi per abilitare un IP pubblico:
    - Il cluster può usare l'indirizzo IP pubblico creato per impostazione predefinita con BLB o SLB oppure
    - Il cluster può essere creato senza un indirizzo IP pubblico e quindi un indirizzo IP pubblico viene configurato con un firewall con una route definita dall'utente. Per altre informazioni, vedere [personalizzare l'uscita del cluster con una route definita dall'utente](/azure/aks/egress-outboundtype).
    
    Il piano di controllo AML non comunica con questo indirizzo IP pubblico. Comunica con il piano di controllo AKS per le distribuzioni. 

- Se si **connette** un cluster AKS per cui è [abilitato un intervallo di indirizzi IP autorizzati per accedere al server API](/azure/aks/api-server-authorized-ip-ranges), abilitare gli intervalli IP del piano di controllo AML per il cluster AKS. Il piano di controllo AML viene distribuito tra le aree abbinate e distribuisce i pod di inferenza nel cluster AKS. Senza l'accesso al server API, non è possibile distribuire i pod di inferenza. Usare gli [intervalli IP](https://www.microsoft.com/download/confirmation.aspx?id=56519) per entrambe le [aree abbinate](/azure/best-practices-availability-paired-regions) quando si abilitano gli intervalli IP in un cluster AKS.

    Gli intervalli IP autorizzati funzionano solo con Load Balancer Standard.

- Se si vuole usare un cluster del servizio contenitore di Azure privato (usando il collegamento privato di Azure), è necessario creare prima il cluster e quindi **collegarlo** all'area di lavoro. Per altre informazioni, vedere [creare un cluster di servizi Kubernetes di Azure privato](/azure/aks/private-clusters).

- Il nome di calcolo per il cluster AKS deve essere univoco all'interno dell'area di lavoro di Azure ML.
    - Il nome è obbligatorio e deve avere una lunghezza compresa tra 3 e 24 caratteri.
    - I caratteri validi sono lettere maiuscole e minuscole, cifre e il carattere.
    - Il nome deve iniziare con una lettera.
    - Il nome deve essere univoco in tutti i calcoli esistenti all'interno di un'area di Azure. Se il nome scelto non è univoco, verrà visualizzato un avviso.
   
 - Se si desidera distribuire modelli a nodi **GPU** o a nodi **FPGA** (o a qualsiasi SKU specifico), è necessario creare un cluster con lo SKU specifico. Non è disponibile alcun supporto per la creazione di un pool di nodi secondari in un cluster esistente e la distribuzione di modelli nel pool di nodi secondari.
 
- Quando si crea o si connette un cluster, è possibile scegliere se creare il cluster per __sviluppo/test__ o __produzione__. Se si vuole creare un cluster AKS per __lo sviluppo__, __la convalida__e il __test__ anziché la produzione, impostare lo __scopo del cluster__ su __dev-test__. Se non si specifica lo scopo del cluster, viene creato un cluster di __produzione__ . 

    > [!IMPORTANT]
    > Un cluster di __sviluppo e test__ non è adatto per il traffico a livello di produzione e può aumentare i tempi di inferenza. Anche i cluster di sviluppo/test non garantiscono la tolleranza di errore.

- Quando si crea o si collega un cluster, se il cluster verrà usato per la __produzione__, deve contenere almeno 12 __CPU virtuali__. Il numero di CPU virtuali può essere calcolato moltiplicando il __numero di nodi__ nel cluster per il __numero di core__ fornito dalle dimensioni della macchina virtuale selezionate. Se ad esempio si usano le dimensioni della macchina virtuale "Standard_D3_v2" con 4 core virtuali, è necessario selezionare 3 o una versione successiva come numero di nodi.

    Per un cluster di __sviluppo e test__ , si riordinano almeno 2 CPU virtuali.

- Il Azure Machine Learning SDK non fornisce il supporto per il ridimensionamento di un cluster AKS. Per ridimensionare i nodi del cluster, usare l'interfaccia utente per il cluster AKS in Azure Machine Learning Studio. È possibile modificare solo il numero di nodi, non le dimensioni della macchina virtuale del cluster. Per altre informazioni sul ridimensionamento dei nodi in un cluster AKS, vedere gli articoli seguenti:

    - [Ridimensionare manualmente il numero di nodi in un cluster AKS](../aks/scale-cluster.md)
    - [Configurare la scalabilità automatica del cluster in AKS](../aks/cluster-autoscaler.md)

## <a name="create-a-new-aks-cluster"></a>Creare un nuovo cluster AKS

**Tempo stimato**: circa 10 minuti.

La creazione o il fissaggio di un cluster AKS è un processo di tipo One-Time per l'area di lavoro. È possibile riutilizzare questo cluster per più distribuzioni. Se si elimina il cluster o il gruppo di risorse che lo contiene, è necessario creare un nuovo cluster la volta successiva che è necessario distribuire. È possibile collegare più cluster AKS all'area di lavoro.

L'esempio seguente illustra come creare un nuovo cluster AKS usando l'SDK e l'interfaccia della riga di comando:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

# Example configuration to use an existing virtual network
# prov_config.vnet_name = "mynetwork"
# prov_config.vnet_resourcegroup_name = "mygroup"
# prov_config.subnet_name = "default"
# prov_config.service_cidr = "10.0.0.0/16"
# prov_config.dns_service_ip = "10.0.0.10"
# prov_config.docker_bridge_cidr = "172.17.0.1/16"

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Per ulteriori informazioni sulle classi, i metodi e i parametri utilizzati in questo esempio, vedere i documenti di riferimento seguenti:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py&preserve-view=true)
* [AksCompute. provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget. wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks
```

Per ulteriori informazioni, vedere il riferimento [AZ ml computetarget create AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) .

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per informazioni sulla creazione di un cluster AKS nel portale, vedere [creare destinazioni di calcolo in Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="attach-an-existing-aks-cluster"></a>Alleghi un cluster AKS esistente

**Tempo stimato:** Circa 5 minuti.

Se si dispone già del cluster AKS nella sottoscrizione di Azure ed è la versione 1,17 o una versione precedente, è possibile usarla per distribuire l'immagine.

> [!TIP]
> Il cluster AKS esistente può trovarsi in un'area di Azure diversa dall'area di lavoro Azure Machine Learning.


> [!WARNING]
> Non creare più allegati simultanei nello stesso cluster AKS dall'area di lavoro. Ad esempio, se si connette un cluster AKS a un'area di lavoro usando due nomi diversi. Ogni nuovo allegato interromperà gli allegati esistenti precedenti.
>
> Se si vuole ricollegare un cluster AKS, ad esempio per modificare TLS o altre impostazioni di configurazione del cluster, è necessario innanzitutto rimuovere l'allegato esistente usando [AksCompute. Detach ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--).

Per altre informazioni sulla creazione di un cluster AKS con l'interfaccia della riga di comando di Azure o il portale, vedere gli articoli seguenti:

* [Creare un cluster del servizio Azure Kubernetes (interfaccia della riga di comando)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Creare un cluster AKS (portale)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)
* [Creare un cluster AKS (modello ARM nei modelli di avvio rapido di Azure)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

L'esempio seguente illustra come aggiungere un cluster AKS esistente all'area di lavoro:

# <a name="python"></a>[Python](#tab/python)

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

# Wait for the attach process to complete
aks_target.wait_for_completion(show_output = True)
```

Per ulteriori informazioni sulle classi, i metodi e i parametri utilizzati in questo esempio, vedere i documenti di riferimento seguenti:

* [AksCompute. attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py&preserve-view=true)
* [AksCompute. Connetti](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

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

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per informazioni sul fissaggio di un cluster AKS nel portale, vedere [creare destinazioni di calcolo in Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="next-steps"></a>Passaggi successivi

* [Come e dove distribuire un modello](how-to-deploy-and-where.md)
* [Distribuire un modello in un cluster del servizio Kubernetes di Azure](how-to-deploy-azure-kubernetes-service.md)