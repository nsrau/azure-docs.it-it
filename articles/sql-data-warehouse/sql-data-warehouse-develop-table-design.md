<properties
   pageTitle="Progettazione di tabelle in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per la progettazione di tabelle in Azure SQL Data Warehouse per lo sviluppo di soluzioni."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/04/2016"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Progettazione di tabelle in SQL Data Warehouse #
SQL Data Warehouse è un sistema di database distribuito a elaborazione parallela massiva. Archivia i dati in molte posizioni diverse, dette **distribuzioni**. Ogni **distribuzione** è simile a un bucket: archivia un subset univoco dei dati nel data warehouse. Distribuendo i dati e le funzionalità di elaborazione tra più nodi, SQL Data Warehouse è in grado di offrire un'enorme scalabilità, ben oltre qualsiasi sistema singolo.

Quando si crea una tabella in SQL Data Warehouse, questa viene in effetti suddivisa tra tutte le distribuzioni.

Questo articolo include gli argomenti seguenti:

- Tipi di dati supportati
- Principi della distribuzione dei dati
- Distribuzione round robin
- Distribuzione hash
- Partizionamento di tabelle
- Statistiche
- Funzionalità non supportate

## Tipi di dati supportati
SQL Data Warehouse supporta i tipi di dati aziendali comuni elencati di seguito:

- **bigint**
- **binary**
- **bit**
- **char**
- **date**
- **datetime**
- **datetime2**
- **datetimeoffset**
- **decimal**
- **float**
- **int**
- **money**
- **nchar**
- **nvarchar**
- **real**
- **smalldatetime**
- **smallint**
- **smallmoney**
- **time**
- **tinyint**
- **varbinary**
- **varchar**

Per identificare le colonne del data warehouse che contengono tipi non compatibili, è possibile usare la query seguente:

```
SELECT  t.[name]
,       c.[name]
,       c.[system_type_id]
,       c.[user_type_id]
,       y.[is_user_defined]
,       y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN
                (   'geography'
                ,   'geometry'
                ,   'hierarchyid'
                ,   'image'
                ,   'ntext'
                ,   'numeric'
                ,   'sql_variant'
                ,   'sysname'
                ,   'text'
                ,   'timestamp'
                ,   'uniqueidentifier'
                ,   'xml'
                )

OR  (   y.[name] IN (  'nvarchar','varchar','varbinary')
    AND c.[max_length] = -1
    )
OR  y.[is_user_defined] = 1
;

```

La query include tutti i tipi di dati definiti dall'utente che non sono supportati.

di seguito ci sono alcune alternative che è possibile utilizzare al posto di tipi di dati non supportati.

Invece di:

- **geometry**, usare un tipo varbinary.
- **geography**, usare un tipo varbinary
- **hierarchyid**, tipo CLR non nativo
- **image**, **text**, **ntext**, quando è basato su testo, usare varchar/nvarchar (il valore inferiore offre prestazioni migliori)
- **nvarchar(max)**, usare nvarchar(4000) o un valore inferiore per prestazioni migliori
- **numeric**, usare decimal.
- **sql\_variant**, dividere la colonna in più colonne fortemente tipizzate.
- **sysname**, usare nvarchar(128).
- **table**, convertire in tabelle temporanee.
- **timestamp**, rielaborare il codice per l'uso di datetime2 e della funzione `CURRENT_TIMESTAMP`. Tenere presente che non è possibile usare current\_timestamp come vincolo predefinito e che il valore non verrà aggiornato automaticamente. Se è necessario eseguire la migrazione di valori rowversion da una colonna di tipo timestamp, usare BINARY(8) o VARBINARY(8) per i valori di versione di riga NOT NULL o NULL.
- **varchar(max)**, usare varchar(8000) o un valore inferiore per prestazioni migliori
- **uniqueidentifier**, usare varbinary(8).
- **tipi definiti dall'utente**, riconvertirli nei tipi nativi corrispondenti, se possibile
- **xml**, usare varchar(8000) o un valore inferiore per prestazioni migliori; dividere tra più colonne se necessario

Supporto parziale:

- I vincoli predefiniti supportano solo valori letterali e costanti. Le espressioni o le funzioni non deterministiche, ad esempio `GETDATE()` o `CURRENT_TIMESTAMP`, non sono supportate.

> [AZURE.NOTE]Definire le tabelle in modo che le dimensioni massime possibili della riga, inclusa la lunghezza totale delle colonne a lunghezza variabile, non superino 32.767 byte. Anche se è possibile definire una riga con dati a lunghezza variabile che possono superare questa cifra, non si potranno inserire dati nella tabella. Provare anche a limitare le dimensioni delle colonne a lunghezza variabile per migliorare la velocità effettiva delle query in esecuzione.

## Principi della distribuzione dei dati

Per distribuire i dati in SQL Data Warehouse sono disponibili due opzioni:

1. Distribuire i dati in modo uniforme ma casuale 
2. Distribuire i dati in base ai valori hash di una singola colonna

La distribuzione dei dati viene decisa a livello di tabella. Tutte le tabelle vengono distribuite. Si assegnerà distribuzione per ogni tabella nel database SQL Data Warehouse.

La prima opzione è detta distribuzione **round robin** e a volte anche hash casuale. Può essere considerata come l'opzione predefinita o di tipo operatore alternativo.

La seconda opzione è nota come distribuzione **hash**. Può essere considerata come una forma ottimizzata di distribuzione dei dati. È preferibile che cluster di tabelle condividano criteri di unione e/o aggregazione comuni.

## Distribuzione round robin

La distribuzione round robin è un metodo di suddivisone quanto più possibile uniforme dei dati tra tutte le distribuzioni. I buffer contenenti righe di dati vengono allocati a rotazione (da ciò il nome round robin) a ogni distribuzione. Il processo viene ripetuto finché tutti i buffer di dati sono stati allocati. In nessuna fase i dati vengono ordinati o classificati in una tabella con distribuzione round robin. Per questo motivo, una distribuzione round robin viene a volte definita hash causale. I dati vengono suddivisi nel modo più uniforme possibile tra le distribuzioni.

Di seguito è riportato un esempio di tabella con distribuzione round robin:

```
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = ROUND_ROBIN
)
;
```

Anche questo è un esempio di tabella con distribuzione round robin:

```
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
)
;
```

> [AZURE.NOTE]Nel secondo esempio precedente, come si noterà, non viene fatto alcun accenno alla chiave di distribuzione. Round robin è l'opzione predefinita e quindi la chiave non è assolutamente necessaria. È tuttavia considerata una procedura consigliata, perché assicura che i peer conoscano le intenzioni dell'autore durante la revisione della progettazione della tabella.

Questo tipo di tabella viene usato comunemente quando non sono presenti colonne chiave ovvie in base alle quali eseguire l'hashing dei dati. Può essere usato anche in tabelle più piccole o meno significative in cui il costo dello spostamento potrebbe non essere così elevato.

Il caricamento di dati in una tabella con distribuzione round robin tende a essere più veloce rispetto a una tabella con distribuzione hash. In una tabella con distribuzione round robin non è necessario comprendere i dati o eseguire l'hashing prima del caricamento. Per questo motivo le tabelle round robin sono spesso un'ottima scelta come destinazioni di caricamento.

> [AZURE.NOTE]Quando i dati vengono distribuiti con il metodo round robin, vengono allocati alla distribuzione a livello di *buffer*.

### Indicazioni

Si consideri l'uso della distribuzione round robin della tabella negli scenari seguenti:

- Quando non è presente una chiave di join ovvia
- Se non è nota una chiave di distribuzione hash candidata
- Se la tabella non condivide una chiave di join comune con altre tabelle
- Se il join è meno significativo di altri join nella query
- Quando la tabella è una tabella di caricamento iniziale

## Distribuzione hash

La distribuzione hash usa una funzione interna per suddividere i set di dati tra le distribuzioni mediante l'hash di una singola colonna. Quando viene eseguito l'hashing dei dati, non viene applicato un ordine specifico ai dati allocati a una distribuzione. L'hash stesso è tuttavia un processo deterministico, che rende prevedibile il risultato dell'hash. Ad esempio, l'hash di una colonna integer che contiene il valore 10 produrrà sempre lo stesso valore hash. Ciò significa che ***qualsiasi*** colonna integer con hash contenente il valore 10 finirà per essere allocata alla stessa distribuzione. Questo vale anche tra tabelle.

La prevedibilità dell'hash è estremamente importante. Significa che l'hash che distribuisce i dati può fornire miglioramenti delle prestazioni durante la lettura dei dati e il join delle tabelle.

Come si vedrà di seguito, la distribuzione hash può essere molto efficace per l'ottimizzazione delle query. Ecco perché viene considerata una forma ottimizzata di distribuzione dei dati.

> [AZURE.NOTE]Tenere presente che l'hash non si basa sul valore dei dati, ma sul tipo dei dati di cui viene eseguito l'hashing.

Di seguito è riportata una tabella con distribuzione hash in base a ProductKey.

```
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
)
;
```

> [AZURE.NOTE]Quando i dati vengono distribuiti con il metodo round robin, vengono allocati alla distribuzione a livello di riga.

## Partizioni della tabella
Le partizioni della tabella sono supportate e facili da definire.

Esempio di comando `CREATE TABLE` partizionata in SQL Data Warehouse:

```
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

Notare che nella definizione non è presente una funzione o uno schema di partizionamento. Questo aspetto viene considerato al momento della creazione della tabella. È sufficiente identificare i punti di delimitazione per la colonna che verrà usata come chiave di partizionamento.

## Statistiche

SQL Data Warehouse usa Query Optimizer in modalità distribuita per creare il piano di query appropriato quando gli utenti eseguono query sulle tabelle. Dopo essere stato creato, il piano di query fornisce la strategia e il metodo usati dal database per accedere ai dati e soddisfare la richiesta dell'utente. Query Optimizer di SQL Data Warehouse si basa sul costo. In altre parole, confronta le diverse opzioni (piani) in base al relativo costo e sceglie il piano disponibile più efficiente. SQL Data Warehouse deve quindi avere una quantità di informazioni per prendere decisioni oculate basate sul costo. Contiene informazioni statistiche sulla tabella (per le dimensioni della tabella) e in oggetti di database noti come `STATISTICS`.

Le statistiche vengono mantenute per colonne singole o multiple di indici o tabelle. Forniscono informazioni importanti relative a cardinalità e selettività dei valori a Query Optimizer basato sul costo. Questo aspetto riveste particolare interesse quando Query Optimizer deve valutare clausole JOIN, GROUP BY, HAVING e WHERE in una query. È perciò molto importante che le informazioni contenute in questi oggetti statistics riflettano in modo *accurato* lo stato attuale della tabella. È fondamentale comprendere che l'accuratezza del costo è il fattore importante. Se le statistiche riflettono in modo accurato lo stato della tabella, i piani possono essere confrontati per trovare il costo inferiore. Se non sono accurate, SQL Data Warehouse potrebbe scegliere il piano non corretto.

Le statistiche a livello di colonna in SQL Data Warehouse sono definite dall'utente.

In altre parole, è necessario crearle. Come si è appena appreso, questo non è un aspetto da ignorare. Si tratta di una differenza importante tra SQL Server e SQL Data Warehouse. SQL Server crea automaticamente le statistiche quando vengono eseguite query sulle colonne. Per impostazione predefinita, le statistiche in SQL Server vengono anche aggiornate automaticamente. In SQL Data Warehouse, invece, le statistiche devono essere create e gestite manualmente.

### Indicazioni

Per generare le statistiche, applicare le indicazioni seguenti:

1. Creare statistiche di singole colonne sulle colonne usate nelle clausole `WHERE`, `JOIN`, `GROUP BY`, `ORDER BY` e `DISTINCT`.
2. Generare statistiche multicolonna su clausole composite.
3. Aggiornare le statistiche periodicamente. Come si ricorderà, questa operazione non viene eseguita automaticamente.

>[AZURE.NOTE]In genere SQL Server Data Warehouse si basa solo su `AUTOSTATS` per mantenere aggiornare le statistiche relative alle colonne. Questa non è una procedura consigliata nemmeno per i data warehouse di SQL Server. Gli oggetti `AUTOSTATS` vengono attivati da una frequenza di modifica del 20%, che risulta insufficiente per le tabelle dei fatti di grandi dimensioni che contengono milioni o miliardi di righe. È quindi sempre consigliabile tenere sotto controllo gli aggiornamenti delle statistiche, per assicurarsi che riflettano accuratamente la cardinalità della tabella.

## Funzionalità non supportate
SQL Data Warehouse non usa o non supporta le funzionalità seguenti:

- chiavi primarie
- chiavi esterne
- vincoli CHECK
- vincoli UNIQUE
- indici univoci
- colonne calcolate
- colonne di tipo sparse
- tipi definiti dall'utente
- viste indicizzate
- identità
- sequenze
- trigger
- sinonimi


## Passaggi successivi
Per altri suggerimenti relativi allo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Panoramica sullo sviluppo per SQL Data Warehouse]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0107_2016-->