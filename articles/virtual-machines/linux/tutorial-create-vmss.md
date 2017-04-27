---
title: "Creare un&quot;app a disponibilità elevata in Azure usando il set di scalabilità di macchine virtuali | Microsoft Docs"
description: "Creare e distribuire un&quot;applicazione a disponibilità elevata in macchine virtuali Linux usando un set di scalabilità di macchine virtuali e l&quot;interfaccia della riga di comando di Azure."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/05/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 4c76fb202f501f671504646395b800aeb90d8e69
ms.lasthandoff: 04/07/2017

---

# <a name="create-a-highly-available-application-on-linux-with-virtual-machine-scale-sets"></a>Creare un'applicazione a disponibilità elevata in Linux usando i set di scalabilità di macchine virtuali
Questa esercitazione illustra come creare un'applicazione a disponibilità elevata in un set di scalabilità di macchine virtuali. È inclusa anche la procedura per automatizzare la configurazione delle macchine virtuali nel set di scalabilità. 


## <a name="step-1---create-a-resource-group"></a>Passaggio 1: creare un gruppo di risorse
Per completare questa esercitazione, verificare di aver installato l'ultima versione dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). Se non è già stato eseguito l'accesso alla sottoscrizione di Azure, accedere con [az login](/cli/azure/#login) e seguire le indicazioni visualizzate.

Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group#create). Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroupVMSS` nella località `westus`:

```azurecli
az group create --name myResourceGroupVMSS --location westus
```


## <a name="step-2---define-your-app"></a>Passaggio 2: definire l'app
Usare la stessa configurazione **cloud-init** dell'esercitazione in cui è stata creata un'app a disponibilità elevata con carico bilanciato. Per altre informazioni sull'uso di **cloud-init**, vedere [Usare cloud-init per personalizzare una VM Linux durante la creazione](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Creare un file denominato `cloud-init.txt` e incollare la configurazione seguente:

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
  - nginx -s reload
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```


## <a name="step-3---create-scale-set"></a>Passaggio 3: creare un set di scalabilità
Un set di scalabilità di macchine virtuali consente di distribuire e gestire un set di macchine virtuali identiche con scalabilità automatica. I set di scalabilità usano gli stessi componenti dell'esercitazione [Build a highly available app on Azure](tutorial-load-balance-nodejs.md) (Creare un'app a disponibilità elevata in Azure). Questi componenti includono come set di disponibilità, i domini di errore e di aggiornamento e i bilanciamenti del carico.

Con un set di scalabilità, queste risorse vengono create e gestite direttamente. Il numero di macchine virtuali nel set di scalabilità può aumentare o diminuire in base alle regole definite. È possibile [usare un'immagine personalizzata](capture-image.md) come origine per la macchina virtuale, o configurare le macchine virtuali durante la distribuzione con **cloud-init** come illustrato in questa esercitazione.

Come creare un set di scalabilità di macchine virtuali con [az vmss create](/cli/azure/vmss#create). Nell'esempio seguente viene creato un set di scalabilità denominato `myScaleSet`:

```azurecli
az vmss create \
  --resource-group myResourceGroupVMSS \
  --name myScaleSet \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys      
```

La creazione e la configurazione di tutte le macchine virtuali e risorse del set di scalabilità richiedono alcuni minuti.


## <a name="step-4---configure-firewall"></a>Passaggio 4: configurare un firewall
Un bilanciamento del carico è stato creato automaticamente come parte del set di scalabilità di macchine virtuali. Il bilanciamento del carico distribuisce il traffico ad un set di macchine virtuali definite usando le proprie regole. Per consentire al traffico di raggiungere l'app Web, creare una regola con [az network lb probe create](/cli/azure/network/lb/probe#create). Nell'esempio seguente viene creata una regola denominata `myLoadBalancerRuleWeb`:

```azurecli
az network lb rule create \
  --resource-group myResourceGroupVMSS \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="step-5---test-your-app"></a>Passaggio 5: testare l'app
Ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con [az network public-ip show](/cli/azure/network/public-ip#show). L'esempio seguente ottiene l'indirizzo IP per `myScaleSetLBPublicIP` creato come parte del set di scalabilità:

```azurecli
az network public-ip show \
    --resource-group myResourceGroupVMSS \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

Immettere l'indirizzo IP pubblico in un Web browser. Verrà visualizzata l'app, con il nome host della VM a cui il servizio di bilanciamento del carico ha distribuito il traffico:

![Esecuzione dell'app Node.js](./media/tutorial-load-balance-nodejs/running-nodejs-app.png)

Eseguire l'aggiornamento del Web browser per verificare la distribuzione del traffico da parte del bilanciamento del carico tra tutte le macchine virtuali del set di scalabilità che eseguono l'app.


## <a name="step-6---management-tasks"></a>Passaggio 6: attività di gestione
Nel ciclo di vita del set di scalabilità, potrebbe essere necessario eseguire una o più attività di gestione. Si potrebbe anche voler creare script per automatizzare le attività di ciclo di vita. L'interfaccia della riga di comando di Azure 2.0 offre un modo rapido per eseguire tali operazioni. Di seguito vengono illustrate alcune attività comuni.

### <a name="increase-or-decrease-vm-instances"></a>Aumentare o diminuire le istanze delle macchine virtuali
È possibile aumentare o ridurre manualmente il numero di macchine virtuali nel set di scalabilità con [az vmss scale](/cli/azure/vmss#scale). L'esempio seguente aumenta il numero di macchine virtuali del set di scalabilità a `5`:

```azurecli
az vmss scale --resource-group myResourceGroupVMSS --name myScaleSet --new-capacity 5
```

Le regole di scalabilità automatica consentono di definire come aumentare o ridurre il numero di macchine virtuali del set di scalabilità in base alla domanda, ad esempio il traffico di rete o l'utilizzo della CPU. Non è attualmente possibile impostare queste regole nell'interfaccia della riga di comando di Azure 2.0. Usare il [Portale di Azure](https://portal.azure.com) per configurare la scalabilità automatica.

### <a name="get-connection-info"></a>Ottenere informazioni sulla connessione
Per ottenere informazioni sulla connessione delle macchine virtuali nel set di scalabilità, usare [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info). Questo comando restituisce l'indirizzo IP pubblico e le porte per ogni macchina virtuale a cui consente di connettersi a SSH:

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroupVMSS --name myScaleSet
```

### <a name="delete-resource-group"></a>Eliminare un gruppo di risorse
Se si elimina un gruppo di risorse, vengono eliminate anche tutte le risorse in esso contenute.

```azurecli
az group delete --name myResourceGroupVMSS
```


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione viene definita l'app Web con **cloud-init** e viene configurata ogni macchina virtuale durante la distribuzione. Per informazioni sull'acquisizione di una macchina virtuale da usare come immagine di origine nel set di scalabilità, vedere [Come generalizzare e acquisire una macchina virtuale Linux](capture-image.md).

Per altre informazioni su alcune funzionalità del set di scalabilità di macchine virtuali illustrate in questa esercitazione, vedere le informazioni seguenti:

- [Informazioni sui set di scalabilità di macchine virtuali in Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)
- [Panoramica di Azure Load Balancer](../../load-balancer/load-balancer-overview.md)
- [Controllare il flusso del traffico di rete con i gruppi di sicurezza di rete](../../virtual-network/virtual-networks-nsg.md)
