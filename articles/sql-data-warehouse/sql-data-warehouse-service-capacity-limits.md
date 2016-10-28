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
   ms.date="09/01/2016"
   ms.author="sonyama;barbkess;jrj"/>

# Limiti di capacità di SQL Data Warehouse

Le tabelle seguenti contengono i valori massimi consentiti per vari componenti di SQL Data Warehouse di Azure.


## Gestione del carico di lavoro

| Categoria | Descrizione | Massima |
| :------------------ | :------------------------------------------------ | :----------------- |
| [Unità Data Warehouse (DWU)][]| Max DWU per un singolo SQL Data Warehouse | 6000 |
| [Unità Data Warehouse (DWU)][]| Max DWU per un singolo SQL server | 6000 per impostazione predefinita<br/><br/>Per impostazione predefinita, ogni server SQL, ad esempio myserver.database.windows.net, ha una Quota DTU di 45000 che consente massimo 6000 DWU. Questa quota è semplicemente un limite di sicurezza. È possibile aumentare la quota [creando un ticket di supporto][] e selezionando *Quota* come tipo di richiesta. Per calcolare le esigenze in termini di DTU, moltiplicare 7,5 per il valore DWU totale necessario. È possibile visualizzare l'utilizzo di DTU attuale nel pannello SQL Server del portale. I database in pausa e non in pausa vengono conteggiati nella quota di DTU. |
| Connessione del database | Sessioni simultanee aperte | 1024<br/><br/>È supportato un massimo di 1024 connessioni attive, ciascuna delle quali può inviare richieste a un database SQL Data Warehouse contemporaneamente. Si noti che sono previsti dei limiti sul numero di query effettivamente eseguibili in contemporanea. Quando si supera il limite di concorrenza, la richiesta viene inviata a una coda interna in cui resta in attesa di elaborazione.|
| Connessione del database | Memoria massima per le istruzioni preparate | 20 MB |
| [Gestione del carico di lavoro][] | Numero massimo di query simultanee | 32<br/><br/> Per impostazione predefinita, SQL Data Warehouse può eseguire un massimo di 32 query simultanee, le query restanti vengono inserite in coda.<br/><br/>Quando gli utenti vengono assegnati a una classe di risorse superiore o quando SQL Data Warehouse è configurato con un DWU basso, il livello di concorrenza può diminuire. Alcune query, come ad esempio le query DMV, possono essere sempre eseguite.|
| [Tempdb][] | Dimensioni massime del database Tempdb | 399 GB per DW100. Pertanto il database Tempdb DWU1000 ha come dimensioni 3,99 TB |


## Oggetti di database

| Categoria | Descrizione | Massima |
| :---------------- | :------------------------------------------- | :----------------- |
| Database | Dimensioni massime | 240 TB compressi su disco<br/><br/>Questo spazio è indipendente dallo spazio di tempdb o del log ed è dedicato alle tabelle permanenti. La compressione stimata per columnstore cluster è 5X. Questa compressione consente al database di crescere fino a circa 1 PB quando tutte le tabelle sono columnstore cluster (tipo di tabella predefinito).|
| Tabella | Dimensioni massime | 60 TB compressi su disco |
| Tabella | Tabelle per database | 2 miliardi |
| Tabella | Colonne per tabella | 1024 colonne |
| Tabella | Byte per colonna | Dipende dalla colonna [tipo di dati][]. Il limite è 8000 per i tipi di dati char, 4000 per nvarchar o 2 GB per i tipi di dati MAX.|
| Tabella | Byte per riga, dimensioni definite | 8060 byte<br/><br/>Il numero di byte per riga viene calcolato come per SQL Server, con la compressione pagina. Come SQL Server, SQL Data Warehouse supporta l'archiviazione di dati di overflow della riga che consente di spostare **colonne a lunghezza variabile** all'esterno delle righe. Quando le righe di lunghezza variabile vengono inviate all'esterno delle righe, viene archiviata nel record principale solo una radice 24 byte. Per ulteriori informazioni, vedere l'articolo di MSDN [Dati di overflow della riga che superano 8 KB][].|
| Tabella | Partizioni per tabella | 15\.000<br/><br/>Per ottenere prestazioni elevate è consigliabile ridurre al minimo il numero di partizioni necessarie e allo stesso tempo continuare a supportare i requisiti aziendali. Con l'aumentare del numero di partizioni, l'overhead per le operazioni DDL (Data Definition Language) e DML (Data Manipulation Language ) aumenta e le prestazioni rallentano.|
| Table | Caratteri per valore limite della partizione.| 4000 |
| Indice | Indici non in cluster per tabella. | 999<br/><br/>Si applica solo alle tabelle rowstore.|
| Indice | Indici in cluster per tabella. | 1<br><br/>Si applica sia alle tabelle rowstore che alle tabelle columnstore.|
| Indice | Dimensioni della chiave indice. | 900 byte<br/><br/>Si applica solo agli indici rowstore.<br/><br/>È possibile creare indici in colonne varchar con una dimensione massima di oltre 900 byte se i dati esistenti nelle colonne non superano i 900 byte quando viene creato l'indice. Tuttavia, le azioni INSERT o UPDATE successive eseguite nelle colonne che causano un aumento delle dimensioni totali oltre i 900 byte avranno esito negativo.|
| Indice | Colonne chiave per indice. | 16<br/><br/>Si applica solo agli indici rowstore. Gli indici columnstore in cluster includono tutte le colonne.|
| Statistiche | Dimensione dei valori combinati delle colonne. | 900 byte. |
| Statistiche | Colonne per oggetto statistiche. | 32 |
| Statistiche | Statistiche create per le colonne per tabella. | 30\.000 |
| Stored procedure | Livello massimo di annidamento. | 8 |
| View | Colonne per vista | 1\.024 |


## Operazioni di caricamento

| Categoria | Descrizione | Massima |
| :---------------- | :------------------------------------------- | :----------------- |
| Operazioni di caricamento di PolyBase | Byte per riga | 32\.768<br/><br/>Le operazioni di caricamento di PolyBase sono limitate al caricamento di righe inferiori a 32.000 che non possono essere caricate in VARCHR(MAX), NVARCHAR(MAX) o VARBINARY(MAX). Questo limite verrà presto rimosso.<br/><br/>


## Query

| Categoria | Descrizione | Massima |
| :---------------- | :------------------------------------------- | :----------------- |
| Query | Query in coda nelle tabelle utente | 1000 |
| Query | Query simultanee nelle viste di sistema | 100 |
| Query | Query in coda nelle viste di sistema | 1000 |
| Query | Parametri massimi | 2098 |
| Batch | Dimensione massima | 65\.536*4096 |
| Risultati SELECT | Colonne per riga | 4096<br/><br/>Il risultato di SELECT non può includere più di 4096 colonne per riga. Non è garantito che si possa avere sempre 4096. Se il piano di query richiede una tabella temporanea, potrebbe venire applicato il valore massimo di 1024 colonne per tabella.|
| SELECT | Sottoquery nidificate | 32<br/><br/>In un'istruzione SELECT non possono essere presenti più di 32 sottoquery annidate. Non è garantito che si possa averne sempre 32. Ad esempio, un JOIN può introdurre una sottoquery nel piano di query. Il numero di sottoquery può essere limitato anche dalla memoria disponibile.|
| SELECT | Colonne per JOIN | 1024 colonne<br/><br/>Il JOIN non può includere più di 1024 colonne. Non è garantito che si possa averne sempre 1024. Se il piano JOIN richiede una tabella temporanea con più colonne del risultato JOIN, il limite di 1024 viene applicato alla tabella temporanea. |
| SELECT | Byte per le colonne GROUP BY. | 8060<br/><br/>Le colonne nella clausola GROUP BY possono avere un massimo di 8060 byte.|
| SELECT | Byte per le colonne ORDER BY | 8060<br/><br/>Le colonne nella clausola ORDER BY possono avere un massimo di 8060 byte.|
| Identificatori e costanti per istruzione | Numero di identificatori e costanti di riferimento. | 65\.535<br/><br/>SQL Data Warehouse limita il numero di identificatori e costanti che possono essere contenuti in una singola espressione di una query. Il limite è 65.535. Il superamento di questo numero genera un errore 8632 di SQL Server. Per altre informazioni, vedere la pagina relativa al messaggio [Errore interno: è stato raggiunto un limite per i servizi di gestione delle espressioni][].|


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
[Unità Data Warehouse (DWU)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[panoramica degli argomenti di riferimento di SQL Data Warehouse]: ./sql-data-warehouse-overview-reference.md
[Gestione del carico di lavoro]: ./sql-data-warehouse-develop-concurrency.md
[Tempdb]: ./sql-data-warehouse-tables-temporary.md
[tipo di dati]: ./sql-data-warehouse-tables-data-types.md
[creando un ticket di supporto]: /sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->
[Dati di overflow della riga che superano 8 KB]: https://msdn.microsoft.com/library/ms186981.aspx
[Errore interno: è stato raggiunto un limite per i servizi di gestione delle espressioni]: https://support.microsoft.com/kb/913050

<!---HONumber=AcomDC_0907_2016-->