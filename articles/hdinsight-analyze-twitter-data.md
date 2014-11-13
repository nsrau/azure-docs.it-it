<properties urlDisplayName="Analyze Twitter data with HDInsight Hadoop" pageTitle="Analizzare i dati di Twitter con Hadoop in HDInsight | Azure" metaKeywords="" description="Informazioni su come usare Hive per analizzare i dati di Twitter con Hadoop in HDInsight per rilevare la frequenza d'uso di una parola specifica." metaCanonical="" services="HDInsight" documentationCenter="" title="Analisi dei dati di Twitter con Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Analisi dei dati di Twitter con Hadoop in HDInsight

Informazioni su come analizzare i dati di Twitter con Hadoop usando Hive in HDInsight.

I siti Web di social networking rappresentano una delle principali forze trainanti per l'adozione di Big Data. Le API pubbliche offerte da siti quali Twitter costituiscono un'utile origine di dati per l'analisi e la comprensione delle tendenze più popolari. In questo tutorial verrà effettuata la connessione al servizio Web Twitter per ricevere alcuni tweet usando l'API di streaming di Twitter, quindi verrà usato Hive per ottenere un elenco di utenti Twitter che hanno inviato il numero maggiore di tweet contenenti una determinata parola.

**Tempo previsto per il completamento:** 30 minuti

## Contenuto dell'articolo

-   [Prerequisiti][Prerequisiti]
-   [Ricezione di feed Twitter][Ricezione di feed Twitter]
-   [Creazione di uno script HiveQL][Creazione di uno script HiveQL]
-   [Elaborazione dei dati usando Hive][Elaborazione dei dati usando Hive]
-   [Pulitura dell'esercitazione][Pulitura dell'esercitazione]
-   [Passaggi successivi][Passaggi successivi]

## <span id="prerequisites"></span></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

-   **Una workstation** in cui sia stato installato e configurato Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][Come installare e configurare Azure PowerShell]. Per eseguire script di PowerShell, è necessario eseguire Azure PowerShell come amministratore e impostare i criteri di esecuzione su *RemoteSigned*. Vedere la pagina relativa all'[esecuzione di script di Windows PowerShell][esecuzione di script di Windows PowerShell].

    Prima di eseguire script PowerShell, assicurarsi di essere connessi alla sottoscrizione di Azure usando il seguente cmdlet:

        Add-AzureAccount

    Se si dispone di più sottoscrizioni di Azure, usare il seguente cmdlet per impostare la sottoscrizione corrente:

        Select-AzureSubscription <AzureSubscriptionName>

-   **Un cluster HDInsight di Azure**. Per istruzioni sul provisioning del cluster, vedere [Introduzione all'uso di HDInsight][Introduzione all'uso di HDInsight] o [Provisioning di cluster HDInsight][Provisioning di cluster HDInsight]. Per completare l'esercitazione sono necessari i dati seguenti:

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Proprietà del cluster</th>
    <th align="left">Nome variabile di PowerShell</th>
    <th align="left">Valore</th>
    <th align="left">Descrizione</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Nome del cluster HDInsight</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">Corrisponde al nome del cluster HDInsight.</td>
    </tr>
    <tr class="even">
    <td align="left">Nome dell'account di archiviazione di Azure</td>
    <td align="left">$storageAccountName</td>
    <td align="left"></td>
    <td align="left">Un account di archiviazione di Azure disponibile per il cluster HDInsight. Ai fini di questa esercitazione, usare l'account di archiviazione predefinito, specificato durante il processo di provisioning del cluster.</td>
    </tr>
    <tr class="odd">
    <td align="left">Nome del contenitore BLOB di Azure</td>
    <td align="left">$containerName</td>
    <td align="left"></td>
    <td align="left">Per questo esempio, usare il contenitore di archiviazione BLOB di Azure usato come file system predefinito del cluster HDInsight. Per impostazione predefinita, il nome del contenitore corrisponde al nome del cluster HDInsight.</td>
    </tr>
    </tbody>
    </table>

**Informazioni sull'archiviazione in HDInsight**

HDInsight usa l'archivio BLOB di Azure per l'archiviazione dei dati. Questa risorsa è denominata *WASB* o *Archiviazione BLOB di Azure*. WASB è l'implementazione Microsoft di HDFS sull'archivio BLOB di Azure. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight][Usare l'archivio BLOB di Azure con HDInsight].

Durante il provisioning di un cluster HDInsight, un contenitore di archiviazione BLOB viene designato come file system predefinito, come in HDFS. Oltre a tale contenitore, è possibile aggiungere ulteriori contenitori dallo stesso account di archiviazione di Azure o da account di archiviazione di Azure diversi durante il processo di provisioning. Per istruzioni sull'aggiunta di ulteriori account di archiviazione, vedere [Provisioning di cluster HDInsight][Provisioning di cluster HDInsight].

> [WACOM.NOTE] Per semplificare lo script di PowerShell usato in questa esercitazione, tutti i file vengono archiviati nel contenitore del file system predefinito, che si trova in */tutorials/twitter*. Per impostazione predefinita, il nome di questo contenitore corrisponde al nome del cluster HDInsight. Se si sceglie di usare un contenitore diverso per l'archiviazione dei file, aggiornare lo script di conseguenza.

La sintassi WASB è la seguente:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Nella versione 3.0 del cluster HDInsight è supportata solo la sintassi *wasb://*. La sintassi *asv://*, meno recente, è supportata nei cluster HDInsight 2.1 e 1.6, ma non è supportata nei cluster HDInsight 3.0 e non sarà supportata nelle versioni successive.

> Il percorso di WASB è un percorso virtuale. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight][Usare l'archivio BLOB di Azure con HDInsight].

È possibile accedere da HDInsight a un file archiviato nel contenitore del file system predefinito usando uno degli URI seguenti (usare come esempio tweets.txt):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/twitter/tweets.txt
    wasb:///tutorials/twitter/tweets.txt
    /tutorials/twitter/tweets.txt

Per accedere al file direttamente dall'account di archiviazione, il nome del BLOB per questo file è:

    tutorials/twitter/tweets.txt

Nella tabella seguente vengono elencati i file usati nell'esercitazione:

| File                               | Descrizione                                                                                                                        |
|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------|
| /tutorials/twitter/data/tweets.txt | I dati di origine per il processo Hive.                                                                                            |
| /tutorials/twitter/output          | La cartella di output per il processo Hive. Il nome file di output del processo Hive relativo al file predefinito è **000000\_0**. |
| tutorials/twitter/twitter.hql      | Il file script HiveQL.                                                                                                             |
| /tutorials/twitter/jobstatus       | Lo stato del processo Hadoop.                                                                                                      |

## <span id="feed"></span></a>Ricezione di feed Twitter

In questa esercitazione verranno usate le [API di streaming di Twitter][API di streaming di Twitter]. L'API di streaming di Twitter specifica che verrà usata è [statuses/filter][statuses/filter].

[I dati dei tweet][I dati dei tweet] vengono archiviati nel formato JSon, che include una struttura nidificata complessa. Invece di scrivere molte righe di codice usando un linguaggio di programmazione convenzionale, tale struttura nidificata verrà trasformata in una tabella Hive, in modo da consentire l'esecuzione di query da parte di un linguaggio analogo a SQL, denominato HQL.

Twitter usa OAuth per fornire accesso autorizzato alla propria API. OAuth è un protocollo di autenticazione che consente agli utenti di autorizzare l'applicazione ad agire per proprio conto senza divulgare la propria password. Altre informazioni sono disponibili su [oauth.net][oauth.net] o nell'ottima guida [Beginner's Guide to OAuth][Beginner's Guide to OAuth] di Hueniverse.

Il primo passaggio da seguire per usare OAuth consiste nel creare una nuova applicazione sul sito Twitter Developers.

**Per creare un'applicazione Twitter**

1.  Accedere a <https://apps.twitter.com/>. Se non si dispone di un account Twitter, fare clic sul collegamento **Sign up now**.
2.  Fare clic su **Create New App**.
3.  Compilare i campi **Name**, **Description**, **Website**. Per il campo Website è possibile creare un URL fittizio. Nella tabella seguente vengono mostrati alcuni valori di esempio da usare:

    | Campo       | Valore                        |
    |-------------|-------------------------------|
    | Nome        | MyHDInsightApp                |
    | Descrizione | MyHDInsightApp                |
    | Sito Wen    | http://www.myhdinsightapp.com |

4.  Fare clic su **Yes, I agree**, quindi scegliere **Create your Twitter application**.
5.  Fare clic sulla scheda **Permissions**. L'autorizzazione predefinita è **Read only**. Questo livello di autorizzazione è sufficiente per l'esercitazione.
6.  Fare clic sulla scheda **API Keys**.
7.  Fare clic su **Create my access token**.
8.  Fare clic su **Test OAuth** nell'angolo superiore destro della pagina.
9.  Compilare i campi **consumer key**, **Consumer secret**, **Access token** e **Access token secret**. Sarà necessario usare questi valori più avanti nell'esercitazione.

In questa esercitazione verrà usato PowerShell per effettuare una chiamata del servizio Web. L'altro strumento di ampia diffusione per effettuare chiamate di servizi Web è [*Curl*][*Curl*]. Curl può essere scaricato [da questa pagina][da questa pagina].

> [WACOM.NOTE] Quando si usa il comando curl in Windows, per i valori delle opzioni sostituire le virgolette alle virgolette singole.

**Per ricevere tweet**

1.  Aprire Windows PowerShell ISE (nella schermata Start di Windows 8 digitare **PowerShell\_ISE** e quindi fare clic su **Windows PowerShell ISE**. Vedere la pagina dedicata all'[avvio di Windows PowerShell in Windows 8 e Windows][avvio di Windows PowerShell in Windows 8 e Windows]).

2.  Copiare lo script seguente nel riquadro di script:

        Write-Host "Set variables ..." -ForegroundColor Green
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<BlobContainerName>"

        $destBlobName = "tutorials/twitter/data/tweets.txt"

        $trackString = "Azure, Cloud, HDInsight"
        $track = [System.Uri]::EscapeDataString($trackString);
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

    | Variabile                | Descrizione                                                                                                                                                                                                                    |
    |--------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | $storageAccountName      | L'account di archiviazione di Azure usato per il file system predefinito del cluster HDInsight. L'account di archiviazione specificato al momento del provisioning. Vedere [Prerequisiti][Prerequisiti].                                   |
    | $containerName           | Il contenitore BLOB usato per il file system del cluster HDInsight. Il contenitore specificato al momento del provisioning. Vedere [Prerequisiti][Prerequisiti].                                                                           |
    | $destBlobName            | Il nome BLOB dell'output. Il valore predefinito è **tutorials/twitter/data/tweets.txt**. Se si modifica il valore predefinito, sarà necessario aggiornare gli script PowerShell di conseguenza.                                |
    | $trackString             | Il servizio Web restituirà tweet correlati a queste parole chiave. Il valore predefinito è **Azure, Cloud, HDInsight**. Se si modifica il valore predefinito, sarà necessario aggiornare gli script PowerShell di conseguenza. |
    | $lineMax                 | Il valore determina il numero di tweet che lo script leggerà. Per leggere 100 tweet sono necessari circa tre minuti. È possibile impostare un numero più elevato, ma il download richiederà più tempo.                         |
    | $oauth\_consumer\_key    | L'applicazione Twitter **consumer key** scritta in precedenza al momento della creazione dell'applicazione Twitter.                                                                                                            |
    | $oauth\_consumer\_secret | L'applicazione Twitter **consumer secret** scritta in precedenza.                                                                                                                                                              |
    | $oauth\_token            | L'applicazione Twitter **access token** scritta in precedenza.                                                                                                                                                                 |
    | $oauth\_token\_secret    | L'applicazione Twitter **access token secret** scritta in precedenza.                                                                                                                                                          |

4.  Premere **F5** per eseguire lo script. In caso di problemi, come soluzione selezionare tutte le righe e premere **F8**.
5.  Alla fine dell'output verrà visualizzato il testo "Complete!". Eventuali messaggi di errore verranno visualizzati in rosso.

Come procedura di convalida, è possibile controllare il file di output, **/tutorials/twitter/data/tweets.txt**, nell'archivio BLOB di Azure usando uno strumento per l'esplorazione dei servizi di archiviazione Azure oppure Azure PowerShell. Per uno script PowerShell di esempio per l'elenco dei file, vedere [Uso dell'archiviazione BLOB con HDInsight][Uso dell'archiviazione BLOB con HDInsight].

## <span id="script"></span></a>Creazione di uno script HiveQL

Azure PowerShell consente di eseguire più istruzioni HiveQL contemporaneamente o di inserire l'istruzione HiveQL in un file di script. In questa esercitazione verrà creato uno script HiveQL. Il file di script deve essere caricato in WASB. Nella sezione successiva il file di script verrà eseguito mediante Azure PowerShell.

Il file di script HiveQL eseguirà le operazioni seguenti:

1.  **Eliminazione della tabella tweets\_raw** nel caso in cui la tabella esista già.
2.  **Creazione della tabella tweets\_raw Hive**. Questa tabella strutturata Hive temporanea contiene i dati per ulteriori operazioni di elaborazione ETL. Per informazioni sulla partizione vedere l'[esercitazione su Hive][esercitazione su Hive].
3.  **Caricare i dati** dalla cartella di origine /tutorials/twitter/data. Il set di dati di tweet di grandi dimensioni in formato JSon nidificato è stato ora trasformato in una struttura di tabella Hive temporanea.
4.  **Eliminazione della tabella tweets** nel caso in cui la tabella esista già.
5.  **Creazione della tabella tweets**. Prima di eseguire query sul set di dati di tweet usando Hive, è necessario eseguire un altro processo ETL. Il processo ETL definisce uno schema di tabella più dettagliato per i dati archiviati nella tabella "twitter\_raw".
6.  **Inserimento della tabella di sovrascrittura**. Questo script complesso di Hive consente di avviare una serie di processi Map Reduce dal cluster Hadoop. In base al set di dati e alla dimensione del cluster, tale operazione potrebbe richiedere circa 10 minuti.
7.  **Inserimento della directory di sovrascrittura**. Esecuzione di una query e invio del set di dati a un file. Questa query restituirà un elenco di utenti di Twitter che hanno inviato il numero maggiore di tweet contenenti la parola "Azure".

**Per creare uno script Hive e caricarlo in Azure**

1.  Aprire Windows PowerShell ISE.
2.  Copiare lo script seguente nel riquadro di script:

        Write-Host "Define variables ..." -ForegroundColor Green
        $storageAccountName = "<AzureStorageAccountName>"
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

    | Variabile           | Descrizione                                                                                                                                                                                  |
    |---------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | $storageAccountName | L'account di archiviazione di Azure usato per il file system predefinito del cluster HDInsight. L'account di archiviazione specificato al momento del provisioning. Vedere [Prerequisiti][Prerequisiti]. |
    | $containerName      | Il contenitore BLOB usato per il file system del cluster HDInsight. Il contenitore specificato al momento del provisioning. Vedere [Prerequisiti][Prerequisiti].                                         |
    | $sourceDataPath     | Il percorso WASB dal quale le query Hive effettueranno la lettura dei dati. Non è necessario modificare questa variabile.                                                                    |
    | $outputPath         | Il percorso WASB al quale le query Hive invieranno i risultati. Non è necessario modificare questa variabile.                                                                                |
    | $hqlScriptFile      | La posizione e il nome del file relativo allo script HiveQL.                                                                                                                                 |

4.  Premere **F5** per eseguire lo script. In caso di problemi, come soluzione selezionare tutte le righe e premere **F8**.
5.  Alla fine dell'output verrà visualizzato il testo "Complete!". Eventuali messaggi di errore verranno visualizzati in rosso.

Come procedura di convalida, è possibile controllare il file di output, **/tutorials/twitter/twitter.hql**, nell'archivio BLOB di Azure usando uno strumento per l'esplorazione dei servizi di archiviazione Azure oppure Azure PowerShell. Per uno script PowerShell di esempio per l'elenco dei file, vedere [Uso dell'archiviazione BLOB con HDInsight][Uso dell'archiviazione BLOB con HDInsight].

## <a name="process"></a> Elaborazione dei dati di Twitter usando Hive

Tutte le attività preliminari sono state completate. Ora è possibile richiamare lo script Hive script e controllare i risultati.

### Invio di un processo Hive

Usare lo script PowerShell seguente per eseguire lo script Hive. È necessario impostare la prima variabile.

    Write-Host "Set the variables ... " -ForegroundColor Green
    $clusterName = "<HDInsightClusterName>"

    $hqlScriptFile = "/tutorials/twitter/twitter.hql"
    $statusFolder = "/tutorials/twitter/jobstatus"

    Write-Host "Invoke Hive ... " -ForegroundColor Green
    Use-AzureHDInsightCluster $clusterName
    $response = Invoke-Hive -file $hqlScriptFile -StatusFolder $statusFolder -OutVariable $outVariable

    Write-Host "Display the standard error log ... " -ForegroundColor Green
    $jobID = ($response | Select-String job_ | Select-Object -First 1) -replace ‘\s*$’ -replace ‘.*\s’
    Get-AzureHDInsightJobOutput -cluster $clusterName -JobId $jobID -StandardError 

### Controllo dei risultati

Usare lo script PowerShell seguente per controllare l'output del processo Hive. È necessario impostare le prime due variabili.

    Write-Host "Set the variables ... " -ForegroundColor Green
    $storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.

    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force

    Write-Host "Display the output ..." -ForegroundColor Green
    cat "./$blob"

> [WACOM.NOTE] La tabella Hive usa \\001 come delimitatore di campo. Il delimitatore non è visibile nell'output.

Dopo che i risultati dell'analisi sono stati posizionati in WASB, è possibile esportare i dati nel database Azure SQL/server SQL, esportare i dati in Excel usando Power Query oppure connettere l'applicazione ai dati usando il driver Hive ODBC. Per altre informazioni, vedere [Usare Sqoop con HDInsight][Usare Sqoop con HDInsight] ,[Analizzare i dati sui ritardi dei voli usando HDInsight][Analizzare i dati sui ritardi dei voli usando HDInsight], [Connettere Excel a HDInsight mediante Power Query][Connettere Excel a HDInsight mediante Power Query] e [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver][Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver].

## <span id="cleanup"></span></a>Pulitura dell'esercitazione

Nel caso in cui si desideri ripetere l'esecuzione dell'esercitazione è necessario:

-   **Ricreare il file di dati dei tweet**. Il file di dati dei tweet è stato rimosso dal processo Hive. È necessario generarne uno nuovo. Il nome file è **tutorials/twitter/data/tweets.txt**. Vedere [Ricezione di feed Twitter][Ricezione di feed Twitter].

## <span id="nextsteps"></span></a>Passaggi successivi

In questa esercitazione è stato illustrato come trasformare un set di dati Json non strutturato in una tabella Hive strutturata per consentire l'esecuzione di query, l'esplorazione e l'analisi di dati da Twitter tramite HDInsight in Azure. Per altre informazioni, vedere:

-   [Introduzione all'uso di HDInsight][Introduzione all'uso di HDInsight]
-   [Analizzare i dati sui ritardi dei voli usando HDInsight][Analizzare i dati sui ritardi dei voli usando HDInsight]
-   [Connettere Excel a HDInsight mediante Power Query][Connettere Excel a HDInsight mediante Power Query]
-   [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver][Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver]
-   [Usare Sqoop con HDInsight][Usare Sqoop con HDInsight]

  [Prerequisiti]: #prerequisites
  [Ricezione di feed Twitter]: #feed
  [Creazione di uno script HiveQL]: #script
  [Elaborazione dei dati usando Hive]: #process
  [Pulitura dell'esercitazione]: #cleanup
  [Passaggi successivi]: #nextsteps
  [Come installare e configurare Azure PowerShell]: ../install-configure-powershell
  [esecuzione di script di Windows PowerShell]: http://technet.microsoft.com/it-it/library/ee176961.aspx
  [Introduzione all'uso di HDInsight]: ../hdinsight-get-started/
  [Provisioning di cluster HDInsight]: ../hdinsight-provision-clusters/
  [Usare l'archivio BLOB di Azure con HDInsight]: ../hdinsight-use-blob-storage/
  [API di streaming di Twitter]: https://dev.twitter.com/docs/streaming-apis
  [statuses/filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter
  [I dati dei tweet]: https://dev.twitter.com/docs/platform-objects/tweets
  [oauth.net]: http://oauth.net/
  [Beginner's Guide to OAuth]: http://hueniverse.com/oauth/
  [*Curl*]: http://curl.haxx.se
  [da questa pagina]: http://curl.haxx.se/download.html
  [avvio di Windows PowerShell in Windows 8 e Windows]: http://technet.microsoft.com/it-it/library/hh847889.aspx
  [Uso dell'archiviazione BLOB con HDInsight]: ../hdinsight-use-blob-storage/#powershell
  [esercitazione su Hive]: https://cwiki.apache.org/confluence/display/Hive/Tutorial
  [Usare Sqoop con HDInsight]: ../hdinsight-use-sqoop/
  [Analizzare i dati sui ritardi dei voli usando HDInsight]: ../hdinsight-analyze-flight-delay-data/
  [Connettere Excel a HDInsight mediante Power Query]: ../hdinsight-connect-excel-power-query/
  [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver]: ../hdinsight-connect-excel-hive-ODBC-driver/
