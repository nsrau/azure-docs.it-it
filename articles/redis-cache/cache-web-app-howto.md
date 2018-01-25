---
title: Come creare un'app Web con la cache Redis | Microsoft Docs
description: Informazioni su come creare un'app Web con la cache Redis
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 454e23d7-a99b-4e6e-8dd7-156451d2da7c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: hero-article
ms.date: 05/09/2017
ms.author: wesmc
ms.openlocfilehash: c0cf5baa71ce599cd5c20d34c42bd2c578114efe
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="how-to-create-a-web-app-with-redis-cache"></a>Come creare un'app Web con la cache Redis
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.JS](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Questa esercitazione illustra come creare e distribuire un'applicazione Web ASP.NET in un'app Web nel servizio app di Azure usando Visual Studio 2017. L'applicazione di esempio mostra un elenco di statistiche del team da un database e illustra i diversi modi in cui è possibile usare la cache Redis di Azure per archiviare e recuperare i dati dalla cache. Al termine dell'esercitazione, si avrà un'app Web in esecuzione che legge e scrive in un database, ottimizzata per la cache Redis di Azure e ospitata in Azure.

Si apprenderà come:

* Come creare un'applicazione Web ASP.NET MVC 5 in Visual Studio.
* Come accedere ai dati da un database usando Entity Framework.
* Come ottimizzare la velocità effettiva dei dati e ridurre il carico del database archiviando e recuperando i dati con la cache Redis di Azure.
* Come usare un set ordinato Redis per recuperare i primi 5 team.
* Come effettuare il provisioning delle risorse di Azure per l'applicazione usando un modello di Azure Resource Manager.
* Come pubblicare l'applicazione in Azure usando Visual Studio.

## <a name="prerequisites"></a>prerequisiti
Per completare l'esercitazione, sono necessari i prerequisiti seguenti.

* [Account di Azure](#azure-account)
* [Visual Studio 2017 con Azure SDK per .NET](#visual-studio-2017-with-the-azure-sdk-for-net)

### <a name="azure-account"></a>Account Azure
Per completare l'esercitazione, è necessario un account Azure. È possibile:

* [Aprire un account Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero): sono inclusi crediti da usare per provare i servizi di Azure a pagamento. Una volta esauriti i crediti, è possibile mantenere l'account e usare le funzionalità e i servizi di Azure gratuiti.
* [Attivare i vantaggi della sottoscrizione Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). con l'abbonamento MSDN ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento.

### <a name="visual-studio-2017-with-the-azure-sdk-for-net"></a>Visual Studio 2017 con Azure SDK per .NET
L'esercitazione è stata scritta per Visual Studio 2017 con [Azure SDK per .NET](https://www.visualstudio.com/news/releasenotes/vs2017-relnotes#azuretools). Azure SDK 2.9.5 è incluso nel programma di installazione di Visual Studio.

Se è installato Visual Studio 2015, è possibile seguire l'esercitazione con [Azure SDK per .NET](../dotnet-sdk.md) 2.8.2 o versione successiva. [Scaricare qui la versione più recente di Azure SDK per Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003). Visual Studio viene installato automaticamente con l'SDK, se necessario. Alcune schermate potrebbero essere diverse da quelle illustrate nell'esercitazione.

Se è disponibile Visual Studio 2013, è possibile [scaricare la versione più recente di Azure SDK per Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322). Alcune schermate potrebbero essere diverse da quelle illustrate nell'esercitazione.

## <a name="create-the-visual-studio-project"></a>Creare il progetto di Visual Studio
1. Aprire Visual Studio e fare clic su **File**, **Nuovo**, **Progetto**.
2. Espandere il nodo **Visual C#** nell'elenco **Modelli**, selezionare **Cloud** e fare clic su **Applicazione Web ASP.NET**. Assicurarsi che sia selezionata l'opzione **.NET Framework 4.5.2** o versione successiva.  Immettere **ContosoTeamStats** nella casella di testo **Nome** e fare clic su **OK**.
   
    ![Crea progetto][cache-create-project]
3. Selezionare **MVC** come tipo di progetto. 

    Assicurarsi che per l'impostazione **Autenticazione** sia specificato **Nessuna autenticazione**. A seconda della versione di Visual Studio, il valore predefinito può essere diverso. Per modificarlo, fare clic su **Modifica autenticazione** e selezionare **Nessuna autenticazione**.

    Se si usa Visual Studio 2015, deselezionare la casella di controllo **Ospita nel cloud**. Nei passaggi successivi dell'esercitazione si [effettuerà il provisioning delle risorse di Azure](#provision-the-azure-resources) e si [pubblicherà l'applicazione in Azure](#publish-the-application-to-azure). Per un esempio di provisioning di un'app Web del servizio app da Visual Studio con l'opzione **Ospita nel cloud** selezionata, vedere [Introduzione alle app Web nel servizio app di Azure con ASP.NET e Visual Studio](../app-service/app-service-web-get-started-dotnet.md).
   
    ![Seleziona modello progetto][cache-select-template]
4. Fare clic su **OK** per creare il progetto.

## <a name="create-the-aspnet-mvc-application"></a>Creare l'applicazione ASP.NET MVC
In questa sezione dell'esercitazione verrà creata l'applicazione di base che legge e visualizza le statistiche del team da un database.

* [Aggiungere il pacchetto NuGet di Entity Framework](#add-the-entity-framework-nuget-package)
* [Aggiungere il modello](#add-the-model)
* [Aggiungere il controller](#add-the-controller)
* [Configurare le visualizzazioni](#configure-the-views)

### <a name="add-the-entity-framework-nuget-package"></a>Aggiungere il pacchetto NuGet di Entity Framework

1. Nel menu **Strumenti** fare clic su **Gestione pacchetti NuGet** e quindi su **Console di Gestione pacchetti**.
2. Nella finestra **Console di Gestione pacchetti** eseguire il comando seguente.
    
    ```
    Install-Package EntityFramework
    ```

Per altre informazioni sul pacchetto, vedere la pagina NuGet relativa a [Entity Framework](https://www.nuget.org/packages/EntityFramework/).

### <a name="add-the-model"></a>Aggiungere il modello
1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Modelli**, quindi scegliere **Aggiungi**, **Classe**. 
   
    ![Aggiungi modello][cache-model-add-class]
2. Immettere `Team` come nome della classe e fare clic su **Aggiungi**.
   
    ![Aggiunta di una classe modello][cache-model-add-class-dialog]
3. Sostituire le istruzioni `using` all'inizio del file `Team.cs` con le istruzioni `using` seguenti.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```


1. Sostituire la definizione della classe `Team` con il frammento di codice seguente che contiene una classe `Team` aggiornata, oltre ad altre classi helper di Entity Framework. Per altre informazioni sull'approccio Code First per Entity Framework usato in questa esercitazione, vedere [Code First per un nuovo database](https://msdn.microsoft.com/data/jj193542).

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


1. In **Esplora soluzioni** fare doppio clic su **web.config** per aprirlo.
   
    ![Web.config][cache-web-config]
2. Aggiungere la sezione `connectionStrings` riportata di seguito. Il nome della stringa di connessione deve corrispondere al nome della classe contesto del database Entity Framework, ovvero `TeamContext`.

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    È possibile aggiungere la nuova sezione `connectionStrings` in modo che segua `configSections`, come illustrato nell'esempio seguente.

    ```xml
    <configuration>
      <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
      </configSections>
      <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
      </connectionStrings>
      ...
      ```

    > [!NOTE]
    > La stringa di connessione potrebbe essere diversa a seconda della versione di Visual Studio e SQL Server Express Edition usate per completare l'esercitazione. Il modello di web.config deve essere configurato in modo da corrispondere all'installazione e può contenere voci `Data Source` come `(LocalDB)\v11.0` da SQL Server Express 2012 o `Data Source=(LocalDB)\MSSQLLocalDB` da SQL Server Express 2014 e versioni successive. Per altre informazioni sulle stringhe di connessione e le versioni di SQL Express, vedere [SQL Server 2016 Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-2016-express-localdb).

### <a name="add-the-controller"></a>Aggiungere il controller
1. Premere **F6** per compilare il progetto. 
2. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella **Controller**, quindi scegliere **Aggiungi**, **Controller**.
   
    ![Aggiungi controller][cache-add-controller]
3. Scegliere **Controller MVC 5 con visualizzazioni, che usa Entity Framework** e quindi fare clic su **Aggiungi**. Se viene visualizzato un errore dopo la selezione di **Aggiungi**, assicurarsi prima di tutto di avere compilato il progetto.
   
    ![Aggiunta di una classe controller][cache-add-controller-class]
4. Selezionare **Team (ContosoTeamStats.Models)** dall'elenco a discesa **Classe modello**. Selezionare **TeamContext (ContosoTeamStats.Models)** dall'elenco a discesa **Classe del contesto dei dati**. Immettere `TeamsController` nella casella di testo **Nome controller** , se non è stata popolata automaticamente. Fare clic su **Aggiungi** per creare la classe controller e aggiungere le visualizzazioni predefinite.
   
    ![Configura controller][cache-configure-controller]
5. In **Esplora soluzioni** espandere **Global.asax** e fare doppio clic su **Global.asax.cs** per aprirlo.
   
    ![Global.asax.cs][cache-global-asax]
6. Aggiungere le due istruzioni `using` seguenti all'inizio del file, dopo le altre due istruzioni `using`.

    ```csharp
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```


1. Aggiungere la riga di codice seguente alla fine del metodo `Application_Start` .

    ```csharp
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```


1. In **Esplora soluzioni** espandere `App_Start` e fare doppio clic su `RouteConfig.cs`.
   
    ![RouteConfig.cs][cache-RouteConfig-cs]
2. Sostituire `controller = "Home"` nel codice seguente nel metodo `RegisterRoutes` con `controller = "Teams"`, come illustrato nell'esempio seguente.

    ```csharp
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```


### <a name="configure-the-views"></a>Configurare le visualizzazioni
1. In **Esplora soluzioni** espandere la cartella **Visualizzazioni**, quindi la cartella **Condiviso** e infine fare doppio clic su **_Layout.cshtml**. 
   
    ![file _Layout.cshtml][cache-layout-cshtml]
2. Cambiare il contenuto dell'elemento `title` e sostituire `My ASP.NET Application` con `Contoso Team Stats`, come illustrato nell'esempio seguente.

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```


1. Nella sezione `body` aggiornare la prima istruzione `Html.ActionLink` e sostituire `Application name` con `Contoso Team Stats`, quindi sostituire `Home` con `Teams`.
   
   * Prima: `@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })`
   * Dopo: `@Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
     
     ![Modifiche al codice][cache-layout-cshtml-code]
2. Premere **CTRL+F5** per compilare ed eseguire l'applicazione. Questa versione dell'applicazione legge i risultati direttamente dal database. Si notino le azioni **Crea nuovo**, **Modifica**, **Dettagli** ed **Elimina** aggiunte automaticamente all'applicazione dallo scaffolding **Controller MVC 5 con visualizzazioni, che usa Entity Framework**. Nella sezione successiva dell'esercitazione verrà aggiunta la cache Redis, per ottimizzare l'accesso ai dati e fornire funzionalità aggiuntive all'applicazione.

![Applicazione iniziale][cache-starter-application]

## <a name="configure-the-application-to-use-redis-cache"></a>Configurare l'applicazione per l'uso della cache Redis
In questa sezione dell'esercitazione verrà configurata l'applicazione di esempio per archiviare e recuperare le statistiche del team Contoso da un'istanza della cache Redis di Azure usando il client [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) della cache.

* [Configurare l'applicazione per l'uso di StackExchange.Redis](#configure-the-application-to-use-stackexchangeredis)
* [Aggiornare la classe TeamsController per restituire risultati dalla cache o dal database](#update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database)
* [Aggiornare i metodi Create, Edit e Delete per l'interazione con la cache](#update-the-create-edit-and-delete-methods-to-work-with-the-cache)
* [Aggiornare la visualizzazione Index dei team per l'interazione con la cache](#update-the-teams-index-view-to-work-with-the-cache)

### <a name="configure-the-application-to-use-stackexchangeredis"></a>Configurare l'applicazione per l'uso di StackExchange.Redis
1. Per configurare un'applicazione client in Visual Studio con il pacchetto NuGet StackExchange.Redis, fare clic su **Gestione pacchetti NuGet** e quindi su **Console di Gestione pacchetti** dal menu **Strumenti**.
2. Eseguire questo comando nella finestra `Package Manager Console`.
    
    ```
    Install-Package StackExchange.Redis
    ```
   
    Il pacchetto NuGet scarica e aggiunge i riferimenti ad assembly necessari per consentire all'applicazione client di accedere a Cache Redis di Azure con il client della cache StackExchange.Redis. Se si preferisce usare una versione con nome sicuro della libreria client `StackExchange.Redis`, installare il pacchetto `StackExchange.Redis.StrongName`.
3. In **Esplora soluzioni** espandere la cartella **Controller** e fare doppio clic su **TeamsController.cs** per aprirlo.
   
    ![Controller Teams][cache-teamscontroller]
4. Aggiungere le due istruzioni `using` seguenti a **TeamsController.cs**.

    ```csharp   
    using System.Configuration;
    using StackExchange.Redis;
    ```

5. Aggiungere le due proprietà seguenti alla classe `TeamsController` .

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

6. Creare un file nel computer denominato `WebAppPlusCacheAppSecrets.config` e inserirlo in una posizione che non verrà archiviata con il codice sorgente dell'applicazione di esempio, nel caso in cui si decidesse di archiviarlo. In questo esempio il file `AppSettingsSecrets.config` si trova in `C:\AppSecrets\WebAppPlusCacheAppSecrets.config`.
   
    Modificare il file `WebAppPlusCacheAppSecrets.config` e aggiungere il contenuto seguente. Se si esegue localmente l'applicazione, queste informazioni vengono usate per connettersi all'istanza della cache Redis di Azure. Successivamente nell'esercitazione verrà effettuato il provisioning di un'istanza della cache Redis di Azure e verranno aggiornati il nome e la password della cache. Se non si prevede di eseguire localmente l'applicazione di esempio, è possibile ignorare la creazione di questo file e i passaggi successivi che fanno riferimento al file, perché, quando si esegue la distribuzione in Azure, l'applicazione recupera le informazioni di connessione della cache dall'impostazione dell'app per l'app Web, non da questo file. Poiché `WebAppPlusCacheAppSecrets.config` non viene distribuito in Azure con l'applicazione, sarà necessario solo se si esegue l'applicazione localmente.

    ```xml
    <appSettings>
      <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
    </appSettings>
    ```


1. In **Esplora soluzioni** fare doppio clic su **web.config** per aprirlo.
   
    ![Web.config][cache-web-config]
2. Aggiungere l'attributo `file` seguente all'elemento `appSettings`. Se è stato usato un nome o un percorso di file diverso, sostituire i valori dell'esempio con questi valori.
   
   * Prima: `<appSettings>`
   * Dopo: ` <appSettings file="C:\AppSecrets\WebAppPlusCacheAppSecrets.config">`
     
   Il runtime ASP.NET unisce il contenuto del file esterno con il markup nell'elemento `<appSettings>` . Il runtime ignora l'attributo del file, se non è possibile trovare il file specificato. I segreti, ovvero la stringa di connessione per la cache, non sono inclusi come parte del codice sorgente per l'applicazione. Quando si distribuisce l'app Web in Azure, il file `WebAppPlusCacheAppSecrests.config` non verrà distribuito (approccio consigliato). È possibile specificare in molti modi questi segreti in Azure e in questa esercitazione vengono configurati automaticamente quando si effettua il [provisioning delle risorse di Azure](#provision-the-azure-resources) in un passaggio successivo dell'esercitazione. Per altre informazioni sull'uso dei segreti in Azure, vedere l'articolo [Best practices for deploying passwords and other sensitive data to ASP.NET and Azure App Service](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)(Procedure consigliate per la distribuzione di password e altri dati sensibili in ASP.NET e nel servizio app di Azure).

### <a name="update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database"></a>Aggiornare la classe TeamsController per restituire risultati dalla cache o dal database
In questo esempio è possibile recuperare le statistiche del team dal database o dalla cache. Le statistiche del team vengono archiviate nella cache come elemento `List<Team>`serializzato e anche come set ordinato usando tipi di dati Redis. Quando si recuperano elementi da un set ordinato, è possibile recuperare alcuni o tutti gli elementi o eseguire query per determinati elementi. In questo esempio verrà eseguita una query sul set ordinato per i primi cinque team classificati in base al numero di vittorie.

> [!NOTE]
> Per usare la cache Redis di Azure, non è necessario archiviare le statistiche del team in più formati nella cache. Questa esercitazione usa più formati per illustrare alcuni dei modi diversi e dei tipi di dati diversi che è possibile usare per memorizzare i dati nella cache.
> 
> 

1. Aggiungere le istruzioni `using` seguenti nella parte iniziale del file `TeamsController.cs`, insieme alle altre istruzioni `using`.

    ```csharp   
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

2. Sostituire l'implementazione del metodo `public ActionResult Index()` corrente con l'implementazione seguente.

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


1. Aggiungere i quattro metodi seguenti alla classe `TeamsController` per implementare diversi modi di recupero per le statistiche del team dalla cache e dal database. Ogni metodo restituisce un valore `List<Team>` , che viene quindi mostrato nella visualizzazione.
   
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

    Il metodo `GetFromList` legge le statistiche del team dalla cache, come elemento `List<Team>` serializzato. In caso di mancato riscontro nella cache, le statistiche del team vengono lette dal database e quindi archiviate nella cache per la volta successiva. In questo esempio viene usata la serializzazione JSON.NET per serializzare gli oggetti .NET verso e dalla cache. Per altre informazioni, vedere [Come usare gli oggetti .NET nella cache Redis di Azure](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

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
Il codice di scaffolding generato come parte di questo esempio include metodi per l'aggiunta, la modifica e l'eliminazione di elementi. Quando un team viene aggiunto, modificato o rimosso, i dati nella cache diventano obsoleti. In questa sezione verranno modificati questi tre metodi per cancellare i team memorizzati nella cache, in modo che la cache non risulti non sincronizzata con il database.

1. Passare al metodo `Create(Team team)` nella classe `TeamsController`. Aggiungere una chiamata al metodo `ClearCachedTeams` , come illustrato nell'esempio seguente.

    ```csharp
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
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


1. Passare al metodo `Edit(Team team)` nella classe `TeamsController`. Aggiungere una chiamata al metodo `ClearCachedTeams` , come illustrato nell'esempio seguente.

    ```csharp
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
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


1. Passare al metodo `DeleteConfirmed(int id)` nella classe `TeamsController`. Aggiungere una chiamata al metodo `ClearCachedTeams` , come illustrato nell'esempio seguente.

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


### <a name="update-the-teams-index-view-to-work-with-the-cache"></a>Aggiornare la visualizzazione Index dei team per l'interazione con la cache
1. In **Esplora soluzioni** espandere la cartella **Visualizzazioni**, quindi la cartella **Team** e fare doppio clic su **Index.cshtml**.
   
    ![Index.cshtml][cache-views-teams-index-cshtml]
2. Cercare l'elemento paragrafo seguente nella parte superiore del file.
   
    ![Tabella delle azioni][cache-teams-index-table]
   
    Questo è il collegamento per la creazione di un nuovo team. Sostituire l'elemento paragrafo con la tabella seguente. Questa tabella include collegamenti alle azioni per la creazione di un nuovo team, l'avvio di una nuova stagione di partite, la cancellazione della cache, il recupero dei team dalla cache in diversi formati, il recupero di team dal database e la ricompilazione del database con dati di esempio aggiornati.

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


1. Passare alla parte finale del file **Index.cshtml** e aggiungere l'elemento `tr` seguente, in modo che costituisca l'ultima riga dell'ultima tabella del file.
   
    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```
   
    Questa riga mostra il valore di `ViewBag.Msg` che contiene un rapporto sullo stato relativo all'operazione corrente. `ViewBag.Msg` viene impostato quando si fa clic su uno dei collegamenti all'azione del passaggio precedente.   
   
    ![Messaggio di stato][cache-status-message]
2. Premere **F6** per compilare il progetto.

## <a name="provision-the-azure-resources"></a>provisioning delle risorse di Azure
Per ospitare l'applicazione in Azure, è prima di tutto necessario effettuare il provisioning dei servizi di Azure richiesti dall'applicazione. L'applicazione di esempio di questa esercitazione usa i servizi di Azure seguenti.

* Cache Redis di Azure
* App Web del servizio app
* Database SQL

Per distribuire questi servizi in un gruppo di risorse nuovo o esistente, fare clic sul pulsante **Distribuisci in Azure** seguente.

[![Distribuzione in Azure][deploybutton]](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-redis-cache-sql-database%2Fazuredeploy.json)

Il pulsante **Distribuisci in Azure** usa il modello per [creare un'app Web con cache Redis e database SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) di [Avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates) per effettuare il provisioning di questi servizi e configurare la stringa di connessione per il database SQL e l'impostazione dell'applicazione per la stringa di connessione della cache Redis di Azure.

> [!NOTE]
> Se non si ha un account Azure, è possibile [creare un account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero) in pochi minuti.
> 
> 

Se si fa clic sul pulsante **Distribuisci in Azure** , verrà aperto il portale di Azure e verrà avviato il processo di creazione delle risorse descritte dal modello.

![Distribuisci in Azure][cache-deploy-to-azure-step-1]

1. Nella sezione **Informazioni di base** selezionare la sottoscrizione di Azure da usare, quindi selezionare un gruppo di risorse esistente o crearne uno nuovo e infine specificare la posizione del gruppo di risorse.
2. Nella sezione **Impostazioni** specificare un **account di accesso amministratore** diverso da **admin**, la **password di accesso dell'amministratore** e il **nome del database**. Gli altri parametri vengono configurati per un piano di hosting del Servizio app gratuito e per i piani con opzioni di costo inferiori per il database SQL e la Cache Redis di Azure, non disponibili con un livello gratuito.

    ![Distribuisci in Azure][cache-deploy-to-azure-step-2]

3. Dopo avere configurato le impostazioni desiderate, scorrere fino alla fine della pagina, leggere i termini e le condizioni e selezionare la casella di controllo **Accetto le condizioni riportate sopra**.
4. Per iniziare il provisioning delle risorse, fare clic su **Acquista**.

Per visualizzare lo stato della distribuzione, fare clic sull'icona di notifica e quindi su **La distribuzione è stata avviata**.

![La distribuzione è stata avviata][cache-deployment-started]

È possibile visualizzare lo stato della distribuzione nel pannello **Microsoft.Template** .

![Distribuisci in Azure][cache-deploy-to-azure-step-3]

Al termine del provisioning, è possibile pubblicare l'applicazione in Azure da Visual Studio.

> [!NOTE]
> Eventuali errori durante il processo di provisioning vengono visualizzati nel pannello **Microsoft.Template** . Gli errori comuni sono relativi a un numero eccessivo di server SQL o di piani di hosting del Servizio app gratuito per ogni sottoscrizione. Risolvere eventuali errori e riavviare il processo facendo clic su **Ridistribuisci** nel pannello **Microsoft.Template** o sul pulsante **Distribuisci in Azure** in questa esercitazione.
> 
> 

## <a name="publish-the-application-to-azure"></a>Pubblicare l'applicazione in Azure
In questo passaggio dell'esercitazione l'applicazione verrà pubblicata in Azure e verrà eseguita sul cloud.

1. Fare clic con il pulsante destro del mouse sul progetto **ContosoTeamStats** in Visual Studio e scegliere **Pubblica**.
   
    ![Pubblica][cache-publish-app]
2. Fare clic su **Servizio app di Microsoft Azure**, scegliere **Seleziona esistente** e fare clic su **Pubblica**.
   
    ![Pubblica][cache-publish-to-app-service]
3. Selezionare la sottoscrizione usata durante la creazione delle risorse di Azure, espandere il gruppo di risorse contenente le risorse e scegliere l'app Web da usare. Se è stato usato il pulsante **Distribuisci in Azure**, il nome dell'app Web inizierà con **webSite**, seguito da caratteri aggiuntivi.
   
    ![Selezione dell'app Web][cache-select-web-app]
4. Fare clic su **OK** per iniziare il processo di pubblicazione. Il processo di pubblicazione viene completato in pochi minuti e viene aperto un browser con l'applicazione di esempio in esecuzione. Se viene visualizzato un errore DNS durante la convalida o la pubblicazione e il processo di provisioning per le risorse di Azure per l'applicazione è stato completato di recente, attendere qualche minuto e riprovare.
   
    ![Cache aggiunta][cache-added-to-application]

La tabella seguente illustra ogni collegamento all'azione dall'applicazione di esempio.

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

Fare clic su alcune azioni e provare a recuperare i dati dalle diverse origini. Notare le differenze a livello di tempo necessario per il completamento nelle varie modalità di recupero di dati dal database e dalla cache.

## <a name="delete-the-resources-when-you-are-finished-with-the-application"></a>Eliminare le risorse al termine dell'uso dell'applicazione
Al termine dell'uso dell'applicazione di esempio dell'esercitazione, è possibile eliminare le risorse di Azure per ridurre i costi e l'uso di risorse. Se si usa il pulsante **Distribuisci in Azure** nella sezione [Effettuare il provisioning delle risorse di Azure](#provision-the-azure-resources) e se tutte le risorse sono contenute nello stesso gruppo di risorse, è possibile eliminarle tutte insieme in una sola operazione, eliminando il gruppo di risorse.

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Gruppi di risorse**.
2. Immettere il nome del gruppo di risorse nella casella di testo **Filtra elementi** .
3. Fare clic su **...** a destra del gruppo di risorse.
4. Fare clic su **Elimina**.
   
    ![Delete][cache-delete-resource-group]
5. Immettere il nome del gruppo di risorse e fare clic su **Elimina**.
   
    ![Conferma dell'eliminazione][cache-delete-confirm]

Dopo alcuni minuti il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati.

> [!IMPORTANT]
> Si noti che l'eliminazione di un gruppo di risorse è irreversibile e che il gruppo di risorse e tutte le risorse incluse nel gruppo vengono eliminati definitivamente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se le risorse per l'hosting di questo esempio sono state create in un gruppo di risorse esistente, è possibile eliminare singolarmente ogni risorsa dal rispettivo pannello.
> 
> 

## <a name="run-the-sample-application-on-your-local-machine"></a>Eseguire l'applicazione di esempio nel computer locale
Per eseguire l'applicazione localmente nel computer, è necessaria un'istanza della cache Redis di Azure in cui memorizzare i dati. 

* Se l'applicazione è stata pubblicata in Azure come illustrato nella sezione precedente, è possibile usare l'istanza della cache Redis di Azure di cui è stato effettuato il provisioning in quel passaggio.
* Se è disponibile un'altra istanza esistente della cache Redis di Azure, è possibile usarla per eseguire localmente questo esempio.
* Se è necessario creare un'istanza della cache Redis di Azure, è possibile seguire la procedura illustrata in [Creare una cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

Dopo la selezione o la creazione della cache da usare, passare alla cache nel portale di Azure e recuperare il [nome host](cache-configure.md#properties) e le [chiavi di accesso](cache-configure.md#access-keys) per la cache. Per istruzioni, vedere [Configurare le impostazioni della cache Redis](cache-configure.md#configure-redis-cache-settings).

1. Aprire il file `WebAppPlusCacheAppSecrets.config` creato durante il passaggio [Configurare l'applicazione per l'uso della cache Redis](#configure-the-application-to-use-redis-cache) di questa esercitazione usando l'editor preferito.
2. Modificare l'attributo `value` e sostituire `MyCache.redis.cache.windows.net` con il [nome host](cache-configure.md#properties) della cache, quindi specificare la [chiave primaria o secondaria](cache-configure.md#access-keys) della cache come password.

    ```xml
    <appSettings>
      <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
    </appSettings>
    ```


1. Premere **CTRL+F5** per eseguire l'applicazione.

> [!NOTE]
> Si noti che poiché l'applicazione, incluso il database, viene eseguita localmente e la Cache Redis è ospitata in Azure, è possibile che le prestazioni della cache risultino inferiori a quelle del database. Per prestazioni ottimali, l'applicazione client e la cache Redis di Azure devono trovarsi nella stessa posizione. 
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni, vedere [Getting Started with ASP.NET MVC 5](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started) (Introduzione ad ASP.NET MVC 5) nel sito [ASP.NET](http://asp.net/).
* Per altri esempi di creazione di un'app Web ASP.NET nel servizio app, vedere [Create and deploy an ASP.NET web app in Azure App Service](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service) (Creare e distribuire un'app Web ASP.NET nel sevizio app di Azure) per la [demo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/) [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) di Connect 2015.
  * Per altre guide introduttive della demo HealthClinic.biz, vedere [Azure Developer Tools Quickstarts](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)(Guide introduttive agli strumenti di sviluppo di Azure).
* Altre informazioni sull'approccio [Code First per un nuovo database](https://msdn.microsoft.com/data/jj193542) per Entity Framework usato in questa esercitazione.
* Altre informazioni sulle [app Web nel servizio app di Azure](../app-service/app-service-web-overview.md).
* Altre informazioni sul [monitoraggio](cache-how-to-monitor.md) della cache nel portale di Azure.
* Esplorare le funzionalità Premium della cache Redis di Azure
  
  * [Come configurare la persistenza per una Cache Redis di Azure Premium](cache-how-to-premium-persistence.md)
  * [Come configurare il servizio cluster per una Cache Redis di Azure Premium](cache-how-to-premium-clustering.md)
  * [Come configurare il supporto di una rete virtuale per una Cache Redis di Azure Premium](cache-how-to-premium-vnet.md)
  * Per altri dettagli su dimensioni, velocità effettiva e larghezza di banda con le cache Premium, vedere [Domande frequenti su Cache Redis di Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) .

<!-- IMAGES -->
[cache-starter-application]: ./media/cache-web-app-howto/cache-starter-application.png
[cache-added-to-application]: ./media/cache-web-app-howto/cache-added-to-application.png
[cache-create-project]: ./media/cache-web-app-howto/cache-create-project.png
[cache-select-template]: ./media/cache-web-app-howto/cache-select-template.png
[cache-model-add-class]: ./media/cache-web-app-howto/cache-model-add-class.png
[cache-model-add-class-dialog]: ./media/cache-web-app-howto/cache-model-add-class-dialog.png
[cache-add-controller]: ./media/cache-web-app-howto/cache-add-controller.png
[cache-add-controller-class]: ./media/cache-web-app-howto/cache-add-controller-class.png
[cache-configure-controller]: ./media/cache-web-app-howto/cache-configure-controller.png
[cache-global-asax]: ./media/cache-web-app-howto/cache-global-asax.png
[cache-RouteConfig-cs]: ./media/cache-web-app-howto/cache-RouteConfig-cs.png
[cache-layout-cshtml]: ./media/cache-web-app-howto/cache-layout-cshtml.png
[cache-layout-cshtml-code]: ./media/cache-web-app-howto/cache-layout-cshtml-code.png
[redis-cache-manage-nuget-menu]: ./media/cache-web-app-howto/redis-cache-manage-nuget-menu.png
[redis-cache-stack-exchange-nuget]: ./media/cache-web-app-howto/redis-cache-stack-exchange-nuget.png
[cache-teamscontroller]: ./media/cache-web-app-howto/cache-teamscontroller.png
[cache-web-config]: ./media/cache-web-app-howto/cache-web-config.png
[cache-views-teams-index-cshtml]: ./media/cache-web-app-howto/cache-views-teams-index-cshtml.png
[cache-teams-index-table]: ./media/cache-web-app-howto/cache-teams-index-table.png
[cache-status-message]: ./media/cache-web-app-howto/cache-status-message.png
[deploybutton]: ./media/cache-web-app-howto/deploybutton.png
[cache-deploy-to-azure-step-1]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-1.png
[cache-deploy-to-azure-step-2]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-2.png
[cache-deploy-to-azure-step-3]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-3.png
[cache-deployment-started]: ./media/cache-web-app-howto/cache-deployment-started.png
[cache-publish-app]: ./media/cache-web-app-howto/cache-publish-app.png
[cache-publish-to-app-service]: ./media/cache-web-app-howto/cache-publish-to-app-service.png
[cache-select-web-app]: ./media/cache-web-app-howto/cache-select-web-app.png
[cache-publish]: ./media/cache-web-app-howto/cache-publish.png
[cache-delete-resource-group]: ./media/cache-web-app-howto/cache-delete-resource-group.png
[cache-delete-confirm]: ./media/cache-web-app-howto/cache-delete-confirm.png

