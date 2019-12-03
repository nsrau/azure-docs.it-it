---
title: Configurare app Linux ASP.NET Core
description: Informazioni su come configurare un contenitore ASP.NET Core predefinito per l'app. Questo articolo illustra le attività di configurazione più comuni.
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: d26c490ad37b25785ff1347cccf1e2be21bba277
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670457"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Configurare un'app Linux ASP.NET Core per app Azure servizio

ASP.NET Core le app devono essere distribuite come file binari compilati. Lo strumento di pubblicazione di Visual Studio compila la soluzione e quindi distribuisce direttamente i file binari compilati, mentre il motore di distribuzione del servizio app distribuisce innanzitutto il repository di codice e quindi compila i file binari.

Questa guida fornisce i concetti chiave e le istruzioni per ASP.NET Core gli sviluppatori che usano un contenitore Linux incorporato nel servizio app. Se non si è mai usato app Azure servizio, seguire prima l'esercitazione [ASP.NET Core avvio rapido](quickstart-dotnetcore.md) e [ASP.NET Core con il database SQL](tutorial-dotnetcore-sqldb-app.md) .

## <a name="show-net-core-version"></a>Mostra versione di .NET Core

Per visualizzare la versione corrente di .NET Core, eseguire il comando seguente nella [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Per visualizzare tutte le versioni supportate di .NET Core, eseguire il comando seguente nella [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Impostare la versione di .NET Core

Eseguire il comando seguente nella [cloud Shell](https://shell.azure.com) per impostare la versione di .NET Core su 2,1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>Accedere alle variabili di ambiente

Nel servizio app è possibile [configurare le impostazioni dell'app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) al di fuori del codice dell'app. Quindi, è possibile accedervi in qualsiasi classe usando il modello standard di inserimento delle dipendenze ASP.NET Core:

```csharp
include Microsoft.Extensions.Configuration;

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
            // retrieve App Service app setting
            var myAppSetting = _configuration["MySetting"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

Se si configura un'impostazione dell'app con lo stesso nome nel servizio app e in *appSettings. JSON*, ad esempio, il valore del servizio app avrà la precedenza sul valore *appSettings. JSON* . Il valore locale *appSettings. JSON* consente di eseguire il debug dell'app in locale, ma il valore del servizio app consente di eseguire l'app in un prodotto con impostazioni di produzione. Le stringhe di connessione funzionano allo stesso modo. In questo modo, è possibile proteggere i segreti dell'applicazione all'esterno del repository di codice e accedere ai valori appropriati senza modificare il codice.

## <a name="get-detailed-exceptions-page"></a>Pagina get detailed Exceptions

Quando l'app ASP.NET genera un'eccezione nel debugger di Visual Studio, il browser visualizza una pagina di eccezione dettagliata, ma nel servizio app la pagina viene sostituita da un errore **HTTP 500** generico o **si è verificato un errore durante l'elaborazione della richiesta.** criteri.). Per visualizzare la pagina di eccezione dettagliata nel servizio app, aggiungere l'impostazione dell'app `ASPNETCORE_ENVIRONMENT` all'app eseguendo il comando seguente nel <a target="_blank" href="https://shell.azure.com" >cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Rilevare una sessione HTTPS

Nel servizio app la [terminazione SSL](https://wikipedia.org/wiki/TLS_termination_proxy) si verifica nei servizi di bilanciamento del carico di rete, pertanto tutte le richieste HTTPS raggiungano l'app come richieste HTTP non crittografate. Se la logica dell'app deve essere in grado di verificare se le richieste utente sono crittografate o meno, configurare il middleware delle intestazioni in *Startup.cs*:

- Configurare il middleware con [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) per l'invio delle intestazioni `X-Forwarded-For` e `X-Forwarded-Proto` in `Startup.ConfigureServices`.
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

Per altre informazioni, vedere [configurare ASP.NET Core per lavorare con i server proxy e i bilanciamenti del carico](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="deploy-multi-project-solutions"></a>Distribuire soluzioni per più progetti

Quando si distribuisce un repository ASP.NET nel motore di distribuzione con un file con estensione *csproj* nella directory radice, il motore distribuisce il progetto. Quando si distribuisce un repository ASP.NET con un file con *estensione sln* nella directory radice, il motore preleva il primo sito Web o progetto di applicazione Web trovato come app del servizio app. È possibile che il motore non scelga il progetto desiderato.

Per distribuire una soluzione per più progetti, è possibile specificare il progetto da usare nel servizio app in due modi diversi:

### <a name="using-deployment-file"></a>Uso del file. Deployment

Aggiungere un file con *estensione Deployment* alla radice del repository e aggiungere il codice seguente:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Uso delle impostazioni dell'app

Nel <a target="_blank" href="https://shell.azure.com">Azure cloud Shell</a>aggiungere un'impostazione dell'app all'app del servizio app eseguendo il comando dell'interfaccia della riga di comando seguente. Sostituire *\<nome app >* , *\<nome-gruppo-risorse >* e *\<nome progetto >* con i valori appropriati.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Accedere ai log di diagnostica

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Aprire una sessione SSH nel browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: ASP.NET Core app con il database SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Domande frequenti sul Servizio app di Azure in Linux](app-service-linux-faq.md)