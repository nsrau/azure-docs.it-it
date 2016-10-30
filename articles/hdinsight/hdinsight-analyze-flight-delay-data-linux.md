<properties 
    pageTitle="Analizzare i dati sui ritardi dei voli con Hive usando HDInsight basato su Linux | Microsoft Azure" 
    description="Informazioni su come utilizzare Hive per analizzare i dati sui voli in HDInsight basato su Linux, e su come esportare i dati al database SQL mediante Sqoop." 
    services="hdinsight" 
    documentationCenter="" 
    authors="Blackmist" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016" 
    ms.author="larryfr"/>


#<a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>Analizzare i dati sui ritardi dei voli con Hive in HDInsight

Informazioni su come analizzare i dati sui ritardi dei voli usando Hive in HDInsight basato su Linux, quindi su come esportare i dati al database SQL di Azure mediante Sqoop.

> [AZURE.NOTE] Anche se alcune parti di questo articolo possono essere usate con i cluster HDInsight basati su Windows (Python e Hive, ad esempio), molti passaggi in questo documento sono specifici dei cluster basati su Linux. Per i passaggi relativi a un cluster basato su Windows, vedere [Analizzare i dati sui ritardi dei voli con Hive in HDInsight](hdinsight-analyze-flight-delay-data.md)

###<a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Un cluster HDInsight__. Per la procedura sulla creazione di un nuovo cluster HDInsight basato su Linux, vedere [Introduzione all'uso di Hadoop con Hive in HDInsight in Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

- Un __database SQL di Azure__. Come archivio dati di destinazione verrà usato un database SQL di Azure. Se non si ha già un database SQL, vedere l' [esercitazione sul database SQL per creare un database SQL in pochi minuti](../sql-database/sql-database-get-started.md).

- L'__interfaccia della riga di comando di Azure__. Se l'interfaccia della riga di comando di Azure non è installata, vedere l'articolo relativo all' [installazione e configurazione dell'interfaccia della riga di comando di Azure](../xplat-cli-install.md) per altri passaggi.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]


##<a name="download-the-flight-data"></a>Scaricare i dati relativi ai voli

1. Passare alla pagina [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website].
2. Selezionare i valori seguenti nella pagina:

  	| Nome | Valore |
  	| ---- | ---- |
  	| Filter Year | 2013 |
  	| Filter Period | January |
  	| Fields | Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. Deselezionare tutti gli altri campi |

3. Fare clic su **Download**. 

##<a name="upload-the-data"></a>Caricare i dati

1. Usare il comando seguente per caricare il file con estensione zip nel nodo head del cluster HDInsight:

        scp FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

    Sostituire __FILENAME__ con il nome del file con estensione zip. Sostituire __USERNAME__ con l'account di accesso SSH per il cluster HDInsight. Sostituire CLUSTERNAME con il nome del cluster HDInsight.
    
    > [AZURE.NOTE] Se è stata usata una password per l'autenticazione a SSH, verrà richiesto di specificarla. Se è stata usata una chiave pubblica, può essere necessario usare il parametro `-i` e specificare il percorso alla chiave privata corrispondente. Ad esempio, `scp -i ~/.ssh/id_rsa FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Una volta completato il caricamento, connettersi al cluster tramite SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
        
    Per altre informazioni sull'uso di SSH con HDInsight basato su Linux, vedere gli articoli seguenti:
    
    * [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
    
3. Dopo la connessione, usare il comando seguente per decomprimere il file con estensione zip:

        unzip FILENAME.zip
    
    Verrà estratto un file con estensione csv con una dimensione di circa 60 MB.
    
4. Usare il comando seguente per creare una nuova directory in WASB (l'archivio dati distribuito usato da HDInsight) e copiare il file:

    hdfs dfs -mkdir -p /tutorials/flightdelays/data  hdfs dfs -put FILENAME.csv /tutorials/flightdelays/data/
    
##<a name="create-and-run-the-hiveql"></a>Creare ed eseguire HiveQL

Usare la procedura seguente per importare dati dal file con estensione csv in una tabella Hive denominata __Delays__.

1. Usare il comando seguente per creare e modificare un nuovo file denominato __flightdelays.hql__:

        nano flightdelays.hql
        
    Usare il codice seguente come contenuto di questo file:
    
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
        
2. Usare __Ctrl + X__, quindi premere __Y__ per salvare il file.

3. Usare il comando seguente per avviare Hive ed eseguire il file __flightdelays.hql__ :

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -f flightdelays.hql
        
    > [AZURE.NOTE] In questo esempio viene usato `localhost` dal momento che si è connessi al nodo head del cluster HDInsight, in cui HiveServer2 è in esecuzione.

4. Usare il comando seguente per aprire una sessione Beeline interattiva:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

5. Quando si riceve il prompt di `jdbc:hive2://localhost:10001/>` , usare il comando seguente per recuperare i dati dai dati sui ritardi dei voli importati.

        INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        SELECT regexp_replace(origin_city_name, '''', ''),
            avg(weather_delay)
        FROM delays
        WHERE weather_delay IS NOT NULL
        GROUP BY origin_city_name;

    Viene recuperato un elenco di città in cui si sono verificati ritardi meteo e il tempo di ritardo medio che può essere salvarlo in `/tutorials/flightdelays/output`. Sqoop leggerà in seguito i dati da questo percorso e li esporterà nel database SQL di Azure.

6. Per uscire da Beeline, immettere `!quit` al prompt dei comandi.

## <a name="create-a-sql-database"></a>Creare un database SQL

Se si dispone già di un database SQL, è necessario ottenere il nome del server. È possibile trovarlo nel [portale di Azure](https://portal.azure.com) selezionando __Database SQL__e quindi filtrando il nome del database che si desidera usare. Il nome del server è elencato nella colonna __SERVER__ .

Se non si dispone già di un database SQL, vedere le informazioni in [Esercitazione sul database SQL: Creare un database SQL in pochi minuti usando il portale di Azure](../sql-database/sql-database-get-started.md) per crearne uno. È necessario salvare il nome del server usato per il database.

##<a name="create-a-sql-database-table"></a>Creare una tabella del database SQL

> [AZURE.NOTE] Sono disponibili diversi modi per connettersi al database SQL per creare una tabella. Nei seguenti passaggi viene usato [FreeTDS](http://www.freetds.org/) dal cluster HDInsight.

1. Per connettersi al cluster HDInsight basato su Linux, usare SSH ed eseguire la procedura seguente dalla sessione di SSH.

3. Immettere il comando seguente per installare FreeTDS:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. Dopo aver installato FreeTDS, usare il comando seguente per connettersi al server del database SQL. Sostituire __serverName__ con il nome server del database SQL. Sostituire __adminLogin__ e __adminPassword__ con le credenziali di accesso per il database SQL. Sostituire __databaseName__ con il nome del database.

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>

    L'output sarà simile al seguente:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. Al prompt di `1>` , immettere il codice seguente:

        CREATE TABLE [dbo].[delays](
        [origin_city_name] [nvarchar](50) NOT NULL,
        [weather_delay] float,
        CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
        ([origin_city_name] ASC))
        GO

    Dopo aver immesso l'istruzione `GO` , verranno valutate le istruzioni precedenti. Si creerà una nuova tabella denominata __delays__con un indice cluster (obbligatorio del database SQL).

    Per verificare la corretta creazione della tabella, usare il comando seguente:

        SELECT * FROM information_schema.tables
        GO

    L'output dovrebbe essere simile al seguente:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        databaseName       dbo     delays      BASE TABLE

8. Per uscire dall'utilità psql, immettere `exit` at the `1>` .
    
##<a name="export-data-with-sqoop"></a>Esportare i dati con Sqoop

2. Usare il comando seguente per verificare che Sqoop possa visualizzare il database SQL:

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Dovrebbe essere visualizzato un elenco di database, compreso il database in cui è stata creata in precedenza la tabella delays.

3. Usare il comando seguente per esportare dati dalla tabella hivesampletable alla tabella mobiledata:

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir 'wasbs:///tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1

    In questo modo, si indicherà a Sqoop di connettersi al database SQL, al database contenente la tabella delays e di esportare dati da wasbs:///tutorials/flightdelays/output, dove in precedenza è stato archiviato l'output della query hive, alla tabella delays.

4. Al termine dell'esecuzione del comando, usare le informazioni seguenti per connettersi al database tramite TSQL:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Sopo la connessione, usare le istruzioni seguenti per verificare che i dati siano stati esportati nella tabella mobiledata:
    
        SELECT * FROM delays
        GO

    Dovrebbe essere visualizzato un elenco di dati della tabella. Digitare `exit` per uscire dall'utilità tsql.

##<a name="<a-id="nextsteps"></a>-next-steps"></a><a id="nextsteps"></a> Passaggi successivi

È stato illustrato come caricare file nell'archivio BLOB di Azure, come popolare una tabella Hive con i dati disponibili nell'archivio BLOB di Azure, come eseguire query Hive e come usare Sqoop per esportare i dati da HDFS in un database SQL di Azure. Per altre informazioni, vedere gli articoli seguenti:

* [Introduzione all'uso di HDInsight][hdinsight-get-started]
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Usare Oozie con HDInsight][hdinsight-use-oozie]
* [Usare Sqoop con HDInsight][hdinsight-use-sqoop]
* [Usare Pig con HDInsight][hdinsight-use-pig]
* [Sviluppare programmi MapReduce Java per HDInsight][hdinsight-develop-mapreduce]
* [Sviluppare programmi per la creazione di flussi Python Hadoop per HDInsight][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx


 



<!--HONumber=Oct16_HO2-->


