<properties
   pageTitle="Panoramica del routing di contenuti basato su URL | Microsoft Azure"
   description="Questa pagina fornisce una panoramica del routing di contenuti basato su URL del gateway applicazione, della configurazione UrlPathMap e della regola PathBasedRouting."
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
   ms.date="08/18/2016"
   ms.author="gwallace"/>  

# Panoramica del routing basato su percorso URL

Il routing basato su percorso URL consente di instradare il traffico a pool di server back-end in base ai percorsi URL della richiesta. Uno degli scenari è l'instradamento delle richieste di tipi di contenuto diversi a pool di server back-end diversi. Nell'esempio seguente, il gateway applicazione soddisfa le richieste di traffico per contoso.com dai tre pool di server back-end, ad esempio VideoServerPool, ImageServerPool e DefaultServerPool.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)  

Le richieste per http://contoso.com/video* vengono indirizzate a VideoServerPool, mentre quelle per http://contoso.com/images* vengono indirizzate a ImageServerPool. In caso di mancata corrispondenza dei percorsi, viene selezionato DefaultServerPool.

## Elemento di configurazione UrlPathMap

L'elemento UrlPathMap consente di specificare modelli di percorso dei mapping dei pool di server back-end. Di seguito è riportato il frammento dell'elemento urlPathMap del file modello.

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
	

>[AZURE.NOTE] PathPattern: questa impostazione è un elenco dei modelli di percorso usati per la corrispondenza. Ognuno deve iniziare con una barra / e l'unica posizione in cui è consentito il carattere "*" è alla fine dopo "/". La stringa inviata al selettore di percorsi non include alcun testo dopo il primo carattere "?" o "#" e questi caratteri non sono consentiti qui.

Per altre informazioni, vedere un [modello di Azure Resource Manager che usa il routing basato su URL](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing).

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

Dopo aver acquisito familiarità con il routing di contenuti basato su URL, passare a [Create a Path-based rule for an application gateway by using the portal](application-gateway-create-url-route-portal.md) (Creare una regola basata sul percorso per un gateway applicazione usando il portale) per la creazione di un gateway applicazione con regole di routing basate su URL.

<!---HONumber=AcomDC_0824_2016-->