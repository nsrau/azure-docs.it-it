---
title: Comunicazione dei servizi con ASP.NET Core | Microsoft Docs
description: Informazioni su come usare ASP.NET Core in Reliable Services con e senza stato.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 638c06e1854504dcb7ff34b1d9df56694556c421
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939784"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Core in Reliable Services di Azure Service Fabric

ASP.NET Core è un framework open source e multipiattaforma. Questo framework è progettato per la compilazione di applicazioni basato sul cloud, connesse a internet, ad esempio App web, App IoT e back-end mobili.

Questo articolo viene fornita una guida approfondita per l'hosting dei servizi ASP.NET Core in Reliable Services di Service Fabric usando il **aspnetcore.** set di pacchetti NuGet.

Per un'esercitazione introduttiva su ASP.NET Core in Service Fabric e istruzioni per la configurazione dell'ambiente di sviluppo, vedere [esercitazione: Creare e distribuire un'applicazione con un servizio front-end API Web ASP.NET Core e un servizio back-end con stato](service-fabric-tutorial-create-dotnet-app.md).

Il resto di questo articolo presuppone che si ha già familiarità con ASP.NET Core. In caso contrario, leggere il [nozioni fondamentali su ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core nell'ambiente Service Fabric

App ASP.NET Core e Service Fabric possono essere eseguite in .NET Core o .NET Framework completo. È possibile usare ASP.NET Core in due modi diversi in Service Fabric:
 - **Ospitato come eseguibile guest**. In questo modo viene utilizzato principalmente per eseguire applicazioni esistenti di ASP.NET Core in Service Fabric senza apportare modifiche al codice.
 - **Esecuzione in un servizio Reliable Services**. In questo modo consente una migliore integrazione con il runtime di Service Fabric e consente servizi ASP.NET Core con stato.

Il resto di questo articolo illustra come usare ASP.NET Core in un servizio Reliable Services, tramite i componenti di integrazione ASP.NET Core forniti con Service Fabric SDK.

## <a name="service-fabric-service-hosting"></a>Hosting di servizi di Service Fabric

In Service Fabric, uno o più istanze e/o repliche del servizio vengono eseguiti in un *processo host del servizio*: un file eseguibile che esegue il codice del servizio. L'autore del servizio, possedute il processo host del servizio e attiva e ne esegue il monitoraggio per l'utente di Service Fabric.

La versione tradizionale di ASP.NET, fino a MVC 5, è strettamente associata a IIS tramite System.Web.dll. ASP.NET Core fornisce una separazione tra il server Web e l'applicazione Web. Questa separazione consente alle applicazioni web garantire la portabilità tra i server web diverso. Consente inoltre ai server web *self-hosted*. Ciò significa che è possibile avviare un server web nel proprio processo, invece di un processo di proprietà di software del server web dedicato, ad esempio IIS.

Per combinare un servizio di Service Fabric e ASP.NET, come un eseguibile guest o in un servizio Reliable Services, è necessario essere in grado di avviare ASP.NET all'interno del processo host del servizio. Il self-hosting di ASP.NET Core consente di eseguire questa operazione.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hosting di ASP.NET Core in un servizio reliable Services
Le applicazioni ASP.NET Core con self-hosting creano in genere un WebHost in un punto di ingresso dell'applicazione, ad esempio il metodo `static void Main()` in `Program.cs`. In questo caso, il ciclo di vita del WebHost è associato al ciclo di vita del processo.

![Hosting di ASP.NET Core in un processo][0]

Ma il punto di ingresso dell'applicazione non è il posto giusto per creare un WebHost in un servizio Reliable Services. Ciò avviene perché il punto di ingresso dell'applicazione viene utilizzato solo per registrare un tipo di servizio con il runtime di Service Fabric, in modo da poter creare istanze di quel tipo di servizio. Il WebHost deve essere creato in un servizio reliable stessa. Nel processo host del servizio, le istanze del servizio e/o repliche possono sottoposte a vari cicli di vita. 

Un'istanza di Reliable Services è rappresentata dalla classe del servizio derivante da `StatelessService` o `StatefulService`. Lo stack di comunicazione di un servizio è contenuto in un'implementazione `ICommunicationListener` nella classe del servizio. Il `Microsoft.ServiceFabric.AspNetCore.*` i pacchetti NuGet contengono implementazioni di `ICommunicationListener` che avviano e gestiscono WebHost ASP.NET Core per Kestrel o HTTP. sys in un servizio reliable Services.

![Diagramma per l'hosting di ASP.NET Core in un servizio Reliable Services][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ICommunicationListeners ASP.NET Core
Il `ICommunicationListener` implementazioni per Kestrel e HTTP. sys nel `Microsoft.ServiceFabric.AspNetCore.*` pacchetti NuGet hanno modelli di uso simili. Ma eseguono azioni leggermente specifiche per ogni server web. 

Entrambi i listener di comunicazione forniscono un costruttore che accetta gli argomenti seguenti:
 - **`ServiceContext serviceContext`**: Si tratta di `ServiceContext` oggetto contenente le informazioni relative al servizio in esecuzione.
 - **`string endpointName`**: Si tratta del nome di un `Endpoint` configurazione nel file servicemanifest. Xml. È principalmente qui che i due listener di comunicazione differiscono. Http. sys *richiede* un `Endpoint` configurazione, anche se Kestrel non.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: Si tratta di un'espressione lambda implementata, in cui viene creato e restituito un `IWebHost`. Consente di configurare `IWebHost` di come si farebbe normalmente in un'applicazione ASP.NET Core. L'espressione lambda fornisce un URL che viene generato automaticamente, a seconda delle opzioni di integrazione di Service Fabric è utilizzare e `Endpoint` configuration è fornire. È quindi possibile modificare o usare l'URL per avviare il server web.

## <a name="service-fabric-integration-middleware"></a>Middleware di integrazione di Service Fabric
Il `Microsoft.ServiceFabric.AspNetCore` pacchetto NuGet include le `UseServiceFabricIntegration` metodo di estensione su `IWebHostBuilder` che aggiunge middleware compatibile con Service Fabric. Questo middleware configura il Kestrel o HTTP. sys `ICommunicationListener` per registrare un URL di servizio univoco con Service Fabric Naming Service. Quindi convalida le richieste client per assicurarsi che i client si connettono al servizio corretto. 

Questo passaggio è necessario impedire ai client di connettersi erroneamente al servizio sbagliato. Ciò avviene perché, in un ambiente host condiviso, ad esempio Service Fabric, è possono eseguire nello stesso fisico o nella macchina virtuale più applicazioni web ma non usano nomi host univoci. Questo scenario è descritto più dettagliatamente nella sezione successiva.

### <a name="a-case-of-mistaken-identity"></a>Un caso di identità errata
Le repliche del servizio, indipendentemente dal protocollo, sono in ascolto in una combinazione IP:porta univoca. Una volta che una replica del servizio è in ascolto su un endpoint IP: Port, viene segnalata indirizzo dell'endpoint di Service Fabric Naming Service. Non esiste, client o altri servizi possono individuarlo. Se i servizi usano le porte applicazione assegnata dinamicamente, una replica del servizio può usare per coincidenza lo stesso endpoint IP: Port di un altro servizio in precedenza nello stesso computer fisico o macchina virtuale. In questo modo un client può connettersi per errore al servizio sbagliato. Questo scenario può verificarsi se si verifica la sequenza di eventi seguente:

 1. Il servizio A è in ascolto in 10.0.0.1:30000 su HTTP. 
 2. Client risolve il servizio A e ottiene l'indirizzo 10.0.0.1: 30000.
 3. Il servizio A si sposta in un nodo diverso.
 4. Il servizio B si trova in 10.0.0.1 e usa per coincidenza la stessa porta 30000.
 5. Il client prova a connettersi al servizio A con l'indirizzo 10.0.0.1:30000 memorizzato nella cache.
 6. Client ora è connesso al servizio B, senza rendersi conto è connesso al servizio sbagliato.

Ciò può causare bug in momenti casuali che possono essere difficili da diagnosticare.

### <a name="using-unique-service-urls"></a>Uso di URL di servizio univoci
Per evitare questi bug, servizi possono inviare un endpoint al servizio Naming con un identificatore univoco e quindi convalidare tale identificatore univoco durante le richieste client. Si tratta di un'azione cooperativa tra servizi in un ambiente attendibile di tenant non ostili. Non fornisce l'autenticazione del servizio protetta in un ambiente di tenant ostili.

In un ambiente attendibile, il middleware aggiunto dal `UseServiceFabricIntegration` metodo aggiunge automaticamente un identificatore univoco per l'indirizzo registrato per il servizio Naming. Identificatore a ogni richiesta di convalida. Se l'identificatore non corrisponde, il middleware restituisce immediatamente una risposta HTTP 410-non più.

I servizi che usano una porta assegnata dinamicamente devono usare questo middleware.

I servizi che usano una porta fissa univoca non è il problema in un ambiente collaborativo. Una porta fissa univoca viene in genere usata per i servizi esterni che richiedono una porta nota per la connessione delle applicazioni client. Ad esempio, maggior parte delle applicazioni web con connessione internet userà la porta 80 o 443 per connessioni tramite browser web. In questo caso, l'identificatore univoco non deve essere abilitato.

Il diagramma seguente illustra il flusso della richiesta con il middleware abilitato:

![Integrazione ASP.NET Core di Service Fabric][2]

Kestrel e HTTP. sys `ICommunicationListener` implementazioni usano questo meccanismo nello stesso modo. Sebbene HTTP. sys può differenziare internamente le richieste basate su percorsi URL univoci usando sottostante **HTTP. sys** funzionalità, questa funzionalità di condivisione delle porte *non* usato da HTTP. sys `ICommunicationListener`implementazione. Ciò avviene perché il risultato è codici di stato errore HTTP 503 e HTTP 404 nello scenario descritto in precedenza. Che a sua volta rende difficile per i client determinare la finalità dell'errore, come HTTP 503 e HTTP 404 sono comunemente usati per indicare altri errori. 

Di conseguenza, Kestrel e HTTP. sys `ICommunicationListener` implementazioni standardizzare middleware fornito dal `UseServiceFabricIntegration` metodo di estensione. Di conseguenza, i client debbano solo eseguire un'azione di risolvere di nuovo endpoint di servizio nelle risposte HTTP 410.

## <a name="httpsys-in-reliable-services"></a>Http. sys in Reliable Services
È possibile usare HTTP. sys in Reliable Services importando il **httpsys** pacchetto NuGet. Questo pacchetto contiene `HttpSysCommunicationListener`, un'implementazione di `ICommunicationListener`. `HttpSysCommunicationListener` Consente di creare un WebHost ASP.NET Core in un servizio Reliable Services usando HTTP. sys come server web.

Http. sys si basa il [API Server HTTP di Windows](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Questa API Usa il **HTTP. sys** driver del kernel per elaborare le richieste HTTP e indirizzarle ai processi che eseguono le applicazioni web. Ciò consente a più processi nello stesso fisico o nella macchina virtuale per ospitare applicazioni web sulla stessa porta, risolvere l'ambiguità per entrambi un URL host o percorso nome univoco. Queste funzionalità sono utili in Service Fabric per ospitare più siti Web nello stesso cluster.

>[!NOTE]
>Implementazione di HTTP. sys funziona solo con la piattaforma Windows.

Il diagramma seguente illustra come HTTP. sys Usa la **HTTP. sys** driver del kernel in Windows per la condivisione delle porte:

![Diagramma di HTTP. sys][3]

### <a name="httpsys-in-a-stateless-service"></a>Http. sys in un servizio senza stato
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

### <a name="httpsys-in-a-stateful-service"></a>Http. sys in un servizio con stato

`HttpSysCommunicationListener` attualmente non è progettato per l'utilizzo nei servizi con stato a causa di problemi con l'oggetto sottostante **HTTP. sys** funzionalità di condivisione delle porte. Per altre informazioni, vedere la sezione seguente sull'allocazione delle porte dinamiche con HTTP. sys. Per i servizi con stato, Kestrel è il server web consigliato.

### <a name="endpoint-configuration"></a>Configurazione dell'endpoint

Un `Endpoint` configurazione è necessaria per i server web che usano l'API Server HTTP Windows, tra cui HTTP. sys. Server Web che utilizzano l'API Server HTTP di Windows devono prima riservare i rispettivi URL con HTTP. sys (questa operazione viene in genere eseguita con il [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) tool). 

Questa azione richiede privilegi elevati che i servizi non hanno per impostazione predefinita. Le opzioni "http" o "https" per il `Protocol` proprietà del `Endpoint` configurazione nel file servicemanifest. XML vengono usate specificamente per indicare al runtime di Service Fabric di registrare un URL HTTP. sys per tuo conto. A tale scopo, viene usando il [ *carattere jolly complesso* ](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) prefisso URL.

Ad esempio, per riservare `http://+:80` per un servizio, usare la configurazione seguente in servicemanifest. XML:

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

#### <a name="use-httpsys-with-a-static-port"></a>Usare HTTP. sys con una porta statica
Per usare una porta statica con HTTP. sys, specificare il numero di porta nella `Endpoint` configurazione:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Usare HTTP. sys con una porta dinamica
Per usare una porta assegnata dinamicamente con HTTP. sys, omettere il `Port` proprietà di `Endpoint` configurazione:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Una porta dinamica allocata da un `Endpoint` configurazione offre una sola porta *per ogni processo host*. Il modello di hosting corrente di Service Fabric consente più istanze del servizio e/o repliche per essere ospitati nello stesso processo. Ciò significa che ciascuna di esse condivideranno la stessa porta, se allocata tramite la `Endpoint` configurazione. Più **HTTP. sys** istanze possono condividere una porta tramite l'oggetto sottostante **HTTP. sys** funzionalità di condivisione delle porte. Ma non è supportato da `HttpSysCommunicationListener` a causa di problemi che provoca per le richieste client. Per informazioni sull'utilizzo di porte dinamiche, Kestrel è il server web consigliato.

## <a name="kestrel-in-reliable-services"></a>Kestrel in Reliable Services
È possibile usare Kestrel in Reliable Services importando il **servicefabric** pacchetto NuGet. Questo pacchetto contiene `KestrelCommunicationListener`, un'implementazione di `ICommunicationListener`. `KestrelCommunicationListener` Consente di creare un WebHost ASP.NET Core in un servizio Reliable Services usando Kestrel come server web.

Kestrel è che un server Web multipiattaforma per ASP.NET Core basato su libuv, una libreria I/O asincrona multipiattaforma. A differenza di HTTP. sys, Kestrel non usa una gestione centralizzata dell'endpoint. A differenza di HTTP. sys, Kestrel non supporta inoltre condivisione delle porte tra più processi. Ogni istanza di Kestrel deve usare una porta univoca.

![Diagramma di kestrel][4]

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

In questo esempio viene fornita un'istanza singleton di `IReliableStateManager` al contenitore di inserimento delle dipendenze WebHost. Non è strettamente necessaria, ma è possibile usare `IReliableStateManager` e Reliable Collections nei metodi di azione del controller MVC.

*Non* viene fornito un nome di configurazione `Endpoint` a `KestrelCommunicationListener` in un servizio con stato, come spiegato più dettagliatamente nella sezione seguente.

### <a name="configure-kestrel-to-use-https"></a>Configurare Kestrel per l'uso di HTTPS
Quando si abilita HTTPS con Kestrel nel servizio, è necessario impostare diverse opzioni in ascolto. Aggiorna il `ServiceInstanceListener` usare un *EndpointHttps* endpoint e l'ascolto su una porta specifica (ad esempio la porta 443). Quando si configura l'host web per usare il server web Kestrel, è necessario configurare Kestrel per l'ascolto per gli indirizzi IPv6 su tutte le interfacce di rete: 

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

Per un esempio completo in un'esercitazione, vedere [configurare Kestrel per usare HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Configurazione dell'endpoint
Un `Endpoint` configurazione non è necessario usare Kestrel. 

Kestrel è un server web autonomo semplice. A differenza di HTTP. sys (o HttpListener) non è necessario un `Endpoint` configurazione nel file servicemanifest. XML perché non richiede la registrazione dell'URL prima di iniziare. 

#### <a name="use-kestrel-with-a-static-port"></a>Usare Kestrel con una porta statica
È possibile configurare una porta statica nel `Endpoint` configuration di servicemanifest. XML per l'uso con Kestrel. Anche se ciò non è strettamente necessaria, questo servizio offre due vantaggi potenziali:
 - Se la porta non rientra nell'intervallo di porte dell'applicazione, viene aperto tramite il firewall del sistema operativo da Service Fabric.
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

Se servicemanifest. XML non usa un' `Endpoint` la configurazione, omettere il nome nel `KestrelCommunicationListener` costruttore. In questo caso, userà una porta dinamica. Vedere la sezione successiva per altre informazioni su questo.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Usare Kestrel con una porta dinamica
Kestrel non è possibile usare l'assegnazione automatica delle porte di `Endpoint` configurazione nel file servicemanifest. Xml. Ciò avviene perché automatica porta assegnazione da un `Endpoint` configurazione assegna una porta univoca per ogni *del processo host*, e un singolo processo host può contenere più istanze di Kestrel. Questo non funziona con Kestrel perché non supporta la condivisione delle porte. Pertanto, ogni istanza di Kestrel deve essere aperto su una porta univoca.

Per usare l'assegnazione dinamica delle porte con Kestrel, omettere il `Endpoint` configurazione nel file servicemanifest. XML e non passare un nome di endpoint per il `KestrelCommunicationListener` costruttore, come indicato di seguito:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

In questa configurazione, `KestrelCommunicationListener` selezionerà automaticamente una porta non usata dall'intervallo di porte dell'applicazione.

## <a name="service-fabric-configuration-provider"></a>Provider di configurazione di Service Fabric
Configurazione delle App in ASP.NET Core si basa sulla coppia chiave-valore stabilito dal provider di configurazione. Lettura [configurazione in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) conoscere più in generale configurazione supporto di ASP.NET Core.

Questa sezione viene descritto come il provider di configurazione di Service Fabric si integra con la configurazione di ASP.NET Core tramite l'importazione di `Microsoft.ServiceFabric.AspNetCore.Configuration` pacchetto NuGet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>Estensioni di avvio AddServiceFabricConfiguration
Dopo aver importato il `Microsoft.ServiceFabric.AspNetCore.Configuration` pacchetto NuGet, è necessario registrare l'origine di configurazione di Service Fabric con l'API di configurazione di ASP.NET Core. Eseguire questa operazione controllando **AddServiceFabricConfiguration** estensioni nel `Microsoft.ServiceFabric.AspNetCore.Configuration` spazio dei nomi in `IConfigurationBuilder`.

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

Il servizio ASP.NET Core può ora accedere le impostazioni di configurazione di Service Fabric, proprio come qualsiasi altra impostazione di applicazione. Ad esempio, è possibile usare il modello di opzioni per caricare le impostazioni in oggetti fortemente tipizzati.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Mapping delle chiavi predefinita
Per impostazione predefinita, il provider di configurazione di Service Fabric include il nome del pacchetto, nome della sezione e nome della proprietà. Insieme, formano la chiave di configurazione di ASP.NET Core, come indicato di seguito:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Ad esempio, se si dispone di un pacchetto di configurazione denominato `MyConfigPackage` con il contenuto seguente, quindi il valore di configurazione saranno disponibile in ASP.NET Core `IConfiguration` tramite *MyConfigPackage:MyConfigSection:MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Opzioni di configurazione di Service Fabric
Il provider di configurazione di Service Fabric supporta anche `ServiceFabricConfigurationOptions` per modificare il comportamento predefinito di mapping delle chiavi.

#### <a name="encrypted-settings"></a>Impostazioni crittografate
Service Fabric supporta le impostazioni crittografate, come il provider di configurazione di Service Fabric. Le impostazioni crittografate non vengono decrittografate ad ASP.NET Core `IConfiguration` per impostazione predefinita. I valori crittografati sono archiviati. Ma se si desidera decrittografare il valore da archiviare in ASP.NET Core IConfiguration, è possibile impostare il *DecryptValue* flag su false nella `AddServiceFabricConfiguration` estensione, come indicato di seguito:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = true); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Più pacchetti di configurazione
Service Fabric supporta più pacchetti di configurazione. Per impostazione predefinita, il nome del pacchetto è incluso nella chiave di configurazione. Ma è possibile impostare il `IncludePackageName` flag su false, come indicato di seguito:
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
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Mapping delle chiavi personalizzato, estrazione di valore e il popolamento dei dati
Il provider di configurazione di Service Fabric supporta anche scenari più avanzati per personalizzare il mapping delle chiavi con `ExtractKeyFunc` e i valori con custom-extract `ExtractValueFunc`. È anche possibile modificare l'intero processo di popolamento di dati di configurazione di Service Fabric alla configurazione di ASP.NET Core usando `ConfigAction`.

Gli esempi seguenti illustrano come usare `ConfigAction` per personalizzare il popolamento dei dati:
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
Il provider di configurazione di Service Fabric supporta anche gli aggiornamenti della configurazione. È possibile usare ASP.NET Core `IOptionsMonitor` per ricevere le notifiche di modifica e quindi usare `IOptionsSnapshot` ricaricare i dati di configurazione. Per altre informazioni, vedere [opzioni di ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

Queste opzioni sono supportate per impostazione predefinita. Per abilitare gli aggiornamenti della configurazione è necessaria alcuna ulteriore codice.

## <a name="scenarios-and-configurations"></a>Scenari e configurazioni
Questa sezione vengono fornite la combinazione di server web, configurazione delle porte, opzioni di integrazione di Service Fabric e altre impostazioni, che è consigliabile risolvere gli scenari seguenti:
 - Servizi ASP.NET Core senza stato esposti all'esterno
 - Servizi di ASP.NET Core senza stati solo interno
 - Servizi di ASP.NET Core con stati solo interno

Un' **esposto all'esterno del servizio** è quello che espone un endpoint che viene chiamato dall'esterno del cluster, in genere tramite un servizio di bilanciamento del carico.

Un' **solo interno** è un endpoint di cui viene chiamato solo all'interno del cluster servizio.

> [!NOTE]
> Gli endpoint di servizio con stato in genere non devono essere esposte a internet. I cluster protetti da servizi di bilanciamento del carico che non sono consapevoli di risoluzione del servizio Service Fabric, ad esempio Azure Load Balancer, sarà possibile esporre i servizi con stati. Ciò avviene perché il servizio di bilanciamento del carico non sarà in grado di individuare e instradare il traffico per la replica del servizio con stato appropriata. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Servizi ASP.NET Core senza stato esposti all'esterno
Kestrel è il server web consigliato per i servizi front-end che espongono endpoint HTTP esterno connesso a internet. In Windows, HTTP. sys può fornire funzionalità di condivisione delle porte, che consente di ospitare più servizi web sullo stesso set di nodi usando la stessa porta. In questo scenario, i servizi web si differenziano per il nome host o il percorso, senza basarsi su un proxy front-end o un gateway per implementare un routing HTTP.
 
Quando è esposto a internet, un servizio senza stato deve usare un endpoint noto e stabile raggiungibile tramite un servizio di bilanciamento del carico. È possibile fornire questo URL agli utenti dell'applicazione. È consigliabile la configurazione seguente:

|  |  | **Note** |
| --- | --- | --- |
| Server Web | Kestrel | Kestrel è il server web preferite, perché è supportato in Windows e Linux. |
| Configurazione delle porte | Statica | È necessario configurare una porta statica nota nella configurazione `Endpoints` di ServiceManifest.xml, ad esempio 80 per HTTP o 443 per HTTPS. |
| ServiceFabricIntegrationOptions | Nessuna | Usare il `ServiceFabricIntegrationOptions.None` opzione quando si configura il middleware di integrazione di Service Fabric, in modo che il servizio non tenta di convalidare le richieste in ingresso per un identificatore univoco. Gli utenti esterni dell'applicazione non sanno le informazioni di identificazione univoche che usa il middleware. |
| Conteggio istanze | -1 | In casi d'uso tipici, il numero di istanze deve essere impostato su *-1*. Questa operazione viene eseguita in modo che un'istanza è disponibile in tutti i nodi che ricevono il traffico da un servizio di bilanciamento del carico. |

Se più servizi esposti all'esterno condividono lo stesso set di nodi, è possibile usare HTTP. sys con un percorso URL univoco ma stabile. È possibile farlo modificando l'URL specificato durante la configurazione di IWebHost. Si noti che questo vale per HTTP. sys solo.

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
| Server Web | Kestrel | Sebbene sia possibile usare HTTP. sys per i servizi interni senza stati, Kestrel è il miglior server per consentire più istanze del servizio condividere un host.  |
| Configurazione delle porte | assegnate in modo dinamico | Più repliche di un servizio con stato possono condividere un processo host o un sistema operativo host e richiedono quindi porte univoche. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Con l'assegnazione dinamica delle porte, questa impostazione evita il problema di errata identificazione descritto in precedenza. |
| InstanceCount | qualsiasi | Il numero di istanze può essere impostato su qualsiasi valore necessario per il funzionamento del servizio. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Servizio ASP.NET Core con stato solo interno
I servizi con stato che vengono chiamati solo dall'interno del cluster devono usare porte assegnate dinamicamente per assicurare la cooperazione tra più servizi. È consigliabile la configurazione seguente:

|  |  | **Note** |
| --- | --- | --- |
| Server Web | Kestrel | Il `HttpSysCommunicationListener` non è concepito per i servizi con stato in cui le repliche condividono un processo host. |
| Configurazione delle porte | assegnate in modo dinamico | Più repliche di un servizio con stato possono condividere un processo host o un sistema operativo host e richiedono quindi porte univoche. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Con l'assegnazione dinamica delle porte, questa impostazione evita il problema di errata identificazione descritto in precedenza. |

## <a name="next-steps"></a>Passaggi successivi
[Debug dell'applicazione di Service Fabric mediante Visual Studio](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
