---
title: Configurare Advanced Threat Protection-istanza gestita
description: Advanced Threat Protection rileva le attività anomale del database che indicano potenziali minacce alla sicurezza per il database in un'istanza gestita.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73822558"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>Configurare Advanced Threat Protection nell'istanza gestita di database SQL di Azure

La [protezione avanzata dalle minacce](sql-database-threat-detection-overview.md) per un' [istanza gestita](sql-database-managed-instance-index.yml) rileva le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database. Advanced Threat Protection è in grado di identificare **potenziali attacchi SQL injection**, **accesso da posizioni insolite o Data Center**, **accesso da un'applicazione principale o potenzialmente dannosa**e **forza bruta per le credenziali SQL** . per ulteriori informazioni, vedere gli [avvisi di Advanced Threat Protection](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

È possibile ricevere notifiche sulle minacce rilevate tramite [posta elettronica](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) o il [portale di Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Advanced Threat Protection](sql-database-threat-detection-overview.md) fa parte dell'offerta [Advanced Data Security](sql-database-advanced-data-security.md) (ADS), che è un pacchetto unificato per le funzionalità avanzate di sicurezza di SQL. È possibile accedere e gestire Advanced Threat Protection tramite il portale centrale Sicurezza dei dati avanzata di SQL.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Configurare Advanced Threat Protection nella portale di Azure

1. Avviare il portale di Azure all' [https://portal.azure.com](https://portal.azure.com)indirizzo.
2. Passare alla pagina di configurazione dell'istanza gestita che si vuole proteggere. Nella pagina **Impostazioni** selezionare **sicurezza avanzata dei dati**.
3. Nella pagina di configurazione Advanced Data Security
   - Attivare la sicurezza dei **dati avanzata.**
   - Configurare l'**elenco di indirizzi di posta elettronica** che riceveranno avvisi di sicurezza in caso di rilevamento di attività di database anomale.
   - Selezionare l'**account di archiviazione di Azure** in cui vengono salvati i record di controllo delle minacce anomali.
   - Selezionare i **tipi di Advanced Threat Protection** che si desidera configurare. Altre informazioni sugli [avvisi di Advanced Threat Protection](sql-database-threat-detection-overview.md).
4. Fare clic su **Salva** per salvare i criteri di sicurezza dei dati avanzati nuovi o aggiornati.

   ![Advanced Threat Protection](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > I prezzi nelle schermate non riflettono sempre il prezzo corrente e sono un esempio.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Advanced Threat Protection](sql-database-threat-detection-overview.md).
- Per informazioni in proposito, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md).
- Altre informazioni sulla [protezione avanzata dalle minacce per un database singolo](sql-database-threat-detection.md).
- Altre informazioni sul [controllo dell'istanza gestita](https://go.microsoft.com/fwlink/?linkid=869430).
- Scopri di più sul [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).
