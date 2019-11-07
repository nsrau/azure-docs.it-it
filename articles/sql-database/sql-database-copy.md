---
title: Copiare un database SQL di Azure
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
ms.date: 09/04/2019
ms.openlocfilehash: d49896d8088ae1352cb2785d061cde6c8647cb89
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690812"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Creare una copia coerente a livello transazionale di un database SQL di Azure

Il database SQL di Azure offre diversi metodi per la creazione di una copia coerente a livello di transazioni di un database SQL di Azure esistente ([database singolo](sql-database-single-database.md)) nello stesso server o in un server diverso. È possibile copiare un database SQL tramite il portale di Azure, PowerShell o T-SQL. 

## <a name="overview"></a>Panoramica

Una copia del database è uno snapshot del database di origine al momento della richiesta di copia. È possibile selezionare lo stesso server o un server diverso. È anche possibile scegliere di mantengono il livello di servizio e le dimensioni di calcolo oppure utilizzare una dimensione di calcolo diversa nello stesso livello di servizio (edizione). Al termine del processo di copia, questa diventa un database indipendente e completamente funzionante. A questo punto, è possibile aggiornare o effettuare il downgrade della copia a qualsiasi edizione. Gli account di accesso, gli utenti e le autorizzazioni possono essere gestiti in modo indipendente. La copia viene creata usando la tecnologia di replica geografica e, una volta completato il seeding, il collegamento di replica geografica viene terminato automaticamente. Tutti i requisiti per l'uso della replica geografica si applicano all'operazione di copia del database. Per informazioni dettagliate, vedere [Panoramica della replica geografica attiva](sql-database-active-geo-replication.md) .

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

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per copiare un database tramite PowerShell, usare il cmdlet [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) . 

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

Per uno script di esempio completo, vedere [Copiare un database in un nuovo server](scripts/sql-database-copy-database-to-new-server-powershell.md).

La copia del database è un'operazione asincrona, ma il database di destinazione viene creato immediatamente dopo l'accettazione della richiesta. Se è necessario annullare l'operazione di copia mentre è ancora in corso, eliminare il database di destinazione usando il cmdlet [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) .  

## <a name="rbac-roles-to-manage-database-copy"></a>Ruoli RBAC per gestire la copia del database

Per creare una copia del database, è necessario disporre dei seguenti ruoli

- Proprietario della sottoscrizione
- SQL Server ruolo Collaboratore o
- Ruolo personalizzato nei database di origine e di destinazione con l'autorizzazione seguente:

   Microsoft.Sql/servers/databases/read   
   Microsoft.Sql/servers/databases/write   

Per annullare una copia del database, è necessario avere i seguenti ruoli

- Proprietario della sottoscrizione
- SQL Server ruolo Collaboratore o
- Ruolo personalizzato nei database di origine e di destinazione con l'autorizzazione seguente:

   Microsoft.Sql/servers/databases/read   
   Microsoft.Sql/servers/databases/write   
   
Per gestire la copia del database con portale di Azure, sono necessarie anche le autorizzazioni seguenti:

&nbsp; &nbsp; &nbsp; Microsoft. resources/subscriptions/resources/Read   
&nbsp; &nbsp; &nbsp; Microsoft. resources/subscriptions/resources/Write   
&nbsp; &nbsp; &nbsp; Microsoft. resources/Deployments/Read   
&nbsp; &nbsp; &nbsp; Microsoft. resources/Deployments/Write   
&nbsp; &nbsp; &nbsp; Microsoft. resources/Distributions/operationstatuses/Read    

Se si desidera visualizzare le operazioni in distribuzioni nel gruppo di risorse nel portale, le operazioni tra più provider di risorse, incluse le operazioni SQL, sono necessari i ruoli RBAC aggiuntivi: 

&nbsp; &nbsp; &nbsp; Microsoft. resources/subscriptions/ResourceGroups/Deployments/Operations/Read   
&nbsp; &nbsp; &nbsp; Microsoft. resources/subscriptions/ResourceGroups/Distributions/operationstatuses/Read



## <a name="copy-a-database-by-using-transact-sql"></a>Copiare un database tramite Transact-SQL

Accedere al database master con l'account di accesso dell'entità di livello server o l'account di accesso con cui è stato creato il database che si desidera copiare. Affinché il processo di copia del database abbia esito positivo, gli account di accesso che non corrispondono all'entità di livello server devono essere membri del ruolo dbmanager. Per ulteriori informazioni sugli account di accesso e la connessione al server, vedere [Gestire gli accessi](sql-database-manage-logins.md).

Iniziare a copiare il database di origine con l'istruzione [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . L'esecuzione di questa istruzione avvia il processo di copia del database. Poiché la copia di un database è un processo asincrono, l'istruzione CREATE DATABASE viene restituita prima del completamento della copia del database.

### <a name="copy-a-sql-database-to-the-same-server"></a>Copiare un database SQL nello stesso server

Accedere al database master con l'account di accesso dell'entità di livello server o l'account di accesso con cui è stato creato il database che si desidera copiare. Affinché il processo di copia del database abbia esito positivo, gli account di accesso che non corrispondono all'entità di livello server devono essere membri del ruolo dbmanager.

Questo comando copia Database1 in un nuovo database denominato Database2 sullo stesso server. A seconda delle dimensioni del database, l'operazione di copia potrebbe richiedere alcuni minuti.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database2 AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Copiare un database SQL in un server diverso

Accedere al database master del server di destinazione, il server di database SQL in cui verrà creato il nuovo database. Usare un account di accesso con lo stesso nome e la stessa password del proprietario del database di origine nel server di database SQL di origine. L'account di accesso nel server di destinazione deve anche essere un membro del ruolo dbmanager o corrispondere all'account di accesso dell'entità di livello server.

Questo comando copia Database1 sul server1 in un nuovo database denominato Database2 sul server2. A seconda delle dimensioni del database, l'operazione di copia potrebbe richiedere alcuni minuti.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database2 AS COPY OF server1.Database1;
    
> [!IMPORTANT]
> È necessario configurare i firewall di entrambi i server per consentire la connessione in ingresso dall'IP del client che emette il comando di copia T-SQL.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Copiare un database SQL in una sottoscrizione diversa

È possibile utilizzare la procedura descritta nella sezione precedente per copiare il database in un server di database SQL in una sottoscrizione diversa. Assicurarsi di utilizzare un account di accesso con lo stesso nome e la stessa password del proprietario del database di origine ed è un membro del ruolo dbmanager o l'account di accesso dell'entità di livello server. 

> [!NOTE]
> Il [portale di Azure](https://portal.azure.com) non supporta la copia in una sottoscrizione diversa perché il portale chiama l'API ARM e usa i certificati di sottoscrizione per accedere a entrambi i server che coinvolgono la replica geografica.  

### <a name="monitor-the-progress-of-the-copying-operation"></a>Monitorare lo stato dell'operazione di copia

Monitorare il processo di copia eseguendo una query sulle visualizzazioni sys.databases e sys.dm_database_copies. Durante il processo di copia, la colonna **state_desc** della visualizzazione sys.databases per il nuovo database viene impostata su **COPYING**.

* Se il processo di copia non viene completato, la colonna **state_desc** della visualizzazione sys.databases per il nuovo database viene impostata su **SUSPECT**. Eseguire l'istruzione DROP sul nuovo database e riprovare in un secondo momento.
* Se il processo di copia viene completato, la colonna **state_desc** della visualizzazione sys.databases per il nuovo database viene impostata su **ONLINE**. La copia è stata completata e il nuovo database è un database standard, che può essere modificato indipendentemente dal database di origine.

> [!NOTE]
> Se si decide di annullare il processo di copia mentre è in corso, eseguire l'istruzione [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) nel nuovo database. In alternativa, anche l'esecuzione dell'istruzione DROP DATABASE sul database di origine annulla il processo di copia.

> [!IMPORTANT]
> Se è necessario creare una copia con un SLO notevolmente più piccolo rispetto all'origine, è possibile che il database di destinazione non disponga di risorse sufficienti per completare il processo di seeding, causando l'esito negativo dell'operazione di copia. In questo scenario usare una richiesta di ripristino geografico per creare una copia in un server diverso e/o in un'area diversa. Per ulteriori informazioni, vedere [ripristinare un database SQL di Azure tramite backup del database](sql-database-recovery-using-backups.md#geo-restore) .


## <a name="resolve-logins"></a>Risolvere gli account di accesso

Dopo che il nuovo database è online nel server di destinazione, usare l'istruzione [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) per modificare il mapping degli utenti del nuovo database agli account di accesso nel server di destinazione. Per risolvere gli utenti isolati, vedere [Risolvere i problemi relativi agli utenti isolati (SQL Server)](https://msdn.microsoft.com/library/ms175475.aspx). Vedere anche [Come gestire la sicurezza del database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).

Tutti gli utenti nel nuovo database mantengono le autorizzazioni di cui disponevano nel database di origine. L'utente che ha avviato la copia del database diventa il proprietario del nuovo database e gli viene assegnato un nuovo ID di sicurezza (SID). Dopo il completamento della copia e prima che venga modificato il mapping di altri utenti, solo l'account di accesso che ha avviato la copia, il proprietario del database, può accedere al nuovo database.

Per informazioni sulla gestione di utenti e account di accesso durante la copia di un database in un server di database SQL diverso, vedere [Come gestire la sicurezza dei database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sugli account di accesso, vedere [Controllo e concessione dell'accesso al database](sql-database-manage-logins.md) e [Configurare e gestire la sicurezza dei database SQL di Azure per il ripristino geografico o il failover](sql-database-geo-replication-security-config.md).
* Per esportare un database, vedere [Esportare un database SQL di Azure in un file BACPAC](sql-database-export.md).
