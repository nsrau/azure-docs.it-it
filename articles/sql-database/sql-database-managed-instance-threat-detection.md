---
title: Configurare il rilevamento delle minacce - Istanza gestita di database SQL di Azure | Microsoft Docs
description: La funzionalità di rilevamento delle minacce individua le attività di database anomale che possono indicare la presenza di minacce alla sicurezza del database in Istanza gestita.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: d8522967154a69b8473475932f2074bc98b4f24d
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731224"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Configurare il rilevamento delle minacce (anteprima) in Istanza gestita di database SQL di Azure

[Rilevamento delle minacce](sql-database-threat-detection-overview.md) per [Istanza gestita](sql-database-managed-instance-index.yml) rileva le attività anomale che possono indicare tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database. Rilevamento delle minacce può identificare **potenziali attacchi SQL injection**, **accesso da una posizione o un data center insoliti**, **accesso da un'entità di sicurezza insolita o da un'applicazione potenzialmente dannosa** e **attacco di forza bruta a credenziali SQL**. Per altre informazioni dettagliate, vedere [Avvisi di rilevamento minacce](sql-database-threat-detection-overview.md#threat-detection-alerts).

È possibile ricevere notifiche sulle minacce rilevate tramite [posta elettronica](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) o il [portale di Azure](sql-database-threat-detection-overview.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal)

[Rilevamento delle minacce](sql-database-threat-detection-overview.md) fa parte dell'offerta [Sicurezza dei dati avanzata](sql-database-advanced-data-security.md) (ADS), un pacchetto unificato per le funzionalità di sicurezza avanzate SQL. È possibile accedere e gestire Rilevamento delle minacce tramite il portale centrale Sicurezza dei dati avanzata ADS di SQL. Il servizio di rilevamento delle minacce viene addebitato alla tariffa di $ 15/mese per ogni istanza gestita. I primi 30 giorni non vengono addebiti.

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Configurare Rilevamento minacce per l'istanza gestita tramite il portale di Azure

1. Avviare il portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Passare alla pagina di configurazione dell'istanza gestita che si vuole proteggere. Nella pagina **Impostazioni** selezionare **Rilevamento minacce**.
3. Nella pagina di configurazione del rilevamento delle minacce
   - Impostare il rilevamento delle minacce su **SÌ** .
   - Configurare l'**elenco di indirizzi di posta elettronica** che riceveranno avvisi di sicurezza in caso di rilevamento di attività di database anomale.
   - Selezionare l'**account di archiviazione di Azure** in cui vengono salvati i record di controllo delle minacce anomali.
4. Fare clic su **Salva** per salvare i criteri di rilevamento delle minacce nuovi o aggiornati.

   ![rilevamento delle minacce](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [Rilevamento delle minacce](sql-database-threat-detection-overview.md).
- Per informazioni in proposito, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md).
- Altre informazioni sul [rilevamento delle minacce per database singolo](sql-database-threat-detection.md).
- Altre informazioni sul [Controllo di Istanza gestita](https://go.microsoft.com/fwlink/?linkid=869430).
- Altre informazioni sul [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).
