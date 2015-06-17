<properties 
	pageTitle="Diagnosi dei problemi di prestazioni in un sito Web in esecuzione" 
	description="Monitorare le prestazioni di un sito Web senza ripetere la distribuzione. Usare la versione autonoma o con Application Insights SDK" 
	services="application-insights" 
documentationCenter=".net"
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/23/2015" 
	ms.author="awills"/>
 

# Monitorare le prestazioni in un sito Web attivo

*Application Insights è disponibile in anteprima.*

Se un'applicazione Web non funziona correttamente, è possibile diagnosticare rapidamente eccezioni e problemi di prestazioni senza ricompilarla o ridistribuirla. È sufficiente installare l'agente di Application Insights nel server per individuare i colli di bottiglia delle prestazioni e ottenere le analisi dello stack per tutte le eccezioni. 


#### Per usare questo metodo per configurare Application Insights

Questo approccio viene usato in genere per diagnosticare rapidamente problemi di prestazioni in un sito Web IIS attivo.

È infatti sufficiente installare un agente nel server per visualizzare i dati relativi alle prestazioni in Application Insights.

- Lo stesso approccio è valido anche per un'app ASP.NET ospitata in un server IIS.

- Per visualizzare i dati, server un [account Microsoft Azure](http://azure.com).

- È necessario l'accesso amministrativo al server in cui è in esecuzione l'app Web. 

- *Non è* necessario il codice dell'app Web, né occorre ricompilare o ridistribuire l'app. 

- Questo metodo consente di instrumentare l'app Web così com'è, senza inserire codice per la traccia o il log, anche se è possibile effettuare in seguito questa operazione.

Per inserire le tracce di registrazione o diagnostiche, non procedere nella lettura di questo articolo, ma [aggiungere Application Insights al progetto][greenbrown] e ridistribuirlo. Per conoscere l'intera gamma di opzioni, leggere [Introduzione ad Application Insights][start].

## Installare l'agente di Application Insights nel server Web

1. Nel server Web accedere con le credenziali di amministratore.

2. Assicurarsi che sia installata la versione 5.0 o successiva di [Installazione guidata piattaforma Web](http://www.microsoft.com/web/downloads/platform.aspx)..
3. Installare l'agente di Application Insights tramite Installazione guidata piattaforma Web.

    ![](./media/app-insights-monitor-performance-live-website-now/appinsights-031-wpi.png)
4. Nell'Installazione guidata accedere a Microsoft Azure.

    ![](./media/app-insights-monitor-performance-live-website-now/appinsights-035-signin.png)
5. Selezionare l'applicazione Web installata o il sito Web da monitorare, quindi configurare il nome con cui visualizzare i risultati nel portale Application Insights. Fare infine clic sul pulsante 'Aggiungi'.

    ![](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

    In genere, è consigliabile scegliere di creare una nuova risorsa.

    È possibile usare una risorsa esistente se, ad esempio, sono già stati configurati [test web][availability] per il sito.  

6. Si noti che ApplicationInsights.config è stato inserito tra i siti Web da monitorare.

    ![](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)

   Sono inoltre state apportate alcune modifiche al file web.config.

### Configurare o riconfigurare in un secondo momento

Dopo aver completato la procedura guidata, è possibile riconfigurare l'agente in qualsiasi momento. È inoltre possibile usare questa stessa procedura se l'agente è stato installato ma la configurazione iniziale presenta alcuni problemi.

![Click the Application Insights icon on the task bar](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)

## Visualizzare i dati

Aprire l'account in Azure, passare a Application Insights e aprire la risorsa creata.

![](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

I dati sono visualizzati in Integrità applicazione.

![](./media/app-insights-monitor-performance-live-website-now/appinsights-037-results.png)

#### Dati non visualizzati

  * Usare il sito per generare alcuni dati.
  * Attendere qualche minuto l'arrivo dei dati.
  * Assicurarsi che il firewall del server consenta il traffico in uscita sulla porta 443 a dc.services.visualstudio.com 

#### Come usare i report di Integrità applicazione?
 * [Informazioni sui dati e sulla configurazione dei riquadri][perf]

## <a name="next"></a>Passaggi successivi


[Visualizzare i dati][perf]

[Ricerca nei registri di diagnostica][diagnostic]

[Test Web][availability]

[Configurare il monitoraggio dell'utilizzo][start]

[Risoluzione dei problemi][qna]




[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]




<!--HONumber=46--> 

<!--HONumber=46--> 
 