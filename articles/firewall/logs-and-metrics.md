---
title: Panoramica dei log e delle metriche del firewall di Azure
description: Questo articolo è una panoramica dei log e delle metriche di diagnostica del firewall di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 8524c8f05a5d48755ab1ccca62f0fd53870190bb
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640249"
---
# <a name="azure-firewall-logs-and-metrics"></a>Metriche e log del firewall di Azure

È possibile monitorare Firewall di Azure con i log del firewall. È possibile usare anche i log attività per controllare le operazioni eseguite sulle risorse di Firewall di Azure.

Alcuni di questi log sono accessibili tramite il portale. I log possono essere inviati ai [log di Monitoraggio di Azure](../azure-monitor/insights/azure-networking-analytics.md), Archiviazione e Hub eventi ed essere analizzati nei log di Monitoraggio di Azure o con strumenti diversi come ad esempio Excel e Power BI.

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

Sono disponibili tre opzioni di archiviazione dei log:

* **Account di archiviazione**: sono la soluzione ideale per i log quando questi vengono archiviati per un periodo più lungo ed esaminati quando necessario.
* **Hub eventi**: ottima opzione per l'integrazione con altri strumenti di gestione degli eventi e delle informazioni di sicurezza (SEIM) per ricevere avvisi sulle risorse.
* **Log di Monitoraggio di Azure**: i log di Monitoraggio di Azure sono ideali per il monitoraggio generale in tempo reale dell'applicazione o per l'analisi delle tendenze.

## <a name="activity-logs"></a>Log attività

   Le voci dei log attività vengono raccolte per impostazione predefinita e possono essere visualizzate nel portale di Azure.

   È possibile usare i [log attività di Azure](../azure-resource-manager/resource-group-audit.md) (precedentemente noti come log operativi e log di controllo) per visualizzare tutte le operazioni inviate alla sottoscrizione di Azure.

## <a name="metrics"></a>metrics

Le metriche in monitoraggio di Azure sono valori numerici che descrivono alcuni aspetti di un sistema in un determinato momento. Le metriche vengono raccolte ogni minuto e sono utili per gli avvisi perché possono essere campionate spesso. Un avviso può essere generato rapidamente con una logica relativamente semplice.

Per il firewall di Azure sono disponibili le metriche seguenti:

- Numero di **passaggi delle regole dell'applicazione** : numero di volte in cui è stata raggiunta una regola dell'applicazione.

    Unità: conteggio

- **Dati elaborati** : quantità di dati attraversando il firewall.

    Unità: byte

- **Stato di integrità del firewall** : indica l'integrità del firewall.

    Unità: percentuale

   Questa metrica ha due dimensioni:
  - **Stato**: I valori possibilisono integro, *danneggiato*,non integro.
  - **Motivo**: Indica il motivo dello stato corrispondente del firewall. Ad esempio, può indicare le *porte SNAT* se lo stato del firewall è danneggiato o non integro.



- Numero di **passaggi delle regole di rete** : numero di volte in cui è stata raggiunta una regola di rete.

    Unità: conteggio

- **Utilizzo delle porte SNAT** : la percentuale di porte SNAT utilizzate dal firewall.

    Unità: percentuale

   Quando si aggiungono altri indirizzi IP pubblici al firewall, sono disponibili più porte SNAT, riducendo l'utilizzo delle porte SNAT. Inoltre, quando il firewall viene scalato per diversi motivi, ad esempio CPU o velocità effettiva, diventano disponibili anche porte SNAT aggiuntive. Pertanto, una determinata percentuale di utilizzo delle porte SNAT può diventare inattiva senza aggiungere indirizzi IP pubblici, solo perché il servizio è stato scalato orizzontalmente. È possibile controllare direttamente il numero di indirizzi IP pubblici disponibili per aumentare le porte disponibili sul firewall. Tuttavia, non è possibile controllare direttamente il ridimensionamento del firewall. Attualmente, le porte SNAT vengono aggiunte solo per i primi cinque indirizzi IP pubblici.   


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come monitorare i log e le metriche del firewall di [Azure, vedere Esercitazione: Monitorare i log di Firewall di Azure](tutorial-diagnostics.md).

- Per altre informazioni sulle metriche in monitoraggio di Azure, vedere [metriche in monitoraggio di Azure](../azure-monitor/platform/data-platform-metrics.md).