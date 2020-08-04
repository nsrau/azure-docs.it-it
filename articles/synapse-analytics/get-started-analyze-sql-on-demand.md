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
ms.openlocfilehash: 4011cd93879d9203d8231f24bbf531d14e6e815a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093819"
---
# <a name="analyze-data-with-sql-on-demand"></a>Analizzare i dati con SQL su richiesta

In questa esercitazione si apprenderà come analizzare i dati con SQL su richiesta usando i dati presenti nei database Spark. 

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
