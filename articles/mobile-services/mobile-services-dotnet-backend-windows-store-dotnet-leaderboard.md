<properties
	pageTitle="Creazione di un'app Leaderboard per Windows Store con un back-end .NET | Servizi mobili di Azure"
	description="Informazioni su come compilare un'app Leaderboard per Windows Store mediante Servizi mobili di Azure con un back-end .NET."
	documentationCenter="windows"
	authors="rmcmurray"
	manager="wpickett"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/09/2016"
	ms.author="glenga"/>

# Creazione di un'app Leaderboard con un back-end .NET di Servizi mobili di Azure

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


Questa esercitazione illustra come compilare un'app di Windows Store mediante Servizi mobili di Azure con un back-end .NET. Servizi mobili di Azure fornisce un back-end scalabile e sicuro con autenticazione, monitoraggio, notifiche push e altre funzionalità predefinite, oltre a una libreria client multipiattaforma per la compilazione di app per dispositivi mobili. Il back-end .NET per Servizi mobili è basato su [API Web ASP.NET](http://asp.net/web-api) e offre agli sviluppatori .NET un modo straordinario per creare API REST.

## Panoramica

API Web API è framework open source che offre agli sviluppatori .NET un modo straordinario per creare API REST. Una soluzione API Web può essere ospitata in Siti Web di Azure, in Servizi mobili di Azure mediante il back-end .NET o persino ospitata in proprio in un processo personalizzato. Servizi mobili è un ambiente host progettato espressamente per le app per dispositivi mobili. Quando si ospita il servizio API Web su Servizi mobili, si ottengono i vantaggi seguenti in aggiunta all'archiviazione dei dati:

- Autenticazione predefinita con i provider social e Azure Active Directory (AAD).
- Notifiche push alle app mediante i servizi di notifica specifici del dispositivo.
- Un set completo di librerie client che semplificano l'accesso al servizio da qualsiasi app.
- Registrazione e diagnostica predefinite.

In questa esercitazione si apprenderà come:

- Creare un'API REST mediante Servizi mobili di Azure.
- Pubblicare il servizio in Azure.
- Creare un'app di Windows Store che usa il servizio.
- Usare Entity Framework (EF) per creare relazioni di chiave esterna e oggetti DTO (Data Transfer Object).
- Usare API Web ASP.NET per definire un'API personalizzata.

Questa esercitazione usa la [versione più recente di Visual Studio 2013](http://go.microsoft.com/fwlink/p/?LinkID=390465).


## Informazioni sull'app di esempio

Una *classifica* mostra un elenco di giocatori, insieme ai punteggi e alla posizione in classifica di ogni giocatore. Può far parte di un gioco più ampio oppure essere un'app separata. Si tratta di un'applicazione reale, ma è abbastanza semplice per un'esercitazione. Ecco una schermata dell'app:

![][1]

Per semplificare l'app, non sono presenti giochi effettivi. È però possibile aggiungere giocatori e inviare un punteggio per ognuno di essi. Quando si invia un punteggio, il servizio mobile calcola le nuove posizioni in classifica. Sul lato back-end, il servizio mobile crea un database con due tabelle:

- Player. Contiene l'ID e il nome del giocatore.
- PlayerRank. Contiene il punteggio e la posizione in classifica del giocatore.

PlayerRank include una chiave esterna per Player. Ogni giocatore ha zero o una PlayerRank.

In un'app reale di questo tipo, a PlayerRank può essere associato anche un ID gioco per fare in modo che un giocatore possa inviare punteggi per più giochi.

![][2]

L'app client può eseguire un set completo di operazioni CRUD su Players. Può leggere o eliminare le entità PlayerRank esistenti ma non può crearle o aggiornarle direttamente, dato che il valore della posizione in classifica viene calcolato dal servizio. Il client invia un punteggio e il servizio aggiorna le posizioni in classifica per tutti i giocatori.

Scaricare il progetto completo [qui](http://code.msdn.microsoft.com/Leaderboard-App-with-Azure-9acf63af).


## Creare il progetto

Avviare Visual Studio e creare un nuovo progetto di applicazione Web ASP.NET. Denominare il progetto Leaderboard.

![][3]

In Visual Studio 2013 il progetto di applicazione Web ASP.NET include un modello per il servizio mobile di Azure. Selezionare questo modello e fare clic su **OK**.

![][4]

Il modello di progetto include un controller e un oggetto dati di esempio.

![][5]

Non sono necessari per questa esercitazione, quindi possono essere eliminati dal progetto. Rimuovere anche i riferimenti a TodoItem in WebApiConfig.cs e LeaderboardContext.cs.

## Aggiungere modelli di dati

Si userà [Code First di Entity Framework](http://msdn.microsoft.com/data/ee712907#codefirst) per definire le tabelle di dati. Nella cartella DataObjects aggiungere una classe denominata `Player`.

	using Microsoft.WindowsAzure.Mobile.Service;

	namespace Leaderboard.DataObjects
	{
	    public class Player : EntityData
	    {
	        public string Name { get; set; }
	    }
	}

Aggiungere un'altra classe denominata `PlayerRank`.

	using Microsoft.WindowsAzure.Mobile.Service;
	using System.ComponentModel.DataAnnotations.Schema;

	namespace Leaderboard.DataObjects
	{
	    public class PlayerRank : EntityData
	    {
	        public int Score { get; set; }
	        public int Rank { get; set; }

	        [ForeignKey("Id")]
	        public virtual Player Player { get; set; }
	    }
	}

Si noti che entrambe le classi ereditano dalla classe **EntityData**. L'ereditarietà da **EntityData** semplifica l'uso dei dati da parte dell'app tramite la libreria client multipiattaforma per Servizi mobili di Azure. **EntityData** consente anche a un'app di [gestire i conflitti di scrittura nel database](mobile-services-windows-store-dotnet-handle-database-conflicts.md).

La classe `PlayerRank` include una [proprietà di navigazione](http://msdn.microsoft.com/data/jj713564.aspx) che punta all'entità `Player` correlata. L'attributo **[ForeignKey]** indica a EF che la proprietà `Player` rappresenta una chiave esterna.

## Aggiungere controller API Web

Successivamente, si aggiungono controller API Web per `Player` e `PlayerRank`. Anziché controller API Web semplici, si aggiungerà un tipo particolare di controller denominato *controller tabella*, progettato espressamente per Servizi mobili di Azure.

Fare clic con il pulsante destro del mouse sulla cartella Controller > **Aggiungi** > **Nuovo elemento con scaffolding**

![][6]

Nella finestra di dialogo **Aggiungi scaffolding** espandere **Comuni** a sinistra e selezionare **Servizi mobili di Azure**. Selezionare quindi **Controller tabella di Servizi mobili di Azure**. Fare clic su **Aggiungi**.

![][7]

Nella finestra di dialogo **Aggiungi controller**:

1.	In **Classe modello** selezionare Player.
2.	In **Classe contesto dati** selezionare MobileServiceContext.
3.	Assegnare al controller il nome "PlayerController".
4.	Fare clic su **Aggiungi**.


Questo passaggio aggiunge un file denominato PlayerController.cs al progetto.

![][8]

Il controller deriva da **TableController<T>**. Questa classe eredita **ApiController** ma è specializzata per Servizi mobili di Azure.

- Routing: la route predefinita per un **TableController** è `/tables/{table_name}/{id}`, dove *table\_name* corrisponde al nome dell'entità. Quindi, la route per il controller Player è */tables/player/{id}*. Questa convenzione di routing assicura la coerenza di **TableController** con l'[API REST](http://msdn.microsoft.com/library/azure/jj710104.aspx) di Servizi mobili.
- Accesso ai dati: per le operazioni di database, la classe **TableController** usa l'interfaccia **IDomainManager**, che definisce un'astrazione per l'accesso ai dati. Lo scaffolding usa **EntityDomainManager**, che è un'implementazione concreta di **IDomainManager** che esegue il wrapping di un contesto EF.

Aggiungere ora un secondo controller per le entità PlayerRank. Seguire la stessa procedura, ma scegliendo PlayerRank per la classe modello. Usare la stessa classe contesto dati. Non crearne una nuova. Assegnare al controller il nome "PlayerRankController".

## Usare un oggetto DTO per restituire le entità correlate

Tenere presente che `PlayerRank` ha un'entità `Player` correlata:

    public class PlayerRank : EntityData
    {
        public int Score { get; set; }
        public int Rank { get; set; }

        [ForeignKey("Id")]
        public virtual Player Player { get; set; }
    }

La libreria client di Servizi mobili non supporta le proprietà di navigazione, che non verranno serializzate. Di seguito è indicata ad esempio la risposta HTTP non elaborata per GET `/tables/PlayerRank`:

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 97
	Content-Type: application/json; charset=utf-8
	Expires: 0
	Server: Microsoft-IIS/8.0
	Date: Mon, 21 Apr 2014 17:58:43 GMT

	[{"id":"1","rank":1,"score":150},{"id":"2","rank":3,"score":100},{"id":"3","rank":1,"score":150}]

Si noti che `Player` non è incluso nell'oggetto grafico. Per includere il giocatore, è possibile rendere flat l'oggetto grafico definendo un *oggetto DTO (Data Transfer Object)*.

Un DTO è un oggetto che definisce la modalità di invio dei dati sulla rete. Gli oggetti DTO sono utili quando è preferibile che il formato di trasmissione sia diverso rispetto al modello di database. Per creare un oggetto DTO per `PlayerRank`, aggiungere una nuova classe denominata `PlayerRankDto` nella cartella DataObjects.

	namespace Leaderboard.DataObjects
	{
	    public class PlayerRankDto
	    {
	        public string Id { get; set; }
	        public string PlayerName { get; set; }
	        public int Score { get; set; }
	        public int Rank { get; set; }
	    }
	}

Nella classe `PlayerRankController` si userà il metodo **Select** di LINQ per convertire le istanze di `PlayerRank` in istanze di `PlayerRankDto`. Aggiornare i metodi controller `GetAllPlayerRank` e `GetPlayerRank` nel modo seguente:

	// GET tables/PlayerRank
	public IQueryable<PlayerRankDto> GetAllPlayerRank()
	{
	    return Query().Select(x => new PlayerRankDto()
	    {
	        Id = x.Id,
	        PlayerName = x.Player.Name,
	        Score = x.Score,
	        Rank = x.Rank
	    });
	}

	// GET tables/PlayerRank/48D68C86-6EA6-4C25-AA33-223FC9A27959
	public SingleResult<PlayerRankDto> GetPlayerRank(string id)
	{
	    var result = Lookup(id).Queryable.Select(x => new PlayerRankDto()
	    {
	        Id = x.Id,
	        PlayerName = x.Player.Name,
	        Score = x.Score,
	        Rank = x.Rank
	    });

	    return SingleResult<PlayerRankDto>.Create(result);
	}

Con queste modifiche, i due metodi GET restituiscono oggetti `PlayerRankDto` al client. La proprietà `PlayerRankDto.PlayerName` è impostata sul nome del giocatore. Ecco un esempio di risposta dopo l'applicazione di questa modifica:

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 160
	Content-Type: application/json; charset=utf-8
	Expires: 0
	Server: Microsoft-IIS/8.0
	Date: Mon, 21 Apr 2014 19:57:08 GMT

	[{"id":"1","playerName":"Alice","score":150,"rank":1},{"id":"2","playerName":"Bob","score":100,"rank":3},{"id":"3","playerName":"Charles","score":150,"rank":1}]

Si noti che il payload JSON ora include i nomi dei giocatori.

Anziché usare istruzioni Select di LINQ, è possibile usare AutoMapper. Questa opzione richiede codice di configurazione supplementare, ma consente il mapping automatico dalle entità di dominio agli oggetti DTO. Per altre informazioni, vedere il blog relativo al [mapping tra tipi di database e tipi client nel back-end .NET mediante AutoMapper](http://blogs.msdn.com/b/azuremobile/archive/2014/05/19/mapping-between-database-types-and-client-type-in-the-net-backend-using-automapper.aspx).

## Definire un'API personalizzata per l'invio dei punteggi

L'entità `PlayerRank` include una proprietà `Rank`. Questo valore viene calcolato dal server e non deve essere impostato dai client. I client useranno invece un'API personalizzata per inviare il punteggio di un giocatore. Quando il server riceve un nuovo punteggio, aggiorna la posizione in classifica di tutti i giocatori.

Aggiungere innanzitutto una classe denominata `PlayerScore` alla cartella DataObjects.

	namespace Leaderboard.DataObjects
	{
	    public class PlayerScore
	    {
	        public string PlayerId { get; set; }
	        public int Score { get; set; }
	    }
	}

Nella classe `PlayerRankController` spostare la variabile `MobileServiceContext` dal costruttore a una variabile di classe:

    public class PlayerRankController : TableController<PlayerRank>
    {
        // Add this:
        MobileServiceContext context = new MobileServiceContext();

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);

            // Delete this:
            // MobileServiceContext context = new MobileServiceContext();
            DomainManager = new EntityDomainManager<PlayerRank>(context, Request, Services);
        }


Eliminare i metodi seguenti da `PlayerRankController`:

- `PatchPlayerRank`
- `PostPlayerRank`
- `DeletePlayerRank`

Aggiungere quindi il codice seguente a `PlayerRankController`:

    [Route("api/score")]
    public async Task<IHttpActionResult> PostPlayerScore(PlayerScore score)
    {
        // Does this player exist?
        var count = context.Players.Where(x => x.Id == score.PlayerId).Count();
        if (count < 1)
        {
            return BadRequest();
        }

        // Try to find the PlayerRank entity for this player. If not found, create a new one.
        PlayerRank rank = await context.PlayerRanks.FindAsync(score.PlayerId);
        if (rank == null)
        {
            rank = new PlayerRank { Id = score.PlayerId };
            rank.Score = score.Score;
            context.PlayerRanks.Add(rank);
        }
        else
        {
            rank.Score = score.Score;
        }

        await context.SaveChangesAsync();

        // Update rankings
        // See http://stackoverflow.com/a/575799
        const string updateCommand =
            "UPDATE r SET Rank = ((SELECT COUNT(*)+1 from {0}.PlayerRanks " +
            "where Score > (select score from {0}.PlayerRanks where Id = r.Id)))" +
            "FROM {0}.PlayerRanks as r";

        string command = String.Format(updateCommand, ServiceSettingsDictionary.GetSchemaName());
        await context.Database.ExecuteSqlCommandAsync(command);

        return Ok();
    }

Il metodo `PostPlayerScore` accetta un'istanza di `PlayerScore` come input (il client invia `PlayerScore` in una richiesta HTTP POST). Il metodo esegue le operazioni seguenti:

1.	Aggiunge una nuova istanza di `PlayerRank` per il giocatore, se non ne esiste già una nel database.
2.	Aggiorna il punteggio del giocatore.
3.	Esegue una query SQL che aggiorna in batch la posizione in classifica di tutti i giocatori.

L'attributo **[Route]** definisce una route personalizzata per questo metodo:

	[Route("api/score")]

È anche possibile inserire il metodo in un controller separato. Nessuna delle due opzioni presenta vantaggi particolari. La scelta dipende solo dal modo in cui si vuole organizzare il codice. Per altre informazioni sull'attributo **[Route]**, vedere l'articolo relativo al [routing mediante attributi in API Web](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2).

## Creare l'app di Windows Store

In questa sezione verrà descritta l'app di Windows Store che usa il servizio mobile. L'attenzione non sarà focalizzata in particolare sul codice XAML o sull'interfaccia utente, quanto piuttosto sulla logica dell'applicazione. Il progetto completo è disponibile per il download [qui](http://code.msdn.microsoft.com/Leaderboard-App-with-Azure-9acf63af).

Aggiungere un nuovo progetto di app di Windows Store alla soluzione. È stato usato il modello Applicazione vuota (Windows).

![][10]

Usare Gestione pacchetti NuGet per aggiungere la libreria client di Servizi mobili. In Visual Studio scegliere **Gestione pacchetti NuGet** dal menu **Strumenti**. Selezionare quindi **Console di Gestione pacchetti**. Nella finestra Console di Gestione pacchetti digitare il comando seguente.

	Install-Package WindowsAzure.MobileServices -Project LeaderboardApp

L'opzione -Project specifica il progetto in cui installare il pacchetto.

## Aggiungere classi di modelli

Creare una cartella denominata Models e aggiungere le classi seguenti:

	namespace LeaderboardApp.Models
	{
	    public class Player
	    {
	        public string Id { get; set; }
	        public string Name { get; set; }
	    }

	    public class PlayerRank
	    {
	        public string Id { get; set; }
	        public string PlayerName { get; set; }
	        public int Score { get; set; }
	        public int Rank { get; set; }
	    }

	    public class PlayerScore
	    {
	        public string PlayerId { get; set; }
	        public int Score { get; set; }
	    }
	}

Queste classi corrispondono direttamente alle entità di dati nel servizio mobile.

## Creare un modello di visualizzazione

Il modello MVVM (Model-View-ViewModel) è una variante di MVC (Model-View-Controller). Il modello MVVM consente di separare la logica dell'applicazione dalla presentazione.

- Il modello rappresenta i dati di dominio (giocatore, posizione in classifica del giocatore e punteggio del giocatore).
- Il modello di visualizzazione è una rappresentazione astratta della visualizzazione.
- Questa visualizzazione mostra il modello di visualizzazione e invia l'input dell'utente al modello di visualizzazione. Per un'app di Windows Store, la visualizzazione è definita in XAML.

![][11]

Aggiungere una classe denominata `LeaderboardViewModel`.

	using LeaderboardApp.Models;
	using Microsoft.WindowsAzure.MobileServices;
	using System.ComponentModel;
	using System.Net.Http;
	using System.Threading.Tasks;

	namespace LeaderboardApp.ViewModel
	{
	    class LeaderboardViewModel : INotifyPropertyChanged
	    {
	        MobileServiceClient _client;

	        public LeaderboardViewModel(MobileServiceClient client)
	        {
	            _client = client;
	        }
	    }
	}

Implementare **INotifyPropertyChanged** nel modello di visualizzazione affinché quest'ultimo possa partecipare al data binding.

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        MobileServiceClient _client;

        public LeaderboardViewModel(MobileServiceClient client)
        {
            _client = client;
        }

        // New code:
        // INotifyPropertyChanged implementation
        public event PropertyChangedEventHandler PropertyChanged;

        public void NotifyPropertyChanged(string propertyName)
        {
            if (PropertyChanged != null)
            {
                PropertyChanged(this,
                    new PropertyChangedEventArgs(propertyName));
            }
        }
    }

Aggiungere quindi le proprietà osservabili. XAML eseguirà il data binding a queste proprietà.

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        // ...

        // New code:
        // View model properties
        private MobileServiceCollection<Player, Player> _Players;
        public MobileServiceCollection<Player, Player> Players
        {
            get { return _Players; }
            set
            {
                _Players = value;
                NotifyPropertyChanged("Players");
            }
        }

        private MobileServiceCollection<PlayerRank, PlayerRank> _Ranks;
        public MobileServiceCollection<PlayerRank, PlayerRank> Ranks
        {
            get { return _Ranks; }
            set
            {
                _Ranks = value;
                NotifyPropertyChanged("Ranks");
            }
        }

        private bool _IsPending;
        public bool IsPending
        {
            get { return _IsPending; }
            set
            {
                _IsPending = value;
                NotifyPropertyChanged("IsPending");
            }
        }

        private string _ErrorMessage = null;
        public string ErrorMessage
        {
            get { return _ErrorMessage; }
            set
            {
                _ErrorMessage = value;
                NotifyPropertyChanged("ErrorMessage");
            }
        }
    }

La proprietà `IsPending` è true mentre un'operazione asincrona è in sospeso sul servizio. La proprietà `ErrorMessage` include qualsiasi messaggio di errore dal servizio.

Infine aggiungere i metodi che eseguono chiamate al livello del servizio.

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        // ...

        // New code:
        // Service operations
        public async Task GetAllPlayersAsync()
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<Player> table = _client.GetTable<Player>();
                Players = await table.OrderBy(x => x.Name).ToCollectionAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task AddPlayerAsync(Player player)
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<Player> table = _client.GetTable<Player>();
                await table.InsertAsync(player);
                Players.Add(player);
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task SubmitScoreAsync(Player player, int score)
        {
            IsPending = true;
            ErrorMessage = null;

            var playerScore = new PlayerScore()
            {
                PlayerId = player.Id,
                Score = score
            };

            try
            {
                await _client.InvokeApiAsync<PlayerScore, object>("score", playerScore);
                await GetAllRanksAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task GetAllRanksAsync()
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<PlayerRank> table = _client.GetTable<PlayerRank>();
                Ranks = await table.OrderBy(x => x.Rank).ToCollectionAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
         }
    }

## Aggiungere un'istanza di MobileServiceClient

Aprire il file *App.xaml.cs* e aggiungere un'istanza di **MobileServiceClient** alla classe `App`.

	// New code:
	using Microsoft.WindowsAzure.MobileServices;

	namespace LeaderboardApp
	{
	    sealed partial class App : Application
	    {
	        // New code.
	        // TODO: Replace 'port' with the actual port number.
	        const string serviceUrl = "http://localhost:port/";
	        public static MobileServiceClient MobileService = new MobileServiceClient(serviceUrl);


	        // ...
	    }
	}

Quando si esegue il debug in locale, il servizio mobile viene eseguito in IIS Express. Visual Studio assegna un numero di porta casuale, quindi l'URL locale è http://localhost:*port*, dove *porta* indica il numero di porta. Per ottenere il numero di porta, avviare il servizio in Visual Studio premendo F5 per eseguire il debug. Visual Studio avvierà un browser e passerà all'URL del servizio. L'URL locale è reperibile anche nelle proprietà del progetto, in **Web**.

## Creare la pagina principale

Nella pagina principale aggiungere un'istanza del modello di visualizzazione. Impostare quindi il modello di visualizzazione come **DataContext** per la pagina.

    public sealed partial class MainPage : Page
    {
        // New code:
        LeaderboardViewModel viewModel = new LeaderboardViewModel(App.MobileService);

        public MainPage()
        {
            this.InitializeComponent();
            // New code:
            this.DataContext = viewModel;
        }

       // ...


Come indicato in precedenza, non verrà mostrato tutto il codice XAML per l'app. Un vantaggio del modello MVVM consiste nella separazione della presentazione dalla logica dell'app. Questo permette di modificare facilmente l'interfaccia utente se l'app di esempio non è soddisfacente.

L'elenco dei giocatori viene visualizzato in un oggetto **ListBox**:

	<ListBox Width="200" Height="400" x:Name="PlayerListBox"
	    ItemsSource="{Binding Players}" DisplayMemberPath="Name"/>

Le posizioni in classifica sono visualizzate in un oggetto **ListView**:

	<ListView x:Name="RankingsListView" ItemsSource="{Binding Ranks}" SelectionMode="None">
	    <!-- Header and styles not shown -->
	    <ListView.ItemTemplate>
	        <DataTemplate>
	            <Grid>
	                <Grid.ColumnDefinitions>
	                    <ColumnDefinition Width="*"/>
	                    <ColumnDefinition Width="2*"/>
	                    <ColumnDefinition Width="*"/>
	                </Grid.ColumnDefinitions>
	                <TextBlock Text="{Binding Path=Rank}"/>
	                <TextBlock Text="{Binding Path=PlayerName}" Grid.Column="1"/>
	                <TextBlock Text="{Binding Path=Score}" Grid.Column="2"/>
	            </Grid>
	        </DataTemplate>
	    </ListView.ItemTemplate>
	</ListView>

Tutte le operazioni di data binding vengono effettuate tramite il modello di visualizzazione.

## Pubblicare il servizio mobile

In questo passaggio si pubblicherà il servizio mobile in Microsoft Azure e si modificherà l'app in modo che usi il servizio attivo.

In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto Leaderboard e scegliere **Pubblica**.

![][12]

Nella finestra di dialogo **Pubblica** fare clic su **Servizi mobili di Azure**.

![][13]

Se non è ancora stato eseguito l'accesso all'account Azure, fare clic su **Accedi**.

![][14]


Selezionare un servizio mobile esistente oppure fare clic su **Nuovo** per crearne uno nuovo. Fare quindi clic su **OK** per pubblicare.

![][15]

Durante la procedura di pubblicazione viene creato automaticamente il database. Non è necessario configurare una stringa di connessione.

A questo punto è possibile connettere l'app per la classifica al servizio attivo. Sono necessari due elementi:

- L'URL del servizio
- La chiave applicazione

È possibile ottenere entrambi dal portale di Azure classico. Nel portale fare clic su **Servizi mobili** e quindi sul sevizio mobile. L'URL del servizio è indicato nella scheda del dashboard. Per ottenere la chiave applicazione fare clic su **Gestisci chiavi**.

![][16]

Nella finestra di dialogo **Gestisci chiavi di accesso** copiare il valore relativo alla chiave applicazione.

![][17]


Passare l'URL del servizio e la chiave applicazione al costruttore **MobileServiceClient**.

    sealed partial class App : Application
    {
        // TODO: Replace these strings with the real URL and key.
        const string serviceUrl = "https://yourapp.azure-mobile.net/";
        const string appKey = "YOUR ACCESSS KEY";

        public static MobileServiceClient MobileService = new MobileServiceClient(serviceUrl, appKey);

       // ...

A questo punto, quando si esegue l'app, questa comunica con il servizio reale.

## Passaggi successivi

* [Altre informazioni su Servizi mobili di Azure]
* [Altre informazioni su API Web]
* [Gestire i conflitti di scrittura nel database]
* [Aggiungere notifiche push], ad esempio quando viene aggiunto un nuovo giocatore o viene aggiornato un punteggio.
* [Introduzione all'autenticazione]

<!-- Anchors. -->
[Overview]: #overview
[About the sample app]: #about-the-sample-app
[Create the project]: #create-the-project
[Add data models]: #add-data-models
[Add Web API controllers]: #add-web-api-controllers
[Use a DTO to return related entities]: #use-a-dto-to-return-related-entities
[Define a custom API to submit scores]: #define-a-custom-api-to-submit-scores
[Create the Windows Store app]: #create-the-windows-store-app
[Add model classes]: #add-model-classes
[Create a view model]: #create-a-view-model
[Add a MobileServiceClient instance]: #add-a-mobileserviceclient-instance
[Create the main page]: #create-the-main-page
[Publish your mobile service]: #publish-your-mobile-service
[Next Steps]: #next-steps

<!-- Images. -->

[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/01leaderboard.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/02leaderboard.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/03leaderboard.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/04leaderboard.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/05leaderboard.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/06leaderboard.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/07leaderboard.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/08leaderboard.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/09leaderboard.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/10leaderboard.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/11leaderboard.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/12leaderboard.png
[13]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/13leaderboard.png
[14]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/14leaderboard.png
[15]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/15leaderboard.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/16leaderboard.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/17leaderboard.png

<!-- URLs. -->

[Altre informazioni su Servizi mobili di Azure]: /develop/mobile/resources/
[Altre informazioni su API Web]: http://asp.net/web-api
[Gestire i conflitti di scrittura nel database]: mobile-services-windows-store-dotnet-handle-database-conflicts.md
[Aggiungere notifiche push]: ../notification-hubs-windows-store-dotnet-get-started.md
[Introduzione all'autenticazione]: /develop/mobile/tutorials/get-started-with-users-dotnet

<!---HONumber=AcomDC_0114_2016-->