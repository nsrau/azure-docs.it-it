---
title: Ruota protezione TDE - PowerShellRotate TDE protector - PowerShell
description: Informazioni su come ruotare la protezione di Transparent Data Encryption (TDE) per un server SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: aaed06ac086893f63fde530e46b936b3fb637766
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067178"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Ruotare la protezione Transparent Data Encryption (TDE) tramite PowerShell

Questo articolo descrive la rotazione delle chiavi per un server SQL di Azure usando una protezione TDE di Azure Key Vault. Rotazione della protezione TDE di un server SQL di Azure significa passare a una nuova chiave asimmetrica che protegge i database nel server. La rotazione delle chiavi è un'operazione online e dovrebbe richiedere solo pochi secondi per essere completata, perché in questo modo viene decrittografata e crittografata nuovamente solo la chiave di crittografia dei dati del database, non l'intero database.

In questa guida vengono illustrate due opzioni per ruotare la protezione TDE nel server.

> [!NOTE]
> È necessario riprendere un SQL Data Warehouse in pausa prima delle rotazioni delle chiavi.

> [!IMPORTANT]
> Non eliminare le versioni precedenti della chiave dopo un rollover. Quando le chiavi vengono ruotate, alcuni dati vengono comunque crittografati con le chiavi precedenti, ad esempio, i backup meno recenti del database.

## <a name="prerequisites"></a>Prerequisiti

- Questa guida pratica presuppone che una chiave di Azure Key Vault sia già in uso come protezione TDE per un database SQL di Azure o un data warehouse. Vedere [Transparent Data Encryption con supporto BYOK](transparent-data-encryption-byok-azure-sql.md).
- È necessario che Azure PowerShell sia installato e in esecuzione.
- [Consigliata ma facoltativa] Creare innanzitutto il materiale della chiave per la protezione TDE in un modulo di protezione hardware (HSM) o in un archivio chiavi locale e importare il materiale della chiave in Azure Key Vault. Seguire le [Istruzioni per l'uso di un modulo di protezione hardware (HSM) e di Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) per altre informazioni.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Per istruzioni sull'installazione del modulo Az, vedere [Installare Azure PowerShell](/powershell/azure/install-az-ps). Per cmdlet specifici, vedere [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> Il modulo Di PowerShell Azure Resource Manager (RM) è ancora supportato dal database SQL di Azure, ma tutto lo sviluppo futuro è per il modulo Az.Sql.The PowerShell Azure Resource Manager (RM) module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. Il modulo AzureRM continuerà a ricevere correzioni di bug almeno fino a dicembre 2020.  Gli argomenti per i comandi nel modulo Az e nei moduli di AzureRm sono sostanzialmente identici. Per altre informazioni sulla compatibilità, vedere [Introduzione al nuovo modulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

Per l'installazione, vedere Installare l'interfaccia della riga di comando di [Azure.For installation,](/cli/azure/install-azure-cli)see Install Azure CLI .

* * *

## <a name="manual-key-rotation"></a>Rotazione manuale delle chiavi

La rotazione manuale delle chiavi utilizza i seguenti comandi per aggiungere una chiave completamente nuova, che potrebbe trovarsi sotto un nuovo nome di chiave o anche un altro insieme di credenziali delle chiavi. Questo approccio consente di aggiungere la stessa chiave a diversi insiemi di credenziali delle chiavi per supportare scenari di ripristino di emergenza geografico a disponibilità elevata.

> [!NOTE]
> La lunghezza combinata per il nome dell'insieme di credenziali delle chiavi non può superare 94 caratteri.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Utilizzare i cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)e [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

Utilizzare i comandi di impostazione del tasto [az keyvault create](/cli/azure/keyvault/key#az-keyvault-key-create), [az sql server key create](/cli/azure/sql/server/key#az-sql-server-key-create)e az sql [server tde-key set.](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set)

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Cmdlet PowerShell utili

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

- Per passare dalla modalità TDE gestito da Microsoft alla modalità BYOK, utilizzare il cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Per impostare la protezione TDE dalla modalità BYOK a Microsoft, utilizzare il cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

Negli esempi seguenti viene utilizzato [az sql server tde-key set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

- Per passare dalla modalità TDE dalla modalità BYOK gestita da Microsoft,

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- Per passare dalla modalità TDE dalla modalità BYOK a quella gestita da Microsoft,

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Passaggi successivi

- In caso di rischio per la sicurezza, consultare [Rimuovere una chiave potenzialmente compromessa](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) per informazioni su come rimuovere una protezione TDE potenzialmente compromessa

- Introduzione all'integrazione di Azure Key Vault e supporto per TDE: [attivare TDE usando la propria chiave da Key Vault tramite PowerShellGet](transparent-data-encryption-byok-azure-sql-configure.md) started with Azure Key Vault integration and Bring Your Own Key support for TDE: Turn on TDE using your own key from Key Vault using PowerShell
