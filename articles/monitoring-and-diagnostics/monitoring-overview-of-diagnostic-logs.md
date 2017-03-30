---
title: Panoramica dei log di diagnostica di Azure | Microsoft Docs
description: Informazioni sui log di diagnostica di Azure e su come usarli per comprendere gli eventi che si verificano all&quot;interno di una risorsa di Azure.
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: johnkem; magoedte
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: be27a3541caa1620af432dcff438f70cb9b1074b
ms.lasthandoff: 03/18/2017


---
# <a name="collect-and-consume-diagnostic-data-from-your-azure-resources"></a>Raccogliere e usare i dati di diagnostica dalle risorse di Azure

## <a name="what-are-azure-diagnostic-logs"></a>Definizione di log di diagnostica di Azure
I **log di diagnostica di Azure** sono log generati da una risorsa che forniscono dati completi e frequenti sul funzionamento di tale risorsa. Il contenuto di questi log varia in base al tipo di risorsa. Ad esempio, i log del sistema eventi di Windows sono una categoria di log di diagnostica per le macchine virtuali, i BLOB e le tabelle, mentre i log della coda sono categorie di log di diagnostica per gli account di archiviazione.

I log di diagnostica sono diversi dai [log attività (precedentemente conosciuti come log di controllo o log operativi)](monitoring-overview-activity-logs.md). Il log attività fornisce informazioni approfondite sulle operazioni eseguite sulle risorse nella sottoscrizione. I log di diagnostica forniscono informazioni approfondite sulle operazioni che la risorsa esegue automaticamente.

Non tutte le risorse supportano il nuovo tipo di log di diagnostica descritto di seguito. Questo articolo contiene una sezione che elenca i tipi di risorsa supportati dai nuovi log di diagnostica.

![Log di diagnostica e altri tipi di log ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

Figura 1: Log di diagnostica e altri tipi di log

## <a name="what-you-can-do-with-diagnostic-logs"></a>Che cosa si può fare con i log di diagnostica
Ecco alcune delle attività che è possibile eseguire con i log di diagnostica:

![Posizione logica dei log di diagnostica](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)


* Salvarli in un [**account di archiviazione**](monitoring-archive-diagnostic-logs.md) per il controllo o l'ispezione manuale. È possibile specificare il tempo di conservazione in giorni tramite le **Impostazioni di diagnostica**.
* [Trasmetterli a **Hub eventi**](monitoring-stream-diagnostic-logs-to-event-hubs.md) per l'inserimento da parte di un servizio di terze parti o una soluzione di analisi personalizzata come Power BI.
* Analizzarli con [OMS Log Analytics](../log-analytics/log-analytics-azure-storage.md)

È possibile usare un account di archiviazione o un hub eventi dello spazio dei nomi che non si trovi nella stessa sottoscrizione di quello che crea i log. L'utente che configura l'impostazione deve disporre dell'accesso RBAC appropriato a entrambe le sottoscrizioni.

## <a name="diagnostic-settings"></a>Impostazioni di diagnostica
I log di diagnostica per le risorse non di calcolo vengono configurati tramite le impostazioni di diagnostica. **Impostazioni di diagnostica** per una risorsa permettono di controllare quanto segue:

* Destinazione dei log di diagnostica, ad esempio un account di archiviazione, un hub eventi e/o OMS Log Analytics.
* Categorie di log da inviare.
* Periodo di tempo in cui ogni log di categoria deve essere mantenuto nell'account di archiviazione
    - Un periodo di conservazione di zero giorni significa che i log vengono conservati all'infinito. Se impostato su zero giorni, i log vengono conservati all'infinito.
    - Se i criteri di conservazione sono impostati, ma la memorizzazione dei log in un account di archiviazione è disabilitata, ad esempio se sono selezionate solo le opzioni Hub eventi o OMS, i criteri di conservazione non hanno alcun effetto.
    - I criteri di conservazione vengono applicati su base giornaliera. Al termine della giornata (UTC), i log relativi a tale giornata che non rientrano più nei criteri di conservazione verranno eliminati. Se, ad esempio, è presente un criterio di conservazione di un giorno, all'inizio della giornata vengono eliminati i log relativi al giorno precedente.

È possibile configurare facilmente queste impostazioni usando il pannello Diagnostica della risorsa nel portale di Azure, i comandi di Azure PowerShell o l'interfaccia della riga di comando oppure l'[API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [!WARNING]
> Le metriche e i log di diagnostica per le risorse di calcolo, ad esempio le macchine virtuali o Service Fabric, usano un [meccanismo distinto per la configurazione e la selezione degli output](../azure-diagnostics.md).
>
>

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>Come abilitare la raccolta dei log di diagnostica
La raccolta dei log di diagnostica può essere abilitata durante la creazione di una risorsa o successivamente usando il pannello della risorsa nel portale. È anche possibile abilitare i log di diagnostica in qualsiasi momento usando i comandi di Azure PowerShell o l'interfaccia della riga di comando oppure l'API REST di Monitoraggio di Azure.

> [!TIP]
> Le istruzioni riportate di seguito potrebbero non essere direttamente applicabili a tutte le risorse. Vedere i collegamenti dello schema nella parte inferiore della pagina per comprendere i possibili passaggi speciali per determinati tipi di risorse.
>
>

[Questo articolo illustra come usare il modello di una risorsa per abilitare le impostazioni di diagnostica durante la creazione di una risorsa](monitoring-enable-diagnostic-logs-using-template.md)

### <a name="enable-diagnostic-logs-in-the-portal"></a>Abilitare i log di diagnostica nel portale
È possibile abilitare i log di diagnostica nel portale di Azure quando si creano tipi di risorse di calcolo abilitando l'estensione di Diagnostica di Azure per Windows o Linux:

1. Andare a **Nuovo** e scegliere la risorsa a cui si è interessati.
2. Dopo aver configurato le impostazioni di base e aver selezionato una dimensione, nel pannello **Impostazioni**, in **Monitoraggio** selezionare **Abilitato** e scegliere un account di archiviazione in cui archiviare i log di diagnostica. Per l'invio della diagnostica a un account di archiviazione vengono addebitate le normali tariffe dati per l'archiviazione e le transazioni.

   ![Abilitare i log di diagnostica durante la creazione della risorsa](./media/monitoring-overview-of-diagnostic-logs/enable-portal-new.png)
3. Fare clic su **OK** e creare la risorsa.

Per le risorse non di calcolo, è possibile abilitare i log di diagnostica nel portale di Azure dopo aver creato una risorsa eseguendo queste operazioni:

1. Passare al pannello della risorsa e aprire il pannello **Diagnostica** .
2. Fare clic su **Sì** e selezionare un account di archiviazione e/o un hub eventi.

   ![Abilitare i log di diagnostica dopo la creazione della risorsa](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3. In **Log** selezionare le **categorie di log** da raccogliere o trasmettere.
4. Fare clic su **Save**.

### <a name="enable-diagnostic-logs-via-powershell"></a>Abilitare i log di diagnostica tramite PowerShell
Per abilitare i log di diagnostica tramite i cmdlet di Azure PowerShell, usare i comandi seguenti:

Per abilitare la memorizzazione dei log di diagnostica in un account di archiviazione, usare questo comando:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

L'ID account di archiviazione è l'ID risorsa per l'account di archiviazione a cui devono essere inviati i log.

Per abilitare la trasmissione dei log di diagnostica a un hub eventi, usare questo comando:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
```

L'ID regola del bus di servizio è una stringa nel formato seguente: `{service bus resource ID}/authorizationrules/{key name}`.

Per consentire l'invio dei log di diagnostica all'area di lavoro di Log Analytics , usare questo comando:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

È possibile ottenere l'ID risorsa dell'area di lavoro di Log Analytics usando il comando seguente:

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

È possibile combinare questi parametri per abilitare più opzioni di output.

### <a name="enable-diagnostic-logs-via-cli"></a>Abilitare i log di diagnostica tramite l'interfaccia della riga di comando
Per abilitare i log di diagnostica tramite l'interfaccia della riga di comando di Azure, usare i comandi seguenti:

Per abilitare la memorizzazione dei log di diagnostica in un account di archiviazione, usare questo comando:

```azurecli
    azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
```

L'ID account di archiviazione è l'ID risorsa per l'account di archiviazione a cui devono essere inviati i log.

Per abilitare la trasmissione dei log di diagnostica a un hub eventi, usare questo comando:

```azurecli
    azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

L'ID regola del bus di servizio è una stringa nel formato seguente: `{service bus resource ID}/authorizationrules/{key name}`.

Per consentire l'invio dei log di diagnostica all'area di lavoro di Log Analytics , usare questo comando:

```azurecli
    azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
```

È possibile combinare questi parametri per abilitare più opzioni di output.

### <a name="enable-diagnostic-logs-via-rest-api"></a>Abilitare i log di diagnostica tramite l'API REST
Per modificare le impostazioni di diagnostica usando l'API REST di Monitoraggio di Azure, vedere [questo documento](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-diagnostic-settings-in-the-portal"></a>Gestire le impostazioni di diagnostica nel portale
Verificare che tutte le risorse siano configurate con le impostazioni di diagnostica. Passare al pannello **Monitoraggio** nel portale e aprire il pannello **Log di diagnostica**.

![Pannello Log di diagnostica nel portale](./media/monitoring-overview-of-diagnostic-logs/manage-portal-nav.png)

Potrebbe essere necessario fare clic su "Altri servizi" per individuare il pannello Monitoraggio.

In questo pannello, è possibile visualizzare e filtrare tutte le risorse che supportano i Log di diagnostica per vedere se la diagnostica è abilitata. È inoltre possibile controllare in quali account di archiviazione, hub eventi e/o aree di lavoro di Log Analytics è presente il flusso di tali log.

![Risultati del pannello Log di diagnostica nel portale](./media/monitoring-overview-of-diagnostic-logs/manage-portal-blade.png)

Facendo clic su una risorsa vengono visualizzati tutti i log memorizzati nell'account di archiviazione ed è possibile disattivare o modificare le impostazioni di diagnostica. Fare clic sull'icona di download per scaricare i log relativi a un periodo di tempo specifico.

![Una risorsa del pannello Log di diagnostica](./media/monitoring-overview-of-diagnostic-logs/manage-portal-logs.png)

> [!NOTE]
> I log di diagnostica verranno visualizzati soltanto in questa vista e saranno disponibili per il download se le impostazioni di diagnostica sono state configurate per salvare i log in un account di archiviazione.
>
>

Facendo clic sul collegamento per le **impostazioni di diagnostica**, verrà visualizzato il pannello Impostazioni di diagnostica, in cui è possibile abilitare, disabilitare o modificare tali impostazioni per la risorsa selezionata.

## <a name="supported-services-and-schema-for-diagnostic-logs"></a>Servizi supportati e schema per i log di diagnostica
Lo schema per i log di diagnostica varia a seconda della risorsa e della categoria di log.   

| Service | Schema e documenti |
| --- | --- |
| Bilanciamento del carico |[Analisi dei log per Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md) |
| Gruppi di sicurezza di rete |[Analisi dei log per i gruppi di sicurezza di rete](../virtual-network/virtual-network-nsg-manage-log.md) |
| Gateway applicazione |[Registrazione diagnostica per il gateway applicazione](../application-gateway/application-gateway-diagnostics.md) |
| Insieme di credenziali di chiave |[Registrazione dell'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-logging.md) |
| Ricerca di Azure |[Abilitazione e uso di Analisi del traffico di ricerca](../search/search-traffic-analytics.md) |
| Archivio Data Lake |[Accesso ai log di diagnostica per Archivio Data Lake di Azure](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Analisi Data Lake |[Accesso ai log di diagnostica per Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| App per la logica |[Schema di rilevamento personalizzato per le app per la logica B2B](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Azure Batch |[Registrazione diagnostica di Azure Batch](../batch/batch-diagnostics.md) |
| Automazione di Azure |[Analisi dei log per Automazione di Azure](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Hub eventi |[Log di diagnostica di Hub eventi in Azure](../event-hubs/event-hubs-diagnostic-logs.md) |
| Analisi di flusso |[Log di diagnostica dei processi](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Bus di servizio |[Log di diagnostica del bus di servizio di Azure](../service-bus-messaging/service-bus-diagnostic-logs.md) |


## <a name="supported-log-categories-per-resource-type"></a>Categorie di log supportate per tipo di risorsa
|Tipo di risorsa|Categoria|Nome visualizzato della categoria|
|---|---|---|
|Microsoft.ApiManagement/service|GatewayLogs|Log correlati ad ApiManagement Gateway|
|Microsoft.Automation/automationAccounts|JobLogs|Log del processo|
|Microsoft.Automation/automationAccounts|JobStreams|Flussi del processo|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Stato del nodo Dsc|
|Microsoft.Batch/batchAccounts|ServiceLog|Log del servizio|
|Microsoft.DataLakeAnalytics/accounts|Audit|Log di controllo|
|Microsoft.DataLakeAnalytics/accounts|Requests|Log delle richieste|
|Microsoft.DataLakeStore/accounts|Audit|Log di controllo|
|Microsoft.DataLakeStore/accounts|Requests|Log delle richieste|
|Microsoft.EventHub/namespaces|ArchiveLogs|Log di archiviazione|
|Microsoft.EventHub/namespaces|OperationalLogs|Log operativi|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Log di scalabilità automatica|
|Microsoft.KeyVault/vaults|AuditEvent|Log di controllo|
|Microsoft.Logic/workflows|WorkflowRuntime|Eventi di diagnostica del runtime del flusso di lavoro|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Eventi di rilevamento degli account di integrazione|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Event del gruppo di sicurezza di rete|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Contatore di regole del gruppo di sicurezza di rete|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Evento del flusso di regole del gruppo di sicurezza di rete|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Eventi di avviso del servizio di bilanciamento del carico|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Stato di integrità dei probe del servizio di bilanciamento del carico|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Log di accesso del gateway applicazione|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Log delle prestazioni del gateway applicazione|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Log del firewall del gateway applicazione|
|Microsoft.Network/expressRouteCircuits|GWMCountersTable|Tabella di contatori GWM|
|Microsoft.Search/searchServices|OperationLogs|Log delle operazioni|
|Microsoft.ServerManagement/nodes|RequestLogs|Log delle richieste|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Log operativi|
|Microsoft.StreamAnalytics/streamingjobs|Esecuzione|Esecuzione|
|Microsoft.StreamAnalytics/streamingjobs|Creazione|Creazione|

## <a name="next-steps"></a>Passaggi successivi

* [Trasmettere log di diagnostica di Azure a **Hub eventi**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Modificare le impostazioni di diagnostica usando l'API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analizzare i log di Archiviazione di Azure con Log Analytics](../log-analytics/log-analytics-azure-storage.md)

