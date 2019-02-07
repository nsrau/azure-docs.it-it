---
title: 'PowerShell e interfaccia della riga di comando: abilitare TDE in SQL - Chiavi - Database SQL di Azure | Microsoft Docs'
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
ms.date: 12/04/2018
ms.openlocfilehash: be73f5cb7db232538f301b2eb56bf61267fce5d5
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2019
ms.locfileid: "55566721"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-using-your-own-key-from-azure-key-vault"></a>PowerShell e interfaccia della riga di comando: abilitare Transparent Data Encryption usando la propria chiave di Azure Key Vault

Questo articolo illustra come usare una chiave di Azure Key Vault per Transparent Data Encryption (TDE) in un database SQL o Data Warehouse. Per altre informazioni su TDE con il supporto Bring Your Own Key (BYOK), visitare [Bring Your Own Key di TDE per SQL Azure](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>Prerequisiti di PowerShell

- È necessario disporre di una sottoscrizione di Azure e avere il ruolo di amministratore di tale sottoscrizione.
- [Consigliata ma facoltativa] Disporre di un modulo di protezione hardware (HSM) o una chiave locale già archiviati a scopo di creazione di una copia locale del materiale della chiave di protezione TDE.
- È necessario che sia installato e sia esecuzione Azure PowerShell 4.2.0 o una versione più recente. 
- Creare le chiavi e un Azure Key Vault per l'uso con TDE.
   - [Istruzioni di PowerShell da Key Vault](../key-vault/key-vault-get-started.md)
   - [Istruzioni per l'uso di un modulo di protezione hardware (HSM) e di Key Vault](../key-vault/key-vault-get-started.md#HSM)
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
   $server = Set-AzureRmSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

Se si sta creando un server, usare il cmdlet [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) con il tag Identità per aggiungere un'identità Azure AD durante la creazione del server:

   ```powershell
   $server = New-AzureRmSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Passaggio 2. Concedere le autorizzazioni di Key Vault al server

Usare il cmdlet [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) per concedere l'accesso al server all'insieme di credenziali delle chiavi prima di usare una chiave da quest'ultimo per TDE.

   ```powershell
   Set-AzureRmKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Passaggio 3. Aggiungere la chiave di Key Vault al server e impostare la protezione TDE

- Usare il cmdlet [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) per aggiungere al server la chiave da Key Vault.
- Usare il cmdlet [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) per impostare la chiave come protezione TDE per tutte le risorse del server.
- Usare il cmdlet [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) per confermare che la protezione TDE è configurata come prestabilito.

> [!Note]
> La lunghezza combinata per il nome dell'insieme di credenziali delle chiavi non può superare 94 caratteri.
> 

>[!Tip]
>Un KeyId di esempio da Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzureRmSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>Passaggio 4. Attivare la crittografia TDE 

Usare il cmdlet [Set-AzureRmSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) per attivare la TDE.

   ```powershell
   Set-AzureRMSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

A questo punto il database o il data warehouse ha abilitato TDE con una chiave di crittografia in Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Passaggio 5. Controllare lo stato della crittografia e l'attività di crittografia

Usare [Get-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption) per ottenere lo stato di crittografia e [Get-AzureRMSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryptionactivity) per controllare l'avanzamento della crittografia per un database o un data warehouse.

   ```powershell
   # Get the encryption state
   Get-AzureRMSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzureRMSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>Altri cmdlet PowerShell utili

- Usare il cmdlet [Set-AzureRmSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) per disattivare la TDE.

   ```powershell
   Set-AzureRMSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled”
   ```
 
- Usare il cmdlet [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) per restituire l'elenco delle chiavi di Key Vault aggiunte al server.

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzureRmSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- Usare [Remove-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/remove-azurermsqlserverkeyvaultkey) per rimuovere una chiave di Key Vault dal server.

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzureRmSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>risoluzione dei problemi

Se si verifica un problema, controllare quanto segue:
- Se l'insieme di credenziali delle chiavi non viene trovato, verificare che sia attiva la sottoscrizione corretta con il cmdlet [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

   ```powershell
   Get-AzureRmSubscription `
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
- Interfaccia della riga di comando versione 2.0 o successiva. Per installare l'ultima versione e associarla alla sottoscrizione di Azure, vedere [Install and Configure the Azure Cross-Platform Command-Line Interface 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) (Installare e configurare l'interfaccia della riga di comando multipiattaforma di Azure 2.0). 
- Creare le chiavi e un Azure Key Vault per l'uso con TDE.
   - [Gestire Key Vault tramite l'interfaccia della riga di comando 2.0](../key-vault/key-vault-manage-with-cli2.md)
   - [Istruzioni per l'uso di un modulo di protezione hardware (HSM) e di Key Vault](../key-vault/key-vault-get-started.md#HSM)
 - L'insieme di credenziali delle chiavi deve avere la seguente proprietà da usare per TDE:
   - [Eliminazione temporanea](../key-vault/key-vault-ovw-soft-delete.md)
   - [Come usare l'eliminazione temporanea di Key Vault con l'interfaccia della riga di comando](../key-vault/key-vault-soft-delete-cli.md) 
- La chiave deve avere i seguenti attributi per essere usata per TDE:
   - Nessuna data di scadenza
   - Non disabilitato
   - In grado di eseguire le operazioni *Ottieni*, *Esegui il wrapping della chiave*, *Annulla il wrapping della chiave*
   
## <a name="step-1-create-a-server-and-assign-an-azure-ad-identity-to-your-server"></a>Passaggio 1. Creare un server e assegnare un'identità Azure AD al server
      cli
      # create server (with identity) and database
      az sql server create -n "ServerName" -g "ResourceGroupName" -l "westus" -u "cloudsa" -p "YourFavoritePassWord99@34" -i 
      az sql db create -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 
      

 
## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Passaggio 2. Concedere le autorizzazioni di Key Vault al server
      cli
      # create key vault, key and grant permission
      az keyvault create -n "VaultName" -g "ResourceGroupName" 
      az keyvault key create -n myKey -p software --vault-name "VaultName" 
      az keyvault set-policy -n "VaultName" --object-id "ServerIdentityObjectId" -g "ResourceGroupName" --key-permissions wrapKey unwrapKey get list 
      

 
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Passaggio 3. Aggiungere la chiave di Key Vault al server e impostare la protezione TDE
  
     cli
     # add server key and update encryption protector
      az sql server key create -g "ResourceGroupName" -s "ServerName" -t "AzureKeyVault" -u "FullVersionedKeyUri 
      az sql server tde-key update -g "ResourceGroupName" -s "ServerName" -t AzureKeyVault -u "FullVersionedKeyUri" 
      
  
  > [!Note]
> La lunghezza combinata per il nome dell'insieme di credenziali delle chiavi non può superare 94 caratteri.
> 

>[!Tip]
>Un KeyId di esempio da Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
  
## <a name="step-4-turn-on-tde"></a>Passaggio 4. Attivare la crittografia TDE 
      cli
      # enable encryption
      az sql db tde create -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" --status Enabled 
      

A questo punto il database o il data warehouse ha abilitato TDE con una chiave di crittografia in Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Passaggio 5. Controllare lo stato della crittografia e l'attività di crittografia

     cli
      # get encryption scan progress
      az sql db tde show-activity -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 

      # get whether encryption is on or off
      az sql db tde show-configuration -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 

## <a name="sql-cli-references"></a>Informazioni di riferimento sull'interfaccia della riga di comando SQL

https://docs.microsoft.com/cli/azure/sql?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/server/key?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/server/tde-key?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/db/tde?view=azure-cli-latest 

