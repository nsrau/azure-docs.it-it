<properties  pageTitle="Schedule Backend Tasks with Scheduler - Mobile Services" metaKeywords="" description="Use the Windows Azure Mobile Services Scheduler to schedule jobs for your mobile app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Schedule recurring jobs in Mobile Services" authors="glenga"  solutions="mobile" writer="" manager="" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Pianificazione di processi ricorrenti in Servizi mobili

<div class="dev-center-tutorial-subselector">
    <a href="/it-it/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/" title=".NET backend" class="current">Back-end .NET</a> | <a href="/it-it/documentation/articles/mobile-services-schedule-recurring-tasks/"  title="JavaScript backend" >Back-end JavaScript</a>
</div>

In questo argomento viene illustrato come utilizzare la funzionalità di pianificazione processi nel portale di gestione per definire codice dello script del server da eseguire in base a una pianificazione definita dall'utente. Lo script esegue verifiche periodiche con un servizio remoto, in questo caso Twitter, e archivia i risultati in una nuova tabella. Di seguito sono riportate altre attività periodiche che è possibile pianificare:

-   Archiviazione di record di dati obsoleti o duplicati.
-   Richiesta e archiviazione di dati esterni, ad esempio tweet, voci RSS e informazioni sulla posizione.
-   Elaborazione o ridimensionamento delle immagini archiviate.

In questa esercitazione vengono descritte le procedure per utilizzare la pianificazione processi per creare un processo pianificato che richiede i dati dei tweet da Twitter e li archivia in una nuova tabella Updates:

-   [Registrazione per l'accesso a Twitter e archiviazione delle credenziali][Registrazione per l'accesso a Twitter e archiviazione delle credenziali]
-   [Download e installazione della libreria LINQ to Twitter][Download e installazione della libreria LINQ to Twitter]
-   [Creazione della nuova tabella Updates][Creazione della nuova tabella Updates]
-   [Creazione di un nuovo processo pianificato][Creazione di un nuovo processo pianificato]
-   [Test locale del processo pianificato][Test locale del processo pianificato]
-   [Pubblicazione del servizio e registrazione del processo][Pubblicazione del servizio e registrazione del processo]

> [WACOM.NOTE] In questa esercitazione viene utilizzata la libreria LINQ to Twitter di terze parti per semplificare l'accesso OAuth 2.0 alle API di Twitter v1.1. Per completare questa esercitazione è necessario scaricare e installare il pacchetto NuGet della libreria LINQ to Twitter. Per ulteriori informazioni vedere il [progetto CodePlex di LINQ to Twitter][progetto CodePlex di LINQ to Twitter].

## <a name="get-oauth-credentials"></a>Registrazione per l'accesso alle API di Twitter v1.1 e archiviazione delle credenziali

[WACOM.INCLUDE [mobile-services-register-twitter-access](../includes/mobile-services-register-twitter-access.md)]

1.  In Visual Studio, in Esplora soluzioni aprire il file web.config relativo al progetto di servizio mobile, individuare le impostazioni dell'app **MS\_TwitterConsumerKey** e **MS\_TwitterConsumerSecret** e sostituire i valori di queste chiavi con i valori di chiave e segreto consumer di Twitter configurate nel portale.

2.  Nella stessa sezione, aggiungere le nuove impostazioni dell'app elencate di seguito, sostituendo i segnaposto con i valori di token di accesso e di segreto token di accesso configurati come impostazioni app nel portale:

        <add key="TWITTER_ACCESS_TOKEN" value="**your_access_token**" />
        <add key="TWITTER_ACCESS_TOKEN_SECRET" value="**your_access_token_secret**" />

    Il servizio mobile utilizza le impostazioni memorizzate quando è in esecuzione sul computer locale, consentendo in tal modo di testare il processo pianificato prima di pubblicarlo. Quando è in esecuzione in Azure, il servizio mobile utilizza invece i valori configurati nel portale, ignorando le impostazioni di progetto.

## <a name="install-linq2twitter"></a>Download e installazione della libreria LINQ to Twitter

1.  In **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Manage NuGet Packages**.

2.  Nel riquadro sinistro selezionare la categoria **Online**, cercare `linq2twitter`, fare clic su **Installa** nel pacchetto **linqtotwitter**, quindi leggere e accettare il contratto di licenza.

    ![][0]

    La libreria Linq to Twitter verrà aggiunta al progetto di servizio mobile.

In seguito, verrà aggiunta una nuova tabella in cui archiviare i tweet.

## <a name="create-table"></a>Creazione della nuova tabella Updates

1.  In Esplora soluzioni in Visual Studio fare clic con il pulsante destro del mouse sulla cartella DataObjects, espandere **Aggiungi**, fare clic su **Classe**, digitare `Updates` nella sezione **Nome**, quindi fare clic su **Aggiungi**.

    Viene creato un nuovo file di progetto per la classe Updates.

2.  Fare clic con il pulsante destro del mouse su **Riferimenti**, scegliere **Aggiungi riferimento**, selezionare **Framework** in **Assembly**, selezionare **System.ComponentModel.DataAnnotations**, quindi fare clic su **OK**.

    ![][1]

    Viene aggiunto un nuovo riferimento ad assembly.

3.  In questa nuova classe aggiungere le istruzioni **using** seguenti:

        using Microsoft.WindowsAzure.Mobile.Service;
        using System.ComponentModel.DataAnnotations;

4.  Sostituire la definizione della classe **Updates** con il codice seguente:

        public class Updates 
        {
            [Key]
            public int UpdateId { get; set; }
            public long TweetId { get; set; }
            public string Text { get; set; }
            public string Author { get; set; }
            public DateTime Date { get; set; }
        }

5.  Espandere la cartella Models, aprire il contesto modello di dati (denominato *service\_name*Context.cs) e aggiungere la proprietà seguente che restituisce una classe **DbSet** tipizzata:

        public DbSet<Updates> Updates { get; set; }

    La tabella Updates, creata nel database al primo accesso alla classe DbSet, viene utilizzata per archiviare i dati dei tweet.

    > [WACOM.NOTE] Quando si utilizza l'inizializzatore del database predefinito, Entity Framework elimina e crea nuovamente il database ogni volta che rileva una modifica nel modello di dati nella definizione del modello Code First. Per apportare modifiche al modello di dati e conservare i dati esistenti nel database, è necessario utilizzare Migrazioni Code First. L'inizializzatore predefinito non può essere utilizzato su un database SQL in Azure. Per altre informazioni vedere [Come utilizzare le Migrazioni Code First per aggiornare il modello di dati][Come utilizzare le Migrazioni Code First per aggiornare il modello di dati].

Sarà quindi possibile creare l'attività pianificata che accede a Twitter e archivia i dati dei tweet nella nuova tabella Updates.

## <a name="add-job"></a>Creazione di un nuovo processo pianificato

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

    Nel codice precedente è necessario sostituire le stringhe *todolistService* e *todolistContext* con lo spazio dei nomi e la classe DbContext del progetto scaricato, che sono rispettivamente *mobile\_service\_name*Service e *mobile\_service\_name*Context.

    Inoltre, il metodo di override **ExecuteAsync** chiama l'API query Twitter usando credenziali archiviate per richiedere i tweet recenti contenenti l'hashtag `#mobileservices`. I tweet duplicati e le risposte sono rimossi dai risultati prima di essere archiviati nella tabella.

## <a name="run-job-locally"></a>Test locale del processo pianificato

È possibile testare a livello locale i processi pianificati prima della relativa pubblicazione in Azure e la registrazione sul portale.

1.  In Visual Studio, con il progetto di servizio mobile configurato come progetto di avvio, premere F5.

    Verrà avviato il progetto di servizio mobile e sarà visualizzata una nuova finestra del browser con la pagina di benvenuto.

2.  Fare clic su **Prova**, quindi su **POST jobs/{jobName}**.

    ![][2]

3.  Fare clic su **Prova**, digitare `Sample` come valore del parametro **{jobName}**, quindi fare clic su **Invia**.

    ![][3]

    Viene inviata una nuova richiesta POST all'endpoint dei processi Sample. Nel servizio locale viene avviato il metodo **ExecuteAsync**. In questo metodo è possibile impostare un punto di interruzione per il debug del codice.

4.  In Esplora server, espandere **Connessioni dati**, **MSTableConnectionString** e **Tabelle**; fare clic con il pulsante destro del mouse su **Aggiornamenti** e fare clic su **Mostra dati tabella**.

    I nuovi tweet verranno immessi come righe nella tabella dati.

## <a name="register-job"></a>Pubblicazione del servizio e registrazione del nuovo processo

È necessario registrare il processo nella scheda **Scheduler** in modo che sia possibile eseguirlo in base alla pianificazione definita dall'utente in Servizi mobili.

1.  Ripubblicare il progetto di servizio mobile in Azure.

2.  Nel [portale di gestione di Azure][portale di gestione di Azure], fare clic su Mobile Services e quindi sull'app.

    ![][4]

3.  Fare clic sulla scheda **Scheduler** e quindi su **+Create**.

    ![][5]

    > [WACOM.NOTE]Quando si esegue il servizio mobile nella modalità *Gratuita* è possibile eseguire un solo processo pianificato alla volta. Nelle modalità a pagamento è invece possibile eseguire fino a dieci processi pianificati contemporaneamente.

4.  Nella finestra di dialogo dell'utilità di pianificazione immettere *SampleJob* per **Job Name**, impostare le unità e l'intervallo di pianificazione e quindi fare clic sul segno di spunta.

    ![][6]

    Viene creato un nuovo processo denominato **SampleJob**.

5.  Fare clic sul nuovo processo appena creato e quindi su **Run Once** per testare lo script.

    ![][7]

    Il processo verrà eseguito pur rimanendo disabilitato nell'utilità di pianificazione. Da questa pagina è possibile abilitare il processo e modificarne la pianificazione in qualsiasi momento.

    > [WACOM.NOTE]È comunque possibile usare una richiesta POST per avviare il processo pianificato. Per impostazione predefinita, l'autorizzazione è concessa all'utente, quindi la richiesta dovrà includere la chiave applicazione nell'intestazione.

6.  (Facoltativo) Nel [portale di gestione di Azure][portale di gestione di Azure] fare clic su Manage per il database associato al servizio mobile.

    ![][8]

7.  Nel portale di gestione eseguire una query per visualizzare le modifiche apportate dall'app. La query sarà simile a quella riportata di seguito, ma come nome dello schema verrà usato il nome del servizio mobile anziché `todolist`.

        SELECT * FROM [todolist].[Updates]

In questa esercitazione è stato creato un nuovo processo pianificato nel servizio mobile. Il processo verrà eseguito come pianificato fino a quando non verrà disabilitato o modificato.



  [Registrazione per l'accesso a Twitter e archiviazione delle credenziali]: #get-oauth-credentials
  [Download e installazione della libreria LINQ to Twitter]: #install-linq2twitter
  [Creazione della nuova tabella Updates]: #create-table
  [Creazione di un nuovo processo pianificato]: #add-job
  [Test locale del processo pianificato]: #run-job-locally
  [Pubblicazione del servizio e registrazione del processo]: #register-job
  [progetto CodePlex di LINQ to Twitter]: http://linqtotwitter.codeplex.com/
  [0]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-linq2twitter-nuget-package.png
  [1]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-component-model-reference.png
  [Come utilizzare le Migrazioni Code First per aggiornare il modello di dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-use-code-first-migrations
  [2]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-start-page.png
  [3]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-try-this-out.png
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [4]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-services-selection.png
  [5]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
  [6]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/create-new-job.png
  [7]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/sample-job-run-once.png
  [8]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/manage-sql-azure-database.png
