---
title: Creare un servizio di bilanciamento del carico pubblico - Interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come creare un servizio di bilanciamento del carico pubblico con l'interfaccia della riga di comando di Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: a8bcdd88-f94c-4537-8143-c710eaa86818
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2017
ms.author: kumud
ms.openlocfilehash: f2ba819c2341b2e481c2cfa5d5231f4cd5b6295b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-public-load-balancer-to-load-balance-vms-using-azure-cli-20"></a>Creare un servizio pubblico di bilanciamento del carico per le macchine virtuali tramite l'interfaccia della riga di comando di Azure 2.0

Questa guida introduttiva illustra come creare un servizio Azure Load Balancer. Per testare il servizio di bilanciamento del carico, si distribuiscono due macchine virtuali (VM) che eseguono Ubuntu Server e si bilancia il carico di un'app Web tra di esse.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Come prima cosa creare un gruppo di risorse con [az group create](https://docs.microsoft.com/cli/azure/group#create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupLB* nella posizione *eastus*:

```azurecli-interactive
  az group create \
    --name myResourceGroupLB \
    --location eastus
```

## <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

Per accedere all'app Web in Internet, assegnare un indirizzo IP pubblico al servizio di bilanciamento del carico. Usare il comando [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#create) per creare un indirizzo IP pubblico denominato *myPublicIP* in *myResourceGroupLB*.

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupLB --name myPublicIP
```

## <a name="create-azure-load-balancer"></a>Creare un Azure Load Balancer

Questa sezione descrive dettagliatamente come creare e configurare i componenti seguenti del servizio di bilanciamento del carico:
  - un pool IP front-end che riceve il traffico di rete in ingresso sul servizio di bilanciamento del carico
  - un pool IP back-end a cui il pool front-end invia il traffico di rete con carico bilanciato
  - un probe di integrità che determina l'integrità delle istanze back-end delle VM
  - una regola di bilanciamento del carico che definisce come il traffico verrà distribuito alle VM.

### <a name="create-the-load-balancer"></a>Creare il servizio di bilanciamento del carico

Usare il comando [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#create) per creare un servizio Azure Load Balancer pubblico denominato **myLoadBalancer**, che include un pool front-end denominato **myFrontEndPool** e un pool back-end denominato **myBackEndPool**, associato all'indirizzo IP pubblico **myPublicIP** creato nel passaggio precedente.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool       
  ```

### <a name="create-the-health-probe"></a>Creare il probe di integrità

Un probe di integrità controlla tutte le istanze di una macchina virtuale per assicurarsi che possano inviare il traffico di rete. L'istanza della macchina virtuale con controlli di probe falliti non viene rimossa dal servizio di bilanciamento del carico fino a quando non è nuovamente online e il controllo dei probe ne certifica l'integrità. Creare un probe di integrità con il comando [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#create) per monitorare l'integrità delle macchine virtuali. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Creare la regola di bilanciamento del carico

Una regola di bilanciamento del carico definisce la configurazione IP front-end per il traffico in ingresso e il pool IP di back-end che riceve il traffico, insieme alla porta di origine e di destinazione necessaria. Creare una regola di bilanciamento del carico *myLoadBalancerRuleWeb* con il comando [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#create) per l'ascolto sulla porta 80 nel pool front-end *myFrontEndPool* e l'invio del traffico di rete con carico bilanciato al pool di indirizzi back-end *myBackEndPool* sempre tramite la porta 80. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="configure-virtual-network"></a>Configurare la rete virtuale

Prima di distribuire alcune macchine virtuali e testare il servizio di bilanciamento del carico, creare le risorse di rete virtuale di supporto.

### <a name="create-a-virtual-network"></a>Crea rete virtuale

Creare una rete virtuale denominata *myVnet* con una subnet denominata *mySubnet* nel gruppo *myResourceGroup* con il comando [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create).

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVnet \
    --subnet-name mySubnet
```
###  <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete
Creare un gruppo di sicurezza di rete per definire le connessioni in ingresso alla rete virtuale.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>Creare una regola del gruppo di sicurezza di rete

Creare una regola del gruppo di sicurezza di rete per consentire il traffico in ingresso attraverso la porta 80.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
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

Creare tre interfacce di rete con il comando [az network nic create](/cli/azure/network/nic#az_network_nic_create) e associarle all'indirizzo IP pubblico e al gruppo di sicurezza di rete. 

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNic$i \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
done
```


## <a name="create-backend-servers"></a>Creare i server back-end

In questo esempio vengono create tre macchine virtuali da usare come server back-end per il bilanciamento del carico. Viene inoltre installato NGINX nelle macchine virtuali per verificare l'avvenuta creazione del servizio di bilanciamento del carico.

### <a name="create-an-availability-set"></a>Creare un set di disponibilità

Creare un set di disponibilità con il comando [az vm availabilityset create](/cli/azure/network/nic#az_network_availabilityset_create)

 ```azurecli-interactive
  az vm availability-set create \
    --resource-group myResourceGroupLB \
    --name myAvailabilitySet
```

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
 
Creare le macchine virtuali con il comando [az vm create](/cli/azure/vm#az_vm_create).

 ```azurecli-interactive
for i in `seq 1 2`; do
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM$i \
    --availability-set myAvailabilitySet \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
    --no-wait
    done
```
La distribuzione delle macchine virtuali può richiedere alcuni minuti.

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico

Per ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico, usare il comando [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
![Testare il bilanciamento del carico](./media/load-balancer-get-started-internet-arm-cli/running-nodejs-app.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile rimuovere il gruppo di risorse, il servizio di bilanciamento del carico e tutte le risorse correlate tramite il comando [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive 
  az group delete --name myResourceGroupLB
```


## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva si è creato un servizio di bilanciamento del carico, si sono collegate macchine virtuali, si è eseguita la configurazione della regola del traffico di bilanciamento del carico e del probe di integrità e quindi si è testato il servizio di bilanciamento del carico. Per altre informazioni sui servizi di bilanciamento del carico e sulle risorse associate, passare alle procedure dettagliate.
