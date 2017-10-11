---
title: Copiare un database SQL di Azure | Documentazione Microsoft
description: Creare una copia di un database SQL di Azure
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 5aaf6bcd-3839-49b5-8c77-cbdf786e359b
ms.service: sql-database
ms.custom: load & move data
ms.devlang: NA
ms.date: 06/15/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.openlocfilehash: 8c1e3c80b9f24089dc99463d6ea8ae5d0ea7b19d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="copy-an-azure-sql-database"></a>Copiare un database SQL di Azure

Il Database SQL di Azure fornisce diversi metodi per la creazione di una copia coerente a livello transazionale di un database SQL di Azure esistente nello stesso server o un altro server. È possibile copiare un database SQL tramite il portale di Azure, PowerShell o T-SQL. 

## <a name="overview"></a>Panoramica

Una copia del database è uno snapshot del database di origine al momento della richiesta di copia. È possibile selezionare lo stesso server o un server diverso, il relativo livello di servizio e livello di prestazioni o un livello di prestazioni diverso all'interno dello stesso livello di servizio (edizione). Al termine del processo di copia, questa diventa un database indipendente e completamente funzionante. A questo punto, è possibile aggiornare o effettuare il downgrade della copia a qualsiasi edizione. Gli account di accesso, gli utenti e le autorizzazioni possono essere gestiti in modo indipendente.  

## <a name="logins-in-the-database-copy"></a>Account di accesso nella copia del database

Quando si copia un database nello stesso server logico, è possibile usare gli stessi account di accesso per entrambi i database. L'entità di sicurezza usata per copiare il database diventa il proprietario del database nel nuovo database. Tutti gli utenti del database, le relative autorizzazioni e i relativi identificatori di sicurezza (SID) vengono copiati nella copia del database.  

Quando la copia del database viene eseguita su un server logico diverso, l'entità di sicurezza sul nuovo server diventa il proprietario del database nel nuovo database. L'uso di [utenti di database indipendente](sql-database-manage-logins.md) per l'accesso ai dati garantisce che per i database primari e secondari ci siano sempre le stesse credenziali utente, in modo che dopo il completamento della copia sia possibile accedervi immediatamente con le stesse credenziali. 

Se si usa [Azure Active Directory](../active-directory/active-directory-whatis.md), è possibile eliminare completamente l'esigenza di gestione delle credenziali nella copia. Tuttavia, quando si copia il database in un nuovo server, è possibile che l'accesso basato sulle credenziali non funzioni perché le credenziali di accesso non esistono nel nuovo server. Per informazioni sulla gestione degli accessi con credenziali durante la copia di un database in un server logico differente, vedere [Configurare e gestire la sicurezza dei database SQL di Azure per il ripristino geografico o il failover](sql-database-geo-replication-security-config.md). 

Dopo il completamento della copia e prima che venga modificato il mapping di altri utenti, solo l'account di accesso che ha avviato la copia, il proprietario del database, può accedere al nuovo database. Per risolvere gli account di accesso al termine dell'operazione di copia, vedere [Risolvere gli account di accesso](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Copiare un database tramite il portale di Azure

Per copiare un database tramite il portale di Azure, aprire la pagina per il database e quindi fare clic su **Copia**. 

   ![Copia del database](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>Copiare un database tramite PowerShell

Per copiare un database tramite PowerShell usare il cmdlet [New-AzureRmSqlDatabaseCopy](/powershell/module/azurerm.sql/new-azurermsqldatabasecopy). 

```PowerShell
New-AzureRmSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

Per uno script di esempio completo, vedere [Copiare un database in un nuovo server](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="copy-a-database-by-using-transact-sql"></a>Copiare un database tramite Transact-SQL

Accedere al database master con l'account di accesso dell'entità di livello server o l'account di accesso con cui è stato creato il database che si desidera copiare. Affinché il processo di copia del database abbia esito positivo, gli account di accesso che non corrispondono all'entità di livello server devono essere membri del ruolo dbmanager. Per ulteriori informazioni sugli account di accesso e la connessione al server, vedere [Gestire gli accessi](sql-database-manage-logins.md).

Iniziare a copiare il database di origine con l'istruzione [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . L'esecuzione di questa istruzione avvia il processo di copia del database. Poiché la copia di un database è un processo asincrono, l'istruzione CREATE DATABASE viene restituita prima del completamento della copia del database.

### <a name="copy-a-sql-database-to-the-same-server"></a>Copiare un database SQL nello stesso server
Accedere al database master con l'account di accesso dell'entità di livello server o l'account di accesso con cui è stato creato il database che si desidera copiare. Affinché il processo di copia del database abbia esito positivo, gli account di accesso che non corrispondono all'entità di livello server devono essere membri del ruolo dbmanager.

Questo comando copia Database1 in un nuovo database denominato Database2 sullo stesso server. A seconda delle dimensioni del database, l'operazione di copia potrebbe richiedere alcuni minuti.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Copiare un database SQL in un server diverso

Accedere al database master del server di destinazione, il server del database SQL in cui verrà creato il nuovo database. Usare un account di accesso con lo stesso nome e la stessa password del proprietario del database di origine nel server del database SQL di origine. L'account di accesso nel server di destinazione deve anche essere un membro del ruolo dbmanager o corrispondere all'account di accesso dell'entità di livello server.

Questo comando copia Database1 sul server1 in un nuovo database denominato Database2 sul server2. A seconda delle dimensioni del database, l'operazione di copia potrebbe richiedere alcuni minuti.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;


### <a name="monitor-the-progress-of-the-copying-operation"></a>Monitorare lo stato dell'operazione di copia

Monitorare il processo di copia eseguendo una query sulle visualizzazioni sys.databases e sys.dm_database_copies. Durante il processo di copia, la colonna **state_desc** della visualizzazione sys.databases per il nuovo database viene impostata su **COPYING**.

* Se il processo di copia non viene completato, la colonna **state_desc** della visualizzazione sys.databases per il nuovo database viene impostata su **SUSPECT**. Eseguire l'istruzione DROP sul nuovo database e riprovare in un secondo momento.
* Se il processo di copia viene completato, la colonna **state_desc** della visualizzazione sys.databases per il nuovo database viene impostata su **ONLINE**. La copia è stata completata e il nuovo database è un database standard, che può essere modificato indipendentemente dal database di origine.

> [!NOTE]
> Se si decide di annullare il processo di copia mentre è in corso, eseguire l'istruzione [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) nel nuovo database. In alternativa, anche l'esecuzione dell'istruzione DROP DATABASE sul database di origine annulla il processo di copia.
> 

## <a name="resolve-logins"></a>Risolvere gli account di accesso

Dopo che il nuovo database è online nel server di destinazione, usare l'istruzione [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) per modificare il mapping degli utenti del nuovo database agli account di accesso nel server di destinazione. Per risolvere gli utenti isolati, vedere [Risolvere i problemi relativi agli utenti isolati (SQL Server)](https://msdn.microsoft.com/library/ms175475.aspx). Vedere anche [Come gestire la sicurezza dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).

Tutti gli utenti nel nuovo database mantengono le autorizzazioni di cui disponevano nel database di origine. L'utente che ha avviato la copia del database diventa il proprietario del nuovo database e gli viene assegnato un nuovo ID di sicurezza (SID). Dopo il completamento della copia e prima che venga modificato il mapping di altri utenti, solo l'account di accesso che ha avviato la copia, il proprietario del database, può accedere al nuovo database.

Per informazioni sulla gestione di utenti e account di accesso durante la copia di un database in un server logico diverso, vedere [Configurare e gestire la sicurezza dei database SQL di Azure per il ripristino geografico o il failover](sql-database-geo-replication-security-config.md).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sugli account di accesso, vedere [Controllo e concessione dell'accesso al database](sql-database-manage-logins.md) e [Configurare e gestire la sicurezza dei database SQL di Azure per il ripristino geografico o il failover](sql-database-geo-replication-security-config.md).
* Per esportare un database, vedere [Esportare un database SQL di Azure in un file BACPAC](sql-database-export.md).
