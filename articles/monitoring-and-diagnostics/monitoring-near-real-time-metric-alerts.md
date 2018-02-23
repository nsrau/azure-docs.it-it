---
title: Avvisi delle metriche near real time in Monitoraggio di Azure | Microsoft Docs
description: Gli avvisi delle metriche near real time consentono di monitorare le metriche delle risorse di Azure con una frequenza di 1 minuto.
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
ms.openlocfilehash: d3e88a98e0ba93a630d131c25ca4dd5cb16f1b1a
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>Avvisi metriche near real time (anteprima)
Monitoraggio di Azure supporta ora un nuovo tipo di avvisi delle metriche denominati Avvisi metriche near real time (anteprima). Questa funzionalità è attualmente in anteprima pubblica.
Questi avvisi sono diversi dagli avvisi delle metriche regolari per alcuni aspetti

- **Migliore latenza** - gli avvisi delle metriche near real time possono monitorare le modifiche in valori di metrica di 1 minuto.
- **Maggiore controllo delle condizioni delle metriche** - gli avvisi delle metriche near real time consentono agli utenti di definire regole di avviso più dettagliate. Gli avvisi ora supportano il monitoraggio dei valori massimo, minimo, medio e totale delle metriche.
- **Monitoraggio combinato di più metriche** - gli avvisi delle metriche near real time possono monitorare più metriche (attualmente due) con una singola regola. L'avviso viene attivato se entrambe le metriche violano le rispettive soglie per il periodo di tempo specificato.
- **Sistema di notifica modulare** - gli avvisi delle metriche near real time usano i [gruppi di azioni](monitoring-action-groups.md). Questa funzionalità consente agli utenti di creare azioni in modo modulare e riusarle per molte regole di avviso.

> [!NOTE]
> Gli avvisi delle metriche near real time sono attualmente disponibili in anteprima pubblica. L'esperienza utente e la funzionalità sono soggette a modifiche.
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>Per quali risorse è possibile creare gli avvisi delle metriche near real time?
L'elenco completo dei tipi di risorse che sono supportati dagli avvisi delle metriche near real time:

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

## <a name="near-real-time-metric-alerts-on-metrics-with-dimensions"></a>Avvisi quasi in tempo reale per le metriche con dimensioni
Gli avvisi quasi in tempo reale sono supportati per le metriche con dimensioni. Le dimensioni rappresentano un modo per filtrare le metriche al livello corretto. Gli avvisi quasi in tempo reale per le metriche con dimensioni sono supportati per i tipi di risorsa seguenti

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts (supportato solo per gli account di archiviazione nelle aree degli Stati Uniti)
* Microsoft.Storage/storageAccounts/services (supportato solo per gli account di archiviazione nelle aree degli Stati Uniti)


## <a name="create-a-near-real-time-metric-alert"></a>Creare un avviso delle metriche near real time
Attualmente gli avvisi delle metriche near real time possono essere creati solo tramite il portale di Azure. Il supporto per la configurazione degli avvisi delle metriche near real time tramite PowerShell, l'interfaccia della riga di comando (CLI) e API REST di Monitoraggio di Azure sarà presto disponibile.

L'esperienza di creazione per gli avvisi delle metriche quasi in tempo reale è passata nella nuova esperienza **Avvisi (anteprima)**. Anche se la pagina Avvisi corrente visualizza **Aggiungi Avviso delle metriche quasi in tempo reale**, l'utente viene reindirizzato alla nuova esperienza.

È possibile creare un avviso delle metriche quasi in tempo reale tramite la procedura descritta [qui](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="managing-near-real-time-metric-alerts"></a>Gestione degli avvisi delle metriche near real time
Dopo averlo creato, è possibile gestire un **avviso delle metriche in tempo reale** tramite la procedura descritta [qui](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="payload-schema"></a>Schema del payload

L'operazione POST contiene il payload e lo schema JSON seguenti per tutti gli avvisi delle metriche quasi in tempo reale.

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

* [Altre informazioni sulla nuova esperienza di avvisi (anteprima)](monitoring-overview-unified-alerts.md)
* [Informazioni sugli avvisi relativi ai log negli avvisi di Azure (anteprima)](monitor-alerts-unified-log.md)
* [Informazioni sugli avvisi in Azure](monitoring-overview-alerts.md)