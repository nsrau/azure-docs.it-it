<properties title="Monitor your app's health and usage with Application Insights" pageTitle="Monitor your app's health and usage with Application Insights" description="Get started with Application Insights. Analyze usage, availability and performance of your on-premises or Microsoft Azure applications." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

# Monitorare l'integrità e l'utilizzo dell'applicazione

*Application Insights è attualmente disponibile in anteprima.*

Assicurarsi che l'applicazione venga eseguita correttamente e che soddisfi le esigenze degli utenti. Application Insights avviserà di eventuali problemi di prestazioni e aiuterà a trovare e diagnosticare le cause principali. Consentirà di tracciare le attività degli utenti per poter ottimizzare l'app in base alle loro esigenze.

Application Insights può monitorare app per Windows Phone, app di Windows Store e applicazioni Web ospitate in locale o su macchine virtuali, oltre che su siti Web Microsoft Azure.

Saranno necessari [Visual Studio Update 3][Visual Studio Update 3] e un account in [Microsoft Azure][Microsoft Azure] (è previsto un periodo di valutazione gratuito).

1.  [Aggiungere Application Insights][Aggiungere Application Insights]

-   [Eseguire il progetto][Eseguire il progetto]
-   [Visualizzare i dati monitorati][Visualizzare i dati monitorati]
-   [Distribuire l'applicazione][Distribuire l'applicazione]
-   [Passaggi successivi][Passaggi successivi]

*In alternativa, per monitorare un servizio Web esistente senza ridistribuirlo o usare Visual Studio, è possibile [installare un agente nel server][installare un agente nel server].*

## <a name="add"></a>1. Aggiungere Application Insights

### Se è un nuovo progetto...

Quando si crea un nuovo progetto in Visual Studio 2013, assicurarsi che Application Insights sia selezionato.

![Create an ASP.NET project][Create an ASP.NET project]

Se è la prima volta, verrà chiesto di specificare l'account di accesso per l'anteprima Microsoft Azure o di registrarsi. Questo account è diverso da quello di Visual Studio Online.

> Nessuna opzione Application Insights? Assicurarsi di usare Visual Studio 2013 Update 3 e di creare un progetto Web, Windows Store o Windows Phone.

### ... o se è un progetto esistente

Per aggiungere Application Insights a un nuovo progetto, fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere Aggiungi Application Insights.

![Choose Add Application Insights][Choose Add Application Insights]

## <a name="run"></a>2. Eseguire il progetto

Eseguire l'applicazione con F5 e provarla aprendo alcune pagine.

In Visual Studio, si vedrà un conteggio degli eventi ricevuti.

![][0]

## <a name="monitor"></a>3. Visualizzare i dati monitorati

Aprire Application Insights dal progetto.

![Right-click your project and open the Azure portal][Right-click your project and open the Azure portal]

Cercare i dati nei riquadri Integrità applicazione e Dati di analisi utilizzo. Ad esempio:

![Click through to more data][Click through to more data]

Fare clic su un riquadro per visualizzare altri dettagli. [Altre informazioni sui riquadri e i report.][Altre informazioni sui riquadri e i report.]

> [WACOM.NOTE] Molti riquadri mostrano dettagli limitati in questa versione di anteprima.

## <a name="deploy"></a>4. Distribuire l'applicazione

Distribuire l'applicazione e osservare i dati accumulati.

## <a name="next"></a>Passaggi successivi

[Altre informazioni sui riquadri e i report][Altre informazioni sui riquadri e i report.]

[Test Web][Test Web]

[Acquisire ed eseguire ricerche nei log di diagnostica][Acquisire ed eseguire ricerche nei log di diagnostica]

[Risoluzione dei problemi][Risoluzione dei problemi]

## Altre informazioni

-   [Application Insights][Application Insights]
-   [Aggiungere Application Insights al progetto][Aggiungere Application Insights al progetto]
-   [Monitorare un server Web attivo][installare un agente nel server]
-   [Esaminare le metriche in Application Insights][Altre informazioni sui riquadri e i report.]
-   [Ricerche nei log di diagnostica][Acquisire ed eseguire ricerche nei log di diagnostica]
-   [Verifica della disponibilità mediante test Web][Test Web]
-   [Verifica dell'utilizzo con eventi e metriche][Verifica dell'utilizzo con eventi e metriche]
-   [Domande e risposte e risoluzione dei problemi][Risoluzione dei problemi]

<!--Link references-->

  [Visual Studio Update 3]: http://go.microsoft.com/fwlink/?LinkId=397827
  [Microsoft Azure]: http://azure.com
  [Aggiungere Application Insights]: #add
  [Eseguire il progetto]: #run
  [Visualizzare i dati monitorati]: #monitor
  [Distribuire l'applicazione]: #deploy
  [Passaggi successivi]: #next
  [installare un agente nel server]: ../app-insights-monitor-performance-live-website-now/
  [Create an ASP.NET project]: ./media/appinsights/appinsights-01-vsnewp1.png
  [Choose Add Application Insights]: ./media/appinsights/appinsights-03-addExisting.png
  [0]: ./media/appinsights/appinsights-09eventcount.png
  [Right-click your project and open the Azure portal]: ./media/appinsights/appinsights-04-openPortal.png
  [Click through to more data]: ./media/appinsights/appinsights-05-usageTiles.png
  [Altre informazioni sui riquadri e i report.]: ../app-insights-explore-metrics/
  [Test Web]: ../app-insights-monitor-web-app-availability/
  [Acquisire ed eseguire ricerche nei log di diagnostica]: ../app-insights-search-diagnostic-logs/
  [Risoluzione dei problemi]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Aggiungere Application Insights al progetto]: ../app-insights-monitor-application-health-usage/
  [Verifica dell'utilizzo con eventi e metriche]: ../app-insights-track-usage-custom-events-metrics/
