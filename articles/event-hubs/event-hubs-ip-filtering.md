---
title: Regole del firewall per Hub eventi di Azure | Microsoft Docs
description: Usare le regole del firewall per consentire le connessioni da indirizzi IP specifici ad Hub eventi di Azure.
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: 18212726f0ab921a05a3b640a32754c62958d047
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393137"
---
# <a name="configure-ip-firewall-rules-for-an-azure-event-hubs-namespace"></a>Configurare le regole del firewall IP per uno spazio dei nomi di hub eventi di Azure
Per impostazione predefinita, gli spazi dei nomi di hub eventi sono accessibili da Internet, purché la richiesta venga fornita con autenticazione e autorizzazione valide. Con il firewall IP, è possibile limitarlo ulteriormente a un set di indirizzi IPv4 o a intervalli di indirizzi IPv4 in notazione [CIDR (instradamento tra domini senza classi)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Questa funzionalità è utile negli scenari in cui gli hub eventi di Azure devono essere accessibili solo da determinati siti noti. Le regole del firewall consentono di configurare regole per accettare il traffico originato da indirizzi IPv4 specifici. Ad esempio, se si usa hub eventi con [Azure Express Route][express-route], è possibile creare una **regola del firewall** per consentire il traffico solo da indirizzi IP dell'infrastruttura locale. 

>[!WARNING]
> L'abilitazione del filtro IP può impedire ad altri servizi di Azure di interagire con hub eventi.
>
> I servizi Microsoft considerati attendibili non sono supportati quando sono implementate reti virtuali.
>
> Scenari comuni di Azure che non supportano le reti virtuali (l'elenco **NON** è esaustivo) -
> - Monitoraggio di Azure (impostazione di diagnostica)
> - Analisi di flusso di Azure
> - Integrazione con Griglia di eventi di Azure
> - Route dell'hub IoT di Azure
> - Azure IoT Device Explorer
>
> I servizi Microsoft seguenti devono trovarsi in una rete virtuale
> - App Web di Azure
> - Funzioni di Azure


## <a name="ip-firewall-rules"></a>Regole del firewall IP
Le regole del firewall IP vengono applicate a livello di spazio dei nomi di hub eventi. Vengono pertanto applicate a tutte le connessioni provenienti dai client con qualsiasi protocollo supportato. Qualsiasi tentativo di connessione proveniente da un indirizzo IP che non corrisponde a una regola di indirizzi IP consentiti nello spazio dei nomi di Hub eventi viene rifiutato come non autorizzato. Nella risposta non viene fatto riferimento alla regola IP. Le regole del filtro IP vengono applicate in ordine e la prima regola corrispondente all'indirizzo IP determina l'azione di accettazione o rifiuto.

## <a name="use-azure-portal"></a>Usare il portale di Azure
Questa sezione illustra come usare la portale di Azure per creare regole del firewall IP per uno spazio dei nomi di hub eventi. 

1. Passare allo **spazio dei nomi di hub eventi** nel [portale di Azure](https://portal.azure.com).
2. Nel menu a sinistra selezionare opzione di **rete** . Se si seleziona l'opzione **tutte le reti** , l'hub eventi accetta le connessioni da qualsiasi indirizzo IP. Questa impostazione equivale a una regola che accetta l'intervallo di indirizzi IP 0.0.0.0/0. 

    ![Opzione Firewall-tutte le reti selezionata](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Per limitare l'accesso a reti e indirizzi IP specifici, selezionare l'opzione **reti selezionate** . Nella sezione **Firewall** , attenersi alla seguente procedura:
    1. Selezionare l'opzione **Aggiungi indirizzo IP client** per concedere all'IP del client corrente l'accesso allo spazio dei nomi. 
    2. Per **intervallo di indirizzi**, immettere un indirizzo IPv4 specifico o un intervallo di indirizzi IPv4 nella notazione CIDR. 
    3. Specificare se si desidera **consentire ai servizi Microsoft attendibili di ignorare il firewall**. 

        ![Opzione Firewall-tutte le reti selezionata](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. Selezionare **Salva** sulla barra degli strumenti per salvare le impostazioni. Attendere alcuni minuti prima che la conferma venga visualizzata nelle notifiche del portale.


## <a name="use-resource-manager-template"></a>Usare i modelli di Resource Manager

> [!IMPORTANT]
> Le regole del firewall sono supportate nei livelli **standard** e **dedicato** di Hub eventi. Non sono supportate nel livello Basic.

Il modello di Resource Manager seguente consente di aggiungere una regola di filtro IP a uno spazio dei nomi esistente di Hub eventi.

Parametri del modello:

- **ipMask** è un singolo indirizzo IPv4 o un blocco di indirizzi IP in notazione CIDR. Ad esempio, nella notazione CIDR, 70.37.104.0/24 rappresenta i 256 indirizzi IPv4, da 70.37.104.0 a 70.37.104.255, con 24 che indica il numero di bit di prefisso significativi per l'intervallo.

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

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
