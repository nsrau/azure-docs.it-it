---
title: Architettura MPP - Azure SQL Data Warehouse | Microsoft Docs
description: "Informazioni su come Azure SQL Data Warehouse combina l'elaborazione parallela elevata (Massively Parallel Processing, MPP) con l'archiviazione di Azure per ottenere prestazioni e scalabilità elevate."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: architecture
ms.date: 11/15/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 4c230eb0633b2917b90a5c1f9f4176882bfd0290
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="azure-sql-data-warehouse---massively-parallel-processing-mpp-architecture"></a>Azure SQL Data Warehouse - Architettura MPP (Massively Parallel Processing)
Informazioni su come Azure SQL Data Warehouse combina l'elaborazione parallela elevata (Massively Parallel Processing, MPP) con l'archiviazione di Azure per ottenere prestazioni e scalabilità elevate. 

## <a name="mpp-architecture-components"></a>Componenti dell'architettura MPP
SQL Data Warehouse usa un'architettura a scalabilità orizzontale per distribuire l'elaborazione computazionale dei dati su più nodi. L'unità di scalabilità è un'astrazione della potenza di calcolo nota come unità di data warehouse. SQL Data Warehouse separa il calcolo dall'archiviazione e consente agli utenti di scalare il calcolo indipendentemente dai dati nel sistema.

![Architettura di SQL Data Warehouse](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

SQL Data Warehouse usa un'architettura basata sui nodi. Le applicazioni si connettono ed eseguono comandi T-SQL in un nodo di controllo che rappresenta l'unico punto di ingresso per il data warehouse. Il nodo di controllo esegue il motore MPP che ottimizza le query per l'elaborazione parallela e quindi passa le operazioni ai nodi di calcolo per l'elaborazione in parallelo. I nodi di calcolo archiviano tutti i dati utente nell'archiviazione di Azure ed eseguono le query parallele. Data Movement Service (DMS) è un servizio interno a livello di sistema che sposta i dati tra i nodi per eseguire query in parallelo e restituire risultati accurati. 

Grazie alla separazione tra archiviazione e calcolo, SQL Data Warehouse consente di:

* Ridimensionare la potenza di calcolo indipendentemente dalle esigenze di archiviazione.
* Aumentare o ridurre la potenza senza spostare i dati.
* Sospendere la capacità di calcolo mantenendo intatti i dati e pagando solo per l'archiviazione.
* Riprendere le capacità di calcolo durante l'orario operativo.

### <a name="azure-storage"></a>Archiviazione di Azure
SQL Data Warehouse usa l'archiviazione di Azure per proteggere i dati utente.  Poiché i dati sono archiviati e gestiti dall'archiviazione di Azure, SQL Data Warehouse addebita separatamente l'utilizzo dell'archiviazione. I dati sono partizionati in **distribuzioni** per ottimizzare le prestazioni del sistema. È possibile scegliere il modello di partizionamento orizzontale da usare per distribuire i dati quando si definisce la tabella. SQL Data Warehouse supporta i modelli di partizionamento orizzontale seguenti:

* Hash
* Round robin
* Replica

### <a name="control-node"></a>Nodo di controllo

Il nodo di controllo è il componente centrale del data warehouse. È il front-end che interagisce con tutte le applicazioni e le connessioni. Il motore MPP viene eseguito nel nodo di controllo per ottimizzare e coordinare le query parallele. Quando si invia una query T-SQL a SQL Data Warehouse, il nodo di controllo la trasforma in query che vengono eseguite su ogni distribuzione in parallelo.

### <a name="compute-nodes"></a>Nodi di calcolo

I nodi di calcolo forniscono la potenza di calcolo. Viene eseguito il mapping delle distribuzioni ai nodi di calcolo per l'elaborazione. Quando si paga per risorse di calcolo aggiuntive, SQL Data Warehouse esegue di nuovo il mapping delle distribuzioni ai nodi di calcolo disponibili. Il numero di nodi di calcolo è compreso tra 1 e 60 ed è determinato dal livello di servizio per il data warehouse.

Ogni nodo di calcolo ha un ID visibile nelle visualizzazioni di sistema. È possibile individuare l'ID del nodo di calcolo cercando la colonna node_id nelle visualizzazioni di sistema il cui nome inizia con sys.pdw_nodes. Per un elenco delle visualizzazioni di sistema, vedere le [visualizzazioni di sistema MPP](sql-data-warehouse-reference-tsql-statements.md).

### <a name="data-movement-service"></a>Data Movement Service
Data Movement Service (DMS) è la tecnologia di trasporto dei dati che coordina lo spostamento dei dati da un nodo di calcolo all'altro. Alcune query richiedono lo spostamento dei dati per garantire che le query parallele restituiscano risultati accurati. Quando lo spostamento dei dati è necessario, DMS assicura che i dati corretti vengano spostati nel percorso corretto. 

## <a name="distributions"></a>Distribuzioni

La distribuzione è l'unità di base dell'archiviazione e dell'elaborazione di query parallele eseguite su dati distribuiti. Quando SQL Data Warehouse esegue una query, il processo viene suddiviso in 60 query più piccole eseguite in parallelo. Ognuna delle 60 query viene eseguita in una distribuzione dei dati. Ogni nodo di calcolo gestisce una o più delle 60 distribuzioni. Un data warehouse con il numero massimo di risorse di calcolo prevede una distribuzione per ogni nodo di calcolo. Un data warehouse con il numero minimo di risorse di calcolo prevede tutte le distribuzioni in un unico nodo di calcolo.  

## <a name="hash-distributed-tables"></a>Tabelle con distribuzione hash
Una tabella con distribuzione hash offre prestazioni di query più elevate per join e aggregazioni in tabelle di grandi dimensioni. 

Per partizionare i dati in una tabella con distribuzione hash, SQL Data Warehouse usa una funzione hash per assegnare in modo deterministico ogni riga a una sola distribuzione. Nella definizione della tabella una delle colonne è definita come colonna di distribuzione. La funzione hash usa i valori della colonna di distribuzione per assegnare ogni riga a una distribuzione.

La figura seguente illustra come una tabella completa non distribuita viene archiviata come tabella con distribuzione hash. 

![Tabella distribuita](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Tabella distribuita")  

* Ogni riga appartiene a una sola distribuzione.  
* Un algoritmo hash deterministico assegna ogni riga a una sola distribuzione.  
* Il numero di righe di tabella per distribuzione varia come mostrano le diverse dimensioni delle tabelle.

Ai fini delle prestazioni, per la selezione di una colonna di distribuzione è necessario considerare alcuni aspetti, ad esempio la specificità, la differenza dei dati e i tipi di query eseguite nel sistema.

## <a name="round-robin-distributed-tables"></a>Tabelle con distribuzione round robin
La tabella round robin è la tabella più semplice da creare e offre ottime prestazioni quando viene usata come tabella di staging per i caricamenti.

Una tabella con distribuzione round robin distribuisce i dati in modo uniforme all'interno della tabella senza alcuna ottimizzazione aggiuntiva. Viene innanzitutto selezionata casualmente una distribuzione, quindi i buffer di righe vengono assegnati in sequenza alle distribuzioni. Sebbene il caricamento dei dati in una tabella round robin risulti rapido, è spesso possibile ottenere prestazioni di query migliori con le tabelle con distribuzione hash. I join nelle tabelle round robin necessitano di una ridistribuzione dei dati che richiede più tempo.


## <a name="replicated-tables"></a>Tabelle replicate
Una tabella replicata offre le migliori prestazioni di query per le tabelle di piccole dimensioni.

Una tabella replicata memorizza nella cache una copia completa della tabella di ogni nodo di calcolo. Di conseguenza, la replica di una tabella elimina la necessità di trasferire i dati tra i nodi di calcolo prima di un join o un'aggregazione. Le tabelle replicate sono particolarmente adatte all'uso con tabelle di piccole dimensioni. È necessario più spazio di archiviazione e si verificano sovraccarichi durante la scrittura dei dati che impediscono l'uso di tabelle di grandi dimensioni.  

La figura seguente illustra una tabella replicata. Per SQL Data Warehouse, la tabella replicata viene memorizzata nella cache durante la prima distribuzione in ogni nodo di calcolo.  

![Tabella replicata](media/sql-data-warehouse-distributed-data/replicated-table.png "Tabella replicata") 

## <a name="next-steps"></a>Passaggi successivi
Dopo aver appreso alcune informazioni su SQL Data Warehouse, vedere come [creare un SQL Data Warehouse][create a SQL Data Warehouse] rapidamente e [caricare i dati di esempio][load sample data]. Se non si ha familiarità con Azure, il [glossario di Azure][Azure glossary] può essere utile quando si incontrano termini nuovi. Oppure vedere alcune delle altre risorse disponibili per SQL Data Warehouse.  

* [Casi di successo dei clienti]
* [Blog]
* [Richieste di funzionalità]
* [Video]
* [Blog Customer Advisory Team]
* [Creare un ticket di supporto]
* [Forum MSDN]
* [Forum su Stack Overflow]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Creare un ticket di supporto]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Casi di successo dei clienti]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Blog]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blog Customer Advisory Team]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Richieste di funzionalità]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum MSDN]: https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse
[Forum su Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Video]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
