---
title: Limiti delle risorse del server di database SQL di Azure | Microsoft Docs
description: Questo articolo offre una panoramica dei limiti delle risorse del server di database SQL di Azure per database singoli e pool elastici. Offre anche informazioni su cosa accade quando tali limiti delle risorse vengono raggiunti o superati.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan,moslake
manager: craigg
ms.date: 11/13/2018
ms.openlocfilehash: 57ff6025cd60d786931177f8bc0cc22e9baa60e7
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55479122"
---
# <a name="sql-database-resource-limits-for-azure-sql-database-server"></a>Limiti delle risorse del database SQL per il server di database SQL di Azure

Questo articolo offre una panoramica dei limiti delle risorse del database SQL per un server di database SQL che gestisce database singoli e pool elastici. Offre anche informazioni su cosa accade quando tali limiti delle risorse vengono raggiunti o superati.

> [!NOTE]
> Per i limiti di istanze gestite, consultare [Limiti delle risorse del database SQL per le istanze gestite](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Limiti massimi delle risorse

| Risorsa | Limite |
| :--- | :--- |
| Database per server | 5000 |
| Numero predefinito di server per sottoscrizione in ogni area | 20 |
| Numero massimo di server per sottoscrizione in ogni area | 200 |  
| Quota DTU/eDTU per server | 54.000 |  
| Quota vCore per server/istanza | 540 |
| N. max pool per server | Limitato dal numero di DTU o vCore. Se ad esempio ogni pool è da 1000 DTU, un server può supportare 54 pool.|
||||

> [!NOTE]
> Per ottenere una quota di DTU/eDTU o vCore maggiore o un numero di server più alto rispetto alla quantità predefinita, è possibile inviare una nuova richiesta di supporto nel portale di Azure per la sottoscrizione con il tipo di problema "Quota". La quota di DTU/eDTU e il limite di database per server limita il numero di pool elastici per ogni server.
> [!IMPORTANT]
> Poiché il numero di database si avvicina al limite per server di database SQL, può verificarsi quanto segue:
> - Latenza in aumento nelle query in esecuzione nel database master.  Ciò include le visualizzazioni delle statistiche di utilizzo delle risorse, ad esempio sys.resource_stats.
> - Latenza in aumento nelle operazioni di gestione e nel portale di esecuzione del rendering dei punti di visualizzazione che coinvolgono l'enumerazione dei database nel server.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Cosa accade quando vengono raggiunti i limiti delle risorse del database?

### <a name="compute-dtus-and-edtus--vcores"></a>Elaborazione (DTU ed eDTU/vCore)

Quando l'uso delle risorse di elaborazione del database (misurato in base a DTU ed eDTU o VCore) diventa elevato, la latenza delle query aumenta e può anche verificarsi un timeout. In queste condizioni le query possono essere messe in coda dal servizio e vengono rese disponibili alcune risorse per l'esecuzione.
In caso di uso elevato di risorse di elaborazione, le opzioni di mitigazione includono:

- Aumento della dimensione di calcolo del database o del pool elastico per mettere a disposizione del database un numero maggiore di risorse del computer. Vedere [Ridimensionare le risorse del database singolo](sql-database-single-database-scale.md) e [Ridimensionare le risorse del pool elastico](sql-database-elastic-pool-scale.md).
- Ottimizzazione delle query per ridurre l'uso delle risorse di ogni query. Per altre informazioni, vedere la sezione [Hint/ottimizzazione di query](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Archiviazione

Quando la quantità di spazio del database usato raggiunge il limite di dimensioni massime, gli inserimenti e gli aggiornamenti del database che aumentano la dimensione dei dati hanno esito negativo e i clienti ricevono un [messaggio di errore](sql-database-develop-error-messages.md). Le operazioni SELECT e DELETE del database continuano ad avere esito positivo.

In caso di uso elevato di spazio, le opzioni di mitigazione includono:

- Aumento delle dimensioni massime del database o pool elastico o aggiungere più archiviazione. Vedere [Ridimensionare le risorse del database singolo](sql-database-single-database-scale.md) e [Ridimensionare le risorse del pool elastico](sql-database-elastic-pool-scale.md).
- Se il database è in un pool elastico, in alternativa può essere spostato all'esterno del pool in modo che lo spazio di archiviazione non venga condiviso con altri database.
- Compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sessioni e ruoli di lavoro (richieste)

Il numero massimo di sessioni e ruoli di lavoro è determinato dal livello di servizio e dalla dimensione di calcolo (DTU ed eDTU). Le nuove richieste vengono rifiutate quando vengono raggiunti i limiti delle sessioni o dei ruoli di lavoro e i clienti ricevono un messaggio di errore. Mentre il numero di connessioni disponibili può essere controllato dall'applicazione, il numero di ruoli di lavoro simultanei è spesso più difficile da stimare e da controllare. Ciò vale soprattutto durante i picchi di periodi di carico quando vengono raggiunti i limiti di risorse del database e i ruoli di lavoro si accumulano per via di query a esecuzione prolungata.

In caso di uso elevato di sessioni o ruoli di lavoro, le opzioni di mitigazione includono:

- Aumento del livello di servizio o della dimensione di calcolo del database o del pool elastico. Vedere [Ridimensionare le risorse del database singolo](sql-database-single-database-scale.md) e [Ridimensionare le risorse del pool elastico](sql-database-elastic-pool-scale.md).
- Ottimizzazione delle query per ridurre l'uso delle risorse di ogni query, se l'aumento dell'uso di ruoli di lavoro è dovuto a un conflitto delle risorse di elaborazione. Per altre informazioni, vedere la sezione [Hint/ottimizzazione di query](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Passaggi successivi

- Per le risposte alle domande più frequenti, vedere [Domande frequenti sul database SQL](sql-database-faq.md).
- Per informazioni sui limiti generici di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).
- Per informazioni su DTU ed eDTU, vedere [DTU ed eDTU](sql-database-service-tiers.md#dtu-based-purchasing-model).
- Per informazioni sui limiti di tempdb relativi alle dimensioni, vedere [TempDB nel database SQL di Azure](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
