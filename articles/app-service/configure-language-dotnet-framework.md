---
title: Configurare app ASP.NET
description: Informazioni su come configurare un'app ASP.NET nel servizio app Azure. Questo articolo illustra le attività di configurazione più comuni.
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 30fddaec9ca5d0439beadedf7c5ca6b6c7d51d83
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88961704"
---
# <a name="configure-an-aspnet-app-for-azure-app-service"></a>Configurare un'app ASP.NET per il servizio app Azure

> [!NOTE]
> Per ASP.NET Core, vedere [configurare un'app ASP.NET Core per il servizio app Azure](configure-language-dotnetcore.md)

Le app ASP.NET devono essere distribuite nel servizio app Azure come file binari compilati. Lo strumento di pubblicazione di Visual Studio compila la soluzione e quindi distribuisce direttamente i file binari compilati, mentre il motore di distribuzione del servizio app distribuisce innanzitutto il repository di codice e quindi compila i file binari.

Questa guida fornisce i concetti chiave e le istruzioni per gli sviluppatori ASP.NET. Se non si è mai usato app Azure servizio, seguire prima l'esercitazione [introduttiva di ASP.NET](quickstart-dotnet-framework.md) e [ASP.NET con il database SQL](app-service-web-tutorial-dotnet-sqldatabase.md) .

## <a name="show-supported-net-framework-runtime-versions"></a>Mostra versioni di Runtime .NET Framework supportate

Nel servizio app, nelle istanze di Windows sono già installate tutte le versioni di .NET Framework supportate. Per visualizzare le versioni di runtime e SDK di .NET Framework disponibili, passare a `https://<app-name>.scm.azurewebsites.net/DebugConsole` ed eseguire il comando appropriato nella console basata su browser:

Per le versioni di runtime di CLR 4 (.NET Framework 4 e versioni successive):

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\"
```

La versione più recente del .NET Framework potrebbe non essere immediatamente disponibile.

Per le versioni di runtime di CLR 2 (.NET Framework 3,5 e versioni precedenti):

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\"
```

## <a name="show-current-net-framework-runtime-version"></a>Mostra la versione corrente di .NET Framework Runtime

Eseguire il comando seguente nella [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query netFrameworkVersion
```

`v4.0`Il valore indica che viene utilizzata la versione più recente di CLR 4 (.NET Framework 4. x). Il valore `v2.0` indica che viene utilizzata una versione di CLR 2 (.NET Framework 3,5).

## <a name="set-net-framework-runtime-version"></a>Imposta .NET Framework versione runtime

Per impostazione predefinita, il servizio app usa la versione più recente di .NET Framework supportata per eseguire l'app ASP.NET. Per eseguire l'app usando .NET Framework 3,5, eseguire il comando seguente nella [cloud Shell](https://shell.azure.com) (la versione 2.0 indica CLR 2):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --net-framework-version v2.0
```

## <a name="access-environment-variables"></a>Accedere alle variabili di ambiente

Nel servizio app è possibile [impostare le impostazioni dell'app e le](configure-common.md#configure-app-settings) stringhe di connessione all'esterno del codice dell'app. Quindi, è possibile accedervi in qualsiasi classe usando il modello ASP.NET standard:

```csharp
using System.Configuration;
...
// Get an app setting
ConfigurationManager.AppSettings["MySetting"];
// Get a connection string
ConfigurationManager.ConnectionStrings["MyConnection"];
}
```

Se si configura un'impostazione dell'app con lo stesso nome nel servizio app e in *web.config*, il valore del servizio app avrà la precedenza sul valore di *web.config* . Il valore di *web.config* locale consente di eseguire il debug dell'app in locale, ma il valore del servizio app consente di eseguire l'app in un prodotto con impostazioni di produzione. Le stringhe di connessione funzionano allo stesso modo. In questo modo, è possibile proteggere i segreti dell'applicazione all'esterno del repository di codice e accedere ai valori appropriati senza modificare il codice.

## <a name="deploy-multi-project-solutions"></a>Distribuire soluzioni per più progetti

Quando una soluzione di Visual Studio include più progetti, il processo di pubblicazione di Visual Studio include già la selezione del progetto da distribuire. Quando si esegue la distribuzione nel motore di distribuzione del servizio app, ad esempio con git o con la distribuzione ZIP, con l'automazione della compilazione attivata, il motore di distribuzione del servizio app seleziona il primo sito Web o progetto di applicazione Web trovato come app del servizio app. È possibile specificare il servizio app del progetto da usare specificando l' `PROJECT` impostazione dell'app. Ad esempio, eseguire il comando seguente nel [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="get-detailed-exceptions-page"></a>Pagina get detailed Exceptions

Quando l'app ASP.NET genera un'eccezione nel debugger di Visual Studio, il browser visualizza una pagina di eccezione dettagliata, ma nel servizio app la pagina viene sostituita da un messaggio di errore generico. Per visualizzare la pagina di eccezione dettagliata nel servizio app, aprire il file di *Web.config* e aggiungere l' `<customErrors mode="Off"/>` elemento sotto l' `<system.web>` elemento. Ad esempio:

```xml
<system.web>
    <customErrors mode="Off"/>
</system.web>
```

Ridistribuire l'app con il *Web.config*aggiornato. A questo punto verrà visualizzata la stessa pagina di eccezione dettagliata.

## <a name="access-diagnostic-logs"></a>Accedere ai log di diagnostica

È possibile aggiungere messaggi di diagnostica nel codice dell'applicazione usando [System. Diagnostics. Trace](/dotnet/api/system.diagnostics.trace). Ad esempio: 

```csharp
Trace.TraceError("Record not found!"); // Error trace
Trace.TraceWarning("Possible data loss"); // Warning trace
Trace.TraceInformation("GET /Home/Index"); // Information trace
```

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Creare un'app ASP.NET in Azure con un database SQL](app-service-web-tutorial-dotnet-sqldatabase.md)