---
title: "Esercitazione: Instradare il traffico Web in base all'URL - Interfaccia della riga di comando di Azure"
description: Informazioni su come instradare il traffico Web in base all'URL a pool di server scalabili specifici tramite l'interfaccia della riga di comando di Azure.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 7/14/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 2402dd2adb27abb784c34a2a3569e8f6e4a60f77
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954790"
---
# <a name="tutorial-route-web-traffic-based-on-the-url-using-the-azure-cli"></a>Esercitazione: Instradare il traffico Web in base all'URL tramite l'interfaccia della riga di comando di Azure

È possibile usare l'interfaccia della riga di comando di Azure per configurare il routing del traffico Web a pool di server scalabili specifici in base all'URL usato per accedere all'applicazione. In questa esercitazione si creerà un [gateway applicazione di Azure](application-gateway-introduction.md) con tre pool back-end usando [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Ognuno dei pool back-end viene usato per uno scopo specifico, ad esempio dati comuni, immagini e video.  Il routing del traffico a pool separati permette ai clienti di ottenere le informazioni necessarie in qualsiasi momento.

Per abilitare il routing del traffico, è necessario creare [regole di routing](application-gateway-url-route-overview.md) da assegnare ai listener che sono in ascolto su porte specifiche per garantire che il traffico Web arrivi ai server appropriati nei pool.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare la rete
> * Creare i listener, il mapping dei percorsi URL e le regole
> * Creare i pool back-end scalabili


![Esempio di routing basato su URL](./media/tutorial-url-route-cli/scenario.png)

Se si preferisce, è possibile completare questa esercitazione usando [Azure PowerShell](tutorial-url-route-powershell.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successive. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui vengono distribuite e gestite le risorse di Azure. Creare un gruppo di risorse usando [az group create](/cli/azure/group#create).

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupAG* nella località *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Creare risorse di rete 

Creare la rete virtuale denominata *myVNet* e la subnet denominata *myAGSubnet* usando [az network vnet create](/cli/azure/network/vnet#az-net). Aggiungere quindi una subnet denominata *myBackendSubnet*, necessaria per i server back-end, tramite [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network_vnet_subnet_create). Creare l'indirizzo IP pubblico denominato *myAGPublicIPAddress* tramite [az network public-ip create](/cli/azure/network/public-ip#az-network_public_ip_create).

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

## <a name="create-the-application-gateway-with-url-map"></a>Creare il gateway applicazione con il mapping di URL

Usare [az network application-gateway create](/cli/azure/network/application-gateway#create) per creare un gateway applicazione denominato *myAppGateway*. Quando si crea un gateway applicazione usando l'interfaccia della riga di comando di Azure, specificare le informazioni di configurazione, ad esempio le impostazioni relative a capacità, SKU e HTTP. Il gateway applicazione viene assegnato alla subnet *myAGSubnet* e all'indirizzo IP pubblico *myAGPublicIPAddress* creati in precedenza. 

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
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 La creazione del gateway applicazione può richiedere diversi minuti. Dopo aver creato il gateway applicazione, saranno disponibili le nuove funzionalità seguenti:

- *appGatewayBackendPool*: un gateway applicazione deve avere almeno un pool di indirizzi back-end.
- *appGatewayBackendHttpSettings*: specifica che per le comunicazioni vengono usati la porta 80 e il protocollo HTTP.
- *appGatewayHttpListener*: il listener predefinito associato ad *appGatewayBackendPool*.
- *appGatewayFrontendIP*: assegna *myAGPublicIPAddress* ad *appGatewayHttpListener*.
- *rule1*: la regola di routing predefinita associata ad *appGatewayHttpListener*.


### <a name="add-image-and-video-backend-pools-and-port"></a>Aggiungere i pool back-end di immagini e video e la porta

Aggiungere due pool back-end denominati *imagesBackendPool* e *videoBackendPool* al gateway applicazione usando [az network application-gateway address-pool create](/cli/azure/network/application-gateway#az-network_application_gateway_address-pool_create). Aggiungere la porta front-end per i pool usando [az network application-gateway frontend-port create](/cli/azure/network/application-gateway#az-network_application_gateway_frontend_port_create). 

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name imagesBackendPool

az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name videoBackendPool

az network application-gateway frontend-port create \
  --port 8080 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name port8080
```

### <a name="add-backend-listener"></a>Aggiungere il listener back-end

Aggiungere il listener back-end denominato *backendListener* necessario per instradare il traffico usando [az network application-gateway http-listener create](/cli/azure/network/application-gateway#az-network_application_gateway_http_listener_create).


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-url-path-map"></a>Aggiungere la mappa di percorso URL

I mapping dei percorsi URL garantiscono che URL specifici vengano instradati a determinati pool back-end. Creare due mapping dei percorsi URL denominati *imagePathRule* e *videoPathRule* usando [az network application-gateway url-path-map create](/cli/azure/network/application-gateway#az-network_application_gateway_url_path_map_create) e [az network application-gateway url-path-map rule create](/cli/azure/network/application-gateway#az-network_application_gateway_url_path_map_rule_create)

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name myPathMap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --address-pool imagesBackendPool \
  --default-address-pool appGatewayBackendPool \
  --default-http-settings appGatewayBackendHttpSettings \
  --http-settings appGatewayBackendHttpSettings \
  --rule-name imagePathRule

az network application-gateway url-path-map rule create \
  --gateway-name myAppGateway \
  --name videoPathRule \
  --resource-group myResourceGroupAG \
  --path-map-name myPathMap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-routing-rule"></a>Aggiungere la regola di routing

La regola di routing associa le mappe URL al listener creato. Aggiungere una regola denominata *rule2* usando [az network application-gateway rule create](/cli/azure/network/application-gateway#az-network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map myPathMap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Creare set di scalabilità di macchine virtuali

In questa esercitazione si creeranno tre set di scalabilità di macchine virtuali che supportano i tre pool back-end creati. È necessario creare tre set di scalabilità denominati *myvmss1*, *myvmss2* e *myvmss3*. Ogni set di scalabilità contiene due istanze di macchina virtuale in cui è necessario installare NGINX.

```azurecli-interactive
for i in `seq 1 3`; do

  if [ $i -eq 1 ]
  then
    poolName="appGatewayBackendPool" 
  fi

  if [ $i -eq 2 ]
  then
    poolName="imagesBackendPool"
  fi

  if [ $i -eq 3 ]
  then
    poolName="videoBackendPool"
  fi

  az vmss create \
    --name myvmss$i \
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
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>Installare NGINX

```azurecli-interactive
for i in `seq 1 3`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

Per ottenere l'indirizzo IP pubblico del gateway applicazione, usare [az network public-ip show](/cli/azure/network/public-ip#az-network_public_ip_show). Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser. Ad esempio, *http://40.121.222.19*, *http://40.121.222.19:8080/images/test.htm* oppure *http://40.121.222.19:8080/video/test.htm*.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testare l'URL di base nel gateway applicazione](./media/tutorial-url-route-cli/application-gateway-nginx.png)

Modificare l'URL in http://&lt;indirizzo-ip&gt;:8080/video/test.htm, sostituendo &lt;indirizzo-ip&gt; con l'indirizzo IP desiderato per ottenere qualcosa di simile all'esempio seguente:

![Testare l'URL delle immagini nel gateway applicazione](./media/tutorial-url-route-cli/application-gateway-nginx-images.png)

Modificare l'URL in http://&lt;ip-address&gt;:8080/video/test.html sostituendo &lt;ip-address&gt; con l'indirizzo IP usato come nell'esempio seguente:

![Testare l'URL video nel gateway applicazione](./media/tutorial-url-route-cli/application-gateway-nginx-video.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, rimuovere il gruppo di risorse, il gateway applicazione e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Configurare la rete
> * Creare i listener, il mapping dei percorsi URL e le regole
> * Creare i pool back-end scalabili

> [!div class="nextstepaction"]
> [Creare un gateway applicazione con reindirizzamento basato su percorsi URL](./tutorial-url-redirect-cli.md)
