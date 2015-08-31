<properties 
	pageTitle="Analizzare i dati sui ritardi dei voli usando HDInsight | Microsoft Azure" 
	description="Informazioni su come usare uno script di Windows PowerShell per effettuare il provisioning di un cluster HDInsight, eseguire un processo Hive, eseguire un processo Sqoop ed eliminare il cluster." 
	services="hdinsight" 
	documentationCenter="" 
	authors="Blackmist" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="larryfr"/>

#Analizzare i dati sui ritardi dei voli con Hive in HDInsight

Informazioni su come analizzare i dati sui ritardi dei voli usando Hive in HDInsight basato su Linux (anteprima), quindi su come esportare i dati al database SQL di Azure mediante Sqoop.

> [AZURE.NOTE]Anche se alcune parti di questo articolo possono essere usate con i cluster HDInsight basati su Windows (Python e Hive, ad esempio), molti passaggi in questo documento sono specifici dei cluster basati su Linux. Per i passaggi relativi a un cluster basato su Windows, vedere [Analizzare i dati sui ritardi dei voli con Hive in HDInsight](hdinsight-analyze-flight-delay-data.md).

###Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- Una **sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Un cluster HDInsight__. Per la procedura sulla creazione di un nuovo cluster HDInsight basato su Linux, vedere [Introduzione all'uso di Hadoop con Hive in HDInsight in Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

- Un __database SQL di Azure__. Come archivio dati di destinazione verrà usato un database SQL di Azure. Se non è ancora disponibile un database SQL, vedere l'articolo relativo alla [creazione e configurazione di un database SQL di Azure](../sql-database/sql-database-create-configure.md) per crearne uno.

- L'__interfaccia della riga di comando di Azure__. Se l'interfaccia della riga di comando di Azure non è installata, vedere l'articolo relativo all'[installazione e configurazione dell'interfaccia della riga di comando di Azure](../xplat-cli.md) per altri passaggi.


##Scaricare i dati relativi ai voli

1. Passare alla pagina [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website] (RITA).
2. Selezionare i valori seguenti nella pagina:

	| Name | Value | | Filter Year | 2013 | | Filter Period | January | | Fields | Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. Deselezionare tutti gli altri campi.

3. Fare clic su **Download**.

##Caricare i dati

1. Usare il comando seguente per caricare il file con estensione zip nel nodo head del cluster HDInsight:

		scp FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

	Sostituire __FILENAME__ con il nome del file con estensione zip. Sostituire __USERNAME__ con l'account di accesso SSH per il cluster HDInsight. Sostituire CLUSTERNAME con il nome del cluster HDInsight.
	
	> [AZURE.NOTE]Se è stata usata una password per l'autenticazione a SSH, verrà richiesto di specificarla. Se è stata usata una chiave pubblica, può essere necessario usare il parametro `-i` e specificare il percorso alla chiave privata corrispondente. Ad esempio, `scp -i ~/.ssh/id_rsa FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Una volta completato il caricamento, connettersi al cluster tramite SSH:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	Per altre informazioni sull'uso di SSH con HDInsight basato su Linux, vedere gli articoli seguenti:
	
	* [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows)
	
3. Dopo la connessione, usare il comando seguente per decomprimere il file con estensione zip:

		unzip FILENAME.zip
	
	Verrà estratto un file con estensione csv con una dimensione di circa 60 MB.
	
4. Usare il comando seguente per creare una nuova directory in WASB (l'archivio dati distribuito usato da HDInsight) e copiare il file:

	hadoop fs -mkdir -p /tutorials/flightdelays/data hadoop fs -copyFromLocal FILENAME.csv /tutorials/flightdelays/data/FILENAME.csv
	
##Creare ed eseguire HiveQL

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
		
2. Usare __CTRL+X__, quindi __S__ per salvare il file.

3. Usare il comando seguente per avviare Hive ed eseguire il file __flightdelays.hql__:

		hive -i flightdelays.hql
		
	In questo modo, verrà eseguito il file, quindi viene restituito un prompt di `hive>`.

4. Quando si riceve il prompt di `hive>`, usare il comando seguente per recuperare i dati dai dati sui ritardi dei voli importati.

		INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
		SELECT regexp_replace(origin_city_name, '''', ''),
			avg(weather_delay)
		FROM delays
		WHERE weather_delay IS NOT NULL
		GROUP BY origin_city_name;

	Viene recuperato un elenco di città in cui si sono verificati ritardi meteo e il tempo di ritardo medio che può essere salvarlo in `/tutorials/flightdelays/output`. Sqoop leggerà in seguito i dati da questo percorso e li esporterà nel database SQL di Azure.

##Creare un database SQL

Per creare un database SQL di Azure, seguire questa procedura. Il database contiene i dati esportati da HDInsight tramite Sqoop.

1. Dall'ambiente di sviluppo in cui è installata l'interfaccia della riga di comando di Azure, usare il comando seguente per creare un nuovo database SQL di Azure:

		azure sql server create <adminLogin> <adminPassword> <region>

	Ad esempio, `azure sql server create admin password "West US"`.

	Quando il comando viene completato, si riceverà una risposta simile alla seguente:

		info:    Executing command sql server create
		+ Creating SQL Server
		data:    Server Name i1qwc540ts
		info:    sql server create command OK

> [AZURE.IMPORTANT]Si noti il nome del server restituito da questo comando: è il nome breve del server di database SQL che è stato creato. Il nome di dominio completo (FQDN) è `<shortname>.database.windows.net`.

2. Usare il comando seguente per creare un database denominato **sqooptest** nel server di database SQL:

        azure sql db create [options] <serverName> sqooptest <adminLogin> <adminPassword>

    Quando viene completato il comando, verrà restituito un messaggio di conferma positiva.

	> [AZURE.NOTE]se viene visualizzato un errore che indica che non si dispone dell'accesso, potrebbe essere necessario aggiungere l'indirizzo IP della workstation client per il firewall del database SQL utilizzando il seguente:
	>
	> `sql firewallrule create [options] <serverName> <ruleName> <startIPAddress> <endIPAddress>`

##Creare una tabella del database SQL

> [AZURE.NOTE]Sono disponibili diversi modi per connettersi al database SQL per creare una tabella. Nei seguenti passaggi viene usato [FreeTDS](http://www.freetds.org/) dal cluster HDInsight.

1. Per connettersi al cluster HDInsight basato su Linux, usare SSH ed eseguire la procedura seguente dalla sessione di SSH.

3. Immettere il comando seguente per installare FreeTDS:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. Dopo aver installato  FreeTDS, utilizzare il comando seguente per connettersi al server di database SQL creato in precedenza:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    L'output sarà simile al seguente:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. Al prompt di `1>`, immettere il codice seguente:

        CREATE TABLE [dbo].[delays](
		[origin_city_name] [nvarchar](50) NOT NULL,
		[weather_delay] float,
		CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
		([origin_city_name] ASC))
		GO

    Dopo aver immesso l'istruzione `GO`, verranno valutate le istruzioni precedenti. Si creerà una nuova tabella denominata __delays__ con un indice cluster (obbligatorio del database SQL).

    Per verificare la corretta creazione della tabella, usare il comando seguente:

        SELECT * FROM information_schema.tables
        GO

    L'output dovrebbe essere simile al seguente:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     delays      BASE TABLE

8. Per uscire dall'utilità psql, immettere `exit` al prompt di `1>`.
	
##Esportare i dati con Sqoop

1. Usare il comando seguente per creare un collegamento al driver JDBC di SQL Server dalla directory lib di Sqoop. Ciò consentirà a Sqoop utilizzare tale driver per comunicare con il database SQL:

		sudo ln /usr/share/java/sqljdbc_4.1/enu/sqljdbc4.jar /usr/hdp/current/sqoop-client/lib/sqljdbc4.jar

2. Usare il comando seguente per verificare che Sqoop possa visualizzare il database SQL:

		sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

	Dovrebbe essere visualizzato un elenco di database, compreso il database sqooptest creato in precedenza.

3. Usare il comando seguente per esportare dati dalla tabella hivesampletable alla tabella mobiledata:

		sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir 'wasb:///tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1

	I questo modo, si indicherà a Sqoop di connettersi ai database SQL e sqooptest e di esportare dati da wasb:///tutorials/flightdelays/output, dove è stato archiviato l'output della query hive, alla tabella delays.

4. Al termine dell'esecuzione del comando, usare le informazioni seguenti per connettersi al database tramite TSQL:

		TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

	Sopo la connessione, usare le istruzioni seguenti per verificare che i dati siano stati esportati nella tabella mobiledata:
	
		SELECT * FROM delays
		GO

	Dovrebbe essere visualizzato un elenco di dati della tabella. Digitare `exit` per uscire dall'utilità tsql.

##<a id="nextsteps"></a> Passaggi successivi
È stato illustrato come caricare file nell'archivio BLOB di Azure, come popolare una tabella Hive con i dati disponibili nell'archivio BLOB di Azure, come eseguire query Hive e come usare Sqoop per esportare i dati da HDFS in un database SQL di Azure. Per altre informazioni, vedere gli articoli seguenti:

* [Introduzione a HDInsight][hdinsight-get-started]
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Usare Oozie con HDInsight][hdinsight-use-oozie]
* [Usare Sqoop con Hadoop in HDInsight][hdinsight-use-sqoop]
* [Usare Pig con HDInsight][hdinsight-use-pig]
* [Sviluppare programmi MapReduce Java per HDInsight][hdinsight-develop-mapreduce]
* [Sviluppare programmi per la creazione di flussi Hadoop in Python per HDInsight][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
[powershell-install-configure]: ../install-configure-powershell.md

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


 

<!---HONumber=August15_HO8-->