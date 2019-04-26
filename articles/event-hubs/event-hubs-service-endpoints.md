---
title: Endpoint servizio di rete virtuale - Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni su come aggiungere un endpoint del servizio eventhub a una rete virtuale.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 03/12/2019
ms.author: shvija
ms.openlocfilehash: 7b5a62f81238d1ae2b627c395613066350b36efe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60343444"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Usare gli endpoint servizio di rete virtuale con Hub eventi di Azure

L'integrazione di Hub eventi con gli [endpoint del servizio della rete virtuale][vnet-sep] consente di proteggere l'accesso alle funzionalità di messaggistica da carichi di lavoro come macchine virtuali associate a reti virtuali, con il percorso del traffico di rete protetto a entrambe le estremità.

Dopo aver configurato associare almeno un endpoint di servizio di subnet di rete virtuale, i rispettivi nomi di hub eventi non è più accetta il traffico da qualsiasi posizione ma autorizzato subnet nelle reti virtuali. Dal punto di vista della rete virtuale, l'associazione di uno spazio dei nomi di Hub eventi a un endpoint del servizio consente di configurare un tunnel di rete isolato dalla subnet della rete virtuale al servizio di messaggistica. 

Il risultato è una relazione privata e isolata tra i carichi di lavoro associati alla subnet e lo spazio dei nomi di Hub eventi corrispondente, nonostante l'indirizzo di rete osservabile dell'endpoint del servizio di messaggistica sia in un intervallo di IP pubblici. Si verifica un'eccezione a questo comportamento. L'abilitazione di un endpoint del servizio, per impostazione predefinita, abilita la regola denyall del firewall IP associate alla rete virtuale. È possibile aggiungere indirizzi IP specifici nel firewall IP per abilitare l'accesso all'endpoint pubblico dell'Hub eventi. 


>[!WARNING]
> L'implementazione dell'integrazione delle reti virtuali può impedire l'interazione da parte di altri servizi Azure con l'Hub eventi di Azure.
>
> I servizi Microsoft considerati attendibili non sono supportati quando sono implementate reti virtuali.
>
> Scenari comuni di Azure che non supportano le reti virtuali (l'elenco **NON** è esaustivo) -
> - Monitoraggio di Azure
> - Analisi di flusso di Azure
> - Integrazione con Griglia di eventi di Azure
> - Route dell'hub IoT di Azure
> - Azure IoT Device Explorer
> - Esplora dati di Azure
>
> I servizi Microsoft seguenti devono essere in una rete virtuale
> - App Web di Azure 
> - Funzioni di Azure

> [!IMPORTANT]
> Le reti virtuali sono supportate nei livelli **standard** e **dedicato** di Hub eventi. Non sono supportate nel livello Basic.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Scenari di sicurezza avanzati resi possibili dall'integrazione della rete virtuale 

Soluzioni che richiedono una stretta e set specifico di sicurezza e in subnet della rete virtuale forniscono la segmentazione tra i servizi contenente sezioni, è comunque necessario percorsi di comunicazione tra servizi che risiedono in tali raggruppamenti.

Qualsiasi route IP immediata tra i compartimenti, incluse quelle destinate al traffico HTTPS su TCP/IP, comportano il rischio di sfruttamento delle vulnerabilità dal livello rete verso l'alto. I servizi di messaggistica offrono percorsi di comunicazione completamente isolati, in cui i messaggi vengono anche scritti su disco durante la transizione tra le parti coinvolte. I carichi di lavoro in due reti virtuali distinte associate alla stessa istanza di Hub eventi possono comunicare in modo efficiente e affidabile tramite messaggi, pur mantenendo l'integrità del confine di isolamento rete.
 
Questo significa che le soluzioni cloud con requisiti di sicurezza elevati non solo possono ottenere l'accesso alle funzionalità di messaggistica asincrona scalabili e affidabili leader del settore di Azure, ma possono ora usare la messaggistica per creare percorsi di comunicazione tra compartimenti sicuri della soluzione, intrinsecamente più sicuri di quanto sia possibile con qualsiasi modalità di comunicazione peer-to-peer, inclusi i protocolli HTTPS e altri protocolli socket protetti da TLS.

## <a name="bind-event-hubs-to-virtual-networks"></a>Associare Hub eventi a reti virtuali

Le *regole delle reti virtuali* rappresentano la funzionalità di sicurezza firewall che controlla se lo spazio dei nomi di Hub eventi di Azure accetta le connessioni da una specifica subnet della rete virtuale.

L'associazione di uno spazio dei nomi di Hub eventi a una rete virtuale è un processo in due passaggi. È prima necessario creare un **endpoint servizio di rete virtuale** su una subnet della rete virtuale e abilitarlo per "Microsoft.EventHub" come descritto nella [panoramica sull'endpoint del servizio][vnet-sep]. Dopo aver aggiunto l'endpoint del servizio, è necessario associare lo spazio dei nomi di Hub eventi all'endpoint con una *regola di rete virtuale*.

La regola di rete virtuale è un'associazione dello spazio dei nomi di Hub eventi e una subnet della rete virtuale. Fino a quando esiste la regola, a tutti i carichi di lavoro associati alla subnet viene concesso l'accesso allo spazio dei nomi di Hub eventi. Hub eventi non stabilisce mai direttamente connessioni in uscita e non deve ottenere l'accesso, quindi non ottiene mai l'accesso alla subnet abilitando questa regola.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Creare una regola di rete virtuale con i modelli di Azure Resource Manager

Il modello di Resource Manager seguente consente di aggiungere una regola di rete virtuale a uno spazio dei nomi esistente di Hub eventi.

Parametri del modello:

* **namespaceName**: Spazio dei nomi dell'Hub eventi di Azure.
* **vnetRuleName**: nome per la regola di rete virtuale da creare.
* **virtualNetworkingSubnetId**: percorso completo di Resource Manager per la subnet della rete virtuale. Ad esempio, `subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` per la subnet predefinita di una rete virtuale.

> [!NOTE]
> Sebbene non siano possibili regole di rifiuto, il modello di Azure Resource Manager ha l'azione predefinita impostata su **"Consenti"**, che non limita le connessioni.
> Quando si creano regole di rete virtuale o del firewall, occorre modificare ***"defaultAction"***
> 
> from
> ```json
> "defaultAction": "Allow"
> ```
> to
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.EventHub"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
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
