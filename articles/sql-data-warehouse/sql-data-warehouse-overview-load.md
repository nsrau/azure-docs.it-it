<properties
   pageTitle="Caricare i dati in SQL Data Warehouse | Microsoft Azure"
   description="Informazioni sugli scenari comuni per il caricamento dei dati in SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/21/2015"
   ms.author="mausher;barbkess"/>

# Caricare i dati in SQL Data Warehouse
SQL Data Warehouse presenta numerose opzioni di caricamento dei dati per un'ampia gamma di scenari. Ad esempio, è possibile caricare gli elementi seguenti:

- Batch di grandi dimensioni una volta al giorno
- Batch più piccoli nel corso della giornata
- Semplici aggiornamenti a tabelle (delle dimensioni) più piccole

Le esigenze di ogni scenario sono svariate come i tipi di dati da caricare in SQL Data Warehouse. Questo articolo riporta alcune delle scelte disponibili per il caricamento dei dati.

## Tecnologie
SQL Data Warehouse supporta questi strumenti di caricamento dati standard:

- Data factory di Azure
- Utilità della riga di comando bcp
- PolyBase
- SQL Server Integration Services (SSIS)
- Strumenti di caricamento dati di terze parti

### Data factory di Azure
Data factory di Azure è un servizio completamente gestito per la composizione di servizi per l'archiviazione, l'elaborazione e lo spostamento dei dati in pipeline di produzione dei dati ottimizzate, scalabili e affidabili. SQL Data Warehouse è un'[origine/sink][] supportata per l'[attività di copia][] di Data factory di Azure.

### Utilità della riga di comando bcp
L'eseguibile dalla riga di comando **bcp** è un'utilità Microsoft che consente il caricamento e l'estrazione dei dati da SQL Server, dal database SQL e da SQL Data Warehouse. Per iniziare, seguire l'esercitazione relativa al [caricamento dei dati con bcp][].

### PolyBase
PolyBase è una tecnologia Microsoft che semplifica l'analisi dei dati consentendo di eseguire query sull'archivio BLOB di Archiviazione di Azure e Hadoop con il linguaggio Transact-SQL standard e senza l'uso di MapReduce. PolyBase consente anche di caricare i dati dall'archivio BLOB di Azure in SQL Data Warehouse. Per iniziare, seguire l'esercitazione relativa al [caricamento dei dati con PolyBase][].

### SQL Server Integration Services (SSIS)
[SSIS][] è una piattaforma per la creazione di soluzioni di integrazione e trasformazione dei dati a livello aziendale. Per creare pacchetti che si connettono a SQL Data Warehouse, utilizzare lo standard [] [scheda destinazione OLE DB] utilizzando un manager di connessione ADO.Net.

### Strumenti di terze parti
SQL Data Warehouse supporta le principali soluzioni del settore per il caricamento dei dati. Per informazioni più dettagliate, vedere l'elenco dei [partner delle soluzioni][].

## Passaggi successivi
Per altri suggerimenti relativi allo sviluppo, vedere la [panoramica sullo sviluppo][].

<!--Image references-->

<!--Article references-->
[caricamento dei dati con bcp]: sql-data-warehouse-load-with-bcp.md
[caricamento dei dati con PolyBase]: sql-data-warehouse-load-with-polybase.md
[partner delle soluzioni]: sql-data-warehouse-solution-partners.md
[panoramica sullo sviluppo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[origine/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[attività di copia]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141237.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!--Other Web references-->

<!---HONumber=July15_HO3-->