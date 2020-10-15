---
title: Accedere ad Archiviazione BLOB di Azure con Azure Databricks e Azure Key Vault
description: Questa esercitazione illustra come accedere ad Archiviazione BLOB di Azure da Azure Databricks usando i segreti archiviati in Azure Key Vault
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/16/2020
ms.openlocfilehash: bb574bb3dd000682090c6c3f861e885761753e19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88588518"
---
# <a name="tutorial-access-azure-blob-storage-using-azure-databricks-and-azure-key-vault"></a>Esercitazione: Accedere ad Archiviazione BLOB di Azure con Azure Databricks e Azure Key Vault

Questa esercitazione illustra come accedere ad Archiviazione BLOB di Azure da Azure Databricks usando i segreti archiviati in Azure Key Vault. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un account di archiviazione e un contenitore BLOB con l'interfaccia della riga di comando di Azure
> * Creare un'istanza di Key Vault e impostare un segreto
> * Creare un'area di lavoro di Azure Databricks e aggiungere un ambito di segreti di Key Vault
> * Accedere al contenitore BLOB dall'area di lavoro di Azure Databricks

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Prima di iniziare questa esercitazione, installare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

## <a name="create-a-storage-account-and-blob-container-with-azure-cli"></a>Creare un account di archiviazione e un contenitore BLOB con l'interfaccia della riga di comando di Azure

Per usare i BLOB, è necessario creare prima un account di archiviazione per utilizzo generico. Se non è disponibile, creare un [gruppo di risorse](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) prima di eseguire il comando. Il comando seguente crea e visualizza i metadati del contenitore di archiviazione. Copiare l'**ID**.

```azurecli
az storage account create --name contosoblobstorage5 --resource-group contosoResourceGroup --location eastus --sku Standard_ZRS --encryption-services blob
```

![Output della console del comando precedente. L'ID è evidenziato in verde per renderlo visibile all'utente finale.](../media/databricks-command-output-1.png)

Prima di creare un contenitore in cui caricare il BLOB, è necessario assegnare il ruolo [Collaboratore ai dati dei BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) a se stessi. Per questo esempio, il ruolo verrà assegnato all'account di archiviazione creato in precedenza.

```azurecli
az role assignment create --role "Storage Blob Data Contributor" --assignee t-trtr@microsoft.com --scope "/subscriptions/885e24c8-7a36-4217-b8c9-eed31e110504/resourceGroups/contosoResourceGroup5/providers/Microsoft.Storage/storageAccounts/contosoblobstorage5
```

Ora che il ruolo è stato assegnato all'account di archiviazione, è possibile creare un contenitore per il BLOB.

```azurecli
az storage container create --account-name contosoblobstorage5 --name contosocontainer5 --auth-mode login
```

Una volta creato il contenitore, è possibile caricarvi un BLOB (file a scelta). In questo esempio viene caricato un file con estensione txt con helloworld.

```azurecli
az storage blob upload --account-name contosoblobstorage5 --container-name contosocontainer5 --name helloworld --file helloworld.txt --auth-mode login
```

Elencare i BLOB nel contenitore per verificare che siano presenti.

```azurecli
az storage blob list --account-name contosoblobstorage5 --container-name contosocontainer5 --output table --auth-mode login
```

![Output della console del comando precedente. Visualizza il file appena archiviato nel contenitore.](../media/databricks-command-output-2.png)

Ottenere il valore di **key1** del contenitore di archiviazione usando il comando seguente. Copiare il valore.

```azurecli
az storage account keys list -g contosoResourceGroup5 -n contosoblobstorage5
```

![Output della console del comando precedente. Il valore di key1 è evidenziato in un riquadro verde.](../media/databricks-command-output-3.png)

## <a name="create-a-key-vault-and-set-a-secret"></a>Creare un'istanza di Key Vault e impostare un segreto

Verrà creata un'istanza di Key Vault usando il comando seguente. Questo comando visualizzerà anche i metadati di Key Vault. Copiare i valori di **ID** e **vaultUri**.

```azurecli
az keyvault create --name contosoKeyVault10 --resource-group contosoResourceGroup5 --location eastus
```

![Immagine](../media/databricks-command-output-4.png)
![Output della console del comando precedente. I valori di ID e vaultUri sono entrambi evidenziati in verde per renderli visibili all'utente.](../media/databricks-command-output-5.png)

Per creare il segreto, usare il comando seguente. Impostare il valore del segreto sul valore di **key1** dell'account di archiviazione.

```azurecli
az keyvault secret set --vault-name contosoKeyVault10 --name storageKey --value "value of your key1"
```

## <a name="create-an-azure-databricks-workspace-and-add-key-vault-secret-scope"></a>Creare un'area di lavoro di Azure Databricks e aggiungere un ambito di segreti di Key Vault

Questa azione non può essere completata tramite la riga di comando. Seguire questa [guida](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault#create-an-azure-databricks-workspace-and-add-a-secret-scope). Sarà necessario accedere al [portale di Azure](https://ms.portal.azure.com/#home) per:

1. Creare la risorsa di Azure Databricks
1. Avviare l'area di lavoro
1. Creare un ambito di segreti con supporto di Key Vault

## <a name="access-your-blob-container-from-azure-databricks-workspace"></a>Accedere al contenitore BLOB dall'area di lavoro di Azure Databricks

Questa azione non può essere completata tramite la riga di comando. Seguire questa [guida](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault#access-your-blob-container-from-azure-databricks). Sarà necessario usare l'area di lavoro di Azure Databricks per:

1. Creare un **nuovo cluster**
1. Creare un **nuovo notebook**
1. Compilare i campi corrispondenti nello script Python
1. Eseguire lo script Python

```python
dbutils.fs.mount(
source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
mount_point = "/mnt/<mount-name>",
extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})

df = spark.read.text("/mnt/<mount-name>/<file-name>")

df.show()
```

## <a name="next-steps"></a>Passaggi successivi

Assicurarsi che l'istanza di Key Vault sia recuperabile:
> [!div class="nextstepaction"]
> [Pulire le risorse](https://docs.microsoft.com/azure/azure-resource-manager/management/delete-resource-group?tabs=azure-powershell)
