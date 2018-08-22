---
title: 'PowerShell: Rimuovere una protezione TDE - Database SQL di Azure| Microsoft Docs'
description: Guida pratica per la gestione di una protezione TDE potenzialmente compromessa per un database SQL di Azure o un data warehouse che usa TDE con supporto Bring Your Own Key (BYOK).
keywords: ''
services: sql-database
documentationcenter: ''
author: becczhang
manager: craigg
ms.prod: ''
ms.reviewer: ''
ms.suite: sql
ms.prod_service: sql-database, sql-data-warehouse
ms.service: sql-database
ms.custom: ''
ms.tgt_pltfrm: ''
ms.topic: conceptual
ms.date: 08/07/2017
ms.author: rebeccaz
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: feb187101ec02d6e765d6b025f518dc416f55b8b
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043550"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Rimuovere una protezione TDE (Transparent Data Encryption) tramite PowerShell
## <a name="prerequisites"></a>Prerequisiti
- È necessario disporre di una sottoscrizione di Azure e avere il ruolo di amministratore di tale sottoscrizione.
- È necessario che sia installato e in esecuzione Azure PowerShell 4.2.0 o una versione più recente. 
- Questa guida pratica presuppone che una chiave di Azure Key Vault sia già in uso come protezione TDE per un database SQL di Azure o un data warehouse. Per altre informazioni, vedere [Transparent Data Encryption with BYOK Support](transparent-data-encryption-byok-azure-sql.md) (Transparent Data Encryption con supporto BYOK).

## <a name="overview"></a>Panoramica
Questa guida pratica descrive come gestire una protezione TDE potenzialmente compromessa per un database SQL di Azure o un data warehouse che usa TDE con supporto Bring Your Own Key (BYOK). Per altre informazioni sul supporto BYOK per TDE, vedere la [pagina di panoramica](transparent-data-encryption-byok-azure-sql.md). 

Le procedure seguenti devono essere eseguite solo in casi estremi o in ambienti di test. Leggere attentamente la guida pratica poiché l'eliminazione di protezioni TDE in uso da Azure Key Vault può comportare una **perdita di dati**. 

Se si ha il sospetto che una chiave sia compromessa, ad esempio per un accesso non autorizzato alla chiave da parte di un utente o un servizio, è consigliabile eliminarla.

Tenere presente che, dopo l'eliminazione della protezione TDE in Key Vault, **tutte le connessioni ai database crittografati nel server sono bloccate e i database passano alla modalità offline e vengono rilasciati entro 24 ore**. I backup precedenti crittografati con la chiave compromessa non sono più accessibili.

Questa guida pratica descrive due approcci che si distinguono in base al risultato desiderato dopo la risposta all'evento imprevisto:
- Per mantenere **accessibili** i database SQL di Azure o i data warehouse
- Per rendere **inaccessibili** i database SQL di Azure o i data warehouse

## <a name="to-keep-the-encrypted-resources-accessible"></a>Per mantenere accessibili le risorse crittografate
1. Creare una [nuova chiave in Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultkey?view=azurermps-4.1.0). Assicurarsi che la nuova chiave venga creata in un insieme di credenziali delle chiavi separato dalla protezione TDE potenzialmente compromessa, poiché il provisioning del controllo di accesso viene eseguito a livello di insieme di credenziali. 
2. Aggiungere la nuova chiave al server usando i cmdlet [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) e [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) e aggiornarla come nuova protezione TDE del server.

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzureRmSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>
   
   # Set the key as the TDE protector for all resources under the server
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. Verificare che il server e le repliche siano stati aggiornati in base alla nuova protezione TDE usando il cmdlet [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector). 

   >[!NOTE]
   > La propagazione della nuova protezione TDE a tutti i database e ai database secondari nel server può richiedere alcuni minuti.
   >

   ```powershell
   Get-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Eseguire un copia di [backup della nuova chiave](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey) in Key Vault.

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. Eliminare la chiave compromessa da Key Vault usando il cmdlet [Remove-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/remove-azurekeyvaultkey). 

   ```powershell
   Remove-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. Per ripristinare una chiave in Key Vault in futuro usando il cmdlet [Restore-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey):
   ```powershell
   Restore-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```
 
## <a name="to-make-the-encrypted-resources-inaccessible"></a>Per rendere inaccessibili le risorse crittografate
1. Rilasciare i database crittografati dalla chiave potenzialmente compromessa.
Il backup dei file di database e log viene eseguito automaticamente. È quindi possibile eseguire un ripristino temporizzato del database in qualsiasi momento, purché si specifichi la chiave. I database devono essere rilasciati prima dell'eliminazione di una protezione TDE attiva per evitare possibili perdite di dati, per un intervallo massimo di 10 minuti, dalle transazioni più recenti. 
2. Eseguire il backup del materiale della chiave della protezione TDE in Key Vault.
3. Rimuovere la chiave potenzialmente compromessa da Key Vault.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come eseguire la rotazione della protezione TDE di un server per soddisfare i requisiti di sicurezza: [Rotate the Transparent Data Encryption protector Using PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md) (Ruotare la protezione Transparent Data Encryption tramite PowerShell)

- Introduzione al supporto Bring Your Own Key per TDE: [Turn on TDE using your own key from Key Vault using PowerShell](transparent-data-encryption-byok-azure-sql-configure.md) (Abilitare TDE tramite la propria chiave da Key Vault usando PowerShell)
