---
title: Regole del firewall per Hub eventi di Azure | Microsoft Docs
description: Usare le regole del firewall per consentire le connessioni da indirizzi IP specifici ad Hub eventi di Azure.
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 7870260b77785af59f4f186274775067f2292ef6
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88066050"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-ip-addresses-or-ranges"></a>Consentire l'accesso agli spazi dei nomi di hub eventi di Azure da intervalli o indirizzi IP specifici
Per impostazione predefinita, gli spazi dei nomi di Hub eventi sono accessibili da Internet, purché la richiesta sia accompagnata da un'autenticazione e da un'autorizzazione valide. Con un firewall per gli indirizzi IP, è possibile limitare ulteriormente l'accesso a un set di indirizzi IPv4 o a intervalli di indirizzi IPv4 in notazione [CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Questa funzionalità è utile negli scenari in cui Hub eventi di Azure deve essere accessibile solo da siti noti specifici. Le regole del firewall consentono di configurare regole di ammissione del traffico proveniente da indirizzi IPv4 specifici. Se ad esempio si usa Hub eventi con [Azure ExpressRoute][express-route], è possibile creare una **regola del firewall** per consentire traffico solo dagli indirizzi IP dell'infrastruttura locale. 

>[!WARNING]
> L'abilitazione di filtri IP può impedire ad altri servizi di Azure di interagire con Hub eventi.
>
> I servizi Microsoft attendibili non sono supportati quando vengono implementati i filtri IP.
>
> Scenari comuni di Azure che non funzionano con il filtro IP (si noti che l'elenco **non** è esaustivo)-
> - Analisi di flusso di Azure
> - Route dell'hub IoT di Azure
> - Azure IoT Device Explorer
>
> I servizi Microsoft seguenti devono essere in una rete virtuale
> - App Web di Azure
> - Funzioni di Azure
> - Monitoraggio di Azure (impostazioni di diagnostica)


## <a name="ip-firewall-rules"></a>Regole del firewall IP
Le regole del firewall IP vengono applicate a livello dello spazio dei nomi di Hub eventi. Vengono pertanto applicate a tutte le connessioni provenienti dai client con qualsiasi protocollo supportato. Qualsiasi tentativo di connessione proveniente da un indirizzo IP che non corrisponde a una regola di indirizzi IP consentiti nello spazio dei nomi di Hub eventi viene rifiutato come non autorizzato. Nella risposta non viene fatto riferimento alla regola IP. Le regole del filtro IP vengono applicate in ordine e la prima regola corrispondente all'indirizzo IP determina l'azione di accettazione o rifiuto.

## <a name="use-azure-portal"></a>Usare il portale di Azure
Questa sezione illustra come usare il portale di Azure per creare regole del firewall per uno spazio dei nomi di Hub eventi. 

1. Passare allo **spazio dei nomi di Hub eventi** nel [portale di Azure](https://portal.azure.com).
4. Selezionare **rete** in **Impostazioni** nel menu a sinistra. 

    > [!NOTE]
    > Viene visualizzata la scheda **rete** solo per gli spazi dei nomi **standard** o **dedicati** . 

    Per impostazione predefinita, è selezionata l'opzione **reti selezionate** . Se non si specifica una regola del firewall IP o si aggiunge una rete virtuale in questa pagina, è possibile accedere allo spazio dei nomi tramite Internet pubblico (usando la chiave di accesso). 

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="Scheda reti-opzione reti selezionate" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    Se si seleziona l'opzione **tutte le reti** , l'hub eventi accetta le connessioni da qualsiasi indirizzo IP (usando la chiave di accesso). Questa impostazione equivale a una regola che accetti l'intervallo di indirizzi IP 0.0.0.0/0. 

    ![Opzione Firewall - Tutte le reti selezionata](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Per limitare l'accesso a indirizzi IP specifici, verificare che sia selezionata l'opzione **reti selezionate** . Nella sezione **Firewall** seguire questa procedura:
    1. Selezionare l'opzione **Aggiungere l'indirizzo IP client** per concedere all'IP del client corrente l'accesso allo spazio dei nomi. 
    2. Per **Intervallo di indirizzi** immettere un indirizzo IPv4 specifico o un intervallo di indirizzi IPv4 in notazione CIDR. 
    3. Specificare se si vuole **consentire ai servizi Microsoft attendibili di ignorare il firewall**. 

        ![Opzione Firewall - Tutte le reti selezionata](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. Selezionare **Salva** sulla barra degli strumenti per salvare le impostazioni. Attendere qualche minuto la visualizzazione della conferma tra le notifiche del portale.

    > [!NOTE]
    > Per limitare l'accesso a reti virtuali specifiche, vedere [consentire l'accesso da reti specifiche](event-hubs-service-endpoints.md).

## <a name="use-resource-manager-template"></a>Usare i modelli di Resource Manager

> [!IMPORTANT]
> Le regole del firewall sono supportate nei livelli **standard** e **dedicato** di Hub eventi. Non sono supportate nel livello Basic.

Il modello di Resource Manager seguente consente di aggiungere una regola di filtro IP a uno spazio dei nomi esistente di Hub eventi.

Parametri del modello:

- **ipMask** è un singolo indirizzo IPv4 o un blocco di indirizzi IP in notazione CIDR. Ad esempio, nella notazione CIDR, 70.37.104.0/24 rappresenta i 256 indirizzi IPv4, da 70.37.104.0 a 70.37.104.255, con 24 che indica il numero di bit di prefisso significativi per l'intervallo.

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
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
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
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
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

Per limitare l'accesso ad Hub eventi dalle reti virtuali di Azure, vedere il collegamento seguente:

- [Usare gli endpoint servizio di rete virtuale con Hub eventi di Azure][lnk-vnet]

<!-- Links -->

[express-route]:  ../expressroute/expressroute-faqs.md#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
