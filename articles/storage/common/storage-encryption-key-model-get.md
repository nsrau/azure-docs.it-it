---
title: Determinare il modello di chiave di crittografia in uso per l'account di archiviazioneDetermine which encryption key model is in use for the storage account
titleSuffix: Azure Storage
description: Usare il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure per verificare la modalità di gestione delle chiavi di crittografia per l'account di archiviazione. Le chiavi possono essere gestite da Microsoft (impostazione predefinita) o dal cliente. Le chiavi gestite dal cliente devono essere archiviate in Archiviazione delle chiavi di Azure.Customer-managed keys must be stored in Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/13/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0df0ba4ce76d249bcb4738b41c94677e061f14ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409857"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>Determinare il modello di chiave di crittografia di Archiviazione di Azure in uso per l'account di archiviazioneDetermine which Azure Storage encryption key model is in use for the storage account

I dati nell'account di archiviazione vengono crittografati automaticamente da Archiviazione di Azure.Data in your storage account is automatically encrypted by Azure Storage. La crittografia di Archiviazione di Azure offre due opzioni per la gestione delle chiavi di crittografia a livello dell'account di archiviazione:Azure Storage encryption offers two options for managing encryption keys at the level of the storage account:

- **Chiavi gestite da Microsoft.** Per impostazione predefinita, Microsoft gestisce le chiavi usate per crittografare l'account di archiviazione.
- **Chiavi gestite dal cliente.** Facoltativamente, è possibile scegliere di gestire le chiavi di crittografia per l'account di archiviazione. Le chiavi gestite dal cliente devono essere archiviate in Archiviazione delle chiavi di Azure.Customer-managed keys must be stored in Azure Key Vault.

Inoltre, è possibile fornire una chiave di crittografia a livello di una singola richiesta per alcune operazioni di archiviazione BLOB. Quando viene specificata una chiave di crittografia nella richiesta, tale chiave esegue l'override della chiave di crittografia attiva nell'account di archiviazione. Per altre informazioni, vedere [Specificare una chiave fornita dal cliente in una richiesta di archiviazione BLOB.](../blobs/storage-blob-customer-provided-key.md)

Per altre informazioni sulle chiavi di crittografia, vedere Crittografia di Archiviazione di Azure per i [dati inattivi.](storage-service-encryption.md)

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>Controllare il modello di chiave di crittografia per l'account di archiviazioneCheck the encryption key model for the storage account

Per determinare se un account di archiviazione usa chiavi gestite da Microsoft o chiavi gestite dal cliente per la crittografia, usare uno degli approcci seguenti.

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

Per controllare il modello di crittografia per l'account di archiviazione usando il portale di Azure, eseguire la procedura seguente:To check the encryption model for the storage account by using the Azure portal, follow these steps:

1. Nel portale di Azure passare all'account di archiviazione.
1. Selezionare l'impostazione **Crittografia** e prendere nota dell'impostazione.

L'immagine seguente mostra un account di archiviazione crittografato con chiavi gestite da Microsoft:The following image shows a storage account that is encrypted with Microsoft-managed keys:

![Visualizzare l'account crittografato con chiavi gestite da MicrosoftView account encrypted with Microsoft-managed keys](media/storage-encryption-key-model-get/microsoft-managed-encryption-key-setting-portal.png)

L'immagine seguente mostra un account di archiviazione crittografato con chiavi gestite dal cliente:And the following image shows a storage account that is encrypted with customer-managed keys:

![Screenshot che mostra l'impostazione della chiave di crittografia nel portale di Azure](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershell"></a>[Powershell](#tab/powershell)

Per controllare il modello di crittografia per l'account di archiviazione tramite PowerShell, chiamare il comando [Get-AzStorageAccount,](/powershell/module/az.storage/get-azstorageaccount) quindi controllare la proprietà **KeySource** per l'account.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

Se il valore della proprietà `Microsoft.Storage` **KeySource** è , l'account viene crittografato con chiavi gestite da Microsoft. Se il valore della proprietà `Microsoft.Keyvault` **KeySource** è , l'account viene crittografato con chiavi gestite dal cliente.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/cli)

Per controllare il modello di crittografia per l'account di archiviazione usando l'interfaccia della riga di comando di Azure, chiamare il comando [az storage account show,](/cli/azure/storage/account#az-storage-account-show) quindi controllare la proprietà **keySource** per l'account.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

Se il valore della proprietà `Microsoft.Storage` **keySource** è , l'account viene crittografato con chiavi gestite da Microsoft. Se il valore della proprietà `Microsoft.Keyvault` **keySource** è , l'account viene crittografato con chiavi gestite dal cliente.

---

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia di Archiviazione di Azure per i dati inattiviAzure Storage encryption for data at rest](storage-service-encryption.md)
- [Usare le chiavi gestite dal cliente con l'insieme di credenziali delle chiavi di Azure per gestire la crittografia di Archiviazione di AzureUse customer-managed keys with Azure Key Vault to manage Azure Storage encryption](encryption-customer-managed-keys.md)