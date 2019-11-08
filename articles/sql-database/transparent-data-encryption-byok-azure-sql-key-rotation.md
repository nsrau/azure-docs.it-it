---
title: Ruotare la protezione Transparent Data Encryption-PowerShell
description: Informazioni su come ruotare la protezione di Transparent Data Encryption (TDE) per un server SQL di Azure.
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
ms.openlocfilehash: 5bfcacb9348940e0b36947f6e4e0d27839de35bb
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824672"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Ruotare la protezione Transparent Data Encryption (TDE) tramite PowerShell

Questo articolo descrive la rotazione delle chiavi per un server SQL di Azure usando una protezione TDE di Azure Key Vault. Ruotare una protezione TDE di un server SQL di Azure significa passare a una nuova chiave asimmetrica che protegge i database nel server. La rotazione delle chiavi è un'operazione online e richiede solo pochi secondi, perché si limita a decrittografare e crittografare nuovamente la chiave di crittografia dei dati del database, ma non l'intero database.

In questa guida vengono illustrate due opzioni per ruotare la protezione TDE nel server.

> [!NOTE]
> È necessario riprendere un SQL Data Warehouse in pausa prima delle rotazioni delle chiavi.
>

> [!IMPORTANT]
> **Non eliminare** le versioni precedenti della chiave dopo l'effetto di rotazione.  Quando le chiavi vengono ruotate, alcuni dati vengono comunque crittografati con le chiavi precedenti, ad esempio, i backup meno recenti del database. 
>

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato dal database SQL di Azure, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Per questi cmdlet, vedere [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo AZ e nei moduli AzureRm sono sostanzialmente identici.

- Questa guida pratica presuppone che una chiave di Azure Key Vault sia già in uso come protezione TDE per un database SQL di Azure o un data warehouse. Vedere [Transparent Data Encryption con supporto BYOK](transparent-data-encryption-byok-azure-sql.md).
- È necessario che Azure PowerShell sia installato e in esecuzione.
- [Consigliata ma facoltativa] Creare innanzitutto il materiale della chiave per la protezione TDE in un modulo di protezione hardware (HSM) o in un archivio chiavi locale e importare il materiale della chiave in Azure Key Vault. Seguire le [Istruzioni per l'uso di un modulo di protezione hardware (HSM) e di Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) per altre informazioni.

## <a name="manual-key-rotation"></a>Rotazione manuale delle chiavi

La rotazione manuale della chiave usa i cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)e [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) per aggiungere una chiave completamente nuova che potrebbe trovarsi in un nuovo nome di chiave o anche in un'altra chiave credenziali. Questo approccio consente di aggiungere la stessa chiave a diversi insiemi di credenziali delle chiavi per supportare scenari di ripristino di emergenza geografico a disponibilità elevata.

>[!NOTE]
>La lunghezza combinata per il nome dell'insieme di credenziali delle chiavi non può superare 94 caratteri.

   ```powershell
   # Add a new key to Key Vault
   Add-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
  
   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```


## <a name="other-useful-powershell-cmdlets"></a>Altri cmdlet PowerShell utili

- Per passare la protezione Transparent Data Encryption dalla modalità gestita da Microsoft a BYOK, usare il cmdlet [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Per passare la protezione Transparent Data Encryption dalla modalità BYOK a quella gestita da Microsoft, usare il cmdlet [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>Passaggi successivi

- In caso di rischio per la sicurezza, consultare [Rimuovere una chiave potenzialmente compromessa](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) per informazioni su come rimuovere una protezione TDE potenzialmente compromessa 

- Introduzione all'integrazione Azure Key Vault e al supporto Bring Your Own Key per Transparent Data Encryption: attivare Transparent [Data Encryption con la propria chiave da Key Vault usando PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
