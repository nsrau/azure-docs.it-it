---
title: Pianificazioni della manutenzione di Azure (anteprima) | Microsoft Docs
description: La pianificazione della manutenzione consente ai clienti di programmare tutti gli eventi di manutenzione pianificata necessari usati da Azure SQL Data Warehouse per distribuire nuovi aggiornamenti, patch e funzionalità.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 10/06/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: db5502b796fc345b2e2bf083f864d80bd59e7293
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427128"
---
# <a name="view-a-maintenance-schedule"></a>Visualizzare una pianificazione della manutenzione 

## <a name="portal"></a>Portale

Per impostazione predefinita, a tutte le istanze di Azure SQL Data Warehouse create di recente viene applicata una finestra di manutenzione primaria e secondaria di otto ore durante la distribuzione. È possibile modificare le finestre non appena la distribuzione sarà stata completata. Nessuna manutenzione verrà eseguita al di fuori delle finestre di manutenzione specificate senza preavviso.

Per visualizzare la pianificazione della manutenzione applicata al data warehouse, completare i passaggi seguenti:

1.  Accedere al [portale di Azure](https://portal.azure.com/).
2.  Selezionare il data warehouse che si vuole visualizzare. 
3.  Il data warehouse selezionato verrà aperto nel pannello Panoramica. La pianificazione della manutenzione applicata al data warehouse verrà visualizzata sotto la **pianificazione della manutenzione (anteprima)**.

![Pannello Panoramica](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>Passaggi successivi
- [Altre informazioni](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) sulla creazione, visualizzazione e gestione degli avvisi con Monitoraggio di Azure.
- [Altre informazioni](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sulle azioni webhook per le regole di avviso relative ai log.
- [Altre informazioni](https://docs.microsoft.com/azure/service-health/service-health-overview) sull'integrità dei servizi di Azure.


