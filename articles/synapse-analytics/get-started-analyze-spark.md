---
title: "Esercitazione: Introduzione all'analisi con Spark"
description: Questa esercitazione illustra i passaggi di base per la configurazione e l'uso di Azure Synapse Analytics.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 5c6b35c1d9f00cae8fc688569e3a491679900995
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093799"
---
# <a name="analyze-with-apache-spark"></a>Analizzare dati con Apache Spark

Questa esercitazione illustra i passaggi di base per caricare e analizzare i dati con Apache Spark per Azure Synapse.

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Caricare i dati di NYC Taxi nel database nyctaxi di Spark

I dati sono disponibili in una tabella in **SQLDB1**. Caricarli in un database Spark denominato **nyctaxi**.

1. In Synapse Studio passare all'hub **Develop** (Sviluppo).
1. Selezionare **+**  > **Notebook**.
1. Nella parte superiore del notebook impostare il valore per **Collega a** su **Spark1**.
1. Selezionare **Aggiungi codice** per aggiungere una cella di codice del notebook e incollare il testo seguente:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Passare all'hub **Dati**, fare clic con il pulsante destro del mouse su **Database** e scegliere **Aggiorna**. Verranno visualizzati i database seguenti:

    - **SQLDB1** (pool SQL)
    - **nyctaxi** (Spark)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analizzare i dati dell'esempio NYC Taxi con Spark e notebook

1. Tornare al notebook.
1. Creare una nuova cella di codice e immettere il testo seguente. Eseguire quindi la cella per visualizzare i dati di NYC Taxi caricati nel database Spark **nyctaxi**.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Eseguire il codice seguente per ripetere la stessa analisi effettuata in precedenza con il pool SQL **SQLDB1**. Questo codice salva i risultati dell'analisi in una tabella denominata **nyctaxi.passengercountstats** e consente di visualizzarli.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. Nei risultati della cella selezionare **Grafico** per visualizzare i dati.

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>Personalizzare la visualizzazione dei dati con Spark e notebook

È possibile controllare il rendering dei grafici tramite i notebook. Il codice seguente mostra un semplice esempio. Usa le note librerie **matplotlib** e **seaborn**. Il rendering del codice verrà eseguito nella stessa tipologia di grafico a linee delle query SQL eseguite in precedenza.

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```



## <a name="load-data-from-a-spark-table-into-a-sql-pool-table"></a>Caricare dati da una tabella Spark in una tabella del pool SQL

In precedenza sono stati copiati i dati dalla tabella del pool SQL **SQLDB1.dbo.Trip** nella tabella Spark **nyctaxi.trip**. In seguito, tramite Spark i dati sono stati aggregati nella tabella Spark **nyctaxi.passengercountstats**. A questo punto, i dati verranno copiati da **nyctaxi.passengercountstats** in una tabella del pool SQL denominata **SQLDB1.dbo.PassengerCountStats**.

Eseguire la cella seguente nel notebook. La tabella Spark aggregata verrà copiata nuovamente nella tabella del pool SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analizzare i dati nell'archiviazione](get-started-analyze-storage.md)


