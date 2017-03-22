---
title: Caricare i dati in Azure SQL Data Warehouse | Microsoft Docs
description: "Informazioni sugli scenari comuni per il caricamento dei dati in SQL Data Warehouse. Questi includono l&quot;uso di PolyBase, dell’archiviazione BLOB di Azure, di file flat e l’invio dei dischi. È anche possibile usare strumenti di terze parti."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 2253bf46-cf72-4de7-85ce-f267494d55fa
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
ms.custom: loading
translationtype: Human Translation
ms.sourcegitcommit: 9366bae67be7d1abf932d07971d2062c7bd29f3c
ms.openlocfilehash: 49825cc7455ae082ef750a2a31abbd2d71693c79
ms.lasthandoff: 02/27/2017


---
# <a name="load-data-into-azure-sql-data-warehouse"></a>Caricare i dati in Azure SQL Data Warehouse
Riepilogo delle opzioni di scenario e consigli per il caricamento dei dati in SQL Data Warehouse.

La parte più difficile del caricamento dei dati è in genere la preparazione dei dati per il caricamento. Azure semplifica il caricamento usando l’archiviazione BLOB come archivio dati comune per molti servizi e Azure Data Factory per orchestrare la comunicazione e lo spostamento dei dati tra i servizi di Azure. Questi processi sono integrati con la tecnologia PolyBase che usa l'elaborazione parallela massiva (MPP) per caricare i dati in parallelo dall’archiviazione BLOB di Azure in SQL Data Warehouse. 

Per esercitazioni in cui vengono caricati database di esempio, vedere [Caricare i dati di esempio in SQL Data Warehouse][Load sample databases].

## <a name="load-from-azure-blob-storage"></a>Caricare i dati dall’archiviazione BLOB di Azure
Il modo più rapido per importare dati in SQL Data Warehouse consiste nell'usare PolyBase per caricare i dati dall'archiviazione BLOB di Azure. PolyBase usa la progettazione di elaborazione parallela massiva (MPP) di SQL Data Warehouse per caricare dati in parallelo dall’archiviazione BLOB di Azure. Per usare PolyBase, è possibile usare i comandi T-SQL o una pipeline di Azure Data Factory.

### <a name="1-use-polybase-and-t-sql"></a>1. Usare PolyBase e T-SQL
Riepilogo del processo di caricamento:

1. Spostare i dati in Archiviazione BLOB di Azure o Azure Data Lake Store e archiviarli in file di testo.
2. Configurare gli oggetti esterni in SQL Data Warehouse per definire il percorso e il formato dei dati.
3. Eseguire un comando T-SQL per caricare i dati in parallelo in una nuova tabella di database.

<!-- 5. Schedule and run a loading job. --> 

Per un'esercitazione, vedere [Caricare dati dall'archivio BLOB di Azure in SQL Data Warehouse (PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)].

### <a name="2-use-azure-data-factory"></a>2. Usare Azure Data Factory
Per usare PolyBase in modo più semplice, è possibile creare una pipeline di Azure Data Factory che usa PolyBase per caricare i dati dall'archiviazione BLOB di Azure in SQL Data Warehouse. Si tratta di un’operazione rapida da configurare poiché non è necessario definire gli oggetti T-SQL. Se è necessario eseguire query sui dati esterni senza importarli, usare T-SQL. 

Riepilogo del processo di caricamento:

1. Spostare i dati nell’archiviazione BLOB di Azure e archiviarli in file di testo. Azure Data Factory attualmente non supporta la connettività ADLS con PolyBase.
2. Creare una pipeline di Azure Data Factory per l'inserimento dei dati. Usare l'opzione PolyBase.
4. Pianificare ed eseguire la pipeline.

Per un'esercitazione, vedere [Caricare i dati dall'archivio BLOB di Azure in Azure SQL Data Warehouse (Azure Data Factory)][Load data from Azure blob storage to SQL Data Warehouse (Azure Data Factory)].

## <a name="load-from-sql-server"></a>Caricare i dati da SQL Server
Per caricare i dati da SQL Server in SQL Data Warehouse, è possibile usare SQL Server Integration Services (SSIS), trasferire file flat o inviare i dischi a Microsoft. Continuare a leggere per un riepilogo dei diversi processi di caricamento e i collegamenti alle relative esercitazioni.

Per pianificare una migrazione completa dei dati da SQL Server a SQL Data Warehouse, vedere [Eseguire la migrazione della soluzione in SQL Data Warehouse][Migration overview]. 

### <a name="use-integration-services-ssis"></a>Usare SQL Server Integration Services (SSIS)
Se già si usano pacchetti di SQL Server Integration Services (SSIS) per il caricamento in SQL Server, è possibile aggiornare i pacchetti in modo da usare SQL Server come origine e SQL Data Warehouse come destinazione. Si tratta di un’operazione semplice e veloce ed è un'ottima scelta se non si sta tentando di eseguire la migrazione del processo di caricamento per usare dati già presenti nel cloud. Il caricamento sarà tuttavia più lento rispetto all'uso di PolyBase poiché SSIS non esegue il caricamento in parallelo.

Riepilogo del processo di caricamento:

1. Modificare il pacchetto SSIS in modo da puntare all'istanza di SQL Server per l'origine e al database di SQL Data Warehouse per la destinazione.
2. Eseguire la migrazione dello schema in SQL Data Warehouse se non è già presente.
3. Modificare il mapping nei pacchetti usando solo i tipi di dati supportati da SQL Data Warehouse.
4. Pianificare ed eseguire il pacchetto.

Per un'esercitazione, vedere [Caricare dati da SQL Server in Azure SQL Data Warehouse (SSIS)][Load data from SQL Server to Azure SQL Data Warehouse (SSIS)].

### <a name="use-azcopy-recommended-for--10-tb-data"></a>Usare AZCopy (scelta consigliata per i dati < 10 TB)
Se la dimensione dei dati è < 10 TB, è possibile esportarli da SQL Server in file flat, copiare i file nell’archiviazione BLOB di Azure e quindi usare PolyBase per caricare i dati in SQL Data Warehouse.

Riepilogo del processo di caricamento:

1. Usare l'utilità della riga di comando bcp per esportare i dati da SQL Server in file flat.
2. Usare l'utilità della riga di comando AZCopy per copiare i dati dai file flat nell’archiviazione BLOB di Azure.
3. Usare PolyBase per caricare i dati in SQL Data Warehouse.

Per un'esercitazione, vedere [Caricare dati dall'archivio BLOB di Azure in SQL Data Warehouse (PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)].

### <a name="use-bcp"></a>Usare bcp
Se si dispone di una piccola quantità di dati, è possibile usare bcp per caricarli direttamente in Azure SQL Data Warehouse.

Riepilogo del processo di caricamento:

1. Usare l'utilità della riga di comando bcp per esportare i dati da SQL Server in file flat.
2. Usare bcp per caricare i dati dai file flat direttamente in SQL Data Warehouse.

Per un'esercitazione, vedere [Caricare dati da SQL Server in Azure SQL Data Warehouse (file flat)][Load data from SQL Server to Azure SQL Data Warehouse (bcp)].

### <a name="use-importexport-recommended-for--10-tb-data"></a>Usare il servizio Importazione/Esportazione (scelta consigliata per i dati > 10 TB)
Se la dimensione dei dati è maggioire di 10 TB e si intende spostarli in Azure, è consigliabile usare il servizio di invio di dischi [Importazione/Esportazione][Import/Export]. 

Riepilogo del processo di caricamento

1. Usare l'utilità della riga di comando bcp per esportare i dati da SQL Server in file flat su dischi trasferibili.
2. Inviare i dischi a Microsoft.
3. Microsoft carica i dati in SQL Data Warehouse.

## <a name="load-from-hdinsight"></a>Caricare da HDInsight
SQL Data Warehouse supporta il caricamento di dati da HDInsight tramite PolyBase. Il processo è lo stesso usato per il caricamento dei dati dall'archivio BLOB di Azure, usando PolyBase per connettersi a HDInsight per caricare i dati. 

### <a name="1-use-polybase-and-t-sql"></a>1. Usare PolyBase e T-SQL
Riepilogo del processo di caricamento:

1. Spostare i dati in HDInsight e archiviarli nel file di testo, in formato ORC o Parquet.
2. Configurare gli oggetti esterni in SQL Data Warehouse per definire il percorso e il formato dei dati.
3. Eseguire un comando T-SQL per caricare i dati in parallelo in una nuova tabella di database.

Per un'esercitazione, vedere [Caricare dati dall'archivio BLOB di Azure in SQL Data Warehouse (PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)].

## <a name="recommendations"></a>Raccomandazioni
Molti partner Microsoft dispongono di soluzioni di caricamento. Per altre informazioni, vedere l'elenco dei [partner di soluzioni][solution partners]. 

Se i dati provengono da un'origine non relazionale e si intende caricarli in SQL Data Warehouse, è necessario trasformarli in righe e colonne prima di caricarli. I dati trasformati non devono essere archiviati in un database e possono essere archiviati in file di testo.

Creare statistiche sui dati appena caricati. SQL Data Warehouse di Azure non supporta ancora le statistiche di creazione automatica o aggiornamento automatico.  Per ottenere le migliori prestazioni dalle query, è importante creare statistiche per tutte le colonne di tutte le tabelle dopo il primo caricamento o dopo eventuali modifiche sostanziali dei dati.  Per informazioni dettagliate, vedere [Managing statistics on tables in SQL Data Warehouse][Statistics](Gestione delle statistiche nelle tabelle in SQL Data Warehouse).

## <a name="next-steps"></a>Passaggi successivi
Per altri suggerimenti relativi allo sviluppo, vedere la [panoramica sullo sviluppo][development overview].

<!--Image references-->

<!--Article references-->
[Load data from Azure blob storage to SQL Data Warehouse (PolyBase)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Load data from Azure blob storage to SQL Data Warehouse (Azure Data Factory)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md
[Load data from SQL Server to Azure SQL Data Warehouse (SSIS)]: ./sql-data-warehouse-load-from-sql-server-with-integration-services.md
[Load data from SQL Server to Azure SQL Data Warehouse (bcp)]: ./sql-data-warehouse-load-from-sql-server-with-bcp.md
[Load data from SQL Server to Azure SQL Data Warehouse (AZCopy)]: ./sql-data-warehouse-load-from-sql-server-with-azcopy.md

[Load sample databases]: ./sql-data-warehouse-load-sample-databases.md
[Migration overview]: ./sql-data-warehouse-overview-migrate.md
[solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[development overview]: ./sql-data-warehouse-overview-develop.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->

<!--Other Web references-->
[Import/Export]: https://azure.microsoft.com/documentation/articles/storage-import-export-service/

