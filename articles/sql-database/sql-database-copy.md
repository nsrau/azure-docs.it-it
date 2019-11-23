---
title: Copiare un database
description: Creare una copia coerente a livello transazionale di un database SQL di Azure esistente nello stesso server o su un altro server.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 11/14/2019
ms.openlocfilehash: b3bc99d0fbdb551af0fb3711d74db537d3f9b1a5
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74421336"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Creare una copia coerente a livello transazionale di un database SQL di Azure

Azure SQL Database provides several methods for creating a transactionally consistent copy of an existing Azure SQL database ([single database](sql-database-single-database.md)) on either the same server or a different server. È possibile copiare un database SQL tramite il portale di Azure, PowerShell o T-SQL.

## <a name="overview"></a>Panoramica

Una copia del database è uno snapshot del database di origine al momento della richiesta di copia. You can select the same server or a different server. Also you can choose to keep its service tier and compute size, or use a different compute size within the same service tier (edition). Al termine del processo di copia, questa diventa un database indipendente e completamente funzionante. A questo punto, è possibile aggiornare o effettuare il downgrade della copia a qualsiasi edizione. Gli account di accesso, gli utenti e le autorizzazioni possono essere gestiti in modo indipendente. The copy is created using the geo-replication technology and once seeding is completed the geo-replication link is automatically terminated. All the requirements for using geo-replication apply to the database copy operation. See [Active geo-replication overview](sql-database-active-geo-replication.md) for details.

> [!NOTE]
> [I backup di database automatizzati](sql-database-automated-backups.md) vengono usati quando si crea una copia del database.

## <a name="logins-in-the-database-copy"></a>Account di accesso nella copia del database

Quando si copia un database nello stesso server di database SQL, è possibile usare gli stessi account di accesso per entrambi i database. L'entità di sicurezza usata per copiare il database diventa il proprietario del database nel nuovo database. Tutti gli utenti del database, le relative autorizzazioni e i relativi identificatori di sicurezza (SID) vengono copiati nella copia del database.  

Quando la copia del database viene eseguita su un server di database SQL diverso, l'entità di sicurezza sul nuovo server diventa il proprietario del database nel nuovo database. L'uso di [utenti di database indipendente](sql-database-manage-logins.md) per l'accesso ai dati garantisce che per i database primari e secondari ci siano sempre le stesse credenziali utente, in modo che dopo il completamento della copia sia possibile accedervi immediatamente con le stesse credenziali.

Se si usa [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), è possibile eliminare completamente l'esigenza di gestione delle credenziali nella copia. Tuttavia, quando si copia il database in un nuovo server, è possibile che l'accesso basato sulle credenziali non funzioni perché le credenziali di accesso non esistono nel nuovo server. Per informazioni sulla gestione degli accessi con credenziali durante la copia di un database in un server di database SQL diverso, vedere [Come gestire la sicurezza dei database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).

Dopo il completamento della copia e prima che venga modificato il mapping di altri utenti, solo l'account di accesso che ha avviato la copia, il proprietario del database, può accedere al nuovo database. Per risolvere gli account di accesso al termine dell'operazione di copia, vedere [Risolvere gli account di accesso](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Copiare un database tramite il portale di Azure

Per copiare un database tramite il portale di Azure, aprire la pagina per il database e quindi fare clic su **Copia**.

   ![Copia del database](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>Copiare un database tramite PowerShell

To copy a database, use the following examples.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

For PowerShell, use the [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) cmdlet.

> [!IMPORTANT]
> The PowerShell Azure Resource Manager (RM) module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. The AzureRM module will continue to receive bug fixes until at least December 2020.  The arguments for the commands in the Az module and in the AzureRm modules are substantially identical. For more about their compatibility, see [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

The database copy is a asynchronous operation but the target database is created immediately after the request is accepted. If you need to cancel the copy operation while still in progress, drop the the target database using the [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet.

# <a name="azure-clitabazure-cli"></a>[interfaccia della riga di comando di Azure](#tab/azure-cli)

```azure-cli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

The database copy is a asynchronous operation but the target database is created immediately after the request is accepted. If you need to cancel the copy operation while still in progress, drop the the target database using the [az sql db delete](/cli/azure/sql/db#az-sql-db-delete) command.

* * *

Per uno script di esempio completo, vedere [Copiare un database in un nuovo server](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="rbac-roles-to-manage-database-copy"></a>RBAC roles to manage database copy

To create a database copy, you will need to be in the following roles

- Proprietario della sottoscrizione
- SQL Server Contributor role or
- Custom role on the source and target databases with following permission:

   Microsoft.Sql/servers/databases/read  Microsoft.Sql/servers/databases/write

To cancel a database copy, you will need to be in the following roles

- Proprietario della sottoscrizione
- SQL Server Contributor role or
- Custom role on the source and target databases with following permission:

   Microsoft.Sql/servers/databases/read  Microsoft.Sql/servers/databases/write

To manage database copy using Azure portal, you will also need the following permissions:

   Microsoft.Resources/subscriptions/resources/read Microsoft.Resources/subscriptions/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/deployments/operationstatuses/read

If you want to see the operations under deployments in the resource group on the portal, operations across multiple resource providers including SQL operations, you will need these additional RBAC roles:

   Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read

## <a name="copy-a-database-by-using-transact-sql"></a>Copiare un database tramite Transact-SQL

Accedere al database master con l'account di accesso dell'entità di livello server o l'account di accesso con cui è stato creato il database che si desidera copiare. Affinché il processo di copia del database abbia esito positivo, gli account di accesso che non corrispondono all'entità di livello server devono essere membri del ruolo dbmanager. Per ulteriori informazioni sugli account di accesso e la connessione al server, vedere [Gestire gli accessi](sql-database-manage-logins.md).

Iniziare a copiare il database di origine con l'istruzione [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . L'esecuzione di questa istruzione avvia il processo di copia del database. Poiché la copia di un database è un processo asincrono, l'istruzione CREATE DATABASE viene restituita prima del completamento della copia del database.

### <a name="copy-a-sql-database-to-the-same-server"></a>Copiare un database SQL nello stesso server

Accedere al database master con l'account di accesso dell'entità di livello server o l'account di accesso con cui è stato creato il database che si desidera copiare. Affinché il processo di copia del database abbia esito positivo, gli account di accesso che non corrispondono all'entità di livello server devono essere membri del ruolo dbmanager.

Questo comando copia Database1 in un nuovo database denominato Database2 sullo stesso server. A seconda delle dimensioni del database, l'operazione di copia potrebbe richiedere alcuni minuti.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-a-sql-database-to-a-different-server"></a>Copiare un database SQL in un server diverso

Accedere al database master del server di destinazione, il server di database SQL in cui verrà creato il nuovo database. Usare un account di accesso con lo stesso nome e la stessa password del proprietario del database di origine nel server di database SQL di origine. L'account di accesso nel server di destinazione deve anche essere un membro del ruolo dbmanager o corrispondere all'account di accesso dell'entità di livello server.

Questo comando copia Database1 sul server1 in un nuovo database denominato Database2 sul server2. A seconda delle dimensioni del database, l'operazione di copia potrebbe richiedere alcuni minuti.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Both servers' firewalls must be configured to allow inbound connection from the IP of the client issuing the T-SQL COPY command.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Copy a SQL database to a different subscription

You can use the steps described in the previous section to copy your database to a SQL Database server in a different subscription. Make sure you use a login that has the same name and password as the database owner of the source database and it is a member of the dbmanager role or is the server-level principal login. 

> [!NOTE]
> The [Azure portal](https://portal.azure.com) does not support copy to a different subscription because Portal calls the ARM API and it uses the subscription certificates to access both servers involved in geo-replication.  

### <a name="monitor-the-progress-of-the-copying-operation"></a>Monitorare lo stato dell'operazione di copia

Monitorare il processo di copia eseguendo una query sulle visualizzazioni sys.databases e sys.dm_database_copies. Durante il processo di copia, la colonna **state_desc** della visualizzazione sys.databases per il nuovo database viene impostata su **COPYING**.

* Se il processo di copia non viene completato, la colonna **state_desc** della visualizzazione sys.databases per il nuovo database viene impostata su **SUSPECT**. Eseguire l'istruzione DROP sul nuovo database e riprovare in un secondo momento.
* Se il processo di copia viene completato, la colonna **state_desc** della visualizzazione sys.databases per il nuovo database viene impostata su **ONLINE**. La copia è stata completata e il nuovo database è un database standard, che può essere modificato indipendentemente dal database di origine.

> [!NOTE]
> Se si decide di annullare il processo di copia mentre è in corso, eseguire l'istruzione [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) nel nuovo database. In alternativa, anche l'esecuzione dell'istruzione DROP DATABASE sul database di origine annulla il processo di copia.

> [!IMPORTANT]
> If you need to create a copy with a substantially smaller SLO than the source, the target database may not have sufficient resources to complete the seeding process and it can cause the copy operaion to fail. In this scenario use a geo-restore request to create a copy in a different server and/or a different region. See [Recover an Azure SQL database using database backups](sql-database-recovery-using-backups.md#geo-restore) for more informaion.

## <a name="resolve-logins"></a>Risolvere gli account di accesso

Dopo che il nuovo database è online nel server di destinazione, usare l'istruzione [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) per modificare il mapping degli utenti del nuovo database agli account di accesso nel server di destinazione. Per risolvere gli utenti isolati, vedere [Risolvere i problemi relativi agli utenti isolati (SQL Server)](https://msdn.microsoft.com/library/ms175475.aspx). Vedere anche [Come gestire la sicurezza del database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).

Tutti gli utenti nel nuovo database mantengono le autorizzazioni di cui disponevano nel database di origine. L'utente che ha avviato la copia del database diventa il proprietario del nuovo database e gli viene assegnato un nuovo ID di sicurezza (SID). Dopo il completamento della copia e prima che venga modificato il mapping di altri utenti, solo l'account di accesso che ha avviato la copia, il proprietario del database, può accedere al nuovo database.

Per informazioni sulla gestione di utenti e account di accesso durante la copia di un database in un server di database SQL diverso, vedere [Come gestire la sicurezza dei database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).

## <a name="database-copy-errors"></a>Errori di copia del database

Durante la copia di un database nel database SQL di Azure, possono essere rilevati gli errori seguenti. Per altre informazioni, vedere [Copiare un database SQL di Azure](sql-database-copy.md).

| Codice di errore | Severity | Description |
| ---:| ---:|:--- |
| 40635 |16 |Il client con indirizzo IP '%.&#x2a;ls' è temporaneamente disabilitato. |
| 40637 |16 |La creazione della copia del database è attualmente disabilitata. |
| 40561 |16 |Copia del database non riuscita. Il database di origine o di destinazione non esiste. |
| 40562 |16 |Copia del database non riuscita. Il database di origine è stato rimosso. |
| 40563 |16 |Copia del database non riuscita. Il database di destinazione è stato rimosso. |
| 40564 |16 |Copia del database non riuscita a causa di un errore interno. Rimuovere il database di destinazione e riprovare. |
| 40565 |16 |Copia del database non riuscita. Non è consentita più di una copia simultanea del database dalla stessa origine. Rimuovere il database di destinazione e riprovare in un secondo momento. |
| 40566 |16 |Copia del database non riuscita a causa di un errore interno. Rimuovere il database di destinazione e riprovare. |
| 40567 |16 |Copia del database non riuscita a causa di un errore interno. Rimuovere il database di destinazione e riprovare. |
| 40568 |16 |Copia del database non riuscita. Il database di origine non è più disponibile. Rimuovere il database di destinazione e riprovare. |
| 40569 |16 |Copia del database non riuscita. Il database di destinazione non è più disponibile. Rimuovere il database di destinazione e riprovare. |
| 40570 |16 |Copia del database non riuscita a causa di un errore interno. Rimuovere il database di destinazione e riprovare in un secondo momento. |
| 40571 |16 |Copia del database non riuscita a causa di un errore interno. Rimuovere il database di destinazione e riprovare in un secondo momento. |

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sugli account di accesso, vedere [Controllo e concessione dell'accesso al database](sql-database-manage-logins.md) e [Configurare e gestire la sicurezza dei database SQL di Azure per il ripristino geografico o il failover](sql-database-geo-replication-security-config.md).
- Per esportare un database, vedere [Esportare un database SQL di Azure in un file BACPAC](sql-database-export.md).
