---
title: Risorse supportate per gli avvisi delle metriche in Monitoraggio di Azure
description: Informazioni di riferimento sulle metriche e i log di supporto per gli avvisi delle metriche in Monitoraggio di Azure
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 69de0f34f735c30fbd99b3266dc0151128fb73eb
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669317"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Risorse supportate per gli avvisi delle metriche in Monitoraggio di Azure

Monitoraggio di Azure supporta ora un [nuovo tipo di avvisi delle metriche](../../azure-monitor/platform/alerts-overview.md) che presenta vantaggi significativi rispetto agli [avvisi delle metriche classici](../../azure-monitor/platform/alerts-classic.overview.md). Sono disponibili metriche per un [lungo elenco di servizi di Azure](../../azure-monitor/platform/metrics-supported.md). Gli avvisi più recenti supportano un sottoinsieme (in espansione) di tipi di risorse. In questo articolo viene illustrato tale sottoinsieme.

È possibile usare gli avvisi delle metriche più recenti anche nei log di Log Analytics più comuni estratti come metriche. Per altre informazioni, vedere [Avvisi delle metriche per i log](../../azure-monitor/platform/alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Supporto per il portale, PowerShell, l'interfaccia della riga di comando, REST
Al momento è possibile creare avvisi metrica più recenti solo nel portale di Azure, nell'[API REST](https://docs.microsoft.com/rest/api/monitor/metricalerts/) o [Modelli di Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md). Il supporto per la configurazione di nuovi avvisi con PowerShell e l'interfaccia della riga di comando di Azure versione 2.0 e versioni successive sarà presto disponibile.

## <a name="metrics-and-dimensions-supported"></a>Metriche e dimensioni supportate
Gli avvisi delle metriche più recenti supportano l'invio di avvisi per le metriche che usano le dimensioni. Le dimensioni possono essere usate per filtrare le metriche al livello corretto. Tutte le metriche supportate e le dimensioni applicabili possono essere esplorate e visualizzate da [Monitoraggio di Azure - Esplora metriche](../../azure-monitor/platform/metrics-charts.md).

Ecco l'elenco completo delle origini delle metriche di Monitoraggio di Azure supportate dagli avvisi più recenti:

|Tipo di risorsa  |Dimensioni supportate  | Metriche disponibili|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Sì        | [Gestione API](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Sì   | [Account di automazione](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | N/D| [Account Batch](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    N/D     |[Cache Redis di Azure](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft.CognitiveServices/accounts     |    N/D     | [Servizi cognitivi](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines     |    N/D     | [Macchine virtuali](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   N/D      |[Set di scalabilità di macchine virtuali](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Sì| [Gruppi di contenitori](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Sì | [Cluster gestiti](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataFactory/datafactories| Sì| [Data factory V1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   Sì     |[Data factory V2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   N/D      |[Database per MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    N/D     | [Database per PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs    | N/D     |[Metriche dell'Hub IoT](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)
|Microsoft.Devices/provisioningServices    | Sì     |[Metriche di servizio Device Provisioning](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)
|Microsoft.EventHub/namespaces     |  Sì      |[Hub eventi](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| No  | [Vaults](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults) (Insiemi di credenziali)|
|Microsoft.Logic/workflows     |     N/D    |[App per la logica](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    N/D     | [Gateway applicazione](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/dnsZones | N/D| [Zone DNS](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | N/D |  [Circuiti Express Route](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (solo per SKU Standard)| Sì| [Servizi di bilanciamento del carico](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  N/D       |[Indirizzi IP pubblici](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Sì | [Profili di Gestione traffico](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Sì|[Aree di lavoro di Log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.PowerBIDedicated/capacities | N/D | [Capacità](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices     |   N/D      |[Servizi di ricerca](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Sì       |[Bus di servizio](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Sì     | [Account di archiviazione](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Sì    | [Servizi BLOB](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [servizi file](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices), [servizi di accodamento](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) e [servizi tabelle](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  N/D       | [Analisi dei flussi](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
| Microsoft.Web/serverfarms | Sì | [Piani del servizio app](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)  |
| Microsoft.Web/sites | Sì | [Servizi app](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) e [Funzioni](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
| Microsoft.Web/sites/slots | Sì | [Slot del servizio app](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|


## <a name="payload-schema"></a>Schema del payload

L'operazione POST contiene il payload e lo schema JSON seguenti per tutti gli avvisi delle metriche più recenti se viene usato un [gruppo di azioni](../../azure-monitor/platform/action-groups.md) correttamente configurato:

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
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
            "metricValue": 1
          }
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

* Altre informazioni sulla nuova [esperienza di avvisi](../../azure-monitor/platform/alerts-overview.md).
* Per altre informazioni, fare riferimento agli [avvisi di log in Azure](../../azure-monitor/platform/alerts-unified-log.md).
* Informazioni sugli [avvisi in Azure](../../azure-monitor/platform/alerts-overview.md).
