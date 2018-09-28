---
title: Panoramica dei log del Firewall di Azure
description: Questo articolo è una panoramica dei log di diagnostica del Firewall di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 6d8d0b2dc0f6baf48d2aacb9c7203937aef08d15
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957798"
---
# <a name="azure-firewall-logs"></a>Log del Firewall di Azure

È possibile monitorare Firewall di Azure con i log del firewall. È possibile usare anche i log attività per controllare le operazioni eseguite sulle risorse di Firewall di Azure.

Alcuni di questi log sono accessibili tramite il portale. I log possono essere inviati a [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Archiviazione e Hub eventi ed essere analizzati in Log Analytics o con strumenti diversi come ad esempio Excel e Power BI.

## <a name="diagnostic-logs"></a>Log di diagnostica

 I log di diagnostica seguenti sono disponibili per Firewall di Azure:

* **Log delle regole di applicazione**

   Il log delle regole di applicazione viene salvato in un account di archiviazione, trasmesso all'Hub eventi e/o inviato a Log Analytics solo se è stato abilitato per ogni Firewall di Azure. Ogni nuova connessione che corrisponde a una delle regole di applicazione configurate genera un log per la connessione accettata/negata. I dati vengono registrati in formato JSON, come illustrato nell'esempio seguente:

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

   Il log delle regole di rete viene salvato in un account di archiviazione, trasmesso all'Hub eventi e/o inviato a Log Analytics solo se è stato abilitato per ogni Firewall di Azure. Ogni nuova connessione che corrisponde a una delle regole di rete configurate genera un log per la connessione accettata/negata. I dati vengono registrati in formato JSON, come illustrato nell'esempio seguente:

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

* **Account di archiviazione**: ideali quando i log vengono archiviati per un periodo più lungo ed esaminati quando necessario.
* **Hub eventi**: ottima opzione per l'integrazione con altri strumenti di gestione delle informazioni di sicurezza e degli eventi (SEIM) per ricevere avvisi sulle risorse.
* **Log Analytics**: soluzione adatta al monitoraggio generale in tempo reale dell'applicazione o all'analisi delle tendenze.

## <a name="activity-logs"></a>Log attività

   Le voci dei log attività vengono raccolte per impostazione predefinita e possono essere visualizzate nel portale di Azure.

   È possibile usare i [log attività di Azure](../azure-resource-manager/resource-group-audit.md), chiamati in precedenza log operativi e log di controllo, per visualizzare tutte le operazioni inviate alla sottoscrizione di Azure.


## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come monitorare log e metriche del Firewall di Azure, vedere [Esercitazione: monitorare i log del Firewall di Azure](tutorial-diagnostics.md).