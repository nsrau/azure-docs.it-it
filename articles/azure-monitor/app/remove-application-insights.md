---
title: Rimuovere Application Insights in Visual Studio-monitoraggio di Azure
description: Come rimuovere Application Insights SDK per ASP.NET e ASP.NET Core in Visual Studio.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 1c9ff8d3d305645ac7d113421e2c6c5f8451bd2b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80805105"
---
# <a name="how-to-remove-application-insights-in-visual-studio"></a>Come rimuovere Application Insights in Visual Studio

In questo articolo viene illustrato come rimuovere ASP.NET e ASP.NET Core Application Insights SDK in Visual Studio.

Per rimuovere Application Insights, è necessario rimuovere i pacchetti NuGet e i riferimenti dall'API nell'applicazione. È possibile disinstallare i pacchetti NuGet usando la console di Gestione pacchetti o gestire la soluzione NuGet in Visual Studio. Nelle sezioni seguenti vengono illustrati due modi per rimuovere i pacchetti NuGet e gli elementi aggiunti automaticamente al progetto. Assicurarsi di confermare che i file aggiunti e le aree con nel proprio codice in cui sono state effettuate chiamate all'API vengano rimossi.

## <a name="uninstall-using-the-package-management-console"></a>Disinstallare utilizzando la console di Gestione pacchetti

# <a name="net"></a>[.NET](#tab/net)

1. Per aprire la console di Gestione pacchetti, nel menu in alto selezionare strumenti > gestione pacchetti NuGet > console di gestione pacchetti.
     
    ![Nel menu in alto fare clic su strumenti > gestione pacchetti NuGet > console di gestione pacchetti](./media/remove-application-insights/package-manager.png)

    > [!NOTE]
    > Se la raccolta di tracce è abilitata, è necessario prima disinstallare Microsoft. ApplicationInsights. TraceListener. Immettere `Uninstall-package Microsoft.ApplicationInsights.TraceListener` quindi seguire questa procedura per rimuovere Microsoft. ApplicationInsights. Web.

1. Immettere il seguente comando: `Uninstall-Package Microsoft.ApplicationInsights.Web -RemoveDependencies`

    Dopo l'immissione del comando, il pacchetto di Application Insights e tutte le relative dipendenze verranno disinstallate dal progetto.
    
    ![Immettere il comando nella console](./media/remove-application-insights/package-management-console.png)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. Per aprire la console di Gestione pacchetti, nel menu in alto selezionare strumenti > gestione pacchetti NuGet > console di gestione pacchetti.

    ![Nel menu in alto fare clic su strumenti > gestione pacchetti NuGet > console di gestione pacchetti](./media/remove-application-insights/package-manager.png)

1. Immettere il seguente comando: ` Uninstall-Package Microsoft.ApplicationInsights.AspNetCore -RemoveDependencies`

    Dopo l'immissione del comando, il pacchetto di Application Insights e tutte le relative dipendenze verranno disinstallate dal progetto.

---

## <a name="uninstall-using-the-visual-studio-nugetui"></a>Disinstallare usando l'interfaccia utente di Visual Studio NuGet

# <a name="net"></a>[.NET](#tab/net)

1. Nella *Esplora soluzioni*a   destra, fare clic con il pulsante destro del mouse su **soluzione**   e scegliere **Gestisci pacchetti NuGet per la soluzione**.

    Verrà visualizzata una schermata che consente di modificare tutti i pacchetti NuGet che fanno parte del progetto.
    
     ![Fare clic con il pulsante destro del mouse su soluzione, nel Esplora soluzioni, quindi selezionare Gestisci pacchetti NuGet per la soluzione](./media/remove-application-insights/manage-nuget-framework.png)

    > [!NOTE]
    > Se la raccolta di tracce è abilitata, è necessario prima disinstallare Microsoft. ApplicationInsights. TraceListener senza rimuovere le dipendenze selezionate, quindi attenersi alla procedura seguente per disinstallare Microsoft. ApplicationInsights. Web con Rimuovi dipendenze selezionate.
    
1. Fare clic sul pacchetto "Microsoft. ApplicationInsights. Web".Sulla destra selezionare la casella di controllo accanto a *progetto*   per selezionare tutti i progetti.
    
1. Per rimuovere tutte le dipendenze durante la disinstallazione, selezionare il pulsante a discesa **Opzioni**   sotto la sezione in cui è stato selezionato progetto.

    In *Opzioni di disinstallazione*selezionare la casella di controllo accanto a *Rimuovi dipendenze*.

1. Selezionare **Disinstalla**.
    
    ![Selezionare Rimuovi dipendenze, quindi Disinstalla](./media/remove-application-insights/uninstall-framework.png)

    Verrà visualizzata una finestra di dialogo che Mostra tutte le dipendenze da rimuovere dall'applicazione.Selezionare **OK**   per disinstallare.
    
    ![Selezionare Rimuovi dipendenze, quindi Disinstalla](./media/remove-application-insights/preview-uninstall-framework.png)
    
1.  Dopo la disinstallazione di tutti gli elementi, è possibile che nella *Esplora soluzioni*venga visualizzato anche "ApplicationInsights.config" e "AiHandleErrorAttribute.cs".È possibile eliminare manualmente i due file.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. Nella *Esplora soluzioni*a   destra, fare clic con il pulsante destro del mouse su **soluzione**   e scegliere **Gestisci pacchetti NuGet per la soluzione**.

    Verrà visualizzata una schermata che consente di modificare tutti i pacchetti NuGet che fanno parte del progetto.

    ![Fare clic con il pulsante destro del mouse su soluzione, nel Esplora soluzioni, quindi selezionare Gestisci pacchetti NuGet per la soluzione](./media/remove-application-insights/manage-nuget-core.png)

1. Fare clic sul pacchetto "Microsoft. ApplicationInsights. AspNetCore". Sulla destra selezionare la casella di controllo accanto a *progetto* per selezionare tutti i progetti, quindi selezionare **Disinstalla**.

    ![Selezionare Rimuovi dipendenze, quindi Disinstalla](./media/remove-application-insights/uninstall-core.png)

---

## <a name="what-is-created-when-you-add-application-insights"></a>Cosa viene creato quando si aggiunge Application Insights

Quando si aggiungono Application Insights al progetto, vengono creati file e viene aggiunto codice ad alcuni file. La disinstallazione esclusivamente dei pacchetti NuGet non eliminerà sempre i file e il codice. Per rimuovere completamente Application Insights, è consigliabile controllare ed eliminare manualmente il codice o i file aggiunti insieme a qualsiasi chiamata API aggiunta al progetto.

# <a name="net"></a>[.NET](#tab/net)

Quando si aggiunge Application Insights Telemetry a un progetto ASP.NET di Visual Studio, vengono aggiunti i file seguenti:

- ApplicationInsights.config
- AiHandleErrorAttribute.cs

Sono state aggiunte le parti di codice seguenti:

- [Nome del progetto]. csproj

    ```C#
     <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4</ApplicationInsightsResourceId>
    ```

- Packages.config

    ```xml
    <packages>
    ...
    
      <package id="Microsoft.ApplicationInsights" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Agent.Intercept" version="2.4.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.DependencyCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.PerfCounterCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Web" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" version="2.12.0" targetFramework="net472" />
    
      <package id="Microsoft.AspNet.TelemetryCorrelation" version="1.0.7" targetFramework="net472" />
    
      <package id="System.Buffers" version="4.4.0" targetFramework="net472" />
      <package id="System.Diagnostics.DiagnosticSource" version="4.6.0" targetFramework="net472" />
      <package id="System.Memory" version="4.5.3" targetFramework="net472" />
      <package id="System.Numerics.Vectors" version="4.4.0" targetFramework="net472" />
      <package id="System.Runtime.CompilerServices.Unsafe" version="4.5.2" targetFramework="net472" />
    ...
    </packages>
    ```

- Layout. cshtml

    Se il progetto contiene un file layout. cshtml, viene aggiunto il codice seguente.
    
    ```html
    <head>
    ...
        <script type = 'text/javascript' >
            var appInsights=window.appInsights||function(config)
            {
                function r(config){ t[config] = function(){ var i = arguments; t.queue.push(function(){ t[config].apply(t, i)})} }
                var t = { config:config},u=document,e=window,o='script',s=u.createElement(o),i,f;for(s.src=config.url||'//az416426.vo.msecnd.net/scripts/a/ai.0.js',u.getElementsByTagName(o)[0].parentNode.appendChild(s),t.cookie=u.cookie,t.queue=[],i=['Event','Exception','Metric','PageView','Trace','Ajax'];i.length;)r('track'+i.pop());return r('setAuthenticatedUserContext'),r('clearAuthenticatedUserContext'),config.disableExceptionTracking||(i='onerror',r('_'+i),f=e[i],e[i]=function(config, r, u, e, o) { var s = f && f(config, r, u, e, o); return s !== !0 && t['_' + i](config, r, u, e, o),s}),t
            }({
                instrumentationKey:'00000000-0000-0000-0000-000000000000'
            });
            
            window.appInsights=appInsights;
            appInsights.trackPageView();
        </script>
    ...
    </head>
    ```

- ConnectedService.json

    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=613413"
      }
    }
    ```

- FilterConfig.cs

    ```csharp
            public static void RegisterGlobalFilters(GlobalFilterCollection filters)
            {
                filters.Add(new ErrorHandler.AiHandleErrorAttribute());// This line was added
            }
    ```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Quando si aggiunge Application Insights Telemetry a un progetto di modello di ASP.NET Core di Visual Studio, viene aggiunto il codice seguente:

- [Nome del progetto]. csproj

    ```csharp
      <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4core</ApplicationInsightsResourceId>
      </PropertyGroup>
    
      <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.12.0" />
      </ItemGroup>
    
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    ```

- Appsettings.js:

    ```json
    "ApplicationInsights": {
        "InstrumentationKey": "00000000-0000-0000-0000-000000000000"
    ```

- ConnectedService.json
    
    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
      }
    }
    ```
- Startup.cs

    ```csharp
       public void ConfigureServices(IServiceCollection services)
            {
                services.AddRazorPages();
                services.AddApplicationInsightsTelemetry(); // This is added
            }
    ```

---

## <a name="next-steps"></a>Passaggi successivi

- [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview)