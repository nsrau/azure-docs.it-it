---
title: Azure Batch Analytics
description: Gli argomenti di Batch Analytics includono informazioni di riferimento per gli eventi e gli avvisi disponibili per le risorse del servizio Batch.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: 0d55ecd7f10e267a9cb469dffcdf26c131c8ce41
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91849512"
---
# <a name="batch-analytics"></a>Batch Analytics

Negli argomenti di questa sezione sono contenute informazioni di riferimento per gli eventi e gli avvisi disponibili per le risorse del servizio batch.

Per altre informazioni sull'abilitazione e sull'uso di log di diagnostica di Batch, vedere [Registrazione diagnostica di Azure Batch](./batch-diagnostics.md).

## <a name="diagnostic-logs"></a>Log di diagnostica

Il servizio Azure Batch produce i seguenti eventi di registro di diagnostica nel corso della durata di determinate risorse Batch.

### <a name="service-log-events"></a>Eventi del log del servizio

- [Creazione di pool](batch-pool-create-event.md)
- [Avvio dell'eliminazione di pool](batch-pool-delete-start-event.md)
- [Completamento dell'eliminazione di pool](batch-pool-delete-complete-event.md)
- [Avvio del ridimensionamento di pool](batch-pool-resize-start-event.md)
- [Completamento del ridimensionamento di pool](batch-pool-resize-complete-event.md)
- [Scalabilità automatica del pool](batch-pool-autoscale-event.md)
- [Avvio dell'attività](batch-task-start-event.md)
- [Attività completata](batch-task-complete-event.md)
- [Errore dell'attività](batch-task-fail-event.md)
- [Pianificazione attività non riuscita](batch-task-schedule-fail-event.md)
