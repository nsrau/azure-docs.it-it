<properties
	pageTitle="Diagnosi dei problemi di prestazioni in un sito IIS in esecuzione | Microsoft Azure"
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
	ms.date="08/24/2016"
	ms.author="awills"/>


# Instrumentare app Web in fase di esecuzione con Application Insights

*Application Insights è disponibile in anteprima.*

È possibile instrumentare un'app Web live con Visual Studio Application Insights senza dover modificare o ridistribuire il codice. In caso di app ospitate da un server IIS locale, si installa Status Monitor. In caso di app Web di Azure o in esecuzione in una VM di Azure, invece, è possibile installare l'estensione Application Insights. Sono disponibili anche articoli separati sulla strumentazione di [app Web J2EE live](app-insights-java-live.md) e [Servizi cloud di Azure](app-insights-cloudservices.md).

![grafici di esempio](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

È possibile scegliere di applicare Application Insights alle applicazioni Web .NET in tre modi.

* **Fase di compilazione:** [aggiungere Application Insights SDK][greenbrown] al codice dell'app Web.
* **Fase di esecuzione:** instrumentare l'app Web sul server, come descritto di seguito, senza ricompilare e ridistribuire il codice.
* **Entrambe le opzioni:** compilare l'SDK nel codice dell'app Web e applicare anche le estensioni della fase di esecuzione, sfruttando il meglio delle due opzioni.

Ecco un riepilogo di ciò che offrono i singoli modi:

||Fase di compilazione|Fase di esecuzione|
|---|---|---|
|Richieste ed eccezioni|Sì|Sì|
|[Eccezioni più dettagliate](app-insights-asp-net-exceptions.md)||Sì|
|[Diagnostica delle dipendenze](app-insights-asp-net-dependencies.md)|In .NET 4.6 e versioni successive|Sì|
|[Contatori delle prestazioni di sistema](app-insights-web-monitor-performance.md#system-performance-counters)||IIS o servizio cloud di Azure, non app Web di Azure|
|[API per telemetria personalizzata][api]|Sì||
|[Integrazione log di traccia](app-insights-asp-net-trace-logs.md)|Sì||
|[Visualizzazione pagina e dati utente](app-insights-javascript.md)|Sì||
|Ricompilazione del codice non necessaria|No||




## Strumentazione dell'app Web in fase di esecuzione

È necessaria una sottoscrizione di [Microsoft Azure](http://azure.com).

### Se l'app è ospitata nel server IIS

1. Nel server Web IIS accedere con le credenziali di amministratore.
2. Scaricare e installare il [programma di installazione di Status Monitor](http://go.microsoft.com/fwlink/?LinkId=506648).
4. Nell'Installazione guidata accedere a Microsoft Azure.

    ![Accedere ad Azure con le credenziali dell'account Microsoft](./media/app-insights-monitor-performance-live-website-now/appinsights-035-signin.png)

    *Errori di connessione Vedere [Risoluzione dei problemi](#troubleshooting).*

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

#### Configurare o riconfigurare in un secondo momento

Dopo aver completato la procedura guidata, è possibile riconfigurare l'agente in qualsiasi momento. È inoltre possibile usare questa stessa procedura se l'agente è stato installato ma la configurazione iniziale presenta alcuni problemi.

![Fare clic sull'icona di Application Insights sulla barra delle applicazioni](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)


### Se l'applicazione viene eseguita come un'app Web di Azure

1. Nel [portale di Azure](https://portal.azure.com) creare una risorsa di Application Insights di tipo ASP.NET, in cui verranno archiviati, analizzati e visualizzati i dati di telemetria dell'applicazione.

    ![Aggiungere una risorsa di Application Insights. Selezionare il tipo ASP.NET.](./media/app-insights-monitor-performance-live-website-now/01-new.png)
     
2. Aprire quindi il pannello di controllo dell'app Web di Azure, aprire **Strumenti > Monitoraggio delle prestazioni** e aggiungere l'estensione Application Insights.

    ![Nell'app Web scegliere Strumenti, Estensioni, Aggiungi e quindi Application Insights](./media/app-insights-monitor-performance-live-website-now/05-extend.png)

    Selezionare la risorsa di Application Insights appena creata.


### Se è un progetto di Servizi cloud di Azure

[Aggiungere gli script ai ruoli Web e di lavoro](app-insights-cloudservices.md)


## Visualizzare i dati di telemetria relativi alle prestazioni

Accedere al [portale di Azure](https://portal.azure.com), passare ad Application Insights e aprire la risorsa creata.

![Scegliere Sfoglia, Application Insights, quindi selezionare l'app](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

Aprire il pannello delle prestazioni per visualizzare una richiesta, il tempo di risposta, le dipendenze e altri dati.

![Prestazioni](./media/app-insights-monitor-performance-live-website-now/21-perf.png)

Fare clic su qualsiasi grafico per aprire una visualizzazione più dettagliata.

È possibile [modificare, ridisporre, salvare](app-insights-metrics-explorer.md) e aggiungere a un [dashboard](app-insights-dashboards.md) i grafici o l'intero pannello.

## Dipendenze

Il grafico di durata delle dipendenze mostra il tempo impiegato dalle chiamate dall'app a componenti esterni quali database, API REST o archiviazione BLOB di Azure.

Per segmentare il grafico in base alle chiamate a diverse dipendenze, modificare il grafico, attivare il raggruppamento e quindi raggruppare per Dipendenza, Tipo di dipendenza o Prestazioni dipendenze.

![Dipendenza](./media/app-insights-monitor-performance-live-website-now/23-dep.png)

## Contatori delle prestazioni 

(Non per le app web di Azure.) Fare clic su Server nel pannello della panoramica per visualizzare i grafici dei contatori delle prestazioni del server, come l'utilizzo di memoria e di occupazione della CPU.

Se sono presenti diverse istanze del server, può essere opportuno modificare i grafici eseguendo il raggruppamento per Istanza del ruolo.

![Server](./media/app-insights-monitor-performance-live-website-now/22-servers.png)

È inoltre possibile [modificare il set di contatori delle prestazioni segnalati da SDK](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3).


## Eccezioni

![Fare clic nel grafico di eccezioni del server](./media/app-insights-monitor-performance-live-website-now/appinsights-039-1exceptions.png)

È possibile eseguire il drill down di eccezioni specifiche (degli ultimi sette giorni) e ottenere le analisi dello stack e i dati di contesto.

## Campionamento

Se l'applicazione invia una grande quantità di dati ed è in uso Application Insights SDK per ASP.NET 2.0.0 Beta3 o versioni successive, la funzionalità del campionamento adattivo può operare e inviare solo una percentuale dei dati di telemetria. [Altre informazioni sul campionamento.](app-insights-sampling.md)


## Risoluzione dei problemi

### Errori di connessione

Per consentire il funzionamento di Status Monitor è necessario aprire [alcune porte in uscita](app-insights-ip-addresses.md#outgoing-ports) nel firewall del server.

### Nessun dato di telemetria?

  * Usare il sito per generare alcuni dati.
  * Attendere qualche minuto l'arrivo dei dati, quindi fare clic su **Aggiorna**.
  * Aprire il riquadro Ricerca diagnostica (il riquadro Ricerca) per visualizzare i singoli eventi. Gli eventi sono spesso visibili in Ricerca diagnostica prima che vengano visualizzati i dati aggregati nei grafici.
  * Aprire Status Monitor e selezionare la propria applicazione nel pannello a sinistra. Verificare se sono presenti messaggi di diagnostica per l'applicazione nella sezione "Configuration notifications":

  ![Aprire il pannello delle prestazioni per visualizzare una richiesta, il tempo di risposta, le dipendenze e altri dati](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)

  * Assicurarsi che il firewall del server consenta il traffico in uscita sulle porte sopra elencate.
  * Se sul server viene visualizzato un messaggio relativo alle autorizzazioni insufficienti, provare a seguire questa procedura:
    * In Gestione IIS selezionare il pool di applicazioni, aprire **Impostazioni avanzate** e prendere nota dell'identità in **Modello di processo**.
    * Nel pannello di controllo Gestione computer, aggiungere questa identità al gruppo Utenti di Performance Monitor.
  * Se nel server è installato MMA/SCOM, è possibile che alcune versioni entrino in conflitto. Disinstallare SCOM e Status Monitor e reinstallare le versioni più recenti.
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

## Automazione con PowerShell

È possibile usare PowerShell per avviare e arrestare il monitoraggio.

Importare prima di tutto il modulo di Application Insights:

`Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'`

Individuare le applicazioni sottoposte a monitoraggio:

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (facoltativo): nome di un'app Web.
* Visualizza lo stato del monitoraggio di Application Insights per ogni app Web o per l'app denominata nel server IIS.

* Restituisce `ApplicationInsightsApplication` per ogni app.
 * `SdkState==EnabledAfterDeployment`: l'app viene monitorata ed è stata instrumentata in fase di esecuzione dallo strumento Status Monitor oppure da `Start-ApplicationInsightsMonitoring`.
 * `SdkState==Disabled`: l'app non è instrumentata per Application Insights. Non è mai stata instrumentata oppure il monitoraggio in fase di esecuzione è stato disabilitato con lo strumento Status Monitor o con `Stop-ApplicationInsightsMonitoring`.
 * `SdkState==EnabledByCodeInstrumentation`: l'app è stata instrumentata aggiungendo l'SDK al codice sorgente. Il relativo SDK non può essere aggiornato o arrestato.
 * `SdkVersion`: mostra la versione usata per il monitoraggio dell'app.
 * `LatestAvailableSdkVersion`: mostra la versione attualmente disponibile nella raccolta NuGet. Per aggiornare l'app a questa versione, usare `Update-ApplicationInsightsMonitoring`.

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name`: nome dell'app in IIS.
* `-InstrumentationKey`: valore ikey della risorsa di Application Insights in cui visualizzare i risultati.

* Questo cmdlet influisce solo sulle app che non sono già instrumentate, ovvero SdkState==NotInstrumented.

    Il cmdlet non influisce sulle app già instrumentate, in fase di compilazione aggiungendo l'SDK al codice o in fase di esecuzione da un uso precedente di questo cmdlet.

    La versione SDK usata per instrumentare l'app è la versione scaricata più di recente nel server.

    Per scaricare l'ultima versione, usare Update-ApplicationInsightsVersion.

* Se l'esito è positivo, restituisce `ApplicationInsightsApplication`. Se l'esito è negativo, registra una traccia in stderr.

    
          Name                      : Default Web Site/WebApp1
          InstrumentationKey        : 00000000-0000-0000-0000-000000000000
          ProfilerState             : ApplicationInsights
          SdkState                  : EnabledAfterDeployment
          SdkVersion                : 1.2.1
          LatestAvailableSdkVersion : 1.2.3

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name`: nome di un'app in IIS.
* `-All`: arresta il monitoraggio di tutte le app nel server IIS per cui `SdkState==EnabledAfterDeployment`

* Arresta il monitoraggio delle app specificate e rimuove la strumentazione. Funziona solo per le app instrumentate in fase di esecuzione usando lo strumento Status Monitor o Start-ApplicationInsightsApplication (`SdkState==EnabledAfterDeployment`).

* Restituisce ApplicationInsightsApplication.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name`: nome di un'app Web in IIS.
* `-InstrumentationKey` (facoltativo): consente di modificare la risorsa a cui vengono inviati i dati di telemetria dell'app.
* Questo cmdlet:
 * Aggiorna l'app denominata alla versione dell'SDK scaricata più di recente nel computer. Funziona solo se `SdkState==EnabledAfterDeployment`.
 * Se si specifica una chiave di strumentazione, l'app denominata viene riconfigurata per l'invio di dati di telemetria alla risorsa con tale chiave. Funziona se `SdkState != Disabled`.

`Update-ApplicationInsightsVersion`

* Scarica l'ultima versione di Application Insights SDK nel server.

## Modello di Azure

Se l'app Web è in Azure e si creano le risorse usando un modello di Azure Resource Manager, è possibile configurare Application Insights aggiungendo quanto segue al nodo risorse:

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource`: nome della risorsa di Application Insights.
* `myWebAppName`: ID dell'app Web.

## <a name="next"></a>Passaggi successivi

* [Creare test Web][availability] per assicurarsi che il sito rimanga attivo.
* [Cercare eventi e log][diagnostic] per facilitare la diagnosi dei problemi.
* [Aggiungere dati di telemetria del client Web][usage] per visualizzare le eccezioni dal codice della pagina Web e consentire di inserire le chiamate di traccia.
* [Aggiungere Application Insights SDK al codice del servizio Web][greenbrown] per poter inserire chiamate di traccia e log nel codice del server.



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-web-track-usage.md

<!---HONumber=AcomDC_0907_2016-->