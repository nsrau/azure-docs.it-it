---
title: Monitoraggio e gestione di Azure per enti pubblici | Microsoft Docs
description: Fornisce un confronto delle funzionalità e alcune linee guida sullo sviluppo di applicazioni per Azure per enti pubblici.
services: azure-government
cloud: gov
author: gsacavdm
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.topic: article
ms.workload: azure-government
ms.date: 12/11/2019
ms.author: gsacavdm
ms.openlocfilehash: b6f395964c286aca1bc8a1c190edeea00ba6e15c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362039"
---
# <a name="azure-government-monitoring--management"></a>Monitoraggio e gestione di Azure per enti pubblici
Questo articolo descrive le variazioni ai servizi di monitoraggio e gestione e presenta alcune considerazioni sull'ambiente di Azure per enti pubblici.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="advisor"></a>Advisor
Advisor è disponibile a livello generale in Azure per enti pubblici.

Per ulteriori informazioni, vedere la [documentazione pubblica di Advisor](../advisor/advisor-overview.md).

### <a name="variations"></a>Variazioni
Le seguenti raccomandazioni di Advisor non sono attualmente disponibili in Azure per enti pubblici:

* Disponibilità elevata
  * Configurare il gateway VPN su attivo-attivo per la resilienza della connessione
  * Creare avvisi di integrità dei servizi Azure per ricevere una notifica quando si verificano problemi in Azure
  * Configurazione degli endpoint di Gestione traffico per la resilienza
  * Usare l'eliminazione temporanea per l'account di archiviazione di Azure
* Prestazioni
  * Migliorare affidabilità e prestazioni di Servizi app
  * Ridurre la durata DNS nel profilo di Gestione traffico per eseguire più rapidamente il failover a endpoint integri
  * Migliorare le prestazioni di SQL Data Warehouse
  * Usare Archiviazione Premium
  * Eseguire la migrazione dell'account di archiviazione a Azure Resource Manager
* Costo
  * Acquistare istanze di macchine virtuali riservate per risparmiare denaro rispetto ai costi con pagamento in base al consumo
  * Elimina circuiti ExpressRoute non sottoposte a provisioning
  * Eliminare o riconfigurare i gateway di rete virtuale inattivi

Il calcolo usato per consigliare la corretta dimensione o l'arresto di macchine virtuali sottoutilizzate è il seguente in Azure per enti pubblici:

Advisor monitora l'utilizzo della macchina virtuale per 7 giorni e identifica le macchine virtuali con utilizzo ridotto. Le macchine virtuali sono considerate con scarso utilizzo se l'utilizzo della CPU è pari al 5% o inferiore e l'utilizzo della rete è inferiore al 2% o se il carico di lavoro corrente può essere adattato da una dimensione di macchina virtuale inferiore. Se si desidera essere più aggressivi nell'identificare le macchine virtuali sottoutilizzate, è possibile modificare la regola di utilizzo della CPU in base alla sottoscrizione.

## <a name="automation"></a>Automazione
Automazione è disponibile a livello generale in Azure per enti pubblici.

Per altre informazioni, vedere la [documentazione pubblica di Automazione](../automation/automation-intro.md).

## <a name="azure-migrate"></a>Azure Migrate

Azure Migrate è disponibile a livello generale in Azure per enti pubblici.

Per ulteriori informazioni, vedere [Azure migrate documentazione](../migrate/migrate-overview.md).

### <a name="variations"></a>Variazioni
Le seguenti funzionalità di Azure Migrate non sono attualmente disponibili in Azure per enti pubblici:

* La funzionalità di visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici perché Azure Migrate dipende da Mapping dei servizi per la visualizzazione delle dipendenze che attualmente non è disponibile in Azure per enti pubblici.
* È possibile creare solo valutazioni per Azure per enti pubblici come aree di destinazione e usando le offerte di Azure per enti pubblici.

## <a name="backup"></a>Backup
Backup è disponibile a livello generale in Azure per enti pubblici.

Per altre informazioni, vedere [Azure Government Backup](documentation-government-services-backup.md) (Backup di Azure per enti pubblici).

## <a name="policy"></a>Policy
Il criterio è disponibile a livello generale in Azure per enti pubblici.

Per altre informazioni, vedere [Criteri di Azure](../governance/policy/overview.md).

## <a name="site-recovery"></a>Site Recovery
Azure Site Recovery è disponibile a livello generale in Azure per enti pubblici.

Per ulteriori informazioni, vedere [Site Recovery documentazione commerciale](../site-recovery/site-recovery-overview.md).

### <a name="variations"></a>Variazioni
Le funzionalità di Site Recovery seguenti non sono attualmente disponibili in Azure per enti pubblici:
* Notifica tramite posta elettronica

| Site Recovery | Classico | Gestione risorse |
| --- | --- | --- |
| VMware/fisico  | GA | GA |
| Hyper-V | GA | GA |
| Da sito a sito | GA | GA |

Gli URL per Site Recovery sono diversi in Azure per enti pubblici:

| Azure Public | Azure Government | Note |
| --- | --- | --- |
| \*.hypervrecoverymanager.windowsazure.com | \*.hypervrecoverymanager.windowsazure.us | Accesso al servizio Site Recovery |
| \*.backup.windowsazure.com  | \*.backup.windowsazure.us | Accesso al servizio di protezione |
| \*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net | Per l'archiviazione di snapshot di VM |
| https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | Per scaricare MySQL |

## <a name="monitor"></a>Monitorare
Monitoraggio di Azure è disponibile a livello generale in Azure per enti pubblici.

Per ulteriori informazioni, vedere [monitorare la documentazione commerciale](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview).

### <a name="variations"></a>Variazioni
Le sezioni seguenti illustrano in dettaglio le differenze e le soluzioni alternative per le funzionalità di monitoraggio di Azure in Azure per enti pubblici:

#### <a name="action-groups"></a>Gruppi di azioni
I gruppi di azioni sono disponibili a livello generale in Azure per enti pubblici senza alcuna differenza rispetto a Azure commerciale.   

#### <a name="activity-log-alerts"></a>Avvisi dei log attività
Gli avvisi del log attività sono disponibili a livello generale in Azure per enti pubblici senza alcuna differenza rispetto a Azure commerciale.

#### <a name="alerts-experience"></a>Esperienza degli avvisi
L'esperienza dell'interfaccia utente degli avvisi unificati è disponibile per gli avvisi relativi a metriche e log in Azure per enti pubblici.

#### <a name="autoscale"></a>Autoscale
La scalabilità automatica è disponibile a livello generale in Azure per enti pubblici.

Se si usano le chiamate PowerShell/ARM/REST per specificare le impostazioni, impostare "location" della scalabilità automatica su "US gov Virginia" o "US gov Iowa". La risorsa di destinazione per la scalabilità automatica può esistere in qualsiasi area. Di seguito è riportato un esempio di impostazione:

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "1"
$rule2 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "2"
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1, $rule2 -Name "MyProfile"
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri https://example.com?mytoken=mytokenvalue
$notification1= New-AzAutoscaleNotification -CustomEmails myname@company.com -SendEmailToSubscriptionAdministrator -SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
Add-AzAutoscaleSetting -Location "USGov Virginia" -Name "MyScaleVMSSSetting" -ResourceGroup sdubeys-usgv -TargetResourceId /subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Web/serverFarms/ServerFarm1 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Se si è interessati all'implementazione della scalabilità automatica sulle risorse, usare le chiamate PowerShell/ARM/REST per specificare le impostazioni.

Per ulteriori informazioni sull'utilizzo di PowerShell, vedere la [documentazione pubblica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples#create-and-manage-autoscale-settings).

#### <a name="metrics"></a>Metriche
Le metriche sono disponibili a livello generale in Azure per enti pubblici. Tuttavia, le metriche multidimensionali sono supportate solo tramite l'API REST. La funzionalità di [visualizzazione delle metriche multidimensionali](../azure-monitor/platform/metrics-charts.md) è in anteprima nel portale di Azure per enti pubblici.

#### <a name="metric-alerts"></a>Avvisi delle metriche
La prima generazione di avvisi di metrica è disponibile a livello generale in Azure per enti pubblici e in Azure commerciale. La prima generazione è chiamata *Alerts (classica)* . È ora disponibile anche la seconda generazione di avvisi delle metriche (denominata anche [esperienza di avvisi unificati](../azure-monitor/platform/alerts-overview.md)), ma con un set ridotto di provider di risorse [rispetto al cloud pubblico](../azure-monitor/platform/alerts-metric-near-real-time.md). Altri verranno aggiunti nel tempo. 

Le risorse attualmente supportate nell'esperienza degli avvisi di seconda generazione sono:
- Microsoft.ApiManagement/service
- Microsoft.Cache/redis
- Microsoft.Compute/virtualMachines
- Microsoft.DBforMySQL/servers
- Microsoft.DBforPostgreSQL/servers
- Microsoft.DBforMariaDB/servers
- Microsoft.Devices/IotHubs
- Microsoft.EventGrid/domains
- Microsoft.EventGrid/topics
- Microsoft.EventHub/clusters
- Microsoft.EventHub/namespaces
- Microsoft. Insights/Components
- Microsoft.Network/dnsZones
- Microsoft.Network/loadBalancers
- Microsoft. Network/natGateways
- Microsoft. Network/privateEndpoints
- Microsoft. Network/privateLinkServices
- Microsoft.Network/trafficManagerProfiles
- Microsoft.OperationalInsights/workspaces
- Microsoft.PowerBIDedicated/capacities
- Microsoft.Relay/namespaces
- Microsoft.ServiceBus/namespaces
- Microsoft.Sql/managedInstances
- Microsoft.Sql/servers/databases
- Microsoft.Sql/servers/elasticPools
- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft.Storage/storageAccounts/fileServices
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices
- Microsoft.Web/hostingEnvironments/multiRolePools
- Microsoft.Web/hostingEnvironments/workerPools
- Microsoft.Web/serverfarms
- Microsoft.Web/sites
- Microsoft.Web/sites/slots

È comunque possibile usare gli [avvisi classici](../azure-monitor/platform/alerts-classic.overview.md) per le risorse non ancora disponibili nella seconda generazione di avvisi. 

Quando si usano le chiamate di PowerShell/ARM/REST per creare avvisi delle metriche, sarà necessario impostare "location" dell'avviso della metrica su "US gov Virginia" o "US gov Iowa". Di seguito è riportato un esempio di impostazione:

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "USGov Virginia" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Per ulteriori informazioni sull'utilizzo di PowerShell, vedere la [documentazione pubblica](../azure-monitor/platform/powershell-quickstart-samples.md).

## <a name="application-insights"></a>Application Insights

> [!NOTE]
> Il monitoraggio dell'agente senza codice/basato sull'estensione per i servizi di app Azure **non è attualmente supportato**. Non appena questa funzionalità diventa disponibile, questo articolo verrà aggiornato.

Questa sezione descrive la configurazione aggiuntiva necessaria per usare Application Insights in Azure per enti pubblici. Per ulteriori informazioni su monitoraggio di Azure e Application Insights estrarre la [documentazione completa](https://docs.microsoft.com/azure/azure-monitor/overview).

### <a name="enable-application-insights-for-aspnet--aspnet-core-with-visual-studio"></a>Abilitare Application Insights per ASP.NET & ASP.NET Core con Visual Studio

Attualmente, per i clienti di Azure per enti pubblici, l'unico modo per abilitare Application Insights tramite il pulsante di **telemetria tradizionale per l'aggiunta di Application Insights** in Visual Studio richiede una piccola soluzione alternativa manuale. I clienti che riscontrano il problema associato potrebbero visualizzare messaggi di errore come _"non è presente alcuna sottoscrizione di Azure associata a questo account_ oppure _" la sottoscrizione selezionata non supporta Application Insights_ anche se il provider di risorse `microsoft.insights` ha lo stato registrato per la sottoscrizione. Per attenuare questo problema, è necessario eseguire i passaggi seguenti:

1. Passa a Visual Studio per [la destinazione del cloud di Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-vs).

2. Crea (o se già esistente) la variabile di ambiente utente per AzureGraphApiVersion come indicato di seguito: per creare una variabile di ambiente utente, passare a **Pannello di controllo** > **sistema** > **impostazioni di sistema avanzate** > **variabili di ambiente** **Avanzate** > .

    `Variable name: AzureGraphApiVersion` `Variable value: 2014-04-01`

3. Apportare le modifiche appropriate all'endpoint Application Insights SDK per [ASP.NET](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#net-with-applicationinsightsconfig) o [ASP.NET Core](#aspnet-core) a seconda del tipo di progetto.

### <a name="snapshot-debugger"></a>Debugger di snapshot

Snapshot Debugger è ora disponibile per i clienti di Azure per enti pubblici. Per usare Snapshot Debugger l'unico prerequisito aggiuntivo è assicurarsi che si stia usando [snapshot Collector versione 1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5-pre-1906.403) o successiva. Seguire semplicemente la documentazione di [snapshot debugger](https://docs.microsoft.com/azure/azure-monitor/app/snapshot-debugger)standard.

### <a name="sdk-endpoint-modifications"></a>Modifiche all'endpoint SDK

Per inviare dati da Application Insights all'area di Azure per enti pubblici, sarà necessario modificare gli indirizzi endpoint predefiniti usati dagli SDK Application Insights. Ogni SDK richiede modifiche leggermente diverse.

### <a name="net-with-applicationinsightsconfig"></a>.NET con applicationinsights. config

> [!NOTE]
> Il file applicationinsights. config viene sovrascritto automaticamente ogni volta che viene eseguito un aggiornamento dell'SDK. Dopo l'esecuzione di un aggiornamento dell'SDK, assicurarsi di immettere nuovamente i valori dell'endpoint specifico dell'area.

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://quickpulse.applicationinsights.us/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.applicationinsights.us/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

### <a name="aspnet-core"></a>ASP.NET Core

Modificare il file appSettings. JSON nel progetto come indicato di seguito per modificare l'endpoint principale:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "https://dc.applicationinsights.us/v2/track"
    }
  }
```

I valori per la metrica dinamica e l'endpoint di query del profilo possono essere impostati solo tramite codice. Per eseguire l'override dei valori predefiniti per tutti i valori dell'endpoint tramite codice, apportare le modifiche seguenti nel metodo `ConfigureServices` del file di `Startup.cs`:

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel; //place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="https://quickpulse.applicationinsights.us/QuickPulseService.svc");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });

    //place in ConfigureServices method. If present, place this prior to   services.AddApplicationInsightsTelemetry("instrumentation key");
```

### <a name="azure-functions"></a>Funzioni di Azure

Installare i pacchetti seguenti nel progetto per le funzioni:

- Microsoft. ApplicationInsights versione 2.10.0
- Microsoft. ApplicationInsights. PerfCounterCollector versione 2.10.0
- Microsoft. ApplicationInsights. WindowsServer. TelemetryChannel versione 2.10.0

Inoltre, aggiungere o modificare il codice di avvio per l'applicazione di funzione:

```csharp
[assembly: FunctionsStartup(typeof(Example.Startup))]
namespace Example
{
  class Startup : FunctionsStartup
  {
      public override void Configure(IFunctionsHostBuilder builder)
      {
          var quickPulseFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryModule) && 
                                               sd.ImplementationType == typeof(QuickPulseTelemetryModule));
          if (quickPulseFactory != null)
          {
              builder.Services.Remove(quickPulseFactory);
          }

          var appIdFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(IApplicationIdProvider));
          if (appIdFactory != null)
          {
              builder.Services.Remove(appIdFactory);
          }

          var channelFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryChannel));
          if (channelFactory != null)
          {
              builder.Services.Remove(channelFactory);
          }

          builder.Services.AddSingleton<ITelemetryModule, QuickPulseTelemetryModule>(_ =>
              new QuickPulseTelemetryModule
              {
                  QuickPulseServiceEndpoint = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
              });

          builder.Services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

          builder.Services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });
      }
  }
}
```

### <a name="java"></a>Java

Modificare il file applicationinsights. XML per modificare l'indirizzo dell'endpoint predefinito.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

Modificare il file di `application.properties` e aggiungere:

```yaml
azure.application-insights.channel.in-process.endpoint-address= https://dc.applicationinsights.us/v2/track
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "https://dc.applicationinsights.us/v2/track"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"; //live metrics
appInsights.Configuration.start();
```

Gli endpoint possono anche essere configurati tramite le variabili di ambiente:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "https://dc.applicationinsights.us/api/profiles/{0}/appId"
Live Metrics Endpoint: "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
```

### <a name="javascript"></a>JavaScript

```javascript
<script type="text/javascript">
   var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){
      function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/next/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t
   }({
      instrumentationKey:"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
      endpointUrl: "https://dc.applicationinsights.us/v2/track"
   });

   window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="firewall-exceptions"></a>Eccezioni del firewall

Il servizio applicazione Azure Insights usa diversi indirizzi IP. Potrebbe essere necessario conoscere questi indirizzi se l'app che si sta monitorando è ospitata dietro un firewall.

> [!NOTE]
> Anche se questi indirizzi sono statici, a volte potrebbe essere necessari modificarli. Tutto il traffico Application Insights rappresenta il traffico in uscita, ad eccezione del monitoraggio della disponibilità e dei webhook, che richiedono regole del firewall in ingresso.

### <a name="outgoing-ports"></a>Porte in uscita
È necessario aprire alcune porte in uscita nel firewall del server per consentire ad Application Insights SDK e/o a Status Monitor di inviare dati al portale:

| Scopo | URL | IP | Porte |
| --- | --- | --- | --- |
| Telemetria | dc.applicationinsights.us | 23.97.4.113 | 443 |

## <a name="azure-monitor-logs"></a>Log di Monitoraggio di Azure
Log di monitoraggio di Azure è disponibile a livello generale in Azure per enti pubblici.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="variations"></a>Variazioni

* Le soluzioni disponibili in Azure per enti pubblici includono:
  * [Monitoraggio prestazioni rete (NPM)](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/) -NPM è una soluzione di monitoraggio di rete basata sul cloud per ambienti cloud pubblico e ibrido. Le organizzazioni usano NPM per monitorare la disponibilità di rete in ambienti locali e cloud.  Monitoraggio endpoint: una sottofunzionalità di NPM, monitora la connettività di rete alle applicazioni.

Le funzionalità e le soluzioni dei log di monitoraggio di Azure seguenti non sono attualmente disponibili in Azure per enti pubblici.

* Soluzioni in anteprima in Microsoft Azure, tra cui:
  * Elenco dei servizi
  * Soluzione di analisi di aggiornamento di Windows 10
  * Soluzione Application Insights
  * Soluzione di analisi del gruppo di sicurezza di rete di Azure
  * Soluzione Log Analytics per Automazione di Azure
  * Soluzione Log Analytics per l'insieme di credenziali delle chiavi
* Soluzioni e funzionalità che richiedono aggiornamenti del software locale, tra cui:
  * Soluzione Surface Hub
* Funzionalità disponibili in anteprima in Azure globale, tra cui:
  * Esportazione di dati in Power BI
* Metriche e diagnostica di Azure

Gli URL per i log di monitoraggio di Azure sono diversi in Azure per enti pubblici:

| Azure Public | Azure Government | Note |
| --- | --- | --- |
| mms.microsoft.com |oms.microsoft.us |Portale per aree di lavoro Log Analytics |
| *workspaceId*.ods.opinsights.azure.com |*workspaceId*.ods.opinsights.azure.us |[API di raccolta dati](../azure-monitor/platform/data-collector-api.md) |
| \*.ods.opinsights.azure.com |\*.ods.opinsights.azure.us |Comunicazione tra gli agenti: [configurazione delle impostazioni del firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.oms.opinsights.azure.com |\*.oms.opinsights.azure.us |Comunicazione tra gli agenti: [configurazione delle impostazioni del firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.blob.core.windows.net |\*.blob.core.usgovcloudapi.net |Comunicazione tra gli agenti: [configurazione delle impostazioni del firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| portal.loganalytics.io |portal.loganalytics.us |Portale Advanced Analytics- [configurazione delle impostazioni del firewall](../azure-monitor/log-query/portals.md) |
| api.loganalytics.io |api.loganalytics.us |Portale Advanced Analytics- [configurazione delle impostazioni del firewall](../azure-monitor/log-query/portals.md) |
| docs.loganalytics.io |docs.loganalytics.us |Portale Advanced Analytics- [configurazione delle impostazioni del firewall](../azure-monitor/log-query/portals.md) |
| \*.azure-automation.net |\*. azure-automation.us |Automazione di Azure- [configurazione delle impostazioni del firewall](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) |
| N/D | *. usgovtrafficmanager.net | Gestione traffico di Azure- [configurazione delle impostazioni del firewall](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) |

Il monitoraggio di Azure seguente registra le funzionalità con un comportamento diverso in Azure per enti pubblici:

* Per connettere il gruppo di gestione di System Center Operations Manager ai log di monitoraggio di Azure, è necessario scaricare e importare i Management Pack aggiornati.
  + System Center Operations Manager 2016
    1. Installare l'[aggiornamento cumulativo 2 per System Center Operations Manager 2016](https://support.microsoft.com/help/3209591).
    2. Importare i management pack inclusi nell'aggiornamento cumulativo 2 in Operations Manager. Per informazioni su come importare un Management Pack da un disco, vedere [Come importare un Management Pack di Operations Manager](https://technet.microsoft.com/library/hh212691.aspx).
    3. Per connettersi Operations Manager ai log di monitoraggio di Azure, seguire i passaggi descritti in [connettere Operations Manager ai log di monitoraggio di Azure](../azure-monitor/platform/om-agents.md).
  + System Center Operations Manager 2012 R2 UR3 (o versione successiva)/Operations Manager 2012 SP1 UR7 (o versione successiva)
    1. Scaricare e salvare i [Management Pack aggiornati](https://go.microsoft.com/fwlink/?LinkId=828749).
    2. Decomprimere il file scaricato.
    3. Importare i Management Pack in Operations Manager. Per informazioni su come importare un Management Pack da un disco, vedere [Come importare un Management Pack di Operations Manager](https://technet.microsoft.com/library/hh212691.aspx).
    4. Per connettersi Operations Manager ai log di monitoraggio di Azure, seguire i passaggi descritti in [connettere Operations Manager ai log di monitoraggio di Azure](../azure-monitor/platform/om-agents.md).

* Per altre informazioni sull'uso di gruppi di computer da Configuration Manager, vedere [connettere Configuration Manager a monitoraggio di Azure](../azure-monitor/platform/collect-sccm.md).

### <a name="frequently-asked-questions"></a>Domande frequenti
* È possibile eseguire la migrazione dei dati dai log di monitoraggio di Azure in Microsoft Azure ad Azure per enti pubblici?
  * No. Non è possibile spostare dati o l'area di lavoro da Microsoft Azure ad Azure per enti pubblici.
* È possibile passare tra le aree di lavoro Microsoft Azure e Azure per enti pubblici dal portale di Operations Management Suite?
  * No. I portali per Microsoft Azure e Azure per enti pubblici sono separati e non condividono informazioni.

Per altre informazioni, vedere la [documentazione pubblica relativa ai log di monitoraggio di Azure](../log-analytics/log-analytics-overview.md).

## <a name="scheduler"></a>Scheduler
Per informazioni su questo servizio e su come usarlo, vedere la [documentazione dell'utilità di pianificazione di Azure](../scheduler/index.md).

## <a name="azure-portal"></a>Portale di Azure
È possibile accedere al portale di Azure per enti pubblici [qui](https://portal.azure.us).

## <a name="azure-resource-manager"></a>Azure Resource Manager
Per informazioni su questo servizio e su come usarlo, vedere [Azure Resource Manager documentazione](../azure-resource-manager/management/overview.md).

## <a name="next-steps"></a>Passaggi successivi
* Sottoscrivere il [Blog di Azure per enti pubblici](https://blogs.msdn.microsoft.com/azuregov/)
* Ottenere informazioni su Stack Overflow usando [Azure-gov](https://stackoverflow.com/questions/tagged/azure-gov)
* Inviare commenti e suggerimenti o richiedere nuove funzionalità tramite il [Forum dei commenti di Azure per enti pubblici](https://feedback.azure.com/forums/558487-azure-government)
