---
title: Mappa delle applicazioni in Azure Application Insights | Microsoft Docs
description: Monitorare topologie di applicazioni complesse con la mappa delle applicazioni
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/15/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 618453ec9857254fe14608df8091bb79bd3815b7
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65509982"
---
# <a name="application-map-triage-distributed-applications"></a>Mappa delle applicazioni: Valutazione delle applicazioni distribuite

La mappa delle applicazioni consente di individuare i colli di bottiglia delle prestazioni o le aree sensibili agli errori in tutti i componenti dell'applicazione distribuita. Ogni nodo nella mappa rappresenta un componente dell'applicazione o le relative dipendenze e ha un indicatore KPI dell'integrità e uno stato degli avvisi. È possibile fare clic da qualsiasi componente per ottenere una diagnostica più dettagliata, ad esempio sugli eventi di Application Insights. Se l'app usa i servizi di Azure, è possibile anche fare clic sulla diagnostica di Azure, ad esempio per consigli di Advisor su database SQL.

## <a name="what-is-a-component"></a>Cos'è un componente?

I componenti sono parti dell'applicazione distribuita o di microservizi, distribuibili autonomamente. Gli sviluppatori e i team delle operazioni hanno visibilità o accesso a livello di codice ai dati di telemetria generati dai componenti di queste applicazioni. 

* I componenti sono diversi dalle dipendenze esterne "osservate" quali SQL, EventHub e così via, a cui il team o l'organizzazione potrebbero non avere accesso (codice o dati di telemetria).
* I componenti vengono eseguiti su diverse istanze di ruolo, server o contenitore.
* I componenti possono essere chiavi di strumentazione di Application Insights diverse tra loro (anche in caso di sottoscrizioni diverse) oppure ruoli diversi che creano report per una sola chiave di strumentazione di Application Insights. L'esperienza della mappa di anteprima mostra i componenti indipendentemente dalla loro configurazione.

## <a name="composite-application-map"></a>Mappa delle applicazioni composite

È possibile visualizzare la topologia completa dell'applicazione con più livelli di componenti dell'applicazione correlati. I componenti possono essere risorse di Application Insights diverse o ruoli diversi in una singola risorsa. La mappa delle app consente di trovare i componenti seguendo le chiamate di dipendenza HTTP inviate tra i server con Application Insights SDK installato. 

Questa esperienza inizia con la progressiva individuazione dei componenti. Quando si carica innanzitutto la mappa delle applicazioni, viene attivato un set di query per individuare i componenti correlati a questo componente. Un pulsante nell'angolo superiore sinistro viene aggiornato con il numero di componenti dell'applicazione individuati. 

Quando si fa clic sul pulsante per aggiornare i componenti della mappa, la mappa viene aggiornata con tutti i componenti individuati fino a quel momento. A seconda della complessità dell'applicazione, l'operazione potrebbe richiedere un minuto per il caricamento.

Se tutti i componenti sono ruoli all'interno di una singola risorsa di Application Insights, questo passaggio di individuazione non è necessario. Il caricamento iniziale per tale applicazione includerà tutti i relativi componenti.

![Screenshot della mappa delle applicazioni](media/app-map/app-map-001.png)

Uno dei principali obiettivi di questa esperienza è quello di consentire di visualizzare le topologie complesse con centinaia di componenti.

Fare clic su qualsiasi componente per visualizzare le informazioni dettagliate correlate e passare all'esperienza di valutazione delle prestazioni e degli errori per il componente.

![Riquadro a comparsa](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Esamina errori

Selezionare **Esamina errori** per aprire il riquadro Errori.

![Screenshot del pulsante Esamina errori](media/app-map/investigate-failures.png)

![Screenshot dell'esperienza Errori](media/app-map/failures.png)

### <a name="investigate-performance"></a>Esamina prestazioni

Per risolvere i problemi relativi alle prestazioni, selezionare **Esamina prestazioni**.

![Screenshot del pulsante Esamina prestazioni](media/app-map/investigate-performance.png)

![Screenshot dell'esperienza Prestazioni](media/app-map/performance.png)

### <a name="go-to-details"></a>Vai ai dettagli

Selezionare **passare a dettagli** per esplorare l'esperienza end-to-end delle transazioni, che può offrire viste fino al livello dello stack di chiamate.

![Screenshot del pulsante Vai ai dettagli](media/app-map/go-to-details.png)

![Screenshot dei dettagli delle transazioni end-to-end](media/app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Visualizza in Analisi

Per eseguire query ed esaminare ulteriormente i dati delle applicazioni, fare clic su **Visualizza in Analisi**.

![Screenshot del pulsante Visualizza in Analisi](media/app-map/view-in-analytics.png)

![Screenshot dell'esperienza di analisi](media/app-map/analytics.png)

### <a name="alerts"></a>Avvisi

Per visualizzare gli avvisi attivi e le regole sottostanti che attivano gli avvisi, selezionare **Avvisi**.

![Screenshot del pulsante Avvisi](media/app-map/alerts.png)

![Screenshot dell'esperienza di analisi](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>Nome del ruolo cloud set

Mappa delle applicazioni Usa il **nome del ruolo cloud** proprietà per identificare i componenti sulla mappa. SDK di Application Insights aggiunge automaticamente la proprietà nome del ruolo cloud per i dati di telemetria generati dai componenti. Ad esempio, il SDK aggiungerà un nome di sito web o ruolo del servizio per la proprietà nome del ruolo cloud. Tuttavia, vi sono casi in cui si desidera eseguire l'override del valore predefinito. Eseguire l'override di nome del ruolo cloud e modificare ciò che viene visualizzato nella mappa delle applicazioni:

### <a name="net"></a>.NET

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
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance"
            }
        }
    }
}
```

**Caricare l'inizializzatore**

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

Un metodo alternativo è quello di creare un'istanza dell'inizializzatore nel codice, ad esempio nel file Global.aspx.cs:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
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

Se si usa Spring Boot con l'utilità di avvio Spring Boot di Application Insights, l'unica modifica necessaria consiste nell'impostare il nome personalizzato per l'applicazione nel file application.properties.

`spring.application.name=<name-of-app>`

L'utilità di avvio Spring Boot verrà assegnato automaticamente il nome di ruolo cloud per il valore che immesso per la proprietà spring.application.name.

Per altre informazioni su Java correlazione e su come configurare il ruolo cloud assegnare un nome per l'estrazione di applicazioni non SpringBoot ciò [sezione](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name) sulla correlazione.

### <a name="clientbrowser-side-javascript"></a>JavaScript lato client/browser

```javascript
appInsights.queue.push(() => {
appInsights.context.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Nome del ruolo cloud conoscenza all'interno del contesto di mappa delle applicazioni

Per quanto riguarda come pensi **nome del ruolo cloud**, può essere utile esaminare una mappa delle applicazioni che dispone di più nomi di ruolo cloud presenti:

![Screenshot della mappa delle applicazioni](media/app-map/cloud-rolename.png)

Nella mappa delle applicazioni di sopra di ognuno dei nomi nelle caselle di colore verde è valori di nome di ruolo per i vari aspetti di questa particolare applicazione distribuita a cloud. In modo che per questa app relativi ruoli costituiti da: `Authentication`, `acmefrontend`, `Inventory Management`, un `Payment Processing Worker Role`. 

Nel caso di questa app ognuno di questi nomi di ruolo cloud rappresenta anche un'altra risorsa di Application Insights univoca con le proprie chiavi di strumentazione. Poiché il proprietario di questa applicazione può accedere a ognuno di questi quattro diverse risorse di Application Insights, mappa delle applicazioni è in grado di mettere insieme una mappa delle relazioni sottostante.

Per il [definizioni ufficiali](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

In alternativa **istanza del ruolo cloud** può essere utile per scenari in cui **nome del ruolo cloud** indica il problema è in qualche punto del front-end Web, ma si potrebbe essere in esecuzione il front-end tra più server con carico bilanciato così la possibilità di analizzare i dati di un livello più profondo tramite query Kusto e sapere se il problema sta influenzando tutti i front-end server/istanze web o solo uno può essere estremamente importante.

Uno scenario in cui è possibile sostituire il valore per l'istanza del ruolo cloud potrebbe essere se l'app è in esecuzione in un ambiente basato su contenitori in cui è sufficiente sapere il singolo server potrebbe non essere informazioni sufficienti per individuare un determinato problema.

Per altre informazioni su come eseguire l'override di proprietà del nome di ruolo cloud con gli inizializzatori di telemetria, vedere [aggiungere proprietà: ITelemetryInitializer](api-filtering-sampling.md#add-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>risoluzione dei problemi

Se si verificano problemi nel far funzionare come previsto la mappa delle applicazioni, provare questa procedura:

### <a name="general"></a>Generale

1. Assicurarsi di usare un SDK supportato ufficialmente. Gli SDK non supportati/community potrebbero non supportare la correlazione.

    Fare riferimento a questo [articolo](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) per un elenco degli SDK supportati.

2. Aggiornare tutti i componenti alla versione più recente dell'SDK.

3. Se si usa Funzioni di Azure con C#, eseguire l'aggiornamento a [Funzioni V2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Confermare [nome del ruolo cloud](#set-cloud-role-name) sia configurato correttamente.

5. Se non si presenta una dipendenza, assicurarsi che sia nell'elenco delle [dipendenze raccolte automaticamente](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Se non lo è, si può comunque tenere traccia di essa manualmente con una [chiamata per tenere traccia delle dipendenze](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Troppi nodi sulla mappa

Mappa delle applicazioni crea un nodo dell'applicazione per ogni nome di ruolo cloud esclusive presente nei dati di telemetria di richiesta e un nodo di dipendenza per ogni combinazione univoca del tipo di destinazione e nome del ruolo cloud nei dati di telemetria delle dipendenze. Se sono presenti più di 10.000 nodi nei dati di telemetria, mappa delle applicazioni non sarà in grado di recuperare tutti i nodi e collegamenti, in modo che la mappa sia incompleta. In questo caso, verrà visualizzato un messaggio di avviso durante la visualizzazione della mappa.

Mappa delle applicazioni, inoltre, supporta solo fino a 1000 nodi non raggruppati separati viene eseguito il rendering in una sola volta. Mappa delle applicazioni riduce complessità visiva mediante il raggruppamento delle dipendenze che hanno lo stesso tipo e ai chiamanti, ma se i dati di telemetria contiene troppi nomi di ruolo cloud esclusive o troppi tipi di dipendenza, tale raggruppamento, sarà sufficiente e la mappa sarà in grado di rendere.

Per risolvere questo problema, è necessario modificare la strumentazione per impostare correttamente il nome del ruolo cloud, tipo di dipendenza e i campi di destinazione di dipendenza.

* Destinazione della dipendenza deve rappresentare il nome logico di una dipendenza. In molti casi, è equivalente al server o al nome di risorsa della dipendenza. Ad esempio, nel caso di dipendenze HTTP è impostato sul nome host. Non deve contenere ID univoci o i parametri che variano da un'unica richiesta a un altro.

* Tipo di dipendenza deve rappresentare il tipo di logico di una dipendenza. Ad esempio, HTTP, SQL o Blob di Azure sono tipi di dipendenza tipico. Non deve contenere ID univoci.

* Viene descritto lo scopo del nome del ruolo cloud nel [sopra la sezione](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name).

## <a name="portal-feedback"></a>Commenti e suggerimenti del portale

Per inviare commenti e suggerimenti, usare l'apposita opzione del feedback.

![Immagine MapLink-1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Passaggi successivi

* [Correlazione di comprensione](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)
