---
title: Configurare Advanced Threat Protection
description: Advanced Threat Protection rileva attività di database anomale che indicano potenziali minacce alla sicurezza del database in un singolo database o pool elastico.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822497"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>Azure SQL Database Advanced Threat Protection per database singoli o in poolAzure SQL Database Advanced Threat Protection for single or pooled databases

[Advanced Threat Protection](sql-database-threat-detection-overview.md) per database singoli e in pool rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database. Advanced Threat Protection è in grado di identificare **Potential SQL injection**, Access from unusual location or data **center**, Access **from unfamiliar principal or potentially harmful application**, and **Brute force SQL credentials** - See more details in [Advanced Threat Protection alerts](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

È possibile ricevere notifiche sulle minacce rilevate tramite [posta elettronica](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) o il [portale di Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Advanced Threat Protection](sql-database-threat-detection-overview.md) fa parte dell'offerta avanzata di sicurezza dei dati (ADS, [Advanced Data Security),](sql-database-advanced-data-security.md) che è un pacchetto unificato per le funzionalità di sicurezza SQL avanzate. È possibile accedere e gestire Advanced Threat Protection tramite il portale centrale Sicurezza dei dati avanzata di SQL.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Configurare Advanced Threat Protection nel portale di AzureSet up Advanced Threat Protection in the Azure portal

1. Avviare il [https://portal.azure.com](https://portal.azure.com)portale di Azure all'indirizzo .
2. Passare alla pagina di configurazione del server di database SQL di Azure che si vuole proteggere. Nelle impostazioni di sicurezza, selezionare **Sicurezza dei dati avanzata**.
3. Nella pagina di configurazione **Sicurezza dei dati avanzata**:

   - Abilitare Sicurezza dei dati avanzata nel server.
   - In **Impostazioni di Advanced Threat Protection**, nella casella di testo **Invia avvisi a**, viene fornito l'elenco dei messaggi di posta elettronica per ricevere avvisi di sicurezza al rilevamento delle attività anomale del database.
  
   ![Configurare Advanced Threat Protection](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > I prezzi nelle schermate non sempre riflettono il prezzo corrente e sono un esempio.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Configurare Advanced Threat Protection tramite PowerShell

Per un esempio di script, vedere [Configurare il controllo e Advanced Threat Protection tramite PowerShell.](scripts/sql-database-auditing-and-threat-detection-powershell.md)

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni su [Advanced Threat Protection](sql-database-threat-detection-overview.md).
- Ulteriori informazioni su [Advanced Threat Protection nell'istanza gestita](sql-database-managed-instance-threat-detection.md).  
- Altre informazioni su [Sicurezza dei dati avanzata](sql-database-advanced-data-security.md).
- Altre informazioni su [Controllo](sql-database-auditing.md)
- Altre informazioni sul Centro sicurezza di [AzureLearn](https://docs.microsoft.com/azure/security-center/security-center-intro) more about Azure security center
- Per altre informazioni sui prezzi, vedere la [pagina dei prezzi del database SQLFor](https://azure.microsoft.com/pricing/details/sql-database/) more information on pricing, see the SQL Database pricing page  
