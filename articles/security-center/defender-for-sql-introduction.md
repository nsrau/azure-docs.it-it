---
title: Azure Defender per SQL - Vantaggi e funzionalità
description: Informazioni sui vantaggi e sulle funzionalità di Azure Defender per SQL.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 328a565dbb22a13c71a7001d43941e7be062dff9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449048"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Introduzione ad Azure Defender per SQL

Azure Defender per SQL include due piani di Azure Defender che estendono il [pacchetto di sicurezza dei dati](../azure-sql/database/advanced-data-security.md) del Centro sicurezza di Azure per proteggere i database e i relativi dati in qualunque posizione si trovino. 

## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|**Azure Defender per server di database SQL di Azure** - Disponibile a livello generale<br>**Azure Defender per server SQL nelle macchine virtuali** - Anteprima|
|Prezzi:|I due piani che formano **Azure Defender per SQL** sono soggetti alle tariffe visualizzate nella [pagina dei prezzi](security-center-pricing.md)|
|Versioni di SQL protette:|Database SQL di Azure <br>Istanza gestita di SQL di Azure<br>Azure Synapse Analytics (in precedenza SQL Data Warehouse)<br>SQL Server (tutte le versioni supportate)|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Governo cinese, altri governi|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Cosa è possibile proteggere con Azure Defender per SQL?

**Azure Defender per SQL** è costituito da due piani di Azure Defender distinti:

- **Azure Defender per server di database SQL di Azure** protegge:
  - [Database SQL di Azure](../azure-sql/database/sql-database-paas-overview.md)
  - [Istanza gestita di database SQL di Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
  - [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **Azure Defender per server SQL nelle macchine virtuali (anteprima)** estende le protezioni per i server SQL nativi di Azure in modo da supportare completamente gli ambienti ibridi e proteggere i server SQL (tutte le versioni supportate) ospitati in Azure, in altri ambienti cloud e persino in computer locali


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Quali sono i vantaggi di Azure Defender per SQL?

Questi due piani includono funzionalità per l'identificazione e l'attenuazione di potenziali vulnerabilità dei database e il rilevamento di attività anomale che possono indicare una minaccia per i database:

- [Valutazione della vulnerabilità](../azure-sql/database/sql-vulnerability-assessment.md): servizio di analisi che consente di individuare, monitorare e risolvere potenziali vulnerabilità del database. Le analisi di valutazione forniscono una panoramica dello stato di sicurezza dei computer SQL e i dettagli dei risultati relativi alla sicurezza.

- [Advanced Threat Protection](../azure-sql/database/threat-detection-overview.md): servizio di rilevamento che monitora costantemente i server SQL per individuare minacce come attacchi SQL injection, attacchi di forza bruta e abuso dei privilegi. Questo servizio fornisce avvisi di sicurezza orientati all'azione nel centro Sicurezza di Azure, con i dettagli dell'attività sospetta, indicazioni su come attenuare le minacce e opzioni per proseguire le indagini con Azure Sentinel.


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Quali tipi di avvisi vengono forniti da Azure Defender per SQL?

Gli avvisi di sicurezza vengono attivati in caso di:

- **Potenziali attacchi SQL injection**, incluse le vulnerabilità rilevate quando le applicazioni generano un'istruzione SQL non corretta nel database
- **Accesso anomalo al database e modelli di query**, come ad esempio un numero insolitamente elevato di tentativi di accesso non riusciti con credenziali diverse (tentativo di attacco di forza bruta)
- **Attività sospette sul database**, ad esempio una modifica nella destinazione di archiviazione di esportazione per un'operazione di importazione ed esportazione SQL

Gli avvisi includono i dettagli dell'evento imprevisto che li ha attivati e raccomandazioni su come analizzare e risolvere le minacce.



## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni su Azure Defender per SQL.

Per i materiali correlati, vedere gli articoli seguenti: 

- [Come abilitare Azure Defender per server SQL nelle macchine virtuali](defender-for-sql-usage.md)
- [Come abilitare Azure Defender per server di database SQL](../azure-sql/database/advanced-data-security.md)
- [Elenco degli avvisi di Azure Defender per SQL](alerts-reference.md#alerts-sql-db-and-warehouse)