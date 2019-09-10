---
title: Creare aree di lavoro con interfaccia della riga di comando di Azure
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per creare una nuova area di lavoro del servizio Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 08/30/2019
ms.openlocfilehash: f398eb8124f45562ebc3c4238c641a6638811394
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873487"
---
# <a name="create-a-workspace-for-azure-machine-learning-service-with-azure-cli"></a>Creare un'area di lavoro per Azure Machine Learning servizio con l'interfaccia della riga di comando

Questo articolo illustra come creare un'area di lavoro del servizio Azure Machine Learning usando l'interfaccia della riga di comando di Azure. L'interfaccia della riga di comando di Azure fornisce comandi per la gestione delle risorse di Azure L'estensione di machine learning per l'interfaccia della riga di comando fornisce comandi per l'utilizzo delle risorse del servizio Azure Machine Learning.

## <a name="prerequisites"></a>Prerequisiti

* Una **sottoscrizione di Azure**. Se non se ne possiede una, provare la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

* Per usare i comandi dell'interfaccia della riga di comando in questo documento dall' **ambiente locale**, è necessaria l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Se si usa il [Azure cloud Shell](https://azure.microsoft.com//features/cloud-shell/), l'interfaccia della riga di comando è accessibile tramite il browser e vive nel cloud.

## <a name="connect-the-cli-to-your-azure-subscription"></a>Connettere l'interfaccia della riga di comando alla sottoscrizione di Azure

> [!IMPORTANT]
> Se si usa il Azure Cloud Shell, è possibile ignorare questa sezione. Cloud Shell esegue automaticamente l'autenticazione con l'account usato per accedere alla sottoscrizione di Azure.

Sono disponibili diversi modi per eseguire l'autenticazione alla sottoscrizione di Azure dall'interfaccia della riga di comando. Il livello più semplice consiste nell'eseguire l'autenticazione interattiva tramite un browser. Per eseguire l'autenticazione in modo interattivo, aprire una riga di comando o un terminale e usare il comando seguente:

```azurecli
az login
```

Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso. In caso contrario, è necessario aprire un browser e seguire le istruzioni nella riga di comando. Le istruzioni prevedono [https://aka.ms/devicelogin](https://aka.ms/devicelogin) l'esplorazione e l'immissione di un codice di autorizzazione.

Per altri metodi di autenticazione, vedere [accedere con l'interfaccia](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)della riga di comando di Azure.

## <a name="install-the-machine-learning-extension"></a>Installare l'estensione di Machine Learning

Per installare l'estensione di Machine Learning, usare il comando seguente:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Creare un'area di lavoro

L'area di lavoro del servizio Azure Machine Learning si basa sui servizi o sulle entità di Azure seguenti:

> [!IMPORTANT]
> Se non si specifica un servizio di Azure esistente, ne verrà creato uno automaticamente durante la creazione dell'area di lavoro. È sempre necessario specificare un gruppo di risorse.

| Service | Parametro per specificare un'istanza esistente |
| ---- | ---- |
| **Gruppo di risorse di Azure** | `-g <resource-group-name>`
| **Account di archiviazione di Azure** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Insieme di credenziali chiave Azure** | `--keyvault <service-id>` |
| **Registro Azure Container** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

È necessario creare l'area di lavoro del servizio Azure Machine Learning all'interno di un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno nuovo. Per __creare un nuovo gruppo di risorse__, usare il comando seguente. Sostituire `<resource-group-name>` con il nome da usare per questo gruppo di risorse. Sostituire `<location>` con l'area di Azure da usare per questo gruppo di risorse:

> [!TIP]
> È necessario selezionare un'area in cui è disponibile il servizio Azure Machine Learning. Per informazioni, vedere [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

La risposta da questo comando è simile al codice JSON seguente:

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

Per ulteriori informazioni sull'utilizzo dei gruppi di risorse, vedere [AZ Group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

### <a name="automatically-create-required-resources"></a>Crea automaticamente le risorse necessarie

Per creare una nuova area di lavoro in cui __vengono creati automaticamente i servizi__, usare il comando seguente:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
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

### <a name="use-existing-resources"></a>Usare le risorse esistenti

Per creare un'area di lavoro che usa risorse esistenti, è necessario fornire l'ID per le risorse. Usare i comandi seguenti per ottenere l'ID per i servizi:

> [!IMPORTANT]
> Non è necessario specificare tutte le risorse esistenti. È possibile specificare uno o più. Ad esempio, è possibile specificare un account di archiviazione esistente e l'area di lavoro creerà le altre risorse.

+ **Account di archiviazione di Azure**:`az storage account show --name <storage-account-name> --query "id"`

    La risposta da questo comando è simile al testo seguente ed è l'ID dell'account di archiviazione:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

+ **Azure Application Insights**:

    1. Installare l'estensione di Application Insights:

        ```bash
        az extension add -n application-insights
        ```

    2. Ottenere l'ID del servizio Application Insights:

        ```bash
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        La risposta da questo comando è simile al testo seguente ed è l'ID per il servizio Application Insights:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**:' AZ Key Vault show--name < Key-Vault-Name >--query "ID"

    La risposta da questo comando è simile al testo seguente ed è l'ID dell'insieme di credenziali delle chiavi:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Container Registry di Azure**:`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    La risposta da questo comando è simile al testo seguente ed è l'ID per il registro contenitori:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > Il registro contenitori deve avere l' [account amministratore](/azure/container-registry/container-registry-authentication#admin-account) abilitato prima di poterlo usare con un'area di lavoro del servizio Azure Machine Learning.

Quando si hanno gli ID per le risorse che si vuole usare con l'area di lavoro, usare il comando di base `az workspace create -w <workspace-name> -g <resource-group-name>` e aggiungere i parametri e gli ID per le risorse esistenti. Ad esempio, il comando seguente crea un'area di lavoro che usa un registro contenitori esistente:

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

Per ulteriori informazioni, vedere la documentazione relativa all' [elenco delle aree di lavoro AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list) .

## <a name="get-workspace-information"></a>Ottenere le informazioni sull'area di lavoro

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

Per ulteriori informazioni, vedere la documentazione relativa all' [area di lavoro AZ ml Show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show) .

## <a name="update-a-workspace"></a>Aggiornare un'area di lavoro

Per aggiornare un'area di lavoro, usare il comando seguente:

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

Per ulteriori informazioni, vedere la documentazione relativa all' [aggiornamento dell'area di lavoro AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update) .

## <a name="share-a-workspace-with-another-user"></a>Condividere un'area di lavoro con un altro utente

Per condividere un'area di lavoro con un altro utente nella sottoscrizione, usare il comando seguente:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Per ulteriori informazioni sul controllo degli accessi in base al ruolo (RBAC) con Azure Machine Learning servizio, vedere [gestire utenti e ruoli](how-to-assign-roles.md).

Per ulteriori informazioni, vedere la documentazione di [condivisione dell'area di lavoro AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share) .

## <a name="sync-keys-for-dependent-resources"></a>Sincronizzare le chiavi per le risorse dipendenti

Se si modificano le chiavi di accesso per una delle risorse usate dall'area di lavoro, usare il comando seguente per sincronizzare le nuove chiavi con l'area di lavoro:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Per ulteriori informazioni sulla modifica delle chiavi, vedere [rigenerazione delle chiavi di accesso alle archiviazione](how-to-change-storage-access-key.md).

Per ulteriori informazioni, vedere la documentazione [AZ ml Workspace Sync-Keys](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys) .

## <a name="delete-a-workspace"></a>Eliminazione di un'area di lavoro

Per eliminare un'area di lavoro dopo che non è più necessaria, utilizzare il comando seguente:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> L'eliminazione di un'area di lavoro non comporta l'eliminazione dell'applicazione, dell'account di archiviazione, dell'insieme di credenziali delle chiavi o del registro contenitori usato dall'area di lavoro.

È anche possibile eliminare il gruppo di risorse, che elimina l'area di lavoro e tutte le altre risorse di Azure nel gruppo di risorse. Per eliminare il gruppo di risorse, usare il comando seguente:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Per ulteriori informazioni, vedere la documentazione [AZ ml workspace delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete) .

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'estensione dell'interfaccia della riga di comando di Azure per Machine Learning, vedere la documentazione [AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest) .
