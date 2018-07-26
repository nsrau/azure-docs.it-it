---
title: Azure Application Insights per ASP.NET Core | Microsoft Docs
description: Monitorare la disponibilità, le prestazioni e l'utilizzo delle applicazioni Web.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: mbullwin
ms.openlocfilehash: 6635906b6aae418fa3754f1152fae3e0d8903ffc
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989766"
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights per ASP.NET Core

Azure Application Insights offre funzionalità di monitoraggio avanzate dell'applicazione Web fino al livello di codice. È possibile monitorare con facilità la disponibilità, le prestazioni e l'utilizzo dell'applicazione Web. È anche possibile identificare e diagnosticare rapidamente gli errori nell'applicazione senza attendere che vengano segnalati da un utente.

Questo articolo illustra in modo dettagliato i passaggi necessari per creare un'applicazione [Razor Pages](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) di ASP.NET Core di esempio in Visual Studio e per avviare il monitoraggio con Azure Application Insights.

## <a name="prerequisites"></a>Prerequisiti

- NET Core 2.0.0 SDK o versione successiva.
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) versione 15.7.3 o versioni successive con carico di lavoro Sviluppo ASP.NET e Web. 

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Creare un progetto di ASP.NET Core in Visual Studio

1. Fare clic con il pulsante destro del mouse e avviare **Visual Studio 2017** come amministratore.
2. Selezionare **File** > **Nuovo** > **Progetto** (CTRL-MAIUSC-N).

   ![Screenshot del menu File > Nuovo > Progetto di Visual Studio](./media/app-insights-asp-net-core/001-new-project.png)

3. Espandere **Visual C#** e selezionare **.NET Core** > **Applicazione Web ASP.NET Core**. Immettere un nome nelle caselle **Nome** > **Nome soluzione** e selezionare **Crea nuovo repository Git**.

   ![Screenshot della procedura guidata File > Nuovo > Progetto di Visual Studio](./media/app-insights-asp-net-core/002-asp-net-core-web-application.png)

4. Selezionare **.NET Core** > **Applicazione Web** **ASP.NET Core 2.0** > **OK**.

    ![Screenshot del menu di selezione File > Nuovo > Progetto di Visual Studio](./media/app-insights-asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Ricerca in Application Insights

Per impostazione predefinita in Visual Studio versione 2015 Update 2 o versioni successive con un ASP.NET Core 2 + basato su progetto è possibile sfruttare [ricerca di Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio) anche prima del completamento in modo esplicito aggiungere Application Insights al progetto.

Per provare questa funzionalità:

1. Eseguire l'app facendo clic su IIS Express. ![Screenshot dell'icona IIS Express di Visual Studio](./media/app-insights-asp-net-core/004-iis-express.png)

2. Selezionare **Visualizzazione** > **Altre finestre** > **Ricerca in Application Insights**.

   ![Screenshot degli Strumenti di diagnostica di Visual Studio](./media/app-insights-asp-net-core/005-view-other-windows-search.png)

3. I dati di telemetria della sessione di debug sono attualmente disponibili solo per l'analisi locale. Per abilitare completamente Application Insights, selezionare **Idoneità telemetria** in alto a destra o seguire la procedura nella sezione successiva.

   ![Screenshot di ricerca di Application Insights in Visual Studio](./media/app-insights-asp-net-core/006-search.png)

> [!NOTE]
> Per altre informazioni su come Visual Studio illumina localmente funzionalità come [Ricerca di Application Insights](app-insights-visual-studio.md) e [CodeLens](app-insights-visual-studio-codelens.md) prima di aver aggiunto Application Insights al progetto ASP.NET Core, consultare la spiegazione alla [fine di questo articolo.](#Application-Insights-search-continued)

## <a name="add-application-insights-telemetry"></a>Aggiungere Application Insights Telemetry

1. Selezionare **Progetto** > **Aggiungi Application Insights Telemetry**. (Oppure è possibile fare clic con il pulsante destro del mouse su **Servizi connessi** e selezionare Aggiungi servizio connesso.)

    ![Screenshot del menu di selezione File > Nuovo > Progetto di Visual Studio](./media/app-insights-asp-net-core/007-project-add-telemetry.png)

2. Selezionare **Attività iniziali**. (A seconda della versione di Visual Studio il testo può variare leggermente. Alcune versioni meno recenti sono, invece, dotate di un pulsante **Inizia gratis**.)

    ![Screenshot del menu di selezione File > Nuovo > Progetto di Visual Studio](./media/app-insights-asp-net-core/008-get-started.png)

3. Scegliere una **Sottoscrizione** > **Risorsa** > **Registro**. appropriata.

## <a name="changes-made-to-your-project"></a>Modifiche apportate al progetto

Application Insights ha un impatto ridotto sulle prestazioni. Per esaminare le modifiche apportate al progetto con l'aggiunta di dati di telemetria di Application Insights:

Selezionare **Visualizza** > **Team Explorer** (CTRL+\, CTRL+M) > **Progetto** > **Modifiche**

- Quattro modifiche in totale:

  ![Screenshot dei file modificati con l'aggiunta di Application Insights](./media/app-insights-asp-net-core/009-changes.png)

- Viene creato un nuovo file:

   _ConnectedService.json_

```json
{
  "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
  "Version": "8.12.10405.1",
  "GettingStartedDocument": {
    "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
  }
}
```

- Vengono modificati tre file: (Commenti aggiuntivi aggiunti per evidenziare le modifiche)

  _appsettings.json_

```json
{
  "Logging": {
    "IncludeScopes": false,
    "LogLevel": {
      "Default": "Warning"
    }
  },
// Changes to file post adding Application Insights Telemetry:
  "ApplicationInsights": {
    "InstrumentationKey": "10101010-1010-1010-1010-101010101010"
  }
}
//
```

  _ContosoDotNetCore.csproj_

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp2.0</TargetFramework>
 <!--Changes to file post adding Application Insights Telemetry:-->
    <ApplicationInsightsResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsResourceId>
    <ApplicationInsightsAnnotationResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsAnnotationResourceId>
<!---->
  </PropertyGroup>
  <ItemGroup>
 <!--Changes to file post adding Application Insights Telemetry:-->
    <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.1.1" />
<!---->
    <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.8" />
  </ItemGroup>
  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
  </ItemGroup>
<!--Changes to file post adding Application Insights Telemetry:-->
  <ItemGroup>
    <WCFMetadata Include="Connected Services" />
  </ItemGroup>
<!---->
</Project>
```

   _Program.cs_

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace DotNetCore
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
// Change to file post adding Application Insights Telemetry:
                .UseApplicationInsights()
//
                .UseStartup<Startup>()
                .Build();
    }
}
```

## <a name="synthetic-transactions-with-powershell"></a>Transazioni sintetiche con PowerShell

Per automatizzare le richieste nei confronti dell'app con le transazioni sintetiche.

1. Eseguire l'app facendo clic su IIS Express. ![Screenshot dell'icona IIS Express di Visual Studio](./media/app-insights-asp-net-core/004-iis-express.png)

2. Copiare l'URL dalla barra degli indirizzi del browser. È nel formato `http://localhost:{random port number}`

   ![Screenshot della barra degli indirizzi URL del browser](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Eseguire il ciclo di PowerShell seguente per creare 100 transazioni sintetiche per l'app di test. Modificare il numero di porta dopo **localhost:** in modo che corrisponda all'URL copiato nel passaggio precedente.

   ```PowerShell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>Aprire il portale Application Insights

Dopo aver eseguito PowerShell nella sezione precedente, avviare Application Insights per visualizzare le transazioni e confermare che i dati vengono raccolti. 

Nel menu di Visual Studio selezionare **Progetto** > **Application Insights** > **Apri portale Application Insights**.

   ![Screenshot della pagina Panoramica di Application Insights](./media/app-insights-asp-net-core/010-portal.png)

> [!NOTE]
> Nello screenshot di esempio precedente **Live Stream**, **Tempo di caricamento della visualizzazione pagina** e **Richieste non riuscite** non vengono attualmente raccolti. La prossima sezione illustrerà in modo dettagliato i passaggi necessari per aggiungere ognuna di queste funzionalità. Se **Live Stream** e **Tempo di caricamento della visualizzazione pagina** vengono già raccolti, seguire la procedura solo per **Richieste non riuscite**.

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>Raccogliere Richieste non riuscite, Live Stream e Tempo di caricamento della visualizzazione pagina

### <a name="failed-requests"></a>Richieste non riuscite

Tecnicamente i dati di **Richieste non riuscite** vengono raccolti, ma non si sono ancora verificati eventi di questo tipo. Per accelerare il processo, è possibile aggiungere un'eccezione personalizzata al progetto esistente per simulare un'eccezione reale. Se l'app è ancora in esecuzione in Visual Studio, prima di procedere fare clic su **Termina debug** (MAIUSC+F5).

1. In **Esplora soluzioni** espandere **Pagine** > **About.cshtml** e aprire **About.cshtml.cs**.

   ![Screenshot di Esplora soluzioni di Visual Studio](./media/app-insights-asp-net-core/011-about.png)

2. Aggiungere un'eccezione in ``Message=`` e salvare le modifiche nel file.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc.RazorPages;

    namespace DotNetCore.Pages
    {
        public class AboutModel : PageModel
        {
            public string Message { get; set; }

            public void OnGet()
            {
                Message = "Your application description page.";
                throw new Exception("Test Exception");
            }
        }
    }
    ```

### <a name="live-stream"></a>Live Stream

Per accedere alla funzionalità Live Stream di Application Insights con ASP.NET Core eseguire l'aggiornamento ai pacchetti NuGet **Microsoft.ApplicationInsights.AspNetCore 2.2.0**.

In Visual Studio selezionare **Progetto** > **Gestisci pacchetti NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > Versione **2.2.0** > **Aggiorna**.

  ![Screenshot di Gestione pacchetti NuGet](./media/app-insights-asp-net-core/012-nuget-update.png)

Si verificano più richieste di conferma. Leggere e accettare se si accettano le modifiche.

### <a name="page-view-load-time"></a>Tempo di caricamento della visualizzazione pagina

1. In Visual Studio passare a **Esplora soluzioni** > **Pagine**, dove sarà necessario modificare due file: _Layout.cshtml_ e _ViewImports.cshtml_.

2. In __ViewImports.cshtml_ aggiungere il codice seguente:

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. In **_Layout.cshtml** aggiungere la riga riportata di seguito davanti al tag ``</head>``, ma anche prima di tutti gli altri script.

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>Testare Richieste non riuscite, Tempo di caricamento della visualizzazione pagina e Live Stream

Per eseguire il test e verificare che tutto funzioni:

1. Eseguire l'app facendo clic su IIS Express. ![Screenshot dell'icona IIS Express di Visual Studio](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Passare alla pagina **Informazioni su** per attivare l'eccezione di test. Se l'esecuzione avviene in modalità debug, sarà necessario fare clic su **Continua** in Visual Studio per consentire di visualizzare l'eccezione in Application Insights.)

3. Eseguire nuovamente lo script di transazione simulato di PowerShell illustrato in precedenza. Potrebbe essere necessario modificare il numero di porta nello script.

4. Se la pagina Panoramica di Application Insights non è ancora aperta, selezionare **Progetto** > **Application Insights** > **Apri portale Application Insights** nel menu di Visual Studio. 

   > [!TIP]
   > Se il nuovo traffico non è ancora visibile, controllare **Intervallo di tempo** e fare clic su **Aggiorna**.

   ![Screenshot della finestra Panoramica](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Selezionare Live Stream.

   ![Screenshot di Live Metrics Stream](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   Se lo script di PowerShell è ancora in esecuzione, dovrebbero essere visualizzate le metriche attive. Se lo script è stato arrestato, eseguirlo nuovamente con Live Stream aperto.

## <a name="app-insights-sdk-comparison"></a>Confronto tra SDK di Application Insights

Il gruppo di prodotti Application Insights è stato appositamente studiato per ridurre le differenze di funzionalità tra [.NET Framework SDK completo](https://github.com/Microsoft/ApplicationInsights-dotnet) e .NET Core SDK. Con la versione 2.2.0 di [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) per Application Insights il gap di funzionalità è stato ampiamente colmato.

Di seguito è riportata una tabella per comprendere meglio le differenze esistenti e i compromessi raggiunti tra [.NET e .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

   | Confronto tra SDK | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Metriche attive**      | **+** |**-** | **+** |
   | **Canale di telemetria server** | **+** |**-** | **+**|
   |**Campionamento adattivo**| **+** | **-** | **+**|
   | **Chiamate alle dipendenze SQL**     | **+** |**-** | **+**|
   | **Contatori delle prestazioni*** | **+** | **-**| **-**|

In questo contesto il termine _contatori delle prestazioni_ si riferisce ai [contatori delle prestazioni lato server](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters), ad esempio il processore, la memoria e l'utilizzo dischi.

## <a name="open-source-sdk"></a>SDK open source
[Leggere e contribuire al codice](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="application-insights-search-continued"></a>Ricerche di Application Insights continuate

Per comprendere meglio come la ricerca di Application Insights funziona in Visual Studio per un progetto ASP.NET Core 2 anche quando non è ancora avvenuta un'installazione esplicita dei pacchetti NuGet di Application Insights. Può essere utile esaminare l'Output di Debug.

Se si esegue la ricerca dell'output per la parola _insight_ verranno evidenziati risultati simili al seguente:

```DebugOuput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

CoreCLR sta caricando due assembly: 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

E la voce _annullata_ in ogni istanza di telemetria di Application Insights indica che questa applicazione non è associata a un ikey, pertanto i dati che vengono generati durante l'esecuzione dell'app non sono inviati ad Azure e sono disponibili solo per l'analisi e la ricerca locali.

In parte ciò è possibile perché il pacchetto NuGet _Microsoft.AspNetCore.All_ accetta come una dipendenza [_Microsoft.ASPNetCoreApplicationInsights.HostingStartup_](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1)

![Screenshot del Grafico di dipendenza di NuGet per Microsoft.AspNETCore.all](./media/app-insights-asp-net-core/013-dependency.png)

All'esterno di Visual Studio se si stava modificando un progetto ASP.NET Core in VSCode o un altro editor, questi assembly non caricherebbero automaticamente durante il debug se in modo esplicito non è stato aggiuntoApplication Insights al progetto.

Tuttavia, in Visual Studio l'illuminazione backup delle funzionalità di Application Insights locale da assembly esterni avviene tramite l'utilizzo dell'[interfaccia IHostingStartup](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1) che aggiunge dinamicamente Application Insights durante il debug.

Per altre informazioni su come migliorare un'app da un [assembly esterno in ASP.NET Core con IHostingStartup](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1). 

### <a name="how-to-disable-application-insights-in-visual-studio-net-core-projects"></a>Come disabilitare Application Insights nei progetti di Visual Studio .NET Core

Mentre l'illuminatore automatico accende la funzionalità di ricerca di Application Insights, può essere utile per alcuni vedere dati di telemetria di debug generati quando non ci si aspettava che sarebbe stato poco chiaro.

Se la sola disabilitazione della generazione dati di telemetria è sufficiente è possibile aggiungere questo blocco di codice al metodo di configurazione del file _Startup.cs_:

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

Il CoreCLR caricherà ancorà _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ e _Microsoft.ApplicationInsights.AspNetCore.dll_, ma non eseguono alcuna azione.

Se si desidera disabilitare completamente Application Insights nel progetto di Visual Studio .NET Core, il metodo preferito consiste nel selezionare **Strumenti** > **Opzioni** >  **Progetti e soluzioni** > **Progetti Web** > e selezionare la casella per disabilitare Application Insights locale per i progetti web ASP.NET Core. Questa funzionalità è stata aggiunta in Visual Studio 15,6.

![Screenshot della schermata del progetti web della finestra di opzioni di Visual Studio](./media/app-insights-asp-net-core/014-disable.png)

Se si esegue una versione precedente di Visual Studio e si desidera rimuovere completamente tutti gli assembly caricati tramite IHostingStartup è possibile aggiungere:

`.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")`

a _Program.cs_:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace DotNetCore
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
                .UseStartup<Startup>()
                .Build();
    }
}
```

Oppure in alternativa è possibile aggiungere ``"ASPNETCORE_preventHostingStartup": "True"`` alle variabili di ambiente _launchSettings.json_.

Il problema con l'uso di uno di questi metodi è che non disattiva solo Application Insights, disabiliterà tutto ciò che stava sfruttando la funzionalità della luce di IHostingStartup in Visual Studio.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Passaggi successivi
* [Esplorare i flussi degli utenti](app-insights-usage-flows.md) per comprendere la modalità di navigazione dell'app.
* [Configurare la raccolta di snapshot](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications) per visualizzare lo stato del codice sorgente e delle variabili nel momento in cui viene generata un'eccezione.
* [Utilizzare l'API](app-insights-api-custom-events-metrics.md) per inviare gli eventi e le metriche per una visualizzazione più dettagliata dell'utilizzo e delle prestazioni dell'app.
* [Test di disponibilità](app-insights-monitor-web-app-availability.md) controlla costantemente l’app da tutto il mondo.
