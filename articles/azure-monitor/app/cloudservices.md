---
title: Application Insights per i servizi cloud di Azure | Microsoft Docs
description: Monitorare i ruoli Web e di lavoro in modo efficace con Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/05/2018
ms.openlocfilehash: 860694a750ae313f04aceab924429dcf08ecbb66
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73887548"
---
# <a name="application-insights-for-azure-cloud-services"></a>Application Insights per i servizi cloud di Azure
[Application Insights][start] possibile monitorare le [app del servizio cloud di Azure](https://azure.microsoft.com/services/cloud-services/) per disponibilità, prestazioni, errori e utilizzo combinando i dati di Application Insights SDK con [diagnostica di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) i dati dei servizi cloud. Con il feedback ottenuto sulle prestazioni e sull'efficacia dell'app in circostanze normali, è possibile prendere decisioni informate sulla direzione della progettazione in ogni ciclo di vita di sviluppo.

![Dashboard panoramica](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, è necessario disporre di quanto segue:

* Una sottoscrizione di [Azure](https://azure.com). Accedere con l'account Microsoft per Windows, Xbox Live o altri servizi cloud Microsoft. 
* Strumenti di Microsoft Azure 2.9 o versione successiva.
* Developer Analytics Tools 7.10 o versione successiva.

## <a name="get-started-quickly"></a>Operatività immediata
Il modo più rapido e semplice per monitorare il servizio cloud con Application Insights è scegliere questa opzione quando si pubblica il servizio in Azure.

![Pagina Impostazioni di diagnostica di esempio](./media/cloudservices/azure-cloud-application-insights.png)

Questa opzione instrumenta l'app in fase di esecuzione, fornendo tutti i dati di telemetria necessari per monitorare richieste, eccezioni e dipendenze nel ruolo Web. Monitora inoltre i contatori delle prestazioni dai ruoli di lavoro. Eventuali tracce diagnostiche generate dall'app verranno inviate anche ad Application Insights.

Se non si hanno altre esigenze, non è necessario eseguire altre operazioni. 

I passaggi successivi sono la [visualizzazione delle metriche dall'app](../../azure-monitor/app/metrics-explorer.md)e [l'esecuzione di query sui dati con l'analisi](../../azure-monitor/app/analytics.md). 

Per monitorare le prestazioni nel browser, potrebbe essere necessario configurare [test di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md) e [aggiungere codice alle pagine Web](../../azure-monitor/app/javascript.md).

Le sezioni successive illustrano le opzioni aggiuntive seguenti:

* Inviare dati da componenti diversi e creare configurazioni per separare le risorse.
* Aggiungere dati di telemetria personalizzati dall'app.

## <a name="sample-app-instrumented-with-application-insights"></a>App di esempio instrumentata con Application Insights
In questa [app di esempio](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) Application Insights viene aggiunto a un servizio cloud con due ruoli di lavoro ospitati in Azure. 

Nella sezione successiva viene illustrato come adattare nello stesso modo il proprio progetto di servizio cloud.

## <a name="plan-resources-and-resource-groups"></a>Pianificare le risorse e i gruppi di risorse
I dati di telemetria provenienti dall'app vengono archiviati, analizzati e visualizzati in una risorsa di Azure di tipo Application Insights. 

Ogni risorsa appartiene a un gruppo di risorse. I gruppi di risorse vengono usati per gestire i costi, concedere l'accesso ai membri di un team e distribuire gli aggiornamenti in una singola transazione coordinata. Ad esempio, è possibile [creare uno script per distribuire](../../azure-resource-manager/resource-group-template-deploy.md) un servizio cloud di Azure e le risorse per il monitoraggio con Application Insights in un'unica operazione.

### <a name="resources-for-components"></a>Risorse per i componenti
È consigliabile creare una risorsa separata per ogni componente dell'app, ovvero per ogni ruolo Web e ruolo di lavoro. È possibile analizzare ogni componente separatamente, ma anche creare un [dashboard](../../azure-monitor/app/overview-dashboard.md) che riunisca i grafici chiave per tutti i componenti, per poterli confrontare e monitorare tutti insieme in un'unica visualizzazione. 

Un approccio alternativo prevede l'invio dei dati di telemetria da più di un ruolo alla stessa risorsa, ma anche l'[aggiunta di una proprietà delle dimensioni a ogni elemento della telemetria](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) per identificare il ruolo di origine. In questo approccio i grafici delle metriche, ad esempio le eccezioni, visualizzano in genere un'aggregazione dei conteggi dei diversi ruoli ma, in base alle esigenze, è possibile segmentare il grafico in base all'identificatore di ruolo. Le ricerche possono essere filtrate anche in base alla stessa dimensione. Questa alternativa può rendere un po' più semplice visualizzare tutti gli elementi insieme, ma può anche creare confusione tra i ruoli.

I dati di telemetria del browser in genere vengono inclusi nella stessa risorsa come ruolo Web lato server.

Inserire le risorse di Application Insights per i diversi componenti in un unico gruppo di risorse. Questo approccio consente di gestirli facilmente insieme. 

### <a name="separate-development-test-and-production"></a>Separare sviluppo, test e produzione
Se si sviluppano eventi personalizzati per la funzionalità successiva mentre è live la versione precedente, è opportuno inviare i dati di telemetria dello sviluppo a una risorsa di Application Insights separata. In caso contrario potrebbe essere difficile trovare i dati di telemetria di test in mezzo a tutto il traffico del sito live.

Per evitare questa situazione, creare risorse separate per ogni configurazione della build o "timbro" (sviluppo, test, produzione e così via) del sistema. Inserire le risorse per ogni configurazione della build in un gruppo di risorse separato. 

Per inviare i dati di telemetria alle risorse appropriate, è possibile configurare Application Insights SDK in modo che selezioni una chiave di strumentazione diversa a seconda della configurazione della build. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Creare una risorsa di Application Insights per ogni ruolo

Se si è deciso di creare una risorsa separata per ogni ruolo, ed eventualmente un set separato per ogni configurazione della build, il modo più semplice è crearle tutte insieme nel portale di Application Insights. Se si creano molte risorse, è possibile [automatizzare il processo](../../azure-monitor/app/powershell.md).

1. Nella [portale di Azure][portal]selezionare **nuovo** > servizi per gli **sviluppatori** > **Application Insights**.  

    ![Riquadro di Application Insights](./media/cloudservices/01-new.png)

1. Nell'elenco a discesa **Tipo di applicazione** selezionare **Applicazione Web ASP.NET**.

Ogni risorsa viene identificata da una chiave di strumentazione che potrebbe essere necessaria in seguito per configurare manualmente o verificare la configurazione dell'SDK.


## <a name="set-up-azure-diagnostics-for-each-role"></a>Configurare Diagnostica di Azure per ogni ruolo
Impostare questa opzione per monitorare l'app con Application Insights. Per i ruoli Web, questa opzione offre monitoraggio delle prestazioni, avvisi, diagnostica e analisi dell'utilizzo. Per gli altri ruoli, è possibile cercare e monitorare i dati di Diagnostica di Azure, ad esempio riavvio, contatori delle prestazioni e chiamate a System.Diagnostics.Trace. 

1. In Esplora soluzioni di Visual Studio aprire le proprietà di ogni ruolo in **\<ServizioCloud>**  > **Ruoli**.

1. In **Configurazione** selezionare la casella di controllo **Invia i dati di diagnostica ad Application Insights** e selezionare la risorsa di Application Insights creata in precedenza.

Se si è deciso di usare una risorsa di Application Insights separata per ogni configurazione della build, selezionare prima la configurazione.

![Configura Application Insights](./media/cloudservices/configure-azure-diagnostics.png)

Le chiavi di strumentazione di Application Insights verranno così inserite nei file denominati *ServiceConfiguration.\*.cscfg*. Di seguito è riportato il [codice di esempio](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg).

Per variare il livello delle informazioni diagnostiche inviate ad Application Insights, [modificare direttamente i file *CSCFG*](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md).

## <a name="sdk"></a>Installare l'SDK in ogni progetto
Con questa opzione è possibile aggiungere dati di telemetria business personalizzati a qualsiasi ruolo. L'opzione fornisce un'analisi più accurata dell'utilizzo e delle prestazioni dell'app.

In Visual Studio configurare Application Insights SDK per ogni progetto di app cloud.

1. Per configurare i **ruoli Web**, fare clic con il pulsante destro del mouse sul progetto e selezionare **Configura Application Insights** o **Aggiungi > Application Insights Telemetry**.

1. Per configurare i **ruoli di lavoro**: 

    a. Fare clic con il pulsante destro del mouse sul progetto e selezionare **Gestisci pacchetti NuGet**.

    b. Aggiungere [Application Insights per server Windows](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

    ![Cercare "Application Insights"](./media/cloudservices/04-ai-nuget.png)

1. Per configurare l'SDK per inviare i dati alla risorsa di Application Insights:

    a. In una funzione di avvio appropriata impostare la chiave di strumentazione dall'impostazione di configurazione nel file *CSCFG*:
 
    ```csharp
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    b. Ripetere il "passaggio a" per ogni ruolo nell'app. Vedere gli esempi:
   
    * [Ruolo Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [Ruolo di lavoro](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [Per le pagine Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. Impostare il file *ApplicationInsights.config* da copiare sempre nella directory di output.

   Un messaggio nel file *CONFIG* chiede di inserire la chiave di strumentazione. Tuttavia, per le app cloud è preferibile che venga impostata dal file *CSCFG*. Questo approccio assicura che il ruolo venga identificato correttamente nel portale.

## <a name="set-up-status-monitor-to-collect-full-sql-queries-optional"></a>Configurare Status Monitor per la raccolta di query SQL complete (facoltativo)

Questo passaggio è necessario solo se si desidera acquisire query SQL complete su .NET Framework. 

1. In `\*.csdef` file Aggiungi [attività di avvio](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks) per ogni ruolo simile a 

    ```xml
    <Startup>
      <Task commandLine="AppInsightsAgent\InstallAgent.bat" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="ApplicationInsightsAgent.DownloadLink" value="http://go.microsoft.com/fwlink/?LinkID=522371" />
          <Variable name="RoleEnvironment.IsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
    
2. Scaricare [InstallAgent. bat](https://github.com/microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.bat) e [InstallAgent. ps1](https://github.com/microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.ps1)e inserirli nella cartella `AppInsightsAgent` in ogni progetto di ruolo. Assicurarsi di copiarli nella directory di output tramite le proprietà dei file di Visual Studio o gli script di compilazione.

3. In tutti i ruoli di lavoro aggiungere le variabili di ambiente: 

    ```xml
      <Environment>
        <Variable name="COR_ENABLE_PROFILING" value="1" />
        <Variable name="COR_PROFILER" value="{324F817A-7420-4E6D-B3C1-143FBED6D855}" />
        <Variable name="MicrosoftInstrumentationEngine_Host" value="{CA487940-57D2-10BF-11B2-A3AD5A13CBC0}" />
      </Environment>
    ```
    
## <a name="run-and-publish-the-app"></a>Eseguire e pubblicare l'app

1. Eseguire l'app e accedere ad Azure. 

1. Aprire le risorse di Application Insights create.

   Verranno visualizzati i singoli punti dati in [Cerca][diagnostic] e i dati aggregati in [Esplora metriche](../../azure-monitor/app/metrics-explorer.md).

1. Aggiungere altri dati di telemetria (vedere le sezioni successive) e quindi pubblicare l'app per ottenere commenti e suggerimenti in diretta sull'utilizzo e la diagnostica. 

Se non sono presenti dati, eseguire le operazioni seguenti:

1. Per visualizzare i singoli eventi, aprire il riquadro di [ricerca][diagnostic] .
1. Aprire pagine diverse nell'app in modo da generare alcuni dati di telemetria.
1. Attendere alcuni secondi e fare clic su **Aggiorna**.  

Per altre informazioni, vedere [Risoluzione dei problemi][qna].

## <a name="view-azure-diagnostics-events"></a>Visualizzare gli eventi di Diagnostica di Azure
Le informazioni di [Diagnostica di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) sono disponibili in Application Insights nelle posizioni seguenti:

* I contatori delle prestazioni vengono visualizzati come metriche personalizzate. 
* I log eventi di Windows vengono visualizzati come tracce ed eventi personalizzati.
* I log applicazioni, i log ETW e gli eventuali log dell'infrastruttura di diagnostica vengono visualizzati come tracce.

Per visualizzare i contatori delle prestazioni e i conteggi degli eventi, aprire [Esplora metriche](../../azure-monitor/app/metrics-explorer.md) e aggiungere il grafico seguente:

![Dati di Diagnostica di Azure](./media/cloudservices/23-wad.png)

Per eseguire ricerche nei vari log di traccia inviati da Diagnostica di Azure, usare la [Ricerca](../../azure-monitor/app/diagnostic-search.md) o una [Query Analytics](../../azure-monitor/log-query/get-started-portal.md). Supponendo, ad esempio, di avere un'eccezione non gestita che ha causato l'arresto e il riciclo di un ruolo, questa informazione verrebbe visualizzata nel canale Applicazione del registro eventi di Windows. È possibile usare la funzionalità di ricerca per visualizzare l'errore del registro eventi di Windows e ottenere l'analisi dello stack completa per l'eccezione. In questo modo sarà possibile trovare la causa radice del problema.

![Ricerca di Diagnostica di Azure](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Altri dati di telemetria
Le sezioni successive illustrano come ottenere altri dati di telemetria da aspetti diversi dell'app.

## <a name="track-requests-from-worker-roles"></a>Tenere traccia delle richieste dai ruoli di lavoro
Nei ruoli Web il modulo delle richieste raccoglie automaticamente i dati sulle richieste HTTP. Per alcuni esempi di come è possibile eseguire l'override del comportamento di raccolta predefinito, vedere il progetto [MVCWebRole di esempio](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole). 

È possibile acquisire le prestazioni delle chiamate ai ruoli di lavoro tenendone traccia nello stesso modo in cui avviene per le richieste HTTP. In Application Insights il tipo di telemetria per le richieste misura un'unità di lavoro lato server denominata che può essere programmata e può avere indipendentemente esito positivo o negativo. Sebbene le richieste HTTP vengano acquisite automaticamente dall'SDK, è possibile inserire il proprio codice per tenere traccia delle richieste ai ruoli di lavoro.

Vedere i due ruoli di lavoro di esempio instrumentati per segnalare le richieste: 
* [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Eccezioni
Per informazioni su come raccogliere le eccezioni non gestite da diversi tipi di app Web, vedere [Monitoraggio delle eccezioni in Application Insights](../../azure-monitor/app/asp-net-exceptions.md).

Il ruolo Web di esempio contiene i controller MVC5 e Web API 2. Le eccezioni non gestite da questi due controller vengono acquisite con i gestori di eventi seguenti:

* [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) impostato per i controller MVC5 [come illustrato in questo esempio](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) 
* [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) impostato per i controller Web API 2 [come illustrato in questo esempio](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) 

Per i ruoli di lavoro esistono due modi per tenere traccia delle eccezioni:

* Usare TrackException(ex).
* Se è stato aggiunto il pacchetto NuGet del listener di traccia di Application Insights, è possibile usare System.Diagnostics.Trace per registrare le eccezioni [come illustrato in questo esempio](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107).

## <a name="performance-counters"></a>Contatori delle prestazioni
Per impostazione predefinita, vengono raccolti i contatori seguenti:

* \Processo (?? APP_WIN32_PROC? Tempo processore\%
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Per i ruoli Web vengono raccolti anche i contatori seguenti:

* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

È possibile specificare altri contatori delle prestazioni personalizzati o di Windows modificando*ApplicationInsights.config* [come illustrato in questo esempio](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![Contatori delle prestazioni](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Telemetria correlata per i ruoli di lavoro
Grazie agli strumenti di diagnostica avanzati è possibile individuare la causa di una richiesta non riuscita o con latenza elevata. Con i ruoli Web, l'SDK configura automaticamente una correlazione tra i dati di telemetria correlati. 

Per ottenere questa visualizzazione con i ruoli di lavoro, è possibile usare un inizializzatore di telemetria personalizzato per impostare un attributo di contesto Operation.Id comune per tutti i dati di telemetria. In questo modo sarà possibile visualizzare immediatamente se il problema di errore o latenza è stato causato da una dipendenza o dal codice. 

Ecco come:

* Impostare l'ID correlazione in un oggetto CallContext [come illustrato in questo esempio](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). In questo caso, viene usato l'ID richiesta come ID di correlazione.
* Aggiungere un'implementazione personalizzata di TelemetryInitializer per impostare l'oggetto Operation.Id sull'oggetto correlationId impostato in precedenza. Per un esempio, vedere [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13).
* Aggiungere l'inizializzatore di telemetria personalizzato. È possibile eseguire questa operazione nel file *ApplicationInsights.config* o nel codice [come illustrato in questo esempio](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233).

## <a name="client-telemetry"></a>Telemetria client
Per ottenere i dati di telemetria basati su browser, ad esempio i conteggi delle visualizzazioni pagina, i tempi di caricamento delle pagine o le eccezioni di script e per scrivere dati di telemetria personalizzati negli script della pagina, vedere [aggiungere l'SDK JavaScript alle pagine Web][client].

## <a name="availability-tests"></a>Test della disponibilità
Per assicurarsi che l'app rimanga dinamica e reattiva, [configurare i test Web][availability].

## <a name="display-everything-together"></a>Visualizzare tutti gli elementi contemporaneamente
Per una panoramica del sistema, è possibile riunire i grafici di monitoraggio principali in un unico [dashboard](../../azure-monitor/app/overview-dashboard.md). Ad esempio, è possibile aggiungere i conteggi delle richieste e degli errori di ogni ruolo. 

Se il sistema usa altri servizi di Azure, ad esempio Analisi di flusso, includere anche i relativi grafici di monitoraggio. 

Se si dispone di un'app client per dispositivi mobili, usare [App Center](../../azure-monitor/learn/mobile-center-quickstart.md). Creare query in [Analytics](../../azure-monitor/app/analytics.md) per visualizzare i conteggi degli eventi e aggiungerli al dashboard.

## <a name="example"></a>Esempio
[L'esempio](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) monitora un servizio che ha un ruolo Web e due ruoli di lavoro.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Eccezione "metodo non trovato" durante l'esecuzione dei servizi cloud di Azure
È stata eseguita la compilazione per .NET 4.6? .NET 4.6 non è supportato automaticamente nei ruoli dei servizi cloud di Azure. [Installare .NET 4.6 in ogni ruolo](../../cloud-services/cloud-services-dotnet-install-dotnet.md) prima di eseguire l'app.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Passaggi successivi
* [Configurare l'invio di dati di Diagnostica di Azure ad Application Insights](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Creare automaticamente risorse di Application Insights](../../azure-monitor/app/powershell.md)
* [Automatizzare Diagnostica di Azure](../../azure-monitor/app/powershell-azure-diagnostics.md)
* [Funzioni di Azure](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[azure]: ../../azure-monitor/app/app-insights-overview.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md 
