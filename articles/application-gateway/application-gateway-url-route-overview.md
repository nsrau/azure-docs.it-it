<properties
   pageTitle="Panoramica del routing dei contenuti basato su URL | Microsoft Azure"
   description="Questa pagina fornisce una panoramica del routing dei contenuti basato su URL del gateway applicazione, della configurazione UrlPathMap e della regola PathBasedRouting."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>  
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="gwallace"/>  

# Panoramica del routing basato su percorso URL

Il routing basato su percorso URL consente di instradare il traffico a pool di server back-end in base ai percorsi URL della richiesta. Uno degli scenari è l'instradamento delle richieste di tipi di contenuto diversi a differenti pool di server back-end. Nell'esempio seguente, il gateway applicazione soddisfa le richieste di traffico per contoso.com dai tre pool di server back-end, ad esempio VideoServerPool, ImageServerPool e DefaultServerPool.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

Le richieste per http://contoso.com/video* verranno indirizzate a VideoServerPool, mentre http://contoso.com/images* verrà indirizzato a ImageServerPool. In caso di mancata corrispondenza dei percorsi, viene selezionato DefaultServerPool.

## Elemento di configurazione UrlPathMap

L'elemento UrlPathMap consente di specificare modelli di percorso dei mapping dei pool di server back-end. Di seguito è riportato il frammento dell'elemento urlPathMap del file di modello.

	"urlPathMaps": [
	{
    "name": "<urlPathMapName>",
    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/ urlPathMaps/<urlPathMapName>",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendAddressPools/<poolName>"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendHttpSettingsList/<settingsName>"
        },
        "pathRules": [
            {
                "paths": [
                    <pathPattern>
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendAddressPools/<poolName2>"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendHttpsettingsList/<settingsName2>"
                },

            },

        ],

    }
	}
	

>[AZURE.NOTE] PathPattern: elenco dei modelli di percorso usati per la corrispondenza. Ognuno di essi deve iniziare con una barra / e l'unica posizione in cui è consentito il simbolo * è alla fine, dopo '/'. La stringa inviata al selettore di percorsi non include alcun testo dopo il primo simbolo ? o # e questi caratteri non sono consentiti.

Per altre informazioni, vedere un [modello ARM usando il routing basato su URL](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing).

## Regola PathBasedRouting

RequestRoutingRule di tipo PathBasedRouting consente di associare un listener a un urlPathMap. Tutte le richieste ricevute per il listener vengono instradate in base ai criteri specificati in urlPathMap. Frammento della regola PathBasedRouting:

	"requestRoutingRules": [
  	{

    "name": "<ruleName>",
    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/requestRoutingRules/<ruleName>",
    "properties": {
        "ruleType": "PathBasedRouting",
        "httpListener": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/httpListeners/<listenerName>"
        },
        "urlPathMap": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/ urlPathMaps/<urlPathMapName>"
        },

    }
	
## Passaggi successivi 

Dopo aver acquisito familiarità con il routing dei contenuti basato su URL, passare a [Creare un gateway applicazione usando il routing basato su URL](application-gateway-create-url-route-arm-ps.md) per la creazione di un gateway applicazione con regole di routing basate su URL.

<!---HONumber=AcomDC_0810_2016-->