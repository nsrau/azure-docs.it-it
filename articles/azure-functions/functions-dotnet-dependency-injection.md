---
title: Usare l'inserimento di dipendenze in Funzioni di Azure .NET
description: Informazioni su come usare l'inserimento di dipendenze per la registrazione e l'uso di servizi in funzioni .NET
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: 97e8a34f3b8639990f8de736a8f1f7429ebfd448
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83739142"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Usare l'inserimento di dipendenze in Funzioni di Azure .NET

Funzioni di Azure supporta lo schema progettuale di software per l'inserimento di dipendenze, una tecnica per ottenere l'[IoC (Inversion of Control)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra le classi e le relative dipendenze.

- L'inserimento di dipendenze in Funzioni di Azure si basa sulle funzionalità di inserimento dipendenze .NET Core. È consigliabile acquisire familiarità con l'[inserimento di dipendenze .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Esistono differenze nel modo in cui viene eseguito l'override delle dipendenze e nel modo in cui i valori di configurazione vengono letti con Funzioni di Azure nel piano a consumo.

- Il supporto per l'inserimento di dipendenze inizia a partire da Funzioni di Azure 2.x.

## <a name="prerequisites"></a>Prerequisiti

Prima di poter usare l'inserimento di dipendenze, è necessario installare i pacchetti NuGet seguenti:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) versione pacchetto 1.0.28 o successiva

## <a name="register-services"></a>Registrare i servizi

Per registrare i servizi, creare un metodo per configurare e aggiungere componenti a un'istanza di `IFunctionsHostBuilder`.  L'host di Funzioni di Azure crea un'istanza di `IFunctionsHostBuilder` e la passa direttamente al metodo.

Per registrare il metodo, aggiungere l'attributo dell'assembly `FunctionsStartup` che specifica il nome del tipo usato durante l'avvio.

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

Una serie di passaggi di registrazione viene eseguita prima e dopo l'elaborazione della classe di avvio da parte del runtime. Tenere dunque presenti questi elementi:

- *La classe di avvio è destinata solo alla configurazione e alla registrazione.* Evitare di usare i servizi registrati all'avvio durante il processo di avvio. Ad esempio, non provare a registrare un messaggio in un logger registrato durante l'avvio. A questo punto del processo di registrazione è troppo presto perché i servizi siano disponibili per l'uso. Dopo l'esecuzione del metodo `Configure`, il runtime Funzioni continua a registrare dipendenze aggiuntive, che possono influire sul funzionamento dei servizi.

- *Il contenitore di inserimento delle dipendenze include solo tipi registrati in modo esplicito*. Gli unici servizi disponibili come tipi inseribili sono quelli che vengono impostati nel metodo `Configure`. Di conseguenza, i tipi specifici per Funzioni come `BindingContext` e `ExecutionContext` non sono disponibili durante l'installazione o come tipi inseribili.

## <a name="use-injected-dependencies"></a>Usare dipendenze inserite

L'inserimento del costruttore viene usato per rendere disponibili le dipendenze in una funzione. L'uso dell'inserimento del costruttore richiede che non si usino classi statiche.

Nell'esempio seguente viene illustrata la modalità di inserimento delle dipendenze `IMyService` e `HttpClient` in una funzione attivata tramite HTTP. Questo esempio usa il pacchetto [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) necessario per registrare un `HttpClient` all'avvio.

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

## <a name="service-lifetimes"></a>Durate del servizio

Le app di Funzioni di Azure offrono la stessa durata del servizio dell'[inserimento di dipendenze ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes). Per un'app per le funzioni, le diverse durate del servizio si comportano nel modo seguente:

- **Temporanea**: I servizi temporanei vengono creati a ogni richiesta del servizio.
- **Con ambito**: La durata del servizio con ambito corrisponde a una durata di esecuzione della funzione. I servizi con ambito vengono creati una volta per ogni esecuzione. Le richieste successive per un dato servizio durante l'esecuzione riutilizzeranno l'istanza del servizio esistente.
- **Singleton**: La durata del servizio singleton corrisponde alla durata dell'host e viene riutilizzata tra le esecuzioni di una funzione su tale istanza. I servizi con durata singleton sono consigliati per connessioni e client, ad esempio per le istanze `SqlConnection` o `HttpClient`.

Visualizzare o scaricare un [esempio di diverse durate dei servizi](https://aka.ms/functions/di-sample) su GitHub.

## <a name="logging-services"></a>Servizi di registrazione

Se è necessario un provider di registrazione personalizzato, registrare un tipo personalizzato come istanza di `ILoggerProvider`. Application Insights viene aggiunto automaticamente da Funzioni di Azure.

> [!WARNING]
> - Non aggiungere `AddApplicationInsightsTelemetry()` alla raccolta di servizi durante la registrazione di servizi in conflitto con i servizi forniti dall'ambiente.
> - Non registrare il proprio `TelemetryConfiguration` o `TelemetryClient` se si usa la funzionalità di Application Insights incorporata. Se è necessario configurare un'istanza di `TelemetryClient` personalizzata, crearne una tramite la `TelemetryConfiguration` inserita, come illustrato in [Monitorare Funzioni di Azure](./functions-monitoring.md#version-2x-and-later-2).

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T> e ILoggerFactory

L'host inserirà i servizi `ILogger<T>` e `ILoggerFactory` in costruttori.  Per impostazione predefinita, tuttavia, questi nuovi filtri di registrazione verranno filtrati all'esterno dei log di funzione.  Sarà necessario modificare il file `host.json` per acconsentire esplicitamente ad altri filtri e categorie.  Nell'esempio seguente viene illustrata l'aggiunta di un `ILogger<HttpTrigger>` con log che verranno esposti dall'host

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

e di un file `host.json` che aggiunge il filtro del log.

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

L'host funzione registra molti servizi. I servizi seguenti possono essere considerati sicuri come una dipendenza nell'applicazione:

|Tipo di servizio|Durata|Descrizione|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Configurazione del runtime|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Responsabile della fornitura dell'ID dell'istanza host|

Se sono presenti altri servizi su cui si vuole creare una dipendenza, [creare un argomento e proporlo in GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Override dei servizi host

L'override dei servizi forniti dall'host non è attualmente supportato.  Se sono presenti servizi di cui si vuole eseguire l'override, [creare un argomento e proporlo in GitHub](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Usare opzioni e impostazioni

I valori definiti nelle [impostazioni app](./functions-how-to-use-azure-function-app-settings.md#settings) sono disponibili in un'istanza di `IConfiguration`, che consente di leggere i valori delle impostazioni dell'app nella classe di avvio.

È possibile estrarre i valori dall'istanza di `IConfiguration` in un tipo personalizzato. Copiare i valori delle impostazioni dell'app su un tipo personalizzato facilita il test dei servizi, permettendo l'inserimento di questi valori. Le impostazioni lette nell'istanza di configurazione devono essere semplici coppie chiave/valore.

Si consideri la classe seguente, che include una proprietà denominata in modo coerente con un'impostazione dell'app:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

E un file `local.settings.json`, in grado di strutturare l'impostazione personalizzata come indicato di seguito:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

Dall'interno del metodo `Startup.Configure`, è possibile estrarre i valori dall'istanza `IConfiguration` nel tipo personalizzato usando il codice seguente:

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

La chiamata di `Bind` copia i valori che hanno nomi di proprietà corrispondenti dalla configurazione all'istanza personalizzata. L'istanza Options è ora disponibile nel contenitore IoC per essere inserita in una funzione.

L'oggetto Options viene inserito nella funzione come istanza dell'interfaccia `IOptions` generica. Usare la proprietà `Value` per accedere ai valori presenti nella configurazione.

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

Per informazioni dettagliate sull'uso delle opzioni, fare riferimento al [modello di opzioni in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

> [!WARNING]
> Evitare di provare a leggere i valori da file come *local.settings.json* o *appsettings.{environment}.json* nel piano a consumo. I valori letti da questi file correlati alle connessioni trigger non sono disponibili mentre l'app viene ridimensionata perché l'infrastruttura host non ha accesso alle informazioni di configurazione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

- [Procedura di monitoraggio dell'app per le funzioni](functions-monitoring.md)
- [Procedure consigliate per le funzioni](functions-best-practices.md)
