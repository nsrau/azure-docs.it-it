---
title: Usare l'inserimento delle dipendenze in funzioni di Azure .NET
description: Informazioni su come usare l'inserimento delle dipendenze per la registrazione e l'utilizzo di servizi nelle funzioni .NET
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: funzioni di azure, funzioni, architettura serverless
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2019
ms.author: jehollan
ms.openlocfilehash: 2044718d2ec7a7acc58e1e7ba9ba04ec5caf16b3
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408456"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Usare l'inserimento delle dipendenze in funzioni di Azure .NET

Funzioni di Azure supporta il dipendenza l'inserimento di dipendenze software progettuale, che è una tecnica per ottenere [Inversion of Control (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra classi e le relative dipendenze.

Funzioni di Azure si basa sulle funzionalità di inserimento delle dipendenze di ASP.NET Core.  È necessario comprendere servizi, durate e modelli di progettazione del [inserimento delle dipendenze di ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) prima di utilizzarli nelle funzioni.

## <a name="installing-dependency-injection-packages"></a>L'installazione dei pacchetti di inserimento delle dipendenze

Per usare le funzionalità di inserimento delle dipendenze, è necessario includere il pacchetto NuGet che espone tali API.

```powershell
Install-Package Microsoft.Azure.Functions.Extensions
```

## <a name="registering-services"></a>Registrazione di servizi

Per registrare i servizi, è possibile creare un metodo configura e aggiungere componenti a un `IFunctionsHostBuilder` istanza.  L'host di funzioni di Azure crea un `IFunctionsHostBuilder` e lo passa direttamente il metodo configurato.

Per registrare il configurare un metodo, è necessario aggiungere un attributo dell'assembly che specifica il tipo per il configurare metodo utilizzando il `FunctionsStartup` attributo.

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

## <a name="service-lifetimes"></a>Delle durate del servizio

Azure App per le funzioni forniscono la stessa durata di servizio come [inserimento delle dipendenze ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes), temporaneo, con ambito, singleton e.

In un'app per le funzioni, una durata del servizio con ambito corrisponde alla durata dell'esecuzione di una funzione. Servizi con ambiti vengono creati una sola volta per ogni esecuzione.  Le richieste successive per il servizio durante l'esecuzione riutilizzano quell'istanza.  Una durata del servizio singleton corrisponde alla durata di host e viene riutilizzato tra esecuzioni delle funzioni in tale istanza.

Servizi di durata singleton sono consigliati per le connessioni e i client, ad esempio un `SqlConnection`, `CloudBlobClient`, o `HttpClient`.

Visualizzare o scaricare una [esempio della durata dei servizi differenti](https://aka.ms/functions/di-sample).

## <a name="logging-services"></a>Servizi di registrazione

Se è necessario il proprio provider di registrazione, il metodo consigliato consiste nel registrare un `ILoggerProvider`.  Per Application Insights, funzioni aggiunge Application Insights automaticamente per l'utente.  

> [!WARNING]
> Non aggiungere `AddApplicationInsightsTelemetry()` ai servizi di raccolta poiché registra i servizi che è in conflitto con il contenuto viene fornita dall'ambiente. 
 
## <a name="function-app-provided-services"></a>Servizi app fornita (funzione)

L'host di funzioni verranno registrati molti servizi di se stesso.  Di seguito sono servizi che sono sicuri accettare una dipendenza.  Altri servizi host non sono supportati per la registrazione o dipendono.  Se sono presenti altri servizi si desidera accettare una dipendenza, controllare [creare un problema e una discussione su GitHub](https://github.com/azure/azure-functions-host).

|Tipo di servizio|Durata|Descrizione|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|singleton|Configurazione di runtime|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|singleton|Responsabile di fornire l'ID dell'istanza dell'host|

### <a name="overriding-host-services"></a>Override di servizi Host

Override di servizi forniti dall'host non è attualmente supportata.  Se sono presenti servizi che si desidera eseguire l'override, please [creare un problema e una discussione su GitHub](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Come monitorare le app per le funzioni](functions-monitoring.md)
* [Le procedure consigliate per le funzioni](functions-best-practices.md)