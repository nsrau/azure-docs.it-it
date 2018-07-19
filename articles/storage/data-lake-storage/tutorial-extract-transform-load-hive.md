---
title: 'Esercitazione: Eseguire operazioni di estrazione, trasformazione e caricamento (ETL) usando Hive in HDInsight - Azure'
description: Informazioni su come estrarre dati da un set di dati non elaborati in formato CSV, trasformarli usando Hive in HDInsight e quindi caricare i dati trasformati nel database SQL di Azure tramite Sqoop.
services: storage
documentationcenter: ''
author: jamesbak
manager: jahogg
tags: azure-portal
ms.component: data-lake-storage-gen2
ms.service: storage
ms.devlang: na
ms.topic: tutorial
ms.date: 06/27/2018
ms.author: jamesbak
ms.custom: H1Hack27Feb2017,hdinsightactive,mvc
ms.openlocfilehash: 4f71a4e0570695abbe580ffc16b3361d5cd71ac1
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136487"
---
# <a name="tutorial-extract-transform-and-load-data-using-apache-hive-on-azure-hdinsight"></a>Esercitazione: Estrarre, trasformare e caricare dati usando Apache Hive in Azure HDInsight

In questa esercitazione si importa un file CSV di dati non elaborati in un cluster HDInsight e quindi si trasformano i dati usando Apache Hive in Azure HDInsight. Dopo averli trasformati, i dati vengono caricati in un database SQL di Azure tramite Apache Sqoop. Questo articolo usa dati pubblicamente disponibili sui voli.

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Scaricare i dati di esempio sui voli
> * Caricare i dati in un cluster HDInsight
> * Trasformare i dati usando Hive
> * Creare una tabella nel database SQL di Azure
> * Usare Sqoop per esportare i dati nel database SQL di Azure


L'illustrazione seguente mostra un flusso tipico dell'applicazione ETL.

![Operazione ETL con Apache Hive in Azure HDInsight](./media/tutorial-extract-transform-load-hive/hdinsight-etl-architecture.png "Operazione ETL con Apache Hive in Azure HDInsight")

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* **Un cluster Hadoop basato su Linux in HDInsight**. Per i passaggi della creazione di un nuovo cluster HDInsight basato su Linux, vedere [Configurare i cluster di HDInsight con Hadoop, Spark, Kafka e altro ancora](./quickstart-create-connect-hdi-cluster.md).

* **Database SQL di Azure**. Come archivio dati di destinazione usare un database SQL di Azure. Se non si ha un database SQL, vedere [Creare un database SQL di Azure nel portale di Azure](../../sql-database/sql-database-get-started.md).

* **Interfaccia della riga di comando di Azure 2.0**. Se l'interfaccia della riga di comando di Azure non è stata installata, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) per altre procedure.

* **Un client SSH**. Per altre informazioni, vedere [Connettersi a HDInsight (Hadoop) con SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> I passaggi descritti in questo documento richiedono un cluster HDInsight che usa Linux. Linux è l'unico sistema operativo usato in Azure HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="download-the-flight-data"></a>Scaricare i dati relativi ai voli

1. Passare alla pagina [Research and Innovative Technology Administration, Bureau of Transportation Statistics (RITA)][rita-website].

2. Selezionare i valori seguenti nella pagina:

   | Nome | Valore |
   | --- | --- |
   | Filter Year |2013 |
   | Filter Period |January |
   | Campi |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   Deselezionare tutti gli altri campi

3. Selezionare **Download**. Si otterrà un file con estensione zip con i campi dati selezionati.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Caricare i dati in un cluster HDInsight

Esistono diversi modi per caricare i dati nell'archivio associato a un cluster HDInsight. In questa sezione si usa `scp` per caricare i dati. Per altre informazioni su altri modi per caricare i dati, vedere [Usare Distcp per copiare dati tra i BLOB del servizio di archiviazione di Azure e l'anteprima di Data Lake Storage Gen2](use-distcp.md).

1. Aprire un prompt dei comandi e usare il comando seguente per caricare il file con estensione zip nel nodo head del cluster HDInsight:

    ```bash
    scp <FILE_NAME>.zip <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net:<FILE_NAME.zip>
    ```

    Sostituire *FILE_NAME* con il nome del file con estensione zip. Sostituire *SSH_USER_NAME* con l'account di accesso SSH per il cluster HDInsight. Sostituire *CLUSTER_NAME* con il nome del cluster HDInsight.

   > [!NOTE]
   > Se si usa una password per l'autenticazione in SSH, viene richiesto di specificarla. Se si usa una chiave pubblica, può essere necessario usare il parametro `-i` e specificare il percorso alla chiave privata corrispondente. Ad esempio, `scp -i ~/.ssh/id_rsa FILE_NAME.zip USER_NAME@CLUSTER_NAME-ssh.azurehdinsight.net:`.

2. Al termine del caricamento connettersi al cluster tramite SSH. Nel prompt dei comandi immettere il comando seguente:

    ```bash
    ssh <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net
    ```

3. Usare il comando seguente per decomprimere il file con estensione zip:

    ```bash
    unzip <FILE_NAME>.zip
    ```

    Questo comando estrae un file con estensione *csv* di circa 60 MB.

4. Usare i comandi seguenti per creare una directory e copiare il file con estensione *csv* all'interno di questa:

    ```bash
    hdfs dfs -mkdir -p abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data
    hdfs dfs -put <FILE_NAME>.csv abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data/
    ```

5. Creare il file system di Data Lake Storage Gen2.

    ```bash
    hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/
    ```

## <a name="transform-data-using-a-hive-query"></a>Trasformare i dati usando una query Hive

Esistono diversi modi per eseguire un processo Hive in un cluster HDInsight. In questa sezione si usa Beeline per eseguire un processo Hive. Per informazioni su altri metodi di esecuzione di un processo Hive, vedere [Usare Hive in HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md).

Come parte del processo Hive, importare i dati contenuti nel file con estensione csv in una tabella Hive denominata **Delays**.

1. Dal prompt SSH già disponibile per il cluster HDInsight, usare il comando seguente per creare e modificare un nuovo file denominato **flightdelays.hql**:

    ```bash
    nano flightdelays.hql
    ```

2. Usare il testo seguente come contenuto del file:

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
    LOCATION 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/processed
    AS
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

2. Per salvare il file, premere **ESC** e quindi immettere `:x`.

3. Per avviare Hive ed eseguire il file **flightdelays.hql**, usare il comando seguente:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

4. Al termine dell'esecuzione dello script __flightdelays.hql__ usare il comando seguente per aprire una sessione Beeline interattiva:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

5. Quando si riceve il prompt `jdbc:hive2://localhost:10001/>`, usare la query seguente per recuperare i dati dai dati sui ritardi dei voli importati:

    ```hiveql
    INSERT OVERWRITE DIRECTORY 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    Grazie a questa query vengono recuperati un elenco di città in cui si sono verificati ritardi meteo e il tempo di ritardo medio che può essere salvato in `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output`. Sqoop leggerà in seguito i dati da questo percorso e li esporterà nel database SQL di Azure.

6. Per uscire da Beeline, immettere `!quit` al prompt dei comandi.

## <a name="create-a-sql-database-table"></a>Creare una tabella del database SQL

Questa sezione presuppone che sia già stato creato un database SQL di Azure. Se non si ha un database SQL, usare le informazioni disponibili in [Creare un database SQL di Azure nel portale di Azure](../../sql-database/sql-database-get-started.md) per crearne uno.

Se si ha già un database SQL, è necessario ottenere il nome del server. È possibile trovare il nome del server nel [portale di Azure](https://portal.azure.com) selezionando **Database SQL** e quindi filtrando il nome del database che si intende usare. Il nome del server è indicato nella colonna **Nome server**.

![Ottenere i dettagli del server SQL di Azure](./media/tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Ottenere i dettagli del server SQL di Azure")

> [!NOTE]
> Sono disponibili diversi modi per connettersi al database SQL per creare una tabella. Nei seguenti passaggi viene usato [FreeTDS](http://www.freetds.org/) dal cluster HDInsight.


1. Per installare FreeTDS, usare il comando seguente da una connessione SSH al cluster:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

3. Al termine dell'installazione usare il comando seguente per connettersi al server del database SQL. Sostituire **serverName** con il nome server del database SQL. Sostituire **adminLogin** e **adminPassword** con le credenziali di accesso per il database SQL. Sostituire **databaseName** con il nome del database.

    ```bash
    TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -p 1433 -D <DATABASE_NAME>
    ```

    Quando richiesto, immettere la password per l'account di accesso amministratore al database SQL.

    L'output che si riceve è simile al testo seguente:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to sqooptest
    1>
    ```

4. Al prompt `1>`, immettere il codice seguente:

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
    ([origin_city_name] ASC))
    GO
    ```

    Dopo aver immesso l'istruzione `GO`, vengono valutate le istruzioni precedenti. Questa query crea una tabella denominata **delays**, con un indice cluster.

    Per verificare che la tabella sia stata creata, usare la query seguente:

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    L'output è simile al testo seguente:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

5. Per uscire dall'utilità tsql, immettere `exit` al prompt `1>`.


## <a name="export-data-to-sql-database-using-sqoop"></a>Esportare i dati nel database SQL tramite Sqoop

Nelle sezioni precedenti i dati trasformati sono stati copiati nel percorso `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output`. In questa sezione viene usato Sqoop per esportare i dati da `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output` alla tabella creata nel database SQL di Azure. 

1. Usare il comando seguente per verificare che Sqoop possa visualizzare il database SQL:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
    ```

    Questo comando restituisce un elenco di database, compreso il database in cui è stata creata in precedenza la tabella delays.

2. Usare il comando seguente per esportare i dati dalla tabella hivesampletable alla tabella delays:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<FILE_SYSTEM_NAME>@.dfs.core.windows.net/tutorials/flightdelays/output' 
    --fields-terminated-by '\t' -m 1
    ```

    Sqoop si connette al database contenente la tabella delays ed esporta i dati dalla directory `/tutorials/flightdelays/output` nella tabella delays.

3. Al termine dell'esecuzione del comando sqoop, usare l'utilità tsql per connettersi al database:

    ```bash
    TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
    ```

    Usare le istruzioni seguenti per verificare che i dati siano stati esportati nella tabella delays:

    ```sql
    SELECT * FROM delays
    GO
    ```

    Dovrebbe essere visualizzato un elenco di dati della tabella. La tabella include il nome della città e il tempo di ritardo medio dei voli per la città. 

    Digitare `exit` per uscire dall'utilità tsql.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare i dati in HDInsight, vedere gli articoli seguenti:

* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Usare Pig con HDInsight][hdinsight-use-pig]
* [Sviluppare programmi Java MapReduce per Hadoop in HDInsight][hdinsight-develop-mapreduce]
* [Sviluppare programmi MapReduce per la creazione di flussi Python per HDInsight][hdinsight-develop-streaming]
* [Usare Oozie con HDInsight][hdinsight-use-oozie]
* [Usare Sqoop con Hadoop in HDInsight][hdinsight-use-sqoop]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: ../../hdinsight/hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]:../../hdinsight/hadoop/hdinsight-use-hive.md
[hdinsight-provision]: ../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: ../../hdinsight/hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: ../../hdinsight/hdinsight-upload-data.md
[hdinsight-get-started]: ../../hdinsight/hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]:../../hdinsight/hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-use-pig]:../../hdinsight/hadoop/hdinsight-use-pig.md
[hdinsight-develop-streaming]:../../hdinsight/hadoop/apache-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]:../../hdinsight/hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
