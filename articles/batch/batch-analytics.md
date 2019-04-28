---
title: Analisi di Azure Batch | Microsoft Docs
description: Riferimento per le analisi di Azure Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 999c3037196044250b8a12d6b6b380553e58c6ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60721556"
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