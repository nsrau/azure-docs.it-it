<properties title="Diagnose performance issues on a running website" pageTitle="Diagnose performance issues on a running website" description="Monitor a website's performance without re-deploying it." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

<!-- Required end of Sprint 69 - AUX48 -->

# Monitorare le prestazioni in un sito Web attivo

*Application Insights è attualmente disponibile in anteprima.*

Se un'applicazione Web non funziona correttamente, è possibile diagnosticare rapidamente eccezioni e problemi di prestazioni senza ricompilarla o ridistribuirla. È sufficiente installare l'agente di Application Insights nel server per individuare i colli di bottiglia delle prestazioni e ottenere le analisi dello stack per tutte le eccezioni.

#### Per usare questo metodo per configurare Application Insights

Questo approccio viene usato in genere per diagnosticare rapidamente problemi di prestazioni in un sito Web IIS attivo.

È infatti sufficiente installare un agente nel server per visualizzare i dati relativi alle prestazioni in Application Insights.

-   Lo stesso approccio è valido anche per un'app ASP.NET ospitata in un server IIS.

-   Per visualizzare i dati, serve un [account Microsoft Azure][account Microsoft Azure].

-   È necessario l'accesso amministrativo al server in cui è in esecuzione l'app Web.

-   *Non* è necessario il codice dell'app Web, né si deve ricompilare o ridistribuire l'app.

-   Questo metodo consente di instrumentare l'app Web così com'è, senza inserire codice per la traccia o il log, anche se è possibile effettuare in seguito questa operazione.

Se si vogliono inserire le tracce di registrazione o diagnostiche, non procedere nella lettura di questo articolo, ma [aggiungere Application Insights al progetto][existing] e ridistribuirlo.

## Installare l'agente di Application Insights nel server Web

1.  Nel server Web accedere con le credenziali di amministratore.

2.  Assicurarsi che sia installata la versione 5.0 o successiva di [Installazione guidata piattaforma Web][Installazione guidata piattaforma Web].
3.  Installare l'agente di Application Insights tramite Installazione guidata piattaforma Web.

    ![][0]

4.  Nell'Installazione guidata accedere a Microsoft Azure.

    ![][1]

5.  Selezionare l'applicazione Web installata o il sito Web da monitorare, quindi configurare il nome con cui visualizzare i risultati nel portale Application Insights. Fare infine clic sul pulsante 'Aggiungi'.

    ![][2]

    In genere, è consigliabile scegliere di creare una nuova risorsa.

    È possibile usare una risorsa esistente se, ad esempio, sono già stati configurati [test Web][test Web] per il sito.

6.  Si noti che ApplicationInsights.config è stato inserito tra i siti Web da monitorare.

    ![][3]

Sono inoltre state apportate alcune modifiche al file web.config.

### Configurare o riconfigurare in un secondo momento

Dopo aver completato la procedura guidata, è possibile riconfigurare l'agente in qualsiasi momento. È inoltre possibile usare questa stessa procedura se l'agente è stato installato ma la configurazione iniziale presenta alcuni problemi.

![Click the Application Insights icon on the task bar][Click the Application Insights icon on the task bar]

## Visualizzare i dati

Aprire l'account in Azure, passare a Application Insights e aprire la risorsa creata.

![][4]

I dati sono visualizzati in Integrità applicazione.

![][5]

#### Dati non visualizzati

-   Usare il sito per generare alcuni dati.
-   Attendere qualche minuto l'arrivo dei dati.
-   Assicurarsi che il firewall del server consenta il traffico in uscita sulla porta 443 a dc.services.visualstudio.com

## <a name="next"></a>Passaggi successivi

[Visualizzare i dati][Visualizzare i dati]

[Eseguire ricerche nei log di diagnostica][Eseguire ricerche nei log di diagnostica]

[Test Web][test Web]

[Configurare il monitoraggio dell'utilizzo][Configurare il monitoraggio dell'utilizzo]

[Risoluzione dei problemi][Risoluzione dei problemi]

## Altre informazioni

-   [Application Insights][Application Insights]
-   [Aggiungere Application Insights al progetto][Configurare il monitoraggio dell'utilizzo]
-   [Monitorare un server Web attivo][Monitorare un server Web attivo]
-   [Esaminare le metriche in Application Insights][Visualizzare i dati]
-   [Ricerche nei log di diagnostica][Eseguire ricerche nei log di diagnostica]
-   [Verifica della disponibilità mediante test Web][test Web]
-   [Verifica dell'utilizzo con eventi e metriche][Verifica dell'utilizzo con eventi e metriche]
-   [Domande e risposte e risoluzione dei problemi][Risoluzione dei problemi]

<!--Link references-->

  [account Microsoft Azure]: http://azure.com
  [Installazione guidata piattaforma Web]: http://www.microsoft.com/web/downloads/platform.aspx
  [0]: ./media/appinsights/appinsights-031-wpi.png
  [1]: ./media/appinsights/appinsights-035-signin.png
  [2]: ./media/appinsights/appinsights-036-configAIC.png
  [test Web]: ../app-insights-monitor-web-app-availability/
  [3]: ./media/appinsights/appinsights-034-aiconfig.png
  [Click the Application Insights icon on the task bar]: ./media/appinsights/appinsights-033-aicRunning.png
  [4]: ./media/appinsights/appinsights-08openApp.png
  [5]: ./media/appinsights/appinsights-037-results.png
  [Visualizzare i dati]: ../app-insights-explore-metrics/
  [Eseguire ricerche nei log di diagnostica]: ../app-insights-search-diagnostic-logs/
  [Configurare il monitoraggio dell'utilizzo]: ../app-insights-monitor-application-health-usage/
  [Risoluzione dei problemi]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Monitorare un server Web attivo]: ../app-insights-monitor-performance-live-website-now/
  [Verifica dell'utilizzo con eventi e metriche]: ../app-insights-track-usage-custom-events-metrics/
