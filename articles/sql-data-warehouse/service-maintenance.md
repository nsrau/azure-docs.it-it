---
title: Manutenzione pianificata - Azure SQL Data Warehouse | Microsoft Docs
description: Informazioni sui preparativi per gli eventi di manutenzione pianificata per Azure SQL Data Warehouse.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/19/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 8b5b94c959ae9820cab338f3ee3317fb3ee166d6
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286602"
---
# <a name="planning-for-maintenance-on-your-azure-sql-data-warehouse"></a>Pianificazione per la manutenzione in Azure SQL Data Warehouse

Informazioni sui preparativi per gli eventi di manutenzione pianificata per Azure SQL Data Warehouse.

## <a name="what-is-a-planned-maintenance-event"></a>Che cos'è un evento di manutenzione pianificata?
Un evento di manutenzione pianificata è un intervallo di tempo durante il quale il data warehouse deve essere offline per un'operazione di manutenzione. Questi eventi sono opportunità per l'applicazione di aggiornamenti del servizio, nuove funzionalità o patch. 

## <a name="frequency"></a>Frequenza
In media, ogni mese si verifica almeno un evento di manutenzione pianificata. 

## <a name="notifications-and-downtime"></a>Notifiche e tempi di inattività
Si riceverà una notifica prima di ogni evento di manutenzione pianificata. L'evento di manutenzione annulla tutte le query in esecuzione e porta offline il data warehouse. Il tempo di inattività previsto per ogni data warehouse è di circa 30 minuti. Al termine della manutenzione è prevista una notifica. 

## <a name="setting-up-alerts"></a>Configurazione degli avvisi

Si consiglia di usare [Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) per configurare gli avvisi del log di manutenzione pianificata. Gli avvisi consentono di pianificare la manutenzione necessaria per ridurre al minimo l'impatto sull'azienda. 

Per impostare le notifiche, usare queste [istruzioni per gli avvisi del log](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul monitoraggio, vedere [Monitoraggio del carico di lavoro](sql-data-warehouse-manage-monitor.md).
