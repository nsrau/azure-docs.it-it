---
title: 'Analizzare i dati di Twitter con Hadoop in HDInsight: Azure | Microsoft Docs'
description: Informazioni su come usare Hive per analizzare i dati di Twitter con Hadoop in HDInsight per rilevare la frequenza d'uso di una parola specifica.
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 78e4ea33-9714-424d-ac07-3d60ecaebf2e
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: a5f97dfa084291cefde9bf27b5639926de1bc80e
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>Analizzare i dati di Twitter con Hive in HDInsight
I siti Web di social networking rappresentano una delle principali forze trainanti per l'adozione di Big Data. Le API pubbliche offerte da siti quali Twitter costituiscono un'utile origine di dati per l'analisi e la comprensione delle tendenze più popolari.
In questa esercitazione si userà l'API di streaming di Twitter per ricevere tweet e quindi si userà Apache Hive in Azure HDInsight per ottenere un elenco di utenti di Twitter che hanno inviato il maggior numero di tweet contenenti una determinata parola.

> [!IMPORTANT]
> I passaggi descritti in questo documento richiedono un cluster HDInsight basato su Windows. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Per passaggi specifici di un cluster basato su Linux, vedere [Analizzare i dati di Twitter con Hive in HDInsight (Linux)](hdinsight-analyze-twitter-data-linux.md).

## <a name="prerequisites"></a>prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* **Una workstation** in cui sia stato installato e configurato Azure PowerShell.

    Per eseguire script di Windows PowerShell, è necessario eseguire Azure PowerShell come amministratore e impostare i criteri di esecuzione su *RemoteSigned*. Vedere [Run Windows PowerShell scripts][powershell-script] (Eseguire script di Windows PowerShell).

    Prima di eseguire script di Windows PowerShell, assicurarsi di essere connessi alla sottoscrizione di Azure usando il cmdlet seguente:

    ```powershell
    Login-AzureRmAccount
    ```

    Se si hanno più sottoscrizioni di Azure, usare il cmdlet seguente per impostare la sottoscrizione corrente:

    ```powershell
    Select-AzureRmSubscription -SubscriptionID <Azure Subscription ID>
    ```

    > [!IMPORTANT]
    > Il supporto di Azure PowerShell per la gestione delle risorse HDInsight tramite Azure Service Manager è **deprecato** ed è stato rimosso dal 1° gennaio 2017. La procedura descritta in questo documento usa i nuovi cmdlet HDInsight, compatibili con Azure Resource Manager.
    >
    > Per installare la versione più recente, seguire la procedura descritta in [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) . Se sono presenti script che devono essere modificati per l'uso dei nuovi cmdlet compatibili con Azure Resource Manager, per altre informazioni vedere [Migrazione a strumenti di sviluppo basati su Azure Resource Manager per i cluster HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md) .

* **Un cluster HDInsight di Azure**. Per istruzioni sul provisioning del cluster, vedere [Introduzione a HDInsight][hdinsight-get-started] o [Effettuare il provisioning di cluster HDInsight][hdinsight-provision]. Il nome del cluster sarà necessario più avanti nell'esercitazione.

Nella tabella seguente vengono elencati i file usati nell'esercitazione:

| File | DESCRIZIONE |
| --- | --- |
| /tutorials/twitter/data/tweets.txt |I dati di origine per il processo Hive. |
| /tutorials/twitter/output |Cartella di output per il processo Hive. Il nome file di output del processo Hive relativo al file predefinito è **000000_0**. |
| tutorials/twitter/twitter.hql |File script HiveQL. |
| /tutorials/twitter/jobstatus |Stato del processo Hadoop. |

## <a name="get-twitter-feed"></a>Ricezione di feed Twitter
In questa esercitazione verranno usate le [API di streaming di Twitter][twitter-streaming-api]. L'API di streaming di Twitter specifica che verrà usata è [statuses/filter][twitter-statuses-filter].

> [!NOTE]
> Un file contenente 10.000 tweet e il file di script Hive (descritto nella sezione successiva) sono stati caricati in un contenitore BLOB pubblico. Se si preferisce usare i file caricati, è possibile ignorare questa sezione.

[I dati dei tweet](https://dev.twitter.com/docs/platform-objects/tweets) vengono archiviati nel formato JSON (JavaScript Object Notation) che include una struttura annidata complessa. Invece di scrivere molte righe di codice usando un linguaggio di programmazione convenzionale, è possibile trasformare la struttura annidata in una tabella Hive, in modo da consentire l'esecuzione di query tramite un linguaggio analogo a SQL ( Structured Query Language), denominato HiveQL.

Twitter usa OAuth per fornire accesso autorizzato alla propria API. OAuth è un protocollo di autenticazione che consente agli utenti di autorizzare l'applicazione ad agire per proprio conto senza divulgare la propria password. Altre informazioni sono disponibili su [oauth.net](http://oauth.net/) o nell'ottima guida [Beginner's Guide to OAuth](http://hueniverse.com/oauth/) di Hueniverse.

Il primo passaggio da seguire per usare OAuth consiste nel creare una nuova applicazione sul sito Twitter Developers.

**Per creare un'applicazione Twitter**

1. Accedere a [https://apps.twitter.com/](https://apps.twitter.com/). Se non si dispone di un account Twitter, fare clic sul collegamento **Sign up now** .
2. Fare clic su **Create New App**.
3. Compilare i campi **Name**, **Description**, **Website**. Per il campo **Website** è possibile creare un URL fittizio. Nella tabella seguente vengono mostrati alcuni valori di esempio da usare:

   | Campo | Valore |
   | --- | --- |
   |  NOME |MyHDInsightApp |
   |  DESCRIZIONE |MyHDInsightApp |
   |  Website |http://www.myhdinsightapp.com |
4. Fare clic su **Yes, I agree** e su **Create your Twitter application**.
5. Fare clic sulla scheda **Permissions** . L'autorizzazione predefinita è **Read only**. Questo livello di autorizzazione è sufficiente per l'esercitazione.
6. Fare clic sulla scheda **Keys and Access Tokens** .
7. Fare clic su **Create my access token**.
8. Fare clic su **Test OAuth** nell'angolo superiore destro della pagina.
9. Compilare i campi **Consumer key**, **Consumer secret**, **Access token** e **Access token secret**. Sarà necessario usare questi valori più avanti nell'esercitazione.

In questa esercitazione si usa Windows PowerShell per effettuare la chiamata al servizio Web. L'altro strumento di ampia diffusione per effettuare chiamate di servizi Web è [*Curl*][curl]. Curl può essere scaricato da [questa pagina][curl-download].

> [!NOTE]
> Quando si usa il comando curl in Windows, per i valori delle opzioni usare le virgolette doppie invece di quelle singole.

**Per ricevere tweet**

1. Aprire Windows PowerShell Integrated Scripting Environment (ISE). Nella schermata Start di Windows 8 digitare **PowerShell_ISE** e quindi fare clic su **Windows PowerShell ISE**. Vedere [Start Windows PowerShell on Windows 8 and Windows][powershell-start] (Avviare Windows PowerShell in Windows 8 e Windows).
2. Copiare lo script seguente nel riquadro di script:

    ```powershell
    #region - variables and constants
    $clusterName = "<HDInsightClusterName>" # Enter the HDInsight cluster name

    # Enter the OAuth information for your Twitter application
    $oauth_consumer_key = "<TwitterAppConsumerKey>";
    $oauth_consumer_secret = "<TwitterAppConsumerSecret>";
    $oauth_token = "<TwitterAppAccessToken>";
    $oauth_token_secret = "<TwitterAppAccessTokenSecret>";

    $destBlobName = "tutorials/twitter/data/tweets.txt" # This script saves the tweets into this blob.

    $trackString = "Azure, Cloud, HDInsight" # This script gets the tweets containing these keywords.
    $track = [System.Uri]::EscapeDataString($trackString);
    $lineMax = 10000  # The script will get this number of tweets. It is about 3 minutes every 100 lines.
    #endregion

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    Login-AzureRmAccount
    #endregion

    #region - Create a block blob object for writing tweets into Blob storage
    Write-Host "Get the default storage account name and Blob container name using the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -Name $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $storageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $containerName = $myCluster.DefaultStorageContainer
    Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow

    Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
    Write-Host "`tThe connection string is $storageConnectionString." -ForegroundColor Yellow

    Write-Host "Create block blob object ..." -ForegroundColor Green
    $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
    $storageClient = $storageAccount.CreateCloudBlobClient();
    $storageContainer = $storageClient.GetContainerReference($containerName)
    $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
    #end region

    # region - Format OAuth strings
    Write-Host "Format oauth strings ..." -ForegroundColor Green
    $oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
    $ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
    $oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();

    $signature = "POST&";
    $signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
    $signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
    $signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
    $signature += [System.Uri]::EscapeDataString("track=" + $track);

    $signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);

    $hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
    $hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
    $oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));

    $oauth_authorization = 'OAuth ';
    $oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
    $oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
    $oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
    $oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
    $oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
    $oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
    $oauth_authorization += 'oauth_version="1.0"';

    $post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track);
    #endregion

    #region - Read tweets
    Write-Host "Create HTTP web request ..." -ForegroundColor Green
    [System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
    $request.Method = "POST";
    $request.Headers.Add("Authorization", $oauth_authorization);
    $request.ContentType = "application/x-www-form-urlencoded";
    $body = $request.GetRequestStream();

    $body.write($post_body, 0, $post_body.length);
    $body.flush();
    $body.close();
    $response = $request.GetResponse() ;

    Write-Host "Start stream reading ..." -ForegroundColor Green

    Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream

    $sReader = New-Object System.IO.StreamReader($response.GetResponseStream())

    $inrec = $sReader.ReadLine()
    $count = 0
    while (($inrec -ne $null) -and ($count -le $lineMax))
    {
        if ($inrec -ne "")
        {
            Write-Host "`n`t $count tweets received." -ForegroundColor Yellow

            $writeStream.WriteLine($inrec)
            $count ++
        }

        $inrec=$sReader.ReadLine()
    }
    #endregion

    #region - Write tweets to Blob storage
    Write-Host "Write to the destination blob ..." -ForegroundColor Green
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $destBlob.UploadFromStream($memStream)

    $sReader.close()
    #endregion

    Write-Host "Completed!" -ForegroundColor Green
    ```

3. Impostare le prime cinque-otto variabili dello script:

    Variabile|DESCRIZIONE
    ---|---
    $clusterName|Nome del cluster HDInsight in cui eseguire l'applicazione.
    $oauth_consumer_key|Applicazione Twitter **consumer key** scritta in precedenza al momento della creazione dell'applicazione Twitter.
    $oauth_consumer_secret|L'applicazione Twitter **consumer secret** scritta in precedenza.
    $oauth_token|L'applicazione Twitter **access token** scritta in precedenza.
    $oauth_token_secret|L'applicazione Twitter **access token secret** scritta in precedenza.
    $destBlobName|Il nome BLOB dell'output. Il valore predefinito è **tutorials/twitter/data/tweets.txt**. Se si modifica il valore predefinito, sarà necessario aggiornare gli script di Windows PowerShell di conseguenza.
    $trackString|Il servizio Web restituirà tweet correlati a queste parole chiave. Il valore predefinito è **Azure, Cloud, HDInsight**. Se si modifica il valore predefinito, si dovranno aggiornare gli script di Windows PowerShell di conseguenza.
    $lineMax|Il valore determina il numero di tweet che lo script leggerà. Per leggere 100 tweet sono necessari circa tre minuti. È possibile impostare un numero più elevato, ma il download richiederà più tempo.

1. Premere **F5** per eseguire lo script. In caso di problemi, come soluzione selezionare tutte le righe e premere **F8**.
2. Alla fine dell'output verrà visualizzato "Completato". Eventuali messaggi di errore verranno visualizzati in rosso.

Come procedura di convalida, è possibile controllare il file di output, **/tutorials/twitter/data/tweets.txt**, nell'archivio BLOB di Azure tramite uno strumento per l'esplorazione dei servizi di archiviazione di Azure oppure Azure PowerShell. Per uno script di Windows PowerShell di esempio per l'elenco dei file, vedere [Usare l'archivio BLOB di Azure con HDInsight][hdinsight-storage-powershell].

## <a name="create-hiveql-script"></a>Creare uno script HiveQL
Azure PowerShell consente di eseguire più istruzioni HiveQL contemporaneamente o di inserire l'istruzione HiveQL in un file di script. In questa esercitazione verrà creato uno script HiveQL. Il file di script deve essere caricato nell'archivio BLOB di Azure. Nella sezione successiva si eseguirà il file di script tramite Azure PowerShell.

> [!NOTE]
> Il file di script Hive e un file contenente 10.000 tweet sono stati caricati in un contenitore BLOB pubblico. Se si preferisce usare i file caricati, è possibile ignorare questa sezione.

Il file di script HiveQL eseguirà le operazioni seguenti:

1. **Eliminazione della tabella tweets_raw** nel caso in cui la tabella esista già.
2. **Creazione della tabella tweets_raw Hive**. Questa tabella Hive strutturata e temporanea contiene i dati per altri processi di estrazione, trasformazione e caricamento (ETL, Extraction, Transformation, and Loading). Per informazioni sulle partizioni, vedere [Hive tutorial][apache-hive-tutorial] (Esercitazione su Hive).
3. **Caricare i dati** dalla cartella di origine /tutorials/twitter/data. Il set di dati di tweet di grandi dimensioni in formato JSON annidato è stato trasformato in una struttura di tabella Hive temporanea.
4. **Eliminare la tabella tweets** nel caso esista già.
5. **Creare la tabella tweets**. Prima di eseguire query sul set di dati di tweet tramite Hive, è necessario eseguire un altro processo ETL. Il processo ETL definisce uno schema di tabella più dettagliato per i dati archiviati nella tabella "twitter_raw".
6. **Inserire la tabella di sovrascrittura**. Questo script Hive complesso avvia una serie di processi MapReduce dal cluster Hadoop. In base al set di dati e alla dimensione del cluster, tale operazione potrebbe richiedere circa 10 minuti.
7. **Inserire la directory di sovrascrittura**. Eseguire una query e inviare il set di dati a un file. Questa query restituirà un elenco di utenti di Twitter che hanno inviato il maggior numero di tweet contenenti la parola "Azure".

**Per creare uno script Hive e caricarlo in Azure**

1. Aprire Windows PowerShell ISE.
2. Copiare lo script seguente nel riquadro di script:

    ```powershell
    #region - variables and constants
    $clusterName = "<Existing HDInsight Cluster Name>" # Enter your HDInsight cluster name
    $subscriptionID = "<Azure Subscription ID>"

    $sourceDataPath = "/tutorials/twitter/data"
    $outputPath = "/tutorials/twitter/output"
    $hqlScriptFile = "tutorials/twitter/twitter.hql"

    $hqlStatements = @"
    set hive.exec.dynamic.partition = true;
    set hive.exec.dynamic.partition.mode = nonstrict;

    DROP TABLE tweets_raw;
    CREATE EXTERNAL TABLE tweets_raw (
        json_response STRING
    )
    STORED AS TEXTFILE LOCATION '$sourceDataPath';

    DROP TABLE tweets;
    CREATE TABLE tweets
    (
        id BIGINT,
        created_at STRING,
        created_at_date STRING,
        created_at_year STRING,
        created_at_month STRING,
        created_at_day STRING,
        created_at_time STRING,
        in_reply_to_user_id_str STRING,
        text STRING,
        contributors STRING,
        retweeted STRING,
        truncated STRING,
        coordinates STRING,
        source STRING,
        retweet_count INT,
        url STRING,
        hashtags array<STRING>,
        user_mentions array<STRING>,
        first_hashtag STRING,
        first_user_mention STRING,
        screen_name STRING,
        name STRING,
        followers_count INT,
        listed_count INT,
        friends_count INT,
        lang STRING,
        user_location STRING,
        time_zone STRING,
        profile_image_url STRING,
        json_response STRING
    );

    FROM tweets_raw
    INSERT OVERWRITE TABLE tweets
    SELECT
        cast(get_json_object(json_response, '$.id_str') as BIGINT),
        get_json_object(json_response, '$.created_at'),
        concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
        substr (get_json_object(json_response, '$.created_at'),27,4)),
        substr (get_json_object(json_response, '$.created_at'),27,4),
        case substr (get_json_object(json_response, '$.created_at'),5,3)
            when "Jan" then "01"
            when "Feb" then "02"
            when "Mar" then "03"
            when "Apr" then "04"
            when "May" then "05"
            when "Jun" then "06"
            when "Jul" then "07"
            when "Aug" then "08"
            when "Sep" then "09"
            when "Oct" then "10"
            when "Nov" then "11"
            when "Dec" then "12" end,
        substr (get_json_object(json_response, '$.created_at'),9,2),
        substr (get_json_object(json_response, '$.created_at'),12,8),
        get_json_object(json_response, '$.in_reply_to_user_id_str'),
        get_json_object(json_response, '$.text'),
        get_json_object(json_response, '$.contributors'),
        get_json_object(json_response, '$.retweeted'),
        get_json_object(json_response, '$.truncated'),
        get_json_object(json_response, '$.coordinates'),
        get_json_object(json_response, '$.source'),
        cast (get_json_object(json_response, '$.retweet_count') as INT),
        get_json_object(json_response, '$.entities.display_url'),
        array(
            trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
        array(
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
        trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
        trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
        get_json_object(json_response, '$.user.screen_name'),
        get_json_object(json_response, '$.user.name'),
        cast (get_json_object(json_response, '$.user.followers_count') as INT),
        cast (get_json_object(json_response, '$.user.listed_count') as INT),
        cast (get_json_object(json_response, '$.user.friends_count') as INT),
        get_json_object(json_response, '$.user.lang'),
        get_json_object(json_response, '$.user.location'),
        get_json_object(json_response, '$.user.time_zone'),
        get_json_object(json_response, '$.user.profile_image_url'),
        json_response
    WHERE (length(json_response) > 500);

    INSERT OVERWRITE DIRECTORY '$outputPath'
    SELECT name, screen_name, count(1) as cc
        FROM tweets
        WHERE text like "%Azure%"
        GROUP BY name,screen_name
        ORDER BY cc DESC LIMIT 10;
    "@
    #endregion

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green

    Try{
        Get-AzureRmSubscription
    }
    Catch{
        Login-AzureRmAccount
    }

    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    #endregion

    #region - Create a block blob object for writing the Hive script file
    Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow

    Write-Host "Define the connection string ..." -ForegroundColor Green
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"

    Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
    $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
    $storageClient = $storageAccount.CreateCloudBlobClient();
    $storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
    $hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)

    Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream
    $writeStream.Writeline($hqlStatements)
    #endregion

    #region - Write the Hive script file to Blob storage
    Write-Host "Write to the destination blob ... " -ForegroundColor Green
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $hqlScriptBlob.UploadFromStream($memStream)
    #endregion

    Write-Host "Completed!" -ForegroundColor Green
    ```

3. Impostare le prime due variabili nello script:

   | Variabile | DESCRIZIONE |
   | --- | --- |
   |  $clusterName |Immettere il nome del cluster HDInsight in cui eseguire l'applicazione. |
   |  $subscriptionID |Inserire L'ID della sottoscrizione di Azure. |
   |  $sourceDataPath |Percorso dell'archivio BLOB di Azure dal quale le query Hive leggeranno i dati. Non è necessario modificare questa variabile. |
   |  $outputPath |Percorso dell'archivio BLOB di Azure dal quale le query Hive restituiranno i risultati. Non è necessario modificare questa variabile. |
   |  $hqlScriptFile |Posizione e nome del file di script HiveQL. Non è necessario modificare questa variabile. |
4. Premere **F5** per eseguire lo script. In caso di problemi, come soluzione selezionare tutte le righe e premere **F8**.
5. Alla fine dell'output verrà visualizzato "Completato". Eventuali messaggi di errore verranno visualizzati in rosso.

Come procedura di convalida, è possibile controllare il file di output, **/tutorials/twitter/twitter.hql**, nell'archivio BLOB di Azure usando uno strumento per l'esplorazione dei servizi di archiviazione Azure oppure Azure PowerShell. Per uno script di Windows PowerShell di esempio per l'elenco dei file, vedere [Usare l'archivio BLOB di Azure con HDInsight][hdinsight-storage-powershell].

## <a name="process-twitter-data-by-using-hive"></a>Elaborare i dati di Twitter tramite Hive
Tutte le attività preliminari sono state completate. Ora è possibile richiamare lo script Hive script e controllare i risultati.

### <a name="submit-a-hive-job"></a>Inviare un processo Hive
Usare lo script PowerShell seguente per eseguire lo script Hive. È necessario impostare la prima variabile.

> [!NOTE]
> Per usare i tweet e lo script HiveQL caricati nelle ultime due sezioni, impostare $hqlScriptFile su "/tutorials/twitter/twitter.hql". Per usare quelli caricati in un BLOB pubblico per l'utente, impostare $hqlScriptFile su "wasb://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql".

```powershell
#region variables and constants
$clusterName = "<Existing Azure HDInsight Cluster Name>"
$httpUserName = "admin"
$httpUserPassword = "<HDInsight Cluster HTTP User Password>"

#use one of the following
$hqlScriptFile = "wasb://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"
$hqlScriptFile = "/tutorials/twitter/twitter.hql"

$statusFolder = "/tutorials/twitter/jobstatus"
#endregion

$myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $myCluster.ResourceGroup
$defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value

$defaultBlobContainerName = $myCluster.DefaultStorageContainer

#region - Invoke Hive
Write-Host "Invoke Hive ... " -ForegroundColor Green

# Create the HDInsight cluster
$pw = ConvertTo-SecureString -String $httpUserPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

Use-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $httpCredential
$response = Invoke-AzureRmHDInsightHiveJob -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -file $hqlScriptFile -StatusFolder $statusFolder #-OutVariable $outVariable

Write-Host "Display the standard error log ... " -ForegroundColor Green
$jobID = ($response | Select-String job_ | Select-Object -First 1) -replace ‘\s*$’ -replace ‘.*\s’
Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $jobID -DefaultContainer $defaultBlobContainerName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -HttpCredential $httpCredential
#endregion
```

### <a name="check-the-results"></a>Controllare i risultati
Usare lo script di Windows PowerShell seguente per controllare l'output del processo Hive. È necessario impostare le prime due variabili.

```powershell
#region variables and constants
$clusterName = "<Existing Azure HDInsight Cluster Name>"

$blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
#endregion

#region - Create an Azure storage context object
Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
$myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $myCluster.ResourceGroup
$defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
$defaultBlobContainerName = $myCluster.DefaultStorageContainer

Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow

Write-Host "Create a context object ... " -ForegroundColor Green
$storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
#endregion

#region - Download blob and display blob
Write-Host "Download the blob ..." -ForegroundColor Green
cd $HOME
Get-AzureStorageBlobContent -Container $defaultBlobContainerName -Blob $blob -Context $storageContext -Force

Write-Host "Display the output ..." -ForegroundColor Green
Write-Host "==================================" -ForegroundColor Green
cat "./$blob"
Write-Host "==================================" -ForegroundColor Green
#end region
```

> [!NOTE]
> La tabella Hive usa \001 come delimitatore di campo. Il delimitatore non è visibile nell'output.

Dopo che i risultati dell'analisi sono stati inserivi in un archivio BLOB di Azure, è possibile esportare i dati in un database SQL o un server SQL di Azure, esportare i dati in Excel tramite Power Query oppure connettere l'applicazione ai dati usando Hive ODBC Driver. Per altre informazioni, vedere [Usare Sqoop con Hadoop in HDInsight][hdinsight-use-sqoop], [Analizzare i dati sui ritardi dei voli con HDInsight][hdinsight-analyze-flight-delay-data], [Connettere Excel a HDInsight mediante Power Query][hdinsight-power-query] e [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver][hdinsight-hive-odbc].

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato illustrato come trasformare un set di dati JSON non strutturato in una tabella Hive strutturata per eseguire query, esplorare e analizzare dati da Twitter tramite HDInsight in Azure. Per altre informazioni, vedere:

* [Introduzione a HDInsight][hdinsight-get-started]
* [Analizzare i dati sui ritardi dei voli con HDInsight][hdinsight-analyze-flight-delay-data]
* [Connettere Excel a HDInsight mediante Power Query][hdinsight-power-query]
* [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver][hdinsight-hive-odbc]
* [Usare Sqoop con Hadoop in HDInsight][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176961.aspx

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]:hdinsight-hadoop-use-blob-storage.md#access-blobs-using-azure-powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-power-query]:hadoop/apache-hadoop-connect-excel-power-query.md
[hdinsight-hive-odbc]:hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md
