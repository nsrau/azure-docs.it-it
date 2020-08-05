---
title: Endpoint servizio di rete virtuale - Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni su come aggiungere un endpoint di servizio Microsoft. EventHub a una rete virtuale.
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 8c798efc21f5b846965f2247d7e76249177ef946
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87554074"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-virtual-networks"></a>Consentire l'accesso agli spazi dei nomi di hub eventi di Azure da reti virtuali specifiche 

L'integrazione di Hub eventi con gli [endpoint del servizio della rete virtuale][vnet-sep] consente di proteggere l'accesso alle funzionalità di messaggistica da carichi di lavoro come macchine virtuali associate a reti virtuali, con il percorso del traffico di rete protetto a entrambe le estremità.

Una volta configurata per l'associazione ad almeno un endpoint del servizio subnet della rete virtuale, il rispettivo spazio dei nomi di hub eventi non accetta più il traffico da tutte le subnet autorizzate nelle reti virtuali. Dal punto di vista della rete virtuale, l'associazione di uno spazio dei nomi di Hub eventi a un endpoint del servizio consente di configurare un tunnel di rete isolato dalla subnet della rete virtuale al servizio di messaggistica. 

Il risultato è una relazione privata e isolata tra i carichi di lavoro associati alla subnet e lo spazio dei nomi di Hub eventi corrispondente, nonostante l'indirizzo di rete osservabile dell'endpoint del servizio di messaggistica sia in un intervallo di IP pubblici. Si è verificata un'eccezione a questo comportamento. Per impostazione predefinita, l'abilitazione di un endpoint del servizio Abilita la `denyall` regola nel [firewall IP](event-hubs-ip-filtering.md) associato alla rete virtuale. È possibile aggiungere indirizzi IP specifici nel firewall IP per abilitare l'accesso all'endpoint pubblico dell'hub eventi. 

>[!WARNING]
> L'implementazione dell'integrazione delle reti virtuali può impedire l'interazione da parte di altri servizi Azure con l'Hub eventi di Azure.
>
> I servizi Microsoft considerati attendibili non sono supportati quando sono implementate reti virtuali.
>
> Scenari comuni di Azure che non supportano le reti virtuali (l'elenco **NON** è esaustivo) -
> - Analisi di flusso di Azure
> - Route dell'hub IoT di Azure
> - Azure IoT Device Explorer
>
> I servizi Microsoft seguenti devono essere in una rete virtuale
> - App Web di Azure
> - Funzioni di Azure
> - Monitoraggio di Azure (impostazioni di diagnostica)


> [!IMPORTANT]
> Le reti virtuali sono supportate nei livelli **standard** e **dedicato** di Hub eventi. Il livello **Basic** non è supportato.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Scenari di sicurezza avanzati resi possibili dall'integrazione della rete virtuale 

Le soluzioni che richiedono una sicurezza rigida e compartimentata e dove le subnet della rete virtuale forniscono la segmentazione tra i servizi di compartimentazione, richiedono comunque percorsi di comunicazione tra i servizi che risiedono in questi raggruppamenti.

Qualsiasi route IP immediata tra i compartimenti, incluse quelle destinate al traffico HTTPS su TCP/IP, comportano il rischio di sfruttamento delle vulnerabilità dal livello rete verso l'alto. I servizi di messaggistica forniscono percorsi di comunicazione isolati, in cui i messaggi vengono scritti su disco durante la transizione tra le parti. I carichi di lavoro in due reti virtuali distinte associate alla stessa istanza di Hub eventi possono comunicare in modo efficiente e affidabile tramite messaggi, pur mantenendo l'integrità del confine di isolamento rete.
 
Questo significa che le soluzioni cloud con requisiti di sicurezza elevati non solo possono ottenere l'accesso alle funzionalità di messaggistica asincrona scalabili e affidabili leader del settore di Azure, ma possono ora usare la messaggistica per creare percorsi di comunicazione tra compartimenti sicuri della soluzione, intrinsecamente più sicuri di quanto sia possibile con qualsiasi modalità di comunicazione peer-to-peer, inclusi i protocolli HTTPS e altri protocolli socket protetti da TLS.

## <a name="bind-event-hubs-to-virtual-networks"></a>Associare Hub eventi a reti virtuali

Le **regole di rete virtuale** rappresentano la funzionalità di sicurezza firewall che controlla se lo spazio dei nomi di Hub eventi di Azure accetta le connessioni da una specifica subnet della rete virtuale.

L'associazione di uno spazio dei nomi di Hub eventi a una rete virtuale è un processo in due passaggi. Per prima cosa è necessario creare un **endpoint di servizio di rete virtuale** nella subnet di una rete virtuale e abilitarlo per **Microsoft. EventHub** come illustrato nell'articolo [Panoramica dell'endpoint di servizio][vnet-sep] . Dopo aver aggiunto l'endpoint del servizio, è necessario associare lo spazio dei nomi di Hub eventi all'endpoint con una **regola di rete virtuale**.

La regola di rete virtuale è un'associazione dello spazio dei nomi di Hub eventi e una subnet della rete virtuale. Fino a quando esiste la regola, a tutti i carichi di lavoro associati alla subnet viene concesso l'accesso allo spazio dei nomi di Hub eventi. Hub eventi stesso non stabilisce mai le connessioni in uscita, non è necessario ottenere l'accesso e pertanto non viene mai concesso l'accesso alla subnet abilitando questa regola.

## <a name="use-azure-portal"></a>Usare il portale di Azure
Questa sezione illustra come usare portale di Azure per aggiungere un endpoint del servizio rete virtuale. Per limitare l'accesso, è necessario integrare l'endpoint del servizio rete virtuale per questo spazio dei nomi di hub eventi.

1. Passare allo **spazio dei nomi di Hub eventi** nel [portale di Azure](https://portal.azure.com).
4. Selezionare **rete** in **Impostazioni** nel menu a sinistra. 

    > [!NOTE]
    > Viene visualizzata la scheda **rete** solo per gli spazi dei nomi **standard** o **dedicati** . 

    Per impostazione predefinita, è selezionata l'opzione **reti selezionate** . Se non si specifica una regola del firewall IP o si aggiunge una rete virtuale in questa pagina, è possibile accedere allo spazio dei nomi da tutte le reti, inclusa la rete Internet pubblica (usando la chiave di accesso). 

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="Scheda reti-opzione reti selezionate" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    Se si seleziona l'opzione **tutte le reti** , l'hub eventi accetta le connessioni da qualsiasi indirizzo IP (usando la chiave di accesso). Questa impostazione equivale a una regola che accetti l'intervallo di indirizzi IP 0.0.0.0/0. 

    ![Opzione Firewall - Tutte le reti selezionata](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Per limitare l'accesso a reti specifiche, selezionare l'opzione **reti selezionate** nella parte superiore della pagina, se non è già selezionata.
2. Nella sezione **rete virtuale** della pagina selezionare * * + Aggiungi rete virtuale esistente * * *. Selezionare **+ Crea nuova rete virtuale** se si vuole creare una nuova VNet. 

    ![aggiungi rete virtuale esistente](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)
3. Selezionare la rete virtuale dall'elenco di reti virtuali e quindi selezionare la **subnet**. Prima di aggiungere la rete virtuale all'elenco, è necessario abilitare l'endpoint del servizio. Se l'endpoint del servizio non è abilitato, il portale richiederà di abilitarlo.
   
   ![Selezionare una subnet](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. Dopo che l'endpoint del servizio per la subnet è stato abilitato per **Microsoft. EventHub**, dovrebbe essere visualizzato il messaggio seguente. Selezionare **Aggiungi** nella parte inferiore della pagina per aggiungere la rete. 

    ![seleziona subnet e abilita endpoint](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Se non è possibile abilitare l'endpoint del servizio, è possibile ignorare l'endpoint del servizio di rete virtuale mancante usando il modello di Gestione risorse. Questa funzionalità non è disponibile sul portale.
6. Selezionare **Salva** sulla barra degli strumenti per salvare le impostazioni. Attendere qualche minuto la visualizzazione della conferma tra le notifiche del portale.

    ![Salva rete](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)

    > [!NOTE]
    > Per limitare l'accesso a intervalli o indirizzi IP specifici, vedere [consentire l'accesso da indirizzi o intervalli IP specifici](event-hubs-ip-filtering.md).

## <a name="use-resource-manager-template"></a>Usare i modelli di Resource Manager

Il modello di Resource Manager seguente consente di aggiungere una regola di rete virtuale a uno spazio dei nomi esistente di Hub eventi.

Parametri del modello:

* `namespaceName`: Spazio dei nomi di hub eventi.
* `vnetRuleName`: Nome della regola della rete virtuale da creare.
* `virtualNetworkingSubnetId`: Percorso Gestione risorse completo della subnet della rete virtuale. ad esempio, `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` per la subnet predefinita di una rete virtuale.

> [!NOTE]
> Sebbene non siano possibili regole di rifiuto, il modello di Azure Resource Manager ha l'azione predefinita impostata su **"Consenti"** , che non limita le connessioni.
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
