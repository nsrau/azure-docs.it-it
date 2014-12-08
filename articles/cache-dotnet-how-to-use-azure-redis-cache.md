<properties pageTitle="Come usare Cache Redis di Azure" metaKeywords="" description="Learn how to create a use a cache in Azure Redis Cache" metaCanonical="" services="cache" documentationCenter="API Management" title="How to Use Azure Redis Cache" authors="sdanie" solutions="" manager="dwrede" editor="" />

<tags ms.service="cache" ms.workload="tbd" ms.tgt_pltfrm="cache-redis" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="sdanie" />

# Come usare Cache Redis di Azure

In questa guida viene illustrato come iniziare a usare 
**Cache Redis di Azure**. Gli esempi sono scritti in codice C\ # e
viene usata l'API .NET.. Gli scenari presentati includono **creazione e configurazione di una cache**, **configurazione di client della cache**, **aggiunta e rimozione di oggetti dalla cache** e **archiviazione dello stato della sessione ASP.NET nella cache**. Per altre
informazioni sull'uso di Cache Redis di Azure, fare riferimento alla sezione [Passaggi successivi][].

## Sommario

-   [Informazioni su Cache Redis di Azure][]
-	[Introduzione all'utilizzo di Cache Redis di Azure][]
	-	[Creare la cache][]
	-	[Configurare i client della cache][]
-	[Utilizzo delle cache][]
	-	[Connettersi alla cache][]
	-   [Aggiungere e recuperare oggetti dalla cache][]
	-   [Specificare la scadenza di un oggetto nella cache][]
	-   [Archiviare lo stato della sessione ASP.NET nella cache][]
-   [Passaggi successivi][]

<a name="what-is"></a>
## Informazioni su Cache Redis di Azure

Cache Redis di Microsoft Azure si basa sulla popolare cache Redis open source e consente di accedere a una cache Redis sicura e dedicata gestita da Microsoft. Una cache creata usando Cache Redis di Azure sarà accessibile da qualsiasi applicazione all'interno di Microsoft Azure.

Cache Redis di Microsoft Azure è disponibile in due livelli:

-	**Basic**: singolo nodo, più dimensioni fino a 53 GB.
-	**Standard**: master/slave a due nodi. Più dimensioni fino a 53 GB. Contratti di servizio del 99,9%.

Ogni livello presenta differenze in termini di funzionalità e prezzi. Le funzionalità vengono illustrate più avanti in questa guida. Per altre informazioni sui prezzi, vedere [Dettagli prezzi del servizio Cache][].

In questa guida viene fornita una panoramica generale di Cache Redis di Azure. Per informazioni dettagliate su queste funzionalità che non rientrano nell'ambito di questa guida introduttiva, vedere la [panoramica di Cache Redis di Azure][].

<a name="getting-started-cache-service"></a>
## Introduzione all'utilizzo di Cache Redis di Azure

Iniziare a usare Cache Redis di Azure è semplice. Innanzitutto, è necessario eseguire il provisioning e la configurazione di una cache. Successivamente, verranno configurati i client della cache affinché possano accedere alla cache. Dopo avere configurato i client della cache, è possibile iniziare a usarli.

-	[Creare la cache][]
-	[Configurare la cache][]
-	[Configurare i client della cache][]

<a name="create-cache"></a>
## Creare una cache

Per creare una cache, accedere prima al portale di anteprima di gestione di Azure e fare clic su **Nuova**, **Cache Redis**.

![New cache][NewCacheMenu]

Nel pannello **Nuova cache Redis** specificare la configurazione desiderata per la cache.

![Create cache][CacheCreate]

In **Nome DNS** immettere il nome di un sottodominio da usare per l'endpoint della cache. L'endpoint deve essere una stringa con un numero di caratteri compreso tra sei e 20, deve contenere solo numeri e lettere minuscole e deve iniziare con una lettera.

Usare **Livello di prezzo** per selezionare le dimensioni e le funzionalità desiderate per la cache. Cache Redis è disponibile nei due livelli seguenti.

-	**Basic**: singolo nodo, più dimensioni fino a 53 GB.
-	**Standard**: due nodi master/slave, contratto di servizio con disponibilità del 99,9%, più dimensioni fino a 53 GB.

Per **Sottoscrizione** selezionare la sottoscrizione di Azure da usare per la cache.

>[AZURE.NOTE] Se l'account dispone di una sola sottoscrizione, questa verrà selezionata automaticamente e l'elenco a discesa per le sottoscrizioni non verrà visualizzato.

In **Gruppo di risorse** selezionare o creare un gruppo di risorse per la cache.

>[AZURE.NOTE] Per altre informazioni, vedere [Uso dei gruppi di risorse per la gestione delle risorse di Azure][]. 

Usare **Georilevazione** per specificare la posizione geografica in cui viene ospitata la cache. Per prestazioni ottimali, è consigliabile creare la cache nella stessa area dell'applicazione client della cache.

Dopo aver configurato le opzioni della nuova cache, fare clic su **Crea**. La creazione della cache può richiedere alcuni minuti. Per controllare lo stato, è possibile monitorare l'avanzamento nella schermata iniziale. Dopo la creazione della cache, il relativo stato è **In esecuzione** ed è possibile usarla con le impostazioni predefinite.

![Cache created][CacheCreated]

Dopo aver creato la cache, è possibile accedervi dal pannello **Sfoglia**.

![Browse blade][BrowseCaches]

Fare clic su **Cache** per visualizzare le proprie cache.

![Caches][Caches]

<a name="NuGet"></a>
## Configurare i client della cache

Una cache creata usando Cache Redis di Azure sarà accessibile da qualsiasi applicazione Azure. Le applicazioni .NET sviluppate in Visual Studio possono usare il client della cache **StackExchange.Redis**, che può essere configurato con un pacchetto NuGet per semplificare la configurazione delle applicazioni client della cache. 

>[AZURE.NOTE] Per altre informazioni, vedere la pagina di github su [StackExchange.Redis][] e la[ documentazione del client della cache StackExchange.Redis][]..

Per configurare un'applicazione client in Visual Studio con il pacchetto NuGet StackExchange.Redis, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**. .

![Manage NuGet packages][NuGetMenu]

Digitare **StackExchange.Redis** o **StackExchange.Redis.StrongName** nella casella di testo **Cerca online**, selezionare la versione desiderata nei risultati e fare clic su **Installa**.

>[AZURE.NOTE] Se si preferisce usare una versione con nome sicuro della libreria client **StackExchange.Redis**, scegliere **StackExchange.Redis.StrongName**; in caso contrario, scegliere **StackExchange.Redis**.

![StackExchange.Redis NuGet package][StackExchangeNuget]

Il pacchetto NuGet scarica e aggiunge i riferimenti ad assembly necessari per consentire all'applicazione client di accedere a Cache Redis di Azure con il client della cache StackExchange.Redis.

Dopo avere configurato il progetto client per la memorizzazione nella cache, è possibile usare le tecniche descritte nelle sezioni seguenti per usare la cache.

<a name="working-with-caches"></a>
## Utilizzo delle cache

Nelle procedure riportate in questa sezione viene descritto come eseguire attività comuni con il Servizio cache.

-	[Connettersi alla cache][]
-   [Aggiungere e recuperare oggetti dalla cache][]
-   [Archiviare lo stato della sessione ASP.NET nella cache][]

<a name="connect-to-cache"></a>
## Connettersi alla cache

Per usare una cache a livello di codice, è necessario un riferimento alla cache. Aggiungere l'elemento seguente nella parte superiore di ogni file da cui usare il client StackExchange.Redis per accedere a un'istanza di Cache Redis di Azure:

    using StackExchange.Redis;

>[AZURE.NOTE] Con il client StackExchange.Redis è richiesto .NET Framework 4 o versione successiva.

La connessione a Cache Redis di Azure è gestita dalla classe `ConnectionMultiplexer`. Questa classe è concepita in modo da essere condivisa e riusata nell'applicazione client e non deve essere creata per ogni singola operazione. 

Per connettersi a Cache Redis di Azure e ricevere un'istanza di un elemento `ConnectionMultiplexer`, connesso, chiamare il metodo statico `Connect` e passare la chiave e l'endpoint della cache come nell'esempio seguente. Usare la chiave Azure generata dal portale del servizio come parametro password.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

>[AZURE.NOTE] Avviso: non archiviare mai le credenziali nel codice sorgente. Per semplicità però in questo esempio saranno visibili nel codice sorgente. Vedere il post di blog sull'[uso di stringhe di applicazione e stringhe di connessione in Siti Web di Microsoft Azure][] per informazioni su come archiviare le credenziali.

Se si preferisce non usare SSL, impostare `ssl=false` o passare semplicemente l'endpoint e la chiave.

	connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,password=...");

>[AZURE.NOTE] Per altre informazioni sulle opzioni di configurazione avanzate per la connessione, vedere [Modello di configurazione di StackExchange.Redis][]..

Le chiavi e l'endpoint della cache sono disponibili nel pannello del portale di anteprima di gestione di Azure per l'istanza della cache.

![Cache properties][CacheProperties]

![Manage keys][ManageKeys]

Dopo aver stabilito la connessione, restituire un riferimento al database di Cache Redis chiamando il metodo `ConnectionMultiplexer.GetDatabase`.

	// connection referes to a previously configured ConnectionMultiplexer
	IDatabase cache = connection.GetDatabase();

>[AZURE.NOTE] L'oggetto restituito dal metodo `GetDatabase` è un oggetto pass-through leggero che non deve essere archiviato.

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

>[AZURE.NOTE] Redis archivia la maggior parte dei dati come stringhe Redis, tuttavia queste stringhe possono contenere molti tipi di dati, inclusi dati binari serializzati, utilizzabili durante la memorizzazione di oggetti .NET nella cache.

Quando si chiama `StringGet`, viene restituito l'oggetto, se esistente; in caso contrario, viene restituito Null. In questo caso è possibile recuperare il valore dall'origine dati desiderata e memorizzarlo nella cache per usarlo in seguito. Questa operazione è nota come modello cache-aside.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

>[AZURE.NOTE] Cache Redis di Azure è in grado di memorizzare nella cache sia oggetti .NET che tipi di dati primitivi. Tuttavia, prima di essere memorizzato nella cache un oggetto .NET deve essere serializzato. Questa operazione spetta allo sviluppatore dell'applicazione, che può scegliere liberamente il serializzatore da usare. Per altre informazioni, vedere [Utilizzo degli oggetti .NET nella cache][].

<a name="specify-expiration"></a>
## Specificare la scadenza di un elemento nella cache

Per specificare la scadenza di un elemento nella cache, usare il parametro `TimeSpan` di `StringSet`.

	cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));


<a name="store-session"></a>
## Archiviare lo stato della sessione ASP.NET nella cache

Cache Redis di Azure include un provider di stato della sessione che consente di archiviare lo stato della sessione in una cache anziché in memoria o in un database di SQL Server. Per usare la memorizzazione nella cache della sessione
di memorizzazione nella cache, configurare innanzitutto la cache e quindi l'applicazione ASP.NET per la cache usando il pacchetto NuGet dello stato della sessione per Cache Redis.

Per configurare un'applicazione client in Visual Studio con il pacchetto NuGet dello stato della sessione per Cache Redis, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**. 

![Manage NuGet packages][NuGetMenu]

Digitare **RedisSessionStateProvider** nella casella di testo **Cerca online**, selezionare il pacchetto dai risultati e fare clic su **Installa**.

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

Configurare gli attributi con i valori del pannello Cache nel portale di anteprima e configurare gli altri valori come desiderato.

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

Per altre informazioni sulla configurazione e l'uso del provider di stato della sessione per Cache Redis di Azure, vedere [Provider di stato della sessione per Cache Redis di Azure][]..

<a name="next-steps"></a>
## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base di Cache Redis di Azure,
seguire i collegamenti seguenti per informazioni su come eseguire attività di memorizzazione nella cache più complesse.

-	Altre informazioni sul client StackExchange.Redis: [documentazione del client della cache StackExchange.Redis][]
-	Vedere la documentazione su [redis][] e in particolare l'articolo sui [tipi di dati Redis][] e [un'introduzione di 15 minuti ai tipi di dati Redis][]..
-   Vedere il riferimento in MSDN: [Cache Redis di Azure][]


<!-- INTRA-TOPIC LINKS -->
[Passaggi successivi]: #next-steps
[Informazioni su Cache Redis di Azure]: #what-is
[Creare una Cache di Azure]: #create-cache
[Qual è il tipo di memorizzazione nella cache appropriato?]: #choosing-cache
[Preparare il progetto di Visual Studio per usare la memorizzazione nella cache di Azure]: #prepare-vs
[Configurare l'applicazione in modo da usare la memorizzazione nella cache]: #configure-app
[Introduzione all'utilizzo di Cache Redis di Azure]: #getting-started-cache-service
[Creare la cache]: #create-cache
[Configurare la cache]: #enable-caching
[Configurare i client della cache]: #NuGet
[Utilizzo delle cache]: #working-with-caches
[Connettersi alla cache]: #connect-to-cache
[Aggiungere e recuperare oggetti dalla cache]: #add-object
[Specificare la scadenza di un oggetto nella cache]: #specify-expiration
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
[Provider di stato della sessione Redis di Azure]: http://go.microsoft.com/fwlink/?LinkId=398249
[Portale di gestione di Azure]: http://windows.azure.com/
[Procedura: Configurare un client della cache a livello di codice]: http://msdn.microsoft.com/it-it/library/windowsazure/gg618003.aspx
[Provider di stato della sessione per Cache di Azure]: http://go.microsoft.com/fwlink/?LinkId=320835
[Cache di Azure AppFabric: Stato della sessione di memorizzazione nella cache]: http://www.microsoft.com/it-it/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Provider di cache di output per Cache di Azure]: http://go.microsoft.com/fwlink/?LinkId=320837
[Memorizzazione nella cache condivisa di Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/gg278356.aspx
[Blog del team]: http://blogs.msdn.com/b/windowsazure/
[Cache di Azure]: http://www.microsoft.com/it-it/showcase/Search.aspx?phrase=azure+caching
[Come configurare le dimensioni delle macchine virtuali]: http://go.microsoft.com/fwlink/?LinkId=164387
[Considerazioni sulla pianificazione della capacità di memorizzazione nella cache di Azure]: http://go.microsoft.com/fwlink/?LinkId=320167
[Cache di Azure]: http://go.microsoft.com/fwlink/?LinkId=252658
[Procedura: Impostare in modo dichiarativo nella cache di una pagina ASP.NET]: http://msdn.microsoft.com/it-it/library/zd1ysf1y.aspx
[Procedura: Impostare la possibilità di inserire una pagina nella cache a livello di codice]: http://msdn.microsoft.com/it-it/library/z852zf6b.aspx

[Modello di configurazione Stackexchange]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Gestire gli oggetti .NET nella cache]: http://msdn.microsoft.com/it-it/library/dn690521.aspx#Objects


[Installazione di gestione pacchetti NuGet]: http://go.microsoft.com/fwlink/?LinkId=240311
[Dettagli prezzi del servizio cache]: http://www.windowsazure.com/it-it/pricing/details/cache/
[Portale di gestione]: https://manage.windowsazure.com/

[Panoramica di Cache Redis di Azure]: http://go.microsoft.com/fwlink/?LinkId=320830
[Cache Redis di Azure]: http://go.microsoft.com/fwlink/?LinkId=398247

[Eseguire la migrazione a Cache Redis di Azure]: http://go.microsoft.com/fwlink/?LinkId=317347
[Esempi di Cache Redis di Azure]: http://go.microsoft.com/fwlink/?LinkId=320840
[Uso dei gruppi di risorse per la gestione delle risorse di Azure]: http://azure.microsoft.com/it-it/documentation/articles/azure-preview-portal-using-resource-groups/

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[documentazione del client della cache StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Tipi di dati di Redis]: http://redis.io/topics/data-types
[un'introduzione di quindici minuti ai tipi di dati Redis]: http://redis.io/topics/data-types-intro

[Uso di stringhe di applicazione e stringhe di connessione in Siti Web di Azure]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/
