---
title: Analisi Azure Batch
description: Gli argomenti di Batch Analytics includono informazioni di riferimento per gli eventi e gli avvisi disponibili per le risorse del servizio Batch.
ms.topic: article
ms.date: 04/20/2017
ms.openlocfilehash: d35bf249a3ae5dd06f42b89c3d5c2773f9508603
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82113069"
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