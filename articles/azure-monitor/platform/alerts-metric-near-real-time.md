---
title: Risorse supportate per gli avvisi delle metriche in Monitoraggio di Azure
description: Informazioni di riferimento sulle metriche e i log di supporto per gli avvisi delle metriche in Monitoraggio di Azure
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 12/17/2019
ms.subservice: alerts
ms.openlocfilehash: 9585e74170d21ad899224462b6e41e7e9bb75727
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664815"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Risorse supportate per gli avvisi delle metriche in Monitoraggio di Azure

Monitoraggio di Azure supporta ora un [nuovo tipo di avvisi delle metriche](../../azure-monitor/platform/alerts-overview.md) che presenta vantaggi significativi rispetto agli [avvisi delle metriche classici](../../azure-monitor/platform/alerts-classic.overview.md). Sono disponibili metriche per un [lungo elenco di servizi di Azure](../../azure-monitor/platform/metrics-supported.md). Gli avvisi più recenti supportano un sottoinsieme (in espansione) di tipi di risorse. In questo articolo viene illustrato tale sottoinsieme.

È anche possibile usare gli avvisi delle metriche più recenti sui dati di log più diffusi archiviati in un'area di lavoro Log Analytics estratti come metriche. Per altre informazioni, vedere [Avvisi delle metriche per i log](../../azure-monitor/platform/alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Supporto per il portale, PowerShell, l'interfaccia della riga di comando, REST
Al momento è possibile creare avvisi metrica più recenti solo nel portale di Azure, nell'[API REST](https://docs.microsoft.com/rest/api/monitor/metricalerts/) o [Modelli di Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md). Il supporto per la configurazione di nuovi avvisi con PowerShell e l'interfaccia della riga di comando di Azure versione 2.0 e versioni successive sarà presto disponibile.

## <a name="metrics-and-dimensions-supported"></a>Metriche e dimensioni supportate
Gli avvisi delle metriche più recenti supportano l'invio di avvisi per le metriche che usano le dimensioni. Le dimensioni possono essere usate per filtrare le metriche al livello corretto. Tutte le metriche supportate e le dimensioni applicabili possono essere esplorate e visualizzate da [Monitoraggio di Azure - Esplora metriche](../../azure-monitor/platform/metrics-charts.md).

Ecco l'elenco completo delle origini delle metriche di Monitoraggio di Azure supportate dagli avvisi più recenti:

|Tipo di risorsa  |Dimensioni supportate |Avvisi per più risorse| Metriche disponibili|
|---------|---------|-----|----------|
|Microsoft.ApiManagement/service | Sì| No | [Gestione API](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft. AppPlatform/Spring |No| Sì|
|Microsoft.Automation/automationAccounts | Sì| No | [Account di automazione](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | N/D| No | [Account Batch](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis|Sì| No |[Cache Redis di Azure](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic|No|Sì|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/blobServices|No|Sì|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/FileService|No|Sì|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/queueServices|No|Sì|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/tableServices|No|Sì| |
|Microsoft.CognitiveServices/accounts| N/D | No | [Servizi cognitivi](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines |Sì | Sì | [Macchine virtuali](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets |N/D | Sì |[Set di scalabilità di macchine virtuali](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Sì| No | [Gruppi di contenitori](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Sì | No | [Cluster gestiti](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | Sì | Sì | |
|Microsoft.DataFactory/datafactories| Sì| No | [Data factory V1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories |Sì | No |[Data factory V2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft. DataShare/accounts |No| Sì|
|Microsoft.DBforMySQL/servers |N/D| No |[Database per MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers |N/D | No | [Database per PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs | N/D | No |[Metriche dell'hub Internet](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Microsoft.Devices/provisioningServices| Sì | No |[Metriche DPS](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|Microsoft.EventGrid/domains|No|Sì| |
|Microsoft.EventGrid/topics |Sì | No |[Argomenti di griglia di eventi](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Microsoft.EventHub/clusters |Sì| No |[Cluster di hub eventi](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Microsoft.EventHub/namespaces |Sì| No |[Hub eventi](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| No |No |[Vaults](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults) (Insiemi di credenziali)|
|Microsoft.Logic/workflows |N/D | No |[App per la logica](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces|Sì| No | [Machine Learning](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.Network/applicationGateways|N/D| No |  |
|Microsoft.Network/dnsZones | N/D| No | [Zone DNS](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | N/D | No |[Circuiti Express Route](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (solo per SKU Standard)| Sì| No | [Servizi di bilanciamento del carico](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways|No|Sì|
|Microsoft. Network/privateEndpoints|No|Sì|
|Microsoft. Network/privateLinkServices|No|Sì|
|Microsoft.Network/publicipaddresses |N/D | No |[Indirizzi IP pubblici](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Sì | No | [Profili di Gestione traffico](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Sì | No | [Aree di lavoro di Log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Relay/namespaces | Sì | No | [Relè](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|Microsoft. peering/peeringServices|No|Sì|
|Microsoft.PowerBIDedicated/capacities | N/D | No | [Capacità](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices |N/D|No | [Servizi di ricerca](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces |Sì| No |[Bus di servizio](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Sql/servers/elasticPools |   No | Sì |
|Microsoft.Sql/servers/databases    | No | Sì |
|Microsoft.Storage/storageAccounts |Sì | No | [Account di archiviazione](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services | Sì| No | [Servizi BLOB](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [servizi file](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices), [servizi di accodamento](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) e [servizi tabelle](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs |N/D| No | [Analisi dei flussi](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft. Microsoft. VMWareCloudSimple/virtualMachines |Sì|No |[Macchine virtuali CloudSimple](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
|Microsoft.Web/serverfarms | Sì | No | [Piani del servizio app](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Sì | No | [Servizi app](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) e [Funzioni](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
|Microsoft.Web/sites/slots | Sì | No | [Slot del servizio app](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|

## <a name="payload-schema"></a>Schema del payload

> [!NOTE]
> È anche possibile usare lo [schema di avviso comune](https://aka.ms/commonAlertSchemaDocs), che offre il vantaggio di avere un singolo payload di avviso estendibile e unificato per tutti i servizi di avviso in monitoraggio di Azure, per le integrazioni del webhook. [Informazioni sulle definizioni comuni dello schema di avviso.](https://aka.ms/commonAlertSchemaDefinitions)


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
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
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
