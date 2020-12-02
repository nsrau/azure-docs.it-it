---
title: 'Esercitazione: Esplorare e analizzare data lake con Synapse SQL serverless'
description: Questa esercitazione illustra come eseguire facilmente l'analisi esplorativa dei dati combinando diversi set di dati aperti di Azure usando un pool SQL serverless (anteprima) e visualizzare i risultati in Synapse Studio per Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 11/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: af6fc75b5de22fc77313932ca17ce695e889dad3
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2020
ms.locfileid: "95237964"
---
# <a name="tutorial-explore-and-analyze-data-lakes-with-serverless-sql-pool-preview"></a>Esercitazione: Esplorare e analizzare data lake con un pool SQL serverless (anteprima)

Questa esercitazione illustra come eseguire un'analisi esplorativa dei dati. Si combineranno diversi set di dati aperti di Azure usando un pool SQL serverless. Quindi si visualizzeranno i risultati in Synapse Studio per Azure Synapse Analytics.

La funzione OPENROWSET (BULK) consente di accedere ai file di Archiviazione di Azure. La funzione [OPENROWSET](develop-openrowset.md) legge il contenuto di un'origine dati remota, ad esempio un file, e lo restituisce come set di righe.

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

Il [set di dati dei taxi di New York City (NYC)](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) include:

- Date e ore di inizio e fine corsa.
- Luoghi di inizio e fine corsa. 
- Distanze delle corse.
- Tariffe dettagliate.
- Tipi di tariffa.
- Tipi di pagamento. 
- Numero di passeggeri segnalato dall'autista.

Allo stesso modo, è possibile eseguire una query sul set di dati di festività pubbliche con la query seguente:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

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

Altre informazioni sul significato delle singole colonne sono disponibili nelle descrizioni dei set di dati: 
- [Taxi di NYC](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)
- [Festività pubbliche](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)
- [Dati meteo](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)

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

![Frammento del risultato del numero annuale di corse di taxi](./media/tutorial-data-analyst/yearly-taxi-rides.png)

I dati possono essere visualizzati in Synapse Studio passando da una visualizzazione **Tabella** a una visualizzazione **Grafico**. È possibile scegliere tra diversi tipi di grafico, ad esempio **ad area**, **a barre**, **istogramma**, **a linee**, **a torta** e **a dispersione**. In questo caso, verrà tracciato un **istogramma** con la colonna **Categoria** impostata su **current_year**:

![Istogramma che mostra il numero annuale di corse](./media/tutorial-data-analyst/column-chart-rides-year.png)

In questa visualizzazione è riportata una tendenza di numeri di corse in diminuzione nel corso degli anni. Presumibilmente, questo decremento è dovuto al recente aumento di popolarità delle aziende di ride-sharing.

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

![Frammento del risultato del numero giornaliero di corse per il 2016](./media/tutorial-data-analyst/daily-rides.png)

Anche in questo caso, è possibile visualizzare facilmente i dati tracciando l'**istogramma** con la colonna **Categoria** impostata su **current_day** e la colonna **Legenda (serie)** su **rides_per_day**.

![Istogramma che mostra il numero giornaliero di corse per il 2016](./media/tutorial-data-analyst/column-chart-daily-rides.png)

Dal grafico del tracciato si evince un modello settimanale, con il sabato come giorno di punta. Durante i mesi estivi, il numero di corse di taxi è inferiore a causa delle vacanze. Sono anche visibili anche alcuni cali significativi nel numero di corse di taxi senza un modello chiaro che evidenzi quando e perché si verificano.

Verrà ora illustrata la correlazione tra il calo di corse e le festività pubbliche. È possibile verificare che esiste una correzione unendo il set di dati sulle corse dei taxi di New York con quello delle festività pubbliche:

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

![Visualizzazione del risultato dei set di dati delle corse di taxi di New York e delle festività pubbliche](./media/tutorial-data-analyst/rides-public-holidays.png)

Questa volta verrà evidenziato il numero di corse di taxi durante le festività pubbliche. A tale scopo, si sceglierà **none** per la colonna **Categoria** e **rides_per_day** e **holiday** come colonne **Legenda (serie)** .

![Tracciato del grafico del numero di corse di taxi durante le festività](./media/tutorial-data-analyst/plot-chart-public-holidays.png)

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

![Visualizzazione del risultato del set di dati meteo](./media/tutorial-data-analyst/weather-data-set-visualization.png)

I risultati della query indicano che il calo del numero di corse di taxi è stato dovuto a:

- Una bufera di neve su New York, con una pesante nevicata (circa 30 cm).
- Una temperatura particolarmente fredda, inferiore a zero gradi Celsius.
- Un vento forte (circa 10 m/s).

Questa esercitazione ha illustrato come gli analisti dei dati possono eseguire rapidamente l'analisi esplorativa dei dati, combinare facilmente set di dati diversi usando un pool SQL serverless e visualizzare i risultati con Azure Synapse Studio.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come connettere il pool SQL serverless a Power BI Desktop e creare report, vedere l'articolo [Connettere un pool SQL serverless a Power BI Desktop e creare report](tutorial-connect-power-bi-desktop.md).

Per informazioni su come usare tabelle esterne nel pool SQL serverless, vedere [Usare tabelle esterne con Synapse SQL](develop-tables-external-tables.md?tabs=sql-pool)
 
