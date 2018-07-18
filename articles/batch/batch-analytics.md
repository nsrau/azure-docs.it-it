---
title: Analisi di Azure Batch | Microsoft Docs
description: Riferimento per le analisi di Azure Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: danlep
ms.openlocfilehash: 4c81acb282b24bbd899227c4dcc449fed4ba6c7d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
ms.locfileid: "30312678"
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