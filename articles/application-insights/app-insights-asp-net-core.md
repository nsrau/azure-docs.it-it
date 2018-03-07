---
title: Azure Application Insights per ASP.NET Core | Microsoft Docs
description: "Monitorare la disponibilità, le prestazioni e l'utilizzo delle applicazioni Web."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: mbullwin
ms.openlocfilehash: e9fb3e68db66449d9ca3b43e6974910cb9477e62
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights per ASP.NET Core

Azure Application Insights offre funzionalità di monitoraggio avanzate dell'applicazione Web fino al livello di codice. È possibile monitorare con facilità la disponibilità, le prestazioni e l'utilizzo dell'applicazione Web. È anche possibile identificare e diagnosticare rapidamente gli errori nell'applicazione senza attendere che vengano segnalati da un utente.

Questo articolo illustra in modo dettagliato i passaggi necessari per creare un'applicazione [Razor Pages](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) di ASP.NET Core di esempio in Visual Studio e per avviare il monitoraggio con Azure Application Insights.

## <a name="prerequisites"></a>Prerequisiti

- NET Core 2.0.0 SDK o versione successiva.
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) versione 15.3 o versioni successive con carico di lavoro Sviluppo ASP.NET e Web.

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Creare un progetto di ASP.NET Core in Visual Studio

1. Fare clic con il pulsante destro del mouse e avviare **Visual Studio 2017** come amministratore.
2. Selezionare **File** > **Nuovo** > **Progetto** (CTRL-MAIUSC-N).

   ![Screenshot del menu File > Nuovo > Progetto di Visual Studio](./media/app-insights-asp-net-core/0001-file-new-project.png)

3. Espandere **Visual C#** e selezionare **.NET Core** > **Applicazione Web ASP.NET Core**. Immettere un nome nelle caselle **Nome** > **Nome soluzione** e selezionare **Crea nuovo repository Git**.

   ![Screenshot della procedura guidata File > Nuovo > Progetto di Visual Studio](./media/app-insights-asp-net-core/0002-new-project-web-application.png)

4. Selezionare **.NET Core** > **Applicazione Web** **ASP.NET Core 2.0** > **OK**.

    ![Screenshot del menu di selezione File > Nuovo > Progetto di Visual Studio](./media/app-insights-asp-net-core/0003-dot-net-core.png)

## <a name="add-application-insights-telemetry"></a>Aggiungere Application Insights Telemetry

1. Selezionare **Progetto** > **Aggiungi Application Insights Telemetry**. In alternativa, è possibile fare clic con il pulsante destro del mouse su **Servizi connessi** > Aggiungi servizio connesso.

    ![Screenshot del menu di selezione File > Nuovo > Progetto di Visual Studio](./media/app-insights-asp-net-core/0004-add-application-insights-telemetry.png)

2. Selezionare **Inizia gratis**.

    ![Screenshot del menu di selezione File > Nuovo > Progetto di Visual Studio](./media/app-insights-asp-net-core/0005-start-free.png)

3. Selezionare un'opzione appropriata in **Sottoscrizione** > **Risorsa**, scegliere se consentire la raccolta di dati oltre 1 GB al mese e fare clic su **Registra**.

    ![Screenshot del menu di selezione File > Nuovo > Progetto di Visual Studio](./media/app-insights-asp-net-core/0006-register.png)

## <a name="changes-made-to-your-project"></a>Modifiche apportate al progetto

Application Insights ha un impatto molto ridotto sulle prestazioni. Per esaminare le modifiche apportate al progetto con l'aggiunta di dati di telemetria di Application Insights:

Selezionare **Visualizza** > **Team Explorer** (CTRL+\, CTRL+M) > **Progetto** > **Modifiche**

- Quattro modifiche in totale:

  ![Screenshot dei file modificati con l'aggiunta di Application Insights](./media/app-insights-asp-net-core/0007-changes.png)

- Viene creato un nuovo file:

   **ConnectedService.json**

  ![Screenshot dei file modificati con l'aggiunta di Application Insights](./media/app-insights-asp-net-core/0008-connectedservice-json.png)

- Vengono modificati tre file:

  **appsettings.json**

   ![Screenshot dei file modificati con l'aggiunta di Application Insights](./media/app-insights-asp-net-core/0009-appsettings-json.png)

  **ContosoDotNetCore.csproj**

   ![Screenshot dei file modificati con l'aggiunta di Application Insights](./media/app-insights-asp-net-core/0010-contoso-netcore-csproj.png)

   **Program.cs**

   ![Screenshot dei file modificati con l'aggiunta di Application Insights](./media/app-insights-asp-net-core/0011-program-cs.png)

## <a name="synthetic-transactions-with-powershell"></a>Transazioni sintetiche con PowerShell

Per generare traffico di test, è possibile avviare l'app e quindi fare clic manualmente su collegamenti sparsi. Tuttavia, è spesso utile creare una semplice transazione sintetica in PowerShell.

1. Eseguire l'app facendo clic su IIS Express. ![Screenshot dell'icona IIS Express di Visual Studio](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Copiare l'URL dalla barra degli indirizzi del browser. È nel formato http://localhost:{numero di porta casuale}.

   ![Screenshot della barra degli indirizzi URL del browser](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Eseguire il ciclo di PowerShell seguente per creare 100 transazioni sintetiche per l'app di test. Modificare il numero di porta dopo **localhost:** in modo che corrisponda all'URL copiato nel passaggio precedente.

   ```PS
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>Aprire il portale Application Insights

Dopo aver eseguito PowerShell nella sezione precedente, avviare Application Insights per visualizzare le transazioni e confermare che i dati vengono raccolti. 

Nel menu di Visual Studio selezionare **Progetto** > **Application Insights** > **Apri portale Application Insights**.

   ![Screenshot della pagina Panoramica di Application Insights](./media/app-insights-asp-net-core/0014-portal-01.png)

> [!NOTE]
> Nello screenshot di esempio precedente **Live Stream**, **Tempo di caricamento della visualizzazione pagina** e **Richieste non riuscite** non vengono attualmente raccolti. La prossima sezione illustrerà in modo dettagliato i passaggi necessari per aggiungere ognuna di queste funzionalità. Se **Live Stream** e **Tempo di caricamento della visualizzazione pagina** vengono già raccolti, seguire la procedura solo per **Richieste non riuscite**.

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>Raccogliere Richieste non riuscite, Live Stream e Tempo di caricamento della visualizzazione pagina

### <a name="failed-requests"></a>Richieste non riuscite

Tecnicamente i dati di **Richieste non riuscite** vengono raccolti, ma non si sono ancora verificati eventi di questo tipo. Per accelerare il processo, è possibile aggiungere un'eccezione personalizzata al progetto esistente per simulare un'eccezione reale. Se l'app è ancora in esecuzione in Visual Studio, prima di procedere fare clic su **Termina debug** (MAIUSC+F5).

1. In **Esplora soluzioni** espandere **Pagine** > **About.cshtml** e aprire **About.cshtml.cs**.

   ![Screenshot di Esplora soluzioni di Visual Studio](./media/app-insights-asp-net-core/0015-solution-explorer-about.png)

2. Aggiungere un'eccezione in ``Message=`` e salvare le modifiche nel file.

   ```C#
   throw new Exception("Test Exception");
   ```

   ![Screenshot del codice di eccezione](./media/app-insights-asp-net-core/000016-exception.png)

### <a name="live-stream"></a>Live Stream

Per accedere alla funzionalità Live Stream di Application Insights con ASP.NET Core eseguire l'aggiornamento ai pacchetti NuGet **Microsoft.ApplicationInsights.AspNetCore 2.2.0**.

In Visual Studio selezionare **Progetto** > **Gestisci pacchetti NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > Versione **2.2.0** > **Aggiorna**.

  ![Screenshot di Gestione pacchetti NuGet](./media/app-insights-asp-net-core/0017-update-nuget.png)

Verranno visualizzate diverse richieste di conferma. Leggere e confermare se si accettano le modifiche.

### <a name="page-view-load-time"></a>Tempo di caricamento della visualizzazione pagina

1. In Visual Studio passare a **Esplora soluzioni** > **Pagine**, dove sarà necessario modificare due file: **_Layout.cshtml** e **_ViewImports.cshtml**.

2. In **_ViewImports.cshtml** aggiungere il codice seguente:

   ```C#
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```
     ![Screenshot della modifica apportata al codice in _ViewImports.cshtml](./media/app-insights-asp-net-core/00018-view-imports.png)

3. In **Layout.cshtml** aggiungere la riga riportata di seguito davanti al tag ``</head>``, ma prima di tutti gli altri script.

    ```C#
    @Html.Raw(snippet.FullScript)
    ```
    ![Screenshot della modifica apportata al codice in layout.cshtml](./media/app-insights-asp-net-core/0018-layout-cshtml.png)

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>Testare Richieste non riuscite, Tempo di caricamento della visualizzazione pagina e Live Stream

Dopo aver completato i passaggi precedenti, è possibile eseguire il test per confermare il corretto funzionamento del codice.

1. Eseguire l'app facendo clic su IIS Express. ![Screenshot dell'icona IIS Express di Visual Studio](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Passare alla pagina **Informazioni su** per attivare l'eccezione di test. Se l'esecuzione avviene in modalità debug, sarà necessario fare clic su **Continua** in Visual Studio per consentire ad Application Insights di prelevare l'eccezione.

3. Eseguire nuovamente lo script di transazione simulato di PowerShell illustrato in precedenza. Potrebbe essere necessario modificare il numero di porta nello script.

4. Se la pagina Panoramica di Application Insights non è ancora aperta, selezionare **Progetto** > **Application Insights** > **Apri portale Application Insights** nel menu di Visual Studio. 

   > [!TIP]
   > Se il nuovo traffico non è ancora visibile, controllare **Intervallo di tempo** e fare clic su **Aggiorna**.

   ![Screenshot della finestra Panoramica](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Selezionare Live Stream.

   ![Screenshot di Live Metrics Stream](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   Se lo script di PowerShell è ancora in esecuzione, dovrebbero essere visualizzate le metriche attive. Se lo script è stato arrestato, eseguirlo nuovamente con Live Stream aperto.

## <a name="app-insights-sdk-comparison"></a>Confronto tra SDK di Application Insights

Il gruppo di prodotti Application Insights è stato appositamente studiato per ridurre al minimo le differenze di funzionalità tra [.NET Framework SDK completo](https://github.com/Microsoft/ApplicationInsights-dotnet) e .NET Core SDK. Con la versione 2.2.0 di [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) per Application Insights il gap di funzionalità è stato ampiamente colmato.

Di seguito è riportata una tabella per comprendere meglio le differenze esistenti e i compromessi raggiunti tra [.NET e .NET Core](https://docs.microsoft.com/en-us/dotnet/standard/choosing-core-framework-server).

   | Confronto tra SDK | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Metriche attive**      | **+** |**-** | **+** |
   | **Canale di telemetria server** | **+** |**-** | **+**|
   |**Campionamento adattivo**| **+** | **-** | **+**|
   | **Chiamate alle dipendenze SQL**     | **+** |**-** | **+**|
   | **Contatori delle prestazioni*** | **+** | **-**| **-**|

In questo contesto il termine _contatori delle prestazioni_ si riferisce ai [contatori delle prestazioni lato server](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-performance-counters), ad esempio il processore, la memoria e l'utilizzo dischi.

## <a name="open-source-sdk"></a>SDK open source
[Leggere e contribuire al codice](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Passaggi successivi
* [Esplorare i flussi degli utenti](app-insights-usage-flows.md) per comprendere la modalità di navigazione dell'app.
* [Utilizzare l'API](app-insights-api-custom-events-metrics.md) per inviare gli eventi e le metriche per una visualizzazione più dettagliata dell'utilizzo e delle prestazioni dell'app.
* [Test di disponibilità](app-insights-monitor-web-app-availability.md) controlla costantemente l’app da tutto il mondo.