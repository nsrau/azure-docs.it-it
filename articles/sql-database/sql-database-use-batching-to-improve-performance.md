 <properties
	pageTitle="Come usare l'invio in batch per migliorare le prestazioni delle applicazioni di database SQL di Azure"
	description="Questo argomento dimostra che le operazioni di database in batch migliorano significativamente la velocità e la scalabilità delle applicazioni di database SQL di Azure. Anche se le tecniche di invio in batch funzionano con qualsiasi database SQL, questo articolo è incentrato su Azure."
	services="sql-database"
	documentationCenter="na"
	authors="annemill"
	manager="jhubbard"
	editor="" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="07/12/2016"
	ms.author="annemill" />

# Come usare l'invio in batch per migliorare le prestazioni delle applicazioni di database SQL

Le operazioni di invio in batch al database SQL di Azure migliorano in modo significativo le prestazioni e la scalabilità delle applicazioni. Per comprendere i vantaggi, la prima parte di questo articolo descrive alcuni risultati dei test di esempio che confrontano le richieste sequenziali e in batch inviate a un database SQL. Il resto dell'articolo illustra le tecniche, gli scenari e le considerazioni che facilitano l'uso corretto dell'invio in batch nelle applicazioni Azure.

**Autori**: Jason Roth, Silvano Coriani, Trent Swanson (Full Scale 180 Inc)

**Revisori**: Conor Cunningham, Michael Thomassy

## Perché l'invio in batch è importante per il database SQL?
L'invio in batch di chiamate a un servizio remoto è una strategia nota per migliorare le prestazioni e la scalabilità. Ogni interazione con un servizio remoto, ad esempio la serializzazione, il trasferimento in rete e la deserializzazione, comporta costi fissi di elaborazione. Il raggruppamento di più transazioni distinte in un singolo batch consente di ridurre al minimo questi costi.

In questo articolo si esamineranno diversi scenari e strategie di invio in batch al database SQL. Queste strategie sono importanti anche per le applicazioni locali che usano SQL Server, tuttavia ci sono diversi motivi per evidenziare l'uso dell'invio in batch per il database SQL:

- La latenza di rete per l'accesso al database SQL è potenzialmente maggiore, in particolare se si accede dall'esterno dello stesso data center di Microsoft Azure.
- Le caratteristiche multi-tenant del database SQL indicano che l'efficienza del livello di accesso ai dati è correlata alla scalabilità generale del database. Il database SQL deve impedire che qualsiasi tenant/utente singolo monopolizzi le risorse del database a scapito di altri tenant. In risposta all'utilizzo eccessivo di quote predefinite, il database SQL può ridurre la velocità effettiva o rispondere con eccezioni di limitazione delle richieste. Strategie efficienti, come l'invio in batch, consentono di effettuare un maggior numero di operazioni sul database SQL prima di raggiungere questi limiti.
- L'invio in batch è efficace anche per le architetture che usano più database (partizionamento orizzontale). L'efficienza dell'interazione con ogni unità database rimane comunque un fattore chiave ai fini della scalabilità generale.

Uno dei vantaggi che derivano dall'uso del database SQL consiste nel non dover gestire i server che ospitano il database. Tuttavia, questa infrastruttura gestita implica anche una diversa concezione delle ottimizzazioni del database. Non è più possibile migliorare l'hardware o l'infrastruttura di rete del database. Gli ambienti sono controllati da Microsoft Azure. L'area principale su cui è possibile esercitare un controllo è la modalità di interazione dell'applicazione con il database SQL. L'invio in batch è una di queste ottimizzazioni.

La prima parte del documento esamina le diverse tecniche di invio in batch per le applicazioni .NET che usano il database SQL. Le ultime due sezioni illustrano invece le linee guida e gli scenari di invio in batch.

## Strategie di invio in batch

### Nota sui risultati della tempistica in questo argomento
>[AZURE.NOTE] I risultati non sono benchmark ma servono per indicare le **prestazioni relative**. Le tempistiche si basano su una media di almeno 10 esecuzioni del test. Le operazioni sono inserimenti in una tabella vuota. Questi test sono stati misurati con un database antecedente a V12 e non corrispondono necessariamente alla velocità effettiva che si potrebbe ottenere in un database V12 usando i nuovi [livelli di servizio](sql-database-service-tiers.md). Il vantaggio relativo della tecnica di invio in batch dovrebbe essere simile.

### Transazioni
Può apparire inconsueto che si inizi un'analisi dell'invio in batch parlando di transazioni. Tuttavia, l'uso delle transazioni sul lato client ha un sottile effetto sull'invio in batch sul lato server che migliora le prestazioni. Inoltre, le transazioni possono essere aggiunte con poche righe di codice, quindi sono un modo rapido per migliorare le prestazioni delle operazioni sequenziali.

Si consideri il codice C# seguente che contiene una sequenza di operazioni di inserimento e aggiornamento in una semplice tabella.

	List<string> dbOperations = new List<string>();
	dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
	dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
	dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
	dbOperations.Add("insert MyTable values ('new value',1)");
	dbOperations.Add("insert MyTable values ('new value',2)");
	dbOperations.Add("insert MyTable values ('new value',3)");

Il codice ADO.NET seguente esegue queste operazioni in sequenza.

	using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
	{
	    conn.Open();
	
	    foreach(string commandString in dbOperations)
	    {
	        SqlCommand cmd = new SqlCommand(commandString, conn);
	        cmd.ExecuteNonQuery();                   
	    }
	}

Il modo migliore per ottimizzare il codice consiste nell'implementare una qualche forma di invio in batch di queste chiamate sul lato client. Esiste tuttavia un modo semplice per migliorare le prestazioni del codice, eseguendo semplicemente il wrapping della sequenza di chiamate in una transazione. Ecco lo stesso codice che usa una transazione.

	using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
	{
	    conn.Open();
	    SqlTransaction transaction = conn.BeginTransaction();
	
	    foreach (string commandString in dbOperations)
	    {
	        SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
	        cmd.ExecuteNonQuery();
	    }
	
	    transaction.Commit();
	}

Le transazioni vengono in effetti usate in entrambi questi esempi. Nel primo ogni singola chiamata rappresenta una transazione implicita. Nel secondo esempio viene eseguito il wrapping di tutte le chiamate in una transazione esplicita. Secondo la documentazione relativa al [log delle transazioni write-ahead](https://msdn.microsoft.com/library/ms186259.aspx), i record del log vengono scaricati su disco al momento del commit della transazione. Si conseguenza, se si includono più chiamate in una transazione, la scrittura nel log delle transazioni può essere ritardata finché non viene eseguito il commit della transazione stessa. In effetti, si abilita l'invio in batch per le operazioni di scrittura nel log delle transazioni del server.

La tabella seguente illustra alcuni risultati di test ad hoc. I test eseguono le medesime operazioni sequenziali di inserimento con e senza transazioni. Per maggiore chiarezza, il primo set di test è stato eseguito in remoto da un portatile al database in Microsoft Azure. Il secondo set di test è stato eseguito da un servizio cloud e un database entrambi residenti nello stesso data center di Microsoft Azure (Stati Uniti occidentali). La tabella seguente mostra la durata in millisecondi delle operazioni di inserimento sequenziali con e senza transazioni.

**Da ambiente locale ad Azure**

| Operazioni | Senza transazione (ms) | Con transazione (ms) |
|---|---|---|
| 1 | 130 | 402 |
| 10 | 1208 | 1226 |
| 100 | 12662 | 10395 |
| 1000 | 128852 | 102917 |


**Da Azure ad Azure (stesso data center)**:

| Operazioni | Senza transazione (ms) | Con transazione (ms) |
|---|---|---|
| 1 | 21 | 26 |
| 10 | 220 | 56 |
| 100 | 2145 | 341 |
| 1000 | 21479 | 2756 |

>[AZURE.NOTE] I risultati non sono benchmark. Vedere la [nota sui risultati della tempistica in questo argomento](#note-about-timing-results-in-this-topic).

In base ai risultati di test precedenti, il wrapping di una singola operazione in una transazione riduce in effetti le prestazioni. Tuttavia, maggiore è il numero di operazioni in una singola transazione, più evidente risulta il miglioramento delle prestazioni. La differenza nelle prestazioni è anche più significativa se tutte le operazioni vengono eseguite nello stesso data center di Microsoft Azure. La maggiore latenza dovuta all'uso del database SQL dall'esterno del data center di Microsoft Azure vanifica il vantaggio in termini di prestazioni derivante dall'uso delle transazioni.

Anche se l'uso delle transazioni può migliorare le prestazioni, continuare a [osservare le procedure consigliate per transazioni e connessioni](https://msdn.microsoft.com/library/ms187484.aspx). Usare transazioni più brevi possibile e chiudere la connessione al database al termine delle operazioni. L'istruzione using nell'esempio precedente assicura la chiusura della connessione al termine del blocco di codice successivo.

L'esempio precedente illustra che è possibile aggiungere una transazione locale al codice ADO.NET con due righe. Le transazioni rappresentano un modo rapido per migliorare le prestazioni del codice usato per le operazioni sequenziali di inserimento, aggiornamento ed eliminazione. Per prestazioni ottimali, provare tuttavia a modificare ulteriormente il codice per sfruttare l'invio in batch sul lato client, ad esempio i parametri con valori di tabella.

Per altre informazioni sulle transazioni in ADO.NET, vedere [Transazioni locali in ADO.NET](https://msdn.microsoft.com/library/vstudio/2k2hy99x.aspx).

### Parametri con valori di tabella
I parametri con valori di tabella supportano tipi di tabella definiti dall'utente come parametri nelle funzioni, nelle stored procedure e nelle istruzioni Transact-SQL. Questa tecnica di invio in batch sul lato client consente di inviare più righe di dati nel parametro con valori di tabella. Per usare parametri con valori di tabella, definire prima di tutto un tipo di tabella. L'istruzione Transact-SQL seguente crea un tipo di tabella denominato **MyTableType**.

	CREATE TYPE MyTableType AS TABLE 
	( mytext TEXT,
	  num INT );
 

Nel codice si crea un oggetto **DataTable** con gli stessi nomi e tipi del tipo di tabella. Passare l'oggetto **DataTable** in un parametro in una chiamata di stored procedure o query di testo. Questa tecnica è illustrata nell'esempio seguente:

	using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
	{
	    connection.Open();
	
	    DataTable table = new DataTable();
	    // Add columns and rows. The following is a simple example.
	    table.Columns.Add("mytext", typeof(string));
	    table.Columns.Add("num", typeof(int));    
	    for (var i = 0; i < 10; i++)
	    {
	        table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
	    }
	
	    SqlCommand cmd = new SqlCommand(
	        "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
	        connection);
	                
	    cmd.Parameters.Add(
	        new SqlParameter()
	        {
	            ParameterName = "@TestTvp",
	            SqlDbType = SqlDbType.Structured,
	            TypeName = "MyTableType",
	            Value = table,
	        });
	
	    cmd.ExecuteNonQuery();
	}

Nell'esempio precedente l'oggetto **SqlCommand** inserisce righe da un parametro con valori di tabella, **@TestTvp**. L'oggetto **DataTable** creato in precedenza viene assegnato a questo parametro con il metodo **SqlCommand.Parameters.Add**. L'invio in batch delle operazioni di inserimento in una singola chiamata migliora sensibilmente le prestazioni rispetto alle operazioni di inserimento sequenziali.

Per migliorare ulteriormente l'esempio precedente, usare una stored procedure anziché un comando basato su testo. Il comando Transact-SQL seguente crea una stored procedure che accetta il parametro con valori di tabella **SimpleTestTableType**.

	CREATE PROCEDURE [dbo].[sp_InsertRows] 
	@TestTvp as MyTableType READONLY
	AS
	BEGIN
	INSERT INTO MyTable(mytext, num) 
	SELECT mytext, num FROM @TestTvp
	END
	GO

Modificare quindi la dichiarazione dell'oggetto **SqlCommand** nell'esempio di codice precedente come segue.

	SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
	cmd.CommandType = CommandType.StoredProcedure;

Nella maggior parte dei casi i parametri con valori di tabella hanno prestazioni equivalenti o superiori rispetto ad altre tecniche di invio in batch. I parametri con valori di tabella sono spesso preferibili perché più flessibili di altre opzioni. Ad esempio, altre tecniche come la copia bulk di SQL consentono solo l'inserimento di nuove righe. Con i parametri con valori di tabella è invece possibile usare la logica della stored procedure per determinare le righe da aggiornare e quelle da inserire. È anche possibile modificare il tipo di tabella perché contenga la colonna "Operation" che indica se la riga specificata deve essere inserita, aggiornata o eliminata.

La tabella seguente illustra i risultati, in millisecondi, dei test ad hoc per l'uso di parametri con valori di tabella.

| Operazioni | Da ambiente locale ad Azure (ms) | Azure stesso data center (ms) |
|---|---|---|
| 1 | 124 | 32 |
| 10 | 131 | 25 |
| 100 | 338 | 51 |
| 1000 | 2615 | 382 |
| 10000 | 23830 | 3586 |

>[AZURE.NOTE] I risultati non sono benchmark. Vedere la [nota sui risultati della tempistica in questo argomento](#note-about-timing-results-in-this-topic).

Il miglioramento delle prestazioni che deriva dall'invio in batch è evidente. Nel test sequenziale precedente 1000 operazioni hanno richiesto 129 secondi all'esterno del data center e 21 secondi all'interno del data center. Con i parametri con valori di tabella 1000 operazioni richiedono invece solo 2,6 secondi all'esterno del data center e 0,4 secondi all'interno.

Per altre informazioni sui parametri con valori di tabella, vedere [Usare parametri con valori di tabella (motore di database)](https://msdn.microsoft.com/library/bb510489.aspx).

### Copia bulk di SQL
La copia bulk di SQL è un altro modo per inserire una grande quantità di dati in un database di destinazione. Le applicazioni .NET possono usare la classe **SqlBulkCopy** per eseguire le operazioni di inserimento bulk. In termini di funzionamento, la classe **SqlBulkCopy** è analoga allo strumento da riga di comando **Bcp.exe** o all'istruzione Transact-SQL **BULK INSERT**. L'esempio di codice seguente illustra come eseguire la copia bulk delle righe nella tabella di origine **DataTable** alla tabella di destinazione MyTable in SQL Server.

	using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
	{
	    connection.Open();
	
	    using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
	    {
	        bulkCopy.DestinationTableName = "MyTable";
	        bulkCopy.ColumnMappings.Add("mytext", "mytext");
	        bulkCopy.ColumnMappings.Add("num", "num");
	        bulkCopy.WriteToServer(table);
	    }
	}

In alcuni casi la copia bulk è preferibile rispetto ai parametri con valori di tabella. Vedere la tabella di confronto dei parametri con valori di tabella rispetto alle operazioni BULK INSERT nell'argomento [Parametri con valori di tabella (motore di database)](https://msdn.microsoft.com/library/bb510489.aspx).

I risultati dei test ad hoc seguenti mostrano le prestazioni, in millisecondi, dell'invio in batch con **SqlBulkCopy**.

| Operazioni | Da ambiente locale ad Azure (ms) | Azure stesso data center (ms) |
|---|---|---|
| 1 | 433 | 57 |
| 10 | 441 | 32 |
| 100 | 636 | 53 |
| 1000 | 2535 | 341 |
| 10000 | 21605 | 2737 |

>[AZURE.NOTE] I risultati non sono benchmark. Vedere la [nota sui risultati della tempistica in questo argomento](#note-about-timing-results-in-this-topic).

Nei batch di dimensioni inferiori l'uso dei parametri con valori di tabella ha prodotto prestazioni migliori rispetto alla classe **SqlBulkCopy**. Tuttavia, l'esecuzione della classe **SqlBulkCopy** risulta del 12-31% più rapida rispetto ai parametri con valori di tabella per i test di 1.000 e 10.000 righe. Come i parametri con valori di tabella, la classe **SqlBulkCopy** è un'opzione valida per le operazioni di inserimento in batch, in particolare rispetto alle prestazioni di operazioni non in batch.

Per altre informazioni sulla copia bulk in ADO.NET, vedere [Operazioni di copia bulk in SQL Server](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### Istruzioni INSERT con parametri a più righe
Un'alternativa per i batch piccoli consiste nella creazione di un'istruzione INSERT con parametri di grandi dimensioni per l'inserimento di più righe. L'esempio di codice seguente illustra questa tecnica.

	using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
	{
	    connection.Open();
	
	    string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
	        "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";
	
	    SqlCommand cmd = new SqlCommand(insertCommand, connection);
	
	    for (int i = 1; i <= 10; i += 2)
	    {
	        cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
	        cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
	    }
	
	    cmd.ExecuteNonQuery();
	}
 

Questo esempio è ideato per illustrare il concetto di base. Uno scenario più realistico prevedrebbe l'esecuzione di un ciclo sulle entità richieste per costruire contemporaneamente la stringa di query e i parametri del comando. Esiste un limite massimo di 2100 parametri di query, il che limita il numero totale di righe elaborabili in questo modo.

I risultati dei test ad hoc seguenti mostrano le prestazioni di questo tipo di istruzione di inserimento, espresse in millisecondi.

| Operazioni | Parametri con valori di tabella (ms) | Singola istruzione INSERT (ms) |
|---|---|---|
| 1 | 32 | 20 |
| 10 | 30 | 25 |
| 100 | 33 | 51 |

>[AZURE.NOTE] I risultati non sono benchmark. Vedere la [nota sui risultati della tempistica in questo argomento](#note-about-timing-results-in-this-topic).

Questo approccio può essere leggermente più veloce per i batch minori di 100 righe. Sebbene l'entità del miglioramento sia lieve, questa tecnica rappresenta un'altra opzione che potrebbe rivelarsi utile in scenari di applicazione specifici.

### DataAdapter
La classe **DataAdapter** consente di modificare un oggetto **DataSet** e di inviare quindi le modifiche come operazioni di tipo INSERT, UPDATE e DELETE. Se si usa **DataAdapter** in questo modo, è importante notare che vengono eseguite chiamate separate per ogni singola operazione. Per migliorare le prestazioni, usare la proprietà **UpdateBatchSize** impostata sul numero di operazioni da eseguire in batch contemporaneamente. Per altre informazioni, vedere [Esecuzione di operazioni batch usando DataAdapters](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### Entity Framework
Entity Framework non supporta attualmente l'invio in batch. Diversi sviluppatori della community hanno provato a elaborare soluzioni alternative, ad esempio l'override del metodo **SaveChanges**. Tuttavia, le soluzioni sono in genere complesse e personalizzate a seconda dell'applicazione e del modello di dati. Il progetto codeplex di Entity Framework include attualmente una pagina di discussione sulla richiesta di questa funzionalità. Per accedere alla discussione, vedere la pagina [Design Meeting Notes - 2 agosto 2012](http://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012).

### XML
Per completezza, è importante considerare l'XML come strategia di invio in batch. Tuttavia, l'uso di XML non offre vantaggi rispetto ad altri metodi e presenta numerosi svantaggi. L'approccio è simile a quello dei parametri con valori di tabella, con la differenza che alla stored procedure viene passato un file o una stringa XML invece di una tabella definita dall'utente. La stored procedure analizza i comandi al suo interno.

Questo approccio presenta diversi svantaggi:

- L'uso di XML può risultare complesso e soggetto a errori.
- L'analisi dell'XML nel database può implicare un uso intensivo della CPU.
- Nella maggior parte dei casi questo metodo è più lento rispetto ai parametri con valori di tabella.

Per questi motivi, l'uso dell'XML per le query in batch non è consigliabile.

## Considerazioni sull'invio in batch
Le sezioni seguenti includono altre indicazioni per l'uso dell'invio in batch nelle applicazioni di database SQL.

### Compromessi
A seconda dell'architettura, l'invio in batch può comportare un compromesso tra prestazioni e resilienza. Si consideri ad esempio uno scenario in cui il proprio ruolo viene inaspettatamente disattivato. La perdita di una riga di dati produce un impatto inferiore alla perdita di un grosso batch di righe non inviate. Esiste un rischio maggiore nei casi in cui le righe vengono memorizzate in un buffer prima dell'invio al database in una finestra temporale specificata.

Considerando questo compromesso, valutare con attenzione i tipi di operazioni da eseguire in batch. Usare più ampiamente i batch, con dimensioni maggiori e finestre temporali più ampie, per i dati meno critici.

### Dimensioni dei batch
I test non hanno in genere evidenziato vantaggi correlati alla suddivisione di batch di grandi dimensioni in blocchi più piccoli. In effetti, questa suddivisione ha causato spesso prestazioni inferiori rispetto all'invio di un singolo batch di grandi dimensioni. Si consideri ad esempio uno scenario che prevede l'inserimento di 1000 righe. La tabella seguente mostra il tempo necessario per usare parametri con valori di tabella per l'inserimento di 1000 righe divise in batch più piccoli.

| Dimensioni dei batch | Iterazioni | Parametri con valori di tabella (ms) |
| -------- | --- | --- |
| 1000 | 1 | 347 |
| 500 | 2 | 355 |
| 100 | 10 | 465 |
| 50 | 20 | 630 |

>[AZURE.NOTE] I risultati non sono benchmark. Vedere la [nota sui risultati della tempistica in questo argomento](#note-about-timing-results-in-this-topic).

È possibile notare che le prestazioni migliori per 1000 righe si ottengono inviandole tutte insieme. In altri test, non riportati qui, si è notato un lieve miglioramento delle prestazioni suddividendo un batch di 10000 righe in due batch da 5000. Lo schema della tabella in questi test è tuttavia relativamente semplice, quindi è consigliabile condurre test su dati e dimensioni di batch specifici per verificare questi risultati.

Un altro fattore da tenere presente è il fatto che se il batch complessivo diventa troppo grande, il database SQL potrebbe applicare limitazioni e rifiutarne il commit. Per ottenere risultati ottimali, testare il proprio scenario specifico per determinare le dimensioni ideali dei batch. Rendere configurabili le dimensioni del batch in fase di esecuzione per consentire modifiche rapide in base alle prestazioni o agli errori.

Infine, individuare un equilibro tra le dimensioni del batch e i rischi associati all'invio in batch. Se si verificano errori temporanei o il ruolo ha esito negativo, valutare le conseguenze di dover ripetere l'operazione o di perdere i dati nel batch.

### Elaborazione parallela
Che cosa accadrebbe se si adottasse l'approccio di ridurre le dimensioni del batch, ma si utilizzassero più thread per eseguire le operazioni? Anche in questo caso, i test indicano che diversi batch multithreading di dimensioni ridotte producono prestazioni generalmente inferiori a un singolo batch di dimensioni maggiori. Il test seguente tenta di inserire 1000 righe in uno o più batch paralleli. Questo test indica come più batch simultanei causino in effetti una riduzione delle prestazioni.

| Dimensioni dei batch [iterazioni] | Due thread (ms) | Quattro thread (ms) | Sei thread (ms) |
| -------- | --- | --- | --- |
| 1000 [1] | 277 | 315 | 266 |
| 500 [2] | 548 | 278 | 256 |
| 250 [4] | 405 | 329 | 265 |
| 100 [10] | 488 | 439 | 391 |

>[AZURE.NOTE] I risultati non sono benchmark. Vedere la [nota sui risultati della tempistica in questo argomento](#note-about-timing-results-in-this-topic).

Esistono diverse potenziali cause della riduzione delle prestazioni derivante dal parallelismo:

- Sono presenti più chiamate di rete simultanee invece di una.
- L'esecuzione di più operazioni su una singola tabella può determinare contese e blocchi.
- Il multithreading implica overhead.
- Il costo dell'apertura di più connessioni annulla il vantaggio dell'elaborazione parallela.

Se si opera su più tabelle o database, questa strategia può produrre un aumento delle prestazioni. Il partizionamento orizzontale o le federazioni di database rappresentano uno scenario possibile per questo approccio. Il partizionamento orizzontale usa più database e indirizza dati diversi a ognuno di essi. Se ogni piccolo batch viene indirizzato a un database diverso, l'esecuzione delle operazioni in parallelo può risultare più efficiente. Tuttavia, il miglioramento delle prestazioni non è sufficientemente elevato da giustificare la decisione di adottare il partizionamento orizzontale del database nella propria soluzione.

In alcune progettazioni, l'esecuzione parallela di batch di piccole dimensioni può produrre un miglioramento della velocità effettiva delle richieste in un sistema sotto carico. In questo caso, anche se l'esecuzione di un singolo batch di dimensioni maggiori è più rapida, quella di più batch in parallelo può risultare più efficiente.

Se si usa l'esecuzione parallela, provare a controllare il numero massimo di thread di lavoro. Un numero più piccolo potrebbe ridurre il rischio di contese e i tempi di esecuzione. Tenere anche presente il carico aggiuntivo di questa soluzione sul database di destinazione, sia in termini di connessioni sia di transazioni.

### Fattori correlati alle prestazioni
Le indicazioni tipiche relative alle prestazioni dei database influiscono anche sull'invio in batch. Le prestazioni delle operazioni di inserimento, ad esempio, risultano ridotte per le tabelle con chiave primaria di grandi dimensioni o con numerosi indici non cluster.

Se i parametri con valori di tabella usano una stored procedure, è possibile eseguire il comando **SET NOCOUNT ON** all'inizio della routine. Questa istruzione rimuove la restituzione del conteggio delle righe interessate nella routine. Tuttavia, nei test l'uso di **SET NOCOUNT ON** non ha avuto alcun effetto sulle prestazioni o le ha ridotte. La stored procedure usata per i test era semplice, con un singolo comando **INSERT** del parametro con valori di tabella. È possibile che stored procedure più complesse possano trarre vantaggio da questa istruzione. Non si deve tuttavia supporre che l'aggiunta di **SET NOCOUNT ON** alla stored procedure migliori automaticamente le prestazioni. Per comprendere l'effetto, testare la stored procedure con e senza l'istruzione **SET NOCOUNT ON**.

## Scenari di invio in batch
Nelle sezioni seguenti viene descritto come usare parametri con valori di tabella in tre scenari di applicazione. Il primo scenario illustra in che modo interagiscono il buffering e l'invio in batch. Nel secondo scenario le prestazioni vengono migliorate eseguendo operazioni master/dettaglio in una singola chiamata di stored procedure. Lo scenario finale illustra come usare parametri con valori di tabella in un'operazione "UPSERT".

### Buffering
Sebbene alcuni scenari siano particolarmente adatti all'invio in batch, ne esistono numerosi che potrebbero trarre vantaggio da questo tipo di operazione grazie all'elaborazione ritardata. L'elaborazione ritardata implica tuttavia anche un maggiore rischio che i dati vengano persi in caso di errore imprevisto. È importante comprendere tale rischio e valutarne le conseguenze.

Si consideri ad esempio un'applicazione Web che tiene traccia della cronologia di navigazione di ogni utente. Per ogni richiesta di pagina, l'applicazione potrebbe eseguire una chiamata al database per registrare la visualizzazione della pagina da parte dell'utente. Tuttavia è possibile conseguire livelli maggiori di prestazioni e scalabilità mediante il buffering delle attività di navigazione dell'utente e quindi inviando i dati al database in batch. È possibile attivare l'aggiornamento del database in base al tempo trascorso e/o alle dimensioni del buffer. Ad esempio, una regola potrebbe indicare che il batch deve essere elaborato dopo 20 secondi o quando il buffer raggiunge i 1000 elementi.

L'esempio di codice seguente usa [Reactive Extensions - Rx](https://msdn.microsoft.com/data/gg577609) per elaborare eventi memorizzati nel buffer generati da una classe di monitoraggio. Quando il buffer si riempie o si raggiunge un timeout, il batch di dati utente viene inviato al database con un parametro con valori di tabella.

La classe NavHistoryData seguente modella i dettagli di navigazione dell'utente. Contiene informazioni di base quali l'identificatore utente, l'URL a cui si accede e l'ora di accesso.

	public class NavHistoryData
	{
	    public NavHistoryData(int userId, string url, DateTime accessTime)
	    { UserId = userId; URL = url; AccessTime = accessTime; }
	    public int UserId { get; set; }
	    public string URL { get; set; }
	    public DateTime AccessTime { get; set; }
	}

La classe NavHistoryDataMonitor è responsabile del buffering dei dati di navigazione dell'utente nel database. Contiene un metodo RecordUserNavigationEntry che risponde generando un evento **OnAdded**. Il codice seguente illustra la logica del costruttore che utilizza Rx per creare una raccolta osservabile in base all'evento. Sottoscrive quindi questa raccolta osservabile con il metodo Buffer. L'overload specifica che il buffer deve essere inviato ogni 20 secondi o 1000 voci.

	public NavHistoryDataMonitor()
	{
	    var observableData =
	        Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");
	
	    observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
	}

Il gestore converte tutti gli elementi memorizzati nel buffer nel tipo con valori di tabella e quindi passa questo tipo a una stored procedure che elabora il batch. Il codice seguente illustra la definizione completa per le classi NavHistoryDataEventArgs e NavHistoryDataMonitor.

	public class NavHistoryDataEventArgs : System.EventArgs
	{
	    public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
	    public NavHistoryData Data { get; set; }
	}
	
	public class NavHistoryDataMonitor
	{
	    public event EventHandler<NavHistoryDataEventArgs> OnAdded;
	
	    public NavHistoryDataMonitor()
	    {
	        var observableData =
	            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");
	
	        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
	    }
	
	    public void RecordUserNavigationEntry(NavHistoryData data)
	    {    
	        if (OnAdded != null)
	            OnAdded(this, new NavHistoryDataEventArgs(data));
	    }
	
	    protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
	    {
	        DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
	        navHistoryBatch.Columns.Add("UserId", typeof(int));
	        navHistoryBatch.Columns.Add("URL", typeof(string));
	        navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
	        foreach (EventPattern<NavHistoryDataEventArgs> item in items)
	        {
	            NavHistoryData data = item.EventArgs.Data;
	            navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
	        }
	
	        using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
	        {
	            connection.Open();
	
	            SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
	            cmd.CommandType = CommandType.StoredProcedure;
	
	            cmd.Parameters.Add(
	                new SqlParameter()
	                {
	                    ParameterName = "@NavHistoryBatch",
	                    SqlDbType = SqlDbType.Structured,
	                    TypeName = "NavigationHistoryTableType",
	                    Value = navHistoryBatch,
	                });
	
	            cmd.ExecuteNonQuery();
	        }
	    }
	}

Per usare questa classe di buffering, l'applicazione crea un oggetto statico NavHistoryDataMonitor. Ogni volta che l'utente accede a una pagina, l'applicazione chiama il metodo NavHistoryDataMonitor.RecordUserNavigationEntry. La logica di buffering continua a provvedere all'invio delle voci al database in batch.

### Master/dettaglio
I parametri con valori di tabella sono utili per gli scenari INSERT semplici. Tuttavia, può risultare più difficile inviare in batch le operazioni di inserimento che includono più tabelle. Lo scenario "master/dettaglio" è un buon esempio. La tabella master identifica l'entità primaria. In una o più tabelle dei dettagli sono archiviati altri dati sull'entità. In questo scenario, le relazioni di chiave esterna applicano la relazione dei dettagli a un'entità master univoca. Si consideri una versione semplificata di una tabella PurchaseOrder e la tabella associata OrderDetail. L'istruzione Transact-SQL seguente crea la tabella PurchaseOrder con quattro colonne: OrderID, OrderDate, CustomerID e Status.

	CREATE TABLE [dbo].[PurchaseOrder](
	[OrderID] [int] IDENTITY(1,1) NOT NULL,
	[OrderDate] [datetime] NOT NULL,
	[CustomerID] [int] NOT NULL,
	[Status] [nvarchar](50) NOT NULL,
	 CONSTRAINT [PrimaryKey_PurchaseOrder] 
	PRIMARY KEY CLUSTERED ( [OrderID] ASC ))

Ogni ordine contiene uno o più acquisti di prodotti. Tali informazioni vengono acquisite nella tabella PurchaseOrderDetail. L'istruzione Transact-SQL seguente crea la tabella PurchaseOrderDetail con cinque colonne: OrderID, OrderDetailID, ProductID, UnitPrice e OrderQty.

	CREATE TABLE [dbo].[PurchaseOrderDetail](
	[OrderID] [int] NOT NULL,
	[OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
	[ProductID] [int] NOT NULL,
	[UnitPrice] [money] NULL,
	[OrderQty] [smallint] NULL,
	 CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED 
	( [OrderID] ASC, [OrderDetailID] ASC ))

La colonna OrderID della tabella PurchaseOrderDetail deve fare riferimento a un ordine dalla tabella PurchaseOrder. La seguente definizione di una chiave esterna applica il vincolo.

	ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
	CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
	REFERENCES [dbo].[PurchaseOrder] ([OrderID])

Per usare parametri con valori di tabella, è necessario avere un tipo di tabella definito dall'utente per ogni tabella di destinazione.

	CREATE TYPE PurchaseOrderTableType AS TABLE 
	( OrderID INT,
	  OrderDate DATETIME,
	  CustomerID INT,
	  Status NVARCHAR(50) );
	GO
	
	CREATE TYPE PurchaseOrderDetailTableType AS TABLE 
	( OrderID INT,
	  ProductID INT,
	  UnitPrice MONEY,
	  OrderQty SMALLINT );
	GO

Definire quindi una stored procedure che accetti tabelle di questi tipi. Questa routine consente a un'applicazione di inviare in batch localmente un set di ordini e i dettagli degli ordini in una singola chiamata. L'istruzione Transact-SQL seguente fornisce la dichiarazione completa della stored procedure per questo esempio di ordine di acquisto.

	CREATE PROCEDURE sp_InsertOrdersBatch (
	@orders as PurchaseOrderTableType READONLY,
	@details as PurchaseOrderDetailTableType READONLY )
	AS
	SET NOCOUNT ON;
	
	-- Table that connects the order identifiers in the @orders
	-- table with the actual order identifiers in the PurchaseOrder table
	DECLARE @IdentityLink AS TABLE ( 
	SubmittedKey int, 
	ActualKey int, 
	RowNumber int identity(1,1)
	);
	 
	      -- Add new orders to the PurchaseOrder table, storing the actual
	-- order identifiers in the @IdentityLink table   
	INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
	OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
	SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;
	
	-- Match the passed-in order identifiers with the actual identifiers
	-- and complete the @IdentityLink table for use with inserting the details
	WITH OrderedRows As (
	SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber 
	FROM @orders
	)
	UPDATE @IdentityLink SET SubmittedKey = M.OrderID
	FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;
	
	-- Insert the order details into the PurchaseOrderDetail table, 
	      -- using the actual order identifiers of the master table, PurchaseOrder
	INSERT INTO PurchaseOrderDetail (
	[OrderID],
	[ProductID],
	[UnitPrice],
	[OrderQty] )
	SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
	FROM @details D
	JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
	GO

In questo esempio la tabella @IdentityLink definita a livello locale archivia i valori OrderID effettivi dalle righe appena inserite. Gli identificatori di ordine sono diversi dai valori OrderID temporanei nei parametri con valori di tabella @orders e @details. Per questo motivo, la tabella @IdentityLink connette quindi i valori OrderID del parametro @orders ai valori OrderID reali per le nuove righe della tabella PurchaseOrder. Dopo questo passaggio, la tabella @IdentityLink può facilitare l'inserimento dei dettagli degli ordini con il valore OrderID effettivo che soddisfa il vincolo di chiave esterna.

Questa stored procedure può essere utilizzata dal codice o da altre chiamate Transact-SQL. Vedere la sezione sui parametri con valori di tabella di questo articolo per un esempio di codice. Il codice Transact-SQL seguente mostra come chiamare sp\_InsertOrdersBatch.

	declare @orders as PurchaseOrderTableType
	declare @details as PurchaseOrderDetailTableType
	
	INSERT @orders 
	([OrderID], [OrderDate], [CustomerID], [Status])
	VALUES(1, '1/1/2013', 1125, 'Complete'),
	(2, '1/13/2013', 348, 'Processing'),
	(3, '1/12/2013', 2504, 'Shipped')
	
	INSERT @details
	([OrderID], [ProductID], [UnitPrice], [OrderQty])
	VALUES(1, 10, $11.50, 1),
	(1, 12, $1.58, 1),
	(2, 23, $2.57, 2),
	(3, 4, $10.00, 1)
	
	exec sp_InsertOrdersBatch @orders, @details

Questa soluzione consente a ogni batch di usare un set di valori OrderID che iniziano da 1. Questi valori OrderID temporanei descrivono le relazioni nel batch, ma i valori OrderID effettivi vengono determinati al momento dell'operazione di inserimento. È possibile eseguire ripetutamente le stesse istruzioni dell'esempio precedente e generare ordini univoci nel database. Per questo motivo, considerare l'aggiunta di più logica di database o di codice che impedisca gli ordini duplicati quando si usa questa tecnica di invio in batch.

In questo esempio viene spiegato che è possibile eseguire in batch anche operazioni di database più complesse, ad esempio operazioni master/dettaglio, usando i parametri con valori di tabella.

### UPSERT
Un altro scenario di invio in batch prevede l'aggiornamento simultaneo di righe esistenti e l'inserimento di nuove righe. Questa operazione viene talvolta denominata "UPSERT" (aggiornamento + inserimento). Invece di eseguire chiamate separate a INSERT e UPDATE, l'istruzione MERGE è più adatta a questa attività. L'istruzione MERGE può eseguire operazioni di inserimento e aggiornamento in una singola chiamata.

I parametri con valori di tabella possono essere usati con l'istruzione MERGE per eseguire aggiornamenti e inserimenti. Si consideri ad esempio una tabella Employee semplificata che contiene le colonne seguenti: EmployeeID, FirstName, LastName, SocialSecurityNumber:

	CREATE TABLE [dbo].[Employee](
	[EmployeeID] [int] IDENTITY(1,1) NOT NULL,
	[FirstName] [nvarchar](50) NOT NULL,
	[LastName] [nvarchar](50) NOT NULL,
	[SocialSecurityNumber] [nvarchar](50) NOT NULL,
	 CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
	([EmployeeID] ASC ))
 
In questo esempio, è possibile usare il fatto che la colonna SocialSecurityNumber è unica per eseguire un'operazione MERGE di più dipendenti. Creare innanzitutto il tipo di tabella definito dall'utente:

	CREATE TYPE EmployeeTableType AS TABLE 
	( Employee_ID INT,
	  FirstName NVARCHAR(50),
	  LastName NVARCHAR(50),
	  SocialSecurityNumber NVARCHAR(50) );
	GO

Successivamente, creare una stored procedure oppure scrivere codice che usi l'istruzione MERGE per eseguire l'aggiornamento e l'inserimento. L'esempio seguente usa l'istruzione MERGE in un parametro con valori di tabella, @employees, di tipo EmployeeTableType. Il contenuto della tabella @employees non è illustrato.

	MERGE Employee AS target
	USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees) 
	AS source ([FirstName], [LastName], [SocialSecurityNumber])
	ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
	WHEN MATCHED THEN 
	UPDATE SET
	target.FirstName = source.FirstName, 
	target.LastName = source.LastName
	WHEN NOT MATCHED THEN
	   INSERT ([FirstName], [LastName], [SocialSecurityNumber])
	   VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);

Per altre informazioni, vedere la documentazione e gli esempi relativi all'istruzione MERGE. Anche se la stessa operazione può essere eseguita in una chiamata di stored procedure in più passaggi con le operazioni INSERT e UPDATE, l'istruzione MERGE è più efficiente. Il codice del database può anche creare chiamate Transact-SQL che usano l'istruzione MERGE direttamente senza richiedere due chiamate di database per INSERT e UPDATE.

## Riepilogo delle indicazioni

L'elenco seguente fornisce un riepilogo delle indicazioni relative all'invio in batch descritte in questo argomento:

- Usare il buffering e l'invio in batch per migliorare le prestazioni e la scalabilità delle applicazioni di database SQL.
- Esaminare i compromessi tra invio in batch/buffering e resilienza. In caso di errore in un ruolo, il rischio di perdere un batch non elaborato di dati aziendali critici potrebbe vanificare il vantaggio in termini di prestazioni che deriva dall'invio in batch.
- Provare a mantenere tutte le chiamate al database entro un singolo data center per ridurre la latenza.
- Se si sceglie una singola tecnica di invio in batch, i parametri con valori di tabella offrono il miglior rapporto tra prestazioni e flessibilità.
- Per prestazioni di inserimento estremamente veloci, attenersi alle linee guida generali, ma testare lo scenario:
	- Per < 100 righe, usare un singolo comando INSERT con parametri.
	- Per < 1000 righe usare parametri con valori di tabella.
	- Per >= 1000 righe usare SqlBulkCopy.
- Per operazioni di aggiornamento ed eliminazione, usare parametri con valori di tabella con la logica della stored procedure che determini il corretto funzionamento in ogni riga del parametro della tabella.
- Linee guida sulle dimensioni dei batch:
	- Usare batch di grandi dimensioni considerando i requisiti delle applicazioni e aziendali.
	- Bilanciare il miglioramento delle prestazioni dei batch di grandi dimensioni e i rischi di errori temporanei o irreversibili. Qual è la conseguenza di più tentativi o di perdita dei dati nel batch?
	- Testare i batch con le massime dimensioni per verificare che il database SQL non li rifiuti.
	- Creare le impostazioni di configurazione che controllano l'invio in batch, ad esempio le dimensioni dei batch o la finestra temporale di buffering. Queste impostazioni offrono flessibilità. È possibile modificare il comportamento di invio in batch in produzione senza ridistribuire il servizio cloud.
- Evitare l'esecuzione parallela di batch che operano su una singola tabella in un database. Se si sceglie di dividere un singolo batch tra più thread di lavoro, eseguire i test per determinare il numero ideale di thread. Dopo una soglia non specificata, più thread determinano una riduzione delle prestazioni invece di un aumento.
- Considerare il buffering in base a dimensioni e tempo come modalità di implementazione dell'invio in batch per più scenari.

## Passaggi successivi

Questo articolo descrive in che modo le tecniche di progettazione e codifica di database correlate all'invio in batch possano migliorare le prestazioni e la scalabilità delle applicazioni. Questo è però solo uno dei fattori della strategia complessiva. Per altri modi di migliorare le prestazioni e la scalabilità, vedere [Indicazioni sulle prestazioni del database SQL di Azure per i singoli database](sql-database-performance-guidance.md) e [Considerazioni su prezzo e prestazioni per un pool di database elastici](sql-database-elastic-pool-guidance.md).

<!---HONumber=AcomDC_0720_2016-->