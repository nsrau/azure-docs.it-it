---
title: 'PowerShell e interfaccia della riga di comando: Abilitare TDE SQL - con Azure Key Vault - Bring Your Own Key - Database SQL di Azure | Microsoft Docs'
description: Informazioni su come configurare un database SQL di Azure e Data Warehouse per iniziare a usare Transparent Data Encryption (TDE) per la crittografia dei dati inattivi tramite PowerShell o CLI.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: c42c6175512105de38a29be260c370851e152137
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60330874"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell e interfaccia della riga di comando: Abilitare Transparent Data Encryption con una chiave di Azure Key Vault gestita dal cliente

Questo articolo illustra come usare una chiave di Azure Key Vault per Transparent Data Encryption (TDE) in un database SQL o Data Warehouse. Per altre informazioni sull'integrazione di TDE con Azure Key Vault e sulla possibilità di usare chiavi personalizzate o BYOK (Bring Your Own Key), vedere [TDE con chiavi gestite dal cliente in Azure Key Vault](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>Prerequisiti di PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure PowerShell per Resource Manager è ancora supportato dal Database SQL di Azure, ma i progetti di sviluppo future è per il modulo Az.Sql. Per questi cmdlet, vedere [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo Az e nei moduli AzureRm sono sostanzialmente identici.

- È necessario disporre di una sottoscrizione di Azure e avere il ruolo di amministratore di tale sottoscrizione.
- [Consigliata ma facoltativa] Disporre di un modulo di protezione hardware (HSM) o una chiave locale già archiviati a scopo di creazione di una copia locale del materiale della chiave di protezione TDE.
- È necessario disporre di Azure PowerShell installato e in esecuzione. 
- Creare le chiavi e un Azure Key Vault per l'uso con TDE.
  - [Istruzioni di PowerShell da Key Vault](../key-vault/key-vault-overview.md)
  - [Istruzioni per l'uso di un modulo di protezione hardware (HSM) e di Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - L'insieme di credenziali delle chiavi deve avere la seguente proprietà da usare per TDE:
  - [Eliminazione temporanea](../key-vault/key-vault-ovw-soft-delete.md)
  - [Come usare l'eliminazione temporanea di Key Vault con PowerShell](../key-vault/key-vault-soft-delete-powershell.md) 
- La chiave deve avere i seguenti attributi per essere usata per TDE:
   - Nessuna data di scadenza
   - Non disabilitato
   - In grado di eseguire le operazioni *Ottieni*, *Esegui il wrapping della chiave*, *Annulla il wrapping della chiave*

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>Passaggio 1. Assegnare un'identità Azure AD al server 

Se si dispone di un server esistente, usare quanto segue per aggiungere un'identità Azure AD al server:

   ```powershell
   $server = Set-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

Se si sta creando un server, usare il [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) cmdlet con il tag-Identity per aggiungere un'identità Azure AD durante la creazione del server:

   ```powershell
   $server = New-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Passaggio 2. Concedere le autorizzazioni di Key Vault al server

Usare la [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet per concedere l'accesso al server per la chiave dell'insieme di credenziali prima di usare una chiave da quest'ultimo per TDE.

   ```powershell
   Set-AzKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Passaggio 3. Aggiungere la chiave di Key Vault al server e impostare la protezione TDE

- Usare la [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) cmdlet per aggiungere la chiave da Key Vault al server.
- Usare la [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet per impostare la chiave come protezione TDE per tutte le risorse del server.
- Usare la [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet per verificare che la protezione TDE è stata configurata nel modo previsto.

> [!Note]
> La lunghezza combinata per il nome dell'insieme di credenziali delle chiavi non può superare 94 caratteri.
> 

>[!Tip]
>Un KeyId di esempio da Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>Passaggio 4. Attivare la crittografia TDE 

Usare la [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet per abilitare TDE.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

A questo punto il database o il data warehouse ha abilitato TDE con una chiave di crittografia in Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Passaggio 5. Controllare lo stato della crittografia e l'attività di crittografia

Usare la [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) per ottenere lo stato di crittografia e la [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) per controllare l'avanzamento della crittografia per un database o data warehouse di.

   ```powershell
   # Get the encryption state
   Get-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>Altri cmdlet PowerShell utili

- Usare la [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet per disattivare TDE.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled”
   ```
 
- Usare la [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet per restituire l'elenco delle chiavi di Key Vault aggiunte al server.

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- Usare la [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) per rimuovere una chiave di Key Vault dal server.

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>risoluzione dei problemi

Se si verifica un problema, controllare quanto segue:
- Se l'insieme di credenziali delle chiavi non viene trovato, verificare che sia attiva la sottoscrizione a destra con il [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) cmdlet.

   ```powershell
   Get-AzSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- Se non è possibile aggiungere la nuova chiave al server o la nuova chiave non può essere aggiornata come protezione TDE, verificare quanto segue:
   - La chiave non deve avere una data di scadenza
   - La chiave deve avere le operazioni *Ottieni*, *Esegui il wrapping della chiave*, *Annulla il wrapping della chiave*.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come eseguire la rotazione della protezione TDE di un server per soddisfare i requisiti di sicurezza: [Ruotare la protezione Transparent Data Encryption tramite PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- In caso di rischio per la sicurezza, vedere come rimuovere una protezione TDE potenzialmente compromessa: [Rimuovere una chiave potenzialmente compromessa](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md). 

## <a name="prerequisites-for-cli"></a>Prerequisiti per CLI

- È necessario disporre di una sottoscrizione di Azure e avere il ruolo di amministratore di tale sottoscrizione.
- [Consigliata ma facoltativa] Disporre di un modulo di protezione hardware (HSM) o una chiave locale già archiviati a scopo di creazione di una copia locale del materiale della chiave di protezione TDE.
- Interfaccia della riga di comando versione 2.0 o successiva. Per installare l'ultima versione e associarla alla sottoscrizione di Azure, vedere [Install and Configure the Azure Cross-Platform Command-Line Interface 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (Installare e configurare l'interfaccia della riga di comando multipiattaforma di Azure 2.0). 
- Creare le chiavi e un Azure Key Vault per l'uso con TDE.
  - [Gestire Key Vault tramite l'interfaccia della riga di comando 2.0](../key-vault/key-vault-manage-with-cli2.md)
  - [Istruzioni per l'uso di un modulo di protezione hardware (HSM) e di Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - L'insieme di credenziali delle chiavi deve avere la seguente proprietà da usare per TDE:
  - [Eliminazione temporanea](../key-vault/key-vault-ovw-soft-delete.md)
  - [Come usare l'eliminazione temporanea di Key Vault con l'interfaccia della riga di comando](../key-vault/key-vault-soft-delete-cli.md) 
- La chiave deve avere i seguenti attributi per essere usata per TDE:
   - Nessuna data di scadenza
   - Non disabilitato
   - In grado di eseguire le operazioni *Ottieni*, *Esegui il wrapping della chiave*, *Annulla il wrapping della chiave*
   
## <a name="step-1-create-a-server-with-an-azure-ad-identity"></a>Passaggio 1. Creare un server con un'identità di Azure AD
      cli
      # create server (with identity) and database
      az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
      az sql db create --name <dbname> --server <servername> --resource-group <rgname>  
 
 
>[!Tip]
>Conservare il valore "principalID" della fase di creazione del server, è l'ID oggetto usato per assegnare le autorizzazioni di Key Vault nel passaggio successivo
>
 
## <a name="step-2-grant-key-vault-permissions-to-the-logical-sql-server"></a>Passaggio 2. Concedere le autorizzazioni di Key Vault al server SQL logico
      cli
      # create key vault, key and grant permission
       az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
       az keyvault key create --name <keyname> --vault-name <kvname> --protection software
       az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get 


>[!Tip]
>Conservare l'URI della chiave o il valore keyID della nuova chiave per il passaggio successivo, ad esempio: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
 
       
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Passaggio 3. Aggiungere la chiave di Key Vault al server e impostare la protezione TDE
  
     cli
     # add server key and update encryption protector
     az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
     az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>

        
  > [!Note]
> La lunghezza combinata per il nome dell'insieme di credenziali delle chiavi non può superare 94 caratteri.
> 

  
## <a name="step-4-turn-on-tde"></a>Passaggio 4. Attivare la crittografia TDE 
      cli
      # enable encryption
      az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled 
      

A questo punto il database o il data warehouse ha abilitato TDE con una chiave di crittografia gestita dal cliente in Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Passaggio 5. Controllare lo stato della crittografia e l'attività di crittografia

     cli
      # get encryption scan progress
      az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

      # get whether encryption is on or off
      az sql db tde show --database <dbname> --server <servername> --resource-group <rgname> 

## <a name="sql-cli-references"></a>Informazioni di riferimento sull'interfaccia della riga di comando SQL

https://docs.microsoft.com/cli/azure/sql 

https://docs.microsoft.com/cli/azure/sql/server/key 

https://docs.microsoft.com/cli/azure/sql/server/tde-key 

https://docs.microsoft.com/cli/azure/sql/db/tde 

