---
title: Schema delle risorse di Application Insights basate sull'area di lavoro in Monitoraggio di Azure
description: Informazioni sul nuovo schema e struttura della tabella per monitoraggio di Azure Application Insights risorse basate sull'area di lavoro.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/09/2020
ms.openlocfilehash: 1d7275c928b4d25e200a3a8d3d690c7575c056e7
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323180"
---
# <a name="workspace-based-resource-changes-preview"></a>Modifiche alle risorse basate sull'area di lavoro (anteprima)

Prima dell'introduzione delle [risorse Application Insights basate sull'area di lavoro](create-workspace-resource.md), i dati Application Insights sono stati archiviati separatamente dagli altri dati di log in monitoraggio di Azure. Entrambi sono basati su Esplora dati di Azure e usano lo stesso linguaggio di query kusto (KQL). Questa operazione è descritta in [log in monitoraggio di Azure](../platform/data-platform-logs.md).

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
|appId|Stringa|\_GUID risorsa|Stringa|
|application_Version|Stringa|AppVersion|Stringa|
|appName|Stringa|\_ResourceId|Stringa|
|client_Browser|Stringa|ClientBrowser|Stringa|
|client_City|Stringa|ClientCity|Stringa|
|client_CountryOrRegion|Stringa|ClientCountryOrRegion|Stringa|
|client_IP|Stringa|ClientIP|Stringa|
|client_Model|Stringa|ClientModel|Stringa|
|client_OS|Stringa|Clientos|Stringa|
|client_StateOrProvince|Stringa|ClientStateOrProvince|Stringa|
|client_Type|Stringa|TipoClient|Stringa|
|cloud_RoleInstance|Stringa|AppRoleInstance|Stringa|
|cloud_RoleName|Stringa|AppRoleName|Stringa|
|customDimensions|dinamico|Proprietà|Dinamico|
|customMeasurements|dinamico|Misurazioni|Dinamico|
|duration|real|DurationMs|real|
|`id`|Stringa|`Id`|Stringa|
|iKey|Stringa|IKey|Stringa|
|itemCount|INT|ItemCount|INT|
|itemId|Stringa|\_ItemId|Stringa|
|itemType|Stringa|Type|string|
|posizione|Stringa|Location|Stringa|
|message|string|Message|Stringa|
|name|Stringa|Nome|stringa|
|operation_Id|Stringa|OperationId|Stringa|
|operation_Name|Stringa|OperationName|Stringa|
|operation_ParentId|Stringa|OperationParentId|Stringa|
|operation_SyntheticSource|Stringa|OperationSyntheticSource|Stringa|
|performanceBucket|Stringa|PerformanceBucket|Stringa|
|sdkVersion|Stringa|SdkVersion|Stringa|
|session_Id|Stringa|SessionId|Stringa|
|size|real|Dimensione|real|
|esito positivo|Stringa|Operazione completata|Bool|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|Stringa|UserAccountId|Stringa|
|user_AuthenticatedId|Stringa|UserAuthenticatedId|Stringa|
|user_Id|Stringa|UserId|Stringa|

### <a name="appbrowsertimings"></a>AppBrowserTimings

Tabella legacy: browserTimings

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|Stringa|\_GUID risorsa|Stringa|
|application_Version|Stringa|AppVersion|Stringa|
|appName|Stringa|\_ResourceId|Stringa|
|client_Browser|Stringa|ClientBrowser|Stringa|
|client_City|Stringa|ClientCity|Stringa|
|client_CountryOrRegion|Stringa|ClientCountryOrRegion|Stringa|
|client_IP|Stringa|ClientIP|Stringa|
|client_Model|Stringa|ClientModel|Stringa|
|client_OS|Stringa|Clientos|Stringa|
|client_StateOrProvince|Stringa|ClientStateOrProvince|Stringa|
|client_Type|Stringa|TipoClient|Stringa|
|cloud_RoleInstance|Stringa|AppRoleInstance|Stringa|
|cloud_RoleName|Stringa|AppRoleName|Stringa|
|customDimensions|dinamico|Proprietà|Dinamico|
|customMeasurements|dinamico|Misurazioni|Dinamico|
|iKey|Stringa|IKey|Stringa|
|itemCount|INT|ItemCount|INT|
|itemId|Stringa|\_ItemId|Stringa|
|itemType|Stringa|Type|Stringa|
|name|string|Nome|Datetime|
|networkDuration|real|NetworkDurationMs|real|
|operation_Id|Stringa|OperationId|Stringa|
|operation_Name|Stringa|OperationName|Stringa|
|operation_ParentId|Stringa|OperationParentId|Stringa|
|operation_SyntheticSource|Stringa|OperationSyntheticSource|Stringa|
|performanceBucket|Stringa|PerformanceBucket|Stringa|
|processingDuration|real|ProcessingDurationMs|real|
|receiveDuration|real|ReceiveDurationMs|real|
|sdkVersion|Stringa|SdkVersion|Stringa|
|sendDuration|real|SendDurationMs|real|
|session_Id|Stringa|SessionId|Stringa|
|timestamp|Datetime|TimeGenerated|Datetime|
|totalDuration|real|TotalDurationMs|real|
|url|string|URL|Stringa|
|user_AccountId|Stringa|UserAccountId|Stringa|
|user_AuthenticatedId|Stringa|UserAuthenticatedId|Stringa|
|user_Id|Stringa|UserId|Stringa|

### <a name="appdependencies"></a>AppDependencies

Tabella legacy: dipendenze

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|Stringa|\_GUID risorsa|Stringa|
|application_Version|Stringa|AppVersion|Stringa|
|appName|Stringa|\_ResourceId|Stringa|
|client_Browser|Stringa|ClientBrowser|Stringa|
|client_City|Stringa|ClientCity|Stringa|
|client_CountryOrRegion|Stringa|ClientCountryOrRegion|Stringa|
|client_IP|Stringa|ClientIP|Stringa|
|client_Model|Stringa|ClientModel|Stringa|
|client_OS|Stringa|Clientos|Stringa|
|client_StateOrProvince|Stringa|ClientStateOrProvince|Stringa|
|client_Type|Stringa|TipoClient|Stringa|
|cloud_RoleInstance|Stringa|AppRoleInstance|Stringa|
|cloud_RoleName|Stringa|AppRoleName|Stringa|
|customDimensions|dinamico|Proprietà|Dinamico|
|customMeasurements|dinamico|Misurazioni|Dinamico|
|data|Stringa|Data|Stringa|
|duration|real|DurationMs|real|
|`id`|Stringa|`Id`|Stringa|
|iKey|Stringa|IKey|Stringa|
|itemCount|INT|ItemCount|INT|
|itemId|Stringa|\_ItemId|Stringa|
|itemType|Stringa|Type|string|
|name|string|Nome|stringa|
|operation_Id|Stringa|OperationId|Stringa|
|operation_Name|Stringa|OperationName|Stringa|
|operation_ParentId|Stringa|OperationParentId|Stringa|
|operation_SyntheticSource|Stringa|OperationSyntheticSource|Stringa|
|performanceBucket|Stringa|PerformanceBucket|Stringa|
|resultCode|Stringa|ResultCode|Stringa|
|sdkVersion|Stringa|SdkVersion|Stringa|
|session_Id|Stringa|SessionId|Stringa|
|esito positivo|Stringa|Operazione completata|Bool|
|target|Stringa|Destinazione|Stringa|
|timestamp|Datetime|TimeGenerated|Datetime|
|type|Stringa|TipoDipendenza|Stringa|
|user_AccountId|Stringa|UserAccountId|Stringa|
|user_AuthenticatedId|Stringa|UserAuthenticatedId|Stringa|
|user_Id|Stringa|UserId|Stringa|

### <a name="appevents"></a>AppEvents

Tabella legacy: customEvents

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|Stringa|\_GUID risorsa|Stringa|
|application_Version|Stringa|AppVersion|Stringa|
|appName|Stringa|\_ResourceId|Stringa|
|client_Browser|Stringa|ClientBrowser|Stringa|
|client_City|Stringa|ClientCity|Stringa|
|client_CountryOrRegion|Stringa|ClientCountryOrRegion|Stringa|
|client_IP|Stringa|ClientIP|Stringa|
|client_Model|Stringa|ClientModel|Stringa|
|client_OS|Stringa|Clientos|Stringa|
|client_StateOrProvince|Stringa|ClientStateOrProvince|Stringa|
|client_Type|Stringa|TipoClient|Stringa|
|cloud_RoleInstance|Stringa|AppRoleInstance|Stringa|
|cloud_RoleName|Stringa|AppRoleName|Stringa|
|customDimensions|dinamico|Proprietà|Dinamico|
|customMeasurements|dinamico|Misurazioni|Dinamico|
|iKey|Stringa|IKey|Stringa|
|itemCount|INT|ItemCount|INT|
|itemId|Stringa|\_ItemId|Stringa|
|itemType|Stringa|Type|Stringa|
|name|string|Nome|stringa|
|operation_Id|Stringa|OperationId|Stringa|
|operation_Name|Stringa|OperationName|Stringa|
|operation_ParentId|Stringa|OperationParentId|Stringa|
|operation_SyntheticSource|Stringa|OperationSyntheticSource|Stringa|
|sdkVersion|Stringa|SdkVersion|Stringa|
|session_Id|Stringa|SessionId|Stringa|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|Stringa|UserAccountId|Stringa|
|user_AuthenticatedId|Stringa|UserAuthenticatedId|Stringa|
|user_Id|Stringa|UserId|Stringa|

### <a name="appmetrics"></a>AppMetrics

Tabella legacy: customMetrics

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|Stringa|\_GUID risorsa|Stringa|
|application_Version|Stringa|AppVersion|Stringa|
|appName|Stringa|\_ResourceId|Stringa|
|client_Browser|Stringa|ClientBrowser|Stringa|
|client_City|Stringa|ClientCity|Stringa|
|client_CountryOrRegion|Stringa|ClientCountryOrRegion|Stringa|
|client_IP|Stringa|ClientIP|Stringa|
|client_Model|Stringa|ClientModel|Stringa|
|client_OS|Stringa|Clientos|Stringa|
|client_StateOrProvince|Stringa|ClientStateOrProvince|Stringa|
|client_Type|Stringa|TipoClient|Stringa|
|cloud_RoleInstance|Stringa|AppRoleInstance|Stringa|
|cloud_RoleName|Stringa|AppRoleName|Stringa|
|customDimensions|dinamico|Proprietà|Dinamico|
|iKey|Stringa|IKey|Stringa|
|itemId|Stringa|\_ItemId|Stringa|
|itemType|Stringa|Type|Stringa|
|name|string|Nome|stringa|
|operation_Id|Stringa|OperationId|Stringa|
|operation_Name|Stringa|OperationName|Stringa|
|operation_ParentId|Stringa|OperationParentId|Stringa|
|operation_SyntheticSource|Stringa|OperationSyntheticSource|Stringa|
|sdkVersion|Stringa|SdkVersion|Stringa|
|session_Id|Stringa|SessionId|Stringa|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|Stringa|UserAccountId|Stringa|
|user_AuthenticatedId|Stringa|UserAuthenticatedId|Stringa|
|user_Id|Stringa|UserId|Stringa|
|value|real|rimosso||
|valueCount|INT|ValueCount|INT|
|valueMax|real|ValueMax|real|
|valueMin|real|ValueMin|real|
|valueStdDev|real|ValueStdDev|real|
|Valore|real|Valore|real|

### <a name="apppageviews"></a>AppPageViews

Tabella legacy: visualizzazioni

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|Stringa|\_GUID risorsa|Stringa|
|application_Version|Stringa|AppVersion|Stringa|
|appName|Stringa|\_ResourceId|Stringa|
|client_Browser|Stringa|ClientBrowser|Stringa|
|client_City|Stringa|ClientCity|Stringa|
|client_CountryOrRegion|Stringa|ClientCountryOrRegion|Stringa|
|client_IP|Stringa|ClientIP|Stringa|
|client_Model|Stringa|ClientModel|Stringa|
|client_OS|Stringa|Clientos|Stringa|
|client_StateOrProvince|Stringa|ClientStateOrProvince|Stringa|
|client_Type|Stringa|TipoClient|Stringa|
|cloud_RoleInstance|Stringa|AppRoleInstance|Stringa|
|cloud_RoleName|Stringa|AppRoleName|Stringa|
|customDimensions|dinamico|Proprietà|Dinamico|
|customMeasurements|dinamico|Misurazioni|Dinamico|
|duration|real|DurationMs|real|
|`id`|Stringa|`Id`|Stringa|
|iKey|Stringa|IKey|Stringa|
|itemCount|INT|ItemCount|INT|
|itemId|Stringa|\_ItemId|Stringa|
|itemType|Stringa|Type|string|
|name|string|Nome|stringa|
|operation_Id|Stringa|OperationId|Stringa|
|operation_Name|Stringa|OperationName|Stringa|
|operation_ParentId|Stringa|OperationParentId|Stringa|
|operation_SyntheticSource|Stringa|OperationSyntheticSource|Stringa|
|performanceBucket|Stringa|PerformanceBucket|Stringa|
|sdkVersion|Stringa|SdkVersion|Stringa|
|session_Id|Stringa|SessionId|Stringa|
|timestamp|Datetime|TimeGenerated|Datetime|
|url|Stringa|URL|Stringa|
|user_AccountId|Stringa|UserAccountId|Stringa|
|user_AuthenticatedId|Stringa|UserAuthenticatedId|Stringa|
|user_Id|Stringa|UserId|Stringa|

### <a name="appperformancecounters"></a>AppPerformanceCounters

Tabella legacy: performanceCounters

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|Stringa|\_GUID risorsa|Stringa|
|application_Version|Stringa|AppVersion|Stringa|
|appName|Stringa|\_ResourceId|Stringa|
|category|string|Categoria|Stringa|
|client_Browser|Stringa|ClientBrowser|Stringa|
|client_City|Stringa|ClientCity|Stringa|
|client_CountryOrRegion|Stringa|ClientCountryOrRegion|Stringa|
|client_IP|Stringa|ClientIP|Stringa|
|client_Model|Stringa|ClientModel|Stringa|
|client_OS|Stringa|Clientos|Stringa|
|client_StateOrProvince|Stringa|ClientStateOrProvince|Stringa|
|client_Type|Stringa|TipoClient|Stringa|
|cloud_RoleInstance|Stringa|AppRoleInstance|Stringa|
|cloud_RoleName|Stringa|AppRoleName|Stringa|
|counter|Stringa|rimosso||
|customDimensions|dinamico|Proprietà|Dinamico|
|iKey|Stringa|IKey|Stringa|
|instance|Stringa|Istanza|Stringa|
|itemId|Stringa|\_ItemId|Stringa|
|itemType|Stringa|Type|Stringa|
|name|string|Nome|stringa|
|operation_Id|Stringa|OperationId|Stringa|
|operation_Name|Stringa|OperationName|Stringa|
|operation_ParentId|Stringa|OperationParentId|Stringa|
|operation_SyntheticSource|Stringa|OperationSyntheticSource|Stringa|
|sdkVersion|Stringa|SdkVersion|Stringa|
|session_Id|Stringa|SessionId|Stringa|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|Stringa|UserAccountId|Stringa|
|user_AuthenticatedId|Stringa|UserAuthenticatedId|Stringa|
|user_Id|Stringa|UserId|Stringa|
|value|real|valore|real|

### <a name="apprequests"></a>AppRequests

Tabella legacy: richieste

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|Stringa|\_GUID risorsa|Stringa|
|application_Version|Stringa|AppVersion|Stringa|
|appName|Stringa|\_ResourceId|Stringa|
|client_Browser|Stringa|ClientBrowser|Stringa|
|client_City|Stringa|ClientCity|Stringa|
|client_CountryOrRegion|Stringa|ClientCountryOrRegion|Stringa|
|client_IP|Stringa|ClientIP|Stringa|
|client_Model|Stringa|ClientModel|Stringa|
|client_OS|Stringa|Clientos|Stringa|
|client_StateOrProvince|Stringa|ClientStateOrProvince|Stringa|
|client_Type|Stringa|TipoClient|Stringa|
|cloud_RoleInstance|Stringa|AppRoleInstance|Stringa|
|cloud_RoleName|Stringa|AppRoleName|Stringa|
|customDimensions|dinamico|Proprietà|Dinamico|
|customMeasurements|dinamico|Misurazioni|Dinamico|
|duration|real|DurationMs|Real|
|`id`|Stringa|`Id`|Stringa|
|iKey|Stringa|IKey|Stringa|
|itemCount|INT|ItemCount|INT|
|itemId|Stringa|\_ItemId|Stringa|
|itemType|Stringa|Type|string|
|name|Stringa|Nome|Stringa|
|operation_Id|Stringa|OperationId|Stringa|
|operation_Name|Stringa|OperationName|Stringa|
|operation_ParentId|Stringa|OperationParentId|Stringa|
|operation_SyntheticSource|Stringa|OperationSyntheticSource|Stringa|
|performanceBucket|Stringa|PerformanceBucket|Stringa|
|resultCode|Stringa|ResultCode|Stringa|
|sdkVersion|Stringa|SdkVersion|Stringa|
|session_Id|Stringa|SessionId|Stringa|
|source|Stringa|Source (Sorgente)|Stringa|
|esito positivo|Stringa|Operazione completata|Bool|
|timestamp|Datetime|TimeGenerated|Datetime|
|url|string|URL|Stringa|
|user_AccountId|Stringa|UserAccountId|Stringa|
|user_AuthenticatedId|Stringa|UserAuthenticatedId|Stringa|
|user_Id|Stringa|UserId|Stringa|

### <a name="appsystemevents"></a>AppSystemEvents

Tabella legacy: eccezioni

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|Stringa|\_GUID risorsa|Stringa|
|application_Version|Stringa|AppVersion|Stringa|
|appName|Stringa|\_ResourceId|Stringa|
|assembly|Stringa|Assembly|Stringa|
|client_Browser|Stringa|ClientBrowser|Stringa|
|client_City|Stringa|ClientCity|Stringa|
|client_CountryOrRegion|Stringa|ClientCountryOrRegion|Stringa|
|client_IP|Stringa|ClientIP|Stringa|
|client_Model|Stringa|ClientModel|Stringa|
|client_OS|Stringa|Clientos|Stringa|
|client_StateOrProvince|Stringa|ClientStateOrProvince|Stringa|
|client_Type|Stringa|TipoClient|Stringa|
|cloud_RoleInstance|Stringa|AppRoleInstance|Stringa|
|cloud_RoleName|Stringa|AppRoleName|Stringa|
|customDimensions|dinamico|Proprietà|dinamico|
|customMeasurements|dinamico|Misurazioni|dinamico|
|dettagli|dinamico|Dettagli|dinamico|
|handledAt|Stringa|HandledAt|Stringa|
|iKey|Stringa|IKey|Stringa|
|innermostAssembly|Stringa|InnermostAssembly|Stringa|
|innermostMessage|Stringa|InnermostMessage|Stringa|
|innermostMethod|Stringa|InnermostMethod|Stringa|
|innermostType|Stringa|InnermostType|Stringa|
|itemCount|INT|ItemCount|INT|
|itemId|Stringa|\_ItemId|Stringa|
|itemType|Stringa|Type|Stringa|
|message|string|Message|Stringa|
|method|Stringa|Metodo|Stringa|
|operation_Id|Stringa|OperationId|Stringa|
|operation_Name|Stringa|OperationName|Stringa|
|operation_ParentId|Stringa|OperationParentId|Stringa|
|operation_SyntheticSource|Stringa|OperationSyntheticSource|Stringa|
|outerAssembly|Stringa|OuterAssembly|Stringa|
|outerMessage|Stringa|OuterMessage|Stringa|
|outerMethod|Stringa|OuterMethod|Stringa|
|outerType|Stringa|OuterType|Stringa|
|problemId|Stringa|ProblemId|Stringa|
|sdkVersion|Stringa|SdkVersion|Stringa|
|session_Id|Stringa|SessionId|Stringa|
|severityLevel|INT|SeverityLevel|INT|
|timestamp|Datetime|TimeGenerated|Datetime|
|type|stringa|ExceptionType|Stringa|
|user_AccountId|Stringa|UserAccountId|Stringa|
|user_AuthenticatedId|Stringa|UserAuthenticatedId|Stringa|
|user_Id|Stringa|UserId|Stringa|

### <a name="apptraces"></a>AppTraces

Tabella legacy: tracce

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|Stringa|\_GUID risorsa|Stringa|
|application_Version|Stringa|AppVersion|Stringa|
|appName|Stringa|\_ResourceId|Stringa|
|client_Browser|Stringa|ClientBrowser|Stringa|
|client_City|Stringa|ClientCity|Stringa|
|client_CountryOrRegion|Stringa|ClientCountryOrRegion|Stringa|
|client_IP|Stringa|ClientIP|Stringa|
|client_Model|Stringa|ClientModel|Stringa|
|client_OS|Stringa|Clientos|Stringa|
|client_StateOrProvince|Stringa|ClientStateOrProvince|Stringa|
|client_Type|Stringa|TipoClient|Stringa|
|cloud_RoleInstance|Stringa|AppRoleInstance|Stringa|
|cloud_RoleName|Stringa|AppRoleName|Stringa|
|customDimensions|dinamico|Proprietà|dinamico|
|customMeasurements|dinamico|Misurazioni|dinamico|
|iKey|Stringa|IKey|Stringa|
|itemCount|INT|ItemCount|INT|
|itemId|Stringa|\_ItemId|Stringa|
|itemType|Stringa|Type|Stringa|
|message|string|Message|Stringa|
|operation_Id|Stringa|OperationId|Stringa|
|operation_Name|Stringa|OperationName|Stringa|
|operation_ParentId|Stringa|OperationParentId|Stringa|
|operation_SyntheticSource|Stringa|OperationSyntheticSource|Stringa|
|sdkVersion|Stringa|SdkVersion|Stringa|
|session_Id|Stringa|SessionId|Stringa|
|severityLevel|INT|SeverityLevel|INT|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|Stringa|UserAccountId|Stringa|
|user_AuthenticatedId|Stringa|UserAuthenticatedId|Stringa|
|user_Id|Stringa|UserId|Stringa|

## <a name="next-steps"></a>Passaggi successivi

* [Esplorare le metriche](../platform/metrics-charts.md)
* [Scrivere query di Analisi](../log-query/log-query-overview.md)

