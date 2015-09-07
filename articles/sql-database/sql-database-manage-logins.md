<properties
   pageTitle="Gestione di database e account di accesso in database SQL di Azure | Microsoft Azure"
	description="Come utilizzare l'entità di livello server e altri account per gestire gli account di accesso e i database nel Database SQL."
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
	ms.date="08/20/2015"
	ms.author="rickbyh"/>

# Gestione di database e account di accesso in database SQL di Azure

Nel Database SQL di Microsoft Azure, quando si effettua l'iscrizione per il servizio, il processo di provisioning crea un server di Database SQL di Azure, un database denominato **master**, e un account di accesso che rappresenta l'entità di livello server di Database SQL Azure. Tale account di accesso è simile all’entità di livello server, **sa**, per un'istanza di SQL Server in situ.

L'account dell’entità di livello server di Database SQL di Azure dispone sempre dell'autorizzazione per gestire la sicurezza a livello di server e a livello di database. In questo argomento viene descritto come utilizzare l'entità di livello server e altri account per gestire gli account di accesso e i database nel Database SQL.

> [AZURE.IMPORTANT]SQL Database versione 12 consente agli utenti di autenticarsi nel database utilizzando utenti del database indipendente. Gli utenti del database indipendente non richiedono account di accesso. Questo rende i database più portabili ma riduce la capacità dell'entità di livello server di controllare l'accesso al database. L’abilitazione degli utenti del database indipendente ha effetti importanti sulla sicurezza. Per ulteriori informazioni, vedere [Utenti del database indipendente - rendere un database portabile](https://msdn.microsoft.com/library/ff929188.aspx), [CREA UTENTE (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx), e [Database indipendenti](https://msdn.microsoft.com/library/ff929071.aspx).

## Panoramica dell'amministrazione della protezione del Database SQL

L’amministrazione della sicurezza nel Database SQL è simile all'amministrazione della sicurezza per un'istanza locale di SQL Server. La gestione della sicurezza a livello di database è pressoché identica, con differenze solo nei parametri disponibili. Poiché i database SQL possono occupare uno o più computer fisici, il Database SQL di Azure utilizza una strategia diversa per l'amministrazione a livello di server. La tabella seguente riepiloga il modo in cui l’amministrazione della sicurezza di un Server SQL locale differisce da quella nel Database di SQL Azure.

| Differenza | Server SQL locale | Database SQL di Azure |
|------------------------------------------------|-----------------------------------------------------------------------------|--------------------------------------------------|
| Dove si gestisce la sicurezza a livello di server | La cartella Sicurezza in Esplora oggetti di SQL Server Management Studio | Il database master e tramite il portale di Azure |
| Ruolo di sicurezza a livello di server per la creazione di account di accesso | il ruolo del server predefinito securityadmin Per ulteriori informazioni, vedere Ruoli a livello di Server | ruolo del database loginmanager nel master |
| Comandi per la gestione degli account di accesso | CREA ACCOUNT DI ACCESSO, ALTER ACCOUNT DI ACCESSO, RIMUOVI ACCOUNT DI ACCESSO | CREA ACCOUNT DI ACCESSO, ALTER ACCOUNT DI ACCESSO, RIMUOVI ACCOUNT DI ACCESSO (esistono alcune limitazioni dei parametri ed è necessario essere connessi al database master) |
| Visualizzazione che mostra tutti gli accessi | sys.server\_principals | sys.sql\_logins (è necessario essere connessi al database master)|
| Ruolo a livello di server per la creazione di database | il ruolo del server predefinito dbcreator Per ulteriori informazioni, vedere Ruoli a livello di Server | ruolo del database dbmanager nel database master |
| Comando per la creazione di un database | CREATE DATABASE | CREA DATABASE (esistono alcune limitazioni dei parametri ed è necessario essere connessi al database master) |
| Visualizzazione che elenca tutti i database | sys.databases | sys.databases (è necessario essere connessi al database master) |

### Amministrazione a livello di server e il database master

Il server di database SQL di Azure è un'astrazione che definisce un raggruppamento di database. I database associati al server di Database SQL di Azure possono risiedere in computer fisici separati presso il data center Microsoft. Utilizzando un singolo database denominato master, eseguire l'amministrazione a livello di server per tutti i database.

Il database master tiene traccia degli account di accesso riconoscendo quelli che dispongono delle autorizzazioni per creare database o ulteriori account di accesso. È necessario essere connessi al database master ogni volta che si crea, modifica o elimina account di accesso o database. Il database **master** dispone anche delle visualizzazioni ``sys.sql_logins`` e ``sys.databases`` che possono essere utilizzate per visualizzare gli account di accesso e i database, rispettivamente.

> [AZURE.NOTE]Il comando ``USE`` non è supportato per il passaggio tra database. È necessario stabilire invece una connessione diretta al database di destinazione.

È possibile gestire la sicurezza a livello di database per gli utenti e gli oggetti di Database SQL di Microsoft Azure con la stessa procedura utilizzata per un'istanza locale di SQL Server. Esistono differenze solo nei parametri disponibili per i comandi corrispondenti. Per altre informazioni, vedere [Linee guida e limitazioni per il database SQL di Azure](https://msdn.microsoft.com/library/azure/ff394108.aspx).

### Gestione degli account di accesso

Gestire gli account di accesso tramite la connessione dell’account di accesso dell’entità di livello server al database master. È possibile utilizzare le istruzioni ``CREATE LOGIN``, ``ALTER LOGIN``, o ``DROP LOGIN``. Nell'esempio seguente viene creato un nuovo account di accesso denominato **login1**:

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
```

> [AZURE.NOTE]Quando si crea un account di accesso, è necessario utilizzare una password complessa. Per ulteriori informazioni, vedere [Password complesse](https://msdn.microsoft.com/library/ms161962.aspx).

#### Utilizzo di nuovi account di accesso

Per connettersi al Database SQL di Microsoft Azure utilizzando gli account di accesso creati, è innanzitutto necessario concedere a ogni account di accesso le autorizzazioni a livello di database utilizzando il comando ``CREATE USER``. Per ulteriori informazioni, vedere Concessione di autorizzazioni a livello di Database a un account di accesso.

Poiché alcuni strumenti implementano il flusso di dati tabulari (TDS) in modo diverso, potrebbe essere necessario aggiungere il nome del server del database SQL di Azure all'account di accesso nella stringa di connessione utilizzando la notazione ``<login>@<server>``. In questi casi, separare l'account di accesso e il nome del server del Database SQL Azure con il simbolo ``@``. Ad esempio, se l'account di accesso è stato denominato **login1** e il nome completo del server del Database SQL Azure è **servername.database.windows.net**, il parametro username della stringa di connessione deve essere: ****login1@servername**. Questa restrizione crea limitazioni sul testo da scegliere per il nome di accesso. Per altre informazioni, vedere [CREA ACCOUNT DI ACCESSO (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx).

## Concessione di autorizzazioni a livello di server per un account di accesso

Affinché gli account di accesso diversi dall’entità di livello server gestiscano la sicurezza a livello di server, il Database SQL di Microsoft Azure offre due ruoli di sicurezza: **loginmanager**, per la creazione di account di accesso e **dbmanager** per la creazione di database. Solo gli utenti nel database **master** possono essere aggiunti a tali ruoli del database.

> [AZURE.NOTE]Per creare account di accesso o database, è necessario essere connessi al database **master** (che è una rappresentazione logica di **master**).

### Ruolo loginmanager

Come il ruolo del server predefinito **securityadmin** per un'istanza locale di SQL Server, il ruolo del database**loginmanager** nel Database SQL di Microsoft Azure è autorizzato a creare account di accesso. Solo l'account di accesso dell’entità di livello server (creato dal processo di provisioning) o i membri del ruolo del database **loginmanager** possono creare nuovi account di accesso.

### Ruolo dbmanager

Il ruolo del database **dbmanager** del Database SQL di Microsoft Azure è simile al ruolo del server predefinito **dbcreator** per un'istanza locale di SQL Server. Solo l'account di accesso dell’entità di livello server (creato dal processo di provisioning) o i membri del ruolo del database **dbmanager** possono creare database. Dopo esser diventato membro del ruolo del database **dbmanager**, un utente può creare un database con il comando ``CREATE DATABASE`` del Database SQL di Azure, ma tale comando deve essere eseguito nel database master. Per ulteriori informazioni, vedere [CREA DATABASE (Transact-SQL di Server SQL)](https://msdn.microsoft.com/library/ms176061.aspx).

### Come assegnare ruoli a livello di server del Database SQL

Per creare un account di accesso e l'utente associato che può creare database o altri account di accesso, eseguire i passaggi seguenti:

1. Connettersi al database **master** utilizzando le credenziali dell'account di accesso dell’entità di livello server (creato dal processo di provisioning) o le credenziali di un membro esistente del ruolo del database**loginmanager**.
2. Creare un account di accesso utilizzando il comando ``CREATE LOGIN``. Per ulteriori informazioni, vedere [CREA ACCOUNT DI ACCESSO (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx).
3. Creare un nuovo utente per tale account di accesso nel database master utilizzando il comando ``CREATE USER``. Per ulteriori informazioni, vedere [CREA UTENTE (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).
4. Utilizzare la stored procedure ``sp_addrolememeber`` per aggiungere il nuovo utente al ruolo del database **dbmanager**, il ruolo del database loginmanager o entrambi.

Il seguente esempio di codice illustra come creare un account di accesso denominato **login1**, e un utente del database corrispondente denominato **login1User** in grado di creare database o altri accessi durante la connessione al database**master**:

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
CREATE USER login1User FROM LOGIN login1;
EXEC sp_addrolemember 'dbmanager', 'login1User';
EXEC sp_addrolemember 'loginmanager', 'login1User';
```

> [AZURE.NOTE]Quando si crea un account di accesso, è necessario utilizzare una password complessa. Per ulteriori informazioni, vedere [Password complesse](https://msdn.microsoft.com/library/ms161962.aspx).

## Concessione dell'accesso ai database a un account di accesso

Tutti gli account di accesso devono essere creati nel database master. Dopo aver creato un account di accesso, è possibile creare un account utente in un altro database per tale account di accesso. Il database SQL di Microsoft Azure supporta inoltre i ruoli del database nello stesso modo di un'istanza locale di SQL Server.

Per creare un account utente in un altro database, presupponendo che non è stato creato un account di accesso o un database, eseguire i passaggi seguenti:

1. Connettersi al database **master** (con un account di accesso che dispone dei ruoli **loginmanager** e **dbmanager**).
2. Creare un nuovo account di accesso utilizzando il comando ``CREATE LOGIN``. Per ulteriori informazioni, vedere [CREA ACCOUNT DI ACCESSO (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx). L'autenticazione Windows non è supportata.
3. Creare un nuovo database utilizzando il comando ``CREATE DATABASE``. Per ulteriori informazioni, vedere [CREA DATABASE (Transact-SQL di Server SQL)](https://msdn.microsoft.com/library/ms176061.aspx).
4. Stabilire una connessione al nuovo database (con l'account di accesso che ha creato il database).
5. Creare un nuovo utente nel nuovo database utilizzando il comando ``CREATE USER``. Per ulteriori informazioni, vedere [CREA UTENTE (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

Il seguente esempio di codice illustra come creare un account di accesso denominato **login1** e un database denominato **database1**:

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
CREATE DATABASE database1;
```

> [AZURE.NOTE]Quando si crea un account di accesso, è necessario utilizzare una password complessa. Per ulteriori informazioni, vedere [Password complesse](https://msdn.microsoft.com/library/ms161962.aspx).

Il seguente esempio illustra come creare un utente del database denominato **login1User** nel database **database1** che corrisponde all'account di accesso **login1**:

```
-- Establish a new connection to the database1 database
CREATE USER login1User FROM LOGIN login1;
```

Questo modello di autorizzazione a livello di database nel Database SQL di Microsoft Azure è uguale a un'istanza locale di SQL Server. Per informazioni, vedere gli argomenti seguenti nella documentazione di riferimento SQL Server Books Online.

- [Gestione di account di accesso, utenti e schemi delle procedure](https://msdn.microsoft.com/library/aa337552.aspx) 
- [Lezione 2: Configurazione delle autorizzazioni sugli oggetti di Database](https://msdn.microsoft.com/library/ms365345.aspx) 

> [AZURE.NOTE]Le istruzioni di Transact-SQL sulla sicurezza nel Database SQL di Microsoft Azure possono differire leggermente nei parametri disponibili. Per altre informazioni, vedere [Guida di riferimento per Transact-SQL del database SQL di Azure](sql-database-transact-sql-information.md).

## Visualizzazione di database e di account di accesso

Per visualizzare gli account di accesso e i database sul server di Database SQL Azure, utilizzare le visualizzazioni ``sys.sql_logins`` e ``sys.databases`` del database master, rispettivamente. Nell'esempio seguente viene illustrato come visualizzare un elenco di tutti gli account di accesso e dei database sul server di Database SQL Azure.

```
-- first, connect to the master database
SELECT * FROM sys.sql_logins;
SELECT * FROM sys.databases; 
```

## Vedere anche

[Linee guida e limitazioni per la sicurezza per il database SQL di Azure](sql-database-security-guidelines.md)

<!---HONumber=August15_HO9-->