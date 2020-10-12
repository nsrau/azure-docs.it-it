---
title: Panoramica dei log e delle metriche del firewall di Azure
description: È possibile monitorare Firewall di Azure con i log del firewall. È possibile usare anche i log attività per controllare le operazioni eseguite sulle risorse di Firewall di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 09/10/2020
ms.author: victorh
ms.openlocfilehash: a0333f9afa69b533ac28dc302987e6d057bfeeb1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90090160"
---
# <a name="azure-firewall-logs-and-metrics"></a>Log e metriche di Firewall di Azure

È possibile monitorare Firewall di Azure con i log del firewall. È possibile usare anche i log attività per controllare le operazioni eseguite sulle risorse di Firewall di Azure.

Alcuni di questi log sono accessibili tramite il portale. I log possono essere inviati a [log di monitoraggio di Azure](../azure-monitor/insights/azure-networking-analytics.md), archiviazione e hub eventi e analizzati nei log di monitoraggio di Azure o in strumenti diversi, ad esempio Excel e Power bi.

Le metriche sono leggere e possono supportare scenari quasi in tempo reale, rendendoli utili per gli avvisi e il rilevamento rapido dei problemi.

## <a name="diagnostic-logs"></a>Log di diagnostica

 I log di diagnostica seguenti sono disponibili per Firewall di Azure:

* **Log delle regole di applicazione**

   Il log delle regole dell'applicazione viene salvato in un account di archiviazione, trasmesso a hub eventi e/o inviato ai log di monitoraggio di Azure solo se è stato abilitato per ogni firewall di Azure. Ogni nuova connessione che corrisponde a una delle regole di applicazione configurate genera un log per la connessione accettata/negata. I dati vengono registrati in formato JSON, come illustrato nell'esempio seguente:

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

   Il log della regola di rete viene salvato in un account di archiviazione, trasmesso a hub eventi e/o inviato ai log di monitoraggio di Azure solo se è stato abilitato per ogni firewall di Azure. Ogni nuova connessione che corrisponde a una delle regole di rete configurate genera un log per la connessione accettata/negata. I dati vengono registrati in formato JSON, come illustrato nell'esempio seguente:

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

* **Log proxy DNS**

   Il log del proxy DNS viene salvato in un account di archiviazione, trasmesso a hub eventi e/o inviato ai log di monitoraggio di Azure solo se è stato abilitato per ogni firewall di Azure. Questo log tiene traccia dei messaggi DNS a un server DNS configurato con il proxy DNS. I dati vengono registrati in formato JSON, come illustrato negli esempi seguenti:


   ```
   Category: DNS proxy logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   Success:
   ```json
   {
     "category": "AzureFirewallDnsProxy",
     "time": "2020-09-02T19:12:33.751Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallDnsProxyLog",
     "properties": {
         "msg": "DNS Request: 11.5.0.7:48197 – 15676 AAA IN md-l1l1pg5lcmkq.blob.core.windows.net. udp 55 false 512 NOERROR - 0 2.000301956s"
     }
   }
   ```

   Non riuscita:

   ```json
   {
     "category": "AzureFirewallDnsProxy",
     "time": "2020-09-02T19:12:33.751Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallDnsProxyLog",
     "properties": {
         "msg": " Error: 2 time.windows.com.reddog.microsoft.com. A: read udp 10.0.1.5:49126->168.63.129.160:53: i/o timeout”
     }
   }
   ```

   formato messaggi:

   `[client’s IP address]:[client’s port] – [query ID] [type of the request] [class of the request] [name of the request] [protocol used] [request size in bytes] [EDNS0 DO (DNSSEC OK) bit set in the query] [EDNS0 buffer size advertised in the query] [response CODE] [response flags] [response size] [response duration]`

Sono disponibili tre opzioni di archiviazione dei log:

* **Account di archiviazione**: ideali quando i log vengono archiviati per un periodo più lungo ed esaminati quando necessario.
* **Hub eventi**: ottima opzione per l'integrazione con altri strumenti di gestione delle informazioni di sicurezza e degli eventi (SEIM) per ricevere avvisi sulle risorse.
* **Log di monitoraggio di Azure**: i log di monitoraggio di Azure sono ideali per il monitoraggio generale in tempo reale dell'applicazione o l'analisi delle tendenze.

## <a name="activity-logs"></a>Log attività

   Le voci dei log attività vengono raccolte per impostazione predefinita e possono essere visualizzate nel portale di Azure.

   È possibile usare i [log attività di Azure](../azure-resource-manager/management/view-activity-logs.md) (precedentemente noti come log operativi e log di controllo) per visualizzare tutte le operazioni inviate alla sottoscrizione di Azure.

## <a name="metrics"></a>Metriche

Le metriche in monitoraggio di Azure sono valori numerici che descrivono alcuni aspetti di un sistema in un determinato momento. Le metriche vengono raccolte ogni minuto e sono utili per gli avvisi perché possono essere campionate spesso. Un avviso può essere generato rapidamente con una logica relativamente semplice.

Per il firewall di Azure sono disponibili le metriche seguenti:

- Numero di **passaggi delle regole dell'applicazione** : numero di volte in cui è stata raggiunta una regola dell'applicazione.

    Unità: conteggio

- Numero di **passaggi delle regole di rete** : numero di volte in cui è stata raggiunta una regola di rete.

    Unità: conteggio

- **Dati elaborati** : somma dei dati attraversando il firewall in un intervallo di tempo specificato.

    Unità: byte

- Velocità **effettiva** -velocità dei dati attraversando il firewall al secondo.

    Unità: bit al secondo

- **Stato di integrità del firewall** : indica lo stato di integrità del firewall in base alla disponibilità della porta SNAT.

    Unità: percentuale

   Questa metrica dispone di due dimensioni:
  - Stato: i valori possibili sono *integro*, *danneggiato*, non *integro*.
  - Motivo: indica il motivo dello stato corrispondente del firewall. 

     Se vengono usate le porte SNAT > 95%, vengono considerate esaurite e l'integrità è 50% con status =**Degraded** e Reason =**SNAT Port**. Il firewall continua a elaborare il traffico e le connessioni esistenti non sono interessate. Tuttavia, le nuove connessioni potrebbero non essere stabilite in modo intermittente.

     Se vengono usate porte SNAT < 95%, il firewall viene considerato integro e l'integrità viene visualizzata come 100%.

     Se non viene segnalato alcun utilizzo delle porte SNAT, l'integrità viene visualizzata come 0%. 

- **Utilizzo delle porte SNAT** : la percentuale di porte SNAT utilizzate dal firewall.

    Unità: percentuale

   Quando si aggiungono altri indirizzi IP pubblici al firewall, sono disponibili più porte SNAT, cosa che ne riduce l'utilizzo. Inoltre, quando il firewall viene ridimensionato per diversi motivi, ad esempio in termini di CPU o velocità effettiva, diventano disponibili anche porte SNAT aggiuntive. Pertanto, una determinata percentuale di utilizzo delle porte SNAT può diventare inattiva senza aggiungere indirizzi IP pubblici, solo perché il servizio è stato scalato orizzontalmente. È possibile controllare direttamente il numero di indirizzi IP pubblici disponibili per aumentare le porte disponibili sul firewall. Tuttavia, non è possibile controllare direttamente il ridimensionamento del firewall.


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come monitorare log e metriche del Firewall di Azure, vedere [Esercitazione: monitorare i log del Firewall di Azure](tutorial-diagnostics.md).

- Per altre informazioni sulle metriche in monitoraggio di Azure, vedere [metriche in monitoraggio di Azure](../azure-monitor/platform/data-platform-metrics.md).
