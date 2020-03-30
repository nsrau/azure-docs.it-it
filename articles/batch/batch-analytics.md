---
title: Analisi batch di AzureAzure Batch Analytics
description: Gli argomenti di Batch Analytics includono informazioni di riferimento per gli eventi e gli avvisi disponibili per le risorse del servizio Batch.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: d9538461dfa48a961f9325566b2adaf265e8daeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025963"
---
# <a name="batch-analytics"></a>Batch Analytics
Gli argomenti di Batch Analytics includono informazioni di riferimento per gli eventi e gli avvisi disponibili per le risorse del servizio Batch.

Per altre informazioni sull'abilitazione e sull'uso di log di diagnostica di Batch, vedere [Registrazione diagnostica di Azure Batch](https://azure.microsoft.com/documentation/articles/batch-diagnostics/).

## <a name="diagnostic-logs"></a>Log di diagnostica

Il servizio Azure Batch produce i seguenti eventi di registro di diagnostica nel corso della durata di determinate risorse Batch.

**Eventi del log del servizio**
* [Pool create](batch-pool-create-event.md) (Creazione del pool)
* [Pool delete start](batch-pool-delete-start-event.md) (Avvio dell'eliminazione del pool)
* [Pool delete complete](batch-pool-delete-complete-event.md) (Completamento dell'eliminazione del pool)
* [Pool resize start](batch-pool-resize-start-event.md) (Avvio del ridimensionamento del pool)
* [Pool resize complete](batch-pool-resize-complete-event.md) (Completamento del ridimensionamento del pool)
* [Task start](batch-task-start-event.md) (Avvio dell'attività)
* [Task complete](batch-task-complete-event.md) (Completamento dell'attività)
* [Task fail](batch-task-fail-event.md) (Errore dell'attività)