---
title: "Creare un gateway applicazione con l'hosting di più siti - Interfaccia della riga di comando di Azure | Microsoft Docs"
description: "Informazioni su come creare un gateway applicazione che ospita più siti usando l'interfaccia della riga di comando di Azure."
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: davidmu
ms.openlocfilehash: df475cb6eed2b75275e573721f754e7de87698f5
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-the-azure-cli"></a>Creare un gateway applicazione con l'hosting di più siti usando l'interfaccia della riga di comando di Azure

È possibile usare l'interfaccia della riga di comando di Azure per configurare l'[hosting di più siti Web](application-gateway-multi-site-overview.md) quando si crea un [gateway applicazione](application-gateway-introduction.md). In questa esercitazione si creano pool back-end usando set di scalabilità di macchine virtuali e quindi si configurano i listener e le regole in base ai domini di cui si è proprietari per assicurarsi che il traffico Web raggiunga i server appropriati nei pool. Questa esercitazione presuppone che l'utente sia proprietario di più domini e che usi gli esempi di *www.contoso.com* e *www.fabrikam.com*.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Configurare la rete
> * Creare un gateway applicazione
> * Creare i listener e le regole di routing
> * Creare set di scalabilità di macchine virtuali con i pool back-end
> * Creare un record CNAME nel dominio

![Esempio di routing multisito](./media/tutorial-multisite-cli/scenario.png)

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

## <a name="create-the-application-gateway"></a>Creare il gateway applicazione

È possibile usare [az network application-gateway create](/cli/azure/application-gateway#create) per creare il gateway applicazione denominato *myAppGateway*. Quando si crea un gateway applicazione usando l'interfaccia della riga di comando di Azure, specificare le informazioni di configurazione, ad esempio le impostazioni relative a capacità, SKU e HTTP. Il gateway applicazione viene assegnato alla subnet *myAGSubnet* e all'indirizzo IP pubblico *myAGPublicIPAddress* creati in precedenza. 

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

### <a name="add-the-backend-pools"></a>Aggiungere i pool back-end

Aggiungere i pool back-end *contosoPool* e *fabrikamPool* necessari per contenere i server back-end usando [az network application-gateway address-pool create](/cli/azure/application-gateway#az_network_application_gateway_address_pool_create).

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name contosoPool
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name fabrikamPool
```

### <a name="add-listeners"></a>Aggiungere i listener

È necessario un listener per consentire al gateway applicazione di instradare il traffico in modo appropriato al pool back-end. In questa esercitazione si creano due listener per i due domini. In questo esempio vengono creati i listener per i domini *www.contoso.com* e *www.fabrikam.com*. 

Aggiungere i listener *contosoListener* e *fabrikamListener* necessari per instradare il traffico usando [az network application-gateway http-listener create](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create).

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com
az network application-gateway http-listener create \
  --name fabrikamListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.fabrikam.com   
  ```

### <a name="add-routing-rules"></a>Aggiungere le regole di routing

Le regole vengono elaborate nell'ordine in cui sono state create e il traffico viene indirizzato usando la prima regola corrispondente all'URL inviato al gateway applicazione. Se ad esempio si dispone di due regole, una che usa un listener di base e una che usa un listener multisito, entrambe sulla stessa porta, la regola con il listener multisito deve essere elencata prima della regola con il listener di base per funzionare come previsto. 

In questo esempio si creano due nuove regole e si elimina la regola predefinita che è stata creata al momento della creazione del gateway applicazione. È possibile aggiungere la regola usando [az network application-gateway rule create](/cli/azure/application-gateway#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoListener \
  --rule-type Basic \
  --address-pool contosoPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name fabrikamRule \
  --resource-group myResourceGroupAG \
  --http-listener fabrikamListener \
  --rule-type Basic \
  --address-pool fabrikamPool
az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Creare set di scalabilità di macchine virtuali

In questo esempio si creano tre set di scalabilità di macchine virtuali che supportano i tre pool back-end nel gateway applicazione. I set di scalabilità creati sono denominati *myvmss1*, *myvmss2* e *myvmss3*. Ogni set di scalabilità contiene due istanze di macchina virtuale in cui si installa NGINX.

```azurecli-interactive
for i in `seq 1 2`; do
  if [ $i -eq 1 ]
  then
    poolName="contosoPool"
  fi
  if [ $i -eq 2 ]
  then
    poolName="fabrikamPool"
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
for i in `seq 1 2`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{
  "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="create-a-cname-record-in-your-domain"></a>Creare un record CNAME nel dominio

Dopo aver creato il gateway applicazione con l'indirizzo IP pubblico, è possibile ottenere l'indirizzo DNS e usarlo per creare un record CNAME nel dominio. Per ottenere l'indirizzo DNS del gateway applicazione è possibile usare [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Copiare il valore *fqdn* di DNSSettings e usarlo come valore del record CNAME creato. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

Non è consigliabile usare record A perché l'indirizzo VIP può cambiare quando il gateway applicazione viene riavviato.

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

Immettere il nome del dominio nella barra degli indirizzi del browser, ad esempio: http://www.contoso.com.

![Testare il sito contoso nel gateway applicazione](./media/tutorial-multisite-cli/application-gateway-nginxtest1.png)

Sostituire l'indirizzo con l'altro dominio come nell'esempio seguente:

![Testare il sito fabrikam nel gateway applicazione](./media/tutorial-multisite-cli/application-gateway-nginxtest2.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Configurare la rete
> * Creare un gateway applicazione
> * Creare i listener e le regole di routing
> * Creare set di scalabilità di macchine virtuali con i pool back-end
> * Creare un record CNAME nel dominio

> [!div class="nextstepaction"]
> [Altre informazioni sulle operazioni che è possibile eseguire con il gateway applicazione](application-gateway-introduction.md)