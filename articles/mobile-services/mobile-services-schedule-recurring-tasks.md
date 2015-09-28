<properties
	pageTitle="Pianificare le attività di back-end in un servizio mobile back-end di JavaScript | Microsoft Azure"
	description="Utilizzare l'utilità di pianificazione in servizi mobili di Azure per definire i processi back-end JavaScript eseguiti in una pianificazione."
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
	ms.date="09/14/2015"
	ms.author="glenga"/>

# Pianificare i processi ricorrenti in Servizi mobili

> [AZURE.SELECTOR]
- [.NET backend](mobile-services-dotnet-backend-schedule-recurring-tasks.md)
- [Javascript backend](mobile-services-schedule-recurring-tasks.md)

Questo argomento illustra come usare la funzionalità di pianificazione processi nel portale di gestione per definire il codice dello script del server da eseguire in base a una pianificazione definita dall'utente. Lo script esegue verifiche periodiche con un servizio remoto, in questo caso Twitter, e archivia i risultati in una nuova tabella. Di seguito sono riportate altre attività periodiche che è possibile pianificare:

+ Archiviazione di record di dati obsoleti o duplicati.
+ Richiesta e archiviazione di dati esterni, ad esempio tweet, voci RSS e informazioni sulla posizione.
+ Elaborazione o ridimensionamento delle immagini archiviate.

Questa esercitazione descrive come usare la pianificazione processi per creare un processo pianificato che richiede i dati dei tweet da Twitter e li archivia in una nuova tabella Updates.

##<a name="get-oauth-credentials"></a>Registrarsi per l'accesso alle API di Twitter 1.1 e l'archiviazione delle credenziali

[AZURE.INCLUDE [mobile-services-register-twitter-access](../../includes/mobile-services-register-twitter-access.md)]

##<a name="create-table"></a>Creare la nuova tabella Updates

In seguito, verrà aggiunta una nuova tabella in cui archiviare i tweet.

2. Nel portale di gestione fare clic sulla scheda **Dati** per il servizio mobile e quindi su **+Crea**.

3. In **Nome tabella** digitare _Updates_ e quindi fare clic sul segno di spunta.

##<a name="add-job"></a>Creare un nuovo processo pianificato  

Ora è possibile creare l'attività pianificata che accede a Twitter e archivia i dati dei tweet nella nuova tabella Updates.

2. Fare clic sulla scheda **Utilità di pianificazione** e quindi su **+Crea**.

    >[AZURE.NOTE]Quando si esegue il servizio mobile nella modalità <em>gratuita</em>, è possibile eseguire un solo processo pianificato alla volta. Nelle modalità a pagamento è invece possibile eseguire fino a dieci processi pianificati contemporaneamente.

3. Nella finestra di dialogo dell'utilità di pianificazione immettere _getUpdates_ per **Nome processo**, impostare le unità e l'intervallo di pianificazione e quindi fare clic sul segno di spunta.

   	In questo modo viene creato un nuovo processo denominato **getUpdates**.

4. Fare clic sul nuovo processo appena creato, scegliere la scheda **Script** e sostituire la funzione segnaposto **getUpdates** con il codice seguente:

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


   	Lo script chiama l'API di query Twitter usando le credenziali archiviate per richiedere i tweet recenti contenenti l'hashtag `#mobileservices`. I tweet duplicati e le risposte sono rimossi dai risultati prima di essere archiviati nella tabella.

    >[AZURE.NOTE]In questo esempio si presuppone che a ogni esecuzione pianificata vengano inserite solo alcune righe nella tabella. Nei casi in cui per ogni ciclo vengano inserite molte righe, è possibile che nella modalità gratuita le connessioni disponibili vengano esaurite. In questo caso, è consigliabile eseguire gli inserimenti in batch. Per altre informazioni, vedere [Procedura: Eseguire inserimenti bulk](mobile-services-how-to-use-server-scripts.md#bulk-inserts).

6. Fare clic su **Esegui una volta** per testare lo script.

   	Il processo verrà salvato ed eseguito pur rimanendo disabilitato nell'utilità di pianificazione.

7. Fare clic sul pulsante Indietro, selezionare la scheda **Dati**, fare clic sulla tabella **Updates**, quindi su **Sfoglia** e verificare che i dati di Twitter siano stati inseriti nella tabella.

8. Fare clic sul pulsante Indietro, quindi su **Utilità di pianificazione**, **getUpdates** e infine su **Abilita**.

   	Il processo verrà abilitato per l'esecuzione in base alla pianificazione selezionata, in questo caso ogni ora.

In questa esercitazione è stato creato un nuovo processo pianificato nel servizio mobile. Il processo verrà eseguito come pianificato fino a quando non verrà disabilitato o modificato.

## <a name="nextsteps"> </a>Vedere anche

* [Informazioni di riferimento sugli script del server di Servizi mobili] <br/>Altre informazioni sulla registrazione e l'uso degli script del server.

<!-- Anchors. -->
[Register for Twitter access and store credentials]: #get-oauth-credentials
[Create the new Updates table]: #create-table
[Create a new scheduled job]: #add-job
[Next steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
[WindowsAzure.com]: http://www.windowsazure.com/
[Azure Management Portal]: https://manage.windowsazure.com/
[Register your apps for Twitter login with Mobile Services]: /develop/mobile/how-to-guides/register-for-twitter-authentication
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!---HONumber=Sept15_HO3-->