<properties
   pageTitle="Comunicazione dei servizi con l'API Web ASP.NET | Microsoft Azure"
   description="Informazioni dettagliate su come implementare la comunicazione dei servizi mediante l'API Web ASP.NET con self-hosting OWIN nell'API di Reliable Services."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="03/25/2016"
   ms.author="vturecek"/>

# Introduzione ai servizi API Web di Service Fabric con self-hosting OWIN

Azure Service Fabric consente di definire la modalità di comunicazione dei servizi con gli utenti e tra loro. Questa esercitazione è incentrata sull'implementazione della comunicazione dei servizi mediante l'API Web ASP.NET con self-hosting OWIN (Open Web Interface for .NET) nell'API di Reliable Services di Service Fabric. Verrà illustrata approfonditamente l'API di comunicazione collegabile di Reliable Services. Verrà anche fornito un esempio dettagliato con l'API Web per illustrare come configurare un listener di comunicazione personalizzato.


## Introduzione alle API Web in Service Fabric

L'API Web ASP.NET è un diffuso e potente framework per la creazione di API HTTP in .NET Framework. Se non si ha già familiarità con il framework, per informazioni vedere l'[introduzione all'API Web 2 ASP.NET](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).

L'API Web in Service Fabric è la stessa API Web ASP.NET già nota e apprezzata dagli utenti. La differenza consiste nella modalità di *hosting* di un'applicazione API Web. Non si userà Microsoft Internet Information Services (IIS). Per comprendere meglio la differenza, l'argomento verrà suddiviso in due parti:

 1. L'applicazione API Web (inclusi i controller e i modelli)
 2. L'host (il server Web, in genere IIS)

L'applicazione API Web non cambia. Non è diversa dalle applicazioni API Web scritte in precedenza, pertanto dovrebbe essere possibile spostare semplicemente la maggior parte del codice dell'applicazione. Se si è abituati all'hosting in IIS, l'hosting dell'applicazione potrebbe tuttavia risultare leggermente diverso. Prima di passare all'hosting, verrà illustrata la parte più familiare: l'applicazione API Web.


## Creazione dell'applicazione

Iniziare creando una nuova applicazione di Service Fabric, con un singolo servizio senza stato in Visual Studio 2015:

![Creare una nuova applicazione di Service Fabric](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

È disponibile un modello di Visual Studio per un servizio senza stato tramite l'API Web. In questa esercitazione verrà compilato un progetto che genera un risultato simile a ciò che si otterrebbe selezionando tale modello. A questo punto è possibile iniziare con l'API Web del servizio senza stato e proseguire oppure iniziare con un servizio senza stato vuoto e partire da zero.

![Creare un singolo servizio senza stato](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

Il primo passaggio consiste nell'eseguire il pull di alcuni pacchetti NuGet per l'API Web. Il pacchetto da usare è Microsoft.AspNet.WebApi.OwinSelfHost. Questo pacchetto include tutti i pacchetti di API Web necessari e i pacchetti *host*, che saranno importanti in un secondo momento.

![Creazione dell'API Web tramite Gestione pacchetti NuGet](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

Dopo aver installato i pacchetti, iniziare a compilare la struttura di base del progetto API Web. Se è già stata usata un'API Web, la struttura del progetto avrà un aspetto familiare. Iniziare aggiungendo una directory `Controllers` e un semplice controller di valori:

**ValuesController.cs**

```csharp
using System.Collections.Generic;
using System.Web.Http;
    
namespace WebService.Controllers
{
    public class ValuesController : ApiController
    {
        // GET api/values 
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5 
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values 
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5 
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5 
        public void Delete(int id)
        {
        }
    }
}

```

Aggiungere quindi una classe Startup alla radice del progetto per registrare il routing, i formattatori e le altre impostazioni di configurazione. In questo punto l'API Web si collega all'*host*, come verrà illustrato più avanti.

**Startup.cs**

```csharp
using System.Web.Http;
using Owin;

namespace WebService
{
    public static class Startup
    {
        public static void ConfigureApp(IAppBuilder appBuilder)
        {
            // Configure Web API for self-host. 
            HttpConfiguration config = new HttpConfiguration();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );

            appBuilder.UseWebApi(config);
        }
    }
}
```

La parte relativa all'applicazione è terminata. È stato quindi impostato il layout del progetto API Web di base. Questo progetto sarà simile ai progetti API Web scritti in precedenza o al modello API Web di base. Come al solito, la logica di business si basa sui controller e sui modelli.

Verrà ora illustrato come configurare l'hosting per eseguire il servizio.

## Hosting del servizio

In Service Fabric il servizio viene eseguito in un *processo host del servizio*, un file eseguibile che esegue il codice del servizio. Quando si scrive un servizio usando l'API di Reliable Services, il progetto di servizio viene compilato solo in un file eseguibile che registra il tipo di servizio ed esegue il codice. Questo vale nella maggior parte dei casi in cui si scrive un servizio in Service Fabric in .NET. Se si apre Program.cs nel progetto di servizio senza stato, verrà visualizzato quanto segue:

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric.Services.Runtime;

internal static class Program
{
    private static void Main()
    {
        try
        {
            ServiceRuntime.RegisterServiceAsync("WebServiceType",
                context => new WebService(context)).GetAwaiter().GetResult();

            ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(WebService).Name);

            // Prevents this host process from terminating so services keeps running. 
            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

L'aspetto potrebbe apparire simile al punto di ingresso a un'applicazione console:

I dettagli relativi al processo host del servizio e alla registrazione del servizio non rientrano nell'ambito di questo articolo. È comunque importante sapere che *il codice del servizio è in esecuzione nel relativo processo*.

## Ospitare in modo autonomo l'API Web con self-hosting OWIN

Il codice dell'applicazione API Web è ospitato nel relativo processo. Verrà ora illustrato come associarlo a un server Web. Immettere [OWIN](http://owin.org/). OWIN è semplicemente un contratto tra le applicazioni Web .NET e i server Web. Quando viene usato ASP.NET, fino a MVC 5, l'applicazione Web in genere è strettamente associata a IIS tramite System.Web. L'API Web implementa tuttavia OWIN, che consente di scrivere un'applicazione Web separata dal server Web che la ospita. Per questo motivo è possibile usare un server Web *con self-hosting* OWIN e avviarlo nel processo. Questo server si adatta perfettamente al modello di hosting di Service Fabric descritto in precedenza.

In questo articolo verrà usato Katana come host OWIN per l'applicazione API Web. Katana è un'implementazione host OWIN open source.

> [AZURE.NOTE] Per altre informazioni su Katana, visitare il [sito di Katana](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana). Per una rapida panoramica sull'uso di Katana per il self-hosting dell'API Web, vedere l'articolo relativo all'[uso di OWIN per il self-hosting dell'API Web ASP.NET 2](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api).


## Configurare il server Web

L'API di Reliable Services offre un punto di ingresso di comunicazione in cui è possibile collegare gli stack di comunicazione che consentono agli utenti e ai client di connettersi al servizio:

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}

```

Il server Web e gli eventuali stack di comunicazione da usare in futuro, ad esempio WebSocket, devono usare l'interfaccia ICommunicationListener per la corretta integrazione con il sistema. I motivi verranno illustrati nei passaggi seguenti.

Creare innanzitutto una classe denominata OwinCommunicationListener che implementa l'interfaccia ICommunicationListener:

**OwinCommunicationListener.cs**

```csharp
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;
using System;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;

namespace WebService
{
    public class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}
```

L'interfaccia ICommunicationListener fornisce tre metodi per gestire un listener di comunicazione per il servizio:

 - *OpenAsync*. Avviare l'ascolto delle richieste.
 - *CloseAsync*. Interrompere l'ascolto delle richieste, completare tutte le richieste in elaborazione ed eseguire l'arresto normalmente.
 - *Abort*. Cancellare tutto ed eseguire l'arresto immediatamente.

Per iniziare, aggiungere membri di classe privata per gli elementi per cui il listener dovrà funzionare. Questi elementi verranno inizializzati tramite il costruttore e verranno usati in seguito durante la configurazione dell'URL di ascolto.

```csharp
public class OwinCommunicationListener : ICommunicationListener
{
    private readonly ServiceEventSource eventSource;
    private readonly Action<IAppBuilder> startup;
    private readonly ServiceContext serviceContext;
    private readonly string endpointName;
    private readonly string appRoot;

    private IDisposable webApp;
    private string publishAddress;
    private string listeningAddress;

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
        : this(startup, serviceContext, eventSource, endpointName, null)
    {
    }

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
    {
        if (startup == null)
        {
            throw new ArgumentNullException(nameof(startup));
        }

        if (serviceContext == null)
        {
            throw new ArgumentNullException(nameof(serviceContext));
        }

        if (endpointName == null)
        {
            throw new ArgumentNullException(nameof(endpointName));
        }

        if (eventSource == null)
        {
            throw new ArgumentNullException(nameof(eventSource));
        }

        this.startup = startup;
        this.serviceContext = serviceContext;
        this.endpointName = endpointName;
        this.eventSource = eventSource;
        this.appRoot = appRoot;
    }
   

    ...

```

## Implementare OpenAsync

Per configurare il server Web, sono necessarie due informazioni:

 - *Un prefisso del percorso URL*. Sebbene sia facoltativo, è consigliabile configurarlo ora per poter ospitare in modo sicuro più servizi Web nell'applicazione.
 - *Una porta*.

Prima di ottenere una porta per il server Web, è importante comprendere che Service Fabric offre un livello di applicazione che funge da buffer tra l'applicazione e il sistema operativo sottostante in cui è in esecuzione. Service Fabric consente pertanto di configurare *endpoint* per i servizi. Service Fabric assicura che gli endpoint siano disponibili per l'uso da parte del servizio. In questo modo non è necessario configurarli manualmente nell'ambiente del sistema operativo sottostante. È possibile ospitare facilmente l'applicazione di Service Fabric in ambienti diversi senza dover apportare modifiche all'applicazione. È possibile ad esempio ospitare la stessa applicazione in Azure o nel proprio data center.

Configurare un endpoint HTTP in PackageRoot\\ServiceManifest.xml:

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="8281" />
    </Endpoints>
</Resources>

```

Questo passaggio è importante perché il processo host del servizio viene eseguito con credenziali con restrizioni (servizio di rete in Windows). Il servizio non avrà quindi l'autorizzazione necessaria per configurare un endpoint HTTP in modo autonomo. Usando la configurazione dell'endpoint, Service Fabric imposta l'elenco di controllo di accesso (ACL) appropriato per l'URL su cui il servizio rimarrà in ascolto. Service Fabric offre anche una posizione standard per la configurazione degli endpoint.


Una volta tornati in OwinCommunicationListener.cs, iniziare a implementare OpenAsync. Da qui si avvia il server Web. Innanzitutto, ottenere le informazioni sull'endpoint e creare l'URL su cui il servizio rimarrà in ascolto. L'URL sarà diverso a seconda che il listener venga usato in un servizio senza stato o in un servizio con stato. Per un servizio con stato, il listener deve creare un indirizzo univoco per ogni replica del servizio con stato di cui è in ascolto. Per un servizio senza stato, l'indirizzo può essere molto più semplice.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
    var protocol = serviceEndpoint.Protocol;
    int port = serviceEndpoint.Port;

    if (this.serviceContext is StatefulServiceContext)
    {
        StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}{3}/{4}/{5}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/',
            statefulServiceContext.PartitionId,
            statefulServiceContext.ReplicaId,
            Guid.NewGuid());
    }
    else if (this.serviceContext is StatelessServiceContext)
    {
        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/');
    }
    else
    {
        throw new InvalidOperationException();
    }
    
    ...

```

Si noti che viene usato "http://+" per assicurarsi che il server Web sia in ascolto su tutti gli indirizzi disponibili, tra cui il localhost, il nome di dominio completo e l'IP del computer.

L'implementazione di OpenAsync è uno dei motivi principali per i quali il server Web (o qualsiasi stack di comunicazione) viene implementato come interfaccia ICommunicationListener anziché venire aperto direttamente da `RunAsync()` nel servizio. Il valore restituito da OpenAsync è l'indirizzo su cui è in ascolto il server Web. Quando questo indirizzo viene restituito al sistema, registra l'indirizzo con il servizio. L'API di Service Fabric consente ai client e ad altri servizi di richiedere questo indirizzo in base al nome del servizio. Questo aspetto è importante perché l'indirizzo del servizio non è statico. I servizi vengono spostati nel cluster ai fini del bilanciamento del carico e della disponibilità delle risorse. Questo meccanismo consente ai client di risolvere l'indirizzo di ascolto per un servizio.

Tenendo presente questo aspetto, OpenAsync avvia il server Web e restituisce l'indirizzo su cui è in ascolto. Si noti che è in ascolto su "http://+", ma prima che OpenAsync restituisca l'indirizzo, il "+" viene sostituito con l'IP o con il nome di dominio completo del nodo corrente. L'indirizzo restituito da questo metodo è quello registrato con il sistema. È anche l'indirizzo visualizzato dai client e dagli altri servizi quando richiedono l'indirizzo di un servizio. Per una corretta connessione dei client, è necessario un IP o un nome di dominio completo effettivo nell'indirizzo.

```csharp
    ...

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    try
    {
        this.eventSource.ServiceMessage(this.serviceContext, "Starting web server on " + this.listeningAddress);

        this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

        this.eventSource.ServiceMessage(this.serviceContext, "Listening on " + this.publishAddress);

        return Task.FromResult(this.publishAddress);
    }
    catch (Exception ex)
    {
        this.eventSource.ServiceMessage(this.serviceContext, "Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

        this.StopWebServer();

        throw;
    }
}

```

Si noti che fa riferimento alla classe Startup passata all'oggetto OwinCommunicationListener nel costruttore. Questa istanza di Startup viene usata dal server Web per avviare l'applicazione API Web.

Durante l'esecuzione dell'applicazione verrà visualizzata la riga `ServiceEventSource.Current.ServiceMessage()` nella finestra degli eventi di diagnostica per confermare che il server Web è stato avviato correttamente.

## Implementare CloseAsync e Abort

Implementare infine CloseAsync e Abort per arrestare il server Web. Il server Web può essere arrestato eliminando l'handle del server creato durante OpenAsync.

```csharp
public Task CloseAsync(CancellationToken cancellationToken)
{
    this.eventSource.ServiceMessage(this.serviceContext, "Closing web server on endpoint {0}", this.endpointName);
            
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.eventSource.ServiceMessage(this.serviceContext, "Aborting web server on endpoint {0}", this.endpointName);
    
    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.serverHandle != null)
    {
        try
        {
            this.serverHandle.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}
```

In questa implementazione di esempio CloseAsync e Abort consentono di arrestare semplicemente il server Web. È possibile scegliere di eseguire normalmente un arresto coordinato del server Web in CloseAsync. L'arresto potrebbe ad esempio attendere il completamento delle richieste in elaborazione prima di restituire il valore.

## Avviare il server Web

È ora possibile creare e restituire un'istanza di OwinCommunicationListener per avviare il server Web. Nella classe del servizio Service.cs eseguire l'override del metodo `CreateServiceInstanceListeners()`:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                           .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                           .Select(endpoint => endpoint.Name);

    return endpoints.Select(endpoint => new ServiceInstanceListener(
        serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
}
```

In questo punto si riuniscono l'*applicazione* API Web e l'*host* OWIN. All'host OwinCommunicationListener viene assegnata un'istanza dell'*applicazione*, ovvero l'API Web tramite l'avvio. Service Fabric ne gestisce quindi il ciclo di vita. Questo modello può essere seguito con qualsiasi stack di comunicazione.

## Combinare tutti gli elementi

In questo esempio non è necessario eseguire alcuna operazione nel metodo `RunAsync()`, quindi l'override può essere semplicemente rimosso.

L'implementazione del servizio finale sarà molto semplice. È necessario solo creare il listener di comunicazione:

```csharp
using System;
using System.Collections.Generic;
using System.Fabric;
using System.Fabric.Description;
using System.Linq;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace WebService
{
    internal sealed class WebService : StatelessService
    {
        public WebService(StatelessServiceContext context)
            : base(context)
        { }

        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                                   .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                                   .Select(endpoint => endpoint.Name);

            return endpoints.Select(endpoint => new ServiceInstanceListener(
                serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
        }
    }
}
```

La classe `OwinCommunicationListener` completa:

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
    private readonly ServiceEventSource eventSource;
    private readonly Action<IAppBuilder> startup;
    private readonly ServiceContext serviceContext;
    private readonly string endpointName;
    private readonly string appRoot;

    private IDisposable webApp;
    private string publishAddress;
    private string listeningAddress;

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
        : this(startup, serviceContext, eventSource, endpointName, null)
    {
    }

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
    {
        if (startup == null)
        {
            throw new ArgumentNullException(nameof(startup));
        }

        if (serviceContext == null)
        {
            throw new ArgumentNullException(nameof(serviceContext));
        }

        if (endpointName == null)
        {
            throw new ArgumentNullException(nameof(endpointName));
        }

        if (eventSource == null)
        {
            throw new ArgumentNullException(nameof(eventSource));
        }

        this.startup = startup;
        this.serviceContext = serviceContext;
        this.endpointName = endpointName;
        this.eventSource = eventSource;
        this.appRoot = appRoot;
    }

        public bool ListenOnSecondary { get; set; }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
            var protocol = serviceEndpoint.Protocol;
            int port = serviceEndpoint.Port;

            if (this.serviceContext is StatefulServiceContext)
            {
                StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}{3}/{4}/{5}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/',
                    statefulServiceContext.PartitionId,
                    statefulServiceContext.ReplicaId,
                    Guid.NewGuid());
            }
            else if (this.serviceContext is StatelessServiceContext)
            {
                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/');
            }
            else
            {
                throw new InvalidOperationException();
            }

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    try
    {
        this.eventSource.ServiceMessage(this.serviceContext, "Starting web server on " + this.listeningAddress);

        this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

        this.eventSource.ServiceMessage(this.serviceContext, "Listening on " + this.publishAddress);

        return Task.FromResult(this.publishAddress);
    }
    catch (Exception ex)
    {
        this.eventSource.ServiceMessage(this.serviceContext, "Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

        this.StopWebServer();

        throw;
    }
}

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            this.eventSource.ServiceMessage(this.serviceContext, "Closing web server on endpoint {0}", this.endpointName);

            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            this.eventSource.ServiceMessage(this.serviceContext, "Aborting web server on endpoint {0}", this.endpointName);

            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.webApp != null)
            {
                try
                {
                    this.webApp.Dispose();
                }
                catch (ObjectDisposedException)
                {
                    // no-op
                }
            }
        }
    }
}

```

Dopo che tutti gli elementi necessari sono pronti, il progetto sarà simile a una tipica applicazione API Web con i punti di ingresso dell'API di Reliable Services e un host OWIN:


![API Web con punti di ingresso dell'API di Reliable Services e host OWIN](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## Esecuzione e connessione tramite un Web browser

Se non lo si è già fatto, [configurare l'ambiente di sviluppo](service-fabric-get-started.md).


È ora possibile compilare e distribuire il servizio. Premere **F5** in Visual Studio per compilare l'applicazione. Nella finestra degli eventi di diagnostica verrà visualizzato un messaggio che indica che il server Web è aperto in http://localhost:8281/.


![Finestra degli eventi di diagnostica di Visual Studio](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE] Se la porta è già stata aperta da un altro processo nel computer, è possibile che venga visualizzato un messaggio di errore. Questo messaggio di errore indica che il listener non può essere aperto. In questo caso provare a usare una porta diversa nella configurazione dell'endpoint in ServiceManifest.xml.


Quando il servizio è in esecuzione, aprire un browser e passare a [http://localhost:8281/api/values](http://localhost:8281/api/values) per testarlo.

## Scalabilità orizzontale

Scalare orizzontalmente app Web senza stato in genere significa aggiungere più macchine e attivare in esse le applicazioni Web. Il motore di orchestrazione di Service Fabric è in grado di farlo automaticamente ogni volta che vengono aggiunti nuovi nodi a un cluster. Durante la creazione di istanze di un servizio senza stato, specificare il numero di istanze che si vuole creare. Service Fabric inserisce il numero di istanze specificato nei nodi del cluster e si assicura che non venga creata più di un'istanza in ciascun nodo. È anche possibile impostare Service Fabric in modo che crei un'istanza in ogni nodo specificando **-1** per il numero di istanze. In questo modo si garantisce che ogni volta che si aggiungono nodi per scalare orizzontalmente il cluster, nei nuovi nodi verrà creata un'istanza del servizio senza stato. Questo valore è una proprietà dell'istanza del servizio, pertanto viene impostato quando si crea un'istanza del servizio. Per farlo, è possibile usare PowerShell:

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

Questa operazione può essere effettuata anche quando si definisce un servizio predefinito in un progetto di servizio senza stato di Visual Studio:

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

Per altre informazioni sulla creazione di istanze di applicazioni e di servizi, vedere [Distribuire un'applicazione](service-fabric-deploy-remove-applications.md).

## Passaggi successivi

[Debug dell'applicazione di Service Fabric mediante Visual Studio](service-fabric-debugging-your-application.md)

<!---HONumber=AcomDC_0511_2016-->