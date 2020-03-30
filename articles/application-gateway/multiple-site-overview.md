---
title: Hosting di più siti in un gateway applicazione di Azure
description: Questo articolo offre una panoramica del supporto di più siti in un gateway applicazione di Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 03/11/2020
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 4d945a255dacd35c61c3c80574b7d46b56de4aab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257411"
---
# <a name="application-gateway-multiple-site-hosting"></a>Hosting di più siti in un gateway applicazione

L'hosting di più siti consente di configurare più applicazioni Web sulla stessa porta di un gateway applicazione. Questa funzionalità consente di configurare una topologia più efficiente per le distribuzioni aggiungendo fino a 100 siti Web a un unico gateway applicazione. Ogni sito Web può essere indirizzato al proprio pool back-end. Nell'esempio seguente, il gateway `contoso.com` `fabrikam.com` applicazione gestisce il traffico per e da due pool di server back-end denominati ContosoServerPool e FabrikamServerPool.

![imageURLroute](./media/multiple-site-overview/multisite.png)

> [!IMPORTANT]
> Le regole vengono elaborate nell'ordine in cui sono elencate nel portale per lo SKU v1. Per lo SKU v2, le corrispondenze esatte hanno una precedenza maggiore. È consigliabile configurare i listener multisito prima di configurare un listener di base.  In questo modo il traffico viene indirizzato al back-end appropriato. Se un listener di base viene elencato per primo e corrisponde a una richiesta in ingresso, sarà tale listener a elaborarla.

Per le richieste `http://contoso.com` viene eseguito il routing verso ContosoServerPool mentre per le richieste `http://fabrikam.com` viene eseguito il routing verso FabrikamServerPool.

Analogamente, è possibile ospitare più sottodomini dello stesso dominio padre nella stessa distribuzione del gateway applicazione. Ad esempio, è `http://blog.contoso.com` `http://app.contoso.com` possibile ospitare e in una distribuzione di un singolo gateway applicazione.

## <a name="host-headers-and-server-name-indication-sni"></a>Intestazioni host e indicazione nome server (SNI)

Per abilitare l'hosting di più siti nella stessa infrastruttura sono disponibili tre comuni meccanismi.

1. Ospitare più applicazioni Web con un indirizzo IP univoco per ognuna.
2. Usare il nome host per ospitare più applicazioni Web nello stesso indirizzo IP.
3. Usare porte diverse per ospitare più applicazioni Web nello stesso indirizzo IP.

Attualmente il gateway applicazione supporta un singolo indirizzo IP pubblico in cui è in ascolto del traffico. Quindi più applicazioni, ognuna con il proprio indirizzo IP non è attualmente supportata. 

Il gateway applicazione supporta più applicazioni in ascolto su porte diverse, ma questo scenario richiede che le applicazioni accettino il traffico su porte non standard. Spesso non si tratta di una configurazione desiderata.

Il gateway applicazione si basa su intestazioni host HTTP 1.1 per ospitare più siti Web nello stesso indirizzo IP pubblico e nella stessa porta. I siti ospitati nel gateway applicazione possono anche supportare l'offload TLS con l'estensione TLS SNI (Server Name Indication). In questo scenario il browser client e la Web farm back-end devono quindi supportare HTTP/1.1 e l'estensione TLS definita nella specifica RFC 6066.

## <a name="listener-configuration-element"></a>Elemento di configurazione del listener

Gli elementi di configurazione HTTPListener esistenti sono stati migliorati per supportare gli elementi di indicazione del nome host e del nome del server. Viene utilizzato dal gateway applicazione per instradare il traffico al pool back-end appropriato. 

L'esempio di codice seguente è il frammento di un HttpListeners elemento da un file modello:The following code example is the snippet of an HttpListeners element from a template file:

```json
"httpListeners": [
    {
        "name": "appGatewayHttpsListener1",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
            },
            "Protocol": "Https",
            "SslCertificate": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
            },
            "HostName": "contoso.com",
            "RequireServerNameIndication": "true"
        }
    },
    {
        "name": "appGatewayHttpListener2",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
            },
            "Protocol": "Http",
            "HostName": "fabrikam.com",
            "RequireServerNameIndication": "false"
        }
    }
],
```

Per una distribuzione basata su modello end-to-end, vedere il [modello di Resource Manager con hosting di più siti](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting).

## <a name="routing-rule"></a>Regola di routing

Non è necessaria alcuna modifica nella regola di routing. È ancora necessario scegliere la regola di routing "Basic" per collegare il listener del sito appropriato al pool di indirizzi back-end corrispondente.

```json
"requestRoutingRules": [
{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener1')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

},
{
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener2')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/FabrikamServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}
]
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver acquisito familiarità con l'hosting di più siti, vedere [Creare un gateway applicazione per l'hosting di più applicazioni Web](tutorial-multiple-sites-powershell.md) per creare un gateway applicazione con supporto di più applicazioni Web.

