---
title: Risolvere i problemi di prestazioni e ottimizzare il database | Microsoft Docs
description: Applicare le raccomandazioni sulle prestazioni al database SQL e ottenere informazioni dettagliate sulle prestazioni delle query in esecuzione nel database
metakeywords: azure sql database performance monitoring recommendation
services: sql-database
documentationcenter: 
manager: jhubbard
author: jan-eng
ms.assetid: 
ms.service: sql-database
ms.custom: performance
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 0bdb111257053e4a57510a72ab683bc9c890d2f7
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017


---
# <a name="troubleshoot-performance-issues-and-optimize-your-database"></a>Risolvere i problemi di prestazioni e ottimizzare il database

Spesso le prestazioni non ottimali del database sono dovute a indici mancanti e query non ottimizzate correttamente. In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Esaminare, applicare e ripristinare le raccomandazioni per migliorare le prestazioni.
> * Trovare query con un utilizzo elevato delle risorse.
> * Trovare query con esecuzione prolungata.

> Per ricevere una raccomandazione, è necessario un carico di lavoro continuo in un database con problemi di prestazioni, ad esempio a causa di un indice mancante.
>

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure

Accedere al [Portale di Azure](https://portal.azure.com/).

## <a name="review-and-apply-a-recommendation"></a>Esaminare e applicare una raccomandazione

Per applicare una raccomandazione del sistema per il database, seguire questa procedura:

1. Fare clic sul menu **Raccomandazioni per le prestazioni** nel pannello del database.

    ![Raccomandazioni per le prestazioni](./media/sql-database-performance-tutorial/perf_recommendations.png)

2. Selezionare una raccomandazione attiva dall'elenco delle raccomandazioni. In questo esempio si sceglie Crea indice.

    ![Selezionare una raccomandazione](./media/sql-database-performance-tutorial/create_index.png)

3. Applicare la raccomandazione facendo clic sul pulsante **Applica**. Facoltativamente, è possibile esaminare i dettagli della raccomandazione e visualizzare lo script T-SQL da eseguire facendo clic sul pulsante **Visualizza script**.

    ![Applicare una raccomandazione](./media/sql-database-performance-tutorial/apply.png)

4. [Facoltativo] Abilitare l'ottimizzazione automatica per applicare automaticamente le raccomandazioni.

    ![Ottimizzazione automatica](./media/sql-database-performance-tutorial/auto_tuning.png)

## <a name="revert-a-recommendation"></a>Ripristinare una raccomandazione

Advisor per database monitora tutte le raccomandazioni implementate. Se una raccomandazione non migliora il carico di lavoro, viene ripristinata automaticamente. Il ripristino manualmente di una raccomandazione è possibile, ma nella maggior parte dei casi non è necessario. Per ripristinare una raccomandazione:

1. Passare al menu Raccomandazioni per le prestazioni e selezionare una delle raccomandazioni applicate.

    ![Selezionare una raccomandazione](./media/sql-database-performance-tutorial/select.png)

2. Nella visualizzazione dettagli, fare clic su **Ripristina**.

    ![Ripristinare una raccomandazione](./media/sql-database-performance-tutorial/revert.png)

## <a name="find-the-query-that-consumes-the-most-resources"></a>Trovare la query con l'utilizzo di risorse più elevato

Per trovare la query con l'utilizzo di risorse più elevato, seguire questa procedura:

1. Fare clic sul menu **Informazioni dettagliate prestazioni query** nel pannello del database.

    ![Informazioni dettagliate query](./media/sql-database-performance-tutorial/query_perf_insights.png)

2. Selezionare un tipo di risorsa.

    ![Informazioni dettagliate query](./media/sql-database-performance-tutorial/select_resource_type.png)

3. Selezionare la prima query nella tabella.

    ![Informazioni dettagliate query](./media/sql-database-performance-tutorial/select_query.png)

4. Esaminare i dettagli della query.

    ![Informazioni dettagliate query](./media/sql-database-performance-tutorial/query_details.png)

## <a name="find-the-longest-running-query"></a>Trovare la query in esecuzione da più tempo

1. Passare a Informazioni dettagliate prestazioni query e selezionare la scheda **Query a esecuzione prolungata**.

    ![Informazioni dettagliate query](./media/sql-database-performance-tutorial/long_running.png)

3. Selezionare la prima query nella tabella.

    ![Informazioni dettagliate query](./media/sql-database-performance-tutorial/select_first_query.png)

4. Esaminare i dettagli della query.

    ![Informazioni dettagliate query](./media/sql-database-performance-tutorial/review_query_details.png)



## <a name="next-steps"></a>Passaggi successivi 
Spesso le prestazioni non ottimali del database sono dovute a indici mancanti e query non ottimizzate correttamente. In questa esercitazione si è appreso come:
> [!div class="checklist"]
> * Esaminare, applicare e ripristinare le raccomandazioni per migliorare le prestazioni.
> * Trovare query con un utilizzo elevato delle risorse.
> * Trovare query con esecuzione prolungata.

[Suggerimenti sull'ottimizzazione delle prestazioni del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-performance)

