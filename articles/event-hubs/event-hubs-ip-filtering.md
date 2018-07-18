---
title: Filtri per connessioni IP di Hub eventi di Azure | Microsoft Docs
description: Usare i filtri IP per bloccare le connessioni da indirizzi IP specifici ad Hub eventi di Azure.
services: event-hubs
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: clemensv
ms.openlocfilehash: 425a5b641fbfd2e52e1294c6317b51ff2a584aa3
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034825"
---
# <a name="use-ip-filters"></a>Usare i filtri IP

Per gli scenari in cui Hub eventi di Azure è accessibile solo da determinati siti noti, la funzionalità *Filtro IP* consente di configurare regole per rifiutare o accettare traffico proveniente da indirizzi IPv4 specifici, ad esempio quelli di un gateway NAT aziendale.

## <a name="when-to-use"></a>Quando usare le autorizzazioni

Di seguito sono indicati due importanti casi d'uso in cui è utile bloccare gli endpoint di Hub eventi per determinati indirizzi IP:

- Gli hub eventi devono ricevere il traffico solo da un intervallo di indirizzi IP specificato e rifiutare tutto il resto. Si usa ad esempio Hub eventi con [Azure ExpressRoute][express-route] per creare connessioni private con l'infrastruttura locale. 
- È necessario rifiutare il traffico proveniente da indirizzi IP identificati come sospetti dall'amministratore di Hub eventi.

## <a name="how-filter-rules-are-applied"></a>Come vengono applicate le regole di filtro

Le regole del filtro IP vengono applicate a livello dello spazio dei nomi di Hub eventi. Vengono pertanto applicate a tutte le connessioni provenienti dai client con qualsiasi protocollo supportato.

Qualsiasi tentativo di connessione proveniente da un indirizzo IP che corrisponde a una regola IP di rifiuto nello spazio dei nomi di Hub eventi viene rifiutato come non autorizzato. Nella risposta non viene fatto riferimento alla regola IP.

## <a name="default-setting"></a>Impostazione predefinita

Per impostazione predefinita, la griglia **Filtro IP** nel portale per Hub eventi è vuota. Questa impostazione predefinita indica che l'hub eventi in uso accetta connessioni da qualsiasi indirizzo IP. Questa impostazione predefinita equivale a una regola che accetta l'intervallo di indirizzi IP 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Valutazione delle regole del filtro IP

Le regole del filtro IP vengono applicate in ordine e la prima regola corrispondente all'indirizzo IP determina l'azione di accettazione o rifiuto.

Se ad esempio si vogliono accettare gli indirizzi nell'intervallo 70.37.104.0/24 e rifiutare tutti gli altri, la prima regola nella griglia dovrà accettare l'intervallo di indirizzi 70.37.104.0/24. La regola successiva dovrà rifiutare tutti gli indirizzi usando l'intervallo 0.0.0.0/0.

> [!NOTE]
> Il rifiuto di indirizzi IP può impedire l'interazione di altri servizi di Azure (ad esempio Analisi di flusso di Azure, Macchine virtuali di Azure o Device Explorer nel portale) con Hub eventi.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Creazione di una regola di rete virtuale con i modelli di Azure Resource Manager

Il modello di Resource Manager seguente consente di aggiungere una regola di rete virtuale a uno spazio dei nomi esistente di Hub eventi.

Parametri del modello:

- **ipFilterRuleName** deve essere univoco e costituito da una stringa alfanumerica che non fa distinzione tra maiuscole e minuscole e ha una lunghezza massima di 128 caratteri.
- **ipFilterAction** specifica **Reject** o **Accept** come azione da applicare per la regola del filtro IP.
- **ipMask** è un singolo indirizzo IPv4 o un blocco di indirizzi IP in notazione CIDR. Ad esempio, nella notazione CIDR, 70.37.104.0/24 rappresenta i 256 indirizzi IPv4, da 70.37.104.0 a 70.37.104.255, con 24 che indica il numero di bit di prefisso significativi per l'intervallo.

```json
{  
   "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{     
          "namespaceName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the namespace"
             }
          },
          "ipFilterRuleName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the Authorization rule"
             }
          },
          "ipFilterAction":{  
             "type":"string",
             "allowedValues": ["Reject", "Accept"],
             "metadata":{  
                "description":"IP Filter Action"
             }
          },
          "IpMask":{  
             "type":"string",
             "metadata":{  
                "description":"IP Mask"
             }
          }
      },
    "resources": [
        {
            "apiVersion": "2018-01-01-preview",
            "name": "[concat(parameters('namespaceName'), '/', parameters('ipFilterRuleName'))]",
            "type": "Microsoft.EventHub/Namespaces/IPFilterRules",
            "properties": {
                "FilterName":"[parameters('ipFilterRuleName')]",
                "Action":"[parameters('ipFilterAction')]",              
                "IpMask": "[parameters('IpMask')]"
            }
        } 
    ]
}
```

Per distribuire il modello, seguire le istruzioni per [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Passaggi successivi

Per limitare l'accesso ad Hub eventi dalle reti virtuali di Azure, vedere il collegamento seguente:

- [Usare gli endpoint del servizio Rete virtuale con Hub eventi di Azure][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md