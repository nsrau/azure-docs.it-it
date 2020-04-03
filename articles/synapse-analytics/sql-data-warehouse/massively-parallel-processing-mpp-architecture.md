---
title: Architettura di Azure Synapse Analytics (precedentemente SQL DW)
description: Scopri come Azure Synapse Analytics (in precedenza SQL DW) combina l'elaborazione parallela massiccia (MPP) con Archiviazione di Azure per ottenere prestazioni e scalabilità elevate.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: a42ec523bb1f77c48f7382283a52565c9c9273b6
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584494"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Architettura di Azure Synapse Analytics (precedentemente SQL DW) 

Azure Synapse è un servizio di analisi senza limiti che riunisce funzionalità aziendali di data warehousing e analisi di Big Data. Offre la libertà di eseguire query sui dati in base alle proprie esigenze, usando risorse serverless su richiesta o con provisioning, su larga scala. Azure Synapse riunisce questi due mondi con un'esperienza unificata per l'inserimento, la preparazione, la gestione e la gestione dei dati per le esigenze immediate di Business Intelligence e apprendimento automatico.

 Azure Synapse è costituito da quattro componenti:
- Synapse SQL: Completare l'analisi basata su T-SQLSynapse SQL: Complete T-SQL based analytics 
    - Pool SQL (pay per DWU di cui è stato eseguito il provisioning) – Generalmente disponibile
    - SQL su richiesta (pagamento per ogni TB elaborato) - (anteprima)
- Spark: Apache Spark profondamente integrato (anteprima)
- Integrazione dei dati: Integrazione ibrida dei dati (anteprima)Data Integration: Hybrid data integration (Preview)
- Studio: esperienza utente unificata.  (Anteprima)

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="synapse-sql-mpp-architecture-components"></a>Componenti dell'architettura SQL MPP Synapse

[Synapse SQL](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse) sfrutta un'architettura con scalabilità orizzontale per distribuire l'elaborazione computazionale dei dati su più nodi. L'unità di scala è un'astrazione della potenza di calcolo nota come [unità di data warehouse.](what-is-a-data-warehouse-unit-dwu-cdwu.md) Il calcolo è separato dall'archiviazione, che consente di ridimensionare il calcolo indipendentemente dai dati nel sistema.

![Architettura SQL Synapse](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

Synapse SQL utilizza un'architettura basata su nodi. Le applicazioni connettono ed emettono comandi T-SQL a un nodo Control, ovvero il singolo punto di ingresso per un pool SQL Synapse.Applications connect and issue T-SQL commands to a Control node, which is the single point of entry for a Synapse SQL pool. Il nodo Control esegue il motore MPP, che ottimizza le query per l'elaborazione parallela e quindi passa le operazioni ai nodi di calcolo per eseguire il proprio lavoro in parallelo. 

I nodi di calcolo archiviano tutti i dati utente nell'archiviazione di Azure ed eseguono le query parallele. Data Movement Service (DMS) è un servizio interno a livello di sistema che sposta i dati tra i nodi per eseguire query in parallelo e restituire risultati accurati. 

Con l'archiviazione e il calcolo disaccoppiati, quando si usa il pool SQL Synapse è possibile:With discoupled storage and compute, when using Synapse SQL pool si can:

* Ridimensionare la potenza di calcolo indipendentemente dalle esigenze di archiviazione.
* Aumentare o ridurre la potenza di calcolo, all'interno di un pool SQL (data warehouse), senza spostare i dati.
* Sospendere la capacità di calcolo mantenendo intatti i dati e pagando solo per l'archiviazione.
* Riprendere le capacità di calcolo durante l'orario operativo.

### <a name="azure-storage"></a>Archiviazione di Azure

Synapse SQL sfrutta Archiviazione di Azure per proteggere i dati utente.  Poiché i dati vengono archiviati e gestiti da Archiviazione di Azure, è previsto un addebito separato per l'utilizzo dello spazio di archiviazione. I dati vengono inseriti in **distribuzioni** per ottimizzare le prestazioni del sistema. È possibile scegliere il modello di partizionamento orizzontale da usare per distribuire i dati quando si definisce la tabella. Sono supportati questi modelli di partizionamento di partizione:These sharding patterns are supported:

* Hash
* Round robin
* Replica

### <a name="control-node"></a>Nodo di controllo

Il nodo Control è il cervello dell'architettura. È il front-end che interagisce con tutte le applicazioni e le connessioni. Il motore MPP viene eseguito nel nodo di controllo per ottimizzare e coordinare le query parallele. Quando si invia una query T-SQL, il nodo Control la trasforma in query eseguite su ogni distribuzione in parallelo.

### <a name="compute-nodes"></a>Nodi di calcolo

I nodi di calcolo forniscono la potenza di calcolo. Viene eseguito il mapping delle distribuzioni ai nodi di calcolo per l'elaborazione. Quando si paga per più risorse di calcolo, le distribuzioni vengono rimappate ai nodi Compute disponibili. Il numero di nodi di calcolo varia da 1 a 60 ed è determinato dal livello di servizio per Synapse SQL.

Ogni nodo di calcolo ha un ID visibile nelle visualizzazioni di sistema. È possibile individuare l'ID del nodo di calcolo cercando la colonna node_id nelle visualizzazioni di sistema il cui nome inizia con sys.pdw_nodes. Per un elenco delle visualizzazioni di sistema, vedere le [visualizzazioni di sistema MPP](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7).

### <a name="data-movement-service"></a>Data Movement Service

Data Movement Service (DMS) è la tecnologia di trasporto dei dati che coordina lo spostamento dei dati da un nodo di calcolo all'altro. Alcune query richiedono lo spostamento dei dati per garantire che le query parallele restituiscano risultati accurati. Quando lo spostamento dei dati è necessario, DMS assicura che i dati corretti vengano spostati nel percorso corretto. 

## <a name="distributions"></a>Distribuzioni

La distribuzione è l'unità di base dell'archiviazione e dell'elaborazione di query parallele eseguite su dati distribuiti. Quando viene eseguita una query, il lavoro viene suddiviso in 60 query più piccole che vengono eseguite in parallelo. 

Ognuna delle 60 query viene eseguita in una distribuzione dei dati. Ogni nodo di calcolo gestisce una o più delle 60 distribuzioni. Un pool SQL con risorse di calcolo massime ha una distribuzione per ogni nodo di calcolo. Un pool SQL con risorse di calcolo minime include tutte le distribuzioni in un nodo di calcolo.  

## <a name="hash-distributed-tables"></a>Tabelle con distribuzione hash

Una tabella con distribuzione hash offre prestazioni di query più elevate per join e aggregazioni in tabelle di grandi dimensioni. 

Per partizionare i dati in una tabella con distribuzione hash, viene utilizzata una funzione hash per assegnare in modo deterministico ogni riga a una distribuzione. Nella definizione della tabella una delle colonne è definita come colonna di distribuzione. La funzione hash usa i valori della colonna di distribuzione per assegnare ogni riga a una distribuzione.

La figura seguente illustra come una tabella completa non distribuita viene archiviata come tabella con distribuzione hash. 

![Tabella distribuita](./media/massively-parallel-processing-mpp-architecture/hash-distributed-table.png "Tabella distribuita")  

* Ogni riga appartiene a una sola distribuzione.  
* Un algoritmo hash deterministico assegna ogni riga a una sola distribuzione.  
* Il numero di righe di tabella per distribuzione varia come mostrano le diverse dimensioni delle tabelle.

Ai fini delle prestazioni, per la selezione di una colonna di distribuzione è necessario considerare alcuni aspetti, ad esempio la specificità, la differenza dei dati e i tipi di query eseguite nel sistema.

## <a name="round-robin-distributed-tables"></a>Tabelle con distribuzione round robin

La tabella round robin è la tabella più semplice da creare e offre ottime prestazioni quando viene usata come tabella di staging per i caricamenti.

Una tabella con distribuzione round robin distribuisce i dati in modo uniforme all'interno della tabella senza alcuna ottimizzazione aggiuntiva. Viene innanzitutto selezionata casualmente una distribuzione, quindi i buffer di righe vengono assegnati in sequenza alle distribuzioni. Sebbene il caricamento dei dati in una tabella round robin risulti rapido, è spesso possibile ottenere prestazioni di query migliori con le tabelle con distribuzione hash. I join su tabelle round robin richiedono il rimmatomo dei dati, operazione necessaria.

## <a name="replicated-tables"></a>Tabelle replicate

Una tabella replicata offre le migliori prestazioni di query per le tabelle di piccole dimensioni.

Una tabella replicata memorizza nella cache una copia completa della tabella di ogni nodo di calcolo. Di conseguenza, la replica di una tabella elimina la necessità di trasferire i dati tra i nodi di calcolo prima di un join o un'aggregazione. Le tabelle replicate sono particolarmente adatte all'uso con tabelle di piccole dimensioni. È necessaria ulteriore spazio di archiviazione ed è necessario un sovraccarico aggiuntivo che si verifica durante la scrittura di dati, che rendono le tabelle di grandi dimensioni poco pratico.  

Il diagramma seguente mostra una tabella replicata memorizzata nella cache alla prima distribuzione in ogni nodo di calcolo.  

![Tabella replicata](./media/massively-parallel-processing-mpp-architecture/replicated-table.png "Tabella replicata") 

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce un po' di Azure Synapse, imparare a creare rapidamente [un pool SQL](create-data-warehouse-portal.md) e caricare i dati di [esempio.](load-data-from-azure-blob-storage-using-polybase.md) Se non si ha familiarità con Azure, il [glossario di Azure](../../azure-glossary-cloud-terminology.md) può essere utile quando si incontrano termini nuovi. Oppure esaminare alcune di queste altre risorse di Azure Synapse.Or look at some of these other Azure Synapse Resources.  
