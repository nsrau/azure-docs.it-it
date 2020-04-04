---
title: Isolamento dei carichi di lavoro
description: Indicazioni per l'impostazione dell'isolamento del carico di lavoro con i gruppi del carico di lavoro in Azure Synapse Analytics.Guidance for setting workload isolation with workload groups in Azure Synapse Analytics.
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
ms.openlocfilehash: c3fcbf69e7dae14ccd2114a14c685b0443f70fef
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632445"
---
# <a name="azure-synapse-analytics-workload-group-isolation-preview"></a>Isolamento del gruppo di carico di lavoro di Azure Synapse Analytics (anteprima)Azure Synapse Analytics workload group isolation (Preview)

In questo articolo viene illustrato come utilizzare i gruppi del carico di lavoro per configurare l'isolamento del carico di lavoro, contenere risorse e applicare regole di runtime per l'esecuzione delle query.

## <a name="workload-groups"></a>Gruppi di carichi di lavoro

I gruppi del carico di lavoro sono contenitori per un set di richieste e costituiscono la base per la configurazione della gestione del carico di lavoro, incluso l'isolamento del carico di lavoro, in un sistema.  I gruppi di carico di lavoro vengono creati utilizzando la sintassi [CREATE WORKLOAD GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Una semplice configurazione di gestione del carico di lavoro può gestire i carichi di dati e le query degli utenti.  Ad esempio, un `wgDataLoads` gruppo di carico di lavoro denominato definirà gli aspetti del carico di lavoro per i dati caricati nel sistema. Inoltre, un gruppo `wgUserQueries` di carico di lavoro denominato definirà gli aspetti del carico di lavoro per gli utenti che eseguono query per leggere i dati dal sistema.

Le sezioni seguenti evidenziano il modo in cui i gruppi di carico di lavoro consentono di definire l'isolamento, il contenimento, la definizione delle risorse di richiesta e l'applicazione alle regole di esecuzione.

## <a name="workload-isolation"></a>Isolamento dei carichi di lavoro

Isolamento del carico di lavoro significa che le risorse sono riservate, esclusivamente, per un gruppo di carico di lavoro.  L'isolamento del carico di lavoro si ottiene configurando il parametro MIN_PERCENTAGE_RESOURCE su maggiore di zero nella sintassi [CREATE WORKLOAD GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Per i carichi di lavoro di esecuzione continua che devono aderire a contratti di servizio limitati, l'isolamento garantisce che le risorse siano sempre disponibili per il gruppo di carico di lavoro.

La configurazione dell'isolamento del carico di lavoro definisce in modo implicito un livello di concorrenza garantito. Ad esempio, un gruppo `MIN_PERCENTAGE_RESOURCE` di carico di `REQUEST_MIN_RESOURCE_GRANT_PERCENT` lavoro con un impostato su 30% e impostato su 2% è garantita 15 concorrenza.  Il livello di concorrenza è garantito perché gli slot di risorse 15-2% sono sempre `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT` riservati all'interno del gruppo del carico di lavoro (indipendentemente dalla configurazione).  Se `REQUEST_MAX_RESOURCE_GRANT_PERCENT` è `REQUEST_MIN_RESOURCE_GRANT_PERCENT` maggiore `CAP_PERCENTAGE_RESOURCE` e `MIN_PERCENTAGE_RESOURCE` è maggiore di risorse aggiuntive vengono aggiunte per ogni richiesta.  Se `REQUEST_MAX_RESOURCE_GRANT_PERCENT` `REQUEST_MIN_RESOURCE_GRANT_PERCENT` e sono `CAP_PERCENTAGE_RESOURCE` uguali `MIN_PERCENTAGE_RESOURCE`ed è maggiore di , è possibile una concorrenza aggiuntiva.  Prendere in considerazione il metodo seguente per determinare la concorrenza garantita:Consider the below method for determining guaranteed concurrency:

[Concorrenza garantita] -`MIN_PERCENTAGE_RESOURCE`[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`] / [ ]

> [!NOTE]
> Esistono valori minimi possibili specifici per il livello di servizio per min_percentage_resource.  Per altre informazioni, vedere [Valori effettivi](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values) per ulteriori dettagli.

In assenza di isolamento del carico di lavoro, le richieste operano nel [pool condiviso](#shared-pool-resources) di risorse.  L'accesso alle risorse nel pool condiviso non è garantito e viene assegnato in base [all'importanza.](sql-data-warehouse-workload-importance.md)

La configurazione dell'isolamento del carico di lavoro deve essere eseguita con cautela in quanto le risorse vengono allocate al gruppo di carico di lavoro anche se non sono presenti richieste attive nel gruppo del carico di lavoro. L'isolamento sovraconfigurante può ridurre l'utilizzo complessivo del sistema.

Gli utenti devono evitare una soluzione di gestione del carico di lavoro che configura l'isolamento del carico di lavoro al 100%: l'isolamento del 100% viene ottenuto quando la somma di min_percentage_resource configurata in tutti i gruppi di carico di lavoro è uguale al 100%.  Questo tipo di configurazione è eccessivamente restrittivo e rigido, lasciando poco spazio per le richieste di risorse che vengono accidentalmente classificate in modo errato. È disponibile una disposizione per consentire l'esecuzione di una richiesta da gruppi di carico di lavoro non configurati per l'isolamento. Le risorse allocate a questa richiesta verranno visualizzate come zero nelle DMV di sistema e prenderanno in prestito un livello limitato di concessione di risorse dalle risorse riservate al sistema.

> [!NOTE]
> Per garantire un utilizzo ottimale delle risorse, prendere in considerazione una soluzione di gestione del carico di lavoro che sfrutti un certo isolamento per garantire che i contratti di servizio vengano soddisfatti e combinati con le risorse condivise a cui si accede in base [all'importanza](sql-data-warehouse-workload-importance.md)del carico di lavoro.

## <a name="workload-containment"></a>Contenimento del carico di lavoroWorkload containment

Il contenimento del carico di lavoro si riferisce alla limitazione della quantità di risorse che un gruppo di carico di lavoro può utilizzare.  Il contenimento del carico di lavoro si ottiene configurando il parametro CAP_PERCENTAGE_RESOURCE su meno di 100 nella sintassi [CREATE WORKLOAD GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Si consideri lo scenario in base al quale gli utenti devono accedere in lettura al sistema in modo da poter eseguire un'analisi di simulazione tramite query ad hoc.  Questi tipi di richieste potrebbero avere un impatto negativo su altri carichi di lavoro in esecuzione nel sistema.  La configurazione del contenimento garantisce che la quantità di risorse sia limitata.

La configurazione del contenimento del carico di lavoro definisce in modo implicito un livello massimo di concorrenza.  Con un CAP_PERCENTAGE_RESOURCE impostato su 60% e un REQUEST_MIN_RESOURCE_GRANT_PERCENT impostato su 1%, è consentito fino a un livello di concorrenza di 60 per il gruppo del carico di lavoro.  Considerare il metodo riportato di seguito per determinare la concorrenza massima:Consider the method included below for determining the maximum concurrency:

[Concorrenza massima] -`CAP_PERCENTAGE_RESOURCE`[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`] / [ ]

> [!NOTE]
> Il CAP_PERCENTAGE_RESOURCE effettivo di un gruppo di carico di lavoro non raggiungerà il 100% quando vengono creati gruppi di carico di lavoro con MIN_PERCENTAGE_RESOURCE a un livello maggiore di zero.  Vedere [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) per i valori di runtime effettivi.

## <a name="resources-per-request-definition"></a>Risorse per definizione di richiestaResources per request definition

I gruppi di carico di lavoro forniscono un meccanismo per definire la quantità min e max di risorse allocate per ogni richiesta con i parametri REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT nella sintassi [CREATE WORKLOAD GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Le risorse in questo caso sono CPU e memoria.  La configurazione di questi valori determina la quantità di risorse e il livello di concorrenza che è possibile raggiungere nel sistema.

> [!NOTE]
> REQUEST_MAX_RESOURCE_GRANT_PERCENT è un parametro facoltativo che per impostazione predefinita è lo stesso valore specificato per REQUEST_MIN_RESOURCE_GRANT_PERCENT.

Come la scelta di una classe di risorse, la configurazione di REQUEST_MIN_RESOURCE_GRANT_PERCENT imposta il valore per le risorse utilizzate da una richiesta.  La quantità di risorse indicata dal valore impostato è garantita per l'allocazione alla richiesta prima di iniziare l'esecuzione.  Per i clienti che eseguono la migrazione dalle classi di risorse ai gruppi di carico di lavoro, prendere in considerazione l'articolo [Procedura](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) per eseguire il mapping dalle classi di risorse ai gruppi di carico di lavoro come punto di partenza.

La configurazione di REQUEST_MAX_RESOURCE_GRANT_PERCENT su un valore maggiore di REQUEST_MIN_RESOURCE_GRANT_PERCENT consente al sistema di allocare più risorse per richiesta.  Durante la pianificazione di una richiesta, il sistema determina l'effettiva allocazione delle risorse per la richiesta, che è compresa tra REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT, in base alla disponibilità delle risorse nel pool condiviso e al carico corrente sul sistema.  Le risorse devono esistere nel [pool condiviso](#shared-pool-resources) di risorse quando la query viene pianificata.  

> [!NOTE]
> REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT hanno valori effettivi che dipendono dai valori MIN_PERCENTAGE_RESOURCE e CAP_PERCENTAGE_RESOURCE effettivi.  Vedere [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) per i valori di runtime effettivi.

## <a name="execution-rules"></a>Regole di esecuzione

Nei sistemi di reporting ad hoc, i clienti possono eseguire accidentalmente query in esecuzione che influiscono notevolmente sulla produttività degli altri.  Gli amministratori di sistema sono costretti a dedicare tempo all'uccisione di query in fuga per liberare risorse di sistema.  I gruppi del carico di lavoro offrono la possibilità di configurare una regola di timeout dell'esecuzione delle query per annullare le query che hanno superato il valore specificato.  La regola viene configurata impostando il `QUERY_EXECUTION_TIMEOUT_SEC` parametro nella sintassi CREATE WORKLOAD [GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)

## <a name="shared-pool-resources"></a>Risorse del pool condiviso

Le risorse del pool condiviso sono le risorse non configurate per l'isolamento.  Gruppi del carico di lavoro con un MIN_PERCENTAGE_RESOURCE impostato su zero sfruttare le risorse nel pool condiviso per eseguire le richieste.  I gruppi del carico di lavoro con un CAP_PERCENTAGE_RESOURCE maggiore di MIN_PERCENTAGE_RESOURCE usate anche risorse condivise.  La quantità di risorse disponibili nel pool condiviso viene calcolata come segue.

[Pool condiviso] - 100 `MIN_PERCENTAGE_RESOURCE` - [somma di tutti i gruppi di carico di lavoro]

L'accesso alle risorse nel pool condiviso viene allocato in base [all'importanza.](sql-data-warehouse-workload-importance.md)  Le richieste con lo stesso livello di importanza accederanno alle risorse del pool condiviso in/first out.

## <a name="next-steps"></a>Passaggi successivi

- [Guida introduttiva: configurare l'isolamento del carico di lavoro](quickstart-configure-workload-isolation-tsql.md)
- [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
- [Convertire le classi di risorse in gruppi di carico di lavoro](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md).
- [Monitoraggio del portale di gestione del carico di lavoro](sql-data-warehouse-workload-management-portal-monitor.md).  
