---
title: Supporto di WebSocket nel gateway applicazione di Azure | Microsoft Docs
description: Questa pagina offre una panoramica del supporto di WebSocket nel gateway applicazione.
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 8968dac1-e9bc-4fa1-8415-96decacab83f
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: amsriva
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 75b06ddd02da231b7813c609c848c75e42116da5
ms.contentlocale: it-it
ms.lasthandoff: 06/07/2017

---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Panoramica del supporto di WebSocket nel gateway dell'applicazione

Il gateway applicazione offre il supporto nativo per WebSocket in tutte le dimensioni di gateway. Non esistono impostazioni configurabili dall'utente per abilitare o disabilitare in modo selettivo il supporto di WebSocket. 

Il protocollo WebSocket standardizzato nella specifica [RFC6455](https://tools.ietf.org/html/rfc6455) consente una comunicazione full duplex tra un server e un client su una connessione TCP con esecuzione prolungata. Questa funzionalità consente una comunicazione più interattiva tra il server Web e il client, che può essere bidirezionale senza che sia necessario il polling richiesto invece nelle implementazioni basate su HTTP. A differenza del protocollo HTTP, WebSocket presenta un overhead ridotto e può riusare la stessa connessione TCP per più richieste/risposte garantendo così un utilizzo più efficiente delle risorse. I protocolli WebSocket sono progettati per usare le porte HTTP 80 e 443 tradizionali.

È possibile continuare a usare un listener HTTP standard nella porta 80 o 443 per ricevere il traffico WebSocket. Il traffico WebSocket viene quindi indirizzato al server back-end abilitato per WebSocket usando il pool back-end appropriato, come specificato nelle regole del gateway applicazione. Il server back-end deve rispondere ai probe del gateway applicazione, descritti nella sezione di [panoramica dei probe di integrità](application-gateway-probe-overview.md). I probe di integrità del gateway applicazione sono solo HTTP e HTTPS. Ogni server back-end deve rispondere a probe HTTP per il gateway applicazione per instradare il traffico WebSocket al server.

## <a name="listener-configuration-element"></a>Elemento di configurazione del listener

Per supportare il traffico WebSocket è possibile usare un listener HTTP esistente. Di seguito è riportato il frammento dell'elemento httpListeners di un file modello di esempio. Per supportare WebSocket e il traffico WebSocket sicuro saranno necessari listener sia HTTP che HTTPS. Per creare un gateway applicazione con listener sulla porta 80/443 per supportare il traffico WebSocket, è possibile usare in modo analogo il [portale](application-gateway-create-gateway-portal.md) o [PowerShell](application-gateway-create-gateway-arm.md).

```json
"httpListeners": [
        {
            "name": "appGatewayHttpsListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/DefaultFrontendPublicIP"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort443'"
                },
                "Protocol": "Https",
                "SslCertificate": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/sslCertificates/appGatewaySslCert1'"
                },
            }
        },
        {
            "name": "appGatewayHttpListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/appGatewayFrontendIP'"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort80'"
                },
                "Protocol": "Http",
            }
        }
    ],
```

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Configurazione di BackendAddressPool, BackendHttpSetting e della regola di routing

BackendAddressPool viene usato per definire un pool back-end con server abilitati per WebSocket. backendHttpSetting è definito con una porta back-end 80 e 443. Le proprietà per l'affinità basata su cookie e requestTimeouts non sono rilevanti per il traffico WebSocket. Non è necessaria alcuna modifica nella regola di routing. Viene usato 'Basic' per collegare il listener appropriato al pool di indirizzi back-end corrispondente. 

```json
"requestRoutingRules": [{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpsListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}, {
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }

    }
}]
```

## <a name="websocket-enabled-backend"></a>Back-end abilitato per WebSocket

Per il funzionamento di WebSocket, è necessario che il back-end includa un server Web HTTP/HTTPS in esecuzione sulla porta configurata (in genere 80/443). Il protocollo WebSocket richiede infatti un handshake iniziale HTTP con un campo di intestazione Upgrade per il protocollo WebSocket. Di seguito è riportato un esempio di intestazione:

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: http://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

Un altro motivo risiede nel fatto che il probe di integrità del back-end del gateway applicazione supporta solo i protocolli HTTP e HTTPS. Se il server back-end non risponde ai probe HTTP o HTTPS, il server viene escluso dal pool back-end.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver acquisito familiarità con il supporto di WebSocket, [creare un gateway applicazione](application-gateway-create-gateway.md) per iniziare a usare un'applicazione Web abilitata per WebSocket.


