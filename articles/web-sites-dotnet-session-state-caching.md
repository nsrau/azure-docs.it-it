<properties linkid="video-center-index" urlDisplayName="index" pageTitle="Use ASP.NET session state with Azure Websites" metaKeywords="azure cache service session state" description="Learn how to use the Azure Cache Service to support ASP.NET session state caching." metaCanonical="" services="cache" documentationCenter=".NET" title="How to Use ASP.NET Session State with Azure Websites" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra" />

# Come usare lo stato sessione ASP.NET con Siti Web di Azure

*Autore: [Rick Anderson][Rick Anderson]. Ultimo aggiornamento: 1 luglio 2014.*

In questo argomento vengono fornite informazioni su come usare il servizio Cache Redis di Azure (anteprima) per lo stato della sessione.

Se l'app Web ASP.NET usa lo stato della sessione, sarà necessario usare un provider di stato della sessione esterno (il provider del servizio Cache Redis o un provider di stato della sessione SQL Server). Se si usa lo stato della sessione, e non un provider esterno, verrà applicato il limite di una sola istanza dell'app Web. Il servizio Cache Redis può essere abilitato con la massima rapidità e semplicità.

La procedura di base per l'uso del Servizio cache (Anteprima) per la memorizzazione nella cache dello stato sessione include i seguenti passaggi:

-   [Creazione della cache][Creazione della cache]
-   [Aggiungere il pacchetto NuGet package RedisSessionStateProvider all'app Web.][Aggiungere il pacchetto NuGet package RedisSessionStateProvider all'app Web.]
-   [Modifica del file web.config][Modifica del file web.config]
-   [Utilizzo dell'oggetto Session per memorizzare e recuperare oggetti memorizzati nella cache][Utilizzo dell'oggetto Session per memorizzare e recuperare oggetti memorizzati nella cache]

## <span id="createcache"></span></a>Creazione della cache

Per creare la cache, attenersi a [queste istruzioni][queste istruzioni].

## <span id="configureproject"></span></a>Aggiungere il pacchetto NuGet package RedisSessionStateProvider all'app Web

Installare il pacchetto NuGet `RedisSessionStateProvider`. Usare il comando seguente per effettuare l'installazione dalla Console di Gestione pacchetti (**Strumenti** \> **Gestione pacchetti NuGet** \> **Console di Gestione pacchetti**):

`PM> Install-Package RedisSessionStateProvider -IncludePrerelease`

Per effettuare l'installazione da **Strumenti** \> **Gestione pacchetti NuGet** \> **Gestisci pacchetti NugGet per la soluzione**, cercare `RedisSessionStateProvider` e assicurarsi di specificare **Includi versione preliminare**.

Per altre informazioni, vedere la pagina relativa a [RedisSessionStateProvider di NuGet][RedisSessionStateProvider di NuGet] e la pagina [Configurare il client cache][Configurare il client cache].

## <span id="configurewebconfig"></span></a>Modifica del file web.config

Oltre a creare riferimenti ad assembly per la cache, il pacchetto NuGet aggiunge voci stub nel file *web.config*.

1.  Aprire *web.config* e trovare l'elemento **sessionState**.

2.  Immettere i valori relativi a `host`, `accessKey`, `port` (la porta SSL deve essere 6380) e impostare `SSL` su `true`. Questi valori possono essere ottenuti dal pannello del portale di anteprima di gestione di Azure per l'istanza della cache. Per altre informazioni, vedere l'articolo relativo alla [connessione alla cache][connessione alla cache].
    Il markup seguente mostra le modifiche apportate al file *web.config*.

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

</p>
## <span id="usesessionobject"></span></a>Utilizzo dell'oggetto Session nel codice

Il passaggio finale consiste nell'iniziare a usare l'oggetto Session nel proprio codice ASP.NET. Aggiungere oggetti allo stato sessione usando il metodo **Session.Add** che utilizza coppie chiave-valore per memorizzare elementi nella cache di stato sessione.

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

Il codice seguente recupera questo valore dallo stato sessione.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;  

È inoltre possibile usare Cache Redis per nascondere gli oggetti nell'app Web. Per altre informazioni, vedere il blog relativo all'[applicazione per filmati MVC con Cache Redis di Azure in 15 minuti][applicazione per filmati MVC con Cache Redis di Azure in 15 minuti].
Per altre informazioni su come usare lo stato della sessione ASP.NET, vedere [Cenni preliminari sullo stato della sessione ASP.NET][Cenni preliminari sullo stato della sessione ASP.NET].

  [Rick Anderson]: https://twitter.com/RickAndMSFT
  [Creazione della cache]: #createcache
  [Aggiungere il pacchetto NuGet package RedisSessionStateProvider all'app Web.]: #configureproject
  [Modifica del file web.config]: #configurewebconfig
  [Utilizzo dell'oggetto Session per memorizzare e recuperare oggetti memorizzati nella cache]: #usesessionobject
  [queste istruzioni]: http://azure.microsoft.com/it-it/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#create-cache
  [RedisSessionStateProvider di NuGet]: http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/
  [Configurare il client cache]: http://azure.microsoft.com/it-it/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#NuGet
  [connessione alla cache]: http://azure.microsoft.com/it-it/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#connect-to-cache
  [applicazione per filmati MVC con Cache Redis di Azure in 15 minuti]: http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
  [Cenni preliminari sullo stato della sessione ASP.NET]: http://msdn.microsoft.com/it-it/library/ms178581.aspx
