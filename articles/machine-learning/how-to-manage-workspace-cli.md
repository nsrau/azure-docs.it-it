---
title: Creare aree di lavoro con l'interfaccia della riga di comando di AzureCreate workspaces with Azure
titleSuffix: Azure Machine Learning
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per creare una nuova area di lavoro di Azure Machine Learning.Learn how to use the Azure CLI to create a new Azure Machine Learning workspace.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.openlocfilehash: 64c2e77ffc43ec98c13ce8c0cad002cdb2053241
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296911"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Creare un'area di lavoro per Azure Machine Learning con l'interfaccia della riga di comando di AzureCreate a workspace for Azure Machine Learning with Azure CLI
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In this article, you learn how to create an Azure Machine Learning workspace using the Azure CLI. L'interfaccia della riga di comando di Azure fornisce comandi per la gestione delle risorse di Azure.The Azure CLI provides commands for managing Azure resources. L'estensione di apprendimento automatico all'interfaccia della riga di comando fornisce comandi per l'uso delle risorse di Azure Machine Learning.The machine learning extension to the CLI provides commands for working with Azure Machine Learning resources.

## <a name="prerequisites"></a>Prerequisiti

* Una **sottoscrizione di Azure.** Se non si dispone di uno, provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Per usare i comandi dell'interfaccia della riga di comando in questo documento **dall'ambiente locale,** è necessario l'interfaccia della riga di comando di [Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

    Se si usa [Azure Cloud Shell,](https://azure.microsoft.com//features/cloud-shell/)l'interfaccia della riga di comando avviene tramite il browser e si trova nel cloud.

## <a name="connect-the-cli-to-your-azure-subscription"></a>Connettere l'interfaccia della riga di comando alla sottoscrizione di Azure

> [!IMPORTANT]
> Se si usa Azure Cloud Shell, è possibile ignorare questa sezione. La shell cloud autentica automaticamente l'utente usando l'account che si accede alla sottoscrizione di Azure.The cloud shell automatically authenticates you using the account you log into your Azure subscription.

Esistono diversi modi per eseguire l'autenticazione alla sottoscrizione di Azure dall'interfaccia della riga di comando. Il più semplice è quello di autenticare in modo interattivo utilizzando un browser. Per eseguire l'autenticazione interattiva, aprire una riga di comando o un terminale e utilizzare il comando seguente:

```azurecli-interactive
az login
```

Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso. In caso contrario, è necessario aprire un browser e seguire le istruzioni nella riga di comando. Le istruzioni prevedono la navigazione [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e l'immissione di un codice di autorizzazione.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

Per altri metodi di autenticazione, vedere [Accedere con l'interfaccia della riga](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)di comando di Azure.

## <a name="install-the-machine-learning-extension"></a>Installare l'estensione di apprendimento automaticoInstall the machine learning extension

Per installare l'estensione di apprendimento automatico, usare il comando seguente:To install the machine learning extension, use the following command:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Creare un'area di lavoro

L'area di lavoro di Azure Machine Learning si basa sui servizi o sulle entità di Azure seguenti:The Azure Machine Learning workspace relies on the following Azure services or entities:

> [!IMPORTANT]
> Se non si specifica un servizio di Azure esistente, ne verrà creato uno automaticamente durante la creazione dell'area di lavoro. È necessario specificare sempre un gruppo di risorse.

| Service | Parametro per specificare un'istanza esistenteParameter to specify an existing instance |
| ---- | ---- |
| **Gruppo di risorse di AzureAzure resource group** | `-g <resource-group-name>`
| **Account di archiviazione di AzureAzure Storage Account** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Archivio chiavi di AzureAzure Key Vault** | `--keyvault <service-id>` |
| **Registro di sistema del contenitore di AzureAzure Container** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

L'area di lavoro di Azure Machine Learning deve essere creata all'interno di un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno nuovo. Per __creare un nuovo gruppo di risorse,__ utilizzare il comando seguente. Sostituire `<resource-group-name>` con il nome da usare per questo gruppo di risorse. Sostituire `<location>` con l'area di Azure da usare per questo gruppo di risorse:Replace with the Azure region to use for this resource group:

> [!TIP]
> È necessario selezionare un'area in cui è disponibile Azure Machine Learning.You should select a region where Azure Machine Learning is available. Per informazioni, consultate [Prodotti disponibili per regione.](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

La risposta da questo comando è simile al seguente JSON:

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

Per altre informazioni sull'uso dei gruppi di risorse, vedere [az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

### <a name="automatically-create-required-resources"></a>Creare automaticamente le risorse necessarie

Per creare una nuova area di lavoro in cui i __servizi vengono creati automaticamente,__ utilizzare il comando seguente:

> [!TIP]
> I comandi in questa sezione creano un'area di lavoro edizione di base. Per creare un'area `--sku enterprise` di lavoro `az ml workspace create` dell'organizzazione, utilizzare l'opzione con il comando . Per altre informazioni sulle edizioni di Azure Machine Learning, vedere [Che cos'è Azure Machine Learning.](overview-what-is-azure-ml.md#sku)

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> Il nome dell'area di lavoro non fa distinzione tra maiuscole e minuscole.

L'output di questo comando è simile al seguente JSON:

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

Per creare un'area di lavoro che utilizza risorse esistenti, è necessario fornire l'ID per le risorse. Utilizzare i comandi seguenti per ottenere l'ID per i servizi:

> [!IMPORTANT]
> Non è necessario specificare tutte le risorse esistenti. È possibile specificarne uno o più. Ad esempio, è possibile specificare un account di archiviazione esistente e l'area di lavoro creerà le altre risorse.

+ **Account di archiviazione di Azure:**`az storage account show --name <storage-account-name> --query "id"`

    La risposta di questo comando è simile al testo seguente ed è l'ID dell'account di archiviazione:The response from this command is similar to the following text, and is the ID for your storage account:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

+ **Azure Application Insights**:

    1. Installare l'estensione di Application Insights:Install the application insights extension:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Ottenere l'ID del servizio di informazioni dettagliate sull'applicazione:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        La risposta di questo comando è simile al testo seguente ed è l'ID per il servizio di Application Insights:The response from this command is similar to the following text, and is the ID for your application insights service:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Archivio chiavi di Azure:Azure Key Vault:**`az keyvault show --name <key-vault-name> --query "ID"`

    La risposta di questo comando è simile al testo seguente ed è l'ID dell'insieme di credenziali delle chiavi:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Registro contenitori di Azure:**`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    La risposta da questo comando è simile al testo seguente ed è l'ID per il Registro di sistema del contenitore:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > Il registro contenitori deve avere [l'account amministratore](/azure/container-registry/container-registry-authentication#admin-account) abilitato prima di poter essere usato con un'area di lavoro di Azure Machine Learning.The container registry must have the admin account enabled before it can be used with an Azure Machine Learning workspace.

Dopo aver creato gli ID per le risorse che si desidera utilizzare con `az workspace create -w <workspace-name> -g <resource-group-name>` l'area di lavoro, utilizzare il comando base e aggiungere i parametri e gli ID per le risorse esistenti. Ad esempio, il comando seguente crea un'area di lavoro che utilizza un registro contenitori esistente:For example, the following command creates a workspace that uses an existing container registry:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

L'output di questo comando è simile al seguente JSON:

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

## <a name="list-workspaces"></a>Aree di lavoro elenco

Per elencare tutte le aree di lavoro per la sottoscrizione di Azure, usare il comando seguente:To list all the workspaces for your Azure subscription, use the following command:

```azurecli-interactive
az ml workspace list
```

L'output di questo comando è simile al seguente JSON:

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

Per altre informazioni, vedere la documentazione [dell'elenco dell'area di lavoro di az ml.For](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list) more information, see the az ml workspace list documentation.

## <a name="get-workspace-information"></a>Ottenere informazioni sull'area di lavoroGet workspace information

Per ottenere informazioni su un'area di lavoro, utilizzare il comando seguente:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

L'output di questo comando è simile al seguente JSON:

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

Per altre informazioni, vedere la documentazione relativa alla [presentazione dell'area di lavoro az ml.For](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show) more information, see the az ml workspace show documentation.

## <a name="update-a-workspace"></a>Aggiornare un'area di lavoro

Per aggiornare un'area di lavoro, utilizzare il comando seguente:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

L'output di questo comando è simile al seguente JSON:

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

Per ulteriori informazioni, vedere la documentazione relativa [all'aggiornamento dell'area di lavoro az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update)

## <a name="share-a-workspace-with-another-user"></a>Condividere un'area di lavoro con un altro utente

Per condividere un'area di lavoro con un altro utente nella sottoscrizione, usare il comando seguente:To share a workspace with another user on your subscription, use the following command:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Per altre informazioni sul controllo degli accessi in base al ruolo con Azure Machine Learning, vedere [Gestire utenti e ruoli.](how-to-assign-roles.md)

Per altre informazioni, vedere la documentazione relativa alla [condivisione dell'area di lavoro az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share)

## <a name="sync-keys-for-dependent-resources"></a>Sincronizzare le chiavi per le risorse dipendenti

Se si modificano i tasti di scelta per una delle risorse utilizzate dall'area di lavoro, utilizzare il comando seguente per sincronizzare le nuove chiavi con l'area di lavoro:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Per ulteriori informazioni sulla modifica delle chiavi, vedere [Rigenerare le chiavi di accesso all'archiviazione.](how-to-change-storage-access-key.md)

Per altre informazioni, vedere la documentazione relativa alle [chiavi di sincronizzazione dell'area di lavoro az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys)

## <a name="delete-a-workspace"></a>Eliminazione di un'area di lavoro

Per eliminare un'area di lavoro quando non è più necessaria, utilizzare il comando seguente:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> L'eliminazione di un'area di lavoro non comporta l'eliminazione delle informazioni dettagliate sull'applicazione, dell'account di archiviazione, dell'insieme di credenziali delle chiavi o del registro contenitori usati dall'area di lavoro.

È anche possibile eliminare il gruppo di risorse, che elimina l'area di lavoro e tutte le altre risorse di Azure nel gruppo di risorse. Per eliminare il gruppo di risorse, usare il comando seguente:To delete the resource group, use the following command:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Per ulteriori informazioni, vedere la documentazione [relativa all'eliminazione dell'area di lavoro az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete)

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="resource-provider-errors"></a>Errori del provider di risorse

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Spostamento dell'area di lavoro

> [!WARNING]
> Lo spostamento dell'area di lavoro di Azure Machine Learning in una sottoscrizione diversa o lo spostamento della sottoscrizione proprietaria in un nuovo tenant non sono supportati. Questa operazione può causare errori.

### <a name="deleting-the-azure-container-registry"></a>Eliminazione del Registro di sistema del contenitore di AzureDeleting the Azure Container Registry

L'area di lavoro di Azure Machine Learning usa il Registro di sistema del contenitore di Azure (ACR) per alcune operazioni. Verrà creata automaticamente un'istanza ACR quando ne ha bisogno per la prima volta.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'estensione dell'interfaccia della riga di comando di Azure per l'apprendimento automatico, vedere la documentazione di [az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest)
