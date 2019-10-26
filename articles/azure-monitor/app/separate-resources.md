---
title: Separazione della telemetria da sviluppo, test e rilascio in Azure Application Insights | Microsoft Docs
description: Telemetria diretta a risorse diverse per indicatori di sviluppo, test e produzione.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/15/2017
ms.openlocfilehash: bcf741e82e247a5b79a478ef1015a70cccb4d274
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899902"
---
# <a name="separating-telemetry-from-development-test-and-production"></a>Separazione della telemetria da sviluppo, test e produzione

Quando si sviluppa la versione successiva di un'applicazione Web, non è desiderabile combinare la telemetria di [Application Insights](../../azure-monitor/app/app-insights-overview.md) della nuova versione con quella della versione già rilasciata. Per evitare confusione, inviare i dati di telemetria da diverse fasi di sviluppo per separare le risorse di Application Insights, con chiavi di strumentazione separate (iKey). Per rendere più semplice la modifica della chiave di strumentazione man mano che una versione si sposta da una fase all'altra, può essere utile impostare il valore ikey nel codice anziché nel file di configurazione. 

Se il sistema è un servizio cloud di Azure, è disponibile [un altro metodo di impostazione di valori iKey separati](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Sulle risorse e le chiavi di strumentazione

Quando si configura il monitoraggio di Application Insights per l'app Web, viene creata una *risorsa* di Application Insights in Microsoft Azure. Aprire questa risorsa nel portale di Azure per visualizzare e analizzare i dati di telemetria raccolti dall'app. La risorsa viene identificata da una *chiave di strumentazione* (iKey). Quando si installa il pacchetto Application Insights per monitorare l'applicazione, questo viene configurato con la chiave di strumentazione, in modo che sappia dove inviare la telemetria.

In genere si sceglie di usare risorse separate o una singola risorsa condivisa in scenari diversi:

* Diverse applicazioni indipendenti: usare una risorsa separata e l'iKey per ogni applicazione.
* Più componenti o ruoli di un'applicazione aziendale: usare una [singola risorsa condivisa](../../azure-monitor/app/app-map.md) per tutte le applicazioni componente. È possibile filtrare o segmentare i dati di telemetria tramite la proprietà cloud_RoleName.
* Sviluppo, test e rilascio: usare una risorsa separata e l'iKey per le versioni del sistema in 'stamp' o in fase di produzione.
* Test A | B: usare una singola risorsa. Creare un oggetto TelemetryInitializer per aggiungere una proprietà alla telemetria che identifica le varianti.


## <a name="dynamic-ikey"></a> Chiave di strumentazione dinamica

Per rendere più semplice la modifica del valore ikey man mano che il codice attraversa le fasi di produzione, impostarlo nel codice anziché nel file di configurazione.

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

### <a name="web-pages"></a>Pagine Web
Il valore iKey viene usato anche nelle pagine Web dell'app, nello [script ottenuto dal pannello Avvio rapido](../../azure-monitor/app/javascript.md). Invece di codificarlo letteralmente nello script, generarlo dallo stato del server. Ad esempio, in un'app ASP.NET:

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


## <a name="create-additional-application-insights-resources"></a>Creare risorse di Application Insights aggiuntive
Per separare i dati di telemetria per componenti diversi dell'applicazione o per indicatori diversi (sviluppo, test o produzione) dello stesso componente, è necessario creare una nuova risorsa di Application Insights.

In [portal.azure.com](https://portal.azure.com)aggiungere una nuova risorsa di Application Insights:

![Fare clic su Nuovo, Application Insights](./media/separate-resources/01-new.png)

* **tipo di applicazione** influisce sul contenuto del pannello Panoramica e sulle proprietà disponibili in [Esplora metriche](../../azure-monitor/app/metrics-explorer.md)di Microsoft Azure. Se il tipo dell'app non è visualizzato, scegliere uno dei tipi Web per le pagine Web.
* **gruppo di risorse** è utile per gestire le proprietà come il [controllo di accesso](../../azure-monitor/app/resources-roles-access-control.md)di Microsoft Azure. È possibile usare gruppi di risorse separati per lo sviluppo, i test e la produzione.
* **sottoscrizione** è il proprio account di pagamento in Azure.
* Il **percorso** è la posizione in cui vengono conservati i dati. e attualmente non è modificabile. 
* **Aggiungi al dashboard** inserisce un riquadro di accesso rapido alla propria risorsa nella pagina iniziale di Azure. 

La creazione della risorsa richiede pochi secondi. Al termine della creazione verrà visualizzato un avviso.

È possibile scrivere uno [script di PowerShell](../../azure-monitor/app/powershell-script-create-resource.md) per creare automaticamente una risorsa.

### <a name="getting-the-instrumentation-key"></a>Ottenere la chiave di strumentazione
La chiave di strumentazione identifica la risorsa creata. 

![Fare clic su Informazioni di base, quindi sulla chiave di strumentazione e infine premere CTRL+C.](./media/separate-resources/02-props.png)

Sono necessarie le chiavi di strumentazione di tutte le risorse a cui l'app invierà dati.

## <a name="filter-on-build-number"></a>Filtrare in base al numero di build
Quando si pubblica una nuova versione dell'app, potrebbe essere opportuno separare i dati telemetrici delle diverse build.

È possibile impostare la proprietà della versione dell'applicazione in modo che sia possibile filtrare i risultati della [ricerca](../../azure-monitor/app/diagnostic-search.md) e di [Esplora metriche](../../azure-monitor/app/metrics-explorer.md).

![Filtro su una proprietà](./media/separate-resources/050-filter.png)

Esistono diversi metodi di impostazione della proprietà della versione dell'applicazione.

* Impostare direttamente:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Eseguire il wrapping di tale riga in un [inizializzatore di telemetria](../../azure-monitor/app/api-custom-events-metrics.md#defaults) per assicurarsi che tutte le istanze di TelemetryClient siano impostate in modo coerente.
* [ASP.NET] Impostare la versione `BuildInfo.config`. Il modulo Web selezionerà la versione dal nodo BuildLabel. Includere questo file nel progetto e ricordarsi di impostare la proprietà Copia sempre in Esplora soluzioni.

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] Generare automaticamente BuildInfo.config in MSBuild. A tale scopo, aggiungere alcune righe al file `.csproj`:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Verrà generato un file denominato *yourProjectName*. BuildInfo. config. il processo di pubblicazione lo rinomina in BuildInfo. config.

    L'etichetta di compilazione contiene un segnaposto (AutoGen_) se la compilazione viene eseguita in Visual Studio. Se la compilazione viene eseguita con MSBuild, viene inserito il numero di versione corretto.

    Per consentire a MSBuild di generare i numeri di versione, in AssemblyReference.cs impostare la versione come, ad esempio, `1.0.*` .

## <a name="version-and-release-tracking"></a>Verifica della versione
Per tenere traccia della versione dell'applicazione, assicurarsi che il processo di Microsoft Build Engine generi `buildinfo.config`. Nel file con estensione csproj, aggiungere:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

Quando ha le informazioni di compilazione, il modulo Web di Application Insights aggiunge automaticamente la **versione dell'applicazione** come proprietà a ogni elemento dei dati di telemetria. Questo consente di applicare filtri in base alla versione quando si eseguono [ricerche diagnostiche](../../azure-monitor/app/diagnostic-search.md) o quando si [esaminano le metriche](../../azure-monitor/app/metrics-explorer.md).

Si noti tuttavia che il numero di versione della build viene generato solo da Microsoft Build Engine, non dalla build dello sviluppatore in Visual Studio.

### <a name="release-annotations"></a>Annotazioni sulle versioni
Se si usa Azure DevOps, è possibile [visualizzare un marcatore di annotazione](../../azure-monitor/app/annotations.md) aggiunto ai grafici quando si rilascia una nuova versione. L'immagine seguente illustra come viene visualizzato il marcatore.

![Screenshot di annotazione sulla versione di esempio in un grafico](media/separate-resources/release-annotation.png)
## <a name="next-steps"></a>Passaggi successivi

* [Risorse condivise da più ruoli](../../azure-monitor/app/app-map.md)
* [Creare un inizializzatore di telemetria per distinguere le varianti A|B](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
