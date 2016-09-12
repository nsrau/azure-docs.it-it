<properties
   pageTitle="Distribuzione di tabelle in SQL Data Warehouse | Microsoft Azure"
   description="Introduzione alla distribuzione di tabelle in SQL Data Warehouse di Azure."
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
   ms.date="08/30/2016"
   ms.author="jrj;barbkess;sonyama"/>  

# Distribuzione di tabelle in SQL Data Warehouse

> [AZURE.SELECTOR]
- [Panoramica][]
- [Tipi di dati][]
- [Distribuzione][]
- [Index][]
- [Partition][]
- [Statistiche][]
- [Temporanee][]

SQL Data Warehouse è un sistema di database distribuito a elaborazione parallela massiva. Suddividendo i dati e le funzionalità di elaborazione tra più nodi, SQL Data Warehouse offre grande scalabilità, ben oltre qualsiasi sistema singolo. La distribuzione dei dati all'interno di SQL Data Warehouse è tra i fattori principali che permettono di ottenere prestazioni ottimali. Per ottenere prestazioni ottimali è importante ridurre al minimo lo spostamento dei dati e, per fare ciò, è importante scegliere la giusta strategia di distribuzione.

## Comprendere lo spostamento dei dati

In un sistema MPP i dati di ogni tabella sono suddivisi in più database sottostanti. Le query maggiormente ottimizzate in un sistema MPP possono semplicemente essere passate per l'esecuzione sui singoli database distribuiti, senza alcuna interazione con gli altri database. Ad esempio, si supponga di avere un database con dati di vendita che contiene due tabelle, per vendite e clienti. Se si ha una query che deve creare un join tra la tabella delle vendite con quella dei clienti e si dividono entrambe le tabelle per numero di cliente, inserendo ogni cliente in un database distinto, tutte le query che uniscono vendite e clienti possono essere risolte all'interno di ogni database senza scambio di informazioni con gli altri database. Al contrario, se si dividono i dati di vendita per numero d'ordine e i dati dei clienti per numero di cliente, qualsiasi database specificato non avrà i dati corrispondenti per ogni cliente e quindi se vogliono unire i dati di vendita con quelli dei clienti, sarà necessario ottenere i dati per ogni cliente dagli altri database. Nel secondo esempio lo spostamento dei dati si renderà necessario per spostare i dati dei cliente nei dati di vendita, in modo da consentire l'unione delle due tabelle.

Lo spostamento dei dati non è sempre una procedura negativa, talvolta è necessaria per risolvere una query. Quando tuttavia è possibile evitare questo passaggio supplementare, la query verrà naturalmente eseguita più velocemente. In genere lo spostamento dei dati si verifica quando le tabelle vengono unite in join o vengono eseguite aggregazioni. Spesso è necessario eseguire entrambe le operazioni, quindi anche se si può procedere all'ottimizzazione per uno scenario, ad esempio un join, lo spostamento dei dati è comunque necessario per facilitare la risoluzione per l'altro scenario, ad esempio un'aggregazione. Il trucco è scoprire quale delle due soluzioni è meno impegnativa. Nella maggior parte dei casi, la distribuzione di tabelle dei fatti di grandi dimensioni in una colonna di join comune è il metodo più efficace per ridurre al minimo lo spostamento dei dati. La distribuzione dei dati nelle colonne di join è un metodo molto più comune per ridurre lo spostamento dei dati rispetto alla distribuzione di dati nelle colonne coinvolte in un'aggregazione.

## Selezionare il metodo di distribuzione

Operando in background, SQL Data Warehouse divide i dati in 60 database. Un singolo database è detto **distribuzione**. Quando i dati vengono caricati nelle singole tabelle, SQL Data Warehouse deve sapere come dividerli nelle 60 distribuzioni.

Il metodo di distribuzione viene definito a livello di tabella e attualmente sono disponibili due opzioni:

1. **Round robin**, che distribuisce i dati in modo uniforme ma casuale.
2. **Distribuzione hash**, che distribuisce i dati in base ai valori hash di una singola colonna.

Per impostazione predefinita, quando non si definisce un metodo di distribuzione dei dati, per la tabella viene usato il metodo di distribuzione **round robin**. Tuttavia, man mano che l'implementazione si fa più sofisticata, è consigliabile usare tabelle con **distribuzione hash** per ridurre al minimo lo spostamento dei dati, ottimizzando così le prestazioni delle query.

### Tabelle round robin

Il metodo round robin per la distribuzione dei dati è molto semplice da usare. Man mano che i dati vengono caricati, ogni riga viene semplicemente inviata alla distribuzione successiva. Questo metodo distribuisce sempre i dati in modo casuale e molto uniforme in tutte le distribuzioni. In altre parole, durante il processo round robin che inserisce i dati non viene eseguita alcuna operazione di ordinamento. Per questo motivo, una distribuzione round robin viene a volte definita hash causale. In una tabella con distribuzione round robin non è necessario comprendere i dati. Per questo motivo le tabelle round robin sono spesso un'ottima scelta come destinazioni di caricamento.

Per impostazione predefinita, se non viene scelto un metodo di distribuzione, viene usato il metodo di distribuzione round robin. Tuttavia, se è vero che le tabelle round robin sono facili da usare, perché i dati vengono distribuiti in modo casuale nel sistema, è anche vero che il sistema non può garantire la posizione di ogni riga nella distribuzione. Di conseguenza, a volte il sistema deve richiamare un'operazione di spostamento dei dati per organizzare meglio i dati e poter risolvere una query. Questo passaggio aggiuntivo può rallentare le query.

Si consideri l'uso della distribuzione round robin della tabella negli scenari seguenti:

- Quando si inizia come punto di partenza semplice.
- Se non è presente una chiave di join ovvia.
- Se non è presente una colonna candidata ottimale per la distribuzione hash della tabella.
- Se la tabella non condivide una chiave di join comune con altre tabelle.
- Se il join è meno significativo di altri join nella query.
- Quando si tratta di una tabella di staging temporaneo.

Entrambi gli esempi seguenti creano una tabella round robin:

```SQL
-- Round Robin created by default
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
;

-- Explicitly Created Round Robin Table
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

> [AZURE.NOTE] Anche se la tabella round robin è il tipo predefinito, la procedura consigliata è essere espliciti nella DDL in modo che le intenzioni del layout di tabella siano chiare.

### Tabelle con distribuzione hash

L'uso di un algoritmo di **distribuzione hash** per distribuire le tabelle permette di migliorare le prestazioni in molti scenari riducendo lo spostamento dei dati in fase di query. Le tabelle con distribuzione hash vengono suddivise tra i database distribuiti usando un algoritmo di hash in una singola colonna selezionata dall'utente. La colonna di distribuzione determina il modo in cui i dati vengono divisi tra i database distribuiti. La funzione hash usa la colonna di distribuzione per assegnare righe alle distribuzioni. L'algoritmo di hash e la distribuzione risultante sono deterministici. Vale a dire che per lo stesso valore con lo stesso tipo di dati viene sempre eseguito l'hash alla stessa distribuzione.

Questo esempio permette di creare una tabella distribuita:

```SQL
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
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

## Selezionare la colonna di distribuzione

Per eseguire la **distribuzione hash** di una tabella, è necessario selezionare una singola colonna di distribuzione. Quando si seleziona una colonna di distribuzione occorre prendere in considerazione tre fattori importanti.

Selezionare una singola colonna:

1. da non aggiornare,
2. per distribuire i dati in modo uniforme, evitando asimmetrie,
3. per ridurre al minimo lo spostamento dei dati.

### Selezionare una colonna di distribuzione da non aggiornare

Le colonne di distribuzione non sono aggiornabili. Occorre quindi selezionare una colonna con valori statici. Se una colonna deve essere aggiornata, in generale non è un buon candidato per la distribuzione. Nel caso in cui sia necessario aggiornare una colonna di distribuzione, è possibile procedere prima di tutto eliminando la riga e poi inserendone una nuova.

### Selezionare una colonna di distribuzione per distribuire i dati in modo uniforme

Dal momento che le prestazioni di un sistema distribuito equivalgono a quelle della distribuzione più lenta, è importante dividere il carico di lavoro in modo uniforme tra le distribuzioni per ottenere un'esecuzione bilanciata in tutto il sistema. Il modo in cui il carico lavoro viene diviso in un sistema distribuito dipende da dove si trovano i dati per ogni distribuzione. Per questo motivo è molto importante selezionare la colonna di distribuzione appropriata per distribuire i dati, in modo che ogni distribuzione abbia lo stesso carico di lavoro e impieghi lo stesso tempo a completarlo. Quando lavoro è diviso equamente nel sistema, i dati vengono bilanciati tra le distribuzioni. Quando i dati non sono bilanciati in modo uniforme, si parla di **differenze di dati**.

Per dividere i dati in modo uniforme ed evitare asimmetrie dei dati, selezionare la colonna di distribuzione tenendo presente quanto segue:

1. Selezionare una colonna che contiene un numero significativo di valori distinct.
2. Evitare di distribuire dati in colonne con pochi valori distinti.
3. Evitare di distribuire dati in colonne con una frequenza elevata di valori Null.
4. Evitare di distribuire i dati in colonne data.

Dato che per ogni valore viene eseguito l'hash in 1 delle 60 distribuzioni, per ottenere una distribuzione uniforme è consigliabile selezionare una colonna altamente univoca che contenga più di 60 valori univoci. Per illustrare questo concetto, si consideri il caso di una colonna contenente solo 40 valori univoci. Se la colonna è stata selezionata come chiave di distribuzione, i dati per la tabella potrebbero essere ricevuti su 40 distribuzioni al massimo, lasciando 20 distribuzioni senza dati o elaborazioni da eseguire. Al contrario, le altre 40 distribuzioni avrebbero un carico di lavoro maggiore rispetto a una distribuzione uniforme su 60 distribuzioni. Questo scenario è un esempio di differenza di dati.

Nel sistema MPP, ogni passaggio della query che tutte le distribuzioni completino la loro parte di lavoro. Se una distribuzione svolge più lavoro rispetto alle altre, allora le risorse delle altre distribuzioni vengono essenzialmente sprecate nell'attesa della distribuzione occupata. Quando si lavoro non viene distribuito uniformemente tra tutte le distribuzioni, si parla di **differenza di elaborazioni**. La differenza di elaborazioni causa un rallentamento delle query rispetto a quando un carico di lavoro può essere distribuito uniformemente in tutte le distribuzioni. La differenza di dati porta alla differenza di elaborazioni.

Evitare di distribuire su una colonna con un elevato numero di valori Null, poiché questi verranno ricevuti nella stessa distribuzione. Anche la distribuzione in una colonna di data può causare una differenza di elaborazioni, perché tutti i dati per una determinata data verranno ricevuti nella stessa distribuzione. Se più utenti eseguono query che applicano tutte un filtro alla stessa data, solo 1 delle 60 distribuzioni svolgerà tutto il lavoro, dal momento che una determinata data sarà solo in una distribuzione. In questo scenario le query verranno probabilmente eseguite 60 volte più lentamente rispetto a quando i dati vengono distribuiti uniformemente in tutte le distribuzioni.

Quando non sono disponibili colonne candidate, è consigliabile usare il metodo di distribuzione round robin.

### Selezionare una colonna di distribuzione per ridurre al minimo lo spostamento dei dati

Ridurre al minimo lo spostamento dei dati selezionando la colonna di distribuzione appropriata è una delle strategie principali per l'ottimizzazione delle prestazioni di SQL Data Warehouse. In genere lo spostamento dei dati si verifica quando le tabelle vengono unite in join o vengono eseguite aggregazioni. Tutte le colonne usate nelle clausole `JOIN`, `GROUP BY`, `DISTINCT`, `OVER` e `HAVING` sono candidate **ottimali** per la distribuzione hash.

D'altro canto, le colonne nella clausola `WHERE` **non** sono candidate ottimali come colonne hash, perché limitano le distribuzioni che fanno parte della query, causando una differenza di elaborazioni. Un buon esempio di una colonna che su si potrebbe essere tentati di distribuire, ma che spesso può causare una differenza di elaborazioni è una colonna di data.

In generale, se sono disponibili due tabelle dei fatti di grandi dimensioni, spesso coinvolte in un join, le prestazioni migliori si otterranno con la distribuzione di entrambe le tabelle in una delle colonne di join. Se è presente una tabella che non è mai stata unita a un'altra tabella dei fatti di grandi dimensioni, esaminare le colonne che sono spesso nella clausola `GROUP BY`.

Esistono alcuni criteri fondamentali che devono essere soddisfatti per evitare lo spostamento dei dati durante un join:

1. È necessario eseguire la distribuzione hash delle tabelle coinvolte nel join in **una** delle colonne che fanno parte del join.
2. I tipi di dati delle colonne di join nelle due tabelle devono corrispondere.
3. Le colonne devono essere unite con un operatore Uguale.
4. Il tipo di join può non essere `CROSS JOIN`.


## Risoluzione dei problemi relativi alle asimmetrie dei dati

Quando i dati di una tabella sono distribuiti mediante il metodo di distribuzione hash, alcune distribuzioni presenteranno molti più dati di altre. Un'asimmetria dei dati eccessiva può compromettere le prestazioni delle query, perché per il risultato finale di una query distribuita è necessario aspettare che termini la distribuzione con il tempo di esecuzione più lungo. A seconda del grado di differenza dati potrebbe essere necessario risolvere il problema.

### Identificazione dell'asimmetria dei dati

Un modo semplice per identificare una differenza nelle tabelle consiste nell'uso di `DBCC PDW_SHOWSPACEUSED`. Questo è un metodo molto semplice e rapido per vedere il numero di righe della tabella archiviate in ognuna delle 60 distribuzioni del database. Tenere presente che per ottenere prestazioni più bilanciate, le righe nella tabella distribuita vanno suddivise in modo uniforme in tutte le distribuzioni.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Se tuttavia si esegue una query sulle viste a gestione dinamica di Azure SQL Data Warehouse (DMV) è possibile ottenere un'analisi più dettagliata. Per iniziare, creare la vista [dbo.vTableSizes][] usando il codice SQL fornito nell'articolo [Panoramica delle tabelle][Overview]. Dopo aver creato la vista, eseguire questa query per identificare le tabelle con un'asimmetria dei dati superiore al 10%.

```sql
select *
from dbo.vTableSizes 
where two_part_name in 
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having min(row_count * 1.000)/max(row_count * 1.000) > .10
	)
order by two_part_name, row_count
;
```

### Risoluzione delle asimmetrie di distribuzione

Non tutte le asimmetrie sono sufficienti a giustificare una correzione. In alcuni casi, le prestazioni di una tabella in alcune query possono compensare il danno causato dalla distribuzione asimmetrica. Per decidere se sia necessario risolvere la differenza dati di una tabella, è necessario conoscere nel modo più completo possibile i volumi di dati e le query del carico di lavoro. Per esaminare l'impatto della differenza sulle prestazioni delle query e, in particolare, sul tempo di completamento delle query nelle singole distribuzioni, è possibile seguire la procedura riportata nell'articolo [Monitoraggio delle query][].

La distribuzione è essenzialmente l'individuazione del giusto equilibrio fra la minimizzazione della differenza dati e la minimizzazione dello spostamento dei dati. Questi obiettivi possono essere contrastanti e talvolta può essere necessario tollerare una determinata differenza dati per poter ridurre lo spostamento dei dati. Ad esempio, quando la colonna di distribuzione corrisponde con un'elevata frequenza alla colonna condivisa di join e aggregazioni, si ridurrà al minimo lo spostamento dei dati. Il vantaggio di uno spostamento dei dati minimo potrebbe essere quello di compensare l'impatto negativo della differenza dati.

Il modo più comune per risolvere la differenza dati è ricreare la tabella con una colonna di distribuzione diversa. Dato che non è possibile modificare la colonna di distribuzione in una tabella esistente, per modificare la distribuzione di una tabella occorre crearla nuovamente con [CTAS][]. Di seguito sono riportati due esempi di risoluzione di distribuzioni asimmetriche dei dati:

### Esempio 1: Ricreare la tabella con una nuova colonna di distribuzione

Questo esempio usa [CTAS][] per ricreare una tabella con una colonna di distribuzione hash diversa.

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

### Esempio 2: Ricreare la tabella usando la distribuzione round robin

Questo esempio usa [CTAS][] per ricreare una tabella con una distribuzione round robin anziché una distribuzione hash. Questa modifica produce una distribuzione uniforme dei dati ma aumenta lo spostamento dei dati.

```sql
CREATE TABLE [dbo].[FactInternetSales_ROUND_ROBIN] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  ROUND_ROBIN
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_ROUND_ROBIN')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_ROUND_ROBIN] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_ROUND_ROBIN] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_ROUND_ROBIN] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_ROUND_ROBIN] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_ROUND_ROBIN] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_ROUND_ROBIN] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_ROUND_ROBIN] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_ROUND_ROBIN] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## Passaggi successivi

Per altre informazioni sulla progettazione di tabelle, vedere gli articoli relativi a [distribuzione][], [indice][], [partizione][], [tipi di dati][], [statistiche][] e [tabelle temporanee][Temporary].

Per una panoramica delle procedure consigliate, vedere [Procedure consigliate per SQL Data Warehouse][].


<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Panoramica]: ./sql-data-warehouse-tables-overview.md
[Tipi di dati]: ./sql-data-warehouse-tables-data-types.md
[Distribuzione]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[indice]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[partizione]: ./sql-data-warehouse-tables-partition.md
[Statistiche]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Temporanee]: ./sql-data-warehouse-tables-temporary.md
[Procedure consigliate per SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[Monitoraggio delle query]: ./sql-data-warehouse-manage-monitor.md
[dbo.vTableSizes]: ./sql-data-warehouse-tables-overview.md#querying-table-sizes

<!--MSDN references-->
[DBCC PDW_SHOWSPACEUSED()]: https://msdn.microsoft.com/library/mt204028.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0831_2016-->