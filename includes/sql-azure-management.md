
# Gestione di database SQL di Azure tramite SQL Server Management Studio 

Per amministrare le sottoscrizioni del database SQL nonché creare e gestire server e database logici associati, è possibile usare il portale di gestione database SQL di Azure oppure l'applicazione client di SQL Server Management Studio (SSMS). Le seguenti linee guida illustrano come usare Management Studio per gestire i database e i server logici di un database SQL.

>[AZURE.NOTE] Per gestire il database SQL di Azure, incluso l'ultimo aggiornamento del database SQL V12, è necessario usare SQL Server 2014 Management Studio e installare gli aggiornamenti più recenti (CU5 o versione successiva). È possibile usare anche SQL Server 2012 o la versione SQL Server 2008 R2 di SSMS. Le versioni precedenti non sono supportate. 

Questo argomento include le seguenti procedure:

-   [Passaggio 1: Ottenere SQL Server 2014 Management Studio][]
-   [Passaggio 2: Connettersi al database SQL][]
-   [Passaggio 3: Creare e gestire i database][]
-   [Passaggio 4: Creare e gestire gli account di accesso][]
-   [Passaggio 5: Monitorare il database SQL mediante le viste a gestione dinamica][]

<h2><a id="Step1" name="Step1"> </a>Passaggio 1: Ottenere SQL Server 2014 Management Studio</h2>

Management Studio è un ambiente integrato per la gestione dei database SQL. Per la gestione 
dei database in Azure, è possibile usare l'applicazione Management Studio installata con SQL Server o scaricare la versione gratuita di SQL Server 2014 Management Studio (SSMS). I seguenti passaggi descrivono come installare SSMS.

1.  Nella pagina [SQL Server 2014 Express][] scorrere verso il basso e selezionare **MgmtStudio 32BIT\SQLManagementStudio_x86_ENU.exe** se si esegue un sistema operativo a 32 bit oppure **MgmtStudio 64BIT\SQLManagementStudio_x64_ENU.exe** per un sistema operativo a 64 bit. Fare clic su **Avanti** e, quando richiesto, eseguire il programma di installazione

2.  Fare clic su **Nuova installazione autonoma di SQL Server o aggiunta di funzionalità a un'installazione esistente**, quindi su **OK**.

3.  Accettare le condizioni di licenza e fare clic su **Avanti**.

4. Fare clic su **Installa** per installare i file richiesti dal programma di installazione di SQL Server.

5.  Nella schermata **Selezione funzionalità** sono preselezionate le opzioni**Strumenti di gestione - Di base** e **Strumenti di gestione - Completa**. Fare clic su **Avanti**.

6.  Nella schermata **Segnalazione errori** è possibile scegliere di inviare le segnalazioni degli errori a Microsoft.

7.  Al termine dell'installazione verrà visualizzata la pagina **Operazione completata**. Fare clic su **Chiudi**. 

8. Installare gli aggiornamenti più recenti dalla pagina [Pacchetto di aggiornamento cumulativo 5 per SQL Server Management Studio 2014][Pacchetto di aggiornamento cumulativo 5 per SQL Server 2014].

<h2><a id="Step2" name="Step2"> </a>Passaggio 2: Connettersi al database SQL</h2>

Per la connessione al database SQL è necessario conoscere il nome del server in Azure. Potrebbe essere necessario accedere al portale per ottenere questa informazione.

1.  Accedere al [portale di gestione di Azure][].

2.  Nel riquadro sinistro fare clic su **Database SQL**.

3.  Nella home page Database SQL fare clic su **SERVER** nella parte superiore della pagina per elencare tutti i server associati alla sottoscrizione. Trovare il nome del server a cui si desidera connettersi e copiarlo negli Appunti.

	Configurare quindi il firewall del database SQL per consentire le connessioni dal computer locale. A tale scopo, aggiungere gli indirizzi IP dei computer locali all'elenco di eccezioni del firewall.

1.  Nella home page Database SQL fare clic su **SERVER**, quindi fare clic sul server a cui ci si vuole connettere.

2.  Fare clic su **Configura** nella parte superiore della pagina.

3.  Copiare l'indirizzo IP in INDIRIZZO IP CLIENT CORRENTE.

4.  Nell'area **Indirizzi IP consentiti** della pagina di configurazione sono presenti tre caselle in cui è possibile specificare un nome di regola e un intervallo di indirizzi IP come valori di inizio e fine. Come nome di regola, è possibile immettere il nome del computer in uso. Come valori di inizio e fine dell'intervallo, incollare gli indirizzi IP del computer in entrambe le caselle, quindi fare clic sulla casella di controllo che viene visualizzata.

	Il nome di regola deve essere univoco. Se il computer in uso è il computer di sviluppo, è possibile immetterne l'indirizzo IP sia nella casella di inizio intervallo IP che in quella di fine. In caso contrario, potrebbe essere necessario immettere un intervallo di indirizzi IP più ampio per ospitare connessioni da altri computer dell'organizzazione.
 
5. Fare clic su **SALVA** nella parte inferiore della pagina.

    **Nota:** Le modifiche alle impostazioni del firewall potrebbero impiegare fino a cinque minuti prima di avere effetto.

	È ora possibile effettuare la connessione al database SQL usando Management Studio.

1.  Sulla barra delle applicazioni fare clic su **Start**, scegliere **Tutti i programmi**, **Microsoft SQL Server 2014** e quindi fare clic su **SQL Server Management Studio**.

2.  In **Connetti al server**, specificare il nome completo del server come *serverName*.database.windows.net. In Azure, il nome del server è una stringa autogenerata composta da caratteri alfanumerici.

3.  Selezionare **Autenticazione di SQL Server**.

4.  Nella casella **Accesso** immettere l'account di accesso amministratore di SQL Server specificato nel portale durante la creazione del server.

5.  Nella casella **Password** immettere la password specificata nel nel portale durante la creazione del server.

8.  Fare clic su **Connetti** per stabilire la connessione.

SQL Server 2014 SSMS con gli aggiornamenti più recenti offre ampio supporto per attività come la creazione e la modifica di database SQL di Azure. Sarà possibile usare anche istruzioni Transact-SQL al fine di completare tali attività. Le seguenti procedure forniscono esempi di tali istruzioni. Per altre informazioni sull'uso di Transact-SQL con database SQL, compresi i dettagli sui comandi supportati, vedere [Guida di riferimento a Transact-SQL][Riferimento a Transact-SQL (database SQL)].

<h2><a id="Step3" name="Step3"> </a>Passaggio 3: Creare e gestire i database</h2>

Durante la connessione al database **master**, è possibile creare nuovi database nel server e modificare o eliminare i database esistenti. I seguenti passaggi descrivono come completare diverse attività comuni di gestione del database tramite Management Studio. Per eseguire queste attività, assicurarsi di essere connessi al database **master** con l'account di accesso dell'entità di livello server creato al momento della configurazione del server.

Per aprire una finestra Query in Management Studio, aprire la cartella Database, espandere la cartella **Database di sistema** fare clic con il pulsante destro del mouse su **master** e quindi fare clic su **Nuova query**.

-   Per creare un nuovo database, usare l'istruzione **CREATE DATABASE**. Per altre informazioni, vedere [CREATE DATABASE (database SQL)][]. La seguente istruzione consente di creare un nuovo database denominato **myTestDB** specificando che si tratta di un database Standard S0 Edition con una dimensione massima predefinita di 250 GB.

        CREATE DATABASE myTestDB
        (EDITION='Standard',
         SERVICE_OBJECTIVE='S0');

Fare clic su **Esegui** per eseguire la query.

-   Usare l'istruzione **ALTER DATABASE** per modificare un database esistente, ad esempio se si desidera modificare il nome e l'edizione del database. Per altre informazioni, vedere [ALTER DATABASE (database SQL)][]. La seguente istruzione consente di modificare il database creato nel precedente passaggio per modificare l'edizione in Standard S1.

        ALTER DATABASE myTestDB
        MODIFY
        (SERVICE_OBJECTIVE='S1');

-   Usare l'istruzione **DROP DATABASE** per eliminare un database esistente.
    Per altre informazioni, vedere [DROP DATABASE (database SQL)][]. La seguente istruzione consente di eliminare il database **myTestDB**, ma non immediatamente perché dovrà essere usato per creare account di accesso nel prossimo passaggio.

        DROP DATABASE myTestBase;

-   Il database master presenta la vista **sys.databases** che può essere usata per visualizzare i dettagli relativi a tutti i database. Per visualizzare tutti i database esistenti, eseguire la seguente istruzione:

        SELECT * FROM sys.databases;

-   Nel database SQL l'istruzione **USE** non è supportata per passare da un database a un altro. È necessario stabilire invece una connessione diretta al database di destinazione.

>[AZURE.NOTE] Molte delle istruzioni Transact-SQL che consentono di creare o modificare un database devono essere eseguite nell'ambito del proprio batch e non possono essere raggruppate insieme ad altre istruzioni Transact-SQL. Per altre informazioni, vedere i dettagli specifici di ogni istruzione forniti nei collegamenti elencati sopra.

<h2><a id="Step4" name="Step4"> </a>Passaggio 4: Creare e gestire gli account di accesso</h2>

Il database **master** tiene traccia degli account di accesso riconoscendo quelli che dispongono delle autorizzazioni per creare database o altri account di accesso. Per gestire gli account di accesso, connettersi al database **master** con l'account di accesso dell'entità di livello server creato al momento della configurazione del server. È possibile usare le istruzioni **CREATE LOGIN**, **ALTER LOGIN** o **DROP LOGIN** per eseguire query sul database master che gestirà gli account di accesso nell'intero server. Per altre informazioni, vedere [Gestione di database e account di accesso in database SQL][]. 


-   Usare l'istruzione **CREATE LOGIN** per creare un nuovo account di accesso di livello server. Per altre informazioni, vedere [CREATE LOGIN (database SQL)][]. La seguente istruzione consente di creare un nuovo account di accesso denominato **login1**. Sostituire **password1** con la password desiderata.

        CREATE LOGIN login1 WITH password='password1';

-   Per concedere autorizzazioni di livello database, usare l'istruzione **CREATE USER**. Tutti gli account di accesso devono essere creati nel database **master**, ma per connettere un account di accesso a un database diverso è necessario concedere a tale account autorizzazioni di livello database usando l'istruzione **CREATE USER** nel database. Per altre informazioni, vedere [CREATE USER (database SQL)][]. 

-   Per concedere a login1 le autorizzazioni per un database denominato **myTestDB**, seguire questa procedura:

 1.  Per aggiornare Esplora oggetti in modo da visualizzare il database **myTestDB** creato, fare clic con il pulsante destro del mouse sul nome del server in Esplora oggetti e quindi scegliere **Aggiorna**.

     Se si è chiusa la connessione, è possibile ristabilirla selezionando **Connetti Esplora oggetti** dal menu File. Ripetere le istruzioni nel [Passaggio 2: Connettersi al database SQL][] per connettersi al database.

 2. Fare clic con il pulsante destro del mouse sul database **myTestDB** e selezionare **Nuova query**.

    3.  Eseguire la seguente istruzione sul database the myTestDB per creare un utente database denominato **login1User** che corrisponde all'account di accesso a livello server **login1**.

            CREATE USER login1User FROM LOGIN login1;

-   Usare la stored procedure **sp\_addrolemember** per concedere all'account utente il livello appropriato di autorizzazioni per il database. Per altre informazioni, vedere [sp_addrolemember (Transact-SQL)][]. La seguente istruzione concede a **login1User** autorizzazioni di sola lettura del database mediante l'aggiunta di **login1User** al ruolo **db\_datareader**.

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   Usare l'istruzione **ALTER LOGIN** per modificare un account di accesso esistente, ad esempio se si desidera modificarne la password. Per altre informazioni, vedere [ALTER LOGIN (database SQL)][]. L'istruzione **ALTER LOGIN** deve essere eseguita sul database **master**. Tornare alla finestra Query relativa a tale database. 

    La seguente istruzione consente di modificare l'account di accesso **login1** per reimpostare la password.
    Sostituire **newPassword** con la password desiderata e **oldPassword** con la password attuale dell'account.

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   Per eliminare un account di accesso esistente, usare l'istruzione **DROP LOGIN**.
    L'eliminazione di un account di accesso a livello del server comporta anche l'eliminazione di eventuali account utente database associati. Per altre informazioni, vedere [DROP DATABASE (SQL Database)][DROP DATABASE (database SQL)]. L'istruzione **DROP LOGIN** deve essere eseguita sul database **master**. La seguente istruzione consente di eliminare l'account di accesso **login1**.

        DROP LOGIN login1;

-   Il database master presenta la vista **sys.sql\_logins** che può essere usata per visualizzare gli account di accesso. Per visualizzare tutti gli account di accesso esistenti, eseguire la seguente istruzione:

        SELECT * FROM sys.sql_logins;

<h2><a id="Step5" name="Step5"> </a>Passaggio 5: Monitorare il database SQL mediante le viste a gestione dinamica</h2>

Il database SQL supporta diverse viste a gestione dinamica che possono essere usate per il monitoraggio di database specifici. Di seguito vengono forniti alcuni esempi del tipo di dati di monitoraggio che possono essere recuperati tramite tali viste. Per informazioni dettagliate e altri esempi d'uso, vedere [Monitoraggio di database SQL di Azure mediante le viste a gestione dinamica][].

-   L'esecuzione di query in una vista a gestione dinamica richiede autorizzazioni **VIEW DATABASE STATE**. Per concedere le autorizzazioni **VIEW DATABASE STATE** a un utente database specifico, connettersi al database da gestire usando l'account di accesso dell'entità di livello server ed eseguire la seguente istruzione sul database:

        GRANT VIEW DATABASE STATE TO login1User;

-   Calcolare la dimensione del database usando la vista **sys.dm\_db\_partition\_stats**. La vista **sys.dm\_db\_partition\_stats** restituisce informazioni sulle pagine e sul numero di righe per ogni partizione del database, che possono essere usate per calcolare la dimensione del database. La seguente query restituisce la dimensione del database in megabyte:

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   Usare le viste **sys.dm\_exec\_connections** e **sys.dm\_exec\_sessions** per recuperare le informazioni sulle attuali connessioni degli utenti e sulle attività interne relative al database. La seguente query restituisce informazioni relative alla connessione corrente:

        SELECT
            e.connection_id,
            s.session_id,
            s.login_name,
            s.last_request_end_time,
            s.cpu_time
        FROM
            sys.dm_exec_sessions s
            INNER JOIN sys.dm_exec_connections e
              ON s.session_id = e.session_id;

-   Usare la vista **sys.dm\_exec\_query\_stats** per recuperare le statistiche aggregate sulle prestazioni per i piani di query nella cache. La seguente query restituisce informazioni sulle cinque principali query classificate per tempo medio CPU.

        SELECT TOP 5 query_stats.query_hash AS "Query Hash",
            SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
            MIN(query_stats.statement_text) AS "Statement Text"
        FROM
            (SELECT QS.*,
            SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
                    - QS.statement_start_offset)/2) + 1) AS statement_text
             FROM sys.dm_exec_query_stats AS QS
             CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
        GROUP BY query_stats.query_hash
        ORDER BY 2 DESC;

<h2>Risorse aggiuntive</h2>

* [Introduzione al database SQL][]
* [Gestione di database e account di accesso in database SQL][]
* [Monitoraggio di database SQL di Azure mediante le viste a gestione dinamica][]
* [Riferimento a Transact-SQL (database SQL)][]

  [Come usare il database SQL di Azure]: http://www.windowsazure.com/develop/net/how-to-guides/sql-azure/
  [Passaggio 1: Ottenere SQL Server 2014 Management Studio]: #Step1
  [Passaggio 2: Connettersi al database SQL]: #Step2
  [Passaggio 3: Creare e gestire i database]: #Step3
  [Passaggio 4: Creare e gestire gli account di accesso]: #Step4
  [Passaggio 5: Monitorare il database SQL mediante le viste a gestione dinamica]: #Step5
  [Microsoft SQL Server 2014 Express]: http://www.microsoft.com/download/details.aspx?id=42299
  [Pacchetto di aggiornamento cumulativo 5 per SQL Server 2014]: http://support2.microsoft.com/kb/3011055
  [Installazione di SSMS - Selezione del tipo di installazione]: /media/installer_installation_type.png
  [Installazione di SSMS - Selezione funzionalità]: /media/installer_feature_selection.png
  [Installazione di SSMS - Installazione completata]: /media/installer_completed.png
  [Portale di gestione di Azure]: http://manage.windowsazure.com/
  [Ottenere il nome del server di database SQL dal portale di gestione]: /media/portal_get_database_name.png
  [Connettersi a SSMS]: /media/ssms_connect.png
  [Connettesi a SSMS -- Proprietà]: /media/ssms_connect_properties.png
  [Riferimento a Transact-SQL (database SQL)]: http://msdn.microsoft.com/library/bb510741(v=sql.120).aspx
  [CREATE DATABASE (database SQL)]: https://msdn.microsoft.com/library/dn268335.aspx
  [ALTER DATABASE (database SQL)]: https://msdn.microsoft.com/library/ms174269.aspx
  [DROP DATABASE (database SQL)]: https://msdn.microsoft.com/library/ms178613.aspx
  [Gestione di database e account di accesso in database SQL]: http://msdn.microsoft.com/library/windowsazure/ee336235.aspx
  [CREATE LOGIN (database SQL)]: https://msdn.microsoft.com/library/ms189751.aspx
  [CREATE USER (database SQL)]: https://msdn.microsoft.com/library/ms173463.aspx
  [sp_addrolemember (Transact-SQL)]: http://msdn.microsoft.com/library/ms187750.aspx
  [ALTER LOGIN (database SQL)]: https://msdn.microsoft.com/library/ms189828.aspx
  [Monitoraggio di database SQL di Azure mediante le viste a gestione dinamica]: http://msdn.microsoft.com/library/windowsazure/ff394114.aspx
  [Introduzione al database SQL]: http://azure.microsoft.com/services/sql-database/
 

<!--HONumber=47-->
