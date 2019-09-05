---
title: Usare l'inserimento di dipendenze in funzioni di Azure per .NET
description: Informazioni su come usare l'inserimento di dipendenze per la registrazione e l'uso di servizi nelle funzioni .NET
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: funzioni di azure, funzioni, architettura serverless
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/28/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: e31f3dc166177ce36289b97d85d90a9582c9cae5
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375990"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Usare l'inserimento di dipendenze in funzioni di Azure per .NET

Funzioni di Azure supporta il modello DI progettazione software per l'inserimento delle dipendenze, una tecnica per ottenere l' [inversione del controllo (IOC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra le classi e le relative dipendenze.

Funzioni di Azure si basa sulle funzionalità di inserimento delle dipendenze ASP.NET Core. È consigliabile tenere presente i servizi, le durate e i modelli di progettazione dell' [inserimento delle dipendenze ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) prima di usare le funzionalità di inserimento delle dipendenze in un'app funzioni di Azure.

Il supporto per l'inserimento delle dipendenze inizia con funzioni di Azure 2. x.

## <a name="prerequisites"></a>Prerequisiti

Prima di poter usare l'inserimento di dipendenze, è necessario installare i pacchetti NuGet seguenti:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft. NET. Sdk. Functions Package](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) Version 1.0.28 o versioni successive

- Facoltativo: [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) è necessario solo per la registrazione di HttpClient all'avvio

## <a name="register-services"></a>Registrare i servizi

Per registrare i servizi, è possibile creare un metodo per configurare e aggiungere componenti a `IFunctionsHostBuilder` un'istanza di.  L'host di funzioni di Azure crea un' `IFunctionsHostBuilder` istanza di e la passa direttamente al metodo.

Per registrare il metodo, aggiungere l' `FunctionsStartup` attributo dell'assembly che specifica il nome del tipo usato durante l'avvio. Anche il codice fa riferimento a una versione provvisoria di [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) in NuGet.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
            builder.Services.AddSingleton((s) => {
                return new CosmosClient(Environment.GetEnvironmentVariable("COSMOSDB_CONNECTIONSTRING"));
            });
            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

## <a name="use-injected-dependencies"></a>Usa dipendenze inserite

ASP.NET Core usa l'inserimento del costruttore per rendere disponibili le dipendenze alla funzione. Nell'esempio seguente viene illustrato come `IMyService` inserire `HttpClient` le dipendenze e in una funzione attivata tramite http. 

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();
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

L'uso dell'inserimento del costruttore significa che non è consigliabile usare funzioni statiche se si vuole sfruttare i vantaggi dell'inserimento delle dipendenze. Per Cosmos client fare riferimento a [questa](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/CodeSamples/AzureFunctions/AzureFunctionsCosmosClient.cs).

## <a name="service-lifetimes"></a>Durate del servizio

Le app di funzioni di Azure forniscono le stesse durate dei servizi dell' [inserimento delle dipendenze di ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes): temporaneo, con ambito e Singleton.

In un'app per le funzioni, una durata del servizio con ambito corrisponde a una durata di esecuzione della funzione. I servizi con ambito vengono creati una volta per ogni esecuzione. Le richieste successive per quel servizio durante l'esecuzione riutilizzeranno l'istanza del servizio esistente. Una durata del servizio singleton corrisponde alla durata dell'host e viene riutilizzata tra le esecuzioni di funzioni su tale istanza.

I servizi di durata singleton sono consigliati per le connessioni e i `SqlConnection`client `CloudBlobClient`, ad `HttpClient` esempio le istanze di o.

Visualizzare o scaricare un [esempio di diverse durate dei servizi](https://aka.ms/functions/di-sample) su GitHub.

## <a name="logging-services"></a>Servizi di registrazione

Se è necessario un provider di registrazione personalizzato, il metodo consigliato consiste nel registrare `ILoggerProvider` un'istanza di. Application Insights viene aggiunto automaticamente da funzioni di Azure.

> [!WARNING]
> Non aggiungere `AddApplicationInsightsTelemetry()` alla raccolta di servizi durante la registrazione dei servizi in conflitto con i servizi forniti dall'ambiente.

## <a name="function-app-provided-services"></a>Servizi forniti dall'app per le funzioni

L'host funzione registra molti servizi. I servizi seguenti possono essere considerati sicuri come una dipendenza nell'applicazione:

|Tipo di servizio|Durata|DESCRIZIONE|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Configurazione Runtime|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Responsabile per fornire l'ID dell'istanza host|

Se sono presenti altri servizi su cui si vuole creare una dipendenza, [creare un problema e proporrli in GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Override dei servizi host

La sostituzione dei servizi forniti dall'host non è attualmente supportata.  Se sono presenti servizi di cui si vuole eseguire l'override, [creare un problema e proporrli in GitHub](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

- [Come monitorare l'app per le funzioni](functions-monitoring.md)
- [Procedure consigliate per le funzioni](functions-best-practices.md)
