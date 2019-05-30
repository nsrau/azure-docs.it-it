---
title: Usare l'inserimento delle dipendenze in funzioni di Azure .NET
description: Informazioni su come usare l'inserimento delle dipendenze per la registrazione e l'utilizzo di servizi nelle funzioni .NET
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: funzioni di azure, funzioni, architettura serverless
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/28/2019
ms.author: jehollan, glenga, cshoe
ms.openlocfilehash: 2f2e3db47bbd02ed0351033a694aa826e0e3e9f2
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66396708"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Usare l'inserimento delle dipendenze in funzioni di Azure .NET

Funzioni di Azure supporta il dipendenza l'inserimento di dipendenze software progettuale, che è una tecnica per ottenere [Inversion of Control (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra classi e le relative dipendenze.

Funzioni di Azure si basa sulle funzionalità di inserimento delle dipendenze di ASP.NET Core. Essendo a conoscenza dei servizi, durate e modelli di progettazione del [inserimento delle dipendenze di ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) prima di usare le funzionalità di inserimento delle dipendenze in un funzioni di Azure consiglia di app.

## <a name="prerequisites"></a>Prerequisiti

Prima di poter usare inserimento delle dipendenze, è necessario installare i pacchetti NuGet seguenti:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Pacchetto microsoftnetsdkfunctions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) versione 1.0.28 o versione successiva

## <a name="register-services"></a>Registrare i servizi

Per registrare i servizi, è possibile creare un metodo per configurare e aggiungere componenti a un `IFunctionsHostBuilder` istanza.  L'host di funzioni di Azure crea un'istanza di `IFunctionsHostBuilder` e passa direttamente al metodo.

Per registrare il metodo, aggiungere il `FunctionsStartup` attributo assembly che specifica il nome del tipo utilizzato durante l'avvio.

```csharp
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

## <a name="use-injected-dependencies"></a>Usare le dipendenze inserite

ASP.NET Core Usa l'inserimento del costruttore per rendere disponibili per la funzione delle dipendenze. Nell'esempio seguente viene illustrato come la `IMyService` e `HttpClient` dipendenze vengono inserite in una funzione attivata tramite HTTP.

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, HttpClient client)
        {
            _service = service;
            _client = client;
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

L'uso di inserimento del costruttore significa che è consigliabile non utilizzare funzioni statiche se si vuole sfruttare i vantaggi dell'inserimento delle dipendenze.

## <a name="service-lifetimes"></a>Delle durate del servizio

Le app funzioni di Azure forniscono la stessa durata di servizio come [inserimento delle dipendenze ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes): temporanei, con ambito, singleton e.

In un'app per le funzioni, una durata del servizio con ambito corrisponde alla durata dell'esecuzione di una funzione. Servizi con ambiti vengono creati una sola volta per ogni esecuzione. Le richieste successive per il servizio durante l'esecuzione di riutilizzare l'istanza del servizio esistente. Una durata del servizio singleton corrisponde alla durata di host e viene riutilizzato tra esecuzioni delle funzioni in tale istanza.

Servizi di durata singleton sono consigliati per le connessioni e i client, ad esempio `SqlConnection`, `CloudBlobClient`, o `HttpClient` istanze.

Visualizzare o scaricare una [esempio della durata dei servizi differenti](https://aka.ms/functions/di-sample) su GitHub.

## <a name="logging-services"></a>Servizi di registrazione

Se è necessario il proprio provider di registrazione, il metodo consigliato consiste nel registrare un `ILoggerProvider` istanza. Application Insights viene aggiunto automaticamente da funzioni di Azure.

> [!WARNING]
> Non aggiungere `AddApplicationInsightsTelemetry()` alla raccolta di servizi come registri dei servizi in conflitto con i servizi forniti dall'ambiente.

## <a name="function-app-provided-services"></a>Servizi app fornita (funzione)

L'host di funzioni Registra molti servizi. I servizi seguenti sono sicuri da eseguire come una dipendenza all'interno dell'applicazione:

|Tipo di servizio|Durata|Descrizione|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|singleton|Configurazione di runtime|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|singleton|Responsabile di fornire l'ID dell'istanza dell'host|

Se sono presenti altri servizi che si desidera accettare una dipendenza, [crea un problema e proporre li su GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Override di servizi host

Override di servizi forniti dall'host non è attualmente supportata.  Se sono presenti servizi che si desidera eseguire l'override, [crea un problema e proporre li su GitHub](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

- [Come monitorare le app per le funzioni](functions-monitoring.md)
- [Le procedure consigliate per le funzioni](functions-best-practices.md)
