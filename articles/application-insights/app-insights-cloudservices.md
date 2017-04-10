---
title: Application Insights per Servizi cloud di Azure | Microsoft Docs
description: Monitorare i ruoli Web e di lavoro in modo efficace con Application Insights
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
editor: alancameronwills
ms.assetid: 5c7a5b34-329e-42b7-9330-9dcbb9ff1f88
ms.service: application-insights
ms.devlang: na
ms.tgt_pltfrm: ibiza
ms.topic: get-started-article
ms.workload: tbd
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: 5ea258a8c790dd94e019243e77f2ff694c2515d7
ms.lasthandoff: 03/16/2017


---
# <a name="application-insights-for-azure-cloud-services"></a>Application Insights per Servizi cloud di Azure
Le [app dei servizi cloud di Microsoft Azure](https://azure.microsoft.com/services/cloud-services/) possono essere monitorate da [Application Insights][start] in termini di disponibilità, prestazioni, errori e uso. Con il feedback ottenuto sulle prestazioni e sull'efficacia dell'app in circostanze normali, è possibile prendere decisioni informate sulla direzione della progettazione in ogni ciclo di vita di sviluppo.

![Esempio](./media/app-insights-cloudservices/sample.png)

## <a name="before-you-start"></a>Prima di iniziare
Sono necessari gli elementi seguenti:

* Una sottoscrizione con [Microsoft Azure](http://azure.com). È possibile accedere con un account Microsoft, che in genere si ottiene per Windows, XBox Live o altri servizi cloud Microsoft. 
* Strumenti di Microsoft Azure 2.9 o versione successiva
* Developer Analytics Tools 7.10 o versione successiva

## <a name="quick-start"></a>Avvio rapido
Il modo più rapido e semplice per monitorare il servizio cloud con Application Insights è scegliere questa opzione quando si pubblica il servizio in Azure.

![Esempio](./media/app-insights-cloudservices/azure-cloud-application-insights.png)

Questa opzione instrumenta l'app in fase di esecuzione, fornendo tutti i dati di telemetria necessari per monitorare richieste, eccezioni e dipendenze nel ruolo Web, oltre ai contatori delle prestazioni dai ruoli di lavoro. Eventuali tracce diagnostiche generate dall'app verranno inviate anche ad Application Insights.

Se non si hanno altre esigenze, non è necessario eseguire altre operazioni. I passaggi successivi prevedono la [visualizzazione delle metriche dall'app](app-insights-metrics-explorer.md), la [query dei dati con Analytics](app-insights-analytics.md) ed eventualmente la configurazione di un [dashboard](app-insights-dashboards.md). Potrebbe essere necessario configurare [test di disponibilità](app-insights-monitor-web-app-availability.md) e [aggiungere codice alle pagine Web](app-insights-javascript.md) per monitorare le prestazioni nel browser.

Sono disponibili anche altre opzioni:

* Inviare dati da componenti diversi e creare configurazioni per separare le risorse.
* Aggiungere dati di telemetria personalizzati dall'app.

Queste opzioni vengono illustrate nella parte seguente di questo articolo.

## <a name="sample-application-instrumented-with-application-insights"></a>Applicazione di esempio instrumentata con Application Insights
Esaminare questa [applicazione di esempio](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) in cui Application Insights viene aggiunto a un servizio cloud con due ruoli di lavoro ospitati in Azure. 

Di seguito viene illustrato come adattare nello stesso modo il proprio progetto di servizio cloud.

## <a name="plan-resources-and-resource-groups"></a>Pianificare le risorse e i gruppi di risorse
I dati di telemetria provenienti dall'app vengono archiviati, analizzati e visualizzati in una risorsa di Azure di tipo Application Insights. 

Ogni risorsa appartiene a un gruppo di risorse. I gruppi di risorse vengono usati per gestire i costi, concedere l'accesso ai membri di un team e distribuire gli aggiornamenti in una singola transazione coordinata. Ad esempio, è possibile [creare uno script per distribuire](../azure-resource-manager/resource-group-template-deploy.md) un servizio cloud di Azure e le risorse per il monitoraggio con Application Insights in un'unica operazione.

### <a name="resources-for-components"></a>Risorse per i componenti
Lo schema consigliato prevede la creazione di una risorsa separata per ogni componente dell'applicazione, ovvero per ogni ruolo Web e ruolo di lavoro. È possibile analizzare ogni componente separatamente, ma anche creare un [dashboard](app-insights-dashboards.md) che riunisca i grafici chiave per tutti i componenti, per poterli confrontare e monitorare tutti insieme. 

Uno schema alternativo prevede l'invio dei dati di telemetria da più di un ruolo alla stessa risorsa, ma anche l'[aggiunta di una proprietà delle dimensioni a ogni elemento della telemetria](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer) per identificare il ruolo di origine. In questo schema i grafici delle metriche, ad esempio le eccezioni, visualizzano in genere un'aggregazione dei conteggi dei diversi ruoli ma, se necessario, è possibile segmentare il grafico in base all'identificatore di ruolo. Le ricerche possono essere filtrate anche in base alla stessa dimensione. Questa alternativa può rendere un po' più semplice visualizzare tutti gli elementi insieme, ma può anche creare confusione tra i ruoli.

I dati di telemetria del browser in genere vengono inclusi nella stessa risorsa come ruolo Web lato server.

Inserire le risorse di Application Insights per i diversi componenti in un unico gruppo di risorse, per poterli gestire facilmente insieme. 

### <a name="separating-development-test-and-production"></a>Separazione di sviluppo, test e produzione
Se si sviluppano eventi personalizzati per la funzionalità successiva mentre è live la versione precedente, è opportuno inviare i dati di telemetria dello sviluppo a una risorsa di Application Insights separata. In caso contrario sarà difficile trovare i dati di telemetria di test in mezzo a tutto il traffico del sito live.

Per evitare questa situazione, creare risorse separate per ogni configurazione della build o "timbro" (sviluppo, test, produzione...) del sistema. Inserire le risorse per ogni configurazione della build in un gruppo di risorse separato. 

Per inviare i dati di telemetria alle risorse appropriate, è possibile configurare Application Insights SDK in modo che selezioni una chiave di strumentazione diversa a seconda della configurazione della build. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Creare una risorsa di Application Insights per ogni ruolo
Se si è deciso di creare una risorsa separata per ogni ruolo, ed eventualmente un set separato per ogni configurazione della build, il modo più semplice è crearle tutte insieme nel portale di Application Insights. Se si creano molte risorse, è possibile [automatizzare il processo](app-insights-powershell.md).

1. Nel [portale di Azure][portal] creare una nuova risorsa di Application Insights. Scegliere l'app ASP.NET per il tipo di applicazione. 

    ![Fare clic su Nuovo, Application Insights](./media/app-insights-cloudservices/01-new.png)
2. Si noti che ogni risorsa viene identificata da una chiave di strumentazione che potrebbe essere necessaria in seguito per configurare manualmente o verificare la configurazione dell'SDK.

    ![Fare clic su Proprietà, selezionare il tasto e premere CTRL+C](./media/app-insights-cloudservices/02-props.png) 

## <a name="set-up-azure-diagnostics-for-each-role"></a>Configurare Diagnostica di Azure per ogni ruolo
Impostare questa opzione per monitorare l'app con Application Insights. Per i ruoli Web offre monitoraggio delle prestazioni, avvisi e diagnostica, oltre all'analisi dell'utilizzo. Per gli altri ruoli, è possibile cercare e monitorare i dati di diagnostica di Azure, ad esempio riavvio, contatori delle prestazioni e chiamate a System.Diagnostics.Trace. 

1. In Esplora soluzioni di Visual Studio aprire le proprietà di ogni ruolo in &lt;ServizioCloud&gt;, Ruoli.
2. In **Configurazione** impostare **Invia i dati di diagnostica ad Application Insights** e selezionare la risorsa di Application Insights appropriata creata in precedenza.

Se si è deciso di usare una risorsa di Application Insights separata per ogni configurazione della build, selezionare prima la configurazione.

![Nelle proprietà di ogni ruolo di Azure configurare Application Insights](./media/app-insights-cloudservices/configure-azure-diagnostics.png)

Le chiavi di strumentazione di Application Insights verranno così inserite nei file denominati `ServiceConfiguration.*.cscfg`. ([Codice di esempio](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg)).

Per variare il livello delle informazioni diagnostiche inviate ad Application Insights, [modificare direttamente i file `.cscfg`](app-insights-azure-diagnostics.md).

## <a name="sdk"></a>Installare l'SDK in ogni progetto
Questa opzione consente di aggiungere dati di telemetria aziendali personalizzati a qualsiasi ruolo, per un'analisi più approfondita dell'uso e delle prestazioni dell'applicazione.

In Visual Studio configurare Application Insights SDK per ogni progetto di app cloud.

1. **Ruoli Web**: fare clic con il pulsante destro del mouse sul progetto e scegliere **Configura Application Insights** o **Aggiungi > Application Insights Telemetry**.

2. **Ruoli di lavoro**: 
 * Fare clic con il pulsante destro del mouse sul progetto e selezionare **Gestisci pacchetti NuGet**.
 * Aggiungere [Application Insights per server Windows](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

    ![Cercare "Application Insights"](./media/app-insights-cloudservices/04-ai-nuget.png)

3. Configurare l'SDK per inviare i dati alla risorsa di Application Insights.

    In una funzione di avvio appropriata impostare la chiave di strumentazione dall'impostazione di configurazione nel file CSCFG:
 
    ```C#
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    Eseguire questa operazione per ogni ruolo nell'applicazione. Vedere gli esempi:
   
   * [Ruolo Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
   * [Ruolo di lavoro](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
   * [Per pagine Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 
4. Impostare il file ApplicationInsights.config da copiare sempre nella directory di output. 
   
    (Nel file config, ci sono dei messaggi in cui si chiede di inserire la chiave di strumentazione. Tuttavia, per le applicazioni cloud è preferibile che venga impostata dal file cscfg. Assicura che il ruolo venga identificato correttamente nel portale.)

#### <a name="run-and-publish-the-app"></a>Eseguire e pubblicare l'app
Eseguire l'app e accedere ad Azure. Aprire le risorse di Application Insights create. Verranno visualizzati i singoli punti dati in [Cerca](app-insights-diagnostic-search.md) e i dati aggregati in [Esplora metriche](app-insights-metrics-explorer.md). 

Aggiungere altri dati di telemetria (vedere le sezioni seguenti) e quindi pubblicare l'app per ottenere commenti e suggerimenti in diretta sull'utilizzo e la diagnostica. 

#### <a name="no-data"></a>Dati non visualizzati
* Aprire il riquadro [Ricerca][diagnostic] per visualizzare i singoli eventi.
* Usare l'applicazione, aprendo pagine diverse in modo da generare alcuni dati di telemetria.
* Attendere alcuni secondi e fare clic su Aggiorna.
* Vedere [Risoluzione dei problemi][qna].

## <a name="view-azure-diagnostic-events"></a>Visualizzare gli eventi di diagnostica Azure
Dove trovare la diagnostica:

* I contatori delle prestazioni vengono visualizzati come metriche personalizzate. 
* I registri eventi di Windows vengono visualizzati come tracce ed eventi personalizzati.
* I log applicazioni, i log ETW e gli eventuali log dell'infrastruttura di diagnostica vengono visualizzati come tracce.

Per visualizzare i contatori delle prestazioni e i conteggi degli eventi, aprire [Esplora metriche](app-insights-metrics-explorer.md) e aggiungere un nuovo grafico:

![Dati di diagnostica di Azure](./media/app-insights-cloudservices/23-wad.png)

Usare [Cerca](app-insights-diagnostic-search.md) o una [query di Analisi](app-insights-analytics-tour.md) per eseguire ricerche nei vari log di traccia inviati da Diagnostica di Azure. Supponendo, ad esempio, di avere un'eccezione non gestita che ha causato l'arresto e il riciclo di un ruolo, questa informazione verrebbe visualizzata nel canale Applicazione del registro eventi di Windows. È possibile usare la funzionalità di ricerca per esaminare l'errore del registro eventi di Windows e ottenere l'analisi dello stack completa per l'eccezione. In questo modo sarà possibile trovare la causa radice del problema.

![Ricerca di dati di diagnostica di Azure](./media/app-insights-cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Altri dati di telemetria
Le sezioni seguenti illustrano come ottenere altri dati di telemetria da aspetti diversi dell'applicazione.

## <a name="track-requests-from-worker-roles"></a>Tenere traccia delle richieste dai ruoli di lavoro
Nei ruoli Web il modulo delle richieste raccoglie automaticamente i dati sulle richieste HTTP. Vedere il progetto [MVCWebRole di esempio](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole) per alcuni esempi di come è possibile eseguire l'override del comportamento di raccolta predefinito. 

È possibile acquisire le prestazioni delle chiamate ai ruoli di lavoro tenendone traccia nello stesso modo in cui avviene per le richieste HTTP. In Application Insights il tipo di telemetria per le richieste misura un'unità di lavoro lato server denominata che può essere programmata e può avere indipendentemente esito positivo o negativo. Mentre le richieste HTTP vengono acquisite automaticamente dall'SDK, è possibile inserire il proprio codice per tenere traccia delle richieste ai ruoli di lavoro.

Vedere i due ruoli di lavoro di esempio instrumentati per segnalare le richieste: [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA) e [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Eccezioni
Vedere [Monitoraggio delle eccezioni in Application Insights](app-insights-asp-net-exceptions.md) per informazioni su come è possibile raccogliere le eccezioni non gestite da diversi tipi di applicazioni Web.

Il ruolo Web di esempio contiene i controller MVC5 e Web API 2. Le eccezioni non gestite da questi due controller vengono acquisite con i gestori di eventi seguenti:

* [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) impostato [qui](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) per i controller MVC5
* [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) impostato [qui](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) per i controller Web API 2

Per i ruoli di lavoro esistono due modi per tenere traccia delle eccezioni:

* TrackException(ex)
* Se è stato aggiunto il pacchetto NuGet del listener di traccia di Application Insights, è possibile usare **System.Diagnostics.Trace** per registrare le eccezioni. [Esempio di codice.](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)

## <a name="performance-counters"></a>Contatori delle prestazioni
Per impostazione predefinita, vengono raccolti i contatori seguenti:

    * \Process(??APP_WIN32_PROC??)\% Processor Time
    * \Memory\Available Bytes
    * \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
    * \Process(??APP_WIN32_PROC??)\Private Bytes
    * \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
    * \Processor(_Total)\% Processor Time

Per i ruoli Web vengono raccolti anche i contatori seguenti:

    * \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec
    * \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
    * \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

È possibile specificare altri contatori delle prestazioni personalizzati o di Windows modificando ApplicationInsights.config, [come illustrato in questo esempio](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![Contatori delle prestazioni](./media/app-insights-cloudservices/OLfMo2f.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Telemetria correlata per i ruoli di lavoro
Per riuscire a individuare la causa di una richiesta non riuscita o con latenza elevata occorrono strumenti di diagnostica avanzati. Con i ruoli Web, l'SDK configura automaticamente la correlazione tra i dati di telemetria correlati. Per ottenere lo stesso risultato con i ruoli di lavoro, è possibile usare un inizializzatore di telemetria personalizzato per impostare un attributo di contesto Operation.Id comune per tutti i dati di telemetria. In questo modo sarà possibile scoprire immediatamente se il problema di errore/latenza è stato causato da una dipendenza o dal codice. 

Ecco come:

* Impostare l'ID correlazione in un oggetto CallContext come mostrato [qui](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). In questo caso, viene usato l'ID richiesta come ID correlazione.
* Aggiungere un'implementazione personalizzata di TelemetryInitializer per impostare l'oggetto Operation.Id sull'oggetto correlationId impostato in precedenza. Per un esempio, vedere: [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13)
* Aggiungere l'inizializzatore di telemetria personalizzato. È possibile eseguire questa operazione nel file ApplicationInsights.config o nel codice come illustrato [qui](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233)

L'operazione è terminata. Le funzionalità del portale sono già collegate per poter visualizzare tutti i dati di telemetria associati:

![Dati di telemetria correlati](./media/app-insights-cloudservices/bHxuUhd.png)

## <a name="client-telemetry"></a>Telemetria client
[Aggiungere l'SDK per JavaScript alle pagine Web][client] per ottenere dati di telemetria basati su browser quali i conteggi delle visualizzazioni delle pagine, i tempi di caricamento delle pagine e le eccezioni di script e per consentire la scrittura dei dati di telemetria negli script delle pagine.

## <a name="availability-tests"></a>Test della disponibilità
[Configurare i test Web][availability] in modo da assicurarsi che l'applicazione sia disponibile e reattiva.

## <a name="display-everything-together"></a>Visualizzare tutti gli elementi contemporaneamente
Per ottenere una panoramica del sistema, è possibile riunire i grafici di monitoraggio principali in un unico [dashboard](app-insights-dashboards.md). Ad esempio, è possibile aggiungere i conteggi delle richieste e degli errori di ogni ruolo. 

Se il sistema usa altri servizi di Azure, ad esempio l'analisi di flusso, includere anche i relativi grafici di monitoraggio. 

Se si ha un'app per dispositivi mobili client, inserire un codice per inviare eventi personalizzati sulle principali operazioni degli utenti e creare un'istanza di [HockeyApp Bridge](app-insights-hockeyapp-bridge-app.md). Creare query in [Analytics](app-insights-analytics.md) per visualizzare i conteggi degli eventi e aggiungerli al dashboard.

## <a name="example"></a>Esempio
[L'esempio](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) monitora un servizio che ha un ruolo Web e due ruoli di lavoro.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Eccezione "metodo non trovato" durante l'esecuzione dei servizi cloud di Azure
È stata eseguita la compilazione per .NET 4.6? La versione 4.6 non è supportata automaticamente nei ruoli dei servizi cloud di Azure. [Installare la versione 4.6 in ogni ruolo](../cloud-services/cloud-services-dotnet-install-dotnet.md) prima di eseguire l'app.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Passaggi successivi
* [Configurare l'invio di dati di Diagnostica di Azure ad Application Insights](app-insights-azure-diagnostics.md)
* [Automatizzare la creazione di risorse di Application Insights](app-insights-powershell.md)
* [Automatizzare Diagnostica di Azure](app-insights-powershell-azure-diagnostics.md)

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[azure]: app-insights-azure.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[netlogs]: app-insights-asp-net-trace-logs.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md 

