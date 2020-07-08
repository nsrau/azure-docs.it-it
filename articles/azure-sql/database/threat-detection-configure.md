---
title: Configurare Advanced Threat Protection
description: Advanced Threat Protection rileva le attività anomale del database che indicano potenziali minacce alla sicurezza per il database nel database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 2f4e9841a44252829fae283a12ba804219204022
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84321547"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>Configurare Advanced Threat Protection per il database SQL di Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Advanced Threat Protection](threat-detection-overview.md) per il database SQL di Azure rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database. Advanced Threat Protection è in grado di identificare **potenziali attacchi SQL injection**, **accesso da posizioni insolite o Data Center**, **accesso da un'applicazione principale o potenzialmente dannosa**e **forza bruta per le credenziali SQL** . per ulteriori informazioni, vedere gli [avvisi di Advanced Threat Protection](threat-detection-overview.md#alerts).

È possibile ricevere notifiche sulle minacce rilevate tramite [posta elettronica](threat-detection-overview.md#explore-detection-of-a-suspicious-event) o il [portale di Azure](threat-detection-overview.md#explore-alerts-in-the-azure-portal)

[Advanced Threat Protection](threat-detection-overview.md) fa parte dell'offerta [avanzata di sicurezza dei dati](advanced-data-security.md) , un pacchetto unificato per le funzionalità avanzate di sicurezza di SQL. È possibile accedere e gestire Advanced Threat Protection tramite il portale SQL Advanced Data Security centrale.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Configurare Advanced Threat Protection nella portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare alla pagina di configurazione del server che si desidera proteggere. Nelle impostazioni di sicurezza, selezionare **Sicurezza dei dati avanzata**.
3. Nella pagina di configurazione **Sicurezza dei dati avanzata**:

   - Abilitare Sicurezza dei dati avanzata nel server.
   - In **Impostazioni di Advanced Threat Protection**, nella casella di testo **Invia avvisi a**, viene fornito l'elenco dei messaggi di posta elettronica per ricevere avvisi di sicurezza al rilevamento delle attività anomale del database.
  
   ![Configurare Advanced Threat Protection](./media/threat-detection/set_up_threat_detection.png)

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Configurare Advanced Threat Protection tramite PowerShell

Per un esempio di script, vedere [configurare il controllo e la protezione avanzata dalle minacce tramite PowerShell](scripts/auditing-threat-detection-powershell-configure.md).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Advanced Threat Protection](threat-detection-overview.md).
- Altre informazioni sulla [protezione avanzata dalle minacce in SQL istanza gestita](../managed-instance/threat-detection-configure.md).  
- Altre informazioni su [Sicurezza dei dati avanzata](advanced-data-security.md).
- Altre informazioni su [Controllo](../../azure-sql/database/auditing-overview.md)
- Scopri di più sul [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Per ulteriori informazioni sui prezzi, vedere la [pagina dei prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/)  
