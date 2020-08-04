---
title: "Esercitazione: Introduzione all'analisi dei dati con il pool SQL"
description: In questa esercitazione si useranno i dati di esempio NYC Taxi per esplorare le funzionalità di analisi del pool SQL.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: e2e1d0479b8edacaae8816d74db061eeedb805a7
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325220"
---
# <a name="analyze-data-with-sql-pools"></a>Analizzare i dati con i pool SQL

Azure Synapse Analytics offre la possibilità di analizzare i dati con il pool SQL. In questa esercitazione si useranno i dati di esempio NYC Taxi per esplorare le funzionalità di analisi del pool SQL.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Caricare i dati di esempio di NYC Taxi nel database SQLDB1

1. Nel menu blu più in alto di Synapse Studio selezionare l'icona del punto interrogativo ( **?** ).
1. Selezionare **Getting started** > **Getting started hub** (Attività iniziali > Hub attività iniziali).
1. Nella scheda etichettata **Query sample data** (Dati di esempio query) selezionare il pool SQL denominato **SQLDB1**.
1. Selezionare **Query data** (Dati query). Verrà visualizzata brevemente una notifica di caricamento dei dati di esempio. Nella parte superiore di Synapse Studio viene visualizzata una barra di stato azzurra che indica che è in corso il caricamento dei dati in SQLDB1.
1. Quando la barra di stato diventa verde, eliminarla.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Esplorare i dati di esempio di NYC Taxi nel pool SQL

1. In Synapse Studio passare all'hub **Data** (Dati).
1. Passare a **SQLDB1** > **Tables**. Saranno disponibili numerose tabelle.
1. Fare clic con il pulsante destro del mouse sulla tabella **dbo.Trip** e selezionare **New SQL Script** > **Select TOP 100 Rows** (Nuovo script SQL > Seleziona prime 100 righe).
1. Attendere mentre viene creato ed eseguito un nuovo script SQL.
1. Si noti che nella parte superiore dello script SQL l'opzione **Connetti a** è impostata automaticamente sul pool SQL denominato **SQLDB1**.
1. Sostituire il testo dello script SQL con il codice ed eseguirlo.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    Questa query mostra come le distanze totali e la distanza media delle corse sono correlate al numero di passeggeri.
1. Nella finestra dei risultati dello script SQL modificare la **Vista** impostandola su **Grafico** per visualizzare i risultati come un grafico a linee.



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Usare Spark per l'analisi](get-started-analyze-spark.md)

