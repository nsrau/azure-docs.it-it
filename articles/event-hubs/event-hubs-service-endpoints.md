---
title: Endpoint del servizio e regole della rete virtuale per Hub eventi di Azure | Microsoft Docs
description: Aggiungere un endpoint del servizio Microsoft.EventHub a una rete virtuale.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: shvija
ms.openlocfilehash: c5114af69a24ac2f2723becf846ff3712a7afdbb
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2018
ms.locfileid: "40002822"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Usare gli endpoint del servizio della rete virtuale con Hub eventi di Azure

L'integrazione di Hub eventi con gli [endpoint del servizio della rete virtuale][vnet-sep] consente di proteggere l'accesso alle funzionalità di messaggistica da carichi di lavoro come macchine virtuali associate a reti virtuali, con il percorso del traffico di rete protetto a entrambe le estremità. 

Una volta configurato per essere associato ad almeno un endpoint del servizio della subnet della rete virtuale, lo spazio dei nomi di Hub eventi corrispondente non accetta più traffico se non dalle reti virtuali autorizzate. Dal punto di vista della rete virtuale, l'associazione di uno spazio dei nomi di Hub eventi a un endpoint del servizio consente di configurare un tunnel di rete isolato dalla subnet della rete virtuale al servizio di messaggistica.

Il risultato è una relazione privata e isolata tra i carichi di lavoro associati alla subnet e lo spazio dei nomi di Hub eventi corrispondente, nonostante l'indirizzo di rete osservabile dell'endpoint del servizio di messaggistica sia in un intervallo di IP pubblici.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Scenari di sicurezza avanzati resi possibili dall'integrazione della rete virtuale 

Per le soluzioni con meccanismi di sicurezza rigidi e suddivisi in vari compartimenti e per le quali le subnet della rete virtuale offrono la segmentazione tra i servizi nei diversi compartimenti, sono in genere comunque necessari percorsi di comunicazione tra i servizi che risiedono in tali compartimenti.

Qualsiasi route IP immediata tra i compartimenti, incluse quelle destinate al traffico HTTPS su TCP/IP, comportano il rischio di sfruttamento delle vulnerabilità dal livello rete verso l'alto. I servizi di messaggistica offrono percorsi di comunicazione completamente isolati, in cui i messaggi vengono anche scritti su disco durante la transizione tra le parti coinvolte. I carichi di lavoro in due reti virtuali distinte associate alla stessa istanza di Hub eventi possono comunicare in modo efficiente e affidabile tramite messaggi, pur mantenendo l'integrità del confine di isolamento rete.
 
Questo significa che le soluzioni cloud con requisiti di sicurezza elevati non solo possono ottenere l'accesso alle funzionalità di messaggistica asincrona scalabili e affidabili leader del settore di Azure, ma possono ora usare la messaggistica per creare percorsi di comunicazione tra compartimenti sicuri della soluzione, intrinsecamente più sicuri di quanto sia possibile con qualsiasi modalità di comunicazione peer-to-peer, inclusi i protocolli HTTPS e altri protocolli socket protetti da TLS.

## <a name="bind-event-hubs-to-virtual-networks"></a>Associare Hub eventi a reti virtuali

Le *regole della rete virtuale* rappresentano una funzionalità di sicurezza del firewall che consente di definire se il server di Hub eventi di Azure accetta le connessioni da una subnet specifica della rete virtuale.

L'associazione di uno spazio dei nomi di Hub eventi a una rete virtuale è un processo in due passaggi. È prima necessario creare un **endpoint del servizio di rete virtuale** su una subnet della rete virtuale e abilitarlo per "Microsoft.EventHub" come descritto nella [panoramica sull'endpoint del servizio][vnet-sep]. Dopo aver aggiunto l'endpoint del servizio, è necessario associare lo spazio dei nomi di Hub eventi all'endpoint con una *regola della rete virtuale*.

La regola della rete virtuale è un'associazione denominata tra lo spazio dei nomi di Hub eventi e una subnet della rete virtuale. Fino a quando esiste la regola, a tutti i carichi di lavoro associati alla subnet viene concesso l'accesso allo spazio dei nomi di Hub eventi. Hub eventi non stabilisce mai direttamente connessioni in uscita e non deve ottenere l'accesso, quindi non ottiene mai l'accesso alla subnet abilitando questa regola.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Creare una regola di rete virtuale con i modelli di Azure Resource Manager

Il modello di Resource Manager seguente consente di aggiungere una regola di rete virtuale a uno spazio dei nomi esistente di Hub eventi.

Parametri del modello:

* **namespaceName**: spazio dei nomi di Hub eventi.
* **vnetRuleName**: nome per la regola della rete virtuale da creare.
* **virtualNetworkingSubnetId**: percorso completo di Resource Manager per la subnet della rete virtuale. Ad esempio, `subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` per la subnet predefinita di una rete virtuale.

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
          "vnetRuleName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the Authorization rule"
             }
          },
          "virtualNetworkSubnetId":{  
             "type":"string",
             "metadata":{  
                "description":"subnet Azure Resource Manager ID"
             }
          }
      },
    "resources": [
        {
            "apiVersion": "2018-01-01-preview",
            "name": "[concat(parameters('namespaceName'), '/', parameters('vnetRuleName'))]",
            "type":"Microsoft.EventHub/namespaces/VirtualNetworkRules",         
            "properties": {             
                "virtualNetworkSubnetId": "[parameters('virtualNetworkSubnetId')]"  
            }
        } 
    ]
}
```

Per distribuire il modello, seguire le istruzioni per [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle reti virtuali, vedere i collegamenti seguenti:

- [Endpoint del servizio Rete virtuale di Azure][vnet-sep]
- [Azure Event Hubs IP filtering][ip-filtering] (Filtri IP per Hub eventi di Azure)

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md