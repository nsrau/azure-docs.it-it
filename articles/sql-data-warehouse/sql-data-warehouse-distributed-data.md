---
title: Funzionamento dei dati distribuiti in Azure SQL Data Warehouse | Documentazione Microsoft
description: Informazioni sulla distribuzione dei dati per l'elaborazione parallela elevata (Massively Parallel Processing, MPP) e sulle opzioni di distribuzione delle tabelle in Azure SQL Data Warehouse e Parallel Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: bae494a6-7ac5-4c38-8ca3-ab2696c63a9f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 07/12/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 3c166acb17193caae32d7bad133ec510ff679353
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="distributed-data-and-distributed-tables-for-massively-parallel-processing-mpp"></a>Dati distribuiti e tabelle distribuite per l'elaborazione parallela elevata (Massively Parallel Processing, MPP)
Informazioni sulla distribuzione dei dati utente in Azure SQL Data Warehouse e Parallel Data Warehouse, i sistemi di elaborazione parallela elevata (Massively Parallel Processing, MPP) di Microsoft. La progettazione di un data warehouse che usa dati distribuiti consente di ottenere i vantaggi derivanti dall'elaborazione di query dell'architettura MPP. Un numero limitato di scelte nella progettazione dei database può avere un impatto significativo nel miglioramento delle prestazioni delle query.  

> [!NOTE]
> Azure SQL Data Warehouse e Parallel Data Warehouse usano la stessa struttura di elaborazione parallela elevata (Massively Parallel Processing, MPP), con alcune differenze dovute alla piattaforma sottostante. SQL Data Warehouse è una piattaforma distribuita come servizio (PaaS) eseguita in Azure. Parallel Data Warehouse viene eseguita in APS (Analytics Platform System) un'appliance locale eseguita in Windows Server.
> 
> 

## <a name="what-is-distributed-data"></a>Che cosa sono i dati distribuiti?
In SQL Data Warehouse e Parallel Data Warehouse, i dati distribuiti fanno riferimento a dati utente archiviati in più percorsi nel sistema MPP. Ognuno di questi percorsi svolge la funzione di archivio indipendente e unità di elaborazione che esegue query nella relativa parte di dati. I dati distribuiti sono fondamentali per l'esecuzione parallela di query per ottenere prestazioni elevate.

Per distribuire i dati, il data warehouse assegna ogni riga della tabella utente a un unico percorso distribuito.  È possibile distribuire le tabelle con un metodo di distribuzione hash o un metodo round robin. Il metodo di distribuzione è specificato nell'istruzione CREATE TABLE. 

## <a name="hash-distributed-tables"></a>Tabelle con distribuzione hash
La figura seguente illustra come una tabella completa non distribuita viene archiviata come tabella con distribuzione hash. Una funzione deterministica assegna ogni riga in modo che appartenga a una sola distribuzione. Nella definizione della tabella una delle colonne è definita come colonna di distribuzione. La funzione hash usa il valore della colonna di distribuzione per assegnare ciascuna riga a una distribuzione.

Ai fini delle prestazioni, per la selezione di una colonna di distribuzione è necessario considerare alcuni aspetti, ad esempio la specificità, la differenza dei dati e i tipi di query eseguite nel sistema.

![Tabella distribuita](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Tabella distribuita")  

* Ogni riga appartiene a una sola distribuzione.  
* Un algoritmo hash deterministico assegna ogni riga a una sola distribuzione.  
* Il numero di righe di tabella per distribuzione varia come mostrano le diverse dimensioni delle tabelle.

## <a name="round-robin-distributed-tables"></a>Tabelle con distribuzione round robin
Una tabella con distribuzione round robin distribuisce le righe assegnando ciascuna riga a una distribuzione in modo sequenziale. Il caricamento dei dati in una tabella round robin è rapido, ma le prestazioni delle query potrebbero essere inferiori.  L'aggiunta di una tabella round robin richiede in genere una ridistribuzione delle righe per consentire alla query di restituire un risultato accurato, un'operazione che richiede tempo.

## <a name="distributed-storage-locations-are-called-distributions"></a>Le posizioni di archiviazione distribuite sono chiamate distribuzioni
Ogni posizione distribuita è una distribuzione. Quando viene eseguita una query in parallelo, ogni distribuzione esegue una query SQL nella relativa parte di dati. SQL Data Warehouse usa il database SQL per eseguire la query. Parallel Data Warehouse usa SQL Server per eseguire la query. Questa architettura priva di condivisione è fondamentale per ottenere un'elaborazione parallela con scalabilità orizzontale.

### <a name="can-i-view-the-distributions"></a>È possibile visualizzare le distribuzioni?
Ogni distribuzione ha un ID distribuzione ed è visibile nelle visualizzazioni di sistema relative a SQL Data Warehouse e Parallel Data Warehouse. È possibile usare l'ID distribuzione per risolvere i problemi di prestazioni delle query e altri problemi. Per un elenco delle visualizzazioni di sistema, vedere la [visualizzazione di sistema MPP](sql-data-warehouse-reference-tsql-statements.md).

## <a name="difference-between-a-distribution-and-a-compute-node"></a>Differenza tra una distribuzione e un nodo di calcolo
Una distribuzione è l'unità di base per l'archiviazione dei dati distribuiti e l'elaborazione di query parallela. Le distribuzioni sono raggruppate in nodi di calcolo. Ogni nodo di calcolo tiene traccia di una o più distribuzioni.  

* Analytics Platform System usa i nodi di calcolo come componente fondamentale dell'architettura hardware e delle funzionalità di scalabilità orizzontale. Vengono sempre usate otto distribuzioni per ogni nodo di calcolo, come illustrato nella figura per le tabelle con distribuzione hash. Il numero di nodi di calcolo e pertanto il numero di distribuzioni è determinato dal numero di nodi di calcolo acquistati per il dispositivo. Ad esempio, se si acquistano otto nodi di calcolo, si avranno a disposizione 64 distribuzioni (8 nodi di calcolo x 8 distribuzioni/nodo). 
* SQL Data Warehouse ha un numero fisso di 60 distribuzioni e un numero variabile di nodi di calcolo. I nodi di calcolo vengono implementati con le risorse di elaborazione e di archiviazione di Azure. Il numero di nodi di calcolo può variare in base al carico di lavoro del servizio back-end e alla capacità di elaborazione (DWU) specificata per il data warehouse. Quando il numero di nodi di calcolo viene modificato, viene modificato anche il numero di distribuzioni per nodo di calcolo. 

### <a name="can-i-view-the-compute-nodes"></a>È possibile visualizzare i nodi di calcolo?
Ogni nodo di calcolo ha un ID nodo ed è visibile nelle visualizzazioni di sistema relative a SQL Data Warehouse e Parallel Data Warehouse.  È possibile visualizzare il nodo di calcolo cercando la colonna node_id nelle visualizzazioni di sistema il cui nome inizia con sys.pdw_nodes. Per un elenco delle visualizzazioni di sistema, vedere la [visualizzazione di sistema MPP](sql-data-warehouse-reference-tsql-statements.md).

## <a name="Replicated"></a>Tabelle replicate
Una tabella replicata include una copia completa della tabella archiviata in ogni nodo di calcolo. La replica di una tabella elimina la necessità di trasferire dati tra i nodi di calcolo prima di un join o un'aggregazione. Le tabelle replicate sono realizzabili con tabelle di piccole dimensioni a causa della memoria aggiuntiva necessaria per archiviare l'intera tabella in ogni nodo di calcolo.  

La figura seguente illustra una tabella replicata archiviata in ogni nodo di calcolo. Per SQL Data Warehouse, la tabella replicata viene copiata per intero in un database di distribuzione in ogni nodo di calcolo. Per Parallel Data Warehouse, la tabella replicata è archiviata in tutti i dischi assegnati al nodo di calcolo.  Questa strategia di archiviazione su disco viene implementata usando i filegroup di SQL Server.  

![Tabella replicata](media/sql-data-warehouse-distributed-data/replicated-table.png "Tabella replicata") 

## <a name="next-steps"></a>Passaggi successivi
Per un uso efficace delle tabelle distribuite, vedere [Distributing tables in SQL Data Warehouse](sql-data-warehouse-tables-distribute.md) (Distribuzione di tabelle in SQL Data Warehouse)  

