---
title: Rilevamento minacce - Database SQL di Azure | Microsoft Docs
description: La funzionalità di rilevamento delle minacce individua le attività anomale del database che possono indicare la presenza di minacce alla sicurezza, in un database singolo o in un pool elastico.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 60839fa06d8436d3d2202d1d97735c42c5768e9e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797210"
---
# <a name="azure-sql-database-threat-detection-for-single-or-pooled-databases"></a>Rilevamento delle minacce del database SQL di Azure per database autonomi o in pool

[Rilevamento delle minacce](sql-database-threat-detection-overview.md) consente di rilevare le attività anomale in database singoli e in pool, che possono indicare tentativi di accesso o exploit dei database, insoliti e potenzialmente dannosi. Rilevamento delle minacce può identificare **potenziali attacchi SQL injection**, **accesso da una posizione o un data center insoliti**, **accesso da un'entità di sicurezza insolita o da un'applicazione potenzialmente dannosa** e **attacco di forza bruta a credenziali SQL**. Per altre informazioni dettagliate, vedere [Avvisi di rilevamento minacce](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

È possibile ricevere notifiche sulle minacce rilevate tramite [posta elettronica](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) o il [portale di Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Rilevamento delle minacce](sql-database-threat-detection-overview.md) fa parte dell'offerta [Sicurezza dei dati avanzata](sql-database-advanced-data-security.md) (ADS), un pacchetto unificato per le funzionalità di sicurezza avanzate SQL. È possibile accedere e gestire Rilevamento delle minacce tramite il portale centrale Sicurezza dei dati avanzata di SQL. Il pacchetto Sicurezza dei dati avanzata viene addebitato alla tariffa di $ 15/mese per ogni server logico. I primi 30 giorni non vengono addebitati.

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Configurare il rilevamento delle minacce per il database tramite il portale di Azure

1. Avviare il portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Passare alla pagina di configurazione del server del database SQL di Azure che si vuole proteggere. Nelle impostazioni di sicurezza, selezionare **Sicurezza dei dati avanzata**.
3. Nella pagina di configurazione **Sicurezza dei dati avanzata**:

   - Abilitare Sicurezza dei dati avanzata nel server.
   - In **Impostazioni di rilevamento delle minacce**nella casella di testo **Invia avvisi a**, viene fornito l'elenco dei messaggi di posta elettronica per ricevere avvisi di sicurezza al rilevamento delle attività anomale del database.
  
   ![Impostare il rilevamento delle minacce](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Configurare il rilevamento delle minacce tramite PowerShell

Per un esempio di script, vedere [Configurare il controllo del database SQL e il rilevamento delle minacce usando PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Rilevamento delle minacce](sql-database-threat-detection-overview.md).
- Altre informazioni su [Rilevamento delle minacce in istanza gestita](sql-database-managed-instance-threat-detection.md).  
- Altre informazioni su [Sicurezza dei dati avanzata](sql-database-advanced-data-security.md).
- Altre informazioni su [Controllo](sql-database-auditing.md)
- Altre informazioni su [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Per altre informazioni sui prezzi, visitare la [pagina relativa ai prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/)  
