---
title: Architettura del pool SQL dedicato (precedentemente SQL DW)
description: Informazioni su come il pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics combina le funzionalità di elaborazione delle query distribuite con archiviazione di Azure per ottenere prestazioni e scalabilità elevate.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 45c7f89f773095a102429c07f7441223de3c2dec
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448263"
---
# <a name="dedicated-sql-pool-formerly-sql-dw-architecture-in-azure-synapse-analytics"></a>Architettura del pool SQL dedicato (precedentemente SQL DW) in Azure sinapsi Analytics

Azure Synapse Analytics è un servizio di analisi che riunisce funzionalità aziendali di data warehouse e analisi di Big Data. Consente di eseguire query sui dati in base alle proprie esigenze.

> [!NOTE]
>Esplorare la [documentazione di Azure sinapsi Analytics](../overview-what-is.md).
>


> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="synapse-sql-architecture-components"></a>Componenti dell'architettura di Synapse SQL

Il [pool SQL dedicato (in precedenza SQL DW)](sql-data-warehouse-overview-what-is.md) si avvale di un'architettura con scalabilità orizzontale per distribuire l'elaborazione computazionale dei dati tra più nodi. L'unità di scalabilità è un'astrazione della potenza di calcolo nota come [unità di data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md). Poiché le risorse di calcolo sono separate dall'archiviazione, è possibile ridimensionarle indipendentemente dai dati presenti nel sistema.

![Architettura del pool SQL dedicato (precedentemente SQL DW)](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

Il pool SQL dedicato (in precedenza SQL DW) usa un'architettura basata su nodi. Le applicazioni si connettono e inviano comandi T-SQL a un nodo di controllo. Il nodo di controllo ospita il motore delle query distribuite, che ottimizza le query per l'elaborazione parallela e quindi passa le operazioni ai nodi di calcolo per eseguire il lavoro in parallelo.

I nodi di calcolo archiviano tutti i dati utente in Archiviazione di Azure ed eseguono le query parallele. Data Movement Service (DMS) è un servizio interno a livello di sistema che sposta i dati tra i nodi per eseguire query in parallelo e restituire risultati accurati.

Con le risorse di archiviazione e di calcolo separate, quando si usa un pool SQL dedicato (in precedenza SQL DW), è possibile:

- Ridimensionare la potenza di calcolo indipendentemente dalle esigenze di archiviazione.
- Aumentare o ridurre la potenza di calcolo, all'interno di un pool SQL dedicato (in precedenza SQL DW), senza lo stato di trasferimento dei dati.
- Sospendere la capacità di calcolo mantenendo intatti i dati e consentendo di pagare solo per l'archiviazione.
- Riprendere le capacità di calcolo durante l'orario operativo.

### <a name="azure-storage"></a>Archiviazione di Azure

SQL pool SQL dedicato (in precedenza SQL DW) si avvale di archiviazione di Azure per proteggere i dati utente.  Poiché i dati vengono archiviati e gestiti da Archiviazione di Azure, è previsto un addebito separato per l'uso dello spazio di archiviazione. I dati sono partizionati in **distribuzioni** per ottimizzare le prestazioni del sistema. È possibile scegliere il modello di partizionamento orizzontale da usare per distribuire i dati quando si definisce la tabella. Sono supportati i modelli di partizionamento seguenti:

- Hash
- Round robin
- Replica

### <a name="control-node"></a>Nodo di controllo

Il nodo di controllo costituisce il componente principale dell'architettura. È il front-end che interagisce con tutte le applicazioni e le connessioni. Il motore di query distribuite viene eseguito sul nodo di controllo per ottimizzare e coordinare le query parallele. Quando si invia una query T-SQL, il nodo di controllo la trasforma in query che verranno eseguite in ogni distruzione in parallelo.

### <a name="compute-nodes"></a>Nodi di calcolo

I nodi di calcolo forniscono la potenza di calcolo. Viene eseguito il mapping delle distribuzioni ai nodi di calcolo per l'elaborazione. Quando si ottengono risorse di calcolo aggiuntive a pagamento, viene eseguito il mapping delle distribuzioni ai nodi di calcolo disponibili. Il numero di nodi di calcolo è compreso tra 1 e 60 e viene determinato in base al livello di servizio associato a Synapse SQL.

Ogni nodo di calcolo ha un ID visibile nelle visualizzazioni di sistema. È possibile individuare l'ID del nodo di calcolo cercando la colonna node_id nelle visualizzazioni di sistema il cui nome inizia con sys.pdw_nodes. Per un elenco di queste viste di sistema, vedere [sinapsi SQL System views](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="data-movement-service"></a>Data Movement Service

Data Movement Service (DMS) è la tecnologia di trasporto dei dati che coordina lo spostamento dei dati da un nodo di calcolo all'altro. Alcune query richiedono lo spostamento dei dati per garantire che le query parallele restituiscano risultati accurati. Quando lo spostamento dei dati è necessario, DMS assicura che i dati corretti vengano spostati nel percorso corretto.

## <a name="distributions"></a>Distribuzioni

La distribuzione è l'unità di base dell'archiviazione e dell'elaborazione di query parallele eseguite su dati distribuiti. Quando Synapse SQL esegue una query, il processo viene suddiviso in 60 query di dimensioni più piccole che vengono eseguite in parallelo.

Ognuna delle 60 query viene eseguita in una distribuzione dei dati. Ogni nodo di calcolo gestisce una o più delle 60 distribuzioni. Un pool SQL dedicato (in precedenza SQL DW) con risorse di calcolo massime dispone di una distribuzione per ogni nodo di calcolo. Un pool SQL dedicato (in precedenza SQL DW) con risorse di calcolo minime dispone di tutte le distribuzioni in un nodo di calcolo.  

## <a name="hash-distributed-tables"></a>Tabelle con distribuzione hash

Una tabella con distribuzione hash offre prestazioni di query più elevate per join e aggregazioni in tabelle di grandi dimensioni.

Per partizionare i dati in una tabella con distribuzione hash, viene usata una funzione hash per assegnare in modo deterministico ogni riga a un'unica distribuzione. Nella definizione della tabella una delle colonne è definita come colonna di distribuzione. La funzione hash usa i valori della colonna di distribuzione per assegnare ogni riga a una distribuzione.

La figura seguente illustra come una tabella completa non distribuita viene archiviata come tabella con distribuzione hash.

![Tabella distribuita](./media/massively-parallel-processing-mpp-architecture/hash-distributed-table.png "Tabella distribuita")  

- Ogni riga appartiene a una sola distribuzione.  
- Un algoritmo hash deterministico assegna ogni riga a una sola distribuzione.  
- Il numero di righe di tabella per distribuzione varia come mostrano le diverse dimensioni delle tabelle.

Ai fini delle prestazioni, per la selezione di una colonna di distribuzione è necessario considerare alcuni aspetti, ad esempio la specificità, la differenza dei dati e i tipi di query eseguite nel sistema.

## <a name="round-robin-distributed-tables"></a>Tabelle con distribuzione round robin

La tabella round robin è la tabella più semplice da creare e offre ottime prestazioni quando viene usata come tabella di staging per i caricamenti.

Una tabella con distribuzione round robin distribuisce i dati in modo uniforme all'interno della tabella senza alcuna ottimizzazione aggiuntiva. Viene innanzitutto selezionata casualmente una distribuzione, quindi i buffer di righe vengono assegnati in sequenza alle distribuzioni. Sebbene il caricamento dei dati in una tabella round robin risulti rapido, è spesso possibile ottenere prestazioni di query migliori con le tabelle con distribuzione hash. Per i join nelle tabelle round robin è necessaria una ridistribuzione dei dati, che richiede più tempo.

## <a name="replicated-tables"></a>Tabelle replicate

Una tabella replicata offre le migliori prestazioni di query per le tabelle di piccole dimensioni.

Una tabella replicata memorizza nella cache una copia completa della tabella di ogni nodo di calcolo. Di conseguenza, la replica di una tabella elimina la necessità di trasferire i dati tra i nodi di calcolo prima di un join o un'aggregazione. Le tabelle replicate sono particolarmente adatte all'uso con tabelle di piccole dimensioni. È necessario più spazio di archiviazione e si verifica un sovraccarico aggiuntivo durante la scrittura dei dati, che impedisce l'uso di tabelle di grandi dimensioni.  

Il diagramma seguente mostra una tabella replicata memorizzata nella cache durante la prima distribuzione in ogni nodo di calcolo.  

![Tabella replicata](./media/massively-parallel-processing-mpp-architecture/replicated-table.png "Tabella replicata")

## <a name="next-steps"></a>Passaggi successivi

Ora che si conoscono le sinapsi di Azure, si apprenderà come [creare rapidamente un pool SQL dedicato (in precedenza SQL DW)](create-data-warehouse-portal.md) e [caricare i dati di esempio](load-data-from-azure-blob-storage-using-polybase.md). Se non si ha familiarità con Azure, il [glossario di Azure](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) può essere utile quando si incontrano termini nuovi. In alternativa, vedere alcune delle altre risorse disponibili per Azure Synapse.  

- [Casi di successo dei clienti](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Blog](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Richieste di funzionalità](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Video](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Creare un ticket di supporto](sql-data-warehouse-get-started-create-support-ticket.md)
- [Pagina delle domande di Domande e risposte Microsoft](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)
- [Forum su Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
