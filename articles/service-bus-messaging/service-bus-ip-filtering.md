---
title: Configurare le regole del firewall IP per il bus di servizio di AzureConfigure IP firewall rules for Azure Service Bus
description: Uso delle regole del firewall per consentire le connessioni da indirizzi IP specifici a bus di servizio di Azure.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: a20882de34cb306b767959e21327180ff284e658
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475944"
---
# <a name="configure-ip-firewall-rules-for-azure-service-bus"></a>Configurare le regole del firewall IP per il bus di servizio di AzureConfigure IP firewall rules for Azure Service Bus
Per impostazione predefinita, gli spazi dei nomi del bus di servizio sono accessibili da Internet finché la richiesta viene fornita con l'autenticazione e l'autorizzazione valide. Con il firewall IP, è possibile limitarlo ulteriormente a un set di indirizzi IPv4 o intervalli di indirizzi IPv4 nella notazione [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Questa funzionalità è utile negli scenari in cui il bus di servizio di Azure deve essere accessibile solo da determinati siti noti. Le regole del firewall consentono di configurare le regole per accettare il traffico proveniente da indirizzi IPv4 specifici. Ad esempio, se si usa il bus di servizio con [Azure Express Route,][express-route]è possibile creare una **regola del firewall** per consentire il traffico solo dagli indirizzi IP dell'infrastruttura locale o dagli indirizzi di un gateway NAT aziendale. 

## <a name="ip-firewall-rules"></a>Regole del firewall IP
Le regole del firewall IP vengono applicate a livello di spazio dei nomi del bus di servizio. Vengono pertanto applicate a tutte le connessioni provenienti dai client con qualsiasi protocollo supportato. Qualsiasi tentativo di connessione proveniente da un indirizzo IP che non corrisponde a una regola di indirizzi IP consentiti nello spazio dei nomi di bus di servizio di Microsoft Azure viene rifiutato come non autorizzato. Nella risposta non viene fatto riferimento alla regola IP. Le regole del filtro IP vengono applicate in ordine e la prima regola corrispondente all'indirizzo IP determina l'azione di accettazione o rifiuto.

## <a name="use-azure-portal"></a>Usare il portale di Azure
Questa sezione illustra come usare il portale di Azure per creare regole del firewall IP per uno spazio dei nomi del bus di servizio. 

1. Passare allo spazio dei nomi del bus di servizio nel portale di Azure.Navigate to your **Service Bus namespace** in the Azure [portal](https://portal.azure.com).
2. Nel menu a sinistra selezionare l'opzione **Rete.On** the left menu, select Networking option. Per impostazione predefinita, l'opzione **Tutte le reti** è selezionata. Lo spazio dei nomi del bus di servizio accetta connessioni da qualsiasi indirizzo IP. Questa impostazione predefinita equivale a una regola che accetta l'intervallo di indirizzi IP 0.0.0.0/0. 

    ![Firewall - Opzione Tutte le reti selezionata](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Selezionare l'opzione **Reti selezionate** nella parte superiore della pagina. Nella sezione **Firewall,** attenersi alla seguente procedura:
    1. Selezionare l'opzione **Aggiungi l'indirizzo IP del client** per concedere all'IP client corrente l'accesso allo spazio dei nomi. 
    2. Per **l'intervallo**di indirizzi, immettere un indirizzo IPv4 specifico o un intervallo di indirizzo IPv4 in notazione CIDR. 
    3. Specificare se si desidera **consentire ai servizi Microsoft attendibili di ignorare questo firewall.** 

        ![Firewall - Opzione Tutte le reti selezionata](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. Selezionare **Salva** sulla barra degli strumenti per salvare le impostazioni. Attendere alcuni minuti che la conferma venga visualizzata nelle notifiche del portale.

## <a name="use-resource-manager-template"></a>Usare i modelli di Resource Manager
Questa sezione include un modello di Azure Resource Manager di esempio che crea una rete virtuale e una regola del firewall.

> [!IMPORTANT]
> Firewall e reti virtuali sono supportati solo nel livello **premium** del bus di servizio.

Il modello di Resource Manager seguente consente di aggiungere una regola di rete virtuale a uno spazio dei nomi esistente del bus di servizio.

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
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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

Per limitare l'accesso al bus di servizio dalle reti virtuali di Azure, vedere il collegamento seguente:

- [Usare gli endpoint servizio di rete virtuale con il bus di servizio di Azure][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
