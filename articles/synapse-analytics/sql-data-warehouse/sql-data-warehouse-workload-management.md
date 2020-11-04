---
title: Gestione dei carichi di lavoro
description: Indicazioni per l'implementazione della gestione del carico di lavoro in Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 761976741bf794a21182e8f962b274ae32925060
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324481"
---
# <a name="what-is-workload-management"></a>Definizione di gestione del carico di lavoro

L'esecuzione di carichi di lavoro misti può causare problemi di risorse nei sistemi sovraccarichi.  Gli architetti di soluzioni aspirano a separare le classiche attività di data warehousing, come il caricamento, la trasformazione e l'esecuzione di query sui dati, per assicurare la disponibilità di una quantità di risorse sufficiente per soddisfare i contratti di servizio.  

L'isolamento dei server fisici può portare a sacche dell'infrastruttura che risultano sottoutilizzate, sovraprenotate o in uno stato in cui le cache vengono costantemente caricate con l'avvio e l'arresto di hardware.  Uno schema corretto di gestione del carico di lavoro comporta una gestione efficace delle risorse, ne assicura l'utilizzo altamente efficiente e massimizza il ritorno sugli investimenti.

Per carico di lavoro del data warehouse si intendono tutte le operazioni che si verificano in relazione a un data warehouse. La varietà e la complessità di questi componenti variano in base al livello di maturità del data warehouse.  Il carico di lavoro del data warehouse comprende:

- L'intero processo di caricamento dei dati nel warehouse
- L'esecuzione di analisi e report del data warehouse
- La gestione dei dati nel data warehouse
- L'esportazione di dati dal data warehouse

La capacità a livello di prestazioni di un data warehouse è determinata dalle [unità di data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md).

- Per visualizzare le risorse allocate per tutti i profili di prestazioni, vedere [Limiti di concorrenza e memoria](memory-concurrency-limits.md).
- Per regolare la capacità, è possibile [aumentare o ridurre la quantità di risorse](quickstart-scale-compute-portal.md).

## <a name="workload-management-concepts"></a>Concetti relativi alla gestione del carico di lavoro

In passato, per Synapse SQL in Azure Synapse le prestazioni delle query venivano gestite tramite [classi di risorse](resource-classes-for-workload-management.md).  Le classi di risorse consentivano l'assegnazione di memoria a una query in base all'appartenenza ai ruoli.  Il problema principale delle classi di risorse è che, una volta configurate, non è possibile controllare o regolamentare il carico di lavoro.  

Ad esempio, la concessione di un'appartenenza a un ruolo utente ad hoc a smallrc consente all'utente di utilizzare il 100% della memoria nel sistema.  Con le classi di risorse, non è possibile riservare e garantire la disponibilità di risorse per i carichi di lavoro critici.

La gestione del carico di lavoro del pool SQL dedicato in sinapsi di Azure è costituita da tre concetti di alto livello: [classificazione del carico](sql-data-warehouse-workload-classification.md)di lavoro, [importanza del carico di lavoro](sql-data-warehouse-workload-importance.md)e [isolamento](sql-data-warehouse-workload-isolation.md)  Queste funzionalità offrono un maggior controllo sul modo in cui il carico di lavoro utilizza le risorse di sistema.

La classificazione del carico di lavoro consiste nell'assegnare una richiesta a un gruppo di carico di lavoro e impostare i livelli di priorità.  Nel passato, questa assegnazione veniva eseguita tramite l'appartenenza ai ruoli con [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Questa azione può ora essere eseguita tramite [CREATE WORKLOAD CLASSIFER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  La funzionalità di classificazione offre un set più completo di opzioni, come etichetta, sessione e tempo, per classificare le richieste.

La priorità del carico di lavoro influisce sull'ordine in cui una richiesta ottiene l'accesso alle risorse.  In un sistema sovraccarico una richiesta con priorità più alta ottiene per prima l'accesso alle risorse.  La priorità può anche assicurare un accesso ordinato ai blocchi.

L'isolamento del carico di lavoro consente di riservare risorse per un gruppo di carico di lavoro.  Le risorse riservate in un gruppo di carico di lavoro vengono mantenute esclusivamente per tale gruppo per garantire l'esecuzione.  I gruppi di carico di lavoro consentono anche di definire la quantità di risorse assegnate per ogni richiesta, in modo simile alle classi di risorse.  I gruppi di carico di lavoro offrono la possibilità di riservare o limitare la quantità di risorse utilizzabili da un set di richieste.  Infine, i gruppi di carico di lavoro rappresentano un meccanismo per applicare regole, ad esempio il timeout delle query, alle richieste.  

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla classificazione del carico di lavoro, vedere [Classificazione del carico di lavoro](sql-data-warehouse-workload-classification.md).  
- Per altre informazioni sull'isolamento del carico di lavoro, vedere [Isolamento del carico di lavoro](sql-data-warehouse-workload-isolation.md).  
- Per altre informazioni sulla priorità del carico di lavoro, vedere [Priorità del carico di lavoro](sql-data-warehouse-workload-importance.md).  
- Per altre informazioni sul monitoraggio della gestione del carico di lavoro, vedere [Monitoraggio del portale di gestione del carico di lavoro](sql-data-warehouse-workload-management-portal-monitor.md).  
