---
title: Configurare app Windows ASP.NET Core
description: Informazioni su come configurare un'app ASP.NET Core nelle istanze di Windows native del servizio app. Questo articolo illustra le attività di configurazione più comuni.
ms.devlang: dotnet
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 5819fc5b2d6e64d1812dacd88a2a4f840f6e03c5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84907929"
---
# <a name="configure-a-windows-aspnet-core-app-for-azure-app-service"></a>Configurare un'app ASP.NET Core Windows per il servizio app Azure

> [!NOTE]
> Per ASP.NET in .NET Framework, vedere [configurare un'app ASP.NET per il servizio app Azure](configure-language-dotnet-framework.md)

ASP.NET Core le app devono essere distribuite in app Azure servizio come file binari compilati. Lo strumento di pubblicazione di Visual Studio compila la soluzione e quindi distribuisce direttamente i file binari compilati, mentre il motore di distribuzione del servizio app distribuisce innanzitutto il repository di codice e quindi compila i file binari. Per informazioni sulle app Linux, vedere [configurare un'app linux ASP.NET Core per app Azure servizio](containers/configure-language-dotnetcore.md).

Questa guida fornisce i concetti chiave e le istruzioni per ASP.NET Core gli sviluppatori. Se non si è mai usato app Azure servizio, seguire prima l'esercitazione [introduttiva di ASP.NET](app-service-web-get-started-dotnet.md) e [ASP.NET Core con il database SQL](app-service-web-tutorial-dotnetcore-sqldb.md) .

## <a name="show-supported-net-core-runtime-versions"></a>Mostra le versioni di runtime di .NET Core supportate

Nel servizio app, nelle istanze di Windows sono già installate tutte le versioni di .NET Core supportate. Per visualizzare le versioni di runtime e SDK di .NET core disponibili, passare a `https://<app-name>.scm.azurewebsites.net/DebugConsole` ed eseguire il comando seguente nella console basata su browser:

```azurecli-interactive
dotnet --info
```

## <a name="set-net-core-version"></a>Impostare la versione di .NET Core

Impostare il Framework di destinazione nel file di progetto per il progetto ASP.NET Core. Per altre informazioni, vedere [selezionare la versione di .NET Core da usare](https://docs.microsoft.com/dotnet/core/versions/selection) nella documentazione di .NET Core.

## <a name="access-environment-variables"></a>Accedere alle variabili di ambiente

Nel servizio app è possibile [configurare le impostazioni dell'app](configure-common.md#configure-app-settings) al di fuori del codice dell'app. Quindi, è possibile accedervi in qualsiasi classe usando il modello standard di inserimento delle dipendenze ASP.NET Core:

```csharp
using Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve nested App Service app setting
            var myHierarchicalConfig = _configuration["My:Hierarchical:Config:Data"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

Se si configura un'impostazione dell'app con lo stesso nome nel servizio app e in *appsettings.js*, ad esempio, il valore del servizio app ha la precedenza sul valore di *appsettings.js* . Il valore locale *appsettings.json* consente di eseguire il debug dell'app in locale, ma il valore del servizio app consente di eseguire l'app in un prodotto con impostazioni di produzione. Le stringhe di connessione funzionano allo stesso modo. In questo modo, è possibile proteggere i segreti dell'applicazione all'esterno del repository di codice e accedere ai valori appropriati senza modificare il codice.

> [!NOTE]
> Si noti che è possibile accedere ai [dati di configurazione gerarchici](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/#hierarchical-configuration-data) in *appsettings.json* usando il `:` delimitatore standard di .NET Core. Per eseguire l'override di un'impostazione di configurazione gerarchica specifica nel servizio app, impostare il nome dell'impostazione dell'app con lo stesso formato delimitato nella chiave. nell' [cloud Shell](https://shell.azure.com)è possibile eseguire l'esempio seguente:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings My:Hierarchical:Config:Data="some value"
```

## <a name="deploy-multi-project-solutions"></a>Distribuire soluzioni per più progetti

Quando una soluzione di Visual Studio include più progetti, il processo di pubblicazione di Visual Studio include già la selezione del progetto da distribuire. Quando si esegue la distribuzione nel motore di distribuzione del servizio app, ad esempio con git o con la distribuzione ZIP, con l'automazione della compilazione attivata, il motore di distribuzione del servizio app seleziona il primo sito Web o progetto di applicazione Web trovato come app del servizio app. È possibile specificare il servizio app del progetto da usare specificando l' `PROJECT` impostazione dell'app. Ad esempio, eseguire il comando seguente nel [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Accedere ai log di diagnostica

ASP.NET Core fornisce un [provider di registrazione predefinito per il servizio app](https://docs.microsoft.com/aspnet/core/fundamentals/logging/#azure-app-service). In *Program.cs* del progetto aggiungere il provider all'applicazione tramite il `ConfigureLogging` metodo di estensione, come illustrato nell'esempio seguente:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {
            logging.AddAzureWebAppDiagnostics();
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

È quindi possibile configurare e generare log con il [modello standard di .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Per altre informazioni sulla risoluzione dei problemi relativi alle app ASP.NET Core nel servizio app, vedere [risolvere i problemi di ASP.NET Core in app Azure Service e IIS](https://docs.microsoft.com/aspnet/core/test/troubleshoot-azure-iis)

## <a name="get-detailed-exceptions-page"></a>Pagina get detailed Exceptions

Quando l'app ASP.NET Core genera un'eccezione nel debugger di Visual Studio, il browser visualizza una pagina di eccezione dettagliata, ma nel servizio app la pagina viene sostituita da un errore **HTTP 500** generico o **si è verificato un errore durante l'elaborazione della richiesta.** criteri.). Per visualizzare la pagina di eccezione dettagliata nel servizio app, aggiungere l' `ASPNETCORE_ENVIRONMENT` impostazione dell'app all'app eseguendo il comando seguente nella <a target="_blank" href="https://shell.azure.com" >cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Rilevare una sessione HTTPS

Nel servizio app la [terminazione SSL](https://wikipedia.org/wiki/TLS_termination_proxy) si verifica nei servizi di bilanciamento del carico di rete, pertanto tutte le richieste HTTPS raggiungano l'app come richieste HTTP non crittografate. Se la logica dell'app deve essere in grado di verificare se le richieste utente sono crittografate o meno, configurare il middleware delle intestazioni in *Startup.cs*:

- Configurare il middleware con [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) per l'inoltro delle intestazioni `X-Forwarded-For` e `X-Forwarded-Proto` in `Startup.ConfigureServices`.
- Aggiungere gli intervalli di indirizzi IP privati alle reti note, in modo che il middleware possa considerare attendibile il servizio di bilanciamento del carico del servizio app.
- Richiamare il metodo [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) in `Startup.Configure` prima di chiamare altri middleware.

Unendo tutti e tre gli elementi, il codice è simile all'esempio seguente:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

Per altre informazioni, vedere [Configurare ASP.NET Core per l'utilizzo di server proxy e servizi di bilanciamento del carico](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: App ASP.NET Core con database SQL](app-service-web-tutorial-dotnetcore-sqldb.md)
