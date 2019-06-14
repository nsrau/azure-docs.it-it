---
title: Monitorare un'app Web ASP.NET live con Azure Application Insights | Microsoft Docs
description: Monitorare le prestazioni di un sito Web senza ripetere la distribuzione. È possibile usare questa funzionalità con app Web ASP.NET ospitate in locale o con macchine virtuali.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: mbullwin
ms.openlocfilehash: 6ad2ab00060528557f618eb684ccfa710c3f09b9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074185"
---
# <a name="instrument-web-apps-at-runtime-with-application-insights-status-monitor"></a>Instrumentare app Web in fase di esecuzione con Application Insights Status Monitor

È possibile instrumentare un'app Web attiva con Azure Application Insights senza dover modificare o ridistribuire il codice. È necessaria una sottoscrizione di [Microsoft Azure](https://azure.com) .

Status Monitor viene usato per instrumentare un'applicazione .NET ospitata in IIS, in locale o in una macchina virtuale.

- Se l'app viene distribuita in servizi app di Azure, seguire [queste istruzioni](azure-web-apps.md).
- Se l'app viene distribuita in una macchina virtuale di Azure, è possibile attivare il monitoraggio di Application Insights dal pannello di controllo di Azure.
- (Sono disponibili anche articoli separati sulla strumentazione [servizi Cloud di Azure](../../azure-monitor/app/cloudservices.md).)


![Screenshot dei grafi della panoramica di Application Insights contenente informazioni su richieste non riuscite, tempo di risposta del server e richieste server](./media/monitor-performance-live-website-now/overview-graphs.png)

È possibile scegliere di applicare Application Insights alle applicazioni Web .NET in due modi:

* **Fase di compilazione:** [aggiungere Application Insights SDK][greenbrown] al codice dell'app Web.
* **Fase di esecuzione:** instrumentare l'app Web sul server, come descritto di seguito, senza ricompilare e ridistribuire il codice.

> [!NOTE]
> Se si usa la strumentazione in fase di compilazione, la strumentazione in fase di esecuzione non funzionerà anche se è attivata.

Ecco un riepilogo di ciò che offrono i singoli modi:

|  | Fase di compilazione | Fase di esecuzione |
| --- | --- | --- |
| Richieste ed eccezioni |Yes |Yes |
| [Eccezioni più dettagliate](../../azure-monitor/app/asp-net-exceptions.md) | |Yes |
| [Diagnostica delle dipendenze](../../azure-monitor/app/asp-net-dependencies.md) |In .NET 4.6 e versioni successive, ma meno dettagli |Sì, dettagli completi: codici risultato, testo del comando SQL, verbo HTTP|
| [Contatori delle prestazioni di sistema](../../azure-monitor/app/performance-counters.md) |Yes |Yes |
| [API per telemetria personalizzata][api] |Yes |No |
| [Integrazione log di traccia](../../azure-monitor/app/asp-net-trace-logs.md) |Yes |No |
| [Visualizzazione pagina e dati utente](../../azure-monitor/app/javascript.md) |Yes |No |
| Ricompilazione del codice necessaria |Yes | No |



## <a name="monitor-a-live-iis-web-app"></a>Monitorare un'app Web live di IIS

Se l'app è ospitata in un server IIS, abilitare Application Insights usando Status Monitor.

1. Nel server Web IIS accedere con le credenziali di amministratore.
2. Se Application Insights Status Monitor non è già installato, [scaricare ed eseguire il programma di installazione](#download)
3. In Status Monitor selezionare l'applicazione Web installata o il sito Web da monitorare. Accedere con le credenziali di Azure.

    Configurare la risorsa in cui si vogliono visualizzare i risultati nel portale di Application Insights. È in genere consigliabile creare una nuova risorsa. Selezionare una risorsa esistente se sono già disponibili [test Web][availability] o il [monitoraggio del client][client] per questa app. 

    ![Scegliere un'applicazione e una risorsa.](./media/monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. Riavviare IIS.

    ![Scegliere Riavvia nella parte superiore della finestra di dialogo.](./media/monitor-performance-live-website-now/appinsights-036-restart.png)

    Il servizio Web viene interrotto per un breve periodo di tempo.

## <a name="customize-monitoring-options"></a>Personalizzare le opzioni di monitoraggio

L'abilitazione di Application Insights aggiunge DLL e il file ApplicationInsights.config all'app Web. È possibile [modificare il file con estensione config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) per modificare alcune opzioni.

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>Quando si ripubblica l'app, riabilitare Application Insights

Prima di ripubblicare l'app, prendere in considerazione l'[aggiunta di Application Insights al codice in Visual Studio][greenbrown]. Questo approccio consente di ottenere dati di telemetria più dettagliati e di scrivere dati di telemetria personalizzati.

Per ripetere la pubblicazione senza aggiungere Application Insights al codice, si noti che il processo di distribuzione potrebbe eliminare i file DLL e il file ApplicationInsights.config dal sito Web pubblicato. Di conseguenza:

1. Se sono state apportate modifiche al file ApplicationInsights.config, copiarlo prima di ripubblicare l'app.
2. Pubblicare di nuovo l'app.
3. Abilitare di nuovo il monitoraggio di Application Insights. Usare il metodo appropriato, ovvero il pannello di controllo dell'app Web di Azure o Status Monitor in un host IIS.
4. Ripristinare eventuali modifiche apportate al file con estensione config.


## <a name="troubleshoot"></a>Risoluzione dei problemi

### <a name="confirm-a-valid-installation"></a>Confermare un'installazione valida 

Questi sono alcuni passaggi che è possibile eseguire per verificare che l'installazione abbia avuto esito positivo.

- Confermare la presenza del file applicationinsights.config nella directory di destinazione dell'app e l'inclusione della ikey.

- Se si ritiene che i dati non siano presenti, è possibile eseguire una query semplice [Analytics](../log-query/get-started-portal.md) per elencare tutti i ruoli del cloud che stanno inviando dati di telemetria.
  ```Kusto
  union * | summarize count() by cloud_RoleName, cloud_RoleInstance
  ```

- Per confermare che Application Insights sia stato associato correttamente è possibile eseguire [Sysinternals Handle](https://docs.microsoft.com/sysinternals/downloads/handle) in una finestra di comando per confermare applicationinsights.dll sia stato caricato da IIS.
  ```cmd
  handle.exe /p w3wp.exe
  ```


### <a name="cant-connect-no-telemetry"></a>Nessuna connessione? Nessun dato di telemetria?

* Per consentire il funzionamento di Status Monitor, aprire le [porte in uscita necessarie](../../azure-monitor/app/ip-addresses.md#outgoing-ports) nel firewall del server.

### <a name="unable-to-login"></a>Non è possibile effettuare l'accesso

* Se Status Monitor non è in grado di effettuare l'accesso, eseguire l'installazione dalla riga di comando. Status Monitor tenta di effettuare l'accesso per raccogliere la ikey, tuttavia è possibile fornirla manualmente usando il comando:

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000
```

### <a name="could-not-load-file-or-assembly-systemdiagnosticsdiagnosticsource"></a>Impossibile caricare il file o l'assembly "System.Diagnostics.DiagnosticSource"

Questo errore può verificarsi dopo l'abilitazione di Application Insights. Questo avviene perché il programma di installazione sostituisce la dll nella directory bin.
Per correggere, aggiornare il file web.config:

```xml
<dependentAssembly>
    <assemblyIdentity name="System.Diagnostics.DiagnosticSource" publicKeyToken="cc7b13ffcd2ddd51"/>
    <bindingRedirect oldVersion="0.0.0.0-4.*.*.*" newVersion="4.0.2.1"/>
</dependentAssembly>
```

Microsoft sta verificando [qui](https://github.com/Microsoft/ApplicationInsights-Home/issues/301) questo problema.


### <a name="application-diagnostic-messages"></a>Messaggi di diagnostica applicazioni

* Aprire Status Monitor e selezionare la propria applicazione nel pannello a sinistra. Verificare se sono presenti messaggi di diagnostica per l'applicazione nella sezione "Configuration notifications":

  ![Aprire il pannello delle prestazioni per visualizzare una richiesta, il tempo di risposta, le dipendenze e altri dati](./media/monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
  
### <a name="detailed-logs"></a>Log dettagliati

* Per impostazione predefinita Status Monitor restituirà i log di diagnostica in: `C:\Program Files\Microsoft Application Insights\Status Monitor\diagnostics.log`

* Per output di log dettagliati, modificare il file config: `C:\Program Files\Microsoft Application Insights\Status Monitor\Microsoft.Diagnostics.Agent.StatusMonitor.exe.config` e aggiungere `<add key="TraceLevel" value="All" />` ad `appsettings`.
Quindi riavviare il monitoraggio stato.

* Come monitorare lo stato è un'applicazione .NET è anche possibile abilitare [traccia di .net, aggiungere i dati di diagnostica appropriato al file di configurazione](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/trace-debug/system-diagnostics-element). Ad esempio, in alcuni scenari può essere utile vedere cosa accade a livello di rete da [configurazione della traccia di rete](https://docs.microsoft.com/dotnet/framework/network-programming/how-to-configure-network-tracing)

### <a name="insufficient-permissions"></a>Autorizzazioni insufficienti
  
* Se sul server viene visualizzato un messaggio relativo alle autorizzazioni insufficienti, provare a seguire questa procedura:
  * In Gestione IIS selezionare il pool di applicazioni, aprire **Impostazioni avanzate** e prendere nota dell'identità in **Modello di processo**.
  * Nel pannello di controllo Gestione computer, aggiungere questa identità al gruppo Utenti di Performance Monitor.

### <a name="conflict-with-systems-center-operations-manager"></a>Conflitto con Systems Center Operations Manager

* Se nel server è installato MMA/SCOM (System Center Operations Manager), alcune versioni potrebbero entrare in conflitto. Disinstallare SCOM e Status Monitor e reinstallare le versioni più recenti.

### <a name="failed-or-incomplete-installation"></a>Installazione non riuscita o incompleta

Se Status Monitor dà esito negativo durante un'installazione, il risultato potrebbe essere un'installazione incompleta in cui Status Monitor non è in grado di effettuare il ripristino. Ciò richiede una reimpostazione manuale.

Eliminare uno qualsiasi di questi file trovati nella directory dell'applicazione:
- Tutte le DLL nella directory bin che iniziano con "Microsoft.AI." oppure "Microsoft.ApplicationInsights.".
- Questa DLL nella directory bin: "Microsoft.Web.Infrastructure.dll"
- Questa DLL nella directory bin: "System.Diagnostics.DiagnosticSource.dll"
- Rimuovere "App_Data\packages" nella directory dell'applicazione
- Rimuovere "applicationinsights.config" nella directory dell'applicazione


### <a name="additional-troubleshooting"></a>Risoluzione dei problemi aggiuntiva

* Vedere [Risoluzione dei problemi][qna] aggiuntiva.

## <a name="system-requirements"></a>Requisiti di sistema
Supporto del sistema operativo per Application Insights Status Monitor sul server

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

con SP più recente e .NET Framework 4.5 (Status Monitor è integrato in questa versione di framework)

Sul lato client: Windows 7, 8, 8.1 e 10, con .NET Framework 4.5

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

    Il cmdlet non influisce sulle app già instrumentate, sia che siano state instrumentate in fase di compilazione, aggiungendo l'SDK al codice, o in fase di esecuzione da un uso precedente di questo cmdlet.

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

## <a name="questions"></a>Domande su Status Monitor

### <a name="what-is-status-monitor"></a>Che cos'è Status Monitor?

Un'applicazione desktop che viene installata nel server Web IIS e consente di instrumentare e configurare le app Web. 

### <a name="when-do-i-use-status-monitor"></a>Quando si usa Status Monitor?

* Per instrumentare qualsiasi app Web eseguita nel server IIS, anche se è già in esecuzione.
* Per abilitare altri dati di telemetria per le app Web [compilate con Application Insights SDK](../../azure-monitor/app/asp-net.md) in fase di compilazione. 

### <a name="can-i-close-it-after-it-runs"></a>È possibile chiudere Status Monitor dopo l'esecuzione?

Sì. Dopo aver instrumentato i siti Web selezionati, è possibile chiuderlo.

Status Monitor non raccoglie i dati di telemetria, ma si limita a configurare le app Web e impostare alcune autorizzazioni.

### <a name="what-does-status-monitor-do"></a>Che cosa fa Status Monitor?

Quando si seleziona un'app Web per l'instrumentazione da parte di Status Monitor:

* Scarica e inserisce gli assembly di Application Insights e il file ApplicationInsights.config nella cartella dei file binari dell'app Web.
* Abilita la profilatura CLR per raccogliere le chiamate alle dipendenze.

### <a name="what-version-of-application-insights-sdk-does-status-monitor-install"></a>Quale versione di Application Insights SDK installa Status Monitor?

A oggi Status Monitor può installare solo le versioni di Application Insights SDK 2.3 o 2.4. 

Application Insights SDK versione 2.4 è la [ultima versione per il supporto di .NET 4.0](https://github.com/microsoft/ApplicationInsights-dotnet/releases/tag/v2.5.0-beta1) che è stata [EOL gennaio 2016](https://devblogs.microsoft.com/dotnet/support-ending-for-the-net-framework-4-4-5-and-4-5-1/). Pertanto, a partire da ora Status Monitor è utilizzabile per instrumentare un'applicazione .NET 4.0. 

### <a name="do-i-need-to-run-status-monitor-whenever-i-update-the-app"></a>È necessario eseguire Status Monitor ogni volta che si aggiorna l'app?

Se si esegue la ridistribuzione in modo incrementale, non è necessario. 

Se si seleziona l'opzione "Elimina file esistenti" nel processo di pubblicazione, è necessario eseguire nuovamente Status Monitor per configurare Application Insights.

### <a name="what-telemetry-is-collected"></a>Quali dati di telemetria vengono raccolti?

Per le applicazioni che vengono instrumentate solo in fase di esecuzione tramite Status Monitor:

* Richieste HTTP
* Chiamate alle dipendenze
* Eccezioni
* Contatori delle prestazioni

Per le applicazioni già instrumentate in fase di compilazione:

 * Contatori dei processi
 * Chiamate alle dipendenze (.NET 4.5) e valori restituiti nelle chiamate alle dipendenze (.NET 4.6)
 * Valori di analisi dello stack delle eccezioni

[Altre informazioni](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="download"></a>Scaricare Status Monitor

- Scaricare e installare il [programma di installazione di Status Monitor](https://go.microsoft.com/fwlink/?LinkId=506648)
- Oppure eseguire [Installazione guidata piattaforma Web](https://www.microsoft.com/web/downloads/platform.aspx) e cercarvi all'interno Application Insights Status Monitor.

## <a name="next"></a>Passaggi successivi

Visualizzare i dati di telemetria:

* [Esaminare le metriche](../../azure-monitor/app/metrics-explorer.md) per monitorare le prestazioni e l'utilizzo
* Per diagnosticare i problemi, vedere [Eventi e log di ricerca][diagnostic]
* Per informazioni sulle query più avanzate, vedere [Analytics](../../azure-monitor/app/analytics.md)

Aggiungere altri dati di telemetria:

* [Creare test Web][availability] per assicurarsi che il sito rimanga attivo.
* [Aggiungere dati di telemetria del client Web][usage] per visualizzare le eccezioni dal codice della pagina Web e consentire di inserire le chiamate di traccia.
* [Aggiungere Application Insights SDK al codice][greenbrown] per poter inserire chiamate di traccia e log nel codice del server

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[availability]: monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[usage]: ../../azure-monitor/app/javascript.md
