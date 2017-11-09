---
title: Comunicazione dei servizi con ASP.NET Core | Microsoft Docs
description: Informazioni su come usare ASP.NET Core in Reliable Services con e senza stato.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 05/02/2017
ms.author: vturecek
ms.openlocfilehash: 8ac4d409f7363e8b4ae98be659a627ac8db8d787
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="aspnet-core-in-service-fabric-reliable-services"></a>ASP.NET Core in Reliable Services di Service Fabric

ASP.NET Core è un nuovo framework open source e multipiattaforma per la compilazione di applicazioni Internet moderne basate sul cloud, ad esempio app Web, app per IoT e back-end per dispositivi mobili. 

Questo articolo è una guida dettagliata per l'hosting dei servizi ASP.NET Core in Reliable Services di Service Fabric mediante il set di pacchetti NuGet **Microsoft.ServiceFabric.AspNetCore.***.

Per un'esercitazione introduttiva su ASP.NET Core in Service Fabric e istruzioni per la configurazione dell'ambiente di sviluppo, vedere [Compilare un front-end di servizio Web per l'applicazione tramite ASP.NET Core](service-fabric-add-a-web-frontend.md).

La parte restante di questo articolo presuppone che si conosca il funzionamento di ASP.NET Core. In caso contrario è consigliabile vedere [ASP.NET Core fundamentals overview](https://docs.microsoft.com/aspnet/core/fundamentals/index) (Panoramica sulle nozioni fondamentali di ASP.NET Core).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core nell'ambiente Service Fabric

Anche se le app ASP.NET Core possono essere eseguite in .NET o nella versione completa di .NET Framework, i servizi di Service Fabric possono essere attualmente eseguiti solo nella versione completa di .NET Framework. Ciò significa che quando si compila un servizio di Service Fabric in ASP.NET Core è comunque necessario usare la versione completa di .NET Framework.

ASP.NET Core può essere usato in due modi diversi in Service Fabric:
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

Un'istanza di Reliable Services è rappresentata dalla classe del servizio derivante da `StatelessService` o `StatefulService`. Lo stack di comunicazione di un servizio è contenuto in un'implementazione `ICommunicationListener` nella classe del servizio. I pacchetti NuGet `Microsoft.ServiceFabric.Services.AspNetCore.*` contengono implementazioni di `ICommunicationListener` che avviano e gestiscono WebHost ASP.NET Core per Kestrel o WebListener in un servizio Reliable Services.

![Hosting di ASP.NET Core in un servizio Reliable Services][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ICommunicationListeners ASP.NET Core
Le implementazioni di `ICommunicationListener` per Kestrel e WebListener nei pacchetti NuGet `Microsoft.ServiceFabric.Services.AspNetCore.*` hanno modelli di uso simili, ma eseguono azioni leggermente specifiche per ogni server Web. 

Entrambi i listener di comunicazione forniscono un costruttore che accetta gli argomenti seguenti:
 - **`ServiceContext serviceContext`**: oggetto `ServiceContext` che contiene informazioni sul servizio in esecuzione.
 - **`string endpointName`**: nome di una configurazione `Endpoint` in ServiceManifest.xml. È principalmente qui che i due listener di comunicazione differiscono: WebListener **richiede** una configurazione `Endpoint` che non è invece necessaria per Kestrel.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: espressione lambda implementata in cui viene creato e restituito un elemento `IWebHost`. È così possibile configurare `IWebHost` come si farebbe normalmente in un'applicazione ASP.NET Core. L'espressione lambda fornisce un URL che viene generato automaticamente a seconda delle opzioni di integrazione di Service Fabric usate e della configurazione `Endpoint` specificata. L'URL può quindi essere modificato o usato così com'è per avviare il server Web.

## <a name="service-fabric-integration-middleware"></a>Middleware di integrazione di Service Fabric
Il pacchetto NuGet `Microsoft.ServiceFabric.Services.AspNetCore` include il metodo di estensione `UseServiceFabricIntegration` in `IWebHostBuilder` che aggiunge middleware compatibile con Service Fabric. Questo middleware configura l'elemento `ICommunicationListener` di Kestrel o WebListener per la registrazione di un URL di servizio univoco con Service Fabric Naming Service e quindi convalida le richieste dei client per assicurare che i client si connettano al servizio corretto. Ciò è necessario in un ambiente host condiviso, ad esempio Service Fabric, in cui più applicazioni Web possono essere eseguite nello stesso computer fisico o nella stessa macchina virtuale ma non usano nomi host univoci, per impedire ai client di connettersi erroneamente al servizio sbagliato. Questo scenario è descritto più dettagliatamente nella sezione successiva.

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

Le implementazioni di `ICommunicationListener` di Kestrel e WebListener usano questo meccanismo nello stesso identico modo. Anche se WebListener può differenziare internamente le richieste in base a percorsi URL univoci usando la funzionalità di condivisione delle porte *http.sys* sottostante, questa funzionalità *non* viene usata dall'implementazione `ICommunicationListener` di WebListener perché provocherebbe i codici di stato di errore HTTP 503 e HTTP 404 nello scenario descritto in precedenza. Ciò rende a sua volta molto difficile per i client determinare la finalità dell'errore, perché i codici HTTP 503 e HTTP 404 sono già comunemente usati per indicare altri errori. Le implementazioni di `ICommunicationListener` di Kestrel e WebListener vengono quindi standardizzate con il middleware fornito dal metodo di estensione `UseServiceFabricIntegration`, in modo che i client debbano solo eseguire nuovamente la risoluzione dell'endpoint del servizio nelle risposte HTTP 410.

## <a name="weblistener-in-reliable-services"></a>WebListener in Reliable Services
WebListener può essere usato in un servizio Reliable Services importando il pacchetto NuGet **Microsoft.ServiceFabric.AspNetCore.WebListener**. Questo pacchetto contiene `WebListenerCommunicationListener`, un'implementazione di `ICommunicationListener`, che consente di creare un WebHost ASP.NET Core all'interno di un servizio Reliable Services usando WebListener come server Web.

WebListener si basa sull'[API server HTTP di Windows](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx), che usa il driver del kernel *http.sys* usato da IIS per elaborare le richieste HTTP e indirizzarle ai processi che eseguono le applicazioni Web. Ciò consente a più processi nello stesso computer fisico o nella stessa macchina virtuale di ospitare applicazioni Web sulla stessa porta, identificate senza ambiguità dal nome host o dal percorso URL univoco. Queste funzionalità sono utili in Service Fabric per ospitare più siti Web nello stesso cluster.

Il diagramma seguente illustra il modo in cui WebListener usa il driver del kernel *http.sys* in Windows per la condivisione delle porte:

![http.sys][3]

### <a name="weblistener-in-a-stateless-service"></a>WebListener in un servizio senza stato
Per usare `WebListener` in un servizio senza stato, eseguire l'override del metodo `CreateServiceInstanceListeners` e restituire un'istanza `WebListenerCommunicationListener`:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new WebListenerCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseWebListener()
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

### <a name="weblistener-in-a-stateful-service"></a>WebListener in un servizio con stato

`WebListenerCommunicationListener` non è attualmente progettato per l'uso in servizi con stato, a causa di problemi con la funzionalità di condivisione delle porte *http.sys* sottostante. Per altre informazioni, vedere la sezione seguente sull'allocazione dinamica delle porte con WebListener. Per i servizi con stato, Kestrel è il server Web consigliato.

### <a name="endpoint-configuration"></a>Configurazione dell'endpoint

È necessaria una configurazione `Endpoint` per i server Web che usano l'API server HTTP di Windows, tra cui WebListener. I server Web che usano l'API server HTTP di Windows devono prima riservare i rispettivi URL con *http.sys*. Questa operazione viene in genere eseguita con lo strumento [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx). Questa azione richiede privilegi elevati che i servizi non hanno per impostazione predefinita. Le opzioni "http" o "https" per la proprietà `Protocol` della configurazione `Endpoint` in *ServiceManifest.xml* vengono usate specificamente per indicare al runtime di Service Fabric di registrare un URL con *http.sys* per conto dell'utente con il prefisso URL [*con caratteri jolly complessi*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx).

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

Il nome dell'endpoint deve essere passato al costruttore `WebListenerCommunicationListener`:

```csharp
 new WebListenerCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseWebListener()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-weblistener-with-a-static-port"></a>Usare WebListener con una porta statica
Per usare una porta statica con WebListener, specificare il numero della porta nella configurazione `Endpoint`:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-weblistener-with-a-dynamic-port"></a>Usare WebListener con una porta dinamica
Per usare una porta assegnata dinamicamente con WebListener, omettere la proprietà `Port` nella configurazione `Endpoint`:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Si noti che una porta dinamica allocata da una configurazione `Endpoint` fornisce una sola porta *per ogni processo host*. Il modello di hosting corrente di Service Fabric consente l'hosting di più istanze e/o repliche del servizio nello stesso processo, vale a dire che ognuna condivide la stessa porta, se allocata tramite la configurazione `Endpoint`. Più istanze di WebListener possono condividere una porta usando la funzionalità di condivisione delle porte *http.sys* sottostante, ma questa funzionalità non è supportata da `WebListenerCommunicationListener` a causa dei problemi che provoca per le richieste dei client. Per l'uso delle porte dinamiche, Kestrel è il server Web consigliato.

## <a name="kestrel-in-reliable-services"></a>Kestrel in Reliable Services
Kestrel può essere usato in un servizio Reliable Services importando il pacchetto NuGet **Microsoft.ServiceFabric.AspNetCore.Kestrel**. Questo pacchetto contiene `KestrelCommunicationListener`, un'implementazione di `ICommunicationListener`, che consente di creare un WebHost ASP.NET Core all'interno di un servizio Reliable Services usando Kestrel come server Web.

Kestrel è che un server Web multipiattaforma per ASP.NET Core basato su libuv, una libreria I/O asincrona multipiattaforma. A differenza di WebListener, Kestrel non usa una gestione centralizzata dell'endpoint come *http.sys*. A differenza di WebListener, Kestrel non supporta la condivisione delle porte tra più processi. Ogni istanza di Kestrel deve usare una porta univoca.

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

Si noti che **non** viene fornito un nome di configurazione `Endpoint` a `KestrelCommunicationListener` in un servizio con stato, come spiegato più dettagliatamente nella sezione seguente.

### <a name="endpoint-configuration"></a>Configurazione dell'endpoint
Non è necessaria una configurazione `Endpoint` per usare Kestrel. 

Kestrel è un server Web autonomo semplice e a differenza di WebListener (o HttpListener) non necessita di una configurazione `Endpoint` in *ServiceManifest.xml* perché non richiede la registrazione dell'URL prima dell'avvio. 

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

Per usare l'assegnazione dinamica delle porte con Kestrel è sufficiente omettere del tutto la configurazione `Endpoint` in ServiceManifest.xml e non passare un nome endpoint al costruttore `KestrelCommunicationListener`:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

In questa configurazione, `KestrelCommunicationListener` selezionerà automaticamente una porta non usata dall'intervallo di porte dell'applicazione.

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
WebListener è il server Web consigliato per i servizi front-end che espongono endpoint HTTP Internet esterni in Windows. Offre una migliore protezione dagli attacchi e supporta funzionalità non supportate da Kestrel, ad esempio l'autenticazione di Windows e la condivisione delle porte. 

Kestrel non è attualmente supportato come server perimetrale per Internet. È necessario usare un server proxy inverso, ad esempio IIS o Nginx, per gestire il traffico da Internet pubblico.
 
Quando è esposto a Internet, un servizio senza stato deve usare un endpoint noto e stabile raggiungibile tramite un servizio di bilanciamento del carico. Si tratta dell'URL che verrà fornito agli utenti dell'applicazione. È consigliabile la configurazione seguente:

|  |  | **Note** |
| --- | --- | --- |
| Server Web | WebListener | Se il servizio viene esposto solo a una rete attendibile, ad esempio una rete Intranet, è possibile usare Kestrel. In caso contrario, WebListener è l'opzione preferita. |
| Configurazione delle porte | Statica | È necessario configurare una porta statica nota nella configurazione `Endpoints` di ServiceManifest.xml, ad esempio 80 per HTTP o 443 per HTTPS. |
| ServiceFabricIntegrationOptions | None | È necessario usare l'opzione `ServiceFabricIntegrationOptions.None` quando si configura il middleware di integrazione di Service Fabric, in modo che il servizio non provi a convalidare le richieste in ingresso per un identificatore univoco. Gli utenti esterni dell'applicazione non saranno a conoscenza delle informazioni di identificazione univoche usate dal middleware. |
| Conteggio istanze | -1 | In casi d'uso tipici, l'impostazione del numero di istanze deve essere "-1" in modo che un'istanza sia disponibile in tutti i nodi che ricevono il traffico da un servizio di bilanciamento del carico. |

Se più servizi esposti all'esterno condividono lo stesso set di nodi, usare un percorso URL univoco ma stabile, modificando l'URL specificato durante la configurazione di IWebHost. Si noti che questa operazione è applicabile solo a WebListener.

 ```csharp
 new WebListenerCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseWebListener()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>Servizio ASP.NET Core senza stato solo interno
I servizi senza stato che vengono chiamati solo dall'interno del cluster devono usare URL univoci e porte assegnate dinamicamente per assicurare la cooperazione tra più servizi. È consigliabile la configurazione seguente:

|  |  | **Note** |
| --- | --- | --- |
| Server Web | Kestrel | Anche se è possibile usare WebListener per i servizi interni senza stato, Kestrel è il server consigliato per consentire la condivisione di un host da parte di più istanze del servizio.  |
| Configurazione delle porte | assegnate in modo dinamico | Più repliche di un servizio con stato possono condividere un processo host o un sistema operativo host e richiedono quindi porte univoche. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Con l'assegnazione dinamica delle porte, questa impostazione evita il problema di errata identificazione descritto in precedenza. |
| InstanceCount | qualsiasi | Il numero di istanze può essere impostato su qualsiasi valore necessario per il funzionamento del servizio. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Servizio ASP.NET Core con stato solo interno
I servizi con stato che vengono chiamati solo dall'interno del cluster devono usare porte assegnate dinamicamente per assicurare la cooperazione tra più servizi. È consigliabile la configurazione seguente:

|  |  | **Note** |
| --- | --- | --- |
| Server Web | Kestrel | `WebListenerCommunicationListener` non può essere usato dai servizi con stato in cui le repliche condividono un processo host. |
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
