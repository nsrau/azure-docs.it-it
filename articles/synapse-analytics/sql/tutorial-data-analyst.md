---
title: Usare SQL su richiesta (anteprima) per analizzare i set di dati aperti di Azure in Azure Synapse Studio (anteprima)
description: Questa esercitazione illustra come eseguire facilmente l'analisi esplorativa dei dati combinando diversi set di dati aperti di Azure con SQL su richiesta (anteprima) e visualizzare i risultati in Azure Synapse Studio.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 2fc98e927fcf9686f0f39dae600f944b485c5a06
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089142"
---
# <a name="use-sql-on-demand-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio"></a>Usare SQL su richiesta per analizzare i set di dati aperti di Azure e visualizzare i risultati in Azure Synapse Studio

Questa esercitazione illustra come eseguire l'analisi esplorativa dei dati combinando diversi set di dati aperti di Azure con SQL su richiesta e visualizzare i risultati in Azure Synapse Studio.

In particolare, si analizza il [set di dati del taxi di New York](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) che include:

- Date e ore di inizio e fine corsa.
- Luoghi di inizio e fine corsa. 
- Distanze delle corse.
- Tariffe dettagliate.
- Tipi di tariffa.
- Tipi di pagamento. 
- Numero di passeggeri segnalato dall'autista.

## <a name="automatic-schema-inference"></a>Inferenza automatica dello schema

Poiché i dati vengono archiviati nel formato di file Parquet, è disponibile l'inferenza automatica dello schema. È possibile eseguire facilmente query sui dati senza elencare i tipi di dati di tutte le colonne nei file. È anche possibile usare il meccanismo delle colonne virtuali e la funzione filepath per filtrare un determinato sottoinsieme di file.

Per acquisire familiarità con i dati dei taxi di New York, eseguire la query seguente:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

Il frammento seguente mostra il risultato per i dati dei taxi di New York:

![Frammento di risultati dei dati dei taxi di New York](./media/tutorial-data-analyst/1.png)

Allo stesso modo, è possibile eseguire una query sul set di dati di festività pubbliche con la query seguente:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Il frammento seguente mostra il risultato per il set di dati di festività pubbliche:

![Frammento di risultati del set di dati di festività pubbliche](./media/tutorial-data-analyst/2.png)

Infine, è anche possibile eseguire una query sul set di dati meteo con la query seguente:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Il frammento seguente mostra il risultato per il set di dati meteo:

![Frammento di risultati del set di dati meteo](./media/tutorial-data-analyst/3.png)

Per altre informazioni sul significato delle singole colonne, vedere le descrizioni dei set di dati [NYC Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/), [Festività pubbliche](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) e [Dati meteorologici](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/).

## <a name="time-series-seasonality-and-outlier-analysis"></a>Analisi di outlier, stagionalità e serie temporali

È possibile riepilogare facilmente il numero annuale di corse di taxi con la query seguente:

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

Il frammento seguente mostra il risultato per il numero annuale di corse di taxi:

![Frammento del risultato del numero annuale di corse di taxi](./media/tutorial-data-analyst/4.png)

I dati possono essere visualizzati in Synapse Studio passando da una visualizzazione **Tabella** a una visualizzazione **Grafico**. È possibile scegliere tra diversi tipi di grafico, ad esempio **ad area**, **a barre**, **istogramma**, **a linee**, **a torta** e **a dispersione**. In questo caso, verrà tracciato un **istogramma** con la colonna **Categoria** impostata su **current_year**:

![Istogramma che mostra il numero annuale di corse](./media/tutorial-data-analyst/5.png)

In questa visualizzazione è chiaramente visibile una tendenza decrescente del numero di corse nel corso degli anni. Presumibilmente, questo decremento è dovuto al recente aumento di popolarità delle aziende di ride-sharing.

> [!NOTE]
> Al momento della stesura di questa esercitazione, i dati per 2019 sono incompleti. Di conseguenza, è presente un enorme calo nel numero di corse per quell'anno.

A questo punto, l'analisi verrà incentrata su un singolo anno, ad esempio il 2016. La query seguente restituisce il numero giornaliero di corse durante l'anno:

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

Il frammento seguente mostra il risultato di questa query:

![Frammento del risultato del numero giornaliero di corse per il 2016](./media/tutorial-data-analyst/6.png)

Anche in questo caso, è possibile visualizzare facilmente i dati tracciando l'**istogramma** con la colonna **Categoria** impostata su **current_day** e la colonna **Legenda (serie)** su **rides_per_day**.

![Istogramma che mostra il numero giornaliero di corse per il 2016](./media/tutorial-data-analyst/7.png)

Dal grafico del tracciato si evince un modello settimanale, con sabato come giorno di punta. Durante i mesi estivi, il numero di corse di taxi è inferiore a causa delle vacanze. Sono anche visibili anche alcuni cali significativi nel numero di corse di taxi senza un modello chiaro che evidenzi quando e perché si verificano.

A questo punto, è necessario verificare se tali cali sono correlati alle festività pubbliche incrociando il set di dati delle corse dei taxi di New York con quello delle festività pubbliche:

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT='PARQUET'
        ) AS [nyc]
    WHERE nyc.filepath(1) = '2016'
    GROUP BY CAST([tpepPickupDateTime] AS DATE)
),
public_holidays AS
(
    SELECT
        holidayname as holiday,
        date
    FROM
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
            FORMAT='PARQUET'
        ) AS [holidays]
    WHERE countryorregion = 'United States' AND YEAR(date) = 2016
)
SELECT
*
FROM taxi_rides t
LEFT OUTER JOIN public_holidays p on t.current_day = p.date
ORDER BY current_day ASC
```

![Visualizzazione del risultato dei set di dati delle corse di taxi di New York e delle festività pubbliche](./media/tutorial-data-analyst/8.png)

Questa volta verrà evidenziato il numero di corse di taxi durante le festività pubbliche. A tale scopo, si sceglierà **none** per la colonna **Categoria** e **rides_per_day** e **holiday** come colonne **Legenda (serie)** .

![Tracciato del grafico del numero di corse di taxi durante le festività](./media/tutorial-data-analyst/9.png)

Dal tracciato, si osserva che durante le festività pubbliche il numero di corse di taxi è inferiore. Il 23 gennaio si registra un importante calo inspiegabile. A questo punto, verrà controllato il meteo di New York per il giorno in questione tramite una query sul set di dati meteo:

```sql
SELECT
    AVG(windspeed) AS avg_windspeed,
    MIN(windspeed) AS min_windspeed,
    MAX(windspeed) AS max_windspeed,
    AVG(temperature) AS avg_temperature,
    MIN(temperature) AS min_temperature,
    MAX(temperature) AS max_temperature,
    AVG(sealvlpressure) AS avg_sealvlpressure,
    MIN(sealvlpressure) AS min_sealvlpressure,
    MAX(sealvlpressure) AS max_sealvlpressure,
    AVG(precipdepth) AS avg_precipdepth,
    MIN(precipdepth) AS min_precipdepth,
    MAX(precipdepth) AS max_precipdepth,
    AVG(snowdepth) AS avg_snowdepth,
    MIN(snowdepth) AS min_snowdepth,
    MAX(snowdepth) AS max_snowdepth
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![Visualizzazione del risultato del set di dati meteo](./media/tutorial-data-analyst/10.png)

I risultati della query indicano che il calo del numero di corse di taxi è stato dovuto a:

- Una bufera di neve su New York, con una pesante nevicata (circa 30 cm).
- Una temperatura particolarmente fredda, inferiore a zero gradi Celsius.
- Un vento forte (circa 10 m/s).

Questa esercitazione ha illustrato come gli analisti dati possono eseguire rapidamente l'analisi esplorativa dei dati, combinare facilmente set di dati diversi usando SQL su richiesta e visualizzare i risultati con Azure Synapse Studio.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come connettere SQL su richiesta a Power BI Desktop e creare report, vedere l'articolo [Connettere SQL su richiesta a Power BI Desktop e creare report](tutorial-connect-power-bi-desktop.md).
 
