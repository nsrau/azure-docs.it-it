<properties umbracoNaviHide="0" pageTitle="How to Manage SQL Database" metaKeywords="Azure SQL database, SQL database, manage sql database, add logins, connect to sql database" description="Learn how to manage Azure SQL database." linkid="devnav-manage-services-cloud-services" urlDisplayName="Cloud Services" headerExpose="" footerExpose="" disqusComments="1" title="How to Manage SQL Database" authors="jeffreyg" manager="jeffreyg" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jeffreyg" />

# <span id="swap"></span></a>Come gestire un database SQL

Questo argomento descrive come eseguire semplici attività di gestione in un database SQL di Azure.

## Sommario

-   [Procedura: Connettersi al database SQL in Azure mediante Management Studio][Procedura: Connettersi al database SQL in Azure mediante Management Studio]
-   [Procedura: Aggiungere account di accesso e utenti al database SQL in Azure][Procedura: Aggiungere account di accesso e utenti al database SQL in Azure]

## <span id="connect"></span></a>Procedura: Connettersi al database SQL in Azure mediante Management Studio

Management Studio è uno strumento di amministrazione che consente di gestire più istanze e server di SQL Server in un'unica area di lavoro. Se si dispone già di un'istanza locale di SQL Server, sarà possibile aprire una connessione sia all'istanza locale che a un server logico su Azure per eseguire le attività affiancate.

Management Studio include funzionalità non attualmente disponibili nel portale di gestione, ad esempio un correttore sintattico e la possibilità di salvare script e query denominate in modo da poterli riutilizzare. Il database SQL è semplicemente un endpoint di flusso TDS (Tabular Data Stream). Tutti gli strumenti utilizzabili con il flusso TDS, incluso Management Studio, possono essere utilizzati anche per operazioni del database SQL. Gli script sviluppati per il server locale verranno eseguiti in un server logico di database SQL.

Nel passaggio seguente verrà usato Management Studio per la connessione a un server logico su Azure. Per questo passaggio è necessario disporre di SQL Server Management Studio versione 2008 R2 o 2012. Per informazioni sul download o sulla connessione a Management Studio, vedere [Gestione del database SQL mediante Management Studio][Gestione del database SQL mediante Management Studio] in questo sito.

Prima di potere effettuare la connessione, è a volte necessario creare un'eccezione del firewall per consentire richieste in uscita sulla porta 1433 nel sistema locale. La porta 1433 non è in genere aperta nei computer protetti per impostazione predefinita.

## Configurazione del firewall per un server locale

1.  In Windows Firewall con protezione avanzata creare una nuova regola in uscita.

2.  Scegliere **Porta**, specificare TCP 1433, quindi **Consenti la connessione** e verificare che sia stato selezionato il profilo **Pubblico**.

3.  Specificare un nome significativo, ad esempio *WindowsAzureSQLDatabase (tcp-out) porta 1433*.

## Connessione a un server logico

1.  In Management Studio verificare in Connetti al server che Motore di database sia selezionato, quindi immettere il nome del server logico con il formato seguente: *nomeserver*.database.widnows.net

    È inoltre possibile ottenere il nome completo del server nel portale di gestione, nel dashboard del server e in MANAGE URL.

2.  In Autenticazione scegliere **Autenticazione di SQL Server** e quindi immettere l'account di accesso di amministratore creato durante la configurazione del server logico.

3.  Fare clic su **Opzioni**.

4.  In Connetti al database specificare **master**.

## Connessione a un server locale

1.  In Management Studio verificare in Connetti al server che Motore di database sia selezionato, quindi immettere il nome di un'istanza locale con il formato seguente: *nomeserver*\\*nomeistanza*. Se il server è locale e l'istanza è predefinita, immettere *localhost*.

2.  In Autenticazione scegliere **Autenticazione di Windows** e quindi immettere un account Windows appartenente al ruolo sysadmin.

## <span id="addlogins"></span></a>Procedura: Aggiungere account di accesso e utenti al database SQL in Azure

Dopo la distribuzione di un database, sarà necessario configurare gli account di accesso e assegnare le autorizzazioni. Nel passaggio successivo verranno eseguiti due script.

Per il primo script verrà effettuata la connessione al database master e verrà eseguito uno script per la creazione di account di accesso. Tali account verranno utilizzati per supportare operazioni di lettura e scrittura e per delegare attività operative, ad esempio la possibilità di eseguire query di sistema senza autorizzazioni 'sa'.

È necessario che gli account di accesso creati siano account di accesso con autenticazione di SQL Server. Se sono già disponibili script predefiniti che utilizzano identità utente di Windows o identità basate sulle attestazioni, tale script non funzionerà nel database SQL.

Il secondo script consente di assegnare autorizzazioni utente database. Per questo script verrà effettuata la connessione a un database già caricato in Azure.

## Creazione di account di accesso

1.  In Management Studio connettersi a un server logico in Azure, espandere la cartella Databases, fare clic con il pulsante destro del mouse su **master** e quindi scegliere **Nuova query**.

2.  Usare le istruzioni Transact-SQL seguenti per creare account di accesso. Sostituire il valore di password con una password valida. Selezionare ogni account di accesso individualmente e quindi fare clic su **Esegui**. Ripetere per gli account di accesso rimanenti.

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- run on master, execute each line separately
    -- use this login to manage other logins on this server
    CREATE LOGIN sqladmin WITH password='&lt;ProvidePassword&gt;'; 
    CREATE USER sqladmin FROM LOGIN sqladmin;
    EXEC sp_addrolemember 'loginmanager', 'sqladmin';

    -- use this login to create or copy a database
    CREATE LOGIN sqlops WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlops FROM LOGIN sqlops;
    EXEC sp_addrolemember 'dbmanager', 'sqlops';
</pre></div>

## Creazione di utenti database

1.  Espandere la cartella Databases, fare clic con il pulsante destro del mouse su **school** e quindi scegliere **Nuova query**.

2.  Usare le istruzioni Transact-SQL seguenti per aggiungere utenti database. Sostituire il valore di password con una password valida.

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- run on a regular database, execute each line separately
    -- use this login for read operations
    CREATE LOGIN sqlreader WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlreader FROM LOGIN sqlreader;
    EXEC sp_addrolemember 'db_datareader', 'sqlreader';

    -- use this login for write operations
    CREATE LOGIN sqlwriter WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlwriter FROM LOGIN sqlwriter;
    EXEC sp_addrolemember 'db_datawriter', 'sqlwriter';

    -- grant DMV permissions on the school database to 'sqlops'
    GRANT VIEW DATABASE STATE to 'sqlops';
</pre></div>

## Visualizzazione e test degli account di accesso

1.  In una nuova finestra Query effettuare la connessione a **master** ed eseguire l'istruzione seguente:

        SELECT * from sys.sql_logins;

2.  In Management Studio fare clic con il pulsante destro del mouse sul database **school** e quindi scegliere **Nuova query**.

3.  Dal menu Query scegliere **Connessione** e quindi fare clic su **Cambia connessione**.

4.  Accedere come *sqlreader*.

5.  Copiare l'istruzione seguente e provare a eseguirla. Dovrebbe essere visualizzato un errore che indica che l'oggetto non esiste.

        INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
        VALUES (1061, 30, 9);

6.  Aprire una seconda finestra Query e cambiare il contesto di connessione in *sqlwriter*. Dovrebbe essere ora possibile eseguire correttamente la stessa query.

Sono stati ora creati e testati diversi account di accesso. Per altre informazioni, vedere [Gestione di database e account di accesso in database SQL][Gestione di database e account di accesso in database SQL] e [Monitoraggio di database SQL mediante le viste a gestione dinamica][Monitoraggio di database SQL mediante le viste a gestione dinamica].

  [Procedura: Connettersi al database SQL in Azure mediante Management Studio]: #connect
  [Procedura: Aggiungere account di accesso e utenti al database SQL in Azure]: #addlogins
  [Gestione del database SQL mediante Management Studio]: http://www.windowsazure.com/it-it/develop/net/common-tasks/sql-azure-management/
  [Gestione di database e account di accesso in database SQL]: http://msdn.microsoft.com/it-it/library/windowsazure/ee336235.aspx
  [Monitoraggio di database SQL mediante le viste a gestione dinamica]: http://msdn.microsoft.com/it-it/library/windowsazure/ff394114.aspx
