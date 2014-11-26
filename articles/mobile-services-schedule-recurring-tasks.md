<properties linkid="develop-mobile-tutorials-schedule-backend-tasks" urlDisplayName="Schedule Backend Tasks" pageTitle="Schedule Backend Tasks with Scheduler - Mobile Services" metaKeywords="" description="Use the Azure Mobile Services Scheduler to schedule jobs for your mobile app." metaCanonical="" services="" documentationCenter="Mobile" title="Schedule recurring jobs in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Pianificazione di processi ricorrenti in Servizi mobili

<div class="dev-center-tutorial-subselector">
    <a href="/it-it/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/" title=".NET backend">Back-end .NET</a> | <a href="/it-it/documentation/articles/mobile-services-schedule-recurring-tasks/"  title="JavaScript backend" class="current">Back-end JavaScript</a>
</div>

In questo argomento viene illustrato come utilizzare la funzionalità di pianificazione processi nel portale di gestione per definire codice dello script del server da eseguire in base a una pianificazione definita dall'utente. Lo script esegue verifiche periodiche con un servizio remoto, in questo caso Twitter, e archivia i risultati in una nuova tabella. Di seguito sono riportate altre attività periodiche che è possibile pianificare:

-   Archiviazione di record di dati obsoleti o duplicati.
-   Richiesta e archiviazione di dati esterni, ad esempio tweet, voci RSS e informazioni sulla posizione.
-   Elaborazione o ridimensionamento delle immagini archiviate.

<!-- // Removed because this shortcode b/c it's old and doesn't use the new Twitter v1.1. APIs >[AZURE.VIDEO Windows-Store-app-Getting-Started-with-the-Windows-Azure-Mobile-Services-Scheduler] -->
<!-- // Original video HTML code for reference. <div class="dev-onpage-video-clear clearfix"> <div class="dev-onpage-left-content"> <p> </div> <div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-the-Windows-Azure-Mobile-Services-Scheduler" target="_blank" class="label">watch the tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-scheduler-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-the-Windows-Azure-Mobile-Services-Scheduler" target="_blank" class="dev-onpage-video"><span class="icon">Play Video</span></a> <span class="time">5:22</span></div> </div>-->

In questa esercitazione vengono descritte le procedure per utilizzare la pianificazione processi per creare un processo pianificato che richiede i dati dei tweet da Twitter e li archivia in una nuova tabella Updates:

-   [Registrazione per l'accesso a Twitter e archiviazione delle credenziali][Registrazione per l'accesso a Twitter e archiviazione delle credenziali]
-   [Creazione della nuova tabella Updates][Creazione della nuova tabella Updates]
-   [Creazione di un nuovo processo pianificato][Creazione di un nuovo processo pianificato]

## <a name="get-oauth-credentials"></a>Registrazione per l'accesso alle API di Twitter v1.1 e archiviazione delle credenziali

[WACOM.INCLUDE [mobile-services-register-twitter-access](../includes/mobile-services-register-twitter-access.md)]

## <a name="create-table"></a>Creazione della nuova tabella Updates

In seguito, verrà aggiunta una nuova tabella in cui archiviare i tweet.

1.  Nel portale di gestione fare clic sulla scheda **Data** per il servizio mobile e quindi su **+Create**.

    ![][0]

    Verrà visualizzata la finestra di dialogo **Create new table**.

2.  In **Table name** digitare *Updates*, quindi fare clic sul segno di spunta.

    ![][1]

    Verrà creata una nuova tabella di archiviazione **Updates**.

## <a name="add-job"></a>Creazione di un nuovo processo pianificato

Ora è possibile creare l'attività pianificata che accede a Twitter e archivia i dati dei tweet nella nuova tabella Updates.

1.  Fare clic sulla scheda **Scheduler** e quindi su **+Create**.

    ![][2]

    > [WACOM.NOTE]Quando si esegue il servizio mobile nella modalità *Gratuita* è possibile eseguire un solo processo pianificato alla volta. Nelle modalità a pagamento è invece possibile eseguire fino a dieci processi pianificati contemporaneamente.

2.  Nella finestra di dialogo dell'utilità di pianificazione immettere *getUpdates* per **Job Name**, impostare le unità e l'intervallo di pianificazione e quindi fare clic sul segno di spunta.

    ![][3]

    In questo modo viene creato un nuovo processo denominato **getUpdates**.

3.  Fare clic sul nuovo processo appena creato e quindi sulla scheda **Script**.

    ![][4]

4.  Sostituire la funzione segnaposto **getUpdates** con il codice seguente:

        var updatesTable = tables.getTable('Updates');
        var request = require('request');
        var twitterUrl = "https://api.twitter.com/1.1/search/tweets.json?q=%23mobileservices&result_type=recent";

        // Get the service configuration module.
        var config = require('mobileservice-config');

        // Get the stored Twitter consumer key and secret. 
        var consumerKey = config.twitterConsumerKey,
            consumerSecret = config.twitterConsumerSecret
        // Get the Twitter access token from app settings.    
        var accessToken= config.appSettings.TWITTER_ACCESS_TOKEN,
            accessTokenSecret = config.appSettings.TWITTER_ACCESS_TOKEN_SECRET;

        function getUpdates() {   
            // Check what is the last tweet we stored when the job last ran
            // and ask Twitter to only give us more recent tweets
            appendLastTweetId(
                twitterUrl, 
                function twitterUrlReady(url){            
                    // Create a new request with OAuth credentials.
                    request.get({
                        url: url,                
                        oauth: {
                            consumer_key: consumerKey,
                            consumer_secret: consumerSecret,
                            token: accessToken,
                            token_secret: accessTokenSecret
                        }},
                        function (error, response, body) {
                        if (!error && response.statusCode == 200) {
                            var results = JSON.parse(body).statuses;
                            if(results){
                                console.log('Fetched ' + results.length + ' new results from Twitter');                       
                                results.forEach(function (tweet){
                                    if(!filterOutTweet(tweet)){
                                        var update = {
                                            twitterId: tweet.id,
                                            text: tweet.text,
                                            author: tweet.user.screen_name,
                                            date: tweet.created_at
                                        };
                                        updatesTable.insert(update);
                                    }
                                });
                            }            
                        } else { 
                            console.error('Could not contact Twitter');
                        }
                    });

                });
         }
        // Find the largest (most recent) tweet ID we have already stored
        // (if we have stored any) and ask Twitter to only return more
        // recent ones
        function appendLastTweetId(url, callback){
            updatesTable
            .orderByDescending('twitterId')
            .read({success: function readUpdates(updates){
                if(updates.length){
                    callback(url + '&since_id=' + (updates[0].twitterId + 1));           
                } else {
                    callback(url);
                }
            }});
        }

        function filterOutTweet(tweet){
            // Remove retweets and replies
            return (tweet.text.indexOf('RT') === 0 || tweet.to_user_id);
        }

    Lo script chiama l'API query Twitter usando le credenziali archiviate per richiedere i tweet recenti contenenti l'hashtag `#mobileservices`. I tweet duplicati e le risposte sono rimossi dai risultati prima di essere archiviati nella tabella.

    > [WACOM.NOTE]In questo esempio si presuppone che a ogni esecuzione pianificata vengano inserite solo alcune righe nella tabella. Nei casi in cui per ogni ciclo vengano inserite molte righe, è possibile che nella modalità gratuita le connessioni disponibili vengano esaurite. In questo caso, è consigliabile effettuare gli inserimenti in batch. Per altre informazioni, vedere [Procedura: Eseguire inserimenti bulk][Procedura: Eseguire inserimenti bulk].

5.  Fare clic su **Run Once** per testare lo script.

    ![][5]

    Il processo verrà salvato ed eseguito pur rimanendo disabilitato nell'utilità di pianificazione.

6.  Fare clic sul pulsante Indietro, selezionare la scheda **Data**, fare clic sulla tabella **Updates**, quindi su **Browse** e verificare che i dati di Twitter siano stati inseriti nella tabella.

    ![][6]

7.  Fare clic sul pulsante Indietro, quindi su **Scheduler**, **getUpdates** e infine su **Enable**.

    ![][7]

    Il processo verrà abilitato per l'esecuzione in base alla pianificazione selezionata, in questo caso ogni ora.

In questa esercitazione è stato creato un nuovo processo pianificato nel servizio mobile. Il processo verrà eseguito come pianificato fino a quando non verrà disabilitato o modificato.

## <a name="nextsteps"> </a>Passaggi successivi

-   [Riferimento per gli script del server di Servizi mobili][Riferimento per gli script del server di Servizi mobili]
    Ulteriori informazioni sulla registrazione e l'utilizzo di script del server.



  [Registrazione per l'accesso a Twitter e archiviazione delle credenziali]: #get-oauth-credentials
  [Creazione della nuova tabella Updates]: #create-table
  [Creazione di un nuovo processo pianificato]: #add-job
  [0]: ./media/mobile-services-schedule-recurring-tasks/mobile-data-tab-empty-cli.png
  [1]: ./media/mobile-services-schedule-recurring-tasks/mobile-create-updates-table.png
  [2]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
  [3]: ./media/mobile-services-schedule-recurring-tasks/mobile-create-job-dialog.png
  [4]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script-new.png
  [Procedura: Eseguire inserimenti bulk]: /it-it/develop/mobile/how-to-guides/work-with-server-scripts/#bulk-inserts
  [5]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script.png
  [6]: ./media/mobile-services-schedule-recurring-tasks/mobile-browse-updates-table.png
  [7]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-enabled.png
  [Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
