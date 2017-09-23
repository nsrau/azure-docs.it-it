---
title: Configurare l'offload SSL - Gateway applicazione di Azure - Interfaccia della riga di comando di Azure 2.0 | Microsoft Docs
description: Questo articolo contiene istruzioni per creare un gateway applicazione con offload SSL usando l'interfaccia della riga di comando di Azure 2.0.
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 032a514ddab625e4f7c5ef23a1da03a0162f43e3
ms.contentlocale: it-it
ms.lasthandoff: 08/30/2017

---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-cli-20"></a>Configurare un gateway applicazione per l'offload SSL con l'interfaccia della riga di comando di Azure 2.0

> [!div class="op_single_selector"]
> * [Portale di Azure](application-gateway-ssl-portal.md)
> * [PowerShell per Azure Resource Manager](application-gateway-ssl-arm.md)
> * [PowerShell classico per Azure](application-gateway-ssl.md)
> * [Interfaccia della riga di comando di Azure 2.0](application-gateway-ssl-cli.md)

Il gateway applicazione di Azure può essere configurato per terminare la sessione Secure Sockets Layer (SSL) nel gateway ed evitare costose attività di decrittografia SSL nella Web farm. L'offload SSL semplifica anche la gestione dei certificati nel server front-end.

## <a name="prerequisite-install-the-azure-cli-20"></a>Prerequisito: installare l'interfaccia della riga di comando di Azure 2.0

Per eseguire i passaggi indicati in questo articolo è necessario [installare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows (interfaccia della riga di comando di Azure)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

## <a name="required-components"></a>Componenti richiesti

* **Pool di server back-end**: elenco di indirizzi IP dei server back-end. Gli indirizzi IP elencati devono appartenere alla subnet della rete virtuale o devono essere indirizzi IP o VIP pubblici.
* **Impostazioni del pool di server back-end**: ogni pool ha impostazioni come porta, protocollo e affinità basata sui cookie. Queste impostazioni sono associate a un pool e vengono applicate a tutti i server nel pool.
* **Porta front-end**: porta pubblica aperta sul gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
* **Listener**: ha una porta front-end, un protocollo (HTTP o HTPPS, queste impostazioni fanno distinzione tra maiuscole e minuscole) e il nome del certificato SSL, se si configura l'offload SSL.
* **Regola**: associa il listener e il pool di server back-end e definisce il pool di server back-end a cui indirizzare il traffico quando raggiunge un listener specifico. È attualmente supportata solo la regola *basic* . La regola *basic* è una distribuzione del carico di tipo round robin.

**Note aggiuntive sulla configurazione**

Per la configurazione dei certificati SSL, il protocollo in **HttpListener** deve essere sostituito con *Https* (distinzione tra maiuscole e minuscole). Aggiungere l'elemento **SslCertificate** ad **HttpListener** con il valore della variabile configurato per il certificato SSL. La porta front-end deve essere aggiornata a **443**.

**Per abilitare l'affinità basata sui cookie**: è possibile configurare un gateway applicazione per fare in modo che una richiesta proveniente da una sessione client sia sempre diretta alla stessa macchina virtuale nella Web farm. A tale scopo, aggiungere un cookie di sessione che consenta al gateway di indirizzare il traffico in modo appropriato. Per abilitare l'affinità basata sui cookie, impostare **CookieBasedAffinity** su *Enabled* nell'elemento **BackendHttpSettings**.

## <a name="configure-ssl-offload-on-an-existing-application-gateway"></a>Configurare l'offload SSL in un gateway applicazione

Immettere i comandi seguenti per configurare l'offload SSL per un gateway applicazione esistente:

```azurecli-interactive
#!/bin/bash

# Create a new front end port to be used for SSL
az network application-gateway frontend-port create \
  --name sslport \
  --port 443 \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Upload the .pfx certificate for SSL offload
az network application-gateway ssl-cert create \
  --name "newcert" \
  --cert-file /home/azureuser/self-signed/AdatumAppGatewayCert.pfx \
  --cert-password P@ssw0rd \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new listener referencing the port and certificate created earlier
az network application-gateway http-listener create \
  --frontend-ip "appGatewayFrontendIP" \
  --frontend-port sslport  \
  --name sslListener \
  --ssl-cert newcert \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new back-end pool to be used
az network application-gateway address-pool create \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG" \
  --name "appGatewayBackendPool2" \
  --servers 10.0.0.7 10.0.0.8

# Create a new back-end HTTP settings using the new probe
az network application-gateway http-settings create \
  --name "settings2" \
  --port 80 \
  --cookie-based-affinity Enabled \
  --protocol "Http" \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new rule linking the listener to the back-end pool
az network application-gateway rule create \
  --name "rule2" \
  --rule-type Basic \
  --http-settings settings2 \
  --http-listener ssllistener \
  --address-pool temp1 \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

```

## <a name="create-an-application-gateway-with-ssl-offload"></a>Creare un gateway applicazione con l'offload SSL

L'esempio seguente crea un gateway applicazione con l'offload SSL. Il certificato e la password del certificato devono essere aggiornati a una chiave privata valida.

```azurecli-interactive
#!/bin/bash

# Creates an application gateway with SSL offload
az network application-gateway create \
  --name "AdatumAppGateway3" \
  --location "eastus" \
  --resource-group "AdatumAppGatewayRG2" \
  --vnet-name "AdatumAppGatewayVNET2" \
  --cert-file /home/azureuser/self-signed/AdatumAppGatewayCert.pfx \
  --cert-password P@ssw0rd \
  --vnet-address-prefix "10.0.0.0/16" \
  --subnet "Appgatewaysubnet" \
  --subnet-address-prefix "10.0.0.0/28" \
  --frontend-port 443 \
  --servers "10.0.0.5 10.0.0.4" \
  --capacity 2 \
  --sku "Standard_Small" \
  --http-settings-cookie-based-affinity "Enabled" \
  --http-settings-protocol "Http" \
  --frontend-port "80" \
  --routing-rule-type "Basic" \
  --http-settings-port "80" \
  --public-ip-address "pip" \
  --public-ip-address-allocation "dynamic"
```

## <a name="get-an-application-gateway-dns-name"></a>Ottenere un nome DNS del gateway applicazione

Dopo avere creato il gateway, il passaggio successivo prevede la configurazione del front-end per la comunicazione.  Il gateway applicazione richiede un nome DNS assegnato in modo dinamico, non descrittivo quando si usa un IP pubblico. Per assicurarsi che gli utenti finali possano raggiungere il gateway applicazione, è possibile usare un record CNAME che faccia riferimento all'endpoint pubblico del gateway applicazione. Per altre informazioni, vedere [Configurare un nome di dominio personalizzato in Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Per configurare un alias, recuperare i dettagli del gateway applicazione e il relativo nome DNS/indirizzo IP usando l'elemento **PublicIPAddress** collegato al gateway applicazione. Usare il nome DNS del gateway applicazione per creare un record CNAME che associ le due applicazioni Web a questo nome DNS. Non è consigliabile usare record A perché l'indirizzo VIP può cambiare al riavvio del gateway applicazione.


```azurecli-interactive
az network public-ip show --name "pip" --resource-group "AdatumAppGatewayRG"
```

```
{
  "dnsSettings": {
    "domainNameLabel": null,
    "fqdn": "8c786058-96d4-4f3e-bb41-660860ceae4c.cloudapp.net",
    "reverseFqdn": null
  },
  "etag": "W/\"3b0ac031-01f0-4860-b572-e3c25e0c57ad\"",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/publicIPAddresses/pip2",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "40.121.167.250",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway2/frontendIPConfigurations/appGatewayFrontendIP",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "AdatumAppGatewayRG",
    "subnet": null
  },
  "location": "eastus",
  "name": "pip2",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "AdatumAppGatewayRG",
  "resourceGuid": "3c30d310-c543-4e9d-9c72-bbacd7fe9b05",
  "tags": {
    "cli[2] owner[administrator]": ""
  },
  "type": "Microsoft.Network/publicIPAddresses"
}
```

## <a name="next-steps"></a>Passaggi successivi

Per configurare un gateway applicazione da usare con un servizio di bilanciamento del carico interno, vedere [Creare un gateway applicazione con un servizio di bilanciamento del carico interno](application-gateway-ilb.md).

Per altre informazioni generali sulle opzioni di bilanciamento del carico, vedere:

* [Servizio di bilanciamento del carico di Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gestione traffico di Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

