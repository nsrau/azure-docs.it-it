---
title: Configurare un endpoint privato
titleSuffix: Azure Machine Learning
description: Usare il collegamento privato di Azure per accedere in modo sicuro all'area di lavoro di Azure Machine Learning da una rete virtuale.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.openlocfilehash: 89bad470d5ead43b79e3691343b53fff796f7abc
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92172789"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>Configurare il collegamento privato di Azure per un'area di lavoro Azure Machine Learning

In questo documento si apprenderà come usare il collegamento privato di Azure con l'area di lavoro Azure Machine Learning. Per informazioni sulla creazione di una rete virtuale per Azure Machine Learning, vedere [Panoramica dell'isolamento e della privacy della rete virtuale](how-to-network-security-overview.md)

Il collegamento privato di Azure consente di connettersi all'area di lavoro usando un endpoint privato. L'endpoint privato è un set di indirizzi IP privati all'interno della rete virtuale. È quindi possibile limitare l'accesso all'area di lavoro solo per gli indirizzi IP privati. Il collegamento privato consente di ridurre il rischio di exfiltration dei dati. Per altre informazioni sugli endpoint privati, vedere l'articolo [Collegamento privato di Azure](/azure/private-link/private-link-overview).

> [!IMPORTANT]
> Il collegamento privato di Azure non produce il piano di controllo di Azure (operazioni di gestione), ad esempio l'eliminazione dell'area di lavoro o la gestione delle risorse di calcolo. Ad esempio la creazione, l'aggiornamento o l'eliminazione di una destinazione di calcolo. Queste operazioni vengono eseguite sulla rete Internet pubblica come di consueto. Le operazioni del piano dati, ad esempio l'uso di Azure Machine Learning Studio, le API (incluse le pipeline pubblicate) o l'SDK usano l'endpoint privato.
>
> Se si usa Mozilla Firefox, è possibile che si verifichino problemi durante il tentativo di accedere all'endpoint privato per l'area di lavoro. Questo problema può essere correlato a DNS su HTTPS in Mozilla. È consigliabile usare Microsoft Edge di Google Chrome come soluzione alternativa.

## <a name="prerequisites"></a>Prerequisiti

Se si prevede di usare un'area di lavoro con collegamento privato abilitato con una chiave gestita dal cliente, è necessario richiedere questa funzionalità usando un ticket di supporto. Per altre informazioni, vedere [gestire e aumentare le quote](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

## <a name="limitations"></a>Limitazioni

L'uso di un'area di lavoro Azure Machine Learning con collegamento privato non è disponibile nelle aree di Azure per enti pubblici o Azure Cina 21Vianet.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Creare un'area di lavoro che usa un endpoint privato

Usare uno dei metodi seguenti per creare un'area di lavoro con un endpoint privato. Ognuno di questi metodi __richiede una rete virtuale esistente__:

> [!TIP]
> Se si desidera creare contemporaneamente un'area di lavoro, un endpoint privato e una rete virtuale, vedere [usare un modello di Azure Resource Manager per creare un'area di lavoro per Azure Machine Learning](how-to-create-workspace-template.md).

# <a name="python"></a>[Python](#tab/python)

Il Azure Machine Learning Python SDK fornisce la classe [PrivateEndpointConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) , che può essere usata con [Workspace. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) per creare un'area di lavoro con un endpoint privato. Questa classe richiede una rete virtuale esistente.

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.create(name='myworkspace',
    subscription_id='<my-subscription-id>',
    resource_group='myresourcegroup',
    location='eastus2',
    private_endpoint_config=pe,
    private_endpoint_auto_approval=True,
    show_output=True)
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

L' [estensione dell'interfaccia della riga di comando di Azure per Machine Learning](reference-azure-machine-learning-cli.md) include il comando [AZ ml Workspace create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_create) . È possibile utilizzare i seguenti parametri per questo comando per creare un'area di lavoro con una rete privata, ma è necessaria una rete virtuale esistente:

* `--pe-name`: Nome dell'endpoint privato creato.
* `--pe-auto-approval`: Se le connessioni all'area di lavoro dell'endpoint privato devono essere approvate automaticamente.
* `--pe-resource-group`: Il gruppo di risorse in cui creare l'endpoint privato. Deve essere lo stesso gruppo che contiene la rete virtuale.
* `--pe-vnet-name`: Rete virtuale esistente in cui creare l'endpoint privato.
* `--pe-subnet-name`: Nome della subnet in cui creare l'endpoint privato. Il valore predefinito è `default`.

# <a name="portal"></a>[Portale](#tab/azure-portal)

La scheda __rete__ in Azure Machine Learning Studio consente di configurare un endpoint privato. Tuttavia, richiede una rete virtuale esistente. Per altre informazioni, vedere [creare aree di lavoro nel portale](how-to-manage-workspace.md).

---

## <a name="add-a-private-endpoint-to-a-workspace"></a>Aggiungere un endpoint privato a un'area di lavoro

Usare uno dei metodi seguenti per aggiungere un endpoint privato a un'area di lavoro esistente:

> [!IMPORTANT]
>
> È necessario disporre di una rete virtuale esistente in cui creare l'endpoint privato. Prima di aggiungere l'endpoint privato, è inoltre necessario [disabilitare i criteri di rete per gli endpoint privati](../private-link/disable-private-endpoint-network-policy.md) .

> [!WARNING]
>
> Se sono presenti destinazioni di calcolo associate a questa area di lavoro e non si trovano dietro la stessa rete virtuale in cui viene creato l'endpoint privato, non funzioneranno.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.from_config()
ws.add_private_endpoint(private_endpoint_config=pe, private_endpoint_auto_approval=True, show_output=True)
```

Per ulteriori informazioni sulle classi e sui metodi utilizzati in questo esempio, vedere [PrivateEndpointConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) e [Workspace.add_private_endpoint](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#add-private-endpoint-private-endpoint-config--private-endpoint-auto-approval-true--location-none--show-output-true--tags-none-).

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

L' [estensione dell'interfaccia della riga di comando di Azure per Machine Learning](reference-azure-machine-learning-cli.md) include il comando [AZ ml Workspace private-endpoint Add](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace/private-endpoint?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_private_endpoint_add) .

```azurecli
az ml workspace private-endpoint add -w myworkspace  --pe-name myprivateendpoint --pe-auto-approval true --pe-vnet-name myvnet
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

Dall'area di lavoro Azure Machine Learning nel portale selezionare __connessioni endpoint privato__ e quindi selezionare __+ endpoint privato__. Usare i campi per creare un nuovo endpoint privato.

* Quando si seleziona l' __area__, selezionare la stessa area della rete virtuale. 
* Quando si seleziona il __tipo di risorsa__, utilizzare __Microsoft. MachineLearningServices/Workspaces__. 
* Impostare la __risorsa__ sul nome dell'area di lavoro.

Infine, selezionare __Crea__ per creare l'endpoint privato.

---

## <a name="remove-a-private-endpoint"></a>Rimuovere un endpoint privato

Usare uno dei metodi seguenti per rimuovere un endpoint privato da un'area di lavoro:

# <a name="python"></a>[Python](#tab/python)

Usare [Workspace.delete_private_endpoint_connection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#delete-private-endpoint-connection-private-endpoint-connection-name-) per rimuovere un endpoint privato.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
# get the connection name
_, _, connection_name = ws.get_details()['privateEndpointConnections'][0]['id'].rpartition('/')
ws.delete_private_endpoint_connection(private_endpoint_connection_name=connection_name)
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

L' [estensione dell'interfaccia della riga di comando di Azure per Machine Learning](reference-azure-machine-learning-cli.md) include il comando [AZ ml Workspace private-endpoint Delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace/private-endpoint?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_private_endpoint_delete) .

# <a name="portal"></a>[Portale](#tab/azure-portal)

Dall'area di lavoro Azure Machine Learning nel portale selezionare __connessioni a endpoint privati__, quindi selezionare l'endpoint che si vuole rimuovere. Infine, selezionare __Rimuovi__.

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>Uso di un'area di lavoro su un endpoint privato

Poiché la comunicazione con l'area di lavoro è consentita solo dalla rete virtuale, tutti gli ambienti di sviluppo che usano l'area di lavoro devono essere membri della rete virtuale. Ad esempio, una macchina virtuale nella rete virtuale.

> [!IMPORTANT]
> Per evitare l'interferenza temporanea della connettività, Microsoft consiglia di scaricare la cache DNS nei computer che si connettono all'area di lavoro dopo aver abilitato il collegamento privato. 

Per informazioni sulle macchine virtuali di Azure, vedere la [documentazione relativa alle macchine virtuali](/azure/virtual-machines/).


## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla protezione dell'area di lavoro Azure Machine Learning, vedere l'articolo [Panoramica dell'isolamento e della privacy della rete virtuale](how-to-network-security-overview.md) .

* Se si prevede di usare una soluzione DNS personalizzata nella rete virtuale, vedere [come usare un'area di lavoro con un server DNS personalizzato](how-to-custom-dns.md).
