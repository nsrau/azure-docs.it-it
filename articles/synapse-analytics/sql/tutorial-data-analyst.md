---
title: 'Analista dati - Esercitazione: Usare SQL su richiesta (anteprima) per analizzare i set di dati aperti di Azure in Azure Synapse Studio (anteprima)'
description: Questa esercitazione illustra come eseguire facilmente l'analisi esplorativa dei dati combinando diversi set di dati aperti di Azure con SQL su richiesta (anteprima) e visualizzare i risultati in Azure Synapse Studio.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1080001cb222f91503080914d7fb253e5ee82626
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81426280"
---
# <a name="use-sql-on-demand-preview-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio-preview"></a>Usare SQL su richiesta (anteprima) per analizzare i set di dati aperti di Azure e visualizzare i risultati in Azure Synapse Studio (anteprima)

Questa esercitazione illustra come eseguire l'analisi esplorativa dei dati combinando diversi set di dati aperti di Azure con SQL su richiesta e visualizzare i risultati in Azure Synapse Studio.

In particolare, verrà analizzato il [set di dati New York City (NYC) Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) che include date e ore di partenza e di arrivo, luoghi di partenza e di arrivo, distanze delle corse, dettagli delle tariffe, tipi di tariffa, tipi di pagamento e numero di passeggeri segnalato dal tassista.

L'obiettivo dell'analisi è trovare le tendenze relative alle variazioni del numero di corse di taxi nel tempo. Verranno anche analizzati altri due set di dati aperti di Azure ([Festività pubbliche](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) e [Dati meteorologici](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)) per comprendere gli outlier in numero di corse di taxi.

## <a name="create-credentials"></a>Creare le credenziali

```sql
-- There is no secret. We are using public storage account which doesn't need a secret.
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/nyctlc]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO
```

## <a name="automatic-schema-inference"></a>Inferenza automatica dello schema

Poiché i dati sono archiviati nel formato di file Parquet, è disponibile l'inferenza automatica dello schema, quindi è possibile eseguire facilmente query sui dati senza dover elencare i tipi di dati di tutte le colonne nei file. È inoltre possibile usare il meccanismo delle colonne virtuali e la funzione FilePath per filtrare un determinato subset di file.

Per acquisire familiarità con i dati di NYC Taxi, è prima necessario eseguire la query seguente:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

Di seguito è riportato il frammento del risultato per i dati di NYC Taxi:

![frammento del risultato](./media/tutorial-data-analyst/1.png)

Allo stesso modo, è possibile eseguire una query sul set di dati Festività pubbliche con la query seguente:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Di seguito è riportato il frammento del risultato per il set di dati delle festività pubbliche:

![frammento di risultato 2](./media/tutorial-data-analyst/2.png)

Infine, è anche possibile eseguire una query sul set di dati meteorologici con la query seguente:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Di seguito è riportato il frammento del risultato per il set di dati meteorologici:

![frammento del risultato 3](./media/tutorial-data-analyst/3.png)

Per altre informazioni sul significato delle singole colonne, vedere le descrizioni dei set di dati [NYC Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/), [Festività pubbliche](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) e [Dati meteorologici](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/).

## <a name="time-series-seasonality-and-outlier-analysis"></a>Analisi di outlier, stagionalità e serie temporali

È possibile riepilogare facilmente il numero annuale di corse taxi usando la query seguente:

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

Di seguito è riportato il frammento del risultato per il numero annuale di corse taxi:

![frammento del risultato 4](./media/tutorial-data-analyst/4.png)

I dati possono essere visualizzati in Synapse Studio passando da una visualizzazione tabella a una visualizzazione grafico. È possibile scegliere tra diversi tipi di grafico (ad area, a barre, istogramma, a linee, a torta e a dispersione). In questo caso, verrà tracciato un istogramma con la colonna Categoria impostata su "current_year":

![visualizzazione dei risultati 5](./media/tutorial-data-analyst/5.png)

Da questa visualizzazione, si evidenzia chiaramente una tendenza di un numero decrescente di corse negli anni, presumibilmente a causa dell'aumento della diffusione delle società di ride sharing.

> [!NOTE]
> Al momento della stesura di questa esercitazione, i dati per l'anno 2019 sono incompleti, evidenziando quindi un notevole calo del numero di corse annuale.

A questo punto, l'analisi verrà concentrata su un singolo anno, ad esempio il 2016. La query seguente restituisce il numero giornaliero di corse durante l'anno:

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

Di seguito è riportato il frammento del risultato per questa query:

![frammento del risultato 6](./media/tutorial-data-analyst/6.png)

Anche in questo caso, è possibile visualizzare facilmente i dati tracciando l'istogramma con la colonna Categoria "current_day" e la colonna Legenda (serie) "rides_per_day".

![visualizzazione dei risultati 7](./media/tutorial-data-analyst/7.png)

Dal tracciato è possibile osservare che è presente un modello settimanale, con il picco di sabato. Durante i mesi estivi, il numero di corse di taxi è inferiore a causa delle vacanze. Sono tuttavia presenti anche alcuni cali significativi nel numero di corse di taxi senza un modello chiaro che evidenzi quando e perché si verificano.

A questo punto, è necessario verificare se tali cali sono potenzialmente correlati alle festività pubbliche incrociando il set di dati delle corse dei taxi di NYC con il set di dati delle festività pubbliche:

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

![visualizzazione dei risultati 8](./media/tutorial-data-analyst/8.png)

Questa volta verrà evidenziato il numero di corse di taxi durante le festività pubbliche. A tale scopo, si sceglierà "none" per la colonna Categoria e "rides_per_day" e "holiday" come colonne Legenda (serie).

![visualizzazione dei risultati 9](./media/tutorial-data-analyst/9.png)

Dal tracciato, si osserva chiaramente che durante le festività pubbliche il numero di corse di taxi è inferiore. Tuttavia, il 23 gennaio si registra un importante calo inspiegabile. A questo punto, verrà controllato il meteo di NYC per il giorno in questione tramite una query sul set di dati meteorologici:

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

![visualizzazione dei risultati 10](./media/tutorial-data-analyst/10.png)

I risultati della query indicano che il calo del numero di corse di taxi è stato dovuto a:

- una bufera di neve su NYC, con una pesante nevicata (circa 30 cm)
- tempo freddo (temperatura inferiore a zero gradi Celsius)
- e vento (circa 10 m/s)

Questa esercitazione ha illustrato come gli analisti dati possono eseguire rapidamente l'analisi esplorativa dei dati, combinare facilmente set di dati diversi usando SQL su richiesta e visualizzare i risultati con Azure Synapse Studio.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come connettere SQL su richiesta a Power BI Desktop e creare report, vedere l'articolo [Connettere SQL su richiesta a Power BI Desktop e creare report](tutorial-connect-power-bi-desktop.md).
 
