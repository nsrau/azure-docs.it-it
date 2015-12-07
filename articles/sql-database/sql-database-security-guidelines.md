<properties
   pageTitle="Linee guida sulla sicurezza e limitazioni del Database SQL di Azure | Microsoft Azure"
   description="Linee guida per il Database SQL di Microsoft Azure e le limitazioni relative alla sicurezza."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="11/24/2015"
   ms.author="rickbyh"/>

# Linee guida e limitazioni per la sicurezza per il database SQL di Azure

In questo argomento vengono descritte le linee guida per il Database SQL di Microsoft Azure e le limitazioni relative alla sicurezza. Tenere presente quanto segue quando si gestisce la protezione dei database SQL Azure.

## Firewall

Il servizio Database SQL di Azure è disponibile solo tramite la porta TCP 1433. Per accedere al Database SQL dal computer, assicurarsi che il firewall consenta la comunicazione TCP in uscita sulla porta TCP 1433. Come parte del processo di connessione, le connessioni da macchine virtuali di Azure vengono reindirizzate a un indirizzo IP diverso e a una porta, univoca per ogni ruolo di lavoro. Il numero di porta sarà compreso tra 11000 e 11999.

Prima di connettersi al server di Database SQL di Azure per la prima volta, è necessario utilizzare il [portale di Azure](https://portal.azure.com) o [il portale di gestione della piattaforma Azure](https://manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/All/dashboard) per configurare il firewall del Database di SQL Azure. È necessario creare un'impostazione del firewall a livello di server che consenta tentativi di connessione dal computer o Azure al server di Database SQL di Azure. Inoltre, se si desidera controllare l'accesso ad alcuni database nel server di Database SQL di Azure, creare regole del firewall a livello di database per i rispettivi database. Per altre informazioni, vedere [Firewall di database SQL di Azure](sql-database-firewall-configure.md).

## Crittografia di connessione e convalida dei certificati

Tutte le comunicazioni tra Database SQL e l'applicazione richiedono sempre la crittografia (SSL). Se l'applicazione client non convalida i certificati al momento della connessione, la connessione al Database SQL è soggetta ad attacchi "man in the middle".

Per convalidare i certificati con gli strumenti o il codice dell'applicazione, richiedere una connessione crittografata in modo esplicito e non considerare attendibili i certificati del server. Se il codice dell'applicazione o gli strumenti non richiedono una connessione crittografata, riceveranno comunque connessioni crittografate. Tuttavia, potrebbero non convalidare i certificati del server e pertanto saranno soggetti ad attacchi "man in the middle".

Per convalidare i certificati con il codice dell'applicazione ADO.NET, impostare ``Encrypt=True`` e ``TrustServerCertificate=False`` nella stringa di connessione del database. Per ulteriori informazioni, vedere[Codice di esempio: logica di tentativi per la connessione al database SQL di Azure con ADO.NET](https://msdn.microsoft.com/library/azure/ee336243.aspx).

SQL Server Management Studio supporta inoltre la convalida dei certificati. Nella finestra di dialogo **Connetti al Server**, fare clic su **Crittografa connessione** sulla scheda **Proprietà di connessione**.

> [AZURE.NOTE]Nelle versioni precedenti a SQL Server 2008 R2, SQL Server Management Studio non supporta connessioni al Database SQL.

Benché SQLCMD supporti il Database SQL a partire da SQL Server 2008, esso non supporta la convalida dei certificati nelle versioni precedenti a SQL Server 2008 R2. Per convalidare i certificati con SQLCMD a partire da SQL Server 2008 R2, utilizzare ``-N`` l’opzione della riga di comando e non utilizzare l’opzione ``-C``. Utilizzando l'opzione -N, SQLCMD richiede una connessione crittografata. Se non si utilizza l’opzione ``-C``, SQLCMD non considera attendibile il certificato del server in modo implicito e è costretto a convalidare il certificato.

Per informazioni tecniche supplementari, vedere l’articolo [Sicurezza della connessione del Database SQL Azure](http://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#comment-4847) nel sito Wiki di TechNet.

## Autenticazione

L’autenticazione di Active Directory (sicurezza integrata) è disponibile come anteprima nel database V12 SQL. Per su come configurare l’autenticazione di Active Directory, vedere [Connettersi al Database SQL utilizzando l’autenticazione di Azure Active Directory](sql-database-aad-authentication.md). Quando non si utilizza l’anteprima, gli utenti devono specificare le credenziali (account di accesso e password) ogni volta che si connettono al database SQL. Per ulteriori informazioni sull'autenticazione di SQL Server, vedere [Scelta di una modalità di autenticazione](https://msdn.microsoft.com/library/ms144284.aspx) nella documentazione Online di SQL Server.

[SQL Database versione 12](sql-database-v12-whats-new.md) consente agli utenti di autenticarsi nel database utilizzando utenti del database indipendente. Per ulteriori informazioni, vedere [Utenti del database indipendente - rendere un database portabile](https://msdn.microsoft.com/library/ff929188.aspx), [CREA UTENTE (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx), e [Database indipendenti](https://technet.microsoft.com/library/ff929071.aspx).

> [AZURE.NOTE]Microsoft consiglia di utilizzare gli utenti del database indipendente per migliorare la scalabilità.

Il motore di Database chiude le connessioni che rimangono inattive per più di 30 minuti. La connessione deve accedere nuovamente prima di poter essere utilizzata.

Le connessioni continuamente attive al Database SQL richiedono la riautorizzazione (operazione eseguita dal motore di Database) almeno ogni 10 ore. Il motore di Database tenta la nuova autorizzazione utilizzando la password originariamente inviata ed non è necessario alcun input dell'utente. Per motivi di prestazioni quando si reimposta una password nel Database SQL, la connessione non viene nuovamente autenticata, anche se viene reimpostata a causa del pool di connessioni. Ciò differisce dal comportamento del Server SQL locale. Se la password è stata modificata poichè la connessione è stata inizialmente autorizzata, è necessario terminare la connessione e effettuarne una nuova utilizzando la nuova password. Un utente con l'autorizzazione KILL DATABASE CONNECTION può terminare in modo esplicito una connessione al Database SQL utilizzando il comando [KILL](https://msdn.microsoft.com/library/ms173730.aspx).

## Account di accesso e utenti

Quando si gestiscono gli account di accesso e gli utenti nel Database SQL, esistono alcune restrizioni.

Per l'account di accesso dell’entità di livello server, si applicano le restrizioni seguenti:

- L'utente del database nel database master corrispondente per l'account di accesso dell’entità di livello server non può essere modificato o eliminato. 
- Anche se l'account di accesso dell’entità di livello server non è un membro dei due ruoli del database **dbmanager** e **loginmanager** nel database **master**, dispone di tutte le autorizzazioni concesse a questi due ruoli.

> [AZURE.NOTE]Questo account di accesso viene creato durante il provisioning del server ed è simile all’account di accesso **sa** in un'istanza di SQL Server.

Per tutti gli account di accesso, si applicano le restrizioni seguenti:

- L’inglese americano è la lingua predefinita.
- Per l'accesso al database **master**, ogni account di accesso deve essere mappato a un account utente nel database**master**. Il database **master** non supporta utenti del database indipendente.
- Se non si specifica un database nella stringa di connessione, si verrà connessi al database **master** per impostazione predefinita.
- È necessario essere connessi al database **master** durante l'esecuzione delle istruzioni ``CREATE/ALTER/DROP LOGIN`` e ``CREATE/ALTER/DROP DATABASE``. 
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
- Solo l'accesso dell’entità di livello server e dei membri del ruolo del database **dbmanager** nel database **master** dispongono dell'autorizzazione per eseguire le istruzioni ``CREATE DATABASE`` e ``DROP DATABASE``.
- Solo l'accesso dell’entità di livello server e dei membri del ruolo del database **loginmanager** nel database **master** dispongono dell'autorizzazione per eseguire le istruzioni ``CREATE LOGIN``, ``ALTER LOGIN``, e ``DROP LOGIN``.
- Per ``CREATE/ALTER/DROP`` un utente richiede l’autorizzazione ``ALTER ANY USER`` per il database.
- Quando il proprietario di un ruolo del database tenta di aggiungere o rimuovere un altro utente del database in o da tale ruolo del database, potrebbe verificarsi il seguente errore: **L’utente o il ruolo 'Name' non esiste nel database.** Questo errore si verifica perché l'utente non è visibile al proprietario. Per risolvere questo problema, concedere al proprietario del ruolo l’autorizzazione``VIEW DEFINITION`` per l'utente. 

Per ulteriori informazioni su account di accesso e utenti, vedere [Gestione di database e account di accesso nel database SQL di Azure](sql-database-manage-logins.md).

## Procedure di sicurezza consigliate

Considerare i seguenti punti per rendere le applicazioni del Database SQL di Azure meno vulnerabili alle minacce alla sicurezza:

- Utilizzare sempre gli aggiornamenti più recenti: durante la connessione al Database SQL, utilizzare sempre la versione più aggiornata di strumenti e librerie per impedire la vulnerabilità di sicurezza.
- Bloccare le connessioni in ingresso sulla porta TCP 1433: sono necessarie solo le connessioni in uscita sulla porta TCP 1433 alle applicazioni per comunicare con il Database SQL. Se le comunicazioni in ingresso non sono necessarie ad altre applicazioni nel computer, assicurarsi che il firewall continuerà a bloccare le connessioni in ingresso sulla porta TCP 1433.
- Impedire la vulnerabilità di tipo injection: per assicurarsi che le applicazioni non dispongano di vulnerabilità di tipo injection di SQL, utilizzare query con parametri laddove possibile. Inoltre, assicurarsi di esaminare attentamente il codice ed eseguire un test di penetrazione prima di distribuire l'applicazione.


## Vedere anche

[Firewall del database SQL di Azure](sql-database-firewall-configure.md)

[Procedura: configurare le impostazioni del firewall (Database SQL di Azure)](sql-database-configure-firewall-settings.md)

[Gestione di database e account di accesso in database SQL di Azure](sql-database-manage-logins.md)

[Centro sicurezza per il motore di Database di SQL Server e il Database SQL di Azure](https://msdn.microsoft.com/library/bb510589)

<!---HONumber=AcomDC_1125_2015-->