---
title: Copiare un database SQL di Azure con Transact-SQL | Documentazione Microsoft
description: Creare una copia di un database SQL di Azure tramite Transact-SQL
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 40ea3718-33f8-41af-90cb-3aa15059365e
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 60bcd02d24e2084b9020ce56ef6a9f8268c6b1b5
ms.openlocfilehash: 3ade1f2850b2a67f68e8a4a7f519b7dc7ba1de10


---
# <a name="copy-an-azure-sql-database-using-transact-sql"></a>Copiare un database SQL di Azure con Transact-SQL

La procedura seguente illustra come copiare un database SQL con Transact-SQL nello stesso server o in un server diverso. L'operazione di copia di database usa l'istruzione [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . 

> [!NOTE]
> È possibile anche copiare un database SQL usando il [portale Azure](sql-database-copy-portal.md) o [PowerShell](sql-database-copy-powershell.md).
>

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure. Se è necessaria una sottoscrizione di Azure, fare semplicemente clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.
* Database SQL di Azure. Se non si dispone di un database SQL, crearne uno seguendo i passaggi indicati in questo articolo: [Creare il primo database SQL di Azure](sql-database-get-started.md).
* [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms174173.aspx). Se non si dispone di SSMS o se le funzionalità descritte in questo articolo non sono disponibili, [scaricare la versione più recente](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="copy-your-sql-database"></a>Copiare il database SQL
Accedere al database master usando l'account di accesso dell'entità di livello server o l'account di accesso con cui è stato creato il database che si desidera copiare. Gli account di accesso che non corrispondono all'entità di livello server devono essere membri del ruolo dbmanager per copiare i database. Per ulteriori informazioni sugli account di accesso e la connessione al server, vedere [Gestire gli accessi](sql-database-manage-logins.md).

Iniziare a copiare il database di origine con l'istruzione [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . L'esecuzione di questa istruzione avvia il processo di copia del database. Poiché la copia di un database è un processo asincrono, l'istruzione CREATE DATABASE viene restituita prima del completamento della copia del database.

### <a name="copy-a-sql-database-to-the-same-server"></a>Copiare un database SQL nello stesso server
Accedere al database master usando l'account di accesso dell'entità di livello server o l'account di accesso con cui è stato creato il database che si desidera copiare. Gli account di accesso che non corrispondono all'entità di livello server devono essere membri del ruolo dbmanager per copiare i database.

Questo comando copia Database1 in un nuovo database denominato Database2 sullo stesso server. A seconda delle dimensioni del database, l'operazione di copia potrebbe richiedere alcuni minuti.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Copiare un database SQL in un server diverso
Accedere al database master del server di destinazione, il server del database SQL di Azure in cui verrà creato il nuovo database. Usare un account di accesso con lo stesso nome e la stessa password del proprietario (DBO) del database di origine nel server del database SQL di Azure di origine. L'account di accesso nel server di destinazione deve anche essere un membro del ruolo dbmanager o corrispondere all'account di accesso dell'entità di livello server.

Questo comando copia Database1 sul server1 in un nuovo database denominato Database2 sul server2. A seconda delle dimensioni del database, l'operazione di copia potrebbe richiedere alcuni minuti.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;


## <a name="monitor-the-progress-of-the-copy-operation"></a>Monitorare lo stato dell'operazione di copia
Monitorare il processo di copia eseguendo una query sulle visualizzazioni sys.databases e sys.dm_database_copies. Durante il processo di copia, la colonna state_desc della visualizzazione sys.databases per il nuovo database viene impostata su COPYING.

* Se il processo di copia non viene completato, la colonna state_desc della visualizzazione sys.databases per il nuovo database viene impostata su SUSPECT. In questo caso, eseguire l'istruzione DROP sul nuovo database e riprovare in un secondo momento.
* Se il processo di copia viene completato, la colonna state_desc della visualizzazione sys.databases per il nuovo database viene impostata su ONLINE. In questo caso, la copia è stata completata e il nuovo database è un database standard, che può essere modificato indipendentemente dal database di origine.

> [!NOTE]
> * Se si decide di annullare il processo di copia mentre è in corso, eseguire l'istruzione [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) nel nuovo database. In alternativa, anche l'esecuzione dell'istruzione DROP DATABASE sul database di origine annulla il processo di copia.
> 
> 

## <a name="resolve-logins-after-the-copy-operation-completes"></a>Risolvere gli account di accesso al termine dell'operazione di copia
Dopo che il nuovo database è online nel server di destinazione, usare l'istruzione [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) per modificare il mapping degli utenti del nuovo database agli account di accesso nel server di destinazione. Per risolvere gli utenti isolati, vedere [Risolvere i problemi relativi agli utenti isolati (SQL Server)](https://msdn.microsoft.com/library/ms175475.aspx). Vedere anche [Come gestire la sicurezza dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).

Tutti gli utenti nel nuovo database gestiscono le autorizzazioni di cui disponevano nel database di origine. L'utente che ha avviato la copia del database diventa il proprietario del nuovo database e gli viene assegnato un nuovo ID di sicurezza (SID). Dopo il completamento della copia e prima che venga modificato il mapping di altri utenti, solo l'account di accesso che ha avviato la copia, il proprietario del database (DBO), può accedere al nuovo database.

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sulla gestione di utenti e account di accesso durante la copia di un database in un server logico diverso, vedere [Come gestire la sicurezza dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).
* [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
* [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)



<!--HONumber=Feb17_HO2-->


