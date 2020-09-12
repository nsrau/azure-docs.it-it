---
title: Proteggere gli ambienti di inferenza con le reti virtuali
titleSuffix: Azure Machine Learning
description: Usare una rete virtuale di Azure isolata per proteggere l'ambiente di Azure Machine Learning inferenza.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/16/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: 43d8211c7cbe5d785f6004157ff40c0bf9dfa788
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664583"
---
# <a name="secure-an-azure-machine-learning-inferencing-environment-with-virtual-networks"></a>Proteggere un ambiente di Azure Machine Learning inferenza con reti virtuali
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo si apprenderà come proteggere gli ambienti di inferenza con una rete virtuale in Azure Machine Learning.

Questo articolo fa parte di una serie in cinque parti che illustra come proteggere un flusso di lavoro Azure Machine Learning. È consigliabile leggere prima di tutto la [parte 1: Panoramica di VNet](how-to-network-security-overview.md) per comprendere prima l'architettura complessiva. 

Vedere gli altri articoli di questa serie:

[1. Panoramica](how-to-network-security-overview.md)  >  [di VNet proteggere l'area di lavoro](how-to-secure-workspace-vnet.md)  >  [3. Proteggere l'ambiente di training](how-to-secure-training-vnet.md)  >  **4. Proteggere l'ambiente di inferenza**  >  [5. Abilitare la funzionalità di studio](how-to-enable-studio-virtual-network.md)

Questo articolo illustra come proteggere le risorse di inferenza seguenti in una rete virtuale:
> [!div class="checklist"]
> - Cluster Azure Kubernetes Service (AKS) predefinito
> - Cluster AKS privato
> - Istanze di Azure Container


## <a name="prerequisites"></a>Prerequisiti

+ Vedere l'articolo [Panoramica della sicurezza di rete](how-to-network-security-overview.md) per comprendere gli scenari di rete virtuale comuni e l'architettura complessiva della rete virtuale.

+ Una rete virtuale e una subnet esistenti da usare con le risorse di calcolo.

+ Per distribuire le risorse in una rete virtuale o in una subnet, l'account utente deve avere le autorizzazioni per le azioni seguenti nei controlli degli accessi in base al ruolo (RBAC) di Azure:

    - "Microsoft. Network/virtualNetworks/join/Action" sulla risorsa di rete virtuale.
    - "Microsoft. Network/virtualNetworks/subnet/join/Action" sulla risorsa della subnet.

    Per ulteriori informazioni sul controllo degli accessi in base al ruolo con la rete, vedere [ruoli predefiniti di rete](/azure/role-based-access-control/built-in-roles#networking)

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Servizio Azure Kubernetes

Per usare un cluster AKS in una rete virtuale, è necessario soddisfare i requisiti di rete seguenti:

> [!div class="checklist"]
> * Seguire i prerequisiti in [configurare Advanced Networking in Azure Kubernetes Service (AKS)](../aks/configure-azure-cni.md#prerequisites).
> * L'istanza di AKS e la rete virtuale devono trovarsi nella stessa area. Se si proteggono gli account di archiviazione di Azure usati dall'area di lavoro in una rete virtuale, è necessario che si trovino nella stessa rete virtuale dell'istanza di AKS.


Per aggiungere AKS in una rete virtuale all'area di lavoro, seguire questa procedura:

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com/), quindi selezionare la sottoscrizione e l'area di lavoro.

1. Selezionare __Calcolo__ a sinistra.

1. Selezionare __Inference clusters__ (Cluster di inferenza) dal centro, quindi selezionare __+__ .

1. Nella finestra di dialogo __New Inference Cluster__ (Nuovo cluster di inferenza) selezionare __Avanzata__ in __Configurazione di rete__.

1. Per configurare questa risorsa di calcolo per l'uso di una rete virtuale, eseguire le operazioni seguenti:

    1. Nell'elenco a discesa __Gruppo di risorse__ selezionare il gruppo di risorse che contiene la rete virtuale.
    1. Selezionare la rete virtuale che contiene la subnet nell'elenco a discesa __Rete virtuale__.
    1. Selezionare la subnet nell'elenco a discesa __Subnet__.
    1. Immettere l'intervallo di indirizzi del servizio Kubernetes nella casella __Intervallo di indirizzi del servizio Kubernetes__. Questo intervallo di indirizzi usa un indirizzo IP in notazione CIDR (Classless Inter-Domain Routing) per definire gli indirizzi IP disponibili per il cluster. Non deve sovrapporsi a nessun intervallo IP della subnet, ad esempio 10.0.0.0/16.
    1. Nella casella __Indirizzo IP del servizio DNS di Kubernetes__ immettere l'indirizzo IP del servizio DNS di Kubernetes. Questo indirizzo IP viene assegnato al servizio DNS di Kubernetes. Deve essere compreso nell'intervallo di indirizzi del servizio Kubernetes, ad esempio 10.0.0.10.
    1. Nella casella __Indirizzo del bridge Docker__ immettere l'indirizzo del Bridge Docker. Questo indirizzo IP viene assegnato al bridge Docker. Non deve essere compreso in nessun intervallo IP della subnet o nell'intervallo di indirizzi del servizio Kubernetes, ad esempio 172.17.0.1/16.

   ![Azure Machine Learning: impostazioni della rete virtuale dell'ambiente di calcolo di Machine Learning](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Verificare che per il gruppo di sicurezza di rete che controlla la rete virtuale sia abilitata una regola di sicurezza in ingresso per l'endpoint di punteggio, in modo che possa essere chiamata dall'esterno della rete virtuale.
   > [!IMPORTANT]
   > Mantenere le regole in uscita predefinite per il gruppo di sicurezza di rete. Per altre informazioni, vedere le regole di sicurezza predefinite in [Gruppi di sicurezza](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

   [![Aggiungere una regola di sicurezza in ingresso](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

È anche possibile usare Azure Machine Learning SDK per aggiungere il servizio Azure Kubernetes in una rete virtuale. Se è già presente un cluster del servizio Azure Kubernetes in una rete virtuale, collegarlo all'area di lavoro come descritto nell'articolo relativo alla [distribuzione nel servizio Azure Kubernetes](how-to-deploy-and-where.md). Il codice seguente crea una nuova istanza del servizio Azure Kubernetes nella subnet `default` di una rete virtuale denominata `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

Al termine del processo di creazione, è possibile eseguire l'inferenza o il calcolo del modello in un cluster del servizio Azure Kubernetes in una rete virtuale. Per altre informazioni, vedere [Come eseguire la distribuzione nel servizio Azure Kubernetes](how-to-deploy-and-where.md).

## <a name="private-aks-cluster"></a>Cluster AKS privato

Per impostazione predefinita, i cluster AKS hanno un piano di controllo o un server API con indirizzi IP pubblici. È possibile configurare AKS per l'uso di un piano di controllo privato creando un cluster AKS privato. Per altre informazioni, vedere [creare un cluster di servizi Kubernetes di Azure privato](../aks/private-clusters.md).

Dopo aver creato il cluster AKS privato, [associare il cluster alla rete virtuale](how-to-create-attach-kubernetes.md) da usare con Azure Machine Learning.

## <a name="internal-aks-load-balancer"></a>Servizio di bilanciamento del carico interno AKS

Per impostazione predefinita, le distribuzioni AKS usano un servizio di [bilanciamento del carico pubblico](../aks/load-balancer-standard.md). In questa sezione viene illustrato come configurare AKS per l'uso di un servizio di bilanciamento del carico interno. Viene usato un servizio di bilanciamento del carico interno (o privato) in cui sono consentiti solo indirizzi IP privati come front-end. I bilanciamento del carico interno vengono usati per bilanciare il carico del traffico all'interno di una rete virtuale

Un servizio di bilanciamento del carico privato viene abilitato configurando AKS per l'uso di un servizio di _bilanciamento del carico interno_. 

### <a name="network-contributor-role"></a>Ruolo Collaboratore rete

> [!IMPORTANT]
> Se si crea o si connette un cluster AKS fornendo una rete virtuale creata in precedenza, è necessario concedere all'entità servizio (SP) o all'identità gestita per il cluster AKS il ruolo _collaboratore rete_ per il gruppo di risorse che contiene la rete virtuale. Questa operazione deve essere eseguita prima di provare a modificare il servizio di bilanciamento del carico interno in un indirizzo IP privato.
>
> Per aggiungere l'identità come collaboratore rete, attenersi alla procedura seguente:

1. Per trovare l'entità servizio o l'ID identità gestita per AKS, usare i seguenti comandi dell'interfaccia della riga di comando di Azure. Sostituire `<aks-cluster-name>` con il nome del cluster. Sostituire `<resource-group-name>` con il nome del gruppo di risorse che _contiene il cluster AKS_:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    Se questo comando restituisce un valore di `msi` , usare il comando seguente per identificare l'ID entità per l'identità gestita:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. Per trovare l'ID del gruppo di risorse che contiene la rete virtuale, usare il comando seguente. Sostituire `<resource-group-name>` con il nome del gruppo di risorse che _contiene la rete virtuale_:

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. Per aggiungere l'entità servizio o l'identità gestita come collaboratore alla rete, usare il comando seguente. Sostituire `<SP-or-managed-identity>` con l'ID restituito per l'entità servizio o l'identità gestita. Sostituire `<resource-group-id>` con l'ID restituito per il gruppo di risorse che contiene la rete virtuale:

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
Per altre informazioni sull'uso del bilanciamento del carico interno con il servizio Azure Kubernetes, vedere l'articolo sull'[uso del bilanciamento del carico interno con il servizio Azure Kubernetes](/azure/aks/internal-lb).

### <a name="enable-private-load-balancer"></a>Abilita bilanciamento del carico privato

> [!IMPORTANT]
> Non è possibile abilitare l'indirizzo IP privato durante la creazione del cluster del servizio Azure Kubernetes. Deve essere abilitato come aggiornamento a un cluster esistente.

Il frammento di codice seguente illustra come __creare un nuovo cluster del servizio Azure Kubernetes__ e quindi aggiornarlo per usare un bilanciamento del carico interno/IP privato:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__Interfaccia della riga di comando di Azure__

```azurecli-interactive
az rest --method put --uri https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace>/computes/<compute>?api-version=2018-11-19 --body @body.json
```

Il contenuto del file `body.json` a cui fa riferimento il comando è simile al documento JSON seguente:

```json
{ 
    "location": "<region>", 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-name>", 
        "computeType": "AKS", 
        "provisioningState": "Succeeded", 
        "properties": { 
            "loadBalancerType": "InternalLoadBalancer", 
            "agentCount": <agent-count>, 
            "agentVmSize": "vm-size", 
            "clusterFqdn": "<cluster-fqdn>" 
        } 
    } 
} 
```

Quando si __connette un cluster esistente__ all'area di lavoro, è necessario attendere fino al termine dell'operazione di connessione per configurare il servizio di bilanciamento del carico.

Per informazioni sul fissaggio di un cluster, vedere la pagina relativa alla [connessione di un cluster AKS esistente](how-to-create-attach-kubernetes.md).

Dopo aver collegato il cluster esistente, è possibile aggiornare il cluster per usare un indirizzo IP privato.

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute

# ws = workspace object. Creation not shown in this snippet
aks_target = AksCompute(ws,"myaks")

# Change to the name of the subnet that contains AKS
subnet_name = "default"
# Update AKS configuration to use an internal load balancer
update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
aks_target.update(update_config)
# Wait for the operation to complete
aks_target.wait_for_completion(show_output = True)
```

## <a name="enable-azure-container-instances-aci"></a>Abilitare istanze di contenitore di Azure (ACI)

Le Istanze di Azure Container vengono create dinamicamente quando si distribuisce un modello. Per abilitare Azure Machine Learning per la creazione di Istanze di Azure Container all'interno della rete virtuale, è necessario abilitare la __delega subnet__ per la subnet usata dalla distribuzione.

> [!WARNING]
> Quando si usano istanze di contenitore di Azure in una rete virtuale, la rete virtuale deve trovarsi nello stesso gruppo di risorse dell'area di lavoro Azure Machine Learning.
>
> Quando si usano istanze di contenitore di Azure all'interno della rete virtuale, il Container Registry di Azure (ACR) per l'area di lavoro non può trovarsi anche nella rete virtuale.

Per usare Istanze di Azure Container all'area di lavoro, seguire questa procedura:

1. Per abilitare la delega subnet nella rete virtuale, usare le informazioni contenute nell'articolo relativo ad [aggiunta e rimozione di una delega subnet](../virtual-network/manage-subnet-delegation.md). È possibile abilitare la delega quando si crea una rete virtuale o aggiungerla a una rete esistente.

    > [!IMPORTANT]
    > Quando si abilita la delega, usare `Microsoft.ContainerInstance/containerGroups` come valore di __Delega subnet a un servizio__.

2. Distribuire il modello usando [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-&preserve-view=true), usare i parametri `vnet_name` e `subnet_name`. Impostare questi parametri sul nome e sulla subnet della rete virtuale in cui è stata abilitata la delega.


## <a name="next-steps"></a>Passaggi successivi

Questo articolo è la terza parte di una serie di reti virtuali in quattro parti. Vedere il resto degli articoli per informazioni su come proteggere una rete virtuale:

* [Parte 1: Panoramica di rete virtuale](how-to-network-security-overview.md)
* [Parte 2: proteggere le risorse dell'area di lavoro](how-to-secure-workspace-vnet.md)
* [Parte 3: proteggere l'ambiente di training](how-to-secure-training-vnet.md)
* [Parte 5: abilitare la funzionalità di studio](how-to-enable-studio-virtual-network.md)