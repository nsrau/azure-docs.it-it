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
ms.openlocfilehash: 9648307bb7278f36686d8a53be90c2d9ef7159e1
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69016702"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Core in Azure Service Fabric Reliable Services

ASP.NET Core è un framework open source e multipiattaforma. Questo Framework è progettato per la creazione di applicazioni basate su cloud e connesse a Internet, ad esempio app Web, app per le cose e back-end per dispositivi mobili.

Questo articolo è una guida dettagliata per l'hosting di servizi ASP.NET Core in Service Fabric Reliable Services usando **Microsoft. ServiceFabric. AspNetCore.** set di pacchetti NuGet.

Per un'esercitazione introduttiva su ASP.NET Core in Service Fabric e istruzioni su come configurare l'ambiente di sviluppo, [vedere Esercitazione: Creare e distribuire un'applicazione con un servizio front-end API Web ASP.NET Core e un servizio](service-fabric-tutorial-create-dotnet-app.md)back-end con stato.

Il resto di questo articolo presuppone che si abbia già familiarità con ASP.NET Core. In caso contrario, leggere l' [ASP.NET Core nozioni fondamentali](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core nell'ambiente Service Fabric

Le app ASP.NET Core e Service Fabric possono essere eseguite in .NET Core o .NET Framework completo. È possibile utilizzare ASP.NET Core in due modi diversi in Service Fabric:
 - **Ospitato come eseguibile guest**. Questo metodo viene utilizzato principalmente per eseguire applicazioni ASP.NET Core esistenti in Service Fabric senza modifiche al codice.
 - Viene **eseguito all'interno di un servizio Reliable Services**. In questo modo è possibile integrare meglio il Service Fabric Runtime e consentire i servizi ASP.NET Core con stato.

Il resto di questo articolo illustra come usare ASP.NET Core all'interno di un servizio Reliable Services, tramite i componenti di integrazione ASP.NET Core forniti con l'SDK di Service Fabric.

## <a name="service-fabric-service-hosting"></a>Hosting di servizi di Service Fabric

In Service Fabric, una o più istanze e/o repliche del servizio vengono eseguite in un *processo host del servizio*: un file eseguibile che esegue il codice del servizio. L'autore del servizio è il proprietario del processo host del servizio e Service Fabric lo attiva e lo monitora.

La versione tradizionale di ASP.NET, fino a MVC 5, è strettamente associata a IIS tramite System.Web.dll. ASP.NET Core fornisce una separazione tra il server Web e l'applicazione Web. Questa separazione consente la portabilità delle applicazioni Web tra server Web diversi. Consente inoltre di ospitare i server Web in modo *indipendente*. Ciò significa che è possibile avviare un server Web nel proprio processo, in contrapposizione a un processo di proprietà di un software server Web dedicato, ad esempio IIS.

Per combinare un servizio Service Fabric e ASP.NET, come eseguibile Guest o in un servizio Reliable Services, è necessario essere in grado di avviare ASP.NET all'interno del processo host del servizio. Il self-hosting di ASP.NET Core consente di eseguire questa operazione.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hosting di ASP.NET Core in un servizio Reliable Services
Le applicazioni ASP.NET Core con self-hosting creano in genere un WebHost in un punto di ingresso dell'applicazione, ad esempio il metodo `static void Main()` in `Program.cs`. In questo caso, il ciclo di vita del provider di hosting è associato al ciclo di vita del processo.

![Hosting di ASP.NET Core in un processo][0]

Tuttavia, il punto di ingresso dell'applicazione non è il posto giusto per creare un provider in un servizio Reliable Services. Questo perché il punto di ingresso dell'applicazione viene usato solo per registrare un tipo di servizio con il runtime di Service Fabric, in modo che possa creare istanze del tipo di servizio. Il provider deve essere creato in un servizio Reliable Services. All'interno del processo host del servizio, le istanze del servizio e/o le repliche possono passare attraverso più cicli di vita. 

Un'istanza di Reliable Services è rappresentata dalla classe del servizio derivante da `StatelessService` o `StatefulService`. Lo stack di comunicazione di un servizio è contenuto in un'implementazione `ICommunicationListener` nella classe del servizio. I `Microsoft.ServiceFabric.AspNetCore.*` pacchetti NuGet contengono implementazioni di `ICommunicationListener` che avviano e gestiscono il provider di ASP.NET Core per gheppio o http. sys in un servizio Reliable Services.

![Diagramma per l'hosting di ASP.NET Core in un servizio Reliable Services][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ICommunicationListeners ASP.NET Core
Le `ICommunicationListener` implementazioni per gheppio e http. sys `Microsoft.ServiceFabric.AspNetCore.*` nei pacchetti NuGet hanno modelli di utilizzo simili. Ma eseguono azioni leggermente diverse specifiche per ogni server Web. 

Entrambi i listener di comunicazione forniscono un costruttore che accetta gli argomenti seguenti:
 - **`ServiceContext serviceContext`** : Si tratta dell' `ServiceContext` oggetto che contiene informazioni sul servizio in esecuzione.
 - **`string endpointName`** : Si tratta del nome di una `Endpoint` configurazione in ServiceManifest. XML. Si tratta principalmente dei due listener di comunicazione diversi. HTTP. sys *richiede* una `Endpoint` configurazione, mentre il gheppio non lo fa.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`** : Si tratta di un'espressione lambda implementata, in cui è possibile creare e `IWebHost`restituire un oggetto. Consente di configurare `IWebHost` il modo in cui si farebbe normalmente in un'applicazione ASP.NET Core. L'espressione lambda fornisce un URL generato automaticamente, a seconda delle opzioni di integrazione Service Fabric usate e della `Endpoint` configurazione fornita. È quindi possibile modificare o utilizzare tale URL per avviare il server Web.

## <a name="service-fabric-integration-middleware"></a>Middleware di integrazione di Service Fabric
Il `Microsoft.ServiceFabric.AspNetCore` pacchetto NuGet include il `UseServiceFabricIntegration` metodo di estensione `IWebHostBuilder` su che aggiunge un middleware compatibile con Service Fabric. Questo middleware configura il gheppio o http. sys `ICommunicationListener` per registrare un URL di servizio univoco con il Service Fabric Naming Service. Convalida quindi le richieste client per assicurarsi che i client si connettano al servizio corretto. 

Questo passaggio è necessario per evitare che i client si connettano erroneamente al servizio errato. Ciò è dovuto al fatto che in un ambiente host condiviso, ad esempio Service Fabric, più applicazioni Web possono essere eseguite nella stessa macchina virtuale o fisica, ma non utilizzano nomi host univoci. Questo scenario è descritto più dettagliatamente nella sezione successiva.

### <a name="a-case-of-mistaken-identity"></a>Un caso di identità errata
Le repliche del servizio, indipendentemente dal protocollo, sono in ascolto in una combinazione IP:porta univoca. Una volta che una replica del servizio ha iniziato l'ascolto su un endpoint IP: porta, segnala tale indirizzo endpoint al Naming Service Service Fabric. Qui, i client o altri servizi possono individuarlo. Se i servizi usano porte dell'applicazione assegnate in modo dinamico, una replica del servizio può usare in modo casuale lo stesso endpoint IP: porta di un altro servizio in precedenza nella stessa macchina virtuale o fisica. In questo modo un client può connettersi per errore al servizio sbagliato. Questo scenario può verificarsi se si verifica la sequenza di eventi seguente:

 1. Il servizio A è in ascolto in 10.0.0.1:30000 su HTTP. 
 2. Il client risolve il servizio A e ottiene l'indirizzo 10.0.0.1:30000.
 3. Il servizio A si sposta in un nodo diverso.
 4. Il servizio B si trova in 10.0.0.1 e usa per coincidenza la stessa porta 30000.
 5. Il client prova a connettersi al servizio A con l'indirizzo 10.0.0.1:30000 memorizzato nella cache.
 6. Il client è ora connesso al servizio B, senza rendersene conto che è connesso al servizio errato.

Ciò può causare bug in momenti casuali che possono essere difficili da diagnosticare.

### <a name="using-unique-service-urls"></a>Uso di URL di servizio univoci
Per evitare questi bug, i servizi possono inviare un endpoint al Naming Service con un identificatore univoco e quindi convalidare tale identificatore univoco durante le richieste del client. Si tratta di un'azione cooperativa tra servizi in un ambiente attendibile di tenant non ostili. Non fornisce l'autenticazione del servizio sicura in un ambiente tenant ostile.

In un ambiente attendibile, il middleware aggiunto dal `UseServiceFabricIntegration` metodo aggiunge automaticamente un identificatore univoco all'indirizzo inviato al Naming Service. Convalida tale identificatore per ogni richiesta. Se l'identificatore non corrisponde, il middleware restituisce immediatamente una risposta HTTP 410 Gone.

I servizi che usano una porta assegnata dinamicamente devono usare questo middleware.

I servizi che usano una porta univoca fissa non presentano questo problema in un ambiente cooperativa. Una porta fissa univoca viene in genere usata per i servizi esterni che richiedono una porta nota per la connessione delle applicazioni client. Ad esempio, la maggior parte delle applicazioni Web con connessione Internet utilizzerà la porta 80 o 443 per le connessioni Web browser. In questo caso, l'identificatore univoco non deve essere abilitato.

Il diagramma seguente illustra il flusso della richiesta con il middleware abilitato:

![Integrazione ASP.NET Core di Service Fabric][2]

Entrambe le implementazioni di Gheppio `ICommunicationListener` e http. sys utilizzano questo meccanismo esattamente allo stesso modo. Sebbene http. sys possa differenziare internamente le richieste in base a percorsi URL univoci usando la funzionalità di condivisione delle porte **http. sys** sottostante, questa funzionalità *non* viene usata `ICommunicationListener` dall'implementazione di http. sys. Questo perché restituisce i codici di stato di errore HTTP 503 e HTTP 404 nello scenario descritto in precedenza. Che a sua volta rende difficile per i client determinare lo scopo dell'errore, in quanto HTTP 503 e HTTP 404 vengono comunemente usati per indicare altri errori. 

Pertanto, le `UseServiceFabricIntegration` implementazioni di gheppio e `ICommunicationListener` http. sys standardizzano il middleware fornito dal metodo di estensione. Pertanto, i client devono solo eseguire un'azione di ririsoluzione dell'endpoint del servizio sulle risposte HTTP 410.

## <a name="httpsys-in-reliable-services"></a>HTTP. sys in Reliable Services
È possibile usare HTTP. sys in Reliable Services importando il pacchetto NuGet **Microsoft. ServiceFabric. AspNetCore. HttpSys** . Questo pacchetto contiene `HttpSysCommunicationListener`, un'implementazione di `ICommunicationListener`. `HttpSysCommunicationListener`consente di creare un provider di ASP.NET Core all'interno di un servizio Reliable Services usando HTTP. sys come server Web.

HTTP. sys è basato sull' [API server http di Windows](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Questa API usa il driver del kernel **http. sys** per elaborare le richieste HTTP e instradarle ai processi che eseguono applicazioni Web. Questo consente a più processi nella stessa macchina virtuale o fisica di ospitare applicazioni Web sulla stessa porta, ambiguità in base a un percorso URL univoco o a un nome host. Queste funzionalità sono utili in Service Fabric per ospitare più siti Web nello stesso cluster.

>[!NOTE]
>L'implementazione di HTTP. sys funziona solo sulla piattaforma Windows.

Il diagramma seguente illustra come HTTP. sys usa il driver del kernel **http. sys** in Windows per la condivisione delle porte:

![Diagramma HTTP. sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP. sys in un servizio senza stato
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

### <a name="httpsys-in-a-stateful-service"></a>HTTP. sys in un servizio con stato

`HttpSysCommunicationListener`non è attualmente progettato per l'uso nei servizi con stato a causa di complicazioni con la funzionalità di condivisione delle porte **http. sys** sottostante. Per ulteriori informazioni, vedere la sezione seguente sull'allocazione dinamica delle porte con HTTP. sys. Per i servizi con stato, gheppio è il server Web consigliato.

### <a name="endpoint-configuration"></a>Configurazione dell'endpoint

È `Endpoint` necessaria una configurazione per i server Web che usano l'API server http di Windows, incluso http. sys. I server Web che usano l'API di Windows HTTP Server devono innanzitutto riservare il proprio URL con HTTP. sys. questa operazione viene in genere eseguita con lo strumento [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) . 

Questa azione richiede privilegi elevati che i servizi non hanno per impostazione predefinita. Le opzioni "http" o "https" per la `Protocol` proprietà `Endpoint` della configurazione in ServiceManifest. XML vengono usate in modo specifico per indicare al runtime di Service fabric di registrare un URL con http. sys per conto dell'utente. A tale scopo, viene utilizzato il prefisso URL con [*carattere jolly*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) complesso.

Per riservare `http://+:80` per un servizio, ad esempio, usare la configurazione seguente in ServiceManifest. XML:

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

#### <a name="use-httpsys-with-a-static-port"></a>Utilizzare HTTP. sys con una porta statica
Per usare una porta statica con http. sys, specificare il numero di porta nella `Endpoint` configurazione:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Usare HTTP. sys con una porta dinamica
Per usare una porta assegnata dinamicamente con http. sys, omettere la `Port` proprietà `Endpoint` nella configurazione:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Una porta dinamica allocata da `Endpoint` una configurazione fornisce solo una porta *per ogni processo host*. Il modello di hosting Service Fabric corrente consente l'hosting di più istanze e/o repliche del servizio nello stesso processo. Ciò significa che ognuno condivide la stessa porta quando viene allocata tramite `Endpoint` la configurazione. Più istanze **http. sys** possono condividere una porta usando la funzionalità di condivisione delle porte **http. sys** sottostante. Ma non è supportato da `HttpSysCommunicationListener` a causa delle complicazioni introdotte per le richieste dei client. Per l'utilizzo della porta dinamica, gheppio è il server Web consigliato.

## <a name="kestrel-in-reliable-services"></a>Kestrel in Reliable Services
È possibile usare gheppio in Reliable Services importando il pacchetto NuGet **Microsoft. ServiceFabric. AspNetCore. gheppio** . Questo pacchetto contiene `KestrelCommunicationListener`, un'implementazione di `ICommunicationListener`. `KestrelCommunicationListener`consente di creare un provider di ASP.NET Core all'interno di un servizio Reliable Services usando gheppio come server Web.

Kestrel è che un server Web multipiattaforma per ASP.NET Core basato su libuv, una libreria I/O asincrona multipiattaforma. Diversamente da HTTP. sys, gheppio non usa una gestione centralizzata degli endpoint. Diversamente da HTTP. sys, gheppio non supporta la condivisione delle porte tra più processi. Ogni istanza di Kestrel deve usare una porta univoca.

![Diagramma gheppio][4]

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

In questo esempio viene fornita un'istanza singleton di `IReliableStateManager` al contenitore di inserimento delle dipendenze WebHost. Questa operazione non è strettamente necessaria, ma consente di usare `IReliableStateManager` e reliable Collections nei metodi di azione del controller MVC.

*Non* viene fornito un nome di configurazione `Endpoint` a `KestrelCommunicationListener` in un servizio con stato, come spiegato più dettagliatamente nella sezione seguente.

### <a name="configure-kestrel-to-use-https"></a>Configurare Kestrel per l'uso di HTTPS
Quando si Abilita HTTPS con gheppio nel servizio, è necessario impostare diverse opzioni di ascolto. Aggiornare per `ServiceInstanceListener` usare un endpoint *EndpointHttps* e restare in ascolto su una porta specifica, ad esempio la porta 443. Quando si configura l'host Web per l'utilizzo del server Web gheppio, è necessario configurare gheppio per l'ascolto degli indirizzi IPv6 su tutte le interfacce di rete: 

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

Per un esempio completo in un'esercitazione, vedere [configurare gheppio per l'uso di HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Configurazione dell'endpoint
Non `Endpoint` è necessaria una configurazione per usare gheppio. 

Gheppio è un semplice server Web autonomo. Diversamente da http. sys (o HttpListener), non è necessaria una `Endpoint` configurazione in ServiceManifest. XML perché non richiede la registrazione dell'URL prima dell'avvio. 

#### <a name="use-kestrel-with-a-static-port"></a>Usare Kestrel con una porta statica
È possibile configurare una porta statica nella configurazione `Endpoint` di ServiceManifest. XML per l'uso con gheppio. Sebbene non sia strettamente necessario, offre due vantaggi potenziali:
 - Se la porta non rientra nell'intervallo di porte dell'applicazione, viene aperta tramite il firewall del sistema operativo Service Fabric.
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

Se ServiceManifest. XML non usa una `Endpoint` configurazione, omettere il nome `KestrelCommunicationListener` nel costruttore. In questo caso, utilizzerà una porta dinamica. Per ulteriori informazioni, vedere la sezione successiva.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Usare Kestrel con una porta dinamica
Il gheppio non può usare l'assegnazione automatica delle `Endpoint` porte dalla configurazione in ServiceManifest. XML. Questo perché l'assegnazione automatica delle porte da `Endpoint` una configurazione assegna una porta univoca per ogni *processo host*e un singolo processo host può contenere più istanze di gheppio. Questa operazione non funziona con gheppio perché non supporta la condivisione delle porte. Ogni istanza di Gheppio deve pertanto essere aperta su una porta univoca.

Per usare l'assegnazione dinamica delle porte con gheppio, `Endpoint` omettere la configurazione in ServiceManifest. XML completamente e non passare il `KestrelCommunicationListener` nome di un endpoint al costruttore, come indicato di seguito:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

In questa configurazione, `KestrelCommunicationListener` selezionerà automaticamente una porta non usata dall'intervallo di porte dell'applicazione.

## <a name="service-fabric-configuration-provider"></a>Provider di configurazione Service Fabric
La configurazione dell'app in ASP.NET Core si basa sulle coppie chiave-valore stabilite dal provider di configurazione. Per ulteriori informazioni sul supporto della configurazione ASP.NET Core generale, vedere la pagina relativa alla [configurazione ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) .

Questa sezione descrive il modo in cui il provider di configurazione Service Fabric si integra con la `Microsoft.ServiceFabric.AspNetCore.Configuration` configurazione ASP.NET Core importando il pacchetto NuGet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>Estensioni di avvio AddServiceFabricConfiguration
Dopo aver importato il `Microsoft.ServiceFabric.AspNetCore.Configuration` pacchetto NuGet, è necessario registrare l'origine di configurazione Service fabric con ASP.NET Core API di configurazione. A tale scopo, controllare le `Microsoft.ServiceFabric.AspNetCore.Configuration` estensioni AddServiceFabricConfiguration nello spazio dei `IConfigurationBuilder`nomi rispetto a.

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

A questo punto il servizio ASP.NET Core può accedere alle impostazioni di configurazione Service Fabric, come per qualsiasi altra impostazione dell'applicazione. Ad esempio, è possibile usare il modello Options per caricare le impostazioni in oggetti fortemente tipizzati.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Mapping della chiave predefinita
Per impostazione predefinita, il provider di configurazione Service Fabric include il nome del pacchetto, il nome della sezione e il nome della proprietà. Insieme, costituiscono la chiave di configurazione ASP.NET Core, come indicato di seguito:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Se, ad esempio, si dispone di un pacchetto `MyConfigPackage` di configurazione denominato con il contenuto seguente, il valore di configurazione sarà disponibile `IConfiguration` in ASP.NET Core tramite *MyConfigPackage: MyConfigSection: parametro*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Opzioni di configurazione Service Fabric
Il provider di configurazione Service Fabric supporta `ServiceFabricConfigurationOptions` anche per modificare il comportamento predefinito del mapping delle chiavi.

#### <a name="encrypted-settings"></a>Impostazioni crittografate
Service Fabric supporta le impostazioni crittografate, così come il provider di configurazione Service Fabric. Le impostazioni crittografate non vengono decrittografate ASP.NET Core `IConfiguration` per impostazione predefinita. Sono invece archiviati i valori crittografati. Tuttavia, se si desidera decrittografare il valore da archiviare in ASP.NET Core IConfiguration, è possibile impostare il flag *DecryptValue* su false `AddServiceFabricConfiguration` nell'estensione, come indicato di seguito:

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
Service Fabric supporta più pacchetti di configurazione. Per impostazione predefinita, il nome del pacchetto è incluso nella chiave di configurazione. È tuttavia possibile impostare il `IncludePackageName` flag su false, come indicato di seguito:
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
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Mapping di chiavi personalizzate, estrazione di valori e popolamento dei dati
Il provider di configurazione Service Fabric supporta anche scenari più avanzati per personalizzare il mapping delle `ExtractKeyFunc` chiavi con ed estrarre in modo personalizzato `ExtractValueFunc`i valori con. È anche possibile modificare l'intero processo di popolamento dei dati dalla configurazione Service Fabric alla configurazione ASP.NET Core tramite `ConfigAction`.

Negli esempi seguenti viene illustrato come utilizzare `ConfigAction` per personalizzare il popolamento dei dati:
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
Il provider di configurazione Service Fabric supporta anche gli aggiornamenti della configurazione. È possibile usare ASP.NET Core `IOptionsMonitor` per ricevere le notifiche di modifica e quindi `IOptionsSnapshot` usare per ricaricare i dati di configurazione. Per ulteriori informazioni, vedere [opzioni ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

Queste opzioni sono supportate per impostazione predefinita. Per abilitare gli aggiornamenti della configurazione non è necessaria alcuna ulteriore codifica.

## <a name="scenarios-and-configurations"></a>Scenari e configurazioni
In questa sezione viene fornita la combinazione di server Web, configurazione delle porte, opzioni di integrazione Service Fabric e impostazioni varie consigliate per risolvere i problemi relativi agli scenari seguenti:
 - Servizi ASP.NET Core senza stato esposti all'esterno
 - Servizi senza stato solo interno ASP.NET Core
 - Servizi con stato di ASP.NET Core solo interno

Un **servizio esposto esternamente** è un servizio che espone un endpoint chiamato dall'esterno del cluster, in genere tramite un servizio di bilanciamento del carico.

Un servizio **solo interno** è un servizio il cui endpoint viene chiamato solo dall'interno del cluster.

> [!NOTE]
> Gli endpoint del servizio con stato in genere non devono essere esposti a Internet. I cluster dietro i servizi di bilanciamento del carico che non sono a conoscenza di Service Fabric risoluzione del servizio, ad esempio Azure Load Balancer, non saranno in grado di esporre i servizi con stato. Questo perché il servizio di bilanciamento del carico non sarà in grado di individuare e instradare il traffico alla replica del servizio con stato appropriato. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Servizi ASP.NET Core senza stato esposti all'esterno
Gheppio è il server Web consigliato per i servizi front-end che espongono endpoint HTTP esterni con connessione Internet. In Windows, HTTP. sys può fornire funzionalità di condivisione delle porte, che consente di ospitare più servizi Web sullo stesso set di nodi tramite la stessa porta. In questo scenario, i servizi Web si differenziano in base al nome host o al percorso, senza basarsi su un proxy o gateway front-end per fornire il routing HTTP.
 
Se esposti a Internet, un servizio senza stato deve usare un endpoint noto e stabile raggiungibile tramite un servizio di bilanciamento del carico. Questo URL verrà fornito agli utenti dell'applicazione. È consigliabile la configurazione seguente:

|  |  | **Note** |
| --- | --- | --- |
| Server Web | Kestrel | Gheppio è il server Web preferito perché è supportato in Windows e Linux. |
| Configurazione delle porte | Statica | È necessario configurare una porta statica nota nella configurazione `Endpoints` di ServiceManifest.xml, ad esempio 80 per HTTP o 443 per HTTPS. |
| ServiceFabricIntegrationOptions | Nessuna | Usare l' `ServiceFabricIntegrationOptions.None` opzione quando si configura Service Fabric middleware di integrazione, in modo che il servizio non tenti di convalidare le richieste in ingresso per un identificatore univoco. Gli utenti esterni dell'applicazione non saranno in grado di riconoscere le informazioni di identificazione univoche utilizzate dal middleware. |
| Conteggio istanze | -1 | Nei casi d'uso tipici, l'impostazione del numero di istanze deve essere impostata su *-1*. Questa operazione viene eseguita in modo che un'istanza sia disponibile in tutti i nodi che ricevono traffico da un servizio di bilanciamento del carico. |

Se più servizi esposti esternamente condividono lo stesso set di nodi, è possibile usare HTTP. sys con un percorso URL univoco ma stabile. A tale scopo, è possibile modificare l'URL fornito durante la configurazione di IWebHost. Si noti che questo vale solo per HTTP. sys.

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
| Server Web | Kestrel | Sebbene sia possibile utilizzare HTTP. sys per i servizi senza stato interno, gheppio è il server migliore per consentire a più istanze del servizio di condividere un host.  |
| Configurazione delle porte | assegnate in modo dinamico | Più repliche di un servizio con stato possono condividere un processo host o un sistema operativo host e quindi dovranno avere porte univoche. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Con l'assegnazione dinamica delle porte, questa impostazione evita il problema di errata identificazione descritto in precedenza. |
| InstanceCount | qualsiasi | Il numero di istanze può essere impostato su qualsiasi valore necessario per il funzionamento del servizio. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Servizio ASP.NET Core con stato solo interno
I servizi con stato che vengono chiamati solo dall'interno del cluster devono usare porte assegnate dinamicamente per assicurare la cooperazione tra più servizi. È consigliabile la configurazione seguente:

|  |  | **Note** |
| --- | --- | --- |
| Server Web | Kestrel | Non `HttpSysCommunicationListener` è progettato per l'uso da parte di servizi con stato in cui le repliche condividono un processo host. |
| Configurazione delle porte | assegnate in modo dinamico | Più repliche di un servizio con stato possono condividere un processo host o un sistema operativo host e quindi dovranno avere porte univoche. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Con l'assegnazione dinamica delle porte, questa impostazione evita il problema di errata identificazione descritto in precedenza. |

## <a name="next-steps"></a>Passaggi successivi
[Debug dell'applicazione di Service Fabric mediante Visual Studio](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
