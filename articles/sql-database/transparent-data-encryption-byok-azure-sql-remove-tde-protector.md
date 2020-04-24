---
title: Rimuovere la protezione Transparent Data Encryption-PowerShell
description: Guida pratica per la gestione di una protezione TDE potenzialmente compromessa per un database SQL di Azure o un data warehouse che usa TDE con supporto Bring Your Own Key (BYOK).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: 64f3f3c1a4fa656edc7163f6c8ee0ad60f117be6
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116571"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Rimuovere una protezione TDE (Transparent Data Encryption) tramite PowerShell

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre di una sottoscrizione di Azure e avere il ruolo di amministratore di tale sottoscrizione.
- È necessario che Azure PowerShell sia installato e in esecuzione.
- Questa guida pratica presuppone che una chiave di Azure Key Vault sia già in uso come protezione TDE per un database SQL di Azure o un data warehouse. Per altre informazioni, vedere [Transparent Data Encryption with BYOK Support](transparent-data-encryption-byok-azure-sql.md) (Transparent Data Encryption con supporto BYOK).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

 Per istruzioni sull'installazione del modulo Az, vedere [Installare Azure PowerShell](/powershell/azure/install-az-ps). Per i cmdlet specifici, vedere [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell (RM) è ancora supportato dal database SQL di Azure, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Il modulo AzureRM continuerà a ricevere correzioni di bug fino ad almeno il 2020 dicembre.  Gli argomenti per i comandi nel modulo AZ e nei moduli AzureRm sono sostanzialmente identici. Per altre informazioni sulla compatibilità, vedere [Introduzione al nuovo Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az).

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per l'installazione, vedere [Install Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="overview"></a>Panoramica

Questa guida pratica descrive come gestire una protezione TDE potenzialmente compromessa per un database SQL di Azure o un data warehouse che usa TDE con chiavi gestite dal cliente in Azure Key Vault e supporto Bring Your Own Key (BYOK). Per altre informazioni sul supporto BYOK per TDE, vedere la [pagina di panoramica](transparent-data-encryption-byok-azure-sql.md).

Le procedure seguenti devono essere eseguite solo in casi estremi o in ambienti di test. Esaminare attentamente le procedure guidate, poiché l'eliminazione di protezioni Transparent Data Encryption da Azure Key Vault comporterà la mancata **disponibilità del database**.

Se si sospetta che una chiave sia compromessa, in modo che un servizio o un utente abbia accesso non autorizzato alla chiave, è consigliabile eliminare la chiave.

Tenere presente che una volta eliminata la protezione Transparent Data Encryption in Key Vault, in un massimo di 10 minuti tutti i database crittografati inizieranno a negare tutte le connessioni con il messaggio di errore corrispondente e a impostare lo stato su [inaccessibile](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql#inaccessible-tde-protector).

Nei passaggi seguenti viene descritto come controllare le identificazioni personali della protezione Transparent Data Encryption ancora utilizzate dai file di log virtuali (VLF) di un determinato database.
È possibile trovare l'identificazione personale della protezione Transparent Data Encryption del database e l'ID del database eseguendo:

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint]
 FROM [sys].[dm_database_encryption_keys]
```

La query seguente restituisce il VLF e le rispettive identificazioni personali di crittografia in uso. Ogni identificazione digitale diversa fa riferimento a una chiave diversa in Azure Key Vault (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Il comando di PowerShell **Get-AzureRmSqlServerKeyVaultKey** fornisce l'identificazione personale della protezione Transparent Data Encryption utilizzata nella query, in modo che sia possibile visualizzare le chiavi da mantenere e le chiavi da eliminare in AKV. Solo le chiavi non più utilizzate dal database possono essere eliminate in modo sicuro dal Azure Key Vault.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Il comando di PowerShell **AZ SQL Server Key Show** fornisce l'identificazione personale della protezione Transparent Data Encryption usata nella query, in modo da poter vedere quali sono le chiavi da mantenere e quali chiavi eliminare in AKV. Solo le chiavi non più utilizzate dal database possono essere eliminate in modo sicuro dal Azure Key Vault.

* * *

Questa guida pratica descrive due approcci che si distinguono in base al risultato desiderato dopo la risposta all'evento imprevisto:

- Per rendere **accessibili** i database/data warehouse SQL di Azure
- Per rendere **inaccessibili** i database/data warehouse SQL di Azure

## <a name="to-keep-the-encrypted-resources-accessible"></a>Per mantenere accessibili le risorse crittografate

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Creare una [nuova chiave in Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Assicurarsi che la nuova chiave venga creata in un insieme di credenziali delle chiavi separato dalla protezione TDE potenzialmente compromessa, poiché il provisioning del controllo di accesso viene eseguito a livello di insieme di credenziali.

2. Aggiungere la nuova chiave al server usando i cmdlet [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) e [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) e aggiornarla come nuova protezione Transparent Data Encryption del server.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Verificare che il server e tutte le repliche siano stati aggiornati alla nuova protezione Transparent Data Encryption usando il cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) .

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

5. Eliminare la chiave compromessa da Key Vault usando il cmdlet [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) .

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Per ripristinare una chiave per Key Vault in futuro usando il cmdlet [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) :

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per informazioni di riferimento sui comandi, vedere l'insieme di credenziali delle credenziali di [Azure](/cli/azure/keyvault/key).

1. Creare una [nuova chiave in Key Vault](/cli/azure/keyvault/key#az-keyvault-key-create). Assicurarsi che la nuova chiave venga creata in un insieme di credenziali delle chiavi separato dalla protezione TDE potenzialmente compromessa, poiché il provisioning del controllo di accesso viene eseguito a livello di insieme di credenziali.

2. Aggiungere la nuova chiave al server e aggiornarla come nuova protezione Transparent Data Encryption del server.

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Verificare che il server e tutte le repliche siano stati aggiornati alla nuova protezione Transparent Data Encryption.

   > [!NOTE]
   > La propagazione della nuova protezione TDE a tutti i database e ai database secondari nel server può richiedere alcuni minuti.

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Eseguire un copia di backup della nuova chiave in Key Vault.

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Eliminare la chiave compromessa dal Key Vault.

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. Per ripristinare una chiave per Key Vault in futuro.

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Per rendere inaccessibili le risorse crittografate

1. Rilasciare i database crittografati dalla chiave potenzialmente compromessa.

   Il backup dei file di database e log viene eseguito automaticamente. È quindi possibile eseguire un ripristino temporizzato del database in qualsiasi momento, purché si specifichi la chiave. I database devono essere rilasciati prima dell'eliminazione di una protezione TDE attiva per evitare possibili perdite di dati, per un intervallo massimo di 10 minuti, dalle transazioni più recenti.

2. Eseguire il backup del materiale della chiave della protezione TDE in Key Vault.
3. Rimuovere la chiave potenzialmente compromessa da Key Vault.

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come eseguire la rotazione della protezione TDE di un server per soddisfare i requisiti di sicurezza: [Rotate the Transparent Data Encryption protector Using PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md) (Ruotare la protezione Transparent Data Encryption tramite PowerShell)
- Introduzione al supporto Bring Your Own Key per TDE: [Abilitare TDE tramite la propria chiave da Key Vault usando PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
