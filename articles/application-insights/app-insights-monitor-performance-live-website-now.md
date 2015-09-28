<properties
	pageTitle="Diagnosi dei problemi di prestazioni in un sito Web in esecuzione | Microsoft Azure"
	description="Monitorare le prestazioni di un sito Web senza ripetere la distribuzione. Usare la versione autonoma o con Application Insights SDK per ottenere la telemetria di dipendenza."
	services="application-insights"
    documentationCenter=".net"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/10/2015"
	ms.author="awills"/>


# Installare Application Insights Status Monitor per monitorare le prestazioni del sito Web

*Application Insights è disponibile in anteprima.*

Application Insights Status Monitor di Visual Studio consente di diagnosticare le eccezioni e i problemi di prestazioni nelle applicazioni Web eseguite in qualsiasi server IIS. Basta installarlo sul proprio server Web IIS per instrumentare le app Web ASP.NET rilevate, inviando i dati al portale di Application Insights per poi poter condurre ricerche e analisi. È possibile installarlo in qualsiasi server fisico o virtuale a cui si ha accesso come amministratore.

![grafici di esempio](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

È possibile scegliere di applicare Application Insights alle applicazioni Web IIS in tre modi:

* **Tempo di compilazione:** [Aggiungere Application Insights SDK][greenbrown] al codice dell'app Web: Il risultato è il seguente:
 * Una gamma di diagnostica standard e telemetria relativa all'utilizzo.
 * È anche possibile usare l'[API di Application Insights][api] se si vogliono scrivere i propri dati di telemetria per tenere traccia dell'utilizzo o diagnosticare i problemi.
* **Tempo di esecuzione**: usare Status Monitor per instrumentare l'app Web sul server.
 * Monitorare le app Web che sono già in esecuzione, senza doverle ricompilare o ripubblicare.
 * Una gamma di diagnostica standard e telemetria relativa all'utilizzo.
 * Diagnostica delle dipendenze&#151;trovare gli errori o le prestazioni scarse in cui l'app usa altri componenti come database, API REST o altri servizi.
 * Risolvere eventuali problemi di telemetria.
* **Entrambi:** compilare l'SDK nel codice dell'app Web ed eseguire Status Monito nel server Web. Offre le migliori funzionalità:
 * Diagnostica standard e telemetria relativa all'utilizzo.
 * Diagnostica delle dipendenze.
 * Scrittura di dati di telemetria personalizzati usando l'API.
 * Risolvere eventuali problemi dell'SDK e di telemetria.



> [AZURE.TIP]Nel caso si tratti di un'[app Web del servizio app di Azure](../app-service-web/websites-learning-map.md), [aggiungere Application Insights SDK][greenbrown] quindi [aggiungere l'estensione di Application Insights](../insights-perf-analytics.md) dal pannello di controllo dell'app in Microsoft Azure.


## Installare Application Insights Status Monitor sul proprio server Web IIS

1. È necessaria una sottoscrizione di [Microsoft Azure](http://azure.com).

1. Nel server Web IIS accedere con le credenziali di amministratore.
2. Scaricare e installare il [programma di installazione di Status Monitor](http://go.microsoft.com/fwlink/?LinkId=506648).

4. Nell'Installazione guidata accedere a Microsoft Azure.

    ![Accedere ad Azure con le credenziali dell'account Microsoft](./media/app-insights-monitor-performance-live-website-now/appinsights-035-signin.png)

    *Errori di connessione? Vedere [Risoluzione dei problemi](#troubleshooting).*

5. Selezionare l'applicazione Web installata o il sito Web da monitorare, quindi configurare la risorsa in cui visualizzare i risultati nel portale Application Insights.

    ![Scegliere un'applicazione e una risorsa.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

    In genere, si sceglie di configurare una nuova risorsa e un nuovo [gruppo di risorse][roles].

    In alternativa, è possibile usare una risorsa esistente se sono già stati configurati [test Web][availability] per il sito o il [monitoraggio del client Web][client].

6. Riavviare IIS.

    ![Scegliere Riavvia nella parte superiore della finestra di dialogo.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    Il servizio Web verrà interrotto per un breve periodo di tempo.

6. Si noti che ApplicationInsights.config è stato inserito tra le app Web da monitorare.

    ![Trovare il file con estensione config insieme ai file di codice dell'app Web.](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)

   Sono inoltre state apportate alcune modifiche al file web.config.

### Configurare o riconfigurare in un secondo momento

Dopo aver completato la procedura guidata, è possibile riconfigurare l'agente in qualsiasi momento. È inoltre possibile usare questa stessa procedura se l'agente è stato installato ma la configurazione iniziale presenta alcuni problemi.

![Fare clic sull'icona di Application Insights sulla barra delle applicazioni](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)

## Visualizzare i dati di telemetria relativi alle prestazioni

Accedere al [portale di anteprima di Azure](http://portal.azure.com), passare ad Application Insights e aprire la risorsa creata.

![Scegliere Sfoglia, Application Insights, quindi selezionare l'app](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

Aprire il pannello Prestazioni per vedere la dipendenza e altri dati.

![Prestazioni](./media/app-insights-monitor-performance-live-website-now/21-perf.png)

Fare clic su qualsiasi grafico per visualizzare altri dettagli.


![](./media/app-insights-monitor-performance-live-website-now/appinsights-038-dependencies.png)

#### Dipendenze

I grafici denominati HTTP, SQL e AZUREBLOB mostrano i tempi di risposta e il numero di chiamate alle dipendenze, cioè i servizi esterni usati dall'applicazione.



#### Contatori delle prestazioni

Fare clic su qualsiasi contatore delle prestazioni per modificare la visualizzazione. In alternativa, è possibile aggiungere un nuovo grafico.

#### Eccezioni

![Fare clic nel grafico di eccezioni del server](./media/app-insights-monitor-performance-live-website-now/appinsights-039-1exceptions.png)

È possibile eseguire il drill down di eccezioni specifiche (degli ultimi sette giorni) e ottenere le analisi dello stack e i dati di contesto.


## Risoluzione dei problemi

### Errori di connessione

È necessario aprire alcune porte in uscita nei firewall del server per consentire il funzionamento di Status Monitor:

+ Telemetria - queste informazioni sono sempre necessarie:
 +	`dc.services.visualstudio.com:80`
 +	`f5.services.visualstudio.com:80`
 +	`dc.services.visualstudio.com:443`
 +	`f5.services.visualstudio.com:443`
 +	`dc.services.vsallin.net:443`
+ Configurazione - necessaria solo quando si apportano modifiche:
 -	`management.core.windows.net:443`
 -	`management.azure.com:443`
 -	`login.windows.net:443`
 -	`login.microsoftonline.com:443`
 -	`secure.aadcdn.microsoftonline-p.com:443`
 -	`auth.gfx.ms:443`
 -	`login.live.com:443`
+ Installazione:
 +	`packages.nuget.org:443`
 +	`appinsightsstatusmonitor.blob.core.windows.net:80`

Questo elenco può variare nel tempo.

### Nessun dato di telemetria?

  * Usare il sito per generare alcuni dati.
  * Attendere qualche minuto l'arrivo dei dati, quindi fare clic su **Aggiorna**.
  * Aprire il riquadro Ricerca diagnostica (il riquadro Ricerca) per visualizzare i singoli eventi. Gli eventi sono spesso visibili in Ricerca diagnostica prima che vengano visualizzati i dati aggregati nei grafici.
  * Aprire Status Monitor e selezionare la propria applicazione nel pannello a sinistra. Verificare se sono presenti messaggi di diagnostica per l'applicazione nella sezione "Configuration notifications":

  ![](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)

  * Assicurarsi che il firewall del server consenta il traffico in uscita sulle porte sopra elencate.
  * Se sul server viene visualizzato un messaggio relativo alle autorizzazioni insufficienti, provare a seguire questa procedura:
    * In Gestione IIS selezionare il pool di applicazioni, aprire **Impostazioni avanzate** e prendere nota dell'identità in **Modello di processo**.
    * Nel pannello di controllo Gestione computer, aggiungere questa identità al gruppo Utenti di Performance Monitor.
  * Vedere [Risoluzione dei problemi][qna].

## Requisiti di sistema

Supporto del sistema operativo per Application Insights Status Monitor sul server

- Windows Server 2008
- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

con la Service Pack più recente e .NET Framework 4.0 e 4.5

Sul lato client Windows 7, 8 e 8.1, con .NET Framework 4.0 e 4.5

Il supporto IIS è: IIS 7, 7.5, 8, 8.5 (IIS è obbligatorio)

## <a name="next"></a>Passaggi successivi

* [Creare test Web][availability] per assicurarsi che il sito rimanga attivo.
* [Cercare eventi e log][diagnostic] per facilitare la diagnosi dei problemi.
* [Aggiungere dati di telemetria del client Web][usage] per visualizzare le eccezioni dal codice della pagina Web e consentire di inserire le chiamate di traccia.
* [Aggiungere Application Insights SDK al codice del servizio Web][greenbrown] per poter inserire chiamate di traccia e log nel codice del server.

## Video

#### Monitoraggio delle prestazioni

[AZURE.VIDEO app-insights-performance-monitoring]

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-web-track-usage.md

<!---HONumber=Sept15_HO3-->