---
title: Architettura di Azure sinapsi Analytics (in precedenza SQL DW)
description: Informazioni su come Azure sinapsi Analytics (in precedenza SQL DW) combina l'elaborazione parallela massiva (MPP) con archiviazione di Azure per ottenere prestazioni e scalabilità elevate.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: ea9629c63fcab97ba8ba83cd88592c37ae41818a
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646398"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Architettura di Azure sinapsi Analytics (in precedenza SQL DW) 

La sinapsi di Azure è un servizio di analisi senza limiti che riunisce data warehousing aziendale e analisi di Big Data. Consente di eseguire query sui dati in base alle proprie esigenze, usando risorse su richiesta o con provisioning senza server, su larga scala. Le sinapsi di Azure offrono questi due mondi insieme a un'esperienza unificata per l'inserimento, la preparazione, la gestione e la gestione dei dati per esigenze immediate di BI e machine learning.

 La sinapsi di Azure è costituita da quattro componenti:
- Analisi SQL: completare le analisi basate su T-SQL 
    - Pool SQL (pagamento con provisioning per DWU): disponibile a livello generale
    - SQL su richiesta (pagamento per TB elaborato)-(anteprima)
- Spark: Apache Spark con integrazione approfondita (anteprima) 
- Integrazione dei dati: integrazione di dati ibridi (anteprima)
- Studio: esperienza utente unificata.  (Anteprima)

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="sql-analytics-mpp-architecture-components"></a>Componenti dell'architettura MPP di SQL Analytics

[Analisi SQL](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) sfrutta un'architettura di scalabilità orizzontale per distribuire l'elaborazione computazionale dei dati tra più nodi. L'unità di scalabilità è un'astrazione della potenza di calcolo nota come [unità di data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md). Il calcolo è separato dall'archiviazione che consente di ridimensionare le risorse di calcolo indipendentemente dai dati nel sistema.

![Architettura di analisi SQL](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

SQL Analytics usa un'architettura basata su nodi. Le applicazioni si connettono e inviano comandi T-SQL a un nodo di controllo, che è il singolo punto di ingresso per analisi SQL. Il nodo di controllo esegue il motore MPP che ottimizza le query per l'elaborazione parallela e quindi passa le operazioni ai nodi di calcolo per l'elaborazione in parallelo. 

I nodi di calcolo archiviano tutti i dati utente nell'archiviazione di Azure ed eseguono le query parallele. Data Movement Service (DMS) è un servizio interno a livello di sistema che sposta i dati tra i nodi per eseguire query in parallelo e restituire risultati accurati. 

Con le risorse di archiviazione e di calcolo disaccoppiate, quando si usa SQL Analytics è possibile:

* Ridimensionare la potenza di calcolo indipendentemente dalle esigenze di archiviazione.
* Aumentare o ridurre la potenza di calcolo, all'interno di un pool SQL (data warehouse), senza lo stato di trasferimento dei dati.
* Sospendere la capacità di calcolo mantenendo intatti i dati e pagando solo per l'archiviazione.
* Riprendere le capacità di calcolo durante l'orario operativo.

### <a name="azure-storage"></a>Archiviazione di Azure

Analisi SQL sfrutta l'archiviazione di Azure per proteggere i dati utente.  Poiché i dati vengono archiviati e gestiti da archiviazione di Azure, è previsto un addebito separato per il consumo di spazio di archiviazione. I dati sono partizionati in **distribuzioni** per ottimizzare le prestazioni del sistema. È possibile scegliere il modello di partizionamento orizzontale da usare per distribuire i dati quando si definisce la tabella. Questi modelli di partizionamento orizzontale sono supportati:

* Hash
* Round robin
* Replica

### <a name="control-node"></a>Nodo di controllo

Il nodo di controllo è il cervello dell'architettura. È il front-end che interagisce con tutte le applicazioni e le connessioni. Il motore MPP viene eseguito nel nodo di controllo per ottimizzare e coordinare le query parallele. Quando si invia una query T-SQL a SQL Analytics, il nodo di controllo lo trasforma in query eseguite su ogni distribuzione in parallelo.

### <a name="compute-nodes"></a>Nodi di calcolo

I nodi di calcolo forniscono la potenza di calcolo. Viene eseguito il mapping delle distribuzioni ai nodi di calcolo per l'elaborazione. Quando si paga per più risorse di calcolo, SQL Analytics esegue nuovamente il mapping delle distribuzioni ai nodi di calcolo disponibili. Il numero di nodi di calcolo è compreso tra 1 e 60 ed è determinato dal livello di servizio per analisi SQL.

Ogni nodo di calcolo ha un ID visibile nelle visualizzazioni di sistema. È possibile individuare l'ID del nodo di calcolo cercando la colonna node_id nelle visualizzazioni di sistema il cui nome inizia con sys.pdw_nodes. Per un elenco delle visualizzazioni di sistema, vedere le [visualizzazioni di sistema MPP](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7).

### <a name="data-movement-service"></a>Data Movement Service
Data Movement Service (DMS) è la tecnologia di trasporto dei dati che coordina lo spostamento dei dati da un nodo di calcolo all'altro. Alcune query richiedono lo spostamento dei dati per garantire che le query parallele restituiscano risultati accurati. Quando lo spostamento dei dati è necessario, DMS assicura che i dati corretti vengano spostati nel percorso corretto. 

## <a name="distributions"></a>Distribuzioni

La distribuzione è l'unità di base dell'archiviazione e dell'elaborazione di query parallele eseguite su dati distribuiti. Quando SQL Analytics esegue una query, il lavoro viene suddiviso in 60 query più piccole eseguite in parallelo. 

Ognuna delle 60 query viene eseguita in una distribuzione dei dati. Ogni nodo di calcolo gestisce una o più delle 60 distribuzioni. Un pool SQL con risorse di calcolo massime dispone di una distribuzione per ogni nodo di calcolo. Un pool SQL con risorse di calcolo minime dispone di tutte le distribuzioni in un nodo di calcolo.  

## <a name="hash-distributed-tables"></a>Tabelle con distribuzione hash
Una tabella con distribuzione hash offre prestazioni di query più elevate per join e aggregazioni in tabelle di grandi dimensioni. 

Per partizionare i dati in una tabella con distribuzione hash, SQL Analytics usa una funzione hash per assegnare in modo deterministico ogni riga a una sola distribuzione. Nella definizione della tabella una delle colonne è definita come colonna di distribuzione. La funzione hash usa i valori della colonna di distribuzione per assegnare ogni riga a una distribuzione.

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

Una tabella replicata memorizza nella cache una copia completa della tabella di ogni nodo di calcolo. Di conseguenza, la replica di una tabella elimina la necessità di trasferire i dati tra i nodi di calcolo prima di un join o un'aggregazione. Le tabelle replicate sono particolarmente adatte all'uso con tabelle di piccole dimensioni. È necessario spazio di archiviazione aggiuntivo e si verifica un sovraccarico aggiuntivo durante la scrittura di dati che rendono le tabelle di grandi dimensioni non pratiche.  

Il diagramma seguente mostra una tabella replicata memorizzata nella cache nella prima distribuzione in ogni nodo di calcolo.  

![Tabella replicata](media/sql-data-warehouse-distributed-data/replicated-table.png "Tabella replicata") 

## <a name="next-steps"></a>Passaggi successivi
Ora che si conoscono le sinapsi di Azure, si apprenderà come [creare rapidamente un pool SQL][create a SQL pool] e [caricare i dati di esempio][load sample data]. Se non si ha familiarità con Azure, il [glossario di Azure][Azure glossary] può essere utile quando si incontrano termini nuovi. Oppure esaminare alcune di queste altre risorse di Azure sinapsi.  

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
[create a SQL pool]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[Azure Synapse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
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
[Forum su Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Video]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for Azure Synapse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
