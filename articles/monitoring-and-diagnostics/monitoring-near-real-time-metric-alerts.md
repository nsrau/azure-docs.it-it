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
ms.openlocfilehash: 88995b1f3350fe485e28efccc93779ae0a42eb97
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>Avvisi metriche near real time (anteprima)
Monitoraggio di Azure supporta un nuovo tipo di avvisi delle metriche denominati avvisi delle metriche near real time (anteprima). Questa funzionalità è attualmente in anteprima pubblica.

Gli avvisi delle metriche near real time sono diversi dai normali avvisi delle metriche per alcuni aspetti:

- **Migliore latenza**: gli avvisi delle metriche near real time possono monitorare le modifiche in valori di metrica con una frequenza minima di un minuto.
- **Maggiore controllo delle condizioni delle metriche**: è possibile definire regole di avviso più dettagliate negli avvisi delle metriche near real time. Gli avvisi supportano il monitoraggio dei valori massimo, minimo, medio e totale delle metriche.
- **Metriche dai log**: dai comuni dati di log in arrivo in [Log Analytics](../log-analytics/log-analytics-overview.md) è possibile estrarre le metriche in Monitoraggio di Azure e ricevere avvisi quasi in tempo reale.
- **Monitoraggio combinato di più metriche**: gli avvisi delle metriche near real time possono monitorare più metriche (attualmente un massimo di due) con una singola regola. Viene attivato un avviso se entrambe le metriche violano le rispettive soglie per il periodo di tempo specificato.
- **Sistema di notifica modulare**: gli avvisi delle metriche near real time usano i [gruppi di azioni](monitoring-action-groups.md). È possibile usare i gruppi di azioni per creare azioni modulari. È possibile riutilizzare i gruppi di azioni per più regole di avviso.

> [!NOTE]
> Gli avvisi delle metriche near real time sono attualmente disponibili in anteprima pubblica. Le metriche dalle funzionalità di log sono disponibili in anteprima pubblica *limitata*. L'esperienza utente e la funzionalità sono soggette a modifiche.
>

## <a name="metrics-and-dimensions-supported"></a>Metriche e dimensioni supportate
Gli avvisi delle metriche near real time supportano gli avvisi per le metriche con dimensioni. Le dimensioni possono essere usate per filtrare le metriche al livello corretto. Tutte le metriche supportate e le dimensioni applicabili possono essere esplorate e visualizzate da [Monitoraggio di Azure - Esplora metriche (anteprima)](monitoring-metric-charts.md).

Ecco l'elenco completo delle origini delle metriche basate su Monitoraggio di Azure supportate dagli avvisi delle metriche near real time:

|Nome metrica/Dettagli  |Dimensioni supportate  |
|---------|---------|
|Microsoft.ApiManagement/service     | Sì        |
|Microsoft.Automation/automationAccounts     |     N/D    |
|Microsoft.Automation/automationAccounts     |   N/D      |
|Microsoft.Cache/Redis     |    N/D     |
|Microsoft.Compute/virtualMachines     |    N/D     |
|Microsoft.Compute/virtualMachineScaleSets     |   N/D      |
|Microsoft.DataFactory/factories     |   N/D      |
|Microsoft.DBforMySQL/servers     |   N/D      |
|Microsoft.DBforPostgreSQL/servers     |    N/D     |
|Microsoft.EventHub/namespaces     |   N/D      |
|Microsoft.Logic/workflows     |     N/D    |
|Microsoft.Network/applicationGateways     |    N/D     |
|Microsoft.Network/publicipaddresses     |  N/D       |
|Microsoft.Search/searchServices     |   N/D      |
|Microsoft.ServiceBus/namespaces     |  N/D       |
|Microsoft.Storage/storageAccounts     |    Sì     |
|Microsoft.Storage/storageAccounts/services     |     Sì    |
|Microsoft.StreamAnalytics/streamingjobs     |  N/D       |
|Microsoft.CognitiveServices/accounts     |    N/D     |


Le metriche dai log supportano attualmente i log OMS comuni seguenti:
- [Contatori delle prestazioni](../log-analytics/log-analytics-data-sources-performance-counters.md) per i computer Windows e Linux
- Record di heartbeat per computer
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
> Metriche e/o dimensioni specifiche verranno visualizzate solo se i relativi dati esistono nel periodo scelto

## <a name="create-a-near-real-time-metric-alert"></a>Creare un avviso delle metriche near real time
Al momento è possibile creare avvisi delle metriche near real time solo nel portale di Azure. Il supporto per la configurazione degli avvisi delle metriche near real time tramite PowerShell, l'interfaccia della riga di comando di Azure (Azure CLI) e le API REST di Monitoraggio di Azure saranno presto disponibili.

L'esperienza di creazione di avvisi delle metriche near real time è passata nella nuova pagina **Avvisi (anteprima)**. Anche se la pagina Avvisi corrente visualizza **Aggiungi avviso metriche near real time**, l'utente viene reindirizzato alla pagina **Avvisi (anteprima)**.

Per informazioni su come creare un avviso delle metriche near real time, vedere [Creare una regola di avviso nel portale di Azure](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="manage-near-real-time-metric-alerts"></a>Gestire gli avvisi delle metriche near real time
Dopo aver creato un avviso delle metriche near real time, è possibile gestirlo seguendo i passaggi descritti in [Gestire gli avvisi nel portale di Azure](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

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

* Altre informazioni sulla nuova [esperienza di Avvisi (anteprima)](monitoring-overview-unified-alerts.md).
* Informazioni sugli [avvisi del log in Avvisi di Azure (anteprima)](monitor-alerts-unified-log.md).
* Informazioni sugli [avvisi in Azure](monitoring-overview-alerts.md).
