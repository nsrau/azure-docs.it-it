<properties linkid="manage-services-hdinsight-howto-social-data" urlDisplayName="Analyze Twitter data with Hive" pageTitle="Analyze Twitter data with HDinsight | Azure" metaKeywords="" description="Learn how to use Hive to analyze Twitter data on HDInsight to find usage frequency of a particular word." metaCanonical="" services="HDInsight" documentationCenter="" title="Analyze Twitter data with Hive" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

Analisi dei dati di Twitter con HDInsight
=========================================

Informazioni su come analizzare i dati di Twitter utilizzando Hive con HDInsight.

I siti Web di social networking rappresentano una delle principali forze trainanti per l'adozione di Big Data. Le API pubbliche offerte da siti quali Twitter costituiscono un'utile origine di dati per l'analisi e la comprensione delle tendenze più popolari. In questo tutorial verrà effettuata la connessione al servizio Web Twitter per ricevere alcuni tweet utilizzando l'API di streaming di Twitter, quindi verrà utilizzato Hive per ottenere un elenco di utenti Twitter che hanno inviato il numero maggiore di tweet contenenti una determinata parola.

**Tempo previsto per il completamento:** 30 minuti

Contenuto dell'articolo
-----------------------

-   [Prerequisiti](#prerequisites)
-   [Ricezione di feed Twitter](#feed)
-   [Creazione di uno script HiveQL](#script)
-   [Elaborazione dei dati utilizzando Hive](#process)
-   [Pulitura dell'esercitazione](#cleanup)
-   [Passaggi successivi](#nextsteps)

Prerequisiti
------------

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

-   **Una workstation** in cui sia stato installato e configurato Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell](../install-configure-powershell). Per eseguire script di PowerShell, è necessario eseguire Azure PowerShell come amministratore e impostare i criteri di esecuzione su *RemoteSigned*. Vedere la pagina relativa all'[esecuzione di script di Windows PowerShell](http://technet.microsoft.com/en-us/library/ee176949.aspx).

    Prima di eseguire script PowerShell, assicurarsi di essere connessi alla sottoscrizione di Azure utilizzando il seguente cmdlet:

         Add-AzureAccount

    Se si dispone di più sottoscrizioni di Azure, utilizzare il seguente cmdlet per impostare la sottoscrizione corrente:

          Select-AzureSubscription <WindowsAzureSubscirptionName>

-   **Un cluster HDInsight di Azure**. Per istruzioni sul provisioning del cluster, vedere [Introduzione a HDInsight](../hdinsight-get-started/) o [Provisioning di cluster HDInsight](../hdinsight-provision-clusters/). Per completare l'esercitazione sono necessari i dati seguenti:

    <table border="1">
	<tr><th>Proprietà del cluster</th><th>Nome variabile di PowerShell</th><th>Valore</th><th>Descrizione</th></tr>
	<tr><td>Nome del cluster HDInsight</td><td>\$clusterName</td><td></td><td>Corrisponde al nome del cluster HDInsight.</td></tr>
    <tr><td>Nome dell'account di archiviazione di Azure</td><td>\$storageAccountName</td><td></td><td>Un account di archiviazione di Azure disponibile per il cluster HDInsight. Ai fini di questa esercitazione, utilizzare l'account di archiviazione predefinito, specificato durante il processo di provisioning del cluster.</td></tr>
    <tr><td>Nome del contenitore BLOB di Azure</td><td>\$containerName</td><td></td><td>Per questo esempio, utilizzare il contenitore di archiviazione BLOB di Azure utilizzato come file system predefinito del cluster HDInsight. Per impostazione predefinita, il nome del contenitore corrisponde al nome del cluster HDInsight.</td></tr>
	</table>

**Informazioni sull'archiviazione in HDInsight**

HDInsight utilizza l'archivio BLOB di Azure per l'archiviazione dei dati. Questa risorsa è denominata *WASB* o *Archiviazione BLOB di Azure*. WASB è l'implementazione Microsoft di HDFS sull'archivio BLOB di Azure. Per ulteriori informazioni, vedere [Utilizzo dell'archivio BLOB di Azure con HDInsight](../hdinsight-use-blob-storage/).

Durante il provisioning di un cluster HDInsight, un contenitore di archiviazione BLOB viene designato come file system predefinito, come in HDFS. Oltre a tale contenitore, è possibile aggiungere ulteriori contenitori dallo stesso account di archiviazione di Azure o da account di archiviazione di Azure diversi durante il processo di provisioning. Per istruzioni sull'aggiunta di ulteriori account di archiviazione, vedere [Provisioning di cluster HDInsight](../hdinsight-provision-clusters/).

Per semplificare lo script di PowerShell utilizzato in questa esercitazione, tutti i file vengono archiviati nel contenitore del file system predefinito, che si trova in */tutorials/twitter*. Per impostazione predefinita, il nome di questo contenitore corrisponde al nome del cluster HDInsight.

La sintassi WASB è la seguente:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Nella versione 3.0 del cluster HDInsight è supportata solo la sintassi *wasb://*. La sintassi *asv://*, meno recente, è supportata nei cluster HDInsight 2.1 e 1.6, ma non è supportata nei cluster HDInsight 3.0 e non sarà supportata nelle versioni successive.

> Il percorso di WASB è un percorso virtuale. Per ulteriori informazioni, vedere [Utilizzo dell'archivio BLOB di Azure con HDInsight](../hdinsight-use-blob-storage/).

È possibile accedere da HDInsight a un file archiviato nel contenitore del file system predefinito utilizzando uno degli URI seguenti (utilizzare come esempio tweets.txt):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/twitter/tweets.txt
    wasb:///tutorials/twitter/tweets.txt
    /tutorials/twitter/tweets.txt

Per accedere al file direttamente dall'account di archiviazione, il nome del BLOB per questo file è:

    tutorials/twitter/tweets.txt

Nella tabella seguente vengono elencati i file utilizzati nell'esercitazione:

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">File</th><th data-morhtml="true">Descrizione</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">/tutorials/twitter/data/tweets.txt</td><td data-morhtml="true">I dati di origine per il processo Hive.</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">/tutorials/twitter/output</td><td data-morhtml="true">La cartella di output per il processo Hive. Il nome file di output del processo Hive relativo al file predefinito &egrave; <strong data-morhtml="true">000000_0</strong>. </td></tr>
<tr data-morhtml="true"><td data-morhtml="true">tutorials/twitter/twitter.hql</td><td data-morhtml="true">Il file script HiveQL.</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">/tutorials/twitter/jobstatus</td><td data-morhtml="true">Lo stato del processo Hadoop.</td></tr>
</table>

Ricezione di feed Twitter
-------------------------

In questa esercitazione verranno utilizzate le [API di streaming di Twitter](https://dev.twitter.com/docs/streaming-apis). L'API di streaming di Twitter specifica che verrà utilizzata è [statuses/filter](https://dev.twitter.com/docs/api/1.1/post/statuses/filter).

[I](https://dev.twitter.com/docs/platform-objects/tweets)dati dei tweet vengono archiviati nel formato JSon, che include una struttura nidificata complessa. Invece di scrivere molte righe di codice utilizzando un linguaggio di programmazione convenzionale, tale struttura nidificata verrà trasformata in una tabella Hive, in modo da consentire l'esecuzione di query da parte di un linguaggio analogo a SQL, denominato HQL.

Twitter utilizza OAuth per fornire accesso autorizzato alla propria API. OAuth è un protocollo di autenticazione che consente agli utenti di autorizzare l'applicazione ad agire per proprio conto senza divulgare la propria password. Ulteriori informazioni sono disponibili su [oauth.net](http://oauth.net/) o nell'ottima guida [Beginner's Guide to OAuth](http://hueniverse.com/oauth/) di Hueniverse.

Il primo passaggio da seguire per utilizzare OAuth consiste nel creare una nuova applicazione sul sito Twitter Developers.

**Per creare un'applicazione Twitter**

1.  Accedere a <https://apps.twitter.com/>. Se non si dispone di un account Twitter, fare clic sul collegamento **Sign up now**.
2.  Fare clic su **Create New App**.
3.  Compilare i campi **Name**, **Description**, **Website**. Per il campo Website è possibile creare un URL fittizio. Nella tabella seguente vengono mostrati alcuni valori di esempio da utilizzare:

    <table data-morhtml="true" border="1">
	<tr data-morhtml="true"><th data-morhtml="true">Campo</th><th data-morhtml="true">Valore</th></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Nome</td><td data-morhtml="true">MyHDInsightApp</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Descrizione</td><td data-morhtml="true">MyHDInsightApp</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Sito Wen</td><td data-morhtml="true">http://www.myhdinsightapp.com</td></tr>
	</table>

4.  Fare clic su **Yes, I agree**, quindi scegliere **Create your Twitter application**.
5.  Fare clic sulla scheda **Permissions**. L'autorizzazione predefinita è **Read only**. Questo livello di autorizzazione è sufficiente per l'esercitazione.
6.  Fare clic sulla scheda **API Keys**.
7.  Fare clic su **Create my access token**.
8.  Fare clic su **Test OAuth** nell'angolo superiore destro della pagina.
9.  Compilare i campi **consumer key**, **Consumer secret**, **Access token** e **Access token secret**. Sarà necessario utilizzare questi valori più avanti nell'esercitazione.

In questa esercitazione verrà utilizzato PowerShell per effettuare una chiamata del servizio Web. L'altro strumento di ampia diffusione per effettuare chiamate di servizi Web è [*Curl*](http://curl.haxx.se). Curl può essere scaricato [da questa pagina](http://curl.haxx.se/download.html).

> [WACOM.NOTE] Quando si utilizza il comando curl in Windows, per i valori delle opzioni sostituire le virgolette alle virgolette singole.

**Per ricevere tweet**

1.  Aprire Windows PowerShell ISE (nella schermata Start di Windows 8 digitare **PowerShell\_ISE** e quindi fare clic su **Windows PowerShell ISE**. Vedere la pagina dedicata all'[avvio di Windows PowerShell in Windows 8 e Windows](http://technet.microsoft.com/en-us/library/hh847889.aspx)).

2.  Copiare lo script seguente nel riquadro di script:

         Write-Host "Set variables ..." -ForegroundColor Green
         $storageAccountName = "<WindowsAzureStorageAccountName>"
         $containerName = "<BlobContainerName>"

         $destBlobName = "tutorials/twitter/data/tweets.txt"
            
         $trackString = "Azure, WindowsAzure, Cloud, HDInsight"
         $lineMax = 100  #about 3 minutes every 100 lines
            
         $oauth_consumer_key = "<TwitterAppConsumerKey>";
         $oauth_consumer_secret = "<TwitterAppConsumerSecret>";
         $oauth_token = "<TwitterAppAccessToken>";
         $oauth_token_secret = "<TwitterAppAccessTokenSecret>";
            
         Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
         $storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
         $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
            
         Write-Host "Create block blob object ..." -ForegroundColor Green
         $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
         $storageClient = $storageAccount.CreateCloudBlobClient();
         $storageContainer = $storageClient.GetContainerReference($containerName)
         $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
            
         Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
         $memStream = New-Object System.IO.MemoryStream
         $writeStream = New-Object System.IO.StreamWriter $memStream
            
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
            
         $track = [System.Uri]::EscapeDataString($trackString);
         $post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track); 
                    
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
            
         $sReader = New-Object System.IO.StreamReader($response.GetResponseStream())
            
         $inrec = $sReader.ReadLine()
         $count = 0
         while (($inrec -ne $null) -and ($count -le $lineMax))
         {
             if ($inrec -ne "")
             {
                 Write-Host $count.ToString() ", " -NoNewline -ForegroundColor Green
                 if ($count%10 -eq 0){
                     write-host " --- " -NoNewline
                     Get-Date -Format hh:mm:sstt
                 }
            
                 $writeStream.WriteLine($inrec)
                 $count ++
             }
            
             $inrec=$sReader.ReadLine()
         }
            
         Write-Host "Write to the destination blob ..." -ForegroundColor Green
         $writeStream.Flush()
         $memStream.Seek(0, "Begin")
         $destBlob.UploadFromStream($memStream) 
            
         $sReader.close()
         Write-Host "Complete!" -ForegroundColor Green

3.  Impostare le prime nove variabili nello script:

    <table data-morhtml="true" border="1">
	<tr data-morhtml="true"><th data-morhtml="true">Variabile</th><th data-morhtml="true">Descrizione</th></tr>
	<tr data-morhtml="true"><td data-morhtml="true">$storageAccountName</td><td data-morhtml="true">L'account di archiviazione di Azure utilizzato per il file system predefinito del cluster HDInsight. L'account di archiviazione specificato al momento del provisioning. Vedere <a data-morhtml="true" href="#prerequisites">Prerequisiti</a>.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">$containerName</td><td data-morhtml="true">Il contenitore BLOB utilizzato per il file system del cluster HDInsight. Il contenitore specificato al momento del provisioning. Vedere <a data-morhtml="true" href="#prerequisites">Prerequisiti</a>.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">$destBlobName</td><td data-morhtml="true">Il nome BLOB dell'output.  Il valore predefinito &egrave; <strong data-morhtml="true">tutorials/twitter/data/tweets.txt</strong>. Se si modifica il valore predefinito, sar&agrave; necessario aggiornare gli script PowerShell di conseguenza.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">$trackString</td><td data-morhtml="true">Il servizio Web restituir&agrave; tweet correlati a queste parole chiave.  Il valore predefinito &egrave; <strong data-morhtml="true">Azure, WindowsAzure, Cloud, HDInsight</strong>. Se si modifica il valore predefinito, sar&agrave; necessario aggiornare gli script PowerShell di conseguenza.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">$lineMax</td><td data-morhtml="true">Il valore determina il numero di tweet che lo script legger&agrave;. Per leggere 100 tweet sono necessari circa tre minuti. &Egrave; possibile impostare un numero pi&ugrave; elevato, ma il download richieder&agrave; pi&ugrave; tempo.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">$oauth_consumer_key</td><td data-morhtml="true">L'applicazione Twitter <strong data-morhtml="true">consumer key</strong> scritta in precedenza al momento della creazione dell'applicazione Twitter.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">$oauth_consumer_secret</td><td data-morhtml="true">L'applicazione Twitter <strong data-morhtml="true">consumer secret</strong> scritta in precedenza.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">$oauth_token</td><td data-morhtml="true">L'applicazione Twitter <strong data-morhtml="true">access token</strong> scritta in precedenza.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">$oauth_token_secret</td><td data-morhtml="true">L'applicazione Twitter <strong data-morhtml="true">access token secret</strong> scritta in precedenza.</td></tr>
	</table>

4.  Premere **F5** per eseguire lo script. In caso di problemi, come soluzione selezionare tutte le righe e premere **F8**.
5.  Alla fine dell'output verrà visualizzato il testo "Complete!". Eventuali messaggi di errore verranno visualizzati in rosso.

Come procedura di convalida, è possibile controllare il file di output, **/tutorials/twitter/data/tweets.txt**, nell'archivio BLOB di Azure utilizzando uno strumento per l'esplorazione dei servizi di archiviazione Azure oppure Azure PowerShell. Per uno script PowerShell di esempio per l'elenco dei file, vedere [Utilizzo dell'archiviazione BLOB con HDInsight](../hdinsight-use-blob-storage/#powershell).

Creazione di uno script HiveQL
------------------------------

Azure PowerShell consente di eseguire più istruzioni HiveQL contemporaneamente o di inserire l'istruzione HiveQL in un file di script. In questa esercitazione verrà creato uno script HiveQL. Il file di script deve essere caricato in WASB. Nella sezione successiva il file di script verrà eseguito mediante Azure PowerShell.

Il file di script HiveQL eseguirà le operazioni seguenti:

1.  **Eliminazione della tabella tweets\_raw** nel caso in cui la tabella esista già.
2.  **Creazione della tabella tweets\_raw Hive**. Questa tabella strutturata Hive temporanea contiene i dati per ulteriori operazioni di elaborazione ETL. Per informazioni sulla partizione vedere l'[esercitazione su Hive](https://cwiki.apache.org/confluence/display/Hive/Tutorial).
3.  **Caricare i dati** dalla cartella di origine /tutorials/twitter/data. Il set di dati di tweet di grandi dimensioni in formato JSon nidificato è stato ora trasformato in una struttura di tabella Hive temporanea.
4.  **Eliminazione della tabella tweets** nel caso in cui la tabella esista già.
5.  **Creazione della tabella tweets**. Prima di eseguire query sul set di dati di tweet utilizzando Hive, è necessario eseguire un altro processo ETL. Il processo ETL definisce uno schema di tabella più dettagliato per i dati archiviati nella tabella "twitter\_raw".
6.  **Inserimento della tabella di sovrascrittura**. Questo script complesso di Hive consente di avviare una serie di processi Map Reduce dal cluster Hadoop. In base al set di dati e alla dimensione del cluster, tale operazione potrebbe richiedere circa 10 minuti.
7.  **Inserimento della directory di sovrascrittura**. Esecuzione di una query e invio del set di dati a un file. Questa query restituirà un elenco di utenti di Twitter che hanno inviato il numero maggiore di tweet contenenti la parola "Azure".

**Per creare uno script Hive e caricarlo in Azure**

1.  Aprire Windows PowerShell ISE.
2.  Copiare lo script seguente nel riquadro di script:

         Write-Host "Define variables ..." -ForegroundColor Green
         $storageAccountName = "<WindowsAzureStorageAccountName>"
         $containerName = "<BlobContainerName>"
            
         $sourceDataPath = "/tutorials/twitter/data"
         $outputPath = "/tutorials/twitter/output"
            
         $hqlScriptFile = "tutorials/twitter/twitter.hql"
            
         $hqlStatements = @"
         set hive.exec.dynamic.partition = true;
         set hive.exec.dynamic.partition.mode = nonstrict;
            
         DROP TABLE tweets_raw;
         CREATE TABLE tweets_raw (
             json_response STRING
         ) 
         PARTITIONED BY (filesequence INT);
            
         LOAD DATA INPATH '$sourceDataPath'
         INTO TABLE tweets_raw
         PARTITION (filesequence = 1);
            
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
         )
         PARTITIONED BY (filesequence INT);
            
         FROM tweets_raw
         INSERT OVERWRITE TABLE tweets
         PARTITION (filesequence)
         SELECT
             cast(get_json_object(json_response, '$.id_str') as BIGINT),
             get_json_object(json_response, '$.created_at'),
             concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
             substr (get_json_object(json_response, '$.created_at'),27,4)),
             substr (get_json_object(json_response, '$.created_at'),27,4),
             case substr (get_json_object(json_response,    '$.created_at'),5,3)
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
             json_response,
             filesequence
         WHERE (length(json_response) > 500);
            
         INSERT OVERWRITE DIRECTORY '$outputPath'
         SELECT name, screen_name, count(1) as cc 
             FROM tweets 
             WHERE text like "%Azure%" 
             GROUP BY name,screen_name 
             ORDER BY cc DESC LIMIT 10;
         "@
            
         Write-Host "Define the connection string ..." -ForegroundColor Green
         $storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
         $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
            
         Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
         $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
         $storageClient = $storageAccount.CreateCloudBlobClient();
         $storageContainer = $storageClient.GetContainerReference($containerName)
         $hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)
            
         Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
         $memStream = New-Object System.IO.MemoryStream
         $writeStream = New-Object System.IO.StreamWriter $memStream
         $writeStream.Writeline($hqlStatements)
            
         Write-Host "Write to the destination blob ... " -ForegroundColor Green
         $writeStream.Flush()
         $memStream.Seek(0, "Begin")
         $hqlScriptBlob.UploadFromStream($memStream)
            
         Write-Host "Complete!" -ForegroundColor Green

3.  Impostare le prime due variabili nello script:

    <table data-morhtml="true" border="1">
 <tr data-morhtml="true"><th data-morhtml="true">Variabile</th><th data-morhtml="true">Descrizione</th></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$storageAccountName</td><td data-morhtml="true">L'account di archiviazione di Azure utilizzato per il file system predefinito del cluster HDInsight. L'account di archiviazione specificato al momento del provisioning. Vedere <a data-morhtml="true" href="#prerequisites">Prerequisiti</a>.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$containerName</td><td data-morhtml="true">Il contenitore BLOB utilizzato per il file system del cluster HDInsight. Il contenitore specificato al momento del provisioning. Vedere <a data-morhtml="true" href="#prerequisites">Prerequisiti</a>.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$sourceDataPath</td><td data-morhtml="true">Il percorso WASB dal quale le query Hive effettueranno la lettura dei dati. Non &egrave; necessario modificare questa variabile.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$outputPath</td><td data-morhtml="true">Il percorso WASB al quale le query Hive invieranno i risultati. Non &egrave; necessario modificare questa variabile.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$hqlScriptFile</td><td data-morhtml="true">La posizione e il nome del file relativo allo script HiveQL.</td></tr>
 </table>

4.  Premere **F5** per eseguire lo script. In caso di problemi, come soluzione selezionare tutte le righe e premere **F8**.
5.  Alla fine dell'output verrà visualizzato il testo "Complete!". Eventuali messaggi di errore verranno visualizzati in rosso.

Come procedura di convalida, è possibile controllare il file di output, **/tutorials/twitter/twitter.hql**, nell'archivio BLOB di Azure utilizzando uno strumento per l'esplorazione dei servizi di archiviazione Azure oppure Azure PowerShell. Per uno script PowerShell di esempio per l'elenco dei file, vedere [Utilizzo dell'archiviazione BLOB con HDInsight](../hdinsight-use-blob-storage/#powershell).

Elaborazione dei dati di Twitter utilizzando Hive
-------------------------------------------------

Tutte le attività preliminari sono state completate. Ora è possibile richiamare lo script Hive script e controllare i risultati.

### Invio di un processo Hive

Utilizzare lo script PowerShell seguente per eseguire lo script Hive. È necessario impostare la prima variabile.

    Write-Host "Set the variables ... " -ForegroundColor Green
    $clusterName = "<HDInsightClusterName>"

    $hqlScriptFile = "/tutorials/twitter/twitter.hql"
    $statusFolder = "/tutorials/twitter/jobstatus"

    Write-Host "Invoke Hive ... " -ForegroundColor Green
    Use-AzureHDInsightCluster $clusterName
    $response = Invoke-Hive -file $hqlScriptFile -StatusFolder $statusFolder -OutVariable $outVariable

    Write-Host "Display the standard error log ... " -ForegroundColor Green
    $jobID = ($response | Select-String job_ | Select-Object -First 1) -replace â€˜\s*$â€™ -replace â€˜.*\sâ€™
    Get-AzureHDInsightJobOutput -cluster $clusterName -JobId $jobID -StandardError 

### Controllo dei risultati

Utilizzare lo script PowerShell seguente per controllare l'output del processo Hive. È necessario impostare le prime due variabili.

    Write-Host "Set the variables ... " -ForegroundColor Green
    $storageAccountName = "<WindowsAzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.

    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force

    Write-Host "Display the output ..." -ForegroundColor Green
    cat "./$blob"

> [WACOM.NOTE] La tabella Hive utilizza \\001 come delimitatore di campo. Il delimitatore non è visibile nell'output.

Dopo che i risultati dell'analisi sono stati posizionati in WASB, è possibile esportare i dati nel database Azure SQL/server SQL, esportare i dati in Excel utilizzando Power Query oppure connettere l'applicazione ai dati utilizzando il driver Hive ODBC. Per ulteriori informazioni, vedere [Utilizzo di Sqoop con HDInsight](../hdinsight-use-sqoop/), [Analisi dei dati sui ritardi dei voli utilizzando HDInsight](../hdinsight-analyze-flight-delay-data/), [Connessione di Excel a HDInsight mediante Power Query](../hdinsight-connect-excel-power-query/) e [Connessione di Excel a HDInsight mediante Microsoft Hive ODBC Driver](../hdinsight-connect-excel-hive-ODBC-driver/).

Pulitura dell'esercitazione
---------------------------

Nel caso in cui si desideri ripetere l'esecuzione dell'esercitazione è necessario:

-   **Ricreare il file di dati dei tweet**. Il file di dati dei tweet è stato rimosso dal processo Hive. È necessario generarne uno nuovo. Il nome file è **tutorials/twitter/data/tweets.txt**. Vedere [Ricezione di feed Twitter](#feed).

Passaggi successivi
-------------------

In questa esercitazione è stato illustrato come trasformare un set di dati Json non strutturato in una tabella Hive strutturata per consentire l'esecuzione di query, l'esplorazione e l'analisi di dati da Twitter tramite HDInsight in Azure. Per ulteriori informazioni, vedere:

-   [Introduzione all'utilizzo di HDInsight](../hdinsight-get-started/)
-   [Analisi dei dati sui ritardi dei voli utilizzando HDInsight](../hdinsight-analyze-flight-delay-data/)
-   [Connessione di Excel a HDInsight mediante Power Query](../hdinsight-connect-excel-power-query/)
-   [Connessione di Excel a HDInsight mediante Microsoft Hive ODBC Driver](../hdinsight-connect-excel-hive-ODBC-driver/)
-   [Utilizzo di Sqoop con HDInsight.](../hdinsight-use-sqoop/)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/en-us/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell
[powershell-script]: http://technet.microsoft.com/en-us/library/ee176949.aspx

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage/#powershell
[hdinsight-analyze-flight-delay-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/
[hdinsight-hive-odbc]: ../hdinsight-connect-excel-hive-ODBC-driver/

