---
title: Endpoint servizio di rete virtuale - Hub eventi di Azure | Microsoft Docs
description: In questo articolo vengono fornite informazioni su come aggiungere un endpoint del servizio Microsoft.EventHub a una rete virtuale.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 11/26/2019
ms.author: shvija
ms.openlocfilehash: abd7940551f7a8182364475b0cf50b60afb5e1b7
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313801"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Usare gli endpoint servizio di rete virtuale con Hub eventi di Azure

L'integrazione di Hub eventi con gli [endpoint del servizio della rete virtuale][vnet-sep] consente di proteggere l'accesso alle funzionalità di messaggistica da carichi di lavoro come macchine virtuali associate a reti virtuali, con il percorso del traffico di rete protetto a entrambe le estremità.

Una volta configurato per essere associato ad almeno un endpoint del servizio subnet di rete virtuale, il rispettivo spazio dei nomi Hub eventi non accetta più traffico da qualsiasi luogo, ma subnet autorizzate nelle reti virtuali. Dal punto di vista della rete virtuale, l'associazione di uno spazio dei nomi di Hub eventi a un endpoint del servizio consente di configurare un tunnel di rete isolato dalla subnet della rete virtuale al servizio di messaggistica. 

Il risultato è una relazione privata e isolata tra i carichi di lavoro associati alla subnet e lo spazio dei nomi di Hub eventi corrispondente, nonostante l'indirizzo di rete osservabile dell'endpoint del servizio di messaggistica sia in un intervallo di IP pubblici. Esiste un'eccezione a questo comportamento. L'abilitazione di un endpoint `denyall` del servizio, per impostazione predefinita, abilita la regola nel [firewall IP](event-hubs-ip-filtering.md) associato alla rete virtuale. È possibile aggiungere indirizzi IP specifici nel firewall IP per abilitare l'accesso all'endpoint pubblico dell'hub eventi. 

>[!WARNING]
> L'implementazione dell'integrazione delle reti virtuali può impedire l'interazione da parte di altri servizi Azure con l'Hub eventi di Azure.
>
> I servizi Microsoft considerati attendibili non sono supportati quando sono implementate reti virtuali.
>
> Scenari comuni di Azure che non supportano le reti virtuali (l'elenco **NON** è esaustivo) -
> - Analisi di flusso di Azure
> - Integrazione con Griglia di eventi di Azure
> - Route dell'hub IoT di Azure
> - Azure IoT Device Explorer
>
> I seguenti servizi Microsoft devono essere in una rete virtuale
> - App Web di Azure
> - Funzioni di Azure


> [!IMPORTANT]
> Le reti virtuali sono supportate nei livelli **standard** e **dedicato** di Hub eventi. Non è supportato nel livello **di base.**

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Scenari di sicurezza avanzati resi possibili dall'integrazione della rete virtuale 

Le soluzioni che richiedono una sicurezza stretta e compartimentata e in cui le subnet di rete virtuale forniscono la segmentazione tra i servizi compartimentati, necessitano comunque di percorsi di comunicazione tra i servizi che risiedano in tali compartimenti.

Qualsiasi route IP immediata tra i compartimenti, incluse quelle destinate al traffico HTTPS su TCP/IP, comportano il rischio di sfruttamento delle vulnerabilità dal livello rete verso l'alto. I servizi di messaggistica forniscono percorsi di comunicazione isolati, in cui i messaggi vengono anche scritti su disco durante la transizione tra le parti. I carichi di lavoro in due reti virtuali distinte associate alla stessa istanza di Hub eventi possono comunicare in modo efficiente e affidabile tramite messaggi, pur mantenendo l'integrità del confine di isolamento rete.
 
Questo significa che le soluzioni cloud con requisiti di sicurezza elevati non solo possono ottenere l'accesso alle funzionalità di messaggistica asincrona scalabili e affidabili leader del settore di Azure, ma possono ora usare la messaggistica per creare percorsi di comunicazione tra compartimenti sicuri della soluzione, intrinsecamente più sicuri di quanto sia possibile con qualsiasi modalità di comunicazione peer-to-peer, inclusi i protocolli HTTPS e altri protocolli socket protetti da TLS.

## <a name="bind-event-hubs-to-virtual-networks"></a>Associare hub eventi a reti virtualiBind event hubs to virtual networks

Le **regole di rete virtuale** rappresentano la funzionalità di sicurezza firewall che controlla se lo spazio dei nomi di Hub eventi di Azure accetta le connessioni da una specifica subnet della rete virtuale.

L'associazione di uno spazio dei nomi di Hub eventi a una rete virtuale è un processo in due passaggi. È innanzitutto necessario creare un endpoint del **servizio di rete virtuale** nella subnet di una rete virtuale e abilitarlo per **Microsoft.EventHub** come illustrato nell'articolo [Panoramica dell'endpoint del servizio.][vnet-sep] Dopo aver aggiunto l'endpoint del servizio, è necessario associare lo spazio dei nomi di Hub eventi all'endpoint con una **regola di rete virtuale**.

La regola di rete virtuale è un'associazione dello spazio dei nomi di Hub eventi e una subnet della rete virtuale. Fino a quando esiste la regola, a tutti i carichi di lavoro associati alla subnet viene concesso l'accesso allo spazio dei nomi di Hub eventi. Gli hub eventi non stabiliscono mai connessioni in uscita, non devono ottenere l'accesso e pertanto non viene mai concesso l'accesso alla subnet abilitando questa regola.

## <a name="use-azure-portal"></a>Usare il portale di Azure
Questa sezione illustra come usare il portale di Azure per aggiungere un endpoint del servizio di rete virtuale. Per limitare l'accesso, è necessario integrare l'endpoint del servizio di rete virtuale per questo spazio dei nomi Hub eventi.

1. Passare allo **spazio dei nomi Hub eventi** nel portale di [Azure.](https://portal.azure.com)
2. Nel menu a sinistra selezionare l'opzione **Rete.On** the left menu, select Networking option. Se si seleziona l'opzione **Tutte le reti,** l'hub eventi accetta connessioni da qualsiasi indirizzo IP. Questa impostazione equivale a una regola che accetta l'intervallo di indirizzi IP 0.0.0.0/0.This setting is equivalent to a rule that accepts the 0.0.0.0/0 IP address range. 

    ![Firewall - Opzione Tutte le reti selezionata](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Per l'accesso restrct a reti specifiche, selezionare l'opzione **Reti selezionate** nella parte superiore della pagina.
2. Nella sezione **Rete virtuale** della pagina, selezionare . Se si desidera creare una nuova rete virtuale, **selezionare Crea nuova rete virtuale.** 

    ![aggiungi rete virtuale esistente](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)
3. Selezionare la rete virtuale dall'elenco delle reti virtuali e quindi selezionare la **subnet**. È necessario abilitare l'endpoint del servizio prima di aggiungere la rete virtuale all'elenco. Se l'endpoint del servizio non è abilitato, il portale richiederà di abilitarlo.
   
   ![selezionare la subnet](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. Dopo che l'endpoint del servizio per la subnet è abilitato per **Microsoft.EventHub,** verrà visualizzato il seguente messaggio di operazione riuscita. Selezionare **Aggiungi** nella parte inferiore della pagina per aggiungere la rete. 

    ![seleziona subnet e abilita endpoint](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Se non è possibile abilitare l'endpoint del servizio, è possibile ignorare l'endpoint del servizio di rete virtuale mancante usando il modello Resource Manager.If you are unable to enable the service endpoint, you may ignore the missing virtual network service endpoint using the Resource Manager template. Questa funzionalità non è disponibile sul portale.
6. Selezionare **Salva** sulla barra degli strumenti per salvare le impostazioni. Attendere alcuni minuti che la conferma venga visualizzata nelle notifiche del portale.

    ![Salva rete](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)


## <a name="use-resource-manager-template"></a>Usare i modelli di Resource Manager

Il modello di Resource Manager seguente consente di aggiungere una regola di rete virtuale a uno spazio dei nomi esistente di Hub eventi.

Parametri del modello:

* **namespaceName**: spazio dei nomi di Hub eventi.
* **vnetRuleName**: nome per la regola di rete virtuale da creare.
* **virtualNetworkingSubnetId**: percorso completo di Resource Manager per la subnet della rete virtuale. Ad esempio, `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` per la subnet predefinita di una rete virtuale.

> [!NOTE]
> Sebbene non siano possibili regole di rifiuto, il modello di Azure Resource Manager ha l'azione predefinita impostata su **"Consenti"**, che non limita le connessioni.
> Quando si creano regole di rete virtuale o del firewall, occorre modificare ***"defaultAction"***
> 
> da
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
          "trustedServiceAccessEnabled": false,
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

- [Endpoint servizio di rete virtuale di Azure][vnet-sep]
- [Azure Event Hubs IP filtering][ip-filtering] (Filtri IP per Hub eventi di Azure)

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
