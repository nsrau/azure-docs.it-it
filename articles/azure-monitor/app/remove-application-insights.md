---
title: Rimuovere Application Insights in Visual Studio - Monitoraggio di AzureRemove Application Insights in Visual Studio - Azure Monitor
description: Come rimuovere Application Insights SDK per ASP.NET e ASP.NET Core in Visual Studio.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 1c9ff8d3d305645ac7d113421e2c6c5f8451bd2b
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80805105"
---
# <a name="how-to-remove-application-insights-in-visual-studio"></a>Come rimuovere Application Insights in Visual StudioHow to remove Application Insights in Visual Studio

Questo articolo illustra come rimuovere il ASP.NET e ASP.NET Core Application Insights SDK in Visual Studio.

Per rimuovere Application Insights, è necessario rimuovere i pacchetti NuGet e i riferimenti dall'API nell'applicazione. È possibile disinstallare i pacchetti NuGet utilizzando la console di gestione dei pacchetti o gestisci la soluzione NuGet in Visual Studio.You can uninstall NuGet packages by using the Package Management Console or Manage NuGet Solution in Visual Studio. Le sezioni seguenti mostreranno due modi per rimuovere i pacchetti NuGet e ciò che è stato aggiunto automaticamente nel progetto. Assicurarsi di confermare i file aggiunti e le aree con nel proprio codice in cui sono state effettuate chiamate all'API vengono rimosse.

## <a name="uninstall-using-the-package-management-console"></a>Eseguire la disinstallazione tramite la console di gestione dei pacchetti

# <a name="net"></a>[.NET](#tab/net)

1. Per aprire la Console di gestione pacchetti, nel menu in alto selezionare Strumenti > Gestione pacchetti NuGet > Console di gestione pacchetti.
     
    ![Nel menu in alto fare clic su Strumenti > Gestione pacchetti NuGet > Console di gestione pacchetti](./media/remove-application-insights/package-manager.png)

    > [!NOTE]
    > Se la raccolta di tracce è abilitata, è necessario disinstallare prima Microsoft.ApplicationInsights.TraceListener.If trace collection is enabled it need to first uninstall Microsoft.ApplicationInsights.TraceListener. Immettere quindi seguire la procedura seguente per rimuovere Microsoft.ApplicationInsights.Web.Enter `Uninstall-package Microsoft.ApplicationInsights.TraceListener` then follow the step below to remove Microsoft.ApplicationInsights.Web.

1. Immettere il seguente comando: `Uninstall-Package Microsoft.ApplicationInsights.Web -RemoveDependencies`

    Dopo aver immesso il comando, il pacchetto di Application Insights e tutte le relative dipendenze verranno disinstallati dal progetto.
    
    ![Comando Invio nella console](./media/remove-application-insights/package-management-console.png)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. Per aprire la Console di gestione pacchetti, nel menu in alto selezionare Strumenti > Gestione pacchetti NuGet > Console di gestione pacchetti.

    ![Nel menu in alto fare clic su Strumenti > Gestione pacchetti NuGet > Console di gestione pacchetti](./media/remove-application-insights/package-manager.png)

1. Immettere il seguente comando: ` Uninstall-Package Microsoft.ApplicationInsights.AspNetCore -RemoveDependencies`

    Dopo aver immesso il comando, il pacchetto di Application Insights e tutte le relative dipendenze verranno disinstallati dal progetto.

---

## <a name="uninstall-using-the-visual-studio-nugetui"></a>Eseguire la disinstallazione utilizzando l'interfaccia utente NuGet di Visual StudioUninstall using the Visual Studio NuGet UI

# <a name="net"></a>[.NET](#tab/net)

1. In *Esplora* soluzioni a destra fare clic con il pulsante destro del mouse su **Soluzione** e scegliere **Gestisci pacchetti NuGet per soluzione**.

    Verrà quindi visualizzata una schermata che consente di modificare tutti i pacchetti NuGet che fanno parte del progetto.
    
     ![Fare clic con il pulsante destro del mouse su Soluzione, in Esplora soluzioni, quindi selezionare Gestisci pacchetti NuGet per la soluzione](./media/remove-application-insights/manage-nuget-framework.png)

    > [!NOTE]
    > Se la raccolta di tracce è abilitata, è necessario disinstallare prima Microsoft.ApplicationInsights.TraceListener senza rimuovere le dipendenze selezionate, quindi eseguire la procedura seguente per disinstallare Microsoft.ApplicationInsights.Web con la rimozione delle dipendenze selezionata.
    
1. Fare clic sul pacchetto "Microsoft.ApplicationInsights.Web".A destra, seleziona la casella di controllo accanto a *Progetto* per selezionare tutti i progetti.
    
1. Per rimuovere tutte le dipendenze durante la disinstallazione, selezionare il pulsante a discesa **Opzioni** sotto la sezione in cui è stato selezionato il progetto.

    In *Opzioni di disinstallazione*selezionare la casella di controllo accanto a *Rimuovi dipendenze*.

1. Selezionare **Disinstalla**.
    
    ![Selezionare Rimuovi dipendenze, quindi disinstallare](./media/remove-application-insights/uninstall-framework.png)

    Verrà visualizzata una finestra di dialogo che mostra tutte le dipendenze da rimuovere dall'applicazione.Selezionare **ok** per disinstallare.
    
    ![Selezionare Rimuovi dipendenze, quindi disinstallare](./media/remove-application-insights/preview-uninstall-framework.png)
    
1.  Dopo la disinstallazione di tutti gli elementi, è possibile che vengano ancora visualizzati "ApplicationInsights.config" e "AiHandleErrorAttribute.cs" in *Esplora soluzioni*.È possibile eliminare i due file manualmente.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. In *Esplora* soluzioni a destra fare clic con il pulsante destro del mouse su **Soluzione** e scegliere **Gestisci pacchetti NuGet per soluzione**.

    Verrà quindi visualizzata una schermata che consente di modificare tutti i pacchetti NuGet che fanno parte del progetto.

    ![Fare clic con il pulsante destro del mouse su Soluzione, in Esplora soluzioni, quindi selezionare Gestisci pacchetti NuGet per la soluzione](./media/remove-application-insights/manage-nuget-core.png)

1. Fare clic sul pacchetto "Microsoft.ApplicationInsights.AspNetCore". A destra, seleziona la casella di controllo accanto a *Progetto* per selezionare tutti i progetti, quindi seleziona **Disinstalla**.

    ![Selezionare Rimuovi dipendenze, quindi disinstallare](./media/remove-application-insights/uninstall-core.png)

---

## <a name="what-is-created-when-you-add-application-insights"></a>Elementi creati quando si aggiungono Application Insights

Quando si aggiungono Application Insights al progetto, vengono creati file e viene aggiunto codice ad alcuni file. La sola disinstallazione dei pacchetti NuGet non sempre eliminerà i file e il codice. Per rimuovere completamente Application Insights, è necessario controllare ed eliminare manualmente il codice o i file aggiunti insieme alle chiamate API aggiunte nel progetto.

# <a name="net"></a>[.NET](#tab/net)

Quando si aggiunge Application Insights Telemetry a un progetto di Visual Studio ASP.NET, vengono aggiunti i file seguenti:When you add Application Insights Telemetry to a Visual Studio ASP.NET project, it adds the following files:

- ApplicationInsights.config
- AiHandleErrorAttribute.cs

Vengono aggiunte le seguenti parti di codice:

- [Nome del progetto].csproj

    ```C#
     <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4</ApplicationInsightsResourceId>
    ```

- Packages.config (informazioni in tempo in corso)

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

- Layout.cshtml

    Se il progetto contiene un file Layout.cshtml, viene aggiunto il codice seguente.
    
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

Quando si aggiunge la telemetria di Application Insights a un progetto di modello di Visual Studio ASP.NET Core, viene aggiunto il codice seguente:When you add Application Insights Telemetry to a Visual Studio ASP.NET Core template project, it adds the following code:

- [Nome del progetto].csproj

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

- Appsettings.json:

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