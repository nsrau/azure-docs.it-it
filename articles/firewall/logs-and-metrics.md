---
title: Panoramica dei log del Firewall di Azure
description: Questo articolo è una panoramica dei log di diagnostica del Firewall di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: c129c394f3d694b832722287027c1f9e58028a33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61065853"
---
# <a name="azure-firewall-logs"></a>Log del Firewall di Azure

È possibile monitorare Firewall di Azure con i log del firewall. È possibile usare anche i log attività per controllare le operazioni eseguite sulle risorse di Firewall di Azure.

Alcuni di questi log sono accessibili tramite il portale. I log possono essere inviati ai [log di Monitoraggio di Azure](../azure-monitor/insights/azure-networking-analytics.md), Archiviazione e Hub eventi ed essere analizzati nei log di Monitoraggio di Azure o con strumenti diversi come ad esempio Excel e Power BI.

## <a name="diagnostic-logs"></a>Log di diagnostica

 I log di diagnostica seguenti sono disponibili per Firewall di Azure:

* **Log delle regole di applicazione**

   Salvataggio del log di regole di applicazione a un account di archiviazione, trasmessi a hub eventi e/o inviati a log di monitoraggio di Azure solo se è stato abilitato per ogni Firewall di Azure. Ogni nuova connessione che corrisponde a una delle regole di applicazione configurate genera un log per la connessione accettata/negata. I dati vengono registrati in formato JSON, come illustrato nell'esempio seguente:

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **Log delle regole di rete**

   Salvataggio del log di regola di rete a un account di archiviazione, trasmessi a hub eventi e/o inviati a log di monitoraggio di Azure solo se è stato abilitato per ogni Firewall di Azure. Ogni nuova connessione che corrisponde a una delle regole di rete configurate genera un log per la connessione accettata/negata. I dati vengono registrati in formato JSON, come illustrato nell'esempio seguente:

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

Sono disponibili tre opzioni di archiviazione dei log:

* **Account di archiviazione**: sono la soluzione ideale per i log quando questi vengono archiviati per un periodo più lungo ed esaminati quando necessario.
* **Hub eventi**: ottima opzione per l'integrazione con altri strumenti di gestione degli eventi e delle informazioni di sicurezza (SEIM) per ricevere avvisi sulle risorse.
* **Log di Monitoraggio di Azure**: i log di Monitoraggio di Azure sono ideali per il monitoraggio generale in tempo reale dell'applicazione o per l'analisi delle tendenze.

## <a name="activity-logs"></a>Log attività

   Le voci dei log attività vengono raccolte per impostazione predefinita e possono essere visualizzate nel portale di Azure.

   È possibile usare i [log attività di Azure](../azure-resource-manager/resource-group-audit.md), chiamati in precedenza log operativi e log di controllo, per visualizzare tutte le operazioni inviate alla sottoscrizione di Azure.


## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come monitorare le metriche e log del Firewall di Azure, vedere [esercitazione: Monitorare i log di Firewall di Azure](tutorial-diagnostics.md).