---
title: Regole del firewall per il bus di servizio di Azure | Microsoft Docs
description: Uso delle regole del firewall per consentire le connessioni da indirizzi IP specifici a bus di servizio di Azure.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 04/23/2019
ms.author: aschhab
ms.openlocfilehash: 02d6e150e638321e11a8dec9838e360faa00783e
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280946"
---
# <a name="use-firewall-rules"></a>Usare le regole del firewall

Per gli scenari in cui bus di servizio di Azure deve essere accessibile solo da determinati siti noti, le regole del firewall consentono di configurare regole per accettare il traffico proveniente da indirizzi IPv4 specifici, ad esempio quelli di un gateway NAT aziendale.

## <a name="when-to-use"></a>Quando usare la funzionalità

Se si desidera configurare il bus di servizio di Microsoft Azure in modo che riceva il traffico solo da un intervallo specificato di indirizzi IP e rifiutare tutto il resto, è possibile usare un *Firewall* per bloccare gli endpoint del bus di servizio di Microsoft Azure da altri indirizzi IP. Si usa ad esempio il bus di servizio con [Azure Express Route][express-route] per creare connessioni private all'infrastruttura locale. 

## <a name="how-filter-rules-are-applied"></a>Come vengono applicate le regole di filtro

Le regole del filtro IP vengono applicate a livello dello spazio dei nomi del bus di servizio. Vengono pertanto applicate a tutte le connessioni provenienti dai client con qualsiasi protocollo supportato.

Qualsiasi tentativo di connessione proveniente da un indirizzo IP che non corrisponde a una regola di indirizzi IP consentiti nello spazio dei nomi di bus di servizio di Microsoft Azure viene rifiutato come non autorizzato. Nella risposta non viene fatto riferimento alla regola IP.

## <a name="default-setting"></a>Impostazione predefinita

Per impostazione predefinita, la griglia **Filtro IP** nel portale per il bus di servizio è vuota. Questa impostazione predefinita indica che lo spazio dei nomi accetta connessioni da qualsiasi indirizzo IP. Questa impostazione predefinita equivale a una regola che accetta l'intervallo di indirizzi IP 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Valutazione delle regole del filtro IP

Le regole del filtro IP vengono applicate in ordine e la prima regola corrispondente all'indirizzo IP determina l'azione di accettazione o rifiuto.

>[!WARNING]
> L'implementazione delle regole del firewall può impedire ad altri servizi di Azure di interagire con bus di servizio di Microsoft Azure.
>
> I servizi Microsoft attendibili non sono supportati quando sono implementate le funzionalità di filtro IP (regole del firewall), ma saranno presto disponibili.
>
> Scenari comuni di Azure che non supportano il filtro IP (l'elenco **NON** è esaustivo)
> - Azure Stream Analytics
> - Integrazione con Griglia di eventi di Azure
> - Route dell'hub IoT di Azure
> - Azure IoT Device Explorer
>
> I servizi Microsoft seguenti devono essere in una rete virtuale
> - servizio app di Azure
> - Funzioni di Azure

### <a name="creating-a-virtual-network-and-firewall-rule-with-azure-resource-manager-templates"></a>Creazione di una regola di rete virtuale e di una regola del firewall con i modelli di Azure Resource Manager

> [!IMPORTANT]
> I firewall e le reti virtuali sono supportati solo nel livello **Premium** del bus di servizio.

Il modello di Resource Manager seguente consente di aggiungere una regola di rete virtuale a uno spazio dei nomi esistente del bus di servizio.

Parametri del modello:

- **ipMask** è un singolo indirizzo IPv4 o un blocco di indirizzi IP in notazione CIDR. Ad esempio, nella notazione CIDR, 70.37.104.0/24 rappresenta i 256 indirizzi IPv4, da 70.37.104.0 a 70.37.104.255, con 24 che indica il numero di bit di prefisso significativi per l'intervallo.

> [!NOTE]
> Sebbene non siano possibili regole di rifiuto, il modello di Azure Resource Manager ha l'azione predefinita impostata su **"Consenti"** , che non limita le connessioni.
> Quando si creano regole di rete virtuale o del firewall, occorre modificare ***"defaultAction"***
> 
> Da
> ```json
> "defaultAction": "Allow"
> ```
> To
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

- [Endpoint del servizio rete virtuale per il bus di servizio][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
