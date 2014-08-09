<properties pageTitle="Schedule Backend Tasks with Scheduler - Mobile Services" metaKeywords="" description="Use the Windows Azure Mobile Services Scheduler to schedule jobs for your mobile app." metaCanonical="" services="" documentationCenter="Mobile" title="Schedule recurring jobs in Mobile Services" authors="" solutions="" writer="" manager="" editor="" />

Pianificazione di processi ricorrenti in Servizi mobili
=======================================================

[Back-end .NET](/it-it/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/ "Back-end .NET") | [Back-end JavaScript](/it-it/documentation/articles/mobile-services-schedule-recurring-tasks/ "Back-end JavaScript")

In questo argomento viene illustrato come utilizzare la funzionalità di pianificazione processi nel portale di gestione per definire codice dello script del server da eseguire in base a una pianificazione definita dall'utente. Lo script esegue verifiche periodiche con un servizio remoto, in questo caso Twitter, e archivia i risultati in una nuova tabella. Di seguito sono riportate altre attività periodiche che è possibile pianificare:

-   Archiviazione di record di dati obsoleti o duplicati.
-   Richiesta e archiviazione di dati esterni, ad esempio tweet, voci RSS e informazioni sulla posizione.
-   Elaborazione o ridimensionamento delle immagini archiviate.

In questa esercitazione vengono descritte le procedure per utilizzare la pianificazione processi per creare un processo pianificato che richiede i dati dei tweet da Twitter e li archivia in una nuova tabella Updates:

-   [Registrazione per l'accesso a Twitter e archiviazione delle credenziali](#get-oauth-credentials)
-   [Download e installazione della libreria LINQ to Twitter](#install-linq2twitter)
-   [Creazione della nuova tabella Updates](#create-table)
-   [Creazione di un nuovo processo pianificato](#add-job)
-   [Test locale del processo pianificato](#run-job-locally)
-   [Pubblicazione del servizio e registrazione del processo](#register-job)

> [WACOM.NOTE] In questa esercitazione viene utilizzata la libreria LINQ to Twitter di terze parti per semplificare l'accesso OAuth 2.0 alle API di Twitter v1.1. Per completare questa esercitazione è necessario scaricare e installare il pacchetto NuGet della libreria LINQ to Twitter. Per ulteriori informazioni vedere il [progetto CodePlex di LINQ to Twitter](http://linqtotwitter.codeplex.com/).

Registrazione per l'accesso alle API di Twitter v1.1 e archiviazione delle credenziali
--------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-register-twitter-access](../includes/mobile-services-register-twitter-access.md)]

1.  In Visual Studio, in Esplora soluzioni aprire il file web.config relativo al progetto di servizio mobile, individuare le impostazioni dell'app **MS\_TwitterConsumerKey** e **MS\_TwitterConsumerSecret** e sostituire i valori di queste chiavi con i valori di chiave e segreto consumer di Twitter configurate nel portale.

2.  Nella stessa sezione, aggiungere le nuove impostazioni dell'app elencate di seguito, sostituendo i segnaposto con i valori di token di accesso e di segreto token di accesso configurati come impostazioni app nel portale:

    ``` {}
    <add key="TWITTER_ACCESS_TOKEN" value="**your_access_token**" />
    <add key="TWITTER_ACCESS_TOKEN_SECRET" value="**your_access_token_secret**" />
    ```

    Il servizio mobile utilizza le impostazioni memorizzate quando è in esecuzione sul computer locale, consentendo in tal modo di testare il processo pianificato prima di pubblicarlo. Quando è in esecuzione in Azure, il servizio mobile utilizza invece i valori configurati nel portale, ignorando le impostazioni di progetto.

Download e installazione della libreria LINQ to Twitter
-------------------------------------------------------

1.  In **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Manage NuGet Packages**.

2.  Nel riquadro sinistro selezionare la categoria **Online**, cercare `linq2twitter`, fare clic su **Installa** nel pacchetto **linqtotwitter**, quindi leggere e accettare il contratto di licenza.

	![](./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-linq2twitter-nuget-package.png) 
	
	La libreria Linq to Twitter verrà aggiunta al progetto di servizio mobile.

In seguito, verrà aggiunta una nuova tabella in cui archiviare i tweet.

Creazione della nuova tabella Updates
-------------------------------------

1.  In Esplora soluzioni in Visual Studio, fare clic con il pulsante destro del mouse sulla cartella DataObjects, espandere **Add**, fare clic su **Class**, digitare `Updates` nella sezione **Name**, quindi fare clic su **Add**.

    Viene creato un nuovo file di progetto per la classe Updates.

2.  In questa nuova classe aggiungere le istruzioni **using** seguenti:

         using Microsoft.WindowsAzure.Mobile.Service;
         using System.ComponentModel.DataAnnotations;

3.  Sostituire la definizione della classe **Updates** con il codice seguente:

         public class Updates 
         {
             [Key]
             public int UpdateId { get; set; }
             public long TweetId { get; set; }
             public string Text { get; set; }
             public string Author { get; set; }
             public DateTime Date { get; set; }
		}

4.  Espandere la cartella Models, aprire il contesto modello di dati (denominato *service\_name*Context.cs) e aggiungere la proprietà seguente che restituisce una classe **DbSet** tipizzata:

         public DbSet<Updates> Updates { get; set; }

    La tabella Updates, creata nel database al primo accesso alla classe DbSet, viene utilizzata per archiviare i dati dei tweet.

    > [WACOM.NOTE] Quando si utilizza l'inizializzatore del database predefinito, Entity Framework elimina e crea nuovamente il database ogni volta che rileva una modifica nel modello di dati nella definizione del modello Code First. Per apportare modifiche al modello di dati e conservare i dati esistenti nel database, è necessario utilizzare Migrazioni Code First. L'inizializzatore predefinito non può essere utilizzato su un database SQL in Azure. Per ulteriori informazioni vedere [Come utilizzare le Migrazioni Code First per aggiornare il modello di dati](/it-it/documentation/articles/mobile-services-dotnet-backend-use-code-first-migrations).

Sarà quindi possibile creare l'attività pianificata che accede a Twitter e archivia i dati dei tweet nella nuova tabella Updates.

Creazione di un nuovo processo pianificato
------------------------------------------

1.  Espandere la cartella ScheduledJobs e aprire il file del progetto SampleJob.cs.

    Questa classe, che eredita da **ScheduledJob**, rappresenta un processo la cui esecuzione può essere pianificata nel portale di gestione di Azure secondo una pianificazione fissa oppure su richiesta.

2.  Sostituire il contenuto del file SampleJob.cs con il codice seguente:

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
            
                 protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor, CancellationToken cancellationToken)
                 {
                     base.Initialize(scheduledJobDescriptor, cancellationToken);
            
                     // Create a new context with the supplied schema name.
                     context = new todolistContext(Services.Settings.Name);
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

    Nel codice precedente è necessario sostituire le stringe *todolistService* e *todolistContext* con lo spazio dei nomi e la classe DbContext del progetto scaricato, che sono rispettivamente *mobile\_service\_name*Service e *mobile\_service\_name*Context.

    Inoltre, il metodo di override **ExecuteAsync** chiama l'API query Twitter utilizzando le credenziali archiviate per richiedere i tweet recenti contenenti l'hashtag `#mobileservices`. I tweet duplicati e le risposte sono rimossi dai risultati prima di essere archiviati nella tabella.

Test locale del processo pianificato
------------------------------------

È possibile testare a livello locale i processi pianificati prima della relativa pubblicazione in Azure e la registrazione sul portale.

1.  In Visual Studio, con il progetto di servizio mobile configurato come progetto di avvio, premere F5.

    Verrà avviato il progetto di servizio mobile e sarà visualizzata una nuova finestra del browser con la pagina di benvenuto.

2.  Copiare l'URL del servizio mobile dalla finestra del browser aperta, aggiungere il percorso `/tables/samplejob` all'URL, quindi eseguire una nuova richiesta POST a questo URL mediante un'utilità HTTP, come Fiddler.

    Il metodo *\*ExecuteAsync* \*viene avviato sul computer locale.

3.  In Esplora server, espandere **Connessioni dati**, **MSTableConnectionString** e **Tabelle**; fare clic con il pulsante destro del mouse su **Aggiornamenti** e fare clic su **Mostra dati tabella**.

    I nuovi tweet verranno immessi come righe nella tabella dati.

Pubblicazione del servizio e registrazione del nuovo processo
-------------------------------------------------------------

È necessario registrare il processo nella scheda **Scheduler** in modo che sia possibile eseguirlo in base alla pianificazione definita dall'utente in Servizi mobili.

1.  Ripubblicare il progetto di servizio mobile in Azure.

2.  Nel [portale di gestione di Azure](https://manage.windowsazure.com/), fare clic su Mobile Services e quindi sull'app.

    ![](./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-services-selection.png)

3.  Fare clic sulla scheda **Scheduler** e quindi su **+Create**.

    ![][3]

    > [WACOM.NOTE]Quando si esegue il servizio mobile nella modalità *gratuita* è possibile eseguire un solo processo pianificato alla volta. Nelle modalità a pagamento è invece possibile eseguire fino a dieci processi pianificati contemporaneamente.

4.  Nella finestra di dialogo dell'utilità di pianificazione immettere *SampleJob* per **Job Name**, impostare le unità e l'intervallo di pianificazione e quindi fare clic sul segno di spunta.

    ![][4]

    Verrà creato un nuovo processo denominato **SampleJob**. 

5.  Fare clic sul nuovo processo appena creato e quindi su **Run Once** per testare lo script.

	![](./media/mobile-services-dotnet-backend-schedule-recurring-tasks/sample-job-run-once.png) 

	Il processo verrà eseguito pur rimanendo disabilitato nell'utilità di pianificazione. Da questa pagina è possibile abilitare il processo e modificarne la pianificazione in qualsiasi momento.

    >[WACOM.NOTE]È comunque possibile utilizzare una richiesta POST per avviare il processo pianificato. Per impostazione predefinita, l'autorizzazione è concessa all'utente, quindi la richiesta dovrà includere la chiave applicazione nell'intestazione.

1.  (Facoltativo) Nel [portale di gestione di Azure](https://manage.windowsazure.com/) fare clic su Manage per il database associato al servizio mobile.

    ![](./media/mobile-services-dotnet-backend-schedule-recurring-tasks/manage-sql-azure-database.png)

2.  Nel portale di gestione eseguire una query per visualizzare le modifiche apportate dall'app. La query da utilizzare sarà simile alla seguente, ma anziché `todolist` conterrà il nome del servizio mobile come nome dello schema.

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

<!-- URLs. -->
[Azure Management Portal]: https://manage.windowsazure.com/
[Register your apps for Twitter login with Mobile Services]: /it-it/documentation/articles/mobile-services-how-to-register-twitter-authentication
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App settings]: http://msdn.microsoft.com/it-it/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
[LINQ to Twitter CodePlex project]: http://linqtotwitter.codeplex.com/