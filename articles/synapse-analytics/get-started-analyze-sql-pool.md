---
title: "Esercitazione: Introduzione all'analisi dei dati con pool SQL dedicati"
description: In questa esercitazione si useranno i dati di esempio NYC Taxi per esplorare le funzionalità di analisi del pool SQL.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: c46adf9e9f5c1b2e74c1098ebf137c4556bfc58d
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147552"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>Analizzare i dati con pool SQL dedicati

Azure Synapse Analytics offre la possibilità di analizzare i dati con un pool SQL dedicato. In questa esercitazione si useranno i dati dei taxi di New York per esplorare le funzionalità di un pool SQL dedicato.

## <a name="load-the-nyc-taxi-data-into-sqldb1"></a>Caricare i dati dei taxi di New York in SQLDB1

1. In Synapse Studio passare all'hub **Sviluppo** e creare un nuovo script SQL
1. Selezionare il pool 'SQLDB1'(creato nel [passaggio 1](https://docs.microsoft.com/azure/synapse-analytics/get-started-create-workspace#create-a-sql-pool) di questa esercitazione) nella sezione 'Connetti a' dello script.
1. Immettere il codice seguente:
    ```
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );

    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/QID6392_20171107_05910_0.txt.gz'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```
1. L'esecuzione di questo script richiederà circa 1 minuto. Carica 2 milioni righe di dati dei taxi di New York in una tabella denominata **dbo.Trip**

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>Esplorare i dati dei taxi di New York nel pool SQL dedicato

1. In Synapse Studio passare all'hub **Data** (Dati).
1. Passare a **SQLDB1** > **Tables** . Saranno disponibili numerose tabelle.
1. Fare clic con il pulsante destro del mouse sulla tabella **dbo.Trip** e selezionare **New SQL Script** > **Select TOP 100 Rows** (Nuovo script SQL > Seleziona prime 100 righe).
1. Attendere mentre viene creato ed eseguito un nuovo script SQL.
1. Si noti che nella parte superiore dello script SQL l'opzione **Connetti a** è impostata automaticamente sul pool SQL denominato **SQLDB1** .
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

