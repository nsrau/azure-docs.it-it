<properties 
	pageTitle="Come usare Cache Redis di Azure" 
	description="Informazioni su come migliorare le prestazioni delle applicazioni Azure con Cache Redis di Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="sdanie"/>

# Come usare Cache Redis di Azure

In questa guida viene illustrato come usare **Cache Redis di Azure**. Negli esempi, scritti in codice C#, viene utilizzato il client [StackExchange.Redis][]. Gli scenari presentati includono **creazione e configurazione di una cache**, **configurazione di client della cache**, **aggiunta e rimozione di oggetti dalla cache** e **archiviazione dello stato della sessione ASP.NET nella cache**. Per ulteriori informazioni sull’uso di Cache Redis di Azure, fare riferimento alla sezione [Passaggi successivi][].

<a name="what-is"></a>
## Informazioni su Cache Redis di Azure

Cache Redis di Microsoft Azure si basa sulla popolare cache Redis open source e consente di accedere a una cache Redis sicura e dedicata gestita da Microsoft. Una cache creata usando Cache Redis di Azure sarà accessibile da qualsiasi applicazione all'interno di Microsoft Azure.

Cache Redis di Microsoft Azure è disponibile in due livelli:

-	**Basic**: singolo nodo, Più dimensioni fino a 53 GB.
-	**Standard**: principale/replica a due nodi. Più dimensioni fino a 53 GB. Contratti di servizio del 99,9%.

Ogni livello presenta differenze in termini di funzionalità e prezzi. Le funzionalità vengono illustrate più avanti in questa guida. Per altre informazioni sui prezzi, vedere [Dettagli prezzi del servizio Cache][].

In questa guida vengono fornite informazioni generali su Cache Redis di Azure. Per informazioni dettagliate su queste funzionalità che non rientrano nell'ambito di questa guida introduttiva, vedere la [panoramica di Cache Redis di Azure][].

<a name="getting-started-cache-service"></a>
## Introduzione all'uso di Cache Redis di Azure

Iniziare a usare Cache Redis di Azure è semplice. Innanzitutto, è necessario eseguire il provisioning e la configurazione di una cache. Successivamente, verranno configurati i client della cache affinché possano accedere alla cache. Dopo avere configurato i client della cache, è possibile iniziare a usarli.

-	[Creare la cache][]
-	[Configurare i client della cache][]

<a name="create-cache"></a>
## Creare una cache

Per creare una cache, accedere prima al [portale di anteprima di Microsoft Azure][] e fare clic su **Nuovo**, **Dati + Archiviazione**, **Cache Redis**.

![New cache][NewCacheMenu]

>[AZURE.NOTE]Se non si dispone di un account Azure, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][].

Nel pannello **Nuova cache Redis** specificare la configurazione desiderata per la cache.

![Create cache][CacheCreate]

In **Nome DNS** immettere il nome di un sottodominio da usare per l'endpoint della cache. L'endpoint deve essere una stringa con un numero di caratteri compreso tra sei e 20, deve contenere solo numeri e lettere minuscole e deve iniziare con una lettera.

Usare **Livello di prezzo** per selezionare le dimensioni e le funzionalità desiderate per la cache. Le cache **Basic** dispongono di un singolo nodo con più dimensioni fino a 53 GB. Le cache **Standard** dispongono di una configurazione di replica o primaria a due nodi con un contratto di servizio pari al 99,9% e più dimensioni fino a 53 GB.

In **Gruppo di risorse** selezionare o creare un gruppo di risorse per la cache.

>[AZURE.NOTE]Per altre informazioni, vedere [Uso di Gruppi di risorse per gestire le risorse di Azure][].

Per **Sottoscrizione** selezionare la sottoscrizione di Azure da usare per la cache. Se l'account dispone di una sola sottoscrizione, questa verrà selezionata automaticamente e l'elenco a discesa **Sottoscrizione** non verrà visualizzato.

Usare **Posizione** per specificare la posizione geografica in cui viene ospitata la cache. Per prestazioni ottimali, è consigliabile creare la cache nella stessa area dell'applicazione client della cache.

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
-   [Archiviare lo stato della sessione ASP.NET nella cache][]

<a name="connect-to-cache"></a>
## Connettersi alla cache

Per usare una cache a livello di codice, è necessario un riferimento alla cache. Aggiungere l'elemento seguente nella parte superiore di ogni file da cui usare il client StackExchange.Redis per accedere a un'istanza di Cache Redis di Azure:

    using StackExchange.Redis;

>[AZURE.NOTE]Con il client StackExchange.Redis è richiesto .NET Framework 4 o versione successiva.

La connessione a Cache Redis di Azure è gestita dalla classe `ConnectionMultiplexer`. Questa classe è concepita in modo da essere condivisa e riusata nell'applicazione client e non deve essere creata per ogni singola operazione.

Per connettersi a Cache Redis di Azure e ricevere un'istanza di un elemento connesso `ConnectionMultiplexer`, chiamare il metodo statico `Connect` e passare la chiave e l’endpoint della cache come nell’esempio seguente. Usare la chiave Azure generata dal portale del servizio come parametro password.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

>[AZURE.IMPORTANT]Avviso: non archiviare mai le credenziali nel codice sorgente. Per semplicità però in questo esempio saranno visibili nel codice sorgente. Per informazioni su come archiviare le credenziali, vedere l’articolo su [come funzionano le stringhe applicazione e le stringhe di connessione][].

Se non si desidera usare SSL, impostare `ssl=false` o passare semplicemente l’endpoint e la chiave.

>[AZURE.NOTE]Per le nuove cache la porta senza SSL è disabilitata per impostazione predefinita. Per istruzioni sull’abilitazione della porta senza SSL, vedere l’argomento [Configurare una cache in Cache Redis di Azure][].

	connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,password=...");

Per altre informazioni sulle opzioni di configurazione avanzate per la connessione, vedere [Modello di configurazione di StackExchange.Redis][].

Le chiavi e l'endpoint della cache sono disponibili nel pannello **Cache Redis** dell’istanza della cache.

![Cache properties][CacheProperties]

![Manage keys][ManageKeys]

Dopo aver stabilito la connessione, restituire un riferimento al database di Cache Redis chiamando il metodo `ConnectionMultiplexer.GetDatabase`.

	// connection referes to a previously configured ConnectionMultiplexer
	IDatabase cache = connection.GetDatabase();

>[AZURE.NOTE]L’oggetto restituito dal metodo `GetDatabase` è un oggetto pass-through leggero che non è necessario archiviare.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

	IDatabase cache = connection.GetDatabase();

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

>[AZURE.NOTE]Redis archivia la maggior parte dei dati come stringhe Redis, tuttavia queste stringhe possono contenere molti tipi di dati, inclusi dati binari serializzati, utilizzabili durante la memorizzazione di oggetti .NET nella cache.

Quando si chiama `StringGet`, viene restituito l'oggetto, se esistente; in caso contrario, viene restituito Null. In questo caso è possibile recuperare il valore dall'origine dati desiderata e memorizzarlo nella cache per usarlo in seguito. Questa operazione è nota come modello cache-aside.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

>[AZURE.NOTE]Cache Redis di Azure è in grado di memorizzare nella cache sia oggetti .NET che tipi di dati primitivi, ma prima della memorizzazione nella cache un oggetto .NET deve essere serializzato. Questa operazione spetta allo sviluppatore dell'applicazione, che può scegliere liberamente il serializzatore da usare. Per altre informazioni, vedere [Utilizzo degli oggetti .NET nella cache][].

<a name="specify-expiration"></a>
## Specificare la scadenza di un elemento nella cache

Per specificare la scadenza di un elemento nella cache, usare il parametro `TimeSpan` di `StringSet`.

	cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));


<a name="store-session"></a>
## Archiviare lo stato della sessione ASP.NET nella cache

Cache Redis di Azure include un provider di stato della sessione che consente di archiviare lo stato della sessione in una cache anziché in memoria o in un database di SQL Server. Per usare il provider di stato della sessione di memorizzazione nella cache, configurare innanzitutto la cache e quindi l'applicazione ASP.NET per la cache usando il pacchetto NuGet dello stato della sessione per Cache Redis.

Per configurare un'applicazione client in Visual Studio con il pacchetto NuGet dello stato della sessione per Cache Redis, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**.

![Manage NuGet packages][NuGetMenu]

Digitare **RedisSessionStateProvider** nella casella di testo della **ricerca online**, selezionarlo nei risultati e fare clic su **Installa**.

![Redis Cache Session State NuGet Package][SessionStateNuGet]

Il pacchetto NuGet scarica e aggiunge i riferimenti all'assembly necessari e aggiunge la seguente sezione nel file web.config che contiene la configurazione richiesta per consentire all'applicazione ASP.NET di usare il provider di stato della sessione per Cache Redis.

    <sessionState mode="Custom" customProvider="MySessionStateStore">
      <providers>
        <!--
          <add name="MySessionStateStore" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />
      </providers>
    </sessionState>

La sezione commentata fornisce un esempio degli attributi e delle impostazioni di esempio.

Configurare gli attributi con i valori del pannello Cache nel portale e configurare gli altri valori come desiderato.

	<sessionState mode="Custom" customProvider="MySessionStateStore">
      <providers>
        <!--
          <add name="MySessionStateStore" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="contoso5.redis.cache.windows.net" 
		accessKey="..." ssl="true" />
      </providers>
    </sessionState>

Assicurarsi di impostare come commento il provider di stato della sessione **InProc** standard.

    <!-- <sessionState mode="InProc" customProvider="DefaultSessionProvider">
      <providers>
        <add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
      </providers>
    </sessionState> -->

Per altre informazioni sulla configurazione di queste impostazioni e sull'uso del provider di stato della sessione per Cache Redis di Azure, vedere [Provider di stato della sessione ASP.NET per Cache Redis di Azure][].

<a name="next-steps"></a>
## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base di Cache Redis di Azure, usare i collegamenti seguenti per altre informazioni su come eseguire attività più complesse di memorizzazione nella cache.

-	[Abilitare la diagnostica della cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) per poter [monitorare](https://msdn.microsoft.com/library/azure/dn763945.aspx) l'integrità della cache. È possibile visualizzare le metriche nel portale, nonché [scaricarle e analizzarle](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) usando gli strumenti preferiti.
-	Vedere la [documentazione del client della cache StackExchange.Redis][].
	-	È possibile accedere a Cache Redis di Azure da numerosi linguaggi di sviluppo e client Redis. Per ulteriori informazioni, vedere [http://redis.io/clients][] and [Sviluppo in altri linguaggi per Cache Redis di Azure][].
	-	È anche possibile usare Cache Redis di Azure con altri servizi come Redsmin. Per ulteriori informazioni, vedere l’articolo su [come recuperare una stringa di connessione di Redis di Azure e usarla con Redsmin][].
-	Vedere la documentazione su [Redis][] e in particolare l'articolo sui [tipi di dati Redis][] e l'[introduzione di 15 minuti ai tipi di dati Redis][].
-   Vedere la documentazione MSDN per [Cache Redis di Azure][]. 


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
[Archiviare lo stato della sessione ASP.NET nella cache]: #store-session

  
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
[Provider di stato della sessione ASP.NET per Cache Redis di Azure]: http://go.microsoft.com/fwlink/?LinkId=398249
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
[Configurare una cache in Cache Redis di Azure]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[Modello di configurazione di StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Utilizzo degli oggetti .NET nella cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Dettagli prezzi del servizio Cache]: http://www.windowsazure.com/pricing/details/cache/
[portale di anteprima di Microsoft Azure]: https://portal.azure.com/

[panoramica di Cache Redis di Azure]: http://go.microsoft.com/fwlink/?LinkId=320830
[Cache Redis di Azure]: http://go.microsoft.com/fwlink/?LinkId=398247

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

<!---HONumber=July15_HO1-->