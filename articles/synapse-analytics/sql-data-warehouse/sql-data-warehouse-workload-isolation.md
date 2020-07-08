---
title: Isolamento dei carichi di lavoro
description: Indicazioni per la configurazione dell'isolamento del carico di lavoro con gruppi di carico di lavoro in Azure Synapse Analytics.
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
ms.openlocfilehash: a9ebee68c7abd90f5fb3345eec1ee929fc30ca20
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212310"
---
# <a name="azure-synapse-analytics-workload-group-isolation"></a>Isolamento del gruppo di carico di lavoro di Azure Synapse Analytics

Questo articolo illustra come usare i gruppi di carico di lavoro per configurare l'isolamento del carico di lavoro, contenere le risorse e applicare regole di runtime per l'esecuzione delle query.

## <a name="workload-groups"></a>Gruppi di carichi di lavoro

I gruppi di carico di lavoro sono contenitori per un set di richieste e su di essi si basa la configurazione della gestione del carico di lavoro in un sistema, incluso l'isolamento del carico di lavoro.  I gruppi di carico di lavoro vengono creati usando la sintassi [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Una semplice configurazione della gestione del carico di lavoro consente di gestire carichi di dati e query utente.  Ad esempio, un gruppo di carico di lavoro denominato `wgDataLoads` definirà gli aspetti del carico di lavoro per i dati caricati nel sistema. Inoltre, un gruppo di carico di lavoro denominato `wgUserQueries` definirà gli aspetti del carico di lavoro per gli utenti che eseguono query per leggere dati dal sistema.

Nelle sezioni seguenti verrà evidenziato in che modo i gruppi di carico di lavoro consentono di definire l'isolamento, il contenimento e la definizione della risorsa della richiesta e di aderire alle regole di esecuzione.

## <a name="workload-isolation"></a>Isolamento dei carichi di lavoro

L'isolamento del carico di lavoro significa che le risorse sono riservate esclusivamente per un gruppo di carico di lavoro.  L'isolamento del carico di lavoro si ottiene configurando il parametro MIN_PERCENTAGE_RESOURCE su un valore maggiore di zero nella sintassi [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Per i carichi di lavoro a esecuzione continua che devono essere conformi a Contratti di servizio rigorosi, l'isolamento garantisce che le risorse siano sempre disponibili per il gruppo di carico di lavoro.

La configurazione dell'isolamento del carico di lavoro definisce in modo implicito un livello di concorrenza garantita. Ad esempio, a un gruppo di carico di lavoro con `MIN_PERCENTAGE_RESOURCE` impostato sul 30% e `REQUEST_MIN_RESOURCE_GRANT_PERCENT` impostato sul 2% è garantita una concorrenza di 15.  Il livello di concorrenza è garantito perché il 15-2% degli slot di risorse è riservato all'interno del gruppo di carico di lavoro in qualsiasi momento, indipendentemente dalla configurazione di `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT`.  Se `REQUEST_MAX_RESOURCE_GRANT_PERCENT` è maggiore di `REQUEST_MIN_RESOURCE_GRANT_PERCENT` e `CAP_PERCENTAGE_RESOURCE` è maggiore di `MIN_PERCENTAGE_RESOURCE`, vengono aggiunte ulteriori risorse per ogni richiesta.  Se `REQUEST_MAX_RESOURCE_GRANT_PERCENT` e `REQUEST_MIN_RESOURCE_GRANT_PERCENT` sono uguali e `CAP_PERCENTAGE_RESOURCE` è maggiore di `MIN_PERCENTAGE_RESOURCE`, è possibile un'ulteriore concorrenza.  Prendere in considerazione il metodo seguente per determinare la concorrenza garantita:

[Concorrenza garantita] = [`MIN_PERCENTAGE_RESOURCE`] / [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE]
> Per min_percentage_resource ci sono valori validi minimi specifici per il livello di servizio.  Per altre informazioni, vedere [Valori effettivi](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#effective-values).

In assenza di isolamento del carico di lavoro, le richieste funzionano nel [pool condiviso](#shared-pool-resources) di risorse.  L'accesso alle risorse nel pool condiviso non è garantito e viene assegnato in base all'[importanza](sql-data-warehouse-workload-importance.md).

La configurazione dell'isolamento del carico di lavoro deve essere eseguita con cautela, poiché le risorse vengono allocate al gruppo di carico di lavoro anche se non ci sono richieste attive nel gruppo di carico di lavoro. Una configurazione eccessiva dell'isolamento può comportare una riduzione dell'utilizzo complessivo del sistema.

Gli utenti dovrebbero evitare una soluzione di gestione del carico di lavoro che configura l'isolamento del carico di lavoro al 100%: l'isolamento al 100% si ottiene quando la somma di min_percentage_resource configurata in tutti i gruppi di carico di lavoro è uguale a 100%.  Questo tipo di configurazione è eccessivamente rigida e restrittiva e lascia poco spazio alle richieste di risorse accidentalmente classificate in modo errato. Esiste la possibilità di consentire l'esecuzione di una richiesta da gruppi di carico di lavoro non configurati per l'isolamento. Le risorse allocate per questa richiesta verranno visualizzate come zero nei sistemi DMV e prendono in prestito un livello inferiore di concessione di risorse dalle risorse riservate del sistema.

> [!NOTE]
> Per garantire un utilizzo ottimale delle risorse, prendere in considerazione una soluzione di gestione del carico di lavoro che sfrutti un certo isolamento per garantire che i Contratti di servizio siano soddisfatti e combinati con risorse condivise a cui si accede in base all'[importanza del carico di lavoro](sql-data-warehouse-workload-importance.md).

## <a name="workload-containment"></a>Contenimento del carico di lavoro

Il contenimento del carico di lavoro fa riferimento alla limitazione della quantità di risorse che un gruppo di carico di lavoro può utilizzare.  Il contenimento del carico di lavoro si ottiene configurando il parametro CAP_PERCENTAGE_RESOURCE su un valore inferiore a 100 nella sintassi [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Si consideri lo scenario in cui gli utenti necessitano dell'accesso in lettura al sistema in modo da poter eseguire un'analisi di simulazione tramite query ad hoc.  Questi tipi di richieste potrebbero avere un impatto negativo su altri carichi di lavoro in esecuzione nel sistema.  La configurazione del contenimento garantisce che la quantità di risorse sia limitata.

La configurazione del contenimento del carico di lavoro definisce in modo implicito un livello di concorrenza massima.  Con CAP_PERCENTAGE_RESOURCE impostato sul 60% e REQUEST_MIN_RESOURCE_GRANT_PERCENT impostato sull'1%, per il gruppo di carico di lavoro è consentito un livello di concorrenza di un massimo di 60.  Si consideri il metodo riportato di seguito per determinare la concorrenza massima:

[Concorrenza massima] = [`CAP_PERCENTAGE_RESOURCE`] / [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE]
> Il valore effettivo di CAP_PERCENTAGE_RESOURCE di un gruppo di carico di lavoro non raggiungerà il 100% quando vengono creati gruppi di carico di lavoro con MIN_PERCENTAGE_RESOURCE a un livello maggiore di zero.  Per i valori di runtime effettivi, vedere [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="resources-per-request-definition"></a>Definizione delle risorse per richiesta

I gruppi di carico di lavoro forniscono un meccanismo per definire la quantità minima e massima di risorse allocate per ogni richiesta con i parametri REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT nella sintassi [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Le risorse in questo caso sono la CPU e la memoria.  La configurazione di questi valori determina la quantità di risorse e il livello di concorrenza che è possibile ottenere nel sistema.

> [!NOTE]
> REQUEST_MAX_RESOURCE_GRANT_PERCENT è un parametro facoltativo che usa per impostazione predefinita lo stesso valore specificato per REQUEST_MIN_RESOURCE_GRANT_PERCENT.

Come per la scelta di una classe di risorse, la configurazione di REQUEST_MIN_RESOURCE_GRANT_PERCENT imposta il valore per le risorse utilizzate da una richiesta.  La quantità di risorse indicata dal valore impostato è garantita per l'allocazione alla richiesta prima che inizi l'esecuzione.  Per i clienti che eseguono la migrazione dalle classi di risorse ai gruppi di carico di lavoro, provare a seguire le indicazioni dell'[articolo sulle procedure](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) per eseguire il mapping dalle classi di risorse ai gruppi di carico di lavoro come punto iniziale.

La configurazione di REQUEST_MAX_RESOURCE_GRANT_PERCENT su un valore maggiore di REQUEST_MIN_RESOURCE_GRANT_PERCENT consente al sistema di allocare più risorse per ogni richiesta.  Durante la pianificazione di una richiesta, il sistema determina l'allocazione effettiva delle risorse alla richiesta, che è compresa tra REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT, in base alla disponibilità delle risorse nel pool condiviso e al carico corrente nel sistema.  Le risorse devono essere presenti nel [pool condiviso](#shared-pool-resources) di risorse quando la query viene pianificata.  

> [!NOTE]
> REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT hanno valori effettivi che dipendono dai valori effettivi di MIN_PERCENTAGE_RESOURCE e CAP_PERCENTAGE_RESOURCE.  Per i valori di runtime effettivi, vedere [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="execution-rules"></a>Regole di esecuzione

Nei sistemi di reporting ad hoc i clienti possono eseguire accidentalmente query con eccessivo tempo di esecuzione che incidono gravemente sulla produttività degli altri utenti.  Gli amministratori di sistema sono costretti a dedicare tempo a terminare le query con eccessivo tempo di esecuzione per liberare le risorse di sistema.  I gruppi di carico di lavoro offrono la possibilità di configurare una regola di timeout per l'esecuzione della query per annullare le query che hanno superato il valore specificato.  La regola viene configurata impostando il parametro `QUERY_EXECUTION_TIMEOUT_SEC` nella sintassi [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="shared-pool-resources"></a>Risorse del pool condiviso

Le risorse del pool condiviso sono le risorse non configurate per l'isolamento.  I gruppi di carico di lavoro con un valore MIN_PERCENTAGE_RESOURCE impostato su zero sfruttano le risorse nel pool condiviso per l'esecuzione delle richieste.  Anche i gruppi di carico di lavoro con un valore CAP_PERCENTAGE_RESOURCE maggiore di MIN_PERCENTAGE_RESOURCE hanno usato le risorse condivise.  La quantità di risorse disponibili nel pool condiviso viene calcolata come indicato di seguito.

[Pool condiviso] = 100 - [somma di `MIN_PERCENTAGE_RESOURCE` tra tutti i gruppi di carico di lavoro]

L'accesso alle risorse nel pool condiviso viene allocato in base all'[importanza](sql-data-warehouse-workload-importance.md).  Le richieste con lo stesso livello di importanza accederanno alle risorse del pool condiviso su base FIFO (First In First Out).

## <a name="next-steps"></a>Passaggi successivi

- [Avvio rapido: configurare l'isolamento del carico di lavoro](quickstart-configure-workload-isolation-tsql.md)
- [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Convertire le classi di risorse in gruppi di carico di lavoro](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md)
- [Monitoraggio del portale di gestione del carico di lavoro](sql-data-warehouse-workload-management-portal-monitor.md)  
