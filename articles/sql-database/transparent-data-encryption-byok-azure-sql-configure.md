---
title: Enable SQL TDE with Azure Key Vault
description: Informazioni su come configurare un database SQL di Azure e Data Warehouse per iniziare a usare Transparent Data Encryption (TDE) per la crittografia dei dati inattivi tramite PowerShell o CLI.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: e60b83f8eb1e95733512180417bb2a1be679b0a0
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422458"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell and CLI: Enable Transparent Data Encryption with customer-managed key from Azure Key Vault

Questo articolo illustra come usare una chiave di Azure Key Vault per Transparent Data Encryption (TDE) in un database SQL o Data Warehouse. Per altre informazioni sull'integrazione di TDE con Azure Key Vault e sulla possibilità di usare chiavi personalizzate o BYOK (Bring Your Own Key), vedere [TDE con chiavi gestite dal cliente in Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites-for-powershell"></a>Prerequisiti di PowerShell

- È necessario disporre di una sottoscrizione di Azure e avere il ruolo di amministratore di tale sottoscrizione.
- [Consigliata ma facoltativa] Disporre di un modulo di protezione hardware (HSM) o una chiave locale già archiviati a scopo di creazione di una copia locale del materiale della chiave di protezione TDE.
- You must have Azure PowerShell installed and running.
- Creare le chiavi e un Azure Key Vault per l'uso con TDE.
  - [Istruzioni per l'uso di un modulo di protezione hardware (HSM) e di Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - L'insieme di credenziali delle chiavi deve avere la seguente proprietà da usare per TDE:
  - [soft-delete](../key-vault/key-vault-ovw-soft-delete.md) and purge protection
- La chiave deve avere i seguenti attributi per essere usata per TDE:
   - Nessuna data di scadenza
   - Non disabilitato
   - In grado di eseguire le operazioni *Ottieni*, *Esegui il wrapping della chiave*, *Annulla il wrapping della chiave*

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Per istruzioni sull'installazione del modulo Az, vedere [Installare Azure PowerShell](/powershell/azure/install-az-ps). For specific cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

For specifics on Key Vault, see [PowerShell instructions from Key Vault](../key-vault/quick-create-powershell.md) and [How to use Key Vault soft-delete with PowerShell](../key-vault/key-vault-soft-delete-powershell.md).

> [!IMPORTANT]
> The PowerShell Azure Resource Manager (RM) module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. The AzureRM module will continue to receive bug fixes until at least December 2020.  The arguments for the commands in the Az module and in the AzureRm modules are substantially identical. For more about their compatibility, see [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Assegnare un'identità Azure AD al server

Se si dispone di un server esistente, usare quanto segue per aggiungere un'identità Azure AD al server:

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

If you are creating a server, use the [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) cmdlet with the tag -Identity to add an Azure AD identity during server creation:

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>Concedere le autorizzazioni di Key Vault al server

Use the [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet to grant your server access to the key vault before using a key from it for TDE.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Aggiungere la chiave di Key Vault al server e impostare la protezione TDE

- Use the [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) cmdlet to retrieve the key ID from key vault
- Use the [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) cmdlet to add the key from the Key Vault to the server.
- Use the [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet to set the key as the TDE protector for all server resources.
- Use the [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet to confirm that the TDE protector was configured as intended.

> [!NOTE]
> La lunghezza combinata per il nome dell'insieme di credenziali delle chiavi non può superare 94 caratteri.

> [!TIP]
> Un KeyId di esempio da Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

```powershell
# add the key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId>

# confirm the TDE protector was configured as intended
Get-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
```

## <a name="turn-on-tde"></a>Attivare la crittografia TDE

Use the [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet to turn on TDE.

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

A questo punto il database o il data warehouse ha abilitato TDE con una chiave di crittografia in Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Controllare lo stato della crittografia e l'attività di crittografia

Use the [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) to get the encryption state and the [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) to check the encryption progress for a database or data warehouse.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="azure-clitabazure-cli"></a>[interfaccia della riga di comando di Azure](#tab/azure-cli)

To install the required Command-Line Interface version 2.0 or later and connect to your Azure subscription, see [Install and Configure the Azure Cross-Platform Command-Line Interface 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

For specifics on Key Vault, see [Manage Key Vault using CLI 2.0](../key-vault/key-vault-manage-with-cli2.md) and [How to use Key Vault soft-delete with CLI](../key-vault/key-vault-soft-delete-cli.md).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Assegnare un'identità Azure AD al server

```powershell
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> Conservare il valore "principalID" della fase di creazione del server, è l'ID oggetto usato per assegnare le autorizzazioni di Key Vault nel passaggio successivo

## <a name="grant-key-vault-permissions-to-your-server"></a>Concedere le autorizzazioni di Key Vault al server

```powershell
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> Conservare l'URI della chiave o il valore keyID della nuova chiave per il passaggio successivo, ad esempio: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Aggiungere la chiave di Key Vault al server e impostare la protezione TDE

```powershell
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> La lunghezza combinata per il nome dell'insieme di credenziali delle chiavi non può superare 94 caratteri.

## <a name="turn-on-tde"></a>Attivare la crittografia TDE

```powershell
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

A questo punto il database o il data warehouse ha abilitato TDE con una chiave di crittografia gestita dal cliente in Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Controllare lo stato della crittografia e l'attività di crittografia

```powershell
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Cmdlet PowerShell utili

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

- Use the [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet to turn off TDE.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled”
   ```

- Use the [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet to return the list of Key Vault keys added to the server.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- Use the [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) to remove a Key Vault key from the server.

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-clitabazure-cli"></a>[interfaccia della riga di comando di Azure](#tab/azure-cli)

- For general database settings, see [az sql](/cli/azure/sql).

- For vault key settings, see [az sql server key](/cli/azure/sql/server/key).

- For TDE settings, see [az sql server tde-key](/cli/azure/sql/server/tde-key) and [az sql db tde](/cli/azure/sql/db/tde).

* * *

## <a name="troubleshooting"></a>risoluzione dei problemi

Se si verifica un problema, controllare quanto segue:

- If the key vault cannot be found, make sure you're in the right subscription.

   # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="azure-clitabazure-cli"></a>[interfaccia della riga di comando di Azure](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- Se non è possibile aggiungere la nuova chiave al server o la nuova chiave non può essere aggiornata come protezione TDE, verificare quanto segue:
   - La chiave non deve avere una data di scadenza
   - La chiave deve avere le operazioni *Ottieni*, *Esegui il wrapping della chiave*, *Annulla il wrapping della chiave*.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come eseguire la rotazione della protezione TDE di un server per soddisfare i requisiti di sicurezza: [Rotate the Transparent Data Encryption protector Using PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md) (Ruotare la protezione Transparent Data Encryption tramite PowerShell).
- In caso di rischio per la sicurezza, consultare [Rimuovere una chiave potenzialmente compromessa](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) per informazioni su come rimuovere una protezione TDE potenzialmente compromessa.
