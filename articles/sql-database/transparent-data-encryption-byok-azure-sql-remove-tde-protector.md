---
title: 'PowerShell: Rimuovere una protezione TDE - Database SQL di Azure| Microsoft Docs'
description: Guida pratica per la gestione di una protezione TDE potenzialmente compromessa per un database SQL di Azure o un data warehouse che usa TDE con supporto Bring Your Own Key (BYOK).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: dc117dd844a3a47cafa1b37170c95fe852bb82ef
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566052"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Rimuovere una protezione TDE (Transparent Data Encryption) tramite PowerShell

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato dal database SQL di Azure, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Per questi cmdlet, vedere [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo AZ e nei moduli AzureRm sono sostanzialmente identici.

- È necessario disporre di una sottoscrizione di Azure e avere il ruolo di amministratore di tale sottoscrizione.
- È necessario che Azure PowerShell sia installato e in esecuzione. 
- Questa guida pratica presuppone che una chiave di Azure Key Vault sia già in uso come protezione TDE per un database SQL di Azure o un data warehouse. Per altre informazioni, vedere [Transparent Data Encryption with BYOK Support](transparent-data-encryption-byok-azure-sql.md) (Transparent Data Encryption con supporto BYOK).

## <a name="overview"></a>Panoramica

Questa guida pratica descrive come gestire una protezione TDE potenzialmente compromessa per un database SQL di Azure o un data warehouse che usa TDE con chiavi gestite dal cliente in Azure Key Vault e supporto Bring Your Own Key (BYOK). Per altre informazioni sul supporto BYOK per TDE, vedere la [pagina di panoramica](transparent-data-encryption-byok-azure-sql.md). 

Le procedure seguenti devono essere eseguite solo in casi estremi o in ambienti di test. Leggere attentamente la guida pratica poiché l'eliminazione di protezioni TDE in uso da Azure Key Vault può comportare una **perdita di dati**. 

Se si ha il sospetto che una chiave sia compromessa, ad esempio per un accesso non autorizzato alla chiave da parte di un utente o un servizio, è consigliabile eliminarla.

Tenere presente che, dopo l'eliminazione della protezione TDE in Key Vault, **tutte le connessioni ai database crittografati nel server sono bloccate e i database passano alla modalità offline e vengono rilasciati entro 24 ore**. I backup precedenti crittografati con la chiave compromessa non sono più accessibili.

Nei passaggi seguenti viene descritto come controllare le identificazioni personali della protezione Transparent Data Encryption ancora utilizzate dai file di log virtuali (VLF) di un determinato database. È possibile trovare l'identificazione personale della protezione Transparent Data Encryption del database e l'ID del database eseguendo: Select [database_id],       [encryption_state], [encryptor_type],/*asimmetrica Key significa AKV, certificate indica le chiavi gestite dal servizio*/[encryptor_thumbprint], da [sys]. [ dm_database_encryption_keys] 
 
La query seguente restituisce il VLF e le rispettive identificazioni personali di crittografia in uso. Ogni identificazione digitale diversa fa riferimento a una chiave diversa in Azure Key Vault (AKV): SELECT * FROM sys. dm _db_log_info (database_id) 

Il comando di PowerShell Get-AzureRmSqlServerKeyVaultKey fornisce l'identificazione personale della protezione Transparent Data Encryption utilizzata nella query, in modo che sia possibile visualizzare le chiavi da mantenere e le chiavi da eliminare in AKV. Solo le chiavi non più utilizzate dal database possono essere eliminate in modo sicuro dal Azure Key Vault.

Questa guida pratica descrive due approcci che si distinguono in base al risultato desiderato dopo la risposta all'evento imprevisto:

- Per mantenere **accessibili** i database SQL di Azure o i data warehouse
- Per rendere **inaccessibili** i database SQL di Azure o i data warehouse

## <a name="to-keep-the-encrypted-resources-accessible"></a>Per mantenere accessibili le risorse crittografate

1. Creare una [nuova chiave in Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Assicurarsi che la nuova chiave venga creata in un insieme di credenziali delle chiavi separato dalla protezione TDE potenzialmente compromessa, poiché il provisioning del controllo di accesso viene eseguito a livello di insieme di credenziali.
2. Aggiungere la nuova chiave al server usando i cmdlet [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) e [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) e aggiornarla come nuova protezione Transparent Data Encryption del server.

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   # Set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. Verificare che il server e tutte le repliche siano stati aggiornati alla nuova protezione Transparent Data Encryption usando il cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) . 

   >[!NOTE]
   > La propagazione della nuova protezione TDE a tutti i database e ai database secondari nel server può richiedere alcuni minuti.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Eseguire un copia di [backup della nuova chiave](/powershell/module/az.keyvault/backup-azkeyvaultkey) in Key Vault.

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. Eliminare la chiave compromessa da Key Vault usando il cmdlet [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) . 

   ```powershell
   Remove-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. Per ripristinare una chiave per Key Vault in futuro usando il cmdlet [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) :
   ```powershell
   Restore-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Per rendere inaccessibili le risorse crittografate

1. Rilasciare i database crittografati dalla chiave potenzialmente compromessa.

   Il backup dei file di database e log viene eseguito automaticamente. È quindi possibile eseguire un ripristino temporizzato del database in qualsiasi momento, purché si specifichi la chiave. I database devono essere rilasciati prima dell'eliminazione di una protezione TDE attiva per evitare possibili perdite di dati, per un intervallo massimo di 10 minuti, dalle transazioni più recenti. 
2. Eseguire il backup del materiale della chiave della protezione TDE in Key Vault.
3. Rimuovere la chiave potenzialmente compromessa da Key Vault.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come eseguire la rotazione della protezione TDE di un server per soddisfare i requisiti di sicurezza: [Ruotare la protezione Transparent Data Encryption tramite PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Introduzione al supporto Bring Your Own Key per TDE: [Abilitare TDE con la propria chiave di Key Vault usando PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
