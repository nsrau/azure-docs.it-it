---
title: Esercitazione per creare un'app Web con Cache Redis di Azure che usa il modello cache-aside | Microsoft Docs
description: Informazioni su come creare un'app Web con Cache Redis di Azure che usa il modello cache-aside
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/30/2018
ms.author: wesmc
ms.openlocfilehash: da99d446da1451c2b05f6ecab8ba19eaa1e2bfa3
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54104948"
---
# <a name="tutorial-create-a-cache-aside-leaderboard-on-aspnet"></a>Esercitazione: Creare un tabellone punteggi cache-aside in ASP.NET

In questa esercitazione verrà aggiornata l'app Web ASP.NET *ContosoTeamStats*, creata nella [guida introduttiva di ASP.NET per Cache Redis di Azure](cache-web-app-howto.md), in modo da includere un tabellone punteggi che usa il [modello cache-aside](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) con Cache Redis di Azure. L'applicazione di esempio mostra un elenco di statistiche del team prese da un database e illustra i diversi modi in cui è possibile usare Cache Redis di Azure per archiviare e recuperare i dati dalla cache in modo da aumentare le prestazioni. Al termine dell'esercitazione, si ottiene un'app Web in esecuzione che legge e scrive in un database, ottimizzata per Cache Redis di Azure e ospitata in Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aumentare la velocità effettiva dei dati e ridurre il carico del database archiviando e recuperando i dati con Cache Redis di Azure.
> * Usare un set ordinato Redis per recuperare i primi cinque team.
> * Effettuare il provisioning delle risorse di Azure per l'applicazione usando un modello di Resource Manager.
> * Pubblicare l'applicazione in Azure usando Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono previsti i prerequisiti seguenti:

* Questa esercitazione prosegue da dove era terminata la [guida introduttiva di ASP.NET per Cache Redis di Azure](cache-web-app-howto.md). Se non si è ancora provveduto, seguire prima tale guida introduttiva.
* Installare [Visual Studio 2017](https://www.visualstudio.com/downloads/) con i carichi di lavoro seguenti:
    * Sviluppo Web e ASP.NET
    * Sviluppo di Azure
    * Sviluppo desktop .NET con SQL Server Express Local DB o [SQL Server 2017 Express edition](https://www.microsoft.com/sql-server/sql-server-editions-express).

## <a name="add-a-leaderboard-to-the-project"></a>Aggiungere un classifica al progetto

In questa sezione dell'esercitazione viene configurato il progetto *ContosoTeamStats* con un classifica che segnala le statistiche di vittorie, sconfitte e pareggi per un elenco di team fittizi.

### <a name="add-the-entity-framework-to-the-project"></a>Aggiungere Entity Framework al progetto

1. In Visual Studio aprire la soluzione *ContosoTeamStats* creata nella [guida introduttiva di ASP.NET per Cache Redis di Azure](cache-web-app-howto.md).
2. Fare clic su **Strumenti > Gestione pacchetti NuGet > Console di Gestione pacchetti**.
3. Nella finestra **Console di Gestione pacchetti** eseguire il comando seguente per installare EntityFramework:

    ```powershell
    Install-Package EntityFramework
    ```

Per altre informazioni sul pacchetto, vedere la pagina NuGet relativa a [Entity Framework](https://www.nuget.org/packages/EntityFramework/).

### <a name="add-the-team-model"></a>Aggiungere il modello di team

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Modelli**, quindi scegliere **Aggiungi**, **Classe**.

1. Immettere `Team` come nome della classe e fare clic su **Aggiungi**.

    ![Aggiunta di una classe modello](./media/cache-web-app-cache-aside-leaderboard/cache-model-add-class-dialog.png)

1. Sostituire le istruzioni `using` all'inizio del file *Team.cs* con le istruzioni `using` seguenti:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```

1. Sostituire la definizione della classe `Team` con il frammento di codice seguente che contiene una classe `Team` aggiornata, oltre ad altre classi helper di Entity Framework. Questa esercitazione usa il primo approccio al codice con Entity Framework. Questo approccio consente a Entity Framework di creare il database dal codice. Per altre informazioni sull'approccio Code First per Entity Framework usato in questa esercitazione, vedere [Code First per un nuovo database](https://msdn.microsoft.com/data/jj193542).

    ```csharp
    public class Team
    {
        public int ID { get; set; }
        public string Name { get; set; }
        public int Wins { get; set; }
        public int Losses { get; set; }
        public int Ties { get; set; }

        static public void PlayGames(IEnumerable<Team> teams)
        {
            // Simple random generation of statistics.
            Random r = new Random();

            foreach (var t in teams)
            {
                t.Wins = r.Next(33);
                t.Losses = r.Next(33);
                t.Ties = r.Next(0, 5);
            }
        }
    }

    public class TeamContext : DbContext
    {
        public TeamContext()
            : base("TeamContext")
        {
        }

        public DbSet<Team> Teams { get; set; }
    }

    public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
    {
        protected override void Seed(TeamContext context)
        {
            var teams = new List<Team>
            {
                new Team{Name="Adventure Works Cycles"},
                new Team{Name="Alpine Ski House"},
                new Team{Name="Blue Yonder Airlines"},
                new Team{Name="Coho Vineyard"},
                new Team{Name="Contoso, Ltd."},
                new Team{Name="Fabrikam, Inc."},
                new Team{Name="Lucerne Publishing"},
                new Team{Name="Northwind Traders"},
                new Team{Name="Consolidated Messenger"},
                new Team{Name="Fourth Coffee"},
                new Team{Name="Graphic Design Institute"},
                new Team{Name="Nod Publishers"}
            };

            Team.PlayGames(teams);

            teams.ForEach(t => context.Teams.Add(t));
            context.SaveChanges();
        }
    }

    public class TeamConfiguration : DbConfiguration
    {
        public TeamConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
        }
    }
    ```

1. In **Esplora soluzioni** fare doppio clic su **Web.config** per aprirlo.

    ![Web.config](./media/cache-web-app-cache-aside-leaderboard/cache-web-config.png)

1. Aggiungere la sezione `connectionStrings` seguente all'interno della sezione `configuration`. Il nome della stringa di connessione deve corrispondere al nome della classe di contesto del database Entity Framework, ovvero `TeamContext`.

    Questa stringa di connessione presuppone che siano stati soddisfatti i [prerequisiti](#prerequisites) e che si sia installato SQL Server Express Local DB, che fa parte del flusso di lavoro per lo *sviluppo desktop di .NET* installato con Visual Studio 2017.

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    L'esempio seguente mostra la nuova sezione `connectionStrings` che segue `configSections` all'interno della sezione `configuration`:

    ```xml
    <configuration>
        <configSections>
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
        </configSections>
        <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
        </connectionStrings>
        ...
    ```

### <a name="add-the-teamscontroller-and-views"></a>Aggiungere TeamsController e visualizzazioni

1. Compilare il progetto in Visual Studio. 

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella **Controller**, quindi scegliere **Aggiungi**, **Controller**.

1. Scegliere **Controller MVC 5 con visualizzazioni, che usa Entity Framework** e quindi fare clic su **Aggiungi**. Se viene visualizzato un errore dopo la selezione di **Aggiungi**, assicurarsi prima di tutto di avere compilato il progetto.

    ![Aggiunta di una classe controller](./media/cache-web-app-cache-aside-leaderboard/cache-add-controller-class.png)

1. Selezionare **Team (ContosoTeamStats.Models)** dall'elenco a discesa **Classe modello**. Selezionare **TeamContext (ContosoTeamStats.Models)** dall'elenco a discesa **Classe del contesto dei dati**. Immettere `TeamsController` nella casella di testo **Nome controller** , se non è stata popolata automaticamente. Fare clic su **Aggiungi** per creare la classe controller e aggiungere le visualizzazioni predefinite.

    ![Configura controller](./media/cache-web-app-cache-aside-leaderboard/cache-configure-controller.png)

1. In **Esplora soluzioni** espandere **Global.asax** e fare doppio clic su **Global.asax.cs** per aprirlo.

    ![Global.asax.cs](./media/cache-web-app-cache-aside-leaderboard/cache-global-asax.png)

1. Aggiungere le due istruzioni `using` seguenti all'inizio del file, dopo le altre due istruzioni `using`:

    ```csharp
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```

1. Aggiungere la seguente riga di codice alla fine del metodo `Application_Start` :

    ```csharp
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```

1. In **Esplora soluzioni** espandere `App_Start` e fare doppio clic su `RouteConfig.cs`.

    ![RouteConfig.cs](./media/cache-web-app-cache-aside-leaderboard/cache-RouteConfig-cs.png)

1. Nel metodo `RegisterRoutes` sostituire `controller = "Home"` nella route `Default` con `controller = "Teams"`, come mostrato nell'esempio seguente:

    ```csharp
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```

### <a name="configure-the-layout-view"></a>Configurare la visualizzazione Layout

1. In **Esplora soluzioni** espandere la cartella **Visualizzazioni**, quindi la cartella **Condiviso** e infine fare doppio clic su **_Layout.cshtml**. 

    ![file _Layout.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml.png)

1. Cambiare il contenuto dell'elemento `title` e sostituire `My ASP.NET Application` con `Contoso Team Stats`, come illustrato nell'esempio seguente:

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```

1. Nella sezione `body` aggiungere la nuova istruzione `Html.ActionLink` seguente per *Contoso Team Stats* appena sotto al collegamento per il *test di Cache Redis di Azure*.

    ```csharp
    @Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
    ```

    ![Modifiche al codice](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml-code.png)

1. Premere **CTRL+F5** per compilare ed eseguire l'applicazione. Questa versione dell'applicazione legge i risultati direttamente dal database. Si notino le azioni **Crea nuovo**, **Modifica**, **Dettagli** ed **Elimina** aggiunte automaticamente all'applicazione dallo scaffolding **Controller MVC 5 con visualizzazioni, che usa Entity Framework**. Nella sezione successiva dell'esercitazione verrà aggiunta l'istanza di Cache Redis di Azure per ottimizzare l'accesso ai dati e fornire funzionalità aggiuntive all'applicazione.

    ![Applicazione iniziale](./media/cache-web-app-cache-aside-leaderboard/cache-starter-application.png)

## <a name="configure-the-app-for-azure-cache-for-redis"></a>Configurare l'app per Cache Redis di Azure

In questa sezione dell'esercitazione viene configurata l'applicazione di esempio per archiviare e recuperare le statistiche del team Contoso da un'istanza di Cache Redis di Azure usando il client [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) della cache.

### <a name="add-a-cache-connection-to-the-teams-controller"></a>Aggiungere una connessione della cache a TeamsController

L'installazione del pacchetto della libreria client *StackExchange.Redis* è già stata effettuata nella guida introduttiva. Inoltre è già stata configurata l'impostazione dell'app *CacheConnection* da usare in locale e con il servizio app pubblicato. Usare la stessa libreria client e le informazioni di *CacheConnection* in *TeamsController*.

1. In **Esplora soluzioni** espandere la cartella **Controller** e fare doppio clic su **TeamsController.cs** per aprirlo.

    ![Controller Teams](./media/cache-web-app-cache-aside-leaderboard/cache-teamscontroller.png)

1. Aggiungere le due istruzioni `using` seguenti a **TeamsController.cs**:

    ```csharp
    using System.Configuration;
    using StackExchange.Redis;
    ```

1. Aggiungere le due proprietà seguenti alla classe `TeamsController`:

    ```csharp
    // Redis Connection string info
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

### <a name="update-the-teamscontroller-to-read-from-the-cache-or-the-database"></a>Aggiornare TeamsController in modo da leggere dalla cache o dal database

In questo esempio è possibile recuperare le statistiche del team dal database o dalla cache. Le statistiche del team vengono archiviate nella cache come elemento `List<Team>`serializzato e anche come set ordinato usando tipi di dati Redis. Quando si recuperano elementi da un set ordinato, è possibile recuperare alcuni o tutti gli elementi o eseguire query per determinati elementi. In questo esempio verrà eseguita una query sul set ordinato per i primi 5 team classificati in base al numero di vittorie.

Per usare Cache Redis di Azure non è necessario archiviare le statistiche del team in più formati nella cache. Questa esercitazione usa più formati per illustrare alcuni dei modi diversi e dei tipi di dati diversi che è possibile usare per memorizzare i dati nella cache.

1. Aggiungere le istruzioni `using` seguenti nella parte iniziale del file `TeamsController.cs`, insieme alle altre istruzioni `using`:

    ```csharp
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

1. Sostituire l'implementazione del metodo `public ActionResult Index()` corrente con l'implementazione seguente:

    ```csharp
    // GET: Teams
    public ActionResult Index(string actionType, string resultType)
    {
        List<Team> teams = null;

        switch(actionType)
        {
            case "playGames": // Play a new season of games.
                PlayGames();
                break;

            case "clearCache": // Clear the results from the cache.
                ClearCachedTeams();
                break;

            case "rebuildDB": // Rebuild the database with sample data.
                RebuildDB();
                break;
        }

        // Measure the time it takes to retrieve the results.
        Stopwatch sw = Stopwatch.StartNew();

        switch(resultType)
        {
            case "teamsSortedSet": // Retrieve teams from sorted set.
                teams = GetFromSortedSet();
                break;

            case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                teams = GetFromSortedSetTop5();
                break;

            case "teamsList": // Retrieve teams from the cached List<Team>.
                teams = GetFromList();
                break;

            case "fromDB": // Retrieve results from the database.
            default:
                teams = GetFromDB();
                break;
        }

        sw.Stop();
        double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

        // Add the elapsed time of the operation to the ViewBag.msg.
        ViewBag.msg += " MS: " + ms.ToString();

        return View(teams);
    }
    ```

1. Aggiungere i tre metodi seguenti alla classe `TeamsController` per implementare i tipi di azione `playGames`, `clearCache` e `rebuildDB` dall'istruzione switch aggiunta nel frammento di codice precedente.

    Il metodo `PlayGames` aggiorna le statistiche del team simulando una stagione di partite, salva i risultati nel database e cancella i dati ora obsoleti dalla cache.

    ```csharp
    void PlayGames()
    {
        ViewBag.msg += "Updating team statistics. ";
        // Play a "season" of games.
        var teams = from t in db.Teams
                    select t;

        Team.PlayGames(teams);

        db.SaveChanges();

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    Il metodo `RebuildDB` inizializza di nuovo il database con il set predefinito di team, genera le rispettive statistiche e cancella i dati ora obsoleti dalla cache.

    ```csharp
    void RebuildDB()
    {
        ViewBag.msg += "Rebuilding DB. ";
        // Delete and re-initialize the database with sample data.
        db.Database.Delete();
        db.Database.Initialize(true);

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    Il metodo `ClearCachedTeams` rimuove dalla cache eventuali statistiche del team memorizzate.

    ```csharp
    void ClearCachedTeams()
    {
        IDatabase cache = Connection.GetDatabase();
        cache.KeyDelete("teamsList");
        cache.KeyDelete("teamsSortedSet");
        ViewBag.msg += "Team data removed from cache. ";
    }
    ```

1. Aggiungere i quattro metodi seguenti alla classe `TeamsController` per implementare diversi modi di recupero per le statistiche del team dalla cache e dal database. Ognuno di questi metodi restituisce un valore `List<Team>`, che viene quindi mostrato dalla visualizzazione.

    Il metodo `GetFromDB` legge le statistiche del team dal database.
    ```csharp
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t;

        return results.ToList<Team>();
    }
    ```

    Il metodo `GetFromList` legge le statistiche del team dalla cache, come elemento `List<Team>` serializzato. Se le statistiche non sono presenti nella cache, si verifica un mancato riscontro nella cache. In caso di mancato riscontro nella cache, le statistiche del team vengono lette dal database e quindi archiviate nella cache per la richiesta successiva. In questo esempio viene usata la serializzazione JSON.NET per serializzare gli oggetti .NET da e verso la cache. Per altre informazioni, vedere [Come usare gli oggetti .NET in Cache Redis di Azure](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

    ```csharp
    List<Team> GetFromList()
    {
        List<Team> teams = null;

        IDatabase cache = Connection.GetDatabase();
        string serializedTeams = cache.StringGet("teamsList");
        if (!String.IsNullOrEmpty(serializedTeams))
        {
            teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

            ViewBag.msg += "List read from cache. ";
        }
        else
        {
            ViewBag.msg += "Teams list cache miss. ";
            // Get from database and store in cache
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
        }
        return teams;
    }
    ```

    Il metodo `GetFromSortedSet` legge le statistiche del team da un set ordinato memorizzato nella cache. In caso di mancato riscontro nella cache, le statistiche del team vengono lette dal database e archiviate nella cache come set ordinato.

    ```csharp
    List<Team> GetFromSortedSet()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();
        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
        if (teamsSortedSet.Count() > 0)
        {
            ViewBag.msg += "Reading sorted set from cache. ";
            teams = new List<Team>();
            foreach (var t in teamsSortedSet)
            {
                Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                teams.Add(tt);
            }
        }
        else
        {
            ViewBag.msg += "Teams sorted set cache miss. ";

            // Read from DB
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            foreach (var t in teams)
            {
                Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
            }
        }
        return teams;
    }
    ```

    Il metodo `GetFromSortedSetTop5` legge i primi cinque team dal set ordinato memorizzato nella cache. Controlla prima di tutto la cache per verificare l'esistenza della chiave `teamsSortedSet` . Se la chiave non è presente, viene chiamato il metodo `GetFromSortedSet` per leggere le statistiche del team e archiviarle nella cache. Il set ordinato memorizzato nella cache viene quindi sottoposto a query alla ricerca dei primi cinque team, che vengono restituiti.

    ```csharp
    List<Team> GetFromSortedSetTop5()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();

        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        if(teamsSortedSet.Count() == 0)
        {
            // Load the entire sorted set into the cache.
            GetFromSortedSet();

            // Retrieve the top 5 teams.
            teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        }

        ViewBag.msg += "Retrieving top 5 teams from cache. ";
        // Get the top 5 teams from the sorted set
        teams = new List<Team>();
        foreach (var team in teamsSortedSet)
        {
            teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
        }
        return teams;
    }
    ```

### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>Aggiornare i metodi Create, Edit e Delete per l'interazione con la cache

Il codice di scaffolding generato come parte di questo esempio include metodi per l'aggiunta, la modifica e l'eliminazione di elementi. Quando un team viene aggiunto, modificato o rimosso, i dati nella cache diventano obsoleti. In questa sezione verranno modificati questi tre metodi per cancellare i team memorizzati nella cache, in modo che la cache venga aggiornata.

1. Passare al metodo `Create(Team team)` nella classe `TeamsController`. Aggiungere una chiamata al metodo `ClearCachedTeams`, come illustrato nell'esempio seguente:

    ```csharp
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Teams.Add(team);
            db.SaveChanges();
            // When a team is added, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }

        return View(team);
    }
    ```

2. Passare al metodo `Edit(Team team)` nella classe `TeamsController`. Aggiungere una chiamata al metodo `ClearCachedTeams`, come illustrato nell'esempio seguente:

    ```csharp
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Entry(team).State = EntityState.Modified;
            db.SaveChanges();
            // When a team is edited, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }
        return View(team);
    }
    ```

3. Passare al metodo `DeleteConfirmed(int id)` nella classe `TeamsController`. Aggiungere una chiamata al metodo `ClearCachedTeams`, come illustrato nell'esempio seguente:

    ```csharp
    // POST: Teams/Delete/5
    [HttpPost, ActionName("Delete")]
    [ValidateAntiForgeryToken]
    public ActionResult DeleteConfirmed(int id)
    {
        Team team = db.Teams.Find(id);
        db.Teams.Remove(team);
        db.SaveChanges();
        // When a team is deleted, the cache is out of date.
        // Clear the cached teams.
        ClearCachedTeams();
        return RedirectToAction("Index");
    }
    ```

### <a name="add-caching-methods-to-the-teams-index-view"></a>Aggiungere metodi di memorizzazione nella cache per la visualizzazione dell'indice dei team

1. In **Esplora soluzioni** espandere la cartella **Visualizzazioni**, quindi la cartella **Team** e fare doppio clic su **Index.cshtml**.

    ![Index.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-views-teams-index-cshtml.png)

1. Cercare l'elemento paragrafo seguente nella parte superiore del file:

    ![Tabella delle azioni](./media/cache-web-app-cache-aside-leaderboard/cache-teams-index-table.png)

    Questo collegamento crea un nuovo team. Sostituire l'elemento paragrafo con la tabella seguente. Questa tabella include collegamenti alle azioni per la creazione di un nuovo team, l'avvio di una nuova stagione di partite, la cancellazione della cache, il recupero dei team dalla cache in diversi formati, il recupero di team dal database e la ricompilazione del database con dati di esempio aggiornati.

    ```html
    <table class="table">
        <tr>
            <td>
                @Html.ActionLink("Create New", "Create")
            </td>
            <td>
                @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
            </td>
            <td>
                @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
            </td>
            <td>
                @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
            </td>
            <td>
                @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
            </td>
            <td>
                @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
            </td>
            <td>
                @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
            </td>
            <td>
                @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
            </td>
        </tr>
    </table>
    ```

1. Passare alla parte finale del file **Index.cshtml** e aggiungere l'elemento `tr` seguente, in modo che costituisca l'ultima riga dell'ultima tabella del file:

    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```
    Questa riga mostra il valore di `ViewBag.Msg` che contiene un rapporto sullo stato relativo all'operazione corrente. `ViewBag.Msg` viene impostato quando si fa clic su uno dei collegamenti all'azione del passaggio precedente.

    ![Messaggio di stato](./media/cache-web-app-cache-aside-leaderboard/cache-status-message.png)

1. Premere **F6** per compilare il progetto.

## <a name="run-the-app-locally"></a>Eseguire l'app in locale

Eseguire l'applicazione in locale nel computer in uso per verificare la funzionalità che è stata aggiunta per supportare i team.

In questo test sia l'applicazione che il database vengono eseguiti in locale. Cache Redis di Azure è tuttavia ospitata in remoto in Azure. Pertanto, le prestazioni della cache saranno probabilmente leggermente inferiori rispetto a quelle del database. Per prestazioni ottimali, l'applicazione client e l'istanza di Cache Redis di Azure devono trovarsi nella stessa posizione. Nella sezione successiva verranno distribuite tutte le risorse in Azure per vedere le prestazioni migliorate attraverso l'uso di una cache.

Per eseguire l'app in locale:

1. Premere **CTRL+F5** per eseguire l'applicazione.

    ![App in esecuzione in locale](./media/cache-web-app-cache-aside-leaderboard/cache-local-application.png)

1. Testare ognuno dei nuovi metodi aggiunti alla visualizzazione. Dal momento che la cache è remota in questi test, le prestazioni del database dovrebbero essere leggermente superiori a quelle della cache.

## <a name="publish-and-run-in-azure"></a>Pubblicare ed eseguire in Azure

### <a name="provision-a-sql-azure-database-for-the-app"></a>Effettuare il provisioning di un database di SQL Azure per l'app

In questa sezione si effettuerà il provisioning di un nuovo database di SQL Azure che l'app userà mentre è ospitata in Azure.

1. Nell'angolo in alto a sinistra del [portale di Azure](https://portal.azure.com/) fare clic su **Crea una risorsa**.

1. Nella pagina **Nuovo** fare clic su **Database** > **Database SQL**.

1. Usare le impostazioni seguenti per il nuovo database SQL:

   | Impostazione       | Valore consigliato | Descrizione |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nome database** | *ContosoTeamsDatabase* | Per i nomi di database validi, vedere [Identificatori del database](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). |
   | **Sottoscrizione** | *Sottoscrizione in uso*  | Selezionare la stessa sottoscrizione che è stata usata per creare la cache e ospitare il servizio app. |
   | **Gruppo di risorse**  | *TestResourceGroup* | Fare clic su **Usa esistente** e usare lo stesso gruppo di risorse in cui sono stati inseriti la cache e il servizio app. |
   | **Seleziona origine** | **Database vuoto** | Iniziare con un database vuoto. |

1. In **Server** fare clic su **Configura le impostazioni obbligatorie** > **Crea un nuovo server** e specificare le informazioni seguenti, quindi fare clic sul pulsante **Seleziona**:

   | Impostazione       | Valore consigliato | Descrizione |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nome server** | Qualsiasi nome globalmente univoco | Per i nomi di server validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Accesso amministratore server** | Qualsiasi nome valido | Per i nomi di accesso validi, vedere [Identificatori del database](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). |
   | **Password** | Qualsiasi password valida | La password deve almeno 8 caratteri e contenere caratteri inclusi in tre delle categorie seguenti: caratteri maiuscoli, caratteri minuscoli, numeri e caratteri non alfanumerici. |
   | **Posizione** | *Stati Uniti orientali* | Selezionare la stessa area geografica in cui sono stati creati la cache e il servizio app. |

1. Fare clic su **Pin to dashboard** (Aggiungi al dashboard) e quindi **Crea** per creare il nuovo database e il nuovo server.

1. Dopo aver creato il nuovo database, fare clic su **Mostra stringhe di connessione del database** e copiare la stringa di connessione **ADO.NET**.

    ![Mostra stringhe di connessione](./media/cache-web-app-cache-aside-leaderboard/cache-show-connection-strings.png)

1. Nel portale di Azure passare al servizio app e fare clic su **Impostazioni applicazione**, quindi su **Aggiungi nuova stringa di connessione** nella sezione Stringhe di connessione.

1. Aggiungere una nuova stringa di connessione denominata *TeamContext* corrispondente alla classe di contesto del database Entity Framework. Incollare la stringa di connessione per il nuovo database come valore. Assicurarsi di sostituire i segnaposto seguenti nella stringa di connessione e fare clic su **Salva**:

    | Placeholder | Valore consigliato |
    | --- | --- |
    | *{your_username}* | Usare l'**accesso di amministratore server** per il server di database appena creato. |
    | *{your_password}* | Usare la password per il server di database appena creato. |

    Se vengono aggiunti come impostazioni applicazione, il nome utente e la password non vengono inclusi nel codice. Questo approccio consente di proteggere tali credenziali.

### <a name="publish-the-application-updates-to-azure"></a>Pubblicare gli aggiornamenti dell'applicazione in Azure

In questo passaggio dell'esercitazione l'applicazione verrà aggiornata in Azure per essere eseguita nel cloud.

1. Fare clic con il pulsante destro del mouse sul progetto **ContosoTeamStats** in Visual Studio e scegliere **Pubblica**.

    ![Pubblica](./media/cache-web-app-cache-aside-leaderboard/cache-publish-app.png)

2. Fare clic su **Pubblica** per usare lo stesso profilo di pubblicazione che è stato creato della guida introduttiva.

3. Dopo che la pubblicazione è stata completata, Visual Studio avvia l'app nel Web browser predefinito.

    ![Cache aggiunta](./media/cache-web-app-cache-aside-leaderboard/cache-added-to-application.png)

    La tabella seguente illustra ogni collegamento all'azione dall'applicazione di esempio:

    | Azione | DESCRIZIONE |
    | --- | --- |
    | Creazione di un nuovo sito |Crea un nuovo team. |
    | Play Season |Avvia una stagione di partite, aggiorna le statistiche del team e cancella eventuali dati del team obsoleti dalla cache. |
    | Clear Cache |Cancella le statistiche del team dalla cache. |
    | List from Cache |Recupera le statistiche del team dalla cache. In caso di mancato riscontro nella cache, carica le statistiche dal database e le salva nella cache per la volta successiva. |
    | Sorted Set from Cache |Recupera le statistiche del team dalla cache usando un set ordinato. In caso di mancato riscontro nella cache, carica le statistiche dal database e le salva nella cache mediante un set ordinato. |
    | Top 5 Teams from Cache |Recupera i primi cinque team dalla cache usando un set ordinato. In caso di mancato riscontro nella cache, carica le statistiche dal database e le salva nella cache mediante un set ordinato. |
    | Load from DB |Recupera le statistiche del team dal database. |
    | Rebuild DB |Ricompila il database e lo ricarica con i dati del team di esempio. |
    | Edit / Details / Delete |Modifica un team, visualizza dettagli per un team, elimina un team. |

Fare clic su alcune azioni e provare a recuperare i dati dalle diverse origini. Si notino le differenze a livello di tempo necessario per il completamento nelle varie modalità di recupero di dati dal database e dalla cache.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'uso dell'applicazione di esempio dell'esercitazione, è possibile eliminare le risorse di Azure per ridurre i costi e l'uso di risorse. Tutte le risorse devono essere contenute nello stesso gruppo di risorse; è possibile eliminarle tutte insieme in un'unica operazione eliminando il gruppo di risorse. Le istruzioni di questo argomento usano un gruppo di risorse denominato *TestResources*.

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile e comporta l'eliminazione definitiva del gruppo di risorse e di tutte le risorse incluse nel gruppo. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se le risorse per questo esempio sono state create all'interno di un gruppo di risorse esistente che contiene anche elementi da mantenere, è possibile eliminare ogni risorsa singolarmente dai rispettivi pannelli.
>

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Gruppi di risorse**.
2. Immettere il nome del gruppo di risorse nella casella di testo **Filtra elementi** .
3. Fare clic su **...** a destra del gruppo di risorse, poi fare clic su **Elimina gruppo di risorse**.

    ![Delete](./media/cache-web-app-cache-aside-leaderboard/cache-delete-resource-group.png)

4. Verrà chiesto di confermare l'eliminazione del gruppo di risorse. Immettere il nome del gruppo di risorse per confermare e fare clic su **Elimina**.

    Dopo qualche istante il gruppo di risorse e tutte le risorse che contiene vengono eliminati.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Come ridimensionare Cache Redis di Azure](./cache-how-to-scale.md)