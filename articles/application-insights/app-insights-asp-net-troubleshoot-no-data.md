---
title: Risoluzione dei problemi relativi a dati non disponibili in Application Insights per .NET
description: "I dati non vengono visualizzati in Azure Application Insights Risposte ai problemi più comuni."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: e231569f-1b38-48f8-a744-6329f41d91d3
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: dea21a59b189d1d3d474cbc5e67f64df485a1981
ms.openlocfilehash: b625bd693de1a8e24a0236403c69e500db1aec2d


---
# <a name="troubleshooting-no-data---application-insights-for-net"></a>Risoluzione dei problemi relativi a dati non disponibili in Application Insights per .NET
## <a name="some-of-my-telemetry-is-missing"></a>Alcuni dati di telemetria sono mancanti
*In Application Insights, è visibile solo una frazione degli eventi generati dall'applicazione.*

* Se si visualizza in modo costante la stessa frazione, il problema è probabilmente causato dal [campionamento](app-insights-sampling.md)adattivo. Per verificarlo, aprire Ricerca dal pannello della panoramica ed esaminare l'istanza di una Richiesta o di un altro evento. Nella parte inferiore della sezione Proprietà fare clic su "..." per visualizzare i dettagli completi della proprietà. Se il Conteggio richieste è inferiore a 1, il campionamento è in esecuzione. 
* In caso contrario, è possibile che sia stato raggiunto il [limite di velocità dati](app-insights-pricing.md#limits-summary) per il piano tariffario. Questi limiti vengono applicati per minuto.

## <a name="no-data-from-my-server"></a>Non sono disponibili dati dal server
*L'app è stata installata nel server Web e ora non vengono visualizzati i dati di telemetria. Nel computer di sviluppo funzionava correttamente.*

* Probabilmente è un problema del firewall. [Impostare le eccezioni del firewall per l'invio di dati da parte di Application Insights](app-insights-ip-addresses.md).

*È stato [installato Status Monitor](app-insights-monitor-performance-live-website-now.md) nel server Web per monitorare le app esistenti, ma non è presente alcun risultato.*

* Vedere [Risoluzione dei problemi relativi a Status Monitor](app-insights-monitor-performance-live-website-now.md#troubleshooting). 

## <a name="a-nameq01ano-add-application-insights-option-in-visual-studio"></a><a name="q01"></a>Nessuna opzione "Aggiungi Application Insights" in Visual Studio
*Quando si crea un nuovo progetto in Visual Studio o si fa clic con il pulsante destro del mouse su un progetto esistente in Esplora soluzioni, non è presente alcuna opzione di Application Insights.*

* Non tutti i tipi di progetto .NET sono supportati dagli strumenti. I progetti Web e WCF sono supportati. Per altri tipi di progetto, ad esempio applicazioni desktop o di servizio, è comunque possibile [aggiungere manualmente un SDK Application Insights al progetto](app-insights-windows-desktop.md).
* Assicurarsi di disporre di [Visual Studio 2013 Update 3 o versioni successive](http://go.microsoft.com/fwlink/?LinkId=397827). È fornito con gli Strumenti Application Insights preinstallati.
* Selezionare **Strumenti**, **Estensioni e aggiornamenti** e verificare l'installazione e l'abilitazione di **Strumenti Application Insights**. In tal caso, fare clic su **Aggiornamenti** per verificare se è disponibile un aggiornamento.
* Aprire la finestra di dialogo Nuovo progetto e scegliere Applicazione Web ASP.NET. Se l'opzione Application Insights è presente, gli strumenti sono installati. In caso contrario, provare a disinstallare e reinstallare Strumenti Application Insights.

## <a name="a-nameq02aadding-application-insights-failed"></a><a name="q02"></a>Non è stato possibile aggiungere Application Insights
*Quando si crea un nuovo progetto Web o quando si prova ad aggiungere Application Insights a un progetto esistente, viene visualizzato un messaggio di errore.*

Cause possibili:

* la comunicazione con il portale Application Insights si interrompe;
* si è verificato un problema con l'account Azure;
* si ha [accesso in sola lettura alla sottoscrizione o al gruppo in cui si è provato a creare la nuova risorsa](app-insights-resources-roles-access-control.md).

Correzione:

* Verificare di avere specificato le credenziali di accesso per l'account Azure appropriato. 
* Nel browser, verificare di avere accesso al [portale di Azure](https://portal.azure.com). Aprire le impostazioni e vedere se sono presenti restrizioni.
* [Aggiungere Application Insights a un nuovo progetto](app-insights-asp-net.md): in Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e scegliere "Aggiungi Application Insights".
* Se il problema continua a verificarsi, seguire la [procedura manuale](app-insights-windows-services.md) per aggiungere una risorsa al portale e quindi aggiungere l'SDK al progetto. 

## <a name="a-nameemptykeyai-get-an-error-instrumentation-key-cannot-be-empty"></a><a name="emptykey"></a>Viene visualizzato l'errore: "La chiave di strumentazione non può essere vuota"
Sembra che si sia verificato un problema durante l'installazione di Application Insights o forse di un adattatore di registrazione.

In Esplora soluzioni fare clic con il pulsante destro del mouse su `ApplicationInsights.config` e scegliere **Configura Application Insights**. Verrà visualizzata una finestra di dialogo che invita ad accedere ad Azure e a creare una risorsa di Application Insights o a riusarne una esistente.

## <a name="a-namenugetbuilda-nuget-packages-are-missing-on-my-build-server"></a><a name="NuGetBuild"></a> Un messaggio informa che i pacchetti NuGet sono mancanti nel server di compilazione
*La compilazione funziona come previsto durante il debug nel computer di sviluppo, ma nel server di compilazione viene visualizzato un messaggio di errore NuGet.*

Vedere [NuGet Package Restore](http://docs.nuget.org/Consume/Package-Restore) (Ripristino dei pacchetti NuGet) e [Automatic Package Restore](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore) (Ripristino automatico dei pacchetti).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Comando di menu mancante per l'apertura di Application Insights da Visual Studio
*Quando si fa doppio clic su un progetto in Esplora soluzioni, non vengono visualizzati i comandi di Application Insights o non viene visualizzato il comando Apri Application Insights.*

Cause possibili:

* la risorsa di Application Insights è stata creata manualmente oppure il progetto è di un tipo non supportato dagli Strumenti Application Insights;
* gli Strumenti Application Insights sono disabilitati in Visual Studio;
* la versione di Visual Studio è precedente alla 2013 Update 3.

Correzione:

* Assicurarsi che la versione di Visual Studio sia 2013 Update 3 o versione successiva.
* Selezionare **Strumenti**, **Estensioni e aggiornamenti** e verificare l'installazione e l'abilitazione di **Strumenti Application Insights**. In tal caso, fare clic su **Aggiornamenti** per verificare se è disponibile un aggiornamento.
* Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni. Se viene visualizzato il comando **Configura Application Insights**, usarlo per connettere il progetto alla risorsa nel servizio Application Insights.

In caso contrario, il tipo di progetto non è supportato direttamente dagli Strumenti Application Insights. Per visualizzare i dati di telemetria, accedere al [portale di Azure](https://portal.azure.com), scegliere Application Insights nella barra di spostamento a sinistra e selezionare l'applicazione.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>"Accesso negato" all'apertura di Application Insights da Visual Studio
*Il comando di menu "Apri Application Insights" apre il portale di Azure, ma si verifica un errore di accesso negato.*

![](./media/app-insights-asp-net-troubleshoot-no-data/access-denied.png)

Le ultime informazioni di accesso Microsoft usate nel browser predefinito non hanno accesso alla [risorsa creata quando Application Insights è stato aggiunto all'app](app-insights-asp-net.md). Le cause possibili sono due: 

* L'utente ha più di un account Microsoft, ad esempio un account Microsoft di lavoro e uno personale. Le ultime informazioni di accesso usate nel browser predefinito sono relative a un account diverso da quello che ha accesso per [Application Insights al progetto](app-insights-asp-net.md). 
  
  * Correzione: fare clic sul nome nell'angolo superiore destro della finestra del browser e disconnettersi. Quindi accedere con l'account corretto. Nella barra di spostamento a sinistra fare quindi clic su Application Insights e selezionare l'app.
* Un altro utente ha aggiunto Application Insights al progetto e ha dimenticato di concedere l' [accesso al gruppo di risorse](app-insights-resources-roles-access-control.md) in cui è stato creato. 
  
  * Correzione: se è stato usato un account aziendale, è possibile chiedere di essere aggiunti al team o che venga concesso l'accesso individuale al gruppo di risorse.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>L'asset non viene trovato all'apertura di Application Insights da Visual Studio
*Il comando di menu "Apri Application Insights" apre il portale di Azure, ma si verifica un errore di asset non trovato.*

Cause possibili:

* la risorsa di Application Insights per l'applicazione è stata eliminata;
* la chiave di strumentazione è stata impostata o modificata in ApplicationInsights.config modificandola direttamente, senza aggiornare il file di progetto. 

La chiave di strumentazione in ApplicationInsights.config determina dove vengono inviati i dati di telemetria. Una riga nel file di progetto determina quale risorsa viene aperta quando si usa il comando in Visual Studio. 

Correzione:

* Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere Application Insights, Configura Application Insights. Nella finestra di dialogo è possibile scegliere di inviare i dati di telemetria a una risorsa esistente o crearne una nuova. In alternativa:
* Aprire direttamente la risorsa. Accedere al [portale di Azure](https://portal.azure.com), fare clic su Application Insights nella barra di spostamento a sinistra e quindi selezionare l'app.

## <a name="where-do-i-find-my-telemetry"></a>Dove è possibile trovare i dati di telemetria?
*Dopo aver eseguito l'accesso al [portale di Microsoft Azure](https://portal.azure.com) e aver visualizzato il dashboard della schermata iniziale di Azure, dove è possibile trovare i dati di Application Insights?*

* Nella barra di spostamento a sinistra fare clic su Application Insights e selezionare l'app. Se non è presente alcun progetto, è necessario [aggiungere o configurare Application Insights nel progetto Web](app-insights-asp-net.md).
  
    Verranno visualizzati alcuni grafici di riepilogo. Fare clic su qualsiasi grafico per visualizzare altri dettagli.
* In Visual Studio fare clic sul pulsante Application Insights durante il debug.

## <a name="a-nameq03a-no-server-data-or-no-data-at-all"></a><a name="q03"></a> Dati assenti o dati del server non presenti
*Dopo aver eseguito l'app e aver aperto il servizio Application Insights in Microsoft Azure, i grafici mostrano solo messaggi di informazioni su come raccogliere i dati o di mancata configurazione.* Oppure *mostrano solo la visualizzazione pagina e i dati utente, ma non i dati del server.*

* Eseguire l'applicazione in modalità di debug in Visual Studio (F5). Usare l'applicazione per generare alcuni dati di telemetria. Verificare che gli eventi registrati vengano visualizzati nella finestra di output di Visual Studio. 
  
    ![](./media/app-insights-asp-net-troubleshoot-no-data/output-window.png)
* Nel portale di Application Insights aprire [Diagnostic Search](app-insights-diagnostic-search.md)(Ricerca diagnostica). I dati vengono in genere visualizzati prima qui.
* Fare clic sul pulsante Aggiorna. Il pannello viene automaticamente aggiornato periodicamente, ma è anche possibile farlo manualmente. L'intervallo di aggiornamento è più lungo per intervalli di tempo maggiori.
* Verificare che le chiavi di strumentazione corrispondano. Nel pannello principale per l'app nel portale di Application Insights esaminare **Chiave di strumentazione** nell'elenco a discesa **Informazioni di base**. Nel progetto in Visual Studio aprire quindi ApplicationInsights.config e trovare `<instrumentationkey>`. Verificare che le due chiavi siano identiche. In caso contrario:
  
  * Nel portale fare clic su Application Insights e cercare la risorsa dell'app con la chiave corretta.
  * In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse e scegliere Application Insights, Configura. Reimpostare l'app per l'invio di dati di telemetria alla risorsa corretta.
  * Se le chiavi non corrispondono, assicurarsi di aver usato le stesse credenziali di accesso in Visual Studio e nel portale.
    
    ![](./media/app-insights-asp-net-troubleshoot-no-data/ikey-check.png)
* Nel [dashboard della schermata iniziale di Microsoft Azure](https://portal.azure.com)esaminare la mappa relativa all'integrità del servizio. Se ci sono indicazioni di avviso, attendere che tornino alla normalità, quindi chiudere e riaprire il pannello dell'applicazione di Application Insights.
* Controllare anche il [blog sullo stato](http://blogs.msdn.com/b/applicationinsights-status/).
* Potrebbe essere stato scritto un codice per l'[SDK lato server](app-insights-api-custom-events-metrics.md) che trasforma la chiave di strumentazione in istanze di `TelemetryClient` o in `TelemetryContext`. Potrebbe anche essere stata scritta una [configurazione del filtro o di campionamento](app-insights-api-filtering-sampling.md) che esclude troppi dati.
* Se si è modificato ApplicationInsights.config, controllare attentamente la configurazione di [TelemetryInitializers e TelemetryProcessors](app-insights-api-filtering-sampling.md). Un tipo o un parametro denominato in modo non corretto può impedire a SDK l'invio dei dati.

## <a name="a-nameq04ano-data-on-page-views-browsers-usage"></a><a name="q04"></a>Dati non presenti in Visualizzazioni pagina, Browser o Utilizzo
*I dati sono presenti nei grafici Tempo di risposta server e Richieste server, ma non sono presenti in Tempo di caricamento della visualizzazione pagina o nei pannelli Browser e Utilizzo.*

I dati provengono da script nelle pagine Web. 

* Se Application Insights è stato aggiunto a un progetto Web esistente, [è necessario aggiungere manualmente gli script](app-insights-javascript.md).
* Verificare che il sito non venga visualizzato in modalità di compatibilità in Internet Explorer.
* Usare la funzionalità di debug del browser, F12 in alcuni browser e quindi scegliere Rete, per verificare che i dati vengano inviati a `dc.services.visualstudio.com`.

## <a name="no-dependency-or-exception-data"></a>Dati eccezione o dati sulle dipendenze non presenti
Vedere gli articoli relativi alla [telemetria delle dipendenze](app-insights-asp-net-dependencies.md) e alla [telemetria delle eccezioni](app-insights-asp-net-exceptions.md).

## <a name="no-performance-data"></a>Nessun dato sulle prestazioni
I dati sulle prestazioni (CPU, velocità di IO e così via) sono disponibili per[servizi Web Java](app-insights-java-collectd.md), [app desktop di Windows](app-insights-windows-desktop.md), [app e servizi Web IIS se si installa Status Monitor](app-insights-monitor-performance-live-website-now.md) e [servizi cloud di Azure](app-insights-azure.md). Sono disponibili in Impostazioni > Server.

I dati non sono disponibili per i siti Web di Azure.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Dati del server non presenti dopo la pubblicazione dell'app nel server
* Verificare di aver effettivamente copiato tutti i Microsoft. DLL ApplicationInsights al server, insieme a Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
* Nel firewall potrebbe essere necessario [aprire alcune porte TCP](app-insights-ip-addresses.md#data-access-api).
* Se è necessario usare un proxy per inviare all'esterno della rete aziendale, impostare [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) in Web.config
* Windows Server 2008: assicurarsi che siano stati installati i seguenti aggiornamenti: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Non vengono più visualizzati i dati disponibili in precedenza
* Controllare il [blog sullo stato](http://blogs.msdn.com/b/applicationinsights-status/).
* È stata raggiunta la quota mensile relativa ai punti dati? Per saperlo, aprire Impostazioni/Quota e Prezzi. Se la quota è stata raggiunta, è possibile aggiornare il piano oppure pagare per disporre di ulteriore capacità. Vedere lo [schema dei prezzi](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Non sono presenti tutti i dati previsti
Se l'applicazione invia una grande quantità di dati ed è in uso Application Insights SDK per ASP.NET 2.0.0 Beta3 o versione successiva, la funzionalità di [campionamento adattivo](app-insights-sampling.md) può funzionare e inviare solo una percentuale dei dati di telemetria. 

È possibile disabilitarla, ma non è consigliabile. Il campionamento è progettato in modo che i dati di telemetria correlati vengano trasmessi correttamente per scopi diagnostici. 

## <a name="wrong-geographical-data-in-user-telemetry"></a>Dati geografici errati nella telemetria utente
Le dimensioni relative alla città, all'area, al paese e alla regione vengono derivate dagli indirizzi IP e non sono sempre accurate.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Eccezione "metodo non trovato" durante l'esecuzione dei servizi cloud di Azure
È stata eseguita la compilazione per .NET 4.6? La versione 4.6 non è supportata automaticamente nei ruoli dei servizi cloud di Azure. [Installare la versione 4.6 in ogni ruolo](../cloud-services/cloud-services-dotnet-install-dotnet.md) prima di eseguire l'app.

## <a name="still-not-working"></a>Non funzionante...
* [Forum di Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)




<!--HONumber=Nov16_HO3-->


