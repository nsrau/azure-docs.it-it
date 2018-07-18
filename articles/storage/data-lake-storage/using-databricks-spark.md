---
title: Accedere ai dati della versione di anteprima di Azure Data Lake Storage Gen2 con Databricks usando Spark | Microsoft Docs
description: Illustra come eseguire query Spark in un cluster Databricks per accedere ai dati in un account di archiviazione di Azure Data Lake Storage Gen2.
services: hdinsight,storage
tags: azure-portal
author: dineshm
manager: twooley
ms.component: data-lake-storage-gen2
ms.service: hdinsight
ms.workload: big-data
ms.topic: tutorial
ms.date: 6/27/2018
ms.author: dineshm
ms.openlocfilehash: 27ed860c7dd3b979a25860d453231de74d3f46be
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096917"
---
# <a name="tutorial-access-azure-data-lake-storage-gen2-preview-data-with-databricks-using-spark"></a>Esercitazione: Accedere ai dati della versione di anteprima di Azure Data Lake Storage Gen2 con Databricks usando Spark

In questa esercitazione si apprenderà come eseguire query Spark in un cluster Databricks per eseguire query sui dati in un account con supporto della versione di anteprima di Azure Data Lake Storage Gen2.

> [!div class="checklist"]
> * Creare un cluster Databricks
> * Inserire dati non strutturati in un account di archiviazione
> * Attivare una funzione di Azure per elaborare i dati
> * Esecuzione di analisi sui dati nell'archiviazione BLOB

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione illustra come utilizzare ed eseguire query sui dati relativi ai voli di una compagnia aerea, resi disponibili dal [Dipartimento dei trasporti degli Stati Uniti](https://transtats.bts.gov/Tables.asp?DB_ID=120&DB_Name=Airline%20On-Time%20Performance%20Data&DB_Short_Name=On-Time). Scaricare almeno due anni di dati sui voli (selezionando tutti i campi) e salvare il risultato nel computer in uso. Assicurarsi di prendere nota del nome file e del percorso del download, perché queste informazioni saranno necessarie in un passaggio successivo.

> [!NOTE]
> Fare clic sulla casella di controllo **Prezipped file** (File precompresso) per selezionare tutti i campi dati. Le dimensioni del download saranno di molti gigabyte, ma questa quantità di dati è necessaria per l'analisi.

## <a name="create-an-azure-data-lake-storage-gen2-account"></a>Creare un account di Azure Data Lake Storage Gen2

Per iniziare, creare un nuovo [account di Azure Data Lake Storage Gen2](quickstart-create-account.md) e assegnargli un nome univoco. Passare quindi all'account di archiviazione per recuperare le impostazioni di configurazione.

> [!IMPORTANT]
> Durante l'anteprima, Funzioni di Azure può essere usato solo con gli account di Azure Data Lake Storage Gen2 creati con uno spazio dei nomi flat.

1. In **Impostazioni** fare clic su **Chiavi di accesso**.
3. Fare clic sul pulsante **Copia** accanto a **key1** per copiare il valore della chiave.

Per i passaggi successivi di questa esercitazione sono richiesti sia il nome dell'account che la chiave. Aprire un editor di testo e tenere da parte il nome dell'account e la chiave per riferimento futuro.

## <a name="create-a-databricks-cluster"></a>Creare un cluster Databricks

Il passaggio successivo consiste nel creare un [cluster Databricks](https://docs.azuredatabricks.net/) per creare un'area di lavoro di dati.

1. Creare un [servizio Databricks](https://ms.portal.azure.com/#create/Microsoft.Databricks) e denominarlo **myFlightDataService** (assicurarsi di selezionare la casella di controllo *Aggiungi al dashboard* durante la creazione del servizio).
2. Fare clic su **Avvia area di lavoro** per aprire l'area di lavoro in una nuova finestra del browser.
3. Fare clic su **Cluster** nella barra di spostamento a sinistra.
4. Fare clic su **Crea cluster**.
5. Immettere **myFlightDataCluster** nel campo *Nome cluster*.
6. Selezionare **Standard_D8s_v3** nel campo *Tipo di ruolo di lavoro*.
7. Modificare **Min Workers** (Ruoli di lavoro min) impostandolo su *4*.
8. Fare clic su **Crea cluster** nella parte superiore della pagina (questo processo potrebbe richiedere fino a 5 minuti).
9. Al termine del processo, selezionare **Azure Databricks** in alto a sinistra nella barra di spostamento.
10. Selezionare **Notebook** nella sezione **Nuovo** nella parte inferiore della pagina.
11. Immettere un nome di propria scelta nel campo **Nome** e selezionare **Python** come linguaggio.
12. In tutti gli altri campi possono essere lasciati i valori predefiniti.
13. Selezionare **Crea**.
14. Incollare il codice seguente nella cella **Cmd 1** e sostituire i valori con quelli dell'account di archiviazione in uso.

    ```bash
    spark.conf.set("fs.azure.account.key.<account_name>.dfs.core.windows.net", "<account_key>") 
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
    dbutils.fs.ls("abfs://<file_system>@<account_name>.dfs.core.windows.net/")
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
    ```

## <a name="ingest-data"></a>Inserire dati

### <a name="copy-source-data-into-the-storage-account"></a>Copiare i dati di origine nell'account di archiviazione

L'operazione successiva prevede l'uso di AzCopy per copiare i dati dal file con estensione *csv* in Archiviazione di Azure. Aprire una finestra del prompt dei comandi e immettere i comandi seguenti. Assicurarsi di sostituire i segnaposto `<DOWNLOAD_FILE_PATH>`, `<ACCOUNT_NAME>` e `<ACCOUNT_KEY>` con i valori corrispondenti annotati in un passaggio precedente.

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_FILE_PATH>" https://<ACCOUNT_NAME>.dfs.core.windows.net/dbricks/folder1/On_Time --recursive 
```

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Usare il notebook di Databricks per convertire il formato CSV in Parquet

Aprire nuovamente Databricks nel browser ed eseguire i passaggi seguenti:

1. Selezionare **Azure Databricks** in alto a sinistra nella barra di spostamento.
2. Selezionare **Notebook** nella sezione **Nuovo** nella parte inferiore della pagina.
3. Immettere **CSV2Parquet** nel campo **Nome**.
4. In tutti gli altri campi possono essere lasciati i valori predefiniti.
5. Selezionare **Crea**.
6. Incollare il codice seguente nella cella **Cmd 1** (questo codice viene salvato automaticamente nell'editor).

    ```python
    #mount Azure Blob Storage as an HDFS file system to your databricks cluster
    #you need to specify a storage account and container to connect to. 
    #use a SAS token or an account key to connect to Blob Storage.  
    accountname = "<insert account name>' 
    accountkey = " <insert account key>'
    fullname = "fs.azure.account.key." +accountname+ ".blob.core.windows.net"
    accountsource = "abfs://dbricks@" +accountname+ ".blob.core.windows.net/folder1"
    #create a dataframe to read data
    flightDF = spark.read.format('csv').options(header='true', inferschema='true').load(accountsource + "/On_Time_On_Time*.csv")
    #read the all the airline csv files and write the output to parquet format for easy query
    flightDF.write.mode("append").parquet(accountsource + '/parquet/flights')

    #read the flight details parquet file 
    #flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load(accountsource + "/parquet/flights")
    print("Done")
    ```

## <a name="explore-data-using-hadoop-distributed-file-system"></a>Esplorare i dati usando Hadoop Distributed File System

Tornare all'area di lavoro di Databricks e fare clic sull'icona **Recenti** nella barra di spostamento a sinistra.

1. Fare clic sul notebook **Flight Data Analytics** per l'analisi dei dati sui voli.
2. Premere **CTRL+ALT+N** per creare una nuova cella.

Immettere ognuno dei blocchi di codice seguenti in **Cmd 1** e premere **Cmd+INVIO** per eseguire lo script Python.

Per ottenere un elenco dei file con estensione csv caricati tramite AzCopy, eseguire lo script seguente:

```python
import os.path
import IPython
from pyspark.sql import SQLContext
source = "abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/"
dbutils.fs.ls(source + "/temp")
display(dbutils.fs.ls(source + "/temp/"))
```

Per creare un nuovo file ed elencare i file nella cartella *parquet/flights*, eseguire questo script:

```python
source = "abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/"

dbutils.fs.help()

dbutils.fs.put(source + "/temp/1.txt", "Hello, World!", True)
dbutils.fs.ls(source + "/temp/parquet/flights")
```
Con questi esempi di codice è stata esaminata la natura gerarchica di HDFS usando i dati archiviati in un account con supporto di Azure Data Lake Storage Gen2.

## <a name="query-the-data"></a>Eseguire query sui dati

È quindi possibile iniziare a eseguire query sui dati caricati in Azure Data Lake Storage. Immettere ognuno dei blocchi di codice seguenti in **Cmd 1** e premere **Cmd+INVIO** per eseguire lo script Python.

### <a name="simple-queries"></a>Query semplici

Per creare frame di dati per le origini dati, eseguire lo script seguente:

> [!IMPORTANT]
> Assicurarsi di sostituire il segnaposto **<YOUR_CSV_FILE_NAME>** con il nome del file scaricato all'inizio di questa esercitazione.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load(accountsource + "/<YOUR_CSV_FILE_NAME>.csv")
acDF.write.parquet(accountsource + '/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created via the Azure Function
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load(accountsource + "/parquet/flights")

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
#preferrably run this in a separate cmd cell
display(flightDF)
```

Per eseguire query di analisi sui dati, eseguire lo script seguente:

```python
#Run each of these queries, preferrably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet(accountsource + '/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet(accountsource + '/parquet/airlinecodes')
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
### <a name="complex-queries"></a>Query complesse

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

## <a name="next-steps"></a>Passaggi successivi

* [Estrarre, trasformare e caricare dati usando Apache Hive in Azure HDInsight](tutorial-extract-transform-load-hive.md)