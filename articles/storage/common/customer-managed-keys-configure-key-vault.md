---
title: Configurare la crittografia con chiavi gestite dal cliente archiviate in Azure Key Vault
titleSuffix: Azure Storage
description: Informazioni su come configurare la crittografia di archiviazione di Azure con chiavi gestite dal cliente archiviate in Azure Key Vault usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0991992a6138d263dfb4d200c9555a8d53366d70
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994927"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault"></a>Configurare la crittografia con chiavi gestite dal cliente archiviate in Azure Key Vault

Archiviazione di Azure crittografa tutti i dati in un account di archiviazione inattivo. Per impostazione predefinita, i dati vengono crittografati con chiavi gestite da Microsoft. Per un maggiore controllo sulle chiavi di crittografia, è possibile gestire le proprie chiavi. Le chiavi gestite dal cliente devono essere archiviate in Azure Key Vault o Key Vault modello di protezione hardware (HSM) gestito (anteprima).

Questo articolo illustra come configurare la crittografia con chiavi gestite dal cliente archiviate in un insieme di credenziali delle chiavi usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure. Per informazioni su come configurare la crittografia con chiavi gestite dal cliente archiviate in un modulo di protezione hardware gestito, vedere [configurare la crittografia con chiavi gestite dal cliente archiviate in Azure Key Vault HSM gestito (anteprima)](customer-managed-keys-configure-key-vault-hsm.md).

> [!NOTE]
> Azure Key Vault e Azure Key Vault HSM gestito supportano le stesse API e le stesse interfacce di gestione per la configurazione.

## <a name="configure-a-key-vault"></a>Configurare un insieme di credenziali delle chiavi

È possibile usare un insieme di credenziali delle chiavi nuovo o esistente per archiviare le chiavi gestite dal cliente. L'account di archiviazione e l'insieme di credenziali chiave devono essere nella stessa area, ma possono appartenere a sottoscrizioni diverse.

Per usare le chiavi gestite dal cliente con la crittografia di archiviazione di Azure, è necessario che sia abilitata l'eliminazione temporanea che la protezione per l'insieme di credenziali delle chiavi. L'eliminazione temporanea è abilitata per impostazione predefinita quando si crea un nuovo insieme di credenziali delle chiavi e non può essere disabilitato. È possibile abilitare la protezione ripulitura quando si crea l'insieme di credenziali delle chiavi o dopo che è stato creato.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Per informazioni su come creare un insieme di credenziali delle chiavi con il portale di Azure, vedere [Guida introduttiva: creare un insieme di credenziali delle chiavi usando il portale di Azure](../../key-vault/general/quick-create-portal.md). Quando si crea l'insieme di credenziali delle chiavi, selezionare **Abilita Ripulisci protezione**, come illustrato nella figura seguente.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/configure-key-vault-portal.png" alt-text="Screenshot che illustra come abilitare la ripulitura della protezione durante la creazione di un insieme di credenziali delle chiavi":::

Per abilitare la ripulitura della protezione in un insieme di credenziali delle chiavi esistente, seguire questa procedura:

1. Passare all'insieme di credenziali delle chiavi nella portale di Azure.
1. In **Impostazioni**scegliere **Proprietà**.
1. Nella sezione **Ripulisci protezione** scegliere **Abilita Ripulisci protezione**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per creare un nuovo insieme di credenziali delle chiavi con PowerShell, installare la versione 2.0.0 o successiva del modulo [AZ. Key Vault](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) PowerShell. Chiamare quindi [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) per creare un nuovo insieme di credenziali delle chiavi. Con la versione 2.0.0 e successive del modulo AZ. Key Vault, l'eliminazione temporanea è abilitata per impostazione predefinita quando si crea un nuovo insieme di credenziali delle chiavi.

Nell'esempio seguente viene creato un nuovo insieme di credenziali delle chiavi con la protezione eliminazione temporanea e ripulitura abilitata. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

Per informazioni su come abilitare la ripulitura della protezione in un insieme di credenziali delle chiavi esistente con PowerShell, vedere [come usare l'eliminazione temporanea con PowerShell](../../key-vault/general/soft-delete-powershell.md).

Assegnare quindi all'account di archiviazione un'identità gestita assegnata dal sistema. Questa identità gestita verrà usata per concedere le autorizzazioni dell'account di archiviazione per accedere all'insieme di credenziali delle chiavi. Per altre informazioni sulle identità gestite assegnate dal sistema, vedere informazioni sulle [identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Per assegnare un'identità gestita usando PowerShell, chiamare [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount):

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Configurare infine i criteri di accesso per l'insieme di credenziali delle chiavi in modo che l'account di archiviazione disponga delle autorizzazioni di accesso. In questo passaggio si userà l'identità gestita precedentemente assegnata all'account di archiviazione.

Per impostare i criteri di accesso per l'insieme di credenziali delle chiavi, chiamare [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy):

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per creare un nuovo insieme di credenziali delle chiavi tramite l'interfaccia della riga di comando di Azure, chiamare [AZ Key Vault create](/cli/azure/keyvault#az-keyvault-create) Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-purge-protection
```

Per informazioni su come abilitare la ripulitura della protezione in un insieme di credenziali delle chiavi esistente con l'interfaccia della riga di comando di Azure, vedere [come usare l'eliminazione temporanea con CLI](../../key-vault/general/soft-delete-cli.md).

Assegnare quindi all'account di archiviazione un'identità gestita assegnata dal sistema. Questa identità gestita verrà usata per concedere le autorizzazioni dell'account di archiviazione per accedere all'insieme di credenziali delle chiavi. Per altre informazioni sulle identità gestite assegnate dal sistema, vedere informazioni sulle [identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Per assegnare un'identità gestita usando l'interfaccia della riga di comando di Azure, chiamare [AZ storage account Update](/cli/azure/storage/account#az-storage-account-update):

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Configurare infine i criteri di accesso per l'insieme di credenziali delle chiavi in modo che l'account di archiviazione disponga delle autorizzazioni di accesso. In questo passaggio si userà l'identità gestita precedentemente assegnata all'account di archiviazione.

Per impostare i criteri di accesso per l'insieme di credenziali delle chiavi, chiamare [AZ Key Vault set-Policy](/cli/azure/keyvault#az-keyvault-set-policy):

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

---

## <a name="add-a-key"></a>Aggiungere una chiave

Successivamente, aggiungere una chiave nell'insieme di credenziali delle chiavi.

La crittografia di archiviazione di Azure supporta chiavi RSA e RSA-HSM di dimensioni 2048, 3072 e 4096. Per ulteriori informazioni sulle chiavi, vedere **Key Vault chiavi** in [informazioni su Azure Key Vault chiavi, segreti e certificati](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Per informazioni su come aggiungere una chiave con il portale di Azure, vedere [Guida introduttiva: impostare e recuperare una chiave da Azure Key Vault tramite il portale di Azure](../../key-vault/keys/quick-create-portal.md).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per aggiungere una chiave con PowerShell, chiamare [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di usare le variabili definite negli esempi precedenti.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName `
    -Name <key> `
    -Destination 'Software'
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per aggiungere una chiave con l'interfaccia della riga di comando di Azure, chiamare [AZ Key Vault Key create](/cli/azure/keyvault/key#az-keyvault-key-create). Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

---

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurare la crittografia con chiavi gestite dal cliente

Configurare quindi l'account di archiviazione di Azure per usare le chiavi gestite dal cliente con Azure Key Vault, quindi specificare la chiave da associare all'account di archiviazione.

Quando si configura la crittografia con chiavi gestite dal cliente, è possibile scegliere di aggiornare automaticamente la versione della chiave usata per la crittografia di archiviazione di Azure ogni volta che è disponibile una nuova versione nell'insieme di credenziali delle chiavi associato. In alternativa, è possibile specificare in modo esplicito una versione della chiave da usare per la crittografia finché la versione della chiave non viene aggiornata manualmente.

> [!NOTE]
> Per ruotare una chiave, creare una nuova versione della chiave in Azure Key Vault. Archiviazione di Azure non gestisce la rotazione della chiave in Azure Key Vault, pertanto sarà necessario ruotare manualmente la chiave o creare una funzione per ruotarla in base a una pianificazione.

### <a name="configure-encryption-for-automatic-updating-of-key-versions"></a>Configurare la crittografia per l'aggiornamento automatico delle versioni delle chiavi

Archiviazione di Azure può aggiornare automaticamente la chiave gestita dal cliente usata per la crittografia per usare la versione più recente della chiave. Quando la chiave gestita dal cliente viene ruotata in Azure Key Vault, archiviazione di Azure inizierà automaticamente a usare la versione più recente della chiave per la crittografia.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Per configurare le chiavi gestite dal cliente con l'aggiornamento automatico della versione della chiave nella portale di Azure, attenersi alla procedura seguente:

1. Passare all'account di archiviazione.
1. Nel pannello **Impostazioni** relativo all'account di archiviazione fare clic su **Crittografia**. Selezionare l'opzione **chiavi gestite dal cliente** , come illustrato nella figura seguente.

    ![Screenshot del portale che mostra l'opzione di crittografia](./media/customer-managed-keys-configure-key-vault/portal-configure-encryption-keys.png)

1. Scegliere l'opzione **Selezionare la chiave dall'insieme di credenziali delle chiavi**.
1. Selezionare **selezionare un insieme di credenziali delle chiavi e una chiave**.
1. Selezionare l'insieme di credenziali delle chiavi contenente la chiave che si vuole usare.
1. Selezionare la chiave dall'insieme di credenziali delle chiavi.

   ![Screenshot che illustra come selezionare Key Vault e Key](./media/customer-managed-keys-configure-key-vault/portal-select-key-from-key-vault.png)

1. Salvare le modifiche.

Dopo aver specificato la chiave, il portale di Azure indica che l'aggiornamento automatico della versione della chiave è abilitato e visualizza la versione della chiave attualmente in uso per la crittografia.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/portal-auto-rotation-enabled.png" alt-text="Screenshot che illustra l'aggiornamento automatico della versione della chiave abilitata":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per configurare le chiavi gestite dal cliente con l'aggiornamento automatico della versione della chiave con PowerShell, installare il modulo [AZ. storage](https://www.powershellgallery.com/packages/Az.Storage) , versione 2.0.0 o successiva.

Per aggiornare automaticamente la versione della chiave per una chiave gestita dal cliente, omettere la versione della chiave quando si configura la crittografia con chiavi gestite dal cliente per l'account di archiviazione. Chiamare [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) per aggiornare le impostazioni di crittografia dell'account di archiviazione, come illustrato nell'esempio seguente, e includere l'opzione **-KeyvaultEncryption** per abilitare le chiavi gestite dal cliente per l'account di archiviazione.

Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di usare le variabili definite negli esempi precedenti.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per configurare chiavi gestite dal cliente con aggiornamento automatico della versione della chiave con l'interfaccia della riga di comando di Azure, installare l'interfaccia della riga di comando di [Azure versione 2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) o successiva Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Per aggiornare automaticamente la versione della chiave per una chiave gestita dal cliente, omettere la versione della chiave quando si configura la crittografia con chiavi gestite dal cliente per l'account di archiviazione. Chiamare [AZ storage account Update](/cli/azure/storage/account#az-storage-account-update) per aggiornare le impostazioni di crittografia dell'account di archiviazione, come illustrato nell'esempio seguente. Includere il `--encryption-key-source` parametro e impostarlo su `Microsoft.Keyvault` per abilitare le chiavi gestite dal cliente per l'account.

Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

---

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>Configurare la crittografia per l'aggiornamento manuale delle versioni delle chiavi

Se si preferisce aggiornare manualmente la versione della chiave, specificare in modo esplicito la versione nel momento in cui si configura la crittografia con chiavi gestite dal cliente. In questo caso, archiviazione di Azure non aggiornerà automaticamente la versione della chiave quando viene creata una nuova versione nell'insieme di credenziali delle chiavi. Per usare una nuova versione della chiave, è necessario aggiornare manualmente la versione usata per la crittografia di archiviazione di Azure.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Per configurare le chiavi gestite dal cliente con aggiornamento manuale della versione della chiave nella portale di Azure, specificare l'URI della chiave, inclusa la versione. Per specificare una chiave come URI, attenersi alla procedura seguente:

1. Per individuare l'URI della chiave nella portale di Azure, passare all'insieme di credenziali delle chiavi e selezionare l'impostazione **chiavi** . Selezionare la chiave desiderata, quindi fare clic sulla chiave per visualizzarne le versioni. Selezionare una versione della chiave per visualizzare le impostazioni per tale versione.
1. Copiare il valore del campo **identificatore chiave** , che fornisce l'URI.

    ![Screenshot che mostra l'URI della chiave di Key Vault](media/customer-managed-keys-configure-key-vault/portal-copy-key-identifier.png)

1. Nelle impostazioni della **chiave di crittografia** per l'account di archiviazione scegliere l'opzione immettere l'URI del **tasto** .
1. Incollare l'URI copiato nel campo **URI chiave** . Omettere la versione della chiave dall'URI per abilitare l'aggiornamento automatico della versione della chiave.

   ![Screenshot che illustra come immettere l'URI della chiave](./media/customer-managed-keys-configure-key-vault/portal-specify-key-uri.png)

1. Specificare la sottoscrizione che contiene l'insieme di credenziali delle chiavi.
1. Salvare le modifiche.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per configurare le chiavi gestite dal cliente con l'aggiornamento manuale della versione della chiave, fornire in modo esplicito la versione della chiave quando si configura la crittografia per l'account di archiviazione. Chiamare [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) per aggiornare le impostazioni di crittografia dell'account di archiviazione, come illustrato nell'esempio seguente, e includere l'opzione **-KeyvaultEncryption** per abilitare le chiavi gestite dal cliente per l'account di archiviazione.

Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di usare le variabili definite negli esempi precedenti.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

Quando si aggiorna manualmente la versione della chiave, è necessario aggiornare le impostazioni di crittografia dell'account di archiviazione per usare la nuova versione. Prima di tutto, chiamare [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) per ottenere la versione più recente della chiave. Chiamare quindi [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) per aggiornare le impostazioni di crittografia dell'account di archiviazione per usare la nuova versione della chiave, come illustrato nell'esempio precedente.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per configurare le chiavi gestite dal cliente con l'aggiornamento manuale della versione della chiave, fornire in modo esplicito la versione della chiave quando si configura la crittografia per l'account di archiviazione. Chiamare [AZ storage account Update](/cli/azure/storage/account#az-storage-account-update) per aggiornare le impostazioni di crittografia dell'account di archiviazione, come illustrato nell'esempio seguente. Includere il `--encryption-key-source` parametro e impostarlo su `Microsoft.Keyvault` per abilitare le chiavi gestite dal cliente per l'account.

Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

Quando si aggiorna manualmente la versione della chiave, è necessario aggiornare le impostazioni di crittografia dell'account di archiviazione per usare la nuova versione. Per prima cosa, eseguire una query per l'URI dell'insieme di credenziali delle chiavi chiamando [AZ Key Vault Show](/cli/azure/keyvault#az-keyvault-show)e per la versione della chiave chiamando [AZ Key Vault Key List-Versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Chiamare quindi il comando [AZ storage account Update](/cli/azure/storage/account#az-storage-account-update) per aggiornare le impostazioni di crittografia dell'account di archiviazione in modo da usare la nuova versione della chiave, come illustrato nell'esempio precedente.

---

## <a name="change-the-key"></a>Modificare la chiave

È possibile modificare la chiave usata per la crittografia di archiviazione di Azure in qualsiasi momento.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Per modificare la chiave con il portale di Azure, attenersi alla procedura seguente:

1. Passare all'account di archiviazione e visualizzare le impostazioni di **crittografia** .
1. Selezionare l'insieme di credenziali delle chiavi e scegliere una nuova chiave.
1. Salvare le modifiche.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per modificare la chiave con PowerShell, chiamare [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) , come illustrato in [configurare la crittografia con chiavi gestite dal cliente](#configure-encryption-with-customer-managed-keys) e specificare il nome e la versione della nuova chiave. Se la nuova chiave si trova in un insieme di credenziali delle chiavi diverso, è necessario aggiornare anche l'URI dell'insieme di credenziali delle chiavi.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per modificare la chiave con l'interfaccia della riga di comando di Azure, chiamare [AZ storage account Update](/cli/azure/storage/account#az-storage-account-update) come illustrato in [configurare la crittografia con chiavi gestite dal cliente](#configure-encryption-with-customer-managed-keys) e specificare il nome e la versione della nuova chiave. Se la nuova chiave si trova in un insieme di credenziali delle chiavi diverso, è necessario aggiornare anche l'URI dell'insieme di credenziali delle chiavi.

---

## <a name="revoke-customer-managed-keys"></a>Revocare le chiavi gestite dal cliente

Quando si revoca una chiave gestita dal cliente, viene rimossa l'associazione tra l'account di archiviazione e l'insieme di credenziali delle chiavi.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Per revocare le chiavi gestite dal cliente con la portale di Azure, disabilitare la chiave come descritto in [disabilitare le chiavi gestite dal cliente](#disable-customer-managed-keys).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

È possibile revocare le chiavi gestite dal cliente rimuovendo i criteri di accesso di Key Vault. Per revocare una chiave gestita dal cliente con PowerShell, chiamare il comando [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) , come illustrato nell'esempio seguente. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di usare le variabili definite negli esempi precedenti.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

È possibile revocare le chiavi gestite dal cliente rimuovendo i criteri di accesso di Key Vault. Per revocare una chiave gestita dal cliente con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ Key Vault Delete-Policy](/cli/azure/keyvault#az-keyvault-delete-policy) , come illustrato nell'esempio seguente. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di usare le variabili definite negli esempi precedenti.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

---

## <a name="disable-customer-managed-keys"></a>Disabilitare le chiavi gestite dal cliente

Quando si disabilitano le chiavi gestite dal cliente, l'account di archiviazione viene nuovamente crittografato con le chiavi gestite da Microsoft.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Per disabilitare le chiavi gestite dal cliente nel portale di Azure, attenersi alla procedura seguente:

1. Passare all'account di archiviazione e visualizzare le impostazioni di **crittografia** .
1. Deselezionare la casella di controllo accanto all'impostazione **Usa una chiave personalizzata** .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per disabilitare le chiavi gestite dal cliente con PowerShell, chiamare [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) con l' `-StorageEncryption` opzione, come illustrato nell'esempio seguente. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di usare le variabili definite negli esempi precedenti.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per disabilitare le chiavi gestite dal cliente con l'interfaccia della riga di comando di Azure, chiamare [AZ storage account Update](/cli/azure/storage/account#az-storage-account-update) e impostare `--encryption-key-source parameter` su `Microsoft.Storage` , come illustrato nell'esempio seguente. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di usare le variabili definite negli esempi precedenti.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

---

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md)
- [Chiavi gestite dal cliente per la crittografia di archiviazione di Azure](customer-managed-keys-overview.md)
- [Configurare la crittografia con chiavi gestite dal cliente archiviate nel modulo di protezione hardware Azure Key Vault gestito (anteprima)](customer-managed-keys-configure-key-vault-hsm.md)
