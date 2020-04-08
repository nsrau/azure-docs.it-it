---
title: Risoluzione dei problemi relativi a dati non disponibili in Application Insights per .NET
description: I dati non vengono visualizzati in Azure Application Insights Risposte ai problemi più comuni.
ms.topic: conceptual
ms.date: 07/23/2018
ms.openlocfilehash: 34fc51f8f656ec0f630bd984ac1b28fbaa5e4dae
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802587"
---
# <a name="troubleshooting-no-data---application-insights-for-netnet-core"></a>Risoluzione dei problemi relativi all'assenza di dati - Application Insights per .NET/.NET CoreTroubleshooting no data - Application Insights for .NET/.NET Core

## <a name="some-of-my-telemetry-is-missing"></a>Alcuni dati di telemetria sono mancanti
*In Application Insights, è visibile solo una frazione degli eventi generati dall'applicazione.*

* Se si visualizza in modo costante la stessa frazione, il problema è probabilmente causato dal [campionamento](../../azure-monitor/app/sampling.md)adattivo. Per verificarlo, aprire Ricerca dal pannello della panoramica ed esaminare l'istanza di una Richiesta o di un altro evento. Nella parte inferiore della sezione Proprietà fare clic su "..." per visualizzare i dettagli completi della proprietà. Se il Conteggio richieste è inferiore a 1, il campionamento è in esecuzione.
* In caso contrario, è possibile che sia stato raggiunto il [limite di velocità dati](../../azure-monitor/app/pricing.md#limits-summary) per il piano tariffario. Questi limiti vengono applicati per minuto.

*Sto vivendo la perdita di dati in modo casuale.*

* Verificare se si verifica una perdita di dati nel canale di [telemetriaCheck](telemetry-channels.md#does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost) if you are experiencing data loss at Telemetry Channel

* Verificare la presenza di eventuali problemi noti nel repository GitHub del canale di telemetriaCheck for any known issues in Telemetry Channel [GitHub repo](https://github.com/Microsoft/ApplicationInsights-dotnet/issues)

*Sto riscontrando una perdita di dati in Console App o su Web App quando l'app sta per arrestarsi.*

* Il canale SDK mantiene i dati di telemetria nel buffer e li invia in batch. Se l'applicazione è in arresto, potrebbe essere necessario chiamare in modo esplicito [Flush()](api-custom-events-metrics.md#flushing-data). Il `Flush()` comportamento di dipende dal [canale](telemetry-channels.md#built-in-telemetry-channels) effettivo utilizzato.

## <a name="no-data-from-my-server"></a>Non sono disponibili dati dal server
*L'app è stata installata nel server Web e ora non vengono visualizzati i dati di telemetria. Nel computer di sviluppo funzionava correttamente.*

* Probabilmente è un problema del firewall. [Impostare le eccezioni del firewall per l'invio di dati da parte di Application Insights](../../azure-monitor/app/ip-addresses.md).
* È possibile che il server IIS non sia conforme ad alcuni prerequisiti: Estendibilità .NET 4.5 e ASP.NET 4.5.

*Ho [installato Status Monitor](../../azure-monitor/app/monitor-performance-live-website-now.md) sul mio server web per monitorare le applicazioni esistenti. Non vedo nessun risultato.*

* Vedere [Risoluzione dei problemi relativi a Status Monitor](../../azure-monitor/app/monitor-performance-live-website-now.md#troubleshoot).

## <a name="no-add-application-insights-option-in-visual-studio"></a><a name="q01"></a>Nessuna opzione "Aggiungi Application Insights" in Visual Studio
*Quando si fa clic con il pulsante destro del mouse su un progetto esistente in Esplora soluzioni, non è presente alcuna opzione di Application Insights.*

* Non tutti i tipi di progetto .NET sono supportati dagli strumenti. I progetti Web e WCF sono supportati. Per altri tipi di progetto, ad esempio applicazioni desktop o di servizio, è comunque possibile [aggiungere manualmente un SDK Application Insights al progetto](../../azure-monitor/app/windows-desktop.md).
* Assicurarsi di disporre di [Visual Studio 2013 Update 3 o versioni successive](https://docs.microsoft.com/visualstudio/releasenotes/vs2013-update3-rtm-vs). È preinstallata con Developer Analytics Tools che fornisce Application Insights SDK.
* Selezionare **Strumenti**, **Estensioni e aggiornamenti** e verificare l'installazione e l'abilitazione di **Developer Analytics Tools**. In tal caso, fare clic su **Aggiornamenti** per verificare se è disponibile un aggiornamento.
* Aprire la finestra di dialogo Nuovo progetto e scegliere Applicazione Web ASP.NET. Se l'opzione Application Insights è presente, gli strumenti sono installati. In caso contrario, prova a disinstallare e quindi reinstallare gli strumenti di Analisi per sviluppatori.

## <a name="adding-application-insights-failed"></a><a name="q02"></a>Non è stato possibile aggiungere Application Insights
*Quando si prova ad aggiungere Application Insights a un progetto esistente, viene visualizzato un messaggio di errore.*

Cause possibili:

* la comunicazione con il portale Application Insights si interrompe;
* si è verificato un problema con l'account Azure;
* si ha [accesso in sola lettura alla sottoscrizione o al gruppo in cui si è provato a creare la nuova risorsa](../../azure-monitor/app/resources-roles-access-control.md).

Correzione:

* Verificare di avere specificato le credenziali di accesso per l'account Azure appropriato.
* Nel browser, verificare di avere accesso al [portale di Azure](https://portal.azure.com). Aprire le impostazioni e vedere se sono presenti restrizioni.
* [Aggiungere Application Insights a un nuovo progetto](../../azure-monitor/app/asp-net.md): in Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e scegliere "Aggiungi Application Insights".

## <a name="i-get-an-error-instrumentation-key-cannot-be-empty"></a><a name="emptykey"></a>Viene visualizzato l'errore: "La chiave di strumentazione non può essere vuota"
Sembra che si sia verificato un problema durante l'installazione di Application Insights o forse di un adattatore di registrazione.

Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Application Insights > Configura Application Insights**. Verrà visualizzata una finestra di dialogo che invita ad accedere ad Azure e a creare una risorsa di Application Insights o a riusarne una esistente.

## <a name="nuget-packages-are-missing-on-my-build-server"></a><a name="NuGetBuild"></a> Un messaggio informa che i pacchetti NuGet sono mancanti nel server di compilazione
*La compilazione funziona come previsto durante il debug nel computer di sviluppo, ma nel server di compilazione viene visualizzato un messaggio di errore NuGet.*

Vedere [NuGet Package Restore](https://docs.nuget.org/Consume/Package-Restore) (Ripristino dei pacchetti NuGet) e [Automatic Package Restore](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore) (Ripristino automatico dei pacchetti).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Comando di menu mancante per l'apertura di Application Insights da Visual Studio
*Quando si fa doppio clic su un progetto in Esplora soluzioni, non vengono visualizzati i comandi di Application Insights o non viene visualizzato il comando Apri Application Insights.*

Cause possibili:

* la risorsa di Application Insights è stata creata manualmente oppure il progetto è di un tipo non supportato dagli Strumenti Application Insights;
* L'estensione Developer Analytics Tools è disabilitata nella propria istanza di Visual Studio.
* la versione di Visual Studio è precedente alla 2013 Update 3.

Correzione:

* Assicurarsi che la versione di Visual Studio sia 2013 Update 3 o versione successiva.
* Seleziona **Strumenti,** **Estensioni e aggiornamenti** e verifica che gli strumenti di Developer **Analytics** siano installati e abilitati. In tal caso, fare clic su **Aggiornamenti** per verificare se è disponibile un aggiornamento.
* Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni. Se viene visualizzato il comando **Application Insights > Configura Application Insights**, usarlo per connettere il progetto alla risorsa nel servizio Application Insights.

In caso contrario, il tipo di progetto non è supportato direttamente dagli strumenti di Developer Analytics. Per visualizzare i dati di telemetria, accedere al [portale di Azure](https://portal.azure.com), scegliere Application Insights nella barra di spostamento a sinistra e selezionare l'applicazione.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>"Accesso negato" all'apertura di Application Insights da Visual Studio
*Il comando di menu "Apri Application Insights" apre il portale di Azure, ma si verifica un errore di accesso negato.*

Le ultime informazioni di accesso Microsoft usate nel browser predefinito non hanno accesso alla [risorsa creata quando Application Insights è stato aggiunto all'app](../../azure-monitor/app/asp-net.md). Le cause possibili sono due:

* L'utente ha più di un account Microsoft, ad esempio un account Microsoft di lavoro e uno personale. Le ultime informazioni di accesso usate nel browser predefinito sono relative a un account diverso da quello che ha accesso per [Application Insights al progetto](../../azure-monitor/app/asp-net.md).
  * Correzione: fai clic sul tuo nome in alto a destra nella finestra del browser e disconnettiti. Quindi accedi con l'account che ha accesso. Nella barra di spostamento a sinistra fare quindi clic su Application Insights e selezionare l'app.
* Un altro utente ha aggiunto Application Insights al progetto e ha dimenticato di concedere l' [accesso al gruppo di risorse](../../azure-monitor/app/resources-roles-access-control.md) in cui è stato creato.
  * Correzione: se è stato usato un account aziendale, è possibile chiedere di essere aggiunti al team o che venga concesso l'accesso individuale al gruppo di risorse.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>L'asset non viene trovato all'apertura di Application Insights da Visual Studio
*Il comando di menu "Apri Application Insights" apre il portale di Azure, ma si verifica un errore di asset non trovato.*

Cause possibili:

* la risorsa di Application Insights per l'applicazione è stata eliminata;
* la chiave di strumentazione è stata impostata o modificata in ApplicationInsights.config modificandola direttamente, senza aggiornare il file di progetto.

La chiave di strumentazione in ApplicationInsights.config determina dove vengono inviati i dati di telemetria. Una riga nel file di progetto determina quale risorsa viene aperta quando si usa il comando in Visual Studio.

Correzione:

* Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere Application Insights, Configura Application Insights. Nella finestra di dialogo è possibile scegliere di inviare i dati di telemetria a una risorsa esistente o crearne una nuova. Oppure:
* Aprire direttamente la risorsa. Accedere al [portale di Azure](https://portal.azure.com), fare clic su Application Insights nella barra di spostamento a sinistra e quindi selezionare l'app.

## <a name="where-do-i-find-my-telemetry"></a>Dove è possibile trovare i dati di telemetria?
*Ho eseguito l'accesso al portale di [Microsoft Azure](https://portal.azure.com)e si sta esaminando il dashboard principale di Azure. Dove si trovano i dati di Application Insights?*

* Nella barra di spostamento a sinistra fare clic su Application Insights e selezionare l'app. Se non è presente alcun progetto, è necessario [aggiungere o configurare Application Insights nel progetto Web](../../azure-monitor/app/asp-net.md).  
  Verranno visualizzati alcuni grafici di riepilogo. Fare clic su qualsiasi grafico per visualizzare altri dettagli.
* In Visual Studio fare clic sul pulsante Application Insights durante il debug.

## <a name="no-server-data-or-no-data-at-all"></a><a name="q03"></a> Dati assenti o dati del server non presenti
*Dopo aver eseguito l'app e aver aperto il servizio Application Insights in Microsoft Azure, i grafici mostrano solo messaggi di informazioni su come raccogliere i dati o di mancata configurazione.* Oppure *mostrano solo la visualizzazione pagina e i dati utente, ma non i dati del server.*

* Eseguire l'applicazione in modalità di debug in Visual Studio (F5). Usare l'applicazione per generare alcuni dati di telemetria. Verificare che gli eventi registrati vengano visualizzati nella finestra di output di Visual Studio.  
  ![](./media/asp-net-troubleshoot-no-data/output-window.png)
* Nel portale di Application Insights aprire [Diagnostic Search](../../azure-monitor/app/diagnostic-search.md)(Ricerca diagnostica). I dati vengono in genere visualizzati prima qui.
* Fare clic sul pulsante Aggiorna. Il pannello viene automaticamente aggiornato periodicamente, ma è anche possibile farlo manualmente. L'intervallo di aggiornamento è più lungo per intervalli di tempo maggiori.
* Verificare che le chiavi di strumentazione corrispondano. Nel pannello principale per l'app nel portale di Application Insights esaminare **Chiave di strumentazione** nell'elenco a discesa **Informazioni di base**. Nel progetto in Visual Studio aprire quindi ApplicationInsights.config e trovare `<instrumentationkey>`. Verificare che le due chiavi siano identiche. In caso contrario:  
  * Nel portale fare clic su Application Insights e cercare la risorsa dell'app con la chiave corretta.
  * In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse e scegliere Application Insights, Configura. Reimpostare l'app per l'invio di dati di telemetria alla risorsa corretta.
  * Se le chiavi non corrispondono, assicurarsi di aver usato le stesse credenziali di accesso in Visual Studio e nel portale.
* Nel [dashboard della schermata iniziale di Microsoft Azure](https://portal.azure.com)esaminare la mappa relativa all'integrità del servizio. Se ci sono indicazioni di avviso, attendere che tornino alla normalità, quindi chiudere e riaprire il pannello dell'applicazione di Application Insights.
* Controllare anche il [blog sullo stato](https://blogs.msdn.microsoft.com/servicemap-status/).
* Potrebbe essere stato scritto un codice per l'[SDK lato server](../../azure-monitor/app/api-custom-events-metrics.md) che trasforma la chiave di strumentazione in istanze di `TelemetryClient` o in `TelemetryContext`. Potrebbe anche essere stata scritta una [configurazione del filtro o di campionamento](../../azure-monitor/app/api-filtering-sampling.md) che esclude troppi dati.
* Se si è modificato ApplicationInsights.config, controllare attentamente la configurazione di [TelemetryInitializers e TelemetryProcessors](../../azure-monitor/app/api-filtering-sampling.md). Un tipo o un parametro denominato in modo non corretto può impedire a SDK l'invio dei dati.

## <a name="no-data-on-page-views-browsers-usage"></a><a name="q04"></a>Dati non presenti in Visualizzazioni pagina, Browser o Utilizzo
*I dati sono presenti nei grafici Tempo di risposta server e Richieste server, ma non sono presenti in Tempo di caricamento della visualizzazione pagina o nei pannelli Browser e Utilizzo.*

I dati provengono da script nelle pagine Web. 

* Se Application Insights è stato aggiunto a un progetto Web esistente, [è necessario aggiungere manualmente gli script](../../azure-monitor/app/javascript.md).
* Verificare che il sito non venga visualizzato in modalità di compatibilità in Internet Explorer.
* Usare la funzionalità di debug del browser, F12 in alcuni browser e quindi scegliere Rete, per verificare che i dati vengano inviati a `dc.services.visualstudio.com`.

## <a name="no-dependency-or-exception-data"></a>Dati eccezione o dati sulle dipendenze non presenti
Vedere gli articoli relativi alla [telemetria delle dipendenze](../../azure-monitor/app/asp-net-dependencies.md) e alla [telemetria delle eccezioni](asp-net-exceptions.md).

## <a name="no-performance-data"></a>Nessun dato sulle prestazioni
I dati sulle prestazioni (CPU, velocità di I/O e così via) sono disponibili per i [servizi Web Java](../../azure-monitor/app/java-collectd.md), le app desktop di [Windows](../../azure-monitor/app/windows-desktop.md), le app e i servizi [Web IIS se si installa Status Monitor](../../azure-monitor/app/monitor-performance-live-website-now.md)e Azure Cloud [Services](../../azure-monitor/app/app-insights-overview.md). Sono disponibili in Impostazioni > Server.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Dati del server non presenti dopo la pubblicazione dell'app nel server
* Verificare di aver effettivamente copiato tutti i Microsoft. DLL ApplicationInsights al server, insieme a Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
* Nel firewall potrebbe essere necessario [aprire alcune porte TCP](../../azure-monitor/app/ip-addresses.md).
* Se è necessario usare un proxy per inviare all'esterno della rete aziendale, impostare [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) in Web.config
* Windows Server 2008: assicurarsi che siano stati installati i seguenti aggiornamenti: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Non vengono più visualizzati i dati disponibili in precedenza
* Controllare il [blog sullo stato](https://blogs.msdn.com/b/applicationinsights-status/).
* È stata raggiunta la quota mensile relativa ai punti dati? Apri Impostazioni/Quota e Prezzi per scoprirlo. In tal caso, è possibile aggiornare il piano o pagare per la capacità aggiuntiva. Vedere lo [schema dei prezzi](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Non sono presenti tutti i dati previsti
Se l'applicazione invia una grande quantità di dati ed è in uso Application Insights SDK per ASP.NET 2.0.0 Beta3 o versione successiva, la funzionalità di [campionamento adattivo](../../azure-monitor/app/sampling.md) può funzionare e inviare solo una percentuale dei dati di telemetria.

È possibile disabilitarla, ma non è consigliabile. Il campionamento è progettato in modo che i dati di telemetria correlati vengano trasmessi correttamente per scopi diagnostici.

## <a name="client-ip-address-is-0000"></a>L'indirizzo IP del client è 0.0.0.0

Il 5 febbraio 2018 è stata annunciata la rimozione della registrazione dell'indirizzo IP del client. Questa operazione non influenza la posizione geografica.

> [!NOTE]
> Se si ha bisogno dei primi 3 ottetti dell'indirizzo IP è possibile usare un [inizializzatore di telemetria](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#addmodify-properties-itelemetryinitializer) per aggiungere un attributo personalizzato.
> Questa operazione non influisce sui dati raccolti prima del 5 febbraio 2018.

## <a name="wrong-geographical-data-in-user-telemetry"></a>Dati geografici errati nella telemetria utente
Le dimensioni relative alla città, all'area, al paese e alla regione vengono derivate dagli indirizzi IP e non sono sempre accurate. Questi indirizzi IP vengono elaborati prima di tutto per la posizione e quindi modificati su 0.0.0.0 per essere archiviati.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Eccezione "metodo non trovato" durante l'esecuzione dei servizi cloud di Azure
È stata eseguita la compilazione per .NET 4.6? La versione 4.6 non è supportata automaticamente nei ruoli dei servizi cloud di Azure. [Installare la versione 4.6 in ogni ruolo](../../cloud-services/cloud-services-dotnet-install-dotnet.md) prima di eseguire l'app.

## <a name="troubleshooting-logs"></a>Log di risoluzione dei problemi

Seguire queste istruzioni per acquisire i log di risoluzione dei problemi per il framework.

### <a name="net-framework"></a>.NET Framework

1. Installare il pacchetto [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) da NuGet. La versione installata deve corrispondere alla versione attualmente installata di `Microsoft.ApplicationInsighs`

2. Modificare il file applicationinsights.config in modo che includa quanto segue:

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.HostingStartup.FileDiagnosticsTelemetryModule, Microsoft.AspNet.ApplicationInsights.HostingStartup">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    L'applicazione deve disporre delle autorizzazioni di scrittura per il percorso configurato

3. Riavviare il processo in modo che le nuove impostazioni vengano prelevate dall'SDK

4. Al termine, ripristinare queste modifiche.

### <a name="net-core"></a>.NET Core

1. Installare il pacchetto [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) da NuGet. La versione installata deve corrispondere alla versione attualmente installata di `Microsoft.ApplicationInsights`

La versione più recente di Microsoft.ApplicationInsights.AspNetCore è 2.8.2 e fa riferimento a Microsoft.ApplicationInsights versione 2.11.2. Pertanto la versione di Microsoft.AspNet.ApplicationInsights.HostingStartup da installare deve essere 2.11.2

2. Modificare il metodo `ConfigureServices` nella classe `Startup.cs`:

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    L'applicazione deve disporre delle autorizzazioni di scrittura per il percorso configurato

3. Riavviare il processo in modo che le nuove impostazioni vengano prelevate dall'SDK

4. Al termine, ripristinare queste modifiche.


## <a name="collect-logs-with-perfview"></a><a name="PerfView"></a>Raccogliere i log con PerfViewCollect logs with PerfView
[PerfView](https://github.com/Microsoft/perfview) è uno strumento di diagnostica e analisi delle prestazioni gratuito che consente di isolare CPU, memoria e altri problemi raccogliendo e visualizzando le informazioni di diagnostica da molte origini.

Il log EventSource di Application Insights SDK log di auto-risoluzione dei problemi che possono essere acquisiti da PerfView.

Per raccogliere i log, scaricare PerfView ed eseguire questo comando:To collect logs, download PerfView and run this command:
```cmd
PerfView.exe collect -MaxCollectSec:300 -NoGui /onlyProviders=*Microsoft-ApplicationInsights-Core,*Microsoft-ApplicationInsights-Data,*Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,*Microsoft-ApplicationInsights-Extensibility-DependencyCollector,*Microsoft-ApplicationInsights-Extensibility-HostingStartup,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,*Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,*Microsoft-ApplicationInsights-Extensibility-Web,*Microsoft-ApplicationInsights-Extensibility-WindowsServer,*Microsoft-ApplicationInsights-WindowsServer-Core,*Microsoft-ApplicationInsights-Extensibility-EventSourceListener,*Microsoft-ApplicationInsights-AspNetCore
```

È possibile modificare questi parametri in base alle esigenze:You can modify these parameters as needed:
- **MaxCollectSec**. Impostare questo parametro per impedire l'esecuzione di PerfView all'infinito e influire sulle prestazioni del server.
- **OnlyProviders**. Impostare questo parametro per raccogliere solo i log dall'SDK. È possibile personalizzare questo elenco in base alle indagini specifiche. 
- **NoGui**. Impostare questo parametro per raccogliere i log senza la GUI.


Per ulteriori informazioni,
- [Registrazione delle tracce delle prestazioni con PerfView](https://github.com/dotnet/roslyn/wiki/Recording-performance-traces-with-PerfView).
- [Application Insights Event Sources](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/ETW)

## <a name="how-to-remove-application-insights"></a>Come rimuovere Application Insights

Per informazioni su come rimuovere Application Insights in Visual Studio, attenersi alla procedura descritta [nell'articolo](../../azure-monitor/app/remove-application-insights.md)sulla rimozione.

## <a name="still-not-working"></a>Non funzionante...
* [Forum di Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)
