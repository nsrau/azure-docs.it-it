---
title: Usare PowerShell per configurare le chiavi gestite dal cliente
titleSuffix: Azure Storage
description: Informazioni su come usare PowerShell per configurare le chiavi gestite dal cliente per la crittografia di archiviazione di Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/24/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 1c928056ec0e7b101d991c8d8c8db3bd659251ba
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799129"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>Configurare chiavi gestite dal cliente con Azure Key Vault tramite PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Questo articolo illustra come configurare un Azure Key Vault con chiavi gestite dal cliente tramite PowerShell. Per informazioni su come creare un insieme di credenziali delle chiavi tramite l'interfaccia della riga di comando di Azure, vedere [Guida introduttiva: impostare e recuperare un segreto da Azure Key Vault usando PowerShell](../../key-vault/secrets/quick-create-powershell.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Assegnare un'identità all'account di archiviazione

Per abilitare le chiavi gestite dal cliente per l'account di archiviazione, assegnare innanzitutto un'identità gestita assegnata dal sistema all'account di archiviazione. Questa identità gestita verrà usata per concedere le autorizzazioni dell'account di archiviazione per accedere all'insieme di credenziali delle chiavi.

Per assegnare un'identità gestita usando PowerShell, chiamare [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Per altre informazioni sulla configurazione di identità gestite assegnate dal sistema con PowerShell, vedere [configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure con PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Creare un nuovo insieme di credenziali delle chiavi

Per creare un nuovo insieme di credenziali delle chiavi con PowerShell, installare la versione 2.0.0 o successiva del modulo [AZ. Key Vault](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) PowerShell. Chiamare quindi [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) per creare un nuovo insieme di credenziali delle chiavi.

L'insieme di credenziali delle chiavi usato per archiviare le chiavi gestite dal cliente per la crittografia di archiviazione di Azure deve avere due impostazioni di protezione della chiave abilitate, l' **eliminazione** temporanea e non la **ripulitura**. Nella versione 2.0.0 e successive del modulo AZ. Key Vault, l'eliminazione temporanea è abilitata per impostazione predefinita quando si crea un nuovo insieme di credenziali delle chiavi.

Nell'esempio seguente viene creato un nuovo insieme di credenziali delle chiavi con l' **eliminazione** temporanea e **non vengono rieliminate** le proprietà abilitate. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

Per informazioni su come abilitare **l'eliminazione** temporanea e **non ripulirla** in un insieme di credenziali delle chiavi esistente con PowerShell, vedere le sezioni intitolate **Abilitazione** dell'eliminazione temporanea e **Abilitazione della protezione di ripulitura** in [come usare l'eliminazione temporanea con PowerShell](../../key-vault/general/soft-delete-powershell.md).

## <a name="configure-the-key-vault-access-policy"></a>Configurare i criteri di accesso dell'insieme di credenziali delle chiavi

Configurare quindi i criteri di accesso per l'insieme di credenziali delle chiavi in modo che l'account di archiviazione disponga delle autorizzazioni di accesso. In questo passaggio si userà l'identità gestita precedentemente assegnata all'account di archiviazione.

Per impostare i criteri di accesso per l'insieme di credenziali delle chiavi, chiamare [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di usare le variabili definite negli esempi precedenti.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

## <a name="create-a-new-key"></a>Creare una nuova chiave

Successivamente, creare una nuova chiave nell'insieme di credenziali delle chiavi. Per creare una nuova chiave, chiamare [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di usare le variabili definite negli esempi precedenti.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

La crittografia di archiviazione di Azure supporta chiavi RSA e RSA-HSM di dimensioni 2048, 3072 e 4096. Per ulteriori informazioni sulle chiavi, vedere **Key Vault chiavi** in [informazioni su Azure Key Vault chiavi, segreti e certificati](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurare la crittografia con chiavi gestite dal cliente

Per impostazione predefinita, la crittografia di archiviazione di Azure usa chiavi gestite da Microsoft. In questo passaggio, configurare l'account di archiviazione di Azure per usare le chiavi gestite dal cliente con Azure Key Vault, quindi specificare la chiave da associare all'account di archiviazione.

Quando si configura la crittografia con chiavi gestite dal cliente, è possibile scegliere di aggiornare automaticamente la chiave usata per la crittografia quando cambia la versione della chiave nell'insieme di credenziali delle chiavi associato. In alternativa, è possibile specificare in modo esplicito una versione della chiave da usare per la crittografia finché la versione della chiave non viene aggiornata manualmente.

> [!NOTE]
> Per ruotare una chiave, creare una nuova versione della chiave in Azure Key Vault. Archiviazione di Azure non gestisce la rotazione della chiave in Azure Key Vault, pertanto sarà necessario ruotare manualmente la chiave o creare una funzione per ruotarla in base a una pianificazione.

### <a name="configure-encryption-to-automatically-update-the-key-version"></a>Configurare la crittografia per aggiornare automaticamente la versione della chiave

Per configurare la crittografia con chiavi gestite dal cliente per aggiornare automaticamente la versione della chiave, installare il modulo [AZ. storage](https://www.powershellgallery.com/packages/Az.Storage) , versione 2.0.0 o successiva.

Per aggiornare automaticamente la versione della chiave per una chiave gestita dal cliente, omettere la versione della chiave quando si configura la crittografia con chiavi gestite dal cliente per l'account di archiviazione. Chiamare [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) per aggiornare le impostazioni di crittografia dell'account di archiviazione, come illustrato nell'esempio seguente, e includere l'opzione **-KeyvaultEncryption** per abilitare le chiavi gestite dal cliente per l'account di archiviazione. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di usare le variabili definite negli esempi precedenti.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>Configurare la crittografia per l'aggiornamento manuale delle versioni delle chiavi

Per specificare in modo esplicito una versione della chiave da usare per la crittografia, fornire la versione della chiave quando si configura la crittografia con chiavi gestite dal cliente per l'account di archiviazione. Chiamare [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) per aggiornare le impostazioni di crittografia dell'account di archiviazione, come illustrato nell'esempio seguente, e includere l'opzione **-KeyvaultEncryption** per abilitare le chiavi gestite dal cliente per l'account di archiviazione. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di usare le variabili definite negli esempi precedenti.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

Quando si aggiorna manualmente la versione della chiave, è necessario aggiornare le impostazioni di crittografia dell'account di archiviazione per usare la nuova versione. Prima di tutto, chiamare [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) per ottenere la versione più recente della chiave. Chiamare quindi [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) per aggiornare le impostazioni di crittografia dell'account di archiviazione per usare la nuova versione della chiave, come illustrato nell'esempio precedente.

## <a name="use-a-different-key"></a>Usare una chiave diversa

Per modificare la chiave usata per la crittografia di archiviazione di Azure, chiamare [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) , come illustrato nella pagina relativa alla [configurazione della crittografia con chiavi gestite dal cliente](#configure-encryption-with-customer-managed-keys) e fornire il nome e la versione della nuova chiave. Se la nuova chiave si trova in un insieme di credenziali delle chiavi diverso, aggiornare anche l'URI dell'insieme di credenziali delle chiavi.

## <a name="revoke-customer-managed-keys"></a>Revocare le chiavi gestite dal cliente

È possibile revocare le chiavi gestite dal cliente rimuovendo i criteri di accesso di Key Vault. Per revocare una chiave gestita dal cliente, chiamare il comando [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) , come illustrato nell'esempio seguente. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di usare le variabili definite negli esempi precedenti.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>Disabilitare le chiavi gestite dal cliente

Quando si disabilitano le chiavi gestite dal cliente, l'account di archiviazione viene nuovamente crittografato con le chiavi gestite da Microsoft. Per disabilitare le chiavi gestite dal cliente, chiamare [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) con l' `-StorageEncryption` opzione, come illustrato nell'esempio seguente. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di usare le variabili definite negli esempi precedenti.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md)
- [Che cos'è Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
