---
title: Instradare il traffico Web in base all'URL - Interfaccia della riga di comando di Azure
description: In questo articolo viene descritto come instradare il traffico Web, in base all'URL, a pool di server scalabili specifici tramite l'interfaccia della riga di comando di Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/01/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: b6bc0b00579bdef0a358f756b8cf2b6034aca017
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688186"
---
# <a name="route-web-traffic-based-on-the-url-using-the-azure-cli"></a>Instradare il traffico Web in base all'URL tramite l'interfaccia della riga di comando di Azure

Come amministratore IT che gestisce il traffico Web, si vuole aiutare i clienti o gli utenti a ottenere le informazioni loro necessarie nel minor tempo possibile. Un modo per ottimizzare la loro esperienza è quello di indirizzare tipi diversi di traffico Web a risorse server diverse. Questo articolo illustra come usare l'interfaccia della riga di comando di Azure per configurare il routing del gateway applicazione per i diversi tipi di traffico generati dall'applicazione. Il routing indirizza quindi il traffico a pool di server diversi in base all'URL.

![Esempio di routing basato su URL](./media/tutorial-url-route-cli/scenario.png)

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare un gruppo di risorse per le risorse di rete necessarie
> * Creare le risorse di rete
> * Creare un gateway applicazione per il traffico proveniente dall'applicazione
> * Specificare pool di server e regole di routing per i diversi tipi di traffico
> * Creare un set di scalabilità per ogni pool in modo da consentirne la scalabilità automatica
> * Eseguire un test per verificare che i diversi tipi di traffico vengano indirizzati al pool corretto

Se si preferisce, è possibile completare questa procedura usando [Azure PowerShell](tutorial-url-route-powershell.md) oppure il [portale di Azure](create-url-route-portal.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successive. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui vengono distribuite e gestite le risorse di Azure. Per creare un gruppo di risorse, usare il comando `az group create`.

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupAG* nella località *eastus*.

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Creare risorse di rete

Creare la rete virtuale denominata *myVNet* e la subnet denominata *myAGSubnet* usando il comando `az network vnet create`. Aggiungere quindi una subnet denominata *myBackendSubnet*, necessaria per i server back-end, usando il comando `az network vnet subnet create`. Creare l'indirizzo IP pubblico denominato *myAGPublicIPAddress* usando il comando `az network public-ip create`.

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
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-the-app-gateway-with-a-url-map"></a>Creare il gateway applicazione con un mapping di URL

Usare il comando `az network application-gateway create` per creare un gateway applicazione denominato *myAppGateway*. Quando si crea un gateway applicazione usando l'interfaccia della riga di comando di Azure, specificare le informazioni di configurazione, ad esempio le impostazioni relative a capacità, SKU e HTTP. Il gateway applicazione viene assegnato a *myAGSubnet* e *myAGPublicIPAddress*.

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 La creazione del gateway applicazione può richiedere diversi minuti. Dopo aver creato il gateway applicazione, saranno disponibili le nuove funzionalità seguenti:


|Funzionalità  |DESCRIZIONE  |
|---------|---------|
|appGatewayBackendPool     |Un gateway applicazione deve avere almeno un pool di indirizzi back-end.|
|appGatewayBackendHttpSettings     |Specifica che per le comunicazioni vengono usati la porta 80 e il protocollo HTTP.|
|appGatewayHttpListener     |Il listener predefinito associato ad appGatewayBackendPool|
|appGatewayFrontendIP     |Assegna myAGPublicIPAddress ad appGatewayHttpListener.|
|rule1     |Regola di routing predefinita associata ad appGatewayHttpListener.|

### <a name="add-image-and-video-backend-pools-and-a-port"></a>Aggiungere pool back-end di immagini e video e una porta

Aggiungere i pool back-end denominati *imagesBackendPool* e *videoBackendPool* al gateway applicazione usando il comando `az network application-gateway address-pool create`. Per aggiungere la porta front-end per i pool usare il comando `az network application-gateway frontend-port create`.

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

### <a name="add-a-backend-listener"></a>Aggiungere un listener back-end

Aggiungere il listener back-end denominato *backendListener* necessario per instradare il traffico usando il comando `az network application-gateway http-listener create`.


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-a-url-path-map"></a>Aggiungere un mapping del percorso URL

I mapping dei percorsi URL garantiscono che URL specifici vengano instradati a determinati pool back-end. Creare i mapping dei percorsi URL denominati *imagePathRule* e *videoPathRule* usando il comando `az network application-gateway url-path-map create` e il comando `az network application-gateway url-path-map rule create`.

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

### <a name="add-a-routing-rule"></a>Aggiungi una regola di routing

La regola di routing associa le mappe URL al listener creato. Aggiungere una regola denominata *rule2* usando il comando `az network application-gateway rule create`.

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

In questo articolo si creeranno tre set di scalabilità di macchine virtuali che supportano i tre pool back-end creati. È necessario creare tre set di scalabilità denominati *myvmss1*, *myvmss2* e *myvmss3*. Ogni set di scalabilità contiene due istanze di macchina virtuale in cui è necessario installare NGINX.

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

Per ottenere l'indirizzo IP pubblico del gateway applicazione, usare il comando az network public-ip show. Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser. Ad esempio `http://40.121.222.19`, `http://40.121.222.19:8080/images/test.htm` o `http://40.121.222.19:8080/video/test.htm`.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testare l'URL di base nel gateway applicazione](./media/tutorial-url-route-cli/application-gateway-nginx.png)

Modificare l'URL in http://&lt;IP-address&gt;: 8080/images/test.html, sostituendo l'indirizzo IP &lt;per IP-&gt;Address e dovrebbe essere visualizzato un esempio simile al seguente:

![Testare l'URL delle immagini nel gateway applicazione](./media/tutorial-url-route-cli/application-gateway-nginx-images.png)

Modificare l'URL in http://&lt;IP-address&gt;: 8080/video/test.html, sostituendo l'indirizzo IP &lt;per IP-&gt;Address e dovrebbe essere visualizzato un esempio simile al seguente.

![Testare l'URL video nel gateway applicazione](./media/tutorial-url-route-cli/application-gateway-nginx-video.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, rimuovere il gruppo di risorse, il gateway applicazione e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Passaggi successivi

[Creare un gateway applicazione con reindirizzamento basato su percorsi URL](./tutorial-url-redirect-cli.md)
