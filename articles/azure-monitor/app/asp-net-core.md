---
title: Azure Application Insights per ASP.NET Core | Microsoft Docs
description: Monitorare le applicazioni Web ASP.NET Core per identificare disponibilità, prestazioni e utilizzo.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: mbullwin
ms.openlocfilehash: 7f3b8101b633c977201b6c413ad12e4bbe55e9a7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011789"
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights per ASP.NET Core

Azure Application Insights offre funzionalità di monitoraggio avanzate dell'applicazione Web fino al livello di codice. È possibile monitorare con facilità la disponibilità, le prestazioni e l'utilizzo dell'applicazione Web. È anche possibile identificare e diagnosticare rapidamente gli errori nell'applicazione senza attendere che vengano segnalati da un utente.

Questo articolo presenta in modo dettagliato i passaggi necessari per creare un'applicazione [Razor Pages](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) ASP.NET Core di esempio in Visual Studio. Descrive anche come avviare il monitoraggio tramite Application Insights.

## <a name="prerequisites"></a>Prerequisiti

- .NET Core 2.0.0 SDK o versioni successive
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) 15.7.3 o versioni successive con carico di lavoro di sviluppo Web e ASP.NET

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Creare un progetto di ASP.NET Core in Visual Studio

1. Fare clic con il pulsante destro del mouse su **Visual Studio 2017** e quindi scegliere **Esegui come amministratore**.
2. Selezionare **File** > **Nuovo** > **Progetto** (CTRL+MAIUSC+N).

   ![Screenshot del menu Nuovo progetto di Visual Studio](./media/asp-net-core/001-new-project.png)

3. Espandere **Visual C#**. Selezionare **.NET Core** > **Applicazione Web ASP.NET Core**. Immettere un nome di progetto e un nome di soluzione e quindi selezionare **Crea nuovo repository Git**.

   ![Screenshot della procedura guidata Nuovo progetto di Visual Studio](./media/asp-net-core/002-asp-net-core-web-application.png)

4. Selezionare **.NET Core** > **ASP.NET Core 2.0** **Applicazione Web** > **OK**.

    ![Screenshot della selezione del modello per il nuovo progetto di Visual Studio](./media/asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Ricerca di Application Insights

Nei progetti basati su Visual Studio 2015 Update 2 o versioni successive con ASP.NET Core 2 e versioni successive è possibile usare [Ricerca di Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio), anche prima di aggiungere in modo esplicito Application Insights al progetto.

Per testare questa funzionalità:

1. Eseguire l'app. Per eseguire l'app, selezionare l'icona **IIS Express** (![Screenshot dell'icona di IIS Express in Visual Studio](./media/asp-net-core/004-iis-express.png)).

2. Selezionare **Visualizza** > **Altre finestre** > **Ricerca di Application Insights**.

   ![Screenshot della selezione degli strumenti di diagnostica di Visual Studio](./media/asp-net-core/005-view-other-windows-search.png)

3. Attualmente la telemetria della sessione di debug è disponibile solo per l'analisi locale. Per abilitare completamente Application Insights, selezionare **Livello di preparazione per la telemetria** nell'angolo in alto a destra o completare i passaggi indicati nella prossima sezione.

   ![Screenshot di ricerca di Application Insights in Visual Studio](./media/asp-net-core/006-search.png)

> [!NOTE]
> Per altre informazioni sul modo in cui Visual Studio attiva funzionalità come [Ricerca di Application Insights](../../azure-monitor/app/visual-studio.md) e [CodeLens](../../azure-monitor/app/visual-studio-codelens.md) in locale prima di aggiungere Application Insights al progetto ASP.NET Core, vedere [Ricerca di Application Insights - Continua](#application-insights-search-continued).

## <a name="add-application-insights-telemetry"></a>Aggiungere Application Insights Telemetry

1. Selezionare **Progetto** > **Aggiungi Application Insights Telemetry**. In alternativa, è possibile fare clic con il pulsante destro del mouse su **Servizi connessi** e quindi scegliere **Aggiungi servizio connesso**.

    ![Screenshot del menu di selezione del nuovo progetto di Visual Studio](./media/asp-net-core/007-project-add-telemetry.png)

2. Selezionare **Attività iniziali**. A seconda della versione di Visual Studio, il testo può variare leggermente. Alcune versioni meno recenti includono invece un pulsante **Inizia gratis**.

    ![Screenshot del pulsante Attività iniziali di Application Insights](./media/asp-net-core/008-get-started.png)

3. Selezionare la sottoscrizione e quindi **Risorsa** > **Registra**.

## <a name="changes-made-to-your-project"></a>Modifiche apportate al progetto

Application Insights ha un impatto ridotto sulle prestazioni. Per esaminare le modifiche apportate al progetto con l'aggiunta di dati di telemetria di Application Insights:

Selezionare **Visualizza** > **Team Explorer** (CTRL+\, CTRL+M) > **Progetto** > **Modifiche**

- Vengono visualizzate quattro modifiche totali:

  ![Screenshot dei file modificati con l'aggiunta di Application Insights](./media/asp-net-core/009-changes.png)

- Viene creato un nuovo file:

  - _ConnectedService.json_

    ```json
    {
     "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
     "Version": "8.12.10405.1",
     "GettingStartedDocument": {
       "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
     }
    }
    ```

- Vengono modificati tre file (con commenti aggiuntivi per evidenziare le modifiche):

  - _appsettings.json_:

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

  - _ContosoDotNetCore.csproj_:

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

  -  _Program.cs_:

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

## <a name="send-ilogger-logs-to-application-insights"></a>Inviare i log di ILogger ad Application Insights

Application Insights supporta i log di acquisizione inviati tramite ILogger. Per configurare gli esempi di codice di registrazione estrazione [qui](https://docs.microsoft.com/azure/azure-monitor/app/ilogger).

## <a name="synthetic-transactions-with-powershell"></a>Transazioni sintetiche con PowerShell

Per automatizzare le richieste all'app tramite transazioni sintetiche:

1. Per eseguire l'app, selezionare l'icona ![Screenshot dell'icona IIS Express di Visual Studio](./media/asp-net-core/004-iis-express.png) .

2. Copiare l'URL dalla barra degli indirizzi del browser. L'URL è in questo formato: `http://localhost:<port number>`.

   ![Screenshot dell'URL nella barra degli indirizzi del browser](./media/asp-net-core/0013-copy-url.png)

3. Eseguire il ciclo di PowerShell seguente per creare 100 transazioni sintetiche usando l'app di test. Modificare il numero di porta dopo `localhost:` in modo che corrisponda all'URI copiato nel passaggio precedente. Ad esempio: 

   ```PowerShell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-the-application-insights-portal"></a>Aprire il portale di Application Insights

Dopo aver eseguito i comandi di PowerShell nella sezione precedente, aprire Application Insights per visualizzare le transazioni e confermare che i dati vengono raccolti. 

Nel menu di Visual Studio selezionare **Progetto** > **Application Insights** > **Apri portale Application Insights**.

   ![Screenshot della pagina Panoramica di Application Insights](./media/asp-net-core/010-portal.png)

> [!NOTE]
> Nello screenshot di esempio precedente non vengono raccolti i dati di **Live Stream**, **Tempo di caricamento della visualizzazione pagina** e **Richieste non riuscite**. La sezione seguente descrive in modo dettagliato i passaggi per aggiungere questi dati. Se si stanno già raccogliendo i dati di **Live Stream** e **Tempo di caricamento della visualizzazione pagina**, completare la procedura solo per **Richieste non riuscite**.

## <a name="collect-failed-requests-live-stream-and-page-view-load-time"></a>Raccogliere i dati di Richieste non riuscite, Live Stream e Tempo di caricamento della visualizzazione pagina

### <a name="failed-requests"></a>Richieste non riuscite

Tecnicamente, i dati delle richieste non riuscite vengono raccolti, ma non sono ancora presenti richieste di questo tipo. Per accelerare il processo, è possibile aggiungere un'eccezione personalizzata al progetto esistente per simulare un'eccezione reale. Se l'app è ancora in esecuzione in Visual Studio, prima di continuare selezionare **Termina debug** (MAIUSC+F5).

1. In **Esplora soluzioni** espandere **Pagine** > **About.cshtml** e quindi aprire *About.cshtml.cs*.

   ![Screenshot di Esplora soluzioni di Visual Studio](./media/asp-net-core/011-about.png)

2. Aggiungere un'eccezione in ``Message=`` e quindi salvare la modifica nel file.

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

Per accedere alla funzionalità Live Stream di Application Insights con ASP.NET Core, aggiornare i pacchetti NuGet Microsoft.ApplicationInsights.AspNetCore 2.2.0.

In Visual Studio selezionare **Progetto** > **Gestisci pacchetti NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > (versione) **2.2.0** > **Aggiorna**.

  ![Screenshot di Gestione pacchetti NuGet](./media/asp-net-core/012-nuget-update.png)

Vengono visualizzate più richieste di conferma. Leggere e accettare se si accettano le modifiche.

### <a name="page-view-load-time"></a>Tempo di caricamento della visualizzazione pagina

1. In Visual Studio passare a **Esplora soluzioni** > **Pagine**. È necessario modificare due file: *Layout.cshtml* e *ViewImports.cshtml*.

2. In *ViewImports.cshtml* aggiungere il codice seguente:

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. In *Layout.cshtml* aggiungere il codice seguente prima del tag ``</head>`` e prima di qualsiasi altro script:

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-and-live-stream"></a>Testare la raccolta di dati di Richieste non riuscite, Tempo di caricamento della visualizzazione pagina e Live Stream

Per verificare e confermare che tutto funzioni correttamente:

1. Eseguire l'app. Per eseguire l'app, selezionare l'icona ![Screenshot dell'icona IIS Express di Visual Studio](./media/asp-net-core/004-iis-express.png) .

2. Passare alla pagina **Informazioni su** per attivare l'eccezione di test. Se è attiva la modalità debug, in Visual Studio selezionare **Continua** perché l'eccezione venga visualizzata in Application Insights.

3. Eseguire di nuovo lo script delle transazioni di PowerShell usato in precedenza. Potrebbe essere necessario modificare il numero di porta nello script.

4. Se la pagina **Panoramica** in Applications Insights non è ancora visualizzata, nel menu di Visual Studio selezionare **Progetto** > **Application Insights** > **Apri portale Application Insights**. 

   > [!TIP]
   > Se il nuovo traffico non è visualizzato, controllare il valore di **Intervallo di tempo** e quindi selezionare **Aggiorna**.

   ![Screenshot della finestra Panoramica](./media/asp-net-core/0019-overview-updated.png)

5. Selezionare **Live Stream**.

   ![Screenshot di Live Metrics Stream](./media/asp-net-core/0020-live-metrics-stream.png)

   Se lo script di PowerShell è ancora in esecuzione, dovrebbero essere visualizzate le metriche attive. Se l'esecuzione dello script di PowerShell è stata arrestata, eseguire di nuovo lo script con Live Stream aperto.

## <a name="application-insights-sdk-comparison"></a>Confronto tra SDK per Application Insights

Il gruppo di prodotti Application Insights è stato appositamente studiato per ottenere la parità delle funzionalità tra la versione completa di [.NET Framework SDK](https://github.com/Microsoft/ApplicationInsights-dotnet) e .NET Core SDK. Con la versione 2.2.0 di [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) per Application Insights, le carenze in termini di funzionalità sono state ampiamente colmate.

La tabella seguente descrive meglio le differenze e i compromessi raggiunti tra [.NET e .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server):

   | Confronto tra SDK | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Metriche attive**      | **+** |**-** | **+** |
   | **Canale di telemetria server** | **+** |**-** | **+**|
   |**Campionamento adattivo**| **+** | **-** | **+**|
   | **Chiamate alle dipendenze SQL**     | **+** |**-** | **+**|
   | **Contatori delle prestazioni*** | **+** | **-**| **-**|

In questo contesto il termine "contatori delle prestazioni" si riferisce a [contatori delle prestazioni lato server](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters), ad esempio relativamente a utilizzo di processore, memoria e dischi.

## <a name="open-source-sdk"></a>SDK open source
[Leggere e contribuire al codice](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="application-insights-search-continued"></a>Ricerca di Application Insights - Continua

Questa sezione descrive in modo più approfondito il funzionamento di Ricerca di Application Insights in Visual Studio per un progetto ASP.NET Core 2. La funzionalità ha lo stesso funzionamento anche quando non sono stati ancora installati in modo esplicito i pacchetti NuGet di Application Insights. Può essere utile esaminare l'output di debug.

Se si esegue una ricerca dell'output con la parola _insight_, verranno evidenziati risultati simili ai seguenti:

```DebugOutput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

Nell'output CoreCLR carica due assembly: 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

Il riferimento _non configurato_ in ogni istanza di Application Insights Telemetry indica che l'applicazione non è associata a una chiave di strumentazione. I dati generati durante l'esecuzione dell'app non vengono inviati ad Azure. I dati sono disponibili solo per la ricerca e l'analisi locali.

Questa funzionalità è possibile in parte perché il pacchetto NuGet _Microsoft.AspNetCore.All_ accetta [_Microsoft.ASPNetCoreApplicationInsights.HostingStartup_](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1) come dipendenza.

![Screenshot del grafico dipendenze NuGet per Microsoft.AspNETCore.all](./media/asp-net-core/013-dependency.png)

Esternamente a Visual Studio, se si stava modificando un progetto ASP.NET Core in Visual Studio code o un altro editor, questi assembly non caricherebbero automaticamente durante il debug se è stato aggiunto Application Insights in modo esplicito al progetto.

Tuttavia, in Visual Studio l'attivazione delle funzionalità di Application Insights locali da assembly esterni avviene tramite l'[interfaccia IHostingStartup](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1). L'interfaccia aggiunge dinamicamente Application Insights durante il debug.

Sono disponibili altre informazioni su come migliorare un'app da un [assembly esterno in ASP.NET Core con IHostingStartup](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1). 

### <a name="disable-application-insights-in-visual-studio-net-core-projects"></a>Disabilitare Application Insights in progetti di Visual Studio .NET Core

Benché l'attivazione automatica della funzionalità di ricerca di Application Insights possa essere utile, la visualizzazione di dati di telemetria generati in momenti imprevisti può essere motivo di confusione.

Se la semplice disabilitazione della generazione dei dati di telemetria è sufficiente, è possibile aggiungere questo blocco di codice al metodo **configure** del file _Startup.cs_:

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

CoreCLR carica comunque _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ e _Microsoft.ApplicationInsights.AspNetCore.dll_, ma i file non eseguono alcuna operazione.

Se si vuole disabilitare completamente Application Insights nel progetto di Visual Studio .NET Core, il metodo consigliato consiste nel selezionare **Strumenti** > **Opzioni** >  **Progetti e soluzioni** > **Progetti Web**. Selezionare la casella di controllo **Disabilita Application Insights locale per progetti Web ASP.NET Core**. Questa funzionalità è stata aggiunta in Visual Studio 15,6.

![Screenshot della schermata del progetti web della finestra di opzioni di Visual Studio](./media/asp-net-core/014-disable.png)

Se si esegue una versione precedente di Visual Studio e si vuole rimuovere completamente tutti gli assembly caricati tramite *IHostingStartup*, sono disponibili due opzioni:

* Aggiungere `.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")` a _Program.cs_:

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

* Aggiungere ``"ASPNETCORE_preventHostingStartup": "True"`` a variabili di ambiente _launchSettings.json_.

Quando si usa uno di questi due metodi, il problema è che non disabilitano solo Application Insights. I metodi disabilitano anche qualsiasi strumento in Visual Studio che usa la funzionalità di attivazione *IHostingStartup*.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Passaggi successivi
* [Esplorare i flussi degli utenti](../../azure-monitor/app/usage-flows.md) per identificare in che modo gli utenti si sposteranno nell'app.
* [Configurare la raccolta di snapshot](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications) per visualizzare lo stato del codice sorgente e delle variabili nel momento in cui viene generata un'eccezione.
* [Utilizzare l'API](../../azure-monitor/app/api-custom-events-metrics.md) per inviare gli eventi e le metriche per una visualizzazione più dettagliata dell'utilizzo e delle prestazioni dell'app.
* Usare [test di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md) per controllare costantemente l'app da ogni parte del mondo.
