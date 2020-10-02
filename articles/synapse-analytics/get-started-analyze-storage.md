---
title: "Esercitazione: Introduzione all'analisi dei dati in account di archiviazione"
description: In questa esercitazione si apprenderà ad analizzare i dati presenti in un account di archiviazione.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: a0d5c758873413e549b31e3ec4cc41791fc8c371
ms.sourcegitcommit: 0194a29a960e3615f96a2d9d8a7e681cf3e8f9ab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89667412"
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
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyze-data-in-a-storage-account"></a>Analizzare i dati in un account di archiviazione

1. In Synapse Studio passare all'hub **Data** (Dati) e quindi selezionare **Linked** (Collegati).
1. Passare a **Account di archiviazione** > **myworkspace (Primario - contosolake)** .
1. Selezionare **utenti (Primario)** . Verrà visualizzata la cartella **NYCTaxi**. Al suo interno saranno presenti due cartelle denominate **PassengerCountStats.csv** e **PassengerCountStats.parquet**.
1. Aprire la cartella **PassengerCountStats.parquet**. All'interno sarà presente un file parquet con nome come `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet`.
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

    Nella finestra dello script il campo **Connetti a** è impostato su **SQL su richiesta**.

1. Eseguire lo script.



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Orchestrare le attività con le pipeline](get-started-pipelines.md)
