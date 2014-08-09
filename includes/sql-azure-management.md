
# Gestione di database SQL di Azure tramite SQL Server Management Studio

Per amministrare le sottoscrizioni del database SQL nonché creare e gestire server e database logici associati, è possibile utilizzare il portale di gestione database SQL di Azure oppure l'applicazione client di SQL Server Management Studio (SSMS). Nelle linee guida seguenti viene illustrato come utilizzare Management Studio per gestire i database e i server logici di un database SQL. Per informazioni su come utilizzare connessioni al database SQL nel codice dell'applicazione, vedere [Come utilizzare un database SQL di Azure][1].

 
<div  class="dev-callout-new-collapsed">
<strong>Nota <span>Fare clic per comprimere</span></strong>
<div  class="dev-callout-content">
<p>È possibile utilizzare sia SQL Server 2012 o la versione di Management Studio di SQL Server 2008 R2. Le versioni precedenti non sono supportate.</p>
</div>

 Questa attività include i passaggi seguenti:

* [Passaggio 1: Ottenere SQL Server Management Studio](#Step1)
* [Passaggio 2: Connettersi al database SQL](#Step2)
* [Passaggio 3: Creare e gestire i database](#Step3)
* [Passaggio 4: Creare e gestire gli accessi](#Step4)
* [Passaggio 5: Monitorare il database SQL utilizzando viste a gestione dinamica][]

<h2><a id="Step1" name="Step1"> </a>Passaggio 1: Ottenere Management Studio</h2>


Management Studio è un ambiente integrato per la gestione dei database SQL. Per la gestione dei database in Azure, è possibile utilizzare l'applicazione Management Studio installata con SQL Server oppure scaricare la versione gratuita di SQL Server 2012 Management Studio Express (SSMSE). Nei passaggi seguenti viene descritto come eseguire l'installazione di SSMSE.

1.  Nella pagina [Microsoft SQL Server 2012 Express][], selezionare
    la versione x86 di Management Studio se si esegue un sistema
    operativo a 32 bit, oppure la versione x64 per un sistema operativo
    a 64 bit. Fare clic su **Download** e, quando richiesto, eseguire il
    programma di installazione.

2.  Fare clic su **New SQL Server stand-alone installation or add
    features to an existing installation**, quindi su **OK**.

3.  Accettare le condizioni di licenza e fare clic su **OK**.

4.  Fare clic su **Install** per installare i file richiesti dal
    programma di installazione di SQL Server.

5.  Nella schermata **Feature Selection** è preselezionata l'opzione
    **Management Tools - Basic**. Questo perché si sta eseguendo il
    programma di installazione per Management Studio. Se si sta
    eseguendo l'installazione completa di SQL Server Express, scegliere
    l'opzione **Management Tools - Basic** e fare clic su **Next**.

6.  Nella schermata **Error Reporting**, se lo si desidera, è possibile
    scegliere di inviare le segnalazioni degli errori a Microsoft.
    Questa scelta non è obbligatoria per poter utilizzare SSMSE. Fare
    clic su **Next** per avviare l'installazione.

7.  Al termine dell'installazione verrà visualizzata la **pagina di
    completamento**. Fare clic su **Close**.

<h2><a id="Step2" name="Step2"> </a>Passaggio 2: Connettersi al database SQL</h2>


Per la connessione al database SQL è necessario conoscere il nome del server in Azure. Potrebbe essere necessario accedere al portale per ottenere questa informazione.

1.  Accedere al [portale di gestione di Azure][].

2.  Nel riquadro sinistro, fare clic su **SQL Databases**.

3.  Nella home page dei database SQL fare clic su **SERVERS** nella
    parte superiore della pagina per elencare tutti i server associati
    alla sottoscrizione. Trovare il nome del server a cui si desidera
    connettersi e copiarlo negli Appunti.
    
    Configurare al firewall del database SQL in modo da consentire le
    connessioni dal computer locale. A tale scopo, aggiungere gli
    indirizzi IP dei computer locali all'elenco di eccezioni del
    firewall.

4.  Nella home page dei database SQL fare clic su **SERVERS**, quindi
    fare clic sul server a cui si desidera connettersi.

5.  Fare clic su **Configure** nella parte superiore della pagina.

6.  Copiare l'indirizzo IP in CURRENT CLIENT IP ADDRESS.

7.  Nell'area **Allowed IP Addresses** della pagina di configurazione,
    sono presenti tre caselle in cui è possibile specificare un nome di
    regola e un intervallo di indirizzi IP come valori di inizio e fine.
    Come nome di regola, è possibile immettere il nome del computer in
    uso. Come valori di inizio e fine dell'intervallo, incollare gli
    indirizzi IP del computer in entrambe le caselle, quindi fare clic
    sulla casella di controllo che viene visualizzata.
    
    Il nome di regola deve essere univoco. Se il computer in uso è il
    computer di sviluppo, è possibile immetterne l'indirizzo IP sia
    nella casella di inizio intervallo IP che in quella di fine. In caso
    contrario, potrebbe essere necessario immettere un intervallo di
    indirizzi IP più ampio per ospitare connessioni da altri computer
    dell'organizzazione.

8.  Fare clic su **SAVE** nella parte inferiore della pagina.
    
    **Nota:** le modifiche alle impostazioni del firewall potrebbero
    impiegare fino a cinque minuti prima di avere effetto.
    
    È ora possibile effettuare la connessione al database SQL
    utilizzando Management Studio.

9.  Nella barra delle applicazioni fare clic su **Start**, scegliere
    **Tutti i programmi**, **Microsoft SQL Server 2012**, quindi fare
    clic su **SQL Server Management Studio**.

10. In **Connetti al server** specificare il nome completo del server
    nel formato *nomeServer*.database.windows.net. In Azure il nome del
    server è una stringa generata automaticamente e formata da caratteri
    alfanumerici.

11. Selezionare **Autenticazione di SQL Server**.

12. Nella casella **Accesso** immettere l'account di accesso
    amministratore di SQL Server specificato nel portale durante la
    creazione del server, nel formato *account*@*yourServerName*.

13. Nella casella **Password** immettere la password specificata nel
    portale durante la creazione del server.

14. Fare clic su **Connetti** per stabilire la connessione.

In Azure, ogni server logico di database SQL è un'astrazione che definisce un raggruppamento di database. L'ubicazione fisica di ciascun database potrebbe essere in qualsiasi computer nel data center.

Nelle versioni precedenti è necessario connettersi direttamente a
**master** quando si imposta la connessione in Management Studio. Questo
passaggio non è più necessario. La riuscita delle connessioni dipenderà dal nome del server, dal tipo di autenticazione e dalle credenziali di amministratore.

Molte delle procedure guidate di SSMS per attività come la creazione e la modifica di database e di account di accesso in un database SQL Server non sono disponibili per i database SQL in Azure, pertanto, al fine di completare tali attività, sarà necessario utilizzare istruzioni Transact-SQL. Nei passaggi successivi vengono forniti esempi di tali istruzioni. Per ulteriori informazioni sull'utilizzo di Transact-SQL con database SQL, compresi i dettagli sui comandi supportati, vedere
[Riferimento a Transact-SQL (database SQL)][].

<h2><a id="Step3" name="Step3"> </a>Passaggio 3: Creare e gestire i database</h2>


Durante la connessione al database **master**, è possibile creare nuovi database nel server e modificare o eliminare database esistenti. Nei passaggi seguenti viene descritto come completare diverse attività comuni di gestione del database tramite Management Studio. Per eseguire queste attività, assicurarsi di essere connessi al database **master** con l'account di accesso dell'entità di livello server creato al momento della configurazione del server.

Per aprire una finestra Query in Management Studio, aprire la cartella Database, fare clic con il pulsante destro del mouse su **master**, quindi scegliere **New Query**.

Fare clic su **Execute** per eseguire la query.

* Per creare un nuovo database, utilizzare l'istruzione **CREATE DATABASE**. Per ulteriori informazioni, vedere [CREATE DATABASE (SQL Database)][]. L'istruzione seguente consente di creare un
  nuovo database denominato **myTestDB** specificando che si tratta di
  un database Web Edition con una dimensione massima di 1 GB.
  
      	CREATE DATABASE myTestDB
      	(MAXSIZE=1GB,
       	EDITION='web');

* Per modificare un database esistente, ad esempio se si desidera
  modificarne il nome, la dimensione massima o l'edizione (Business o
  Web), utilizzare l'istruzione **ALTER DATABASE**. Per ulteriori
  informazioni, vedere [ALTER DATABASE (database SQL)][].
  L'istruzione seguente consente di modificare il database creato nel
  passaggio precedente per impostarne la dimensione massima su 5 GB.
  
      	ALTER DATABASE myTestDB
      	MODIFY
      	(MAXSIZE=5GB,
       	EDITION='web');

* Per eliminare un database esistente, utilizzare l'istruzione **DROP
  DATABASE**. Per ulteriori informazioni, vedere [DROP DATABASE (database SQL)][]. L'istruzione seguente consente di eliminare il database
  **myTestDB**, ma non eliminarlo ora perché sarà necessario utilizzarlo
  per creare account di accesso nel prossimo passaggio.
  
      	DROP DATABASE myTestBase;

* Il database master presenta la vista **sys.databases** che può essere
  utilizzata per visualizzare i dettagli di tutti i database. Per
  visualizzare tutti i database esistenti, eseguire l'istruzione
  seguente:
  
      	SELECT * FROM sys.databases;

* Nel database SQL l'istruzione **USE** non è supportata per passare da
  un database a un altro. È necessario stabilire invece una connessione
  diretta al database di destinazione.

 
<div  class="dev-callout-new">
 <strong>Nota <span>Fare clic per comprimere</span></strong>
 <div  class="dev-callout-content">
   <p>Molte delle istruzioni Transact-SQL che consentono di creare o modificare un database devono essere eseguite nell'ambito del proprio batch e non possono essere raggruppate insieme ad altre istruzioni Transact-SQL. Per ulteriori informazioni, vedere i dettagli specifici di ogni istruzione forniti nei collegamenti elencati sopra.</p>
</div>

 <h2><a id="Step4" name="Step4"> </a>Passaggio 4: Creare e gestire gli accessi</h2>


Il database master tiene traccia dei login di accesso riconoscendo quelli che dispongono delle autorizzazioni per creare database o ulteriori account di accesso. Per gestire gli account di accesso, connettersi al database **master** con l'account di accesso dell'entità di livello server creato al momento della configurazione del server. È possibile utilizzare le istruzioni **CREATE LOGIN**,
**ALTER LOGIN** o **DROP LOGIN** per eseguire query sul database master
che gestirà gli account di accesso per l'intero server. Per ulteriori informazioni, vedere [Gestione di database e account di accesso in database SQL][].

* Utilizzare l'istruzione **CREATE LOGIN** per creare un nuovo account
  di accesso di livello server. Per ulteriori informazioni, vedere
  [CREATE LOGIN (database SQL)][]. L'istruzione seguente consente
  di creare un nuovo account di accesso denominato **login1**.
  Sostituire **password1** con la password desiderata.
  
      	CREATE LOGIN login1 WITH password='password1';

* Per concedere autorizzazioni di livello database, utilizzare
  l'istruzione **CREATE USER**. Tutti gli account di accesso devono
  essere creati nel database **master** ma per connettere un account di
  accesso a un database diverso, è necessario concedere a tale account
  autorizzazioni di livello database utilizzando l'istruzione **CREATE USER** nel database a cui si desidera connetterlo. Per ulteriori
  informazioni, vedere [CREATE USER (database SQL)][].

* Per concedere a login1 le autorizzazioni per un database denominato
  **myTestDB**, eseguire i passaggi seguenti:
  
  1.  Aggiornare Esplora oggetti per visualizzare il database
      **myTestDB** appena creato, visualizzato sotto la cartella
      **System Databases** che contiene **master**.
  
  	Se si è chiusa la connessione, è possibile ristabilirla selezionando
  	**Connect Object Explorer** dal menu File. Ripetere le istruzioni in
  	[Passaggio 2: Connettersi al database SQL](#Step2) per connettersi al
  	database.
  
  1.  Fare clic con il pulsante destro del mouse sul database
      **myTestDB** e selezionare **New Query**.
  
  2.  Eseguire l'istruzione seguente sul database myTestDB per creare
      un utente database denominato **login1User** che corrisponde
      all'account di accesso a livello di server **login1**.
      
          CREATE USER login1User FROM LOGIN login1;

* Utilizzare la stored procedure **sp\_addrolemember** per concedere
  all'account utente il livello appropriato di autorizzazioni per il
  database. Per ulteriori informazioni, vedere [sp\_addrolemember (Transact-SQL)][].
  L'istruzione seguente concede a **login1User**
  autorizzazioni di sola lettura del database mediante l'aggiunta di
  **login1User** al ruolo **db\_datareader**.
  
      	exec sp_addrolemember 'db_datareader', 'login1User';    

* Per modificare un account di accesso esistente, ad esempio se si
  desidera modificarne la password, utilizzare l'istruzione **ALTER
  LOGIN**. Per ulteriori informazioni, vedere [ALTER LOGIN (database SQL)][]. L'istruzione **ALTER LOGIN** deve essere eseguita sul
  database **master**. Tornare alla finestra Query relativa a tale
  database.
  
 	 L'istruzione seguente consente di modificare l'account di accesso
 	 **login1** per reimpostare la password. Sostituire **newPassword** con
	  la password desiderata e **oldPassword** con la password attuale
	  dell'account.
  
      	ALTER LOGIN login1
      	WITH PASSWORD = 'newPassword'
      	OLD_PASSWORD = 'oldPassword';

* Per eliminare un account di accesso esistente, utilizzare
  l'istruzione **DROP LOGIN**. L'eliminazione di un account di accesso
  a livello del server comporta anche l'eliminazione di eventuali
  account utente database associati. Per ulteriori informazioni, vedere [DROP DATABASE (database SQL)][]. L'istruzione **DROP LOGIN** deve essere eseguita sul database **master**. L'istruzione seguente consente di eliminare l'account di accesso **login1**.
  
      	DROP LOGIN login1;

* Il database master presenta la vista **sys.sql\_logins** che può
  essere utilizzata per visualizzare gli account di accesso. Per
  visualizzare tutti gli account di accesso esistenti, eseguire
  l'istruzione seguente:
  
      	SELECT * FROM sys.sql_logins;

<h2><a id="Step5" name="Step5"> </a>Passaggio 5: Monitorare il database SQL utilizzando viste a gestione dinamica</h2>


Il database SQL supporta diverse viste a gestione dinamica che possono essere utilizzate per il monitoraggio di database specifici. Di seguito vengono forniti alcuni esempi del tipo di dati di monitoraggio che possono essere recuperati tramite tali viste. Per informazioni dettagliate e altri esempi di utilizzo, vedere [Monitoraggio di database SQL mediante le viste a gestione dinamica][].

* L'esecuzione di query in una vista a gestione dinamica richiede
  autorizzazioni **VIEW DATABASE STATE**. Per concedere le
  autorizzazioni **VIEW DATABASE STATE** a un utente database specifico,
  connettersi al database che si desidera gestire utilizzando l'account
  di accesso dell'entità di livello server ed eseguire l'istruzione
  seguente sul database:
  
      	GRANT VIEW DATABASE STATE TO login1User;

* Utilizzare la vista **sys.dm\_db\_partition\_stats** per calcolare la
  dimensione del database. La vista **sys.dm\_db\_partition\_stats**
  restituisce informazioni sulle pagine e sul numero di righe per ogni
  partizione del database, che possono essere utilizzate per calcolare
  la dimensione del database. La query seguente restituisce la
  dimensione del database in megabyte:
  
      	SELECT SUM(reserved_page_count)*8.0/1024
      	FROM sys.dm_db_partition_stats;   

* Utilizzare le viste **sys.dm\_exec\_connections** e
  **sys.dm\_exec\_sessions** per recuperare informazioni sulle attuali
  connessioni degli utenti e sulle attività interne relative al
  database. La query seguente restituisce informazioni relative alla
  connessione corrente:
  
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

* Utilizzare la vista **sys.dm\_exec\_query\_stats** per recuperare
  statistiche aggregate sulle prestazioni per piani di query nella
  cache. La query seguente restituisce informazioni sulle cinque
  principali query classificate per tempo medio CPU.
  
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
* [Monitoraggio di database SQL mediante le viste a gestione dinamica][]
* [Modello di provisioning di database SQL][]
* [Aggiunta di utenti al database SQL di Azure][]
* [Riferimento a Transact-SQL (database SQL)][]
  
  [Passaggio 5: Monitorare il database SQL utilizzando viste a gestione dinamica]: #Step5
  [Microsoft SQL Server 2012 Express]:http://www.microsoft.com/it-it/download/details.aspx?id=29062
  [Installazione di SSMS - Selezione tipo di installazione]: /media/installer_installation_type.png
  [Installazione di SSMS  Selezione funzionalità]: /media/installer_feature_selection.png
  [Installazione di SSMS - Installazione completata]: /media/installer_completed.png
  [Portale di gestione di Azure]: http://manage.windowsazure.com/
  [Recupero del nome del server database SQL dal portale di gestione]: /media/portal_get_database_name.png
  [Connessione a SSMS]: /media/ssms_connect.png
  [Connessione a SSMS - proprietà]: /media/ssms_connect_properties.png
  [Riferimento a Transact-SQL (database SQL)]: http://msdn.microsoft.com/it-it/library/windowsazure/ee336281.aspx
  [CREATE DATABASE (SQL Database)]: http://msdn.microsoft.com/it-it/library/windowsazure/ee336274.aspx
  [ALTER DATABASE (database SQL)]: http://msdn.microsoft.com/it-it/library/windowsazure/ff394109.aspx
  [DROP DATABASE (database SQL)]: http://msdn.microsoft.com/it-it/library/windowsazure/ee336259.aspx
  [Gestione di database e account di accesso in database SQL]: http://msdn.microsoft.com/it-it/library/windowsazure/ee336235.aspx
  [CREATE LOGIN (database SQL)]: http://msdn.microsoft.com/it-it/library/windowsazure/ee336268.aspx
  [CREATE USER (database SQL)]: http://msdn.microsoft.com/it-it/library/ee336277.aspx
  [sp_addrolemember (Transact-SQL)]: http://msdn.microsoft.com/it-it/library/ms187750.aspx
  [ALTER LOGIN (database SQL)]: http://msdn.microsoft.com/it-it/library/windowsazure/ee336254.aspx
  [Monitoraggio di database SQL mediante le viste a gestione dinamica]: http://msdn.microsoft.com/it-it/library/windowsazure/ff394114.aspx
  [Introduzione al database SQL]: http://msdn.microsoft.com/it-it/library/windowsazure/ee336230.aspx
  [Modello di provisioning di database SQL]: http://msdn.microsoft.com/it-it/library/ee336227.aspx
  [Aggiunta di utenti al database SQL di Azure]: http://blogs.msdn.com/b/sqlazure/archive/2010/06/21/10028038.aspx



[1]: http://www.windowsazure.com/it-it/develop/net/how-to-guides/sql-azure/