---
title: Panoramica dei log delle risorse di Azure | Microsoft Docs
description: Informazioni sui servizi e sullo schema di eventi supportati per i log delle risorse di Azure.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 09/20/2019
ms.author: robb
ms.subservice: logs
ms.openlocfilehash: bfcd2ded96c2679ba9177a760a8b11dc7d2c9a77
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262544"
---
# <a name="azure-resource-logs-overview"></a>Panoramica dei log delle risorse di Azure
I log delle risorse di Azure sono [log della piattaforma](platform-logs-overview.md) emessi dalle risorse di Azure che descrivono l'operazione interna. Tutti i log delle risorse condividono uno schema di primo livello comune con la flessibilità per ogni servizio di creare proprietà univoche per i rispettivi eventi.

> [!NOTE]
> I log delle risorse erano noti in precedenza come log di diagnostica.

## <a name="collecting-resource-logs"></a>Raccolta dei log delle risorse
I log delle risorse vengono generati automaticamente dalle risorse di Azure supportate, ma non vengono raccolti a meno che non vengano configurati usando un' [impostazione di diagnostica](diagnostic-settings.md). Creare un'impostazione di diagnostica per ogni risorsa di Azure per l'invio dei log alle destinazioni seguenti:

| Destination | Scenario |
|:---|:---|:---|
| [area di lavoro Log Analytics](resource-logs-collect-storage.md) | Analizzare i log con altri dati di monitoraggio e sfruttare le funzionalità di monitoraggio di Azure, ad esempio query di log e avvisi del log. |
| [Archiviazione di Azure](archive-diagnostic-logs.md) | Archiviare i log per il controllo o il backup. |
| [Hub eventi](resource-logs-stream-event-hubs.md) | Trasmettere i log a sistemi di registrazione e telemetria di terze parti.  |

## <a name="compute-resources"></a>Risorse di calcolo
I log delle risorse sono diversi dai log a livello del sistema operativo guest nelle risorse di calcolo di Azure. Per le risorse di calcolo è necessario che un agente raccolga log e metriche dal sistema operativo guest, inclusi i dati come log eventi, syslog e i contatori delle prestazioni. Usare l' [estensione di diagnostica](agents-overview.md#azure-diagnostic-extension) per instradare i dati di log dalle macchine virtuali di Azure e l' [agente di log Analytics](agents-overview.md#log-analytics-agent) per raccogliere i log e le metriche dalle macchine virtuali in Azure, in altri cloud e in locale in un'area di lavoro di log Analytics. Per informazioni dettagliate, vedere [origini dei dati di monitoraggio per monitoraggio di Azure](data-sources.md) .

## <a name="resource-logs-schema"></a>Schema dei log delle risorse
Ogni servizio di Azure dispone di uno schema personalizzato quando i log delle risorse vengono scritti in una delle destinazioni, ma tutti condividono uno schema di primo livello nella tabella seguente. Vedere gli [schemi specifici del servizio](#service-specific-schemas) di seguito per i collegamenti allo schema per ogni servizio. 

| Attività | Obbligatorio/Facoltativo | Descrizione |
|---|---|---|
| time | Richiesto | Il timestamp dell’evento (fuso UTC). |
| resourceId | Richiesto | ID della risorsa che ha emesso l’evento. Per i servizi di tenant, questo ha la forma /tenants/tenant-id/providers/provider-name. |
| tenantId | Obbligatorio per i log di tenant | L'ID tenant del tenant di Active Directory associato a questo evento. Questa proprietà viene utilizzata solo per i log a livello di tenant, non viene visualizzata nei log a livello di risorsa. |
| operationName | Richiesto | Il nome dell'operazione rappresentata da questo evento. Se l'evento rappresenta un'operazione RBAC, si tratta del nome di operazione RBAC (ad es. Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Tipicamente modellate sotto forma di operazione di Resource Manager, anche se non sono effettivamente operazioni documentate di Resource Manager (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Facoltativi | La versione api associata all'operazione, se operationName è stato eseguito utilizzando un'API (ad es. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Se non esiste un'API corrispondente a questa operazione, la versione rappresenta la versione di tale operazione nel caso in cui le proprietà associate all'operazione cambino in futuro. |
| category | Richiesto | La categoria di log dell'evento. La categoria è la granularità con cui è possibile abilitare o disabilitare i log di una particolare risorsa. Le proprietà che appaiono all'interno del BLOB delle proprietà di un evento sono le stesse all'interno di una particolare categoria di log e tipo di risorsa. Tipiche categorie di log sono "Controllo" "Operativo" "Esecuzione" e "Richiesta". |
| resultType | Facoltativi | Lo stato dell'evento. I valori tipici includono: Started, In Progress, Succeeded, Failed, Active e Resolved. |
| resultSignature | Facoltativi | Lo stato secondario dell'evento. Se questa operazione corrisponde a una chiamata API REST, questo è il codice di stato HTTP della chiamata REST corrispondente. |
| resultDescription | Facoltativi | Il testo statico che descrive questa operazione, ad es. "Recupera file di archiviazione". |
| durationMs | Facoltativi | La durata dell'operazione in millisecondi. |
| callerIpAddress | Facoltativi | L'indirizzo IP del chiamante, se l'operazione corrisponde a una chiamata API proveniente da un'entità con un indirizzo IP accessibile al pubblico. |
| correlationId | Facoltativi | Un GUID utilizzato per raggruppare un set di eventi correlati. In genere, se due eventi hanno lo stesso operationName ma due diversi stati (ad es. "Started" e "Succeeded"), condividono lo stesso ID di correlazione. Ciò può anche rappresentare altre relazioni tra gli eventi. |
| identity | Facoltativi | Un blob JSON che descrive l'identità dell'utente o dell'applicazione che ha eseguito l'operazione. In genere includerà l'autorizzazione e le attestazioni / token JWT da Active Directory. |
| Livello | Facoltativi | Il livello di gravità dell'evento. Deve essere di tipo Informativo, Avviso, Errore o Critico. |
| location | Facoltativi | L'area della risorsa che emette l'evento, ad es. "Stati Uniti orientali" o "Francia meridionale" |
| proprietà | Facoltativi | Eventuali proprietà estese relative a questa particolare categoria di eventi. Tutte le proprietà personali/uniche devono essere inserite all'interno di questa "Parte B" dello schema. |

## <a name="service-specific-schemas"></a>Schemi specifici del servizio
Lo schema per i log di diagnostica delle risorse varia a seconda del tipo di risorsa definito dalla `resourceId` proprietà e delle `category` proprietà. L'elenco seguente mostra tutti i servizi di Azure che supportano i log delle risorse con collegamenti al servizio e allo schema specifico della categoria, se disponibili.

| Service | Schema e documenti |
| --- | --- |
| Azure Active Directory | [Panoramica](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), schema del [Registro di controllo](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) e [schema degli accessi](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| Gestione API | [Log di diagnostica di Gestione API](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Gateway applicazione |[Registrazione diagnostica per il gateway applicazione](../../application-gateway/application-gateway-diagnostics.md) |
| Automazione di Azure |[Analisi dei log per Automazione di Azure](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Registrazione diagnostica di Azure Batch](../../batch/batch-diagnostics.md) |
| Database di Azure per MySQL | [Log di diagnostica di database di Azure per MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Database di Azure per PostgreSQL | [Log di diagnostica di database di Azure per PostgreSQL](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Servizi cognitivi | [Registrazione diagnostica per servizi cognitivi di Azure](../../cognitive-services/diagnostic-logging.md) |
| Rete per la distribuzione di contenuti (CDN) | [Log di diagnostica di Azure per la rete CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Registrazione di Azure Cosmos DB](../../cosmos-db/logging.md) |
| Data factory | [Monitorare le data factory con Monitoraggio di Azure](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Accesso ai log di diagnostica per Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Accesso ai log di diagnostica per Archivio Data Lake di Azure](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Hub eventi |[Log di diagnostica di Hub eventi in Azure](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Lo schema non è disponibile. |
| Firewall di Azure | Lo schema non è disponibile. |
| Hub IoT | [Operazioni dell'hub IoT](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Registrazione dell'insieme di credenziali delle chiavi di Azure](../../key-vault/key-vault-logging.md) |
| Bilanciamento del carico |[Analisi dei log per il servizio di bilanciamento del carico di Azure](../../load-balancer/load-balancer-monitor-log.md) |
| App per la logica |[Schema di rilevamento personalizzato per le app per la logica B2B](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Gruppi di sicurezza di rete |[Analisi dei log per i gruppi di sicurezza di rete](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Protezione DDoS | [Gestire la protezione DDoS di Azure Standard](../../virtual-network/manage-ddos-protection.md) |
| Power BI dedicato | [Registrazione diagnostica per Power BI Embedded in Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Servizi di ripristino | [Modello di dati per Backup di Microsoft Azure](../../backup/backup-azure-reports-data-model.md)|
| Cerca |[Abilitazione e uso di Analisi del traffico di ricerca](../../search/search-traffic-analytics.md) |
| Bus di servizio |[Log di diagnostica del bus di servizio di Azure](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Database SQL | [Registrazione diagnostica del database SQL di Azure](../../sql-database/sql-database-metrics-diag-logging.md) |
| Analisi di flusso |[Log di diagnostica dei processi](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Gestione traffico | [Schema dei log di Gestione traffico](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Reti virtuali | Lo schema non è disponibile. |
| Gateway di rete virtuale | Lo schema non è disponibile. |

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni su altri log della piattaforma Azure](platform-logs-overview.md) che è possibile usare per monitorare Azure.
