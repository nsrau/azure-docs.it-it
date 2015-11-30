<properties
   pageTitle="Comunicazione dei servizi con l'API Web ASP.NET | Microsoft Azure"
   description="Informazioni su come implementare la comunicazione dettagliata dei servizi mediante l'API Web ASP.NET con self-hosting OWIN in Reliable Services."
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
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Introduzione ai servizi API Web di Microsoft Azure Service Fabric con self-hosting OWIN

Service Fabric consente di decidere la modalità di comunicazione dei servizi con gli utenti e tra loro. Questa esercitazione è incentrata sull'implementazione della comunicazione dei servizi mediante l'API Web ASP.NET con self-hosting OWIN nell'API di *Reliable Services* di Service Fabric. Verrà illustrata in dettaglio l'API di comunicazione modulare di *Reliable Services* e verranno fornite istruzioni dettagliate per configurare un listener di comunicazione personalizzato per il servizio con l'API Web come esempio. Per visualizzare un esempio completo di un listener di comunicazione con API Web, vedere la pagina di [esempio di applicazione Web Service Fabric in GitHub](https://github.com/Azure/servicefabric-samples/tree/master/samples/Services/VS2015/WebApplication).


## Introduzione all'API Web in Service Fabric

L'API Web ASP.NET è un diffuso e potente framework per la creazione di API HTTP in .NET Framework. Se non si ha familiarità con l'API Web, per altre informazioni visitare la pagina [www.asp.net/webapi](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).

L'API Web in Service Fabric è la stessa API Web ASP.NET già nota e apprezzata dagli utenti. La differenza consiste nella modalità usata per *ospitare* un'applicazione API Web (suggerimento: non si userà IIS). Per comprendere meglio la differenza, l'argomento verrà suddiviso in due parti:

 1. L'applicazione API Web (i controller, i modelli e così via)
 2. L'host (il server Web, in genere IIS)

L'applicazione API Web non cambia e non è diversa dalle applicazioni API Web scritte in precedenza, pertanto dovrebbe essere possibile spostare semplicemente la maggior parte del codice dell'applicazione. Se si è abituati all'hosting in IIS, l'hosting dell'applicazione potrebbe risultare leggermente diverso. Prima di passare all'hosting, verrà illustrata la parte più familiare: l'applicazione API Web.


## Creare l'applicazione

Iniziare creando una nuova applicazione di infrastruttura di servizi, con un singolo servizio senza stato, in Visual Studio 2015:

![Creare una nuova applicazione di Service Fabric](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

![Creare un singolo servizio senza stato](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

Verrà quindi creato un servizio senza stato vuoto che ospiterà l'applicazione API Web. Sarà necessario configurare l'applicazione da zero per capire come è stata creata.

Il primo passaggio consiste nell'eseguire il pull di alcuni pacchetti NuGet per l'API Web. Il pacchetto da usare è **Microsoft.AspNet.WebApi.OwinSelfHost**. Questo pacchetto include tutti i pacchetti di API Web necessari e i pacchetti *host*, che saranno importanti in un secondo momento.

![](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

Con i pacchetti installati, è possibile iniziare a compilare la struttura di base del progetto API Web. Se è già stata usata un'API Web, la struttura del progetto dovrebbe avere un aspetto familiare. Iniziare creando le directory API Web di base:

 + App\_Start
 + Controller
 + Modelli

Aggiungere le classi di configurazione dell'API Web di base nella directory App\_Start: Per ora si aggiungerà solo una configurazione di formattatore di tipo di supporto vuoto:

 + FormatterConfig.cs

```csharp

namespace WebApiService
{
    using System.Net.Http.Formatting;

    public static class FormatterConfig
    {
        public static void ConfigureFormatters(MediaTypeFormatterCollection formatters)
        {
        }
    }
}

```

Aggiungere un controller predefinito nella directory Controller:

 + DefaultController.cs

```csharp

namespace WebApiService.Controllers
{
    using System.Collections.Generic;
    using System.Web.Http;

    [RoutePrefix("api")]
    public class DefaultController : ApiController
    {
        // GET api/values
        [Route("values")]
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5
        [Route("values/{id}")]
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values
        [Route("values")]
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5
        [Route("values/{id}")]
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5
        [Route("values/{id}")]
        public void Delete(int id)
        {
        }
    }
}

```

Aggiungere infine una classe Startup alla radice del progetto per registrare il routing, i formattatori e le altre impostazioni di configurazione. Si tratta inoltre della classe in cui l'API Web si collega all'*host*, come verrà illustrato più avanti. Durante la configurazione della classe Startup, creare un'interfaccia denominata *IOwinAppBuilder* per la classe Startup che definisce il metodo di configurazione. Anche se tecnicamente non è necessaria per il funzionamento dell'API Web, l'interfaccia consentirà un uso più flessibile della classe Startup.

 + Startup.cs

```csharp

namespace WebApiService
{
    using Owin;
    using System.Web.Http;

    public class Startup : IOwinAppBuilder
    {
        public void Configuration(IAppBuilder appBuilder)
        {
            HttpConfiguration config = new HttpConfiguration();

            config.MapHttpAttributeRoutes();
            FormatterConfig.ConfigureFormatters(config.Formatters);

            appBuilder.UseWebApi(config);
        }
    }
}

```

 + IOwinAppBuilder.cs

```csharp

namespace WebApiService
{
    using Owin;

    public interface IOwinAppBuilder
    {
        void Configuration(IAppBuilder appBuilder);
    }
}

```

La parte relativa all'applicazione è terminata. È stato quindi impostato il layout del progetto API Web di base. Questo progetto dovrebbe essere simile ai progetti API Web scritti in precedenza o al modello API Web di base. Come al solito, la logica di business si basa sui controller e sui modelli.

Verrà ora illustrato come configurare l'hosting per eseguire il servizio.


## Hosting del servizio

In Service Fabric il servizio viene eseguito in un *processo host del servizio*, un file eseguibile che esegue il codice del servizio. Durante la scrittura di un servizio tramite l'API di Reliable Services e nella maggior parte dei casi durante la creazione di un servizio in Service Fabric in .NET, il progetto di servizio viene compilato solo in un file con estensione exe che registra il tipo di servizio ed esegue il codice. Se si apre **Program.cs** nel progetto di servizio senza stato, verrà in effetti visualizzato quanto segue:

```csharp

public class Program
{
    public static void Main(string[] args)
    {
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterServiceType("WebApiServiceType", typeof(Service));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e);
            throw;
        }
    }
}

```

L'aspetto potrebbe apparire simile al punto di ingresso a un'applicazione console:

I dettagli relativi al processo host del servizio e alla registrazione del servizio non rientrano nell'ambito di questo argomento, ma è importante sapere che **il codice del servizio è in esecuzione nel relativo processo**.

## API Web con self-hosting OWIN

Il codice dell'applicazione API Web è ospitato nel relativo processo. Verrà ora illustrato come associarlo a un server Web. Immettere [OWIN](http://owin.org/). OWIN è semplicemente un contratto tra le applicazioni Web .NET e i server Web. Con ASP.NET, fino a 5 MVC, l'applicazione Web in genere era strettamente associata a IIS tramite System.Web. Tuttavia, l'API Web implementa OWIN, che consente di scrivere un'applicazione Web separata dal server Web che la ospita. In questo modo è possibile usare un server Web OWIN *self-host* che è possibile avviare nel proprio processo e che si adatta perfettamente al modello di hosting di Service Fabric descritto in precedenza.

In questo articolo verrà usato Katana come host OWIN per l'applicazione API Web. Katana è un'implementazione host OWIN open source.

> [AZURE.NOTE]Per altre informazioni su Katana, visitare il [ sito di Katana](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) e per una rapida panoramica sull'uso di Katana per l'API Web self-host, fare riferimento a questo articolo relativo all'[uso di OWIN per il self-hosting dell'API Web ASP.NET 2](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api).


## Configurare il server Web

L'API dei Reliable Services fornisce un punto di ingresso di comunicazione in cui è possibile collegare gli stack di comunicazione per consentire agli utenti e ai client di connettersi al servizio:

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}

```

Il server Web e gli eventuali stack di comunicazione da usare in futuro, ad esempio WebSockets, devono usare l'interfaccia ICommunicationListener per la corretta integrazione con il sistema. I motivi verranno illustrati nei passaggi seguenti.

Creare prima una classe denominata OwinCommunicationListener che implementa ICommunicationListener:

 + OwinCommunicationListener.cs:

```csharp

namespace WebApi
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;

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

 + **OpenAsync**: opzione per avviare l'ascolto delle richieste.
 + **CloseAsync**: opzione per interrompere l'ascolto delle richieste, completare tutte le richieste in elaborazione ed eseguire l'arresto normalmente.
 + **Abort**: opzione per cancellare tutto ed eseguire l'arresto immediatamente.

Per iniziare, aggiungere i membri della classe privata per un prefisso del percorso URL e la classe **Startup** creata in precedenza. Questi elementi vengono inizializzati tramite il costruttore e verranno usati in seguito durante la configurazione dell'URL di ascolto. Aggiungere inoltre i membri della classe privata per salvare l'indirizzo di ascolto e l'handle del server creati rispettivamente durante l'inizializzazione e in un secondo momento durante l'avvio del server.

```csharp

public class OwinCommunicationListener : ICommunicationListener
{
    private readonly IOwinAppBuilder startup;
    private readonly string appRoot;
    private IDisposable serverHandle;
    private string listeningAddress;
    private readonly ServiceInitializationParameters serviceInitializationParameters;

    public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup, ServiceInitializationParameters serviceInitializationParameters)
    {
        this.startup = startup;
        this.appRoot = appRoot;
        this.serviceInitializationParameters = serviceInitializationParameters;
    }        

    ...

```

### Implementazione

Per configurare il server web, abbiamo bisogno di un paio di informazioni:

 + **Un prefisso del percorso URL**. Sebbene sia facoltativo, è consigliabile configurarlo ora per poter ospitare in modo sicuro più servizi Web nell'applicazione.
 + **Una porta**.

Prima di ottenere una porta per il server Web, è importante comprendere che Service Fabric offre un livello di applicazione che funge da buffer tra l'applicazione e il sistema operativo sottostante in cui è in esecuzione. Service Fabric consente pertanto di configurare *endpoint* per i servizi. Service Fabric verifica che l'endpoint sia disponibile per l'uso da parte del servizio, pertanto non è necessario configurarlo manualmente con l'ambiente del sistema operativo sottostante. In questo modo è possibile ospitare facilmente l'applicazione di Service Fabric in ambienti diversi senza dover apportare modifiche all'applicazione (ad esempio, è possibile ospitare la stessa applicazione in Azure o nel proprio data center).

Configurare un endpoint HTTP in PackageRoot\\ServiceManifest.xml:

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="80" />
    </Endpoints>
</Resources>

```

Questo passaggio è importante perché il processo host del servizio viene eseguito con credenziali con restrizioni (Servizio di rete in Windows), pertanto il servizio non disporrà dell'accesso per la configurazione di un endpoint HTTP. Usando la configurazione dell'endpoint, Service Fabric imposta l'elenco di controllo di accesso appropriato per l'URL su cui il servizio rimarrà in ascolto, offrendo al contempo una posizione standard per la configurazione degli endpoint.


Una volta tornati in OwinCommunicationListener.cs, è possibile iniziare a implementare OpenAsync. Da qui si avvia il server web. Innanzitutto, ottenere le informazioni sull'endpoint e creare l'URL su cui il servizio rimarrà in ascolto.

```csharp

public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = String.Format(
        CultureInfo.InvariantCulture,
        "http://+:{0}/{1}",
        port,
        String.IsNullOrWhiteSpace(this.appRoot)
            ? String.Empty
            : this.appRoot.TrimEnd('/') + '/');
    ...

```

Si noti che viene usato "http://+" per assicurarsi che il server Web sia in ascolto su tutti gli indirizzi disponibili, tra cui il localhost, il nome di dominio completo e l'IP del computer.

L'implementazione di OpenAsync è uno dei motivi principali per i quali il server Web (o qualsiasi stack di comunicazione) viene implementato come un oggetto ICommunicationListener anziché venire aperto direttamente da RunAsync() nel servizio. Il valore restituito da OpenAsync è l'indirizzo su cui è in ascolto il server Web. Quando questo indirizzo viene restituito al sistema, registra l'indirizzo con il servizio. L'API di Service Fabric consente ai client o ad altri servizi di richiedere questo indirizzo in base al nome del servizio. Questo è importante perché l'indirizzo del servizio non è statico, in quanto i servizi vengono spostati nel cluster per scopi di disponibilità e bilanciamento delle risorse. Questo meccanismo consente ai client di risolvere l'indirizzo di ascolto per un servizio.

Tenendo presente questo aspetto, OpenAsync avvia il server Web e restituisce l'indirizzo su cui è in ascolto. Si noti che è in ascolto su "http://+", ma prima di restituire l'indirizzo il "+" viene sostituito con l'IP o con il nome di dominio completo del nodo corrente. L'indirizzo restituito dal metodo viene infatti registrato nel sistema e verrà visualizzato quando i client e gli altri servizi richiederanno l'indirizzo del servizio. Per una corretta connessione dei client, è necessario un IP o un nome di dominio completo effettivo nell'indirizzo.

```csharp

    ...

    this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));
    string publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    ServiceEventSource.Current.Message("Listening on {0}", publishAddress);

    return Task.FromResult(publishAddress);
}

```

Si noti che si fa riferimento alla classe **Startup** passata all'oggetto OwinCommunicationListener nel costruttore. Questa istanza di Startup viene usata dal server Web per avviare l'applicazione API Web.

Durante l'esecuzione dell'applicazione verrà visualizzata la riga ServiceEventSource.Current.Message() nella finestra degli eventi di diagnostica per confermare che il server Web è stato avviato correttamente.

### CloseAsync e Abort

Implementare infine CloseAsync e Abort per arrestare il server Web. Il server Web può essere arrestato eliminando l'handle del server creato durante OpenAsync.

```csharp

public Task CloseAsync(CancellationToken cancellationToken)
{
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
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

In questa implementazione di esempio CloseAsync e Abort consentono di arrestare semplicemente il server Web. È possibile scegliere di eseguire un arresto coordinato più graduale del server Web in CloseAsync, attendendo ad esempio il completamento delle richieste in elaborazione prima della restituzione.

## Avviare il server Web

È ora possibile creare e restituire un'istanza di OwinCommunicationListener per avviare il server Web. Nella classe del servizio (Service.cs) eseguire l'override del metodo **CreateServiceInstanceListeners()**:

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new[]
    {
        new ServiceInstanceListener(initParams => new OwinCommunicationListener("webapp", new Startup(), initParams))
    };
}

```

A questo punto si riuniscono l'*applicazione* API Web e l'*host* OWIN: all'*host* (**OwinCommunicationListener**) viene assegnata un'istanza dell'*applicazione* (API Web tramite **Startup**) e Service Fabric gestisce il ciclo di vita. Questo modello può essere seguito con qualsiasi stack di comunicazione.

## Riassumendo

In questo esempio non è necessario eseguire alcuna operazione nel metodo RunAsync(), pertanto l'override può essere semplicemente rimosso.

L'implementazione finale del servizio dovrebbe essere molto semplice, in quanto sarà necessario solo creare il listener di comunicazione:

```csharp

namespace WebApiService
{
    using System.Collections.Generic;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;
    using Microsoft.ServiceFabric.Services.Runtime;

    public class WebApiService : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            return new[]
            {
                new ServiceInstanceListener(initParams => new OwinCommunicationListener("webapp", new Startup(), initParams))
            };
        }
    }
}

```

E la classe `OwinCommunicationListener` completa:

```csharp

namespace WebApiService
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;

    public class OwinCommunicationListener : ICommunicationListener
    {
        private readonly IOwinAppBuilder startup;
        private readonly string appRoot;
        private readonly ServiceInitializationParameters serviceInitializationParameters;
        private IDisposable serverHandle;
        private string listeningAddress;
        
        public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup, ServiceInitializationParameters serviceInitializationParameters)
        {
            this.startup = startup;
            this.appRoot = appRoot;
            this.serviceInitializationParameters = serviceInitializationParameters;
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
            int port = serviceEndpoint.Port;

            this.listeningAddress = String.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/{1}",
                port,
                String.IsNullOrWhiteSpace(this.appRoot)
                    ? String.Empty
                    : this.appRoot.TrimEnd('/') + '/');

            this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));
            string publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            ServiceEventSource.Current.Message("Listening on {0}", publishAddress);

            return Task.FromResult(publishAddress);
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            ServiceEventSource.Current.Message("Close");

            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            ServiceEventSource.Current.Message("Abort");

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
    }
}

```

Dopo che tutti gli elementi necessari sono pronti, il progetto sarà simile a una tipica applicazione API Web con i punti di ingresso dell'API di Reliable Services e un host OWIN:


![](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## Esecuzione e connessione tramite un Web browser

Se non lo si è già fatto, [configurare l'ambiente di sviluppo](service-fabric-get-started.md).


È ora possibile compilare e distribuire il servizio. Premere **F5** in Visual Studio per compilare l'applicazione. Nella finestra degli eventi di diagnostica verrà visualizzato un messaggio che indica il server Web aperto in ****http://localhost:80/webapp/api**.


![](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE]Se la porta è già stata aperta da un altro processo nel computer, potrebbe essere visualizzato un errore che indica che non è stato possibile aprire il listener. In questo caso provare a usare una porta diversa nella configurazione dell'endpoint in ServiceManifest.xml.


Dopo che il servizio è in esecuzione, aprire un browser e passare a [http://localhost/webapp/api/values](http://localhost/webapp/api/values) per testarlo.

## Scalabilità orizzontale

Scalare orizzontalmente app Web senza stato in genere significa aggiungere più macchine e attivare in esse l'applicazione Web. Il motore di orchestrazione di Service Fabric è in grado di farlo automaticamente ogni volta che vengono aggiunti nuovi nodi a un cluster. Durante la creazione di istanze di un servizio senza stato, è possibile specificare il numero di istanze che si desidera creare. Service Fabric posizionerà il numero specificato di istanze nei nodi del cluster, verificando che non venga creata più di una istanza in ogni nodo. È inoltre possibile impostare Service Fabric in modo da creare un'istanza in ogni nodo specificando "-1" nel conteggio istanze. In questo modo si garantisce che ogni volta che si aggiungono nodi per scalare orizzontalmente il cluster, nei nuovi nodi verrà creata un'istanza del servizio senza stato. Questo valore è una proprietà dell'istanza del servizio, pertanto viene impostato quando si crea un'istanza del servizio tramite PowerShell:

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

Oppure quando si definisce un servizio predefinito in un progetto di servizio senza stato di Visual Studio:

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

Per altre informazioni sulla creazione di istanze di applicazioni e di servizi, vedere l'articolo relativo alla [distribuzione e rimozione di applicazioni](service-fabric-deploy-remove-applications.md).

## ASP.NET 5

In ASP.NET 5 il concetto e il modello di programmazione per la separazione dell'*applicazione* dall'*host* in applicazioni Web restano gli stessi. Possono essere applicati anche ad altre forme di comunicazione. Inoltre, sebbene l'*host* possa variare in ASP.NET 5, il livello dell'*applicazione* API Web, dove risiede la maggior parte della logica dell'applicazione, rimane invariato.

## Passaggi successivi

[Debug dell'applicazione di Service Fabric in Visual Studio](service-fabric-debugging-your-application.md)

<!---HONumber=Nov15_HO4-->