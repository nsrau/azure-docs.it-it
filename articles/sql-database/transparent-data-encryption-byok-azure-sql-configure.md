---
title: Abilitare SQL Transparent Data Encryption con Azure Key Vault
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
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell e CLI: abilitare Transparent Data Encryption con la chiave gestita dal cliente da Azure Key Vault

Questo articolo illustra come usare una chiave di Azure Key Vault per Transparent Data Encryption (TDE) in un database SQL o Data Warehouse. Per altre informazioni sull'integrazione di TDE con Azure Key Vault e sulla possibilità di usare chiavi personalizzate o BYOK (Bring Your Own Key), vedere [TDE con chiavi gestite dal cliente in Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites-for-powershell"></a>Prerequisiti di PowerShell

- È necessario disporre di una sottoscrizione di Azure e avere il ruolo di amministratore di tale sottoscrizione.
- [Consigliata ma facoltativa] Disporre di un modulo di protezione hardware (HSM) o una chiave locale già archiviati a scopo di creazione di una copia locale del materiale della chiave di protezione TDE.
- È necessario che Azure PowerShell sia installato e in esecuzione.
- Creare le chiavi e un Azure Key Vault per l'uso con TDE.
  - [Istruzioni per l'uso di un modulo di protezione hardware (HSM) e di Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - L'insieme di credenziali delle chiavi deve avere la seguente proprietà da usare per TDE:
  - [eliminazione](../key-vault/key-vault-ovw-soft-delete.md) temporanea e ripulitura della protezione
- La chiave deve avere i seguenti attributi per essere usata per TDE:
   - Nessuna data di scadenza
   - Non disabilitato
   - In grado di eseguire le operazioni *Ottieni*, *Esegui il wrapping della chiave*, *Annulla il wrapping della chiave*

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Per istruzioni sull'installazione del modulo Az, vedere [Installare Azure PowerShell](/powershell/azure/install-az-ps). Per i cmdlet specifici, vedere [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

Per informazioni specifiche su Key Vault, vedere [istruzioni di PowerShell da Key Vault](../key-vault/quick-create-powershell.md) e [come usare Key Vault soft-delete con PowerShell](../key-vault/key-vault-soft-delete-powershell.md).

> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell (RM) è ancora supportato dal database SQL di Azure, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Il modulo AzureRM continuerà a ricevere correzioni di bug fino ad almeno il 2020 dicembre.  Gli argomenti per i comandi nel modulo AZ e nei moduli AzureRm sono sostanzialmente identici. Per altre informazioni sulla compatibilità, vedere [Introduzione al nuovo Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Assegnare un'identità Azure AD al server

Se si dispone di un server esistente, usare quanto segue per aggiungere un'identità Azure AD al server:

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

Se si sta creando un server, usare il cmdlet [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) con tag-identity per aggiungere un'identità Azure ad durante la creazione del server:

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>Concedere le autorizzazioni di Key Vault al server

Usare il cmdlet [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) per concedere l'accesso al server all'insieme di credenziali delle chiavi prima di usare una chiave per Transparent Data Encryption.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Aggiungere la chiave di Key Vault al server e impostare la protezione TDE

- Usare il cmdlet [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) per recuperare l'ID chiave da Key Vault
- Usare il cmdlet [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) per aggiungere la chiave dal Key Vault al server.
- Usare il cmdlet [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) per impostare la chiave come protezione Transparent Data Encryption per tutte le risorse del server.
- Usare il cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) per verificare che la protezione Transparent Data Encryption sia stata configurata come previsto.

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

Usare il cmdlet [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) per attivare Transparent Data Encryption.

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

A questo punto il database o il data warehouse ha abilitato TDE con una chiave di crittografia in Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Controllare lo stato della crittografia e l'attività di crittografia

Usare [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) per ottenere lo stato di crittografia e [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) per controllare lo stato della crittografia per un database o data warehouse.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per installare l'interfaccia della riga di comando richiesta 2,0 o versione successiva e connettersi alla sottoscrizione di Azure, vedere [installare e configurare l'interfaccia della riga di comando multipiattaforma di azure 2,0](https://docs.microsoft.com/cli/azure/install-azure-cli).

Per informazioni specifiche su Key Vault, vedere [gestire Key Vault con l'interfaccia della riga](../key-vault/key-vault-manage-with-cli2.md) di comando 2,0 e [come usare Key Vault soft-delete con l'interfaccia](../key-vault/key-vault-soft-delete-cli.md)della riga di comando.

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

- Usare il cmdlet [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) per disattivare Transparent Data Encryption.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled”
   ```

- Usare il cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) per restituire l'elenco di chiavi di Key Vault aggiunte al server.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- Usare [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) per rimuovere una chiave di Key Vault dal server.

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

- Per le impostazioni generali del database, vedere [AZ SQL](/cli/azure/sql).

- Per le impostazioni della chiave dell'insieme di credenziali, vedere [AZ SQL Server Key](/cli/azure/sql/server/key).

- Per le impostazioni di Transparent Data Encryption, vedere [AZ SQL Server Encryption-Key](/cli/azure/sql/server/tde-key) e [AZ SQL DB](/cli/azure/sql/db/tde)Transparent.

* * *

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verifica un problema, controllare quanto segue:

- Se non è possibile trovare l'insieme di credenziali delle chiavi, assicurarsi di trovarsi nella sottoscrizione corretta.

   # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

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
