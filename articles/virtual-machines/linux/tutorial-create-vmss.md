---
title: "Creare un set di scalabilità di macchine virtuali Linux in Azure | Microsoft Docs"
description: "Creare e distribuire un'applicazione a disponibilità elevata in macchine virtuali Linux usando un set di scalabilità di macchine virtuali"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: 8703d0c06f2507cc3c21d4280d887a8772145a28
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-linux"></a>Creare un set di scalabilità di macchine virtuali e distribuire un'app a disponibilità elevata in Linux
Un set di scalabilità di macchine virtuali consente di distribuire e gestire un set di macchine virtuali identiche con scalabilità automatica. È possibile ridimensionare manualmente il numero di VM nel set di scalabilità o definire regole di scalabilità automatica in base all'utilizzo delle risorse, ad esempio la CPU, alla richiesta di memoria o al traffico di rete. In questa esercitazione viene distribuito un set di scalabilità di macchine virtuali in Azure. Si apprenderà come:

> [!div class="checklist"]
> * Usare cloud-init per creare un'app per la scalabilità
> * Creare un set di scalabilità di macchine virtuali
> * Aumentare o diminuire il numero di istanze in un set di scalabilità
> * Creare regole di scalabilità automatica
> * Visualizzare le informazioni di connessione per le istanze del set di scalabilità
> * Usare dischi di dati in un set di scalabilità


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e utilizzare l'interfaccia CLI in locale, questa esercitazione, è necessario che sia in esecuzione l'interfaccia CLI di Azure versione 2.0.22 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="scale-set-overview"></a>Informazioni generali sui set di scalabilità
Un set di scalabilità di macchine virtuali consente di distribuire e gestire un set di macchine virtuali identiche con scalabilità automatica. Le macchine virtuali di un set di scalabilità vengono distribuite in domini logici di errore e di aggiornamento in uno o più *gruppi di posizionamento*. Si tratta di gruppi di VM configurate in modo simile, analoghi ai [set di disponibilità](tutorial-availability-sets.md).

Le VM vengono create in base alle esigenze in un set di scalabilità. È possibile definire regole di scalabilità automatica per controllare le modalità e i tempi di aggiunta e rimozione delle VM dal set di scalabilità. Queste regole possono essere attivate in base alle metriche, ad esempio carico della CPU, utilizzo della memoria o il traffico di rete.

I set di scalabilità supportano fino a 1000 macchine virtuali quando si usa un'immagine della piattaforma Azure. Per i carichi di lavoro con requisiti significativi di installazione o personalizzazione di VM, si consiglia di [creare un'immagine di VM personalizzata](tutorial-custom-images.md). È possibile creare fino a 300 macchine virtuali in un set di scalabilità quando si usa un'immagine personalizzata.


## <a name="create-an-app-to-scale"></a>Creare un'app per la scalabilità
Per l'uso in ambiente di produzione, è opportuno [creare un'immagine di macchina virtuale personalizzata](tutorial-custom-images.md) che includa l'applicazione installata e configurata. Per questa esercitazione si esegue la personalizzazione delle macchine virtuali al primo avvio per verificare rapidamente il funzionamento di un set di scalabilità.

In un'esercitazione precedente, [How to customize a Linux virtual machine on first boot](tutorial-automate-vm-deployment.md), è stato descritto come personalizzare una macchina virtuale al primo avvio con cloud-init. È possibile usare lo stesso file di configurazione cloud-init per installare NGINX ed eseguire una semplice app Node.js "Hello World". 

Nella shell corrente creare un file denominato *cloud-init.txt* e incollare la configurazione seguente. Ad esempio, creare il file in Cloud Shell anziché nel computer locale. Immettere `sensible-editor cloud-init.txt` per creare il file e visualizzare un elenco degli editor disponibili. Assicurarsi che l'intero file cloud-init venga copiato correttamente, in particolare la prima riga:

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


## <a name="create-a-scale-set"></a>Creare un set di scalabilità
Per poter creare un set di scalabilità, è prima necessario creare un gruppo di risorse con il comando [az group create](/cli/azure/group#create). Nell'esempio seguente viene creato un gruppo di risorse denominato *myResourceGroupScaleSet* nella posizione *eastus*:

```azurecli-interactive 
az group create --name myResourceGroupScaleSet --location eastus
```

Si può ora creare un set di scalabilità di macchine virtuali con il comando [az vmss create](/cli/azure/vmss#create). Nell'esempio seguente viene creato un set di scalabilità denominato *myScaleSet*, viene usato il file cloud-int per personalizzare la VM e vengono generate le chiavi SSH, se non sono presenti:

```azurecli-interactive 
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```

La creazione e la configurazione di tutte le macchine virtuali e risorse del set di scalabilità richiedono alcuni minuti. Sono presenti attività in background la cui esecuzione continua dopo che l'interfaccia della riga di comando di Azure è tornata al prompt. Potrebbe trascorrere ancora qualche minuto prima che sia possibile accedere all'app.


## <a name="allow-web-traffic"></a>Consentire il traffico Web
Un bilanciamento del carico è stato creato automaticamente come parte del set di scalabilità di macchine virtuali. Il bilanciamento del carico distribuisce il traffico ad un set di macchine virtuali definite usando le proprie regole. Altre informazioni sui concetti di bilanciamento del carico e sulla configurazione saranno illustrate nella prossima esercitazione, [Come bilanciare il carico di macchine virtuali in Azure](tutorial-load-balancer.md).

Per consentire al traffico di raggiungere l'app Web, creare una regola con il comando [az network lb rule create](/cli/azure/network/lb/rule#create). Nell'esempio seguente viene creata una regola denominata *myLoadBalancerRuleWeb*:

```azurecli-interactive 
az network lb rule create \
  --resource-group myResourceGroupScaleSet \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="test-your-app"></a>Test dell'app
Per visualizzare l'app Node.js sul Web, ottenere l'indirizzo IP pubblico del bilanciamento del carico con il comando [az network public-ip show](/cli/azure/network/public-ip#show). Nell'esempio seguente si ottiene l'indirizzo IP per *myScaleSetLBPublicIP* creato come parte del set di scalabilità:

```azurecli-interactive 
az network public-ip show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

Immettere l'indirizzo IP pubblico in un Web browser. Verrà visualizzata l'app, con il nome host della VM a cui il servizio di bilanciamento del carico ha distribuito il traffico:

![Esecuzione dell'app Node.js](./media/tutorial-create-vmss/running-nodejs-app.png)

Per verificare il funzionamento del set di scalabilità, è possibile imporre l'aggiornamento del Web browser per visualizzare la distribuzione del traffico da parte del bilanciamento del carico tra tutte le macchine virtuali che eseguono l'app.


## <a name="management-tasks"></a>Attività di gestione
Nel ciclo di vita del set di scalabilità, potrebbe essere necessario eseguire una o più attività di gestione. Si potrebbe anche voler creare script per automatizzare le attività di ciclo di vita. L'interfaccia della riga di comando di Azure 2.0 offre un modo rapido per eseguire tali operazioni. Di seguito vengono illustrate alcune attività comuni.

### <a name="view-vms-in-a-scale-set"></a>Visualizzare le macchine virtuali in un set di scalabilità
Per visualizzare un elenco di macchine virtuali in esecuzione nel set di scalabilità, usare il comando [az vmss list-instances](/cli/azure/vmss#list-instances) come indicato di seguito:

```azurecli-interactive 
az vmss list-instances \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --output table
```

L'output è simile all'esempio seguente:

```azurecli-interactive 
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup            VmId
------------  --------------------  ----------  ------------  -------------------  -----------------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUPSCALESET  c72ddc34-6c41-4a53-b89e-dd24f27b30ab
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUPSCALESET  44266022-65c3-49c5-92dd-88ffa64f95da
```


### <a name="increase-or-decrease-vm-instances"></a>Aumentare o diminuire le istanze delle macchine virtuali
Per visualizzare il numero di istanze attualmente presenti in un set di scalabilità, usare il comando [az vmss show](/cli/azure/vmss#show) ed eseguire una query su *sku.capacity*:

```azurecli-interactive 
az vmss show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

È possibile aumentare o ridurre manualmente il numero di macchine virtuali nel set di scalabilità con il comando [az vmss scale](/cli/azure/vmss#scale). Nell'esempio seguente imposta il numero di macchine virtuali nella scala impostata su *3*:

```azurecli-interactive 
az vmss scale \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --new-capacity 3
```


### <a name="configure-autoscale-rules"></a>Configurare le regole di scalabilità automatica
Invece di ridimensionare manualmente il numero di istanze del set di scalabilità, è possibile definire regole di scalabilità automatica. Queste regole monitorano le istanze nel set di scalabilità e rispondono di conseguenza in base alle metriche e alle soglie definite. L'esempio seguente aumenta il numero di istanze di uno quando il carico della CPU medio è maggiore del 60% per un periodo di 5 minuti. Se il carico della CPU medio scende poi sotto il 30% per un periodo di 5 minuti, le istanze vengono ridotte di una. L'ID della sottoscrizione viene usato per creare gli URI delle risorse per i diversi componenti del set di scalabilità. Per creare queste regole con [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#create), copiare e incollare il profilo del comando di scalabilità automatica seguente:

```azurecli-interactive 
sub=$(az account show --query id -o tsv)

az monitor autoscale-settings create \
    --resource-group myResourceGroupScaleSet \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "East US",
      "notifications": [],
      "profiles": [
        {
          "name": "Auto created scale condition",
          "capacity": {
            "minimum": "2",
            "maximum": "10",
            "default": "2"
          },
          "rules": [
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet",
                "metricResourceLocation": "eastus",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "ChangeCount",
                "value": "1",
                "cooldown": "PT5M"
              }
            },
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet",
                "metricResourceLocation": "eastus",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "ChangeCount",
                "value": "1",
                "cooldown": "PT5M"
              }
            }
          ]
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
    }'
```

Per usare di nuovo il profilo di scalabilità automatica, è possibile creare un file JSON (JavaScript Object Notation) e passarlo al comando `az monitor autoscale-settings create` con il parametro `--parameters @autoscale.json`. Per altre informazioni di progettazione sull'uso della scalabilità automatica, vedere [Procedure consigliate per la scalabilità automatica](/azure/architecture/best-practices/auto-scaling).


### <a name="get-connection-info"></a>Ottenere informazioni sulla connessione
Per ottenere informazioni sulla connessione delle macchine virtuali nel set di scalabilità, usare [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info). Questo comando restituisce l'indirizzo IP pubblico e la porta per ogni macchina virtuale che consente la connessione con SSH:

```azurecli-interactive 
az vmss list-instance-connection-info \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet
```


## <a name="use-data-disks-with-scale-sets"></a>Usare dischi di dati con set di scalabilità
È possibile creare e usare dischi di dati con set di scalabilità. Nell'esercitazione precedente si è appreso come [gestire i dischi di Azure](tutorial-manage-disks.md), con le procedure consigliate e i miglioramenti delle prestazioni per la creazione di applicazioni su dischi di dati piuttosto che sul disco del sistema operativo.

### <a name="create-scale-set-with-data-disks"></a>Creare un set di scalabilità con dischi di dati
Per creare un set di scalabilità e collegare dischi di dati, aggiungere il parametro `--data-disk-sizes-gb` al comando [az vmss create](/cli/azure/vmss#create). Nell'esempio seguente viene creato un set di scalabilità con dischi di dati da *50* Gb collegati a ogni istanza:

```azurecli-interactive 
az vmss create \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetDisks \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --custom-data cloud-init.txt \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50
```

Quando le istanze vengono rimosse da un set di scalabilità, vengono rimossi anche tutti i dischi di dati collegati.

### <a name="add-data-disks"></a>Aggiungere dischi di dati
Per aggiungere un disco di dati per le istanze nel set di scalabilità, usare [az vmss disk attach](/cli/azure/vmss/disk#attach). Nell'esempio seguente viene aggiunto un disco di dati da *50* Gb a ogni istanza:

```azurecli-interactive 
az vmss disk attach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --size-gb 50 \
    --lun 2
```

### <a name="detach-data-disks"></a>Scollegare dischi di dati
Per rimuovere un disco di dati per le istanze nel set di scalabilità, usare [az vmss disk detach](/cli/azure/vmss/disk#detach). Nell'esempio seguente viene rimosso il disco di dati del LUN *2* da ogni istanza:

```azurecli-interactive 
az vmss disk detach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --lun 2
```


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato creato un set di scalabilità di macchine virtuali. Si è appreso come:

> [!div class="checklist"]
> * Usare cloud-init per creare un'app per la scalabilità
> * Creare un set di scalabilità di macchine virtuali
> * Aumentare o diminuire il numero di istanze in un set di scalabilità
> * Creare regole di scalabilità automatica
> * Visualizzare le informazioni di connessione per le istanze del set di scalabilità
> * Usare dischi di dati in un set di scalabilità

Passare all'esercitazione successiva per maggiori informazioni sui concetti di bilanciamento del carico per le macchine virtuali.

> [!div class="nextstepaction"]
> [Bilanciare il carico delle macchine virtuali](tutorial-load-balancer.md)
