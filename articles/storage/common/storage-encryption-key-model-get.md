---
title: Determinare quale modello di chiave di crittografia è in uso per l'account di archiviazione-archiviazione di Azure
description: Usare portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure per verificare la modalità di gestione delle chiavi di crittografia per l'account di archiviazione. Le chiavi possono essere gestite da Microsoft (impostazione predefinita) o dal cliente. Le chiavi gestite dal cliente devono essere archiviate in Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 967e6f278008a59721d8d0c74e34c0252eeb1138
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666598"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>Determinare quale modello della chiave di crittografia di archiviazione di Azure è in uso per l'account di archiviazione

I dati nell'account di archiviazione vengono crittografati automaticamente da archiviazione di Azure. La crittografia di archiviazione di Azure offre due opzioni per la gestione delle chiavi di crittografia a livello dell'account di archiviazione:

- **Chiavi gestite da Microsoft.** Per impostazione predefinita, Microsoft gestisce le chiavi usate per crittografare l'account di archiviazione.
- **Chiavi gestite dal cliente.** Facoltativamente, è possibile scegliere di gestire le chiavi di crittografia per l'account di archiviazione. Le chiavi gestite dal cliente devono essere archiviate in Azure Key Vault.

Inoltre, è possibile fornire una chiave di crittografia al livello di una singola richiesta per alcune operazioni di archiviazione BLOB. Quando si specifica una chiave di crittografia per la richiesta, tale chiave sostituisce la chiave di crittografia attiva nell'account di archiviazione. Per altre informazioni, vedere [specificare una chiave fornita dal cliente per una richiesta all'archiviazione BLOB](../blobs/storage-blob-customer-provided-key.md).

Per altre informazioni sulle chiavi di crittografia, vedere [crittografia di archiviazione di Azure per dati](storage-service-encryption.md)inattivi.

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>Controllare il modello della chiave di crittografia per l'account di archiviazione

Per determinare se un account di archiviazione USA chiavi gestite da Microsoft o chiavi gestite dal cliente per la crittografia, usare uno degli approcci seguenti.

# <a name="azure-portaltabportal"></a>[Azure portal](#tab/portal)

Per controllare il modello di crittografia per l'account di archiviazione usando il portale di Azure, seguire questa procedura:

1. Nel portale di Azure passare all'account di archiviazione.
1. Selezionare l'impostazione **crittografia** e prendere nota dell'impostazione.

La figura seguente illustra un account di archiviazione in cui le chiavi gestite dal cliente sono utilizzate per la crittografia:

![Screenshot che mostra l'impostazione della chiave di crittografia in portale di Azure](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Per controllare il modello di crittografia per l'account di archiviazione usando PowerShell, chiamare il comando [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) , quindi controllare la proprietà di **origine** della crittografia per l'account.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

Se il valore della proprietà della chiave di **origine** è `Microsoft.Storage`, l'account viene crittografato con le chiavi gestite da Microsoft. Se il valore della proprietà chiave **source** è `Microsoft.Keyvault`, l'account viene crittografato con le chiavi gestite dal cliente.

# <a name="azure-clitabcli"></a>[interfaccia della riga di comando di Azure](#tab/cli)

Per controllare il modello di crittografia per l'account di archiviazione usando l'interfaccia della riga di comando di Azure, chiamare il comando [AZ storage account Show](/cli/azure/storage/account#az-storage-account-show) , quindi controllare la proprietà di **origine della risorsa** per l'account.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

Se il valore della proprietà della chiave di **origine** è `Microsoft.Storage`, l'account viene crittografato con le chiavi gestite da Microsoft. Se il valore della proprietà chiave **source** è `Microsoft.Keyvault`, l'account viene crittografato con le chiavi gestite dal cliente.

---

## <a name="next-steps"></a>Passaggi successivi

[Crittografia di archiviazione di Azure per dati inattivi](storage-service-encryption.md)
