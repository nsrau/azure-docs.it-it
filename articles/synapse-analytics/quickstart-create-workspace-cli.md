---
title: "Avvio rapido: Creare un'area di lavoro di Synapse con l'interfaccia della riga di comando di Azure"
description: Creare un'area di lavoro di Azure Synapse con l'interfaccia della riga di comando di Azure seguendo la procedura descritta in questa guida.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 08/25/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.openlocfilehash: 065e24c12e0750cd7a1f3ce2d4cad5c7ad4b95df
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91260695"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-cli"></a>Avvio rapido: Creare un'area di lavoro di Azure Synapse con l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure è l'esperienza della riga di comando di Azure per gestire le risorse di Azure. È possibile usarla nel browser con Azure Cloud Shell. È anche possibile installarla in macOS, Linux o Windows ed eseguirla dalla riga di comando.

Questa guida di avvio rapido illustra come creare un'area di lavoro di Synapse con l'interfaccia della riga di comando di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito prima di iniziare](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti

- Scaricare e installare [jq](https://stedolan.github.io/jq/download/), un processore JSON da riga di comando leggero e flessibile
- [Account di archiviazione di Azure Data Lake Storage Gen2 ](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

    > [!IMPORTANT]
    > L'area di lavoro di Azure Synapse deve essere in grado di leggere e scrivere nell'account di ADLS Gen2 selezionato. Per qualsiasi account di archiviazione collegato come account di archiviazione primario, inoltre, è necessario aver abilitato lo **spazio dei nomi gerarchico** durante la creazione dell'account di archiviazione, come descritto nella pagina [Creare un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal#create-a-storage-account). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="install-the-azure-cli-locally"></a>Installare l'interfaccia della riga di comando di Azure in locale

Se si sceglie di installare e usare l'interfaccia della riga di comando di Azure in locale, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Se si esegue l'interfaccia della riga di comando di Azure in locale, è necessario effettuare l'accesso e l'autenticazione. Se si usa Azure Cloud Shell, questo passaggio non è necessario. Per accedere all'interfaccia della riga di comando di Azure, eseguire `az login` e quindi l'autenticazione nella finestra del browser:

```azurecli
az login
```

Per altre informazioni sull'autenticazione con l'interfaccia della riga di comando di Azure, vedere [Accedere con l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli).

## <a name="install-azure-synapse-extension-for-azure-cli"></a>Installare l'estensione Azure Synapse per l'interfaccia della riga di comando di Azure

```azurecli
az extension add --name synapse
```

> [!WARNING]
> L'estensione Azure Synapse per l'interfaccia della riga di comando di Azure è in anteprima.

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
