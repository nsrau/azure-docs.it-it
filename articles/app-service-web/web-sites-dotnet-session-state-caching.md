<properties 
	pageTitle="Stato della sessione con Cache Redis di Azure in Servizio app di Azure" 
	description="Informazioni su come usare Servizio cache di Azure per supportare la memorizzazione nella cache dello stato della sessione ASP.NET." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="Rick-Anderson" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="get-started-article" 
	ms.date="02/27/2016" 
	ms.author="riande"/>


# Stato della sessione con Cache Redis di Azure in Servizio app di Azure


questo argomento illustra come usare il servizio Cache Redis di Azure per lo stato sessione.

Se l'app Web ASP.NET usa lo stato della sessione, sarà necessario usare un provider di stato della sessione esterno (il provider del servizio Cache Redis o un provider di stato della sessione SQL Server). Se si usa lo stato della sessione, e non un provider esterno, verrà applicato il limite di una sola istanza dell'app Web. Il servizio Cache Redis può essere abilitato con la massima rapidità e semplicità.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##<a id="createcache"></a>Creare la Cache
Per creare la cache, attenersi a [queste istruzioni](../cache-dotnet-how-to-use-azure-redis-cache.md#create-cache).

##<a id="configureproject"></a>Aggiungere il pacchetto NuGet RedisSessionStateProvider all'app Web
Installare il pacchetto NuGet `RedisSessionStateProvider`. Usare il comando seguente per effettuare l'installazione dalla Console di Gestione pacchetti (**Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**):

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`
  
Per effettuare l'installazione da **Strumenti** > **Gestione pacchetti NuGet** > **Gestisci pacchetti NugGet per la soluzione**, cercare `RedisSessionStateProvider`.

Per altre informazioni, vedere la pagina relativa a [RedisSessionStateProvider di NuGet](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/) e la pagina [Configurare il client cache](../cache-dotnet-how-to-use-azure-redis-cache.md#NuGet).

##<a id="configurewebconfig"></a>Modificare il file Web.Config
Oltre a creare riferimenti ad assembly per la cache, il pacchetto NuGet aggiunge voci stub nel file *web.config*.

1. Aprire *web.config* e trovare l'elemento **sessionState**.

1. Immettere i valori relativi a `host`, `accessKey`, `port` (la porta SSL deve essere 6380) e impostare `SSL` su `true` Questi valori possono essere ottenuti dal pannello del [Portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715) per l'istanza della cache. Per altre informazioni, vedere [Connettersi alla cache](../cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache). Si tenga presente che per le nuove cache la porta senza SSL è disabilitata per impostazione predefinita. Per altre informazioni sull'abilitazione della porta senza SSL, vedere la sezione relativa alle [porte di accesso](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) nell'argomento [Configurare una cache in Cache Redis di Azure](https://msdn.microsoft.com/library/azure/dn793612.aspx). Il markup seguente mostra le modifiche del file *web.config*, in particolare le modifiche apportate a*porta*, *host*, accessKey * e *ssl*.

		  <system.web>;
		    <customErrors mode="Off" />;
		    <authentication mode="None" />;
		    <compilation debug="true" targetFramework="4.5" />;
		    <httpRuntime targetFramework="4.5" />;
		    <sessionState mode="Custom" customProvider="RedisSessionProvider">;
		      <providers>;  
		          <!--<add name="RedisSessionProvider" 
		            host = "127.0.0.1" [String]
		            port = "" [number]
		            accessKey = "" [String]
		            ssl = "false" [true|false]
		            throwOnError = "true" [true|false]
		            retryTimeoutInMilliseconds = "0" [number]
		            databaseId = "0" [number]
		            applicationName = "" [String]
		          />;-->;
		         <add name="RedisSessionProvider" 
		              type="Microsoft.Web.Redis.RedisSessionStateProvider" 
		              port="6380"
		              host="movie2.redis.cache.windows.net" 
		              accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
		              ssl="true" />;
		      <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />;-->;
		      </providers>;
		    </sessionState>;
		  </system.web>;


##<a id="usesessionobject"></a> Utilizzo dell'oggetto Session nel codice
Il passaggio finale consiste nell'iniziare a utilizzare l'oggetto Session nel proprio codice ASP.NET. Aggiungere oggetti allo stato sessione utilizzando il metodo **Session.Add** che utilizza coppie chiave-valore per memorizzare elementi nella cache di stato sessione.

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

Il codice seguente recupera questo valore dallo stato sessione.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue;	

È inoltre possibile usare Cache Redis per nascondere gli oggetti nell'app Web. Per altre informazioni, vedere il blog relativo all' applicazione per filmati [MVC con Cache Redis di Azure in 15 minuti](https://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/). Per ulteriori informazioni su come utilizzare lo stato sessione ASP.NET, vedere [Cenni preliminari sullo stato della sessione ASP.NET][].

>[AZURE.NOTE] Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)

  *Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)*
  
  [installed the latest]: http://www.windowsazure.com/downloads/?sdk=net
  [Cenni preliminari sullo stato della sessione ASP.NET]: http://msdn.microsoft.com/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png
 

<!---HONumber=AcomDC_0302_2016-->