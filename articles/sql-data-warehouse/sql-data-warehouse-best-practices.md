<properties
   pageTitle="Procedure consigliate per Azure SQL Data Warehouse | Microsoft Azure"
   description="Indicazioni e procedure consigliate da conoscere per lo sviluppo di soluzioni per Azure SQL Data Warehouse. Queste indicazioni aiuteranno a svolgere al meglio il lavoro."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/29/2016"
   ms.author="sonyama;barbkess"/>

# Procedure consigliate per Azure SQL Data Warehouse

Questo articolo è una raccolta di numerose procedure consigliate che consentono di ottenere un rapporto prezzo/prestazioni ottimale da Azure SQL Data Warehouse. Alcuni concetti in questo articolo sono molto basilari e facili da spiegare, mentre altri sono più avanzati e verranno solo analizzati brevemente. Lo scopo dell'articolo è quello di offrire alcune indicazioni di base e di far capire quali sono gli aspetti importanti da prendere in considerazione per la creazione di un data warehouse. Ogni sezione presenta un concetto e rimanda ad articoli più dettagliati che analizzano il concetto in modo più approfondito.

Chi è alle prime armi con Azure SQL Data Warehouse non deve farsi spaventare dall'articolo. La sequenza degli argomenti indica fondamentalmente l'ordine di importanza. Inizialmente, può essere sufficiente concentrarsi sui primi tre. Man mano che si acquisisce maggiore familiarità e si diventa più esperti nell'uso di SQL Data Warehouse, è possibile rivedere l'articolo ed esaminare altri argomenti. Non ci vorrà molto tempo per arrivare a capire tutto. Inizialmente potrebbe sembrare complesso, ma con il passare del tempo e con l'aumentare dell'esperienza questi argomenti appariranno basilari.

## Ridurre i costi con sospensione e scalabilità
Una funzionalità chiave di SQL Data Warehouse è la possibilità di sospendere il servizio quando non è in uso, interrompendo così la fatturazione delle risorse di calcolo. Un'altra funzionalità fondamentale è la possibilità di ridimensionare le risorse. È possibile sospendere e ridimensionare il servizio dal portale di Azure o usando i comandi di PowerShell. Acquisire familiarità con queste funzionalità, perché possono ridurre notevolmente il costo del data warehouse quando non è in uso. Se si vuole che il data warehouse sia sempre accessibile, è preferibile ridurlo fino alle dimensioni minime, DW100, invece che sospenderlo.

Vedere anche [Sospendere le risorse di calcolo][], [Riavviare le risorse di calcolo][] e [Ridimensionare le prestazioni][].


## Scaricare le transazioni prima della sospensione o del ridimensionamento 
Quando si sospende o si ridimensiona SQL Data Warehouse, in background l'istanza del database viene arrestata. Ciò significa che tutte le query in corso verranno annullate. L'annullamento di una semplice query SELECT è un'operazione rapida senza quasi alcun impatto sul tempo necessario per sospendere o ridimensionare l'istanza. Le query transazionali, che modificano i dati o la struttura dei dati, potrebbero tuttavia richiedere più tempo per l'arresto. **Le query transazionali devono essere completate interamente oppure è necessario il rollback delle modifiche.** Il rollback del lavoro svolto da una query transazionale può richiedere la stessa quantità di tempo, o anche maggiore, della modifica originale applicata dalla query. Se, ad esempio, si annulla una query che sta eliminando righe e che è in esecuzione già da un'ora, potrebbe essere necessaria un'ora affinché il sistema inserisca di nuovo le righe che sono state eliminate. Se si sospende o si ridimensiona il servizio mentre sono in corso transazioni, potrebbe sembrare che l'operazione richieda molto tempo perché per la sospensione o il ridimensionamento è necessario attendere il completamento del rollback.

Vedere anche [Transazioni in SQL Data Warehouse][] e [Ottimizzazione delle transazioni per SQL Data Warehouse][].

## Gestire le statistiche
A differenza di SQL Server, che rileva e crea o aggiorna automaticamente le statistiche sulle colonne in cui può risultare vantaggioso, SQL Data Warehouse richiede la manutenzione manuale delle statistiche. Anche se per il futuro è prevista la modifica di questo comportamento, per il momento è consigliabile gestire le statistiche per garantire l'ottimizzazione dei piani di SQL Data Warehouse. La qualità dei piani creati dall'utilità di ottimizzazione dipende dalla qualità delle statistiche disponibili. **La creazione di statistiche campionate su ogni colonna è un modo semplice per iniziare a usare le statistiche.** È altrettanto importante aggiornare le statistiche quando vengono apportate modifiche significative ai dati. Un approccio conservativo consiste nell'aggiornare le statistiche ogni giorno o dopo ogni operazione di caricamento. Sono sempre necessari compromessi tra le prestazioni e il costo di creazione e aggiornamento delle statistiche. Se si ritiene che la gestione di tutte le statistiche richieda troppo tempo, è consigliabile provare a scegliere in modo più selettivo le colonne con le statistiche o quelle che richiedono aggiornamenti frequenti. Potrebbe ad esempio essere consigliabile aggiornare le colonne di data, in cui potrebbero venire aggiunti nuovi valori ogni giorno. **Il massimo vantaggio è offerto dalle statistiche su colonne usate nei join, colonne usate nella clausola WHERE e colonne presenti in GROUP BY.**

Vedere anche [Managing statistics on tables in SQL Data Warehouse][] \(Gestione delle statistiche nelle tabelle in SQL Data Warehouse), [CREATE STATISTICS (Transact-SQL)][] e [UPDATE STATISTICS (Transact-SQL)][]

## Raggruppare le istruzioni INSERT in batch
Una singola operazione di caricamento in una tabella di piccole dimensioni con un'istruzione INSERT o anche un ricaricamento periodico di una ricerca può essere appropriato per soddisfare le esigenze nel caso di un'istruzione come `INSERT INTO MyLookup VALUES (1, 'Type 1')`. Se tuttavia è necessario caricare migliaia o milioni di righe nel corso della giornata, singole operazioni INSERT potrebbero non essere adeguate. Sviluppare invece i processi in modo che scrivano in un file, mentre un altro processo subentra periodicamente e carica il file.

Vedere anche [INSERT (Transact-SQL)][]
 
## Utilizzare PolyBase per caricare ed esportare rapidamente i dati
SQL Data Warehouse supporta il caricamento e l'esportazione dei dati attraverso diversi strumenti, tra cui Azure Data Factory, PolyBase e BCP. Per piccole quantità di dati in cui le prestazioni non sono fondamentali, qualsiasi strumento può essere adeguato per soddisfare le esigenze. Quando tuttavia si caricano o si esportano grandi volumi di dati oppure sono necessarie prestazioni elevate, PolyBase è la scelta migliore. PolyBase è progettato per sfruttare l'architettura MPP (Massively Parallel Processing, elaborazione parallela massiva) di SQL Data Warehouse ed è quindi in grado di caricare ed esportare grandi volumi di dati più velocemente di qualsiasi altro strumento. Le operazioni di caricamento di PolyBase possono essere eseguite usando CTAS o INSERT INTO. **L'uso di CTAS riduce al minimo la registrazione delle transazioni e rappresenta il modo più rapido per caricare i dati.** Azure Data Factory supporta anche le operazioni di caricamento di PolyBase. PolyBase supporta una vasta gamma di formati di file, inclusi i file GZIP. **Per aumentare al massimo la velocità effettiva quando si usano file di testo GZIP, suddividere i file in 60 o più file per ottimizzare il parallelismo del caricamento.** Per una velocità effettiva totale maggiore, prendere in considerazione il caricamento simultaneo dei dati.

Vedere anche [Caricare i dati in SQL Data Warehouse][], [Guida per l'uso di PolyBase in SQL Data Warehouse][], [Azure SQL Data Warehouse loading patterns and strategies][] \(Strategie e modelli di caricamento di Azure SQL Data Warehouse), [Caricare i dati con Azure Data Factory][], [Spostare dati da e verso Azure SQL Data Warehouse con Azure Data Factory][], [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][] e [Create Table As Select (CTAS) in SQL Data Warehouse][].

## Utilizzare la distribuzione hash per le tabelle di grandi dimensioni
Per impostazione predefinita, per le tabelle viene usata la distribuzione round robin. In questo modo, gli utenti possono iniziare a creare le tabelle in modo semplice senza dover decidere come devono essere distribuite. Le tabelle round robin possono funzionare bene per alcuni carichi di lavoro, ma spesso la selezione di una colonna di distribuzione offre prestazioni molto migliori. L'unione in join di due tabelle dei fatti di grandi dimensioni è l'esempio più comune di come una tabella distribuita in base a una colonna possa offrire prestazioni migliori rispetto a una tabella round robin. Se, ad esempio, sono presenti una tabella degli ordini, distribuita in base a id\_ordine, e una tabella delle transazioni, anch'essa distribuita in base a id\_ordine, quando si uniscono in join le due tabelle in base a id\_ordine, la query diventa una query pass-through, il che significa che si eliminano le operazioni di spostamento dei dati. Un numero inferiore di passaggi consente una maggiore velocità di esecuzione delle query. Anche un numero inferiore di operazioni di spostamento consente query più veloci. Questa spiegazione è molto generica. Durante il caricamento di una tabella distribuita, assicurarsi che i dati in entrata non siano ordinati nella chiave di distribuzione, perché questo rallenterebbe il caricamento. Vedere i collegamenti seguenti per informazioni più dettagliate su come la selezione di una colonna di distribuzione possa migliorare le prestazioni e su come definire una tabella distribuita nella clausola WITH dell'istruzione CREATE TABLES.

Vedere anche [Overview of tables in SQL Data Warehouse][] \(Panoramica sulle tabelle in SQL Data Warehouse), [Distribuzione di tabelle in SQL Data Warehouse][], il post di blog relativo alla [scelta della modalità di distribuzione delle tabelle][] e gli argomenti relativi a [CREATE TABLE][] e [CREATE TABLE AS SELECT][].

## Non creare un numero eccessivo di partizioni
Anche se il partizionamento dei dati può essere molto efficace per la gestione dei dati tramite il cambio di partizione o l'ottimizzazione delle analisi con l'eliminazione delle partizioni, un numero eccessivo di partizioni può rallentare le query. Spesso una strategia di partizionamento che prevede una granularità molto elevata, che potrebbe funzionare bene in SQL Server, potrebbe non essere ideale in SQL Data Warehouse. Un numero eccessivo di partizioni può anche ridurre l'efficacia degli indici columnstore cluster se ogni partizione contiene meno di 1 milione di righe. Tenere presente che in background SQL Data Warehouse partiziona automaticamente i dati in 60 database, quindi se si crea una tabella con 100 partizioni, in realtà si formano 6000 partizioni. Ogni carico di lavoro è diverso, quindi la cosa migliore da fare è sperimentare diversi tipi di partizionamento per capire qual è la soluzione migliore per il carico di lavoro specifico. Prendere in considerazione una granularità inferiore rispetto a quella considerata appropriata in SQL Server. Prendere ad esempio in considerazione l'uso di partizioni settimanali o mensili invece che giornaliere.

Vedere anche [Partitioning tables in SQL Data Warehouse][] \(Partizionamento di tabelle in SQL Data Warehouse).

## Ridurre al minimo le dimensioni delle transazioni
Le istruzioni INSERT, UPDATE e DELETE vengono eseguite in una transazione e quando si verifica un errore è necessario eseguire il rollback. Per ridurre al minimo il rischio di un rollback lungo, ridurre al minimo le dimensioni delle transazioni, quando possibile. A tale scopo, è possibile suddividere in parti le istruzioni INSERT, UPDATE e DELETE. Se, ad esempio, è presente un'istruzione INSERT che si prevede richieda 1 ora, se possibile suddividerla in 4 parti, ognuna delle quali verrà eseguita in 15 minuti. Sfruttare i casi speciali di registrazione minima, ad esempio CTAS, TRUNCATE, DROP TABLE o INSERT in tabelle vuote, in modo da ridurre il rischio di rollback. Un altro modo per eliminare i rollback consiste nell'usare operazioni solo sui metadati come il cambio di partizione per la gestione dati. Ad esempio, invece di eseguire un'istruzione DELETE per eliminare tutte le righe in una tabella in cui data\_ordine corrisponde a ottobre 2001, è possibile creare una partizione mensile dei dati e quindi scambiare la partizione con i dati con una partizione vuota di un'altra tabella (vedere gli esempi relativi ad ALTER TABLE). Per le tabelle non partizionate, prendere in considerazione l'uso di CTAS per scrivere i dati da mantenere in una tabella, invece di usare DELETE. Se un'operazione CTAS richiede la stessa quantità di tempo, è molto più sicura da eseguire perché prevede una registrazione delle transazioni minima e può essere annullata rapidamente, se necessario.

Vedere anche [Transazioni in SQL Data Warehouse][], [Ottimizzazione delle transazioni per SQL Data Warehouse][], [Partitioning tables in SQL Data Warehouse][] \(Partizionamento di tabelle in SQL Data Warehouse), [TRUNCATE TABLE (Transact-SQL)][], [ALTER TABLE (Transact-SQL)][] e [Create Table As Select (CTAS) in SQL Data Warehouse][].

## Utilizzare colonne con dimensioni il più piccole possibili
Quando si definisce il codice DDL, per migliorare le prestazioni di query usare il più piccolo tipo di dati in grado di supportare i dati. Questo aspetto è particolarmente importante per le colonne CHAR e VARCHAR. Se il valore più lungo in una colonna è di 25 caratteri, definire la colonna come VARCHAR(25). Evitare di definire tutte le colonne di tipo carattere impostando una lunghezza predefinita elevata. Definire inoltre le colonne come VARCHAR quando è sufficiente, invece di usare NVARCHAR.

Vedere anche [Overview of tables in SQL Data Warehouse][] \(Panoramica sulle tabelle in SQL Data Warehouse), [Tipi di dati per le tabelle in SQL Data Warehouse][] e [CREATE TABLE][].

## Utilizzare tabelle heap temporanee per i dati temporanei
Quando si inseriscono temporaneamente i dati in SQL Data Warehouse, una tabella heap potrebbe rendere più veloce il processo complessivo. Se si caricano i dati solo per inserirli temporaneamente prima di eseguire altre trasformazioni, il caricamento della tabella in una tabella heap sarà molto più rapido del caricamento dei dati in una tabella columnstore cluster. Inoltre, il caricamento dei dati in una tabella temporanea sarà molto più veloce del caricamento di una tabella in un archivio permanente. Le tabelle temporanee iniziano con "#" e sono accessibili solo dalla sessione in cui sono state create, quindi potrebbero non funzionare in alcuni scenari. Le tabelle heap sono definite nella clausola WITH di CREATE TABLE. Se si usa una tabella temporanea, ricordarsi di creare le statistiche anche su tale tabella temporanea.

Vedere anche [Temporary tables in SQL Data Warehouse][] \(Tabelle temporanee in SQL Data Warehouse), [CREATE TABLE][] e [CREATE TABLE AS SELECT][].

## Ottimizzare le tabelle columnstore cluster
Gli indici columnstore cluster rappresentano uno dei modi più efficienti per archiviare i dati in Azure SQL Data Warehouse. Per impostazione predefinita, le tabelle in SQL Data Warehouse vengono create come tabelle columnstore cluster. Per ottenere prestazioni ottimali per le query sulle tabelle columnstore è importante che la qualità dei segmenti sia buona. Quando le righe vengono scritte nelle tabelle columnstore in condizioni di utilizzo elevato di memoria, la qualità dei segmenti columnstore potrebbe risentirne. La qualità del segmento può essere misurata in base al numero di righe in un gruppo di righe compresso. Per istruzioni dettagliate su come rilevare e migliorare la qualità dei segmenti per le tabelle columnstore cluster, vedere la sezione [Possibili cause di una qualità scadente dell'indice columnstore][] nell'articolo [Indicizzazione di tabelle in SQL Data Warehouse][]. Poiché ottenere una buona qualità dei segmenti columnstore è piuttosto importante, in genere è consigliabile creare un ID utente speciale solo per il caricamento, che utilizza una classe di risorse di medie o grandi dimensioni. Minore è il numero di unità DWU usate, maggiori saranno le dimensioni della classe di risorse da assegnare all'utente incaricato del caricamento.

Poiché le tabelle columnstore in genere non effettuano il push dei dati in un segmento columnstore compresso fino a quando non sono presenti più di 1 milione di righe per tabella e ogni tabella di SQL Data Warehouse è suddivisa in 60 tabelle, come regola generale le tabelle columnstore non risultano vantaggiose per una query a meno che non contengano più di 60 milioni di righe. Per le tabelle con meno di 60 milioni di righe, l'uso di un indice columnstore potrebbe non essere utile, ma potrebbe anche non avere effetti negativi. Inoltre, se si partizionano i dati, tenere presente che ogni partizione dovrà contenere 1 milione di righe per trarre vantaggio da un indice columnstore cluster. Se una tabella è divisa in 100 partizioni, per poter sfruttare i vantaggi di un archivio colonne cluster deve contenere almeno 6 miliardi di righe, ovvero 60 distribuzioni * 100 partizioni * 1 milione di righe. Se la tabella non contiene 6 miliardi di righe come in questo esempio, ridurre il numero di partizioni o prendere in considerazione l'utilizzo di una tabella heap. Potrebbe anche essere utile fare delle prove per verificare se è possibile ottenere prestazioni migliori con una tabella heap con indici secondari al posto di una tabella columnstore. Le tabelle columnstore al momento non supportano gli indici secondari.

Quando si esegue una query su una tabella columnstore, l'esecuzione sarà più rapida se si selezionano solo le colonne necessarie.

Vedere anche gli argomenti sull'[indicizzazione di tabelle][], sulla [guida agli indici columnstore][] e sulla [ricompilazione degli indici columnstore][].

## Utilizzare una classe di risorse più grande per migliorare le prestazioni delle query
SQL Data Warehouse usa i gruppi di risorse per allocare la memoria per le query. Per impostazione predefinita, tutti gli utenti vengono assegnati alla classe di risorse piccola, che garantisce 100 MB di memoria per distribuzione. Poiché ci sono sempre 60 distribuzioni e a ogni distribuzione viene assegnato un minimo di 100 MB, l'allocazione di memoria totale a livello di sistema è di 6.000 MB o poco meno di 6 GB. Alcune query, ad esempio i join di grandi dimensioni oppure le operazioni di caricamento in tabelle columnstore cluster, risultano avvantaggiate da allocazioni di una quantità maggiore di memoria. Alcune query, come le operazioni di sola analisi, non traggono alcun vantaggio. D'altra parte, l'uso di classi di risorse più grandi influisce sulla concorrenza, quindi sarà necessario tenere in considerazione questo aspetto prima di spostare tutti gli utenti in una classe di risorse di grandi dimensioni.
 
Vedere anche [Gestione della concorrenza e del carico di lavoro][].

## Usare una classe di risorse più piccola per aumentare la concorrenza
Se si nota che le query utente sembrano avere un lungo ritardo, è possibile che gli utenti siano in esecuzione in classi di risorse più grandi e stiano utilizzando molti slot di concorrenza, con il conseguente inserimento in coda di altre query. Per vedere se le query degli utenti vengono inserite in coda, eseguire `SELECT * FROM sys.dm_pdw_waits` per verificare se vengono restituite righe.

Vedere anche [Gestione della concorrenza e del carico di lavoro in SQL Data Warehouse][] e [sys.dm\_pdw\_waits (Transact-SQL)][].

## Utilizzare DMV per monitorare e ottimizzare le query
SQL Data Warehouse offre diverse viste a gestione dinamica che è possibile usare per monitorare l'esecuzione delle query. L'articolo relativo al monitoraggio indicato di seguito fornisce istruzioni dettagliate su come esaminare i dati di una query in esecuzione. Per trovare rapidamente le query in queste DMV, può essere utile usare l'opzione LABEL con le query.

Vedere anche [Monitoraggio del carico di lavoro mediante DMV][], [Usare etichette per instrumentare query in SQL Data Warehouse][], [Clausola OPTION (Transact-SQL)][], [sys.dm\_exec\_sessions (Transact-SQL)][], [sys.dm\_pdw\_exec\_requests (Transact-SQL)][], [sys.dm\_pdw\_request\_steps (Transact-SQL)][], [sys.dm\_pdw\_sql\_requests (Transact-SQL)][], [sys.dm\_pdw\_dms\_workers (Transact-SQL)], [DBCC PDW\_SHOWEXECUTIONPLAN (Transact-SQL)][] e [sys.dm\_pdw\_waits (Transact-SQL)][].

## Altre risorse:
Vedere anche l'articolo [Risoluzione dei problemi relativi a SQL Data Warehouse di Azure][] per informazioni sui problemi più comuni e le relative soluzioni.

Se questo articolo non contiene le informazioni necessarie, provare a usare la funzione "Search for docs" (Cerca documenti) sul lato sinistro della pagina per cercare in tutti i documenti relativi ad Azure SQL Data Warehouse. Il [forum di MSDN su Azure SQL Data Warehouse][] è stato creato per offrire un posto in cui porre domande ad altri utenti e al team che si occupa di SQL Data Warehouse. Questo forum viene monitorato attivamente per garantire che venga fornita una risposta a tutte le domande, da un altro utente o da Microsoft. Se si preferisce porre domande in Stack Overflow, è disponibile anche un [forum di Stack Overflow su Azure SQL Data Warehouse][].

Per inviare richieste di funzionalità è possibile usare la pagina dei commenti e suggerimenti su [Azure SQL Data Warehouse][]. Le richieste aggiunte o i voti per altre richieste sono utili per definire le priorità per le funzionalità.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Gestione della concorrenza e del carico di lavoro]: ./sql-data-warehouse-develop-concurrency.md
[Gestione della concorrenza e del carico di lavoro in SQL Data Warehouse]: ./sql-data-warehouse-develop-concurrency.md
[Create Table As Select (CTAS) in SQL Data Warehouse]: ./sql-data-warehouse-develop-ctas.md
[Overview of tables in SQL Data Warehouse]: ./sql-data-warehouse-tables-overview.md
[Tipi di dati per le tabelle in SQL Data Warehouse]: ./sql-data-warehouse-tables-data-types.md
[Distribuzione di tabelle in SQL Data Warehouse]: ./sql-data-warehouse-tables-distribute.md
[Indicizzazione di tabelle in SQL Data Warehouse]: ./sql-data-warehouse-tables-index.md
[indicizzazione di tabelle]: ./sql-data-warehouse-tables-index.md
[Possibili cause di una qualità scadente dell'indice columnstore]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[ricompilazione degli indici columnstore]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Partitioning tables in SQL Data Warehouse]: ./sql-data-warehouse-tables-partition.md
[Managing statistics on tables in SQL Data Warehouse]: ./sql-data-warehouse-tables-statistics.md
[Temporary tables in SQL Data Warehouse]: ./sql-data-warehouse-tables-temporary.md
[Guida per l'uso di PolyBase in SQL Data Warehouse]: ./sql-data-warehouse-load-polybase-guide.md
[Caricare i dati in SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[Spostare dati da e verso Azure SQL Data Warehouse con Azure Data Factory]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[Caricare i dati con Azure Data Factory]: ./sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Load data with PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Monitoraggio del carico di lavoro mediante DMV]: ./sql-data-warehouse-manage-monitor.md
[Sospendere le risorse di calcolo]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Riavviare le risorse di calcolo]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Ridimensionare le prestazioni]: ./sql-data-warehouse-manage-compute-overview.md#scale-performance-bk
[Transazioni in SQL Data Warehouse]: ./sql-data-warehouse-develop-transactions.md
[Ottimizzazione delle transazioni per SQL Data Warehouse]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Risoluzione dei problemi relativi a SQL Data Warehouse di Azure]: ./sql-data-warehouse-troubleshoot.md
[Usare etichette per instrumentare query in SQL Data Warehouse]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/ms190273.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE STATISTICS (Transact-SQL)]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW\_SHOWEXECUTIONPLAN (Transact-SQL)]: https://msdn.microsoft.com/library/mt204017.aspx
[INSERT (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[Clausola OPTION (Transact-SQL)]: https://msdn.microsoft.com/library/ms190322.aspx
[TRUNCATE TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS (Transact-SQL)]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm\_exec\_sessions (Transact-SQL)]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm\_pdw\_exec\_requests (Transact-SQL)]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm\_pdw\_request\_steps (Transact-SQL)]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm\_pdw\_sql\_requests (Transact-SQL)]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm\_pdw\_dms\_workers (Transact-SQL)]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm\_pdw\_waits (Transact-SQL)]: https://msdn.microsoft.com/library/mt203893.aspx
[guida agli indici columnstore]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[scelta della modalità di distribuzione delle tabelle]: https://blogs.msdn.microsoft.com/sqlcat/2015/08/11/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[forum di MSDN su Azure SQL Data Warehouse]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[forum di Stack Overflow su Azure SQL Data Warehouse]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/2016/02/06/azure-sql-data-warehouse-loading-patterns-and-strategies

<!---HONumber=AcomDC_0803_2016-->