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
ms.openlocfilehash: 5a4b7514005da9e9a998dba014fa0ea6c014397a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59268518"
---
# <a name="aspnet-core-in-service-fabric-reliable-services"></a>ASP.NET Core in Reliable Services di Service Fabric

ASP.NET Core è un nuovo framework open source e multipiattaforma per la compilazione di applicazioni Internet moderne basate sul cloud, ad esempio app Web, app per IoT e back-end per dispositivi mobili. 

Questo articolo è una guida dettagliata per l'hosting dei servizi ASP.NET Core in Reliable Services di Service Fabric mediante il set di pacchetti NuGet **Microsoft.ServiceFabric.AspNetCore.** \*.

Per un'esercitazione introduttiva su ASP.NET Core in Service Fabric e istruzioni per la configurazione dell'ambiente di sviluppo, vedere [Creare un'applicazione .NET](service-fabric-tutorial-create-dotnet-app.md).

La parte restante di questo articolo presuppone che si conosca il funzionamento di ASP.NET Core. In caso contrario è consigliabile vedere [ASP.NET Core fundamentals overview](https://docs.microsoft.com/aspnet/core/fundamentals/index) (Panoramica sulle nozioni fondamentali di ASP.NET Core).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core nell'ambiente Service Fabric

Le app ASP.NET Core e Service Fabric possono essere eseguite in .NET Core, così come .NET Framework completa. ASP.NET Core può essere usato in due modi diversi in Service Fabric:
 - **Ospitato come eseguibile guest**. Questa modalità viene usata principalmente per eseguire applicazioni ASP.NET Core esistenti in Service Fabric senza apportare modifiche al codice.
 - **Eseguito in un servizio Reliable Services**. Questa modalità offre una migliore integrazione con il runtime di Service Fabric e consente servizi ASP.NET Core con stato.

Il resto di questo articolo illustra come usare ASP.NET Core in un servizio Reliable Services usando i componenti di integrazione ASP.NET Core forniti con Service Fabric SDK. 

## <a name="service-fabric-service-hosting"></a>Hosting di servizi di Service Fabric

In Service Fabric, una o più istanze e/o repliche del servizio vengono eseguite in un *processo host del servizio*, un file eseguibile che esegue il codice del servizio. L'autore del servizio è il proprietario del processo host del servizio, che verrà attivato e monitorato automaticamente da Service Fabric.

La versione tradizionale di ASP.NET, fino a MVC 5, è strettamente associata a IIS tramite System.Web.dll. ASP.NET Core fornisce una separazione tra il server Web e l'applicazione Web. Ciò consente la portabilità delle applicazioni Web tra server Web diversi e il *self-hosting* dei server Web, vale a dire che è possibile avviare un server Web nel proprio processo, invece di un processo gestito da software del server Web dedicato, ad esempio IIS. 

Per combinare un servizio di Service Fabric e ASP.NET, come eseguibile guest o servizio Reliable Services, è necessario poter avviare ASP.NET all'interno del processo host del servizio. Il self-hosting di ASP.NET Core consente di eseguire questa operazione.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hosting di ASP.NET Core in un servizio Reliable Services
Le applicazioni ASP.NET Core con self-hosting creano in genere un WebHost in un punto di ingresso dell'applicazione, ad esempio il metodo `static void Main()` in `Program.cs`. In questo caso, il ciclo di vita del WebHost è associato al ciclo di vita del processo.

![Hosting di ASP.NET Core in un processo][0]

Il punto di ingresso dell'applicazione non è tuttavia la posizione corretta per creare un WebHost in un servizio Reliable Services, perché il punto di ingresso dell'applicazione viene usato solo per registrare un tipo di servizio con il runtime di Service Fabric per poter creare istanze di quel tipo di servizio. Il WebHost deve essere creato in un servizio Reliable Services. Nel processo host del servizio, le istanze e/o le repliche del servizio possono attraversare più cicli di vita. 

Un'istanza di Reliable Services è rappresentata dalla classe del servizio derivante da `StatelessService` o `StatefulService`. Lo stack di comunicazione di un servizio è contenuto in un'implementazione `ICommunicationListener` nella classe del servizio. I pacchetti NuGet `Microsoft.ServiceFabric.AspNetCore.*` contengono implementazioni di `ICommunicationListener` che avviano e gestiscono WebHost ASP.NET Core per Kestrel o HttpSys in un servizio Reliable Services.

![Hosting di ASP.NET Core in un servizio Reliable Services][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ICommunicationListeners ASP.NET Core
Le implementazioni di `ICommunicationListener` per Kestrel e HttpSys nei pacchetti NuGet `Microsoft.ServiceFabric.AspNetCore.*` hanno modelli di uso simili, ma eseguono azioni leggermente specifiche per ogni server Web. 

Entrambi i listener di comunicazione forniscono un costruttore che accetta gli argomenti seguenti:
 - **`ServiceContext serviceContext`**: oggetto `ServiceContext` che contiene informazioni sul servizio in esecuzione.
 - **`string endpointName`**: nome di una configurazione `Endpoint` in ServiceManifest.xml. La differenza principale tra i due listener di comunicazione è questa: HttpSys **richiede** una configurazione `Endpoint`, mentre Kestrel non la richiede.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: espressione lambda implementata in cui viene creato e restituito un elemento `IWebHost`. È così possibile configurare `IWebHost` come si farebbe normalmente in un'applicazione ASP.NET Core. L'espressione lambda fornisce un URL che viene generato automaticamente a seconda delle opzioni di integrazione di Service Fabric usate e della configurazione `Endpoint` specificata. L'URL può quindi essere modificato o usato così com'è per avviare il server Web.

## <a name="service-fabric-integration-middleware"></a>Middleware di integrazione di Service Fabric
Il pacchetto NuGet `Microsoft.ServiceFabric.AspNetCore` include il metodo di estensione `UseServiceFabricIntegration` in `IWebHostBuilder` che aggiunge middleware compatibile con Service Fabric. Questo middleware configura l'elemento `ICommunicationListener` di Kestrel o HttpSys per la registrazione di un URL di servizio univoco con Service Fabric Naming Service e quindi convalida le richieste dei client per assicurare che i client si connettano al servizio corretto. Ciò è necessario in un ambiente host condiviso, ad esempio Service Fabric, in cui più applicazioni Web possono essere eseguite nello stesso computer fisico o nella stessa macchina virtuale ma non usano nomi host univoci, per impedire ai client di connettersi erroneamente al servizio sbagliato. Questo scenario è descritto più dettagliatamente nella sezione successiva.

### <a name="a-case-of-mistaken-identity"></a>Un caso di identità errata
Le repliche del servizio, indipendentemente dal protocollo, sono in ascolto in una combinazione IP:porta univoca. Quando è in ascolto su un endpoint IP:porta, la replica del servizio segnala l'indirizzo dell'endpoint a Service Fabric Naming Service, dove può essere individuato dai client o da altri servizi. Se i servizi usano porte delle applicazioni assegnate dinamicamente, una replica del servizio può usare per coincidenza lo stesso endpoint IP:porta di un altro servizio che si trovava precedentemente nello stesso computer fisico o nella stessa macchina virtuale. In questo modo un client può connettersi per errore al servizio sbagliato. Questa situazione può verificarsi in presenza di questa sequenza di eventi:

 1. Il servizio A è in ascolto in 10.0.0.1:30000 su HTTP. 
 2. Il client risolve il servizio A e ottiene l'indirizzo 10.0.0.1:30000
 3. Il servizio A si sposta in un nodo diverso.
 4. Il servizio B si trova in 10.0.0.1 e usa per coincidenza la stessa porta 30000.
 5. Il client prova a connettersi al servizio A con l'indirizzo 10.0.0.1:30000 memorizzato nella cache.
 6. Il client è ora connesso al servizio B e non rileva di essersi connesso al servizio sbagliato.

Ciò può causare bug in momenti casuali che possono essere difficili da diagnosticare. 

### <a name="using-unique-service-urls"></a>Uso di URL di servizio univoci
Per evitare questo problema, i servizi possono inviare un endpoint al servizio Naming con un identificatore univoco e quindi convalidare tale identificatore univoco durante le richieste client. Si tratta di un'azione cooperativa tra servizi in un ambiente attendibile di tenant non ostili. Non offre l'autenticazione sicura dei servizi in un ambiente di tenant ostili.

In un ambiente attendibile, il middleware aggiunto dal metodo `UseServiceFabricIntegration` accoda automaticamente un identificatore univoco all'indirizzo inviato al servizio Naming e convalida l'identificatore a ogni richiesta. Se l'identificatore non corrisponde, il middleware restituisce immediatamente una risposta HTTP 410 - Non più disponibile.

I servizi che usano una porta assegnata dinamicamente devono usare questo middleware.

I servizi che usano una porta fissa univoca non hanno questo problema in un ambiente collaborativo. Una porta fissa univoca viene in genere usata per i servizi esterni che richiedono una porta nota per la connessione delle applicazioni client. La maggior parte delle applicazioni Web per Internet, ad esempio, usa la porta 80 o 443 per connessioni tramite Web browser. In questo caso, l'identificatore univoco non deve essere abilitato.

Il diagramma seguente illustra il flusso della richiesta con il middleware abilitato:

![Integrazione ASP.NET Core di Service Fabric][2]

Le implementazioni di `ICommunicationListener` di Kestrel e HttpSys usano questo meccanismo nello stesso identico modo. Anche se HttpSys può differenziare internamente le richieste in base a percorsi URL univoci usando la funzionalità di condivisione delle porte *http.sys* sottostante, questa funzionalità *non* viene usata dall'implementazione `ICommunicationListener` di HttpSys perché provocherebbe i codici di stato di errore HTTP 503 e HTTP 404 nello scenario descritto in precedenza. Ciò rende a sua volta difficile per i client determinare la finalità dell'errore, perché i codici HTTP 503 e HTTP 404 sono già comunemente usati per indicare altri errori. Le implementazioni di `ICommunicationListener` di Kestrel e HttpSys vengono quindi standardizzate con il middleware fornito dal metodo di estensione `UseServiceFabricIntegration`, in modo che i client debbano solo eseguire nuovamente la risoluzione dell'endpoint del servizio nelle risposte HTTP 410.

## <a name="httpsys-in-reliable-services"></a>HttpSys in Reliable Services
HttpSys può essere usato in un servizio Reliable Services importando il pacchetto NuGet **Microsoft.ServiceFabric.AspNetCore.HttpSys**. Questo pacchetto contiene `HttpSysCommunicationListener`, un'implementazione di `ICommunicationListener`, che consente di creare un WebHost ASP.NET Core all'interno di un servizio Reliable Services usando HttpSys come server Web.

HttpSys si basa sull'[API server HTTP di Windows](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx), che usa il driver del kernel *http.sys* usato da IIS per elaborare le richieste HTTP e indirizzarle ai processi che eseguono le applicazioni Web. Ciò consente a più processi nello stesso computer fisico o nella stessa macchina virtuale di ospitare applicazioni Web sulla stessa porta, identificate senza ambiguità dal nome host o dal percorso URL univoco. Queste funzionalità sono utili in Service Fabric per ospitare più siti Web nello stesso cluster.

>[!NOTE]
>L'implementazione di HttpSys funziona solo sulla piattaforma Windows.

Il diagramma seguente illustra il modo in cui HttpSys usa il driver del kernel *http.sys* in Windows per la condivisione delle porte:

![http.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HttpSys in un servizio senza stato
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

### <a name="httpsys-in-a-stateful-service"></a>HttpSys in un servizio con stato

`HttpSysCommunicationListener` attualmente non progettato per l'utilizzo nei servizi con stato a causa di problemi con l'oggetto sottostante *HTTP. sys* funzionalità di condivisione delle porte. Per altre informazioni, vedere la sezione seguente sull'allocazione dinamica delle porte con HttpSys. Per i servizi con stato, Kestrel è il server Web consigliato.

### <a name="endpoint-configuration"></a>Configurazione dell'endpoint

È necessaria una configurazione `Endpoint` per i server Web che usano l'API server HTTP di Windows, tra cui HttpSys. I server Web che usano l'API server HTTP di Windows devono prima riservare i rispettivi URL con *http.sys*. Questa operazione viene in genere eseguita con lo strumento [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx). Questa azione richiede privilegi elevati che i servizi non hanno per impostazione predefinita. Le opzioni "http" o "https" per la proprietà `Protocol` della configurazione `Endpoint` in *ServiceManifest.xml* vengono usate specificamente per indicare al runtime di Service Fabric di registrare un URL con *http.sys* per conto dell'utente con il prefisso URL [*con caratteri jolly complessi*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx).

Per riservare ad esempio `http://+:80` per un servizio, è necessario usare la configurazione seguente in ServiceManifest.xml:

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

#### <a name="use-httpsys-with-a-static-port"></a>Usare HttpSys con una porta statica
Per usare una porta statica con HttpSys, specificare il numero della porta nella configurazione `Endpoint`:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Usare HttpSys con una porta dinamica
Per usare una porta assegnata dinamicamente con HttpSys, omettere la proprietà `Port` nella configurazione `Endpoint`:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Una porta dinamica allocata da una configurazione `Endpoint` fornisce una sola porta *per ogni processo host*. Il modello di hosting corrente di Service Fabric consente l'hosting di più istanze e/o repliche del servizio nello stesso processo, vale a dire che ognuna condivide la stessa porta, se allocata tramite la configurazione `Endpoint`. Più istanze di HttpSys possono condividere una porta usando la funzionalità di condivisione delle porte *http.sys* sottostante, ma questa funzionalità non è supportata da `HttpSysCommunicationListener` a causa dei problemi che provoca per le richieste dei client. Per l'uso delle porte dinamiche, Kestrel è il server Web consigliato.

## <a name="kestrel-in-reliable-services"></a>Kestrel in Reliable Services
Kestrel può essere usato in un servizio Reliable Services importando il pacchetto NuGet **Microsoft.ServiceFabric.AspNetCore.Kestrel**. Questo pacchetto contiene `KestrelCommunicationListener`, un'implementazione di `ICommunicationListener`, che consente di creare un WebHost ASP.NET Core all'interno di un servizio Reliable Services usando Kestrel come server Web.

Kestrel è che un server Web multipiattaforma per ASP.NET Core basato su libuv, una libreria I/O asincrona multipiattaforma. A differenza di HttpSys, Kestrel non usa una gestione centralizzata dell'endpoint come *http.sys*. A differenza di HttpSys, Kestrel non supporta la condivisione delle porte tra più processi. Ogni istanza di Kestrel deve usare una porta univoca.

![kestrel][4]

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

In questo esempio viene fornita un'istanza singleton di `IReliableStateManager` al contenitore di inserimento delle dipendenze WebHost. Questa operazione non è strettamente necessaria, ma consente di usare `IReliableStateManager` e Reliable Collections nei metodi di azione del controller MVC.

**Non** viene fornito un nome di configurazione `Endpoint` a `KestrelCommunicationListener` in un servizio con stato, come spiegato più dettagliatamente nella sezione seguente.

### <a name="configure-kestrel-to-use-https"></a>Configurare Kestrel per l'uso di HTTPS
Quando si abilita HTTPS con Kestrel nel servizio, è necessario impostare diverse opzioni di ascolto.  Aggiornare `ServiceInstanceListener` perché usi un endpoint EndpointHttps e sia in ascolto su una porta specifica (ad esempio la porta 443). Quando si configura l'host Web per l'uso di un server Kestrel, è necessario configurare Kestrel per l'ascolto di indirizzi IPv6 su tutte le interfacce di rete: 

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

Per un'esercitazione in cui venga usato un esempio completo, vedere [Configurare Kestrel per l'uso di HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Configurazione dell'endpoint
Non è necessaria una configurazione `Endpoint` per usare Kestrel. 

Kestrel è un server Web autonomo semplice e a differenza di HttpSys (o HttpListener) non necessita di una configurazione `Endpoint` in *ServiceManifest.xml* perché non richiede la registrazione dell'URL prima dell'avvio. 

#### <a name="use-kestrel-with-a-static-port"></a>Usare Kestrel con una porta statica
È possibile definire una porta statica nella configurazione `Endpoint` di ServiceManifest.xml per l'uso con Kestrel. Anche se non è strettamente necessaria, questa operazione offre due vantaggi potenziali:
 1. Se la porta non rientra nell'intervallo di porte dell'applicazione, verrà aperta da Service Fabric nel firewall del sistema operativo.
 2. L'URL fornito all'utente tramite `KestrelCommunicationListener` userà questa porta.

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

Se non viene usata una configurazione `Endpoint`, omettere il nome nel costruttore `KestrelCommunicationListener`. In questo caso verrà usata una porta dinamica. Per altre informazioni, vedere la sezione seguente.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Usare Kestrel con una porta dinamica
Kestrel non può usare l'assegnazione automatica delle porte della configurazione `Endpoint` in ServiceManifest.xml, perché l'assegnazione automatica delle porte di una configurazione `Endpoint` assegna una porta univoca per ogni *processo host* e un singolo processo host può contenere più istanze di Kestrel. Poiché Kestrel non supporta la condivisione delle porte, questa modalità di assegnazione non funziona perché ogni istanza di Kestrel deve essere aperta in una porta univoca.

Per usare l'assegnazione dinamica delle porte con Kestrel omettere del tutto la configurazione `Endpoint` in ServiceManifest.xml e non passare un nome endpoint al costruttore `KestrelCommunicationListener`:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

In questa configurazione, `KestrelCommunicationListener` selezionerà automaticamente una porta non usata dall'intervallo di porte dell'applicazione.

## <a name="service-fabric-configuration-provider"></a>Provider di configurazione di Service Fabric
Configurazione delle App in ASP.NET Core si basa sulla coppia chiave-valore stabilito dal provider di configurazione, leggere [configurazione in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/) conoscere più in generale configurazione supporto di ASP.NET Core.

Questa sezione viene descritto il Provider di configurazione di Service Fabric per l'integrazione con la configurazione di ASP.NET Core tramite l'importazione di `Microsoft.ServiceFabric.AspNetCore.Configuration` pacchetto NuGet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>Estensioni di avvio AddServiceFabricConfiguration
Dopo aver importazione `Microsoft.ServiceFabric.AspNetCore.Configuration` pacchetto NuGet, è necessario registrare l'origine di configurazione di Service Fabric con l'API di configurazione ASP.NET Core da **AddServiceFabricConfiguration** estensioni in `Microsoft.ServiceFabric.AspNetCore.Configuration` dello spazio dei nomi contro `IConfigurationBuilder`

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

Il servizio ASP.NET Core può ora accedere le impostazioni di configurazione di Service Fabric come qualsiasi altra impostazione di applicazione. Ad esempio, è possibile usare il modello di opzioni per caricare le impostazioni in oggetti fortemente tipizzati.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Chiave predefinita di Mapping
Per impostazione predefinita, il provider di configurazione di Service Fabric include nome del pacchetto, nome della sezione e nome della proprietà tra loro per formare la configurazione di asp.net core usando la seguente funzione della chiave:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Ad esempio, se si dispone di un pacchetto di configurazione denominato `MyConfigPackage` con contenuto, riportato di seguito quindi il valore di configurazione saranno disponibile in ASP.NET Core `IConfiguration` tramite una chiave *MyConfigPackage:MyConfigSection:MyParameter*
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Opzioni di configurazione di Service Fabric
Provider di configurazione di Service Fabric supporta anche `ServiceFabricConfigurationOptions` per modificare il comportamento predefinito di mapping delle chiavi.

#### <a name="encrypted-settings"></a>Impostazioni crittografate
Service Fabric supporta per crittografare le impostazioni, Provider di configurazione di Service Fabric supporta anche questo. Per seguire sicura dal principio per impostazione predefinita, il descrypted are't le impostazioni crittografate per impostazione predefinita per ASP.NET Core `IConfiguration`, il valore crittografato viene archiviato. Tuttavia, se si desidera decrittografare il valore da archiviare in ASP.NET Core IConfiguration è possibile impostare flag DecryptValue su false nella `AddServiceFabricConfiguration` estensioni come indicato di seguito:

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
Service Fabric supporta più pacchetti di configurazione. Per impostazione predefinita, il nome del pacchetto è incluso nella configurazione della chiave. È possibile impostare il `IncludePackageName` flag per modificare il comportamento predefinito.
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
Oltre di sopra i 2 flag per modificare il comportamento predefinito, Provider di configurazione di Service Fabric supporta anche scenari più avanzati su custom il mapping delle chiavi tramite `ExtractKeyFunc` ed estrarre i valori tramite su custom `ExtractValueFunc`. È anche possibile modificare l'intero processo per inserire i dati dalla configurazione di Service Fabric alla configurazione di ASP.NET Core tramite `ConfigAction`.

Negli esempi seguenti per usare `ConfigAction` per personalizzare il popolamento dei dati.
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
### <a name="configuration-update"></a>Aggiornamento della configurazione
Provider di configurazione di Service Fabric supporta anche l'aggiornamento della configurazione e consente di usare ASP.NET Core `IOptionsMonitor` per ricevere le notifiche di modifica e anche con `IOptionsSnapshot` ricaricare i dati di configurazione. Per altre informazioni, vedere [ASP.NET Core opzioni](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options).

Questo è supportato per impostazione predefinita e alcuna ulteriore codice sono necessari per abilitare l'aggiornamento della configurazione.

## <a name="scenarios-and-configurations"></a>Scenari e configurazioni
Questa sezione descrive gli scenari seguenti e offre la combinazione consigliata di server Web, configurazione delle porte, opzioni di integrazione di Service Fabric e altre impostazioni per ottenere un servizio che funzioni correttamente:
 - Servizio ASP.NET Core senza stato esposto all'esterno
 - Servizio ASP.NET Core senza stato solo interno
 - Servizio ASP.NET Core con stato solo interno

Un servizio **esposto all'esterno** è un servizio che espone un endpoint raggiungibile dall'esterno del cluster, in genere tramite un servizio di bilanciamento del carico.

Un servizio **solo interno** è un servizio il cui endpoint è raggiungibile solo dall'interno del cluster.

> [!NOTE]
> Gli endpoint di servizio con stato non devono essere in genere esposti a Internet. I cluster che si trovano dietro a servizi di bilanciamento del carico che non rilevano la risoluzione del servizio di Service Fabric, ad esempio Azure Load Balancer, non possono esporre i servizi con stato perché il servizio di bilanciamento del carico non può individuare e instradare il traffico alla replica del servizio con stato appropriata. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Servizi ASP.NET Core senza stato esposti all'esterno
Kestrel è il server Web consigliato per i servizi front-end che espongono endpoint HTTP Internet esterni. In Windows HttpSys può essere usato per fornire la funzionalità di condivisione delle porte, che consente di ospitare più servizi Web sullo stesso set di nodi tramite la stessa porta, dove ogni servizio Web è differenziato per nome host o percorso senza basarsi su un proxy o gateway front-end per implementare un routing HTTP.
 
Quando è esposto a Internet, un servizio senza stato deve usare un endpoint noto e stabile raggiungibile tramite un servizio di bilanciamento del carico. Si tratta dell'URL che verrà fornito agli utenti dell'applicazione. È consigliabile la configurazione seguente:

|  |  | **Note** |
| --- | --- | --- |
| Server Web | Kestrel | Kestrel è il server Web preferito perché è supportato in Windows e Linux. |
| Configurazione delle porte | Statica | È necessario configurare una porta statica nota nella configurazione `Endpoints` di ServiceManifest.xml, ad esempio 80 per HTTP o 443 per HTTPS. |
| ServiceFabricIntegrationOptions | Nessuna | È necessario usare l'opzione `ServiceFabricIntegrationOptions.None` quando si configura il middleware di integrazione di Service Fabric, in modo che il servizio non provi a convalidare le richieste in ingresso per un identificatore univoco. Gli utenti esterni dell'applicazione non saranno a conoscenza delle informazioni di identificazione univoche usate dal middleware. |
| Conteggio istanze | -1 | In casi d'uso tipici, l'impostazione del numero di istanze deve essere "-1" in modo che un'istanza sia disponibile in tutti i nodi che ricevono il traffico da un servizio di bilanciamento del carico. |

Se più servizi esposti all'esterno condividono lo stesso set di nodi, HttpSys può essere usato con un percorso URL univoco ma stabile, modificando l'URL specificato durante la configurazione di IWebHost. Ciò è valido solo per HttpSys.

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
| Server Web | Kestrel | Anche se è possibile usare HttpSys per i servizi interni senza stato, Kestrel è il server consigliato per consentire la condivisione di un host da parte di più istanze del servizio.  |
| Configurazione delle porte | assegnate in modo dinamico | Più repliche di un servizio con stato possono condividere un processo host o un sistema operativo host e richiedono quindi porte univoche. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Con l'assegnazione dinamica delle porte, questa impostazione evita il problema di errata identificazione descritto in precedenza. |
| InstanceCount | qualsiasi | Il numero di istanze può essere impostato su qualsiasi valore necessario per il funzionamento del servizio. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Servizio ASP.NET Core con stato solo interno
I servizi con stato che vengono chiamati solo dall'interno del cluster devono usare porte assegnate dinamicamente per assicurare la cooperazione tra più servizi. È consigliabile la configurazione seguente:

|  |  | **Note** |
| --- | --- | --- |
| Server Web | Kestrel | `HttpSysCommunicationListener` non può essere usato dai servizi con stato in cui le repliche condividono un processo host. |
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
