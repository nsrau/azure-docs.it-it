---
title: 'Avvio rapido: Creare un servizio di bilanciamento del carico pubblico - Interfaccia della riga di comando di Azure'
titleSuffix: Azure Load Balancer
description: Questa guida introduttiva mostra come creare un servizio di bilanciamento del carico pubblico con l'interfaccia della riga di comando di Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 0b6973f50e4d51c1c94e262a08681ec0431f511e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91333988"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>Avvio rapido: Creare un servizio pubblico di bilanciamento del carico per le macchine virtuali tramite l'interfaccia della riga di comando di Azure

Iniziare a usare Azure Load Balancer con l'interfaccia della riga di comando di Azure per creare un servizio di bilanciamento del carico pubblico e tre macchine virtuali.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Interfaccia della riga di comando di Azure installata in locale o Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento di avvio rapido è necessaria l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Come prima cosa creare con [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) un gruppo di risorse:

* Denominato **myResourceGroupLB**. 
* Nella posizione **eastus**.

```azurecli-interactive
  az group create \
    --name myResourceGroupLB \
    --location eastus
```
---

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Il bilanciamento del carico di SKU Standard è l'impostazione consigliata per i carichi di lavoro di produzione. Per altre informazioni sugli SKU, vedere **[SKU di Azure Load Balancer](skus.md)** .

## <a name="configure-virtual-network"></a>Configurare la rete virtuale

Prima di distribuire le macchine virtuali e testare il servizio di bilanciamento del carico, creare le risorse di rete virtuale di supporto.

### <a name="create-a-virtual-network"></a>Crea rete virtuale

Con [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt) creare una rete virtuale:

* Denominata **myVNet**.
* Prefisso indirizzo **10.1.0.0/16**.
* Con una subnet denominata **myBackendSubnet**.
* Prefisso subnet **10.1.0.0/24**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Posizione **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Per un servizio di bilanciamento del carico standard, le macchine virtuali nell'indirizzo back-end devono avere interfacce di rete appartenenti a un gruppo di sicurezza di rete. 

Con [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) creare un gruppo di sicurezza di rete:

* Denominato **myNSG**.
* Nel gruppo di risorse **myResourceGroupLB**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Creare una regola del gruppo di sicurezza di rete

Con [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) creare una regola del gruppo di sicurezza di rete:

* Denominata **myNSGRuleHTTP**.
* Nel gruppo di sicurezza di rete creato nel passaggio precedente, ovvero **myNSG**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Protocollo **(*)** .
* Direzione **In ingresso**.
* Origine **(*)** .
* Destinazione **(*)** .
* Porta di destinazione **Porta 80**.
* Accesso **Consenti**.
* Priorità **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Creare le interfacce di rete per le macchine virtuali

Con il comando [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) creare tre interfacce di rete:

#### <a name="vm1"></a>VM1

* Denominata **myNicVM1**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Nella rete virtuale **myVNet**.
* Nella subnet **myBackendSubnet**.
* Nel gruppo di sicurezza di rete **myNSG**.

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* Denominata **myNicVM2**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Nella rete virtuale **myVNet**.
* Nella subnet **myBackendSubnet**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm3"></a>VM3

* Denominata **myNicVM3**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Nella rete virtuale **myVNet**.
* Nella subnet **myBackendSubnet**.
* Nel gruppo di sicurezza di rete **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Creare i server back-end

In questa sezione verranno creati:

* Un file di configurazione cloud denominato **cloud-init. txt** per la configurazione del server.
* Tre macchine virtuali da usare come server back-end per il bilanciamento del carico.

### <a name="create-cloud-init-configuration-file"></a>Creare il file di configurazione cloud-init

Usare un file di configurazione cloud-init per installare NGINX ed eseguire un'app Node.js "Hello World" in una macchina virtuale Linux. 

Nella shell corrente creare un file denominato cloud-init.txt. Copiare e incollare la configurazione seguente nella shell. Assicurarsi che l'intero file cloud-init venga copiato correttamente, in particolare la prima riga:

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
### <a name="create-virtual-machines"></a>Creare macchine virtuali

Con il comando [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) creare le macchine virtuali:

#### <a name="vm1"></a>VM1
* Denominata **myVM1**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Con collegamento all'interfaccia di rete **myNicVM1**.
* Immagine di macchina virtuale **UbuntuLTS**.
* File di configurazione **cloud-init.txt** creato nel passaggio precedente.
* Nella **Zona 1**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 1 \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* Denominata **myVM2**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Con collegamento all'interfaccia di rete **myNicVM2**.
* Immagine di macchina virtuale **UbuntuLTS**.
* File di configurazione **cloud-init.txt** creato nel passaggio precedente.
* Nella **Zona 2**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 2 \
    --no-wait
```

#### <a name="vm3"></a>VM3
* Denominata **myVM3**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Con collegamento all'interfaccia di rete **myNicVM3**.
* Immagine di macchina virtuale **UbuntuLTS**.
* File di configurazione **cloud-init.txt** creato nel passaggio precedente.
* Nella **Zona 3**.

```azurecli-interactive
   az vm create \
    --resource-group myResourceGroupLB \
    --name myVM3 \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 3 \
    --no-wait
```
La distribuzione delle macchine virtuali potrebbe richiedere alcuni minuti.

## <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

Per accedere all'app Web in Internet, assegnare un indirizzo IP pubblico al servizio di bilanciamento del carico. 

Usare [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) per:

* Creare un indirizzo IP pubblico ridondante di zona standard denominato **myPublicIP**.
* In **myResourceGroupLB**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Standard
```

Per creare un indirizzo IP pubblico ridondante di zona nella Zona 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Standard \
    --zone 1
```

## <a name="create-standard-load-balancer"></a>Creare un servizio di bilanciamento del carico standard

Questa sezione descrive dettagliatamente come creare e configurare i componenti seguenti del servizio di bilanciamento del carico:

  * Un pool IP front-end che riceve il traffico di rete in ingresso sul servizio di bilanciamento del carico.
  * Un pool IP back-end a cui il pool front-end invia il traffico di rete con carico bilanciato.
  * Un probe di integrità che determina l'integrità delle istanze delle macchine virtuali back-end.
  * Una regola di bilanciamento del carico che definisce come verrà distribuito il traffico alle macchine virtuali.

### <a name="create-the-load-balancer-resource"></a>Creare la risorsa di bilanciamento del carico

Con [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create) creare un servizio di bilanciamento del carico pubblico:

* Denominato **myLoadBalancer**.
* Un pool front-end denominato **myFrontEnd**.
* Un pool back-end denominato **myBackEndPool**.
* Con associazione all'indirizzo IP pubblico **myPublicIP** creato nel passaggio precedente. 

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --sku Standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Creare il probe di integrità

Un probe di integrità controlla tutte le istanze di macchine virtuali per verificare che possano inviare traffico di rete. 

Una macchina virtuale con un controllo probe non riuscito viene rimossa dal servizio di bilanciamento del carico. La macchina virtuale viene nuovamente aggiunta al servizio di bilanciamento del carico quando il problema viene risolto.

Con [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create) creare un probe di integrità:

* Che esegua il monitoraggio dello stato delle macchine virtuali.
* Denominato **myHealthProbe**.
* Protocollo **TCP**.
* Porta **80** per il monitoraggio.

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Creare la regola di bilanciamento del carico

Una regola di bilanciamento del carico definisce:

* La configurazione IP front-end per il traffico in ingresso.
* Il pool IP back-end in cui ricevere il traffico.
* La porta di origine e destinazione richiesta. 

Con [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) creare una regola di bilanciamento del carico:

* Denominata **myHTTPRule**.
* In attesa sulla porta **80** nel pool front-end **myFrontEnd**.
* Per l'invio del traffico di rete con carico bilanciato al pool di indirizzi back-end **myBackEndPool** tramite la porta **80**. 
* Che usa il probe di integrità **myHealthProbe**.
* Protocollo **TCP**.
* Abilitare la funzionalità SNAT (Source Network Address Translation) con l'indirizzo IP front-end.

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true 
```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Aggiungere le macchine virtuali al pool back-end di bilanciamento del carico

Aggiungere le macchine virtuali al pool back-end con [az network nic ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):

#### <a name="vm1"></a>VM1
* Nel pool di indirizzi back-end **myBackEndPool**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Con associazione all'interfaccia di rete **myNicVM1** e **ipconfig1**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* Nel pool di indirizzi back-end **myBackEndPool**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Con associazione all'interfaccia di rete **myNicVM2** e **ipconfig1**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* Nel pool di indirizzi back-end **myBackEndPool**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Con associazione all'interfaccia di rete **myNicVM3** e **ipconfig1**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

## <a name="create-outbound-rule-configuration"></a>Creare la configurazione delle regole in uscita
Le regole in uscita del servizio di bilanciamento del carico configurano la conversione degli indirizzi di rete di origine in uscita per le macchine virtuali nel pool back-end. 

Per altre informazioni sulle connessioni in uscita, vedere [Connessioni in uscita in Azure](load-balancer-outbound-connections.md).

### <a name="create-outbound-public-ip-address-or-public-ip-prefix"></a>Creare un indirizzo IP pubblico in uscita o un prefisso IP pubblico

Usare [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) per creare un singolo indirizzo IP per la connettività in uscita.  

Usare [az network public-ip prefix create](https://docs.microsoft.com/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create) per creare un prefisso IP pubblico per la connettività in uscita.

Per altre informazioni sul ridimensionamento di NAT in uscita e della connettività in uscita, vedere [Scalabilità di NAT in uscita con più indirizzi IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scale).

#### <a name="public-ip"></a>IP pubblico

* Denominato **myPublicIPOutbound**.
* In **myResourceGroupLB**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIPOutbound \
    --sku Standard
```

Per creare un indirizzo IP pubblico ridondante di zona nella Zona 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIPOutbound \
    --sku Standard \
    --zone 1
```
#### <a name="public-ip-prefix"></a>Prefisso di indirizzo IP pubblico

* Denominato **myPublicIPPrefixOutbound**.
* In **myResourceGroupLB**.
* Lunghezza del prefisso pari a **28**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupLB \
    --name myPublicIPPrefixOutbound \
    --length 28
```
Per creare un prefisso IP pubblico ridondante di zona nella Zona 1:

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupLB \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

### <a name="create-outbound-frontend-ip-configuration"></a>Creare la configurazione IP front-end in uscita

Creare una nuova configurazione IP front-end con [az network lb frontend-ip create ](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create):

Selezionare i comandi per l'indirizzo IP pubblico o per il prefisso IP pubblico in base alla decisione presa nel passaggio precedente.

#### <a name="public-ip"></a>IP pubblico

* Denominato **myFrontEndOutbound**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Con associazione all'indirizzo IP pubblico **myPublicIPOutbound**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myResourceGroupLB \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>Prefisso di indirizzo IP pubblico

* Denominato **myFrontEndOutbound**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Con associazione al prefisso IP pubblico **myPublicIPPrefixOutbound**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myResourceGroupLB \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>Creare un pool in uscita

Con [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create) creare un nuovo pool in uscita:

* Denominato **myBackEndPoolOutbound**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>Creare una regola in uscita

Con [az network lb outbound-rule create](https://docs.microsoft.com/cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-create) creare una nuova regola in uscita per il pool back-end in uscita:

* Denominata **myOutboundRule**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.
* Con associazione al front-end **myFrontEndOutbound**.
* Protocollo **All**.
* Timeout di inattività pari a **15**.
* **10000** porte in uscita.
* Con associazione al pool back-end **myBackEndPoolOutbound**.

```azurecli-interactive
  az network lb outbound-rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myOutboundRule \
    --frontend-ip-configs myFrontEndOutbound \
    --protocol All \
    --idle-timeout 15 \
    --outbound-ports 10000 \
    --address-pool myBackEndPoolOutbound
```
### <a name="add-virtual-machines-to-outbound-pool"></a>Aggiungere le macchine virtuali al pool in uscita

Con [az network nic ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add) aggiungere le macchine virtuali al pool in uscita:


#### <a name="vm1"></a>VM1
* Nel pool di indirizzi back-end **myBackEndPoolOutbound**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Con associazione all'interfaccia di rete **myNicVM1** e **ipconfig1**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* Nel pool di indirizzi back-end **myBackEndPoolOutbound**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Con associazione all'interfaccia di rete **myNicVM2** e **ipconfig1**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* Nel pool di indirizzi back-end **myBackEndPoolOutbound**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Con associazione all'interfaccia di rete **myNicVM3** e **ipconfig1**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

# <a name="basic-sku"></a>[**SKU Basic**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Il bilanciamento del carico di SKU Standard è l'impostazione consigliata per i carichi di lavoro di produzione. Per altre informazioni sugli SKU, vedere **[SKU di Azure Load Balancer](skus.md)** .

## <a name="configure-virtual-network"></a>Configurare la rete virtuale

Prima di distribuire le macchine virtuali e testare il servizio di bilanciamento del carico, creare le risorse di rete virtuale di supporto.

### <a name="create-a-virtual-network"></a>Crea rete virtuale

Con [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt) creare una rete virtuale:

* Denominata **myVNet**.
* Prefisso indirizzo **10.1.0.0/16**.
* Con una subnet denominata **myBackendSubnet**.
* Prefisso subnet **10.1.0.0/24**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Posizione **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Per un servizio di bilanciamento del carico standard, le macchine virtuali nell'indirizzo back-end devono avere interfacce di rete appartenenti a un gruppo di sicurezza di rete. 

Con [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) creare un gruppo di sicurezza di rete:

* Denominato **myNSG**.
* Nel gruppo di risorse **myResourceGroupLB**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Creare una regola del gruppo di sicurezza di rete

Con [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) creare una regola del gruppo di sicurezza di rete:

* Denominata **myNSGRuleHTTP**.
* Nel gruppo di sicurezza di rete creato nel passaggio precedente, ovvero **myNSG**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Protocollo **(*)** .
* Direzione **In ingresso**.
* Origine **(*)** .
* Destinazione **(*)** .
* Porta di destinazione **Porta 80**.
* Accesso **Consenti**.
* Priorità **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Creare le interfacce di rete per le macchine virtuali

Con il comando [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) creare tre interfacce di rete:

#### <a name="vm1"></a>VM1

* Denominata **myNicVM1**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Nella rete virtuale **myVNet**.
* Nella subnet **myBackendSubnet**.
* Nel gruppo di sicurezza di rete **myNSG**.

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* Denominata **myNicVM2**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Nella rete virtuale **myVNet**.
* Nella subnet **myBackendSubnet**.
* Nel gruppo di sicurezza di rete **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM2 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm3"></a>VM3

* Denominata **myNicVM3**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Nella rete virtuale **myVNet**.
* Nella subnet **myBackendSubnet**.
* Nel gruppo di sicurezza di rete **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM3 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Creare i server back-end

In questa sezione verranno creati:

* Un file di configurazione cloud denominato **cloud-init. txt** per la configurazione del server. 
* Un set di disponibilità per le macchine virtuali.
* Tre macchine virtuali da usare come server back-end per il bilanciamento del carico.


Viene installato NGINX nelle macchine virtuali per verificare l'avvenuta creazione del servizio di bilanciamento del carico.

### <a name="create-cloud-init-configuration-file"></a>Creare il file di configurazione cloud-init

Usare un file di configurazione cloud-init per installare NGINX ed eseguire un'app Node.js "Hello World" in una macchina virtuale Linux. 

Nella shell corrente creare un file denominato cloud-init.txt. Copiare e incollare la configurazione seguente nella shell. Assicurarsi che l'intero file cloud-init venga copiato correttamente, in particolare la prima riga:

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
### <a name="create-availability-set-for-virtual-machines"></a>Creare il set di disponibilità per le macchine virtuali

Con [az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest#az-vm-availability-set-create) creare il set di disponibilità:

* Denominato **myAvSet**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Posizione **eastus**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group myResourceGroupLB \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Con il comando [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) creare le macchine virtuali:

#### <a name="vm1"></a>VM1
* Denominata **myVM1**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Con collegamento all'interfaccia di rete **myNicVM1**.
* Immagine di macchina virtuale **UbuntuLTS**.
* File di configurazione **cloud-init.txt** creato nel passaggio precedente.
* Nel set di disponibilità **myAvSet**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet \
    --no-wait 
```
#### <a name="vm2"></a>VM2
* Denominata **myVM2**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Con collegamento all'interfaccia di rete **myNicVM2**.
* Immagine di macchina virtuale **UbuntuLTS**.
* File di configurazione **cloud-init.txt** creato nel passaggio precedente.
* Nella **Zona 2**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```

#### <a name="vm3"></a>VM3
* Denominata **myVM3**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Con collegamento all'interfaccia di rete **myNicVM3**.
* Immagine di macchina virtuale **UbuntuLTS**.
* File di configurazione **cloud-init.txt** creato nel passaggio precedente.
* Nella **Zona 3**.

```azurecli-interactive
   az vm create \
    --resource-group myResourceGroupLB \
    --name myVM3 \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```
La distribuzione delle macchine virtuali potrebbe richiedere alcuni minuti.


## <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

Per accedere all'app Web in Internet, assegnare un indirizzo IP pubblico al servizio di bilanciamento del carico. 

Usare [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) per:

* Creare un indirizzo IP pubblico ridondante di zona standard denominato **myPublicIP**.
* Posizionarlo in **myResourceGroupLB**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Basic
```

## <a name="create-basic-load-balancer"></a>Creare un servizio di bilanciamento del carico di base

Questa sezione descrive dettagliatamente come creare e configurare i componenti seguenti del servizio di bilanciamento del carico:

  * Un pool IP front-end che riceve il traffico di rete in ingresso sul servizio di bilanciamento del carico.
  * Un pool IP back-end a cui il pool front-end invia il traffico di rete con carico bilanciato.
  * Un probe di integrità che determina l'integrità delle istanze delle macchine virtuali back-end.
  * Una regola di bilanciamento del carico che definisce come verrà distribuito il traffico alle macchine virtuali.

### <a name="create-the-load-balancer-resource"></a>Creare la risorsa di bilanciamento del carico

Con [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create) creare un servizio di bilanciamento del carico pubblico:

* Denominato **myLoadBalancer**.
* Un pool front-end denominato **myFrontEnd**.
* Un pool back-end denominato **myBackEndPool**.
* Con associazione all'indirizzo IP pubblico **myPublicIP** creato nel passaggio precedente. 

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --sku Basic \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Creare il probe di integrità

Un probe di integrità controlla tutte le istanze di macchine virtuali per verificare che possano inviare traffico di rete. 

Una macchina virtuale con un controllo probe non riuscito viene rimossa dal servizio di bilanciamento del carico. La macchina virtuale viene nuovamente aggiunta al servizio di bilanciamento del carico quando il problema viene risolto.

Con [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create) creare un probe di integrità:

* Che esegua il monitoraggio dello stato delle macchine virtuali.
* Denominato **myHealthProbe**.
* Protocollo **TCP**.
* Porta **80** per il monitoraggio.

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Creare la regola di bilanciamento del carico

Una regola di bilanciamento del carico definisce:

* La configurazione IP front-end per il traffico in ingresso.
* Il pool IP back-end in cui ricevere il traffico.
* La porta di origine e destinazione richiesta. 

Con [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) creare una regola di bilanciamento del carico:

* Denominata **myHTTPRule**.
* In attesa sulla porta **80** nel pool front-end **myFrontEnd**.
* Per l'invio del traffico di rete con carico bilanciato al pool di indirizzi back-end **myBackEndPool** tramite la porta **80**. 
* Che usa il probe di integrità **myHealthProbe**.
* Protocollo **TCP**.

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Aggiungere le macchine virtuali al pool back-end di bilanciamento del carico

Aggiungere le macchine virtuali al pool back-end con [az network nic ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):


#### <a name="vm1"></a>VM1
* Nel pool di indirizzi back-end **myBackEndPool**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Con associazione all'interfaccia di rete **myNicVM1** e **ipconfig1**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* Nel pool di indirizzi back-end **myBackEndPool**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Con associazione all'interfaccia di rete **myNicVM2** e **ipconfig1**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* Nel pool di indirizzi back-end **myBackEndPool**.
* Nel gruppo di risorse **myResourceGroupLB**.
* Con associazione all'interfaccia di rete **myNicVM3** e **ipconfig1**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```
---

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico

Per ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico, usare il comando [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show). 

Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```
:::image type="content" source="./media/load-balancer-standard-public-cli/running-nodejs-app.png" alt-text="Testare il servizio di bilanciamento del carico" border="true":::

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, rimuovere il gruppo di risorse, il servizio di bilanciamento del carico e tutte le risorse correlate tramite il comando [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>Passaggi successivi
In questo argomento di avvio rapido

* È stato creato un servizio di bilanciamento del carico standard o pubblico.
* Sono state collegate le macchine virtuali. 
* Sono stati configurati la regola del traffico di bilanciamento del carico e il probe di integrità.
* È stato testato il servizio di bilanciamento del carico.

Per altre informazioni su Azure Load Balancer, vedere [Informazioni su Azure Load Balancer](load-balancer-overview.md) e le [domande frequenti su Load Balancer](load-balancer-faqs.md).

Altre informazioni su [Load Balancer e zone di disponibilità](load-balancer-standard-availability-zones.md).
