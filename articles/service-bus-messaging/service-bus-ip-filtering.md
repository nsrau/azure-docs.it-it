---
title: Configurare le regole del firewall IP per il bus di servizio di Azure
description: Uso delle regole del firewall per consentire le connessioni da indirizzi IP specifici a bus di servizio di Azure.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a5ae491f82e73c5364788dff8b531e81d17ebb68
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341444"
---
# <a name="configure-ip-firewall-rules-for-azure-service-bus"></a>Configurare le regole del firewall IP per il bus di servizio di Azure
Per impostazione predefinita, gli spazi dei nomi del bus di servizio sono accessibili da Internet, purché la richiesta sia accompagnata da un'autenticazione e da un'autorizzazione valide. Con un firewall per gli indirizzi IP, è possibile limitare ulteriormente l'accesso a un set di indirizzi IPv4 o a intervalli di indirizzi IPv4 in notazione [CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Questa funzionalità è utile negli scenari in cui il bus di servizio deve essere accessibile solo da siti noti specifici. Le regole del firewall consentono di configurare regole di ammissione del traffico proveniente da indirizzi IPv4 specifici. Se ad esempio si usa il bus di servizio con [Azure ExpressRoute][express-route], è possibile creare una **regola del firewall** per consentire traffico solo da indirizzi IP o indirizzi IP dell'infrastruttura locale di un gateway NAT aziendale. 

> [!IMPORTANT]
> I firewall e le reti virtuali sono supportate solo nel **livello Premium** del bus di servizio. Se non è possibile eseguire l'aggiornamento al **livello Premier**, si consiglia di proteggere il token di firma di accesso condiviso (SAS) e condividerlo solo con gli utenti autorizzati. Per altre informazioni sull'autenticazione SAS, vedere [Autenticazione e autorizzazione](service-bus-authentication-and-authorization.md#shared-access-signature).

## <a name="ip-firewall-rules"></a>Regole del firewall IP
Le regole del firewall IP vengono applicate a livello dello spazio dei nomi del bus di servizio. Vengono pertanto applicate a tutte le connessioni provenienti dai client con qualsiasi protocollo supportato. Qualsiasi tentativo di connessione proveniente da un indirizzo IP che non corrisponde a una regola di indirizzi IP consentiti nello spazio dei nomi di bus di servizio di Microsoft Azure viene rifiutato come non autorizzato. Nella risposta non viene fatto riferimento alla regola IP. Le regole del filtro IP vengono applicate in ordine e la prima regola corrispondente all'indirizzo IP determina l'azione di accettazione o rifiuto.

>[!WARNING]
> L'implementazione delle regole del firewall può impedire ad altri servizi di Azure di interagire con bus di servizio di Microsoft Azure.
>
> I servizi Microsoft attendibili non sono supportati quando sono implementate le funzionalità di filtro IP (regole del firewall), ma saranno presto disponibili.
>
> Scenari comuni di Azure che non supportano il filtro IP (l'elenco **NON** è esaustivo)
> - Integrazione con Griglia di eventi di Azure
> - Route dell'hub IoT di Azure
> - Azure IoT Device Explorer
>
> I servizi Microsoft seguenti devono essere in una rete virtuale
> - Servizio app di Azure
> - Funzioni di Azure

## <a name="use-azure-portal"></a>Usare il portale di Azure
Questa sezione illustra come usare il portale di Azure per creare regole del firewall per gli indirizzi IP per uno spazio dei nomi del bus di servizio. 

1. Passare allo  **spazio dei nomi del bus di servizio** nel [portale di Azure](https://portal.azure.com).
2. Nel menu a sinistra selezionare l'opzione **Rete**. Per impostazione predefinita, è selezionata l'opzione **Tutte le reti**. Lo spazio dei nomi del bus di servizio accetta connessioni da qualsiasi indirizzo IP. Questa impostazione predefinita equivale a una regola che accetta l'intervallo di indirizzi IP 0.0.0.0/0. 

    ![Opzione Firewall - Tutte le reti selezionata](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Selezionare l'opzione **Reti selezionate** sulla parte superiore della pagina. Nella sezione **Firewall** seguire questa procedura:
    1. Selezionare l'opzione **Aggiungere l'indirizzo IP client** per concedere all'IP del client corrente l'accesso allo spazio dei nomi. 
    2. Per **Intervallo di indirizzi** immettere un indirizzo IPv4 specifico o un intervallo di indirizzi IPv4 in notazione CIDR. 
    3. Specificare se si vuole **consentire ai servizi Microsoft attendibili di ignorare il firewall**. 

        > [!WARNING]
        > Se si sceglie l'opzione **Reti selezionate** e non si specifica un indirizzo IP o un intervallo di indirizzi, il servizio consentirà il traffico da tutte le reti. 

        ![Opzione Firewall - Tutte le reti selezionata](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. Selezionare **Salva** sulla barra degli strumenti per salvare le impostazioni. Attendere qualche minuto la visualizzazione della conferma tra le notifiche del portale.

## <a name="use-resource-manager-template"></a>Usare i modelli di Resource Manager
Questa sezione include un modello di Azure Resource Manager di esempio che consente di creare una rete virtuale e una regola del firewall.


Il modello di Resource Manager seguente consente di aggiungere una regola di rete virtuale a uno spazio dei nomi esistente del bus di servizio.

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
