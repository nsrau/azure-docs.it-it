---
title: "Esercitazione: Introduzione all'analisi dei dati in account di archiviazione"
description: In questa esercitazione si apprenderà ad analizzare i dati presenti in un account di archiviazione.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 5e3fbd1868cc1216cb7b9d02b2aa8e690af33952
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917682"
---
# <a name="analyze-data-in-a-storage-account"></a>Analizzare i dati in un account di archiviazione

In questa esercitazione si apprenderà ad analizzare i dati presenti in un account di archiviazione.

## <a name="overview"></a>Panoramica

Fino a questo momento sono stati illustrati gli scenari in cui i dati risiedono nei database nell'area di lavoro. Verrà ora illustrato come usare i file negli account di archiviazione. In questo scenario viene usato l'account di archiviazione primario dell'area di lavoro e il contenitore specificato durante la creazione dell'area di lavoro stessa.

* Nome dell'account di archiviazione: **contosolake**
* Nome del contenitore nell'account di archiviazione: **users**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>Creare file CSV e Parquet nell'account di archiviazione

Eseguire il codice seguente in un notebook per creare un file CSV e un file Parquet nell'account di archiviazione.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats_csvformat")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats_parquetformat")
```

### <a name="analyze-data-in-a-storage-account"></a>Analizzare i dati in un account di archiviazione

È possibile analizzare i dati nell'account ADLS Gen2 predefinito dell'area di lavoro oppure collegare un account di archiviazione BLOB o ADLS Gen2 all'area di lavoro tramite "**Gestisci**" > "**Servizi collegati**" > "**Nuovo**" (la procedura seguente fa riferimento all'account ADLS Gen2 primario).

1. In Synapse Studio passare all'hub **Data** (Dati) e quindi selezionare **Linked** (Collegati).
1. Passare a **Account di archiviazione** > **myworkspace (Primario - contosolake)** .
1. Selezionare **utenti (Primario)** . Verrà visualizzata la cartella **NYCTaxi**. All'interno dovrebbero essere visibili due cartelle denominate **PassengerCountStats_csvformat** e **PassengerCountStats_parquetformat**.
1. Aprire la cartella **PassengerCountStats_parquetformat**. All'interno sarà presente un file parquet con nome come `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet`.
1. Fare clic con il pulsante destro del mouse su **.parquet**, quindi scegliere **Nuovo notebook**. Verrà creato un notebook con una cella simile alla seguente:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Eseguire la cella.
1. Fare clic con il pulsante destro del mouse sul file parquet al suo interno, quindi scegliere **New SQL script** > **SELECT TOP 100 rows** (Nuovo script SQL > Seleziona prime 100 righe). Verrà creato uno script SQL simile al seguente:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

    Nella finestra dello script il campo **Connetti a** è impostato su **Pool SQL serverless**.

1. Eseguire lo script.



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Orchestrare le attività con le pipeline](get-started-pipelines.md)
