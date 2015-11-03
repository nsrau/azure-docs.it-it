<properties 
	pageTitle="Come usare Cache Redis di Azure" 
	description="Informazioni su come migliorare le prestazioni delle applicazioni Azure con Cache Redis di Azure" 
	services="redis-cache,app-service" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="10/27/2015" 
	ms.author="sdanie"/>

# Come usare Cache Redis di Azure

> [AZURE.SELECTOR]
- [.Net](cache-dotnet-how-to-use-azure-redis-cache.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

In questa guida viene illustrato come usare **Cache Redis di Azure**. Cache Redis di Microsoft Azure si basa sulla popolare cache Redis open source e consente di accedere a una cache Redis sicura e dedicata gestita da Microsoft. Una cache creata usando Cache Redis di Azure sarà accessibile da qualsiasi applicazione all'interno di Microsoft Azure.

Cache Redis di Microsoft Azure è disponibile nei seguenti livelli:

-	**Basic**: singolo nodo, Più dimensioni fino a 53 GB.
-	**Standard**: principale/replica a due nodi. Più dimensioni fino a 53 GB. Contratti di servizio del 99,9%.
-	**Premium**: attualmente in anteprima. Due nodi primario/Replica che ha fino a 10 partizioni. Più dimensioni da 6 GB a 530 GB (contattare Microsoft per ulteriori informazioni). Supporto per tutte le funzionalità del piano Standard e altre, tra cui [cluster Redis](cache-how-to-premium-clustering.md), [persistenza Redis](cache-how-to-premium-persistence.md) e [Rete virtuale di Azure](cache-how-to-premium-vnet.md). Nessun contratto di servizio durante il periodo di anteprima.

Ogni livello presenta differenze in termini di funzionalità e prezzi. Per altre informazioni sui prezzi, vedere [Dettagli prezzi del servizio Cache][].

Questa guida mostra come usare il client [StackExchange.Redis][] con il codice C#. Gli scenari presentati includono **creazione e configurazione di una cache**, **configurazione di client della cache** e **aggiunta e rimozione di oggetti dalla cache**. Per ulteriori informazioni sull’uso di Cache Redis di Azure, fare riferimento alla sezione [Passaggi successivi][].

<a name="getting-started-cache-service"></a>
## Introduzione all'uso di Cache Redis di Azure

Iniziare a usare Cache Redis di Azure è semplice. Innanzitutto, è necessario eseguire il provisioning e la configurazione di una cache. Successivamente, verranno configurati i client della cache affinché possano accedere alla cache. Dopo avere configurato i client della cache, è possibile iniziare a usarli.

-	[Creare la cache][]
-	[Configurare i client della cache][]

<a name="create-cache"></a>
## Creare una cache

Per creare una cache, accedere prima al [portale di anteprima di Azure][] e fare clic su **Nuovo**, **Dati e archiviazione**, **Cache Redis**.

![New cache][NewCacheMenu]

>[AZURE.NOTE]Se non si dispone di un account Azure, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][].

Nel pannello **Nuova cache Redis** specificare la configurazione desiderata per la cache.

![Create cache][CacheCreate]

-	In **Nome DNS** immettere il nome di un sottodominio da usare per l'endpoint della cache. L'endpoint deve essere una stringa con un numero di caratteri compreso tra sei e 20, deve contenere solo numeri e lettere minuscole e deve iniziare con una lettera.
-	Per **Sottoscrizione** selezionare la sottoscrizione di Azure da usare per la cache. Se l'account dispone di una sola sottoscrizione, questa verrà selezionata automaticamente e l'elenco a discesa **Sottoscrizione** non verrà visualizzato.
-	In **Gruppo di risorse** selezionare o creare un gruppo di risorse per la cache. Per altre informazioni, vedere [Uso di Gruppi di risorse per gestire le risorse di Azure][]. 
-	Usare **Posizione** per specificare la posizione geografica in cui viene ospitata la cache. Per prestazioni ottimali, è consigliabile creare la cache nella stessa area dell'applicazione client della cache.
-	Usare **Livello di prezzo** per selezionare le dimensioni e le funzionalità desiderate per la cache.
-	Il **cluster Redis** consente di creare cache con dimensioni maggiori di 53 GB e di partizionare i dati su più nodi Redis. Per altre informazioni, vedere [Come configurare il clustering per una Cache Redis di Azure Premium](cache-how-to-premium-clustering.md).
-	**Persistenza Redis** consente di rendere la cache persistente in un account di archiviazione di Azure. Per istruzioni su come configurare la persistenza, vedere [Come configurare la persistenza per una Cache Redis di Azure Premium](cache-how-to-premium-persistence.md).
-	**Rete virtuale** fornisce isolamento e protezione avanzati limitando l'accesso alla cache ai soli client che si trovano all'interno della rete virtuale di Azure specificata. È possibile utilizzare tutte le funzionalità di rete virtuale, ad esempio subnet, i criteri di controllo di accesso e altre funzionalità per limitare ulteriormente l'accesso a Redis. Per altre informazioni, vedere [Come configurare il supporto di una rete virtuale per una Cache Redis di Azure Premium](cache-how-to-premium-vnet.md).
-	Passare a **Diagnostica** per specificare l'account di archiviazione per le metriche della cache. Per altre informazioni su come configurare e visualizzare le metriche della cache, vedere [Come monitorare Cache Redis di Azure](cache-how-to-monitor.md).

Dopo aver configurato le opzioni della nuova cache, fare clic su **Crea**. La creazione della cache può richiedere alcuni minuti. Per verificare lo stato, è possibile monitorare l'avanzamento nella Schermata iniziale. Lo stato della cache dopo la creazione sarà **In esecuzione** e sarà possibile usarla subito con le impostazioni predefinite.

![Cache created][CacheCreated]

Dopo aver creato la cache, è possibile accedervi dal pannello **Sfoglia**.

![Browse blade][BrowseCaches]

Fare clic su **Cache Redis** per visualizzare le cache.

![Caches][Caches]

<a name="NuGet"></a>
## Configurare i client della cache

Una cache creata con Cache Redis di Azure è accessibile da qualsiasi applicazione Azure. Le applicazioni .NET sviluppate in Visual Studio possono utilizzare il client di cache **StackExchange.Redis** , che può essere configurato con un pacchetto NuGet che semplifica la configurazione delle applicazioni client della cache.

>[AZURE.NOTE]Per ulteriori informazioni, vedere la pagina di Github [StackExchange.Redis][] e la documentazione del client della cache [StackExchange.Redis][].

Per configurare un'applicazione client in Visual Studio con il pacchetto NuGet StackExchange.Redis, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**.

![Manage NuGet packages][NuGetMenu]

Digitare **StackExchange.Redis** o **StackExchange.Redis.StrongName** nella casella di testo di **ricerca online**, selezionare la versione desiderata nei risultati e fare clic su **Installa**.

>[AZURE.NOTE]Se si preferisce usare una versione con nome sicuro della libreria client **StackExchange.Redis**, scegliere **StackExchange.Redis.StrongName**; in caso contrario, scegliere **StackExchange.Redis**.

![StackExchange.Redis NuGet package][StackExchangeNuget]

Il pacchetto NuGet scarica e aggiunge i riferimenti ad assembly necessari per consentire all'applicazione client di accedere a Cache Redis di Azure con il client della cache StackExchange.Redis.

Dopo avere configurato il progetto client per la memorizzazione nella cache, è possibile usare le tecniche descritte nelle sezioni seguenti per usare la cache.

<a name="working-with-caches"></a>
## Uso delle cache

Nelle procedure riportate in questa sezione viene descritto come eseguire attività comuni con il Servizio cache.

-	[Connettersi alla cache][]
-   [Aggiungere e recuperare oggetti dalla cache][]
-   [Gestire gli oggetti .NET nella cache](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>
## Connettersi alla cache

Per usare una cache a livello di codice, è necessario un riferimento alla cache. Aggiungere l'elemento seguente nella parte superiore di ogni file da cui usare il client StackExchange.Redis per accedere a un'istanza di Cache Redis di Azure.

    using StackExchange.Redis;

>[AZURE.NOTE]Con il client StackExchange.Redis è richiesto .NET Framework 4 o versione successiva.

La connessione a Cache Redis di Azure è gestita dalla classe `ConnectionMultiplexer`. Questa classe è concepita in modo da essere condivisa e riusata nell'applicazione client e non deve essere creata per ogni singola operazione.

Per connettersi a Cache Redis di Azure e ricevere un'istanza di un elemento connesso `ConnectionMultiplexer`, chiamare il metodo statico `Connect` e passare la chiave e l’endpoint della cache come nell’esempio seguente. Usare la chiave Azure generata dal portale di anteprima del servizio come parametro password.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

>[AZURE.IMPORTANT]Avviso: non archiviare mai le credenziali nel codice sorgente. Per semplicità però in questo esempio saranno visibili nel codice sorgente. Per informazioni su come archiviare le credenziali, vedere l’articolo su [come funzionano le stringhe applicazione e le stringhe di connessione][].

Se non si vuole usare SSL, impostare `ssl=false` oppure omettere il parametro `ssl`.

>[AZURE.NOTE]Per le nuove cache la porta senza SSL è disabilitata per impostazione predefinita. Per istruzioni su come abilitare la porta non SSL, vedere [Porte di accesso](cache-configure.md#access-ports).

Un approccio per la condivisione di un'istanza di `ConnectionMultiplexer` nell'applicazione prevede una proprietà statica che restituisce un'istanza connessa, simile a quanto illustrato nell'esempio seguente. Questo costituisce un modo thread-safe per inizializzare solo una singola istanza di `ConnectionMultiplexer` connessa. In questi esempi la proprietà `abortConnect` è impostata su false, a indicare che la chiamata riuscirà anche se non viene stabilita una connessione a Cache Redis di Azure. Una delle funzionalità principali di `ConnectionMultiplexer` è il ripristino automatico della connettività alla cache non appena l'errore di rete o eventuali altri problemi vengono risolti.

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

Per altre informazioni sulle opzioni di configurazione avanzate per la connessione, vedere [Modello di configurazione di StackExchange.Redis][].

Le chiavi e l'endpoint della cache sono disponibili nel pannello **Cache Redis** dell’istanza della cache.

![Cache properties][CacheProperties]

![Manage keys][ManageKeys]

Dopo aver stabilito la connessione, restituire un riferimento al database di Cache Redis chiamando il metodo `ConnectionMultiplexer.GetDatabase`. L’oggetto restituito dal metodo `GetDatabase` è un oggetto pass-through leggero che non è necessario archiviare.

	// Connection refers to a property that returns a ConnectionMultiplexer
	// as shown in the previous example.
	IDatabase cache = Connection.GetDatabase();

	// Perform cache operations using the cache object...
	// Simple put of integral data types into the cache
	cache.StringSet("key1", "value");
	cache.StringSet("key2", 25);

	// Simple get of data types from the cache
	string key1 = cache.StringGet("key1");
	int key2 = (int)cache.StringGet("key2");

A questo punto, dopo aver imparato a connettersi a un'istanza di Cache Redis di Azure e a restituire un riferimento al database della cache, verrà esaminato il funzionamento della cache.

<a name="add-object"></a>
## Aggiungere e recuperare oggetti dalla cache

Per archiviare e recuperare gli elementi nella cache, è possibile usare i metodi `StringSet` e `StringGet`.

	// If key1 exists, it is overwritten.
	cache.StringSet("key1", "value1");

	string value = cache.StringGet("key1");

Redis archivia la maggior parte dei dati come stringhe Redis, tuttavia queste stringhe possono contenere molti tipi di dati, inclusi dati binari serializzati, utilizzabili durante la memorizzazione di oggetti .NET nella cache.

Quando si chiama `StringGet`, viene restituito l'oggetto, se esistente; in caso contrario, viene restituito `null`. In questo caso è possibile recuperare il valore dall'origine dati desiderata e memorizzarlo nella cache per usarlo in seguito. Questa operazione è nota come modello cache-aside.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

Per specificare la scadenza di un elemento nella cache, usare il parametro `TimeSpan` di `StringSet`.

	cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## Gestire gli oggetti .NET nella cache

Cache Redis di Azure è in grado di memorizzare nella cache sia oggetti .NET che tipi di dati primitivi, ma prima della memorizzazione nella cache un oggetto .NET deve essere serializzato. Questa operazione spetta allo sviluppatore dell'applicazione, che può scegliere liberamente il serializzatore da usare.

Un modo semplice per serializzare gli oggetti prevede l'uso dei metodi di serializzazione `JsonConvert` in [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1) ed eseguire la serializzazione a e da JSON. L'esempio seguente mostra le operazioni Get e Set usando un'istanza dell'oggetto `Employee`.


	[Serializable]
	class Employee
	{
	    public int Id { get; set; }
	    public string Name { get; set; }
	
	    public Employee(int EmployeeId, string Name)
	    {
	        this.Id = EmployeeId;
	        this.Name = Name;
	    }
	}

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>
## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base, visitare i collegamenti seguenti per altre informazioni sulla Cache Redis di Azure.

-	Vedere i provider ASP.NET per la Cache Redis di Azure.
	-	[Provider di stato della sessione Redis di Azure](cache-asp.net-session-state-provider.md)
	-	[Provider di cache di output ASP.NET della Cache Redis di Azure](cache-asp.net-output-cache-provider.md)
-	[Abilitare la diagnostica della cache](cache-how-to-monitor.md#enable-cache-diagnostics) per poter [monitorare](cache-how-to-monitor.md) l'integrità della cache. È possibile visualizzare le metriche nel portale di anteprima, nonché [scaricarle e analizzarle](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) usando gli strumenti preferiti.
-	Vedere la [documentazione del client della cache StackExchange.Redis][].
	-	È possibile accedere a Cache Redis di Azure da numerosi linguaggi di sviluppo e client Redis. Per ulteriori informazioni, vedere [http://redis.io/clients][] and [Sviluppo in altri linguaggi per Cache Redis di Azure][].
	-	È anche possibile usare Cache Redis di Azure con altri servizi come Redsmin. Per ulteriori informazioni, vedere l’articolo su [come recuperare una stringa di connessione di Redis di Azure e usarla con Redsmin][].
-	Vedere la documentazione su [Redis][] e in particolare l'articolo sui [tipi di dati Redis][] e l'[introduzione di 15 minuti ai tipi di dati Redis][].



<!-- INTRA-TOPIC LINKS -->
[Passaggi successivi]: #next-steps
[Introduction to Azure Redis Cache (Video)]: #video
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[Creare la cache]: #create-cache
[Configure the cache]: #enable-caching
[Configurare i client della cache]: #NuGet
[Working with Caches]: #working-with-caches
[Connettersi alla cache]: #connect-to-cache
[Aggiungere e recuperare oggetti dalla cache]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[Store ASP.NET session state in the cache]: #store-session

  
<!-- IMAGES -->
[NewCacheMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-create.png

[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png

[CacheCreated]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-created.png




   
<!-- LINKS -->
[http://redis.io/clients]: http://redis.io/clients
[Sviluppo in altri linguaggi per Cache Redis di Azure]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[come recuperare una stringa di connessione di Redis di Azure e usarla con Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis Session State Provider]: http://go.microsoft.com/fwlink/?LinkId=398249
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configure a cache in Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[Modello di configurazione di StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Work with .NET objects in the cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Dettagli prezzi del servizio Cache]: http://www.windowsazure.com/pricing/details/cache/
[portale di anteprima di Azure]: https://portal.azure.com/

[Overview of Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Uso di Gruppi di risorse per gestire le risorse di Azure]: http://azure.microsoft.com/documentation/articles/resource-group-overview/

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation
[documentazione del client della cache StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[tipi di dati Redis]: http://redis.io/topics/data-types
[introduzione di 15 minuti ai tipi di dati Redis]: http://redis.io/topics/data-types-intro

[come funzionano le stringhe applicazione e le stringhe di connessione]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/

[versione di valutazione gratuita di Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero

<!---HONumber=Nov15_HO1-->