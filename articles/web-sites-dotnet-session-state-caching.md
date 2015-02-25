<properties 
	pageTitle="Usare lo stato sessione ASP.NET con Siti Web di Azure" 
	description="Informazioni su come usare Servizio cache di Azure per supportare la memorizzazione nella cache dello stato della sessione ASP.NET." 
	services="cache" 
	documentationCenter=".net" 
	authors="riande" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="1/13/2015" 
	ms.author="Rick-Anderson"/>


# Come usare lo stato sessione ASP.NET con Siti Web di Azure


In questo argomento vengono fornite informazioni su come usare il servizio Cache Redis di Azure (anteprima) per lo stato della sessione.

Se l'app Web ASP.NET usa lo stato della sessione, sarà necessario usare un provider di stato della sessione esterno (il provider del servizio Cache Redis o un provider di stato della sessione SQL Server). Se si usa lo stato della sessione, e non un provider esterno, verrà applicato il limite di una sola istanza dell'app Web. Il servizio Cache Redis può essere abilitato con la massima rapidità e semplicità.

La procedura di base per l'uso del Servizio cache (Anteprima) per la memorizzazione nella cache dello stato sessione include i seguenti passaggi:

* [Creare la cache.](#createcache)
* [Aggiungere il pacchetto NuGet RedisSessionStateProvall'app Web.](#configureproject)
* [Modificare il file web.config.](#configurewebconfig)
* [Usare l'oggetto Session per archiviare e recuperare oggetti memorizzati nella cache.](#usesessionobject)

<h2><a id="createcache"></a>Creare la cache</h2>
Per creare la cache, [attenersi a queste istruzioni](http://azure.microsoft.com/it-it/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#create-cache).

<h2><a id="configureproject"></a>Aggiungere il pacchetto NuGet RedisSessionStateProvider all'app Web</h2>
Installare il pacchetto NuGet `RedisSessionStateProvider`.  Usare il seguente comando per effettuare l'installazione dalla console di Gestione pacchetti (**Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**):

  `PM> Install-Package RedisSessionStateProvider -IncludePrerelease`
  
Per effettuare l'installazione da **Strumenti** > **Gestione pacchetti NuGet** > **Gestisci pacchetti NugGet per la soluzione**, cercare  `RedisSessionStateProvider` e assicurarsi di specificare **Includi versione preliminare**.

Per altre informazioni, vedere la pagina relativa a [RedisSessionStateProvider di NuGet](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ ) e [Configurare i client della cache](http://azure.microsoft.com/it-it/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#NuGet).

<h2><a id="configurewebconfig"></a>Modificare il file Web.Config</h2>
Oltre a creare riferimenti ad assembly per la cache, il pacchetto NuGet aggiunge voci stub nel file  *web.config*. 

1. Aprire il file  *web.config* e trovare l'elemento **sessionState**.

1. Immettere i valori relativi a  `host`, `accessKey`, `port` (la porta SSL deve essere 6380) e impostare  `SSL` su  `true`. Questi valori possono essere ottenuti dal pannello del portale di anteprima di gestione di Azure per l'istanza della cache. Per altre informazioni, vedere [Connettersi alla cache](http://azure.microsoft.com/it-it/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#connect-to-cache).
Il seguente markup mostra le modifiche del file  *web.config*.


  <pre class="prettyprint">  
    &lt;system.web&gt;
    &lt;customErrors mode="Off" /&gt;
    &lt;authentication mode="None" /&gt;
    &lt;compilation debug="true" targetFramework="4.5" /&gt;
    &lt;httpRuntime targetFramework="4.5" /&gt;
  &lt;sessionState mode="Custom" customProvider="RedisSessionProvider"&gt;
      &lt;providers&gt;  
          &lt;!--&lt;add name="RedisSessionProvider" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
          /&gt;--&gt;
         &lt;add name="RedisSessionProvider" 
              type="Microsoft.Web.Redis.RedisSessionStateProvider" 
              <mark>port="6380"
              host="movie2.redis.cache.windows.net" 
              accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
              ssl="true"</mark> /&gt;
      &lt;!--&lt;add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" /&gt;--&gt;
      &lt;/providers&gt;
    &lt;/sessionState&gt;
  &lt;/system.web&gt;</pre>


<h2><a id="usesessionobject"></a>Usare l'oggetto Session nel codice</h2>
Il passaggio finale consiste nell'iniziare a usare l'oggetto Session nel proprio codice ASP.NET. Per aggiungere oggetti allo stato sessione usare il metodo **Session.Add**, che usa coppie chiave-valore per memorizzare elementi nella cache di stato sessione.

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

Il codice seguente recupera questo valore dallo stato sessione.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;	

È inoltre possibile usare Cache Redis per nascondere gli oggetti nell'app Web. Per altre informazioni, vedere il blog relativo all'[applicazione per filmati MVC con Cache Redis di Azure in 15 minuti](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/).
Per altre informazioni su come usare lo stato sessione ASP.NET, vedere [Cenni preliminari sullo stato della sessione ASP.NET][].

  *Autore [Rick Anderson](https://twitter.com/RickAndMSFT)*
  
  [installed the latest]: http://www.windowsazure.com/it-it/downloads/?sdk=net  
  [ASP.NET Session State Overview]: http://msdn.microsoft.com/it-it/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png


<!--HONumber=42-->
