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
ms.openlocfilehash: 4ba7ec73ac70723e21b6acad571d62d14edd250a
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828124"
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

Per creare un'area di lavoro con un endpoint privato, usare uno dei metodi seguenti:

> [!TIP]
> Il modello di Azure Resource Manager può creare una nuova rete virtuale, se necessario. Per tutti gli altri metodi è necessaria una rete virtuale esistente.

# <a name="resource-manager-template"></a>[Modello di Resource Manager](#tab/azure-resource-manager)

Il modello di Azure Resource Manager in [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) fornisce un modo semplice per creare un'area di lavoro con un endpoint privato e una rete virtuale.

Per informazioni sull'uso di questo modello, inclusi gli endpoint privati, vedere [usare un modello di Azure Resource Manager per creare un'area di lavoro per Azure Machine Learning](how-to-create-workspace-template.md).

# <a name="python"></a>[Python](#tab/python)

Il Azure Machine Learning Python SDK fornisce la classe [PrivateEndpointConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) , che può essere usata con [Workspace. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) per creare un'area di lavoro con un endpoint privato. Questa classe richiede una rete virtuale esistente.

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

## <a name="using-a-workspace-over-a-private-endpoint"></a>Uso di un'area di lavoro su un endpoint privato

Poiché la comunicazione con l'area di lavoro è consentita solo dalla rete virtuale, tutti gli ambienti di sviluppo che usano l'area di lavoro devono essere membri della rete virtuale. Ad esempio, una macchina virtuale nella rete virtuale.

> [!IMPORTANT]
> Per evitare l'interferenza temporanea della connettività, Microsoft consiglia di scaricare la cache DNS nei computer che si connettono all'area di lavoro dopo aver abilitato il collegamento privato. 

Per informazioni sulle macchine virtuali di Azure, vedere la [documentazione relativa alle macchine virtuali](/azure/virtual-machines/).


## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla protezione dell'area di lavoro Azure Machine Learning, vedere l'articolo [Panoramica dell'isolamento e della privacy della rete virtuale](how-to-network-security-overview.md) .

* Se si prevede di usare una soluzione DNS personalizzata nella rete virtuale, vedere [come usare un'area di lavoro con un server DNS personalizzato](how-to-custom-dns.md).
