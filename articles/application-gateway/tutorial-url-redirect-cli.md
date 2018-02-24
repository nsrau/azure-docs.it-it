---
title: Creare un gateway applicazione con reindirizzamento basato su percorsi URL - Interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come creare un gateway applicazione con traffico reindirizzato in base ai percorsi URL usando l'interfaccia della riga di comando di Azure.
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: davidmu
ms.openlocfilehash: 73fc20cf738f584008e7d8a019b8f7012dcacab1
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-application-gateway-with-url-path-based-redirection-using-the-azure-cli"></a>Creare un gateway applicazione con reindirizzamento basato su percorsi URL usando l'interfaccia della riga di comando di Azure

È possibile usare l'interfaccia della riga di comando di Azure per configurare [regole di routing basato su percorsi URL](application-gateway-url-route-overview.md) quando si crea un [gateway applicazione](application-gateway-introduction.md). In questa esercitazione si creano pool back-end usando i [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Si creano quindi le regole di routing degli URL per garantire che il traffico Web venga reindirizzato nel pool back-end appropriato.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Configurare la rete
> * Creare un gateway applicazione
> * Aggiungere i listener e le regole di routing
> * Creare set di scalabilità di macchine virtuali per i pool back-end

L'esempio seguente illustra il traffico del sito in arrivo dalle porte 8080 e 8081 e indirizzato agli stessi pool back-end:

![Esempio di routing basato su URL](./media/tutorial-url-redirect-cli/scenario.png)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Creare un gruppo di risorse usando [az group create](/cli/azure/group#create).

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupAG* nella località *eastus*.

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

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

È possibile usare [az network application-gateway create](/cli/azure/application-gateway#create) per creare il gateway applicazione denominato myAppGateway. Quando si crea un gateway applicazione usando l'interfaccia della riga di comando di Azure, specificare le informazioni di configurazione, ad esempio le impostazioni relative a capacità, SKU e HTTP. Il gateway applicazione viene assegnato alla subnet *myAGSubnet* e all'indirizzo IP pubblico *myPublicIPSddress* creati in precedenza.  

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

 Il processo di creazione del gateway applicazione può richiedere alcuni minuti. Dopo aver creato il gateway applicazione, saranno disponibili le nuove funzionalità seguenti:

- *appGatewayBackendPool*: un gateway applicazione deve avere almeno un pool di indirizzi back-end.
- *appGatewayBackendHttpSettings*: specifica che per le comunicazioni vengono usati la porta 80 e il protocollo HTTP.
- *appGatewayHttpListener*: il listener predefinito associato ad *appGatewayBackendPool*.
- *appGatewayFrontendIP*: assegna *myAGPublicIPAddress* ad *appGatewayHttpListener*.
- *rule1*: regola di routing predefinita associata ad *appGatewayHttpListener*.


### <a name="add-backend-pools-and-ports"></a>Aggiunta di pool back-end e porte

È possibile aggiungere i pool back-end denominati *imagesBackendPool* e *videoBackendPool* al gateway applicazione usando [az network application-gateway address-pool create](/cli/azure/application-gateway#az_network_application_gateway_address-pool_create). Aggiungere le porte front-end per i pool usando [az network application-gateway frontend-port create](/cli/azure/application-gateway#az_network_application_gateway_frontend_port_create). 

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
  --name bport
az network application-gateway frontend-port create \
  --port 8081 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name rport
```

## <a name="add-listeners-and-rules"></a>Aggiunta di listener e regole

### <a name="add-listeners"></a>Aggiunta di listener

Aggiungere i listener back-end denominati *backendListener* e *redirectedListener* necessari per instradare il traffico usando [az network application-gateway http-listener create](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create).


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port bport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
az network application-gateway http-listener create \
  --name redirectedListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port rport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-default-url-path-map"></a>Aggiunta della mappa del percorso URL predefinito

Le mappe di percorso URL assicurano che specifici URL vengano instradati a specifici pool back-end. È possibile creare mappe di percorso URL denominate *imagePathRule* e *videoPathRule* tramite [az network application-gateway url-path-map create](/cli/azure/application-gateway#az_network_application_gateway_url_path_map_create) e [az network application-gateway url-path-map rule create](/cli/azure/application-gateway#az_network_application_gateway_url_path_map_rule_create)

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name urlpathmap \
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
  --path-map-name urlpathmap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-redirection-configuration"></a>Aggiunta della configurazione di reindirizzamento

È possibile configurare il reindirizzamento per il listener usando [az network application-gateway redirect-config create](/cli/azure/application-gateway#az_network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --gateway-name myAppGateway \
  --name redirectConfig \
  --resource-group myResourceGroupAG \
  --type Found \
  --include-path true \
  --include-query-string true \
  --target-listener backendListener
```

### <a name="add-the-redirection-url-path-map"></a>Aggiunta della mappa dei percorsi URL di reindirizzamento

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name redirectpathmap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --redirect-config redirectConfig \
  --rule-name redirectPathRule
```

### <a name="add-routing-rules"></a>Aggiunta delle regole di routing

Le regole di routing associano le mappe dei percorsi URL ai listener creati. È possibile aggiungere le regole denominate *defaultRule* e *redirectedRule* usando [az network application-gateway rule create](/cli/azure/application-gateway#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name defaultRule \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map urlpathmap \
  --address-pool appGatewayBackendPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectedRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectedListener \
  --rule-type PathBasedRouting \
  --url-path-map redirectpathmap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Creare set di scalabilità di macchine virtuali

In questo esempio si creano tre set di scalabilità di macchine virtuali che supportano i tre pool back-end creati. I set di scalabilità creati sono denominati *myvmss1*, *myvmss2* e *myvmss3*. Ogni set di scalabilità contiene due istanze di macchina virtuale in cui si installa NGINX.

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
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

Per ottenere l'indirizzo IP pubblico del gateway applicazione, è possibile usare [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser. Ad esempio, *http://40.121.222.19*, *http://40.121.222.19:8080/images/test.htm*, *http://40.121.222.19:8080/video/test.htm*, oppure *http://40.121.222.19:8081/images/test.htm*.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testare l'URL di base nel gateway applicazione](./media/tutorial-url-redirect-cli/application-gateway-nginx.png)

Modificare l'URL in http://&lt;ip-address&gt;:8080/video/test.html sostituendo &lt;ip-address&gt; con l'indirizzo IP usato come nell'esempio seguente:

![Testare l'URL delle immagini nel gateway applicazione](./media/tutorial-url-redirect-cli/application-gateway-nginx-images.png)

Modificare l'URL in http://&lt;ip-address&gt;:8080/video/test.html sostituendo &lt;ip-address&gt; con l'indirizzo IP usato come nell'esempio seguente:

![Testare l'URL video nel gateway applicazione](./media/tutorial-url-redirect-cli/application-gateway-nginx-video.png)

A questo punto modificare l'URL in http://&lt;ip-address&gt;:8081/images/test.htm e sostituendo &lt;ip-address&gt; con l'indirizzo IP usato dovrebbe essere possibile visualizzare il traffico reindirizzato al pool back-end delle immagini in http://&lt;ip-address&gt;:8080/images.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Configurare la rete
> * Creare un gateway applicazione
> * Aggiungere i listener e le regole di routing
> * Creare set di scalabilità di macchine virtuali per i pool back-end

> [!div class="nextstepaction"]
> [Altre informazioni sulle operazioni che è possibile eseguire con il gateway applicazione](application-gateway-introduction.md)