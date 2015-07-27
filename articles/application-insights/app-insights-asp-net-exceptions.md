<properties 
	pageTitle="Diagnosticare errori ed eccezioni nelle app ASP.NET con Application Insights" 
	description="Configurare l'app per ottenere un'esperienza coinvolgente dal punto di vista della diagnostica acquisendo le eccezioni insieme agli eventi di telemetria per la richiesta." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/11/2015" 
	ms.author="awills"/>
 
# Diagnosticare errori ed eccezioni nelle app ASP.NET con Application Insights  

Monitorando l'applicazione con [Visual Studio Application Insights][start] è possibile correlare le richieste non riuscite con le eccezioni e altri eventi nel client e nel server, in modo da poter diagnosticare rapidamente le cause.

Per monitorare un'app ASP.NET, è necessario [aggiungere Application Insights SDK][greenbrown] all'applicazione o [installare Status Monitor nel server IIS][redfield] oppure, se l'app è un'app Web di Azure, aggiungere l'[estensione Application Insights][azure].

## Diagnosi degli errori 

Il riquadro Errori nel pannello Panoramica mostra i grafici delle eccezioni e delle richieste HTTP non riuscite insieme a un elenco di URL delle richieste che causano gli errori più frequenti.

![Selezionare gli errori](./media/app-insights-asp-net-exceptions/012-start.png)

Fare clic su uno dei tipi di richieste non riuscite nell'elenco per visualizzare le singole occorrenze dell'errore. Fare quindi clic sulle eccezioni o sui dati di traccia associati alle eccezioni:

![Selezionare un'istanza di una richiesta non riuscita e nei dettagli dell'eccezione visualizzare le istanze dell'eccezione.](./media/app-insights-asp-net-exceptions/030-req-drill.png)


**In alternativa**, è possibile iniziare dall'elenco di eccezioni visualizzato più in basso nel pannello Errori. Continuare a fare clic finché non verranno visualizzate le singole eccezioni.


![Eseguire il drill-through](./media/app-insights-asp-net-exceptions/040-exception-drill.png)

*Se non vengono visualizzate eccezioni, vedere la sezione relativa all'[acquisizione delle eccezioni](#exceptions).*

Sarà quindi possibile esaminare l'analisi dello stack e le proprietà dettagliate di ogni eccezione e trovare la traccia del log correlata o altri eventi.


![Eseguire il drill-through](./media/app-insights-asp-net-exceptions/050-exception-properties.png)

[Altre informazioni sulla ricerca diagnostica][diagnostic].

## Errori di dipendenze

Una *dipendenza* è un servizio che l'applicazione chiama in genere tramite un'API REST o una connessione di database. [Application Insights Status Monitor][redfield] monitora automaticamente un'ampia gamma di tipi di chiamata alle dipendenze, misurando la durata della chiamata e l'esito, positivo o negativo.

Per ottenere i dati delle dipendenze, è necessario [installare Status Monitor][redfield] nel server IIS o, se l'app è un'app Web di Azure, usare l'[estensione Application Insights][azure]. È possibile eseguire entrambe le operazioni.

Le chiamate non riuscite alle dipendenze sono elencate nel pannello Errori ma sono anche disponibili nella sezione Elementi correlati nei dettagli delle richieste e nei dettagli delle eccezioni.

*Se non vengono visualizzati errori di dipendenze, è una cosa positiva. Tuttavia, per verificare la ricezione dei dati sulle dipendenze, aprire il pannello Prestazioni ed esaminare il grafico Durata della dipendenza.*

 

## Dati di traccia e di log personalizzati

Per ottenere dati di diagnostica specifici per l'app, è possibile inserire codice per l'invio di dati di telemetria personalizzati. Questi dati vengono visualizzati nella ricerca diagnostica insieme alla richiesta, alla visualizzazione di pagina e ad altri dati raccolti automaticamente.

Sono disponibili diverse opzioni:

* [TrackEvent()](app-insights-api-custom-events-metrics.md#track-event) viene usato in genere per il monitoraggio dei modelli di utilizzo, ma i dati inviati vengono visualizzati anche in Eventi personalizzati nella ricerca diagnostica. Gli eventi sono denominati e possono includere proprietà di stringa e metriche numeriche, in base alle quali è possibile [filtrare le ricerche diagnostiche][diagnostic].
* [TrackTrace()](app-insights-api-custom-events-metrics.md#track-trace) permette di inviare dati più lunghi, ad esempio informazioni POST.
* [TrackException()](#exceptions) invia analisi dello stack. [Altre informazioni sulle eccezioni](#exceptions).
* Se si usa già un framework di registrazione come Log4Net o NLog, è possibile [acquisire tali log][netlogs] e visualizzarli nella ricerca diagnostica insieme ai dati relativi alle richieste e alle eccezioni.

Per visualizzare questi eventi, aprire [Cerca][diagnostic], quindi Filtro e infine scegliere Evento personalizzato, Traccia o Eccezione.


![Eseguire il drill-through](./media/app-insights-asp-net-exceptions/viewCustomEvents.png)

### Come visualizzare i dati POST di una richiesta

I dettagli della richiesta non includono i dati inviati all'app in una chiamata POST. Per poter ottenere questi dati:

* [Installare l'SDK][greenbrown] nel progetto dell'applicazione.
* Inserire il codice nell'applicazione per chiamare [Microsoft.ApplicationInsights.TrackTrace()][api]. Inviare i dati POST nel parametro del messaggio. Esiste un limite per le dimensioni consentite, per cui è consigliabile provare a inviare solo i dati essenziali.
* Quando si esamina una richiesta non riuscita, trovare le tracce associate.  

![Eseguire il drill-through](./media/app-insights-asp-net-exceptions/060-req-related.png)


## <a name="exceptions"></a> Acquisizione delle eccezioni e dei relativi dati di diagnostica

Inizialmente, nel portale non verranno visualizzate tutte le eccezioni che causano errori nell'app. Verranno visualizzate tutte le eccezioni del browser (se si usa [JavaScript SDK][client] nelle pagine Web). La maggior parte delle eccezioni del server vengono rilevate da IIS, ma è necessario scrivere qualche riga di codice per visualizzarle.

È possibile:

* **Registrare le eccezioni in modo esplicito** inserendo il codice nei gestori di eccezioni per segnalare le eccezioni.
* **Acquisire automaticamente le eccezioni** configurando il framework di ASP.NET. Gli elementi da aggiungere variano a seconda dei diversi tipi di framework.

## Segnalazione di eccezioni in modo esplicito

Il modo più semplice consiste nell'inserire una chiamata a TrackException() in un gestore di eccezioni.

JavaScript

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C#

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)
  
      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

I parametri delle proprietà e delle misurazioni sono facoltativi, ma sono utili per [filtrare e aggiungere][diagnostic] altre informazioni. Ad esempio, per un'app in grado di eseguire diversi giochi, è possibile trovare tutti i report di eccezione correlati a un gioco specifico. È possibile aggiungere qualsiasi numero di elementi a ogni dizionario.

## Eccezioni del browser

Viene segnalata la maggior parte delle eccezioni del browser.

Se la pagina Web include file di script provenienti da reti per la distribuzione di contenuti o da altri domini, verificare che il tag dello script contenga l'attributo ```crossorigin="anonymous"``` e che il server invii [intestazioni CORS](http://enable-cors.org/). Ciò consentirà di ottenere un'analisi dello stack e i dettagli per le eccezioni JavaScript non gestite da queste risorse.

## Web Form

Per Web Form, il modulo HTTP potrà raccogliere le eccezioni quando non sono configurati reindirizzamenti con CustomErrors.

Se però sono presenti reindirizzamenti attivi, aggiungere le righe seguenti alla funzione Application_Error in Global.asax.cs. Aggiungere un file Global.asax se non ne esiste già uno.

*C#*

    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError  () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }


## MVC

Se la configurazione di [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) è `Off`, le eccezioni potranno essere raccolte dal [modulo HTTP](https://msdn.microsoft.com/library/ms178468.aspx). Se tuttavia è `RemoteOnly` (impostazione predefinita) o `On`, l'eccezione verrà cancellata e non potrà essere raccolta automaticamente da Application Insights. È possibile risolvere questo problema eseguendo l'override della [classe System.Web.Mvc.HandleErrorAttribute](http://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx) e applicando la classe di cui è stato eseguito l'override come illustrato per le diverse versioni MVC riportate di seguito ([origine github](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)] 
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above  
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                } 
            }
            base.OnException(filterContext);
        }
      }
    }

#### MVC 2

Sostituire l'attributo HandleError con il nuovo attributo nei controller.

    namespace MVC2App.Controllers
    {
       [AiHandleError]
       public class HomeController : Controller
       {
    ...

[Esempio](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### MVC 3

Registrare `AiHandleErrorAttribute` come filtro globale in Global.asax.cs:

    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...

[Esempio](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)



#### MVC 4, MVC5

Registrare AiHandleErrorAttribute come filtro globale in FilterConfig.cs:

    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }

[Esempio](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## API Web 1.x


Eseguire l'override di System.Web.Http.Filters.ExceptionFilterAttribute:

    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above 
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);    
            }
            base.OnException(actionExecutedContext);
        }
      }
    }

Sarà possibile aggiungere questo attributo di cui è stato eseguito l'override ai controller specifici o alla configurazione del filtro globale nella classe WebApiConfig:

    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }

[Esempio](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

Ci sono alcuni casi in cui non è possibile gestire i filtri eccezioni. Ad esempio:

* Eccezioni generate dai costruttori dei controller. 
* Eccezioni generate dai gestori di messaggi. 
* Eccezioni generate durante il routing. 
* Eccezioni generate durante la serializzazione del contenuto della risposta. 

## API Web 2.x

Aggiungere un'implementazione di IExceptionLogger:

    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above 
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }

Aggiungere il codice seguente ai servizi in WebApiConfig:

    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger()); 
        }
      }
  }

[Esempio](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

In alternativa, è possibile:

2. Sostituire ExceptionHandler con un'implementazione personalizzata di IExceptionHandler. Questo elemento viene chiamato solo quando il framework può ancora scegliere il messaggio di risposta da inviare (non quando la connessione viene interrotta) 
3. Filtri eccezioni (come descritto nella sezione relativa ai controller Web API 1.x precedente) - non vengono chiamati in tutti i casi.


## WCF

Aggiungere una classe che estenda Attribute e implementi IErrorHandler e IServiceBehavior.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription, 
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription, 
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above 
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error, 
            System.ServiceModel.Channels.MessageVersion version, 
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

Aggiungere l'attributo alle implementazioni del servizio:

    namespace WcfService4
    {
        [ServiceContract]
        [AiLogException]
        public interface IService1
        {
     ...

[Esempio](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[netlogs]: app-insights-asp-net-trace-logs.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md

 

<!---HONumber=July15_HO3-->