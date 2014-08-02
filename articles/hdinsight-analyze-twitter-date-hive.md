<properties linkid="manage-services-hdinsight-howto-social-data" urlDisplayName="Analyze Data with HDInsight" pageTitle="Analyzing Twitter data with Hive - Windows Azure Services" metaKeywords="" description="In this tutorial you will query, explore, and analyze data from Twitter using the HDInsight Service for Windows Azure and a complex Hive example." metaCanonical="" services="" documentationCenter="" title="Analyzing Twitter Data with Hive" authors="" solutions="" writer="wenming" manager="" editor="" />

Analisi dei dati di Twitter con Hive
====================================

In questa esercitazione verranno eseguite query, verranno esplorati e analizzati i dati di Twitter tramite un servizio HDInsight basato su Apache Hadoop per Azure e un esempio complesso relativo a Hive. I siti Web di social networking rappresentano una delle principali forze trainanti per l'adozione di Big Data. Le API pubbliche offerte da siti quali Twitter costituiscono un'utile origine di dati per l'analisi e la comprensione delle tendenze più popolari. In questa esercitazione si presuppone che un cluster HDInsight sia già stato creato tramite il [portale di Azure](http://www.windowsazure.com).

Questa esercitazione è suddivisa nelle sezioni seguenti:

1.  [Recupero di feed di Twitter tramite cURL e l'API di streaming di Twitter](#segment1)
2.  [Elaborazione interattiva di dati di Twitter tramite Hive](#segment2)

## Recupero di feed di Twitter tramite cURL e l'API di streaming di Twitter

1.  Per questo passaggio è necessario curl.exe. Scaricare il file curl appropriato per il sistema operativo in uso (ad esempio il file SSL binario per Win64) da <http://curl.haxx.se/download.html>.

    ![Figura 3](./media/hdinsight-analyze-twitter-date-hive/Figure3.png)

2.  Estrarre il file **curl.exe** nel percorso corretto, ad esempio **C:\\twitterdata**.

3.  Creare questi due file utilizzando il Blocco note, **get\_twitter\_stream.cmd** e **twitter\_params.txt** nella stessa cartella del file **curl.exe**, come indicato di seguito:

    ![Figura 4](./media/hdinsight-analyze-twitter-date-hive/Figure4.png)

4.  Modificare il file **twitter\_params.txt** in modo da tenere traccia dei propri tweet, come indicato di seguito:

         track=weather,Azure,WindowsAzure,cloud

    È possibile utilizzare gli argomenti o gli hashtag desiderati.

5.  Modificare lo script **get\_twitter\_stream.cmd** del comando della finestra, aggiungendo il proprio nome utente di Twitter al posto di **USER** e la propria password al posto di **PASSWORD** nella riga seguente:

         curl -d @twitter_parameters.txt -k https://stream.twitter.com/1/statuses/filter.json -u user:{password} >>twitter_stream_seq2.txt

6.  Eseguire lo script get\_twitter\_stream.cmd da un prompt dei comandi, come indicato di seguito:

    ![Figura 5](./media/hdinsight-analyze-twitter-date-hive/Figure5.png)

    Dovrebbe essere visualizzata una schermata analoga alla seguente:

    ![Figura 6](./media/hdinsight-analyze-twitter-date-hive/Figure6.png)

7.  È possibile interrompere il processo premendo **CTRL+C**. È quindi possibile rinominare il file e riavviare lo script. È possibile lasciare in esecuzione il processo per un intervallo di tempo compreso tra 10 minuti e varie ore. Ai fini dell'esercitazione, limitare le dimensioni dei dati ad alcune centinaia di megabyte.

    I dati di Twitter vengono archiviati nel formato JSon, che include una struttura nidificata complessa. Nel passaggio successivo, invece di scrivere molte righe di codice utilizzando un linguaggio di programmazione convenzionale, tale struttura nidificata verrà trasformata in una tabella Hive, in modo da consentire l'esecuzione di query interattive da parte di un linguaggio analogo a SQL, denominato HQL.

## Elaborazione interattiva di dati di Twitter tramite Hive

1.  Durante la raccolta di tweet, creare una sessione RDP nel cluster HDInsight creato tramite il portale di Azure.

    **Passare** a HDInsight, quindi **selezionare** il cluster creato. Fare clic sull'icona **Connect RDP** nella parte inferiore della schermata. Accedere alla sessione RDP digitando la password. Dopo avere effettuato l'accesso, **aprire** una finestra di Esplora risorse e passare a c:.

    ![Connessione tramite l'icona RDP](./media/hdinsight-analyze-twitter-date-hive/twitter-RDPconnect.PNG)

2.  Premere **CTRL+C** per interrompere il processo di raccolta di tweet Curl, utilizzare Esplora risorse per passare alla posizione in cui si trova il file relativo a Twitter. **Fare clic con il pulsante destro del mouse** per l'invio a Compressed (cartella compressa), in modo da ridurre il tempo di caricamento.

3.  Premere quindi **CTRL-C** sul file con estensione zip selezionato nella macchina locale e quindi passare a c:\\ nell'unità C: della sessione di Desktop remoto. Premere **CTRL-V** dopo avere fatto clic nella finestra di Esplora risorse (C:) per scaricare il file con estensione zip tramite la sessione RDP.

    ![Caricamento di tweet tramite la sessione RDP](./media/hdinsight-analyze-twitter-date-hive/twitter-uploadingzip.PNG)

4.  Dopo il caricamento del file, **fare clic con il pulsante destro del mouse**, quindi **scegliere** Estrai tutto in c:\\ per recuperare il file di testo originale. **Aprire** una finestra della riga di comando di Hadoop per iniziare a utilizzare i comandi di HIVE e Hadoop.

    ![Figura 26](./media/hdinsight-analyze-twitter-date-hive/Figure26.png)

5.  **Digitare** innanzitutto C:\\ e quindi **premere INVIO**. Verrà visualizzata la cartella c:\\ in cui si trovano i dati di Twitter.

6.  Creare quindi una cartella nel file system distribuito di HDFS o Hadoop, quindi copiare i dati di Twitter nella cartella appena creata utilizzando l'opzione -copyFromLocal:

         hadoop fs -mkdir /example/data
         hadoop fs -copyFromLocal c:\twitter_stream_seq2.txt /example/data/

7.  I dati non elaborati di Twitter sono stati copiati in HDFS nel cluster HDInsight. Il passaggio successivo consente di creare una semplice struttura di tabella per i dati caricati. Tale tabella strutturata temporanea di Hive consente di mantenere i dati e di eseguire ulteriori operazioni di elaborazione ETL. Digitare notepad, incollare il codice seguente nel Blocco note e salvarlo senza modifiche: "c:\\load\_twitter\_raw.hql"

         drop table twitter_raw;

         create table twitter_raw (
             json_response string
         ) 
         partitioned by (filesequence int);

         load data inpath '/example/data/twitter_stream_seq2.txt'
         into table twitter_raw
         partition (filesequence = 1);

8.  Dopo il salvataggio del file, eseguire Hive nella finestra di comando:

         C:\apps\dist\hive-0.9.0\bin\hive -f load_twitter_raw.hql

9.  Per il completamento del processo sono necessari alcuni secondi:

    ![Risultati caricamento dati di Twitter non elaborati](./media/hdinsight-analyze-twitter-date-hive/twitter-load-raw-results.PNG)

10. Il set di dati di grandi dimensioni di Twitter in formato JSon nidificato è stato ora trasformato in una struttura di tabella Hive temporanea.

11. Prima di eseguire query rispetto al set di dati di Twitter utilizzando Hive, è necessario eseguire un altro processo ETL. Verrà definito uno schema di tabella più dettagliato per i dati archiviati nella tabella "twitter\_raw". L'esecuzione di questo processo ETL più complesso richiede più tempo. Al prompt dei comandi avviare di nuovo il Blocco note per incollare il codice di query di Hive, quindi salvarlo come "c:\\twitter\_etl.hql.txt".

        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode=nonstrict;

        drop table twitter_temp;

        create table twitter_temp
            (
                id bigint,
            created_at string,
            created_at_date string,
            created_at_year string,
            created_at_month string,
            created_at_day string,
            created_at_time string,
            in_reply_to_user_id_str string,
            text string,
            contributors string,
            retweeted string,
            truncated string,
            coordinates string,
            source string,
            retweet_count int,
            url string,
            hashtags array<string>,
            user_mentions array<string>,
            first_hashtag string,
            first_user_mention string,
            screen_name string,
            name string,
            followers_count int,
            listed_count int,
            friends_count int,
            lang string,
            user_location string,
            time_zone string,
            profile_image_url string,
            json_response string
            )
            partitioned by (filesequence int);

        from twitter_raw
        insert overwrite table twitter_temp
        partition (filesequence)
        select
            cast(get_json_object(json_response, '$.id_str') as bigint),

            get_json_object(json_response, '$.created_at'),
            concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
                substr (get_json_object(json_response, '$.created_at'),27,4)),

            substr (get_json_object(json_response, '$.created_at'),27,4),

            case substr (get_json_object(json_response,     '$.created_at'),5,3)
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
            cast (get_json_object(json_response, '$.retweet_count') as int),
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
            cast (get_json_object(json_response, '$.user.followers_count') as int),
            cast (get_json_object(json_response, '$.user.listed_count') as int),
            cast (get_json_object(json_response, '$.user.friends_count') as int),
            get_json_object(json_response, '$.user.lang'),
            get_json_object(json_response, '$.user.location'),
            get_json_object(json_response, '$.user.time_zone'),
            get_json_object(json_response, '$.user.profile_image_url'),
            json_response,
            filesequence
        where (length(json_response) > 500);

12. Digitare:

        C:\apps\dist\hive-0.9.0\bin\hive -f twitter_etl.hql.txt

13. Questo script complesso di Hive consente di avviare una serie di processi Map Reduce dal cluster Hadoop. In base al set di dati e alla dimensione del cluster, tale operazione potrebbe richiedere circa 10 minuti.

    ![twitter complex etl](./media/hdinsight-analyze-twitter-date-hive/twitter-etl-complex.PNG)

14. È inoltre possibile monitorare l'avanzamento del processo tramite la pagina corrispondente sul nodo head facendo doppio clic sul collegamento "Hadoop MapReduce Status" sul desktop.

    ![Monitoraggio e verifica dei processi](./media/hdinsight-analyze-twitter-date-hive/twitter_longjob_browser.PNG)

15. Al termine del processo ETL, avviare Hive:

        C:\apps\dist\hive-0.9.0\bin\hive

16. È possibile provare a utilizzare semplici query Hive analoghe alle seguenti:

        select name, screen\_name, count(1) as cc from twitter\_temp where text like "%Azure%" group by name,screen\_name order by cc desc limit 10;

17.  Questa query restituirà un elenco di utenti di Twitter che hanno inviato il numero maggiore di tweet contenenti la parola "Azure".

    ![Risultato finale](./media/hdinsight-analyze-twitter-date-hive/twitter_final_query_result.PNG)

## Riepilogo

In questa esercitazione è stato illustrato come trasformare un set di dati Json non strutturato in una tabella Hive strutturata per consentire l'esecuzione di query, l'esplorazione e l'analisi di dati da Twitter tramite HDInsight in Azure. Per aggiornamenti e file di supporto, vedere l'archivio GitHub, disponibile [qui](https://github.com/wenming/BigDataSamples/tree/master/twittersample).

Passaggi successivi
-------------------

Benché questo articolo illustri l'utilizzo della riga di comando di Hadoop, è possibile eseguire queste attività anche utilizzando la console interattiva del servizio HDInsight. Per ulteriori informazioni, vedere [Linee guida: console JavaScript e Hive interattive di HDInsight][interactive-console].

