---
title: Regole del firewall per Hub eventi di Azure | Microsoft Docs
description: Usare le regole del firewall per consentire le connessioni da indirizzi IP specifici ad Hub eventi di Azure.
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: e07f863bf8b7d5f64ec0ba04bf16fba12f4a785d
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427446"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-ip-addresses-or-ranges"></a>Consentire l'accesso agli spazi dei nomi di hub eventi di Azure da intervalli o indirizzi IP specifici
Per impostazione predefinita, gli spazi dei nomi di Hub eventi sono accessibili da Internet, purché la richiesta sia accompagnata da un'autenticazione e da un'autorizzazione valide. Con un firewall per gli indirizzi IP, è possibile limitare ulteriormente l'accesso a un set di indirizzi IPv4 o a intervalli di indirizzi IPv4 in notazione [CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Questa funzionalità è utile negli scenari in cui Hub eventi di Azure deve essere accessibile solo da siti noti specifici. Le regole del firewall consentono di configurare regole di ammissione del traffico proveniente da indirizzi IPv4 specifici. Se ad esempio si usa Hub eventi con [Azure ExpressRoute][express-route], è possibile creare una **regola del firewall** per consentire traffico solo dagli indirizzi IP dell'infrastruttura locale. 

>[!WARNING]
> L'attivazione delle regole firewall per lo spazio dei nomi di hub eventi blocca le richieste in ingresso per impostazione predefinita, a meno che le richieste provengano da un servizio che opera da indirizzi IP pubblici consentiti. Le richieste che vengono bloccate sono quelle che provengono da altri servizi di Azure, dal portale di Azure, dai servizi di registrazione e metriche e così via. Come eccezione, è possibile consentire l'accesso alle risorse di hub eventi da determinati servizi attendibili anche quando è abilitato il filtro IP. Per un elenco di servizi attendibili, vedere [Servizi Microsoft attendibili](#trusted-microsoft-services).

## <a name="ip-firewall-rules"></a>Regole del firewall IP
Le regole del firewall IP vengono applicate a livello dello spazio dei nomi di Hub eventi. Quindi, le regole si applicano a tutte le connessioni dei client che usano qualsiasi protocollo supportato. Qualsiasi tentativo di connessione da un indirizzo IP che non corrisponde a una regola IP consentita nello spazio dei nomi di hub eventi viene rifiutato come non autorizzato. La risposta non menziona la regola IP. Le regole del filtro IP vengono applicate in ordine e la prima regola corrispondente all'indirizzo IP determina l'azione di accettazione o rifiuto.

## <a name="use-azure-portal"></a>Usare il portale di Azure
Questa sezione illustra come usare il portale di Azure per creare regole del firewall per uno spazio dei nomi di Hub eventi. 

1. Passare allo **spazio dei nomi di Hub eventi** nel [portale di Azure](https://portal.azure.com).
4. Selezionare **rete** in **Impostazioni** nel menu a sinistra. Viene visualizzata la scheda **rete** solo per gli spazi dei nomi **standard** o **dedicati** . 
    > [!NOTE]
    > Per impostazione predefinita, è selezionata l'opzione **reti selezionate** , come illustrato nella figura seguente. Se non si specifica una regola del firewall IP o si aggiunge una rete virtuale in questa pagina, è possibile accedere allo spazio dei nomi tramite **Internet pubblico** (usando la chiave di accesso).  

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="Scheda reti-opzione reti selezionate" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    Se si seleziona l'opzione **tutte le reti** , l'hub eventi accetta le connessioni da qualsiasi indirizzo IP (usando la chiave di accesso). Questa impostazione equivale a una regola che accetti l'intervallo di indirizzi IP 0.0.0.0/0. 

    ![Screenshot che mostra la pagina "firewall e reti virtuali" con l'opzione "tutte le reti" selezionata.](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Per limitare l'accesso a indirizzi IP specifici, verificare che sia selezionata l'opzione **reti selezionate** . Nella sezione **Firewall** seguire questa procedura:
    1. Selezionare l'opzione **Aggiungere l'indirizzo IP client** per concedere all'IP del client corrente l'accesso allo spazio dei nomi. 
    2. Per **Intervallo di indirizzi** immettere un indirizzo IPv4 specifico o un intervallo di indirizzi IPv4 in notazione CIDR. 
3. Specificare se si vuole **consentire ai servizi Microsoft attendibili di ignorare il firewall**. Per informazioni dettagliate, vedere [Servizi Microsoft attendibili](#trusted-microsoft-services) . 

      ![Opzione Firewall - Tutte le reti selezionata](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. Selezionare **Salva** sulla barra degli strumenti per salvare le impostazioni. Attendere qualche minuto la visualizzazione della conferma tra le notifiche del portale.

    > [!NOTE]
    > Per limitare l'accesso a reti virtuali specifiche, vedere [consentire l'accesso da reti specifiche](event-hubs-service-endpoints.md).

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]


## <a name="use-resource-manager-template"></a>Usare i modelli di Resource Manager

> [!IMPORTANT]
> Le regole del firewall sono supportate nei livelli **standard** e **dedicato** di Hub eventi. Non sono supportate nel livello Basic.

Il modello di Resource Manager seguente consente di aggiungere una regola di filtro IP a uno spazio dei nomi esistente di Hub eventi.

Parametri del modello:

- **ipMask** è un singolo indirizzo IPv4 o un blocco di indirizzi IP in notazione CIDR. Ad esempio, nella notazione CIDR, 70.37.104.0/24 rappresenta i 256 indirizzi IPv4, da 70.37.104.0 a 70.37.104.255, con 24 che indica il numero di bit di prefisso significativi per l'intervallo.

> [!NOTE]
> Sebbene non siano possibili regole di rifiuto, il modello di Azure Resource Manager ha l'azione predefinita impostata su **"Consenti"** , che non limita le connessioni.
> Quando si apportano regole di rete virtuale o di firewall, è necessario modificare **_"DefaultAction"_**
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
        "type": "Microsoft.EventHub/namespaces/networkrulesets",
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
