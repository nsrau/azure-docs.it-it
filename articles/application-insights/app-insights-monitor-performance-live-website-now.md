---
title: Diagnosi dei problemi di prestazioni in un sito Web IIS in esecuzione | Microsoft Docs
description: Monitorare le prestazioni di un sito Web senza ripetere la distribuzione. Usare la versione autonoma o con Application Insights SDK per ottenere la telemetria di dipendenza.
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: ee9ebc23ce805bb4665669077a4d3fddf4c43e32
ms.openlocfilehash: a190b1990a4ae4e7ad52cc1a7e802c8002522917


---
# <a name="instrument-web-apps-at-runtime-with-application-insights"></a>Instrumentare app Web in fase di esecuzione con Application Insights


È possibile instrumentare un'app Web attiva con Azure Application Insights senza dover modificare o ridistribuire il codice. In caso di app ospitate da un server IIS locale, si installa Status Monitor. In caso di app Web di Azure o in esecuzione in una VM di Azure, invece, è possibile installare l'estensione Application Insights. Sono disponibili anche articoli separati sulla strumentazione di [app Web J2EE live](app-insights-java-live.md) e [Servizi cloud di Azure](app-insights-cloudservices.md).

![grafici di esempio](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

È possibile scegliere di applicare Application Insights alle applicazioni Web .NET in tre modi:

* **Fase di compilazione:** [aggiungere Application Insights SDK][greenbrown] al codice dell'app Web.
* **Fase di esecuzione:** instrumentare l'app Web sul server, come descritto di seguito, senza ricompilare e ridistribuire il codice.
* **Entrambe le opzioni:** compilare l'SDK nel codice dell'app Web e applicare anche le estensioni della fase di esecuzione, sfruttando il meglio delle due opzioni.

Ecco un riepilogo di ciò che offrono i singoli modi:

|  | Fase di compilazione | Fase di esecuzione |
| --- | --- | --- |
| Richieste ed eccezioni |Sì |Sì |
| [Eccezioni più dettagliate](app-insights-asp-net-exceptions.md) | |Sì |
| [Diagnostica delle dipendenze](app-insights-asp-net-dependencies.md) |In .NET 4.6 e versioni successive, ma meno dettagli |Sì, dettagli completi: codici di risultato, testo del comando SQL, verbo HTTP|
| [Contatori delle prestazioni di sistema](app-insights-performance-counters.md) |Sì |Sì |
| [API per telemetria personalizzata][api] |Sì | |
| [Integrazione log di traccia](app-insights-asp-net-trace-logs.md) |Sì | |
| [Visualizzazione pagina e dati utente](app-insights-javascript.md) |Sì | |
| Ricompilazione del codice non necessaria |No | |

## <a name="instrument-your-web-app-at-run-time"></a>Strumentazione dell'app Web in fase di esecuzione
È necessaria una sottoscrizione di [Microsoft Azure](http://azure.com) .

### <a name="if-your-app-is-an-azure-web-app-or-cloud-service"></a>Se l'app è un'app Web di Azure o un servizio cloud
* Selezionare Application Insights nel pannello di controllo dell'app in Azure.

    [Altre informazioni](app-insights-azure.md).

### <a name="if-your-app-is-hosted-on-your-iis-server"></a>Se l'app è ospitata nel server IIS
1. Nel server Web IIS accedere con le credenziali di amministratore.
2. Scaricare e installare il [programma di installazione di Status Monitor](http://go.microsoft.com/fwlink/?LinkId=506648).  
3. Selezionare l'applicazione Web installata o il sito Web da monitorare, quindi configurare la risorsa in cui visualizzare i risultati nel portale Application Insights. È necessario aver eseguito l'accesso a Microsoft Azure.

    ![Scegliere un'applicazione e una risorsa.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

    In genere, si sceglie di configurare una nuova risorsa e un nuovo [gruppo di risorse][roles].

    In alternativa è possibile usare una risorsa esistente se sono già stati configurati [test Web][availability] per il sito o il [monitoraggio del client Web][client].
4. Riavviare IIS.

    ![Scegliere Riavvia nella parte superiore della finestra di dialogo.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    Il servizio Web verrà interrotto per un breve periodo di tempo.
5. Si noti che ApplicationInsights.config è stato inserito tra le app Web da monitorare.

    ![Trovare il file con estensione config insieme ai file di codice dell'app Web.](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)
   

#### <a name="want-to-reconfigure-later"></a>Configurare o riconfigurare in un secondo momento
Dopo aver completato la procedura guidata, è possibile riconfigurare l'agente in qualsiasi momento. È inoltre possibile usare questa stessa procedura se l'agente è stato installato ma la configurazione iniziale presenta alcuni problemi.

![Fare clic sull'icona di Application Insights sulla barra delle applicazioni](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)

## <a name="view-performance-telemetry"></a>Visualizzare i dati di telemetria relativi alle prestazioni
Accedere al [portale di Azure](https://portal.azure.com), passare ad Application Insights e aprire la risorsa creata.

![Scegliere Sfoglia, Application Insights, quindi selezionare l'app](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

Aprire il pannello delle prestazioni per visualizzare una richiesta, il tempo di risposta, le dipendenze e altri dati.

![Prestazioni](./media/app-insights-monitor-performance-live-website-now/21-perf.png)

Fare clic su qualsiasi grafico per aprire una visualizzazione più dettagliata.

È possibile [modificare, ridisporre, salvare](app-insights-metrics-explorer.md) e aggiungere a un [dashboard](app-insights-dashboards.md) i grafici o l'intero pannello.

## <a name="dependencies"></a>Dipendenze
Il grafico di durata delle dipendenze mostra il tempo impiegato dalle chiamate dall'app a componenti esterni quali database, API REST o archiviazione BLOB di Azure.

Per segmentare il grafico in base alle chiamate a diverse dipendenze, modificare il grafico, attivare il raggruppamento e quindi raggruppare per Dipendenza, Tipo di dipendenza o Prestazioni dipendenze.

![Dipendenza](./media/app-insights-monitor-performance-live-website-now/23-dep.png)

## <a name="performance-counters"></a>Contatori delle prestazioni
Fare clic su Server nel pannello della panoramica per visualizzare i grafici dei contatori delle prestazioni del server, come l'utilizzo di memoria e di occupazione della CPU.

Se sono presenti diverse istanze del server, può essere opportuno modificare i grafici eseguendo il raggruppamento per Istanza del ruolo.

![Server](./media/app-insights-monitor-performance-live-website-now/22-servers.png)

È inoltre possibile modificare il set di contatori delle prestazioni segnalati da SDK. 

## <a name="exceptions"></a>Eccezioni
![Fare clic nel grafico di eccezioni del server](./media/app-insights-monitor-performance-live-website-now/appinsights-039-1exceptions.png)

È possibile eseguire il drill down di eccezioni specifiche (degli ultimi sette giorni) e ottenere le analisi dello stack e i dati di contesto.

## <a name="sampling"></a>Campionamento
Se l'applicazione invia una grande quantità di dati ed è in uso Application Insights SDK per ASP.NET 2.0.0 Beta3 o versioni successive, la funzionalità del campionamento adattivo può operare e inviare solo una percentuale dei dati di telemetria. [Altre informazioni sul campionamento.](app-insights-sampling.md)

## <a name="troubleshooting"></a>Risoluzione dei problemi
### <a name="connection-errors"></a>Errori di connessione
Per consentire il funzionamento di Status Monitor è necessario aprire [alcune porte in uscita](app-insights-ip-addresses.md#outgoing-ports) nel firewall del server.

### <a name="no-telemetry"></a>Nessun dato di telemetria?
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

## <a name="system-requirements"></a>Requisiti di sistema
Supporto del sistema operativo per Application Insights Status Monitor sul server

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

con SP più recente e .NET Framework 4.5

Sul lato client Windows 7, 8, 8.1 e 10, con .NET Framework 4.5

Il supporto IIS è: IIS 7, 7.5, 8, 8.5 (IIS è obbligatorio)

## <a name="automation-with-powershell"></a>Automazione con PowerShell
È possibile usare PowerShell nel server IIS per avviare e arrestare il monitoraggio.

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
  * `SdkVersion` : mostra la versione usata per il monitoraggio dell'app.
  * `LatestAvailableSdkVersion`: mostra la versione attualmente disponibile nella raccolta NuGet. Per aggiornare l'app a questa versione, usare `Update-ApplicationInsightsMonitoring`.

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` : nome dell'app in IIS.
* `-InstrumentationKey` : valore ikey della risorsa di Application Insights in cui visualizzare i risultati.
* Questo cmdlet influisce solo sulle app che non sono già instrumentate, ovvero SdkState==NotInstrumented.

    Il cmdlet non influisce sulle app già instrumentate, in fase di compilazione aggiungendo l'SDK al codice o in fase di esecuzione da un uso precedente di questo cmdlet.

    La versione SDK usata per instrumentare l'app è la versione scaricata più di recente nel server.

    Per scaricare l'ultima versione, usare Update-ApplicationInsightsVersion.
* Se l'esito è positivo, restituisce `ApplicationInsightsApplication` . Se l'esito è negativo, registra una traccia in stderr.

          Name                      : Default Web Site/WebApp1
          InstrumentationKey        : 00000000-0000-0000-0000-000000000000
          ProfilerState             : ApplicationInsights
          SdkState                  : EnabledAfterDeployment
          SdkVersion                : 1.2.1
          LatestAvailableSdkVersion : 1.2.3

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` : nome di un'app in IIS.
* `-All`: arresta il monitoraggio di tutte le app nel server IIS per cui `SdkState==EnabledAfterDeployment`
* Arresta il monitoraggio delle app specificate e rimuove la strumentazione. Funziona solo per le app instrumentate in fase di esecuzione usando lo strumento Status Monitor o Start-ApplicationInsightsApplication. (`SdkState==EnabledAfterDeployment`)
* Restituisce ApplicationInsightsApplication.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name`: nome di un'app Web in IIS.
* `-InstrumentationKey` (facoltativo): consente di modificare la risorsa a cui vengono inviati i dati di telemetria dell'app.
* Questo cmdlet:
  * Aggiorna l'app denominata alla versione dell'SDK scaricata più di recente nel computer. Funziona solo se `SdkState==EnabledAfterDeployment`.
  * Se si specifica una chiave di strumentazione, l'app denominata viene riconfigurata per l'invio di dati di telemetria alla risorsa con tale chiave. Funziona se `SdkState != Disabled`.

`Update-ApplicationInsightsVersion`

* Scarica l'ultima versione di Application Insights SDK nel server.

## <a name="a-namenextanext-steps"></a><a name="next"></a>Passaggi successivi
* [Creare test Web][availability] per assicurarsi che il sito rimanga attivo.
* Per facilitare la diagnosi dei problemi, [cercare eventi e log][diagnostic].
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



<!--HONumber=Jan17_HO1-->


