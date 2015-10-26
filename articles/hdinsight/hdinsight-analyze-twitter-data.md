<properties
	pageTitle="Analizzare i dati di Twitter con Hadoop in HDInsight | Microsoft Azure"
	description="Informazioni su come usare Hive per analizzare i dati di Twitter con Hadoop in HDInsight per rilevare la frequenza d'uso di una parola specifica."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2015"
	ms.author="jgao"/>

# Analizzare i dati di Twitter con Hive in HDInsight

I siti Web di social networking rappresentano una delle principali forze trainanti per l'adozione di Big Data. Le API pubbliche offerte da siti quali Twitter costituiscono un'utile origine di dati per l'analisi e la comprensione delle tendenze più popolari. In questa esercitazione si userà l'API di streaming di Twitter per ricevere tweet e quindi si userà Apache Hive in Azure HDInsight per ottenere un elenco di utenti di Twitter che hanno inviato il maggior numero di tweet contenenti una determinata parola.

> [AZURE.NOTE]I passaggi descritti in questo documento richiedono un cluster HDInsight basato su Windows. Per passaggi specifici di un cluster basato su Linux, vedere [Analizzare i dati di Twitter con Hive in HDInsight (Linux)](hdinsight-analyze-twitter-data-linux.md).



> [AZURE.TIP]Un esempio simile è disponibile nella raccolta di esempi di HDInsight. Guardare il video di Channel 9 relativo all'<a href="http://channel9.msdn.com/Series/Getting-started-with-Windows-Azure-HDInsight-Service/Analyze-Twitter-trend-using-Apache-Hive-in-HDInsight" target="_blank">analisi delle tendenze di Twitter con Apache Hive in HDInsight</a>.

###Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una workstation** in cui sia stato installato e configurato Azure PowerShell. Vedere [Installare e usare Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). Per eseguire script di Windows PowerShell, è necessario eseguire Azure PowerShell come amministratore e impostare i criteri di esecuzione su *RemoteSigned*. Vedere la pagina relativa all'[esecuzione di script di Windows PowerShell][powershell-script].

	Prima di eseguire script di Windows PowerShell, assicurarsi di essere connessi alla sottoscrizione di Azure usando il cmdlet seguente:

		Add-AzureAccount

	Se si dispone di più sottoscrizioni di Azure, usare il seguente cmdlet per impostare la sottoscrizione corrente:

		Select-AzureSubscription <AzureSubscriptionName>



- **Un cluster HDInsight di Azure**. Per istruzioni sul provisioning del cluster, vedere [Introduzione a HDInsight][hdinsight-get-started] o [Effettuare il provisioning di cluster HDInsight][hdinsight-provision]. Il nome del cluster sarà necessario più avanti nell'esercitazione.

**Informazioni sull'archiviazione in HDInsight**

HDInsight usa l'archivio BLOB di Azure per l'archiviazione dei dati. L'archivio BLOB di Azure è l'implementazione Microsoft di HDFS (Hadoop Distributed File System). Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight][hdinsight-storage].

Durante il provisioning di un cluster HDInsight, un contenitore di archiviazione BLOB viene designato come file system predefinito, come in HDFS. Oltre a tale contenitore, è possibile aggiungere contenitori dallo stesso account di archiviazione di Azure o da account di archiviazione di Azure diversi durante il processo di provisioning. Per istruzioni sull'aggiunta di account di archiviazione, vedere [Effettuare il provisioning di cluster HDInsight][hdinsight-provision].

> [AZURE.NOTE]Per semplificare lo script di Windows PowerShell usato in questa esercitazione, tutti i file vengono archiviati nel contenitore del file system predefinito, che si trova in */tutorials/twitter*. Per impostazione predefinita, il nome di questo contenitore corrisponde al nome del cluster HDInsight. Se si sceglie di usare un contenitore diverso per l'archiviazione dei file, aggiornare lo script di conseguenza.

Di seguito è riportata la sintassi dell'archivio BLOB di Azure:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE]Nel cluster HDInsight versione 3.0 è supportata solo la sintassi **wasb://*. La sintassi meno recente **asv://* è supportata nei cluster HDInsight 2.1 e 1.6, ma non nei cluster HDInsight 3.0 e non sarà supportata nelle versioni successive.

> Il percorso dell'archivio BLOB di Azure è un percorso virtuale. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight][hdinsight-storage].

È possibile accedere da HDInsight a un file archiviato nel contenitore del file system predefinito usando uno degli URI (Uniform Resource Identifier) seguenti: Questi URI usano tweets.txt come esempio.

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/twitter/tweets.txt
	wasb:///tutorials/twitter/tweets.txt
	/tutorials/twitter/tweets.txt

Per accedere al file direttamente dall'account di archiviazione, il nome del BLOB per questo file è:

	tutorials/twitter/tweets.txt

Nella tabella seguente vengono elencati i file usati nell'esercitazione:

File|Descrizione
---|---
/tutorials/twitter/data/tweets.txt|I dati di origine per il processo Hive.
/tutorials/twitter/output|Cartella di output per il processo Hive. Il nome file di output del processo Hive relativo al file predefinito è **000000\_0**.
tutorials/twitter/twitter.hql|File script HiveQL.
/tutorials/twitter/jobstatus|Stato del processo Hadoop.


##Ricevere feed Twitter

In questa esercitazione verranno usate le [API di streaming di Twitter][twitter-streaming-api]. L'API di streaming di Twitter specifica che verrà usata è [statuses/filter][twitter-statuses-filter].

>[AZURE.NOTE]Un file contenente 10.000 tweet e il file di script Hive (descritto nella sezione successiva) sono stati caricati in un contenitore BLOB pubblico. Se si preferisce usare i file caricati, è possibile ignorare questa sezione.

[I dati dei tweet](https://dev.twitter.com/docs/platform-objects/tweets) vengono archiviati nel formato JSON (JavaScript Object Notation) che include una struttura annidata complessa. Invece di scrivere molte righe di codice usando un linguaggio di programmazione convenzionale, è possibile trasformare la struttura annidata in una tabella Hive, in modo da consentire l'esecuzione di query tramite un linguaggio analogo a SQL ( Structured Query Language), denominato HiveQL.

Twitter usa OAuth per fornire accesso autorizzato alla propria API. OAuth è un protocollo di autenticazione che consente agli utenti di autorizzare l'applicazione ad agire per proprio conto senza divulgare la propria password. Ulteriori informazioni sono disponibili su [oauth.net](http://oauth.net/) o nell'ottima guida [Beginner's Guide to OAuth](http://hueniverse.com/oauth/) di Hueniverse.

Il primo passaggio da seguire per usare OAuth consiste nel creare una nuova applicazione sul sito Twitter Developers.

**Per creare un'applicazione Twitter**

1. Accedere a [https://apps.twitter.com/](https://apps.twitter.com/). Se non si dispone di un account Twitter, fare clic sul collegamento **Sign up now**.
2. Fare clic su **Create New App**.
3. Compilare i campi **Name**, **Description**, **Website**. Per il campo **Website** è possibile creare un URL fittizio. Nella tabella seguente vengono mostrati alcuni valori di esempio da usare:

Campo|Valore
---|---
Nome|MyHDInsightApp
Descrizione|MyHDInsightApp
Sito Web|http://www.myhdinsightapp.com

4. Fare clic su **Yes, I agree**, quindi scegliere **Create your Twitter application**.
5. Fare clic sulla scheda **Permissions**. L'autorizzazione predefinita è **Read only**. Questo livello di autorizzazione è sufficiente per l'esercitazione.
6. Fare clic sulla scheda **Keys and Access Tokens**.
7. Fare clic su **Create my access token**.
8. Fare clic su **Test OAuth** nell'angolo superiore destro della pagina.
9. Compilare i campi **consumer key**, **Consumer secret**, **Access token** e **Access token secret**. Sarà necessario usare questi valori più avanti nell'esercitazione.

In questa esercitazione si userà Windows PowerShell per effettuare la chiamata del servizio Web. Per un esempio di .NET C#, vedere [Analizzare i sentimenti Twitter in tempo reale con HBase in HDInsight][hdinsight-hbase-twitter-sentiment]. L'altro strumento di ampia diffusione per effettuare chiamate di servizi Web è [*Curl*][curl]. Curl può essere scaricato [da questa pagina][curl-download].

>[AZURE.NOTE]Quando si usa il comando curl in Windows, per i valori delle opzioni usare le virgolette doppie invece di quelle singole.

**Per ricevere tweet**

1. Aprire Windows PowerShell Integrated Scripting Environment (ISE). Nella schermata Start di Windows 8 digitare **PowerShell\_ISE** e quindi fare clic su **Windows PowerShell ISE**. Vedere l'articolo relativo all'[avvio di Windows PowerShell in Windows 8 e Windows][powershell-start].

2. Copiare lo script seguente nel riquadro di script:

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
		Add-AzureAccount
		#endregion

		#region - Create a block blob object for writing tweets into Blob storage
		Write-Host "Get the default storage account name and Blob container name using the cluster name ..." -ForegroundColor Green
		$myCluster = Get-AzureHDInsightCluster -Name $clusterName
		$storageAccountName = $myCluster.DefaultStorageAccount.StorageAccountName.Replace(".blob.core.windows.net", "")
		$containerName = $myCluster.DefaultStorageAccount.StorageContainerName
		Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
		Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow

		Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
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

3. Impostare le prime cinque-otto variabili dello script:


Variabile|Descrizione
---|---
$clusterName|Nome del cluster HDInsight in cui eseguire l'applicazione.
$oauth\_consumer\_key|Applicazione Twitter **consumer key** scritta in precedenza al momento della creazione dell'applicazione Twitter.
$oauth\_consumer\_secret|L'applicazione Twitter **consumer secret** scritta in precedenza.
$oauth\_token|L'applicazione Twitter **access token** scritta in precedenza.
$oauth\_token\_secret|L'applicazione Twitter **access token secret** scritta in precedenza.
$destBlobName|Il nome BLOB dell'output. Il valore predefinito è **tutorials/twitter/data/tweets.txt**. Se si modifica il valore predefinito, sarà necessario aggiornare gli script di Windows PowerShell di conseguenza.
$trackString|Il servizio Web restituirà tweet correlati a queste parole chiave. Il valore predefinito è **Azure, Cloud, HDInsight**. Se si modifica il valore predefinito, si dovranno aggiornare gli script di Windows PowerShell di conseguenza.
$lineMax|Il valore determina il numero di tweet che lo script leggerà. Per leggere 100 tweet sono necessari circa tre minuti. È possibile impostare un numero più elevato, ma il download richiederà più tempo.

5. Premere **F5** per eseguire lo script. In caso di problemi, come soluzione selezionare tutte le righe e premere **F8**.
6. Alla fine dell'output verrà visualizzato il testo "Complete!". Eventuali messaggi di errore verranno visualizzati in rosso.

Come procedura di convalida, è possibile controllare il file di output, **/tutorials/twitter/data/tweets.txt**, nell'archivio BLOB di Azure tramite uno strumento per l'esplorazione dei servizi di archiviazione di Azure oppure Azure PowerShell. Per uno script di Windows PowerShell di esempio per l'elenco dei file, vedere [Usare l'archivio BLOB di Azure con HDInsight][hdinsight-storage-powershell].



##Creare uno script HiveQL

Azure PowerShell consente di eseguire più istruzioni HiveQL contemporaneamente o di inserire l'istruzione HiveQL in un file di script. In questa esercitazione verrà creato uno script HiveQL. Il file di script deve essere caricato nell'archivio BLOB di Azure. Nella sezione successiva si eseguirà il file di script tramite Azure PowerShell.

>[AZURE.NOTE]Il file di script Hive e un file contenente 10.000 tweet sono stati caricati in un contenitore BLOB pubblico. Se si preferisce usare i file caricati, è possibile ignorare questa sezione.

Il file di script HiveQL eseguirà le operazioni seguenti:

1. **Eliminazione della tabella tweets\_raw** nel caso in cui la tabella esista già.
2. **Creazione della tabella tweets\_raw Hive**. Questa tabella Hive strutturata e temporanea contiene i dati per altri processi di estrazione, trasformazione e caricamento (ETL, Extraction, Transformation, and Loading). Per informazioni sulle partizioni, vedere la pagina relativa all'[esercitazione su Hive][apache-hive-tutorial].  
3. **Caricare i dati** dalla cartella di origine /tutorials/twitter/data. Il set di dati di tweet di grandi dimensioni in formato JSON annidato è stato trasformato in una struttura di tabella Hive temporanea.
3. **Eliminare la tabella tweets** nel caso esista già.
4. **Creare la tabella tweets**. Prima di eseguire query sul set di dati di tweet tramite Hive, è necessario eseguire un altro processo ETL. Il processo ETL definisce uno schema di tabella più dettagliato per i dati archiviati nella tabella "twitter\_raw".  
5. **Inserire la tabella di sovrascrittura**. Questo script Hive complesso avvia una serie di processi MapReduce dal cluster Hadoop. In base al set di dati e alla dimensione del cluster, tale operazione potrebbe richiedere circa 10 minuti.
6. **Inserire la directory di sovrascrittura**. Eseguire una query e inviare il set di dati a un file. Questa query restituirà un elenco di utenti di Twitter che hanno inviato il maggior numero di tweet contenenti la parola "Azure".

**Per creare uno script Hive e caricarlo in Azure**

1. Aprire Windows PowerShell ISE.
2. Copiare lo script seguente nel riquadro di script:

		#region - variables and constants
		$clusterName = "<HDInsightClusterName>" # Enter your HDInsight cluster name

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
			case substr (get_json_object(json_response,	'$.created_at'),5,3)
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
		Add-AzureAccount
		#endregion

		#region - Create a block blob object for writing the Hive script file
		Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
		$myCluster = Get-AzureHDInsightCluster -Name $clusterName
		$storageAccountName = $myCluster.DefaultStorageAccount.StorageAccountName.Replace(".blob.core.windows.net", "")
		$containerName = $myCluster.DefaultStorageAccount.StorageContainerName
		Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
		Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow

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
		#endregion

		#region - Write the Hive script file to Blob storage
		Write-Host "Write to the destination blob ... " -ForegroundColor Green
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$hqlScriptBlob.UploadFromStream($memStream)
		#endregion

		Write-Host "Completed!" -ForegroundColor Green


4. Impostare le prime due variabili nello script:

Variabile|Descrizione
---|---
$clusterName|Immettere il nome del cluster HDInsight in cui eseguire l'applicazione.
$sourceDataPath|Percorso dell'archivio BLOB di Azure dal quale le query Hive leggeranno i dati. Non è necessario modificare questa variabile.
$outputPath|Percorso dell'archivio BLOB di Azure dal quale le query Hive restituiranno i risultati. Non è necessario modificare questa variabile.
$hqlScriptFile|Posizione e nome del file di script HiveQL. Non è necessario modificare questa variabile.

5. Premere **F5** per eseguire lo script. In caso di problemi, come soluzione selezionare tutte le righe e premere **F8**.
6. Alla fine dell'output verrà visualizzato il testo "Complete!". Eventuali messaggi di errore verranno visualizzati in rosso.

Come procedura di convalida, è possibile controllare il file di output, **/tutorials/twitter/twitter.hql**, nell'archivio BLOB di Azure usando uno strumento per l'esplorazione dei servizi di archiviazione Azure oppure Azure PowerShell. Per uno script di Windows PowerShell di esempio per l'elenco dei file, vedere [Usare l'archivio BLOB di Azure con HDInsight][hdinsight-storage-powershell].


##Elaborare i dati di Twitter tramite Hive

Tutte le attività preliminari sono state completate. Ora è possibile richiamare lo script Hive script e controllare i risultati.

### Inviare un processo Hive

Usare lo script PowerShell seguente per eseguire lo script Hive. È necessario impostare la prima variabile.

>[AZURE.NOTE]Per usare i tweet e lo script HiveQL caricati nelle ultime due sezioni, impostare $hqlScriptFile su "/tutorials/twitter/twitter.hql". Per usare quelli caricati automaticamente in un BLOB pubblico, impostare $hqlScriptFile su "wasb://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql".

	#region variables and constants
	$clusterName = "<HDInsightClusterName>"

	#use one of the following
	$hqlScriptFile = "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"
	$hqlScriptFile = "/tutorials/twitter/twitter.hql"

	$statusFolder = "/tutorials/twitter/jobstatus"
	#endregion

	#region - Invoke Hive
	Write-Host "Invoke Hive ... " -ForegroundColor Green
	Use-AzureHDInsightCluster $clusterName
	$response = Invoke-Hive -file $hqlScriptFile -StatusFolder $statusFolder -OutVariable $outVariable

	Write-Host "Display the standard error log ... " -ForegroundColor Green
	$jobID = ($response | Select-String job_ | Select-Object -First 1) -replace ‘\s*$’ -replace ‘.*\s’
	Get-AzureHDInsightJobOutput -cluster $clusterName -JobId $jobID -StandardError
	#endregion

### Controllare i risultati

Usare lo script di Windows PowerShell seguente per controllare l'output del processo Hive. È necessario impostare le prime due variabili.

	#region variables and constants
	$clusterName = "<HDInsightClusterName>"

	$blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
	#engregion

	#region - Create an Azure storage context object
	Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
	$myCluster = Get-AzureHDInsightCluster -Name $clusterName
	$storageAccountName = $myCluster.DefaultStorageAccount.StorageAccountName.Replace(".blob.core.windows.net", "")
	$containerName = $myCluster.DefaultStorageAccount.StorageContainerName
	Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
	Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow

	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	#endregion

	#region - Download blob and display blob
	Write-Host "Download the blob ..." -ForegroundColor Green
	cd $HOME
	Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force

	Write-Host "Display the output ..." -ForegroundColor Green
	Write-Host "==================================" -ForegroundColor Green
	cat "./$blob"
	Write-Host "==================================" -ForegroundColor Green
	#end region

> [AZURE.NOTE]La tabella Hive usa \\001 come delimitatore di campo. Il delimitatore non è visibile nell'output.

Dopo che i risultati dell'analisi sono stati inserivi in un archivio BLOB di Azure, è possibile esportare i dati in un database SQL o un server SQL di Azure, esportare i dati in Excel tramite Power Query oppure connettere l'applicazione ai dati usando Hive ODBC Driver. Per altre informazioni, vedere [Usare Sqoop con Hadoop in HDInsight][hdinsight-use-sqoop], [Analizzare i dati sui ritardi dei voli con HDInsight][hdinsight-analyze-flight-delay-data], [Connettere Excel a HDInsight mediante Power Query][hdinsight-power-query] e [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver][hdinsight-hive-odbc].

##Passaggi successivi

In questa esercitazione è stato illustrato come trasformare un set di dati JSON non strutturato in una tabella Hive strutturata per eseguire query, esplorare e analizzare dati da Twitter tramite HDInsight in Azure. Per altre informazioni, vedere:

- [Introduzione a HDInsight][hdinsight-get-started]
- [Analizzare i sentimenti Twitter in tempo reale con HBase in HDInsight][hdinsight-hbase-twitter-sentiment]
- [Analizzare i dati sui ritardi dei voli con HDInsight][hdinsight-analyze-flight-delay-data]
- [Connettere Excel a HDInsight mediante Power Query][hdinsight-power-query]
- [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver][hdinsight-hive-odbc]
- [Usare Sqoop con Hadoop in HDInsight][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell.md
[powershell-script]: http://technet.microsoft.com/library/ee176961.aspx


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

<!---HONumber=Oct15_HO3-->