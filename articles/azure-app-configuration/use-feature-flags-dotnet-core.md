---
title: Esercitazione per l'uso dei flag di funzionalità in un'app .NET Core | Microsoft Docs
description: In questa esercitazione si apprenderà come implementare i flag di funzionalità nelle app .NET Core.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: fc5215f71af45d3273da437fc796bf0d396ba3f9
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393524"
---
# <a name="tutorial-use-feature-flags-in-a-net-core-app"></a>Esercitazione: Usare i flag di funzionalità in un'app .NET Core

Le librerie di gestione delle funzionalità di .NET Core offrono supporto idiomatico per implementare i flag di funzionalità in un'applicazione .NET o ASP.NET Core. Esse consentono di aggiungere i flag di funzionalità al codice in modo più dichiarativo così che non sia necessario scrivere tutte le istruzioni `if` manualmente.

Le librerie di gestione delle funzionalità gestiscono anche i cicli di vita dei flag di funzionalità in background. Ad esempio, le librerie aggiornano e memorizzano nella cache gli stati del flag o garantiscono lo stato immutabile di un flag durante una chiamata di richiesta. In aggiunta, la libreria ASP.NET Core offre integrazioni predefinite, tra cui le azioni del controller MVC, viste, route e middleware.

La guida di avvio rapido [Aggiungere i flag di funzionalità a un'app ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) mostra diversi modi per aggiungere flag di funzionalità in un'applicazione ASP.NET Core. Questa esercitazione illustra questi metodi più dettagliatamente. Per informazioni di riferimento complete, vedere la [documentazione sulla gestione della funzionalità ASP.NET Core](https://go.microsoft.com/fwlink/?linkid=2091410).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere i flag di funzionalità nelle parti chiave dell'applicazione per controllare la disponibilità delle funzionalità.
> * Eseguire l'integrazione con Configurazione app quando viene usato per gestire i flag di funzionalità.

## <a name="set-up-feature-management"></a>Configurare la gestione delle funzionalità

La gestione funzionalità di .NET Core `IFeatureManager` ottiene i flag di funzionalità dal sistema di configurazione nativo del framework. Di conseguenza, è possibile definire i flag di funzionalità dell'applicazione usando qualsiasi origine di configurazione supportata da .NET Core, tra cui il file locale *appSettings.JSON* o le variabili di ambiente. `IFeatureManager` si basa sull'inserimento delle dipendenze di .NET Core. È possibile registrare i servizi di gestione di funzionalità usando le convenzioni standard:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
}
```

Per impostazione predefinita, la gestione funzionalità recupera i flag di funzionalità dalla sezione `"FeatureManagement"` dei dati di configurazione di .NET Core. L'esempio seguente indica invece la lettura da un'altra sezione chiamata `"MyFeatureFlags"`:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement(options =>
        {
                options.UseConfiguration(Configuration.GetSection("MyFeatureFlags"));
        });
    }
}
```

Se si usano i filtri nei flag di funzionalità, è necessario includere una libreria aggiuntiva e registrarla. L'esempio seguente illustra come usare un filtro di funzionalità incorporato denominato `PercentageFilter`:

```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

È consigliabile mantenere i flag di funzionalità all'esterno dell'applicazione e gestirli separatamente. In questo modo è possibile modificare gli stati dei flag in qualsiasi momento e applicare immediatamente tali modifiche nell'applicazione. Configurazione app offre una posizione centralizzata per organizzare e controllare tutti i flag di funzionalità tramite un'interfaccia utente del portale dedicata. Configurazione app fornisce anche i flag all'applicazione direttamente attraverso le relative librerie client .NET Core.

Per connettere semplicemente l'applicazione ASP.NET Core a Configurazione app, usare il provider di configurazione `Microsoft.Extensions.Configuration.AzureAppConfiguration`. Per usare questo pacchetto NuGet, aggiungere il codice seguente al file *Program.cs*:

```csharp
using Microsoft.Extensions.Configuration.AzureAppConfiguration;

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
           .ConfigureAppConfiguration((hostingContext, config) => {
               var settings = config.Build();
               config.AddAzureAppConfiguration(options => {
                   options.Connect(settings["ConnectionStrings:AppConfig"])
                          .UseFeatureFlags();
                });
           })
           .UseStartup<Startup>();
```

I valori di flag di funzionalità devono cambiare nel tempo. Per impostazione predefinita, la gestione funzionalità aggiorna i valori dei flag di funzionalità ogni 30 secondi. Il codice seguente mostra come modificare l'intervallo di polling impostandolo su 5 secondi nella chiamata a `options.UseFeatureFlags()`:

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.PollInterval = TimeSpan.FromSeconds(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Dichiarazione dei flag di funzionalità

Ogni flag di funzionalità è costituito da due parti: un nome e un elenco di uno o più filtri che consentono di valutare se lo stato di una funzionalità è *attivo* (ovvero, quando il relativo valore è `True`). Un filtro definisce un caso d'uso per il quale deve essere attivata una funzionalità.

Se un flag di funzionalità ha più filtri, l'elenco dei filtri viene attraversato fino a quando uno dei filtri non determina che la funzionalità deve essere abilitata. A questo punto, il flag di funzionalità è *attivo* e i risultati del filtro rimanenti vengono ignorati. Se nessun filtro indica che deve essere abilitata la funzionalità, il flag di funzionalità viene *disattivato*.

La gestione funzionalità supporta *appSettings.json* come origine di configurazione per i flag di funzionalità. L'esempio seguente mostra come configurare i flag di funzionalità in un file JSON:

```JSON
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

Per convenzione, la sezione `FeatureManagement` di questo documento JSON viene usata per le impostazioni dei flag di funzionalità. L'esempio precedente mostra tre flag di funzionalità con i relativi filtri definiti nella proprietà `EnabledFor`:

* `FeatureA` è *attivo*.
* `FeatureB` è *disattivato*.
* `FeatureC` specifica un filtro denominato `Percentage` con una proprietà `Parameters`. `Percentage` è un filtro configurabile. In questo esempio `Percentage` specifica una probabilità del 50% che il flag `FeatureC` sia *attivo*.

## <a name="feature-flag-references"></a>Riferimenti di flag funzionalità

Per poter facilmente fare riferimento ai flag di funzionalità nel codice, è necessario definirli come variabili `enum`:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Controlli dei flag di funzionalità

Il modello di base di gestione delle funzionalità consiste prima di tutto nel controllare se un flag di funzionalità è impostato su *attivo*. In tal caso, la gestione delle funzionalità esegue le azioni contenute nella funzionalità. Ad esempio:

```csharp
IFeatureManager featureManager;
...
if (featureManager.IsEnabled(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Inserimento delle dipendenze

In ASP.NET Core MVC è possibile accedere alla gestione funzionalità `IFeatureManager` grazie all'inserimento delle dipendenze:

```csharp
public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Azioni del controller

Nei controller MVC è possibile usare l'attributo `Feature` per controllare se è stata abilitata un'intera classe controller o un'azione specifica. Il controller `HomeController` seguente richiede che il flag `FeatureA` sia *attivo* prima che venga eseguita qualsiasi azione contenuta nella classe controller:

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

Per poter eseguire l'azione `Index` seguente, il flag`FeatureA` deve essere *attivo*:

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Quando un controller o un'azione MVC viene bloccato perché il flag di funzionalità di controllo è *disattivato*, viene chiamata un'interfaccia `IDisabledFeaturesHandler` registrata. L'interfaccia `IDisabledFeaturesHandler` predefinita restituisce un codice di stato 404 al client senza alcun corpo della risposta.

## <a name="mvc-views"></a>Viste MVC

Nelle viste MVC è possibile usare un tag `<feature>` per eseguire il rendering del contenuto in base allo stato di attivazione del flag di funzionalità:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>Filtri MVC

È possibile configurare i filtri MVC in modo da attivarli in base allo stato di un flag di funzionalità. Il codice seguente aggiunge un filtro MVC denominato `SomeMvcFilter`. Questo filtro viene attivato all'interno della pipeline MVC solo se il flag `FeatureA` è attivato.

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<SomeMvcFilter>(nameof(MyFeatureFlags.FeatureA));
    });
}
```

## <a name="routes"></a>Route

È possibile usare i flag di funzionalità per esporre in modo dinamico le route. Il codice seguente aggiunge una route, che imposta `Beta` come controller predefinito, solo quando il flag `FeatureA` è attivato:

```csharp
app.UseMvc(routes => {
    routes.MapRouteForFeature(nameof(MyFeatureFlags.FeatureA), "betaDefault", "{controller=Beta}/{action=Index}/{id?}");
});
```

## <a name="middleware"></a>Middleware

È possibile usare i flag di funzionalità per aggiungere rami di applicazioni e middleware in modo condizionale. Il codice seguente inserisce un componente middleware nella pipeline di richiesta solo quando il flag `FeatureA` è attivato:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Questo codice produce la capacità più generica di creare rami nell'intera applicazione in base al flag di funzionalità:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come implementare i flag di funzionalità nell'applicazione ASP.NET Core usando le librerie `Microsoft.FeatureManagement`. Per altre informazioni sul supporto della gestione funzionalità in ASP.NET Core e Configurazione app, vedere le risorse seguenti:

* [Codice di esempio del flag di funzionalità di ASP.NET Core](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Documentazione di Microsoft.FeatureManagement](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Gestire i flag di funzionalità](./manage-feature-flags.md)
