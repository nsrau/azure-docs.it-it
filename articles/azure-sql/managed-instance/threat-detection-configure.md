---
title: Configurare Advanced Threat Protection
titleSuffix: Azure SQL Managed Instance
description: Advanced Threat Protection rileva le attività anomale del database che indicano potenziali minacce alla sicurezza per il database in Azure SQL Istanza gestita.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 09/21/2020
ms.openlocfilehash: 31a47b9032ac014fa4eedde343d03d4fd343ff02
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790662"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Configurare Advanced Threat Protection in Azure SQL Istanza gestita
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Advanced Threat Protection](../database/threat-detection-overview.md) per un [istanza gestita SQL di Azure](sql-managed-instance-paas-overview.md) rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database. Advanced Threat Protection è in grado di identificare **potenziali attacchi SQL injection** , **accesso da posizioni insolite o Data Center** , **accesso da un'applicazione principale o potenzialmente dannosa** e **forza bruta per le credenziali SQL** . per ulteriori informazioni, vedere gli [avvisi di Advanced Threat Protection](../database/threat-detection-overview.md#alerts).

È possibile ricevere notifiche sulle minacce rilevate tramite [notifiche di posta elettronica](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event) o [portale di Azure](../database/threat-detection-overview.md#explore-alerts-in-the-azure-portal)

[Advanced Threat Protection](../database/threat-detection-overview.md) fa parte dell'offerta [Azure Defender for SQL](../database/azure-defender-for-sql.md)  , un pacchetto unificato per le funzionalità avanzate di sicurezza di SQL. È possibile accedere a Advanced Threat Protection e gestirlo tramite il portale di Azure Defender centrale per SQL.

##  <a name="azure-portal"></a>Portale di Azure

1. Accedere al  [portale di Azure](https://portal.azure.com). 
2. Passare alla pagina di configurazione dell'istanza di SQL Istanza gestita che si desidera proteggere. In **sicurezza** selezionare **Centro sicurezza** .
3. Nella pagina di configurazione di Azure Defender per SQL
   - Attivare **Azure Defender** per SQL.
   - Configurare l' **elenco di indirizzi di posta elettronica** che riceveranno avvisi di sicurezza in caso di rilevamento di attività di database anomale.
   - Selezionare l' **account di archiviazione di Azure** in cui vengono salvati i record di controllo delle minacce anomali.
   - Selezionare i **tipi di Advanced Threat Protection** che si desidera configurare. Altre informazioni sugli [avvisi di Advanced Threat Protection](../database/threat-detection-overview.md).
4. Fare clic su **Salva** per salvare i criteri nuovi o aggiornati di Azure Defender per SQL.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Advanced Threat Protection](../database/threat-detection-overview.md).
- Per informazioni sulle istanze gestite, vedere [che cos'è un istanza gestita SQL di Azure](sql-managed-instance-paas-overview.md).
- Altre informazioni sulla [protezione avanzata dalle minacce per il database SQL di Azure](../database/threat-detection-configure.md).
- Altre informazioni sul [controllo di SQL istanza gestita](./auditing-configure.md).
- Scopri di più sul [Centro sicurezza di Azure](../../security-center/security-center-introduction.md).