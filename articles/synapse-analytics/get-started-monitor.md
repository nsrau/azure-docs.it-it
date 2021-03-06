---
title: "Esercitazione: Introduzione ad Azure Synapse Analytics - Monitorare l'area di lavoro Synapse"
description: In questa esercitazione viene illustrato come monitorare le attività nell'area di lavoro Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: monitoring
ms.topic: tutorial
ms.date: 10/15/2020
ms.openlocfilehash: 2970bb58bb73d52c75729b00a8209a9c576d4ec0
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427412"
---
# <a name="monitor-your-synapse-workspace"></a>Monitorare l'area di lavoro Synapse

In questa esercitazione viene illustrato come monitorare le attività nell'area di lavoro Synapse. È possibile monitorare le attività correnti e cronologiche per SQL, Apache Spark e pipeline. 

## <a name="introduction-to-the-monitor-hub"></a>Introduzione all'hub Monitoraggio

Aprire Synapse Studio e passare all'hub **Monitoraggio**. Qui è possibile visualizzare una cronologia di tutte le attività che si verificano nell'area di lavoro e quelle attualmente attive. 

* In **Integrazione** è possibile monitorare pipeline, trigger e runtime di integrazione.
* In **Attività** è possibile monitorare le attività di Spark e SQL. 

## <a name="integration"></a>Integrazione

1. Passare a **Integrazione > Pipeline**. Questa vista mostra quante volte una pipeline è stata eseguita nell'area di lavoro. 
1. Trovare la pipeline eseguita nel passaggio precedente e fare clic su **Nome della pipeline** per visualizzare i dettagli.
1. Fare clic su **Barra di navigazione** nella parte superiore di Synapse Studio e quindi fare clic su **Tutte le esecuzioni della pipeline** per tornare alla vista precedente.

## <a name="apache-spark-activities"></a>Attività di Apache Spark

1. Passare a **Integrazione > Attività > Applicazioni Apache Spark**. È ora possibile vedere tutte le applicazioni Spark che sono in esecuzione o sono state eseguite nell'area di lavoro.
1. Trovare un'applicazione che non è più in esecuzione e fare clic sul relativo **Nome applicazione**. Verranno visualizzati i dettagli dell'applicazione Spark.
1. Se si ha familiarità con Apache Spark, è possibile accedere all'interfaccia utente standard del server cronologia Spark facendo clic su **Server cronologia Spark**.

## <a name="sql-activities"></a>Attività di SQL

1. Passare a **Integrazione > Attività > Richieste SQL**.
1. Verranno visualizzate le richieste SQL.
1. Selezionare un **pool** da monitorare. È ora possibile vedere tutte le richieste SQL che sono in esecuzione o sono state eseguite nell'area di lavoro in tale pool.
1. Trovare una richiesta SQL specifica e passare il puntatore del mouse sulla voce corrispondente. Mentre si passa il mouse, viene visualizzata un'icona di script SQL.
1. Fare clic sull'icona per visualizzare il testo completo della richiesta SQL.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esplorare il Knowledge Center](get-started-knowledge-center.md)
