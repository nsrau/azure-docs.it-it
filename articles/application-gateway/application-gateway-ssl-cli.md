---
title: Creare un gateway applicazione con la terminazione SSL - Interfaccia della riga di comando di Azure | Documentazione Microsoft
description: Informazioni su come creare un gateway applicazione e aggiungere un certificato per la terminazione SSL con l'interfaccia della riga di comando di Azure.
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/18/2018
ms.author: davidmu
ms.openlocfilehash: c69ab3db9f23b714f7de9244e4e7015ae60a4f6e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="create-an-application-gateway-with-ssl-termination-using-the-azure-cli"></a>Creare un gateway applicazione con la terminazione SSL tramite l'interfaccia della riga di comando di Azure

È possibile usare l'interfaccia della riga di comando di Azure per creare un [gateway applicazione](application-gateway-introduction.md) con un certificato per la [terminazione SSL](application-gateway-backend-ssl.md) che usa un [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) per i server back-end. In questo esempio, il set di scalabilità contiene due istanze di macchine virtuali che vengono aggiunte al pool back-end predefinito del gateway applicazione.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare un certificato autofirmato
> * Configurare una rete
> * Creare un gateway applicazione con il certificato
> * Creare un set di scalabilità di macchine virtuali con il pool back-end predefinito

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato

Per la produzione è necessario importare un certificato valido firmato da un provider attendibile. Per questa esercitazione, creare un certificato autofirmato e un file con estensione pfx tramite il comando openssl.

```azurecli-interactive
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out appgwcert.crt
```

Immettere i valori appropriati per il certificato. È possibile accettare i valori predefiniti.

```azurecli-interactive
openssl pkcs12 -export -out appgwcert.pfx -inkey privateKey.key -in appgwcert.crt
```

Immettere la password per il certificato. In questo esempio, *Azure123456!* è la password usata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Creare un gruppo di risorse usando [az group create](/cli/azure/group#create).

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupAG* nella posizione *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Creare risorse di rete

Creare la rete virtuale denominata *myVNet* e la subnet denominata *myAGSubnet* usando [az network vnet create](/cli/azure/network/vnet#az_net). È quindi possibile aggiungere la subnet denominata *myBackendSubnet* necessaria per i server back-end tramite [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Creare l'indirizzo IP pubblico denominato *myAGPublicIPAddress* tramite [az network public-ip create](/cli/azure/public-ip#az_network_public_ip_create).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway"></a>Creare il gateway applicazione

È possibile usare [az network application-gateway create](/cli/azure/application-gateway#create) per creare il gateway applicazione. Quando si crea un gateway applicazione usando l'interfaccia della riga di comando di Azure, specificare le informazioni di configurazione, ad esempio le impostazioni relative a capacità, SKU e HTTP. 

Il gateway applicazione viene assegnato alla subnet *myAGSubnet* e all'indirizzo IP pubblico *myAGPublicIPAddress* creati in precedenza. In questo esempio, associare il certificato creato e la relativa password quando si crea il gateway applicazione. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 443 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress \
  --cert-file appgwcert.pfx \
  --cert-password "Azure123456!"

```

 Il processo di creazione del gateway applicazione può richiedere alcuni minuti. Dopo aver creato il gateway applicazione, saranno disponibili le nuove funzionalità seguenti:

- *appGatewayBackendPool*: un gateway applicazione deve avere almeno un pool di indirizzi back-end.
- *appGatewayBackendHttpSettings*: specifica che per le comunicazioni vengono usati la porta 80 e il protocollo HTTP.
- *appGatewayHttpListener*: il listener predefinito associato ad *appGatewayBackendPool*.
- *appGatewayFrontendIP*: assegna *myAGPublicIPAddress* ad *appGatewayHttpListener*.
- *rule1*: la regola di routing predefinita associata ad *appGatewayHttpListener*.

## <a name="create-a-virtual-machine-scale-set"></a>Creare un set di scalabilità di macchine virtuali

In questo esempio viene creato un set di scalabilità di macchine virtuali che fornisce server per il pool back-end predefinito nel gateway applicazione. Le macchine virtuali nel set di scalabilità sono associate a *myBackendSubnet* e *appGatewayBackendPool*. Per creare il set di scalabilità, è possibile usare [az vmss create](/cli/azure/vmss#az_vmss_create).

```azurecli-interactive
az vmss create \
  --name myvmss \
  --resource-group myResourceGroupAG \
  --image UbuntuLTS \
  --admin-username azureuser \
  --admin-password Azure123456! \
  --instance-count 2 \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --vm-sku Standard_DS2 \
  --upgrade-policy-mode Automatic \
  --app-gateway myAppGateway \
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>Installare NGINX

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

Per ottenere l'indirizzo IP pubblico del gateway applicazione, è possibile usare [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Avviso di sicurezza](./media/application-gateway-ssl-cli/application-gateway-secure.png)

Per accettare l'avviso di sicurezza se si è usato un certificato autofirmato, selezionare **Dettagli** e quindi **Continua per la pagina Web**. Il sito NGINX protetto viene quindi visualizzato come illustrato nell'esempio seguente:

![Testare l'URL di base nel gateway applicazione](./media/application-gateway-ssl-cli/application-gateway-nginx.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un certificato autofirmato
> * Configurare una rete
> * Creare un gateway applicazione con il certificato
> * Creare un set di scalabilità di macchine virtuali con il pool back-end predefinito

Per altre informazioni sui gateway applicazione e sulle risorse associate, continuare con le procedure dettagliate.
