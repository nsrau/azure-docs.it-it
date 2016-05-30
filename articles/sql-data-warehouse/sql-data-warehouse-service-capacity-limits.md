<properties
   pageTitle="Limiti di capacità di SQL Data Warehouse | Microsoft Azure"
   description="I valori massimi per le connessioni, i database, le tabelle e le query per SQL Data Warehouse."
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
   ms.date="05/13/2016"
   ms.author="sonyama;barbkess;jrj"/>

# Limiti di capacità di SQL Data Warehouse

Le tabelle seguenti contengono i valori massimi consentiti per vari componenti di Azure SQL Data Warehouse.


## Gestione del carico di lavoro

| Categoria | Descrizione | Massima |
| :------------------ | :------------------------------------------- | :----------------- |
| Unità Data Warehouse (DWU)| Risorse di calcolo, memoria e I/O | 2000 |
| Connessione del database | Sessioni simultanee aperte | 1024<br/><br/>È supportato un massimo di 1024 connessioni attive, ciascuna delle quali può inviare richieste a un database SQL Data Warehouse contemporaneamente. Si noti che sono previsti dei limiti sul numero di query effettivamente eseguibili in contemporanea. Quando si supera il limite di concorrenza, la richiesta viene inviata a una coda interna in cui resta in attesa di elaborazione.|
| Connessione del database | Memoria massima per le istruzioni preparate | 20 MB |
| Gestione del carico di lavoro | Numero massimo di query simultanee | 32<br/><br/> Per impostazione predefinita, SQL Data Warehouse esegue un massimo di 32 query simultanee e di query rimanenti in coda.<br/><br/>Quando gli utenti vengono assegnati a una classe di risorse superiore, il livello di concorrenza può diminuire. Alcune query, come ad esempio le query DMV, possono essere sempre eseguite. Per altre informazioni, vedere [Gestione della concorrenza e del carico di lavoro][].|


## Oggetti di database

| Categoria | Descrizione | Massima |
| :---------------- | :------------------------------------------- | :----------------- |
| Database | Dimensioni massime | 60 TB compressi su disco<br/><br/>SQL Data Warehouse consente di avere fino a 60 TB di spazio su disco per database. Lo spazio su disco corrisponde alla dimensione compressa per le tabelle permanenti. Questo spazio è indipendente dallo spazio di tempdb o del log ed è dedicato alle tabelle permanenti. La compressione columnstore cluster è stimata a 5x, ovvero la dimensione non compressa del database potrebbe crescere a circa 300 TB quando tutte le tabella sono columnstore cluster (il tipo di tabella predefinito). Il limite di 60 TB diventa di 240 TB alla fine della fase di anteprima pubblica e questo consente a molti database di crescere fino a oltre 1 PB di dati non compressi.|
| Tabella | Dimensioni massime | 60 TB compressi su disco |
| Tabella | Tabelle per database | 2 miliardi |
| Tabella | Colonne per tabella | 1024 colonne |
| Tabella | Byte per colonna | 8000 byte |
| Tabella | Byte per riga, dimensioni definite | 8060 byte<br/><br/>Il numero di byte per riga viene calcolato come per SQL Server, con la compressione pagina attivata. Come SQL Server, SQL Data Warehouse supporta l'archiviazione di dati di overflow della riga che consente di spostare colonne a lunghezza variabile all'esterno delle righe. Nel record principale viene archiviata solo una radice di 24 byte per le colonne di lunghezza variabile spostate all'esterno delle righe. Per altre informazioni, vedere l'articolo dedicato ai [Dati di overflow della riga che superano gli 8 KB][] nella documentazione online di SQL Server.<br/><br/>Per un elenco delle dimensioni dei tipi di dati di SQL Data Warehouse, vedere l'articolo dedicato a [CREATE TABLE in Azure SQL Data Warehouse][]. |
| Tabella | Partizioni per tabella | 15\.000<br/><br/>Per ottenere prestazioni elevate è consigliabile ridurre al minimo il numero di partizioni necessarie pur supportando i requisiti aziendali. Con l'aumentare del numero di partizioni, l'overhead per le operazioni DDL (Data Definition Language) e DML (Data Manipulation Language ) aumenta e le prestazioni rallentano.|
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


## Operazioni di caricamento

| Categoria | Descrizione | Massima |
| :---------------- | :------------------------------------------- | :----------------- |
| Operazioni di caricamento di PolyBase | Byte per riga | 32\.768<br/><br/>Le operazioni di caricamento di PolyBase sono limitate al caricamento di righe minori di 32 KB che non possono essere caricate su VARCHR(MAX), NVARCHAR(MAX) o VARBINARY(MAX). Anche se è presente questo limite, verrà presto rimosso.<br/><br/>


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
| SELECT | Byte per le colonne GROUP BY. | 8060<br/><br/>Le colonne nella clausola GROUP BY possono avere massimo 8060 byte.|
| SELECT | Byte per le colonne ORDER BY | 8060<br/><br/>Le colonne nella clausola ORDER BY possono avere massimo 8060 byte.|
| Identificatori e costanti per istruzione | Numero di identificatori e costanti di riferimento. | 65\.535<br/><br/>SQL Data Warehouse limita il numero di identificatori e costanti che possono essere contenuti nella singola espressione di una query. Il limite è 65.535. Il superamento di questo numero genera un errore 8632 di SQL Server. Per altre informazioni, vedere [Messaggio di errore quando si esegue una query in SQL Server 2005: "Errore interno: è stato raggiunto un limite di servizi di espressione"][].|


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


## Passaggi successivi
Per altre informazioni di riferimento, vedere la [panoramica degli argomenti di riferimento di SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[panoramica degli argomenti di riferimento di SQL Data Warehouse]: sql-data-warehouse-overview-reference.md
[Gestione della concorrenza e del carico di lavoro]: sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->
[Dati di overflow della riga che superano gli 8 KB]: https://msdn.microsoft.com/library/ms186981.aspx
[CREATE TABLE in Azure SQL Data Warehouse]: https://msdn.microsoft.com/library/mt203953.aspx
[Messaggio di errore quando si esegue una query in SQL Server 2005: "Errore interno: è stato raggiunto un limite di servizi di espressione"]: https://support.microsoft.com/kb/913050

<!---HONumber=AcomDC_0518_2016-->