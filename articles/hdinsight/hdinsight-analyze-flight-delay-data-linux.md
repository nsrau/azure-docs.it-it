---
title: 'Analizzare i dati sui ritardi dei voli con Hive in HDInsight: Azure | Microsoft Docs'
description: Informazioni su come usare Hive per analizzare i dati sui voli in HDInsight basato su Linux e su come esportare i dati nel database SQL usando Sqoop.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 0c23a079-981a-4079-b3f7-ad147b4609e5
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: f333354311b16c00a0d43a691f139f5f80383d1a
ms.contentlocale: it-it
ms.lasthandoff: 09/13/2017

---
# <a name="analyze-flight-delay-data-by-using-hive-on-linux-based-hdinsight"></a>Analizzare i dati sui ritardi dei voli con Hive in HDInsight basato su Linux

Informazioni su come analizzare i dati sui ritardi dei voli usando Hive in HDInsight basato su Linux e su come esportare i dati nel database SQL di Azure usando Sqoop.

> [!IMPORTANT]
> I passaggi descritti in questo documento richiedono un cluster HDInsight che usa Linux. Linux è l'unico sistema operativo usato in Azure HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Prerequisiti

* **Un cluster HDInsight**. Per la procedura sulla creazione di un nuovo cluster HDInsight basato su Linux, vedere [Introduzione all'uso di Hadoop in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

* **Database SQL di Azure**. Come archivio dati di destinazione usare un database SQL di Azure. Se non si dispone di un database SQL, vedere [Creare un database SQL di Azure nel portale di Azure](../sql-database/sql-database-get-started.md).

* **Interfaccia della riga di comando di Azure**. Se l'interfaccia della riga di comando di Azure non è stata installata, vedere [Installare l'interfaccia della riga di comando 1.0 di Azure](../cli-install-nodejs.md) per altre procedure.

## <a name="download-the-flight-data"></a>Scaricare i dati relativi ai voli

1. Passare alla pagina [Research and Innovative Technology Administration, Bureau of Transportation Statistics (RITA)][rita-website].

2. Selezionare i valori seguenti nella pagina:

   | Nome | Valore |
   | --- | --- |
   | Filter Year |2013 |
   | Filter Period |January |
   | Fields |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   Deselezionare tutti gli altri campi. 

3. Selezionare **Download**.

## <a name="upload-the-data"></a>Caricare i dati

1. Usare il comando seguente per caricare il file con estensione zip nel nodo head del cluster HDInsight:

    ```
    scp FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

    Sostituire *FILENAME* con il nome del file con estensione zip. Sostituire *USERNAME* con l'account di accesso SSH per il cluster HDInsight. Sostituire *CLUSTERNAME* con il nome del cluster HDInsight.

   > [!NOTE]
   > Se è stata usata una password per l'autenticazione a SSH, viene richiesto di specificarla. Se è stata usata una chiave pubblica, può essere necessario usare il parametro `-i` e specificare il percorso alla chiave privata corrispondente. ad esempio `scp -i ~/.ssh/id_rsa FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Al termine del caricamento connettersi al cluster tramite SSH:

    ```ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net```

    Per altre informazioni, vedere [Connettersi a HDInsight (Hadoop) con SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Usare il comando seguente per decomprimere il file con estensione zip:

    ```
    unzip FILENAME.zip
    ```

    Questo comando estrae un file con estensione CSV di circa 60 MB.

4. Usare il comando seguente per creare una directory nell'archivio di HDInsight e quindi copiare il file nella directory:

    ```
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="create-and-run-the-hiveql"></a>Creare ed eseguire HiveQL

Usare la procedura seguente per importare dati dal file con estensione csv in una tabella Hive denominata **delays**.

1. Usare il comando seguente per creare e modificare un nuovo file denominato **flightdelays.hql**:

    ```
    nano flightdelays.hql
    ```

    Usare il testo seguente come contenuto del file:

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

2. Per salvare il file, usare CTRL + X e quindi premere Y.

3. Per avviare Hive ed eseguire il file **flightdelays.hql**, usare il comando seguente:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

   > [!NOTE]
   > In questo esempio viene usato `localhost` dal momento che si è connessi al nodo head del cluster HDInsight, in cui HiveServer2 è in esecuzione.

4. Al termine dell'esecuzione dello script __flightdelays.hql__ usare il comando seguente per aprire una sessione Beeline interattiva:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

5. Quando si riceve il prompt di `jdbc:hive2://localhost:10001/>`, usare la query seguente per recuperare i dati dai dati sui ritardi dei voli importati:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    Grazie a questa query vengono recuperati un elenco di città in cui si sono verificati ritardi meteo e il tempo di ritardo medio che può essere salvato in `/tutorials/flightdelays/output`. Sqoop leggerà in seguito i dati da questo percorso e li esporterà nel database SQL di Azure.

6. Per uscire da Beeline, immettere `!quit` al prompt dei comandi.

## <a name="create-a-sql-database"></a>Creazione di un database SQL

Se si dispone già di un database SQL, è necessario ottenere il nome del server. È possibile trovare il nome del server nel [portale di Azure](https://portal.azure.com) selezionando **Database SQL** e quindi filtrando il nome del database che si desidera usare. Il nome del server è elencato nella colonna **SERVER** .

Se non si dispone già di un database SQL, vedere le informazioni in [Creare un database SQL di Azure nel portale di Azure](../sql-database/sql-database-get-started.md) per crearne uno. Salvare il nome del server usato per il database.

## <a name="create-a-sql-database-table"></a>Creare una tabella del database SQL

> [!NOTE]
> Sono disponibili diversi modi per connettersi al database SQL per creare una tabella. Nei seguenti passaggi viene usato [FreeTDS](http://www.freetds.org/) dal cluster HDInsight.


1. Per connettersi al cluster HDInsight basato su Linux, usare SSH ed eseguire la procedura seguente dalla sessione di SSH.

2. Immettere il comando seguente per installare FreeTDS:

    ```
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

3. Al termine dell'installazione usare il comando seguente per connettersi al server del database SQL. Sostituire **serverName** con il nome server del database SQL. Sostituire **adminLogin** e **adminPassword** con le credenziali di accesso per il database SQL. Sostituire **databaseName** con il nome del database.

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>
    ```

    L'output che si riceve è simile al testo seguente:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to sqooptest
    1>
    ```

4. Al prompt di `1>` , immettere il codice seguente:

    ```
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
    ([origin_city_name] ASC))
    GO
    ```

    Dopo aver immesso l'istruzione `GO`, vengono valutate le istruzioni precedenti. Questa query crea una tabella denominata **delays**, con un indice cluster.

    Per verificare la corretta creazione della tabella, usare la query seguente:

    ```
    SELECT * FROM information_schema.tables
    GO
    ```

    L'output è simile al testo seguente:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo     delays      BASE TABLE
    ```

5. Per uscire dall'utilità tsql, immettere `exit` al prompt di `1>`.

## <a name="export-data-with-sqoop"></a>Esportare i dati con Sqoop

1. Usare il comando seguente per verificare che Sqoop possa visualizzare il database SQL:

    ```
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>
    ```

    Questo comando restituisce un elenco di database, compreso il database in cui è stata creata in precedenza la tabella delays.

2. Usare il comando seguente per esportare i dati dalla tabella hivesampletable alla tabella delays:

    ```
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop si connette al database contenente la tabella delays ed esporta i dati dalla directory `/tutorials/flightdelays/output` nella tabella delays.

3. Al termine dell'esecuzione del comando sqoop, usare l' utilità Transact-SQL per connettersi al database:

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>
    ```

    Usare le istruzioni seguenti per verificare che i dati siano stati esportati nella tabella delays:

    ```
    SELECT * FROM delays
    GO
    ```

    Dovrebbe essere visualizzato un elenco di dati della tabella. Digitare `exit` per uscire dall'utilità tsql.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare i dati in HDInsight, vedere gli articoli seguenti:

* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Usare Oozie con HDInsight][hdinsight-use-oozie]
* [Usare Sqoop con Hadoop in HDInsight][hdinsight-use-sqoop]
* [Usare Pig con HDInsight][hdinsight-use-pig]
* [Sviluppare programmi Java MapReduce per Hadoop in HDInsight][hdinsight-develop-mapreduce]
* [Sviluppare programmi MapReduce per la creazione di flussi Python per HDInsight][hdinsight-develop-streaming]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

