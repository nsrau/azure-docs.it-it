---
title: Analisi di Azure Batch | Microsoft Docs
description: Riferimento per le analisi di Azure Batch.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: jushiman
ms.openlocfilehash: 39a8bfb6a48bf55ae9f2ec36f7716959e6ada9dd
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76027393"
---
# <a name="batch-analytics"></a>Batch Analytics
Gli argomenti di Batch Analytics includono informazioni di riferimento per gli eventi e gli avvisi disponibili per le risorse del servizio Batch.

Per altre informazioni sull'abilitazione e sull'uso di log di diagnostica di Batch, vedere [Registrazione diagnostica di Azure Batch](https://azure.microsoft.com/documentation/articles/batch-diagnostics/).

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