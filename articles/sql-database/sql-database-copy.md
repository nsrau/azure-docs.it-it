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
ms.date: 02/24/2020
ms.openlocfilehash: 7e4744627cfd08874e07bb126df048ea3e644f39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473745"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Creare una copia coerente a livello transazionale di un database SQL di Azure

Il database SQL di Azure offre diversi metodi per creare una copia coerente dal punto di vista transazionale di un database SQL di Azure esistente ([singolo database](sql-database-single-database.md)) nello stesso server o in un server diverso. È possibile copiare un database SQL tramite il portale di Azure, PowerShell o T-SQL.

## <a name="overview"></a>Panoramica

Una copia del database è uno snapshot del database di origine al momento della richiesta di copia. È possibile selezionare lo stesso server o un server diverso. È inoltre possibile scegliere di mantenere il livello di servizio e le dimensioni di calcolo oppure usare una dimensione di calcolo diversa all'interno dello stesso livello di servizio (edizione). Al termine del processo di copia, questa diventa un database indipendente e completamente funzionante. A questo punto, è possibile aggiornare o effettuare il downgrade della copia a qualsiasi edizione. Gli account di accesso, gli utenti e le autorizzazioni possono essere gestiti in modo indipendente. La copia viene creata utilizzando la tecnologia di replica geografica e una volta completato il seeding, il collegamento di replica geografica viene automaticamente terminato. Tutti i requisiti per l'utilizzo della replica geografica si applicano all'operazione di copia del database. Per informazioni [dettagliate,](sql-database-active-geo-replication.md) vedere Panoramica della replica geografica attiva.

> [!NOTE]
> [I backup automatici del database](sql-database-automated-backups.md) vengono utilizzati quando si crea una copia del database.

## <a name="logins-in-the-database-copy"></a>Account di accesso nella copia del database

Quando si copia un database nello stesso server di database SQL, è possibile usare gli stessi account di accesso per entrambi i database. L'entità di sicurezza usata per copiare il database diventa il proprietario del database nel nuovo database. 

Quando si copia un database in un server di database SQL diverso, l'entità di sicurezza che ha avviato l'operazione di copia nel server di destinazione diventa il proprietario del nuovo database. 

Indipendentemente dal server di destinazione, tutti gli utenti del database, le relative autorizzazioni e i relativi identificatori di protezione (SID) vengono copiati nella copia del database. L'utilizzo di utenti di [database indipendente](sql-database-manage-logins.md) per l'accesso ai dati garantisce che il database copiato abbia le stesse credenziali utente, in modo che al termine della copia sia possibile accedervi immediatamente con le stesse credenziali.

Se si utilizzano account di accesso a livello di server per l'accesso ai dati e si copia il database in un server diverso, l'accesso basato sull'account di accesso potrebbe non funzionare. Ciò può verificarsi perché gli account di accesso non esistono nel server di destinazione o perché le password e gli identificatori di sicurezza (SID) sono diversi. Per informazioni sulla gestione degli accessi con credenziali durante la copia di un database in un server di database SQL diverso, vedere [Come gestire la sicurezza dei database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md). Dopo che l'operazione di copia in un server diverso ha esito positivo e prima che altri utenti vengano rimappati, solo l'account di accesso associato al proprietario del database oppure l'amministratore del server può accedere al database copiato. Per risolvere gli account di accesso e stabilire l'accesso ai dati al termine dell'operazione di copia, vedere [Risolvere gli account di accesso](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Copiare un database tramite il portale di Azure

Per copiare un database tramite il portale di Azure, aprire la pagina per il database e quindi fare clic su **Copia**.

   ![Copia del database](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell-or-azure-cli"></a>Copiare un database tramite PowerShell o l'interfaccia della riga di comando di AzureCopy a database by using PowerShell or Azure CLI

Per copiare un database, utilizzare gli esempi seguenti.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Per PowerShell, utilizzare il cmdlet [New-AzSqlDatabaseCopy.](/powershell/module/az.sql/new-azsqldatabasecopy)

> [!IMPORTANT]
> Il modulo Di PowerShell Azure Resource Manager (RM) è ancora supportato dal database SQL di Azure, ma tutto lo sviluppo futuro è per il modulo Az.Sql.The PowerShell Azure Resource Manager (RM) module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. Il modulo AzureRM continuerà a ricevere correzioni di bug almeno fino a dicembre 2020.  Gli argomenti per i comandi nel modulo Az e nei moduli di AzureRm sono sostanzialmente identici. Per altre informazioni sulla compatibilità, vedere [Introduzione al nuovo modulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

La copia del database è un'operazione asincrona, ma il database di destinazione viene creato immediatamente dopo l'eliminazione della richiesta. Se è necessario annullare l'operazione di copia mentre è ancora in corso, eliminare il database di destinazione utilizzando il cmdlet [Remove-AzSqlDatabase.](/powershell/module/az.sql/new-azsqldatabase)

Per uno script di PowerShell di esempio completo, vedere [Copiare un database in un nuovo server.](scripts/sql-database-copy-database-to-new-server-powershell.md)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

La copia del database è un'operazione asincrona, ma il database di destinazione viene creato immediatamente dopo l'eliminazione della richiesta. Se è necessario annullare l'operazione di copia mentre è ancora in corso, eliminare il database di destinazione utilizzando il comando [az sql db delete.](/cli/azure/sql/db#az-sql-db-delete)

* * *

## <a name="rbac-roles-to-manage-database-copy"></a>Ruoli RBAC per gestire la copia del database

Per creare una copia del database, è necessario essere nei ruoli seguenti

- Proprietario della sottoscrizione
- Ruolo Collaboratore SQL Server o
- Ruolo personalizzato nei database di origine e di destinazione con l'autorizzazione seguente:

   Microsoft.Sql/servers/databases/read Microsoft.Sql/servers/databases/write

Per annullare una copia del database, è necessario essere nei ruoli seguenti

- Proprietario della sottoscrizione
- Ruolo Collaboratore SQL Server o
- Ruolo personalizzato nei database di origine e di destinazione con l'autorizzazione seguente:

   Microsoft.Sql/servers/databases/read Microsoft.Sql/servers/databases/write

Per gestire la copia del database tramite il portale di Azure, sono necessarie anche le autorizzazioni seguenti:To manage database copy using Azure portal, you will also need the following permissions:

   Microsoft.Resources/resources/read Microsoft.Resources/subscriptions/write Microsoft.Resources/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/deployments/operationstatuses/read

Se si desidera visualizzare le operazioni in distribuzioni nel gruppo di risorse nel portale, le operazioni tra più provider di risorse, incluse le operazioni SQL, saranno necessari questi ruoli RBAC aggiuntivi:If you want to see the operations under deployments in the resource group on the portal, operations across multiple resource providers including SQL operations, you will need these additional RBAC roles:

   Microsoft.Resources/subscriptions/resourcesgroups/deployments/operations/read Microsoft.Resources/subscriptions/resourcesgroups/deployments/operationstatuses/read

## <a name="copy-a-database-by-using-transact-sql"></a>Copiare un database tramite Transact-SQL

Accedere al database master con l'account di accesso dell'amministratore del server o l'account di accesso che ha creato il database che si desidera copiare. Affinché la copia del database abbia esito positivo, `dbmanager` gli account di accesso che non sono l'amministratore del server devono essere membri del ruolo. Per ulteriori informazioni sugli account di accesso e la connessione al server, vedere [Gestire gli accessi](sql-database-manage-logins.md).

Iniziare a copiare il database di origine con [CREATE DATABASE... AS COPY OF](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current#copy-a-database) istruzione. L'istruzione T-SQL continua l'esecuzione fino al completamento dell'operazione di copia del database.

> [!NOTE]
> La chiusura dell'istruzione T-SQL non comporta l'interruzione dell'operazione di copia del database. Per terminare l'operazione, eliminare il database di destinazione.
>

### <a name="copy-a-sql-database-to-the-same-server"></a>Copiare un database SQL nello stesso server

Accedere al database master con l'account di accesso dell'amministratore del server o l'account di accesso che ha creato il database che si desidera copiare. Affinché la copia del database abbia esito positivo, `dbmanager` gli account di accesso che non sono l'amministratore del server devono essere membri del ruolo.

Questo comando copia Database1 in un nuovo database denominato Database2 sullo stesso server. A seconda delle dimensioni del database, l'operazione di copia potrebbe richiedere alcuni minuti.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-a-sql-database-to-a-different-server"></a>Copiare un database SQL in un server diverso

Accedere al database master del server di destinazione in cui deve essere creato il nuovo database. Utilizzare un account di accesso con lo stesso nome e la stessa password del proprietario del database di origine nel server di origine. L'account di accesso nel server di `dbmanager` destinazione deve inoltre essere un membro del ruolo o essere l'account di accesso dell'amministratore del server.

Questo comando copia Database1 sul server1 in un nuovo database denominato Database2 sul server2. A seconda delle dimensioni del database, l'operazione di copia potrebbe richiedere alcuni minuti.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> I firewall di entrambi i server devono essere configurati per consentire la connessione in ingresso dall'IP del client che emette il database T-SQL CREATE DATABASE ... AS COPY OF comando.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Copiare un database SQL in una sottoscrizione diversaCopy a SQL database to a different subscription

È possibile utilizzare la procedura descritta nella sezione [Copiare un database SQL in un server diverso](#copy-a-sql-database-to-a-different-server) per copiare il database in un server di database SQL in una sottoscrizione diversa tramite T-SQL. Assicurarsi di utilizzare un account di accesso con lo stesso nome e la stessa password del proprietario del database di origine. Inoltre, l'account di accesso `dbmanager` deve essere un membro del ruolo o un amministratore del server, sia nei server di origine che in quelli di destinazione.

> [!NOTE]
> Il [portale di Azure,](https://portal.azure.com)PowerShell e l'interfaccia della riga di comando di Azure non supportano la copia del database in una sottoscrizione diversa.

### <a name="monitor-the-progress-of-the-copying-operation"></a>Monitorare lo stato dell'operazione di copia

Monitorare il processo di copia eseguendo una query nelle viste [sys.databases](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql), [sys.dm_database_copies](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)e [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) . Mentre la copia è in corso, la **colonna state_desc** della vista sys.databases per il nuovo database è impostata su **COPYING**.

* Se la copia non riesce, la colonna **state_desc** della vista sys.databases per il nuovo database viene impostata su **SUSPECT**. Eseguire l'istruzione DROP sul nuovo database e riprovare in un secondo momento.
* Se la copia ha esito positivo, la colonna **state_desc** della vista sys.databases per il nuovo database viene impostata su **ONLINE**. La copia è stata completata e il nuovo database è un database standard, che può essere modificato indipendentemente dal database di origine.

> [!NOTE]
> Se si decide di annullare il processo di copia mentre è in corso, eseguire l'istruzione [DROP DATABASE](https://docs.microsoft.com/sql/t-sql/statements/drop-database-transact-sql) nel nuovo database.

> [!IMPORTANT]
> Se è necessario creare una copia con un obiettivo di servizio sostanzialmente inferiore rispetto all'origine, il database di destinazione potrebbe non disporre di risorse sufficienti per completare il processo di seeding e potrebbe causare l'impossibilità di eseguire l'operazione di copia. In questo scenario utilizzare una richiesta di ripristino geografico per creare una copia in un server diverso e/o in un'area diversa. Per altre informazioni, vedere Ripristinare un database SQL di Azure usando i backup del [database.](sql-database-recovery-using-backups.md#geo-restore)

## <a name="resolve-logins"></a>Risolvere gli account di accesso

Dopo che il nuovo database è online nel server di destinazione, utilizzare l'istruzione [ALTER USER](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current) per rimappare gli utenti dal nuovo database agli account di accesso nel server di destinazione. Per risolvere gli utenti isolati, vedere [Risolvere i problemi relativi agli utenti isolati (SQL Server)](https://docs.microsoft.com/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server). Vedere anche [Come gestire la sicurezza del database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).

Tutti gli utenti nel nuovo database mantengono le autorizzazioni di cui disponevano nel database di origine. L'utente che ha avviato la copia del database diventa il proprietario del database del nuovo database. Dopo che la copia ha esito positivo e prima che altri utenti vengano rimappati, solo il proprietario del database può accedere al nuovo database.

Per informazioni sulla gestione di utenti e account di accesso durante la copia di un database in un server di database SQL diverso, vedere [Come gestire la sicurezza dei database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).

## <a name="database-copy-errors"></a>Errori di copia del database

Durante la copia di un database nel database SQL di Azure, possono essere rilevati gli errori seguenti. Per altre informazioni, vedere [Copiare un database SQL di Azure](sql-database-copy.md).

| Codice di errore | Gravità | Descrizione |
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
- Per esportare un database, consultate [Esportare il database in un BACPAC.](sql-database-export.md)
