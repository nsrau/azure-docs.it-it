---
title: Remove TDE protector - PowerShell
description: Guida pratica per la gestione di una protezione TDE potenzialmente compromessa per un database SQL di Azure o un data warehouse che usa TDE con supporto Bring Your Own Key (BYOK).
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
ms.openlocfilehash: 29971414219976f6d72caf30a909f1884b04aef7
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422428"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Rimuovere una protezione TDE (Transparent Data Encryption) tramite PowerShell

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre di una sottoscrizione di Azure e avere il ruolo di amministratore di tale sottoscrizione.
- You must have Azure PowerShell installed and running.
- Questa guida pratica presuppone che una chiave di Azure Key Vault sia già in uso come protezione TDE per un database SQL di Azure o un data warehouse. Per altre informazioni, vedere [Transparent Data Encryption with BYOK Support](transparent-data-encryption-byok-azure-sql.md) (Transparent Data Encryption con supporto BYOK).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

 Per istruzioni sull'installazione del modulo Az, vedere [Installare Azure PowerShell](/powershell/azure/install-az-ps). For specific cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> The PowerShell Azure Resource Manager (RM) module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. The AzureRM module will continue to receive bug fixes until at least December 2020.  The arguments for the commands in the Az module and in the AzureRm modules are substantially identical. For more about their compatibility, see [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az).

# <a name="azure-clitabazure-cli"></a>[interfaccia della riga di comando di Azure](#tab/azure-cli)

For installation, see [Install Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="overview"></a>Panoramica

Questa guida pratica descrive come gestire una protezione TDE potenzialmente compromessa per un database SQL di Azure o un data warehouse che usa TDE con chiavi gestite dal cliente in Azure Key Vault e supporto Bring Your Own Key (BYOK). Per altre informazioni sul supporto BYOK per TDE, vedere la [pagina di panoramica](transparent-data-encryption-byok-azure-sql.md).

Le procedure seguenti devono essere eseguite solo in casi estremi o in ambienti di test. Leggere attentamente la guida pratica poiché l'eliminazione di protezioni TDE in uso da Azure Key Vault può comportare una **perdita di dati**.

Se si ha il sospetto che una chiave sia compromessa, ad esempio per un accesso non autorizzato alla chiave da parte di un utente o un servizio, è consigliabile eliminarla.

Tenere presente che, dopo l'eliminazione della protezione TDE in Key Vault, **tutte le connessioni ai database crittografati nel server sono bloccate e i database passano alla modalità offline e vengono rilasciati entro 24 ore**. I backup precedenti crittografati con la chiave compromessa non sono più accessibili.

The following steps outline how to check the TDE Protector thumbprints still in use by Virtual Log Files (VLF) of a given database.
The thumbprint of the current TDE protector of the database, and the database ID can be found by running:

```sql
SELECT [database_id], 
       [encryption_state], 
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/ 
       [encryptor_thumbprint], 
 FROM [sys].[dm_database_encryption_keys]
```

The following query returns the VLFs and the encryptor respective thumbprints in use. Each different thumbprint refers to different key in Azure Key Vault (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

The PowerShell command **Get-AzureRmSqlServerKeyVaultKey** provides the thumbprint of the TDE Protector used in the query, so you can see which keys to keep and which keys to delete in AKV. Only keys no longer used by the database can be safely deleted from Azure Key Vault.

# <a name="azure-clitabazure-cli"></a>[interfaccia della riga di comando di Azure](#tab/azure-cli)

The PowerShell command **az sql server key show** provides the thumbprint of the TDE Protector used in the query, so you can see which keys to keep and which keys to delete in AKV. Only keys no longer used by the database can be safely deleted from Azure Key Vault.

* * *

Questa guida pratica descrive due approcci che si distinguono in base al risultato desiderato dopo la risposta all'evento imprevisto:

- Per mantenere **accessibili** i database SQL di Azure o i data warehouse
- Per rendere **inaccessibili** i database SQL di Azure o i data warehouse

## <a name="to-keep-the-encrypted-resources-accessible"></a>Per mantenere accessibili le risorse crittografate

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Creare una [nuova chiave in Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Assicurarsi che la nuova chiave venga creata in un insieme di credenziali delle chiavi separato dalla protezione TDE potenzialmente compromessa, poiché il provisioning del controllo di accesso viene eseguito a livello di insieme di credenziali.

2. Add the new key to the server using the [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) and [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlets and update it as the server’s new TDE protector.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Make sure the server and any replicas have updated to the new TDE protector using the [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet.

   > [!NOTE]
   > La propagazione della nuova protezione TDE a tutti i database e ai database secondari nel server può richiedere alcuni minuti.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Eseguire un copia di [backup della nuova chiave](/powershell/module/az.keyvault/backup-azkeyvaultkey) in Key Vault.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Delete the compromised key from Key Vault using the [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) cmdlet.

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. To restore a key to Key Vault in the future using the [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) cmdlet:

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-clitabazure-cli"></a>[interfaccia della riga di comando di Azure](#tab/azure-cli)

For command reference, see the [Azure CLI keyvault](/cli/azure/keyvault/key).

1. Creare una [nuova chiave in Key Vault](/cli/azure/keyvault/key#az-keyvault-key-create). Assicurarsi che la nuova chiave venga creata in un insieme di credenziali delle chiavi separato dalla protezione TDE potenzialmente compromessa, poiché il provisioning del controllo di accesso viene eseguito a livello di insieme di credenziali.

2. Add the new key to the server and update it as the server’s new TDE protector.

   ```powershell
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Make sure the server and any replicas have updated to the new TDE protector.

   > [!NOTE]
   > La propagazione della nuova protezione TDE a tutti i database e ai database secondari nel server può richiedere alcuni minuti.

   ```powershell
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Take a backup of the new key in Key Vault.

   ```powershell
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Delete the compromised key from Key Vault.

   ```powershell
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. To restore a key to Key Vault in the future.

   ```powershell
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Per rendere inaccessibili le risorse crittografate

1. Rilasciare i database crittografati dalla chiave potenzialmente compromessa.

   Il backup dei file di database e log viene eseguito automaticamente. È quindi possibile eseguire un ripristino temporizzato del database in qualsiasi momento, purché si specifichi la chiave. I database devono essere rilasciati prima dell'eliminazione di una protezione TDE attiva per evitare possibili perdite di dati, per un intervallo massimo di 10 minuti, dalle transazioni più recenti.

2. Eseguire il backup del materiale della chiave della protezione TDE in Key Vault.
3. Rimuovere la chiave potenzialmente compromessa da Key Vault.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come eseguire la rotazione della protezione TDE di un server per soddisfare i requisiti di sicurezza: [Rotate the Transparent Data Encryption protector Using PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md) (Ruotare la protezione Transparent Data Encryption tramite PowerShell)
- Introduzione al supporto Bring Your Own Key per TDE: [Abilitare TDE tramite la propria chiave da Key Vault usando PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
