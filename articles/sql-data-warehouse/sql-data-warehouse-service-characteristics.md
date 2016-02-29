<properties
   pageTitle="Caratteristiche del servizio SQL Data Warehouse | Microsoft Azure"
   description="Vengono forniti i valori massimi per connessioni, query, DDL e DML Transact-SQL, oltre a viste di sistema per SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="02/11/2016"
   ms.author="barbkess;jrj;sonyama"/>

# Caratteristiche del servizio SQL Data Warehouse

Queste caratteristiche includono i valori massimi definiti da Azure SQL Data Warehouse per supportare i carichi di lavoro analitici più impegnativi, assicurando al tempo stesso a ogni singola query la disponibilità delle risorse necessarie per assicurare prestazioni ottimali.

## Connessioni

| Categoria | Descrizione | Massima |
| :---------------- | :------------------------------------------- | :----------------- |
| Sessione | Sessioni simultanee aperte | 1024<br/><br/>Sono supportate 1024 connessioni simultanee che possono inviare richieste simultanee al data warehouse. Si noti che vi sono limiti nel numero di query che è possibile eseguire contemporaneamente. Quando un limite viene superato, la richiesta viene inviata a una coda interna in cui resta in attesa di elaborazione.|
| Sessione | Memoria massima per le istruzioni preparate | 20 MB |
| Account di accesso | Accessi per SQL Server. | 500\.000 |


## Elaborazione di query

| Categoria | Descrizione | Massima |
| :---------------- | :------------------------------------------- | :----------------- |
| Query | Query simultanee nelle tabelle utente. | 32<br/><br/>Le query aggiuntive vengono inviate a una coda interna in cui rimangono in attesa di elaborazione. Indipendentemente dal numero di query in esecuzione nello stesso momento, ogni query è ottimizzata per sfruttare completamente l'architettura di elaborazione parallela massiva.|
| Query | Query in coda nelle tabelle utente | 1000 |
| Query | Query simultanee nelle viste di sistema | 100 |
| Query | Query in coda nelle viste di sistema | 1000 |
| Query | Parametri massimi | 2098 |
| Batch | Dimensione massima | 65\.536*4096 |


## DDL (Data Definition Language)

| Categoria | Descrizione | Massima |
| :---------------- | :------------------------------------------- | :----------------- |
| Tabella | Tabelle per database | 2 miliardi |
| Tabella | Colonne per tabella | 1024 colonne |
| Tabella | Byte per colonna | 8000 byte |
| Tabella | Byte per riga, dimensioni definite | 8060 byte<br/><br/>Il numero di byte per riga viene calcolato allo stesso modo di SQL Server, con la compressione pagina attivata. Il valore massimo è 8060 byte. Per stimare le dimensioni di una riga, vedere i calcoli delle dimensioni della riga nell'articolo relativo alla [stima delle dimensioni di un indice in cluster](https://msdn.microsoft.com/library/ms178085.aspx) in MSDN.<br/><br/>Per un elenco delle dimensioni dei tipi di dati SQL Data Warehouse, vedere [CREATE TABLE (Azure SQL Data Warehouse)](https://msdn.microsoft.com/library/mt203953.aspx). |
| Tabella | Partizioni per tabella | 15\.000<br/><br/>Per prestazioni elevate, è consigliabile ridurre al minimo il numero di partizioni necessarie garantendo al tempo stesso il rispetto dei requisiti aziendali. Con l'aumentare del numero di partizioni, l'overhead per le operazioni DDL e DML aumenta e le prestazioni rallentano.|
| Tabella | Caratteri per valore limite della partizione.| 4000 |
| Indice | Indici non in cluster per tabella. | 999<br/><br/>Si applica solo alle tabelle rowstore.|
| Indice | Indici in cluster per tabella. | 1<br><br/>Si applica sia alle tabelle rowstore che columnstore.|
| Indice | Righe in un gruppo righe dell'indice columnstore | 1024<br/><br/>Ogni indice columnstore è implementato come più indici columnstore. Si noti che se si inseriscono 1024 righe in un indice columnstore SQL Data Warehouse, non tutte le righe andranno nello stesso gruppo righe.|
| Indice | Compilazioni simultanee di indici columnstore in cluster. | 32<br/><br/>Si applica quando gli indici columnstore in cluster sono tutti compilati in tabelle diverse. È consentita solo 1 compilazione dell'indice columnstore in cluster per tabella. Le richieste aggiuntive resteranno in coda.|
| Indice | Dimensioni della chiave indice. | 900 byte.<br/><br/>Si applica solo agli indici rowstore.<br/><br/>È possibile creare indici in colonne varchar con una dimensione massima di oltre 900 byte se al momento della creazione dell'indice i dati esistenti in tali colonne non superano i 900 byte. Tuttavia, le azioni INSERT o UPDATE successive eseguite nelle colonne che causano un aumento delle dimensioni totali oltre i 900 byte avranno esito negativo.|
| Indice | Colonne chiave per indice. | 16<br/><br/>Si applica solo agli indici rowstore. Gli indici columnstore in cluster includono tutte le colonne.|
| Statistiche | Dimensione dei valori combinati delle colonne. | 900 byte. |
| Statistiche | Colonne per oggetto statistiche. | 32 |
| Statistiche | Statistiche create per le colonne per tabella. | 30\.000 |
| Stored procedure | Livello massimo di annidamento. | 8 |
| View | Colonne per vista | 1\.024 |


## DML (Data Manipulation Language)

| Categoria | Descrizione | Massima |
| :---------------- | :------------------------------------------- | :----------------- |
| Risultati SELECT | Colonne per riga | 4096<br/><br/>Nel risultato SELECT non è possibile avere più di 4096 colonne per riga. Non è garantito che si possa avere sempre 4096. Se il piano di query richiede una tabella temporanea, potrebbe venire applicato il valore massimo di 1024 colonne per tabella.|
| Risultati SELECT | Byte per riga | > 8060<br/><br/>Nei risultati SELECT il numero di byte per riga può essere superiore al valore massimo di 8060 byte consentito per le righe della tabella. Se il piano di query per l'istruzione SELECT richiede una tabella temporanea, potrebbe venire applicato il valore massimo di 8060 byte per tabella.|
| Risultati SELECT | Byte per colonna | > 8000<br/><br/>Nei risultati SELECT il numero di byte per colonna può essere superiore al valore massimo di 8000 byte consentito per le colonne della tabella. Se il piano di query per l'istruzione SELECT richiede una tabella temporanea, potrebbe venire applicato il valore massimo di 8000 byte per tabella.|
| SELECT | Sottoquery nidificate | 32<br/><br/>In un'istruzione SELECT non è possibile avere più di 32 sottoquery nidificate. Non è garantito che si possa averne sempre 32. Ad esempio, un JOIN può introdurre una sottoquery nel piano di query. Il numero di sottoquery può essere limitato anche dalla memoria disponibile.|
| SELECT | Colonne per JOIN | 1024 colonne<br/><br/>Nel JOIN non è possibile avere più di 1024 colonne. Non è garantito che si possa averne sempre 1024. Se il piano JOIN richiede una tabella temporanea con più colonne del risultato JOIN, il limite di 1024 viene applicato alla tabella temporanea. |
| SELECT | Byte per le colonne GROUP BY. | 8060<br/><br/>Le colonne presenti nella clausola GROUP BY possono avere un massimo di 8060 byte.|
| SELECT | Byte per le colonne ORDER BY | 8060<br/><br/>Le colonne presenti nella clausola ORDER BY possono avere un massimo di 8060 byte.|
| INSERT | Byte per colonna, a larghezza fissa e variabile. | 8000 byte. I tentativi di inserimento di più byte rispetto a quelli definiti per la colonna genereranno un errore.|
| INSERT | Byte per riga, colonne a larghezza variabile. | > 8060 byte. I dati che superano 8060 byte vengono inseriti nell'area di archiviazione di overflow.|
| AGGIORNAMENTO | Byte per colonna, a larghezza fissa e variabile. | 8000 byte. I tentativi di aggiornare una colonna a un valore che richiede più byte rispetto a quelli definiti per la colonna genereranno un errore.|
| Identificatori e costanti per istruzione | Numero di identificatori e costanti di riferimento. | 65\.535<br/><br/>SQL Data Warehouse limita il numero di identificatori e costanti che possono essere contenuti in una singola espressione di una query. Il limite è 65.535. Il superamento di questo numero genera un errore 8632 di SQL Server. Per altre informazioni, vedere [Errore interno: è stato raggiunto un limite di servizi di espressione](http://support.microsoft.com/kb/913050/).|

## Viste di sistema

| Vista di sistema | Righe massime |
| :--------------------------------- | :------------|
| sys.dm\_pdw\_component\_health\_alerts | 10\.000 |
| sys.dm\_pdw\_dms\_cores | 100 |
| sys.dm\_pdw\_dms\_workers | Numero totale di ruoli di lavoro DMS per le 1000 richieste SQL più recenti. |
| sys.dm\_pdw\_dms\_worker\_pairs | 10\.000 |
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

<!--MSDN references-->

<!---HONumber=AcomDC_0218_2016-->