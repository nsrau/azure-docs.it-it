---
title: Pianificazioni della manutenzione di Azure (anteprima) | Microsoft Docs
description: La pianificazione della manutenzione consente ai clienti di programmare tutti gli eventi di manutenzione pianificata necessari usati da Azure SQL Data Warehouse per distribuire nuovi aggiornamenti, patch e funzionalità.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/27/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: f0b9b59ec09445a170d1f93181c2b432eafb60bf
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839992"
---
# <a name="view-a-maintenance-schedule"></a>Visualizzare una pianificazione della manutenzione 

## <a name="portal"></a>Portale

Per impostazione predefinita, a tutte le istanze di Azure SQL Data Warehouse create di recente viene applicata una finestra di manutenzione primaria e secondaria di otto ore durante la distribuzione. È possibile modificare le finestre non appena la distribuzione sarà stata completata. Nessuna manutenzione verrà eseguita al di fuori delle finestre di manutenzione specificate senza preavviso.

Per visualizzare la pianificazione della manutenzione applicata al data warehouse, completare i passaggi seguenti:

1.  Accedere al [portale di Azure](https://portal.azure.com/).
2.  Selezionare il data warehouse che si vuole visualizzare. 
3.  Il data warehouse selezionato verrà aperto nel pannello Panoramica. La pianificazione di manutenzione che viene applicata al data warehouse viene visualizzato sotto **pianificazione di manutenzione**.

![Pannello Panoramica](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>Passaggi successivi
- [Altre informazioni](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) sulla creazione, visualizzazione e gestione degli avvisi con Monitoraggio di Azure.
- [Altre informazioni](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sulle azioni webhook per le regole di avviso relative ai log.
- [Altre informazioni](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) Creazione e gestione di gruppi di azioni.
- [Altre informazioni](https://docs.microsoft.com/azure/service-health/service-health-overview) sull'integrità dei servizi di Azure.


