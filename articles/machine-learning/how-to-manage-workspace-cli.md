---
title: Creare aree di lavoro con l'interfaccia della riga di comando di Azure
titleSuffix: Azure Machine Learning
description: Informazioni su come usare l’interfaccia della riga di comando di Azure per creare una nuova area di lavoro di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 45f9f61712903436d63f483673705650f5470b3f
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92635950"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Creare un'area di lavoro per Azure Machine Learning con l'interfaccia della riga di comando di Azure


Questo articolo contiene informazioni su come creare un’area di lavoro di Azure Machine Learning usando l’interfaccia della riga di comando di Azure. L’interfaccia della riga di comando di Azure fornisce i comandi per la gestione delle risorse di Azure. L'estensione Machine Learning dell’interfaccia della riga di comando fornisce i comandi per l'uso delle risorse di Azure Machine Learning.

## <a name="prerequisites"></a>Prerequisiti

* Una **sottoscrizione di Azure** . Se non se ne possiede una, provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Per usare i comandi dell'interfaccia della riga di comando in questo documento dall' **ambiente locale** , è necessaria l' [interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

    Se si usa [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/), l'interfaccia della riga di comando è accessibile tramite il browser e si trova nel cloud.

## <a name="connect-the-cli-to-your-azure-subscription"></a>Connettere l'interfaccia della riga di comando alla sottoscrizione di Azure

> [!IMPORTANT]
> Se si usa Azure Cloud Shell, è possibile ignorare questa sezione. Cloud Shell esegue automaticamente l'autenticazione con l'account usato per accedere alla sottoscrizione di Azure.

È possibile eseguire l'autenticazione della sottoscrizione di Azure dall'interfaccia della riga di comando in diversi modi. Quello più semplice consiste nell'eseguire l'autenticazione interattiva tramite un browser. Per eseguire l'autenticazione in modo interattivo, aprire una riga di comando o un terminale e usare il comando seguente:

```azurecli-interactive
az login
```

Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso. In caso contrario, è necessario aprire un browser e seguire le istruzioni nella riga di comando. Le istruzioni prevedono l'individuazione di [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e l'immissione di un codice di autorizzazione.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

Per altri metodi di autenticazione, vedere [Accedere con l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true).

## <a name="install-the-machine-learning-extension"></a>Installare l'estensione Machine Learning

Per installare l'estensione Machine Learning, usare il comando seguente:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Creare un'area di lavoro

L'area di lavoro di Azure Machine Learning si basa sui servizi o sulle entità seguenti di Azure:

> [!IMPORTANT]
> Se non si specifica un servizio di Azure esistente, ne verrà creato uno automaticamente durante la creazione dell'area di lavoro. È necessario specificare sempre un gruppo di risorse. Quando si collega il proprio account di archiviazione, verificare che soddisfi i criteri seguenti:
>
> * L'account di archiviazione _non_ è un account premium (Premium_LRS e Premium_GRS)
> * Sia il BLOB di Azure che le funzionalità file di Azure sono abilitati
> * Lo spazio dei nomi gerarchico (ADLS gen 2) è disabilitato
>
> Questi requisiti sono solo per l'account di archiviazione _predefinito_ usato dall'area di lavoro.

| Service | Parametro per specificare un'istanza esistente |
| ---- | ---- |
| **Gruppo di risorse di Azure** | `-g <resource-group-name>`
| **Account di archiviazione di Azure** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Insieme di credenziali chiave Azure** | `--keyvault <service-id>` |
| **Registro Azure Container** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

L’area di lavoro di Azure Machine Learning deve essere creata all'interno di un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno nuovo. Per __creare un nuovo gruppo di risorse__ , usare il comando seguente. Sostituire `<resource-group-name>` con il nome da usare per questo gruppo di risorse. Sostituire `<location>` con l'area di Azure da usare per questo gruppo di risorse:

> [!TIP]
> È necessario selezionare un'area in cui è disponibile Azure Machine Learning. Per informazioni, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

La risposta di questo comando è simile al codice JSON seguente:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Per altre informazioni sull'uso dei gruppi di risorse, vedere [az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest&preserve-view=true).

### <a name="automatically-create-required-resources"></a>Creare automaticamente le risorse necessarie

Per creare una nuova area di lavoro in cui i __servizi vengono creati automaticamente__ , utilizzare il comando seguente:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> Il nome dell'area di lavoro non rileva la distinzione tra maiuscole e minuscole.

L'output di questo comando è simile al codice JSON seguente:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

### <a name="virtual-network-and-private-endpoint"></a>Rete virtuale e endpoint privato

> [!IMPORTANT]
> L'uso di un'area di lavoro Azure Machine Learning con collegamento privato non è disponibile nelle aree di Azure per enti pubblici o Azure Cina 21Vianet.

Se si vuole limitare l'accesso all'area di lavoro a una rete virtuale, è possibile usare i parametri seguenti:

* `--pe-name`: Nome dell'endpoint privato creato.
* `--pe-auto-approval`: Se le connessioni all'area di lavoro dell'endpoint privato devono essere approvate automaticamente.
* `--pe-resource-group`: Il gruppo di risorse in cui creare l'endpoint privato. Deve essere lo stesso gruppo che contiene la rete virtuale.
* `--pe-vnet-name`: Rete virtuale esistente in cui creare l'endpoint privato.
* `--pe-subnet-name`: Nome della subnet in cui creare l'endpoint privato. Il valore predefinito è `default`.

Per ulteriori informazioni sull'utilizzo di un endpoint privato e di una rete virtuale con l'area di lavoro, vedere [Panoramica dell'isolamento e della privacy della rete virtuale](how-to-network-security-overview.md).

### <a name="customer-managed-key-and-high-business-impact-workspace"></a>Area di lavoro della chiave gestita dal cliente e dell'elevata incidenza aziendale

Per impostazione predefinita, le metriche e i metadati per l'area di lavoro vengono archiviati in un'istanza di Azure Cosmos DB gestita da Microsoft. Questi dati vengono crittografati tramite chiavi gestite da Microsoft. 

Invece di usare la chiave gestita da Microsoft, è possibile usare la chiave specifica. In questo modo viene creata l'istanza Azure Cosmos DB che archivia le metriche e i metadati nella sottoscrizione di Azure. Usare il `--cmk-keyvault` parametro per specificare il Azure Key Vault che contiene la chiave e `--resource-cmk-uri` per specificare l'URL della chiave all'interno dell'insieme di credenziali.

Prima di usare `--cmk-keyvault` i `--resource-cmk-uri` parametri e, è prima necessario eseguire le azioni seguenti:

1. Autorizzare l' __App Machine Learning__ (in gestione delle identità e degli accessi) con le autorizzazioni di collaboratore per la sottoscrizione.
1. Seguire i passaggi in [configurare le chiavi gestite dal cliente](/azure/cosmos-db/how-to-setup-cmk) per:
    * Registrare il provider di Azure Cosmos DB
    * Creare e configurare un Azure Key Vault
    * Genera una chiave

Non è necessario creare manualmente l'istanza di Azure Cosmos DB, ne verrà creata una automaticamente durante la creazione dell'area di lavoro. Questa istanza di Azure Cosmos DB verrà creata in un gruppo di risorse distinto usando un nome basato su questo modello: `<your-resource-group-name>_<GUID>` .

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Per limitare i dati raccolti da Microsoft nell'area di lavoro, usare il `--hbi-workspace` parametro. 

> [!IMPORTANT]
> Quando si crea un'area di lavoro, è possibile selezionare un elevato effetto aziendale. Questa impostazione non può essere modificata dopo la creazione dell'area di lavoro.

Per ulteriori informazioni sulle chiavi gestite dal cliente e sull'area di lavoro di alto livello di business, vedere [sicurezza aziendale per Azure Machine Learning](concept-enterprise-security.md#encryption-at-rest).

### <a name="use-existing-resources"></a>Usare le risorse esistenti

Per creare un'area di lavoro che usa risorse esistenti, è necessario fornire l'ID per le risorse. Per ottenere l’ID per i servizi, usare i comandi seguenti:

> [!IMPORTANT]
> Non è necessario specificare tutte le risorse esistenti. È sufficiente specificarne minimo una. Ad esempio, è possibile specificare un account di archiviazione esistente e l'area di lavoro creerà le altre risorse.

+ **Account di archiviazione di Microsoft Azure** : `az storage account show --name <storage-account-name> --query "id"`

    La risposta da questo comando è simile al testo seguente ed è l'ID dell'account di archiviazione:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

    > [!IMPORTANT]
    > Se si vuole usare un account di archiviazione di Azure esistente, non può essere un account Premium (Premium_LRS e Premium_GRS). Non può inoltre avere uno spazio dei nomi gerarchico (usato con Azure Data Lake Storage Gen2). Con l'account di archiviazione _predefinito_ dell'area di lavoro non sono supportati né archiviazione Premium né spazio dei nomi gerarchico. È possibile usare archiviazione Premium o uno spazio dei nomi gerarchico con account di archiviazione _non predefiniti_ .

+ **Azure Application Insights** :

    1. Installare l'estensione Application Insights:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Ottenere l’ID del servizio Application Insights:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        La risposta da questo comando è simile al testo seguente ed è l'ID del servizio Application Insights:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault** : `az keyvault show --name <key-vault-name> --query "ID"`

    La risposta da questo comando è simile al testo seguente ed è l'ID dell'insieme di credenziali delle chiavi:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Registro Azure Container** : `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    La risposta da questo comando è simile al testo seguente ed è l'ID del registro contenitori:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > Il registro contenitori deve avere l'[account amministratore](/azure/container-registry/container-registry-authentication#admin-account) abilitato prima di poterlo usare con un'area di lavoro di Azure Machine Learning.

Quando si dispone degli ID per le risorse che si vuole usare con l'area di lavoro, usare il comando `az workspace create -w <workspace-name> -g <resource-group-name>` di base e aggiungere i parametri e gli ID per le risorse esistenti. Ad esempio, il comando seguente crea un'area di lavoro che usa un registro contenitori esistente:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

L'output di questo comando è simile al codice JSON seguente:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="list-workspaces"></a>Elenca aree di lavoro

Per elencare tutte le aree di lavoro per la sottoscrizione di Azure, usare il comando seguente:

```azurecli-interactive
az ml workspace list
```

L'output di questo comando è simile al codice JSON seguente:

```json
[
  {
    "resourceGroup": "myresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "myml"
  },
  {
    "resourceGroup": "anotherresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "anotherml"
  }
]
```

Per altre informazioni, vedere la documentazione sulla [creazione dell’area di lavoro di Azure Machine Learning](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-workspace-list).

## <a name="get-workspace-information"></a>Ottenere informazioni sull’area di lavoro

Per ottenere informazioni su un'area di lavoro, usare il comando seguente:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

L'output di questo comando è simile al codice JSON seguente:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Per altre informazioni, vedere la documentazione sulla [visualizzazione dell’area di lavoro di Azure Machine Learning](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-workspace-show).

## <a name="update-a-workspace"></a>Aggiornare un’area di lavoro

Per aggiornare un’area di lavoro, usare il comando seguente:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

L'output di questo comando è simile al codice JSON seguente:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Per altre informazioni, vedere la documentazione sull’[aggiornamento dell’area di lavoro di Azure Machine Learning](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-workspace-update).

## <a name="share-a-workspace-with-another-user"></a>Condividere un'area di lavoro con un altro utente

Per condividere un'area di lavoro con un altro utente nell’ambito della sottoscrizione, usare il comando seguente:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Per altre informazioni sul controllo degli accessi in base al ruolo di Azure (RBAC di Azure) con Azure Machine Learning, vedere [gestire utenti e ruoli](how-to-assign-roles.md).

Per altre informazioni, vedere la documentazione sulla [condivisione dell’area di lavoro di Azure Machine Learning](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-workspace-share).

## <a name="sync-keys-for-dependent-resources"></a>Sincronizzare le chiavi per le risorse dipendenti

Se si modificano le chiavi di accesso per una delle risorse usate dall'area di lavoro, la sincronizzazione dell'area di lavoro con la nuova chiave richiede circa un'ora. Per forzare la sincronizzazione immediata delle nuove chiavi da parte dell'area di lavoro, usare il comando seguente:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Per altre informazioni sulla modifica delle chiavi, vedere [Rigenerare le chiavi di accesso alle risorse di archiviazione](how-to-change-storage-access-key.md).

Per altre informazioni, vedere la documentazione sulla [sincronizzazione delle chiavi con l’area di lavoro di Azure Machine Learning](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-workspace-sync-keys).

## <a name="delete-a-workspace"></a>Eliminazione di un'area di lavoro

Per eliminare un'area di lavoro dopo che non è più necessaria, usare il comando seguente:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> L'eliminazione di un'area di lavoro non comporta l'eliminazione di Application Insights, dell'account di archiviazione, dell'insieme di credenziali delle chiavi o del registro contenitori usati dall'area di lavoro.

È anche possibile eliminare il gruppo di risorse che comporta l’eliminazione dell’area di lavoro e di tutte le altre risorse di Azure contenute al suo interno. Per eliminare il gruppo di risorse, usare il comando seguente:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Per altre informazioni, vedere la documentazione sull’[eliminazione dell’area di lavoro di Azure Machine Learning](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-workspace-delete).

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="resource-provider-errors"></a>Errori del provider di risorse

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Spostamento dell'area di lavoro

> [!WARNING]
> Lo spostamento dell’area di lavoro di Azure Machine Learning in una diversa sottoscrizione o della sottoscrizione proprietaria su un nuovo tenant non è supportato in quanto ciò può provocare errori.

### <a name="deleting-the-azure-container-registry"></a>Eliminazione del Registro Azure Container

L'area di lavoro di Azure Machine Learning usa il Registro Azure Container (ACR) per alcune operazioni. Verrà creata automaticamente un'istanza di ACR quando è necessaria per la prima volta.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'estensione dell'interfaccia della riga di comando di Azure per Machine Learning, vedere la documentazione di [Azure Machine Learning](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest&preserve-view=true).
