---
title: Filtri per connessioni IP del bus di servizio di Azure | Microsoft Docs
description: Come usare i filtri IP per bloccare le connessioni da indirizzi IP specifici al bus di servizio di Azure.
services: service-bus
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: clemensv
ms.openlocfilehash: c6e9eef762d4a9eb95685d94c61ce10d499bb155
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884804"
---
# <a name="use-ip-filters"></a>Usare i filtri IP

Per gli scenari in cui il bus di servizio di Azure è accessibile solo da determinati siti noti, la funzionalità *Filtro IP* consente di configurare regole per rifiutare o accettare traffico proveniente da indirizzi IPv4 specifici, ad esempio quelli di un gateway NAT aziendale.

## <a name="when-to-use"></a>Quando usare le autorizzazioni

Di seguito sono indicati due casi d'uso specifici in cui è utile bloccare gli endpoint del bus di servizio per determinati indirizzi IP:

- Il bus di servizio deve ricevere il traffico solo da un intervallo di indirizzi IP specificato e rifiutare tutto il resto. Si usa ad esempio il bus di servizio con [Azure ExpressRoute][express-route] per creare connessioni private con l'infrastruttura locale.
- È necessario rifiutare il traffico proveniente da indirizzi IP identificati come sospetti dall'amministratore del bus di servizio.

## <a name="how-filter-rules-are-applied"></a>Come vengono applicate le regole di filtro

Le regole del filtro IP vengono applicate a livello dello spazio dei nomi del bus di servizio. Vengono pertanto applicate a tutte le connessioni provenienti dai client con qualsiasi protocollo supportato.

Qualsiasi tentativo di connessione proveniente da un indirizzo IP che corrisponde a una regola IP di rifiuto nello spazio dei nomi del bus di servizio viene rifiutato come non autorizzato. Nella risposta non viene fatto riferimento alla regola IP.

## <a name="default-setting"></a>Impostazione predefinita

Per impostazione predefinita, la griglia **Filtro IP** nel portale per il bus di servizio è vuota. Questa impostazione predefinita indica che lo spazio dei nomi accetta connessioni da qualsiasi indirizzo IP. Questa impostazione predefinita equivale a una regola che accetta l'intervallo di indirizzi IP 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Valutazione delle regole del filtro IP

Le regole del filtro IP vengono applicate in ordine e la prima regola corrispondente all'indirizzo IP determina l'azione di accettazione o rifiuto.

Se ad esempio si vogliono accettare gli indirizzi nell'intervallo 70.37.104.0/24 e rifiutare tutti gli altri, la prima regola nella griglia dovrà accettare l'intervallo di indirizzi 70.37.104.0/24. La regola successiva dovrà rifiutare tutti gli indirizzi usando l'intervallo 0.0.0.0/0.

> [!NOTE]
> Il rifiuto di indirizzi IP può impedire l'interazione di altri servizi di Azure (ad esempio Analisi di flusso di Azure, Macchine virtuali di Azure o Device Explorer nel portale) con il bus di servizio.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Creazione di una regola di rete virtuale con i modelli di Azure Resource Manager

> ![IMPORTANTE] Le reti virtuali sono supportate solo nel **livello Premium** del bus di servizio.

Il modello di Resource Manager seguente consente di aggiungere una regola di rete virtuale a uno spazio dei nomi esistente del bus di servizio.

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
            "type": "Microsoft.ServiceBus/Namespaces/IPFilterRules",
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

Per limitare l'accesso al bus di servizio dalle reti virtuali di Azure, vedere il collegamento seguente:

- [Usare gli endpoint del servizio Rete virtuale con il bus di servizio di Azure][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services