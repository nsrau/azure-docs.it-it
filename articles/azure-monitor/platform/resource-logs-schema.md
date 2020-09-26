---
title: Servizi e schemi supportati per i log delle risorse di Azure
description: Informazioni sui servizi e sullo schema di eventi supportati per i log delle risorse di Azure.
ms.subservice: logs
ms.topic: reference
ms.date: 09/01/2020
ms.openlocfilehash: 17b4b161e76f018d8f669ee7e9b5dd578bb3e035
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91278397"
---
# <a name="common-and-service-specific-schema-for-azure-resource-logs"></a>Schema comune e specifico del servizio per i log delle risorse di Azure

> [!NOTE]
> I log delle risorse erano noti in precedenza come log di diagnostica. Il nome è stato modificato nel 2019 ottobre perché i tipi di log raccolti da monitoraggio di Azure sono stati spostati in modo da includere solo la risorsa di Azure. Inoltre, l'elenco delle categorie di log delle risorse che è possibile raccogliere in questo articolo. Sono state spostate in [categorie di log delle risorse](resource-logs-categories.md). 

I [log delle risorse di monitoraggio di Azure](./platform-logs-overview.md) sono log emessi da servizi di Azure che descrivono il funzionamento di tali servizi o risorse. Tutti i log delle risorse disponibili tramite monitoraggio di Azure condividono uno schema di primo livello comune, con la flessibilità che consente a ogni servizio di emettere proprietà univoche per gli eventi.

Una combinazione del tipo di risorsa (disponibile nella proprietà `resourceId`) e del `category` identifica in modo univoco uno schema. Questo articolo descrive lo schema di primo livello per i log delle risorse e i collegamenti a schemi per ogni servizio.


## <a name="top-level-common-schema"></a>Schema comune di primo livello

| Nome | Obbligatorio/facoltativo | Descrizione |
|---|---|---|
| time | Necessario | Il timestamp dell’evento (fuso UTC). |
| resourceId | Necessario | ID della risorsa che ha emesso l’evento. Per i servizi di tenant, questo ha la forma /tenants/tenant-id/providers/provider-name. |
| TenantId | Obbligatorio per i log di tenant | L'ID tenant del tenant di Active Directory associato a questo evento. Questa proprietà viene utilizzata solo per i log a livello di tenant, non viene visualizzata nei log a livello di risorsa. |
| operationName | Necessario | Il nome dell'operazione rappresentata da questo evento. Se l'evento rappresenta un'operazione RBAC, questo è il nome dell'operazione RBAC (ad esempio, Microsoft. storage/storageAccounts/blobServices/Blobs/Read). Tipicamente modellate sotto forma di operazione di Resource Manager, anche se non sono effettivamente operazioni documentate di Resource Manager (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Facoltativo | La versione API associata all'operazione, se OperationName è stata eseguita usando un'API (ad esempio, `http://myservice.windowsazure.net/object?api-version=2016-06-01` ). Se non esiste un'API corrispondente a questa operazione, la versione rappresenta la versione di tale operazione nel caso in cui le proprietà associate all'operazione cambino in futuro. |
| category | Necessario | La categoria di log dell'evento. La categoria è la granularità con cui è possibile abilitare o disabilitare i log di una particolare risorsa. Le proprietà che appaiono all'interno del BLOB delle proprietà di un evento sono le stesse all'interno di una particolare categoria di log e tipo di risorsa. Le categorie di log tipiche sono "audit" "Operational" "Execution" e "Request". |
| resultType | Facoltativo | Lo stato dell'evento. I valori tipici includono: Started, In Progress, Succeeded, Failed, Active e Resolved. |
| resultSignature | Facoltativo | Lo stato secondario dell'evento. Se questa operazione corrisponde a una chiamata all'API REST, questo campo è il codice di stato HTTP della chiamata REST corrispondente. |
| resultDescription | Facoltativo | Descrizione statica del testo di questa operazione, ad esempio "Get storage file". |
| durationMs | Facoltativo | La durata dell'operazione in millisecondi. |
| callerIpAddress | Facoltativo | Indirizzo IP del chiamante, se l'operazione corrisponde a una chiamata API proveniente da un'entità con un indirizzo IP disponibile pubblicamente. |
| correlationId | Facoltativo | Un GUID utilizzato per raggruppare un set di eventi correlati. In genere, se due eventi hanno lo stesso OperationName ma due stati diversi, ad esempio "Started" e "succeeded", condividono lo stesso ID di correlazione. Ciò può anche rappresentare altre relazioni tra gli eventi. |
| identity | Facoltativo | Un blob JSON che descrive l'identità dell'utente o dell'applicazione che ha eseguito l'operazione. In genere questo campo include il token di autorizzazione e attestazioni/JWT da Active Directory. |
| Level | Facoltativo | Il livello di gravità dell'evento. Deve essere di tipo Informativo, Avviso, Errore o Critico. |
| posizione | Facoltativo | Area della risorsa che emette l'evento, ad esempio "Stati Uniti orientali" o "Francia meridionale" |
| properties | Facoltativo | Eventuali proprietà estese relative a questa particolare categoria di eventi. Tutte le proprietà personalizzate/univoche devono essere inserite nella "parte B" dello schema. |

## <a name="service-specific-schemas"></a>Schemi specifici del servizio

Lo schema per i log delle risorse varia a seconda della risorsa e della categoria di log. Questo elenco Mostra i servizi che rendono disponibili i log delle risorse e i collegamenti al servizio e allo schema specifico di categoria, se disponibili. Questo elenco è in continua evoluzione quando vengono aggiunti nuovi servizi, quindi, se non vengono visualizzati gli elementi necessari, usare un motore di ricerca per trovare documentazione aggiuntiva. È possibile aprire un problema di GitHub in questo articolo per aggiornarlo.

| Service | Schema e documenti |
| --- | --- |
| Azure Active Directory | [Panoramica](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), schema del [Registro di controllo](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) e [schema degli accessi](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | [Azure Analysis Services-configurare la registrazione diagnostica](../../analysis-services/analysis-services-logging.md) |
| Gestione API | [Log delle risorse di gestione API](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| Gateway applicazione |[Registrazione per il gateway applicazione](../../application-gateway/application-gateway-diagnostics.md) |
| Automazione di Azure |[Log Analytics per automazione di Azure](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Registrazione Azure Batch](../../batch/batch-diagnostics.md) |
| Servizi cognitivi | [Registrazione per servizi cognitivi di Azure](../../cognitive-services/diagnostic-logging.md) |
| Registro Container | [Registrazione per Container Registry di Azure](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Rete per la distribuzione di contenuti (CDN) | [Log di Azure per la rete CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| Cosmos DB | [Registrazione di Azure Cosmos DB](../../cosmos-db/monitor-cosmos-db.md) |
| Data Factory | [Monitorare le data factory con monitoraggio di Azure](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Accesso ai log per Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Accesso ai log per Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Esplora dati di Azure | [Log di Azure Esplora dati](/azure/data-explorer/using-diagnostic-logs) |
| Database di Azure per MySQL | [Log di diagnostica di database di Azure per MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Database di Azure per PostgreSQL | [Log di database di Azure per PostgreSQL](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure Databricks | [Registrazione diagnostica in Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs) |
| Gemelli digitali di Azure | [Configurare la diagnostica di dispositivi gemelli digitali di Azure](../../digital-twins/troubleshoot-diagnostics.md#log-schemas)
| Hub eventi |[Log di hub eventi di Azure](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express Route | Lo schema non è disponibile. |
| Firewall di Azure | Lo schema non è disponibile. |
| Frontdoor | [Registrazione per lo sportello anteriore](../../frontdoor/front-door-diagnostics.md) |
| Hub IoT | [Operazioni dell'hub IoT](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Registrazione dell'insieme di credenziali delle chiavi di Azure](../../key-vault/general/logging.md) |
| Servizio Kubernetes |[Registrazione di Azure Kubernetes](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Analisi dei log per il servizio di bilanciamento del carico di Azure](../../load-balancer/load-balancer-monitor-log.md) |
| App per la logica |[Schema di rilevamento personalizzato per le app per la logica B2B](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Gruppi di sicurezza di rete |[Analisi dei log per i gruppi di sicurezza di rete](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Protezione DDoS | [Gestire la protezione DDoS di Azure Standard](../../virtual-network/manage-ddos-protection.md) |
| Power BI dedicato | [Registrazione per Power BI Embedded in Azure](/power-bi/developer/azure-pbie-diag-logs) |
| Servizi di ripristino | [Modello di dati per backup di Azure](../../backup/backup-azure-reports-data-model.md)|
| Ricerca |[Abilitazione e uso di Analisi del traffico di ricerca](../../search/search-traffic-analytics.md) |
| Bus di servizio |[Log del bus di servizio di Azure](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Database SQL | [Registrazione del database SQL di Azure](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) |
| Analisi di flusso |[Log di processo](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Gestione traffico | [Schema del log di gestione traffico](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Reti virtuali | Lo schema non è disponibile. |
| Gateway di rete virtuale | Lo schema non è disponibile. |



## <a name="next-steps"></a>Passaggi successivi

* [Vedere le categorie di log delle risorse che è possibile raccogliere](resource-logs-categories.md)
* [Altre informazioni sui log delle risorse](./platform-logs-overview.md)
* [Trasmettere i log delle risorse delle risorse a **Hub eventi**](./resource-logs.md#send-to-azure-event-hubs)
* [Modificare le impostazioni di diagnostica del log delle risorse usando l'API REST di monitoraggio di Azure](/rest/api/monitor/diagnosticsettings)
* [Analizzare i log di Archiviazione di Azure con Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

