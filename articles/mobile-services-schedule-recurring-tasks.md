<properties 
	pageTitle="Pianificare attività di back-end con l'Utilità di pianificazione - Servizi mobili" 
	description="Uso dell'utilità di pianificazione di Servizi mobili di Azure per pianificare processi per l'app mobile." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="glenga"/>

# Pianificare processi ricorrenti in Servizi mobili 

<div class="dev-center-tutorial-subselector">
	<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/" title=".NET backend">Back-end .NET</a> | <a href="/it-it/documentation/articles/mobile-services-schedule-recurring-tasks/"  title="JavaScript backend" class="current">Back-end JavaScript</a>
</div>
 
Questo argomento descrive come usare la funzionalità di pianificazione processi nel portale di gestione per definire codice dello script del server da eseguire in base a una pianificazione definita dall'utente. Lo script esegue verifiche periodiche con un servizio remoto, in questo caso Twitter, e archivia i risultati in una nuova tabella. Di seguito sono riportate altre attività periodiche che è possibile pianificare:

+ Archiviazione di record di dati obsoleti o duplicati.
+ Richiesta e archiviazione di dati esterni, ad esempio tweet, voci RSS e informazioni sulla posizione.
+ Elaborazione o ridimensionamento delle immagini archiviate.

In questa esercitazione vengono descritte le procedure per usare la pianificazione processi per creare un processo pianificato che richiede i dati dei tweet da Twitter e li archivia in una nuova tabella Updates:

+ [Registrarsi per l'accesso a Twitter e archiviare le credenziali]
+ [Creare la nuova tabella Updates]
+ [Creare un nuovo processo pianificato]

##<a name="get-oauth-credentials"></a>Registrazione per l'accesso alle API di Twitter v1.1 e archiviazione delle credenziali

[AZURE.INCLUDE [mobile-services-register-twitter-access](../includes/mobile-services-register-twitter-access.md)]

##<a name="create-table"></a>Creare la nuova tabella Updates

In seguito, verrà aggiunta una nuova tabella in cui archiviare i tweet.

2. Nel portale di gestione fare clic sulla scheda **Dati** per il servizio mobile e quindi su **+Crea**.

   	![][2]

   	Verrà visualizzata la finestra di dialogo **Crea nuova tabella**.

3. In **Nome tabella** digitare _Updates_, quindi fare clic sul segno di spunta.

   	![][3]

  	Verrà creata una nuova tabella di archiviazione **Updates**. 

##<a name="add-job"></a>Creare un nuovo processo pianificato  

Ora è possibile creare l'attività pianificata che accede a Twitter e archivia i dati dei tweet nella nuova tabella Updates.

2. Fare clic sulla scheda **Utilità di pianificazione**, quindi su **+Crea**. 

   	![][4]

    >[AZURE.NOTE]Quando si esegue il servizio mobile nella modalità <em>Gratuita</em>, è possibile eseguire un solo processo pianificato alla volta. Nelle modalità a pagamento è invece possibile eseguire fino a dieci processi pianificati contemporaneamente.

3. Nella finestra di dialogo dell'utilità di pianificazione immettere getUpdates per **Nome processo**, impostare le unità e l'intervallo di pianificazione e quindi fare clic sul segno di spunta. 
   
   	![][5]

   	Verrà creato un nuovo processo denominato **getUpdates**. 

4. Fare clic sul nuovo processo appena creato e quindi sulla scheda **Script**.

   	![][6] 

5. Sostituire la funzione segnaposto **getUpdates** con il codice seguente:

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

    >[AZURE.NOTE]In questo esempio si presuppone che a ogni esecuzione pianificata vengano inserite solo alcune righe nella tabella. Nei casi in cui per ogni ciclo vengano inserite molte righe, è possibile che nella modalità gratuita le connessioni disponibili vengano esaurite. In questo caso, è consigliabile effettuare gli inserimenti in batch. Per altre informazioni, vedere <a href="/it-it/develop/mobile/how-to-guides/work-with-server-scripts/#bulk-inserts">Procedura: Eseguire inserimenti bulk</a>.

6. Fare clic su **Esegui una volta** per testare lo script. 

  	![][7]

   	Il processo verrà salvato ed eseguito pur rimanendo disabilitato nell'utilità di pianificazione.

7. Fare clic sul pulsante Indietro, selezionare la scheda **Dati**, fare clic sulla tabella **Updates**, quindi su **Sfoglia** e verificare che i dati di Twitter siano stati inseriti nella tabella.

   	![][8]

8. Fare clic sul pulsante Indietro, quindi su **Utilità di pianificazione**, selezionare **getUpdates** e infine fare clic su **Abilita**.

   	![][9]

   	Il processo verrà abilitato per l'esecuzione in base alla pianificazione selezionata, in questo caso ogni ora.

In questa esercitazione è stato creato un nuovo processo pianificato nel servizio mobile. Il processo verrà eseguito come pianificato fino a quando non verrà disabilitato o modificato.

## <a name="nextsteps"> </a>Passaggi successivi

* [Informazioni di riferimento sugli script del server di Servizi mobili]
  <br/>Altre informazioni sulla registrazione e l'uso di script del server.

<!-- Anchors. -->
[Registrarsi per l'accesso a Twitter e archiviare le credenziali]: #get-oauth-credentials
[Creare la nuova tabella Updates]: #create-table
[Creare un nuovo processo pianificato]: #add-job
[Passaggi successivi]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-schedule-recurring-tasks/mobile-twitter-my-apps.png
[1]: ./media/mobile-services-schedule-recurring-tasks/mobile-twitter-app-secrets.png
[2]: ./media/mobile-services-schedule-recurring-tasks/mobile-data-tab-empty-cli.png
[3]: ./media/mobile-services-schedule-recurring-tasks/mobile-create-updates-table.png
[4]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
[5]: ./media/mobile-services-schedule-recurring-tasks/mobile-create-job-dialog.png
[6]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script-new.png
[7]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script.png
[8]: ./media/mobile-services-schedule-recurring-tasks/mobile-browse-updates-table.png
[9]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-enabled.png
[10]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-app-settings.png
[11]: ./media/mobile-services-schedule-recurring-tasks/mobile-identity-tab-twitter-only.png

<!-- URLs. -->
[Informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
[WindowsAzure.com]: http://azure.microsoft.com/
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Registrare le app per l'autenticazione di Twitter con Servizi mobili]: /it-it/develop/mobile/how-to-guides/register-for-twitter-authentication
[Sviluppatori di Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Impostazioni app]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7


<!--HONumber=42-->
