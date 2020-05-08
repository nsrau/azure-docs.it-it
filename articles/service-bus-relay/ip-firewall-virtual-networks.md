---
title: Configura IP firewall per lo spazio dei nomi di inoltro di Azure
description: Questo articolo descrive come usare le regole del firewall per consentire le connessioni da indirizzi IP specifici agli spazi dei nomi di inoltro di Azure.
services: service-bus-relay
documentationcenter: ''
author: spelluru
ms.service: service-bus-relay
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 05/07/2020
ms.author: spelluru
ms.openlocfilehash: e2a9e89ca8c3b77b09a58a45959f1acda1457b56
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984811"
---
# <a name="configure-ip-firewall-for-an-azure-relay-namespace"></a>Configurare il firewall IP per uno spazio dei nomi di inoltro di Azure
Per impostazione predefinita, gli spazi dei nomi di inoltro sono accessibili da Internet, purché la richiesta venga fornita con autenticazione e autorizzazione valide. Con il firewall IP, è possibile limitarlo ulteriormente a un set di indirizzi IPv4 o a intervalli di indirizzi IPv4 in notazione [CIDR (instradamento tra domini senza classi)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Questa funzionalità è utile negli scenari in cui il servizio di inoltro di Azure deve essere accessibile solo da determinati siti noti. Le regole del firewall consentono di configurare regole per accettare il traffico originato da indirizzi IPv4 specifici. Se ad esempio si usa l'inoltro con [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services), è possibile creare una **regola del firewall** per consentire il traffico solo da indirizzi IP dell'infrastruttura locale. 


> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. 


## <a name="enable-ip-firewall-rules"></a>Abilitare le regole del firewall IP
Le regole del firewall IP vengono applicate a livello di spazio dei nomi. Vengono pertanto applicate a tutte le connessioni provenienti dai client con qualsiasi protocollo supportato. Qualsiasi tentativo di connessione da un indirizzo IP che non corrisponde a una regola IP consentita nello spazio dei nomi viene rifiutato come non autorizzato. Nella risposta non viene fatto riferimento alla regola IP. Le regole del filtro IP vengono applicate in ordine e la prima regola corrispondente all'indirizzo IP determina l'azione di accettazione o rifiuto.

### <a name="use-azure-portal"></a>Usare il portale di Azure
Questa sezione illustra come usare la portale di Azure per creare regole del firewall IP per uno spazio dei nomi. 

1. Passare allo **spazio dei nomi di inoltro** nel [portale di Azure](https://portal.azure.com).
2. Nel menu a sinistra selezionare opzione di **rete** . Se si seleziona l'opzione **tutte le reti** nella sezione **Consenti accesso da** , lo spazio dei nomi di inoltro accetta connessioni da qualsiasi indirizzo IP. Questa impostazione equivale a una regola che accetta l'intervallo di indirizzi IP 0.0.0.0/0. 

    ![Opzione Firewall-tutte le reti selezionata](./media/ip-firewall/all-networks-selected.png)
1. Per limitare l'accesso a reti e indirizzi IP specifici, selezionare l'opzione **reti selezionate** . Nella sezione **Firewall** , attenersi alla seguente procedura:
    1. Selezionare l'opzione **Aggiungi indirizzo IP client** per concedere all'IP del client corrente l'accesso allo spazio dei nomi. 
    2. Per **intervallo di indirizzi**, immettere un indirizzo IPv4 specifico o un intervallo di indirizzi IPv4 nella notazione CIDR. 
    3. Specificare se si desidera **consentire ai servizi Microsoft attendibili di ignorare il firewall**. 

        ![Opzione Firewall-tutte le reti selezionata](./media/ip-firewall/selected-networks-trusted-access-disabled.png)
3. Selezionare **Salva** sulla barra degli strumenti per salvare le impostazioni. Attendere alcuni minuti prima che la conferma venga visualizzata nelle notifiche del portale.


### <a name="use-resource-manager-template"></a>Usare i modelli di Resource Manager
Il modello di Gestione risorse seguente consente di aggiungere una regola di filtro IP a uno spazio dei nomi di inoltro esistente.

Il modello accetta un parametro: **ipMask**, ovvero un singolo indirizzo IPv4 o un blocco di indirizzi IP nella notazione CIDR. Ad esempio, nella notazione CIDR, 70.37.104.0/24 rappresenta i 256 indirizzi IPv4, da 70.37.104.0 a 70.37.104.255, con 24 che indica il numero di bit di prefisso significativi per l'intervallo.

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
Per informazioni sulle altre funzionalità correlate alla sicurezza di rete, vedere [sicurezza di rete](network-security.md).


<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-Deploy]:: 
