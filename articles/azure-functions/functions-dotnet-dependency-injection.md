---
title: Usare l'inserimento delle dipendenze in Funzioni di Azure .NET
description: Informazioni su come usare l'inserimento delle dipendenze per la registrazione e l'uso dei servizi nelle funzioni .NET
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: a1ff8e0aedce5d3a6acc9a39084cf0839efdd88e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678447"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Usare l'inserimento delle dipendenze in Funzioni di Azure .NET

Funzioni di Azure supporta il modello di progettazione software di inserimento delle dipendenze (DI), che è una tecnica per ottenere [l'inversione del controllo (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra le classi e le relative dipendenze.

- L'inserimento delle dipendenze in Funzioni di Azure si basa sulle funzionalità di inserimento delle dipendenze di .NET Core.Dependency injection in Azure Functions is built on the .NET Core Dependency Injection features. È consigliabile familiarizzare con [l'inserimento delle dipendenze di .NET Core.Familiarity with .NET Core dependency injection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) is recommended. Esistono differenze nella modalità di override delle dipendenze e nella modalità di lettura dei valori di configurazione con Funzioni di Azure nel piano Consumo.

- Il supporto per l'inserimento delle dipendenze inizia con Funzioni di Azure 2.x.Support for dependency injection begins with Azure Functions 2.x.

## <a name="prerequisites"></a>Prerequisiti

Prima di poter usare l'inserimento delle dipendenze, è necessario installare i pacchetti NuGet seguenti:Before you can use dependency injection, you must install the following NuGet packages:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Pacchetto Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) versione 1.0.28 o successiva

## <a name="register-services"></a>Registrare i servizi

Per registrare i servizi, creare un `IFunctionsHostBuilder` metodo per configurare e aggiungere componenti a un'istanza.  L'host Funzioni di `IFunctionsHostBuilder` Azure crea un'istanza di e la passa direttamente al metodo.

Per registrare il `FunctionsStartup` metodo, aggiungere l'attributo assembly che specifica il nome del tipo utilizzato durante l'avvio.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

### <a name="caveats"></a>Precisazioni

Una serie di passaggi di registrazione eseguiti prima e dopo l'elaborazione della classe di avvio da parte del runtime. Pertanto, tenere presente i seguenti elementi:

- *La classe di avvio è pensata solo per l'installazione e la registrazione.* Evitare di utilizzare i servizi registrati all'avvio durante il processo di avvio. Ad esempio, non tentare di registrare un messaggio in un logger che viene registrato durante l'avvio. Questo punto del processo di registrazione è troppo presto per poter essere disponibili per l'uso da parte dei servizi. Dopo `Configure` l'esecuzione del metodo, il runtime di Funzioni continua a registrare dipendenze aggiuntive, che possono influire sul funzionamento dei servizi.

- *Il contenitore di inserimento delle dipendenze contiene solo tipi registrati in modo esplicito.* Gli unici servizi disponibili come tipi iniettabili sono quelli che sono impostati nel `Configure` metodo. Di conseguenza, tipi specifici `BindingContext` `ExecutionContext` di funzioni come e non sono disponibili durante l'installazione o come tipi iniettabili.

## <a name="use-injected-dependencies"></a>Usare le dipendenze inserite

Inserimento del costruttore viene utilizzato per rendere le dipendenze disponibili in una funzione. L'uso dell'inserimento del costruttore richiede che non si utilizzino classi statiche.

Nell'esempio seguente `IMyService` viene `HttpClient` illustrato come vengono inserite le dipendenze in una funzione attivata da HTTP. In questo esempio viene utilizzato il pacchetto `HttpClient` [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) necessario per registrare un pacchetto all'avvio.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, HttpClient httpClient)
        {
            _service = service;
            _client = httpClient;
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

## <a name="service-lifetimes"></a>Durate dei servizi

Le app Funzioni di Azure offrono le stesse durate del servizio di [ASP.NET Dependency Injection.](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes) Per un'app Funzioni, le diverse durate del servizio si comportano come segue:For a Functions app, the different service lifetimes behave as follows:

- **Transitorio**: I servizi temporanei vengono creati su ogni richiesta del servizio.
- **Ambito:** la durata del servizio con ambito corrisponde alla durata dell'esecuzione di una funzione. I servizi con ambito vengono creati una volta per esecuzione. Le richieste successive per tale servizio durante l'esecuzione riutilizzano l'istanza del servizio esistente.
- **Singleton:** la durata del servizio singleton corrisponde alla durata dell'host e viene riutilizzata tra le esecuzioni di funzioni in tale istanza. I servizi a vita Singleton sono `SqlConnection` consigliati per le connessioni e i client, ad esempio o `HttpClient` istanze.

Visualizzare o scaricare un [esempio di diverse durate del servizio](https://aka.ms/functions/di-sample) in GitHub.View or download a sample of different service lifetimes on GitHub.

## <a name="logging-services"></a>Servizi di registrazione

Se è necessario un provider di registrazione `ILoggerProvider` personalizzato, registrare un tipo personalizzato come istanza. Application Insights viene aggiunto automaticamente da Funzioni di Azure.Application Insights is added by Azure Functions automatically.

> [!WARNING]
> - Non aggiungere `AddApplicationInsightsTelemetry()` alla raccolta di servizi come registra i servizi che sono in conflitto con i servizi forniti dall'ambiente.
> - Non registrare `TelemetryConfiguration` il `TelemetryClient` proprio o se si utilizza la funzionalità incorporata di Application Insights. Se è necessario configurare una propria `TelemetryClient` istanza, `TelemetryConfiguration` crearne una tramite l'iniettamento come illustrato in [Monitorare le funzioni](./functions-monitoring.md#version-2x-and-later-2)di Azure .

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T> e ILoggerFactory

L'host `ILogger<T>` inietterà e `ILoggerFactory` servizi nei costruttori.  Tuttavia, per impostazione predefinita questi nuovi filtri di registrazione verranno filtrati dai registri delle funzioni.  Sarà necessario modificare `host.json` il file per optare in filtri e categorie aggiuntivi.  Nell'esempio seguente `ILogger<HttpTrigger>` viene illustrata l'aggiunta di un con i log che verranno esposti dall'host.

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly ILogger<HttpTrigger> _log;

        public HttpTrigger(ILogger<HttpTrigger> log)
        {
            _log = log;
        }

        [FunctionName("HttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req)
        {
            _log.LogInformation("C# HTTP trigger function processed a request.");

            // ...
    }
}
```

E `host.json` un file che aggiunge il filtro di log.

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingExcludedTypes": "Request",
            "samplingSettings": {
                "isEnabled": true
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

## <a name="function-app-provided-services"></a>Servizi forniti dall'app per le funzioni

L'host della funzione registra molti servizi. I servizi seguenti sono sicuri da prendere come dipendenza nell'applicazione:The following services are safe to take as a dependency in your application:

|Tipo di servizio|Durata|Descrizione|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Configurazione di runtime|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Responsabile di fornire l'ID dell'istanza dell'host|

Se ci sono altri servizi da cui si desidera prendere una dipendenza, [creare un problema e proporli su GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Override dei servizi host

L'override dei servizi forniti dall'host non è attualmente supportato.  Se sono presenti servizi di cui si desidera eseguire l'override, [creare un problema e proporlo su GitHub](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Utilizzo di opzioni e impostazioni

I valori definiti nelle [impostazioni dell'app](./functions-how-to-use-azure-function-app-settings.md#settings) sono disponibili in un'istanza, `IConfiguration` che consente di leggere i valori delle impostazioni dell'app nella classe di avvio.

È possibile estrarre `IConfiguration` valori dall'istanza in un tipo personalizzato. La copia dei valori delle impostazioni dell'app in un tipo personalizzato semplifica il test dei servizi rendendo questi valori iniettabili. Le impostazioni lette nell'istanza di configurazione devono essere semplici coppie chiave/valore.

Si consideri la classe seguente che include una proprietà denominata coerente con un'impostazione dell'app:Consider the following class that includes a property named consistent with an app setting:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

E `local.settings.json` un file che potrebbe strutturare l'impostazione personalizzata come segue:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

Dall'interno `Startup.Configure` del metodo, è `IConfiguration` possibile estrarre i valori dall'istanza nel tipo personalizzato utilizzando il codice seguente:From inside the method, you can extract values from the instance into your custom type using the following code:

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

La `Bind` chiamata copia i valori che hanno nomi di proprietà corrispondenti dalla configurazione nell'istanza personalizzata. L'istanza di options è ora disponibile nel contenitore IoC da inserire in una funzione.

L'oggetto options viene inserito nella funzione `IOptions` come un'istanza dell'interfaccia generica. Utilizzare `Value` la proprietà per accedere ai valori trovati nella configurazione.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

Fare riferimento al [modello Opzioni in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) per ulteriori dettagli sull'utilizzo delle opzioni.

> [!WARNING]
> Evitate di leggere valori da file come *local.settings.json* o *appsettings. environment.json* nel piano Consumo. I valori letti da questi file relativi alle connessioni trigger non sono disponibili in quanto l'app viene ridimensionata perché l'infrastruttura di hosting non ha accesso alle informazioni di configurazione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

- [Come monitorare la tua app per le funzioni](functions-monitoring.md)
- [Procedure consigliate per le funzioni](functions-best-practices.md)
