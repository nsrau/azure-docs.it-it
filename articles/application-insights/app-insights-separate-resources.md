---
title: Separare le risorse di Application Insights per lo sviluppo, il test e la produzione
description: Monitorare le prestazioni e l&quot;utilizzo dell&quot;applicazione nelle diverse fasi di sviluppo
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 578e30f0-31ed-4f39-baa8-01b4c2f310c9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 7a9c40081f52b2ffe918f4612f790f7fd08acc5a
ms.openlocfilehash: bd4ba58715b216473caa4193567f6d3b95d9d347


---
# <a name="separating-application-insights-resources"></a>Separare risorse di Application Insights
I dati di telemetria provenienti da componenti diversi e le versioni dell'applicazione devono essere inviati a risorse di Application Insights diverse o devono essere combinati in un'unica risorsa? Questo articolo illustra le procedure consigliate e le tecniche necessarie.

Prima di tutto è necessario comprendere bene la domanda. I dati ricevuti dall'applicazione vengono archiviati ed elaborati da Application Insights in una *risorsa*di Microsoft Azure. Ogni risorsa viene identificata da una *chiave di strumentazione* (iKey). Nell'app la chiave viene fornita ad Application Insights SDK, in modo che possa inviare i dati raccolti alla risorsa corretta. La chiave può essere fornita nel codice o nel file ApplicationInsights.config. La modifica della chiave nell'SDK permette di indirizzare i dati a risorse diverse. 

In un caso semplice, quando si crea il codice per una nuova applicazione, si crea anche una nuova risorsa in Application Insights. In Visual Studio questa operazione viene eseguita automaticamente dalla finestra di dialogo *Nuovo progetto* .

Se si tratta di un sito Web con volumi elevati, questa risorsa può essere distribuita in più istanze del server.

Negli scenari più complessi, il sistema è costituito da più componenti, ad esempio un sito Web e un processore back-end. 

## <a name="when-to-use-separate-ikeys"></a>Quando usare iKey separate
Ecco alcune linee guida generali:

* Nei casi in cui si dispone di un'unità di applicazione distribuibile in modo autonomo ed eseguibile in un set di istanze del server che può essere aumentato o ridotto indipendente dagli altri componenti, in genere si esegue il mapping dell'unità di applicazione a un'unica risorsa, in altre parole l'unità di applicazione avrà un'unica chiave di strumentazione (iKey).
* Al contrario, l'uso di iKey separate può avere i motivi seguenti:
  * È più facile leggere metriche diverse se si trovano in componenti separati.
  * Mantenere i dati di telemetria con un volume inferiore separati da quelli con un volume elevato, in modo che la limitazione, le quote e il campionamento in un flusso non influiscano sull'altro.
  * Si vogliono mantenere separate le configurazioni di avvisi, esportazione ed elementi di lavoro.
  * Si vogliono distribuire i [limiti](app-insights-pricing.md#limits-summary) , ad esempio la quota dei dati di telemetria, la limitazione delle richieste e il conteggio dei test Web.
  * Il codice in fase di sviluppo e quello in fase di test devono essere inviati a una iKey separata rispetto all'indicatore di produzione.  

Molte delle esperienze con il portale di Application Insights vengono progettate in base a queste linee guida. Ad esempio, i server visualizzano i segmenti all'interno di un'istanza del server, presupponendo che i dati di telemetria relativi a un componente logico provengano da diverse istanze del server.

## <a name="single-ikey"></a>iKey singola
Se si inviano dati di telemetria da più componenti a un'unica iKey:

* Aggiungere una proprietà a tutti i dati di telemetria che consentono di segmentare e filtrare in base all'identità del componente. L'ID ruolo viene aggiunto automaticamente ai dati di telemetria dalle istanze del ruolo server, ma in altri casi è possibile aggiungere la proprietà tramite un [inizializzatore di telemetria](app-insights-api-filtering-sampling.md#add-properties) .
* Aggiornare gli Application Insights SDK presenti in diversi componenti nello stesso momento. I dati di telemetria per un iKey devono avere origine dalla stessa versione dell'SDK.

## <a name="separate-ikeys"></a>iKey separate
Nei casi in cui si dispone di più iKey per componenti diversi dell'applicazione:

* Creare un [dashboard](app-insights-dashboards.md) per una visualizzazione dei dati chiave di telemetria dell'applicazione logica, combinando i dati provenienti dai diversi componenti dell'applicazione. I dashboard possono essere condivisi. Un'unica visualizzazione logica del sistema, quindi, può essere usata da team diversi.
* Organizzare [gruppi di risorse](app-insights-resources-roles-access-control.md) a livello di team. Le autorizzazioni di accesso, che vengono assegnate per gruppo di risorse, includono le autorizzazioni per l'impostazione di avvisi. 
* Usare [modelli di Azure Resource Manager e PowerShell](app-insights-powershell.md) per semplificare la gestione di elementi quali le regole degli avvisi e i test Web.

## <a name="separate-ikeys-for-devtest-and-production"></a>Separare le iKey di sviluppo e test da quella di produzione
Per rendere più semplice modificare automaticamente la chiave al momento del rilascio dell'applicazione, impostare la iKey nel codice, anziché in ApplicationInsights.config.

Se il sistema è un servizio cloud di Azure, è disponibile [un altro metodo di impostazione di valori iKey separati](app-insights-cloudservices.md).)

### <a name="a-namedynamic-ikeya-dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> Chiave di strumentazione dinamica
Impostare la chiave in un metodo di inizializzazione, ad esempio global.aspx.cs in un servizio ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

In questo esempio i valori ikey per le diverse risorse vengono inseriti in versioni diverse del file di configurazione Web. Lo scambio del file di configurazione Web, che è possibile eseguire nell'ambito dello script di rilascio, consente di scambiare la risorsa di destinazione.

### <a name="web-pages"></a>Pagina Web
Il valore iKey viene usato anche nelle pagine Web dell'app, nello [script ottenuto dal pannello Avvio rapido](app-insights-javascript.md). Invece di codificarlo letteralmente nello script, generarlo dallo stato del server. Ad esempio, in un'app ASP.NET:

*JavaScript in Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="creating-an-additional-application-insights-resource"></a>Creazione di una risorsa di Application Insights aggiuntiva
Se si decide di tenere separati i dati di telemetria per componenti diversi dell'applicazione o per indicatori diversi (sviluppo, test o produzione) dello stesso componente, è necessario creare una nuova risorsa di Application Insights.

In [portal.azure.com](https://portal.azure.com)aggiungere una nuova risorsa di Application Insights:

![Fare clic su Nuovo, Application Insights](./media/app-insights-separate-resources/01-new.png)

* **tipo di applicazione** influisce sul contenuto del pannello Panoramica e sulle proprietà disponibili in [Esplora metriche](app-insights-metrics-explorer.md)di Microsoft Azure. Se il tipo dell'app non è visualizzato, scegliere uno dei tipi Web per le pagine Web.
* **gruppo di risorse** è utile per gestire le proprietà come il [controllo di accesso](app-insights-resources-roles-access-control.md)di Microsoft Azure. È possibile usare gruppi di risorse separati per lo sviluppo, i test e la produzione.
* **sottoscrizione** è il proprio account di pagamento in Azure.
* Il **percorso** è la posizione in cui vengono conservati i dati. e attualmente non è modificabile. 
* **Aggiungi al dashboard** inserisce un riquadro di accesso rapido alla propria risorsa nella pagina iniziale di Azure. 

La creazione della risorsa richiede pochi secondi. Al termine della creazione verrà visualizzato un avviso.

È possibile scrivere uno [script di PowerShell](app-insights-powershell-script-create-resource.md) per creare automaticamente una risorsa.

## <a name="getting-the-instrumentation-key"></a>Ottenere la chiave di strumentazione
La chiave di strumentazione identifica la risorsa creata. 

![Fare clic su Informazioni di base, quindi sulla chiave di strumentazione e infine premere CTRL+C.](./media/app-insights-separate-resources/02-props.png)

Sono necessarie le chiavi di strumentazione di tutte le risorse a cui l'app invierà dati.




<!--HONumber=Nov16_HO3-->


