---
title: Schema delle risorse di Application Insights basate sull'area di lavoro in Monitoraggio di Azure
description: Informazioni sul nuovo schema e struttura della tabella per monitoraggio di Azure Application Insights risorse basate sull'area di lavoro.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/09/2020
ms.openlocfilehash: 21f387a87224615ea6afbdce620c56e3ad2cc6ea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83210541"
---
# <a name="workspace-based-resource-changes-preview"></a>Modifiche alle risorse basate sull'area di lavoro (anteprima)

Prima dell'introduzione delle [risorse Application Insights basate sull'area di lavoro](create-workspace-resource.md), i dati Application Insights sono stati archiviati separatamente dagli altri dati di log in monitoraggio di Azure. Entrambi sono basati su Esplora dati di Azure e usano lo stesso linguaggio di query kusto (KQL). Questa operazione è descritta in [log in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs).

Con i dati delle risorse Application Insights basati sull'area di lavoro vengono archiviati in un'area di lavoro Log Analytics con altri dati di monitoraggio e dati dell'applicazione. Questo semplifica la configurazione consentendo di analizzare più facilmente i dati tra più soluzioni e di sfruttare le funzionalità delle aree di lavoro.

## <a name="table-structure"></a>Struttura della tabella

| Nome tabella legacy | Nome nuova tabella | Descrizione |
|:---|:---|:---|
| availabilityResults | AppAvailabilityResults |  Dati di riepilogo da test di disponibilità.|
| browserTimings | AppBrowserTimings | Dati sulle prestazioni del client, ad esempio il tempo impiegato per elaborare i dati in ingresso.|
| dipendenze | AppDependencies | Chiamate dall'applicazione ad altri componenti (inclusi componenti esterni) registrati tramite TrackDependency(), ad esempio chiamate ad API REST, database o un file system.  |
| customEvents | AppEvents | Eventi personalizzati creati dall'applicazione. |
| customMetrics | AppMetrics | Metriche personalizzate create dall'applicazione. |
| pageViews | AppPageViews| Dati relativi a ogni visualizzazione del sito Web con le informazioni del browser. |
| performanceCounters | AppPerformanceCounters | Misurazioni delle prestazioni dalle risorse di calcolo che supportano l'applicazione, ad esempio, i contatori delle prestazioni di Windows. |
| requests | AppRequests | Richieste ricevute dall'applicazione. Ad esempio, viene registrato un record di richiesta separato per ogni richiesta HTTP ricevuta dall'app Web.  |
| eccezioni | AppSystemEvents | Eccezioni generate dal runtime dell'applicazione, acquisisce sia le eccezioni lato server sia quelle lato client (browser). |
| traces | AppTraces | Log dettagliati (traces) emessi tramite il codice dell'applicazione/framework di registrazione registrati tramite TrackTrace(). |

## <a name="table-schemas"></a>Schemi di tabella

Nelle sezioni seguenti viene illustrato il mapping tra i nomi di proprietà classici e i nuovi nomi di proprietà Application Insights basati sull'area di lavoro.  Utilizzare queste informazioni per convertire le query utilizzando le tabelle legacy.

La maggior parte delle colonne ha lo stesso nome con maiuscole e minuscole diverse. Poiché KQL fa distinzione tra maiuscole e minuscole, sarà necessario modificare il nome di ogni colonna insieme ai nomi di tabella nelle query esistenti. Vengono evidenziate le colonne con modifiche oltre alle maiuscole. È comunque possibile usare le query di Application Insights classiche all'interno del riquadro **log** della risorsa Application Insights, anche se si tratta di una risorsa basata sull'area di lavoro. I nuovi nomi di proprietà sono necessari per l'esecuzione di query dall'interno del contesto dell'esperienza dell'area di lavoro Log Analytics.

### <a name="appavailabilityresults"></a>AppAvailabilityResults

Tabella legacy: disponibilità

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_GUID risorsa|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|Clientos|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|TipoClient|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dinamico|Proprietà|Dinamico|
|customMeasurements|dinamico|Misurazioni|Dinamico|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|posizione|string|Location|string|
|message|string|Messaggio|string|
|name|string|Nome|stringa|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|size|real|Dimensione|real|
|esito positivo|string|Operazione completata|Bool|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appbrowsertimings"></a>AppBrowserTimings

Tabella legacy: browserTimings

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_GUID risorsa|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|Clientos|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|TipoClient|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dinamico|Proprietà|Dinamico|
|customMeasurements|dinamico|Misurazioni|Dinamico|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|name|string|Nome|Datetime|
|networkDuration|real|NetworkDurationMs|real|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|processingDuration|real|ProcessingDurationMs|real|
|receiveDuration|real|ReceiveDurationMs|real|
|sdkVersion|string|SdkVersion|string|
|sendDuration|real|SendDurationMs|real|
|session_Id|string|SessionId|string|
|timestamp|Datetime|TimeGenerated|Datetime|
|totalDuration|real|TotalDurationMs|real|
|url|string|URL|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appdependencies"></a>AppDependencies

Tabella legacy: dipendenze

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_GUID risorsa|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|Clientos|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|TipoClient|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dinamico|Proprietà|Dinamico|
|customMeasurements|dinamico|Misurazioni|Dinamico|
|data|string|Data|string|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|name|string|Nome|stringa|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|resultCode|string|ResultCode|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|esito positivo|string|Operazione completata|Bool|
|target|string|Destinazione|string|
|timestamp|Datetime|TimeGenerated|Datetime|
|tipo|string|TipoDipendenza|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appevents"></a>AppEvents

Tabella legacy: customEvents

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_GUID risorsa|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|Clientos|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|TipoClient|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dinamico|Proprietà|Dinamico|
|customMeasurements|dinamico|Misurazioni|Dinamico|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|name|string|Nome|stringa|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appmetrics"></a>AppMetrics

Tabella legacy: customMetrics

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_GUID risorsa|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|Clientos|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|TipoClient|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dinamico|Proprietà|Dinamico|
|iKey|string|IKey|string|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|name|string|Nome|stringa|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|
|Valore|real|rimosso||
|valueCount|INT|ValueCount|INT|
|valueMax|real|ValueMax|real|
|valueMin|real|ValueMin|real|
|valueStdDev|real|ValueStdDev|real|
|Valore|real|Valore|real|

### <a name="apppageviews"></a>AppPageViews

Tabella legacy: visualizzazioni

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_GUID risorsa|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|Clientos|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|TipoClient|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dinamico|Proprietà|Dinamico|
|customMeasurements|dinamico|Misurazioni|Dinamico|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|name|string|Nome|stringa|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|Datetime|TimeGenerated|Datetime|
|url|string|URL|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appperformancecounters"></a>AppPerformanceCounters

Tabella legacy: performanceCounters

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_GUID risorsa|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|category|string|Category|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|Clientos|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|TipoClient|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|counter|string|rimosso||
|customDimensions|dinamico|Proprietà|Dinamico|
|iKey|string|IKey|string|
|instance|string|Istanza|string|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|name|string|Nome|stringa|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|
|Valore|real|Value|real|

### <a name="apprequests"></a>AppRequests

Tabella legacy: richieste

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_GUID risorsa|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|Clientos|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|TipoClient|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dinamico|Proprietà|Dinamico|
|customMeasurements|dinamico|Misurazioni|Dinamico|
|duration|real|DurationMs|Real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|name|string|Nome|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|resultCode|string|ResultCode|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|source|string|Source (Sorgente)|string|
|esito positivo|string|Operazione completata|Bool|
|timestamp|Datetime|TimeGenerated|Datetime|
|url|string|URL|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appsystemevents"></a>AppSystemEvents

Tabella legacy: eccezioni

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_GUID risorsa|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|assembly|string|Assembly|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|Clientos|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|TipoClient|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dinamico|Proprietà|dinamico|
|customMeasurements|dinamico|Misurazioni|dinamico|
|dettagli|dinamico|Dettagli|dinamico|
|handledAt|string|HandledAt|string|
|iKey|string|IKey|string|
|innermostAssembly|string|InnermostAssembly|string|
|innermostMessage|string|InnermostMessage|string|
|innermostMethod|string|InnermostMethod|string|
|innermostType|string|InnermostType|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|message|string|Messaggio|string|
|method|string|Metodo|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|outerAssembly|string|OuterAssembly|string|
|outerMessage|string|OuterMessage|string|
|outerMethod|string|OuterMethod|string|
|outerType|string|OuterType|string|
|problemId|string|ProblemId|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|severityLevel|INT|SeverityLevel|INT|
|timestamp|Datetime|TimeGenerated|Datetime|
|tipo|stringa|ExceptionType|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="apptraces"></a>AppTraces

Tabella legacy: tracce

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_GUID risorsa|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|Clientos|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|TipoClient|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dinamico|Proprietà|dinamico|
|customMeasurements|dinamico|Misurazioni|dinamico|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|message|string|Messaggio|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|severityLevel|INT|SeverityLevel|INT|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

## <a name="next-steps"></a>Passaggi successivi

* [Esplorare le metriche](../../azure-monitor/platform/metrics-charts.md)
* [Scrivere query di Analisi](../../azure-monitor/app/analytics.md)