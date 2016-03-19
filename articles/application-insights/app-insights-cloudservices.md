<properties
   pageTitle="Application Insights per Servizi cloud di Azure"
   description="Monitorare i ruoli Web e di lavoro in modo efficace con Application Insights"
   services="application-insights"
   documentationCenter=""
   authors="soubhagyadash"
   manager="douge"
   editor="alancameronwills"/>

<tags
   ms.service="application-insights"
   ms.devlang="na"
   ms.tgt_pltfrm="ibiza"
   ms.topic="article"
   ms.workload="tbd"
   ms.date="11/15/2015"
   ms.author="sdash"/>

# Application Insights per Servizi cloud di Azure


*Application Insights è disponibile in anteprima*

Le [app del servizio Cloud di Microsoft Azure](https://azure.microsoft.com/services/cloud-services/) possono essere monitorate da [Visual Studio Application Insights][start] in termini di disponibilità, prestazioni, errori e utilizzo. Con il feedback ottenuto sulle prestazioni e sull'efficacia dell'app in circostanze normali, è possibile prendere decisioni informate sulla direzione della progettazione in ogni ciclo di vita di sviluppo.

![Esempio](./media/app-insights-cloudservices/sample.png)

È necessaria una sottoscrizione con [Microsoft Azure](http://azure.com). È possibile accedere con un account Microsoft, che in genere si ottiene per Windows, XBox Live o altri servizi cloud Microsoft.


#### Applicazione di esempio instrumentata con Application Insights

Esaminare questa [applicazione di esempio](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) in cui Application Insights viene aggiunto a un servizio cloud con due ruoli di lavoro ospitati in Azure.

Di seguito viene illustrato come adattare nello stesso modo il proprio progetto di servizio cloud.

## Creare una risorsa di Application Insights per ogni ruolo

Una risorsa di Application Insights rappresenta il punto in cui vengono analizzati e visualizzati i dati di telemetria.

1.  Nel [portale di Azure][portal] creare una nuova risorsa di Application Insights. Scegliere l'app ASP.NET per il tipo di applicazione. 

    ![Fare clic su Nuovo, Application Insights](./media/app-insights-cloudservices/01-new.png)

2.  Eseguire una copia della chiave di strumentazione. Sarà necessaria per configurare l'SDK.

    ![Fare clic su Proprietà, selezionare il tasto e premere CTRL+C](./media/app-insights-cloudservices/02-props.png)


È in genere preferibile creare una risorsa separata per i dati da ogni ruolo Web e di lavoro.

In alternativa, è possibile inviare dati da tutti i ruoli a una sola risorsa, ma impostare una [proprietà predefinita][apidefaults] in modo da potere filtrare o raggruppare i risultati di ogni ruolo.

## <a name="sdk"></a>Installare l'SDK in ogni progetto


1. In Visual Studio modificare i pacchetti NuGet del progetto dell'app per cloud.

    ![Fare clic con il pulsante destro del mouse sul progetto e selezionare Gestisci pacchetti NuGet](./media/app-insights-cloudservices/03-nuget.png)


2. Aggiungere il pacchetto NuGet [Application Insights per Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web). Questa versione dell'SDK include i moduli che aggiungono il contesto del server come le informazioni sui ruoli. Per i ruoli di lavoro, utilizzare Application Insights per i servizi di Windows.

    ![Cercare "Application Insights"](./media/app-insights-cloudservices/04-ai-nuget.png)


3. Configurare l'SDK per inviare i dati alla risorsa di Application Insights.

    Impostare la chiave di strumentazione di un'impostazione di configurazione nel file `ServiceConfiguration.Cloud.cscfg`. ([Codice di esempio](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg)).
 
    ```XML
     <Role name="WorkerRoleA"> 
      <Setting name="APPINSIGHTS_INSTRUMENTATIONKEY" value="YOUR IKEY" /> 
     </Role>
    ```
 
    In una funzione di avvio appropriato, impostare la chiave di strumentazione dall'impostazione di configurazione:

    ```C#
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```

    Si noti che lo stesso nome `APPINSIGHTS_INSTRUMENTATIONKEY` dell'impostazione di configurazione verrà usato dalla creazione di report di Diagnostica di Azure.


    Eseguire questa operazione per ogni ruolo nell'applicazione. Vedere gli esempi:
 
 * [Ruolo Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
 * [Ruolo di lavoro](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
 * [Per pagine Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13)   

4. Impostare il file ApplicationInsights.config da copiare sempre nella directory di output. 

    (Nel file config, ci sono dei messaggi in cui si chiede di inserire la chiave di strumentazione. Tuttavia, per le applicazioni cloud è preferibile che venga impostata dal file cscfg. Assicura che il ruolo venga identificato correttamente nel portale.)


#### Eseguire e pubblicare l'app

Eseguire l'app e accedere ad Azure. Aprire le risorse di Application Insights create. Verranno visualizzati i singoli punti dati in [Cerca](app-insights-diagnostic-search.md) e i dati aggregati in [Esplora metriche](app-insights-metrics-explorer.md).

Aggiungere altri dati di telemetria (vedere le sezioni seguenti) e quindi pubblicare l'app per ottenere commenti e suggerimenti in diretta sull'utilizzo e la diagnostica.


#### Dati non visualizzati

* Aprire il riquadro [Ricerca][diagnostic] per visualizzare i singoli eventi.
* Usare l'applicazione, aprendo pagine diverse in modo da generare alcuni dati di telemetria.
* Attendere alcuni secondi e fare clic su Aggiorna.
* Vedere [Risoluzione dei problemi][qna].



## Altri dati di telemetria

Le sezioni seguenti illustrano come ottenere altri dati di telemetria da aspetti diversi dell'applicazione.


## Tenere traccia delle richieste dai ruoli di lavoro

Nei ruoli Web il modulo delle richieste raccoglie automaticamente i dati sulle richieste HTTP. Vedere il progetto [MVCWebRole di esempio](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole) per alcuni esempi di come è possibile eseguire l'override del comportamento di raccolta predefinito.

È possibile acquisire le prestazioni delle chiamate ai ruoli di lavoro tenendone traccia nello stesso modo in cui avviene per le richieste HTTP. In Application Insights, il tipo di telemetria per le richieste misura un'unità di lavoro lato server denominata che può essere programmata e può avere indipendentemente esito negativo o positivo. Mentre le richieste HTTP vengono acquisite automaticamente dall'SDK, è possibile inserire il proprio codice per tenere traccia delle richieste ai ruoli di lavoro.

Vedere i due ruoli di lavoro di esempio instrumentati per segnalare le richieste: [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA) e [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## Diagnostica Azure

I dati di [Diagnostica di Azure](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) includono eventi di gestione dei ruoli, contatori delle prestazioni e registri applicazioni. È possibile inviarli ad Application Insights per poterli visualizzare insieme al resto della telemetria, semplificando così la diagnosi dei problemi.

La diagnostica di Azure è utile soprattutto se un ruolo smette di rispondere inaspettatamente o non si riesce ad avviarlo.

1. Fare clic con il pulsante destro del mouse sul ruolo (non sul progetto) per aprirne le proprietà e selezionare **Abilita diagnostica**, **Invia i dati di diagnostica ad Application Insights**.

    ![Cercare "Application Insights"](./media/app-insights-cloudservices/21-wad.png)

    **In alternativa, se l'app è già pubblicata e in esecuzione**, aprire Esplora server o Cloud Explorer, fare clic con il pulsante destro del mouse sull'app e scegliere la stessa opzione.

3.  Selezionare la stessa risorsa di Application Insights come altro tipo di telemetria.

    Se necessario, è possibile impostare una risorsa diversa in ogni configurazione del servizio (cloud, locale) per tenere i dati di sviluppo separati dai dati in diretta.

3. Facoltativamente, [escludere alcuni dati di diagnostica di Azure](app-insights-azure-diagnostics.md) che si vuole inoltrare ad Application Insights. L'impostazione predefinita include tutti i dati.

### Visualizzare gli eventi di diagnostica Azure

Dove trovare la diagnostica:

* I contatori delle prestazioni vengono visualizzati come metriche personalizzate. 
* I registri eventi di Windows vengono visualizzati come tracce ed eventi personalizzati.
* I registri applicazioni, i log ETW e gli eventuali log dell'infrastruttura di diagnostica vengono visualizzati come tracce.

Per visualizzare i contatori delle prestazioni e i conteggi degli eventi, aprire [Esplora metriche](app-insights-metrics-explorer.md) e aggiungere un nuovo grafico:


![](./media/app-insights-cloudservices/23-wad.png)

Usare [Cerca](app-insights-diagnostic-search.md) per eseguire ricerche nei vari log di traccia inviati da Diagnostica di Azure. Ad esempio, se è presente un'eccezione non gestita in un ruolo che ha causato l'arresto e il riciclo del ruolo, tali informazioni verranno visualizzate nel canale Applicazione del registro eventi di Windows. È possibile usare la funzionalità di ricerca per esaminare l'errore del registro eventi di Windows e l'analisi dello stack completa per l'eccezione. In questo modo sarà possibile trovare la causa radice del problema.


![](./media/app-insights-cloudservices/25-wad.png)

## Diagnostica dell'app

La diagnostica di Azure include automaticamente le voci di log generate dall'app con System.Diagnostics.Trace.

Ma, se si usano già i framework Log4N o NLog, è anche possibile [acquisirne le tracce dei log][netlogs].

[Tenere traccia di eventi personalizzati e metriche][api] nel client, nel server o in entrambi per altre informazioni sulle prestazioni e sull'utilizzo dell'applicazione.

## Dipendenze

Application Insights SDK può segnalare le chiamate eseguite dall'app alle dipendenze esterne, ad esempio le API REST e le istanze di SQL Server. Ciò consente di vedere se una specifica dipendenza sta causando ritardi nelle risposte o errori.

Per tenere traccia delle dipendenze, è necessario impostare il ruolo Web o di lavoro con l'[agente di Application Insights](app-insights-monitor-performance-live-website-now.md), noto anche come "Status Monitor".

Per usare l'agente di Application Insights con i ruoli Web/di lavoro:

* Aggiungere la cartella [AppInsightsAgent](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent) e i due file in essa contenuti ai progetti di ruolo Web/di lavoro. Assicurarsi di impostare le proprietà di compilazione in modo che vengano sempre copiate nella directory di output. Questi file installeranno l'agente.
* Aggiungere le attività di avvio per il file CSDEF, come illustrato [qui](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/AzureEmailService/ServiceDefinition.csdef#L18).
* NOTA: i *ruoli di lavoro* richiedono tre variabili di ambiente, come illustrato [qui](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/AzureEmailService/ServiceDefinition.csdef#L44). Questa operazione non è necessaria per i ruoli Web.

Di seguito è illustrato un esempio di ciò che viene visualizzato nel portale di Application Insights:

* Diagnostica avanzata con le richieste e le dipendenze correlate automaticamente:

    ![](./media/app-insights-cloudservices/SMxacy4.png)

* Prestazioni del ruolo Web, con informazioni sulle dipendenze:

    ![](./media/app-insights-cloudservices/6yOBtKu.png)

* Di seguito è illustrata una schermata relativa alle richieste e alle informazioni sulle dipendenze per un ruolo di lavoro:

    ![](./media/app-insights-cloudservices/a5R0PBk.png)

## Eccezioni

Vedere [Monitoraggio delle eccezioni in Application Insights](app-insights-asp-net-exceptions.md) per informazioni su come è possibile raccogliere le eccezioni non gestite da diversi tipi di applicazioni Web.

Il ruolo Web di esempio contiene i controller MVC5 e Web API 2. Le eccezioni non gestite da questi controller vengono acquisite con gli elementi seguenti:

* [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) impostato [qui](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) per i controller MVC5
* [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) impostato [qui](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) per i controller Web API 2

Per i ruoli di lavoro, esistono due modi per tenere traccia delle eccezioni.

* TrackException(ex)
* Se è stato aggiunto il pacchetto NuGet del listener di traccia di Application Insights, è possibile usare System.Diagnostics.Trace per registrare le eccezioni. [Esempio di codice.](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)

## Contatori delle prestazioni

Per impostazione predefinita, vengono raccolti i contatori seguenti:

    * \Process(??APP_WIN32_PROC??)\% Processor Time
	* \Memory\Available Bytes
	* \.NET CLR Exceptions(??APP_CLR_PROC??)# of Exceps Thrown / sec
	* \Process(??APP_WIN32_PROC??)\Private Bytes
	* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
	* \Processor(_Total)\% Processor Time

Inoltre, per i ruoli Web vengono raccolti anche i contatori seguenti:

	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec	
	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

È possibile specificare altri contatori delle prestazioni personalizzati o di Windows, come illustrato [qui](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![](./media/app-insights-cloudservices/OLfMo2f.png)

## Telemetria correlata per i ruoli di lavoro

Per riuscire a individuare la causa di una richiesta non riuscita o con latenza elevata occorrono strumenti di diagnostica avanzati. Con i ruoli Web, l'SDK configura automaticamente la correlazione tra i dati di telemetria correlati. Per ottenere lo stesso risultato con i ruoli di lavoro, è possibile usare un inizializzatore di telemetria personalizzato per impostare un attributo di contesto Operation.Id comune per tutti i dati di telemetria. Ciò consentirà di vedere immediatamente se il problema di errore/latenza è stato causato da una dipendenza o dal codice.

Ecco come:

* Impostare l'ID correlazione in un oggetto CallContext come mostrato [qui](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). In questo caso, viene usato l'ID richiesta come ID correlazione.
* Aggiungere un'implementazione personalizzata di TelemetryInitializer, che imposterà l'oggetto Operation.Id sull'oggetto correlationId impostato in precedenza. Vedere qui: [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13)
* Aggiungere l'inizializzatore di telemetria personalizzato. È possibile eseguire questa operazione nel file ApplicationInsights.config o nel codice come illustrato [qui](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233).

L'operazione è terminata. Le funzionalità del portale sono già collegate per poter visualizzare tutti i dati di telemetria associati:

![](./media/app-insights-cloudservices/bHxuUhd.png)



## Telemetria client

[Aggiungere l'SDK per JavaScript alle pagine Web][client] per ottenere dati di telemetria basati su browser quali i conteggi delle visualizzazioni delle pagine, i tempi di caricamento delle pagina e le eccezioni di script e per consentire la scrittura dei dati di telemetria negli script delle pagine.

## Test della disponibilità

[Configurare i test Web][availability] in modo da assicurarsi che l'applicazione sia disponibile e reattiva.



## Esempio

[L'esempio](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) monitora un servizio che ha un ruolo Web e due ruoli di lavoro.

## Eccezione "metodo non trovato" durante l'esecuzione dei servizi cloud di Azure

È stata eseguita la compilazione per .NET 4.6? La versione 4.6 non è supportata automaticamente nei ruoli dei servizi cloud di Azure. [Installare la versione 4.6 in ogni ruolo](../cloud-services/cloud-services-dotnet-install-dotnet.md) prima di eseguire l'app.

## Argomenti correlati

* [Configurare l'invio di dati di Diagnostica di Azure ad Application Insights](app-insights-azure-diagnostics.md)
* [Uso di PowerShell per l'invio dei dati del servizio Diagnostica di Azure ad Application Insights](app-insights-powershell-azure-diagnostics.md)



[api]: app-insights-api-custom-events-metrics.md
[apidefaults]: app-insights-api-custom-events-metrics.md#default-properties
[apidynamicikey]: app-insights-api-custom-events-metrics.md#dynamic-ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: app-insights-azure.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

<!---HONumber=AcomDC_0224_2016-->