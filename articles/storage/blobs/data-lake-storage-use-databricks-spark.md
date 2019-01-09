---
title: 'Esercitazione: Accedere ai dati della versione di anteprima di Azure Data Lake Storage Gen2 con Azure Databricks usando Spark | Microsoft Docs'
description: Questa esercitazione mostra come eseguire query Spark su un cluster Azure Databricks per accedere ai dati in un account di archiviazione Azure Data Lake Storage Gen2.
services: storage
author: dineshmurthy
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: dineshm
ms.openlocfilehash: b0382d31f9d16228ca3447ace9c7d4f171b206f6
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548987"
---
# <a name="tutorial-access-data-lake-storage-gen2-preview-data-with-azure-databricks-using-spark"></a>Esercitazione: Accedere ai dati della versione di anteprima di Azure Data Lake Storage Gen2 con Azure Databricks tramite Spark

Questa esercitazione mostra come connettere il cluster Azure Databricks ai dati archiviati in un account di archiviazione di Azure per cui è abilitato Azure Data Lake Storage Gen2 (anteprima). Questa connessione permette di eseguire in modo nativo query e analisi dal cluster sui dati.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un cluster di Databricks
> * Inserire dati non strutturati in un account di archiviazione
> * Esecuzione di analisi sui dati nell'archiviazione BLOB

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione illustra come utilizzare ed eseguire query sui dati relativi ai voli di una compagnia aerea, resi disponibili dal [Dipartimento dei trasporti degli Stati Uniti](https://transtats.bts.gov/DL_SelectFields.asp). 

1. Selezionare la casella di controllo **Prezipped file** (File precompresso) per selezionare tutti i campi dati.
2. Selezionare **Scarica** e salvare i risultati nel computer.
3. Prendere nota del nome file e del percorso del download, perché queste informazioni saranno necessarie in un passaggio successivo.

Per completare questa esercitazione, è necessario un account di archiviazione con funzionalità di analisi. È consigliabile completare gli argomenti di [avvio rapido](data-lake-storage-quickstart-create-account.md) pertinenti per crearne uno. Dopo aver creato l'account di archiviazione, passare all'account per recuperare le impostazioni di configurazione.

1. In **Impostazioni** selezionare **Chiavi di accesso**.
2. Selezionare il pulsante **Copia** accanto a **key1** per copiare il valore della chiave.

Per i passaggi successivi di questa esercitazione sono richiesti sia il nome dell'account che la chiave. Aprire un editor di testo e tenere da parte il nome dell'account e la chiave per riferimento futuro.

## <a name="create-a-databricks-cluster"></a>Creare un cluster di Databricks

Il passaggio successivo consiste nel creare un cluster Databricks per creare un'area di lavoro dati.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa**.
2. Immettere **Azure Databricks** nel campo di ricerca.
3. Selezionare **Crea** nel pannello Azure Databricks.
4. Assegnare al servizio Databricks il nome **myFlightDataService**. Nel creare il servizio, assicurarsi di selezionare la casella di controllo *Aggiungi al dashboard*.
5. Selezionare **Avvia area di lavoro** per aprire l'area di lavoro in una nuova finestra del browser.
6. Selezionare **Cluster** sulla barra di spostamento a sinistra.
7. Selezionare **Crea cluster**.
8. Immettere **myFlightDataCluster** nel campo **Nome cluster**.
9. Selezionare **Standard_D8s_v3** nel campo **Tipo di ruolo di lavoro**.
10. Modificare **Min Workers** (Ruoli di lavoro min) impostandolo su **4**.
11. Selezionare **Crea cluster** nella parte superiore della pagina. Il completamento di questo processo può richiedere fino a 5 minuti.
12. Al termine del processo, selezionare **Azure Databricks** in alto a sinistra sulla barra di spostamento.
13. Selezionare **Notebook** nella sezione **Nuovo** nella parte inferiore della pagina.
14. Immettere il nome desiderato nel campo **Nome** e selezionare **Python** come linguaggio.
15. In tutti gli altri campi possono essere lasciati i valori predefiniti.
16. Selezionare **Create**.
17. Incollare il codice seguente nella cella **Cmd 1**. Sostituire i valori segnaposto racchiusi tra parentesi nell'esempio con i propri valori:

    ```scala
    %python%
    configs = {"fs.azure.account.auth.type": "OAuth",
        "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
        "fs.azure.account.oauth2.client.id": "<service-client-id>",
        "fs.azure.account.oauth2.client.secret": "<service-credentials>",
        "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant-id>/oauth2/token"}
        
    dbutils.fs.mount(
        source = "abfss://dbricks@<account-name>.dfs.core.windows.net/folder1",
        mount_point = "/mnt/flightdata",
        extra_configs = configs)
    ```
18. Premere **MAIUSC+INVIO** per eseguire la cella di codice.

## <a name="ingest-data"></a>Inserire dati

### <a name="copy-source-data-into-the-storage-account"></a>Copiare i dati di origine nell'account di archiviazione

L'operazione successiva prevede l'uso di AzCopy per copiare i dati dal file con estensione *csv* in Archiviazione di Azure. Aprire una finestra del prompt dei comandi e immettere i comandi seguenti. Assicurarsi di sostituire i segnaposto `<DOWNLOAD_FILE_PATH>`, `<ACCOUNT_NAME>` e `<ACCOUNT_KEY>` con i valori corrispondenti annotati in un passaggio precedente.

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_FILE_PATH>" https://<ACCOUNT_NAME>.dfs.core.windows.net/dbricks/folder1/On_Time --recursive 
```

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Usare un notebook di Databricks per convertire il formato CSV in Parquet

Aprire nuovamente Databricks nel browser ed eseguire questa procedura:

1. Selezionare **Azure Databricks** in alto a sinistra sulla barra di spostamento.
2. Selezionare **Notebook** nella sezione **Nuovo** nella parte inferiore della pagina.
3. Immettere **CSV2Parquet** nel campo **Nome**.
4. In tutti gli altri campi possono essere lasciati i valori predefiniti.
5. Selezionare **Create**.
6. Incollare il codice seguente nella cella **Cmd 1**. Questo codice viene salvato automaticamente nell'editor.

    ```python
    # Use the previously established DBFS mount point to read the data
    # create a dataframe to read data
    flightDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/On_Time_On_Time*.csv")
    # read the all the airline csv files and write the output to parquet format for easy query
    flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
    print("Done")
    ```

## <a name="explore-data"></a>Esplorazione dei dati

Tornare all'area di lavoro di Databricks e selezionare l'icona **Recenti** sulla barra di spostamento a sinistra.

1. Selezionare il notebook **Flight Data Analytics**.
2. Premere **CTRL+ALT+N** per creare una nuova cella.

Immettere ognuno dei blocchi di codice seguenti in **Cmd 1** e premere **Cmd+INVIO** per eseguire lo script Python.

Per ottenere un elenco dei file con estensione csv caricati tramite AzCopy, eseguire lo script seguente:

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata/temp/"))
```

Per creare un nuovo file ed elencare i file nella cartella *parquet/flights*, eseguire questo script:

```python
dbutils.fs.put("/mnt/flightdata/temp/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/temp/parquet/flights")
```

Con questi esempi di codice è stata esaminata la natura gerarchica di HDFS usando i dati archiviati in un account di archiviazione con Azure Data Lake Storage Gen2 abilitato.

## <a name="query-the-data"></a>Eseguire query sui dati

È quindi possibile iniziare a eseguire query sui dati caricati nell'account di archiviazione. Immettere ognuno dei blocchi di codice seguenti in **Cmd 1** e premere **Cmd+INVIO** per eseguire lo script Python.

### <a name="run-simple-queries"></a>Eseguire query semplici

Per creare frame di dati per le origini dati, eseguire lo script seguente:

> [!IMPORTANT]
> Assicurarsi di sostituire il segnaposto **<YOUR_CSV_FILE_NAME>** con il nome del file scaricato all'inizio di questa esercitazione.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/<YOUR_CSV_FILE_NAME>.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

#print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

#print the flight database size
print("Number of flights in the database: ", flightDF.count())

#show the first 20 rows (20 is the default)
#to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

#Display to run visualizations
#preferably run this in a separate cmd cell
display(flightDF)
```

Per eseguire query di analisi sui dati, eseguire lo script seguente:

```python
#Run each of these queries, preferably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

#using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights=out2.count()
print("Jan 2016: ", NumJan2016Flights," Feb 2016: ",NumFeb2016Flights)
Total= NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql("SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'") 
print('Airports in Texas: ', out.show(100))

#find all airlines that fly from Texas
out1 = spark.sql("SELECT distinct(Carrier) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

### <a name="run-complex-queries"></a>Eseguire query complesse

Per eseguire la seguenti query più complesse, eseguire un segmento per volta nel notebook e analizzare i risultati.

```python
#find the airline with the most flights

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")

#show the top row without truncation
output.show(1, False)

#show the top 10 airlines
output.show(10, False)

#Determine which is the least on time airline

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE DepDelay>60 or ArrDelay>60 group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("select * from v")
#output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")
#show the top row without truncation
output.show(1, False)

#which airline improved its performance
#find the airline with the most improvement in delays
#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v1")
spark.sql("DROP VIEW IF EXISTS v2")
spark.sql("CREATE TEMPORARY VIEW v1 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2016 group by Carrier order by NumFlights desc LIMIT 10")
spark.sql("CREATE TEMPORARY VIEW v2 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2017 group by Carrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT distinct ac.AirlineName, v1.Carrier, v1.NumFlights, v2.NumFlights from v1 INNER JOIN v2 ON v1.Carrier = v2.Carrier INNER JOIN AirlineCodes ac ON v2.Carrier = ac.AirlineCode WHERE v1.NumFlights > v2.NumFlights")
#show the top row without truncation
output.show(10, False)

#display for visual analysis
display(output)
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse e tutte le risorse correlate. A questo scopo, selezionare il gruppo di risorse per l'account di archiviazione e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

[!div class="nextstepaction"] 
> [Estrarre, trasformare e caricare dati usando Apache Hive in Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)

