---
title: 'Avvio rapido: Creare una risorsa Load Balancer pubblica - Interfaccia della riga di comando di Azure'
titleSuffix: Azure Load Balancer
description: Questa guida introduttiva mostra come creare un servizio di bilanciamento del carico pubblico con l'interfaccia della riga di comando di Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
tags: azure-resource-manager
Customer intent: I want to create a Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: a8bcdd88-f94c-4537-8143-c710eaa86818
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: fdbd002ac946f3ac3a1a67980905d4ed6f5510c5
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470344"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-azure-cli"></a>Avvio rapido: Creare un'istanza di Load Balancer Standard per bilanciare il carico delle macchine virtuali tramite l'interfaccia della riga di comando di Azure

Questa guida di avvio rapido illustra come creare una risorsa Load Balancer pubblica. Per testare il servizio di bilanciamento del carico, si distribuiscono due macchine virtuali (VM) che eseguono Ubuntu Server e si bilancia il carico di un'app Web tra le due macchine virtuali.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Come prima cosa creare un gruppo di risorse con [az group create](https://docs.microsoft.com/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupSLB* nella posizione *eastus*:

```azurecli-interactive
  az group create \
    --name myResourceGroupSLB \
    --location eastus
```

## <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

Per accedere all'app Web in Internet, assegnare un indirizzo IP pubblico al servizio di bilanciamento del carico. Usare il comando [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) per creare un indirizzo IP pubblico con ridondanza della zona Standard denominato *myPublicIP* in *myResourceGroupSLB*.

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard
```

Per creare un indirizzo IP pubblico di zona nella zona 1 usare:

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard --zone 1
```

Usare ```-SKU Basic``` per creare un indirizzo IP pubblico Basic. Gli indirizzi IP pubblici Basic non sono compatibili con Load Balancer **Standard**. Per i carichi di lavoro di produzione è consigliabile usare il livello **Standard**.

> [!IMPORTANT]
> Il resto di questa guida di avvio rapido presuppone che durante il precedente processo di selezione dello SKU venga scelto lo SKU **Standard**.

## <a name="create-azure-load-balancer"></a>Creare un'istanza di Azure Load Balancer

Questa sezione descrive dettagliatamente come creare e configurare i componenti seguenti del servizio di bilanciamento del carico:
  - Un pool IP front-end che riceve il traffico di rete in ingresso sul servizio di bilanciamento del carico.
  - un pool IP back-end a cui il pool front-end invia il traffico di rete con carico bilanciato
  - un probe di integrità che determina l'integrità delle istanze delle macchine virtuali back-end
  - una regola di bilanciamento del carico che definisce come verrà distribuito il traffico alle macchine virtuali.

### <a name="create-the-load-balancer"></a>Creare il servizio di bilanciamento del carico

Usare il comando [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) per creare un servizio Azure Load Balancer pubblico denominato **myLoadBalancer**, che include un pool front-end denominato **myFrontEnd** e un pool back-end denominato **myBackEndPool**, associato all'indirizzo IP pubblico **myPublicIP** creato nel passaggio precedente. Usare ```--sku basic``` per creare un indirizzo IP pubblico Basic. Microsoft consiglia di scegliere SKU Standard per i carichi di lavoro di produzione.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupSLB \
    --name myLoadBalancer \
    --sku standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
  ```

> [!IMPORTANT]
> Il resto di questa guida di avvio rapido presuppone che durante il precedente processo di selezione dello SKU venga scelto lo SKU **Standard**.

### <a name="create-the-health-probe"></a>Creare il probe di integrità

Un probe di integrità controlla tutte le istanze di una macchina virtuale per assicurarsi che possano inviare il traffico di rete. L'istanza della macchina virtuale con controlli di probe falliti non viene rimossa dal servizio di bilanciamento del carico fino a quando non è nuovamente online e il controllo dei probe ne certifica l'integrità. Creare un probe di integrità con il comando [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) per monitorare l'integrità delle macchine virtuali. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Creare la regola di bilanciamento del carico

Una regola di bilanciamento del carico definisce la configurazione IP front-end per il traffico in ingresso e il pool IP back-end che riceve il traffico, insieme alle porte di origine e di destinazione necessarie. Creare una regola di bilanciamento del carico *myLoadBalancerRuleWeb* con il comando [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) per l'ascolto sulla porta 80 nel pool front-end *myFrontEnd* e l'invio del traffico di rete con carico bilanciato al pool di indirizzi back-end *myBackEndPool* sempre tramite la porta 80. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="configure-virtual-network"></a>Configurare la rete virtuale

Prima di distribuire alcune macchine virtuali e testare il servizio di bilanciamento del carico, creare le risorse di rete virtuale di supporto.

### <a name="create-a-virtual-network"></a>Crea rete virtuale

Creare una rete virtuale denominata *myVnet* con una subnet denominata *mySubnet* nel gruppo *myResourceGroup* con il comando [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupSLB \
    --location eastus \
    --name myVnet \
    --subnet-name mySubnet
```
###  <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Per un'istanza di Load Balancer Standard le macchine virtuali nell'indirizzo back-end devono avere le schede di interfaccia di rete appartenenti a un gruppo di sicurezza di rete. Creare un gruppo di sicurezza di rete per definire le connessioni in ingresso alla rete virtuale.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupSLB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>Creare una regola del gruppo di sicurezza di rete

Creare una regola del gruppo di sicurezza di rete per consentire il traffico in ingresso attraverso la porta 80.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupSLB \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```
### <a name="create-nics"></a>Creare NIC

Creare tre interfacce di rete con il comando [az network nic create](/cli/azure/network/nic#az-network-nic-create) e associarle all'indirizzo IP pubblico e al gruppo di sicurezza di rete. 

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM1 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool

  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
  
  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool

```

## <a name="create-backend-servers"></a>Creare i server back-end

In questo esempio vengono create tre macchine virtuali da usare come server back-end per il bilanciamento del carico. Viene inoltre installato NGINX nelle macchine virtuali per verificare l'avvenuta creazione del servizio di bilanciamento del carico.

Se si sta creando una risorsa Load Balancer Basic con un indirizzo IP pubblico Basic, sarà necessario creare un set di disponibilità usando [az vm availabilityset create](/cli/azure/network/nic) per aggiungere le macchine virtuali al suo interno. Non è necessario eseguire questo passaggio aggiuntivo per le istanze di Load Balancer Standard. Microsoft consiglia l'uso del livello Standard.

### <a name="create-three-virtual-machines"></a>Creare tre macchine virtuali

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
 
Creare le macchine virtuali con il comando [az vm create](/cli/azure/vm#az-vm-create).

 ```azurecli-interactive

  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM1 \
    --availability-set myAvailabilitySet \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait
   
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM2 \
    --availability-set myAvailabilitySet \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait

   az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM3 \
    --availability-set myAvailabilitySet \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait

```
La distribuzione delle macchine virtuali può richiedere alcuni minuti.

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico

Per ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico, usare il comando [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
   ![Testare il bilanciamento del carico](./media/load-balancer-standard-public-cli/running-nodejs-app.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile rimuovere il gruppo di risorse, il servizio di bilanciamento del carico e tutte le risorse correlate tramite il comando [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive 
  az group delete --name myResourceGroupSLB
```
## <a name="next-steps"></a>Passaggi successivi
In questo argomento di avvio rapido si è creata un'istanza di Load Balancer Standard, si sono collegate macchine virtuali a tale istanza, si è configurata la regola del traffico di Load Balancer, si è definito il probe di integrità e quindi si è testata l'istanza di Load Balancer. Per altre informazioni su Azure Load Balancer, passare alle [Esercitazioni su Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).

Altre informazioni su [Load Balancer e zone di disponibilità](load-balancer-standard-availability-zones.md).