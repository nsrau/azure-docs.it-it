---
title: 'Azure Defender per SQL: vantaggi e funzionalità'
description: Scopri i vantaggi e le funzionalità di Azure Defender per SQL.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: dcdbc3efba53f78890816721b6659aa69553d6ea
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301603"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Introduzione ad Azure Defender per SQL

Azure Defender per SQL include due piani di Azure Defender che estendono il [pacchetto di sicurezza dei dati](../azure-sql/database/advanced-data-security.md) del Centro sicurezza di Azure per proteggere i database e i relativi dati ovunque si trovino. 

## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato versione:|**Azure Defender per server di database SQL di Azure** -disponibile a livello generale (GA)<br>**Azure Defender per SQL Server nei computer** -anteprima|
|Prezzi|I due piani che formano **Azure Defender per SQL** vengono fatturati come indicato nella [pagina dei prezzi](security-center-pricing.md)|
|Versioni SQL protette:|Database SQL di Azure <br>Istanza gestita di SQL di Azure<br>Azure Synapse Analytics (in precedenza SQL Data Warehouse)<br>SQL Server (tutte le versioni supportate)|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Gov per la Cina, altri gov|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Che cosa protegge Azure Defender per SQL?

**Azure Defender per SQL** è costituito da due piani di Azure Defender distinti:

- **Azure Defender per i server di database SQL di Azure** protegge:
  - [Database SQL di Azure](../azure-sql/database/sql-database-paas-overview.md)
  - [Istanza gestita di database SQL di Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
  - [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **Azure Defender per SQL Server nei computer (anteprima)** estende le protezioni per i server SQL nativi di Azure in modo da supportare completamente gli ambienti ibridi e proteggere i server SQL (tutte le versioni supportate) ospitati in Azure, in altri ambienti cloud e persino in computer locali


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Quali sono i vantaggi di Azure Defender per SQL?

Questi due piani includono funzionalità per l'identificazione e la mitigazione di potenziali vulnerabilità del database e per il rilevamento di attività anomale che potrebbero indicare minacce per i database:

- [Valutazione della vulnerabilità](../azure-sql/database/sql-vulnerability-assessment.md) : il servizio di analisi per individuare, monitorare e aiutare a correggere le potenziali vulnerabilità del database. Le analisi di valutazione forniscono una panoramica dello stato di sicurezza dei computer SQL e i dettagli dei risultati di sicurezza.

- [Advanced Threat Protection](../azure-sql/database/threat-detection-overview.md) : servizio di rilevamento che monitora costantemente i server SQL per individuare minacce come attacchi SQL injection, attacchi di forza bruta e abuso dei privilegi. Questo servizio fornisce avvisi di sicurezza orientati alle azioni nel centro sicurezza di Azure con i dettagli dell'attività sospetta, le linee guida su come attenuare le minacce e le opzioni per proseguire le indagini con Azure Sentinel.


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Quali tipi di avvisi sono forniti da Azure Defender per SQL?

Gli avvisi di sicurezza vengono attivati quando sono presenti:

- **Potenziali attacchi intrusivi** nel codice SQL, incluse le vulnerabilità rilevate quando le applicazioni generano un'istruzione SQL non corretta nel database
- **Modelli di query e di accesso al database anomali** , ad esempio un numero anomalo elevato di tentativi di accesso non riusciti con credenziali diverse (un tentativo di forza bruta)
- **Attività sospette del database** , ad esempio una modifica nella destinazione di archiviazione dell'esportazione per un'operazione di importazione ed esportazione SQL

Gli avvisi includono i dettagli dell'evento imprevisto che li ha attivati, nonché indicazioni su come individuare e correggere le minacce.



## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come Azure Defender per SQL.

Per i materiali correlati, vedere gli articoli seguenti: 

- [Come abilitare Azure Defender per SQL Server nei computer](defender-for-sql-usage.md)
- [Come abilitare Azure Defender per i server di database SQL](../azure-sql/database/advanced-data-security.md)
- [Elenco di avvisi di Azure Defender per SQL](alerts-reference.md#alerts-sql-db-and-warehouse)