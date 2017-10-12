---
title: 'Analizzare i dati di Twitter con Apache Hive: Azure HDInsight | Microsoft Docs'
description: Informazioni su come usare Hive e Hadoop in HDInsight per trasformare i dati di Twitter non elaborati in una tabella Hive ricercabile.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e1e249ed-5f57-40d6-b3bc-a1b4d9a871d3
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: b8656123fa9c5158f366872ab050f370080ec18a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-twitter-data-using-hive-and-hadoop-on-hdinsight"></a>Analizzare i dati di Twitter mediante Hive e Hadoop in HDInsight

Informazioni su come usare Apache Hive per elaborare i dati di Twitter. Il risultato è un elenco di utenti Twitter che hanno inviato il maggior numero di tweet contenenti una determinata parola.

> [!IMPORTANT]
> I passaggi descritti in questo documento sono stati testati in HDInsight 3.6.
>
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="get-the-data"></a>Ottenere i dati

Twitter consente di recuperare i [dati relativi ad ogni tweet](https://dev.twitter.com/docs/platform-objects/tweets) come documento JSON (JavaScript Object Notation) attraverso un'API REST. [OAuth](http://oauth.net) .

### <a name="create-a-twitter-application"></a>Creare un'applicazione Twitter

1. Da un Web browser accedere a [https://apps.twitter.com/](https://apps.twitter.com/). Se non si dispone di un account Twitter, fare clic sul collegamento **Sign up now** (Registrati ora).

2. Fare clic su **Create New App**.

3. Compilare i campi **Name**, **Description**, **Website**. Per il campo **Website** è possibile creare un URL fittizio. Nella tabella seguente vengono mostrati alcuni valori di esempio da usare:

   | Campo | Valore |
   |:--- |:--- |
   | Nome |MyHDInsightApp |
   | Descrizione |MyHDInsightApp |
   | Website |http://www.myhdinsightapp.com |

4. Fare clic su **Yes, I agree** e su **Create your Twitter application**.

5. Fare clic sulla scheda **Permissions** . L'autorizzazione predefinita è **Read only**.

6. Fare clic sulla scheda **Keys and Access Tokens** .

7. Fare clic su **Create my access token**.

8. Fare clic su **Test OAuth** nell'angolo superiore destro della pagina.

9. Compilare i campi **Consumer key**, **Consumer secret**, **Access token** e **Access token secret**.

### <a name="download-tweets"></a>Scaricare tweet

Il codice Python seguente consente di scaricare 10.000 tweet da Twitter e salvarli in un file denominato **tweets.txt**.

> [!NOTE]
> I passaggi indicati di seguito vengono eseguiti nel cluster HDInsight, poiché Python è già installato.

1. Connettersi al cluster HDInsight usando SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Per installare i moduli [Tweepy](http://www.tweepy.org/) e [Progressbar](https://pypi.python.org/pypi/progressbar/2.2) e altri pacchetti obbligatori, usare i comandi seguenti:

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

4. Usare il comando seguente per creare un file denominato **gettweets.py**:

   ```bash
   nano gettweets.py
   ```

5. Usare il testo seguente come contenuto del file **gettweets.py**:

   ```python
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

   #Create the listener class that receives and saves tweets
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
   ```

    > [!IMPORTANT]
    > Sostituire il testo segnaposto per gli elementi seguenti con le informazioni fornite dall'app Twitter:
    >
    > * `consumer_secret`
    > * `consumer_key`
    > * `access_token`
    > * `access_token_secret`

6. Usare **Ctrl + X**, quindi premere **Y** per salvare il file.

7. Usare il comando seguente per aprire il file e scaricare i tweet:

    ```bash
    python gettweets.py
    ```

    Viene visualizzato un indicatore di stato che mostra un valore fino al 100% mentre vengono scaricati i tweet.

   > [!NOTE]
   > Se l'indicatore di stato impiega molto tempo per avanzare, è necessario modificare il filtro per monitorare gli argomenti di tendenza. Quando sono presenti molti tweet relativi all'argomento del filtro, è possibile ottenere rapidamente i 10000 tweet necessari.

### <a name="upload-the-data"></a>Caricare i dati

Usare i comandi seguenti per aggiornare i dati nell'archiviazione HDInsight:

   ```bash
   hdfs dfs -mkdir -p /tutorials/twitter/data
   hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Questi comandi archiviano i dati in un percorso accessibile a tutti i nodi del cluster.

## <a name="run-the-hiveql-job"></a>Eseguire il processo di HiveQL

1. Usare il comando seguente per creare un file contenente istruzioni HiveQL:

   ```bash
   nano twitter.hql
   ```

    Usare il testo seguente come contenuto del file:

   ```hiveql
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
       json_response
   WHERE (length(json_response) > 500);
   ```

2. Premere **Ctrl + X**, quindi **Y** per salvare il file.
3. Usare il comando riportato di seguito per eseguire lo script HiveQL contenuto nel file:

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    Questo comando esegue il file **twitter.hql**. Dopo il completamento della query, viene visualizzato un prompt `jdbc:hive2//localhost:10001/>`.

4. Dal prompt dei comandi Beeline usare la query seguente per verificare che i dati siano stati importati:

   ```hiveql
   SELECT name, screen_name, count(1) as cc
       FROM tweets
       WHERE text like "%Azure%"
       GROUP BY name,screen_name
       ORDER BY cc DESC LIMIT 10;
   ```

    Viene restituito un massimo di 10 tweet contenenti la parola **Azure** nel testo del messaggio.

## <a name="next-steps"></a>Passaggi successivi

Si è appreso come trasformare un set di dati JSON non strutturato in una tabella Hive strutturata. Per altre informazioni su Hive in HDInsight, vedere i documenti seguenti:

* [Introduzione all'uso di HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Analizzare i dati sui ritardi dei voli con HDInsight](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter
