---
title: Azure Batch Analytics
description: Gli argomenti di Batch Analytics includono informazioni di riferimento per gli eventi e gli avvisi disponibili per le risorse del servizio Batch.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: 9d0e5c90c7bade82d76c7dbdfd4276e9671c961c
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85958107"
---
# <a name="batch-analytics"></a>Batch Analytics
Gli argomenti di Batch Analytics includono informazioni di riferimento per gli eventi e gli avvisi disponibili per le risorse del servizio Batch.

Per altre informazioni sull'abilitazione e sull'uso di log di diagnostica di Batch, vedere [Registrazione diagnostica di Azure Batch](./batch-diagnostics.md).

## <a name="diagnostic-logs"></a>Log di diagnostica

Il servizio Azure Batch produce i seguenti eventi di registro di diagnostica nel corso della durata di determinate risorse Batch.

**Eventi del log del servizio**
* [Creazione di pool](batch-pool-create-event.md)
* [Avvio dell'eliminazione di pool](batch-pool-delete-start-event.md)
* [Completamento dell'eliminazione di pool](batch-pool-delete-complete-event.md)
* [Avvio del ridimensionamento di pool](batch-pool-resize-start-event.md)
* [Completamento del ridimensionamento di pool](batch-pool-resize-complete-event.md)
* [Avvio dell'attività](batch-task-start-event.md)
* [Attività completata](batch-task-complete-event.md)
* [Errore dell'attività](batch-task-fail-event.md)
