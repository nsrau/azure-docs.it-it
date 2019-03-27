---
title: "Avvio rapido: Indirizzare il traffico Web con un gateway applicazione di Azure dall'interfaccia della riga di comando di Azure | Microsoft Docs"
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per creare un gateway applicazione di Azure che indirizza il traffico Web alle macchine virtuali in un pool back-end.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/8/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: eb0f73d31abced8decbed31e5604a2056584eb98
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57549426"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Avvio rapido: Indirizzare il traffico Web con un gateway applicazione Azure - Interfaccia della riga di comando di Azure

Questa guida di avvio rapido illustra come usare il portale di Azure per creare un gateway applicazione.  Al termine della creazione, si testa il gateway applicazione per verificare che funzioni correttamente. Con il gateway applicazione di Azure si indirizza il traffico Web dell'applicazione a risorse specifiche assegnando listener alle porte, creando regole e aggiungendo risorse a un pool back-end. Per semplicità, questo articolo usa una semplice configurazione con un IP front-end pubblico, un listener di base che ospita un singolo sito nel gateway applicazione, due macchine virtuali usate per il pool back-end e una regola di gestione delle richieste di base.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisiti

### <a name="azure-powershell-module"></a>Modulo di Azure PowerShell

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Per trovare la versione, eseguire **az --version**. Per informazioni sull'installazione o sull'aggiornamento, vedere [Installare l'interfaccia da riga di comando di Azure]( /cli/azure/install-azure-cli).

### <a name="resource-group"></a>Gruppo di risorse

In Azure, si allocano le risorse correlate a un gruppo di risorse. Creare un gruppo di risorse usando [az group create](/cli/azure/group#az-group-create). 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupAG* nella località *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

### <a name="required-network-resources"></a>Risorse di rete necessarie 

Per le comunicazioni tra le risorse create in Azure è necessaria una rete virtuale.  La subnet del gateway applicazione può contenere solo i gateway applicazione. Non sono consentite altre risorse.  È possibile creare una nuova subnet per il gateway applicazione o usarne una esistente. In questo esempio vengono create due subnet: una per il gateway applicazione e l'altra per i server back-end. L'IP front-end del gateway applicazione può essere configurato come pubblico o privato a seconda del caso d'uso. In questo esempio si sceglierà un IP front-end pubblico.

Per creare la rete virtuale e la subnet usare il comando [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). Eseguire [az network public-ip create](/cli/azure/network/public-ip) per creare l'indirizzo IP pubblico.

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

### <a name="backend-servers"></a>Server back-end

Il back-end può essere costituito da schede di interfaccia di rete, set di scalabilità di macchine virtuali, IP pubblici, IP interni, nomi di dominio completi (FQDN) e back-end multi-tenant come Servizio app di Azure. In questo esempio vengono create due macchine virtuali per Azure da usare come server back-end per il gateway applicazione. È anche possibile installare IIS nelle macchine virtuali per verificare l'avvenuta creazione del gateway applicazione in Azure.

#### <a name="create-two-virtual-machines"></a>Creare due macchine virtuali

Installare il [server Web NGINX](https://docs.nginx.com/nginx/) nelle macchine virtuali per verificare l'avvenuta creazione del gateway applicazione. È possibile usare un file di configurazione cloud-init per installare NGINX ed eseguire un'app Node.js "Hello World" in una macchina virtuale Linux. Per altre informazioni su cloud-init, vedere gli [Supporto di cloud-init per macchine virtuali in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).

In Azure Cloud Shell, copiare e incollare la configurazione seguente in un file denominato *cloud-init.txt*. Immettere *editor cloud-init.txt* per creare il file.

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

Creare l'interfaccia di rete con il comando [az network nic create](/cli/azure/network/nic#az-network-nic-create). Per creare le macchine virtuali usare il comando [az vm create](/cli/azure/vm#az-vm-create).

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

Creare un gateway applicazione con il comando [az network application-gateway create](/cli/azure/network/application-gateway). Quando si crea un gateway applicazione con l'interfaccia della riga di comando di Azure, specificare le informazioni di configurazione, ad esempio le impostazioni relative a capacità, SKU e HTTP. Azure aggiunge quindi gli indirizzi IP privati delle interfacce di rete come server nel pool back-end del gateway applicazione.

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

Il processo di creazione del gateway applicazione in Azure può richiedere fino a 30 minuti. Dopo averlo creato, è possibile visualizzare le impostazioni seguenti nella sezione **Impostazioni** della pagina **Gateway applicazione**:

- **appGatewayBackendPool**: disponibile nella pagina **Pool back-end**. Specifica il pool back-end richiesto.
- **appGatewayBackendHttpSettings**: disponibile nella pagina **Impostazioni HTTP**. Specifica che il gateway applicazione usa la porta 80 e il protocollo HTTP per la comunicazione.
- **appGatewayHttpListener**: disponibile nella pagina **Listener**. Specifica il listener predefinito associato ad **appGatewayBackendPool**.
- **appGatewayFrontendIP**: disponibile nella pagina **Configurazioni IP front-end**. Assegna *myAGPublicIPAddress* ad **appGatewayHttpListener**.
- **rule1**: disponibile nella pagina **Regole**. Specifica la regola di gestione predefinita associata ad **appGatewayHttpListener**.

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

Nonostante l'installazione di un server Web NGINX non sia necessaria per creare il gateway applicazione, è stata eseguita in questa guida introduttiva per verificare se il gateway applicazione è stato creato correttamente in Azure. Per ottenere l'indirizzo IP pubblico del nuovo gateway applicazione, usare il comando [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Copiare e incollare l'indirizzo IP pubblico nella barra degli indirizzi del browser.
    
![Testare il gateway applicazione](./media/quick-create-cli/application-gateway-nginxtest.png)

Quando si aggiorna il browser, dovrebbe apparire il nome della seconda macchina virtuale. Una risposta valida verifica che il gateway applicazione sia stato creato correttamente e sia in grado di connettersi correttamente al back-end.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con il gateway applicazione non sono più necessarie, usare il comando [az group delete](/cli/azure/group#az-group-delete) per rimuovere il gruppo di risorse. La rimozione del gruppo di risorse comporta anche la rimozione del gateway applicazione e di tutte le risorse correlate.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Gestire il traffico Web con un gateway applicazione mediante l'interfaccia della riga di comando di Azure](./tutorial-manage-web-traffic-cli.md)

