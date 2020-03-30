---
title: Configurare Advanced Threat Protection - istanza gestitaConfigure Advanced Threat Protection - managed instance
description: Advanced Threat Protection rileva attività di database anomale che indicano potenziali minacce alla sicurezza del database in un'istanza gestita.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 69292a934af8b8777f11ab58ed3fe306abf8b408
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822558"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>Configurare Advanced Threat Protection nell'istanza gestita del database SQL di AzureConfigure Advanced Threat Protection in Azure SQL Database managed instance

[Advanced Threat Protection](sql-database-threat-detection-overview.md) per [un'istanza gestita](sql-database-managed-instance-index.yml) rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database. Advanced Threat Protection è in grado di identificare **Potential SQL injection**, Access from unusual location or data **center**, Access **from unfamiliar principal or potentially harmful application**, and **Brute force SQL credentials** - See more details in [Advanced Threat Protection alerts](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

È possibile ricevere notifiche sulle minacce rilevate tramite [posta elettronica](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) o il [portale di Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Advanced Threat Protection](sql-database-threat-detection-overview.md) fa parte dell'offerta avanzata di sicurezza dei dati (ADS, [Advanced Data Security),](sql-database-advanced-data-security.md) che è un pacchetto unificato per le funzionalità di sicurezza SQL avanzate. È possibile accedere e gestire Advanced Threat Protection tramite il portale centrale Sicurezza dei dati avanzata di SQL.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Configurare Advanced Threat Protection nel portale di AzureSet up Advanced Threat Protection in the Azure portal

1. Avviare il [https://portal.azure.com](https://portal.azure.com)portale di Azure all'indirizzo .
2. Passare alla pagina di configurazione dell'istanza gestita che si vuole proteggere. Nella pagina **Impostazioni** selezionare **Sicurezza avanzata dei dati**.
3. Nella pagina di configurazione di Advanced Data Security
   - **Attivare** Sicurezza avanzata dei dati.
   - Configurare l'**elenco di indirizzi di posta elettronica** che riceveranno avvisi di sicurezza in caso di rilevamento di attività di database anomale.
   - Selezionare l'**account di archiviazione di Azure** in cui vengono salvati i record di controllo delle minacce anomali.
   - Selezionare i **tipi di Advanced Threat Protection** che si desidera configurare. Ulteriori informazioni sugli [avvisi di Advanced Threat Protection](sql-database-threat-detection-overview.md).
4. Fare clic su **Salva** per salvare i criteri di sicurezza avanzata dei dati nuovi o aggiornati.

   ![Advanced Threat Protection](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > I prezzi nelle schermate non sempre riflettono il prezzo corrente e sono un esempio.

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni su [Advanced Threat Protection](sql-database-threat-detection-overview.md).
- Per informazioni in proposito, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md).
- Ulteriori informazioni su [Advanced Threat Protection per un singolo database](sql-database-threat-detection.md).
- Ulteriori informazioni sul [controllo delle istanze gestite](https://go.microsoft.com/fwlink/?linkid=869430).
- Altre informazioni sul Centro sicurezza di Azure .Learn more about [Azure security center](https://docs.microsoft.com/azure/security-center/security-center-intro).
