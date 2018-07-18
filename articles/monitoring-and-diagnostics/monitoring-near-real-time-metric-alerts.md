---
title: Risorse supportate per gli avvisi delle metriche di Monitoraggio di Azure più recenti
description: Informazioni di riferimento sulle metriche e i log di supporto per gli avvisi delle metriche in tempo quasi reale di Azure più recenti.
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: 4d51b099532d3052acc190231ec4be17765a427e
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971024"
---
# <a name="introduction"></a>Introduzione
Monitoraggio di Azure supporta ora un [nuovo tipo di avvisi delle metriche](monitoring-overview-unified-alerts.md) che presenta vantaggi significativi rispetto agli [avvisi delle metriche classici](insights-alerts-portal.md). Sono disponibili metriche per un [lungo elenco di servizi di Azure](monitoring-supported-metrics.md). Gli avvisi più recenti supportano un sottoinsieme (in espansione) di tipi di risorse. In questo articolo viene illustrato tale sottoinsieme. 

È possibile usare gli avvisi delle metriche più recenti anche nei log di Log Analytics più comuni estratti come metriche nel quadro delle metriche dei log (anteprima)  
- [Contatori delle prestazioni](../log-analytics/log-analytics-data-sources-performance-counters.md) per i computer Windows e Linux
- [Record di heartbeat per l'Integrità agente](../operations-management-suite/oms-solution-agenthealth.md)
- Record di [Gestione aggiornamenti](../operations-management-suite/oms-solution-update-management.md)
- I log dei [dati dell'evento](../log-analytics/log-analytics-data-sources-windows-events.md)
 
> [!NOTE]
> Metriche e/o dimensioni specifiche verranno visualizzate solo se i relativi dati esistono nel periodo scelto. Tali metriche sono disponibili per i clienti con Log Analytics di Azure nelle aree di lavoro degli Stati Uniti orientali, degli Stati Uniti centro-occidentali e in Europa occidentale. Le metriche da Log Analytics sono attualmente in anteprima pubblica e soggette a modifiche.

## <a name="portal-powershell-cli-rest-support"></a>Supporto per il portale, PowerShell, l'interfaccia della riga di comando, REST
Al momento è possibile creare avvisi metrica più recenti solo nel portale di Azure, nell'[API REST](https://docs.microsoft.com/rest/api/monitor/metricalerts/createorupdate) o [Modelli di Resource Manager](monitoring-create-metric-alerts-with-templates.md). Il supporto per la configurazione di nuovi avvisi con PowerShell e l'interfaccia della riga di comando di Azure (Azure CLI 2.0) sarà presto disponibile.

## <a name="metrics-and-dimensions-supported"></a>Metriche e dimensioni supportate
Gli avvisi delle metriche più recenti supportano l'invio di avvisi per le metriche che usano le dimensioni. Le dimensioni possono essere usate per filtrare le metriche al livello corretto. Tutte le metriche supportate e le dimensioni applicabili possono essere esplorate e visualizzate da [Monitoraggio di Azure - Esplora metriche (anteprima)](monitoring-metric-charts.md).

Ecco l'elenco completo delle origini delle metriche di Monitoraggio di Azure supportate dagli avvisi più recenti:

|Tipo di risorsa  |Dimensioni supportate  | Metriche disponibili|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Sì        | [Gestione API](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Sì   | [Account di automazione](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | N/D| [Account Batch](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    N/D     |[Cache Redis](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.CognitiveServices/accounts     |    N/D     | [Servizi cognitivi](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines     |    N/D     | [Macchine virtuali](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   N/D      |[Set di scalabilità di macchine virtuali](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Sì| [Gruppi di contenitori](monitoring-supported-metrics.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Sì | [Cluster gestiti](monitoring-supported-metrics.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataFactory/datafactories| Sì| [Data factory V1](monitoring-supported-metrics.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   Sì     |[Data factory V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   N/D      |[Database per MySQL](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    N/D     | [Database per PostgreSQL](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  Sì      |[Hub eventi](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| No  | [Vaults](monitoring-supported-metrics.md#microsoftkeyvaultvaults) (Insiemi di credenziali)|
|Microsoft.Logic/workflows     |     N/D    |[App per la logica](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    N/D     | [Gateway applicazione](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/expressRouteCircuits | N/D |  [Circuiti Express Route](monitoring-supported-metrics.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/dnsZones | N/D| [Zone DNS](monitoring-supported-metrics.md#microsoftnetworkdnszones) |
|Microsoft.Network/loadBalancers (solo per SKU Standard)| Sì| [Servizi di bilanciamento del carico](monitoring-supported-metrics.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  N/D       |[Indirizzi IP pubblici](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.PowerBIDedicated/capacities | N/D | [Capacità](monitoring-supported-metrics.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Network/trafficManagerProfiles | Sì | [Profili di Gestione traffico](monitoring-supported-metrics.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.Search/searchServices     |   N/D      |[Servizi di ricerca](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Sì       |[Bus di servizio](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Sì     | [Account di archiviazione](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Sì    | [Servizi BLOB](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [servizi file](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [servizi di accodamento](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) e [servizi tabelle](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  N/D       | [Analisi dei flussi](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
| Microsoft.Web/serverfarms | Sì | [Piani del servizio app](monitoring-supported-metrics.md#microsoftwebserverfarms)  |
|Microsoft.OperationalInsights/workspaces (anteprima) | Sì|[Aree di lavoro di Log Analytics](monitoring-supported-metrics.md#microsoftoperationalinsightsworkspaces)|



## <a name="payload-schema"></a>Schema del payload

L'operazione POST contiene il payload e lo schema JSON seguenti per tutti gli avvisi delle metriche più recenti se viene usato un [gruppo di azioni](monitoring-action-groups.md) correttamente configurato:

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
