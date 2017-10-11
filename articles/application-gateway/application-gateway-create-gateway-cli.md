---
title: Creare un gateway applicazione - Interfaccia della riga di comando di Azure 2.0 | Microsoft Docs
description: Informazioni su come creare un gateway applicazione usando l'interfaccia della riga di comando di Azure 2.0 in Azure Resource Manager.
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 9429fafa73392c3ab5d6af844453ba97f7d4f65b
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---
# <a name="create-an-application-gateway-by-using-the-azure-cli-20"></a>Creare un gateway applicazione con l'interfaccia della riga di comando di Azure 2.0

> [!div class="op_single_selector"]
> * [Portale di Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell per Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [PowerShell classico per Azure](application-gateway-create-gateway.md)
> * [Modello di Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [Interfaccia della riga di comando di Azure 1.0](application-gateway-create-gateway-cli.md)
> * [Interfaccia della riga di comando di Azure 2.0](application-gateway-create-gateway-cli.md)

Il gateway applicazione è un'appliance virtuale dedicata che offre un servizio di controller per la distribuzione di applicazioni (ADC, Application Delivery Controller) e varie funzionalità di bilanciamento del carico di livello 7 per l'applicazione.

## <a name="cli-versions"></a>Versioni dell'interfaccia della riga di comando

È possibile creare un gateway applicazione usando una delle versioni seguenti dell'interfaccia della riga di comando:

* [Interfaccia della riga di comando di Azure 1.0](application-gateway-create-gateway-cli-nodejs.md): l'interfaccia della riga di comando per i modelli di distribuzione classici e di Azure Resource Manager.
* [Interfaccia della riga di comando di Azure 2.0](application-gateway-create-gateway-cli.md): interfaccia della riga di comando di nuova generazione per il modello di distribuzione di Azure Resource Manager.

## <a name="prerequisite-install-the-azure-cli-20"></a>Prerequisito: installare l'interfaccia della riga di comando di Azure 2.0

Per eseguire i passaggi indicati in questo articolo è necessario [installare l'interfaccia della riga di comando di Azure per MacOS, Linux e Windows](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

> [!NOTE]
> Per creare un gateway applicazione è necessario disporre di un account di Azure. Nel caso in cui non siano disponibili, è possibile usare una [versione di valutazione gratuita](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Scenario

Questo scenario mostra come creare un gateway applicazione usando il portale di Azure.

Questo scenario illustrerà come:

* Creare un gateway applicazione Medium con due istanze.
* Creare una rete virtuale denominata AdatumAppGatewayVNET con un blocco CIDR riservato 10.0.0.0/16.
* Creare una subnet denominata Appgatewaysubnet che usa 10.0.0.0/28 come blocco CIDR.

> [!NOTE]
> La configurazione aggiuntiva del gateway applicazione, che include i probe di integrità personalizzati, gli indirizzi del pool back-end e le regole aggiuntive, viene definita dopo la configurazione del gateway applicazione e non durante la distribuzione iniziale.

## <a name="before-you-begin"></a>Prima di iniziare

Un gateway applicazione richiede una propria subnet. Quando si crea una rete virtuale, assicurarsi di lasciare uno spazio indirizzi sufficiente per più subnet. Dopo aver distribuito un gateway applicazione in una subnet, alla subnet possono essere aggiunti solo altri gateway applicazione.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Aprire il **prompt dei comandi di Microsoft Azure**ed eseguire l'accesso.

```azurecli-interactive
az login -u "username"
```

> [!NOTE]
> È anche possibile usare `az login` senza l'opzione per l'accesso del dispositivo che richiede l'immissione di un codice in aka.ms/devicelogin.

Dopo aver immesso il comando precedente, viene restituito un codice. Passare a https://aka.ms/devicelogin in un browser per continuare la procedura di accesso.

![Comando che illustra l'accesso al dispositivo][1]

Nel browser immettere il codice ricevuto. Questo reindirizza l'utente a una pagina di accesso.

![Browser in cui immettere il codice][2]

Immettere il codice per accedere e quindi chiudere il browser per continuare.

![Accesso eseguito][3]

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse.

Prima di creare il gateway applicazione, creare un gruppo di risorse per contenerlo. Usare il comando seguente:

```azurecli-interactive
az group create --name myresourcegroup --location "eastus"
```

## <a name="create-the-application-gateway"></a>Creare il gateway applicazione

Usare gli indirizzi IP back-end per gli indirizzi IP del server back-end. Questi valori possono essere indirizzi IP privati nella rete virtuale, indirizzi IP pubblici o nomi di dominio completi per i server back-end. L'esempio seguente crea un gateway applicazione con configurazioni aggiuntive per le impostazioni HTTP, le porte e le regole.

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet" \
--subnet-address-prefix "10.0.0.0/28" \
--servers 10.0.0.4 10.0.0.5 \
--capacity 2 \
--sku Standard_Small \
--http-settings-cookie-based-affinity Enabled \
--http-settings-protocol Http \
--frontend-port 80 \
--routing-rule-type Basic \
--http-settings-port 80 \
--public-ip-address "pip2" \
--public-ip-address-allocation "dynamic" \

```

L'esempio precedente mostra numerose proprietà non necessarie durante la creazione di un gateway applicazione. L'esempio di codice seguente crea un gateway applicazione con le informazioni necessarie.

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet \
--subnet-address-prefix "10.0.0.0/28" \
--servers "10.0.0.5"  \
--public-ip-address pip
```
 
> [!NOTE]
> Per un elenco dei parametri che possono essere usati durante la creazione, eseguire questo comando: `az network application-gateway create --help`.

Questo esempio crea un gateway applicazione di base con le impostazioni predefinite per il listener, il pool back-end, le impostazioni HTTP back-end e le regole. Queste impostazioni possono essere modificate in base alla propria distribuzione dopo che è stato completato il provisioning.

Se nei passaggi precedenti l'applicazione Web è stata definita con il pool back-end, il bilanciamento del carico inizia ora.

## <a name="get-the-application-gateway-dns-name"></a>Ottenere il nome DNS del gateway applicazione
Dopo avere creato il gateway, viene configurata la comunicazione front-end. Quando si usa un IP pubblico, il gateway applicazione richiede un nome DNS assegnato in modo dinamico, non descrittivo. Per assicurarsi che gli utenti possano raggiungere il gateway applicazione, usare un record CNAME che faccia riferimento all'endpoint pubblico del gateway applicazione. Per altre informazioni, vedere [Usare il servizio DNS di Azure per specificare impostazioni di dominio personalizzate per un servizio di Azure](../dns/dns-custom-domain.md).

Per configurare un alias, recuperare i dettagli del gateway applicazione e il relativo nome DNS/indirizzo IP usando l'elemento PublicIPAddress collegato al gateway applicazione. Usare il nome DNS del gateway applicazione per creare un record CNAME che associ le due applicazioni Web a questo nome DNS. Non è consigliabile usare record A perché l'indirizzo VIP può cambiare al riavvio del gateway applicazione.


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

## <a name="delete-all-resources"></a>Eliminare tutte le risorse

Per eliminare tutte le risorse create in questo articolo, eseguire il comando seguente:

```azurecli-interactive
az group delete --name AdatumAppGatewayRG
```
 
## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come creare un probe personalizzato, vedere [Creare un probe personalizzato per il gateway applicazione con il portale](application-gateway-create-probe-portal.md).

Per apprendere come configurare l'offload SSL ed evitare così la costosa decrittografia SSL sui server Web, vedere [Configurare un gateway applicazione per l'offload SSL con Azure Resource Manager](application-gateway-ssl-arm.md).

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png

