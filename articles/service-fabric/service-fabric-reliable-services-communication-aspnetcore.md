---
title: Comunicazione del servizio con il ASP.NET CoreService communication with the ASP.NET Core
description: Informazioni su come usare ASP.NET Core nelle applicazioni Reliable Services di Azure Service Fabric senza stato e con stato.
author: vturecek
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 0d432bd19d0689ef508fca0bf24eed4406929f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639633"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET core nell'infrastruttura dei servizi di Azure

ASP.NET Core è un framework open source e multipiattaforma. Questo framework è progettato per la creazione di applicazioni basate su cloud con connessione Internet, ad esempio app Web, app IoT e back-end per dispositivi mobili.

Questo articolo è una guida approfondita all'hosting di servizi ASP.NET Core in Service Fabric Reliable Services utilizzando **Microsoft.ServiceFabric.AspNetCore.** set di pacchetti NuGet.

Per un'esercitazione introduttiva su ASP.NET Core in Service Fabric e istruzioni su come configurare l'ambiente di sviluppo, vedere [Esercitazione: Creare e distribuire un'applicazione con un servizio front-end api Web ASP.NET Core e un servizio back-end con stato.](service-fabric-tutorial-create-dotnet-app.md)

Il resto di questo articolo presuppone che tu abbia già familiarità con ASP.NET Core. In caso contrario, si prega di leggere i [ASP.NET fondamentali di Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core nell'ambiente Service Fabric

Sia ASP.NET core e Service Fabric applicazioni possono essere eseguite in .NET Core o .NET Framework completo. È possibile usare ASP.NET Core in due modi diversi in Service Fabric:You can use a Core in two different ways in Service Fabric:
 - **Ospitato come eseguibile guest**. In questo modo viene usato principalmente per eseguire applicazioni ASP.NET Core esistenti in Service Fabric senza modifiche al codice.
 - **Eseguire all'interno di un servizio affidabile**. In questo modo consente una migliore integrazione con il runtime di Service Fabric e consente lo stato di ASP.NET i servizi Core.This way allows better integration with the Service Fabric runtime and allows stateful ASP.NET Core services.

Il resto di questo articolo illustra come usare ASP.NET Core all'interno di un servizio affidabile, tramite i componenti di integrazione ASP.NET Core fornite con Service Fabric SDK.

## <a name="service-fabric-service-hosting"></a>Hosting di servizi di Service Fabric

In Service Fabric una o più istanze e/o repliche del servizio vengono eseguite in un processo host del *servizio:* un file eseguibile che esegue il codice del servizio. L'autore del servizio è proprietario del processo host del servizio e Service Fabric lo attiva e lo monitora automaticamente.

La versione tradizionale di ASP.NET, fino a MVC 5, è strettamente associata a IIS tramite System.Web.dll. ASP.NET Core fornisce una separazione tra il server Web e l'applicazione Web. Questa separazione consente alle applicazioni web di essere portabili tra diversi server web. Consente inoltre ai server Web di essere *self-hosted*. Ciò significa che è possibile avviare un server Web nel proprio processo, anziché un processo di proprietà di software server web dedicato, ad esempio IIS.

Per combinare un servizio Service Fabric e ASP.NET, come eseguibile guest o in un servizio affidabile, è necessario essere in grado di avviare ASP.NET all'interno del processo host del servizio. Il self-hosting di ASP.NET Core consente di eseguire questa operazione.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hosting di ASP.NET Core in un servizio affidabileHosting ASP.NET Core in a reliable service
Le applicazioni ASP.NET Core con self-hosting creano in genere un WebHost in un punto di ingresso dell'applicazione, ad esempio il metodo `static void Main()` in `Program.cs`. In questo caso, il ciclo di vita di WebHost è associato al ciclo di vita del processo.

![Hosting di ASP.NET Core in un processo][0]

Ma il punto di ingresso dell'applicazione non è il posto giusto per creare un WebHost in un servizio affidabile. Questo perché il punto di ingresso dell'applicazione viene usato solo per registrare un tipo di servizio con il runtime di Service Fabric, in modo che possa creare istanze di tale tipo di servizio. Il WebHost deve essere creato in un servizio affidabile. All'interno del processo host del servizio, le istanze del servizio e/o le repliche possono passare attraverso più cicli di vita. 

Un'istanza di Reliable Services è rappresentata dalla classe del servizio derivante da `StatelessService` o `StatefulService`. Lo stack di comunicazione di un servizio è contenuto in un'implementazione `ICommunicationListener` nella classe del servizio. I `Microsoft.ServiceFabric.AspNetCore.*` pacchetti NuGet contengono implementazioni che `ICommunicationListener` avviano e gestiscono il ASP.NET Core WebHost per Kestrel o HTTP.sys in un servizio affidabile.

![Diagramma per l'hosting di ASP.NET Core in un servizio affidabile][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ICommunicationListeners ASP.NET Core
Le `ICommunicationListener` implementazioni per Kestrel e `Microsoft.ServiceFabric.AspNetCore.*` HTTP.sys nei pacchetti NuGet hanno modelli di utilizzo simili. Ma eseguono azioni leggermente diverse specifiche per ogni server web. 

Entrambi i listener di comunicazione forniscono un costruttore che accetta gli argomenti seguenti:
 - **`ServiceContext serviceContext`**: questo `ServiceContext` è l'oggetto che contiene informazioni sul servizio in esecuzione.
 - **`string endpointName`**: nome di una `Endpoint` configurazione in ServiceManifest.xml. È principalmente dove i due listener di comunicazione differiscono. HTTP.sys *requires* richiede `Endpoint` una configurazione, mentre Kestrel non lo fa.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: si tratta di un'espressione lambda implementata, in cui viene creata e restituita un'istruzione `IWebHost`. Consente di configurare `IWebHost` il modo in cui si farebbe normalmente in un'applicazione ASP.NET Core. L'espressione lambda fornisce un URL generato automaticamente, a seconda delle `Endpoint` opzioni di integrazione di Service Fabric usate e della configurazione fornita. È quindi possibile modificare o utilizzare tale URL per avviare il server Web.

## <a name="service-fabric-integration-middleware"></a>Middleware di integrazione di Service Fabric
Il `Microsoft.ServiceFabric.AspNetCore` pacchetto NuGet `UseServiceFabricIntegration` include `IWebHostBuilder` il metodo di estensione che aggiunge middleware in grado di riconoscere Service Fabric.The NuGet package includes the extension method on that adds Service Fabric-aware middleware. Questo middleware configura Kestrel o `ICommunicationListener` HTTP.sys per registrare un URL di servizio univoco con Service Fabric Naming Service. Convalida quindi le richieste client per garantire che i client si connettano al servizio corretto. 

Questo passaggio è necessario per impedire ai client di connettersi erroneamente al servizio errato. Questo perché, in un ambiente host condiviso come Service Fabric, più applicazioni Web possono essere eseguite nella stessa macchina fisica o virtuale, ma non utilizzano nomi host univoci. Questo scenario è descritto più dettagliatamente nella sezione successiva.

### <a name="a-case-of-mistaken-identity"></a>Un caso di identità errata
Le repliche del servizio, indipendentemente dal protocollo, sono in ascolto in una combinazione IP:porta univoca. Dopo che una replica del servizio ha avviato l'ascolto su un endpoint IP:port, segnala tale indirizzo dell'endpoint al Service Fabric Naming Service. Lì, i clienti o altri servizi possono scoprirlo. Se i servizi usano porte dell'applicazione assegnate dinamicamente, una replica del servizio potrebbe usare casualmente lo stesso endpoint IP:porta di un altro servizio in precedenza nella stessa macchina fisica o virtuale. In questo modo un client può connettersi per errore al servizio sbagliato. Questo scenario può verificarsi se si verifica la seguente sequenza di eventi:

 1. Il servizio A è in ascolto in 10.0.0.1:30000 su HTTP. 
 2. Il client risolve il servizio A e ottiene l'indirizzo 10.0.0.1:30000.
 3. Il servizio A si sposta in un nodo diverso.
 4. Il servizio B si trova in 10.0.0.1 e usa per coincidenza la stessa porta 30000.
 5. Il client prova a connettersi al servizio A con l'indirizzo 10.0.0.1:30000 memorizzato nella cache.
 6. Il client è ora connesso correttamente al servizio B, senza rendersi conto che è connesso al servizio errato.

Ciò può causare bug in momenti casuali che possono essere difficili da diagnosticare.

### <a name="using-unique-service-urls"></a>Uso di URL di servizio univoci
Per evitare questi bug, i servizi possono inviare un endpoint al servizio di denominazione con un identificatore univoco e quindi convalidare tale identificatore univoco durante le richieste client. Si tratta di un'azione cooperativa tra servizi in un ambiente attendibile di tenant non ostili. Non fornisce l'autenticazione sicura del servizio in un ambiente tenant ostile.

In un ambiente attendibile, il middleware `UseServiceFabricIntegration` aggiunto dal metodo aggiunge automaticamente un identificatore univoco all'indirizzo inviato a Naming Service. Convalida tale identificatore a ogni richiesta. Se l'identificatore non corrisponde, il middleware restituisce immediatamente una risposta HTTP 410 Gone.

I servizi che usano una porta assegnata dinamicamente devono usare questo middleware.

I servizi che utilizzano una porta univoca fissa non presentano questo problema in un ambiente cooperativo. Una porta fissa univoca viene in genere usata per i servizi esterni che richiedono una porta nota per la connessione delle applicazioni client. Ad esempio, la maggior parte delle applicazioni Web con connessione Internet utilizzerà la porta 80 o 443 per le connessioni del browser Web. In questo caso, l'identificatore univoco non deve essere abilitato.

Il diagramma seguente illustra il flusso della richiesta con il middleware abilitato:

![Integrazione ASP.NET Core di Service Fabric][2]

Entrambe le implementazioni di `ICommunicationListener` Kestrel e HTTP.sys utilizzano questo meccanismo esattamente nello stesso modo. Sebbene HTTP.sys possa differenziare internamente le richieste in base a percorsi *not* URL univoci utilizzando la `ICommunicationListener` funzionalità di condivisione delle porte **HTTP.sys** sottostante, tale funzionalità non viene utilizzata dall'implementazione HTTP.sys. Questo perché restituisce i codici di stato di errore HTTP 503 e HTTP 404 nello scenario descritto in precedenza. Questo a sua volta rende difficile per i client determinare lo scopo dell'errore, come HTTP 503 e HTTP 404 sono comunemente utilizzati per indicare altri errori. 

Pertanto, entrambe le implementazioni `ICommunicationListener` di Kestrel e `UseServiceFabricIntegration` HTTP.sys vengono standardizzate su middleware fornito dal metodo di estensione. Pertanto, i client devono solo eseguire un'azione di risoluzione dell'endpoint del servizio sulle risposte HTTP 410.Therefore, clients only need to perform a service endpoint re-resolve action on HTTP 410 responses.

## <a name="httpsys-in-reliable-services"></a>HTTP.sys in Reliable Services
È possibile utilizzare HTTP.sys in Reliable Services importando il pacchetto **Microsoft.ServiceFabric.AspNetCore.HttpSys** NuGet. Questo pacchetto `HttpSysCommunicationListener`contiene , `ICommunicationListener`un'implementazione di . `HttpSysCommunicationListener`consente di creare un ASP.NET Core WebHost all'interno di un servizio affidabile utilizzando HTTP.sys come server Web.

HTTP.sys si basa [sull'API del server HTTP di Windows.](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx) Questa API utilizza il driver del kernel **HTTP.sys** per elaborare le richieste HTTP e instradarle ai processi che eseguono applicazioni Web. Ciò consente a più processi nella stessa macchina fisica o virtuale di ospitare applicazioni Web sulla stessa porta, senza ambiguità da un percorso URL univoco o da uno nome host. Queste funzionalità sono utili in Service Fabric per ospitare più siti Web nello stesso cluster.

>[!NOTE]
>L'implementazione HTTP.sys funziona solo sulla piattaforma Windows.HTTP.sys implementation works only on the Windows platform.

Nel diagramma seguente viene illustrato come HTTP.sys utilizza il driver del kernel **HTTP.sys** in Windows per la condivisione delle porte:

![Diagramma HTTP.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP.sys in un servizio senza stato
Per usare `HttpSys` in un servizio senza stato, eseguire l'override del metodo `CreateServiceInstanceListeners` e restituire un'istanza `HttpSysCommunicationListener`:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>HTTP.sys in un servizio con stato

`HttpSysCommunicationListener`non è attualmente progettato per l'utilizzo nei servizi con stato a causa di complicazioni con la funzionalità di condivisione delle porte **HTTP.sys** sottostante. Per altre informazioni, vedere la sezione seguente sull'allocazione dinamica delle porte con HTTP.sys.For more information, see the following section on dynamic port allocation with HTTP.sys. Per i servizi con stato, Kestrel è il server Web suggerito.

### <a name="endpoint-configuration"></a>Configurazione dell'endpoint

È `Endpoint` necessaria una configurazione per i server Web che utilizzano l'API di Windows HTTP Server, incluso HTTP.sys. I server Web che utilizzano l'API di Windows HTTP Server devono prima riservare l'URL con HTTP.sys (questa operazione viene in genere eseguita con lo strumento [netsh).](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) 

Questa azione richiede privilegi elevati che i servizi non dispongono per impostazione predefinita. Le opzioni "http" o "https" per la `Protocol` proprietà della `Endpoint` configurazione in ServiceManifest.xml vengono utilizzate in modo specifico per indicare al runtime di Service Fabric di registrare un URL con HTTP.sys per conto dell'utente. Questa operazione viene a tale scopo utilizzando il prefisso URL [*con caratteri jolly forte.*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx)

Ad esempio, `http://+:80` per prenotare un servizio, utilizzare la configurazione seguente in ServiceManifest.xml:For example, to reserve for a service, use the following configuration in ServiceManifest.xml:

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

Il nome dell'endpoint deve essere passato al costruttore `HttpSysCommunicationListener`:

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>Utilizzare HTTP.sys con una porta staticaUse HTTP.sys with a static port
Per utilizzare una porta statica con HTTP.sys, specificare il numero di porta nella configurazione:To use a static port with HTTP.sys, provide the port number in the `Endpoint` configuration:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Utilizzare HTTP.sys con una porta dinamicaUse HTTP.sys with a dynamic port
Per utilizzare una porta assegnata in modo dinamico con HTTP.sys, omettere la `Port` proprietà nella `Endpoint` configurazione:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Una porta dinamica `Endpoint` allocata da una configurazione fornisce una sola porta *per ogni processo host.* Il modello di hosting di Service Fabric corrente consente di ospitare più istanze del servizio e/o repliche nello stesso processo. Ciò significa che ognuno condividerà `Endpoint` la stessa porta quando allocato attraverso la configurazione. Più istanze **HTTP.sys** possono condividere una porta utilizzando la funzionalità di condivisione delle porte **HTTP.sys** sottostante. Ma non è supportato da `HttpSysCommunicationListener` causa delle complicazioni che introduce per le richieste dei clienti. Per l'utilizzo dinamico delle porte, Kestrel è il server Web suggerito.

## <a name="kestrel-in-reliable-services"></a>Kestrel in Reliable Services
È possibile utilizzare Kestrel in Reliable Services importando il pacchetto **Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet. Questo pacchetto `KestrelCommunicationListener`contiene , `ICommunicationListener`un'implementazione di . `KestrelCommunicationListener`consente di creare un ASP.NET Core WebHost all'interno di un servizio affidabile utilizzando Kestrel come server Web.

Kestrel è un server Web per ASP.NET Core multipiattaforma. A differenza di HTTP.sys, Kestrel non utilizza un gestore di endpoint centralizzato. Inoltre, a differenza di HTTP.sys, Kestrel non supporta la condivisione delle porte tra più processi. Ogni istanza di Kestrel deve usare una porta univoca. Per ulteriori informazioni su Kestrel, vedere i dettagli di [implementazione](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2).

![Diagramma di Ghecco][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel in un servizio senza stato
Per usare `Kestrel` in un servizio senza stato, eseguire l'override del metodo `CreateServiceInstanceListeners` e restituire un'istanza `KestrelCommunicationListener`:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>Kestrel in un servizio con stato
Per usare `Kestrel` in un servizio con stato, eseguire l'override del metodo `CreateServiceReplicaListeners` e restituire un'istanza `KestrelCommunicationListener`:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

In questo esempio viene fornita un'istanza singleton di `IReliableStateManager` al contenitore di inserimento delle dipendenze WebHost. Questo non è strettamente necessario, ma `IReliableStateManager` consente di utilizzare e reliable Collections nei metodi di azione del controller MVC.

*Non* viene fornito un nome di configurazione `Endpoint` a `KestrelCommunicationListener` in un servizio con stato, come spiegato più dettagliatamente nella sezione seguente.

### <a name="configure-kestrel-to-use-https"></a>Configurare Kestrel per l'uso di HTTPS
Quando si abilita HTTPS con Kestrel nel servizio, è necessario impostare diverse opzioni di ascolto. Aggiornare `ServiceInstanceListener` l'oggetto per usare un endpoint EndpointHttps e restare in ascolto su una porta specifica, ad esempio la porta 443.Update the to use an *EndpointHttps* endpoint and listen on a specific port (such as port 443). Quando si configura l'host Web per l'utilizzo del server Web Kestrel, è necessario configurare Kestrel per l'ascolto degli indirizzi IPv6 su tutte le interfacce di rete: 

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Per un esempio completo in un'esercitazione, vedere [Configurare Kestrel per l'utilizzo di HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Configurazione dell'endpoint
Non `Endpoint` è necessaria una configurazione per utilizzare Kestrel. 

Kestrel è un semplice server web autonomo. A differenza di HTTP.sys (o HttpListener), non è necessaria una `Endpoint` configurazione in ServiceManifest.xml perché non richiede la registrazione dell'URL prima di iniziare. 

#### <a name="use-kestrel-with-a-static-port"></a>Usare Kestrel con una porta statica
È possibile configurare una `Endpoint` porta statica nella configurazione di ServiceManifest.xml per l'utilizzo con Kestrel. Anche se questo non è strettamente necessario, offre due potenziali vantaggi:
 - Se la porta non rientra nell'intervallo di porte dell'applicazione, viene aperta tramite il firewall del sistema operativo da Service Fabric.
 - L'URL fornito all'utente tramite `KestrelCommunicationListener` userà questa porta.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Se è configurato un `Endpoint`, il nome deve essere passato al costruttore `KestrelCommunicationListener`: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Se ServiceManifest.xml non utilizza `Endpoint` una configurazione, omettere `KestrelCommunicationListener` il nome nel costruttore. In questo caso, verrà utilizzata una porta dinamica. Vedere la sezione successiva per ulteriori informazioni su questo.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Usare Kestrel con una porta dinamica
Kestrel non può utilizzare l'assegnazione automatica della porta dalla `Endpoint` configurazione in ServiceManifest.xml. Questo perché l'assegnazione `Endpoint` automatica delle porte da una configurazione assegna una porta univoca per *ogni processo host*e un singolo processo host può contenere più istanze di Kestrel. Questo non funziona con Kestrel perché non supporta la condivisione delle porte. Pertanto, ogni istanza di Kestrel deve essere aperta su una porta univoca.

Per utilizzare l'assegnazione dinamica delle porte `Endpoint` con Kestrel, omettere completamente la configurazione in `KestrelCommunicationListener` ServiceManifest.xml e non passare un nome di endpoint al costruttore, come indicato di seguito:To use dynamic port assignment with Kestrel, omit the configuration in ServiceManifest.xml entirely, and don't pass an endpoint name to the constructor, as follows:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

In questa configurazione, `KestrelCommunicationListener` selezionerà automaticamente una porta non usata dall'intervallo di porte dell'applicazione.

Per HTTPS, deve avere l'endpoint configurato con protocollo HTTPS senza una porta specificata in ServiceManifest.xml e passare il nome dell'endpoint al costruttore KestrelCommunicationListener.


## <a name="service-fabric-configuration-provider"></a>Provider di configurazione di Service FabricService Fabric configuration provider
La configurazione dell'app in ASP.NET Core si basa sulle coppie chiave-valore stabilite dal provider di configurazione. Leggere [Configurazione in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) per ottenere ulteriori informazioni sul supporto generale della configurazione ASP.NET Core.Read Configuration in ASP.NET Core to understand more on general ASP.NET Core configuration support.

In questa sezione viene descritto come il provider di configurazione `Microsoft.ServiceFabric.AspNetCore.Configuration` di Service Fabric si integra con ASP.NET configurazione Core importando il pacchetto NuGet.This section describes how the Service Fabric configuration provider integrates with ASP.NET Core configuration by importing the NuGet package.

### <a name="addservicefabricconfiguration-startup-extensions"></a>Estensioni di avvio AddServiceFabricConfigurationAddServiceFabricConfiguration startup extensions
Dopo aver `Microsoft.ServiceFabric.AspNetCore.Configuration` importato il pacchetto NuGet, è necessario registrare l'origine di configurazione di Service Fabric con ASP.NETAPI di configurazione Core.After you import the NuGet package, you need to register the Service Fabric Configuration source with ASP.NET Core configuration API. A tale scopo, controllare le estensioni `IConfigurationBuilder` **AddServiceFabricConfiguration** nello `Microsoft.ServiceFabric.AspNetCore.Configuration` spazio dei nomi rispetto a .

```csharp
using Microsoft.ServiceFabric.AspNetCore.Configuration;

public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
        .AddServiceFabricConfiguration() // Add Service Fabric configuration settings.
        .AddEnvironmentVariables();
    Configuration = builder.Build();
}

public IConfigurationRoot Configuration { get; }
```

Ora il servizio ASP.NET Core può accedere alle impostazioni di configurazione di Service Fabric, come qualsiasi altra impostazione dell'applicazione. Ad esempio, è possibile utilizzare il modello di opzioni per caricare le impostazioni in oggetti fortemente tipizzati.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Mapping dei tasti predefinito
Per impostazione predefinita, il provider di configurazione di Service Fabric include il nome del pacchetto, il nome della sezione e il nome della proprietà. Insieme, questi formano la chiave di configurazione ASP.NET Core, come indicato di seguito:Together, these form the ASP.NET Core configuration key, as follows:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Ad esempio, se si dispone `MyConfigPackage` di un pacchetto di configurazione denominato con `IConfiguration` il contenuto seguente, il valore di configurazione sarà disponibile in ASP.NET Core tramite *MyConfigPackage:MyConfigSection:MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Opzioni di configurazione di Service FabricService Fabric configuration options
Il provider di configurazione `ServiceFabricConfigurationOptions` di Service Fabric supporta anche la modifica del comportamento predefinito del mapping dei tasti.

#### <a name="encrypted-settings"></a>Impostazioni crittografate
Service Fabric supporta le impostazioni crittografate, così come il provider di configurazione di Service Fabric. Le impostazioni crittografate non vengono decrittografate in ASP.NET Core `IConfiguration` per impostazione predefinita. I valori crittografati sono invece memorizzati lì. Ma se si desidera decrittografare il valore da archiviare in ASP.NET Core `AddServiceFabricConfiguration` IConfiguration, è possibile impostare il *DecryptValue* flag false nell'estensione, come segue:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = false); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Più pacchetti di configurazione
Service Fabric supporta più pacchetti di configurazione. Per impostazione predefinita, il nome del pacchetto è incluso nella chiave di configurazione. Ma è possibile `IncludePackageName` impostare il flag su false, come segue:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        // exclude package name from key.
        .AddServiceFabricConfiguration(activationContext, (options) => options.IncludePackageName = false); 
    Configuration = builder.Build();
}
```
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Mapping dei tasti personalizzati, estrazione di valori e popolamento dei dati
Il provider di configurazione di Service Fabric supporta inoltre `ExtractKeyFunc` scenari più avanzati `ExtractValueFunc`per personalizzare il mapping dei tasti ed estrarre in modo personalizzato i valori con . È anche possibile modificare l'intero processo di popolamento dei `ConfigAction`dati dalla configurazione di Service Fabric alla configurazione di ASP.NET Core utilizzando .

Negli esempi seguenti viene `ConfigAction` illustrato come utilizzare per personalizzare il popolamento dei dati:The following examples illustrate how to use to customize data population:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    
    this.valueCount = 0;
    this.sectionCount = 0;
    var builder = new ConfigurationBuilder();
    builder.AddServiceFabricConfiguration(activationContext, (options) =>
        {
            options.ConfigAction = (package, configData) =>
            {
                ILogger logger = new ConsoleLogger("Test", null, false);
                logger.LogInformation($"Config Update for package {package.Path} started");

                foreach (var section in package.Settings.Sections)
                {
                    this.sectionCount++;

                    foreach (var param in section.Parameters)
                    {
                        configData[options.ExtractKeyFunc(section, param)] = options.ExtractValueFunc(section, param);
                        this.valueCount++;
                    }
                }

                logger.LogInformation($"Config Update for package {package.Path} finished");
            };
        });
  Configuration = builder.Build();
}
```

### <a name="configuration-updates"></a>Aggiornamenti della configurazione
Il provider di configurazione di Service Fabric supporta anche gli aggiornamenti della configurazione. È possibile usare `IOptionsMonitor` ASP.NET Core per ricevere `IOptionsSnapshot` le notifiche di modifica e quindi usare per ricaricare i dati di configurazione. Per ulteriori informazioni, vedere [Opzioni di ASP.NET base](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

Queste opzioni sono supportate per impostazione predefinita. Non sono necessarie ulteriori operazioni di codifica per abilitare gli aggiornamenti della configurazione.

## <a name="scenarios-and-configurations"></a>Scenari e configurazioni
In questa sezione viene fornita la combinazione di server Web, configurazione delle porte, opzioni di integrazione di Service Fabric e varie impostazioni, è consigliabile risolvere gli scenari seguenti:
 - Servizi ASP.NET Core senza stato esposti all'esterno
 - Servizi solo interni ASP.NET Core senza stato
 - Servizi di stato ASP.NET solo interni

Un **servizio esposto esternamente** è un servizio che espone un endpoint chiamato dall'esterno del cluster, in genere tramite un servizio di bilanciamento del carico.

Un servizio **solo interno** è un servizio il cui endpoint viene chiamato solo dall'interno del cluster.

> [!NOTE]
> Gli endpoint del servizio con stato in genere non devono essere esposti a Internet.Stateful service endpoints generally shouldn't be exposed to the internet. I cluster dietro i servizi di bilanciamento del carico che non sono a conoscenza della risoluzione del servizio Service Fabric, ad esempio Azure Load Balancer, non saranno in grado di esporre i servizi con stato. Questo perché il servizio di bilanciamento del carico non sarà in grado di individuare e instradare il traffico alla replica del servizio con stato appropriata. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Servizi ASP.NET Core senza stato esposti all'esterno
Kestrel è il server Web consigliato per i servizi front-end che espongono endpoint HTTP esterni con connessione Internet. In Windows, HTTP.sys può fornire funzionalità di condivisione delle porte, che consente di ospitare più servizi Web sullo stesso set di nodi utilizzando la stessa porta. In questo scenario, i servizi Web vengono differenziati in base al nome host o al percorso, senza basarsi su un proxy o un gateway front-end per fornire il routing HTTP.
 
Quando è esposto a Internet, un servizio senza stato deve usare un endpoint noto e stabile raggiungibile tramite un servizio di bilanciamento del carico. Questo URL verrà fornito agli utenti dell'applicazione. È consigliabile la configurazione seguente:

|  |  | **Note** |
| --- | --- | --- |
| Server Web | Kestrel | Kestrel è il server web preferito, in quanto è supportato in Windows e Linux. |
| Configurazione delle porte | static | È necessario configurare una porta statica nota nella configurazione `Endpoints` di ServiceManifest.xml, ad esempio 80 per HTTP o 443 per HTTPS. |
| ServiceFabricIntegrationOptions | nessuno | Utilizzare `ServiceFabricIntegrationOptions.None` l'opzione durante la configurazione del middleware di integrazione di Service Fabric, in modo che il servizio non tenti di convalidare le richieste in ingresso per un identificatore univoco. Gli utenti esterni dell'applicazione non conosceranno le informazioni di identificazione univoche utilizzate dal middleware. |
| Conteggio istanze | -1 | Nei casi d'uso tipici, l'impostazione del conteggio delle istanze deve essere impostata su *-1*. Questa operazione viene eseguita in modo che un'istanza sia disponibile in tutti i nodi che ricevono traffico da un servizio di bilanciamento del carico. |

Se più servizi esposti esternamente condividono lo stesso set di nodi, è possibile utilizzare HTTP.sys con un percorso URL univoco ma stabile. A tale scopo, modificare l'URL fornito durante la configurazione di IWebHost.You can accomplish this by modifying the URL provided when configuring IWebHost. Si noti che questo vale solo per HTTP.sys.

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>Servizio ASP.NET Core senza stato solo interno
I servizi senza stato che vengono chiamati solo dall'interno del cluster devono usare URL univoci e porte assegnate dinamicamente per assicurare la cooperazione tra più servizi. È consigliabile la configurazione seguente:

|  |  | **Note** |
| --- | --- | --- |
| Server Web | Kestrel | Sebbene sia possibile utilizzare HTTP.sys per i servizi interni senza stato, Kestrel è il server migliore per consentire a più istanze del servizio di condividere un host.  |
| Configurazione delle porte | assegnate in modo dinamico | Più repliche di un servizio con stato potrebbero condividere un processo host o un sistema operativo host e pertanto saranno necessarie porte univoche. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Con l'assegnazione dinamica delle porte, questa impostazione evita il problema di errata identificazione descritto in precedenza. |
| InstanceCount | any | Il numero di istanze può essere impostato su qualsiasi valore necessario per il funzionamento del servizio. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Servizio ASP.NET Core con stato solo interno
I servizi con stato che vengono chiamati solo dall'interno del cluster devono usare porte assegnate dinamicamente per assicurare la cooperazione tra più servizi. È consigliabile la configurazione seguente:

|  |  | **Note** |
| --- | --- | --- |
| Server Web | Kestrel | L'oggetto `HttpSysCommunicationListener` non è progettato per l'utilizzo da parte di servizi con stato in cui le repliche condividono un processo host. |
| Configurazione delle porte | assegnate in modo dinamico | Più repliche di un servizio con stato potrebbero condividere un processo host o un sistema operativo host e pertanto saranno necessarie porte univoche. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Con l'assegnazione dinamica delle porte, questa impostazione evita il problema di errata identificazione descritto in precedenza. |

## <a name="next-steps"></a>Passaggi successivi
[Debug dell'applicazione di Service Fabric mediante Visual Studio](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
