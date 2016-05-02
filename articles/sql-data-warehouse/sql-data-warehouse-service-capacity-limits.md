<properties
   pageTitle="Limiti di capacità di SQL Data Warehouse | Microsoft Azure"
   description="I valori massimi per i database, le tabelle, le connessioni e le query per SQL Data Warehouse."
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
   ms.date="04/20/2016"
   ms.author="sonyama;barbkess;jrj"/>

# Limiti di capacità di SQL Data Warehouse

Valori massimi per supportare i carichi di lavoro analitici più impegnativi, assicurando al tempo stesso a ogni singola query la disponibilità delle risorse necessarie per prestazioni ottimali.

## Servizio

| Categoria | Descrizione | Massima |
| :---------------- | :------------------------------------------- | :----------------- |
| Database | Dimensione massima | 60 TB compressi<br/><br/>SQL Data Warehouse consente di avere fino a 60 TB di spazio su disco per database. Lo spazio su disco corrisponde alla dimensione compressa per le tabelle permanenti. Questo spazio è indipendente dallo spazio di tempdb o del log ed è dedicato alle tabelle permanenti. La compressione columnstore cluster è stimata a 5x, ovvero la dimensione non compressa del database potrebbe crescere a circa 300 TB quando tutte le tabella sono columnstore cluster (il tipo di tabella predefinito). Il limite di 60 TB diventerà di 240 TB alla fine della fase di anteprima pubblica e questo dovrebbe consentire a molti database di crescere fino a oltre 1 PB di dati non compressi.|
| Database | Sessioni simultanee aperte | 1024<br/><br/>È supportato un massimo di 1024 connessioni attive che possono inviare richieste a ogni database SQL Data Warehouse contemporaneamente. Si noti che vi sono dei limiti previsti per il numero di query effettivamente eseguibili in contemporanea. Quando un limite viene superato, la richiesta viene inviata a una coda interna in cui resta in attesa di elaborazione.|
| Connessione del database | Memoria massima per le istruzioni preparate | 20 MB |
| Gestione del carico di lavoro | Numero massimo di query simultanee | 32<br/><br/> SQL Data Warehouse offre 32 unità di concorrenza chiamate slot di concorrenza.<br/><br/>Se tutte le query vengono eseguite con l'allocazione delle risorse predefinita di uno slot di concorrenza, è possibile avere 32 query utente simultanee. In pratica, il numero massimo di query simultanee dipende dall'obiettivo del livello di servizio (SLO) e dai requisiti di risorse per ogni query. Quando non sono disponibili risorse, le query vengono messe in attesa in una coda interna. Per altre informazioni, vedere [Gestione della concorrenza e del carico di lavoro][].|
| Gestione del carico di lavoro | Numero massimo di slot di concorrenza per obiettivo del livello di servizio (SLO) |Questo è il numero di slot di concorrenza che ogni livello di servizio può usare per eseguire query che richiedono ulteriori risorse di CPU e memoria. Per la gestione del carico di lavoro, è possibile utilizzare le classi di risorse integrate per incrementare le risorse di CPU e memoria per una query. L'esecuzione con più risorse comporta che la query richieda più slot di concorrenza.<br/><br/>Alcune query non vengono eseguite nell'ambito di classi di risorse. Tali query usano una sola unità di concorrenza e non consumano nessuno degli slot di concorrenza elencati di seguito. Per un elenco delle query eseguite (e non eseguite) da SQL Data Warehouse all'interno di classi di risorse, vedere [Gestione della concorrenza e del carico di lavoro in SQL Data Warehouse][].<br/><br/>DWU100 = 4<br/><br/>DWU200 = 8<br/><br/>DWU300 = 12<br/><br/>DWU400 = 16<br/><br/>DWU500 = 20<br/><br/>DWU600 = 24<br/><br/>DWU1000 = 40<br/><br/>DWU1200 = 48<br/><br/>DWU1500 = 60 |


## Oggetti di database

| Categoria | Descrizione | Massima |
| :---------------- | :------------------------------------------- | :----------------- |
| Tabella | Dimensioni massime | 60 TB compressi su disco |
| Tabella | Tabelle per database | 2 miliardi |
| Tabella | Colonne per tabella | 1024 colonne |
| Tabella | Byte per colonna | 8000 byte |
| Tabella | Byte per riga, dimensioni definite | 8060 byte<br/><br/>Il numero di byte per riga viene calcolato come per SQL Server, con la compressione pagina attivata. Come SQL Server, SQL Data Warehouse supporta l'archiviazione di dati di overflow della riga che consente di spostare colonne a lunghezza variabile all'esterno delle righe. Nel record principale viene archiviata solo una radice di 24 byte per le colonne di lunghezza variabile spostate all'esterno delle righe. Per altre informazioni, vedere l'articolo dedicato ai [dati di overflow della riga che superano gli 8 KB](https://msdn.microsoft.com/library/ms186981.aspx) nella documentazione online di SQL Server.<br/><br/>Per un elenco delle dimensioni dei tipi di dati di SQL Data Warehouse, vedere l'articolo dedicato a [CREATE TABLE in Azure SQL Data Warehouse](https://msdn.microsoft.com/library/mt203953.aspx). |
| Table | Byte per riga, dimensioni del buffer interno per lo spostamento dei dati | 32\.768<br/><br/>NOTA: questo limite è attualmente presente ma verrà presto rimosso.<br/><br/>SQL Data Warehouse usa un buffer interno per spostare le righe all'interno del sistema SQL Data Warehouse distribuito. Il servizio che sposta le righe è denominato DMS (Data Movement Service) e archivia le righe in un formato diverso da SQL Server.<br/><br/>Se una riga non rientra nel buffer interno si otterrà un errore di compilazione di query o un errore interno di spostamento dei dati. Per evitare questo problema, vedere [Informazioni dettagliate sulle dimensioni del buffer DMS](#details-about-the-dms-buffer-size).|
| Table | Partizioni per tabella | 15\.000<br/><br/>Per ottenere prestazioni elevate è consigliabile ridurre al minimo il numero di partizioni necessarie pur supportando i requisiti aziendali. Con l'aumentare del numero di partizioni, l'overhead per le operazioni DDL (Data Definition Language) e DML (Data Manipulation Language ) aumenta e le prestazioni rallentano.|
| Table | Caratteri per valore limite della partizione.| 4000 |
| Indice | Indici non in cluster per tabella. | 999<br/><br/>Si applica solo alle tabelle rowstore.|
| Indice | Indici in cluster per tabella. | 1<br><br/>Si applica sia alle tabelle rowstore che columnstore.|
| Indice | Righe in un gruppo righe dell'indice columnstore | 1024<br/><br/>Ogni indice columnstore è implementato come più indici columnstore. Si noti che se si inseriscono 1024 righe in un indice columnstore SQL Data Warehouse, non tutte le righe andranno nello stesso gruppo righe.|
| Indice | Compilazioni simultanee di indici columnstore in cluster. | 32<br/><br/>Si applica quando gli indici columnstore in cluster sono tutti compilati in tabelle diverse. È consentita solo una compilazione dell'indice columnstore in cluster per tabella. Le richieste aggiuntive resteranno in coda.|
| Indice | Dimensioni della chiave indice. | 900 byte.<br/><br/>Si applica solo agli indici rowstore.<br/><br/>È possibile creare indici in colonne varchar con una dimensione massima di oltre 900 byte se i dati esistenti nelle colonne non superano i 900 byte quando viene creato l'indice. Tuttavia, le azioni INSERT o UPDATE successive eseguite nelle colonne che causano un aumento delle dimensioni totali oltre i 900 byte avranno esito negativo.|
| Indice | Colonne chiave per indice. | 16<br/><br/>Si applica solo agli indici rowstore. Gli indici columnstore in cluster includono tutte le colonne.|
| Statistiche | Dimensione dei valori combinati delle colonne. | 900 byte. |
| Statistiche | Colonne per oggetto statistiche. | 32 |
| Statistiche | Statistiche create per le colonne per tabella. | 30\.000 |
| Stored procedure | Livello massimo di annidamento. | 8 |
| View | Colonne per vista | 1\.024 |


## Query

| Categoria | Descrizione | Massima |
| :---------------- | :------------------------------------------- | :----------------- |
| Query | Query in coda nelle tabelle utente | 1000 |
| Query | Query simultanee nelle viste di sistema | 100 |
| Query | Query in coda nelle viste di sistema | 1000 |
| Query | Parametri massimi | 2098 |
| Batch | Dimensione massima | 65\.536*4096 |
| Risultati SELECT | Colonne per riga | 4096<br/><br/>Non è possibile avere più di 4096 colonne per riga nel risultato di SELECT. Non è garantito che si possa avere sempre 4096. Se il piano di query richiede una tabella temporanea, potrebbe venire applicato il valore massimo di 1024 colonne per tabella.|
| SELECT | Sottoquery nidificate | 32<br/><br/>Non è possibile avere più di 32 sottoquery nidificate in un'istruzione SELECT. Non è garantito che si possa averne sempre 32. Ad esempio, un JOIN può introdurre una sottoquery nel piano di query. Il numero di sottoquery può essere limitato anche dalla memoria disponibile.|
| SELECT | Colonne per JOIN | 1024 colonne<br/><br/>Non è possibile avere più di 1024 colonne nel JOIN. Non è garantito che si possa averne sempre 1024. Se il piano JOIN richiede una tabella temporanea con più colonne del risultato JOIN, il limite di 1024 viene applicato alla tabella temporanea. |
| SELECT | Byte per le colonne GROUP BY. | 8060<br/><br/>Le colonne presenti nella clausola GROUP BY possono avere un massimo di 8060 byte.|
| SELECT | Byte per le colonne ORDER BY | 8060 byte<br/><br/>Le colonne presenti nella clausola ORDER BY possono avere un massimo di 8060 byte.|
| Identificatori e costanti per istruzione | Numero di identificatori e costanti di riferimento. | 65\.535<br/><br/>SQL Data Warehouse limita il numero di identificatori e costanti che possono essere contenuti nella singola espressione di una query. Il limite è 65.535. Il superamento di questo numero genera un errore 8632 di SQL Server. Per altre informazioni, vedere [Messaggio di errore quando si esegue una query in SQL Server 2005: "Errore interno: è stato raggiunto un limite di servizi di espressione"](http://support.microsoft.com/kb/913050/).|



## Metadata

| Vista di sistema | Righe massime |
| :--------------------------------- | :------------|
| sys.dm\_pdw\_component\_health\_alerts | 10\.000 |
| sys.dm\_pdw\_dms\_cores | 100 |
| sys.dm\_pdw\_dms\_workers | Numero totale di ruoli di lavoro DMS per le 1000 richieste SQL più recenti. |
| sys.dm\_pdw\_errors | 10\.000 |
| sys.dm\_pdw\_exec\_requests | 10\.000 |
| sys.dm\_pdw\_exec\_sessions | 10\.000 |
| sys.dm\_pdw\_request\_steps | Numero totale di passaggi per le 1000 richieste SQL più recenti archiviate in sys.dm\_pdw\_exec\_requests. |
| sys.dm\_pdw\_os\_event\_logs | 10\.000 |
| sys.dm\_pdw\_sql\_requests | Le 1000 richieste SQL più recenti archiviate in sys.dm\_pdw\_exec\_requests. |


## Informazioni dettagliate sulle dimensioni del buffer DMS

SQL Data Warehouse usa un buffer interno per spostare le righe tra i nodi di calcolo di back-end. Il servizio che sposta le righe è denominato DMS (Data Movement Service) e usa un formato di archiviazione diverso da SQL Server.

Per migliorare le prestazioni delle query parallele, DMS riempie tutti i dati di lunghezza variabile in base alla dimensione massima definita del database SQL. Ad esempio, il valore "hello" per un elemento `nvarchar(2000) NOT NULL` userà effettivamente 4002 byte nel buffer DMS. Usa 2 byte per ognuno dei 2000 caratteri più 2 byte per la terminazione NULL.

> [AZURE.NOTE] Si verifica un errore interno quando DMS tenta di spostare una riga che supera le dimensioni del buffer DMS di 32.768 byte. Se le dimensioni della riga superano le dimensioni del buffer DMS, è necessario modificare la definizione della tabella in modo che la riga entri nel buffer DMS.

### Come determinare le dimensioni delle righe per il buffer DMS
Questo esempio descrive le dimensioni DMS definite dei dati a lunghezza variabile e quindi illustra come calcolare le dimensioni del buffer DMS per una riga.
 
Nel buffer DMS la dimensione dei dati a lunghezza variabile è la somma degli elementi seguenti:

- Dimensione definita per i caratteri.
- I tipi NULL usano 8 byte per l'indicatore NULL.
- I tipi ASCII usano 1 carattere per il terminatore NULL.
- I tipi Unicode usano 2 caratteri per il terminatore NULL.
             
| Tipo di dati | Dimensioni del buffer DMS |
| :---------------------- | :-------------------------- |                
| char(1000) NULL | 1009 byte = 1000 + 8 + 1 |
| char(1000) NOT NULL | 1001 byte = 1000 + 1 |
| nchar(1000 NULL | 2010 byte = 2000 + 8 + 2 |
| varchar(1000) NULL | 1009 byte = 1000 + 8 + 1 |
| varchar(1000) NOT NULL | 1009 byte = 1000 + 1 |
| nvarchar(1000) NULL | 2010 byte = 2000 + 8 + 2 |
| nvarchar(1000) NOT NULL | 2010 byte = 2000 + 2 |
                
Nel buffer DMS, le colonne a larghezza fissa usano la dimensione nativa di SQL Server. Se il tipo è nullable, DMS richiede 8 byte aggiuntivi. Per le dimensioni di SQL Server, vedere il campo max\_length in sys.types.

Ad esempio:

| Tipo di dati a larghezza fissa | Dimensioni del buffer DMS |
| :-------------------- | :----------------- |
| int NULL | 12 byte = 4 + 8 |
| int NOT NULL | 4 byte = 4 + 0 | 
                
Nel complesso, la dimensione definita del buffer DMS per la riga seguente è 31.134 byte, un valore accettabile per il buffer DMS.

| Colonna | Tipo di dati | Dimensioni colonna |
| :----- | :------------------ | :------------------------ |
| col1 | datetime2 (7) NULL | 20 byte = 8 + 8 |
| col2 | float (4) NULL | 12 byte = 4 + 8 |
| col3 | nchar (6) NULL | 22 byte = 12 + 8 + 2 |
| col4 | char(7000) NULL | 7009 byte = 7000 + 8 + 1 |
| col5 | nvarchar (4000) | 8002 byte = 8000 + 2 |
| col6 | varchar (8000) NULL | 8009 byte = 8000 + 8 + 1 |
| col7 | varbinary (8000) | 8000 byte |
| col8 | binary (60) | 60 byte |
                
              
### Esempio di superamento delle dimensioni del buffer DMS

Questo esempio illustra come inserire correttamente una riga in SQL Data Warehouse, ma con un errore di overflow DMS quando DMS deve spostare la riga per un join di distribuzione non compatibile. Lo scopo è insegnare a creare righe di dimensioni inferiori che il buffer DMS può accettare.

Nell'esempio seguente viene creata la tabella T1. Le dimensioni massime consentite della riga quando tutte le variabili nvarchar hanno 4000 caratteri Unicode sono superiori a 40.000 byte, un valore che supera la dimensione massima del buffer DMS.

Poiché le dimensioni definite effettive di un nvarchar usano 26 byte, la definizione della riga è minore di 8060 byte e può essere contenuta in una pagina di SQL Server. Per questo motivo, l'istruzione CREATE TABLE ha esito positivo, anche se DMS restituisce un errore quando prova a caricare questa riga nel buffer DMS.

```sql
CREATE TABLE T1
  (
    c0 int NOT NULL,
    CustomerKey int NOT NULL,
    c1 nvarchar(4000),
    c2 nvarchar(4000),
    c3 nvarchar(4000),
    c4 nvarchar(4000),
    c5 nvarchar(4000)
  )
WITH ( DISTRIBUTION = HASH (c0) )
;
```

Il passaggio successivo mostra come usare correttamente INSERT per inserire i dati nella tabella. Questa istruzione carica i dati direttamente in SQL Server senza usare DMS e quindi non causa un errore di overflow del buffer DMS. Anche i servizi di integrazione caricano correttamente questa riga.</para>

```sql
--The INSERT operation succeeds because the row is inserted directly into SQL Server without requiring DMS to buffer the row.
INSERT INTO T1
VALUES (
    25,
    429817,
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.'
  )
```

Per prepararsi per la dimostrazione dello spostamento di dati, in questo esempio viene creata una seconda tabella con CustomerKey per la colonna di distribuzione.

```sql
--This second table is distributed on CustomerKey. 
CREATE TABLE T2
  (
    c0 int NOT NULL,
    CustomerKey int NOT NULL,
    c1 nvarchar(4000),
    c2 nvarchar(4000),
    c3 nvarchar(4000),
    c4 nvarchar(4000),
    c5 nvarchar(4000)
  )
WITH ( DISTRIBUTION = HASH (CustomerKey) )
;

INSERT INTO T2
VALUES (
    32,
    429817,
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.'
  )
```
Poiché entrambe le tabelle non sono distribuite in CustomerKey, un join tra T1 e T2 in CustomerKey non è compatibile con la distribuzione. DMS deve caricare almeno una riga e copiarla in una distribuzione diversa.

```sql
SELECT * FROM T1 JOIN T2 ON T1.CustomerKey = T2.CustomerKey;
```

Come previsto, DMS non è in grado di eseguire il join perché la riga, quando vengono riempite tutte le colonne nvarchar, supera la dimensione di 32.768 byte del buffer DMS. Viene visualizzato il messaggio di errore seguente.

```sql
Msg 110802, Level 16, State 1, Line 126

An internal DMS error occurred that caused this operation to fail. Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Workers.DmsSqlNativeException, Message: SqlNativeBufferReader.ReadBuffer, error in OdbcReadBuffer: SqlState: , NativeError: 0, 'COdbcReadConnection::ReadBuffer: not enough buffer space for one row | Error calling: pReadConn-&gt;ReadBuffer(pBuffer, bufferOffset, bufferLength, pBytesRead, pRowsRead) | state: FFFF, number: 81, active connections: 8', Connection String: Driver={SQL Server Native Client 11.0};APP=DmsNativeReader:P13521-CMP02\sqldwdms (4556) - ODBC;Trusted_Connection=yes;AutoTranslate=no;Server=P13521-SQLCMP02,1500
```


## Passaggi successivi
Per altre informazioni di riferimento, vedere la [panoramica degli argomenti di riferimento di SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[panoramica degli argomenti di riferimento di SQL Data Warehouse]: sql-data-warehouse-overview-reference.md
[Gestione della concorrenza e del carico di lavoro]: sql-data-warehouse-develop-concurrency.md
[Gestione della concorrenza e del carico di lavoro in SQL Data Warehouse]: sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->

<!---HONumber=AcomDC_0420_2016-->