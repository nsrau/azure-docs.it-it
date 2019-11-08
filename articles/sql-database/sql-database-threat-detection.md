---
title: Configurare Advanced Threat Protection
description: Advanced Threat Protection rileva le attività anomale del database che indicano potenziali minacce alla sicurezza per il database in un database singolo o in un pool elastico.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 8eb8e4fccc17fe31def671cf6e8edb19d867b244
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822497"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>Protezione avanzata dalle minacce del database SQL di Azure per database singoli o in pool

La [protezione avanzata dalle minacce](sql-database-threat-detection-overview.md) per database singoli e in pool rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database. Advanced Threat Protection è in grado di identificare **potenziali attacchi SQL injection**, **accesso da posizioni insolite o Data Center**, **accesso da un'applicazione principale o potenzialmente dannosa**e **forza bruta per le credenziali SQL** . vedere Altre informazioni sugli [avvisi di Advanced Threat Protection](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

È possibile ricevere notifiche sulle minacce rilevate tramite [posta elettronica](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) o il [portale di Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Advanced Threat Protection](sql-database-threat-detection-overview.md) fa parte dell'offerta [Advanced Data Security](sql-database-advanced-data-security.md) (ADS), che è un pacchetto unificato per le funzionalità avanzate di sicurezza di SQL. È possibile accedere a Advanced Threat Protection e gestirlo tramite il portale SQL ADS centrale.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Configurare Advanced Threat Protection nella portale di Azure

1. Avviare il portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Passare alla pagina di configurazione del server di database SQL di Azure che si vuole proteggere. Nelle impostazioni di sicurezza, selezionare **Sicurezza dei dati avanzata**.
3. Nella pagina di configurazione **Sicurezza dei dati avanzata**:

   - Abilitare Sicurezza dei dati avanzata nel server.
   - In **Impostazioni di Advanced Threat Protection**, nella casella di testo **Invia avvisi a**, viene fornito l'elenco dei messaggi di posta elettronica per ricevere avvisi di sicurezza al rilevamento delle attività anomale del database.
  
   ![Configurare Advanced Threat Protection](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > I prezzi nelle schermate non riflettono sempre il prezzo corrente e sono un esempio.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Configurare Advanced Threat Protection con PowerShell

Per un esempio di script, vedere [configurare il controllo e la protezione avanzata dalle minacce tramite PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Advanced Threat Protection](sql-database-threat-detection-overview.md).
- Altre informazioni su [Advanced Threat Protection nell'istanza gestita](sql-database-managed-instance-threat-detection.md).  
- Altre informazioni su [Sicurezza dei dati avanzata](sql-database-advanced-data-security.md).
- Altre informazioni su [Controllo](sql-database-auditing.md)
- Altre informazioni sul [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Per altre informazioni sui prezzi, visitare la [pagina relativa ai prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/)  
