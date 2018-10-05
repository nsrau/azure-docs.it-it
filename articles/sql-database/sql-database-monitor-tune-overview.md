---
title: Monitoraggio e ottimizzazione delle prestazioni nel database SQL di Azure | Microsoft Docs
description: Suggerimenti per l'ottimizzazione delle prestazioni nel database SQL di Azure tramite valutazione e miglioramento.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 07/16/2018
ms.openlocfilehash: c79285247950510791ede915fcf0e5373792044f
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165042"
---
# <a name="monitoring-and-performance-tuning"></a>Monitoraggio e ottimizzazione delle prestazioni

Database SQL di Azure è gestito automaticamente ed è un servizio di dati flessibile che consente di monitorare l'utilizzo, aggiungere o rimuovere facilmente risorse (CPU, memoria, io), trovare indicazioni per migliorare le prestazioni del database o lasciare che il database si adatti al carico di lavoro e ottimizzi automaticamente le prestazioni.

## <a name="overview-of-monitoring-database-performance-in-azure-sql-database"></a>Panoramica del monitoraggio delle prestazioni del database nel database SQL di Azure
Il monitoraggio delle prestazioni di un database SQL in Azure inizia con il monitoraggio dell'utilizzo delle risorse rispetto al livello di prestazioni scelto per il database. Il monitoraggio consente di determinare se il database ha capacità in eccesso o se presenta problemi perché è stato superato il limite massimo di risorse e quindi decidere se è opportuno modificare la dimensione di calcolo e il livello di servizio del database nel [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) o nel [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md). È possibile monitorare il database nel [portale di Azure](https://portal.azure.com) tramite gli strumenti grafici seguenti o con le [viste a gestione dinamica di SQL](sql-database-monitoring-with-dmvs.md).

Database SQL di Azure consente di individuare le opportunità per migliorare e ottimizzare le prestazioni delle query senza modificare le risorse esaminando le [indicazioni per ottimizzare le prestazioni](sql-database-advisor.md). Spesso le prestazioni non ottimali del database sono dovute a indici mancanti e query non ottimizzate correttamente. È possibile applicare queste indicazioni per migliorare le prestazioni del carico di lavoro.
È anche possibile impostare il database SQL di Azure in modo che [ottimizzi automaticamente le prestazioni delle query](sql-database-automatic-tuning.md) applicando tutte le indicazioni identificate e verificando l'effettivo miglioramento delle prestazioni del database. Per il monitoraggio e la risoluzione dei problemi di prestazioni del database sono disponibili le opzioni seguenti:

- Nel [portale di Azure](https://portal.azure.com) fare clic su **Database SQL**, selezionare il database e quindi usare il grafico di monitoraggio per identificare le risorse che stanno per raggiungere i valori massimi. L'utilizzo di DTU viene visualizzato per impostazione predefinita. Fare clic su **Modifica** per modificare l'intervallo di tempo e i valori indicati.
- Usare [Informazioni dettagliate prestazioni query](sql-database-query-performance.md) per identificare le query che consumano la maggior parte delle risorse.
- Usare [Advisor per database SQL ](sql-database-advisor-portal.md) per visualizzare le indicazioni per creare e rimuovere indici, parametrizzare le query e correggere i problemi di schema.
- Usare [Intelligent Insights SQL di Azure](sql-database-intelligent-insights.md) per il monitoraggio automatico delle prestazioni del database. Dopo aver rilevato un problema di prestazioni, viene generato un log di diagnostica con i dettagli e l'analisi della causa radice del problema. Quando possibile viene fornita un'indicazione di miglioramento delle prestazioni.
- [Abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md) e consentire al database SQL di Azure di correggere automaticamente i problemi di prestazioni identificati.
- È anche possibile usare le [viste a gestione dinamica (DMV)](sql-database-monitoring-with-dmvs.md), gli eventi estesi (`XEvents`)(sql-database/sql-database-xevent-db-diff-from-svr.md) e [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) per ottenere i parametri delle prestazioni in tempo reale. Vedere le [indicazioni relative alle prestazioni](sql-database-performance-guidance.md) per trovare le tecniche che è possibile usare per migliorare le prestazioni del database SQL di Azure, se si individua un problema durante l'uso di questi report o queste viste.

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorare i database tramite il portale di Azure
Nel [portale di Azure](https://portal.azure.com/)è possibile monitorare l'utilizzo di un database singolo selezionandolo e facendo clic sul grafico **Monitoraggio** . Verrà visualizzata una finestra della **metrica** in cui è possibile apportare modifiche facendo clic su l pulsante **Modifica grafico**. Aggiungere le metriche seguenti

* Percentuale CPU
* Percentuale di DTU
* Percentuale di I/O di dati
* Percentuale di dimensioni del database

Dopo aver aggiunto queste metriche, è possibile continuare a visualizzarle nel grafico **Monitoraggio** con altre informazioni nella finestra **Metrica**. Tutte le quattro metriche mostrano la percentuale media di utilizzo relativa alla **DTU** del database. Per altre informazioni sui livelli di servizio, vedere gli articoli [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) o [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).  

![Monitoraggio del livello di servizio delle prestazioni del database.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

È inoltre possibile configurare gli avvisi sulle metriche delle prestazioni. Scegliere il pulsante **Aggiungi avviso** nella finestra **Metrica**. Seguire la procedura guidata per configurare l'avviso. È possibile scegliere di ricevere un avviso se la metrica supera una determinata soglia o scende al di sotto di una determinata soglia.

Ad esempio, se si prevede un aumento del carico di lavoro sul database, è possibile scegliere di configurare un avviso di posta elettronica ogni volta che il database raggiunge l'80% per una qualsiasi delle metriche delle prestazioni. È possibile usarlo come un preavviso per capire quando potrebbe essere necessario passare alla dimensione di calcolo superiore.

Le metriche delle prestazioni consentono inoltre di determinare se è possibile effettuare il downgrade a una dimensione di calcolo inferiore. Presupporre di utilizzare un database Standard S2 e tutte le metriche delle prestazioni mostrano che il database in media non utilizza più del 10% in un dato momento. È probabile che il database funzioni bene in Standard S1. Tuttavia, prestare attenzione ai picchi o alle fluttuazioni dei carichi di lavoro prima di decidere di passare a una dimensione di calcolo inferiore.

## <a name="improving-database-performance-with-more-resources"></a>Miglioramento delle prestazioni del database con più risorse

Se alla fine non esistono elementi eseguibili che possono migliorare le prestazioni del database, è possibile modificare la quantità di risorse disponibili nel database SQL di Azure. È possibile assegnare più risorse modificando il [livello di servizio DTU](sql-database-service-tiers-dtu.md) di un database singolo o aumentare il numero di eDTU di un pool elastico in qualsiasi momento. In alternativa, se si usa il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md) è possibile modificare il livello di servizio o aumentare le risorse allocate al database. 
1. Per i database singoli, è possibile [modificare i livelli di servizio](sql-database-service-tiers-dtu.md) o le [risorse di calcolo](sql-database-service-tiers-vcore.md) su richiesta per migliorare le prestazioni del database.
2. Per più database, è consigliabile usare [pool elastici](sql-database-elastic-pool-guidance.md) per la scalabilità automatica delle risorse.

## <a name="tune-and-refactor-application-or-database-code"></a>Ottimizzare ed eseguire il refactoring del codice del database o dell'applicazione

È possibile modificare il codice dell'applicazione per usare il database in modo più ottimale, modificare gli indici, forzare i piani o usare i suggerimenti per adattare manualmente il database al carico di lavoro. Alcuni suggerimenti e indicazioni per l'ottimizzazione manuale e la riscrittura del codice sono disponibili nell'[argomento relativo alle indicazioni sulle prestazioni](sql-database-performance-guidance.md).


## <a name="next-steps"></a>Passaggi successivi

- Per abilitare l'ottimizzazione automatica nel database SQL di Azure e delegare la gestione completa del carico di lavoro alla funzionalità di ottimizzazione automatica, vedere [Abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md).
- Per usare l'ottimizzazione manuale, è possibile esaminare le [raccomandazioni per l'ottimizzazione nel portale di Azure](sql-database-advisor-portal.md) e applicare manualmente quelle che consentono di migliorare le prestazioni delle query.
- Modificare le risorse disponibili nel database modificando i [livelli di servizio Database SQL di Azure](sql-database-performance-guidance.md)
