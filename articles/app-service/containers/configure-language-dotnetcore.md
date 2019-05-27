---
title: Configurare le app ASP.NET Core - servizio App di Azure | Microsoft Docs
description: Informazioni su come configurare le app ASP.NET Core per lavorare in servizio App di Azure
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: f2781e3cc2433f73ba7ff33e5c452e29de746adf
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956209"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Configurare una Linux app ASP.NET Core per servizio App di Azure

Le app ASP.NET Core devono essere distribuite come file binari compilati. Lo strumento di distribuzione di Visual Studio compila la soluzione e quindi distribuisce i file binari compilati direttamente, mentre il motore di distribuzione del servizio App distribuisce il repository di codice prima di tutto, quindi compila i file binari.

Questa guida fornisce istruzioni per ASP.NET Core e i concetti chiave gli sviluppatori che usano un contenitore Linux incorporato nel servizio App. Se non si è mai usato il servizio App di Azure, seguire le [Guida introduttiva di ASP.NET Core](quickstart-dotnetcore.md) e [ASP.NET Core con l'esercitazione sul Database SQL](tutorial-dotnetcore-sqldb-app.md) prima.

## <a name="show-net-core-version"></a>Versione di .NET Core Show

Per visualizzare la versione corrente di .NET Core, eseguire il comando seguente [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Per visualizzare tutte le versioni di .NET Core, eseguire il comando seguente [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Impostare la versione .NET Core

Eseguire il comando seguente [Cloud Shell](https://shell.azure.com) su cui impostare la versione di .NET Core 2.1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>Accedere alle variabili di ambiente

Nel servizio app è possibile [configurare le impostazioni dell'app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) al di fuori del codice dell'app. Quindi è possibile accedervi usando il modello ASP.NET standard:

```csharp
include Microsoft.Extensions.Configuration;
// retrieve App Service app setting
System.Configuration.ConfigurationManager.AppSettings["MySetting"]
// retrieve App Service connection string
Configuration.GetConnectionString("MyDbConnection")
```

Se si configura un'impostazione dell'app con lo stesso nome nel servizio App e nella *Web. config*, il valore del servizio App ha la precedenza sul valore di Web. config. Il valore di Web. config consente di eseguire il debug dell'app in locale, ma il valore del servizio App consente l'esecuzione dell'app nel prodotto con le impostazioni di produzione. Le stringhe di connessione funzionano nello stesso modo. In questo modo, è possibile mantenere i segreti dell'applicazione all'esterno del repository di codice e accedere ai valori appropriati senza modificare il codice.

## <a name="get-detailed-exceptions-page"></a>Ottenere le eccezioni dettagliate pagina

Quando l'app ASP.NET genera un'eccezione nel debugger di Visual Studio, il browser visualizza una pagina di dettagli dell'eccezione, ma nel servizio App di tale pagina viene sostituita da un oggetto generico **HTTP 500** errore o **un errore durante l'elaborazione l'elaborazione della richiesta.** criteri.). Per visualizzare la pagina di dettagli dell'eccezione nel servizio App, aggiungere il `ASPNETCORE_ENVIRONMENT` impostazione dell'app all'App eseguendo il comando seguente nel <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Rilevare una sessione HTTPS

Nel servizio app la [terminazione SSL](https://wikipedia.org/wiki/TLS_termination_proxy) si verifica nei servizi di bilanciamento del carico di rete, pertanto tutte le richieste HTTPS raggiungano l'app come richieste HTTP non crittografate. Se la logica dell'app deve sapere se l'utente richiede sono crittografati o meno, configurare il Middleware delle intestazioni inoltrate nel *Startup.cs*:

- Configurare il middleware con [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) inoltrare le `X-Forwarded-For` e `X-Forwarded-Proto` intestazioni in `Startup.ConfigureServices`.
- Aggiungere intervalli di indirizzi IP privati per le reti note, in modo che il middleware può considerare attendibili il bilanciamento del carico del servizio App.
- Richiama il [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) metodo `Startup.Configure` prima di chiamare altri middleware.

Combinando i tre elementi, il codice è simile al seguente:

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

Per altre informazioni, vedere [configurare ASP.NET Core per lavorare con i server proxy e bilanciamento del carico](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="deploy-multi-project-solutions"></a>Distribuire soluzioni multiprogetto

Quando si distribuisce un repository ASP.NET per il motore di distribuzione con un *file con estensione csproj* file nella directory radice, il motore di distribuzione del progetto. Quando si distribuisce un repository ASP.NET con un *sln* file nella directory radice, il motore rileva il primo sito Web o un progetto di applicazione Web rileva come App del servizio app. È possibile che il motore non selezionare il progetto desiderato.

Per distribuire una soluzione multiprogetto, è possibile specificare il progetto da utilizzare nel servizio App in due modi diversi:

### <a name="using-deployment-file"></a>Utilizzo di file con estensione Deployment

Aggiungere un *con estensione Deployment* file alla radice del repository e aggiungere il codice seguente:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Usando le impostazioni dell'app

Nel <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>, aggiungere un'impostazione dell'app all'App del servizio app eseguendo il comando seguente dell'interfaccia della riga. Sostituire  *\<app-name >*,  *\<resource-group-name >*, e  *\<nome-progetto >* con i valori appropriati .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Accedere ai log di diagnostica

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Aprire una sessione SSH nel browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: App ASP.NET Core con database SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Domande frequenti sul Servizio app di Azure in Linux](app-service-linux-faq.md)