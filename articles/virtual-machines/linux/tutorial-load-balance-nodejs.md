---
title: "Esercitazione: Creare un&quot;applicazione a disponibilità elevata nelle VM di Azure | Microsoft Docs"
description: "Informazioni su come creare un&quot;applicazione sicura e a disponibilità elevata in tre VM Linux con un servizio di bilanciamento del carico in Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 65ff0e03af6b43478f645274adfeb46d28418894
ms.lasthandoff: 04/07/2017

---

# <a name="build-a-load-balanced-highly-available-application-on-linux-virtual-machines-in-azure"></a>Creare un'applicazione con un servizio di bilanciamento del carico a disponibilità elevata in macchine virtuali Linux in Azure
In questa esercitazione viene creata un'applicazione a disponibilità elevata resiliente agli eventi di manutenzione. L'app usa un servizio di bilanciamento del carico, un set di disponibilità e tre macchine virtuali (VM) Linux. Questa esercitazione crea un'app Node.js, ma può essere usata per distribuire un diverso framework applicazioni sfruttando le stesse linee guida e gli stessi componenti di disponibilità elevata.

## <a name="step-1---azure-prerequisites"></a>Passaggio 1: Prerequisiti di Azure
Per completare questa esercitazione, aprire una finestra del terminale e verificare di aver installato l'ultima versione dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). Se non è già stato effettuato l'accesso alla sottoscrizione di Azure, accedere con [az login](/cli/azure/#login) e seguire le indicazioni visualizzate:

```azurecli
az login
```

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Per poter creare qualsiasi altra risorsa di Azure, è prima necessario creare un gruppo di risorse con [az group create](/cli/azure/group#create). Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella località `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```


## <a name="step-2---create-availability-set"></a>Passaggio 2: Creare un set di disponibilità
Le macchine virtuali possono essere create in domini logici di errore e di aggiornamento. Ogni dominio logico rappresenta una parte dell'hardware del data center di Azure sottostante. Quando si creano due o più VM, le risorse di calcolo e di archiviazione vengono distribuite in tali domini. Questa distribuzione assicura la disponibilità dell'app in caso di manutenzione di un componente hardware. I set di disponibilità consentono di definire questi domini logici di errore e di aggiornamento.

Creare un set di disponibilità con [az vm availability-set create](/cli/azure/vm/availability-set#create). Nell'esempio seguente viene creato un set di disponibilità chiamato `myAvailabilitySet`:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 3 \
    --platform-update-domain-count 2
```


## <a name="step-3---create-load-balancer"></a>Passaggio 3: Creare il servizio di bilanciamento del carico
Un servizio di bilanciamento del carico di Azure distribuisce il traffico tra un set di VM definite usando regole di bilanciamento del carico. Un probe di integrità monitora una determinata porta in ogni VM e distribuisce il traffico solo a una VM operativa.

### <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico
Per accedere all'app in Internet, assegnare un indirizzo IP pubblico al servizio di bilanciamento del carico. Creare un indirizzo IP pubblico con [az network public-ip create](/cli/azure/public-ip#create). L'esempio seguente crea un indirizzo IP pubblico denominato `myPublicIP`:

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP
```

### <a name="create-a-load-balancer"></a>Creare un servizio di bilanciamento del carico
Creare un servizio di bilanciamento del carico con [az network lb create](/cli/azure/network/lb#create). L'esempio seguente crea un servizio di bilanciamento del carico denominato `myLoadBalancer` con l'indirizzo `myPublicIP`:

```azurecli
az network lb create \
    --resource-group myResourceGroup \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>Creare un probe di integrità
Per consentire al servizio di bilanciamento del carico di monitorare lo stato dell'app, si usa un probe di integrità. Il probe di integrità aggiunge o rimuove in modo dinamico le VM nella rotazione del servizio di bilanciamento del carico in base alla rispettiva risposta ai controlli di integrità. Per impostazione predefinita, una VM viene rimossa dalla distribuzione del servizio di bilanciamento del carico dopo due errori consecutivi a intervalli di 15 secondi. Un probe di integrità viene creato in base a un protocollo o una specifica pagina di controllo integrità per l'app. L'esempio seguente crea un probe TCP, ma può essere esteso aggiungendo ad esempio `--path healthcheck.js`. È necessario creare `healthcheck.js`, che dovrà restituire la risposta **HTTP 200 OK** affinché il servizio di bilanciamento del carico mantenga l'host nella rotazione.

Creare un probe di integrità con [az network lb probe create](/cli/azure/network/lb/probe#create). L'esempio seguente crea un probe di integrità denominato `myHealthProbe` che monitora ogni VM:

```azurecli
az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico
Una regola di bilanciamento del carico consente di definire come il traffico verrà distribuito alle VM.

Creare una regola di bilanciamento del carico con [az network lb rule create](/cli/azure/network/lb/rule#create). L'esempio seguente crea una regola denominata `myLoadBalancerRule`, usa il probe di integrità `myHealthProbe` e bilancia il traffico sulla porta `80`:

```azurecli
az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="step-4---configure-networking"></a>Passaggio 4: Configurare la rete
Ogni VM ha una o più schede di interfaccia di rete (NIC) virtuali per la connessione a una rete virtuale. Tale rete virtuale è protetta in modo da filtrare il traffico in base alle regole di accesso definite.

### <a name="create-a-virtual-network"></a>Crea rete virtuale
Per fornire la connettività di rete alle VM, creare una rete virtuale con [az network vnet create](/cli/azure/vnet#create). L'esempio seguente creata una rete virtuale denominata `myVnet` con una subnet denominata `mySubnet`:

```azurecli
az network vnet create --resource-group myResourceGroup --name myVnet --subnet-name mySubnet
```

### <a name="configure-network-security"></a>Configurare la sicurezza di rete
I gruppi di sicurezza di rete usano regole per controllare il flusso del traffico da e verso le VM. Queste regole, ad esempio per consentire il traffico sulla porta 80, vengono definite dall'utente. Mentre il servizio di bilanciamento del carico distribuisce il traffico tra le VM, le regole dei gruppi di sicurezza di rete assicurano che possa passare solo il traffico consentito.

Creare un gruppo di sicurezza di rete con il comando [az network nsg create](/cli/azure/network/nsg#create). Nell'esempio seguente viene creato un gruppo di sicurezza di rete denominato `myNetworkSecurityGroup`:

```azurecli
az network nsg create --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Per consentire al traffico Web di raggiungere l'app, creare una regola del gruppo di sicurezza di rete con [az network nsg rule create](/cli/azure/network/nsg/rule#create). L'esempio seguente crea una regola del gruppo di sicurezza di rete denominata `myNetworkSecurityGroupRule`:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

### <a name="create-virtual-network-interface-cards"></a>Creare le schede di interfaccia di rete virtuali 
Per il funzionamento dei servizi di bilanciamento del carico viene usata la risorsa NIC virtuale anziché la VM effettiva. La scheda di interfaccia di rete virtuale è connessa al servizio di bilanciamento del carico e quindi collegata a una VM.

Creare una scheda di interfaccia di rete virtuale con [az network nic create](/cli/azure/network/nic#create). L'esempio seguente crea tre schede di interfaccia di rete virtuali, una per ogni VM creata per l'app nei passaggi successivi:

```bash
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroup \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```


## <a name="step-5---build-your-app"></a>Passaggio 5: Creare l'app
Un approccio largamente usato per personalizzare una VM è **cloud-init**. **cloud-init** consente di installare pacchetti e scrivere file. Dato che **cloud-init** viene eseguito durante la distribuzione iniziale, non sono necessari passaggi aggiuntivi per rendere operativa l'app. Il servizio di bilanciamento del carico inizia a distribuire il traffico quando la distribuzione della VM è completata e l'app è in esecuzione. Per altre informazioni sull'uso di **cloud-init**, vedere [Usare cloud-init per personalizzare una VM Linux durante la creazione](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

La configurazione di **cloud-init** seguente installa **nodejs** e **npm** e quindi installa e configura **nginx** come proxy Web per l'app. La configurazione crea anche una semplice app Node.js "Hello World" e quindi inizializza e avvia l'app con **Express**. Se si vuole usare un diverso framework applicazioni, modificare i pacchetti e l'applicazione distribuita di conseguenza.

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


## <a name="step-6---create-virtual-machines"></a>Passaggio 6: Creare le macchine virtuali
Dopo aver implementato tutti i componenti sottostanti, è possibile creare VM a disponibilità elevata per eseguire l'app.

### <a name="create-vms"></a>Creare VM
Creare le VM con [az vm create](/cli/azure/vm#create). L'esempio seguente crea tre VM e genera le chiavi SSH, se non sono già presenti:

```bash
for i in `seq 1 3`; do
    az vm create \
        --resource-group myResourceGroup \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image Canonical:UbuntuServer:14.04.4-LTS:latest \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```

La creazione e la configurazione di tutte e tre le VM richiedono alcuni minuti. Il probe di integrità del servizio di bilanciamento del carico rileva quando l'app è in esecuzione in ogni VM. Quando l'app è in esecuzione, la regola di bilanciamento del carico inizia a distribuire il traffico.

### <a name="test-your-app"></a>Test dell'app
Ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con [az network public-ip show](/cli/azure/network/public-ip#show). L'esempio seguente ottiene l'indirizzo IP `myPublicIP` creato in precedenza:

```azurecli
az network public-ip show \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

Immettere l'indirizzo IP pubblico in un Web browser. Verrà visualizzata l'app, con il nome host della VM a cui il servizio di bilanciamento del carico ha distribuito il traffico:

![Esecuzione dell'app Node.js](./media/tutorial-load-balance-nodejs/running-nodejs-app.png)

Forzare l'aggiornamento del Web browser per verificare la distribuzione del traffico tra tutte e tre le VM che eseguono l'app da parte del servizio di bilanciamento del carico.


## <a name="step-7---management-tasks"></a>Passaggio 7: Attività di gestione
Potrebbe essere necessario eseguire attività di manutenzione sulle VM che eseguono l'app, ad esempio per installare aggiornamenti del sistema operativo, oppure aggiungere altre VM per gestire un aumento del traffico verso l'app. Questa sezione illustra come rimuovere o aggiungere una VM nel servizio di bilanciamento del carico.

### <a name="remove-a-vm-from-the-load-balancer"></a>Rimuovere una VM dal servizio di bilanciamento del carico
Rimuovere una VM dal pool di indirizzi back-end con [az network nic ip-config address-pool remove](/cli/azure/network/nic/ip-config/address-pool#remove). L'esempio seguente rimuove la scheda di interfaccia di rete virtuale per **myVM2** da `myLoadBalancer`:

```azurecli
az network nic ip-config address-pool remove \
    --resource-group myResourceGroup \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

Forzare l'aggiornamento del Web browser per verificare la distribuzione del traffico tra le due restanti VM che eseguono l'app da parte del servizio di bilanciamento del carico. È ora possibile eseguire attività di manutenzione sulla VM, ad esempio installare aggiornamenti del sistema operativo o eseguire un riavvio della VM.

### <a name="add-a-vm-to-the-load-balancer"></a>Aggiungere una VM al servizio di bilanciamento del carico
Al termine della manutenzione di una VM o se è necessario espandere la capacità, aggiungere una VM al pool di indirizzi back-end con [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#add). L'esempio seguente aggiunge la scheda di interfaccia di rete virtuale per **myVM2** a `myLoadBalancer`:

```azurecli
az network nic ip-config address-pool add \
    --resource-group myResourceGroup \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```


## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione si basa un'infrastruttura di applicazioni a disponibilità elevata che usano singole risorse di Azure. È possibile anche usare set di scalabilità di macchine virtuali per aumentare o ridurre il numero di macchine virtuali che eseguono l'app. Continuare con l'esercitazione successiva [Create a highly available application on Linux with Virtual Machine Scale Sets](tutorial-convert-to-vmss.md) (Creare un'applicazione a disponibilità elevata in Linux usando i set di scalabilità delle macchine virtuali).

Per altre informazioni su alcune funzionalità di disponibilità elevata presentate in questa esercitazione, vedere le informazioni seguenti:

- [Gestire la disponibilità delle macchine virtuali Linux](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Panoramica di Azure Load Balancer](../../load-balancer/load-balancer-overview.md)
- [Controllare il flusso del traffico di rete con i gruppi di sicurezza di rete](../../virtual-network/virtual-networks-nsg.md)
- [Script di esempio dell'interfaccia della riga di comando di Azure](../windows/cli-samples.md)
