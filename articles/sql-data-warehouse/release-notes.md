---
title: Note sulla versione di Azure SQL Data Warehouse | Microsoft Docs
description: Note sulla versione di Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/09/2019
ms.openlocfilehash: e77556ac0d6f64797906c0f3b4181f147b1668e2
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58448415"
---
# <a name="azure-sql-data-warehouse-release-notes-and-documentation-updates"></a>Note sulla versione di Azure SQL Data Warehouse e aggiornamenti della documentazione

Azure SQL Data Warehouse è un data warehouse aziendale basato sul cloud che sfrutta l'elaborazione parallela elevata (Massively Parallel Processing, MPP) per eseguire rapidamente query complesse su petabyte di dati. Usare SQL Data Warehouse come componente chiave di una soluzione per Big Data. Importare i Big Data in SQL Data Warehouse con semplici query T-SQL PolyBase e quindi sfruttare la potenza dell'elaborazione MPP per eseguire analisi ad alte prestazioni. Man mano che si procede con l'integrazione e l'analisi, il data warehouse diventerà il punto di riferimento dell'azienda da cui ricavare informazioni dettagliate.

Fare clic sui collegamenti seguenti per altre informazioni sulle funzionalità nuove e migliorate previste nella versione più recente di Azure SQL Data Warehouse. Si dovrebbero ricevere questi aggiornamenti del servizio durante la pianificazione della manutenzione identificata.

- [Marzo 2019](./release-notes-10-0-10106-0.md#march-2019)
- [Gennaio 2019](./release-notes-10-0-10106-0.md#january-2019)
- [Dicembre 2018](./release-notes-10-0-10106-0.md#december-2018)
- [Ottobre 2018](./release-notes-10-0-10106-0.md#october-2018)
- [Settembre 2018](./release-notes-september-2018.md)
- [Agosto 2018](./release-notes-august-2018.md)
- [Luglio 2018](./release-notes-july-2018.md)
- [Giugno 2018](./release-notes-june-2018.md)
- [Maggio 2018](./release-notes-may-2018.md)

## <a name="checking-the-code-version-that-has-been-applied-to-your-data-warehouse"></a>Controllo della versione del codice applicata al data warehouse

Per verificare la versione applicata al data warehouse. Connettersi al data warehouse tramite SSMS ed eseguire la sintassi seguente per restituire la versione corrente di SQL Data Warehouse.

```sql
SELECT @@VERSION AS 'SQL Data Warehouse';
```

Output di esempio: ![Versione di SQL Data Warehouse](./media/release-notes/sql_data_warehouse_version.png)

Usare la data identificata per verificare quale versione è stata applicata ad Azure SQL Data Warehouse. 


## <a name="next-steps"></a>Passaggi successivi
- [Altre informazioni](https://docs.microsoft.com/azure/sql-data-warehouse/viewing-maintenance-schedule) sulla visualizzazione di una pianificazione di manutenzione. 
- [Altre informazioni](https://docs.microsoft.com/azure/sql-data-warehouse/changing-maintenance-schedule) sulla modifica di una pianificazione di manutenzione.
- [Altre informazioni](https://docs.microsoft.com/azure/monitoring-and-diagnostics/alert-metric) sulla creazione, visualizzazione e gestione degli avvisi con Monitoraggio di Azure.
- [Altre informazioni](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sulle azioni webhook per le regole di avviso relative ai log.
- [Altre informazioni](https://docs.microsoft.com/azure/service-health/service-health-overview) sull'integrità dei servizi di Azure