<properties
    pageTitle="Analizzare i dati di Twitter con Apache Hive in HDInsight | Microsoft Azure"
    description="Informazioni su come utilizzare Python per archiviare i Tweet che contengono determinate parole chiave, quindi utilizzare Hive e Hadoop su HDInsight per trasformare i dati di Twitter non elaborati in una tabella Hive disponibile per la ricerca."
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
    ms.date="09/27/2016"
    ms.author="larryfr"/>


# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>Analizzare i dati di Twitter con Hive in HDInsight

In questo documento viene illustrato come ricevere tweet usando un'API di streaming di Twitter e quindi come usare Apache Hive in un cluster HDInsight basato su Linux per elaborare i dati in formato JSON. Il risultato sarà un elenco di utenti Twitter che hanno inviato il maggior numero di tweet contenenti una determinata parola.

> [AZURE.NOTE] Anche se alcune parti di questo articolo possono essere usate con i cluster HDInsight basati su Windows (ad esempio Python), molti passaggi descritti in questo documento sono specifici per i cluster HDInsight basati su Linux. Per passaggi specifici di un cluster basato su Windows, vedere [Analizzare i dati di Twitter con Hive in HDInsight](hdinsight-analyze-twitter-data.md).

###<a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- Un __cluster Azure HDInsight basato su Linux__. Per informazioni sulla creazione di un cluster, vedere l' [introduzione a HDInsight basato su Linux](hdinsight-hadoop-linux-tutorial-get-started.md) per la procedura di creazione di un cluster.

- Un __client SSH__. Per altre informazioni sull'uso di SSH con HDInsight basato su Linux, vedere gli articoli seguenti:

    * [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

- __Python__ e [pip](https://pypi.python.org/pypi/pip)

##<a name="get-a-twitter-feed"></a>Ricevere un feed Twitter

Twitter consente di recuperare i [dati relativi ad ogni tweet](https://dev.twitter.com/docs/platform-objects/tweets) come documento JSON (JavaScript Object Notation) attraverso un'API REST. [OAuth](http://oauth.net) . È inoltre necessario creare un' _applicazione Twitter_ contenente le impostazioni usate per accedere all'API.

###<a name="create-a-twitter-application"></a>Creare un'applicazione Twitter

1. Da un Web browser accedere a [https://apps.twitter.com/](https://apps.twitter.com/). Se non si dispone di un account Twitter, fare clic sul collegamento **Sign up now** .
2. Fare clic su **Create New App**.
3. Compilare i campi **Name**, **Description**, **Website**. Per il campo **Website** è possibile creare un URL fittizio. Nella tabella seguente vengono mostrati alcuni valori di esempio da usare:

  	| Campo | Valore |
  	|:----- |:----- |
  	| Nome  | MyHDInsightApp |
  	| Descrizione | MyHDInsightApp |
  	| Website | http://www.myhdinsightapp.com |
    
4. Fare clic su **Yes, I agree** e su **Create your Twitter application**.
5. Fare clic sulla scheda **Permissions** . L'autorizzazione predefinita è **Read only**. Questo livello di autorizzazione è sufficiente per l'esercitazione.
6. Fare clic sulla scheda **Keys and Access Tokens** .
7. Fare clic su **Create my access token**.
8. Fare clic su **Test OAuth** nell'angolo superiore destro della pagina.
9. Compilare i campi **Consumer key**, **Consumer secret**, **Access token** e **Access token secret**. Sarà necessario usare questi valori più avanti.

>[AZURE.NOTE] Quando si usa il comando curl in Windows, per i valori delle opzioni usare le virgolette doppie invece di quelle singole.

###<a name="download-tweets"></a>Scaricare tweet

Il codice Python seguente consente di scaricare 10.000 tweet da Twitter e salvarli in un file denominato __tweets.txt__.

> [AZURE.NOTE] I passaggi indicati di seguito vengono eseguiti nel cluster HDInsight, poiché Python è già installato.

1. Connettersi al cluster HDInsight usando SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
        
    Se è stata usata una password per proteggere l'account utente SSH, verrà richiesto di specificarla. Se è stata usata una chiave pubblica, può essere necessario usare il parametro `-i` per specificare la chiave privata corrispondente. Ad esempio: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
        
    Per altre informazioni sull'uso di SSH con HDInsight basato su Linux, vedere gli articoli seguenti:
    
    * [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows)
    
2. Per impostazione predefinita, l'utilità __pip__ non è installata nel nodo head di HDInsight. Per l'installazione, usare il codice seguente e quindi aggiornare l'utilità:

        sudo apt-get install python-pip
        sudo pip install --upgrade pip

3. Il codice per il download dei tweet si basa su [Tweepy](http://www.tweepy.org/) e [Progressbar](https://pypi.python.org/pypi/progressbar/2.2). Per l'installazione, usare il comando seguente:

        sudo apt-get install python-dev libffi-dev libssl-dev
        sudo apt-get remove python-openssl
        sudo pip install tweepy progressbar pyOpenSSL requests[security]
        
    > [AZURE.NOTE] I bit sulla rimozione di python-openssl, installando python-dev, libffi-dev, libssl-dev, pyOpenSSL e le richieste [sicurezza] consistono nell'evitare un'avvertenza InsecurePlatform durante la connessione a Twitter tramite SSL da Python.
    >
    > Tweepy 3.2.0 viene usato per evitare un [errore](https://github.com/tweepy/tweepy/issues/576) che può verificarsi durante l'elaborazione dei tweet.

4. Usare il comando seguente per creare un nuovo file denominato __gettweets.py__:

        nano gettweets.py

5. Usare quanto segue come contenuto del file __gettweets.py__: Sostituire le informazioni di segnaposto per __consumer\_secret__, __consumer\_key__, __access/\_token__ e __access\_token\_secret__ con quelle dell'applicazione Twitter.

        #!/usr/bin/python

        from tweepy import Stream, OAuthHandler
        from tweepy.streaming import StreamListener
        from progressbar import ProgressBar, Percentage, Bar
        import json
        import sys

        #Twitter app information
        consumer_secret='Your consumer secret'
        consumer_key='Your consumer key'
        access_token='Your access token'
        access_token_secret='Your access token secret'

        #The number of tweets we want to get
        max_tweets=10000

        #Create the listener class that will receive and save tweets
        class listener(StreamListener):
            #On init, set the counter to zero and create a progress bar
            def __init__(self, api=None):
                self.num_tweets = 0
                self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

            #When data is received, do this
            def on_data(self, data):
                #Append the tweet to the 'tweets.txt' file
                with open('tweets.txt', 'a') as tweet_file:
                    tweet_file.write(data)
                    #Increment the number of tweets
                    self.num_tweets += 1
                    #Check to see if we have hit max_tweets and exit if so
                    if self.num_tweets >= max_tweets:
                        self.pbar.finish()
                        sys.exit(0)
                    else:
                        #increment the progress bar
                        self.pbar.update(self.num_tweets)
                return True

            #Handle any errors that may occur
            def on_error(self, status):
                print status

        #Get the OAuth token
        auth = OAuthHandler(consumer_key, consumer_secret)
        auth.set_access_token(access_token, access_token_secret)
        #Use the listener class for stream processing
        twitterStream = Stream(auth, listener())
        #Filter for these topics
        twitterStream.filter(track=["azure","cloud","hdinsight"])

6. Usare __Ctrl + X__, quindi premere __Y__ per salvare il file.

7. Usare il comando seguente per aprire il file e scaricare i tweet:

        python gettweets.py

    Viene visualizzato un indicatore di stato che procede fino al 100%, man mano che i tweet vengono scaricati e salvati nel file.

    > [AZURE.NOTE] Se la barra di avanzamento della procedura ci impiega parecchio tempo a riempirsi, è necessario modificare il filtro per monitorare gli argomenti di tendenza. Quando ci sono svariati tweet sull'argomento filtrato, è possibile ottenere rapidamente i 10.000 tweet richiesti.

###<a name="upload-the-data"></a>Caricare i dati

Per caricare i dati in WASB (il file system distribuito usato da HDInsight), usare i comandi seguenti:

    hdfs dfs -mkdir -p /tutorials/twitter/data
    hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt

In questo modo, i dati vengono archiviati in un percorso accessibile a tutti i nodi del cluster.

##<a name="run-the-hiveql-job"></a>Eseguire il processo di HiveQL


1. Usare il comando seguente per creare un file contenente istruzioni HiveQL:

        nano twitter.hql
    
    Usare quanto segue come contenuto del file:

        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;
        -- Drop table, if it exists
        DROP TABLE tweets_raw;
        -- Create it, pointing toward the tweets logged from Twitter
        CREATE EXTERNAL TABLE tweets_raw (
            json_response STRING
        )
        STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
        -- Drop and recreate the destination table
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
        -- Select tweets from the imported data, parse the JSON,
        -- and insert into the tweets table
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
        
        
3. Premere __Ctrl + X__, quindi __Y__ per salvare il file.

4. Usare il comando riportato di seguito per eseguire lo script HiveQL contenuto nel file:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i twitter.hql
        
    In questo modo, la shell di Hive verrà caricata, HiveQL verrà eseguito nel file __twitter.hql__ e infine verrà restituito un prompt di `jdbc:hive2//localhost:10001/>`.
    
5. Dal prompt dei comandi di Beeline, usare il codice seguente per verificare la possibilità di selezionare i dati dalla tabella dei __tweet__ creata da HiveQL nel file __twitter.hql__:
        
        SELECT name, screen_name, count(1) as cc
            FROM tweets
            WHERE text like "%Azure%"
            GROUP BY name,screen_name
            ORDER BY cc DESC LIMIT 10;

    Verrà restituito un massimo di 10 tweet contenenti la parola __Azure__ nel testo del messaggio.

##<a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come trasformare un set di dati JSON non strutturato in una tabella Hive strutturata per eseguire query, esplorare e analizzare dati da Twitter tramite HDInsight in Azure. Per altre informazioni, vedere:

- [Introduzione all'uso di HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Analizzare i dati sui ritardi dei voli con HDInsight](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter



<!--HONumber=Oct16_HO2-->


