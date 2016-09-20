<properties
   pageTitle="Supporto di WebSocket nel gateway applicazione | Microsoft Azure"
   description="Questa pagina offre una panoramica del supporto di WebSocket nel gateway applicazione."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/18/2016"
   ms.author="amsriva"/>  

# Supporto di WebSocket nel gateway applicazione

Il protocollo WebSocket standardizzato nella specifica [RFC6455](https://tools.ietf.org/html/rfc6455) consente una comunicazione full duplex tra server e client su una connessione TCP con esecuzione prolungata. Tra server Web e client è così possibile una comunicazione più interattiva, che può essere bidirezionale senza che sia necessario il polling richiesto invece nelle implementazioni basate su HTTP. A differenza del protocollo HTTP, WebSocket presenta un overhead ridotto e può riusare la stessa connessione TCP per più richieste/risposte garantendo così un utilizzo più efficiente delle risorse. I protocolli WebSocket sono progettati per usare le porte HTTP 80 e 443 tradizionali.


Il gateway applicazione offre il supporto nativo per WebSocket in tutte le dimensioni di gateway. Non esistono impostazioni configurabili dall'utente per abilitare o disabilitare in modo selettivo il supporto di WebSocket. È possibile continuare a usare un listener HTTP standard sulla porta 80/443 per ricevere il traffico WebSocket. Il traffico WebSocket viene quindi indirizzato al server back-end abilitato per WebSocket usando il pool back-end appropriato, come specificato nelle regole del gateway applicazione.

Il server back-end deve rispondere ai probe del gateway applicazione, descritti nella [panoramica dei probe di integrità](application-gateway-probe-overview.md). I probe di integrità del gateway applicazione sono solo HTTP/HTTPS. Di conseguenza, affinché il gateway applicazione possa instradare il traffico WebSocket al server è necessario che ogni server back-end risponda a probe HTTP.


## Elemento di configurazione del listener

Per supportare WebSocket può essere usato un listener HTTP esistente. Di seguito è riportato il frammento dell'elemento HttpListeners di un file modello di esempio. Per supportare WebSocket e il traffico WebSocket sicuro saranno necessari listener sia HTTP che HTTPS. Per creare un gateway applicazione con listener sulla porta 80/443 per supportare il traffico WebSocket, è possibile usare in modo analogo il [portale](application-gateway-create-gateway-portal.md) o [PowerShell](application-gateway-create-gateway-arm.md).


 		"httpListeners": [
                    {
                        "name": "appGatewayHttpsListener",
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
                        }
                    },
                    {
                        "name": "appGatewayHttpListener",
                        "properties": {
                            "FrontendIPConfiguration": {
                                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
                            },
                            "FrontendPort": {
                                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
                            },
                            "Protocol": "Http",
                        }
                    }
                ],

## Configurazione di BackendAddressPool, BackendHttpSetting e della regola di routing

È necessario usare BackendAddressPool per definire un pool back-end con server abilitati per WebSocket. È necessario definire BackendHttpSetting solo con la porta back-end 80/443. Le proprietà per l'affinità basata su cookie e requestTimeouts non sono rilevanti per il traffico WebSocket. Nella regola di routing non è necessaria alcuna modifica. È ancora necessario usare la regola di routing "Basic" per collegare il listener appropriato al pool di indirizzi back-end corrispondente.

	"requestRoutingRules": [{
		"name": "<ruleName1>",
		"properties": {
			"RuleType": "Basic",
			"httpListener": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener')]"
			},
			"backendAddressPool": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
			},
			"backendHttpSettings": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
			}
		}

	}, {
		"name": "<ruleName2>",
		"properties": {
			"RuleType": "Basic",
			"httpListener": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener')]"
			},
			"backendAddressPool": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
			},
			"backendHttpSettings": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
			}

		}
	}]

## Back-end abilitato per WebSocket
Per il funzionamento di WebSocket, è necessario che il back-end includa un server Web HTTP/HTTPS in esecuzione sulla porta configurata (in genere 80/443). Il protocollo WebSocket richiede infatti un handshake iniziale HTTP con un campo di intestazione "Upgrade" per il protocollo WebSocket.

	GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: http://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13

Un altro motivo risiede nel fatto che il probe di integrità del back-end del gateway applicazione supporta solo i protocolli HTTP/HTTPS. Se il server back-end non risponde ai probe HTTP/HTTPS, viene escluso dal pool back-end e non potrà essere raggiunto da alcuna richiesta, incluse le richieste WebSocket.

	
## Passaggi successivi 

Dopo aver acquisito familiarità con il supporto di WebSocket, [creare un gateway applicazione](application-gateway-create-gateway.md) per iniziare a usare un'applicazione Web abilitata per WebSocket.

	    

<!---HONumber=AcomDC_0907_2016-->