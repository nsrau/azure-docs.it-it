---
title: Architettura SQL di Azure SynapseAzure Synapse SQL architecture
description: Scopri come Azure Synapse SQL combina l'elaborazione parallela massiccia (MPP) con Archiviazione di Azure per ottenere prestazioni elevate e scalabilità.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 570e84b3a545736aad6983c7f0d8c0f0296ca589
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431813"
---
# <a name="azure-synapse-sql-architecture"></a>Architettura SQL di Azure SynapseAzure Synapse SQL architecture 

In questo articolo vengono descritti i componenti dell'architettura di Synapse SQL.

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="synapse-sql-architecture-components"></a>Componenti dell'architettura SQL SynapseSynapse SQL architecture components

Synapse SQL sfrutta un'architettura con scalabilità orizzontale per distribuire l'elaborazione computazionale dei dati su più nodi. Il calcolo è separato dall'archiviazione, che consente di ridimensionare il calcolo indipendentemente dai dati nel sistema. 

Per il pool SQL, l'unità di scala è un'astrazione della potenza di calcolo nota come unità di [data warehouse.](resource-consumption-models.md) 

Per SQL on-demand, essendo senza server, la scalabilità viene eseguita automaticamente per soddisfare i requisiti delle risorse di query. Quando la topologia cambia nel tempo aggiungendo, rimuovendo nodi o failover, si adatta alle modifiche e assicura che la query disponga di risorse e funzionalità sufficienti. Ad esempio, l'immagine seguente mostra SQL su richiesta utilizzando 4 nodi di calcolo per eseguire una query.

![Architettura SQL Synapse](./media//overview-architecture/sql-architecture.png)

Synapse SQL utilizza un'architettura basata su nodi. Le applicazioni connettono ed emettono comandi T-SQL a un nodo Di controllo, ovvero il singolo punto di ingresso per Synapse SQL. 

Il nodo di controllo del pool SQL utilizza il motore MPP per ottimizzare le query per l'elaborazione parallela e quindi passa le operazioni ai nodi di calcolo per eseguire il proprio lavoro in parallelo. 

Il nodo Controllo su richiesta SQL utilizza il motore DQP (Distributed Query Processing) per ottimizzare e orchestrare l'esecuzione distribuita della query utente suddividendola in query più piccole che verranno eseguite nei nodi di calcolo. Ogni piccola query viene chiamata attività e rappresenta l'unità di esecuzione distribuita. Legge i file dall'archiviazione, unisce i risultati di altre attività, gruppi o ordina i dati recuperati da altre attività. 

I nodi di calcolo archiviano tutti i dati utente nell'archiviazione di Azure ed eseguono le query parallele. Data Movement Service (DMS) è un servizio interno a livello di sistema che sposta i dati tra i nodi per eseguire query in parallelo e restituire risultati accurati. 

Con l'archiviazione e il calcolo disaccoppiati, quando si usa Synapse SQL si può trarre vantaggio dal ridimensionamento indipendente della potenza di calcolo indipendentemente dalle esigenze di archiviazione. Per il ridimensionamento su richiesta SQL viene eseguita automaticamente, mentre per il pool SQL è possibile:For SQL on-demand scaling is done automatically, while for SQL pool one can:

* Aumentare o ridurre la potenza di calcolo, all'interno di un pool SQL (data warehouse), senza spostare i dati.
* Sospendere la capacità di calcolo mantenendo intatti i dati e pagando solo per l'archiviazione.
* Riprendere le capacità di calcolo durante l'orario operativo.

## <a name="azure-storage"></a>Archiviazione di Azure

Synapse SQL sfrutta Archiviazione di Azure per proteggere i dati utente. Poiché i dati vengono archiviati e gestiti da Archiviazione di Azure, è previsto un addebito separato per l'utilizzo dello spazio di archiviazione. 

SQL on-demand consente di eseguire query sui file nel data lake in modalità di sola lettura, mentre il pool SQL consente di ingerire i dati. Quando i dati vengono inseriti nel pool SQL, i dati vengono inseriti in **distribuzioni** per ottimizzare le prestazioni del sistema. È possibile scegliere il modello di partizionamento orizzontale da usare per distribuire i dati quando si definisce la tabella. Sono supportati questi modelli di partizionamento di partizione:These sharding patterns are supported:

* Hash
* Round robin
* Replica

## <a name="control-node"></a>Nodo di controllo

Il nodo Control è il cervello dell'architettura. È il front-end che interagisce con tutte le applicazioni e le connessioni. 

Nel pool SQL, il motore MPP viene eseguito nel nodo Control per ottimizzare e coordinare le query parallele. Quando si invia una query T-SQL al pool SQL, il nodo Control la trasforma in query eseguite su ogni distribuzione in parallelo.

In SQL on-demand, il motore DQP viene eseguito nel nodo Control per ottimizzare e coordinare l'esecuzione distribuita della query utente suddividendola in query più piccole che verranno eseguite nei nodi di calcolo. Assegna inoltre set di file che devono essere elaborati da ogni nodo.

## <a name="compute-nodes"></a>Nodi di calcolo

I nodi di calcolo forniscono la potenza di calcolo. 

Nel pool SQL le distribuzioni eseguono il mapping ai nodi di calcolo per l'elaborazione. Quando si paga per più risorse di calcolo, il pool rimappa le distribuzioni ai nodi Compute disponibili. Il numero di nodi di calcolo è compreso tra 1 e 60 ed è determinato dal livello di servizio per il pool SQL. Ogni nodo di calcolo ha un ID visibile nelle visualizzazioni di sistema. È possibile individuare l'ID del nodo di calcolo cercando la colonna node_id nelle visualizzazioni di sistema il cui nome inizia con sys.pdw_nodes. Per un elenco delle visualizzazioni di sistema, vedere le [visualizzazioni di sistema MPP](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest).

In SQL su richiesta, a ogni nodo di calcolo vengono assegnati attività e set di file su cui eseguire l'attività. L'attività è un'unità di esecuzione delle query distribuita, che fa effettivamente parte della query inviata dall'utente. Il ridimensionamento automatico è attivo per assicurarsi che vengano utilizzati un numero sufficiente di nodi di calcolo per eseguire query utente.

## <a name="data-movement-service"></a>Data Movement Service

Data Movement Service (DMS) è la tecnologia di trasporto dati nel pool SQL che coordina lo spostamento dei dati tra i nodi di calcolo. Alcune query richiedono lo spostamento dei dati per garantire che le query parallele restituiscano risultati accurati. Quando lo spostamento dei dati è necessario, DMS assicura che i dati corretti vengano spostati nel percorso corretto.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Distribuzioni

Una distribuzione è l'unità di base di archiviazione ed elaborazione per le query parallele eseguite su dati distribuiti nel pool SQL. Quando il pool SQL esegue una query, il lavoro viene suddiviso in 60 query più piccole che vengono eseguite in parallelo. 

Ognuna delle 60 query viene eseguita in una distribuzione dei dati. Ogni nodo di calcolo gestisce una o più delle 60 distribuzioni. Un pool SQL con risorse di calcolo massime ha una distribuzione per ogni nodo di calcolo. Un pool SQL con risorse di calcolo minime include tutte le distribuzioni in un nodo di calcolo. 

## <a name="hash-distributed-tables"></a>Tabelle con distribuzione hash
Una tabella con distribuzione hash offre prestazioni di query più elevate per join e aggregazioni in tabelle di grandi dimensioni. 

Per partizionare i dati in una tabella con distribuzione hash, il pool SQL utilizza una funzione hash per assegnare in modo deterministico ogni riga a una distribuzione. Nella definizione della tabella una delle colonne è definita come colonna di distribuzione. La funzione hash usa i valori della colonna di distribuzione per assegnare ogni riga a una distribuzione.

La figura seguente illustra come una tabella completa non distribuita viene archiviata come tabella con distribuzione hash. 

![Tabella distribuita](media//overview-architecture/hash-distributed-table.png "Tabella distribuita") 

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

![Tabella replicata](media/overview-architecture/replicated-table.png "Tabella replicata") 

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce un po'di Synapse SQL, imparare a creare rapidamente [un pool SQL](../quickstart-create-sql-pool.md) e caricare i dati di [esempio](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) (./sql-data-warehouse-load-sample-databases.md). Oppure si inizia a [utilizzare SQL su richiesta](../quickstart-sql-on-demand.md). Se non si ha familiarità con Azure, il [glossario di Azure](../../azure-glossary-cloud-terminology.md) può essere utile quando si incontrano termini nuovi. 
