---
title: Mappa delle applicazioni in Azure Application Insights | Microsoft Docs
description: Monitorare topologie di applicazioni complesse con la mappa delle applicazioni
ms.topic: conceptual
ms.date: 03/15/2019
ms.reviewer: sdash
ms.openlocfilehash: dce2fdbe7e0c390309be38d2ebab4c73dbb4ed2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666276"
---
# <a name="application-map-triage-distributed-applications"></a>Mappa delle applicazioni: valutare le applicazioni distribuite

La mappa delle applicazioni consente di individuare i colli di bottiglia delle prestazioni o le aree sensibili agli errori in tutti i componenti dell'applicazione distribuita. Ogni nodo nella mappa rappresenta un componente dell'applicazione o le relative dipendenze e ha un indicatore KPI dell'integrità e uno stato degli avvisi. È possibile fare clic da qualsiasi componente per ottenere una diagnostica più dettagliata, ad esempio sugli eventi di Application Insights. Se l'app usa i servizi di Azure, è possibile anche fare clic sulla diagnostica di Azure, ad esempio per consigli di Advisor su database SQL.

## <a name="what-is-a-component"></a>Cos'è un componente?

I componenti sono parti dell'applicazione distribuita o di microservizi, distribuibili autonomamente. Gli sviluppatori e i team delle operazioni hanno visibilità o accesso a livello di codice ai dati di telemetria generati dai componenti di queste applicazioni. 

* I componenti sono diversi dalle dipendenze esterne "osservate" quali SQL, EventHub e così via, a cui il team o l'organizzazione potrebbero non avere accesso (codice o dati di telemetria).
* I componenti vengono eseguiti su diverse istanze di ruolo, server o contenitore.
* I componenti possono essere chiavi di strumentazione di Application Insights diverse tra loro (anche in caso di sottoscrizioni diverse) oppure ruoli diversi che creano report per una sola chiave di strumentazione di Application Insights. L'esperienza della mappa di anteprima mostra i componenti indipendentemente dalla loro configurazione.

## <a name="composite-application-map"></a>Mappa delle applicazioni composite

È possibile visualizzare la topologia completa dell'applicazione con più livelli di componenti dell'applicazione correlati. I componenti possono essere risorse di Application Insights diverse o ruoli diversi in una singola risorsa. La mappa delle app consente di trovare i componenti seguendo le chiamate di dipendenza HTTP inviate tra i server con Application Insights SDK installato. 

Questa esperienza inizia con la progressiva individuazione dei componenti. Quando si carica per la prima volta la mappa dell'applicazione, viene attivato un set di query per individuare i componenti correlati a questo componente. Un pulsante nell'angolo superiore sinistro viene aggiornato con il numero di componenti dell'applicazione individuati. 

Quando si fa clic sul pulsante per aggiornare i componenti della mappa, la mappa viene aggiornata con tutti i componenti individuati fino a quel momento. A seconda della complessità dell'applicazione, l'operazione potrebbe richiedere un minuto per il caricamento.

Se tutti i componenti sono ruoli all'interno di una singola risorsa di Application Insights, questo passaggio di individuazione non è necessario. Il caricamento iniziale per tale applicazione includerà tutti i relativi componenti.

![Screenshot della mappa delle applicazioni](media/app-map/app-map-001.png)

Uno dei principali obiettivi di questa esperienza è quello di consentire di visualizzare le topologie complesse con centinaia di componenti.

Fare clic su qualsiasi componente per visualizzare le informazioni dettagliate correlate e passare all'esperienza di valutazione delle prestazioni e degli errori per il componente.

![Riquadro a comparsa](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Esaminare gli errori

Selezionare **Esamina errori** per aprire il riquadro Errori.

![Screenshot del pulsante Esamina errori](media/app-map/investigate-failures.png)

![Screenshot dell'esperienza Errori](media/app-map/failures.png)

### <a name="investigate-performance"></a>Esaminare le prestazioni

Per risolvere i problemi di prestazioni, selezionare **Analizza prestazioni**.

![Screenshot del pulsante Esamina prestazioni](media/app-map/investigate-performance.png)

![Screenshot dell'esperienza Prestazioni](media/app-map/performance.png)

### <a name="go-to-details"></a>Vai ai dettagli

Selezionare **Vai ai dettagli** per esplorare l'esperienza di transazione end-to-end, che può offrire visualizzazioni fino al livello dello stack di chiamate.

![Screenshot del pulsante Vai ai dettagli](media/app-map/go-to-details.png)

![Screenshot dei dettagli delle transazioni end-to-end](media/app-map/end-to-end-transaction.png)

### <a name="view-logs-analytics"></a>Visualizza registri (analitica)

Per eseguire query e analizzare ulteriormente i dati delle applicazioni, fare clic su **Visualizza in Registri (analitica)**.

![Screenshot del pulsante Visualizza in Analisi](media/app-map/view-logs.png)

![Screenshot dell'esperienza di analisi. Grafico a linee che riepiloga la durata media della risposta di una richiesta nelle ultime 12 ore.](media/app-map/log-analytics.png)

### <a name="alerts"></a>Avvisi

Per visualizzare gli avvisi attivi e le regole sottostanti che attivano gli avvisi, selezionare **Avvisi**.

![Screenshot del pulsante Avvisi](media/app-map/alerts.png)

![Screenshot dell'esperienza di analisi](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>Impostare il nome del ruolo cloudSet cloud role name

Mappa applicazione utilizza la proprietà del nome del **ruolo cloud** per identificare i componenti sulla mappa. Application Insights SDK aggiunge automaticamente la proprietà del nome del ruolo cloud ai dati di telemetria generati dai componenti. Ad esempio, l'SDK aggiungerà un nome di sito Web o un nome di ruolo del servizio alla proprietà del nome del ruolo cloud. Tuttavia, vi sono casi in cui si desidera eseguire l'override del valore predefinito. Per sostituire il nome del ruolo cloud e modificare gli elementi visualizzati nella mappa applicazioni:To override cloud role name and change what gets displayed on the Application Map:

### <a name="netnet-core"></a>.NET/.NET Core

**Scrivere TelemetryInitializer personalizzato come indicato di seguito.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

**ASP.NET app: caricare l'inizializzatore per la configurazione di telemetria attiva**

In ApplicationInsights.config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

Un metodo alternativo per ASP.NET app Web consiste nel creare un'istanza dell'inizializzatore nel codice, ad esempio in Global.aspx.cs:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

> [!NOTE]
> L'aggiunta `ApplicationInsights.config` di `TelemetryConfiguration.Active` un inizializzatore tramite o utilizzo non è valida per le applicazioni ASP.NET Core.Adding initializer using or using is not valid for ASP.NET Core applications. 

**ASP.NET Core: inizializzatore di caricamento per TelemetryConfiguration**

Per le applicazioni ASP.NET `TelemetryInitializer` [Core,](asp-net-core.md#adding-telemetryinitializers) l'aggiunta di un nuovo viene eseguita aggiungendola al contenitore Inserimento delle dipendenze, come illustrato di seguito. Questa operazione `ConfigureServices` viene eseguita nel metodo della `Startup.cs` classe.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Metodo alternativo per Node. js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

### <a name="java"></a>Java

A partire da Application Insights Java SDK 2.5.0, `<RoleName>` è `ApplicationInsights.xml` possibile specificare il nome del ruolo cloud aggiungendo al file, ad esempio.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
   <RoleName>** Your role name **</RoleName>
   ...
</ApplicationInsights>
```

Se si usa Spring Boot con l'utilità di avvio Spring Boot di Application Insights, l'unica modifica necessaria consiste nell'impostare il nome personalizzato per l'applicazione nel file application.properties.

`spring.application.name=<name-of-app>`

L'avviatore di avvio a molla assegnerà automaticamente il nome del ruolo cloud al valore immesso per la proprietà spring.application.name.

### <a name="clientbrowser-side-javascript"></a>JavaScript lato client/browser

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Informazioni sul nome del ruolo cloud nel contesto della mappa applicazioniUnderstanding cloud role name within the context of the Application Map

Per quanto riguarda come pensare al nome del ruolo cloud , può essere utile esaminare una mappa dell'applicazione che dispone di più nomi di ruolo cloud presenti:As per how to think about **cloud role name**, it can be helpful to look at an Application Map that has multiple cloud role name present:

![Screenshot della mappa delle applicazioni](media/app-map/cloud-rolename.png)

Nella mappa dell'applicazione sopra ogni nome in caselle verdi sono valori di nome del ruolo cloud per diversi aspetti di questa particolare applicazione distribuita. Quindi per questa applicazione i `Authentication` `acmefrontend`suoi `Inventory Management`ruoli `Payment Processing Worker Role`consistono in: , , a . 

Nel caso di questa app, ognuno di questi nomi di ruolo cloud rappresenta anche una risorsa di Application Insights univoca diversa con le proprie chiavi di strumentazione. Poiché il proprietario di questa applicazione ha accesso a ognuna di queste quattro diverse risorse di Application Insights, Application Map è in grado di unire una mappa delle relazioni sottostanti.

Per le [definizioni ufficiali](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

In alternativa, l'istanza del **ruolo cloud** può essere utile per gli scenari in cui il nome del **ruolo cloud** indica che il problema è in un punto qualsiasi del front-end Web, ma è possibile che il front-end Web sia in esecuzione in più server con carico bilanciato in modo da essere in grado di eseguire il drill-in di un livello più profondo tramite query Kusto e sapere se il problema interessa tutti i server/istanze front-end Web o solo uno può essere estremamente importante.

Uno scenario in cui potrebbe essere necessario eseguire l'override del valore per l'istanza del ruolo cloud potrebbe essere se l'app è in esecuzione in un ambiente in contenitori in cui il solo saper conoscere il singolo server potrebbe non essere informazioni sufficienti per individuare un determinato problema.

Per altre informazioni su come eseguire l'override della proprietà del nome del ruolo cloud con inizializzatori di telemetria, vedere Aggiungere proprietà: ITelemetryInitializer .For more information about how to override the cloud role name property with telemetry initializers, see [Add properties: ITelemetryInitializer](api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verificano problemi nel far funzionare come previsto la mappa delle applicazioni, provare questa procedura:

### <a name="general"></a>Generale

1. Assicurarsi di usare un SDK supportato ufficialmente. Gli SDK non supportati/community potrebbero non supportare la correlazione.

    Fare riferimento a questo [articolo](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) per un elenco di SDK supportati.

2. Aggiornare tutti i componenti alla versione più recente dell'SDK.

3. Se si usa Funzioni di Azure con C#, eseguire l'aggiornamento a [Funzioni V2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Verificare che [il nome del ruolo cloud](#set-cloud-role-name) sia configurato correttamente.

5. Se non si presenta una dipendenza, assicurarsi che sia nell'elenco delle [dipendenze raccolte automaticamente](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Se non lo è, si può comunque tenere traccia di essa manualmente con una [chiamata per tenere traccia delle dipendenze](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Troppi nodi sulla mappa

Mappa applicazione costruisce un nodo dell'applicazione per ogni nome di ruolo cloud univoco presente nei dati di telemetria delle richieste e un nodo di dipendenza per ogni combinazione univoca di tipo, destinazione e nome del ruolo cloud nei dati di telemetria delle dipendenze. Se sono presenti più di 10.000 nodi nei dati di telemetria, Application Map non sarà in grado di recuperare tutti i nodi e i collegamenti, pertanto la mappa sarà incompleta. In questo caso, verrà visualizzato un messaggio di avviso durante la visualizzazione della mappa.

Inoltre, la mappa dell'applicazione supporta solo fino a 1000 nodi separati non raggruppati di cui viene eseguito il rendering contemporaneamente. La mappa dell'applicazione riduce la complessità visiva raggruppando le dipendenze insieme con lo stesso tipo e i chiamanti, ma se i dati di telemetria hanno troppi nomi di ruolo cloud univoci o troppi tipi di dipendenza, tale raggruppamento sarà insufficiente e la mappa non sarà in grado di rendere.

Per risolvere questo problema, è necessario modificare la strumentazione per impostare correttamente il nome del ruolo cloud, il tipo di dipendenza e i campi di destinazione delle dipendenze.

* La destinazione della dipendenza deve rappresentare il nome logico di una dipendenza. In molti casi, è equivalente al nome del server o della risorsa della dipendenza. Ad esempio, nel caso di dipendenze HTTP è impostato sul nome host. Non deve contenere ID univoci o parametri che cambiano da una richiesta a un'altra.

* Il tipo di dipendenza deve rappresentare il tipo logico di una dipendenza. Ad esempio, HTTP, SQL o BLOB di Azure sono tipi di dipendenza tipici. Non deve contenere ID univoci.

* Lo scopo del nome del ruolo cloud è descritto nella [sezione precedente.](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name)

## <a name="portal-feedback"></a>Commenti e suggerimenti del portale

Per inviare commenti e suggerimenti, usare l'apposita opzione del feedback.

![Immagine MapLink-1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul funzionamento della correlazione in Application Insights, vedere l'articolo sulla correlazione dei [dati di telemetria.](correlation.md)
* L'esperienza di [diagnostica delle transazioni end-to-end](transaction-diagnostics.md) correla i dati di telemetria sul lato server da tutti i componenti monitorati di Application Insights in un'unica visualizzazione.
* Per gli scenari di correlazione avanzati in ASP.NET Core e ASP.NET consultare l'articolo Tenere traccia delle [operazioni personalizzate.](custom-operations-tracking.md)
