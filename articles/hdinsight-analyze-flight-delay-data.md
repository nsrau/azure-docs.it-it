<properties linkid="manage-services-hdinsight-analyze-flight-delay-data" urlDisplayName="Analyze flight delay data with Hadoop in HDInsight" pageTitle="Analyze flight delay data using Hadoop in HDInsight | Azure" metaKeywords="" description="Learn how to upload data to HDInsight, how to process the data using Hive, and how to export the results to SQL Database using Sqoop." metaCanonical="" services="hdinsight" documentationCenter="" title="Analyze flight delay data using Hadoop in HDInsight " authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Analizzare i dati sui ritardi dei voli usando HDInsight

fornisce un metodo per l'esecuzione di processi MapReduce mediante un linguaggio di scripting simile a SQL, denominato *[HiveQL][]*, che può essere applicato per attività di riepilogo, query e analisi di volumi di dati molto elevati. In questa esercitazione è illustrato come usare Hive per calcolare la media dei ritardi negli aeroporti e come usare Sqoop per esportare i risultati nel database SQL.

**Prerequisiti:**

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

-   Disporre di un cluster HDInsight di Azure. Per informazioni sul provisioning di un cluster HDInsight, vedere [Introduzione all'utilizzo di HDInsight][] o [Provisioning di cluster HDInsight][].
-   Una workstation in cui sia stato installato e configurato Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][].

**Tempo previsto per il completamento:** 30 minuti

## Contenuto dell'esercitazione:

-   [Preparazione dell'esercitazione][]
-   [Creazione e caricamento di uno script HiveQL][]
-   [Esecuzione dello script HiveQL][]
-   [Esportazione dell'output nel database SQL di Azure][]
-   [Passaggi successivi][]

## <span id="prepare"></span></a>Preparazione dell'esercitazione

In questa esercitazioni vengono utilizzati dati relativi alle prestazioni rispetto agli orari previsti dei voli delle compagnie aeree, che possono essere scaricati dalla pagina relativa a [Research and Innovative Technology Administration, Bureau of Transportation Statistics][] (RITA) nella workstation in uso. Verranno eseguite le operazioni seguenti:

1.  Download dei dati relativi alle prestazioni rispetto agli orari previsti da RITA alla workstation mediante un browser Web.
2.  Caricamento dei dati in HDInsight mediante Azure PowerShell
3.  Preparazione del database SQL per l'esportazione dei dati mediante Azure PowerShell

**Informazioni sull'archiviazione in HDInsight**

HDInsight utilizza l'archivio BLOB di Azure per l'archiviazione dei dati. Questa risorsa è denominata *WASB* o *Archiviazione BLOB di Azure*. WASB è l'implementazione Microsoft di HDFS sull'archivio BLOB di Azure. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight][].

Durante il provisioning di un cluster HDInsight, un contenitore di archiviazione BLOB viene designato come file system predefinito, come in HDFS. Oltre a tale contenitore, è possibile aggiungere ulteriori contenitori dallo stesso account di archiviazione di Azure o da account di archiviazione di Azure diversi durante il processo di provisioning. Per istruzioni sull'aggiunta di ulteriori account di archiviazione, vedere [Provisioning di cluster HDInsight][].

Per semplificare lo script di PowerShell utilizzato in questa esercitazione, tutti i file vengono archiviati nel contenitore del file system predefinito, presente in */tutorials/flightdelays*. Per impostazione predefinita, il nome di questo contenitore corrisponde al nome del cluster HDInsight.

La sintassi WASB è la seguente:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Nella versione 3.0 del cluster HDInsight è supportata solo la sintassi *wasb://*. La sintassi *asv://*, meno recente, è supportata nei cluster HDInsight 2.1 e 1.6, ma non è supportata nei cluster HDInsight 3.0 e non sarà supportata nelle versioni successive.

> Il percorso di WASB è un percorso virtuale. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight][].

È possibile accedere da HDInsight a un file archiviato nel contenitore del file system predefinito utilizzando uno degli URI seguenti (utilizzare come esempio flightdelays.hql):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
    wasb:///tutorials/flightdelays/flightdelays.hql
    /tutorials/flightdelays/flightdelays.hql

Per accedere al file direttamente dall'account di archiviazione, il nome del BLOB per questo file è:

    tutorials/flightdelays/flightdelays.hql

Nella tabella seguente vengono elencati i file utilizzati nell'esercitazione:

| **File**                                    | **Descrizione**                         |
|---------------------------------------------|-----------------------------------------|
| \\tutotirals\\flightdelays\\data            | Dati di input relativi ai voli per Hive |
| \\tutorials\\flightdelays\\output           | Output per Hive                         |
| \\tutorials\\flightdelays\\flightdelays.hql | File script HiveQL                      |
| \\tutorials\\flightdelays\\jobstatus        | Stato del processo Hadoop               |

**Comprendere la tabella interna e la tabella esterna di Hive**

Vi sono alcune informazioni che è necessario conoscere riguardo la tabella interna ed esterna di Hive:

-   Il comando CREATE TABLE consente di creare una tabella interna. Il file di dati deve trovarsi nel contenitore predefinito.
-   Il comando CREATE TABLE permette di spostare il file di dati nella cartella /hive/warehouse/<tablename>.
-   Il comando CREATE EXTERNAL TABLE consente di creare una tabella esterna. Il file di dati può trovarsi all'esterno del contenitore predefinito.
-   Il comando CREATE EXTERNAL TABLE non consente di spostare il file di dati.
-   Il comando CREATE EXTERNAL TABLE non consente la creazione di cartelle in LOCATION. È per questo motivo che nell'esercitazione si esegue una copia del file sample.log.

Per ulteriori informazioni, vedere l'articolo introduttivo a [HDInsight e alle tabelle interne ed esterne di Hive][cindygross-hive-tables].

> [WACOM.NOTE] Una delle istruzioni di HiveQL consente di creare una tabella esterna di Hive. La tabella esterna di Hive mantiene il file di dati nella posizione originale. La tabella interna di Hive sposta il file di dati in hive\\warehouse. Per la tabella esterna di Hive è necessario che il file di dati si trovi nel contenitore WASB predefinito del file system. Se si sceglie di archiviare i file di dati relativi ai voli in un contenitore diverso dal contenitore BLOB predefinito, sarà necessario utilizzare le tabelle interne di Hive.

**Per scaricare i dati relativi ai voli**

1.  Passare alla pagina [Research and Innovative Technology Administration, Bureau of Transportation Statistics][] (RITA).
2.  Selezionare i valori seguenti nella pagina:

    | Nome          | Valore                                                                                                                                                                                                                                                                                                                                                                                                |
    |---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Filter Year   | 2012                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Filter Period | January                                                                                                                                                                                                                                                                                                                                                                                               |
    | Campi:        | \*Year\*, \*FlightDate\*, \*UniqueCarrier\*, \*Carrier\*, \*FlightNum\*, \*OriginAirportID\*, \*Origin\*, \*OriginCityName\*, \*OriginState\*, \*DestAirportID\*, \*Dest\*, \*DestCityName\*, \*DestState\*, \*DepDelayMinutes\*, \*ArrDelay\*, \*ArrDelayMinutes\*, \*CarrierDelay\*, \*WeatherDelay\*, \*NASDelay\*, \*SecurityDelay\*, \*LateAircraftDelay\* (deselezionare tutti gli altri campi) |

3.  Fare clic su **Download**. Per il download di ogni file potrebbero essere necessari fino a 15 minuti.
4.  Decomprimere il file nella cartella **C:\\Tutorials\\FlightDelays\\Data**. Ogni file è in formato CSV e ha dimensione pari a circa 60 GB.
5.  Rinominare il file, specificando il nome del mese a cui fanno riferimento i dati. Ad esempio, al file contenente i dati relativi a gennaio verrà assegnato il nome *January.csv*.
6.  Ripetere i passaggi 2 e 5 per scaricare un file per ognuno dei 12 mesi del 2012. Per l'esecuzione dell'esercitazione è necessario disporre di almeno un file.

**Per caricare i dati relativi ai ritardi dei voli nell'archivio BLOB di Azure**

1.  Aprire Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][].
2.  Eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

        Add-AzureAccount

    Verrà richiesto di immettere le credenziali dell'account Azure.

3.  Impostare le prime tre variabili, quindi eseguire i comandi seguenti:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $ContainerName = "<BlobStorageContainerName>"

        $localFolder = "C:\Tutorials\FlightDelays\Data"
        $destFolder = "tutorials/flightdelays/data"

        $month = "January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"

    Di seguito sono riportate le variabili e le rispettive descrizioni:

    <table border="1">

    <tr>
    <td>
    <B>Nome variabile</b>

    </td>
    <td>
    <b>Descrizione</b>

    </td>
    </tr>

    <tr>
    <td>
    $subscriptionName

    </td>
    <td>
    Nome della sottoscrizione di Azure.

    </td>
    </tr>

    <tr>
    <td>
    $storageAccountName

    </td>
    <td>
    Account di archiviazione di Azure utilizzato per l'archiviazione dei file relativi ai dati dei voli. È consigliabile utilizzare l'account di archiviazione predefinito.

    </td>
    </tr>

    <tr>
    <td>
    $containerName

    </td>
    <td>
    Contenitore di archiviazione BLOB di Azure utilizzato per l'archiviazione dei file relativi ai dati dei voli. È consigliabile utilizzare il contenitore BLOB predefinito del file system del cluster HDInsight. Per impostazione predefinita, il nome del contenitore corrisponde al nome del cluster HDInsight.

    </td>
    </tr>

    <tr>
    <td>
    $localFolder

    </td>
    <td>
    Cartella nella workstation in cui sono archiviati i file relativi ai ritardi dei voli.

    </td>
    </tr>

    <tr>
    <td>
    $destFolder

    </td>
    <td>
    Percorso WASB in cui verranno caricati i dati relativi ai ritardi dei voli. Il percorso di Hadoop (HDInsight) rispetta la distinzione tra maiuscole e minuscole.

    </td>
    </tr>

    <tr>
    <td>
    $month

    </td>
    <td>
    Se non sono stati scaricati tutti e 12 i file, sarà necessario aggiornare questa variabile.

    </td>
    </tr>

    </table>
    </p>
4.  Eseguire i comandi seguenti per caricare ed elencare i file:

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to WASB        
        foreach ($item in $month){
            $fileName = "$localFolder\$item.csv"
            $blobName = "$destFolder/$item.csv"

            Write-Host "Copying $fileName to $blobName" -BackgroundColor Green

            Set-AzureStorageBlobContent -File $fileName -Container $dataContainerName -Blob $blobName -Context $destContext
        }

        # List the uploaded files on HDinsight
        Get-AzureStorageBlob -Container $dataContainerName  -Context $destContext -Prefix $destFolder

Se si sceglie un metodo diverso per il caricamento dei file, verificare che il percorso sia *tutorials/flightdelays/data*. Di seguito viene riportata la sintassi per l'accesso ai file:

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

*tutorials/flightdelays/data* è la cartella virtuale creata durante il caricamento dei file. Verificare che siano disponibili 12 file, uno per ogni mese.

**Per preparare il database SQL**

1.  Aprire Azure PowerShell.
2.  Eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

        Add-AzureAccount

    Verrà richiesto di immettere le credenziali dell'account Azure.

3.  Impostare le prime sei variabili necessarie nello script seguente, quindi eseguire i comandi:

        # Azure subscription name
        $subscriptionName = "<AzureSubscriptionName>"

        # SQL database server variables
        $sqlDatabaseServer = ""  # specify the Azure SQL database server name if you have one created. Otherwise use "".
        $sqlDatabaseUsername = "<SQLDatabaseUserName>"
        $sqlDatabasePassword = "<SQLDatabasePassword>"
        $sqlDatabaseLocation = "<MicrosoftDataCenter>"   #For example, West US.

        # SQL database variables
        $sqlDatabaseName = ""  # specify the database name if you have one created.  Otherwise use "" to have the script create one for you.
        $sqlDatabaseMaxSizeGB = 10

        #SQL query string for creating AvgDelays table
        $sqlDatabaseTableName = "AvgDelays"
        $sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                [origin_city_name] [nvarchar](50) NOT NULL,
                [weather_delay] float,
            CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
            (
                [origin_city_name] ASC
            )
            )"

    Di seguito sono riportate le variabili e le rispettive descrizioni:

    <table border="1">

    <tr>
    <td>
    <b>Nome variabile</b>

    </td>
    <td>
    <b>Descrizione</b>

    </td>
    </tr>

    <tr>
    <td>
    $subscriptionName

    </td>
    <td>
    Nome della sottoscrizione di Azure.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseServer

    </td>
    <td>
    Nome del server di database SQL utilizzato da Sqoop come destinazione dell'esportazione dei dati. Se non lo si modifica, lo script ne creerà uno automaticamente. In caso contrario, specificare un database SQL o un server SQL esistente.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseUsername

    </td>
    <td>
    Nome utente del database SQL o del server SQL.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabasePassword

    </td>
    <td>
    Password del database SQL o del server SQL.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseLocation

    </td>
    <td>
    Utilizzato solo se si desidera che lo script crei automaticamente un server di database SQL.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseName

    </td>
    <td>
    Nome del database SQL utilizzato da Sqoop come destinazione dell'esportazione dei dati. Se non lo si modifica, lo script ne creerà uno automaticamente. In caso contrario, specificare un database SQL o un server SQL esistente.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseMaxSizeGB

    </td>
    <td>
    Utilizzato solo se si desidera che lo script crei automaticamente un database SQL.

    </td>
    </tr>

    </table>
    </p>
4.  Eseguire i comandi seguenti per creare un server, un database o una tabella di database SQL.

        # Select the current Azure subscription in case there are multiple subscriptions
        Select-AzureSubscription $subscriptionName

        # create a new Azure SQL Database if requested
        if ([string]::IsNullOrEmpty($sqlDatabaseServer))
        {
            $sqlDatabaseServer = New-AzureSqlDatabaseServer -AdministratorLogin $sqlDatabaseUsername -AdministratorLoginPassword $sqlDatabasePassword -Location $sqlDatabaseLocation 
            Write-Host "The new SQL Database server is $sqlDatabaseServer."  -BackgroundColor Green

        }
        else
        {
            Write-Host "Use an existing SQL Database server: $sqlDatabaseServer" -BackgroundColor Green
        }

        # Create a new SQL database if requested
        if ([string]::IsNullOrEmpty($sqlDatabaseName))
        {
            $sqlDatabaseName = "HDISqoop"
            $sqlDatabaseServerCredential = new-object System.Management.Automation.PSCredential($sqlDatabaseUsername, ($sqlDatabasePassword  | ConvertTo-SecureString -asPlainText -Force)) 
            $sqlDatabaseServerConnectionContext = New-AzureSqlDatabaseServerContext -ServerName $sqlDatabaseServer -Credential $sqlDatabaseServerCredential 

            $sqlDatabase = New-AzureSqlDatabase -ConnectionContext $sqlDatabaseServerConnectionContext -DatabaseName $sqlDatabaseName -MaxSizeGB $sqlDatabaseMaxSizeGB

            Write-Host "The new SQL Database is $sqlDatabaseName."  -BackgroundColor Green

        }
        else
        {
            Write-Host "Use an existing SQL Database : $sqlDatabaseName" -BackgroundColor Green
        }

        #Create AvgDelays table
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseUsername;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $sqlCreateAvgDelaysTable
        $cmd.executenonquery()

        $conn.close()

## <span id="createscript"></span></a>Creazione e caricamento di uno script HiveQL

Azure PowerShell consente di eseguire più istruzioni HiveQL contemporaneamente o di inserire l'istruzione HiveQL in un file di script. In questa esercitazione verrà creato uno script HiveQL. Il file di script deve essere caricato in WASB. Nella sezione successiva il file di script verrà eseguito mediante Azure PowerShell.

Il file di script HiveQL eseguirà le operazioni seguenti:

1.  **Eliminazione della tabella delays\_raw**, nel caso in cui la tabella esista già.
2.  **Creazione della tabella Hive esterna delays\_raw**, che fa riferimento al percorso WASB che include i file relativi ai ritardi dei voli. La query consente di specificare che i campi sono delimitati da "," e che le righe vengono interrotte da "\\n". Ciò costituisce un problema quando i valori dei campi *contengono* virgole, poiché Hive non è in grado di distinguere tra una virgola che delimita i campi e una virgola inclusa in un valore di campo, come ad esempio nel caso dei valori di campo per ORIGIN\_CITY\_NAME e DEST\_CITY\_NAME. Per risolvere questo problema, la query crea colonne TEMP in cui inserire i dati suddivisi erroneamente in colonne.
3.  **Eliminazione della tabella delays**, nel caso in cui la tabella esista già.
4.  **Creazione della tabella delays**. È consigliabile ripulire i dati prima di procedere con l'elaborazione. La query crea una nuova tabella, *delays* dalla tabella *delays\_raw*. Si noti che le colonne TEMP, come indicato in precedenza, non vengono copiate e che la funzione *substring* viene utilizzata per rimuovere le virgolette dai dati.
5.  **Viene calcolata la media dei ritardi dovuti alle condizioni climatiche e i risultati vengono raggruppati in base al nome della città.** I risultati verranno anche inviati come output a WASB. Si noti che la query rimuoverà gli apostrofi dai dati ed escluderà le righe in cui il valore per *weather\_delay* è *null*. Ciò è necessario perché Sqoop, utilizzato più avanti nell'esercitazione, non è in grado di gestire correttamente tali valori per impostazione predefinita

Per un elenco completo di comandi di HiveQL, vedere la pagina relativa al [linguaggio di definizione dei dati Hive][HiveQL]. Ogni comando HiveQL deve terminare con un punto e virgola.

**Per creare un file di script HiveQL**

1.  Aprire Azure PowerShell.
2.  Eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

        Add-AzureAccount

3.  Impostare le prime due variabili, quindi eseguire i comandi seguenti:

        $storageAccountName = "<AzureStorageAccountName>"
        $containerName ="<BlobStorageContainerName>"

        $hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql"  
        $hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 

        $srcDataFolder = "tutorials/flightdelays/data" 
        $dstDataFolder = "tutorials/flightdelays/output"

    Di seguito sono riportate le variabili e le rispettive descrizioni:

    <table border="1">

    <tr>
    <td>
    <b>Nome variabile</b>

    </td>
    <td>
    <b>Descrizione</b>

    </td>
    </tr>

    <tr>
    <td>
    $storageAccountName

    </td>
    <td>
    Account di archiviazione di Azure utilizzato per l'archiviazione del file di script HiveQL. Per gli script di PowerShell disponibili in questa esercitazione è necessario che i file relativi ai dati dei voli e il file di script si trovino nello stesso account di archiviazione di Azure e nello stesso contenitore di archiviazione BLOB.

    </td>
    </tr>

    <tr>
    <td>
    $containerName

    </td>
    <td>
    Contenitore di archiviazione BLOB di Azure utilizzato per l'archiviazione del file di script HiveQL. Per gli script di PowerShell disponibili in questa esercitazione è necessario che i file relativi ai dati dei voli e il file di script si trovino nello stesso account di archiviazione di Azure e nello stesso contenitore di archiviazione BLOB.

    </td>
    </tr>

    <tr>
    <td>
    $hqlLocalFileName

    </td>
    <td>
    Nome dei file locali per lo script HiveQL prima del caricamento in WASB. Per semplificare lo script PowerShell, il file verrà scritto localmente e quindi verrà utilizzato il cmdlet Set-AzureStorageBlobContent per caricare il file di script in HDInsight.

    </td>
    </tr>

    <tr>
    <td>
    $hqlBlobName

    </td>
    <td>
    Nome del file di script con percorso su WASB.

    </td>
    </tr>

    <tr>
    <td>
    $srcDataFolder

    </td>
    <td>
    Cartella in WASB da cui lo script HiveQL recupera i dati.

    </td>
    </tr>

    <tr>
    <td>
    $dstDataFolder

    </td>
    <td>
    Cartella in WASB a cui lo script HiveQL invia l'output. Più avanti nell'esercitazione verrà utilizzato Sqoop per esportare i dati di questa cartella in database SQL di Azure.

    </td>
    </tr>

    </table>
    </p>
4.  Eseguire i comandi seguenti per definire le istruzioni HiveQL:

        $hqlDropDelaysRaw = "DROP TABLE delays_raw;"

        $hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
                "YEAR string, " +
                "FL_DATE string, " +
                "UNIQUE_CARRIER string, " +
                "CARRIER string, " +
                "FL_NUM string, " +
                "ORIGIN_AIRPORT_ID string, " +
                "ORIGIN string, " +
                "ORIGIN_CITY_NAME string, " +
                "ORIGIN_CITY_NAME_TEMP string, " +
                "ORIGIN_STATE_ABR string, " +
                "DEST_AIRPORT_ID string, " +
                "DEST string, " +
                "DEST_CITY_NAME string, " +
                "DEST_CITY_NAME_TEMP string, " +
                "DEST_STATE_ABR string, " +
                "DEP_DELAY_NEW float, " +
                "ARR_DELAY_NEW float, " +
                "CARRIER_DELAY float, " +
                "WEATHER_DELAY float, " +
                "NAS_DELAY float, " +
                "SECURITY_DELAY float, " +
                "LATE_AIRCRAFT_DELAY float) " +
            "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
            "LINES TERMINATED BY '\n' " +
            "STORED AS TEXTFILE " +
            "LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$srcDataFolder';" 

        $hqlDropDelays = "DROP TABLE delays;"

        $hqlCreateDelays = "CREATE TABLE delays AS " +
            "SELECT YEAR AS year, " +
                "FL_DATE AS flight_date, " +
                "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
                "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
                "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
                "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
                "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
                "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
                "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
                "DEST_AIRPORT_ID AS dest_airport_id, " +
                "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
                "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
                "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
                "DEP_DELAY_NEW AS dep_delay_new, " +
                "ARR_DELAY_NEW AS arr_delay_new, " +
                "CARRIER_DELAY AS carrier_delay, " +
                "WEATHER_DELAY AS weather_delay, " +
                "NAS_DELAY AS nas_delay, " +
                "SECURITY_DELAY AS security_delay, " +
                "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
            "FROM delays_raw;" 

        $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$dstDataFolder' " +
            "SELECT regexp_replace(origin_city_name, '''', ''), " +
                "avg(weather_delay) " +
            "FROM delays " +
            "WHERE weather_delay IS NOT NULL " +
            "GROUP BY origin_city_name;"

        $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal

5.  Eseguire i comandi seguenti per scrivere il file di script Hive nella workstation e caricarlo in WASB:

        # Write the Hive script into a local file
        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to WASB
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $containerName -Blob $hqlBlobName -Context $destContext

        # List the script file from WASB
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $hqlBlobName

    L'output deve essere analogo al seguente:

        Container Uri: https://xxxxxxxx.blob.core.windows.net/hdi0212v3

        Name                              BlobType   Length                            ContentType                       LastModified                      SnapshotTime
        ----                              --------   ------                            -----------                       ------------                      ------------
        tutorials/flightdelays/flightd... BlockBlob  1938                              application/octet-stream          2/12/2014 9:57:28 PM +00:00

## <span id="executehqlscript"></span></a>Esecuzione dello script HiveQL

Per eseguire Hive, è possibile utilizzare alcuni cmdlet di Azure PowerShell. In questa esercitazione viene utilizzato Invoke-Hive. Per informazioni sugli altri metodi, vedere [Usare Hive con HDInsight][]. Se si utilizza Invoke-Hive, sarà possibile eseguire un'istruzione HiveQL o uno script HiveQL. Verrà utilizzato lo script HiveQL creato e caricato nell'archivio BLOB di Azure.

Il percorso di Hive è caratterizzato da un problema noto Le istruzioni per la risoluzione del problema sono disponibili nella [Wiki di TechNet][].

**Per eseguire le query Hive usando PowerShell**

1.  Aprire Azure PowerShell.
2.  Eseguire il comando seguente per cambiare la directory corrente:

    cd \\Tutorials\\FlightDelays\\

    Questo passaggio è necessario perché una copia dell'output di Hive verrà scaricata nella workstation. Per impostazione predefinita, non si dispone di autorizzazioni di scrittura per la cartella PowerShell.

3.  Eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

        Add-AzureAccount

4.  Impostare le prime tre variabili, quindi eseguirle:

        $clusterName = "<HDInsightClusterName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureBlobStorageContainerName>"
        $hqlScriptFile = "wasb://$containerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql"
        $outputBlobName = "tutorials/flightdelays/output/000000_0"

    Di seguito sono riportate le variabili e le rispettive descrizioni:

    <table border="1">

    <tr>
    <td>
    <b>Nome variabile</b>

    </td>
    <td>
    <b>Descrizione<b>

    </td>
    </tr>

    <tr>
    <td>
    $clusterName

    </td>
    <td>
    Cluster HDInsight che eseguirà lo script Hive e l'esportazione tramite Sqoop.

    </td>
    </tr>

    <tr>
    <td>
    $storageAccountName

    </td>
    <td>
    Account di archiviazione di Azure utilizzato per l'archiviazione dello script HiveQL. Vedere <a href = "#createScript">Creazione e caricamento di uno script HiveQL</a>.

    </td>
    </tr>

    <tr>
    <td>
    $containerName

    </td>
    <td>
    Contenitore di archiviazione BLOB di Azure utilizzato per l'archiviazione dello script HiveQL. Vedere <a href = "#createScript">Creazione e caricamento di uno script HiveQL</a>.

    </td>
    </tr>

    <tr>
    <td>
    $hqlScriptFile

    </td>
    <td>
    URI per il file di script HiveQL.

    </td>
    </tr>

    <tr>
    <td>
    $outputBlobName

    </td>
    <td>
    File di output dello script HiveQL. Il nome predefinito è *000000\_0*.

    </td>
    </tr>

    </table>
    </p>
5.  Eseguire il comando seguente per richiamare Hive:

        Use-AzureHDInsightCluster $clusterName

        Invoke-Hive –File $hqlScriptFile

6.  Eseguire il comando seguente per verificare i risultati:

        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

        Get-AzureStorageBlobContent -Container $ContainerName -Blob $outputBlobName -Context $storageContext 

        cat ".\$outputBlobName" | findstr /B "Ch"

    L'output deve essere analogo al seguente:

        Champaign/Urbana 19.023255813953487
        Charleston 24.93975903614458
        Charleston/Dunbar 16.954545454545453
        Charlotte 16.88616981831665
        Charlotte Amalie 12.88888888888889
        Charlottesville 25.444444444444443
        Chattanooga 19.883561643835616
        Cheyenne 9.875
        Chicago 16.77321649680922
        Chico 12.340909090909092
        Christiansted 12.318181818181818

    I valori relativi a ogni città e alla durata del ritardo sono separati da un delimitatore non visibile nella finestra di output di PowerShell, ovvero "\\001". Tale delimitatore verrà utilizzato durante l'esecuzione dell'esportazione tramite Sqoop.

## <span id="exportdata"></span></a>Esportazione dell'output del processo Hive in database SQL di Azure

L'ultima procedura consiste nell'eseguire l'esportazione tramite Sqoop per esportare i dati nel database SQL. In precedenza nell'esercitazione sono stati creati il database SQL e la tabella AvgDelays.

**Per esportare i dati nel database SQL**

1.  Aprire Azure PowerShell.
2.  Eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

        Add-AzureAccount

3.  Impostare le prime cinque variabili, quindi eseguire i comandi seguenti:

        $clusterName = "<HDInsightClusterName>"

        $sqlDatabaseServerName = "<SQLDatabaseServerName>"
        $sqlDatabaseUserName = "<SQLDatabaseUsername>"
        $sqlDatabasePassword = "<SQLDatabasePassword>"

        $sqlDatabaseName = "<SQLDatabaseName>" # The default name is "HDISqoop"
        $sqlDatabaseTableName = "AvgDelays" 

        $exportDir = "wasb://$containerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/output"

        $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

    Di seguito sono riportate le variabili e le rispettive descrizioni:

    <table border="1">

    <tr>
    <td>
    <b>Nome variabile</b>

    </td>
    <td>
    <b>Nota</b>

    </td>
    </tr>

    <tr>
    <td>
    $clusterName

    </td>
    <td>
    Nome del cluster HDInsight.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseServer

    </td>
    <td>
    Server di database SQL nel quale Sqoop esporterà i dati.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseUsername

    </td>
    <td>
    Nome utente di database SQL.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabasePassword

    </td>
    <td>
    Password utente del database SQL.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseName

    </td>
    <td>
    Database SQL nel quale Sqoop esporterà i dati. Il nome predefinito è \*HDISqoop".

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseTableName

    </td>
    <td>
    Database SQL nel quale Sqoop esporterà i dati. Il nome predefinito è AvgDelays. Si tratta della tabella creata in precedenza nell'esercitazione.

    </td>
    </tr>

    <tr>
    <td>
    $exportDir

    </td>
    <td>
    Posizione del file di output di Hive. Sqoop esporterà i file in tale posizione nel database SQL.

    </td>
    </tr>

    </table>
    </p>
4.  Eseguire il comando seguente per esportare i dati:

        $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "

        $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
        Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

        Write-Host "Standard Error" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
        Write-Host "Standard Output" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5.  Effettuare la connessione al database SQL e verificare la media dei ritardi dei voli per ogni città nella tabella *AvgDelays*:

    ![HDI.FlightDelays.AvgDelays.Dataset][]

## <span id="nextsteps"></span></a>Passaggi successivi

È stato illustrato come caricare file nell'archiviazione BLOB, come popolare una tabella Hive utilizzando i dati disponibili nell'archiviazione BLOB, come eseguire query Hive e come utilizzare Sqoop per esportare i dati da HDFS nel database SQL di Azure. Per ulteriori informazioni, vedere gli articoli seguenti:

-   [Introduzione all'utilizzo di HDInsight][]
-   [Usare Hive con HDInsight][]
-   [Usare Oozie con HDInsight][]
-   [Usare Sqoop con HDInsight][]
-   [Usare Pig con HDInsight][]
-   [Sviluppare programmi MapReduce Java per HDInsight][]
-   [Sviluppare programmi per la creazione di flussi Hadoop in C# per HDInsight][]

  [HiveQL]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
  [Introduzione all'utilizzo di HDInsight]: ../hdinsight-get-started/
  [Provisioning di cluster HDInsight]: ../hdinsight-provision-clusters/
  [Come installare e configurare Azure PowerShell]: ../install-configure-powershell/
  [Preparazione dell'esercitazione]: #prepare
  [Creazione e caricamento di uno script HiveQL]: #createscript
  [Esecuzione dello script HiveQL]: #executehqlscript
  [Esportazione dell'output nel database SQL di Azure]: #exportdata
  [Passaggi successivi]: #nextsteps
  [Research and Innovative Technology Administration, Bureau of Transportation Statistics]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
  [Usare l'archivio BLOB di Azure con HDInsight]: ../hdinsight-use-blob-storage/
  [Usare Hive con HDInsight]: ../hdinsight-use-hive/
  [Wiki di TechNet]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
  [1]: #createScript
  [HDI.FlightDelays.AvgDelays.Dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
  [Usare Oozie con HDInsight]: ../hdinsight-use-oozie/
  [Usare Sqoop con HDInsight]: ../hdinsight-use-sqoop/
  [Usare Pig con HDInsight]: ../hdinsight-use-pig/
  [Sviluppare programmi MapReduce Java per HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
  [Sviluppare programmi per la creazione di flussi Hadoop in C# per HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
