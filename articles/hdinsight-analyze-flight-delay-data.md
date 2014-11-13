<properties urlDisplayName="Analyze flight delay data with Hadoop in HDInsight" pageTitle="Analizzare i dati sui ritardi dei voli usando HDInsight | Azure" metaKeywords="" description="Informazioni su come caricare i dati in HDInsight, elaborarli usando Hive ed esportare i risultati in un database SQL usando Sqoop." metaCanonical="" services="hdinsight" documentationCenter="" title="Analizzare i dati sui ritardi dei voli usando HDInsight " authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Analizzare i dati sui ritardi dei voli usando HDInsight

Hive fornisce un metodo per l'esecuzione di processi MapReduce mediante un linguaggio di scripting simile a SQL, denominato *[HiveQL][HiveQL]*, che può essere applicato per attività di riepilogo, query e analisi di volumi di dati molto elevati. In questa esercitazione è illustrato come usare Hive per calcolare la media dei ritardi negli aeroporti e come usare Sqoop per esportare i risultati nel database SQL di Azure.

Uno dei principali vantaggi di HDInsight è la separazione tra archiviazione e calcolo dei dati. HDInsight usa l'archivio BLOB di Azure per l'archiviazione dei dati. Un comune processo di HDInsight può essere suddiviso in tre parti:

1.  Archiviazione dei dati nell'archiviazione BLOB di Azure, un processo che può essere continuo. Ad esempio, i dati meteo, i dati dei sensori, i blog e, in questo caso, i dati sui ritardi dei voli vengono salvati nell'archiviazione BLOB.
2.  Esecuzione dei processi. Quando giunge il momento di elaborare i dati, viene eseguito uno script PowerShell (o un'applicazione client) per effettuare il provisioning di un cluster HDInsight, eseguire i processi ed eliminare il cluster. I dati di output dei processi vengono salvati nell'archivio BLOB di Azure e vengono conservati anche in seguito all'eliminazione del cluster. In questo modo, l'utente paga solo in base all'effettivo consumo.
3.  Recuperare l'output dall'archivio BLOB o, in questo caso, esportare i dati in un database SQL di Azure.

Nel seguente diagramma vengono illustrati lo scenario e la struttura di questo articolo:

![HDI.FlightDelays.flow][HDI.FlightDelays.flow]

Nota: i numeri nel diagramma corrispondono ai titoli delle sezioni.

**Tempo previsto per il completamento:** 30 minuti

## Contenuto dell'esercitazione:

-   [Prerequisiti][Prerequisiti]
-   [Eseguire un processo Hive su un cluster HDInsight nuovo o esistente (M1)][Eseguire un processo Hive su un cluster HDInsight nuovo o esistente (M1)]
-   [Usare Sqoop per esportare l'output nel database SQL di Azure (M2)][Usare Sqoop per esportare l'output nel database SQL di Azure (M2)]
-   [Passaggi successivi][Passaggi successivi]
-   [Appendice A: Caricare i dati relativi ai ritardi dei voli nell'archivio BLOB di Azure (A1)][Appendice A: Caricare i dati relativi ai ritardi dei voli nell'archivio BLOB di Azure (A1)]
-   [Appendice B: Creare e caricare uno script HiveQL (A2)][Appendice B: Creare e caricare uno script HiveQL (A2)]

## <span id="prerequisite"></span></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

-   Una workstation in cui sia stato installato e configurato Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][Come installare e configurare Azure PowerShell].
-   Una sottoscrizione di Azure. \*\*\*

**Informazioni sull'archiviazione in HDInsight**

I cluster Hadoop in HDInsight usano l'archivio BLOB di Azure per l'archiviazione dei dati. Questa risorsa è denominata *WASB* o *Archiviazione BLOB di Azure*. WASB è l'implementazione Microsoft di *HDFS* sull'archivio BLOB di Azure. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight][Usare l'archivio BLOB di Azure con HDInsight].

Durante il provisioning di un cluster HDInsight, un contenitore di archiviazione BLOB di un account di archiviazione di Azure viene designato come file system predefinito, proprio come HDFS. Questo account di archiviazione è di solito definito *account di archiviazione predefinito* e il contenitore BLOB viene definito come *contenitore BLOB predefinito* o *contenitore predefinito*. L'account di archiviazione predefinito deve trovarsi nello stesso data center che include il cluster HDInsight. L'eliminazione di un cluster HDInsight non comporta l'eliminazione del contenitore predefinito o dell'account di archiviazione predefinito.

Oltre all'account di archiviazione predefinito, è possibile associare altri account di archiviazione di Azure a un cluster HDInsight durante il processo di provisioning. L'associazione consente di aggiungere l'account di archiviazione e la chiave dell'account di archiviazione al file di configurazione. Dunque, il cluster può accedere agli account di archiviazione in fase di esecuzione. Per istruzioni sull'aggiunta di altri account di archiviazione, vedere [Provisioning di cluster Hadoop in HDInsight][Provisioning di cluster Hadoop in HDInsight].

La sintassi WASB è la seguente:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Il percorso WASB è un percorso virtuale. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight][Usare l'archivio BLOB di Azure con HDInsight].

È possibile accedere da HDInsight ai file archiviati nel contenitore predefinito usando uno degli URI seguenti (usare come esempio flightdelays.hql):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
    wasb:///tutorials/flightdelays/flightdelays.hql
    /tutorials/flightdelays/flightdelays.hql

Per accedere al file direttamente dall'account di archiviazione, il nome del BLOB per questo file è:

    tutorials/flightdelays/flightdelays.hql

Osservare che davanti al nome BLOB non è presente il carattere "/".

**File usati in questa esercitazione**

Nella tabella seguente vengono elencati i file usati nell'esercitazione:

| File                                                                      | Descrizione                                                                                                     |
|---------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql | Il file script HiveQL necessario per il processo Hive da eseguire.                                              |
| wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data         | I dati di input per i processi Hive.                                                                            |
| \\tutorials\\flightdelays\\output                                         | Percorso di input per il processo Hive. Il contenitore predefinito viene usato per archiviare i dati di output. |
| \\tutorials\\flightdelays\\jobstatus                                      | Cartella di stato del processo Hive                                                                             |

In questa esercitazione vengono usati dati relativi alle prestazioni rispetto agli orari previsti dei voli delle compagnie aeree, che possono essere scaricati dalla pagina relativa a [Research and Innovative Technology Administration, Bureau of Transportation Statistics][Research and Innovative Technology Administration, Bureau of Transportation Statistics] (RITA). I dati sono stati caricati in un contenitore di archiviazione BLOB di Azure con autorizzazione di accesso al BLOB pubblico. Poiché si tratta di un contenitore BLOB pubblico non è necessario associare questo account di archiviazione al cluster HDInsight. Anche lo script HiveQL viene caricato nello stesso contenitore BLOB. Per informazioni su come ottenere/caricare i dati nel proprio account di archiviazione e su come creare/caricare il file di script HiveQL, vedere l'[appendice][appendice].

**Comprendere la tabella interna e la tabella esterna di Hive**

Vi sono alcune informazioni che è necessario conoscere riguardo la tabella interna ed esterna di Hive:

-   Il comando CREATE TABLE consente di creare una tabella interna. Il file di dati deve trovarsi nel contenitore predefinito.
-   Il comando CREATE TABLE permette di spostare il file di dati nella cartella /hive/warehouse/<tablename>.
-   Il comando CREATE EXTERNAL TABLE consente di creare una tabella esterna. Il file di dati può trovarsi all'esterno del contenitore predefinito.
-   Il comando CREATE EXTERNAL TABLE non consente di spostare il file di dati.
-   Il comando CREATE EXTERNAL TABLE non consente la creazione di cartelle in LOCATION. È per questo motivo che nell'esercitazione si esegue una copia del file sample.log.

Per altre informazioni vedere l'articolo introduttivo a [HDInsight e alle tabelle interne ed esterne di Hive][HDInsight e alle tabelle interne ed esterne di Hive].

> [WACOM.NOTE] Una delle istruzioni di HiveQL consente di creare una tabella esterna di Hive. La tabella esterna di Hive mantiene il file di dati nella posizione originale. La tabella interna di Hive sposta il file di dati in hive\\warehouse. Per la tabella interna di Hive è necessario che il file di dati si trovi nel contenitore predefinito. Per i dati archiviati all'esterno del contenitore BLOB predefinito, è necessario usare le tabelle esterne di Hive.

## <span id="runjob"></span></a>Eseguire il processo Hive su un cluster HDInsight nuovo o esistente

Per Hadoop è prevista l'elaborazione batch. Il modo più conveniente per eseguire un processo Hive consiste nell'effettuare il provisioning di un cluster per il processo ed eliminare il processo dopo il suo completamento. Lo script seguente descrive l'intero processo. Per altre informazioni sul provisioning del cluster HDInsight e l'esecuzione di processi Hive, vedere [Provisioning di cluster Hadoop in HDInsight][Provisioning di cluster Hadoop in HDInsight] e [Usare Hive con HDInsight][Usare Hive con HDInsight].

**Per eseguire le query Hive usando PowerShell**

1.  Aprire PowerShell ISE.
2.  Copiare e incollare lo script seguente nel riquadro dello script:

        *** add script here

3.  Premere **F5** per eseguire lo script. L'output deve essere analogo al seguente:

    ![HDI.FlightDelays.RunHiveJob.output][HDI.FlightDelays.RunHiveJob.output]

    I valori relativi a ogni città e alla durata del ritardo sono separati da un delimitatore non visibile nella finestra di output di PowerShell, ovvero "\\001". Tale delimitatore verrà usato durante l'esecuzione dell'esportazione tramite Sqoop.

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
    **Nome variabile**

    </td>
    <td>
    **Nota**

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

    ![HDI.FlightDelays.AvgDelays.Dataset][HDI.FlightDelays.AvgDelays.Dataset]

## <span id="nextsteps"></span></a>Passaggi successivi

È stato illustrato come caricare file nell'archiviazione BLOB, come popolare una tabella Hive usando i dati disponibili nell'archiviazione BLOB, come eseguire query Hive e come usare Sqoop per esportare i dati da HDFS nel database SQL di Azure. Per altre informazioni, vedere gli articoli seguenti:

-   [Introduzione all'uso di HDInsight][Introduzione all'uso di HDInsight]
-   [Usare Hive con HDInsight][Usare Hive con HDInsight]
-   [Usare Oozie con HDInsight][Usare Oozie con HDInsight]
-   [Usare Sqoop con HDInsight][Usare Sqoop con HDInsight]
-   [Usare Pig con HDInsight][Usare Pig con HDInsight]
-   [Sviluppare programmi MapReduce Java per HDInsight][Sviluppare programmi MapReduce Java per HDInsight]
-   [Sviluppare programmi per la creazione di flussi Hadoop in C# per HDInsight][Sviluppare programmi per la creazione di flussi Hadoop in C# per HDInsight]

## <span id="appendix-a"></span></a>Appendice A - Caricare i dati relativi ai ritardi dei voli nell'archivio BLOB di Azure

**Per scaricare i dati relativi ai voli**

1.  Passare alla pagina [Research and Innovative Technology Administration, Bureau of Transportation Statistics][Research and Innovative Technology Administration, Bureau of Transportation Statistics] (RITA).
2.  Selezionare i valori seguenti nella pagina:

    | Nome          | Valore                                                                                                                                                                                                                                                                                                                                                                                                |
    |---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Filter Year   | 2013                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Filter Period | January                                                                                                                                                                                                                                                                                                                                                                                               |
    | Campi:        | \*Year\*, \*FlightDate\*, \*UniqueCarrier\*, \*Carrier\*, \*FlightNum\*, \*OriginAirportID\*, \*Origin\*, \*OriginCityName\*, \*OriginState\*, \*DestAirportID\*, \*Dest\*, \*DestCityName\*, \*DestState\*, \*DepDelayMinutes\*, \*ArrDelay\*, \*ArrDelayMinutes\*, \*CarrierDelay\*, \*WeatherDelay\*, \*NASDelay\*, \*SecurityDelay\*, \*LateAircraftDelay\* (deselezionare tutti gli altri campi) |

3.  Fare clic su **Download**.
4.  Decomprimere il file nella cartella **C:\\Tutorials\\FlightDelays\\Data**. Ogni file è in formato CSV e ha dimensione pari a circa 60 GB.
5.  Rinominare il file, specificando il nome del mese a cui fanno riferimento i dati. Ad esempio, al file contenente i dati relativi a gennaio verrà assegnato il nome *January.csv*.
6.  Ripetere i passaggi 2 e 5 per scaricare un file per ognuno dei 12 mesi del 2013. Per l'esecuzione dell'esercitazione è necessario disporre di almeno un file.

**Per caricare i dati relativi ai ritardi dei voli nell'archivio BLOB di Azure**

1.  Aprire PowerShell ISE.
2.  Incollare lo script seguente nel riquadro di script:

        [CmdletBinding()]
        Param(

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
            [String]$storageAccountName,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
            [String]$blobContainerName
        )

        #Region - Variables
        $localFolder = "C:\Tutorials\FlightDelays\2014Data"  # the source folder
        $destFolder = "tutorials/flightdelays/data"     #the blob name prefix for the files to be uploaded
        #EndRegion

        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        if (-not (Get-AzureAccount)){ Add-AzureAccount}
        #EndRegion

        #Region - Validate user inpute
        # Validate the storage account
        if (-not (Get-AzureStorageAccount|Where-Object{$_.Label -eq $storageAccountName}))
        {
            Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
            exit
        }

        # Validate the container
        $storageAccountKey = get-azurestoragekey -StorageAccountName $storageAccountName | %{$_.Primary}
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
        {
            Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
            Exit
        }
        #EngRegion

        #Region - Copy the file from local workstation to Azure Blob storage  
        if (test-path -Path $localFolder)
        {
            foreach ($item in Get-ChildItem -Path $localFolder){
                $fileName = "$localFolder\$item"
                $blobName = "$destFolder/$item"

                Write-Host "Copying $fileName to $blobName" -ForegroundColor Green

                Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
            }
        }
        else
        {
            Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
        }

        # List the uploaded files on HDinsight
        Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
        #EndRegion

3.  Premere **F5** per eseguire lo script.

## <span id="appendix-b"></span></a>Appendice B - Creazione e caricamento di uno script HiveQL

Azure PowerShell consente di eseguire più istruzioni HiveQL contemporaneamente o di inserire l'istruzione HiveQL in un file di script. In questa sezione viene spiegato come creare uno script HiveQL e caricarlo nell'archivio BLOB di Azure con PowerShell. Hive richiede che gli script HiveQL siano archiviati su WASB.

Il file di script HiveQL eseguirà le operazioni seguenti:

1.  **Eliminazione della tabella delays\_raw**, nel caso in cui la tabella esista già.
2.  **Creazione della tabella Hive esterna delays\_raw**, che fa riferimento al percorso WASB che include i file relativi ai ritardi dei voli. La query consente di specificare che i campi sono delimitati da "," e che le righe vengono interrotte da "\\n". Ciò costituisce un problema quando i valori dei campi *contengono* virgole, poiché Hive non è in grado di distinguere tra una virgola che delimita i campi e una virgola inclusa in un valore di campo, come ad esempio nel caso dei valori di campo per ORIGIN\_CITY\_NAME e DEST\_CITY\_NAME. Per risolvere questo problema, la query crea colonne TEMP in cui inserire i dati suddivisi erroneamente in colonne.
3.  **Eliminazione della tabella delays**, nel caso in cui la tabella esista già.
4.  **Creazione della tabella delays**. È consigliabile ripulire i dati prima di procedere con l'elaborazione. La query crea una nuova tabella, *delays* dalla tabella *delays\_raw*. Si noti che le colonne TEMP, come indicato in precedenza, non vengono copiate e che la funzione *substring* viene usata per rimuovere le virgolette dai dati.
5.  **Viene calcolata la media dei ritardi dovuti alle condizioni climatiche e i risultati vengono raggruppati in base al nome della città.** I risultati verranno anche inviati come output a WASB. Si noti che la query rimuoverà gli apostrofi dai dati ed escluderà le righe in cui il valore per *weather\_delay* è *null*. Ciò è necessario perché Sqoop, usato più avanti nell'esercitazione, non è in grado di gestire correttamente tali valori per impostazione predefinita

Per un elenco completo di comandi di HiveQL, vedere la pagina relativa al [linguaggio di definizione dei dati Hive][HiveQL]. Ogni comando HiveQL deve terminare con un punto e virgola.

**Per creare un file di script HiveQL**

1.  Aprire Azure PowerShell ISE.
2.  Copiare e incollare lo script seguente nel riquadro dello script:

        [CmdletBinding()]
        Param(

            # Azure blob storage variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the HDInsight cluster name.")]
            [String]$HDInsightClusterName
        )

        #--------------------------------------------------------------------
        # Constants used in the script
        #--------------------------------------------------------------------
        # the HQL script file is exported as this file before uploaded to WASB
        $hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql" 
        # the HQL script file will be upload to WASB as this blob name
        $hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 

        # this two constants are used by the HQL scrpit file
        $srcDataFolder = "tutorials/flightdelays/data" 
        $dstDataFolder = "tutorials/flightdelays/output"

        #--------------------------------------------------------------------
        # Validate the file and file path
        #--------------------------------------------------------------------

        # check if a file with the same file name already exist on the workstation
        Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
        if (test-path $hqlLocalFileName){

            $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", `
                "Overwrite the existing file."

            $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", `
                "Not overwrite the existing file."

            $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)

            $result = $Host.UI.PromptForChoice("Write file", 
                                                "Overwrite the existing script file, 
                                                $hqlLocalFileName?", 
                                                $options, 
                                                0)

            switch ($result)
            {
            0 {
                # create the folder if it doesn't exist
                $folder = split-path $hqlLocalFileName
                if (-not (test-path $folder))
                {
                    Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green

                    new-item $folder -ItemType directory  
                }
            }
            1 { throw "User cancelled the operation."}
            }
        }

        #--------------------------------------------------------------------
        # Add the Azure account 
        #--------------------------------------------------------------------
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Add-AzureAccount


        #--------------------------------------------------------------------
        # Retrieve the default storage account and the default container name
        #--------------------------------------------------------------------
        Write-Host "`nRetrieving the HDInsight cluster default storage account information ..." `
                    -ForegroundColor Green

        $hdi = Get-AzureHDInsightCluster -Name $HDInsightClusterName

        if (-not $hdi){
            throw "The HDInsight cluster, $HDInsightClusterName, doesn't exist!" 
        }
        else{
            $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName `
                                    -replace ".blob.core.windows.net"
            $containerName = $hdi.DefaultStorageAccount.StorageContainerName

            Write-Host "`tThe default storage account for the cluster is $storageAccountName." `
                        -ForegroundColor Cyan
            Write-host "`tThe default Blob container for the cluster is $containerName." `
                        -ForegroundColor Cyan
        }

        #--------------------------------------------------------------------
        # Write the Hive script into a local file
        #--------------------------------------------------------------------
        Write-Host "Writing the Hive script into a file on your workstation ..." `
                    -ForegroundColor Green

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

        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force 

        #--------------------------------------------------------------------
        # Upload the Hive script to the default Blob container
        #--------------------------------------------------------------------
        Write-Host "Uploading the Hive script to the default Blob container ..." -ForegroundColor Green

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Upload the file from local workstation to WASB
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $containerName -Blob $hqlBlobName -Context $destContext 

        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    Le tre costanti usate nello script sono:

    -   **$hqlLocalFileName**: Lo script salva il file di script HiveQL in locale prima di caricarlo in WASB. Questo è il nome file. Il valore predefinito è <u>C:\\tutorials\\flightdelays\\flightdelays.hql</u>.
    -   **$hqlBlobName**: Questo è il nome BLOB del file di script HiveQL usato per l'archiviazione BLOB di Azure. Il valore predefinito è <u>tutorials/flightdelays/flightdelays.hql</u>. Poiché il file verrà scritto direttamente nell'archiviazione BLOB di Azure, all'inizio del nome BLOB NON è presente il carattere "/". Per accedere al file da WASB sarà necessario aggiungere "/" all'inizio del nome file.
    -   **$srcDataFolder** e **$dstDataFolder**: = "tutorials/flightdelays/data"
         = "tutorials/flightdelays/output"

3.  Eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

        Add-AzureAccount

4.  Impostare le prime due variabili, quindi eseguire i comandi seguenti:

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
    **Nome variabile**

    </td>
    <td>
    **Descrizione**

    </td>
    </tr>

    <tr>
    <td>
    $storageAccountName

    </td>
    <td>
    Account di archiviazione di Azure usato per l'archiviazione del file di script HiveQL. Per gli script di PowerShell disponibili in questa esercitazione è necessario che i file relativi ai dati dei voli e il file di script si trovino nello stesso account di archiviazione di Azure e nello stesso contenitore di archiviazione BLOB.

    </td>
    </tr>

    <tr>
    <td>
    $containerName

    </td>
    <td>
    Contenitore di archiviazione BLOB di Azure usato per l'archiviazione del file di script HiveQL. Per gli script di PowerShell disponibili in questa esercitazione è necessario che i file relativi ai dati dei voli e il file di script si trovino nello stesso account di archiviazione di Azure e nello stesso contenitore di archiviazione BLOB.

    </td>
    </tr>

    <tr>
    <td>
    $hqlLocalFileName

    </td>
    <td>
    Nome dei file locali per lo script HiveQL prima del caricamento in WASB. Per semplificare lo script PowerShell, il file verrà scritto localmente e quindi verrà usato il cmdlet Set-AzureStorageBlobContent per caricare il file di script in HDInsight.

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
    Cartella in WASB a cui lo script HiveQL invia l'output. Più avanti nell'esercitazione verrà usato Sqoop per esportare i dati di questa cartella in database SQL di Azure.

    </td>
    </tr>

    </table>
    </p>
5.  Eseguire i comandi seguenti per definire le istruzioni HiveQL:

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

6.  Eseguire i comandi seguenti per scrivere il file di script Hive nella workstation e caricarlo in WASB:

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

## Preparare il database SQL

**Per preparare il database SQL (unirlo con lo script di Sqoop)**

1.  Aprire PowerShell ISE.
2.  Copiare e incollare lo script seguente nel riquadro dello script:

        [CmdletBinding()]
        Param(

            # SQL database server variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database Server Name to use an existing one. Enter nothing to create a new one.")]
            [AllowEmptyString()]
            [String]$sqlDatabaseServer,  # specify the Azure SQL database server name if you have one created. Otherwise use "".

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database admin user.")]
            [String]$sqlDatabaseUsername,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database admin user password.")]
            [String]$sqlDatabasePassword,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the region to create the Database in.")]
            [String]$sqlDatabaseLocation,   #For example, West US.

            # SQL database variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the database name if you have created one. Enter nothing to create one.")]
            [AllowEmptyString()]
            [String]$sqlDatabaseName # specify the database name if you have one created.  Otherwise use "" to have the script create one for you.
        )

        #--------------------------------------------------------------------
        # Constants used in the script
        #--------------------------------------------------------------------
        # IP address REST service used for retrieving external IP address and creating firewall rules
        [String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
        [String]$fireWallRuleName = "FlightDelay"

        # SQL database variables
        [String]$sqlDatabaseMaxSizeGB = 10

        #SQL query string for creating AvgDelays table
        [String]$sqlDatabaseTableName = "AvgDelays"
        [String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [origin_city_name] [nvarchar](50) NOT NULL,
                    [weather_delay] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
                (
                    [origin_city_name] ASC
                )
                )"

        #--------------------------------------------------------------------
        # Add the Azure account 
        #--------------------------------------------------------------------
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Add-AzureAccount

        #--------------------------------------------------------------------
        # Create and validate Azure SQL Database server
        #--------------------------------------------------------------------
        if ([string]::IsNullOrEmpty($sqlDatabaseServer))
        {
            Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
            $sqlDatabaseServer = (New-AzureSqlDatabaseServer -AdministratorLogin $sqlDatabaseUsername -AdministratorLoginPassword $sqlDatabasePassword -Location $sqlDatabaseLocation).ServerName
            Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan

            Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
            $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
            New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress   
            New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-Azureservices" -AllowAllAzureServices 
        }
        else
        {
            $dbServer = Get-AzureSqlDatabaseServer -ServerName $sqlDatabaseServer
            if (! $dbServer)
            {
                throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
            }
            else
            {
                Write-Host "`nUse an existing SQL Database server, $sqlDatabaseServer" -ForegroundColor Green
            }
        }

        #--------------------------------------------------------------------
        # Create and validate Azure SQL database
        #--------------------------------------------------------------------   
        if ([string]::IsNullOrEmpty($sqlDatabaseName))
        {
            Write-Host "`nCreating SQL Database, HDISqoop ..."  -ForegroundColor Green

            $sqlDatabaseName = "HDISqoop"
            $sqlDatabaseServerCredential = new-object System.Management.Automation.PSCredential($sqlDatabaseUsername, ($sqlDatabasePassword  | ConvertTo-SecureString -asPlainText -Force)) 

            $sqlDatabaseServerConnectionContext = New-AzureSqlDatabaseServerContext -ServerName $sqlDatabaseServer -Credential $sqlDatabaseServerCredential 

            $sqlDatabase = New-AzureSqlDatabase -ConnectionContext $sqlDatabaseServerConnectionContext -DatabaseName $sqlDatabaseName -MaxSizeGB $sqlDatabaseMaxSizeGB
        }
        else
        {
            $db = Get-AzureSqlDatabase -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
            if (! $db)
            {
                throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
            }
            else
            {
                Write-Host "`nUse an existing SQL Database, $sqlDatabaseName" -ForegroundColor Green
            }
        }

        #--------------------------------------------------------------------
        # Excute a SQL command to create the AvgDelays table
        #--------------------------------------------------------------------   
        Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseUsername;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $sqlCreateAvgDelaysTable
        $cmd.executenonquery()

        $conn.close()

        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    > [WACOM.NOTE] Lo script usa un servizio REST, http://bot.whatismyipaddress.com, per recuperare l'indirizzo IP esterno. L'indirizzo IP viene usato per creare una regola del firewall per il server di database SQL.

    Le costanti usate nello script sono:

    -   **$ipAddressRestService**: Il valore predefinito è <u>http://bot.whatismyipaddress.com</u>. È un servizio REST per l'indirizzo IP pubblico che consente di ottenere l'indirizzo IP esterno. È anche possibile usare altri servizi. L'indirizzo IP esterno recuperato usando il servizio verrà usato per creare una regola del firewall per il proprio server di database SQL, in modo che sia possibile accedere al database dalla workstation (usando lo script PowerShell).
    -   **$fireWallRuleName**: È la regola del firewall del server di database SQL di Azure. Il nome predefinito è <u>FlightDelay</u>. È anche possibile rinominarla.
    -   **$sqlDatabaseMaxSizeGB**: Questo valore viene usato solo durante la creazione di un nuovo server di database SQL. Il valore predefinito è <u>10 GB</u>, sufficiente per questa esercitazione.
    -   **$sqlDatabaseName**: Questo valore viene usato solo durante la creazione di un nuovo database SQL di Azure. Il valore predefinito è <u>HDISqoop</u>. Se viene rinominato, sarà necessario aggiornare anche lo script PowerShell di Sqoop.

3.  Premere **F5** per eseguire lo script. È necessario immettere le proprie credenziali della sottoscrizione di Azure e i valori seguenti:

    -   **sqlDatabaseServer**: Immettere il nome del server di database SQL nel quale si vuole esportare l'output di Hive. Non immettere nulla per crearne uno.
    -   **sqlDatabaseUsername**: Immettere le credenziali di accesso al database. È necessario specificarle se si vuole creare un nuovo server di database oppure usarne uno esistente.
    -   **sqlDatabasePassword**: Immettere la password di accesso al database. È necessario specificarle se si vuole creare un nuovo server di database oppure usarne uno esistente.
    -   **sqlDatabaseLocation**: Immettere l'area. Viene usata solo quando si vuole creare un nuovo server di database.
    -   **sqlDatabaseName**: Immettere il nome del database SQL di Azure. Non immettere nulla per crearne uno nuovo. Il nome dek database predefinito è **HDISqoop**.

4.  Convalidare l'output dello script. Assicurarsi che lo script sia stato eseguito correttamente.

Se si sceglie un metodo diverso per il caricamento dei file, verificare che il percorso sia *tutorials/flightdelays/data*. Di seguito viene riportata la sintassi per l'accesso ai file:

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

*tutorials/flightdelays/data* è la cartella virtuale creata durante il caricamento dei file. Verificare che siano disponibili 12 file, uno per ogni mese.

  [HiveQL]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
  [HDI.FlightDelays.flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png
  [Prerequisiti]: #prerequisite
  [Eseguire un processo Hive su un cluster HDInsight nuovo o esistente (M1)]: #runjob
  [Usare Sqoop per esportare l'output nel database SQL di Azure (M2)]: #exportdata
  [Passaggi successivi]: #nextsteps
  [Appendice A: Caricare i dati relativi ai ritardi dei voli nell'archivio BLOB di Azure (A1)]: #appdendix-a
  [Appendice B: Creare e caricare uno script HiveQL (A2)]: #appendix-b
  [Come installare e configurare Azure PowerShell]: ../install-configure-powershell/
  [Usare l'archivio BLOB di Azure con HDInsight]: ../hdinsight-use-blob-storage/
  [Provisioning di cluster Hadoop in HDInsight]: ../hdinsight-provision-clusters/
  [Research and Innovative Technology Administration, Bureau of Transportation Statistics]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
  [appendice]: #appendix
  [HDInsight e alle tabelle interne ed esterne di Hive]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
  [Usare Hive con HDInsight]: ../hdinsight-use-hive/
  [HDI.FlightDelays.RunHiveJob.output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
  [HDI.FlightDelays.AvgDelays.Dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
  [Introduzione all'uso di HDInsight]: ../hdinsight-get-started/
  [Usare Oozie con HDInsight]: ../hdinsight-use-oozie/
  [Usare Sqoop con HDInsight]: ../hdinsight-use-sqoop/
  [Usare Pig con HDInsight]: ../hdinsight-use-pig/
  [Sviluppare programmi MapReduce Java per HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
  [Sviluppare programmi per la creazione di flussi Hadoop in C# per HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
