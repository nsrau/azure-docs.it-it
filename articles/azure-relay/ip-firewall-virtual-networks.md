---
title: Configurare il firewall IP per spazio dei nomi di inoltro di Azure
description: Questo articolo descrive come usare regole del firewall per consentire le connessioni da indirizzi IP specifici a spazi dei nomi di Inoltro di Azure.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 250158aff2ceb89e2823b711717f1d3a1cad438c
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90976011"
---
# <a name="configure-ip-firewall-for-an-azure-relay-namespace"></a>Configurare un firewall per gli indirizzi IP per uno spazio dei nomi di Inoltro di Azure
Per impostazione predefinita, gli spazi dei nomi di Inoltro sono accessibili da Internet, purché la richiesta sia accompagnata da un'autenticazione e da un'autorizzazione valide. Con un firewall per gli indirizzi IP, è possibile limitare ulteriormente l'accesso a un set di indirizzi IPv4 o a intervalli di indirizzi IPv4 in notazione [CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Questa funzionalità è utile negli scenari in cui Inoltro di Azure deve essere accessibile solo da siti noti specifici. Le regole del firewall consentono di configurare regole di ammissione del traffico proveniente da indirizzi IPv4 specifici. Se ad esempio si usa Inoltro con [Azure ExpressRoute](../expressroute/expressroute-faqs.md#supported-services), è possibile creare una **regola del firewall** per consentire traffico solo dagli indirizzi IP dell'infrastruttura locale. 


> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. 


## <a name="enable-ip-firewall-rules"></a>Abilitare regole del firewall per indirizzi IP
Le regole del firewall per indirizzi IP vengono applicate a livello dello spazio dei nomi. Vengono pertanto applicate a tutte le connessioni provenienti dai client con qualsiasi protocollo supportato. Qualsiasi tentativo di connessione proveniente da un indirizzo IP che non corrisponda a una regola di indirizzi IP consentiti nello spazio dei nomi viene rifiutato come non autorizzato. Nella risposta non viene fatto riferimento alla regola IP. Le regole del filtro IP vengono applicate in ordine e la prima regola corrispondente all'indirizzo IP determina l'azione di accettazione o rifiuto.

### <a name="use-azure-portal"></a>Usare il portale di Azure
Questa sezione illustra come usare il portale di Azure per creare regole del firewall per gli indirizzi IP per uno spazio dei nomi. 

1. Passare allo **Spazio dei nomi di inoltro** nel [portale di Azure](https://portal.azure.com).
2. Nel menu a sinistra selezionare l'opzione **Rete**. Se si seleziona l'opzione **Tutte le reti** nella sezione **Consenti l'accesso da**, lo spazio dei nomi di inoltro accetta connessioni da qualsiasi indirizzo IP. Questa impostazione equivale a una regola che accetti l'intervallo di indirizzi IP 0.0.0.0/0. 

    ![Screenshot mostra la pagina rete con l'opzione tutte le reti selezionata.](./media/ip-firewall/all-networks-selected.png)
1. Per limitare l'accesso a reti e indirizzi IP specifici, selezionare l'opzione **Reti selezionate**. Nella sezione **Firewall** seguire questa procedura:
    1. Selezionare l'opzione **Aggiungere l'indirizzo IP client** per concedere all'IP del client corrente l'accesso allo spazio dei nomi. 
    2. Per **Intervallo di indirizzi** immettere un indirizzo IPv4 specifico o un intervallo di indirizzi IPv4 in notazione CIDR. 

        ![Opzione Firewall - Tutte le reti selezionata](./media/ip-firewall/selected-networks-trusted-access-disabled.png)
3. Selezionare **Salva** sulla barra degli strumenti per salvare le impostazioni. Attendere qualche minuto la visualizzazione della conferma tra le notifiche del portale.


### <a name="use-resource-manager-template"></a>Usare i modelli di Resource Manager
Il modello di Resource Manager seguente consente di aggiungere una regola di filtro IP a uno spazio dei nomi esistente di Inoltro.

Il modello accetta un solo parametro: **ipMask**, che è un indirizzo IPv4 singolo o un blocco di indirizzi IP in notazione CIDR. Ad esempio, nella notazione CIDR, 70.37.104.0/24 rappresenta i 256 indirizzi IPv4, da 70.37.104.0 a 70.37.104.255, con 24 che indica il numero di bit di prefisso significativi per l'intervallo.

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
      "relayNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Relay namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('relayNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('relayNamespaceName')]",
        "type": "Microsoft.Relay/namespaces",
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
        "type": "Microsoft.Relay/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.Relay/namespaces/', parameters('relayNamespaceName'))]"
        ],
        "properties": {
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

Per distribuire il modello, seguire le istruzioni per [Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).



## <a name="next-steps"></a>Passaggi successivi
Per informazioni su altre funzionalità relative alla sicurezza di rete, vedere [Sicurezza di rete](network-security.md).


<!-- Links -->

[express-route]:  ../expressroute/expressroute-faqs.md#supported-services
