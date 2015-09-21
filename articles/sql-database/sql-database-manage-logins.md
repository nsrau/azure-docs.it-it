<properties
   pageTitle="Gestione di database e account di accesso in database SQL di Azure | Microsoft Azure"
   description="Come usare l'entità di livello server e altri account per gestire gli account di accesso e i database nel database SQL."
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
   ms.date="09/08/2015"
   ms.author="rickbyh"/>

# Gestione di database e account di accesso in database SQL di Azure

Nel Database SQL di Microsoft Azure, quando si effettua l'iscrizione per il servizio, il processo di provisioning crea un server di Database SQL di Azure, un database denominato **master**, e un account di accesso che rappresenta l'entità di livello server di Database SQL Azure. Tale account di accesso è simile all'entità di livello server, **sa**, per un'istanza locale di SQL Server.

L'account dell’entità di livello server di Database SQL di Azure dispone sempre dell'autorizzazione per gestire la sicurezza a livello di server e a livello di database. In questo argomento viene descritto come utilizzare l'entità di livello server e altri account per gestire gli account di accesso e i database nel Database SQL.

> [AZURE.IMPORTANT]SQL Database versione 12 consente agli utenti di autenticarsi nel database utilizzando utenti del database indipendente. Gli utenti del database indipendente non richiedono account di accesso. Questo rende i database più portabili ma riduce la capacità dell'entità di livello server di controllare l'accesso al database. L’abilitazione degli utenti del database indipendente ha effetti importanti sulla sicurezza. Per altre informazioni, vedere [Utenti di database indipendente: rendere portabile un database](https://msdn.microsoft.com/library/ff929188.aspx), [Database indipendenti](https://technet.microsoft.com/library/ff929071.aspx) e [CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx).

## Panoramica dell'amministrazione della protezione del Database SQL

L’amministrazione della sicurezza nel Database SQL è simile all'amministrazione della sicurezza per un'istanza locale di SQL Server. La gestione della sicurezza a livello di database è pressoché identica, con differenze solo nei parametri disponibili. Poiché i database SQL possono occupare uno o più computer fisici, il Database SQL di Azure utilizza una strategia diversa per l'amministrazione a livello di server. La tabella seguente riepiloga il modo in cui l’amministrazione della sicurezza di un Server SQL locale differisce da quella nel Database di SQL Azure.

| Differenza | Server SQL locale | Database SQL di Azure |
|------------------------------------------------|-----------------------------------------------------------------------------|--------------------------------------------------|
| Dove si gestisce la sicurezza a livello di server | Nella cartella **Sicurezza** in Esplora oggetti di SQL Server Management Studio | Nel database **master** e tramite il portale di Azure |
| Ruolo di sicurezza a livello di server per la creazione di account di accesso | Ruolo del server predefinito **securityadmin** | Ruolo del database **loginmanager** nel database **master** |
| Comandi per la gestione degli account di accesso | CREA ACCOUNT DI ACCESSO, ALTER ACCOUNT DI ACCESSO, RIMUOVI ACCOUNT DI ACCESSO | CREATE LOGIN, ALTER LOGIN, DROP LOGIN (sono previste alcune limitazioni per i parametri ed è necessario essere connessi al database **master**). |
| Visualizzazione che mostra tutti gli accessi | sys.server\_principals | sys.sql\_logins (è necessario essere connessi al database **master**).|
| Ruolo a livello di server per la creazione di database | Ruolo predefinito del database **dbcreator** | Ruolo del database **dbmanager** nel database **master** |
| Comando per la creazione di un database | CREATE DATABASE | CREATE DATABASE (sono previste alcune limitazioni per i parametri ed è necessario essere connessi al database **master**). |
| Visualizzazione che elenca tutti i database | sys.databases | sys.databases (è necessario essere connessi al database **master**). |

## Amministrazione a livello di server e il database master

Il server di database SQL di Azure è un'astrazione che definisce un raggruppamento di database. I database associati al server di Database SQL di Azure possono risiedere in computer fisici separati presso il data center Microsoft. Usando un database singolo denominato **master**, eseguire l'amministrazione a livello di server per tutti i database.

Il database **master** tiene traccia degli account di accesso riconoscendo quelli che dispongono delle autorizzazioni per creare database o ulteriori account di accesso. È necessario essere connessi al database **master** ogni volta che si creano, modificano o eliminano account di accesso o database. Il database **master** include anche le viste ``sys.sql_logins`` e ``sys.databases`` che possono essere usate per visualizzare gli account di accesso e i database.

> [AZURE.NOTE]Il comando ``USE`` non è supportato per il passaggio da un database all'altro. È necessario stabilire invece una connessione diretta al database di destinazione.

È possibile gestire la sicurezza a livello di database per gli utenti e gli oggetti nel database SQL di Azure con la stessa procedura usata per un'istanza locale di SQL Server. Esistono differenze solo nei parametri disponibili per i comandi corrispondenti. Per altre informazioni, vedere [Linee guida e limitazioni per il database SQL di Azure](sql-database-security-guidelines.md).

## Gestione di utenti di database indipendente

Per creare il primo utente di database indipendente in un database, connettersi al database con l'entità di livello server. Usare le istruzioni ``CREATE USER``, ``ALTER USER`` o ``DROP USER``. Nell'esempio seguente viene creato un nuovo account di accesso denominato user1:

```
CREATE USER user1 WITH password='<Strong_Password>';
```

> [AZURE.NOTE]Quando si crea un account di accesso, è necessario usare una password complessa. Per ulteriori informazioni, vedere [Password complesse](https://msdn.microsoft.com/library/ms161962.aspx).

Qualsiasi utente con l'autorizzazione **ALTER ANY USER** può creare altri utenti di database indipendente.

Microsoft consiglia di usare gli utenti di database indipendente con il database SQL. Per altre informazioni, vedere [Utenti di database indipendente: rendere portabile un database](https://msdn.microsoft.com/library/ff929188.aspx).

## Gestione degli account di accesso

Gestire gli account di accesso tramite la connessione dell’account di accesso dell’entità di livello server al database master. È possibile usare le istruzioni ``CREATE LOGIN``, ``ALTER LOGIN`` o ``DROP LOGIN``. Nell'esempio seguente viene creato un nuovo account di accesso denominato **login1**:

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
```

> [AZURE.NOTE]Quando si crea un account di accesso, è necessario utilizzare una password complessa. Per ulteriori informazioni, vedere [Password complesse](https://msdn.microsoft.com/library/ms161962.aspx).

#### Utilizzo di nuovi account di accesso

Per connettersi al database SQL di Microsoft Azure usando gli account di accesso creati, è prima necessario concedere a ogni account di accesso le autorizzazioni a livello di database usando il comando ``CREATE USER``. Per altre informazioni, vedere [Concessione di autorizzazioni a livello di database a un account di accesso](https://msdn.microsoft.com/library/ee336235.aspx#DatabasePerms).

Poiché alcuni strumenti implementano il flusso TDS (Tabular Data Stream) in modo diverso, potrebbe essere necessario aggiungere il nome del server di database SQL di Azure all'account di accesso nella stringa di connessione usando la notazione ``<login>@<server>``. In questi casi, separare l'account di accesso e il nome del server di database SQL di Azure con il simbolo ``@``. Ad esempio, se l'account di accesso è stato denominato **login1** e il nome completo del server di database SQL di Azure è **servername.database.windows.net**, il parametro username della stringa di connessione dovrà essere: ****login1@servername**. Questa restrizione crea limitazioni sul testo da scegliere per il nome di accesso. Per altre informazioni, vedere [CREATE LOGIN (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx).

## Concessione di autorizzazioni a livello di server per un account di accesso

Per consentire agli account di accesso diversi dall'entità di livello server di gestire la sicurezza a livello di server, il database SQL di Microsoft Azure offre due ruoli di sicurezza: **loginmanager**, per la creazione di account di accesso e **dbmanager** per la creazione di database. Solo gli utenti nel database **master** possono essere aggiunti a tali ruoli del database.

> [AZURE.NOTE]Per creare account di accesso o database, è necessario essere connessi al database **master** (che è una rappresentazione logica di **master**).

### Ruolo loginmanager

Come il ruolo del server predefinito **securityadmin** per un'istanza locale di SQL Server, il ruolo del database **loginmanager** nel database SQL di Azure è autorizzato a creare gli account di accesso. Solo l'account di accesso dell'entità di livello server (creato dal processo di provisioning) o i membri del ruolo del database **loginmanager** possono creare nuovi account di accesso.

### Ruolo dbmanager

Il ruolo del database **dbmanager** del database SQL di Azure è simile al ruolo del server predefinito **dbcreator** per un'istanza locale di SQL Server. Solo l'account di accesso dell'entità di livello server (creato dal processo di provisioning) o i membri del ruolo del database **dbmanager** possono creare database. Dopo essere stato aggiunto ai membri del ruolo del database **dbmanager**, un utente può creare un database con il comando ``CREATE DATABASE`` del database SQL di Azure, ma tale comando deve essere eseguito nel database master. Per altre informazioni, vedere [CREATE DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/dn268335.aspx).

### Come assegnare ruoli a livello di server del Database SQL

Per creare un account di accesso e l'utente associato autorizzato a creare database o altri account di accesso, seguire questa procedura: 1. Connettersi al database **master** usando le credenziali dell'account di accesso dell'entità di livello server (creato dal processo di provisioning) o le credenziali di un membro esistente del ruolo del database**loginmanager**. 2. Creare un account di accesso usando il comando ``CREATE LOGIN``. Per altre informazioni, vedere [CREATE LOGIN (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx). 3. Creare un nuovo utente per l'account di accesso nel database master usando il comando ``CREATE USER``. Per altre informazioni, vedere [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx). 4. Usare la stored procedure ``sp_addrolememeber`` per aggiungere il nuovo utente al ruolo del database **dbmanager**, al ruolo del database loginmanager o a entrambi.

L'esempio di codice seguente illustra come creare un account di accesso denominato **login1** e un utente del database corrispondente denominato **login1User** autorizzato a creare database o altri account di accesso durante la connessione al database **master**:

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
CREATE USER login1User FROM LOGIN login1;
EXEC sp_addrolemember 'dbmanager', 'login1User';
EXEC sp_addrolemember 'loginmanager', 'login1User';
```

> [AZURE.NOTE]Quando si crea un account di accesso, è necessario utilizzare una password complessa. Per ulteriori informazioni, vedere [Password complesse](https://msdn.microsoft.com/library/ms161962.aspx).

## Concessione dell'accesso ai database a un account di accesso

Tutti gli account di accesso devono essere creati nel database **master**. Dopo aver creato un account di accesso, è possibile creare un account utente in un altro database per tale account di accesso. Il database SQL di Azure supporta anche i ruoli del database come avviene per le istanze locali di SQL Server.

Per creare un account utente in un altro database, presupponendo che non è stato creato un account di accesso o un database, eseguire i passaggi seguenti:

1. Connettersi al database **master** (con un account di accesso che abbia i ruoli **loginmanager** e **dbmanager**).
2. Creare un nuovo account di accesso usando il comando ``CREATE LOGIN``. Per altre informazioni, vedere [CREATE LOGIN (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx). L'autenticazione Windows non è supportata.
3. Creare un nuovo database usando il comando ``CREATE DATABASE``. Per altre informazioni, vedere [CREATE DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/dn268335.aspx).
4. Stabilire una connessione al nuovo database (con l'account di accesso che ha creato il database).
5. Creare un nuovo utente nel nuovo database usando il comando ``CREATE USER``. Per altre informazioni, vedere [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

L'esempio di codice seguente illustra come creare un account di accesso denominato **login1** e un database denominato **database1**:

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
CREATE DATABASE database1;
```

> [AZURE.NOTE]Quando si crea un account di accesso, è necessario utilizzare una password complessa. Per ulteriori informazioni, vedere [Password complesse](https://msdn.microsoft.com/library/ms161962.aspx).

L'esempio seguente illustra come creare un utente del database denominato **login1User** nel database **database1** che corrisponde all'account di accesso **login1**:

```
-- Establish a new connection to the database1 database
CREATE USER login1User FROM LOGIN login1;
```

Questo modello di autorizzazione a livello di database nel database SQL di Azure è uguale quello di un'istanza locale di SQL Server. Per informazioni, vedere gli argomenti seguenti nella documentazione di riferimento SQL Server Books Online.

- [Gestione di account di accesso, utenti e schemi delle procedure](https://msdn.microsoft.com/library/aa337552.aspx) 
- [Lezione 2: Configurazione delle autorizzazioni sugli oggetti di Database](https://msdn.microsoft.com/library/ms365345.aspx) 

> [AZURE.NOTE]Le istruzioni di Transact-SQL relative alla sicurezza nel database SQL di Azure possono presentare lievi differenti rispetto ai parametri disponibili. Per altre informazioni, vedere la sintassi per le istruzioni specifiche nella documentazione online.

## Visualizzazione di database e di account di accesso


Per visualizzare gli account di accesso e i database nel server di database SQL di Azure, usare rispettivamente le viste ``sys.sql_logins`` e ``sys.databases`` del database master. Nell'esempio seguente viene illustrato come visualizzare un elenco di tutti gli account di accesso e dei database sul server di Database SQL Azure.

```
-- first, connect to the master database
SELECT * FROM sys.sql_logins;
SELECT * FROM sys.databases; 
```

## Vedere anche

[Linee guida e limitazioni per la sicurezza per il database SQL di Azure](sql-database-security-guidelines.md)

<!---HONumber=Sept15_HO2-->