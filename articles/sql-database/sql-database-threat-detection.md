---
title: Rilevamento minacce - Database SQL di Azure | Documentazione Microsoft
description: La funzionalità di rilevamento delle minacce individua le attività di database anomale che possono indicare la presenza di minacce alla sicurezza del database in un database singolo o in un pool elastico.
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
ms.date: 01/30/2019
ms.openlocfilehash: 032146742c1a49fc8cdbda24d5c732add3cc5ea7
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55465351"
---
# <a name="azure-sql-database-threat-detection-for-standalone-or-pooled-database"></a>Rilevamento delle minacce del database SQL di Microsoft Azure per un database autonomo o in pool

[Rilevamento delle minacce](sql-database-threat-detection-overview.md) SQL di Azure per database autonomi e in pool rileva le attività anomale che possono indicare tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database. Rilevamento delle minacce può identificare **potenziali attacchi SQL injection**, **accessi da una posizione o un data center insolito**, **accessi da un'entità di sicurezza insolita o da un'applicazione potenzialmente dannosa** e **credenziali SQL tramite forza bruta**. Per altre informazioni dettagliate, vedere [Avvisi di rilevamento minacce](sql-database-threat-detection-overview.md#azure-sql-database-threat-detection-alerts).

È possibile ricevere notifiche sulle minacce rilevate tramite [posta elettronica](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) o il [portale di Azure](sql-database-threat-detection-overview.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal)

[Rilevamento delle minacce](sql-database-threat-detection-overview.md) fa parte dell'offerta [Sicurezza dei dati avanzata di SQL](sql-advanced-threat-protection.md) (ADS), che è un pacchetto unificato per le funzionalità avanzate di sicurezza SQL. È possibile accedere e gestire Rilevamento delle minacce tramite il portale centrale ADS SQL. Il pacchetto Sicurezza dei dati avanzata viene addebitato alla tariffa di $ 15/mese per ogni server logico. I primi 30 giorni non vengono addebitati.

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

- Altre informazioni sul [rilevamento delle minacce](sql-database-threat-detection-overview.md).
- Altre informazioni sul [rilevamento delle minacce nell'istanza gestita](sql-database-managed-instance-threat-detection.md).  
- Altre informazioni sulla [Sicurezza dei dati avanzata di SQL](sql-advanced-threat-protection.md).
- Altre informazioni sul [controllo del database SQL di Azure](sql-database-auditing.md)
- Altre informazioni sul [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Per altre informazioni sui prezzi, visitare la [pagina relativa ai prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/).  
