   <properties
   pageTitle="Caricare i dati in Azure SQL Data Warehouse | Microsoft Azure"
   description="Informazioni sugli scenari comuni per il caricamento dei dati in SQL Data Warehouse. Questi includono l'uso di PolyBase, dell’archiviazione BLOB di Azure, di file flat e l’invio dei dischi. È anche possibile usare strumenti di terze parti."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Caricare i dati in Azure SQL Data Warehouse

Riepilogo delle opzioni di scenario e consigli per il caricamento dei dati in SQL Data Warehouse.

La parte più difficile del caricamento dei dati è in genere la preparazione dei dati per il caricamento. Azure semplifica il caricamento usando l’archiviazione BLOB come archivio dati comune per molti servizi e Azure Data Factory per orchestrare la comunicazione e lo spostamento dei dati tra i servizi di Azure. Questi processi sono integrati con la tecnologia PolyBase che usa l'elaborazione parallela massiva (MPP) per caricare i dati in parallelo dall’archiviazione BLOB di Azure in SQL Data Warehouse.

Per esercitazioni in cui vengono caricati database di esempio, vedere [Caricare i dati di esempio in SQL Data Warehouse][].

## Caricare i dati dall’archiviazione BLOB di Azure
Il modo più rapido per importare dati in SQL Data Warehouse consiste nell'usare PolyBase per caricare i dati dall'archiviazione BLOB di Azure. PolyBase usa la progettazione di elaborazione parallela massiva (MPP) di SQL Data Warehouse per caricare dati in parallelo dall’archiviazione BLOB di Azure. Per usare PolyBase, è possibile usare i comandi T-SQL o una pipeline di Azure Data Factory.

### 1\. Usare PolyBase e T-SQL

Riepilogo del processo di caricamento:

2. Formattare i dati come UTF-8 poiché PolyBase non supporta attualmente UTF-16.
2. Spostare i dati nell’archiviazione BLOB di Azure e archiviarli in file di testo.
3. Configurare gli oggetti esterni in SQL Data Warehouse per definire il percorso e il formato dei dati.
4. Eseguire un comando T-SQL per caricare i dati in parallelo in una nuova tabella di database.

<!-- 5. Schedule and run a loading job. --> 

Per un'esercitazione, vedere [Caricare dati dall'archivio BLOB di Azure in SQL Data Warehouse (PolyBase)][].

### 2\. Usare Azure Data Factory

Per usare PolyBase in modo più semplice, è possibile creare una pipeline di Azure Data Factory che usa PolyBase per caricare i dati dall'archiviazione BLOB di Azure in SQL Data Warehouse. Si tratta di un’operazione rapida da configurare poiché non è necessario definire gli oggetti T-SQL. Se è necessario eseguire query sui dati esterni senza importarli, usare T-SQL.

Riepilogo del processo di caricamento:

2. Formattare i dati come UTF-8 poiché PolyBase non supporta attualmente UTF-16.
2. Spostare i dati nell’archiviazione BLOB di Azure e archiviarli in file di testo.
3. Creare una pipeline di Azure Data Factory per l'inserimento dei dati. Usare l'opzione PolyBase.
4. Pianificare ed eseguire la pipeline.

Per un'esercitazione, vedere [Caricare i dati dall'archivio BLOB di Azure in Azure SQL Data Warehouse (Azure Data Factory)][].


## Caricare i dati da SQL Server
Per caricare i dati da SQL Server in SQL Data Warehouse, è possibile usare SQL Server Integration Services (SSIS), trasferire file flat o inviare i dischi a Microsoft. Continuare a leggere per un riepilogo dei diversi processi di caricamento e i collegamenti alle relative esercitazioni.

Per pianificare una migrazione completa dei dati da SQL Server a SQL Data Warehouse, vedere [Eseguire la migrazione della soluzione in SQL Data Warehouse][].

### Usare SQL Server Integration Services (SSIS)
Se già si usano pacchetti di SQL Server Integration Services (SSIS) per il caricamento in SQL Server, è possibile aggiornare i pacchetti in modo da usare SQL Server come origine e SQL Data Warehouse come destinazione. Si tratta di un’operazione semplice e veloce ed è un'ottima scelta se non si sta tentando di eseguire la migrazione del processo di caricamento per usare dati già presenti nel cloud. Il caricamento sarà tuttavia più lento rispetto all'uso di PolyBase poiché SSIS non esegue il caricamento in parallelo.

Riepilogo del processo di caricamento:

1. Modificare il pacchetto SSIS in modo da puntare all'istanza di SQL Server per l'origine e al database di SQL Data Warehouse per la destinazione.
2. Eseguire la migrazione dello schema in SQL Data Warehouse se non è già presente.
3. Modificare il mapping nei pacchetti usando solo i tipi di dati supportati da SQL Data Warehouse.
3. Pianificare ed eseguire il pacchetto.

Per un'esercitazione, vedere [Caricare dati da SQL Server in Azure SQL Data Warehouse (SSIS)][].

### Usare AZCopy (scelta consigliata per i dati < 10 TB)
Se la dimensione dei dati è < 10 TB, è possibile esportarli da SQL Server in file flat, copiare i file nell’archiviazione BLOB di Azure e quindi usare PolyBase per caricare i dati in SQL Data Warehouse.

Riepilogo del processo di caricamento:

1. Usare l'utilità della riga di comando bcp per esportare i dati da SQL Server in file flat.
2. Usare l'utilità della riga di comando AZCopy per copiare i dati dai file flat nell’archiviazione BLOB di Azure.
3. Usare PolyBase per caricare i dati in SQL Data Warehouse.

Per un'esercitazione, vedere [Caricare dati dall'archivio BLOB di Azure in SQL Data Warehouse (PolyBase)][].

### Usare bcp
Se si dispone di una piccola quantità di dati, è possibile usare bcp per caricarli direttamente in Azure SQL Data Warehouse.

Riepilogo del processo di caricamento:
1. Usare l'utilità della riga di comando bcp per esportare i dati da SQL Server in file flat.
2. Usare bcp per caricare i dati dai file flat direttamente in SQL Data Warehouse.

Per un'esercitazione, vedere [Caricare dati da SQL Server in Azure SQL Data Warehouse (file flat)][].


### Usare il servizio Importazione/Esportazione (scelta consigliata per i dati > 10 TB)
Se la dimensione dei dati è > 10 TB e si intende spostarli in Azure, è consigliabile usare il servizio di invio di dischi [Importazione/Esportazione][].

Riepilogo del processo di caricamento
2. Usare l'utilità della riga di comando bcp per esportare i dati da SQL Server in file flat su dischi trasferibili.
3. Inviare i dischi a Microsoft.
4. Microsoft carica i dati in SQL Data Warehouse.


## Consigli

Molti partner Microsoft dispongono di soluzioni di caricamento. Per altre informazioni, vedere un elenco dei [partner di soluzioni][].

Se i dati provengono da un'origine non relazionale e si intende caricarli in SQL Data Warehouse, è necessario trasformarli in righe e colonne prima di caricarli. I dati trasformati non devono essere archiviati in un database e possono essere archiviati in file di testo.

Creare statistiche sui dati appena caricati. SQL Data Warehouse di Azure non supporta ancora le statistiche di creazione automatica o aggiornamento automatico. Per ottenere le migliori prestazioni dalle query, è importante creare statistiche per tutte le colonne di tutte le tabelle dopo il primo caricamento o dopo eventuali modifiche sostanziali dei dati. Per informazioni dettagliate, vedere [Managing statistics on tables in SQL Data Warehouse][] \(Gestione delle statistiche nelle tabelle in SQL Data Warehouse).


## Passaggi successivi
Per altri suggerimenti relativi allo sviluppo, vedere la [panoramica sullo sviluppo][].

<!--Image references-->

<!--Article references-->
[Caricare dati dall'archivio BLOB di Azure in SQL Data Warehouse (PolyBase)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Caricare i dati dall'archivio BLOB di Azure in Azure SQL Data Warehouse (Azure Data Factory)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md
[Caricare dati da SQL Server in Azure SQL Data Warehouse (SSIS)]: ./sql-data-warehouse-load-from-sql-server-with-integration-services.md
[Caricare dati da SQL Server in Azure SQL Data Warehouse (file flat)]: ./sql-data-warehouse-load-from-sql-server-with-bcp.md
[Load data from SQL Server to Azure SQL Data Warehouse (AZCopy)]: ./sql-data-warehouse-load-from-sql-server-with-azcopy.md

[Caricare i dati di esempio in SQL Data Warehouse]: ./sql-data-warehouse-load-sample-databases.md
[Eseguire la migrazione della soluzione in SQL Data Warehouse]: ./sql-data-warehouse-overview-migrate.md
[partner di soluzioni]: ./sql-data-warehouse-integrate-solution-partners.md
[panoramica sullo sviluppo]: ./sql-data-warehouse-overview-develop.md
[Managing statistics on tables in SQL Data Warehouse]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->

<!--Other Web references-->
[Importazione/Esportazione]: https://azure.microsoft.com/documentation/articles/storage-import-export-service/

<!---HONumber=AcomDC_0706_2016-->