---
title: Creare un gateway applicazione - Interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come creare un gateway applicazione usando l'interfaccia della riga di comando di Azure.
services: application-gateway
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: davidmu
ms.openlocfilehash: bf7e22e86e593045d25a9f31166aebe992caeb45
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="create-an-application-gateway-using-the-azure-cli"></a>Creare un gateway applicazione con l'interfaccia della riga di comando di Azure

È possibile usare l'interfaccia della riga di comando di Azure per creare o gestire i gateway applicazione dalla riga di comando o in script. In questa guida introduttiva viene illustrato come creare risorse di rete, server back-end e un gateway applicazione.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse usando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupAG* nella località *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Creare risorse di rete 

Creare la rete virtuale e la subnet usando il comando [az network vnet create](/cli/azure/vnet#az_vnet_create). Creare l'indirizzo IP pubblico usando il comando [az network public-ip create](/cli/azure/public-ip#az_public_ip_create).

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
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-backend-servers"></a>Creare i server back-end

In questo esempio vengono create due macchine virtuali da usare come server back-end per il gateway applicazione. È inoltre possibile installare NGINX nelle macchine virtuali per verificare l'avvenuta creazione del gateway applicazione.

### <a name="create-two-virtual-machines"></a>Creare due macchine virtuali

È possibile usare un file di configurazione cloud-init per installare NGINX ed eseguire un'app Node.js "Hello World" in una macchina virtuale Linux. Nella shell corrente creare un file denominato cloud-init.txt e incollare la configurazione seguente. Assicurarsi che l'intero file cloud-init venga copiato correttamente, in particolare la prima riga:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Creare l'interfaccia di rete con il comando [az network nic create](/cli/azure/network/nic#az_network_nic_create). Creare le macchine virtuali con il comando [az vm create](/cli/azure/vm#az_vm_create).

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>Creare il gateway applicazione

Creare un gateway applicazione con il comando [az network application-gateway create](/cli/azure/application-gateway#az_application_gateway_create). Quando si crea un gateway applicazione usando l'interfaccia della riga di comando di Azure, specificare le informazioni di configurazione, ad esempio le impostazioni relative a capacità, SKU e HTTP. Gli indirizzi IP privati delle interfacce di rete vengono aggiunti come server nel pool back-end del gateway applicazione.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Il processo di creazione del gateway applicazione può richiedere alcuni minuti. Dopo aver creato il gateway applicazione, saranno disponibili le funzionalità seguenti:

- *appGatewayBackendPool*: un gateway applicazione deve avere almeno un pool di indirizzi back-end.
- *appGatewayBackendHttpSettings*: specifica che per le comunicazioni vengono usati la porta 80 e il protocollo HTTP.
- *appGatewayHttpListener*: il listener predefinito associato ad *appGatewayBackendPool*.
- *appGatewayFrontendIP*: assegna *myAGPublicIPAddress* ad *appGatewayHttpListener*.
- *rule1*: regola di routing predefinita associata ad *appGatewayHttpListener*.

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

Per ottenere l'indirizzo IP pubblico del gateway applicazione, usare [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
``` 

![Testare il gateway applicazione](./media/application-gateway-create-gateway-cli/application-gateway-nginxtest.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile eliminare il gruppo di risorse, il gateway applicazione e tutte le risorse correlate con il comando [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive 
az group delete --name myResourceGroupAG
```
 
## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva sono stati creati un gruppo di risorse, le risorse di rete e i server back-end. Tali risorse sono state quindi usate per creare un gateway applicazione. Per altre informazioni sui gateway applicazione e sulle risorse associate, continuare con le procedure dettagliate.

