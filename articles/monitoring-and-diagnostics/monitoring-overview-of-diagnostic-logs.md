---
title: Panoramica dei log di diagnostica di Azure | Microsoft Docs
description: Informazioni sui log di diagnostica di Azure e su come usarli per comprendere gli eventi che si verificano all'interno di una risorsa di Azure.
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: johnkem; magoedte
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 8961676a60d922912e383937ca38c5d2f89a348a
ms.contentlocale: it-it
ms.lasthandoff: 08/04/2017

---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Raccogliere e usare i dati dei log dalle risorse di Azure

## <a name="what-are-azure-resource-diagnostic-logs"></a>Definizione di log di diagnostica di risorse di Azure
I **log di diagnostica a livello di risorsa di Azure** sono log generati da una risorsa che presentano dati completi e frequenti sull'attività di tale risorsa. Il contenuto di questi log varia in base al tipo di risorsa. I contatori delle regole di gruppo di sicurezza di rete e i controlli di insieme di credenziali delle chiavi, ad esempio, sono due categorie di log di risorsa.

I log di diagnostica a livello di risorsa differiscono dal [Log attività](monitoring-overview-activity-logs.md). Il log attività fornisce informazioni approfondite sulle operazioni eseguite sulle risorse nella sottoscrizione tramite Resource Manager, ad esempio, la creazione di una macchina virtuale o l'eliminazione di un'app per la logica. Il log attività è un log a livello di sottoscrizione. I log di diagnostica a livello di risorsa includono informazioni sulle operazioni eseguite all'interno della risorsa stessa, ad esempio, il recupero di un segreto da un insieme di credenziali delle chiavi.

I log di diagnostica a livello di risorsa differiscono anche dal log di diagnostica a livello del sistema operativo guest. I log di diagnostica del sistema operativo guest vengono compilati da un agente in esecuzione all'interno di una macchina virtuale o di un altro tipo di risorsa supportato. I log di diagnostica a livello di risorsa non richiedono l'uso di un agente e acquisiscono i dati specifici della risorsa dalla piattaforma di Azure stessa, mentre i log di diagnostica a livello del sistema operativo guest acquisiscono i dati dal sistema operativo e dalle applicazioni in esecuzione in una macchina virtuale.

Non tutte le risorse supportano il nuovo tipo di log di diagnostica di risorsa descritto di seguito. Questo articolo contiene una sezione che elenca i tipi di risorsa che supportano i nuovi log di diagnostica a livello di risorsa.

![Log di diagnostica di risorsa e altri tipi di log ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

Figura 1: Log di diagnostica di risorsa e altri tipi di log

## <a name="what-you-can-do-with-resource-level-diagnostic-logs"></a>Possibili operazioni con i log di diagnostica a livello di risorsa
Ecco alcune delle attività che è possibile eseguire con i log di diagnostica di risorsa:

![Posizionamento logico dei log di diagnostica di risorsa](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)


* Salvarli in un [**account di archiviazione**](monitoring-archive-diagnostic-logs.md) per il controllo o l'ispezione manuale. È possibile specificare il tempo di conservazione, in giorni, tramite le **impostazioni di diagnostica di risorsa**.
* [Trasmetterli a **Hub eventi**](monitoring-stream-diagnostic-logs-to-event-hubs.md) per l'inserimento da parte di un servizio di terze parti o una soluzione di analisi personalizzata come Power BI.
* Analizzarli con [OMS Log Analytics](../log-analytics/log-analytics-azure-storage.md)

È possibile usare un account di archiviazione o uno spazio dei nomi di Hub eventi che non si trovi nella stessa sottoscrizione della risorsa che crea i log. L'utente che configura l'impostazione deve disporre dell'accesso RBAC appropriato a entrambe le sottoscrizioni.

## <a name="resource-diagnostic-settings"></a>Impostazioni di diagnostica di risorsa
I log di diagnostica per le non di calcolo vengono configurati tramite le impostazioni di diagnostica di risorsa. Le **impostazioni di diagnostica di risorsa** permettono di controllare quanto segue:

* Destinazione dei log di diagnostica di risorsa, ad esempio un account di archiviazione, un Hub eventi e/o OMS Log Analytics.
* Categorie di log da inviare.
* Periodo di tempo in cui ogni log di categoria deve essere mantenuto nell'account di archiviazione
    - Un periodo di conservazione di zero giorni significa che i log vengono conservati all'infinito. Se impostato su zero giorni, i log vengono conservati all'infinito.
    - Se i criteri di conservazione sono impostati, ma la memorizzazione dei log in un account di archiviazione è disabilitata, ad esempio se sono selezionate solo le opzioni Hub eventi o OMS, i criteri di conservazione non hanno alcun effetto.
    - I criteri di conservazione vengono applicati su base giornaliera. Al termine della giornata (UTC), i log relativi a tale giornata che non rientrano più nei criteri di conservazione verranno eliminati. Se, ad esempio, è presente un criterio di conservazione di un giorno, all'inizio della giornata vengono eliminati i log relativi al giorno precedente.

È possibile configurare facilmente queste impostazioni usando il pannello di diagnostica di risorsa nel portale di Azure, i comandi di Azure PowerShell e l'interfaccia della riga di comando di Azure oppure l'[API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [!WARNING]
> Le metriche e i log di diagnostica per le risorse di calcolo, ad esempio le macchine virtuali o Service Fabric, usano un [meccanismo distinto per la configurazione e la selezione degli output](../azure-diagnostics.md).
>
>

## <a name="how-to-enable-collection-of-resource-diagnostic-logs"></a>Come abilitare la raccolta dei log di diagnostica di risorsa
La raccolta dei log di diagnostica di risorsa può essere abilitata [durante la creazione di una risorsa in un modello di Resource Manager](./monitoring-enable-diagnostic-logs-using-template.md) o successivamente usando il pannello della risorsa nel portale. È possibile abilitare la raccolta anche in qualsiasi momento usando i comandi di Azure PowerShell o l'interfaccia della riga di comando oppure l'API REST di Monitoraggio di Azure.

> [!TIP]
> Le istruzioni riportate di seguito potrebbero non essere direttamente applicabili a tutte le risorse. Vedere i collegamenti dello schema nella parte inferiore della pagina per comprendere i possibili passaggi speciali per determinati tipi di risorse.
>
>

### <a name="enable-collection-of-resource-diagnostic-logs-in-the-portal"></a>Abilitare la raccolta dei log di diagnostica di risorsa nel portale
È possibile abilitare la raccolta dei log di diagnostica di risorsa nel portale di Azure dopo avere creato una risorsa seguendo questa procedura:

1. Passare al pannello della risorsa e aprire il pannello **Diagnostica** .
2. Fare clic su **Sì** e selezionare un account di archiviazione e/o un hub eventi.

   ![Abilitare i log di diagnostica dopo la creazione della risorsa](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3. In **Log** selezionare le **categorie di log** da raccogliere o trasmettere.
4. Fare clic su **Salva**.

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>Abilitare la raccolta dei log di diagnostica di risorsa con PowerShell
Per abilitare la raccolta dei log di diagnostica di risorsa tramite Azure PowerShell, usare i comandi seguenti:

Per abilitare la memorizzazione dei log di diagnostica in un account di archiviazione, usare questo comando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

L'ID account di archiviazione è l'ID risorsa per l'account di archiviazione a cui devono essere inviati i log.

Per abilitare lo streaming dei log di diagnostica a un hub eventi, usare questo comando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

L'ID regola del bus di servizio è una stringa nel formato seguente: `{Service Bus resource ID}/authorizationrules/{key name}`.

Per consentire l'invio dei log di diagnostica a un'area di lavoro di Log Analytics, usare questo comando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

È possibile ottenere l'ID risorsa dell'area di lavoro di Log Analytics usando il comando seguente:

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

È possibile combinare questi parametri per abilitare più opzioni di output.

### <a name="enable-collection-of-resource-diagnostic-logs-via-cli"></a>Abilitare la raccolta dei log di diagnostica di risorsa con l'interfaccia della riga di comando
Per abilitare la raccolta dei log di diagnostica di risorsa tramite l'interfaccia della riga di comando di Azure, usare i comandi seguenti:

Per abilitare la memorizzazione dei log di diagnostica in un account di archiviazione, usare questo comando:

```azurecli
azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
```

L'ID account di archiviazione è l'ID risorsa per l'account di archiviazione a cui devono essere inviati i log.

Per abilitare lo streaming dei log di diagnostica a un Hub eventi, usare questo comando:

```azurecli
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

L'ID regola del bus di servizio è una stringa nel formato seguente: `{Service Bus resource ID}/authorizationrules/{key name}`.

Per consentire l'invio dei log di diagnostica a un'area di lavoro di Log Analytics, usare questo comando:

```azurecli
azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
```

È possibile combinare questi parametri per abilitare più opzioni di output.

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>Abilitare la raccolta dei log di diagnostica di risorsa con l'API REST
Per modificare le impostazioni di diagnostica usando l'API REST di Monitoraggio di Azure, vedere [questo documento](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>Gestire le impostazioni di diagnostica di risorsa nel portale
Verificare che tutte le risorse siano configurate con le impostazioni di diagnostica. Passare al pannello **Monitoraggio** nel portale e aprire il pannello **Log di diagnostica**.

![Pannello Log di diagnostica nel portale](./media/monitoring-overview-of-diagnostic-logs/manage-portal-nav.png)

Potrebbe essere necessario fare clic su "Altri servizi" per individuare il pannello Monitoraggio.

In questo pannello è possibile visualizzare e filtrare tutte le risorse che supportano i log di diagnostica per vedere se la diagnostica è abilitata. È inoltre possibile controllare in quali account di archiviazione, hub eventi e/o aree di lavoro di Log Analytics è presente il flusso di tali log.

![Risultati del pannello Log di diagnostica nel portale](./media/monitoring-overview-of-diagnostic-logs/manage-portal-blade.png)

Facendo clic su una risorsa vengono visualizzati tutti i log memorizzati nell'account di archiviazione ed è possibile disattivare o modificare le impostazioni di diagnostica. Fare clic sull'icona di download per scaricare i log relativi a un periodo di tempo specifico.

![Una risorsa del pannello Log di diagnostica](./media/monitoring-overview-of-diagnostic-logs/manage-portal-logs.png)

> [!NOTE]
> I log di diagnostica verranno visualizzati soltanto in questa vista e saranno disponibili per il download se le impostazioni di diagnostica sono state configurate per salvare i log in un account di archiviazione.
>
>

Facendo clic sul collegamento per le **impostazioni di diagnostica**, verrà visualizzato il pannello Impostazioni di diagnostica, in cui è possibile abilitare, disabilitare o modificare tali impostazioni per la risorsa selezionata.

## <a name="supported-services-and-schema-for-resource-diagnostic-logs"></a>Servizi e schema supportati per i log di diagnostica di risorsa
Lo schema per i log di diagnostica di risorsa varia a seconda della risorsa e della categoria di log.   

| Service | Schema e documenti |
| --- | --- |
| Gestione API | Lo schema non è disponibile. |
| Gateway applicazione |[Registrazione diagnostica per il gateway applicazione](../application-gateway/application-gateway-diagnostics.md) |
| Automazione di Azure |[Analisi dei log per Automazione di Azure](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Registrazione diagnostica di Azure Batch](../batch/batch-diagnostics.md) |
| Customer Insights | Lo schema non è disponibile. |
| Rete per la distribuzione di contenuti (CDN) | Lo schema non è disponibile. |
| Cosmos DB | Lo schema non è disponibile. |
| Analisi Data Lake |[Accesso ai log di diagnostica per Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Archivio Data Lake |[Accesso ai log di diagnostica per Archivio Data Lake di Azure](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Hub eventi |[Log di diagnostica di Hub eventi in Azure](../event-hubs/event-hubs-diagnostic-logs.md) |
| Insieme di credenziali di chiave |[Registrazione dell'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-logging.md) |
| Bilanciamento del carico |[Analisi dei log per Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md) |
| App per la logica |[Schema di rilevamento personalizzato per le app per la logica B2B](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Gruppi di sicurezza di rete |[Analisi dei log per i gruppi di sicurezza di rete](../virtual-network/virtual-network-nsg-manage-log.md) |
| Servizi di ripristino | Lo schema non è disponibile.|
| Search |[Abilitazione e uso di Analisi del traffico di ricerca](../search/search-traffic-analytics.md) |
| Gestione server | Lo schema non è disponibile. |
| Bus di servizio |[Log di diagnostica del bus di servizio di Azure](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Analisi di flusso |[Log di diagnostica dei processi](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |

## <a name="supported-log-categories-per-resource-type"></a>Categorie di log supportate per tipo di risorsa
|Tipo di risorsa|Categoria|Nome visualizzato della categoria|
|---|---|---|
|Microsoft.ApiManagement/service|GatewayLogs|Log correlati ad ApiManagement Gateway|
|Microsoft.Automation/automationAccounts|JobLogs|Log del processo|
|Microsoft.Automation/automationAccounts|JobStreams|Flussi del processo|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Stato del nodo Dsc|
|Microsoft.Batch/batchAccounts|ServiceLog|Log del servizio|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Ottiene le metriche dell'endpoint, ad esempio la larghezza di banda, i dati in uscita e così via|
|Microsoft.CustomerInsights/hubs|AuditEvents|Eventi di controllo|
|Microsoft.DataLakeAnalytics/accounts|Audit|Log di controllo|
|Microsoft.DataLakeAnalytics/accounts|Requests|Log delle richieste|
|Microsoft.DataLakeStore/accounts|Audit|Log di controllo|
|Microsoft.DataLakeStore/accounts|Requests|Log delle richieste|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.EventHub/namespaces|ArchiveLogs|Log di archiviazione|
|Microsoft.EventHub/namespaces|OperationalLogs|Log operativi|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Log di scalabilità automatica|
|Microsoft.KeyVault/vaults|AuditEvent|Log di controllo|
|Microsoft.Logic/workflows|WorkflowRuntime|Eventi di diagnostica del runtime del flusso di lavoro|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Eventi di rilevamento degli account di integrazione|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Event del gruppo di sicurezza di rete|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Contatore di regole del gruppo di sicurezza di rete|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Eventi di avviso del servizio di bilanciamento del carico|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Stato di integrità dei probe del servizio di bilanciamento del carico|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Log di accesso del gateway applicazione|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Log delle prestazioni del gateway applicazione|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Log del firewall del gateway applicazione|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Dati dei report di Backup di Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Processi di Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Eventi di Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Elementi replicati di Azure Site Recovery|
|Microsoft.Search/searchServices|OperationLogs|Log delle operazioni|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Log operativi|
|Microsoft.StreamAnalytics/streamingjobs|Esecuzione|Esecuzione|
|Microsoft.StreamAnalytics/streamingjobs|Creazione|Creazione|

## <a name="next-steps"></a>Passaggi successivi

* [Trasmettere log di diagnostica di Azure a **Hub eventi**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Modificare le impostazioni di diagnostica di risorsa usando l'API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analizzare i log di Archiviazione di Azure con Log Analytics](../log-analytics/log-analytics-azure-storage.md)

