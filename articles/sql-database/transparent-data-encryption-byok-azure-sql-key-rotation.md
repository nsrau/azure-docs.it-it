---
title: Rotate TDE protector - PowerShell
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
ms.openlocfilehash: 4b9a00b63644e35d23baa1b58c67bb6b8918274b
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422445"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Ruotare la protezione Transparent Data Encryption (TDE) tramite PowerShell

Questo articolo descrive la rotazione delle chiavi per un server SQL di Azure usando una protezione TDE di Azure Key Vault. Ruotare una protezione TDE di un server SQL di Azure significa passare a una nuova chiave asimmetrica che protegge i database nel server. La rotazione delle chiavi è un'operazione online e richiede solo pochi secondi, perché si limita a decrittografare e crittografare nuovamente la chiave di crittografia dei dati del database, ma non l'intero database.

In questa guida vengono illustrate due opzioni per ruotare la protezione TDE nel server.

> [!NOTE]
> È necessario riprendere un SQL Data Warehouse in pausa prima delle rotazioni delle chiavi.

> [!IMPORTANT]
> Do not delete previous versions of the key after a rollover. Quando le chiavi vengono ruotate, alcuni dati vengono comunque crittografati con le chiavi precedenti, ad esempio, i backup meno recenti del database.

## <a name="prerequisites"></a>Prerequisiti

- Questa guida pratica presuppone che una chiave di Azure Key Vault sia già in uso come protezione TDE per un database SQL di Azure o un data warehouse. Vedere [Transparent Data Encryption con supporto BYOK](transparent-data-encryption-byok-azure-sql.md).
- You must have Azure PowerShell installed and running.
- [Consigliata ma facoltativa] Creare innanzitutto il materiale della chiave per la protezione TDE in un modulo di protezione hardware (HSM) o in un archivio chiavi locale e importare il materiale della chiave in Azure Key Vault. Seguire le [Istruzioni per l'uso di un modulo di protezione hardware (HSM) e di Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) per altre informazioni.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Per istruzioni sull'installazione del modulo Az, vedere [Installare Azure PowerShell](/powershell/azure/install-az-ps). For specific cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> The PowerShell Azure Resource Manager (RM) module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. The AzureRM module will continue to receive bug fixes until at least December 2020.  The arguments for the commands in the Az module and in the AzureRm modules are substantially identical. For more about their compatibility, see [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az).

# <a name="azure-clitabazure-cli"></a>[interfaccia della riga di comando di Azure](#tab/azure-cli)

For installation, see [Install Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="manual-key-rotation"></a>Rotazione manuale delle chiavi

Manual key rotation uses the following commands to add a completely new key, which could be under a new key name or even another key vault. Questo approccio consente di aggiungere la stessa chiave a diversi insiemi di credenziali delle chiavi per supportare scenari di ripristino di emergenza geografico a disponibilità elevata.

> [!NOTE]
> La lunghezza combinata per il nome dell'insieme di credenziali delle chiavi non può superare 94 caratteri.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Use the [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey), and [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlets.

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="azure-clitabazure-cli"></a>[interfaccia della riga di comando di Azure](#tab/azure-cli)

Use the [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create), [az sql server key create](/cli/azure/sql/server/key#az-sql-server-key-create), and [az sql server tde-key set](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) commands.

```azure-cli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Cmdlet PowerShell utili

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

- To switch the TDE protector from Microsoft-managed to BYOK mode, use the [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- To switch the TDE protector from BYOK mode to Microsoft-managed, use the [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-clitabazure-cli"></a>[interfaccia della riga di comando di Azure](#tab/azure-cli)

The following examples use [az sql server tde-key set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

- To switch the TDE protector from Microsoft-managed to BYOK mode,

   ```azure-cli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- To switch the TDE protector from BYOK mode to Microsoft-managed,

   ```azure-cli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Passaggi successivi

- In caso di rischio per la sicurezza, consultare [Rimuovere una chiave potenzialmente compromessa](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) per informazioni su come rimuovere una protezione TDE potenzialmente compromessa

- Get started with Azure Key Vault integration and Bring Your Own Key support for TDE: [Turn on TDE using your own key from Key Vault using PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
