---
title: Copiare un database
description: Creare una copia coerente a livello di transazioni di un database esistente nel database SQL di Azure nello stesso server o in un server diverso.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 07/29/2020
ms.openlocfilehash: 4189ea5fd0b2b52b5bf1e0614e4b43b9411df31d
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87530377"
---
# <a name="copy-a-transactionally-consistent-copy-of-a-database-in-azure-sql-database"></a>Copiare una copia coerente a livello di transazioni di un database nel database SQL di Azure

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Il database SQL di Azure offre diversi metodi per la creazione di una copia di un [database](single-database-overview.md) esistente nello stesso server o in un server diverso. È possibile copiare un database usando portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o T-SQL.

## <a name="overview"></a>Panoramica

Una copia del database è uno snapshot coerente a livello di transazione del database di origine al momento dell'avvio della richiesta di copia. È possibile selezionare lo stesso server o un server diverso per la copia. È anche possibile scegliere di proteggere il livello di servizio e le dimensioni di calcolo del database di origine oppure usare una dimensione di calcolo diversa nello stesso livello di servizio o in un altro. Al termine del processo di copia, questa diventa un database indipendente e completamente funzionante. Gli account di accesso, gli utenti e le autorizzazioni nel database copiato vengono gestiti in modo indipendente dal database di origine. La copia viene creata usando la tecnologia di replica geografica. Al termine del seeding della replica, il collegamento di replica geografica viene terminato automaticamente. Tutti i requisiti per l'uso della replica geografica si applicano all'operazione di copia del database. Per informazioni dettagliate, vedere [Panoramica della replica geografica attiva](active-geo-replication-overview.md) .

## <a name="logins-in-the-database-copy"></a>Account di accesso nella copia del database

Quando si copia un database nello stesso server, è possibile utilizzare gli stessi account di accesso in entrambi i database. L'entità di sicurezza usata per copiare il database diventa il proprietario del database nel nuovo database.

Quando si copia un database in un server diverso, l'entità di sicurezza che ha avviato l'operazione di copia nel server di destinazione diventa il proprietario del nuovo database.

Indipendentemente dal server di destinazione, tutti gli utenti del database, le relative autorizzazioni e gli identificatori di sicurezza (SID) vengono copiati nella copia del database. L'uso di [utenti di database indipendente](logins-create-manage.md) per l'accesso ai dati garantisce che il database copiato abbia le stesse credenziali utente, in modo che dopo il completamento della copia sia possibile accedervi immediatamente con le stesse credenziali.

Se si usano account di accesso a livello di server per l'accesso ai dati e si copia il database in un server diverso, l'accesso basato su account di accesso potrebbe non funzionare. Questo problema può verificarsi perché gli account di accesso non esistono nel server di destinazione o perché le password e gli identificatori di sicurezza (SID) sono diversi. Per informazioni sulla gestione degli account di accesso quando si copia un database in un server diverso, vedere [come gestire la sicurezza del database SQL di Azure dopo il ripristino di emergenza](active-geo-replication-security-configure.md). Una volta completata l'operazione di copia in un server diverso e prima che venga eseguito il mapping di altri utenti, solo l'account di accesso associato al proprietario del database o l'amministratore del server può accedere al database copiato. Per risolvere gli account di accesso e stabilire l'accesso ai dati al termine dell'operazione di copia, vedere [risolvere gli account](#resolve-logins)di accesso.

## <a name="copy-using-the-azure-portal"></a>Copiare usando il portale di Azure

Per copiare un database tramite il portale di Azure, aprire la pagina per il database e quindi fare clic su **Copia**.

   ![Copia del database](./media/database-copy/database-copy.png)

## <a name="copy-using-powershell-or-the-azure-cli"></a>Copiare usando PowerShell o l'interfaccia della riga di comando di Azure

Per copiare un database, usare gli esempi seguenti.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per PowerShell, usare il cmdlet [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) .

> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell (RM) è ancora supportato dal database SQL di Azure, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Il modulo AzureRM continuerà a ricevere correzioni di bug fino ad almeno il 2020 dicembre.  Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici. Per altre informazioni sulla compatibilità, vedere [Introduzione al nuovo Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

La copia del database è un'operazione asincrona, ma il database di destinazione viene creato immediatamente dopo l'accettazione della richiesta. Se è necessario annullare l'operazione di copia mentre è ancora in corso, eliminare il database di destinazione usando il cmdlet [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) .

Per uno script di PowerShell di esempio completo, vedere [copiare un database in un nuovo server](scripts/copy-database-to-new-server-powershell.md).

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

La copia del database è un'operazione asincrona, ma il database di destinazione viene creato immediatamente dopo l'accettazione della richiesta. Se è necessario annullare l'operazione di copia mentre è ancora in corso, eliminare il database di destinazione usando il comando [AZ SQL DB Delete](/cli/azure/sql/db#az-sql-db-delete) .

* * *

## <a name="copy-using-transact-sql"></a>Copia tramite Transact-SQL

Accedere al database master con l'account di accesso dell'amministratore del server o l'account di accesso che ha creato il database che si desidera copiare. Per la riuscita della copia del database, gli account di accesso che non sono l'amministratore del server devono essere membri del `dbmanager` ruolo. Per ulteriori informazioni sugli account di accesso e la connessione al server, vedere [Gestire gli accessi](logins-create-manage.md).

Avviare la copia del database di origine con l'oggetto [create database... COME copia dell'](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current#copy-a-database) istruzione. L'esecuzione dell'istruzione T-SQL continua fino al completamento dell'operazione di copia del database.

> [!NOTE]
> Se si termina l'istruzione T-SQL, l'operazione di copia del database non viene terminata. Per terminare l'operazione, eliminare il database di destinazione.
>

### <a name="copy-to-the-same-server"></a>Copia nello stesso server

Accedere al database master con l'account di accesso dell'amministratore del server o l'account di accesso che ha creato il database che si desidera copiare. Affinché la copia del database abbia esito positivo, gli account di accesso che non sono l'amministratore del server devono essere membri del `dbmanager` ruolo.

Questo comando copia Database1 in un nuovo database denominato Database2 sullo stesso server. A seconda delle dimensioni del database, l'operazione di copia potrebbe richiedere alcuni minuti.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-to-a-different-server"></a>Copia in un server diverso

Accedere al database master del server di destinazione in cui verrà creato il nuovo database. Utilizzare un account di accesso con lo stesso nome e la stessa password del proprietario del database del database di origine nel server di origine. L'account di accesso nel server di destinazione deve essere anche un membro del `dbmanager` ruolo o essere l'account di accesso dell'amministratore del server.

Questo comando copia Database1 sul server1 in un nuovo database denominato Database2 sul server2. A seconda delle dimensioni del database, l'operazione di copia potrebbe richiedere alcuni minuti.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> È necessario configurare i firewall di entrambi i server per consentire la connessione in ingresso dall'IP del client che emette il DATABASE T-SQL CREATE DATABASE... COME copia del comando.

### <a name="copy-to-a-different-subscription"></a>Copia in una sottoscrizione diversa

È possibile utilizzare la procedura descritta nella sezione [copiare un database SQL in un server diverso](#copy-to-a-different-server) per copiare il database in un server in una sottoscrizione diversa tramite T-SQL. Assicurarsi di utilizzare un account di accesso con lo stesso nome e la stessa password del proprietario del database di origine. Inoltre, l'account di accesso deve essere un membro del `dbmanager` ruolo o un amministratore del server, sia nel server di origine che in quello di destinazione.

> [!NOTE]
> Il [portale di Azure](https://portal.azure.com), PowerShell e l'interfaccia della riga di comando di Azure non supportano la copia del database in una sottoscrizione diversa.

> [!TIP]
> La copia del database con T-SQL supporta la copia di un database da una sottoscrizione in un tenant di Azure diverso.

## <a name="monitor-the-progress-of-the-copying-operation"></a>Monitorare lo stato dell'operazione di copia

Monitorare il processo di copia eseguendo una query sulle viste [sys. databases](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql), [sys. dm_database_copies](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)e [sys. dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) . Mentre è in corso la copia, la colonna **state_desc** della vista sys. databases per il nuovo database viene impostata su **Copying**.

* Se la copia ha esito negativo, la colonna **state_desc** della vista sys. databases per il nuovo database viene impostata su **sospetta**. Eseguire l'istruzione DROP sul nuovo database e riprovare in un secondo momento.
* Se la copia ha esito positivo, la colonna **state_desc** della vista sys. databases per il nuovo database viene impostata su **online**. La copia è stata completata e il nuovo database è un database standard, che può essere modificato indipendentemente dal database di origine.

> [!NOTE]
> Se si decide di annullare il processo di copia mentre è in corso, eseguire l'istruzione [DROP DATABASE](https://docs.microsoft.com/sql/t-sql/statements/drop-database-transact-sql) nel nuovo database.

> [!IMPORTANT]
> Se è necessario creare una copia con un obiettivo di servizio sostanzialmente più piccolo rispetto all'origine, è possibile che il database di destinazione non disponga di risorse sufficienti per completare il processo di seeding, causando l'esito negativo dell'operazione di copia. In questo scenario usare una richiesta di ripristino geografico per creare una copia in un server diverso e/o in un'area diversa. Per altre informazioni, vedere [ripristinare un database SQL di Azure usando i backup del database](recovery-using-backups.md#geo-restore) .

## <a name="azure-roles-to-manage-database-copy"></a>Ruoli di Azure per gestire la copia del database

Per creare una copia del database, è necessario disporre dei seguenti ruoli

* Proprietario della sottoscrizione
* SQL Server ruolo Collaboratore o
* Ruolo personalizzato nei database di origine e di destinazione con l'autorizzazione seguente:

   Microsoft. SQL/Servers/databases/Read Microsoft. SQL/Servers/databases/Write

Per annullare una copia del database, è necessario avere i seguenti ruoli

* Proprietario della sottoscrizione
* SQL Server ruolo Collaboratore o
* Ruolo personalizzato nei database di origine e di destinazione con l'autorizzazione seguente:

   Microsoft. SQL/Servers/databases/Read Microsoft. SQL/Servers/databases/Write

Per gestire la copia del database usando il portale di Azure, sono necessarie anche le autorizzazioni seguenti:

   Microsoft. resources/subscriptions/resources/leggere Microsoft. resources/subscriptions/resources/scrivere Microsoft. resources/Deployments/Read Microsoft. resources/Deployments/Write Microsoft. resources/Deployments/operationstatuses/Read

Se si desidera visualizzare le operazioni in distribuzioni nel gruppo di risorse nel portale, le operazioni tra più provider di risorse, incluse le operazioni SQL, sono necessari questi ruoli di Azure aggiuntivi:

   Microsoft. resources/subscriptions/ResourceGroups/Deployments/Operations/Read Microsoft. resources/subscriptions/ResourceGroups/Deployments/operationstatuses/Read

## <a name="resolve-logins"></a>Risolvere gli account di accesso

Quando il nuovo database è online nel server di destinazione, utilizzare l'istruzione [ALTER USER](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current) per modificare il mapping degli utenti dal nuovo database agli account di accesso nel server di destinazione. Per risolvere gli utenti isolati, vedere [Risolvere i problemi relativi agli utenti isolati (SQL Server)](https://docs.microsoft.com/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server). Vedere anche [come gestire la sicurezza del database SQL di Azure dopo il ripristino di emergenza](active-geo-replication-security-configure.md).

Tutti gli utenti nel nuovo database mantengono le autorizzazioni di cui disponevano nel database di origine. L'utente che ha avviato la copia del database diventa il proprietario del database del nuovo database. Una volta completata la copia e prima che venga eseguito nuovamente il mapping di altri utenti, solo il proprietario del database può accedere al nuovo database.

Per informazioni sulla gestione di utenti e account di accesso quando si copia un database in un server diverso, vedere [come gestire la sicurezza del database SQL di Azure dopo il ripristino di emergenza](active-geo-replication-security-configure.md).

## <a name="database-copy-errors"></a>Errori di copia del database

Durante la copia di un database nel database SQL di Azure, possono essere rilevati gli errori seguenti. Per altre informazioni, vedere [Copiare un database SQL di Azure](database-copy.md).

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

* Per informazioni sugli account di accesso, vedere [gestire gli account di accesso](logins-create-manage.md) e [come gestire la sicurezza del database SQL di Azure dopo il ripristino di emergenza](active-geo-replication-security-configure.md).
* Per esportare un database, vedere [esportare il database in un BacPac](database-export.md).
