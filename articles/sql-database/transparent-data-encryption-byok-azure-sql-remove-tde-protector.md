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
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: ad7e760bf84ee08e3928164432564fb23c10d211
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60330653"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Rimuovere una protezione TDE (Transparent Data Encryption) tramite PowerShell

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure PowerShell per Resource Manager è ancora supportato dal Database SQL di Azure, ma i progetti di sviluppo future è per il modulo Az.Sql. Per questi cmdlet, vedere [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo Az e nei moduli AzureRm sono sostanzialmente identici.

- È necessario disporre di una sottoscrizione di Azure e avere il ruolo di amministratore di tale sottoscrizione.
- È necessario disporre di Azure PowerShell installato e in esecuzione. 
- Questa guida pratica presuppone che una chiave di Azure Key Vault sia già in uso come protezione TDE per un database SQL di Azure o un data warehouse. Per altre informazioni, vedere [Transparent Data Encryption with BYOK Support](transparent-data-encryption-byok-azure-sql.md) (Transparent Data Encryption con supporto BYOK).

## <a name="overview"></a>Panoramica

Questa guida pratica descrive come gestire una protezione TDE potenzialmente compromessa per un database SQL di Azure o un data warehouse che usa TDE con chiavi gestite dal cliente in Azure Key Vault e supporto Bring Your Own Key (BYOK). Per altre informazioni sul supporto BYOK per TDE, vedere la [pagina di panoramica](transparent-data-encryption-byok-azure-sql.md). 

Le procedure seguenti devono essere eseguite solo in casi estremi o in ambienti di test. Leggere attentamente la guida pratica poiché l'eliminazione di protezioni TDE in uso da Azure Key Vault può comportare una **perdita di dati**. 

Se si ha il sospetto che una chiave sia compromessa, ad esempio per un accesso non autorizzato alla chiave da parte di un utente o un servizio, è consigliabile eliminarla.

Tenere presente che, dopo l'eliminazione della protezione TDE in Key Vault, **tutte le connessioni ai database crittografati nel server sono bloccate e i database passano alla modalità offline e vengono rilasciati entro 24 ore**. I backup precedenti crittografati con la chiave compromessa non sono più accessibili.

I passaggi seguenti illustrano come verificare le identificazioni personali protezione TDE ancora in uso dal file di Log virtuali (VLF) di un determinato database. L'identificazione personale della protezione TDE corrente del database e l'ID del database sono reperibili eseguendo: [Database_id], selezionare       [encryption_state], [encryptor_type], /*certificato indica chiavi gestite dal servizio, chiave asimmetrica significa che Azure Key Vault*/ [encryptor_thumbprint,] da [sys]. [ dm_database_encryption_keys] 
 
La query seguente restituisce il componente di crittografia e i file di log virtuali le identificazioni personali del rispettive in uso. Ogni identificazione personale diversa fa riferimento alla chiave diversi in Azure Key Vault (AKV): SELECT * FROM sys.dm_db_log_info (database_id) 

Il comando PowerShell Get-AzureRmSqlServerKeyVaultKey fornisce l'identificazione personale della protezione TDE usata nella query, pertanto è possibile visualizzare le chiavi da mantenere e le chiavi da eliminare in Azure Key Vault. È possibile eliminare in modo sicuro solo le chiavi non è più utilizzate dal database di Azure Key Vault.

Questa guida pratica descrive due approcci che si distinguono in base al risultato desiderato dopo la risposta all'evento imprevisto:

- Per mantenere **accessibili** i database SQL di Azure o i data warehouse
- Per rendere **inaccessibili** i database SQL di Azure o i data warehouse

## <a name="to-keep-the-encrypted-resources-accessible"></a>Per mantenere accessibili le risorse crittografate

1. Creare una [nuova chiave in Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Assicurarsi che la nuova chiave venga creata in un insieme di credenziali delle chiavi separato dalla protezione TDE potenzialmente compromessa, poiché il provisioning del controllo di accesso viene eseguito a livello di insieme di credenziali.
2. Aggiungere la nuova chiave al server usando il [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) e [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet e aggiornarla come nuova protezione di Transparent Data Encryption del server.

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

3. Assicurarsi che il server e le repliche siano stati aggiornati alla nuova protezione TDE usando la [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet. 

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
 
5. Eliminare la chiave compromessa da Key Vault usando il [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) cmdlet. 

   ```powershell
   Remove-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. Per ripristinare una chiave di Key Vault in futuro usando il [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) cmdlet:
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
