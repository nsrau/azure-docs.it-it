---
title: Avvisi delle metriche near real time in Monitoraggio di Azure | Microsoft Docs
description: Informazioni su come usare avvisi di metriche near real time per monitorare le metriche delle risorse di Azure con una frequenza di circa 1 minuto.
author: snehithm
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: snmuvva, vinagara
ms.custom: ''
ms.openlocfilehash: 15b9b0b69f3805b3e3af1d3973fd3a77bea62ab9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="use-the-newer-metric-alerts-for-azure-services-in-azure-portal"></a>Usare gli avvisi metrica più recenti per i servizi di Azure sul portale di Azure
Monitoraggio di Azure supporta un nuovo tipo di avvisi denominati avvisi delle metriche quasi in tempo reale. 

Gli avvisi delle metriche quasi in tempo reale sono diversi dai [normali avvisi metrica](insights-alerts-portal.md) per alcuni aspetti:

- **Latenza migliorata**: gli avvisi delle metriche quasi in tempo reale sono eseguibili con una frequenza di un minuto. Gli avvisi metrica meno recenti sono sempre eseguibili con una frequenza di 5 minuti.
- **Supporto delle metriche multidimensionali**: è possibile inviare un avviso per le metriche multidimensionali, in modo da monitorare un segmento interessante della metrica.
- **Maggiore controllo delle condizioni delle metriche**: è possibile di definire regole di avviso più dettagliate negli avvisi delle metriche near real time. Gli avvisi supportano il monitoraggio dei valori massimo, minimo, medio e totale delle metriche.
- **Monitoraggio combinato di più metriche**: gli avvisi delle metriche near real time possono monitorare più metriche (attualmente un massimo di due) con una singola regola. Viene attivato un avviso se entrambe le metriche violano le rispettive soglie per il periodo di tempo specificato.
- **Sistema di notifica modulare**: gli avvisi delle metriche near real time usano i [gruppi di azioni](monitoring-action-groups.md). È possibile usare i gruppi di azioni per creare azioni modulari. È possibile riutilizzare i gruppi di azioni per più regole di avviso.
- **Metriche dai log**: dai comuni dati di log in arrivo in [Log Analytics](../log-analytics/log-analytics-overview.md) è possibile estrarre le metriche in Monitoraggio di Azure e ricevere avvisi quasi in tempo reale.


## <a name="metrics-and-dimensions-supported"></a>Metriche e dimensioni supportate
Gli avvisi delle metriche near real time supportano gli avvisi per le metriche con dimensioni. Le dimensioni possono essere usate per filtrare le metriche al livello corretto. Tutte le metriche supportate e le dimensioni applicabili possono essere esplorate e visualizzate da [Monitoraggio di Azure - Esplora metriche (anteprima)](monitoring-metric-charts.md).

Ecco l'elenco completo delle origini delle metriche basate su Monitoraggio di Azure supportate dagli avvisi delle metriche near real time:

|Tipo di risorsa  |Dimensioni supportate  | Metriche disponibili|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Sì        | [Gestione API](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Sì   | [Account di automazione](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | N/D| [Account Batch](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    N/D     |[Cache Redis](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.Compute/virtualMachines     |    N/D     | [Macchine virtuali](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   N/D      |[Set di scalabilità di macchine virtuali](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.DataFactory/factories     |   Sì     |[Data factory V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   N/D      |[Database per MySQL](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    N/D     | [Database per PostgreSQL](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  Sì      |[Hub eventi](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.Logic/workflows     |     N/D    |[App per la logica](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    N/D     | [Gateway applicazione](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/publicipaddresses     |  N/D       |[Indirizzi IP pubblici](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Search/searchServices     |   N/D      |[Servizi di ricerca](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Sì       |[Bus di servizio](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Sì     | [Account di archiviazione](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Sì    | [Servizi BLOB](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [servizi file](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [servizi di accodamento](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) e [servizi tabelle](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  N/D       | [Analisi dei flussi](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.CognitiveServices/accounts     |    N/D     | [Servizi cognitivi](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.OperationalInsights/workspaces (anteprima) | Sì|[Aree di lavoro di Log Analytics](#support-for-oms-logs-as-metrics-for-alerting)|


## <a name="create-a-newer-metric-alert"></a>Creare un avviso metrica più recente
Al momento è possibile creare avvisi metrica più recenti solo nel portale di Azure o nell'API REST. Il supporto per la configurazione degli avvisi delle metriche quasi in tempo reale tramite PowerShell, l'interfaccia della riga di comando di Azure (Azure CLI), sarà presto disponibile.

Per informazioni su come creare un avviso metrica più recente sul portale di Azure, vedere [Creare una regola di avviso nel portale di Azure](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="manage-newer-metric-alerts"></a>Gestione degli avvisi metrica più recenti
Dopo aver creato un avviso delle metriche near real time, è possibile gestirlo seguendo i passaggi descritti in [Gestire gli avvisi nel portale di Azure](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="support-for-oms-logs-as-metrics-for-alerting"></a>Supporto per i log OMS come metriche per gli avvisi

È possibile usare gli avvisi delle metriche quasi in tempo reale anche nei log OMS più comuni estratti come metriche nel quadro dell'anteprima delle metriche dei log.  
- [Contatori delle prestazioni](../log-analytics/log-analytics-data-sources-performance-counters.md) per i computer Windows e Linux
- [Record di heartbeat per l'Integrità agente](../operations-management-suite/oms-solution-agenthealth.md)
- Record di [Gestione aggiornamenti](../operations-management-suite/oms-solution-update-management.md)

Ecco l'elenco completo delle origini delle metriche basate su log OMS supportate dagli avvisi delle metriche near real time:

Nome metrica/Dettagli  |Dimensioni supportate  | Tipo di log  |
|---------|---------|---------|
|Average_Avg. Disk sec/Read     |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Windows      |
| Average_Avg. Disk sec/Write     |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Windows      |
| Average_Current Disk Queue Length   |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Windows      |
| Average_Disk Reads/sec    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Windows      |
| Average_Disk Transfers/sec    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Windows      |
|   Average_% Free Space    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Windows      |
| Average_Available MBytes     |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Windows      |
| Average_% Committed Bytes In Use    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Windows      |
| Average_Bytes Received/sec    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Windows      |
|  Average_Bytes Sent/sec    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Windows      |
|  Average_Bytes Total/sec    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Windows      |
|  Average_% Processor Time    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Windows      |
|   Average_Processor Queue Length    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Windows      |
|   Average_% Free Inodes   |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_% Free Space   |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_% Used Inodes  |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_% Used Space   |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Disk Read Bytes/sec    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Disk Reads/sec |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Disk Transfers/sec |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Disk Write Bytes/sec   |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Disk Writes/sec    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Free Megabytes |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Logical Disk Bytes/sec |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_% Available Memory |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_% Available Swap Space |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_% Used Memory  |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_% Used Swap Space  |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Available MBytes Memory    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Available MBytes Swap  |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Page Reads/sec |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Page Writes/sec    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Pages/sec  |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Used MBytes Swap Space |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Used Memory MBytes |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Total Bytes Transmitted    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Total Bytes Received   |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Total Bytes    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Total Packets Transmitted  |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Total Packets Received |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Total Rx Errors    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Total Tx Errors    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Total Collisions   |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Avg. Disk sec/Read |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Avg. Disk sec/Transfer |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Avg. Disk sec/Write    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Physical Disk Bytes/sec    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Pct Privileged Time    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Pct User Time  |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Used Memory kBytes |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Virtual Shared Memory  |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_% DPC Time |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_% Idle Time    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_% Interrupt Time   |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_% IO Wait Time |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_% Nice Time    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_% Privileged Time  |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_% Processor Time   |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_% User Time    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Free Physical Memory   |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Free Space in Paging Files |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Free Virtual Memory    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Processes  |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Size Stored In Paging Files    |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Uptime |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Average_Users  |     Sì - Computer, ObjectName, InstanceName, CounterPath e SourceSystem    |   Contatore delle prestazioni di Linux      |
|    Heartbeat  |     Sì - Computer, OSType, Version e SourceComputerId    |   Record di heartbeat |
|    Aggiornamento |     Sì - Computer, Product, Classification, UpdateState, Optional e Approved    |   Gestione degli aggiornamenti |

> [!NOTE]
> Metriche e/o dimensioni specifiche verranno visualizzate solo se i relativi dati esistono nel periodo scelto. Tali metriche sono disponibili per i clienti con aree di lavoro negli Stati Uniti orientali, negli Stati Uniti centro-occidentali e in Europa occidentale che hanno acconsentito esplicitamente all'anteprima. Per registrarsi all'anteprima, iscriversi tramite [il sondaggio](https://aka.ms/MetricLogPreview).


## <a name="payload-schema"></a>Schema del payload

L'operazione POST contiene il payload e lo schema JSON seguenti per tutti gli avvisi delle metriche near real time se viene usato il [gruppo di azione](monitoring-action-groups.md) correttamente configurato:

```json
{"schemaId":"AzureMonitorMetricAlert","data":
    {
    "version": "2.0",
    "status": "Activated",
    "context": {
    "timestamp": "2018-02-28T10:44:10.1714014Z",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
    "name": "StorageCheck",
    "description": "",
    "conditionType": "SingleResourceMultipleMetricCriteria",
    "condition": {
      "windowSize": "PT5M",
      "allOf": [
        {
          "metricName": "Transactions",
          "dimensions": [
            {
              "name": "AccountResourceId",
              "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
            },
            {
              "name": "GeoType",
              "value": "Primary"
            }
          ],
          "operator": "GreaterThan",
          "threshold": "0",
          "timeAggregation": "PT5M",
          "metricValue": 1.0
        },
      ]
    },
    "subscriptionId": "00000000-0000-0000-0000-000000000000",
    "resourceGroupName": "Contoso",
    "resourceName": "diag500",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
    "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
  },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla nuova [esperienza di avvisi](monitoring-overview-unified-alerts.md).
* Per altre informazioni, fare riferimento agli [avvisi di log in Azure](monitor-alerts-unified-log.md).
* Informazioni sugli [avvisi in Azure](monitoring-overview-alerts.md).
