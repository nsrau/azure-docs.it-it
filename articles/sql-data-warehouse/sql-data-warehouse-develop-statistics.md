<properties
   pageTitle="Gestire le statistiche in SQL Data Warehouse |Microsoft Azure"
   description="Suggerimenti per la gestione delle statistiche in Azure SQL Data Warehouse per lo sviluppo di soluzioni."
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
   ms.date="09/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Gestire le statistiche in SQL Data Warehouse
 SQL Data Warehouse usa le statistiche per valutare il costo dei diversi modi di eseguire una query distribuita. Se le statistiche sono precise, Query Optimizer può generare piani di query di qualità elevata, che migliorano le prestazioni delle query.

La creazione e l'aggiornamento delle statistiche è importante per ottenere le prestazioni delle query che SQL Data Warehouse è in grado di offrire. Questa Guida fornisce una panoramica delle statistiche e illustra come eseguire le operazioni seguenti:

- Creare statistiche come parte della progettazione dei database
- Aggiornare le statistiche come parte della manutenzione dei database
- Visualizzare le statistiche con visualizzazioni di sistema e funzioni

## Introduzione alle statistiche

Le statistiche a colonna singola sono oggetti che includono informazioni sull'intervallo e sulla frequenza di valori in una singola colonna. Query Optimizer usa questo istogramma per valutare il numero di righe nei risultati delle query. Ciò influisce direttamente sulle decisioni relative al modo in cui ottimizzare la query.

Le statistiche a più colonne sono statistiche create su un elenco di colonne. Includono statistiche a colonna singola nella prima colonna dell'elenco, oltre ad alcune informazioni di correlazione tra colonne definite densità. Le statistiche a più colonne possono migliorare le prestazioni delle query per alcune operazioni, ad esempio nelle clausole JOIN composite e GROUP BY.

Per altri dettagli, vedere [DBCC SHOW\_STATISTICS][] su MSDN.

## Importanza delle statistiche
Senza statistiche appropriate, non sarà possibile ottenere le prestazioni che SQL Data Warehouse è in grado di offrire. SQL Data Warehouse non genera automaticamente statistiche per le tabelle e le colonne, quindi sarà necessario crearle manualmente. È consigliabile creale quando si creano le tabelle e quindi aggiornarle dopo averle popolate.

> [AZURE.NOTE]Se si usa SQL Server, è possibile che SQL Server crei e aggiorni automaticamente le statistiche a colonna singola in base alla necessità. Da questo punto di vista, SQL Data Warehouse è diverso. Poiché i dati sono distribuiti, SQL Data Warehouse non aggrega automaticamente le statistiche in tutti i dati distribuiti. Genererà le statistiche aggregate solo quando si creano e aggiornano le statistiche.

## Quando creare le statistiche
Un set coerente di statistiche aggiornate è una parte importante di SQL Data Warehouse. È quindi essenziale creare statistiche come parte della progettazione delle tabelle.

La creazione di statistiche a colonna singola su ogni colonna è un modo semplice per iniziare a usare le statistiche. Sono tuttavia sempre necessari compromessi tra le prestazioni e il costo di creazione e aggiornamento delle statistiche. Se si creano statistiche a colonna singola su tutte le colonne e in seguito si nota che l'aggiornamento di tutte le statistiche richiede troppo tempo, è sempre possibile eliminare alcune statistiche o aggiornarne alcune meno spesso rispetto ad altre.

Le statistiche a più colonne vengono usate da Query Optimizer solo quando le colonne si trovano nelle clausole JOIN composite o GROUP BY. I filtri compositi non traggono attualmente alcun vantaggio dalle statistiche a più colonne.

Quando si avvia lo sviluppo per SQL Data Warehouse è quindi consigliabile implementare il modello seguente: - Creare statistiche a colonna singola su ogni colonna in ogni tabella - Creare statistiche a più colonne sulle colonne usate dalle query nelle clausole JOIN e GROUP BY.

Dopo avere stabilito come si vogliono eseguire query sui dati, sarà possibile perfezionare questo modello, in particolare se le tabelle sono di dimensioni elevate. Per informazioni su un approccio più avanzato, vedere la sezione [Implementazione della gestione delle statistiche](## Implementing statistics management).

## Quando aggiornare le statistiche
È importante includere l'aggiornamento delle statistiche nella routine di gestione del database. Quando la distribuzione dei dati nel database subisce modifiche, è necessario aggiornare le statistiche. In caso contrario, è possibile che le prestazioni delle query non siano ottimali e il lavoro necessario per un'ulteriore risoluzione dei problemi della query sia inutile.

Quando si risolvono i problemi di una query è quindi essenziale verificare prima di tutto se le statistiche sono aggiornate.

Questa verifica non può essere basata sulla data di creazione delle statistiche. Un oggetto statistiche aggiornato può essere molto vecchio. È necessario aggiornare le statistiche *quando* vengono apportate modifiche al numero di righe o modifiche materiali alla distribuzione dei valori per una colonna specifica.

Ad esempio, le colonne di data in un data warehouse necessitano solitamente di aggiornamenti frequenti delle statistiche. Ogni volta che vengono caricate nuove righe nel data warehouse, vengono aggiunte nuove date di caricamento o date di transazione. Queste righe modificano la distribuzione dei dati e rendono non aggiornate le statistiche.

Al contrario, è possibile che non sia mai necessario aggiornare le statistiche relative alla colonna del sesso in una tabella clienti. Supponendo che la distribuzione sia costante tra i clienti, l'aggiunta di nuove righe alla variazione di tabella non modificherà la distribuzione dei dati. Se tuttavia il data warehouse contiene solo un sesso e uno nuovo requisito ha come risultato più sessi, sarà decisamente necessario aggiornare le statistiche relative alla colonna del sesso.

Per altre informazioni, vedere [Statistiche][] su MSDN.

## Implementazione della gestione delle statistiche

È spesso consigliabile estendere il processo di caricamento dei dati per assicurare che le statistiche vengano aggiornate al termine del caricamento. Il caricamento dei dati è la fase in cui si verifica con maggiore frequenza una modifica delle dimensioni e/o della distribuzione dei valori delle tabelle. Questa è quindi una posizione logica per implementare alcuni processi di gestione.

Di seguito sono disponibili alcuni principi guida per l'aggiornamento delle statistiche durante il processo di caricamento:

- Assicurarsi che ogni tabella caricata includa almeno un oggetto statistiche aggiornato. Ciò permette di aggiornare le informazioni sulle dimensioni delle tabelle (conteggio delle righe e conteggio delle pagine) come parte dell'aggiornamento delle statistiche.
- Concentrarsi sulle colonne incluse nelle clausole JOIN, GROUP BY, ORDER BY e DISTINCT.
- Prendere in considerazione una maggiore frequenza per l'aggiornamento delle colonne di tipo "parola chiave Ascending", ad esempio le date delle transazioni, poiché questi valori non verranno inclusi nell'istogramma delle statistiche.
- Prendere in considerazione una minore frequenza per l'aggiornamento delle colonne relative alla distribuzione statica.
- Occorre ricordare che ogni oggetto statistiche viene aggiornato in serie. La semplice implementazione di `UPDATE STATISTICS <TABLE_NAME>` potrebbe non essere ottimale, in particolare per tabelle di grandi dimensioni con molti oggetti statistici.

> [AZURE.NOTE]Per altre informazioni sulla [parola chiave Ascending], vedere il white paper sul modello di stima della cardinalità di SQL Server 2014.

Per altre informazioni, vedere [Stima della cardinalità][] su MSDN.

## Esempi: Creare le statistiche

Questi esempi illustrano come usare diverse opzioni per la creazione delle statistiche. Le opzioni usate per ogni colonna dipendono dalle caratteristiche dei dati e dal modo in cui la colonna verrà usata nelle query.

### A. Creare statistiche a colonna singola con opzioni predefinite

Per creare statistiche su una colonna, è sufficiente fornire un nome per l'oggetto statistiche e il nome della colonna.

Questa sintassi usa tutte le opzioni predefinite. Per impostazione predefinita, SQL Data Warehouse esegue il campionamento del 20% della tabella quando crea le statistiche.

```
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Ad esempio:

```
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### B. Creare statistiche a colonna singola esaminando ogni riga

La frequenza di campionamento del 20% è sufficiente per la maggior parte delle situazioni. È tuttavia possibile modificare la frequenza di campionamento.

Per eseguire il campionamento dell'intera tabella, usare la sintassi seguente:

```
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Ad esempio:

```
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### C. Creare statistiche a colonna singola specificando le dimensioni del campione

In alternativa, è possibile specificare le dimensioni del campione sotto forma di percentuale:

```
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### D. Creare statistiche a colonna singola solo su alcune righe

È anche possibile creare statistiche su una parte delle righe della tabella. Questa opzione è definita statistica filtrata.

Ad esempio, è possibile usare le statistiche filtrate quando si prevede di eseguire una query in una partizione specifica di una tabella partizionata di grandi dimensioni. Se si creano statistiche solo sui valori della partizione, la precisione delle statistiche migliorerà e miglioreranno quindi le prestazioni delle query.

Questo esempio crea statistiche su un intervallo di valori. È possibile definire con facilità i valori in modo che corrispondano all'intervallo di valori in una partizione.

```
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [AZURE.NOTE]Per fare in modo che Query Optimizer prenda in considerazione l'uso delle statistiche filtrate quando sceglie il piano di query distribuite, è necessario che la query rientri nella definizione dell'oggetto statistiche. Usando l'esempio precedente, la clausola where della query deve specificare valori col1 compresi tra 2000101 e 20001231.

### E. Creare statistiche a colonna singola con tutte le opzioni

È ovviamente possibile combinare tutte le opzioni. L'esempio seguente crea un oggetto statistiche filtrato con una dimensione di campionamento personalizzata:

```
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Per i riferimenti completi, vedere [CREATE STATISTICS][] su MSDN.

### F. Creare statistiche a più colonne

Per creare statistiche a più colonne, è sufficiente usare gli esempi precedenti ma specificare più colonne.

> [AZURE.NOTE]L'istogramma, che viene usato per stimare il numero di righe nei risultati delle query, sarà disponibile solo per la prima colonna elencata nella definizione dell'oggetto statistiche.

In questo esempio l'istogramma è disponibile su *product\_category*. Le statistiche tra le colonne vengono calcolate su *product\_category* e *product\_sub\_c\\ategory*:

```
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Poiché è presente una correlazione tra *product\_category* e *product\_sub\_category*, una statistica a più colonne può essere utile se si accede contemporaneamente a queste colonne.

### G. Creare statistiche su tutte le colonne in una tabella

Un modo per creare le statistiche consiste nell'emettere comandi CREATE STATISTICS dopo la creazione della tabella.

```
CREATE TABLE dbo.table1 
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### H. Usare una stored procedure per creare statistiche su tutte le colonne in un database

SQL Data Warehouse non include una stored procedure di sistema equivalente a [sp\_create\_stats][] in SQL Server. Questa stored procedure crea un oggetto statistiche a colonna singola su ogni colonna del database che non include già statistiche.

Ciò permetterà di iniziare a progettare il database. È possibile adattare l'operazione alle proprie esigenze.

```
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
WHERE       l.[object_id] IS NULL
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    'CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')'
        WHEN 2
        THEN    'CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN'
        WHEN 3
        THEN    'CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+@sample_pct+'PERCENT'
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Per creare statistiche su tutte le colonne della tabella con questa procedura, è sufficiente chiamarla.

```
prc_sqldw_create_stats;
```

## Esempi: Aggiornare le statistiche

Per aggiornare le statistiche, è possibile eseguire le operazioni seguenti:

1. Aggiornare un oggetto statistiche. Specificare il nome dell'oggetto statistiche da aggiornare.
2. Aggiornare tutti gli oggetti statistiche in una tabella. Specificare il nome della tabella invece di un oggetto statistiche specifico.


### A. Aggiornare un oggetto statistiche specifico ###
Usare la sintassi seguente per aggiornare un oggetto statistiche specifico:

```
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Ad esempio:

```
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

L'aggiornamento di oggetti statistiche specifici permette di ridurre al minimo il tempo e le risorse necessari per gestire le statistiche. È tuttavia necessario scegliere con attenzione gli oggetti statistiche migliori da aggiornare.


### B. Aggiornare tutte le statistiche in una tabella ###
Illustra un semplice metodo di aggiornamento di tutti gli oggetti statistiche in una tabella.

```
UPDATE STATISTICS [schema_name].[table_name];
```

Ad esempio:

```
UPDATE STATISTICS dbo.table1;
```

Questa istruzione è facile da usare. Occorre ricordare che aggiorna tutte le statistiche nella tabella e che quindi potrebbe eseguire più lavoro del necessario. Se le prestazioni non sono un problema, questo è decisamente il modo più semplice e più completo per assicurare che le statistiche siano aggiornate.

> [AZURE.NOTE]Quando si aggiornano tutte le statistiche in una tabella, SQL Data Warehouse esegue un'analisi per campionare la tabella per ogni statistica. Se la tabella è grande, include molte colonne e molte statistiche, potrebbe risultare più efficiente aggiornare le singole statistiche in base alla necessità.

Per l'implementazione di una procedura `UPDATE STATISTICS`, vedere l'articolo relativo alle [tabelle temporanee]. Il metodo di implementazione è leggermente diverso rispetto alla procedura `CREATE STATISTICS` precedente, ma il risultato finale è uguale.

Per la sintassi completa, vedere [UPDATE STATISTICS][] su MSDN.

## Metadati delle statistiche
Sono disponibili alcune visualizzazioni di sistema e funzioni che permettono di trovare informazioni sulle statistiche. Ad esempio, è possibile verificare se un oggetto statistiche non è aggiornato usando la funzione stats-date per vedere la data di creazione o dell'ultimo aggiornamento delle statistiche.

### Viste del catalogo per le statistiche
Queste visualizzazioni di sistema forniscono informazioni sulle statistiche:

| Vista del catalogo | Descrizione |
| :----------- | :---------- |
| [sys.columns][] | Una riga per ogni colonna. |
| [sys.objects][] | Una riga per ogni oggetto del database. | |
| [sys.schemas][] | Una riga per ogni schema del database. | |
| [sys.stats][] | Una riga per ogni oggetto statistiche. |
| [sys.stats\_columns][] | Una riga per ogni colonna nell'oggetto statistiche. Si collega a sys.columns. |
| [sys.tables][] | Una riga per ogni tabella (include le tabelle esterne). |
| [sys.table\_types][] | Una riga per ogni tipo di dati. |


### Funzioni di sistema per le statistiche
Queste funzioni di sistema sono utili per usare le statistiche:

| Funzioni di sistema | Descrizione |
| :-------------- | :---------- |
| [STATS\_DATE][] | Data dell'ultimo aggiornamento dell'oggetto statistiche. |
| [DBCC SHOW\_STATISTICS][] | Fornisce informazioni a livello di riepilogo e dettagliate sulla distribuzione di valori riconosciute dall'oggetto statistiche. |

### Combinare le colonne delle statistiche e le funzioni in un'unica visualizzazione

Questa visualizzazione riunisce le colonne relative alle statistiche e i risultati della funzione [STATS\_DATE()][].

```
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1 
AND     sts.[user_created] = 1
;
```

## Esempi di DBCC SHOW\_STATISTICS()

DBCC SHOW\_STATISTICS() mostra i dati inclusi in un oggetto statistiche. Questi dati sono costituiti da tre parti.

1. Intestazione
2. Vettore di densità
3. Istogramma

Metadati di intestazione sulle statistiche. L'istogramma mostra la distribuzione dei valori nella prima colonna chiave dell'oggetto statistiche. Il vettore di densità misura la correlazione tra le colonne. SQL Data Warehouse calcola le stime di cardinalità con tutti i dati nell'oggetto statistiche.

### Mostrare l'intestazione, la densità e l'istogramma

Questo semplice esempio mostra tutte e tre le parti di un oggetto statistiche.

```
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Ad esempio:

```
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### Mostrare una o più parti di DBCC SHOW\_STATISTICS();

Se si è interessati a visualizzare solo parti specifiche, usare la clausola `WITH` e specificare le parti da visualizzare:

```
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Ad esempio:

```
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## Differenze di DBCC SHOW\_STATISTICS()
DBCC SHOW\_STATISTICS() viene implementato in modo più rigoroso in SQL Data Warehouse rispetto a SQL Server.

1. Le funzionalità non documentate non sono supportate
- Non è possibile usare Stats\_stream
- Non è possibile unire i risultati per sottoinsiemi specifici di dati statistici, ad esempio (STAT\_HEADER JOIN DENSITY\_VECTOR)
2. NO\_INFOMSGS non può essere impostato per l'eliminazione del messaggio
3. Non è possibile usare le parentesi quadre per i nomi delle statistiche
4. Non è possibile usare i nomi di colonna per identificare gli oggetti statistiche
5. L'errore personalizzato 2767 non è supportato


## Passaggi successivi
Per altri suggerimenti sullo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][].

<!--Image references-->

<!--Link references--In actual articles, you only need a single period before the slash.-->
[Panoramica sullo sviluppo per SQL Data Warehouse]: ./sql-data-warehouse-overview-develop/
[tabelle temporanee]: ./sql-data-warehouse-develop-temporary-tables/

<!-- External Links -->
[Stima della cardinalità]: https://msdn.microsoft.com/library/dn600374.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC SHOW\_STATISTICS]: https://msdn.microsoft.com/library/ms174384.aspx
[Statistiche]: https://msdn.microsoft.com/library/ms190397.aspx
[STATS\_DATE]: https://msdn.microsoft.com/library/ms190330.aspx
[sys.columns]: https://msdn.microsoft.com/library/ms176106.aspx
[sys.objects]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.schemas]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.stats]: https://msdn.microsoft.com/library/ms177623.aspx
[sys.stats\_columns]: https://msdn.microsoft.com/library/ms187340.aspx
[sys.tables]: https://msdn.microsoft.com/library/ms187406.aspx
[sys.table\_types]: https://msdn.microsoft.com/library/bb510623.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx

<!---HONumber=Sept15_HO4-->