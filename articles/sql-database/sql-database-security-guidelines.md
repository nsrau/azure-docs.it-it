<properties
   pageTitle="Linee guida sulla sicurezza e limitazioni del Database SQL di Azure | Microsoft Azure"
   description="Linee guida per il Database SQL di Microsoft Azure e le limitazioni relative alla sicurezza."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="05/13/2016"
   ms.author="rickbyh"/>

# Linee guida e limitazioni per la sicurezza per il database SQL di Azure

In questo argomento vengono descritte le linee guida per il Database SQL di Microsoft Azure e le limitazioni relative alla sicurezza. Tenere presente quanto segue quando si gestisce la protezione dei database SQL Azure.

## Accedere al database master virtuale

In genere, solo gli amministratori hanno necessità di accedere al database master. L'accesso di routine ad ogni database utente deve avvenire tramite gli utenti non amministratori del database indipendente creati in ogni database. Quando si usano gli utenti del database indipendente non è necessario creare gli account di accesso nel database master. Per altre informazioni, vedere [Utenti di database indipendente: rendere portabile un database](https://msdn.microsoft.com/library/ff929188.aspx).


## Firewall

Il firewall di SQL Server che ha come ambito l'intero Server SQL di Azure viene in genere configurato tramite il portale e deve ammettere solo gli indirizzi IP usati dagli amministratori. Connettersi a un database utente e quindi usare l'istruzione Transact-SQL [sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) per creare una regola firewall con ambito di database che apre l'intervallo di indirizzi IP necessario per ogni database.

Il servizio Database SQL di Azure è disponibile solo tramite la porta TCP 1433. Per accedere al database SQL dal computer, assicurarsi che il firewall del computer client consenta la comunicazione TCP in uscita sulla porta TCP 1433. Se non sono necessarie per altre applicazioni, bloccare le connessioni in ingresso sulla porta TCP 1433.

Come parte del processo di connessione, le connessioni da macchine virtuali di Azure vengono reindirizzate a un indirizzo IP diverso e a una porta, univoca per ogni ruolo di lavoro. Il numero di porta sarà compreso tra 11000 e 11999. Per altre informazioni sulle porte TCP, vedere [Porte superiori a 1433 per ADO.NET 4.5, e database SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md).


## Autenticazione

Usare l'autenticazione di Active Directory (sicurezza integrata) laddove possibile. Per informazioni su come configurare l'autenticazione di Active Directory, vedere [Connessione al database SQL con l'autenticazione di Azure Active Directory](sql-database-aad-authentication.md) e [Scegliere una modalità di autenticazione](https://msdn.microsoft.com/library/ms144284.aspx) nella documentazione online di SQL Server.

Usare gli utenti del database indipendente per migliorare la scalabilità. Per ulteriori informazioni, vedere [Utenti del database indipendente - rendere un database portabile](https://msdn.microsoft.com/library/ff929188.aspx), [CREA UTENTE (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx), e [Database indipendenti](https://technet.microsoft.com/library/ff929071.aspx).

Il motore di Database chiude le connessioni che rimangono inattive per più di 30 minuti. La connessione deve accedere nuovamente prima di poter essere utilizzata.

Le connessioni continuamente attive al Database SQL richiedono la riautorizzazione (operazione eseguita dal motore di Database) almeno ogni 10 ore. Il motore di Database tenta la nuova autorizzazione utilizzando la password originariamente inviata ed non è necessario alcun input dell'utente. Per motivi di prestazioni quando si reimposta una password nel Database SQL, la connessione non viene nuovamente autenticata, anche se viene reimpostata a causa del pool di connessioni. Ciò differisce dal comportamento del Server SQL locale. Se la password è stata modificata poichè la connessione è stata inizialmente autorizzata, è necessario terminare la connessione e effettuarne una nuova utilizzando la nuova password. Un utente con l'autorizzazione KILL DATABASE CONNECTION può terminare in modo esplicito una connessione al Database SQL utilizzando il comando [KILL](https://msdn.microsoft.com/library/ms173730.aspx).

## Account di accesso e utenti

Quando si gestiscono gli account di accesso e gli utenti nel Database SQL, esistono alcune restrizioni.


- È necessario essere connessi al database **master** durante l'esecuzione delle istruzioni ``CREATE/ALTER/DROP DATABASE``. L'utente del database nel database master corrispondente per l'account di accesso dell'entità di livello server non può essere modificato o eliminato. 
- L'inglese americano è la lingua predefinita dell'account di accesso dell'entità a livello di server.
- Per l'accesso al database **master**, ogni account di accesso deve essere mappato a un account utente nel database**master**. Il database **master** non supporta utenti del database indipendente.
- Solo l'accesso dell’entità di livello server e dei membri del ruolo del database **dbmanager** nel database **master** dispongono dell'autorizzazione per eseguire le istruzioni ``CREATE DATABASE`` e ``DROP DATABASE``.
- È necessario essere connessi al database master durante l'esecuzione delle istruzioni ``CREATE/ALTER/DROP LOGIN``. È tuttavia sconsigliato l'uso di account di accesso. Usare invece gli utenti del database indipendente
- Per connettersi a un database utente è necessario fornire il nome del database nella stringa di connessione.
- Solo l'accesso dell’entità di livello server e dei membri del ruolo del database **loginmanager** nel database **master** dispongono dell'autorizzazione per eseguire le istruzioni ``CREATE LOGIN``, ``ALTER LOGIN``, e ``DROP LOGIN``.
- Quando si esegue le istruzioni ``CREATE/ALTER/DROP LOGIN`` e ``CREATE/ALTER/DROP DATABASE`` in un'applicazione ADO.NET, non è consentito utilizzare i comandi con parametri. Per ulteriori informazioni, vedere [Comandi e parametri](https://msdn.microsoft.com/library/ms254953.aspx).
- Quando si esegue le istruzioni ``CREATE/ALTER/DROP DATABASE`` e ``CREATE/ALTER/DROP LOGIN``, ognuna di queste istruzioni deve essere l'unica istruzione in un batch Transact-SQL. In caso contrario, si verifica un errore. Ad esempio, il seguente Transact-SQL controlla se il database esiste. Se esiste, un’istruzione ``DROP DATABASE`` viene chiamata per rimuovere il database. Poiché l’istruzione ``DROP DATABASE`` non è l'unica istruzione nel batch, l'esecuzione della seguente istruzione Transact-SQL genera un errore.

```
IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
     DROP DATABASE [database_name];
GO
```

- Quando si esegue l’istruzione ``CREATE USER`` con l’opzione ``FOR/FROM LOGIN``, l’istruzione deve essere l'unica in un batch Transact-SQL.
- Quando si esegue l’istruzione ``ALTER USER`` con l’opzione ``WITH LOGIN``, l’istruzione deve essere l'unica in un batch Transact-SQL.
- Per ``CREATE/ALTER/DROP`` un utente richiede l’autorizzazione ``ALTER ANY USER`` per il database.
- Quando il proprietario di un ruolo del database tenta di aggiungere o rimuovere un altro utente del database in o da tale ruolo del database, potrebbe verificarsi il seguente errore: **L’utente o il ruolo 'Name' non esiste nel database.** Questo errore si verifica perché l'utente non è visibile al proprietario. Per risolvere questo problema, concedere al proprietario del ruolo l’autorizzazione``VIEW DEFINITION`` per l'utente. 

Per ulteriori informazioni su account di accesso e utenti, vedere [Gestione di database e account di accesso nel database SQL di Azure](sql-database-manage-logins.md).


## Vedere anche

[Firewall del database SQL di Azure](sql-database-firewall-configure.md)

[Procedura: configurare le impostazioni del firewall (Database SQL di Azure)](sql-database-configure-firewall-settings.md)

[Gestione di database e account di accesso in database SQL di Azure](sql-database-manage-logins.md)

[Centro sicurezza per il motore di Database di SQL Server e il Database SQL di Azure](https://msdn.microsoft.com/library/bb510589)

<!---HONumber=AcomDC_0518_2016-->