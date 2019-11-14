---
title: Load Balancer Standard con indirizzo IP pubblico di zona front-end-interfaccia della riga di comando di Azure
titleSuffix: Azure Load Balancer
description: Informazioni su come creare un servizio Load Balancer Standard pubblico con un front-end di indirizzo IP pubblico di zona usando l'interfaccia della riga di comando di Azure
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: b9c3a88df6801566bc927cfc18fda0adfa05a5ae
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076039"
---
#  <a name="create-a-standard-load-balancer-with-zonal-public-ip-address-frontend-using-azure-cli"></a>Creare una Load Balancer Standard con front-end di indirizzo IP pubblico di zona usando l'interfaccia della riga di comando

Questo articolo illustra la creazione di un [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) pubblico con un front-end di zona usando un indirizzo IP pubblico Standard. In questo scenario, specificare una zona particolare per le istanze front-end e back-end, per allineare il percorso dati e le risorse a una zona specifica.

Per altre informazioni sull'uso delle zone di disponibilità con Load Balancer Standard, vedere [Load Balancer Standard e zone di disponibilità](load-balancer-standard-availability-zones.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, assicurarsi di avere installato la versione più recente dell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e di avere eseguito l'accesso a un account di Azure con [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest).

> [!NOTE]
>  Il supporto per le zone di disponibilità viene fornito per determinate risorse, aree e famiglie di dimensioni di macchine virtuali di Azure. Per altre informazioni su come iniziare e con quali risorse, aree e famiglie di dimensioni di macchina virtuale di Azure è possibile provare le zone di disponibilità, vedere [Panoramica di zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview). Per assistenza è possibile usare il forum di [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) oppure [aprire un ticket di supporto di Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group#az-group-create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupLB* nella località *westeurope*:

```azurecli-interactive
az group create \
--name myResourceGroupLB \
--location westeurope
```

## <a name="create-a-zonal-public-ip-standard"></a>Creare un indirizzo IP pubblico Standard di zona
Per accedere all'app in Internet, assegnare un indirizzo IP pubblico al servizio di bilanciamento del carico. Un indirizzo IP pubblico creato in una zona specifica esisterà sempre solo in tale zona. Non è possibile modificare la zona di un indirizzo IP pubblico.

Creare un indirizzo IP pubblico con [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create). Nell'esempio seguente viene creato un indirizzo IP pubblico di zona denominato *myPublicIP* nel gruppo di risorse *myResourceGroupLoadBalancer* nella zona 1.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupLB \
--name myPublicIP \
--sku Standard \
--zone 1
```

## <a name="create-azure-standard-load-balancer"></a>Creare un servizio Azure Load Balancer Standard
Questa sezione descrive dettagliatamente come creare e configurare i componenti seguenti del servizio di bilanciamento del carico:
- Un pool IP front-end che riceve il traffico di rete in ingresso sul servizio di bilanciamento del carico.
- un pool IP back-end a cui il pool front-end invia il traffico di rete con carico bilanciato
- un probe di integrità che determina l'integrità delle istanze delle macchine virtuali back-end
- una regola di bilanciamento del carico che definisce come verrà distribuito il traffico alle macchine virtuali.

### <a name="create-the-load-balancer"></a>Creare il servizio di bilanciamento del carico
Creare un servizio di bilanciamento del carico standard con il comando [az network lb create](/cli/azure/network/lb#az-network-lb-create). L'esempio seguente crea un servizio di bilanciamento del carico denominato *myLoadBalancer* e assegna l'indirizzo *myPublicIP* alla configurazione IP front-end.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEndPool \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Creare un probe di integrità sulla porta 80

Un probe di integrità controlla tutte le istanze di una macchina virtuale per assicurarsi che possano inviare il traffico di rete. L'istanza della macchina virtuale con controlli di probe falliti non viene rimossa dal servizio di bilanciamento del carico fino a quando non è nuovamente online e il controllo dei probe ne certifica l'integrità. Creare un probe di integrità con il comando az network lb probe create per monitorare l'integrità delle macchine virtuali. Per creare un probe di integrità TCP, usare con [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create). L'esempio seguente crea un probe di integrità denominato *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Creare una regola di bilanciamento del carico per la porta 80
Una regola di bilanciamento del carico definisce la configurazione IP front-end per il traffico in ingresso e il pool IP back-end che riceve il traffico, insieme alla porta di origine e di destinazione necessaria. Creare una regola di bilanciamento del carico *myLoadBalancerRuleWeb* con il comando [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) per l'ascolto sulla porta 80 nel pool front-end *myFrontEndPool* e l'invio del traffico di rete con carico bilanciato al pool di indirizzi back-end *myBackEndPool* sempre tramite la porta 80.

```azurecli-interactive
az network lb rule create \
--resource-group myResourceGroupLB \
--lb-name myLoadBalancer \
--name myLoadBalancerRuleWeb \
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

Creare una rete virtuale denominata *myVnet* con una subnet denominata *mySubnet* nel gruppo myResourceGroup con il comando [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Creare un gruppo di sicurezza di rete denominato *myNetworkSecurityGroup* per definire le connessioni in ingresso alla rete virtuale con il comando [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupLB \
--name myNetworkSecurityGroup
```

Creare una regola del gruppo di sicurezza di rete denominata *myNetworkSecurityGroupRule* per la porta 80 con il comando [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create).

```azurecli-interactive
az network nsg rule create \
--resource-group myResourceGroupLB \
--nsg-name myNetworkSecurityGroup \
--name myNetworkSecurityGroupRule \
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
Creare tre schede di interfaccia di rete virtuali con il comando [az network nic create](/cli/azure/network/nic#az-network-nic-create) e associarle all'indirizzo IP pubblico e al gruppo di sicurezza di rete. L'esempio seguente crea tre schede di interfaccia di rete virtuali, Una scheda di interfaccia di rete virtuale per ogni VM creata per l'app nei passaggi successivi. È possibile creare altre schede di interfaccia di rete virtuale e macchine virtuali in qualsiasi momento e aggiungerle al bilanciamento del carico:

```azurecli-interactive
for i in `seq 1 3`; do
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
In questo esempio vengono create tre macchine virtuali nella zona 1 da usare come server back-end per il bilanciamento del carico. Viene anche installato NGINX nelle macchine virtuali per verificare l'avvenuta creazione del servizio di bilanciamento del carico.

### <a name="create-cloud-init-config"></a>Creare una configurazione cloud-init

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

### <a name="create-the-zonal-virtual-machines"></a>Creare le macchine virtuali di zona
Creare le VM con [az vm create](/cli/azure/vm#az-vm-create). Nell'esempio seguente vengono create tre VM nella zona 1 e vengono generate le chiavi SSH, se non sono già presenti:

```azurecli-interactive
for i in `seq 1 3`; do
 az vm create \
--resource-group myResourceGroupLB \
--name myVM$i \
--nics myNic$i \
--image UbuntuLTS \
--generate-ssh-keys \
--zone 1 \
--custom-data cloud-init.txt
done
```

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico
Ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con il comando [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 

Sarà quindi possibile immettere l'indirizzo IP pubblico in un Web browser. Si ricordi che sono necessari alcuni minuti affinché le macchine virtuali siano pronte e che il bilanciamento del carico inizi a distribuire traffico ad esse. Verrà visualizzata l'app, con il nome host della macchina virtuale a cui il servizio di bilanciamento del carico ha distribuito il traffico, come nell'esempio seguente:

![Esecuzione dell'app Node.js](./media/load-balancer-standard-public-zonal-cli/running-nodejs-app.png)

Per verificare la distribuzione del traffico nelle VM della zona 1 che eseguono l'app da parte del servizio di bilanciamento del carico, forzare l'aggiornamento del Web browser.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su [Load Balancer Standard](./load-balancer-standard-overview.md).



