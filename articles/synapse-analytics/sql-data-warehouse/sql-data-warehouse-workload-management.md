---
title: Gestione dei carichi di lavoro
description: Linee guida per l'implementazione della gestione del carico di lavoro in Azure Synapse Analytics.Guidance for implementing workload management in Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: dd867d4aa9a9ef5ed73e78a46826a8cd5239039b
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744226"
---
# <a name="what-is-workload-management"></a>Definizione di gestione del carico di lavoro

L'esecuzione di carichi di lavoro misti può comportare problemi di risorse nei sistemi occupati.  Gli architetti di soluzioni cercano modi per separare le attività di data warehousing classiche (ad esempio il caricamento, la trasformazione e l'esecuzione di query sui dati) per assicurarsi che esistano risorse sufficienti per raggiungere i contratti di servizio.  

L'isolamento fisico del server può portare a sacche di infrastruttura sottoutilizzate, in overbooking o in uno stato in cui le cache vengono costantemente innescate con l'avvio e l'arresto dell'hardware.  Un sistema di gestione efficace del carico di lavoro gestisce efficacemente le risorse, garantisce un utilizzo delle risorse altamente efficiente e massimizza il ritorno sull'investimento (ROI).

Un carico di lavoro del data warehouse fa riferimento a tutte le operazioni che si verificano in relazione a un data warehouse. La profondità e l'ampiezza di questi componenti dipendono dal livello di maturità del data warehouse.  Il carico di lavoro del data warehouse comprende:

- L'intero processo di caricamento dei dati nel magazzino
- Esecuzione di analisi e report del data warehouse
- Gestione dei dati nel data warehouse
- Esportazione di dati dal data warehouse

La capacità a livello di prestazioni di un data warehouse è determinata dalle [unità di data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md).

- Per visualizzare le risorse allocate per tutti i profili delle prestazioni, vedere Limiti di [memoria e concorrenza](memory-concurrency-limits.md).
- Per regolare la capacità, è possibile [aumentare o ridurre](quickstart-scale-compute-portal.md)la dimensione.

## <a name="workload-management-concepts"></a>Concetti di gestione del carico di lavoroWorkload management concepts

In passato, per analisi SQL in Azure Synapse è stata gestita la prestazione delle query tramite le [classi di risorse.](resource-classes-for-workload-management.md)  Classi di risorse consentite per l'assegnazione di memoria a una query in base all'appartenenza al ruolo.  La sfida principale con le classi di risorse è che, una volta configurate, non vi era alcuna governance o capacità di controllare il carico di lavoro.  

Ad esempio, la concessione di un'appartenenza a un ruolo utente ad hoc a smallrc ha consentito all'utente di utilizzare il 100% della memoria nel sistema.  Con le classi di risorse, non è possibile riservare e garantire che le risorse siano disponibili per carichi di lavoro critici.

La gestione del carico di lavoro del pool SQL Synapse in Azure Synapse è costituita da tre concetti di alto livello: [Classificazione del carico di lavoro](sql-data-warehouse-workload-classification.md), [Importanza del carico di lavoro](sql-data-warehouse-workload-importance.md) e Isolamento del carico di [lavoro](sql-data-warehouse-workload-isolation.md).  Queste funzionalità offrono un maggiore controllo sull'utilizzo delle risorse di sistema da parte del carico di lavoro.

La classificazione del carico di lavoro è il concetto di assegnazione di una richiesta a un gruppo di carico di lavoro e di impostazione dei livelli di importanza.  Storicamente, questa assegnazione è stata eseguita tramite l'appartenenza al ruolo utilizzando [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Questa operazione può ora essere eseguita tramite [CREATE WORKLOAD CLASSIFER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  La funzionalità di classificazione offre un set più completo di opzioni, ad esempio etichetta, sessione e tempo per classificare le richieste.

L'importanza del carico di lavoro influisce sull'ordine in cui una richiesta ottiene l'accesso alle risorse.  In un sistema occupato, una richiesta con maggiore importanza ha il primo accesso alle risorse.  L'importanza può anche garantire l'accesso ordinato ai blocchi.

L'isolamento del carico di lavoro riserva le risorse per un gruppo di carico di lavoro.  Le risorse riservate in un gruppo di carico di lavoro vengono mantenute esclusivamente per tale gruppo di carico di lavoro per garantire l'esecuzione.  I gruppi di carico di lavoro consentono inoltre di definire la quantità di risorse assegnate per ogni richiesta, in modo analogo alle classi di risorse.  I gruppi di carico di lavoro offrono la possibilità di riservare o captare la quantità di risorse che un set di richieste può utilizzare.  Infine, i gruppi di carico di lavoro sono un meccanismo per applicare le regole, ad esempio il timeout delle query, alle richieste.  

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla classificazione del carico di lavoro, vedere [Classificazione del carico di lavoro.](sql-data-warehouse-workload-classification.md)  
- Per altre informazioni sull'isolamento del carico di lavoro, vedere [Isolamento del carico di lavoro.](sql-data-warehouse-workload-isolation.md)  
- Per altre informazioni sull'importanza del carico di lavoro, vedere [Priorità del carico di lavoro.](sql-data-warehouse-workload-importance.md)  
- Per ulteriori informazioni sul monitoraggio della gestione del carico di lavoro, vedere Monitoraggio del portale di [gestione del carico di lavoro](sql-data-warehouse-workload-management-portal-monitor.md).  
