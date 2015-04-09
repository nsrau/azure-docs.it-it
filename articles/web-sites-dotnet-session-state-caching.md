<properties 
	pageTitle="Stato della sessione con Cache Redis di Azure in Servizio app di Azure" 
	description="Informazioni su come usare Servizio cache di Azure per supportare la memorizzazione nella cache dello stato della sessione ASP.NET." 
	services="app-service\web" 
	documentationCenter=".net" 
 	authors="Rick-Anderson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="riande"/>


# Stato della sessione con Cache Redis di Azure in Servizio app di Azure


In questo argomento vengono fornite informazioni su come usare il servizio Cache Redis di Azure per lo stato della sessione.

Se l'app Web ASP.NET usa lo stato della sessione, sarà necessario usare un provider di stato della sessione esterno (il provider del servizio Cache Redis o un provider di stato della sessione SQL Server). Se si usa lo stato della sessione, e non un provider esterno, verrà applicato il limite di una sola istanza dell'app Web. Il servizio Cache Redis può essere abilitato con la massima rapidità e semplicità.

<h2><a id="createcache"></a>Creazione della cache</h2>
Seguire le [istruzioni fornite](cache-dotnet-how-to-use-azure-redis-cache.md#create-cache) per creare la cache.

<h2><a id="configureproject"></a>Aggiungere il pacchetto NuGet RedisSessionStateProvider all'app Web</h2>
Installare il pacchetto NuGet  `RedisSessionStateProvider`.  Usare il comando seguente per effettuare l'installazione dalla Console di Gestione pacchetti (**Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**):

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`
  
Per effettuare l'installazione da **Strumenti** > **Gestione pacchetti NuGet** > **Gestisci pacchetti NugGet per la soluzione**, cercare  `RedisSessionStateProvider`.

Per altre informazioni, vedere la [pagina NuGet RedisSessionStateProvider](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ ) e [Configurare il client della cache](cache-dotnet-how-to-use-azure-redis-cache.md#NuGet).

<h2><a id="configurewebconfig"></a>Modifica del file Web.Config</h2>
Oltre a creare riferimenti ad assembly per la cache, il pacchetto NuGet aggiunge voci stub nel file  *web.config*. 

1. Aprire  *web.config* e trovare l'elemento **sessionState**.

1. Immettere i valori relativi a  `host`, `accessKey`, `port` (la porta SSL deve essere 6380) e impostare  `SSL` su  `true`. Questi valori possono essere ottenuti dal pannello del [portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715) per l'istanza della cache. Per altre informazioni, vedere [Connettersi alla cache](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache). Si tenga presente che per le nuove cache la porta senza SSL è disabilitata per impostazione predefinita. Per altre informazioni sull'abilitazione della porta senza SSL, vedere la sezione relativa alle [porte di accesso](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) nell'argomento [Configurare una cache in Cache Redis di Azure](https://msdn.microsoft.com/library/azure/dn793612.aspx). Il markup seguente mostra le modifiche al file  *web.config*.


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


<h2><a id="usesessionobject"></a>Utilizzo dell'oggetto Session nel codice</h2>
Il passaggio finale consiste nell'iniziare a usare l'oggetto Session nel proprio codice ASP.NET. Aggiungere oggetti allo stato sessione usando il metodo **Session.Add**. Tale metodo usa coppie chiave-valore per memorizzare elementi nella cache di stato sessione.

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

The following code retrieves this value from session state.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue;	

È inoltre possibile usare Cache Redis per nascondere gli oggetti nell'app Web. Per altre informazioni, vedere il blog relativo all' [applicazione per filmati MVC con Cache Redis di Azure in 15 minuti](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/).
Per altre informazioni su come utilizzare lo stato sessione ASP.NET, vedere[Cenni preliminari sullo stato della sessione ASP.NET][].

>[AZURE.NOTE] Per iniziare a usare il servizio app di Azure prima di registrare un account di Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751) dove è possibile creare immediatamente un'app Web di base e temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere: [Il servizio app di Azure e il suo impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida relativa al passaggio dal vecchio al nuovo portale, vedere: [Riferimenti per esplorare il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

  *Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)*
  
  [installata la versione più recente]: http://www.windowsazure.com/downloads/?sdk=net  
  [Cenni preliminari sullo stato della sessione ASP.NET]: http://msdn.microsoft.com/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png

<!--HONumber=49-->