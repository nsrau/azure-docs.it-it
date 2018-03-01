---
title: Avvisi delle metriche near real time in Monitoraggio di Azure | Microsoft Docs
description: Informazioni su come usare avvisi di metriche near real time per monitorare le metriche delle risorse di Azure con una frequenza di circa 1 minuto.
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: 6370f4596e6b20962c6de0dbcbd5f86c3b7777cc
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>Avvisi metriche near real time (anteprima)
Monitoraggio di Azure supporta un nuovo tipo di avvisi delle metriche denominati avvisi delle metriche near real time (anteprima). Questa funzionalità è attualmente in anteprima pubblica.

Gli avvisi delle metriche near real time sono diversi dai normali avvisi delle metriche per alcuni aspetti:

- **Migliore latenza**: gli avvisi delle metriche near real time possono monitorare le modifiche in valori di metrica con una frequenza minima di 1 minuto.
- **Maggiore controllo delle condizioni delle metriche**: è possibile di definire regole di avviso più dettagliate negli avvisi delle metriche near real time. Gli avvisi supportano il monitoraggio dei valori massimo, minimo, medio e totale delle metriche.
- **Monitoraggio combinato di più metriche**: gli avvisi delle metriche near real time possono monitorare più metriche (attualmente un massimo di due) con una singola regola. Viene attivato un avviso se entrambe le metriche violano le rispettive soglie per il periodo di tempo specificato.
- **Sistema di notifica modulare**: gli avvisi delle metriche near real time usano i [gruppi di azioni](monitoring-action-groups.md). È possibile usare i gruppi di azioni per creare azioni modulari. È possibile riutilizzare i gruppi di azioni per più regole di avviso.

> [!NOTE]
> La funzionalità degli avvisi delle metriche near real time sono attualmente disponibili in anteprima pubblica. L'esperienza utente e la funzionalità sono soggette a modifiche.
>

## <a name="resources-you-can-use-with-near-real-time-metric-alerts"></a>Risorse che è possibile usare con gli avvisi delle metriche near real time
Ecco l'elenco completo dei tipi di risorse che sono supportati dagli avvisi delle metriche near real time:

* Microsoft.ApiManagement/service
* Microsoft.Automation/automationAccounts
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Storage/storageAccounts
* Microsoft.Storage/storageAccounts/services
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.CognitiveServices/accounts

## <a name="near-real-time-metric-alerts-for-metrics-that-use-dimensions"></a>Avvisi delle metriche near real time per le metriche con dimensioni
Gli avvisi delle metriche near real time supportano gli avvisi per le metriche con dimensioni. Le dimensioni possono essere usate per filtrare le metriche al livello corretto. Gli avvisi delle metriche near real time per le metriche con dimensioni sono supportati per i tipi di risorsa seguenti:

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts (supportato solo per gli account di archiviazione nelle aree degli Stati Uniti)
* Microsoft.Storage/storageAccounts/services (supportato solo per gli account di archiviazione nelle aree degli Stati Uniti)

## <a name="create-a-near-real-time-metric-alert"></a>Creare un avviso delle metriche near real time
Al momento è possibile creare avvisi delle metriche near real time solo nel portale di Azure. Il supporto per la configurazione degli avvisi delle metriche near real time tramite PowerShell, l'interfaccia della riga di comando di Azure (Azure CLI) e le API REST di Monitoraggio di Azure saranno presto disponibili.

L'esperienza di creazione di avvisi delle metriche near real time è passata nella nuova pagina **Avvisi (anteprima)**. Anche se la pagina Avvisi corrente visualizza **Aggiungi avviso metriche near real time**, l'utente viene reindirizzato alla pagina **Avvisi (anteprima)**.

Per informazioni su come creare un avviso delle metriche near real time, vedere [Creare una regola di avviso nel portale di Azure](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="manage-near-real-time-metric-alerts"></a>Gestire gli avvisi delle metriche near real time
Dopo aver creato un avviso delle metriche near real time, è possibile gestirlo seguendo i passaggi descritti in [Gestire gli avvisi nel portale di Azure](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="payload-schema"></a>Schema del payload

L'operazione POST contiene il payload e lo schema JSON seguenti per tutti gli avvisi delle metriche near real time:

```json
{
    "WebhookName": "Alert1510875839452",
    "RequestBody": {
        "status": "Activated",
        "context": {
            "condition": {
                "metricName": "Percentage CPU",
                "metricUnit": "Percent",
                "metricValue": "17.7654545454545",
                "threshold": "1",
                "windowSize": "10",
                "timeAggregation": "Average",
                "operator": "GreaterThan"
            },
            "resourceName": "ContosoVM1",
            "resourceType": "microsoft.compute/virtualmachines",
            "resourceRegion": "westus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/automationtest/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "timestamp": "2017-11-16T23:54:03.9517451Z",
            "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/microsoft.insights/alertrules/VMMetricAlert1",
            "name": "VMMetricAlert1",
            "description": "A metric alert for the VM Win2012R2",
            "conditionType": "Metric",
            "subscriptionId": "00000000-0000-0000-0000-000000000000",
            "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "resourceGroupName": "ContosoVM"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    },
    "RequestHeader": {
        "Connection": "Keep-Alive",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "azure-insights/0.9",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla nuova [esperienza di Avvisi (anteprima)](monitoring-overview-unified-alerts.md).
* Informazioni sugli [avvisi del log in Avvisi di Azure (anteprima)](monitor-alerts-unified-log.md).
* Informazioni sugli [avvisi in Azure](monitoring-overview-alerts.md).