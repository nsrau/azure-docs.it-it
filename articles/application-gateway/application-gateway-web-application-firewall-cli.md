---
title: 'Configurare un web application firewall: gateway applicazione di Azure | Microsoft Docs'
description: Questo articolo fornisce indicazioni su come iniziare a usare il firewall applicazione Web in un gateway applicazione nuovo o esistente.
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 999d1ad3ee54d84e155254655dbb7a39ac60572c
ms.contentlocale: it-it
ms.lasthandoff: 08/29/2017

---
# <a name="configure-a-web-application-firewall-on-a-new-or-existing-application-gateway-with-azure-cli"></a>Configurare un web application firewall in un gateway applicazione nuovo o esistente con l'interfaccia della riga di comando di Azure

> [!div class="op_single_selector"]
> * [Portale di Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Interfaccia della riga di comando di Azure](application-gateway-web-application-firewall-cli.md)

Informazioni su come creare un gateway applicazione con web application firewall (WAF) abilitato. Leggere anche le informazioni su come aggiungere un WAF a un gateway applicazione esistente.

Il WAF nel gateway applicazione di Azure protegge le applicazioni Web dai comuni attacchi basati sul Web, come ad esempio gli attacchi SQL injection, gli attacchi di scripting intersito e il controllo delle sessioni.

 Il gateway applicazione è un servizio di bilanciamento del carico di livello 7. Fornisce richieste HTTP con routing delle prestazioni e failover tra server diversi, sia nel cloud che in locale. Il gateway applicazione offre numerose funzionalità di controller per la distribuzione di applicazioni (ADC, Application Delivery Controller):

 * Bilanciamento del carico HTTP 
 * Affinità di sessione basata su cookie 
 * Offload Secure Sockets Layer (SSL) 
 * Probe di integrità personalizzati 
 * Supporto per la funzionalità multisito
 
 Per un elenco completo delle funzionalità supportate, vedere [Panoramica del gateway applicazione](application-gateway-introduction.md).

Questo articolo mostra come [aggiungere un firewall applicazione Web a un gateway applicazione esistente](#add-web-application-firewall-to-an-existing-application-gateway). Illustra anche come [creare un gateway applicazione con il firewall applicazione Web](#create-an-application-gateway-with-web-application-firewall).

![Immagine dello scenario][scenario]

## <a name="prerequisite-install-the-azure-cli-20"></a>Prerequisito: installare l'interfaccia della riga di comando di Azure 2.0

Per eseguire i passaggi indicati in questo articolo è necessario [installare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

## <a name="waf-configuration-differences"></a>Differenze di configurazione dei WAF

Se è stato letto l'argomento [Creare un gateway applicazione con l'interfaccia della riga di comando di Azure ](application-gateway-create-gateway-cli.md), si conoscono le impostazioni della SKU da configurare quando si crea un gateway applicazione. Il WAF fornisce impostazioni aggiuntive da definire quando si configura lo SKU in un gateway applicazione. Non esistono altre modifiche aggiuntive da apportare sul gateway applicazione.

| **Impostazione** | **Dettagli**
|---|---|
|**SKU** |Un gateway applicazione normale senza WAF supporta le dimensioni **Standard\_Small**, **Standard\_Medium** e **Standard\_Large**. Con l'introduzione di un WAF sono disponibili due SKU aggiuntive, **WAF\_Medium** e **WAF\_Large**. Un WAF non è supportato nei gateway applicazione Small.|
|**Modalità** | Questa impostazione è la modalità del WAF. I valori consentiti sono **Rilevamento** e **Prevenzione**. Quando il firewall WAF è configurato in modalità **Rilevamento**, tutte le minacce vengono archiviate in un file di log. In modalità **Prevenzione**, gli eventi vengono comunque registrati, ma l'autore dell'attacco riceve una risposta di mancata autorizzazione 403 dal gateway applicazione.|

## <a name="add-a-web-application-firewall-to-an-existing-application-gateway"></a>Aggiungere un web application firewall a un gateway applicazione esistente

Il comando seguente modifica un gateway applicazione standard esistente in un gateway applicazione abilitato per WAF:

```azurecli-interactive
#!/bin/bash

az network application-gateway waf-config set \
  --enabled true \
  --firewall-mode Prevention \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"
```

Questo comando aggiorna il gateway applicazione con un WAF. Per informazioni su come visualizzare i log per il gateway applicazione, vedere l'argomento relativo alla [diagnostica del gateway applicazione](application-gateway-diagnostics.md). Per le implicazioni di sicurezza del WAF, esaminare i log a intervalli regolari per essere aggiornati sulle condizioni di sicurezza delle applicazioni Web.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Creare un gateway applicazione con un web application firewall

Il comando seguente crea un gateway applicazione con un WAF:

```azurecli-interactive
#!/bin/bash

az network application-gateway create \
  --name "AdatumAppGateway2" \
  --location "eastus" \
  --resource-group "AdatumAppGatewayRG" \
  --vnet-name "AdatumAppGatewayVNET2" \
  --vnet-address-prefix "10.0.0.0/16" \
  --subnet "Appgatewaysubnet2" \
  --subnet-address-prefix "10.0.0.0/28" \
 --servers "10.0.0.5 10.0.0.4" \
  --capacity 2 
  --sku "WAF_Medium" \
  --http-settings-cookie-based-affinity "Enabled" \
  --http-settings-protocol "Http" \
  --frontend-port "80" \
  --routing-rule-type "Basic" \
  --http-settings-port "80" \
  --public-ip-address "pip2" \
  --public-ip-address-allocation "dynamic" \
  --tags "cli[2] owner[administrator]"
```

> [!NOTE]
> I gateway applicazione creati con la configurazione di base del WAF sono configurati con CRS 3.0 per la protezione.

## <a name="get-an-application-gateway-dns-name"></a>Ottenere un nome DNS del gateway applicazione

Dopo avere creato il gateway, il passaggio successivo prevede la configurazione del front-end per la comunicazione. Quando si usa un IP pubblico, il gateway applicazione richiede un nome DNS assegnato in modo dinamico, non descrittivo. Per assicurarsi che gli utenti possano raggiungere il gateway applicazione, usare un record CNAME che faccia riferimento all'endpoint pubblico del gateway applicazione. Per altre informazioni, vedere [Configurare un nome di dominio personalizzato per un servizio cloud di Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Per configurare un record CNAME, recuperare i dettagli del gateway applicazione e il relativo nome DNS/indirizzo IP usando l'elemento PublicIPAddress collegato al gateway applicazione. Usare il nome DNS del gateway applicazione per creare un record CNAME che associ le due applicazioni Web a questo nome DNS. Non è consigliabile usare record A perché l'indirizzo VIP può cambiare al riavvio del gateway applicazione.

```azurecli-interactive
#!/bin/bash

az network public-ip show \
  --name pip2 \
  --resource-group "AdatumAppGatewayRG"
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

Per informazioni su come personalizzare le regole del WAF, vedere [Customize web application firewall rules through the Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md) (Personalizzare le regole del web application firewall con l'interfaccia della riga di comando di Azure 2.0).

[scenario]: ./media/application-gateway-web-application-firewall-cli/scenario.png

