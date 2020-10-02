---
title: "Esercitazione: Introduzione all'analisi dei dati con SQL su richiesta"
description: In questa esercitazione si apprenderà come analizzare i dati con SQL su richiesta usando i dati presenti nei database Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 93ebc1c5e89e54f4813f270b9f8b7b13f672fbe3
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90016113"
---
# <a name="analyze-data-with-sql-on-demand"></a>Analizzare i dati con SQL su richiesta

In questa esercitazione si apprenderà come analizzare i dati con SQL su richiesta usando i dati presenti nei database Spark. 

## <a name="analyze-nyc-taxi-data-in-blob-storage--using-sql-on-demand"></a>Analizzare i dati dei taxi di New York in archiviazione BLOB con SQL su richiesta

1. Nell'hub **Dati** in **Collegati** fare clic il pulsante destro del mouse su **Archivio BLOB di Azure > Set di dati di esempio > nyc_tlc_yellow** e scegliere **SELECT TOP 100 rows** (Seleziona le prime 100 righe)
1. Verrà creato un nuovo script SQL con il codice seguente:

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. Fare clic su **Run**

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analizzare i dati di NYC Taxi nei database Spark tramite SQL su richiesta

Le tabelle nei database Spark sono automaticamente visibili e possono essere usate da SQL su richiesta per l'esecuzione di query.

1. In Synapse Studio passare all'hub **Develop** (Sviluppo) e creare un nuovo script SQL.
1. Impostare **Connetti a** su **SQL su richiesta**.
1. Incollare il testo seguente nello script ed eseguire lo script.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > La prima volta che si esegue una query che usa SQL su richiesta, la raccolta delle risorse SQL necessarie per l'esecuzione delle query richiederà circa 10 secondi. Le query successive saranno molto più veloci.
  


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Usare Spark per l'analisi](get-started-analyze-spark.md)
