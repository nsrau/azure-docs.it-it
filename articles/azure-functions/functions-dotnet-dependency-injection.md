---
title: Usare l'inserimento di dipendenze in Funzioni di Azure .NET
description: Informazioni su come usare l'inserimento di dipendenze per la registrazione e l'uso di servizi in funzioni .NET
author: ggailey777
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/15/2020
ms.author: glenga
ms.reviewer: jehollan
ms.openlocfilehash: 6badcedba7fa1e1b605fc5553e5c6eed52c4203b
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182072"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Usare l'inserimento di dipendenze in Funzioni di Azure .NET

Funzioni di Azure supporta lo schema progettuale di software per l'inserimento di dipendenze, una tecnica per ottenere l'[IoC (Inversion of Control)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra le classi e le relative dipendenze.

- L'inserimento di dipendenze in Funzioni di Azure si basa sulle funzionalità di inserimento dipendenze .NET Core. È consigliabile acquisire familiarità con l'[inserimento di dipendenze .NET Core](/aspnet/core/fundamentals/dependency-injection). Esistono differenze nel modo in cui viene eseguito l'override delle dipendenze e nel modo in cui i valori di configurazione vengono letti con Funzioni di Azure nel piano a consumo.

- Il supporto per l'inserimento di dipendenze inizia a partire da Funzioni di Azure 2.x.

## <a name="prerequisites"></a>Prerequisiti

Prima di poter usare l'inserimento di dipendenze, è necessario installare i pacchetti NuGet seguenti:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) versione pacchetto 1.0.28 o successiva

## <a name="register-services"></a>Registrare i servizi

Per registrare i servizi, creare un metodo per configurare e aggiungere componenti a un'istanza di `IFunctionsHostBuilder`.  L'host di Funzioni di Azure crea un'istanza di `IFunctionsHostBuilder` e la passa direttamente al metodo.

Per registrare il metodo, aggiungere l'attributo dell'assembly `FunctionsStartup` che specifica il nome del tipo usato durante l'avvio.

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton<IMyService>((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

Questo esempio usa il pacchetto [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) necessario per registrare un `HttpClient` all'avvio.

### <a name="caveats"></a>Precisazioni

Una serie di passaggi di registrazione viene eseguita prima e dopo l'elaborazione della classe di avvio da parte del runtime. Tenere dunque presenti questi elementi:

- *La classe di avvio è destinata solo alla configurazione e alla registrazione.* Evitare di usare i servizi registrati all'avvio durante il processo di avvio. Ad esempio, non provare a registrare un messaggio in un logger registrato durante l'avvio. A questo punto del processo di registrazione è troppo presto perché i servizi siano disponibili per l'uso. Dopo l'esecuzione del metodo `Configure`, il runtime Funzioni continua a registrare dipendenze aggiuntive, che possono influire sul funzionamento dei servizi.

- *Il contenitore di inserimento delle dipendenze include solo tipi registrati in modo esplicito*. Gli unici servizi disponibili come tipi inseribili sono quelli che vengono impostati nel metodo `Configure`. Di conseguenza, i tipi specifici per Funzioni come `BindingContext` e `ExecutionContext` non sono disponibili durante l'installazione o come tipi inseribili.

## <a name="use-injected-dependencies"></a>Usare dipendenze inserite

L'inserimento del costruttore viene usato per rendere disponibili le dipendenze in una funzione. L'uso dell'inserimento del costruttore richiede che non si usino classi statiche per i servizi inseriti o per le classi di funzione.

Nell'esempio seguente viene illustrata la modalità di inserimento delle dipendenze `IMyService` e `HttpClient` in una funzione attivata tramite HTTP.

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;
using System.Threading.Tasks;

namespace MyNamespace
{
    public class MyHttpTrigger
    {
        private readonly HttpClient _client;
        private readonly IMyService _service;

        public MyHttpTrigger(HttpClient httpClient, MyService service)
        {
            this._client = httpClient;
            this._service = service;
        }

        [FunctionName("MyHttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            var response = await _client.GetAsync("https://microsoft.com");
            var message = _service.GetMessage();

            return new OkObjectResult("Response from function with injected dependencies.");
        }
    }
}
```

Questo esempio usa il pacchetto [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) necessario per registrare un `HttpClient` all'avvio.

## <a name="service-lifetimes"></a>Durate del servizio

Le app di Funzioni di Azure offrono la stessa durata del servizio dell'[inserimento di dipendenze ASP.NET](/aspnet/core/fundamentals/dependency-injection#service-lifetimes). Per un'app per le funzioni, le diverse durate del servizio si comportano nel modo seguente:

- **Temporanea**: I servizi temporanei vengono creati a ogni richiesta del servizio.
- **Con ambito**: La durata del servizio con ambito corrisponde a una durata di esecuzione della funzione. I servizi con ambito vengono creati una volta per ogni esecuzione. Le richieste successive per un dato servizio durante l'esecuzione riutilizzeranno l'istanza del servizio esistente.
- **Singleton**: La durata del servizio singleton corrisponde alla durata dell'host e viene riutilizzata tra le esecuzioni di una funzione su tale istanza. I servizi con durata singleton sono consigliati per connessioni e client, ad esempio per le istanze `DocumentClient` o `HttpClient`.

Visualizzare o scaricare un [esempio di diverse durate dei servizi](https://github.com/Azure/azure-functions-dotnet-extensions/tree/main/src/samples/DependencyInjection/Scopes) su GitHub.

## <a name="logging-services"></a>Servizi di registrazione

Se è necessario un provider di registrazione personalizzato, registrare un tipo personalizzato come istanza di [`ILoggerProvider`](/dotnet/api/microsoft.extensions.logging.iloggerfactory) , disponibile tramite il pacchetto NuGet [Microsoft. Extensions. Logging. abstracts](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) .

Application Insights viene aggiunto automaticamente da Funzioni di Azure.

> [!WARNING]
> - Non aggiungere `AddApplicationInsightsTelemetry()` alla raccolta di servizi durante la registrazione di servizi in conflitto con i servizi forniti dall'ambiente.
> - Non registrare il proprio `TelemetryConfiguration` o `TelemetryClient` se si usa la funzionalità di Application Insights incorporata. Se è necessario configurare un'istanza di `TelemetryClient` personalizzata, crearne una tramite la `TelemetryConfiguration` inserita, come illustrato in [Monitorare Funzioni di Azure](./functions-monitoring.md#version-2x-and-later-2).

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T> e ILoggerFactory

L'host inserisce i `ILogger<T>` `ILoggerFactory` Servizi e nei costruttori.  Per impostazione predefinita, tuttavia, questi nuovi filtri di registrazione vengono esclusi dai log delle funzioni.  È necessario modificare il `host.json` file per acconsentire esplicitamente ad altri filtri e categorie.

Nell'esempio seguente viene illustrato come aggiungere un oggetto `ILogger<HttpTrigger>` con i log esposti all'host.

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

Nel file di esempio seguente `host.json` viene aggiunto il filtro del log.

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

Per informazioni dettagliate sull'uso delle opzioni, fare riferimento al [modello di opzioni in ASP.NET Core](/aspnet/core/fundamentals/configuration/options).

## <a name="customizing-configuration-sources"></a>Personalizzazione delle origini di configurazione

> [!NOTE]
> La personalizzazione dell'origine di configurazione è disponibile a partire dalle versioni host di funzioni di Azure 2.0.14192.0 e 3.0.14191.0.

Per specificare origini di configurazione aggiuntive, eseguire l'override del `ConfigureAppConfiguration` metodo nella classe dell'app per le funzioni `StartUp` .

Nell'esempio seguente vengono aggiunti i valori di configurazione da una base e da un file di impostazioni dell'app specifici dell'ambiente.

```csharp
using System.IO;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
        {
            FunctionsHostBuilderContext context = builder.GetContext();

            builder.ConfigurationBuilder
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, "appsettings.json"), optional: true, reloadOnChange: false)
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, $"appsettings.{context.EnvironmentName}.json"), optional: true, reloadOnChange: false)
                .AddEnvironmentVariables();
        }
    }
}
```

Aggiungere i provider di configurazione alla `ConfigurationBuilder` proprietà di `IFunctionsConfigurationBuilder` . Per ulteriori informazioni sull'utilizzo dei provider di configurazione, vedere la pagina relativa [alla configurazione in ASP.NET Core](/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.1#configuration-providers).

Un oggetto `FunctionsHostBuilderContext` viene ottenuto da `IFunctionsConfigurationBuilder.GetContext()` . Usare questo contesto per recuperare il nome dell'ambiente corrente e risolvere il percorso dei file di configurazione nella cartella dell'app per le funzioni.

Per impostazione predefinita, i file di configurazione, ad esempio *appsettings.json* , non vengono copiati automaticamente nella cartella di output dell'app per le funzioni. Aggiornare il file con *estensione csproj* in modo che corrisponda all'esempio seguente per assicurarsi che i file vengano copiati.

```xml
<None Update="appsettings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>      
</None>
<None Update="appsettings.Development.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
</None>
```

> [!IMPORTANT]
> Per le app per le funzioni in esecuzione nei piani di consumo o Premium, le modifiche ai valori di configurazione usati nei trigger possono causare errori di scalabilità. Tutte le modifiche apportate a queste proprietà dalla `FunctionsStartup` classe generano un errore di avvio dell'app per le funzioni.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

- [Procedura di monitoraggio dell'app per le funzioni](functions-monitoring.md)
- [Procedure consigliate per le funzioni](functions-best-practices.md)
