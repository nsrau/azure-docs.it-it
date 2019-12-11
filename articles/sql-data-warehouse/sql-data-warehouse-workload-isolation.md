---
title: Isolamento del carico di lavoro
description: Linee guida per l'impostazione dell'isolamento del carico di lavoro con i gruppi del carico Azure SQL Data Warehouse di lavoro
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 11/27/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 82270c126d8a0894cd3a388dcab62017ed63c2cd
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974649"
---
# <a name="sql-data-warehouse-workload-group-isolation-preview"></a>Isolamento del gruppo di carico di lavoro SQL Data Warehouse (anteprima)

Questo articolo illustra come usare i gruppi del carico di lavoro per configurare l'isolamento del carico di lavoro, contenere le risorse e applicare le regole di runtime per l'esecuzione delle query.

## <a name="workload-groups"></a>Gruppi di carichi di lavoro

I gruppi del carico di lavoro sono contenitori per un set di richieste e costituiscono la base per la configurazione di gestione del carico di lavoro, incluso l'isolamento del carico di lavoro, in un sistema.  I gruppi del carico di lavoro vengono creati utilizzando la sintassi [Crea gruppo del carico di lavoro](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Una semplice configurazione di gestione del carico di lavoro può gestire carichi di dati e query utente.  Un gruppo di carico di lavoro denominato `wgDataLoads`, ad esempio, definisce gli aspetti del carico di lavoro per i dati caricati nel sistema. Inoltre, un gruppo di carico di lavoro denominato `wgUserQueries` definisce gli aspetti del carico di lavoro per gli utenti che eseguono query per leggere i dati dal sistema.

Nelle sezioni seguenti viene evidenziato in che modo i gruppi del carico di lavoro consentono di definire l'isolamento, il contenimento, la definizione delle risorse di richiesta e di rispettare le regole di esecuzione.

## <a name="workload-isolation"></a>Isolamento del carico di lavoro

L'isolamento del carico di lavoro significa che le risorse sono riservate esclusivamente per un gruppo di carico di lavoro.  Per ottenere l'isolamento del carico di lavoro, è necessario configurare il parametro MIN_PERCENTAGE_RESOURCE su un valore maggiore di zero nella sintassi [create workload Group](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Per i carichi di lavoro di esecuzione continua che devono essere conformi a contratti di sicurezza limitati, l'isolamento garantisce che le risorse siano sempre disponibili per il gruppo del carico di lavoro 

La configurazione dell'isolamento del carico di lavoro definisce in modo implicito un livello di concorrenza garantito.  Con un MIN_PERCENTAGE_RESOURCE impostato sul 30% e REQUEST_MIN_RESOURCE_GRANT_PERCENT impostato su 2%, per il gruppo del carico di lavoro viene garantito un livello di concorrenza pari a 15.  Prendere in considerazione il metodo seguente per determinare la concorrenza garantita:

[Concorrenza garantita] = [`MIN_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE] 
> Per min_percentage_resource sono disponibili valori minimi validi per il livello di servizio.  Per ulteriori informazioni, vedere [valori effettivi](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values) .

In assenza di isolamento del carico di lavoro, le richieste funzionano nel pool di risorse [condiviso](#shared-pool-resources) .  L'accesso alle risorse nel pool condiviso non è garantito e viene assegnato in base alla [priorità](sql-data-warehouse-workload-importance.md) .

La configurazione dell'isolamento del carico di lavoro deve essere eseguita con cautela poiché le risorse vengono allocate al gruppo del carico di lavoro anche se non sono presenti richieste attive nel gruppo del carico di lavoro.  L'isolamento con configurazione eccessiva può causare un utilizzo complessivo del sistema ridotto.

Gli utenti devono evitare una soluzione di gestione del carico di lavoro che configuri l'isolamento del carico di lavoro 100%: l'isolamento del 100% viene effettuato quando la somma di min_percentage_resource configurata in tutti i 100 gruppi del carico di lavoro  Questo tipo di configurazione è eccessivamente restrittiva e rigida, lasciando spazio sufficiente per le richieste di risorse accidentalmente classificate in modo errato.  È disponibile un provisioning per consentire l'esecuzione di una richiesta da gruppi di carico di lavoro non configurati per l'isolamento.  Le risorse allocate a questa richiesta vengono visualizzate come zero nei sistemi DMV e prendono in prestito un livello smallrc di concessione di risorse da risorse riservate del sistema.

> [!NOTE] 
> Per garantire un utilizzo ottimale delle risorse, prendere in considerazione una soluzione di gestione del carico di lavoro che sfrutta un certo isolamento per garantire che i contratti di contratto vengano soddisfatti e combinati con le risorse condivise a cui si accede in base all' [importanza](sql-data-warehouse-workload-importance.md)

## <a name="workload-containment"></a>Contenimento del carico di lavoro

Il contenimento del carico di lavoro fa riferimento alla limitazione della quantità di risorse che un gruppo di carico di lavoro può utilizzare.  Il contenimento del carico di lavoro viene eseguito configurando il parametro CAP_PERCENTAGE_RESOURCE su un numero inferiore a 100 nella sintassi [Crea gruppo del carico di lavoro](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Si consideri lo scenario in cui gli utenti necessitano dell'accesso in lettura al sistema in modo da poter eseguire un'analisi di simulazione tramite query ad hoc.  Questi tipi di richieste potrebbero avere un impatto negativo su altri carichi di lavoro in esecuzione nel sistema.  La configurazione del contenimento garantisce che la quantità di risorse sia limitata.

La configurazione del contenimento del carico di lavoro definisce in modo implicito un livello massimo di concorrenza.  Con un CAP_PERCENTAGE_RESOURCE impostato su 60% e un REQUEST_MIN_RESOURCE_GRANT_PERCENT impostato su 1%, per il gruppo di carico di lavoro è consentito un livello di concorrenza 60.  Si consideri il metodo incluso di seguito per determinare la concorrenza massima:

[Massima concorrenza] = [`CAP_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE] 
> Il CAP_PERCENTAGE_RESOURCE effettivo di un gruppo di carico di lavoro non raggiungerà il 100% quando verranno creati gruppi di carico di lavoro con MIN_PERCENTAGE_RESOURCE a un livello maggiore di zero.  Vedere [sys. dm_workload_management_workload_groups_stats](https://review.docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) per i valori di runtime effettivi.

## <a name="resources-per-request-definition"></a>Risorse per definizione di richiesta

I gruppi del carico di lavoro forniscono un meccanismo per definire la quantità minima e massima di risorse allocate per ogni richiesta con i parametri REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT nella sintassi [Crea gruppo del carico di lavoro](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Le risorse in questo caso sono la CPU e la memoria.  La configurazione di questi valori determina la quantità di risorse e il livello di concorrenza che è possibile ottenere nel sistema.

> [!NOTE] 
> REQUEST_MAX_RESOURCE_GRANT_PERCENT è un parametro facoltativo che utilizza per impostazione predefinita lo stesso valore specificato per REQUEST_MIN_RESOURCE_GRANT_PERCENT.

Come la scelta di una classe di risorse, la configurazione di REQUEST_MIN_RESOURCE_GRANT_PERCENT imposta il valore per le risorse utilizzate da una richiesta.  La quantità di risorse indicata dal valore impostato è garantita per l'allocazione alla richiesta prima dell'avvio dell'esecuzione.  Per i clienti che eseguono la migrazione dalle classi di risorse ai gruppi del carico di lavoro, è consigliabile seguire le [procedure](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) per eseguire il mapping dalle classi di risorse ai gruppi del carico di lavoro come punto iniziale.

La configurazione di REQUEST_MAX_RESOURCE_GRANT_PERCENT su un valore maggiore di REQUEST_MIN_RESOURCE_GRANT_PERCENT consente al sistema di allocare più risorse per ogni richiesta.  Durante la pianificazione di una richiesta, il sistema determina l'allocazione effettiva di risorse alla richiesta, che è tra REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT, in base alla disponibilità delle risorse nel pool condiviso e al carico corrente sul sistema.  Quando la query è pianificata, le risorse devono esistere nel [pool condiviso](#shared-pool-resources) di risorse.  

> [!NOTE] 
> REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT hanno valori effettivi che dipendono dai valori di MIN_PERCENTAGE_RESOURCE e CAP_PERCENTAGE_RESOURCE effettivi.  Vedere [sys. dm_workload_management_workload_groups_stats](https://review.docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) per i valori di runtime effettivi.

## <a name="execution-rules"></a>Regole di esecuzione

Nei sistemi di Reporting ad hoc i clienti possono eseguire accidentalmente query runaway che incidono gravemente sulla produttività degli altri.  Gli amministratori di sistema sono costretti a dedicare tempo a uccidere le query Runaway per liberare risorse di sistema.  I gruppi del carico di lavoro offrono la possibilità di configurare una regola di timeout esecuzione query per annullare le query che hanno superato il valore specificato.  La regola viene configurata impostando il parametro `QUERY_EXECUTION_TIMEOUT_SEC` nella sintassi [Crea gruppo del carico di lavoro](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .

## <a name="shared-pool-resources"></a>Risorse del pool condiviso

Le risorse del pool condiviso sono le risorse non configurate per l'isolamento.  I gruppi del carico di lavoro con un MIN_PERCENTAGE_RESOURCE impostato su zero sfruttano le risorse nel pool condiviso per l'esecuzione delle richieste.  I gruppi del carico di lavoro con un CAP_PERCENTAGE_RESOURCE maggiore di MIN_PERCENTAGE_RESOURCE utilizzate anche risorse condivise.  La quantità di risorse disponibili nel pool condiviso viene calcolata come indicato di seguito.

[Pool condiviso] = 100-[somma di `MIN_PERCENTAGE_RESOURCE` in tutti i gruppi del carico di lavoro]

L'accesso alle risorse nel pool condiviso viene allocato in base alla [priorità](sql-data-warehouse-workload-importance.md) .  Le richieste con lo stesso livello di importanza otterranno l'accesso alle risorse del pool condivise in base alla prima in/prima.

## <a name="next-steps"></a>Passaggi successivi

- [Guida introduttiva: configurare l'isolamento del carico di lavoro](quickstart-configure-workload-isolation-tsql.md)
- [CREA GRUPPO DI CARICO DI LAVORO](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
- [Convertire le classi di risorse in gruppi del carico di lavoro](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md).
