---
title: "Avvio rapido: Creare un'area di lavoro di Synapse con l'interfaccia della riga di comando di Azure"
description: Creare un'area di lavoro di Azure Synapse con l'interfaccia della riga di comando di Azure seguendo la procedura descritta in questa guida.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 08/25/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.openlocfilehash: 46ab1df1b776bf8dc9d6d917e5394c3efeec0de4
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659387"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-cli"></a>Avvio rapido: Creare un'area di lavoro di Azure Synapse con l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure è l'esperienza della riga di comando di Azure per gestire le risorse di Azure. È possibile usarla nel browser con Azure Cloud Shell. È anche possibile installarla in macOS, Linux o Windows ed eseguirla dalla riga di comando.

Questa guida di avvio rapido illustra come creare un'area di lavoro di Synapse con l'interfaccia della riga di comando di Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

- Scaricare e installare [jq](https://stedolan.github.io/jq/download/), un processore JSON da riga di comando leggero e flessibile
- [Account di archiviazione di Azure Data Lake Storage Gen2 ](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

    > [!IMPORTANT]
    > L'area di lavoro di Azure Synapse deve essere in grado di leggere e scrivere nell'account di ADLS Gen2 selezionato. Per qualsiasi account di archiviazione collegato come account di archiviazione primario, inoltre, è necessario aver abilitato lo **spazio dei nomi gerarchico** durante la creazione dell'account di archiviazione, come descritto nella pagina [Creare un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal#create-a-storage-account). 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-azure-synapse-workspace-using-the-azure-cli"></a>Creare un'area di lavoro di Azure Synapse con l'interfaccia della riga di comando di Azure

1. Definire le variabili di ambiente necessarie per creare le risorse per l'area di lavoro di Azure Synapse.

    | Nome di variabile di ambiente | Descrizione |
    |---|---|---|
    |StorageAccountName| Nome dell'account di archiviazione di ADLS Gen2 esistente.|
    |StorageAccountResourceGroup| Nome del gruppo di risorse dell'account di archiviazione di ADLS Gen2 esistente. |
    |FileShareName| Nome del file system di archiviazione esistente.|
    |SynapseResourceGroup| Scegliere un nuovo nome per il gruppo di risorse di Azure Synapse. |
    |Area| Scegliere una delle [aree di Azure](https://azure.microsoft.com/global-infrastructure/geographies/#overview). |
    |SynapseWorkspaceName| Scegliere un nome univoco per la nuova area di lavoro di Azure Synapse. |
    |SqlUser| Scegliere un valore per un nuovo nome utente.|
    |SqlPassword| Scegliere una password sicura.|
    |||

2. Creare un gruppo di risorse come contenitore per l'area di lavoro di Azure Synapse:
    ```azurecli
    az group create --name $SynapseResourceGroup --location $Region
    ```
3. Recuperare la chiave dell'account di archiviazione di ADLS Gen2:
    ```azurecli
    StorageAccountKey=$(az storage account keys list \
      --account-name $StorageAccountName \
      | jq -r '.[0] | .value')
    ```
4. Recuperare l'URL dell'endpoint di archiviazione di ADLS Gen2:
    ```azurecli
    StorageEndpointUrl=$(az storage account show \
      --name $StorageAccountName \
      --resource-group $StorageAccountResourceGroup \
      | jq -r '.primaryEndpoints | .dfs')
    ```

5. (Facoltativo) È sempre possibile verificare quali sono l'endpoint e la chiave dell'account di archiviazione di ADLS Gen2:
    ```azurecli
    echo "Storage Account Key: $StorageAccountKey"
    echo "Storage Endpoint URL: $StorageEndpointUrl"
    ```

6. Creare un'area di lavoro di Azure Synapse:
    ```azurecli
    az synapse workspace create \
      --name $SynapseWorkspaceName \
      --resource-group $SynapseResourceGroup \
      --storage-account $StorageAccountName \
      --file-system $FileShareName \
      --sql-admin-login-user $SqlUser \
      --sql-admin-login-password $SqlPassword \
      --location $Region
    ```

7. Ottenere l'URL di sviluppo e Web per l'area di lavoro di Azure Synapse:
    ```azurecli
    WorkspaceWeb=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .web')

    WorkspaceDev=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .dev')
    ```

8. Creare una regola del firewall per consentire l'accesso all'area di lavoro di Azure Synapse dal computer in uso:

    ```azurecli
    ClientIP=$(curl -sb -H "Accept: application/json" "$WorkspaceDev" | jq -r '.message')
    ClientIP=${ClientIP##'Client Ip address : '}
    echo "Creating a firewall rule to enable access for IP address: $ClientIP"

    az synapse workspace firewall-rule create --end-ip-address $ClientIP --start-ip-address $ClientIP --name "Allow Client IP" --resource-group $SynapseResourceGroup --workspace-name $SynapseWorkspaceName
    ```

9. Aprire l'indirizzo URL Web dell'area di lavoro di Azure Synapse archiviato nella variabile di ambiente `WorkspaceWeb` per accedere all'area di lavoro:

    ```azurecli
    echo "Open your Azure Synapse Workspace Web URL in the browser: $WorkspaceWeb"
    ```
    
    [ ![Area di lavoro di Azure Synapse sul Web](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png) ](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png#lightbox)


## <a name="clean-up-resources"></a>Pulire le risorse

Per eliminare l'area di lavoro di Azure Synapse, seguire questa procedura.
> [!WARNING]
> Con l'eliminazione di un'area di lavoro di Azure Synapse verranno rimossi anche i motori di analisi e i dati archiviati nel database dei pool SQL contenuti e dei metadati dell'area di lavoro. Non sarà più possibile connettersi agli endpoint SQL o Apache Spark. Tutti gli artefatti del codice verranno eliminati (query, notebook, definizioni di processi e pipeline).
>
> L'eliminazione dell'area di lavoro **non** influisce sui dati dell'istanza di Data Lake Store Gen2 collegata all'area di lavoro.

Per eliminare l'area di lavoro di Azure Synapse, eseguire questo comando:

```azurecli
az synapse workspace delete --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Successivamente, è possibile [creare pool SQL](quickstart-create-sql-pool-studio.md) o [pool di Apache Spark](quickstart-create-apache-spark-pool-studio.md) per iniziare ad analizzare ed esplorare i dati.
