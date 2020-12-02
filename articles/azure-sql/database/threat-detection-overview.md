---
title: Advanced Threat Protection
titleSuffix: Azure SQL Database, SQL Managed Instance, & Azure Synapse Analytics
description: Advanced Threat Protection rileva le attività anomale del database che indicano potenziali minacce alla sicurezza nel database SQL di Azure, Azure SQL Istanza gestita e Azure sinapsi Analytics.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, sstein
ms.date: 12/01/2020
tags: azure-synapse
ms.openlocfilehash: 931e914cd3c184136395a9bb9a7e148a90e9fb91
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461954"
---
# <a name="advanced-threat-protection-for-azure-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Advanced Threat Protection per database SQL di Azure, SQL Istanza gestita e Azure sinapsi Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Advanced Threat Protection per il [database SQL di Azure](sql-database-paas-overview.md), [Azure SQL istanza gestita](../managed-instance/sql-managed-instance-paas-overview.md) e [Azure sinapsi Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere ai database o sfruttarli.

Advanced Threat Protection fa parte dell'offerta [Azure Defender for SQL](azure-defender-for-sql.md) , un pacchetto unificato per le funzionalità avanzate di sicurezza di SQL. È possibile accedere a Advanced Threat Protection e gestirlo tramite il portale di Azure Defender centrale per SQL.

## <a name="overview"></a>Panoramica

Advanced Threat Protection offre un nuovo livello di sicurezza, che consente ai clienti di rilevare e rispondere alle minacce potenziali non appena si verificano, fornendo avvisi di sicurezza sulle attività anomale. Gli utenti ricevono un avviso in caso di attività di database sospetta, potenziali vulnerabilità e attacchi SQL injection, nonché in caso di modelli di query e accesso ai database anomali. Advanced Threat Protection integra gli avvisi con il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/), che includono informazioni dettagliate sulle attività sospette e consigliano azioni su come analizzare e mitigare la minaccia. Advanced Threat Protection rende più semplice affrontare le potenziali minacce al database senza dover essere esperti della sicurezza o gestire sistemi di monitoraggio della sicurezza avanzati.

Per un'esperienza di analisi completa, è consigliabile abilitare il controllo, che scrive gli eventi del database in un log di controllo nell'account di archiviazione di Azure.  Per abilitare il controllo, vedere [controllo per il database SQL di Azure e la sinapsi di Azure](../../azure-sql/database/auditing-overview.md) o il [controllo per istanza gestita SQL di Azure](../managed-instance/auditing-configure.md).

## <a name="alerts"></a>Avvisi

Advanced Threat Protection per il database SQL di Azure rileva le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database. Per un elenco degli avvisi per il database SQL di Azure, vedere gli [avvisi per il database SQL e Azure sinapsi Analytics nel centro sicurezza di Azure](../../security-center/alerts-reference.md#alerts-sql-db-and-warehouse).

## <a name="explore-detection-of-a-suspicious-event"></a>Esplorazione del rilevamento di un evento sospetto

Si riceverà una notifica tramite posta elettronica al rilevamento di attività di database anomale. Il messaggio di posta elettronica fornirà informazioni sull'evento di sicurezza sospetto, inclusi la natura delle attività anomale, il nome del database, il nome del server, il nome dell'applicazione e l'ora dell'evento. Il messaggio di posta elettronica fornirà anche informazioni sulle possibili cause e le azioni consigliate per analizzare e ridurre il rischio di una potenziale minaccia al database.

![Report Anomalie dell'attività](./media/threat-detection-overview/anomalous_activity_report.png)

1. Fare clic sul collegamento **Visualizza avvisi SQL recenti** nel messaggio di posta elettronica per avviare il portale di Azure e visualizzare la pagina avvisi del Centro sicurezza di Azure, che offre una panoramica delle minacce attive rilevate nel database.

   ![Minacce di attività](./media/threat-detection-overview/active_threats.png)

1. Fare clic su uno specifico avviso per visualizzare altri dettagli e azioni per analizzare la minaccia e risolvere eventuali minacce future.

   Ad esempio, l'attacco SQL injection è uno dei problemi di sicurezza più comuni delle applicazioni Web su Internet, che viene usato per attaccare le applicazioni guidate dai dati. Gli autori degli attacchi sfruttano le vulnerabilità delle applicazioni per introdurre istruzioni SQL dannose nei campi di immissione dell'applicazione, con lo scopo di violare o modificare i dati del database. Negli avvisi di attacchi SQL injection, i dettagli includono l'istruzione SQL vulnerabile che ha subito un exploit.

   ![Avviso specifico](./media/threat-detection-overview/specific_alert.png)

## <a name="explore-alerts-in-the-azure-portal"></a>Esplorare gli avvisi nel portale di Azure

Advanced Threat Protection integra gli avvisi con il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/). I riquadri in tempo reale SQL Advanced Threat Protection all'interno del database e SQL Azure Blade Defender nel portale di Azure tenere traccia dello stato delle minacce attive.

Fare clic su **avviso di Advanced Threat Protection** per avviare la pagina avvisi del Centro sicurezza di Azure e ottenere una panoramica delle minacce SQL attive rilevate nel database.

:::image type="content" source="media/azure-defender-for-sql/advanced-threat-protection-alerts.png" alt-text="Panoramica degli avvisi di Advanced Threat Protection nel database":::

:::image type="content" source="media/azure-defender-for-sql/advanced-threat-protection.png" alt-text="protezione avanzata dalle minacce nel centro sicurezza":::

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [protezione avanzata dalle minacce nel database SQL di azure & sinapsi di Azure](threat-detection-configure.md).
- Altre informazioni sulla [protezione avanzata dalle minacce in Azure SQL istanza gestita](../managed-instance/threat-detection-configure.md).
- Scopri di più su [Azure Defender per SQL](azure-defender-for-sql.md).
- Altre informazioni sul [controllo del database SQL di Azure](../../azure-sql/database/auditing-overview.md)
- Scopri di più sul [Centro sicurezza di Azure](../../security-center/security-center-introduction.md)
- Per altre informazioni sui prezzi, vedere la [pagina dei prezzi del database SQL di Azure](https://azure.microsoft.com/pricing/details/sql-database/)