---
title: Panoramica dei log e delle metriche di Firewall di AzureOverview of Azure Firewall logs and metrics
description: È possibile monitorare Firewall di Azure con i log del firewall. È possibile usare anche i log attività per controllare le operazioni eseguite sulle risorse di Firewall di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/22/2020
ms.author: victorh
ms.openlocfilehash: 89c6700d5df3bcef1332121c3cf7d8f720fe054c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76315032"
---
# <a name="azure-firewall-logs-and-metrics"></a>Log e metriche di Firewall di Azure

È possibile monitorare Firewall di Azure con i log del firewall. È possibile usare anche i log attività per controllare le operazioni eseguite sulle risorse di Firewall di Azure.

Alcuni di questi log sono accessibili tramite il portale. I log possono essere inviati ai [log di Monitoraggio di Azure](../azure-monitor/insights/azure-networking-analytics.md), Archiviazione e Hub eventi ed essere analizzati nei log di Monitoraggio di Azure o con strumenti diversi come ad esempio Excel e Power BI.

Le metriche sono leggere e possono supportare scenari quasi in tempo reale, rendendoli utili per gli avvisi e il rilevamento rapido dei problemi.

## <a name="diagnostic-logs"></a>Log di diagnostica

 I log di diagnostica seguenti sono disponibili per Firewall di Azure:

* **Log delle regole di applicazione**

   Il log delle regole dell'applicazione viene salvato in un account di archiviazione, trasmesso agli hub eventi e/o inviato ai log di Monitoraggio di Azure solo se è stato abilitato per ogni firewall di Azure.The Application rule log is saved to a storage account, streamed to Event hubs and/or sent to Azure Monitor logs only if you've enabled it for each Azure Firewall. Ogni nuova connessione che corrisponde a una delle regole di applicazione configurate genera un log per la connessione accettata/negata. I dati vengono registrati in formato JSON, come illustrato nell'esempio seguente:

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

   Il log delle regole di rete viene salvato in un account di archiviazione, trasmesso agli hub eventi e/o inviato ai log di Monitoraggio di Azure solo se è stato abilitato per ogni firewall di Azure.The Network rule log is saved to a storage account, streamed to Event hubs and/or sent to Azure Monitor logs only if you've enabled it for each Azure Firewall. Ogni nuova connessione che corrisponde a una delle regole di rete configurate genera un log per la connessione accettata/negata. I dati vengono registrati in formato JSON, come illustrato nell'esempio seguente:

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
* Log di **Monitoraggio di Azure:** i log di Monitoraggio di Azure sono ottimali per il monitoraggio generale in tempo reale dell'applicazione o per l'esborrazione delle tendenze.

## <a name="activity-logs"></a>Log attività

   Le voci dei log attività vengono raccolte per impostazione predefinita e possono essere visualizzate nel portale di Azure.

   È possibile usare i log attività di Azure (precedentemente noti come log operativi e log di controllo) per visualizzare tutte le operazioni inviate alla sottoscrizione di Azure.You can use [Azure activity logs](../azure-resource-manager/management/view-activity-logs.md) (formerly known as operational logs and audit logs) to view all operations submitted to your Azure subscription.

## <a name="metrics"></a>Metriche

Le metriche in Monitoraggio di Azure sono valori numerici che descrivono alcuni aspetti di un sistema in un determinato momento. Le metriche vengono raccolte ogni minuto e sono utili per gli avvisi perché possono essere campionate di frequente. Un avviso può essere attivato rapidamente con una logica relativamente semplice.

Per Firewall di Azure sono disponibili le metriche seguenti:The following metrics are available for Azure Firewall:

- **Conteggio passaggi regole applicazione:** il numero di volte in cui una regola dell'applicazione è stata raggiunta.

    Unità: conteggio

- **Numero** di passaggi di una regola di rete.

    Unità: conteggio

- **Dati elaborati** - Quantità di dati che attraversano il firewall.

    Unità: byte

- **Stato integrità firewall:** indica l'integrità del firewall in base alla disponibilità delle porte SNAT.

    Unità: percentuale

   Questa metrica ha due dimensioni:
  - Stato: i valori possibili sono *Healthy*, *Degraded*, *Unhealthy*.
  - Motivo: indica il motivo dello stato corrispondente del firewall. 

     Se vengono utilizzate le porte SNAT > 95%, vengono considerate esaurite e l'integrità è del 50% con**stato, Degradato** e motivo,**porta SNAT**. Il firewall mantiene il traffico di elaborazione e le connessioni esistenti non sono interessate. Tuttavia, le nuove connessioni potrebbero non essere stabilite in modo intermittente.

     Se le porte SNAT vengono utilizzate < 95%, il firewall viene considerato integro e l'integrità viene visualizzata come 100%.

     Se non viene segnalato alcun utilizzo delle porte SNAT, l'integrità viene visualizzato come 0%. 

- **Utilizzo delle porte SNAT:** la percentuale di porte SNAT utilizzate dal firewall.

    Unità: percentuale

   Quando si aggiungono più indirizzi IP pubblici al firewall, sono disponibili più porte SNAT, riducendo l'utilizzo delle porte SNAT. Inoltre, quando il firewall viene scalato orizzontalmente per diversi motivi (ad esempio, CPU o velocità effettiva) diventano disponibili anche porte SNAT aggiuntive. In modo efficace, una determinata percentuale di utilizzo delle porte SNAT potrebbe scendere senza aggiungere indirizzi IP pubblici, solo perché il servizio è stato scalato orizzontalmente. È possibile controllare direttamente il numero di indirizzi IP pubblici disponibili per aumentare le porte disponibili sul firewall. Tuttavia, non è possibile controllare direttamente il ridimensionamento del firewall. Attualmente, le porte SNAT vengono aggiunte solo per i primi cinque indirizzi IP pubblici.   


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come monitorare log e metriche del Firewall di Azure, vedere [Esercitazione: monitorare i log del Firewall di Azure](tutorial-diagnostics.md).

- Per altre informazioni sulle metriche in Monitoraggio di Azure, vedere [Metriche in Monitoraggio di Azure.To](../azure-monitor/platform/data-platform-metrics.md)learn more about metrics in Azure Monitor, see Metrics in Azure Monitor.
