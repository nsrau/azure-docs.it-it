---
title: Usare PowerShell per configurare le chiavi gestite dal clienteUse PowerShell to configure customer-managed keys
titleSuffix: Azure Storage
description: Informazioni su come usare PowerShell per configurare le chiavi gestite dal cliente per la crittografia di Archiviazione di Azure.Learn how to use PowerShell to configure customer-managed keys for Azure Storage encryption.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bfc2e256396904456a7ee0fd8b6173c00a5f53d7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456397"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>Configurare le chiavi gestite dal cliente con l'insieme di credenziali delle chiavi di Azure tramite PowerShellConfigure customer-managed keys with Azure Key Vault by using PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Questo articolo illustra come configurare un insieme di credenziali delle chiavi di Azure con chiavi gestite dal cliente tramite PowerShell.This article shows how to configure an Azure Key Vault with customer-managed keys using PowerShell. Per informazioni su come creare un insieme di credenziali delle chiavi usando l'interfaccia della riga di comando di Azure, vedere Guida introduttiva: Impostare e recuperare un segreto dall'insieme di credenziali delle chiavi di Azure tramite PowerShell.To learn how to create a key vault using Azure CLI, see [Quickstart: Set and retrieve a secret from Azure Key Vault using PowerShell.](../../key-vault/secrets/quick-create-powershell.md)

## <a name="assign-an-identity-to-the-storage-account"></a>Assegnare un'identità all'account di archiviazioneAssign an identity to the storage account

Per abilitare le chiavi gestite dal cliente per l'account di archiviazione, assegnare innanzitutto un'identità gestita assegnata dal sistema all'account di archiviazione. Questa identità gestita verrà usata per concedere all'account di archiviazione le autorizzazioni per accedere all'insieme di credenziali delle chiavi.

Per assegnare un'identità gestita tramite PowerShell, chiamare [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Per altre informazioni sulla configurazione delle identità gestite assegnate dal sistema con PowerShell, vedere Configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure usando PowerShell.For more information about configuring system-assigned managed identities with PowerShell, see [Configure managed identities for Azure resources on an Azure VM using PowerShell.](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)

## <a name="create-a-new-key-vault"></a>Creare un nuovo insieme di credenziali delle chiavi

Per creare un nuovo insieme di credenziali delle chiavi tramite PowerShell, chiamare [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). L'insieme di credenziali delle chiavi usato per archiviare le chiavi gestite dal cliente per la crittografia di Archiviazione di Azure deve avere due impostazioni di protezione delle chiavi abilitate, **Eliminazione temporanea** e **Non eliminare**.

Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

Per informazioni su come abilitare **l'eliminazione temporanea** e l'eliminazione non **eliminare** in un insieme di credenziali delle chiavi esistente con PowerShell, vedere le sezioni **Attivazione dell'eliminazione temporanea** e **dell'abilitazione** della protezione dell'eliminazione in [Come usare l'eliminazione temporanea con PowerShell](../../key-vault/general/soft-delete-powershell.md).

## <a name="configure-the-key-vault-access-policy"></a>Configurare i criteri di accesso dell'insieme di credenziali delle chiavi

Configurare quindi i criteri di accesso per l'insieme di credenziali delle chiavi in modo che l'account di archiviazione disponga delle autorizzazioni per accedervi. In questo passaggio si userà l'identità gestita assegnata in precedenza all'account di archiviazione.

Per impostare i criteri di accesso per l'insieme di credenziali delle chiavi, chiamare [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di utilizzare le variabili definite negli esempi precedenti.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

## <a name="create-a-new-key"></a>Creare una nuova chiave

Successivamente, creare una nuova chiave nell'insieme di credenziali delle chiavi. Per creare una nuova chiave, chiamare [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di utilizzare le variabili definite negli esempi precedenti.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

Solo le chiavi RSA e RSA-HSM a 2048 bit sono supportate con la crittografia di Archiviazione di Azure.Only 2048-bit RSA and RSA-HSM keys are supported with Azure Storage encryption. Per altre informazioni sulle chiavi, vedere **Chiavi dell'insieme di credenziali delle chiavi** in Informazioni su chiavi, segreti e certificati dell'insieme di credenziali delle chiavi di [Azure.](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurare la crittografia con chiavi gestite dal clienteConfigure encryption with customer-managed keys

Per impostazione predefinita, la crittografia di Archiviazione di Azure usa chiavi gestite da Microsoft.By default, Azure Storage encryption uses Microsoft-managed keys. In questo passaggio configurare l'account di archiviazione di Azure per l'uso delle chiavi gestite dal cliente e specificare la chiave da associare all'account di archiviazione.

Chiamare [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) per aggiornare le impostazioni di crittografia dell'account di archiviazione, come illustrato nell'esempio seguente. Includere l'opzione **-KeyvaultEncryption** per abilitare le chiavi gestite dal cliente per l'account di archiviazione. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di utilizzare le variabili definite negli esempi precedenti.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Aggiornare la versione della chiave

Quando si crea una nuova versione di una chiave, è necessario aggiornare l'account di archiviazione per usare la nuova versione. In primo luogo, chiamare [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) per ottenere la versione più recente della chiave. Chiamare [quindi Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) per aggiornare le impostazioni di crittografia dell'account di archiviazione per usare la nuova versione della chiave, come illustrato nella sezione precedente.

## <a name="use-a-different-key"></a>Utilizzare un tasto diverso

Per modificare la chiave usata per la crittografia di Archiviazione di Azure, chiamare [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) come illustrato in Configurare la [crittografia con chiavi gestite dal cliente](#configure-encryption-with-customer-managed-keys) e specificare il nuovo nome e la nuova versione della chiave. Se la nuova chiave si trova in un insieme di credenziali delle chiavi diverso, aggiornare anche l'URI dell'insieme di credenziali delle chiavi.

## <a name="revoke-customer-managed-keys"></a>Revoca delle chiavi gestite dal cliente

Se si ritiene che una chiave possa essere stata compromessa, è possibile revocare le chiavi gestite dal cliente rimuovendo i criteri di accesso dell'insieme di credenziali delle chiavi. Per revocare una chiave gestita dal cliente, chiamare il comando [Remove-AzKeyVaultAccessPolicy,](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) come illustrato nell'esempio seguente. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di utilizzare le variabili definite negli esempi precedenti.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>Disabilitare le chiavi gestite dal clienteDisable customer-managed keys

Quando si disabilitano le chiavi gestite dal cliente, l'account di archiviazione viene nuovamente crittografato con le chiavi gestite da Microsoft.When you disable customer-managed keys, your storage account is once encrypted with Microsoft-managed keys. Per disabilitare le chiavi gestite dal cliente, chiamare [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) con l'opzione, `-StorageEncryption` come illustrato nell'esempio seguente. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di utilizzare le variabili definite negli esempi precedenti.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia di Archiviazione di Azure per i dati inattiviAzure Storage encryption for data at rest](storage-service-encryption.md)
- [Che cos'è Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
