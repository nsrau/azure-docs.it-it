<properties 
    pageTitle="Copiare un database SQL di Azure con Transact-SQL | Microsoft Azure" 
    description="Creare una copia di un database SQL di Azure tramite Transact-SQL" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/06/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Copiare un database SQL di Azure con Transact-SQL


> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)



La procedura seguente illustra come copiare un database SQL con Transact-SQL. L'operazione di copia del database consente di copiare un database SQL in un nuovo database usando l'istruzione [CREATE DATABASE](). La copia è un backup snapshot del database creato nello stesso server o in un server diverso.


> [AZURE.NOTE] Il database SQL di Azure [crea automaticamente e conserva i backup](sql-database-automated-backups.md) per ogni database dell'utente che è possibile ripristinare.


Al completamento del processo di copia, il nuovo database è completamente funzionante, indipendente dal database di origine. Al completamento della copia, il nuovo database è coerente da un punto di vista transazionale con il database di origine. Il livello di servizio e il livello di prestazioni (piano tariffario) della copia del database sono gli stessi del database di origine. Al termine del processo di copia, la copia diventa un database indipendente e completamente funzionante. Gli account di accesso, gli utenti e le autorizzazioni possono essere gestiti in modo indipendente.


Quando si copia un database nello stesso server logico, è possibile usare gli stessi account di accesso per entrambi i database. L'entità di sicurezza usata per copiare il database diventa il proprietario del database (DBO) nel nuovo database. Tutti gli utenti del database, le relative autorizzazioni e i relativi identificatori di sicurezza (SID) vengono copiati nella copia del database.


Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure. Se è necessaria una sottoscrizione ad Azure, fare semplicemente clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.
- Database SQL di Azure. Se non si dispone di un database SQL, crearne uno seguendo i passaggi indicati in questo articolo: [Creare il primo database SQL di Azure](sql-database-get-started.md).
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms174173.aspx). Se non si dispone di SSMS o se le funzionalità descritte in questo articolo non sono disponibili, [scaricare la versione più recente](https://msdn.microsoft.com/library/mt238290.aspx).




## Copiare il database SQL

Accedere al database master usando l'account di accesso dell'entità di livello server o l'account di accesso con cui è stato creato il database che si desidera copiare. Gli account di accesso che non corrispondono all'entità di livello server devono essere membri del ruolo dbmanager per copiare i database. Per altre informazioni sugli account di accesso e sulla connessione al server, vedere rispettivamente Gestione di database, account di accesso e utenti nel database SQL di Azure e Sviluppo per il database SQL di Azure: procedure.

Iniziare a copiare il database di origine con l'istruzione CREATE DATABASE. L'esecuzione di questa istruzione avvia il processo di copia del database. Poiché la copia di un database è un processo asincrono, l'istruzione CREATE DATABASE viene restituita prima del completamento della copia del database.


### Copiare un database SQL nello stesso server

Accedere al database master usando l'account di accesso dell'entità di livello server o l'account di accesso con cui è stato creato il database che si desidera copiare. Gli account di accesso che non corrispondono all'entità di livello server devono essere membri del ruolo dbmanager per copiare i database.

Questo comando copia Database1 in un nuovo database denominato Database2 sullo stesso server. A seconda delle dimensioni del database, l'operazione di copia potrebbe richiedere alcuni minuti.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### Copiare un database SQL in un server diverso

Accedere al database master del server di destinazione, il server del database SQL di Azure in cui verrà creato il nuovo database. Usare un account di accesso con lo stesso nome e la stessa password del proprietario (DBO) del database di origine nel server del database SQL di Azure di origine. L'account di accesso nel server di destinazione deve anche essere un membro del ruolo dbmanager o corrispondere all'account di accesso dell'entità di livello server.

Questo comando copia Database1 sul server1 in un nuovo database denominato Database2 sul server2. A seconda delle dimensioni del database, l'operazione di copia potrebbe richiedere alcuni minuti.


    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;
    

## Monitorare lo stato dell'operazione di copia

Monitorare il processo di copia eseguendo una query sulle visualizzazioni sys.databases e sys.dm\_database\_copies. Durante il processo di copia, la colonna state\_desc della visualizzazione sys.databases per il nuovo database viene impostata su COPYING.


- Se il processo di copia non viene completato, la colonna state\_desc della visualizzazione sys.databases per il nuovo database viene impostata su SUSPECT. In questo caso, eseguire l'istruzione DROP sul nuovo database e riprovare in un secondo momento.
- Se il processo di copia viene completato, la colonna state\_desc della visualizzazione sys.databases per il nuovo database viene impostata su ONLINE. In questo caso, la copia è stata completata e il nuovo database è un database standard, che può essere modificato indipendentemente dal database di origine.



## Passaggi successivi


- Se si decide di annullare il processo di copia mentre è in corso, eseguire l'istruzione [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) nel nuovo database. In alternativa, anche l'esecuzione dell'istruzione DROP DATABASE sul database di origine annulla il processo di copia.
- Dopo che il nuovo database è online nel server di destinazione, usare l'istruzione [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) per modificare il mapping degli utenti del nuovo database agli account di accesso nel server di destinazione. Tutti gli utenti nel nuovo database gestiscono le autorizzazioni di cui disponevano nel database di origine. L'utente che ha avviato la copia del database diventa il proprietario del nuovo database e gli viene assegnato un nuovo ID di sicurezza (SID). Dopo il completamento della copia e prima che venga modificato il mapping di altri utenti, solo l'account di accesso che ha avviato la copia, il proprietario del database (DBO), può accedere al nuovo database.




## Risorse aggiuntive

- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Esercitazioni di ripristino di emergenza](sql-database-disaster-recovery-drills.md)
- [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0615_2016-->