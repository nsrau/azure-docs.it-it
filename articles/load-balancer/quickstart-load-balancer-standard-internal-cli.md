---
title: 'Avvio rapido: Creare un servizio di bilanciamento del carico interno - Interfaccia della riga di comando di Azure'
titleSuffix: Azure Load Balancer
description: Questa guida di avvio rapido illustra come creare un servizio di bilanciamento del carico interno con l'interfaccia della riga di comando di Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 75e37c91b9b3161d7396d94fb086c4dc567a18c1
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546994"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Avvio rapido: Creare un servizio di bilanciamento del carico interno per le macchine virtuali mediante l'interfaccia della riga di comando di Azure

Iniziare a usare Azure Load Balancer con l'interfaccia della riga di comando di Azure per creare un servizio di bilanciamento del carico pubblico e tre macchine virtuali.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Interfaccia della riga di comando di Azure installata in locale o Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento di avvio rapido è necessaria l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Come prima cosa creare con [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) un gruppo di risorse:

* Con nome **CreateIntLBQS-rg**. 
* Nella posizione **eastus**.

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus
```
---

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Il bilanciamento del carico di SKU Standard è l'impostazione consigliata per i carichi di lavoro di produzione. Per altre informazioni sugli SKU, vedere **[SKU di Azure Load Balancer](skus.md)** .

## <a name="configure-virtual-network"></a>Configurare la rete virtuale

Prima di distribuire le macchine virtuali e il servizio di bilanciamento del carico, creare le risorse di rete virtuale di supporto.

### <a name="create-a-virtual-network"></a>Crea rete virtuale

Con [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt) creare una rete virtuale:

* Denominata **myVNet**.
* Prefisso indirizzo **10.1.0.0/16**.
* Con una subnet denominata **myBackendSubnet**.
* Prefisso subnet **10.1.0.0/24**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Posizione **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
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
* Nel gruppo di risorse **CreateIntLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Creare una regola del gruppo di sicurezza di rete

Con [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) creare una regola del gruppo di sicurezza di rete:

* Denominata **myNSGRuleHTTP**.
* Nel gruppo di sicurezza di rete creato nel passaggio precedente, ovvero **myNSG**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Protocollo **(*)** .
* Direzione **In ingresso**.
* Origine **(*)** .
* Destinazione **(*)** .
* Porta di destinazione **Porta 80**.
* Accesso **Consenti**.
* Priorità **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
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

Creare due interfacce di rete con il comando [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

#### <a name="vm1"></a>VM1

* Denominata **myNicVM1**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Nella rete virtuale **myVNet**.
* Nella subnet **myBackendSubnet**.
* Nel gruppo di sicurezza di rete **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* Denominata **myNicVM2**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Nella rete virtuale **myVNet**.
* Nella subnet **myBackendSubnet**.
* Nel gruppo di sicurezza di rete **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Creare i server back-end

In questa sezione verranno creati:

* Un file di configurazione cloud denominato **cloud-init. txt** per la configurazione del server.
* Due macchine virtuali da usare come server back-end per il bilanciamento del carico.

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
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Con collegamento all'interfaccia di rete **myNicVM1**.
* Immagine di macchina virtuale **UbuntuLTS**.
* File di configurazione **cloud-init.txt** creato nel passaggio precedente.
* Nella **Zona 1**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 1 \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* Denominata **myVM2**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Con collegamento all'interfaccia di rete **myNicVM2**.
* Immagine di macchina virtuale **UbuntuLTS**.
* File di configurazione **cloud-init.txt** creato nel passaggio precedente.
* Nella **Zona 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 2 \
    --no-wait
```

La distribuzione delle macchine virtuali potrebbe richiedere alcuni minuti.

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
* Associato alla rete virtuale **myVNet**.
* Associato alla subnet back-end **myBackendSubnet**.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
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
    --resource-group CreateIntLBQS-rg \
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
* Timeout di inattività di **15 minuti**.
* Abilitare la reimpostazione TCP.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
>[!NOTE]
>Le macchine virtuali nel pool back-end non avranno connettività Internet in uscita con questa configurazione. </br> Per altre informazioni su come fornire la connettività in uscita, vedere: </br> **[Connessioni in uscita in Azure](load-balancer-outbound-connections.md)**</br> Opzioni per fornire la connettività: </br> **[Configurazione del servizio di bilanciamento del carico solo in uscita](egress-only.md)** </br> **[Che cos'è NAT di rete virtuale?](https://docs.microsoft.com/azure/virtual-network/nat-overview)**

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Aggiungere le macchine virtuali al pool back-end di bilanciamento del carico

Aggiungere le macchine virtuali al pool back-end con [az network nic ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):


#### <a name="vm1"></a>VM1
* Nel pool di indirizzi back-end **myBackEndPool**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Con associazione all'interfaccia di rete **myNicVM1** e **ipconfig1**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* Nel pool di indirizzi back-end **myBackEndPool**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Con associazione all'interfaccia di rete **myNicVM2** e **ipconfig1**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

# <a name="basic-sku"></a>[**SKU Basic**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Il bilanciamento del carico di SKU Standard è l'impostazione consigliata per i carichi di lavoro di produzione. Per altre informazioni sugli SKU, vedere **[SKU di Azure Load Balancer](skus.md)** .

## <a name="configure-virtual-network"></a>Configurare la rete virtuale

Prima di distribuire le macchine virtuali e il servizio di bilanciamento del carico, creare le risorse di rete virtuale di supporto.

### <a name="create-a-virtual-network"></a>Crea rete virtuale

Con [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt) creare una rete virtuale:

* Denominata **myVNet**.
* Prefisso indirizzo **10.1.0.0/16**.
* Con una subnet denominata **myBackendSubnet**.
* Prefisso subnet **10.1.0.0/24**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Posizione **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
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
* Nel gruppo di risorse **CreateIntLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Creare una regola del gruppo di sicurezza di rete

Con [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) creare una regola del gruppo di sicurezza di rete:

* Denominata **myNSGRuleHTTP**.
* Nel gruppo di sicurezza di rete creato nel passaggio precedente, ovvero **myNSG**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Protocollo **(*)** .
* Direzione **In ingresso**.
* Origine **(*)** .
* Destinazione **(*)** .
* Porta di destinazione **Porta 80**.
* Accesso **Consenti**.
* Priorità **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
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

Creare due interfacce di rete con il comando [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

#### <a name="vm1"></a>VM1

* Denominata **myNicVM1**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Nella rete virtuale **myVNet**.
* Nella subnet **myBackendSubnet**.
* Nel gruppo di sicurezza di rete **myNSG**.

```azurecli-interactive

  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* Denominata **myNicVM2**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Nella rete virtuale **myVNet**.
* Nella subnet **myBackendSubnet**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Creare i server back-end

In questa sezione verranno creati:

* Un file di configurazione cloud denominato **cloud-init. txt** per la configurazione del server. 
* Un set di disponibilità per le macchine virtuali.
* Due macchine virtuali da usare come server back-end per il bilanciamento del carico.

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
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Posizione **eastus**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Con il comando [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) creare le macchine virtuali:

#### <a name="vm1"></a>VM1
* Denominata **myVM1**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Con collegamento all'interfaccia di rete **myNicVM1**.
* Immagine di macchina virtuale **UbuntuLTS**.
* File di configurazione **cloud-init.txt** creato nel passaggio precedente.
* Nel set di disponibilità **myAvSet**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* Denominata **myVM2**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Con collegamento all'interfaccia di rete **myNicVM2**.
* Immagine di macchina virtuale **UbuntuLTS**.
* File di configurazione **cloud-init.txt** creato nel passaggio precedente.
* Nella **Zona 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```

La distribuzione delle macchine virtuali potrebbe richiedere alcuni minuti.

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
* Associato alla rete virtuale **myVNet**.
* Associato alla subnet back-end **myBackendSubnet**.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
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
    --resource-group CreateIntLBQS-rg \
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
* Timeout di inattività di **15 minuti**.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 
```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Aggiungere le macchine virtuali al pool back-end di bilanciamento del carico

Aggiungere le macchine virtuali al pool back-end con [az network nic ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):


#### <a name="vm1"></a>VM1
* Nel pool di indirizzi back-end **myBackEndPool**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Con associazione all'interfaccia di rete **myNicVM1** e **ipconfig1**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* Nel pool di indirizzi back-end **myBackEndPool**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Con associazione all'interfaccia di rete **myNicVM2** e **ipconfig1**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

---

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico

### <a name="create-azure-bastion-public-ip"></a>Creare l'IP pubblico di Azure Bastion

Usare [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) per creare un indirizzo IP pubblico per l'host bastion:

* Creare un indirizzo IP pubblico ridondante di zona standard denominato **myBastionIP**.
* In **CreateIntLBQS-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```

### <a name="create-azure-bastion-subnet"></a>Creare una subnet Azure Bastion

Usare [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create) per creare una subnet:

* Denominata **AzureBastionSubnet**.
* Prefisso indirizzo **10.1.1.0/24**.
* Nella rete virtuale **myVNet**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.

```azurecli-interactive
  az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-azure-bastion-host"></a>Creare l'host Azure Bastion
Usare [az network bastion create](https://docs.microsoft.com/cli/azure/network/bastion?view=azure-cli-latest#az-network-bastion-create) per creare un host bastion:

* Denominato **myBastionHost**
* In **CreateIntLBQS-rg**
* Associato all'IP pubblico **myBastionIP**
* Associato alla rete virtuale **myVNet**
* Nella località **eastus**

```azurecli-interactive
  az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```
La distribuzione dell'host bastion richiederà qualche minuto.

### <a name="create-test-virtual-machine"></a>Creare una macchina virtuale di test

Creare l'interfaccia di rete con il comando [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

* Denominata **myNicTestVM**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Nella rete virtuale **myVNet**.
* Nella subnet **myBackendSubnet**.
* Nel gruppo di sicurezza di rete **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Creare la macchina virtuale con il comando [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create):

* Denominata **myTestVM**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Con collegamento all'interfaccia di rete **myNicTestVM**.
* Immagine della macchina virtuale **Win2019Datacenter**.
* Scegliere i valori per **\<adminpass>** e **\<adminuser>** .
  

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username <adminuser> \
    --admin-password <adminpass> \
    --no-wait
```
La distribuzione della macchina virtuale può richiedere alcuni minuti.

### <a name="test"></a>Test

1. [Accedere](https://portal.azure.com) al portale di Azure.

1. Individuare l'indirizzo IP privato del servizio di bilanciamento del carico nella schermata **Panoramica**. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer**.

2. Annotare o copiare l'indirizzo accanto a **Indirizzo IP privato** nella pagina **Panoramica** di **myLoadBalancer**.

3. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine nell'elenco di risorse selezionare **myTestVM** , che si trova nel gruppo di risorse **CreateIntLBQS-rg**.

4. Nella pagina **Panoramica** selezionare **Connetti** , quindi **Bastion**.

6. Immettere il nome utente e la password specificati durante la creazione della VM.

7. Aprire **Internet Explorer** in **myTestVM**.

8. Immettere l'indirizzo IP del passaggio precedente nella barra degli indirizzi del browser. Nel browser verrà visualizzata la pagina predefinita del server Web IIS.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Creare un servizio di bilanciamento del carico interno standard" border="true":::
   
Per visualizzare il servizio di bilanciamento del carico distribuire il traffico tra tutte e tre macchine virtuali, è possibile personalizzare la pagina predefinita del server Web IIS di ciascuna macchina virtuale e quindi forzare l'aggiornamento del Web browser dal computer client.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, rimuovere il gruppo di risorse, il servizio di bilanciamento del carico e tutte le risorse correlate tramite il comando [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Passaggi successivi
In questo argomento di avvio rapido

* È stato creato un servizio di bilanciamento del carico standard o pubblico.
* Sono state collegate le macchine virtuali. 
* Sono stati configurati la regola del traffico di bilanciamento del carico e il probe di integrità.
* È stato testato il servizio di bilanciamento del carico.

Per altre informazioni su Azure Load Balancer, passare a 
> [!div class="nextstepaction"]
> [Informazioni su Azure Load Balancer](load-balancer-overview.md)