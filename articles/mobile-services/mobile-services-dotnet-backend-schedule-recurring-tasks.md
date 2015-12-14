<properties
	pageTitle="Pianificare le attività di back-end in un servizio mobile back-end di .NET | Microsoft Azure"
	description="Utilizzare l'utilità di pianificazione in servizi mobili di Azure per definire i processi back-end .NET eseguiti in una pianificazione."
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

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


> [AZURE.SELECTOR]
- [.NET backend](mobile-services-dotnet-backend-schedule-recurring-tasks.md)
- [Javascript backend](mobile-services-schedule-recurring-tasks.md)

Questo argomento illustra come usare la funzionalità dell’utilità di pianificazione processi nel portale di Azure classico per definire il codice dello script del server da eseguire in base a una pianificazione definita dall'utente. Lo script esegue verifiche periodiche con un servizio remoto, in questo caso Twitter, e archivia i risultati in una nuova tabella. Di seguito sono riportate altre attività periodiche che è possibile pianificare:

+ Archiviazione di record di dati obsoleti o duplicati.
+ Richiesta e archiviazione di dati esterni, ad esempio tweet, voci RSS e informazioni sulla posizione.
+ Elaborazione o ridimensionamento delle immagini archiviate.

Questa esercitazione descrive come usare la pianificazione processi per creare un processo pianificato che richiede i dati dei tweet da Twitter e li archivia in una nuova tabella Updates:

>[AZURE.NOTE]In questa esercitazione viene usata la libreria LINQ to Twitter di terze parti per semplificare l'accesso OAuth 2.0 alle API di Twitter v1.1 . Per completare questa esercitazione è necessario scaricare e installare il pacchetto NuGet della libreria LINQ to Twitter. Per altre informazioni vedere il [progetto CodePlex di LINQ to Twitter].

##<a name="get-oauth-credentials"></a>Registrarsi per l'accesso alle API di Twitter 1.1 e l'archiviazione delle credenziali

[AZURE.INCLUDE [mobile-services-register-twitter-access](../../includes/mobile-services-register-twitter-access.md)]

&nbsp;&nbsp;7. In Esplora soluzioni di Visual Studio aprire il file web.config relativo al progetto di servizio mobile, individuare le impostazioni dell'app `MS_TwitterConsumerKey` e `MS_TwitterConsumerSecret` e sostituire i valori di queste chiavi con i valori di chiave e segreto consumer di Twitter configurate nel portale.

&nbsp;&nbsp;8. Nella stessa sezione, aggiungere le nuove impostazioni dell'app elencate di seguito, sostituendo i segnaposto con i valori di token di accesso e di segreto token di accesso configurati come impostazioni app nel portale:

	<add key="TWITTER_ACCESS_TOKEN" value="**your_access_token**" />
	<add key="TWITTER_ACCESS_TOKEN_SECRET" value="**your_access_token_secret**" />

Il servizio mobile utilizza le impostazioni memorizzate quando è in esecuzione sul computer locale, consentendo in tal modo di testare il processo pianificato prima di pubblicarlo. Quando è in esecuzione in Azure, il servizio mobile utilizza invece i valori configurati nel portale, ignorando le impostazioni di progetto.

##<a name="install-linq2twitter"></a>Scaricare e installare la libreria LINQ to Twitter

1. In **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Manage NuGet Packages**.

2. Nel riquadro sinistro selezionare la categoria **Online**, cercare `linq2twitter`, fare clic su **Installa** nel pacchetto **linqtotwitter**, quindi leggere e accettare il contratto di licenza.

  	![][1]

  	La libreria LINQ to Twitter verrà aggiunta al progetto di servizio mobile.

In seguito, verrà aggiunta una nuova tabella in cui archiviare i tweet.

##<a name="create-table"></a>Creare la nuova tabella Updates

1. In Esplora soluzioni in Visual Studio fare clic con il pulsante destro del mouse sulla cartella DataObjects, espandere **Aggiungi**, fare clic su **Classe**, digitare `Updates` nella sezione **Nome** e quindi fare clic su **Aggiungi**.

	Viene creato un nuovo file di progetto per la classe Updates.

2. Fare clic con il pulsante destro del mouse su **Riferimenti**, scegliere **Aggiungi riferimento**, selezionare **Framework** in **Assembly**, selezionare **System.ComponentModel.DataAnnotations**, quindi fare clic su **OK**.

	![][7]

	Viene aggiunto un nuovo riferimento ad assembly.

2. In questa nuova classe aggiungere le istruzioni **using** seguenti:

		using Microsoft.WindowsAzure.Mobile.Service;
		using System.ComponentModel.DataAnnotations;

3. Sostituire la definizione della classe **Updates** con il codice seguente:

		public class Updates
	    {
	        [Key]
	        public int UpdateId { get; set; }
	        public long TweetId { get; set; }
	        public string Text { get; set; }
	        public string Author { get; set; }
	        public DateTime Date { get; set; }
    	}

4. Espandere la cartella Models, aprire il file del contesto modello di dati (denominato *nome\_servizio*Context.cs) e aggiungere la proprietà seguente che restituisce una classe **DbSet** tipizzata:

		public DbSet<Updates> Updates { get; set; }

	La tabella Updates, creata nel database al primo accesso alla classe DbSet, viene utilizzata per archiviare i dati dei tweet.

	>[AZURE.NOTE]Quando si usa l'inizializzatore del database predefinito, Entity Framework elimina e crea nuovamente il database ogni volta che rileva una modifica nel modello di dati nella definizione del modello Code First. Per apportare modifiche al modello di dati e conservare i dati esistenti nel database, è necessario utilizzare Migrazioni Code First. L'inizializzatore predefinito non può essere utilizzato su un database SQL in Azure. Per altre informazioni vedere [Come usare le Migrazioni Code First per aggiornare il modello di dati](mobile-services-dotnet-backend-use-code-first-migrations.md).

Sarà quindi possibile creare l'attività pianificata che accede a Twitter e archivia i dati dei tweet nella nuova tabella Updates.

##<a name="add-job"></a>Creare un nuovo processo pianificato

1. Espandere la cartella ScheduledJobs e aprire il file del progetto SampleJob.cs.

	Questa classe, che eredita da **ScheduledJob**, rappresenta un processo la cui esecuzione può essere pianificata nel portale di Azure classico secondo una pianificazione fissa oppure su richiesta.

2. Sostituire il contenuto del file SampleJob.cs con il codice seguente:

		using System;
		using System.Linq;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Web.Http;
		using Microsoft.WindowsAzure.Mobile.Service;
		using Microsoft.WindowsAzure.Mobile.Service.ScheduledJobs;
		using LinqToTwitter;
		using todolistService.Models;
		using todolistService.DataObjects;

		namespace todolistService
		{
		    // A simple scheduled job which can be invoked manually by submitting an HTTP
		    // POST request to the path "/jobs/sample".
		    public class SampleJob : ScheduledJob
		    {
		        private todolistContext context;
		        private string accessToken;
		        private string accessTokenSecret;

		        protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor,
					CancellationToken cancellationToken)
		        {
		            base.Initialize(scheduledJobDescriptor, cancellationToken);

		            // Create a new context with the supplied schema name.
		            context = new todolistContext();
		        }

		        public async override Task ExecuteAsync()
		        {
		            // Try to get the stored Twitter access token from app settings.
		            if (!(Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN", out accessToken) |
		            Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN_SECRET", out accessTokenSecret)))
		            {
		                Services.Log.Error("Could not retrieve Twitter access credentials.");
		            }

		            // Create a new authorizer to access Twitter v1.1 APIs
		            // using single-user OAUth 2.0 credentials.
		            MvcAuthorizer auth = new MvcAuthorizer();
		            SingleUserInMemoryCredentialStore store =
		                new SingleUserInMemoryCredentialStore()
		            {
		                ConsumerKey = Services.Settings.TwitterConsumerKey,
		                ConsumerSecret = Services.Settings.TwitterConsumerSecret,
		                OAuthToken = accessToken,
		                OAuthTokenSecret = accessTokenSecret
		            };

		            // Set the credentials for the authorizer.
		            auth.CredentialStore = store;

		            // Create a new LINQ to Twitter context.
		            TwitterContext twitter = new TwitterContext(auth);

		            // Get the ID of the most recent stored tweet.
		            long lastTweetId = 0;
		            if (context.Updates.Count() > 0)
		            {
		                lastTweetId = (from u in context.Updates
		                               orderby u.TweetId descending
		                               select u).Take(1).SingleOrDefault()
		                                            .TweetId;
		            }

		            // Execute a search that returns a filtered result.
		            var response = await (from s in twitter.Search
		                                  where s.Type == SearchType.Search
		                                  && s.Query == "%23mobileservices"
		                                  && s.SinceID == Convert.ToUInt64(lastTweetId + 1)
		                                  && s.ResultType == ResultType.Recent
		                                  select s).SingleOrDefaultAsync();

		            // Remove retweets and replies and log the number of tweets.
		            var filteredTweets = response.Statuses
		                .Where(t => !t.Text.StartsWith("RT") && t.InReplyToUserID == 0);
		            Services.Log.Info("Fetched " + filteredTweets.Count()
		                + " new tweets from Twitter.");

		            // Store new tweets in the Updates table.
		            foreach (Status tweet in filteredTweets)
		            {
		                Updates newTweet =
		                    new Updates
		                    {
		                        TweetId = Convert.ToInt64(tweet.StatusID),
		                        Text = tweet.Text,
		                        Author = tweet.User.Name,
		                        Date = tweet.CreatedAt
		                    };

		                context.Updates.Add(newTweet);
		            }

		            await context.SaveChangesAsync();
		        }
		        protected override void Dispose(bool disposing)
		        {
		            base.Dispose(disposing);
		            if (disposing)
		            {
		                context.Dispose();
		            }
		        }
		    }
		}

	Nel codice precedente è necessario sostituire le stringhe _todolistService_ e _todolistContext_ con lo spazio dei nomi e la classe DbContext del progetto scaricato, che sono rispettivamente *nome&#95;servizio&#95;mobile*Servizio e nome&#95;servizio&#95;mobile*Contesto.

	Inoltre, il metodo di override **ExecuteAsync** chiama l'API query Twitter usando le credenziali archiviate per richiedere i tweet recenti contenenti l'hashtag `#mobileservices`. I tweet duplicati e le risposte sono rimossi dai risultati prima di essere archiviati nella tabella.

##<a name="run-job-locally"></a>Testare a livello locale il processo pianificato

È possibile testare a livello locale i processi pianificati prima della relativa pubblicazione in Azure e la registrazione sul portale.

1. In Visual Studio, con il progetto di servizio mobile configurato come progetto di avvio, premere F5.

	Verrà avviato il progetto di servizio mobile e sarà visualizzata una nuova finestra del browser con la pagina di benvenuto.

2. Fare clic su **Prova**, quindi su **POST jobs/{jobName}**.

	![][8]

4. Fare clic su **Prova**, digitare `Sample` come valore del parametro **{jobName}** e quindi fare clic su **Invia**.

	![][9]

	Viene inviata una nuova richiesta POST all'endpoint dei processi Sample. Nel servizio locale viene avviato il metodo **ExecuteAsync**. In questo metodo è possibile impostare un punto di interruzione per il debug del codice.

3. In Esplora server, espandere **Connessioni dati**, **MSTableConnectionString** e **Tabelle**; fare clic con il pulsante destro del mouse su **Aggiornamenti** e fare clic su **Mostra dati tabella**.

	I nuovi tweet verranno immessi come righe nella tabella dati.

##<a name="register-job"></a>Pubblicare il servizio e registrare il nuovo processo

È necessario registrare il processo nella scheda **Scheduler** in modo che sia possibile eseguirlo in base alla pianificazione definita dall'utente in Servizi mobili.

3. Ripubblicare il progetto di servizio mobile in Azure.

4. Nel [portale di Azure classico], fare clic su Servizi mobili e quindi sull'app.

2. Fare clic sulla scheda **Utilità di pianificazione** e quindi su **+Crea**.

    >[AZURE.NOTE]Quando si esegue il servizio mobile nella modalità <em>gratuita</em>, è possibile eseguire un solo processo pianificato alla volta. Nelle modalità a pagamento è invece possibile eseguire fino a dieci processi pianificati contemporaneamente.

3. Nella finestra di dialogo dell'utilità di pianificazione immettere _Sample_ per **Nome processo**, impostare le unità e l'intervallo di pianificazione e quindi fare clic sul segno di spunta.

   	![][4]

   	Viene creato un nuovo processo denominato **Sample**.

4. Fare clic sul nuovo processo appena creato e quindi su **Run Once** per testare lo script.

   	Il processo verrà eseguito pur rimanendo disabilitato nell'utilità di pianificazione. Da questa pagina è possibile abilitare il processo e modificarne la pianificazione in qualsiasi momento.

	>[AZURE.NOTE]È comunque possibile usare una richiesta POST per avviare il processo pianificato. Per impostazione predefinita, l'autorizzazione è concessa all'utente, quindi la richiesta dovrà includere la chiave applicazione nell'intestazione.

4. (Facoltativo) Nel [portale di Azure classico] fare clic su Gestisci per il database associato al servizio mobile.

    ![][6]

5. Nel portale di Azure classico eseguire una query per visualizzare le modifiche apportate dall'app. La query sarà simile a quella riportata di seguito, ma come nome dello schema verrà usato il nome del servizio mobile anziché `todolist`.

        SELECT * FROM [todolist].[Updates]

In questa esercitazione è stato creato un nuovo processo pianificato nel servizio mobile. Il processo verrà eseguito come pianificato fino a quando non verrà disabilitato o modificato.

<!-- Anchors. -->
[Register for Twitter access and store credentials]: #get-oauth-credentials
[Download and install the LINQ to Twitter library]: #install-linq2twitter
[Create the new Updates table]: #create-table
[Create a new scheduled job]: #add-job
[Test the scheduled job locally]: #run-job-locally
[Publish the service and register the job]: #register-job
[Next steps]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-linq2twitter-nuget-package.png
[2]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-services-selection.png
[3]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
[4]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/create-new-job.png
[5]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/sample-job-run-once.png
[6]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/manage-sql-azure-database.png
[7]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-component-model-reference.png
[8]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-start-page.png
[9]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-try-this-out.png

<!-- URLs. -->
[portale di Azure classico]: https://manage.windowsazure.com/
[Register your apps for Twitter login with Mobile Services]: mobile-services-how-to-register-twitter-authentication.md
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
[progetto CodePlex di LINQ to Twitter]: http://linqtotwitter.codeplex.com/

<!---HONumber=AcomDC_1203_2015-->