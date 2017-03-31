
---
title: Creare un ambiente Linux completo tramite l&quot;interfaccia della riga di comando di Azure 1.0 | Documentazione Microsoft
description: Usare l&quot;interfaccia della riga di comando 1.0 di Azure per creare da zero una risorsa di archiviazione, una VM di Linux, una rete virtuale con subnet, il bilanciamento del carico, una scheda di interfaccia di rete, un IP pubblico e un gruppo di sicurezza di rete.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: e8ee93cacba552bc7a6ec559dd8aa1fb773fadc2
ms.lasthandoff: 03/21/2017


---
# <a name="create-a-complete-linux-environment-with-the-azure-cli-10"></a>Creare un ambiente Linux completo tramite l'interfaccia della riga di comando di Azure 1.0
In questo articolo viene spiegato come creare una semplice rete con un servizio di bilanciamento del carico e un paio di VM utili per lo sviluppo e per calcoli semplici. Viene presentato in dettaglio l'intero processo, comando dopo comando, fino a creare due VM Linux funzionanti e sicure a cui è possibile connettersi da qualsiasi posizione in Internet. Si potrà quindi passare a reti e ambienti più complessi.

Durante il processo si apprenderanno informazioni sulla gerarchia delle dipendenze fornita dal modello di distribuzione di Resource Manager e sulle potenzialità che offre. Dopo aver osservato come viene realizzato il sistema, è possibile ricrearlo molto più rapidamente utilizzando i [modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). In più, dopo aver scoperto come interagiscono le parti dell'ambiente, diventa più facile creare modelli per automatizzarle.

Nell'ambiente sono presenti:

* Due VM all'interno di un set di disponibilità.
* Un servizio di bilanciamento del carico con una regola di bilanciamento del carico sulla porta 80.
* Regole del gruppo di sicurezza di rete (NSG) per proteggere la VM dal traffico indesiderato.

![Panoramica sull'ambiente di base](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

Per creare questo ambiente personalizzato, è necessario aver installato l'[interfaccia della riga di comando di Azure 1.0](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) in modalità Resource Manager (`azure config mode arm`). È inoltre necessario uno strumento di analisi JSON. In questo esempio viene utilizzato [jq](https://stedolan.github.io/jq/).


## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- [Interfaccia della riga di comando di Azure 1.0](#quick-commands): l'interfaccia della riga di comando per i modelli di distribuzione classica e di gestione delle risorse (questo articolo)
- [Interfaccia della riga di comando di Azure 2.0](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): interfaccia della riga di comando di prossima generazione per il modello di distribuzione di Gestione risorsa


## <a name="quick-commands"></a>Comandi rapidi
Se si vuole eseguire rapidamente l'attività, la sezione seguente indica in dettaglio i comandi base per caricare una VM in Azure. Altre informazioni dettagliate e il contesto per ogni passaggio sono disponibili nelle sezioni successive del documento, a partire da [qui](#detailed-walkthrough).

Controllare di aver effettuato l'accesso tramite l'[interfaccia della riga di comando di Azure 1.0](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e di usare la modalità Resource Manager:

```azurecli
azure config mode arm
```

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono `myResourceGroup`, `mystorageaccount` e `myVM`.

Creare il gruppo di risorse. Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella località `westeurope`:

```azurecli
azure group create -n myResourceGroup -l westeurope
```

Verificare il gruppo di risorse utilizzando il parser JSON:

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Creare l'account di archiviazione. Nell'esempio seguente viene creato un nuovo account di archiviazione denominato `mystorageaccount`. Il nome dell'account di archiviazione deve essere univoco; assegnare quindi all'account il proprio nome.

```azurecli
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Verificare l'account di archiviazione utilizzando il parser JSON:

```azurecli
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Creare la rete virtuale. Nell'esempio seguente viene creata una rete virtuale chiamata `myVnet`:

```azurecli
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Creare una subnet. Nell'esempio seguente viene creata una subnet denominata `mySubnet`:

```azurecli
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

Verificare la rete virtuale e la subnet utilizzando il parser JSON:

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Creare un indirizzo IP pubblico. Nell'esempio seguente viene aggiunto un indirizzo IP pubblico chiamato `myPublicIP` con il nome DNS `mypublicdns`. Il nome DNS deve essere univoco, quindi specificare il proprio nome DNS.

```azurecli
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Creare il servizio di bilanciamento del carico. Nell'esempio seguente viene creato un servizio di bilanciamento del carico chiamato `myLoadBalancer`:

```azurecli
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Creare un pool di indirizzi IP front-end per il servizio di bilanciamento del carico e associare l'IP pubblico. Nell'esempio seguente viene creato un pool di indirizzi IP front-end chiamato `mySubnetPool`:

```azurecli
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool
```

Creare un pool di indirizzi IP back-end per il servizio di bilanciamento del carico. Nell'esempio seguente viene creato un pool di indirizzi IP back-end chiamato `myBackEndPool`:

```azurecli
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Creare regole Network Address Translation (NAT) in ingresso SSH per il servizio di bilanciamento del carico. Nell'esempio seguente vengono create due regole per il servizio di bilanciamento del carico chiamate `myLoadBalancerRuleSSH1` e `myLoadBalancerRuleSSH2`:

```azurecli
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Creare le regole NAT in ingresso Web per il servizio di bilanciamento del carico. Nell'esempio seguente viene creata una regola per il servizio di bilanciamento del carico denominata `myLoadBalancerRuleWeb`:

```azurecli
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Creare il probe di integrità per il servizio di bilanciamento del carico. Nell'esempio seguente viene creato un probe TCP chiamato `myHealthProbe`:

```azurecli
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Verificare il servizio di bilanciamento del carico, il pool di indirizzi IP e le regole NAT utilizzando il parser JSON:

```azurecli
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Creare la prima scheda di interfaccia di rete. Sostituire le sezioni `#####-###-###` con il proprio ID sottoscrizione di Azure. L'ID sottoscrizione è indicato nell'output di **jq** quando si esaminano le risorse che si sta creando. È anche possibile visualizzare l'ID sottoscrizione con `azure account list`.

Nell'esempio seguente viene creata una scheda di interfaccia di rete chiamata `myNic1`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Creare la seconda scheda di interfaccia di rete. Nell'esempio seguente viene creata una scheda di interfaccia di rete chiamata `myNic2`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Verificare le due schede di interfaccia di rete usando il parser JSON:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Creare il gruppo di sicurezza di rete. Nell'esempio seguente viene creato un gruppo di sicurezza di rete denominato `myNetworkSecurityGroup`:

```azurecli
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Aggiungere due regole in ingresso per il gruppo di sicurezza di rete. Nell'esempio seguente vengono create due regole chiamate `myNetworkSecurityGroupRuleSSH` e `myNetworkSecurityGroupRuleHTTP`:

```azurecli
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Verificare il gruppo di sicurezza di rete e le regole in ingresso usando il parser JSON:

```azurecli
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Associare il gruppo di sicurezza di rete alle due schede di interfaccia di rete:

```azurecli
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Creare il set di disponibilità. Nell'esempio seguente viene creato un set di disponibilità chiamato `myAvailabilitySet`:

```azurecli
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Creare la prima VM Linux. Nell'esempio seguente viene creata una VM chiamata `myVM1`:

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic1 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Creare la seconda VM Linux. Nell'esempio seguente viene creata una VM chiamata `myVM2`:

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic2 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Utilizzare il parser JSON per verificare tutto ciò che è stato creato:

```azurecli
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Esportare il nuovo ambiente in un modello per ricreare rapidamente nuove istanze:

```azurecli
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Procedura dettagliata
La procedura dettagliata seguente illustra il funzionamento di ciascun comando durante la creazione dell'ambiente. Questi concetti torneranno utili in fase di creazione degli ambienti personalizzati per lo sviluppo o per la produzione.

Controllare di aver effettuato l'accesso tramite l'[interfaccia della riga di comando di Azure 1.0](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e di usare la modalità Resource Manager:

```azurecli
azure config mode arm
```

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono `myResourceGroup`, `mystorageaccount` e `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Creare i gruppi di risorse e scegliere i percorsi di distribuzione
I gruppi di risorse di Azure sono entità di distribuzione logiche che contengono informazioni di configurazione e altri metadati per abilitare la gestione logica delle distribuzioni di risorse. Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella località `westeurope`:

```azurecli
azure group create --name myResourceGroup --location westeurope
```

Output:

```azurecli                        
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione
Sono necessari account di archiviazione per i dischi della macchina virtuale e per eventuali altri dischi dati che si desidera aggiungere. Si creano account di archiviazione quasi subito dopo la creazione di gruppi di risorse.

Viene usato il comando `azure storage account create` , passando il percorso dell'account, il gruppo di risorse che lo controlla e il tipo di supporto di archiviazione desiderato. Nell'esempio seguente viene creato un nuovo account di archiviazione denominato `mystorageaccount`:

```azurecli
azure storage account create \  
  --location westeurope \
  --resource-group myResourceGroup \
  --kind Storage --sku-name GRS \
  mystorageaccount
```

Output:

```azurecli
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
```

Per esaminare il gruppo di risorse con il comando `azure group show`, usare lo strumento [jq](https://stedolan.github.io/jq/) insieme all'opzione `--json` dell'interfaccia della riga di comando di Azure. È possibile utilizzare **jsawk** o qualsiasi altra libreria di linguaggio per l'analisi del JSON.

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Output:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Per esaminare l'account di archiviazione usando l'interfaccia della riga di comando, è necessario innanzitutto impostare le chiavi e i nomi dell'account. Sostituire il nome dell'account di archiviazione nell'esempio seguente con un nome a scelta:

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Dopodiché è possibile visualizzare facilmente le informazioni di archiviazione:

```azurecli
azure storage container list
```

Output:

```azurecli
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## <a name="create-a-virtual-network-and-subnet"></a>Creare una rete virtuale e una subnet
Dopodiché è necessario creare una rete virtuale che funziona in Azure e una subnet in cui poter creare le VM. Nell'esempio seguente viene creata una rete virtuale chiamata `myVnet` con il prefisso dell'indirizzo `192.168.0.0/16`:

```azurecli
azure network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefixes 192.168.0.0/16
```

Output:

```azurecli
info:    Executing command network vnet create
+ Looking up virtual network "myVnet"
+ Creating virtual network "myVnet"
+ Loading virtual network state
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet
data:    Name                            : myVnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

Anche in questo caso osservare come vengono realizzate le risorse mediante l'opzione --json di `azure group show` e `jq`. Ora sono disponibili una risorsa `storageAccounts` e una risorsa `virtualNetworks`.  

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Output:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
      "name": "myVnet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Viene quindi creata una subnet all'interno della rete virtuale `myVnet` in cui vengono distribuite le VM. Usare il comando `azure network vnet subnet create` insieme alle risorse create in precedenza: il gruppo di risorse `myResourceGroup` e la rete virtuale `myVnet`. Nell'esempio seguente viene aggiunta una subnet chiamata `mySubnet` con il prefisso dell'indirizzo della subnet `192.168.1.0/24`:

```azurecli
azure network vnet subnet create --resource-group myResourceGroup \
  --vnet-name myVnet --name mySubnet --address-prefix 192.168.1.0/24
```

Output:

```azurecli
info:    Executing command network vnet subnet create
+ Looking up the subnet "mySubnet"
+ Creating subnet "mySubnet"
+ Looking up the subnet "mySubnet"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : mySubnet
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Dato che la subnet è logicamente all'interno della rete virtuale, le informazioni relative alla subnet vengono cercate con un comando leggermente diverso. Il comando usato è `azure network vnet show`, ma si continua a esaminare l'output JSON mediante lo strumento `jq`.

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Output:

```json
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "mySubnet",
      "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "name": "myVnet",
  "location": "westeurope"
}
```

## <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico
A questo punto, creare l'indirizzo IP pubblico (PIP) che viene assegnato al bilanciamento del carico. Consente di connettersi alle VM da Internet tramite il comando `azure network public-ip create` . Dato che l'indirizzo predefinito è dinamico, si crea una voce DNS nel dominio **cloudapp.azure.com** usando l'opzione `--domain-name-label`. Nell'esempio seguente viene aggiunto un indirizzo IP pubblico chiamato `myPublicIP` con il nome DNS `mypublicdns`. Il nome DNS deve essere univoco, quindi specificare il proprio nome DNS.

```azurecli
azure network public-ip create --resource-group myResourceGroup \
  --location westeurope --name myPublicIP --domain-name-label mypublicdns
```

Output:

```azurecli
info:    Executing command network public-ip create
+ Looking up the public ip "myPublicIP"
+ Creating public ip address "myPublicIP"
+ Looking up the public ip "myPublicIP"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
data:    Name                            : myPublicIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : mypublicdns
data:    FQDN                            : mypublicdns.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

Anche l'indirizzo IP pubblico è una risorsa di primo livello che può essere visualizzata con `azure group show`.

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Output:

```json
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/myResourceGroup",
"name": "myResourceGroup",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "name": "myPublicIP",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
    "name": "myVnet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "name": "mystorageaccount",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

È possibile esaminare altri dettagli relativi alla risorsa, incluso il nome di dominio completo (FQDN) del sottodominio, usando il comando `azure network public-ip show` completo. La risorsa indirizzo IP pubblico è stata allocata in modo logico, ma non è ancora stato assegnato un indirizzo specifico. Per ottenere un indirizzo IP, sarà necessario un servizio di bilanciamento del carico, che non è ancora stato creato.

```azurecli
azure network public-ip show myResourceGroup myPublicIP --json | jq '.'
```

Output:

```json
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "mypublicdns",
    "fqdn": "mypublicdns.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/\"c63154b3-1130-49b9-a887-877d74d5ebc5\"",
"id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
"name": "myPublicIP",
"location": "westeurope"
}
```

## <a name="create-a-load-balancer-and-ip-pools"></a>Creare un servizio di bilanciamento del carico e i pool di indirizzi IP
Quando si crea un bilanciamento del carico, questo consente di distribuire il traffico tra più VM. Fornisce anche ridondanza all'applicazione grazie all'esecuzione di più VM che rispondono alle richieste degli utenti in caso di manutenzione o carichi elevati. Nell'esempio seguente viene creato un servizio di bilanciamento del carico chiamato `myLoadBalancer`:

```azurecli
azure network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer
```

Output:

```azurecli
info:    Executing command network lb create
+ Looking up the load balancer "myLoadBalancer"
+ Creating load balancer "myLoadBalancer"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer
data:    Name                            : myLoadBalancer
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

Il servizio di bilanciamento del carico è piuttosto vuoto, per cui si creeranno alcuni pool di indirizzi IP. Si creeranno due pool di indirizzi IP per il servizio di bilanciamento del carico, uno per il front-end e uno per il back-end. Il pool di IP front-end è visibile pubblicamente. Rappresenta anche il percorso a cui si assegnano i PIP creati in precedenza. Dopodiché, si usa il pool di back-end come un percorso per le VM a cui connettersi. In questo modo, il traffico può fluire attraverso il bilanciamento del carico verso le VM.

Prima di tutto si creerà il pool di indirizzi IP front-end. Nell'esempio seguente viene creato un pool di indirizzi IP front-end chiamato `myFrontEndPool`:

```azurecli
azure network lb frontend-ip create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --public-ip-name myPublicIP \
  --name myFrontEndPool
```

Output:

```azurecli
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "myLoadBalancer"
+ Looking up the public ip "myPublicIP"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
info:    network lb mySubnet-ip create command OK
```

Si noti che è stata usata l'opzione `--public-ip-name` per passare il valore `myPublicIP` creato in precedenza. L'assegnazione dell'indirizzo IP pubblico al servizio di bilanciamento del carico consente di raggiungere le VM su Internet.

Quindi si creerà il secondo pool di indirizzi IP, questa volta per il traffico back-end. Nell'esempio seguente viene creato un pool di indirizzi IP back-end chiamato `myBackEndPool`:

```azurecli
azure network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Output:

```azurecli
info:    Executing command network lb address-pool create
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Per vedere il servizio di bilanciamento del carico è possibile usare `azure network lb show` ed esaminare l'output JSON:

```azurecli
azure network lb show myResourceGroup myLoadBalancer --json | jq '.'
```

Output:

```json
{
  "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## <a name="create-load-balancer-nat-rules"></a>Creare le regole NAT per il servizio di bilanciamento del carico
Affinché il traffico attraversi il servizio di bilanciamento del carico, è necessario creare le regole Network Address Translation (NAT) che specificano le azioni in ingresso o in uscita. È possibile specificare il protocollo da usare, quindi eseguire il mapping delle porte esterne alle porte interne desiderate. Per questo ambiente si creeranno alcune regole che consentono il passaggio di SSH attraverso il servizio di bilanciamento del carico fino alle VM. Le porte TCP 4222 e 4223 vengono configurate in modo da puntare alla porta TCP 22 nelle VM, che vengono create successivamente. Nell'esempio seguente viene creata una regola chiamata `myLoadBalancerRuleSSH1` per eseguire il mapping della porta TCP 4222 sulla porta 22:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 \
  --protocol tcp --frontend-port 4222 --backend-port 22
```

Output:

```azurecli
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default mySubnet IP configuration "myFrontEndPool"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleSSH1
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Ripetere la procedura per la seconda regola NAT per SSH. Nell'esempio seguente viene creata una regola chiamata `myLoadBalancerRuleSSH2` per eseguire il mapping della porta TCP 4223 sulla porta 22:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Verrà anche creata una regola NAT per la porta TCP 80 per il traffico Web, collegando la regola ai pool di indirizzi IP. Se la regola viene associata a un pool IP, anziché essere associata singolarmente alle VM, è possibile aggiungere o rimuovere le VM dal pool di indirizzi IP. Il servizio di bilanciamento del carico regola automaticamente il flusso del traffico. Nell'esempio seguente viene creata una regola chiamata `myLoadBalancerRuleWeb` per eseguire il mapping della porta TCP 80 sulla porta 80:

```azurecli
azure network lb rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleWeb --protocol tcp \
  --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-address-pool-name myBackEndPool
```

Output:

```azurecli
info:    Executing command network lb rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleWeb
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
info:    network lb rule create command OK
```

## <a name="create-a-load-balancer-health-probe"></a>Creare un probe di integrità per il servizio di bilanciamento del carico
Un probe di integrità verifica periodicamente le VM che si trovano dietro il servizio di bilanciamento del carico per assicurarsi che siano operative e che rispondano alle richieste, come specificato. In caso contrario ne viene disabilitata l'operatività per fare in modo che gli utenti non vengano indirizzati a esse. È possibile definire controlli personalizzati per il probe di integrità, nonché gli intervalli e i valori di timeout. Per altre informazioni sui probe di integrità, vedere [Probe di integrità del servizio di bilanciamento del carico](../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Nell'esempio seguente viene creato un probe di integrità TCP chiamato `myHealthProbe`:

```azurecli
azure network lb probe create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myHealthProbe --protocol "tcp" \
  --interval 15 --count 4
```

Output:

```azurecli
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myHealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

In questo caso è stato specificato un intervallo di 15 secondi per i controlli di integrità. È consentito un massimo di quattro probe mancati (un minuto) prima che il bilanciamento del carico consideri l'host come non più funzionante.

## <a name="verify-the-load-balancer"></a>Verificare il bilanciamento del carico
La configurazione del bilanciamento del carico è completa. Ecco i passaggi effettuati:

1. È stato creato un bilanciamento del carico.
2. È stato creato un pool di indirizzi IP front-end a cui è stato assegnato un indirizzo IP pubblico.
3. È stato creato un pool IP back-end a cui possono connettersi le VM.
4. Sono state create le regole NAT che consentono alle macchine virtuali di usare il protocollo SSH per la gestione, oltre a una regola che consente all'app Web di usare la porta TCP 80.
5. È stato aggiunto un probe di integrità per verificare periodicamente lo stato delle VM. Questo probe di integrità garantisce agli utenti di accedere solo alle VM non più funzionanti o con contenuti non più disponibili.

Ecco il bilanciamento del carico risultante:

```azurecli
azure network lb show --resource-group myResourceGroup \
  --name myLoadBalancer --json | jq '.'
```

Output:

```json
{
  "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH1",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH2",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleWeb",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "mySubnetPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myHealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe"
    }
  ]
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Creare una scheda di interfaccia di rete da usare con la VM di Linux
Le schede di interfaccia di rete sono disponibili a livello di programmazione in quanto è possibile applicare delle regole di utilizzo. È inoltre possibile averne più di una. Nel comando `azure network nic create` seguente la scheda di interfaccia di rete viene associata al pool di indirizzi IP back-end del carico e alla regola NAT per consentire il traffico SSH.

Sostituire le sezioni `#####-###-###` con il proprio ID sottoscrizione di Azure. L'ID sottoscrizione è indicato nell'output di `jq` quando si esaminano le risorse che si sta creando. È anche possibile visualizzare l'ID sottoscrizione con `azure account list`.

Nell'esempio seguente viene creata una scheda di interfaccia di rete chiamata `myNic1`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 \
  --lb-address-pool-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
```

Output:

```azurecli
info:    Executing command network nic create
+ Looking up the subnet "mySubnet"
+ Looking up the network interface "myNic1"
+ Creating network interface "myNic1"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1
data:    Name                            : myNic1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
data:
info:    network nic create command OK
```

È possibile visualizzare i dettagli esaminando la risorsa direttamente. La risorsa viene esaminata con il comando `azure network nic show` :

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
```

Output:

```json
{
  "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1",
  "name": "myNic1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

Ora è possibile creare la seconda scheda di interfaccia di rete, collegandola di nuovo al pool IP back-end. Questa volta la seconda regola NAT consente il traffico SSH. Nell'esempio seguente viene creata una scheda di interfaccia di rete chiamata `myNic2`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Creare un gruppo di sicurezza di rete e le regole corrispondenti
Vengono ora creati il gruppo di sicurezza di rete e le regole in ingresso che regolano l'accesso alla scheda di interfaccia di rete. Per una scheda di interfaccia di rete o una subnet è possibile usare un gruppo di sicurezza di rete. Si definiscono regole per controllare il flusso del traffico da e verso le VM. Nell'esempio seguente viene creato un gruppo di sicurezza di rete denominato `myNetworkSecurityGroup`:

```azurecli
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Si aggiungerà la regola in ingresso per il gruppo di sicurezza di rete per consentire le connessioni in ingresso sulla porta 22 (per supportare SSH). Nell'esempio seguente viene creata una regola denominata `myNetworkSecurityGroupRuleSSH` per consentire il traffico TCP sulla porta 22:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Si aggiungerà la regola in ingresso per il gruppo di sicurezza di rete per consentire le connessioni in ingresso sulla porta 80 (per supportare il traffico Web). Nell'esempio seguente viene creata una regola denominata `myNetworkSecurityGroupRuleHTTP` per consentire il traffico TCP sulla porta 80:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [!NOTE]
> La regola in ingresso è un filtro per le connessioni di rete in ingresso. In questo esempio il gruppo di sicurezza di rete viene associato alla scheda di rete virtuale delle VM, di conseguenza qualsiasi richiesta per la porta 22 viene passata alla scheda di rete nella VM. Questa regola è relativa alla connessione di rete anziché all'endpoint, come invece sarebbe nelle distribuzioni classiche. Per aprire una porta, è necessario lasciare `--source-port-range` impostato su '\*' (valore predefinito) per accettare le richieste in ingresso da **qualsiasi** porta richiedente. Le porte sono solitamente dinamiche.
>
>

## <a name="bind-to-the-nic"></a>Associare alla scheda di rete
Associare il gruppo di sicurezza di rete alle schede di interfaccia di rete. È necessario connettere le schede di interfaccia di rete al gruppo di sicurezza di rete. Eseguire entrambi i comandi per collegare entrambe le schede di interfaccia di rete:

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Creare un set di disponibilità
I set di disponibilità agevolano la distribuzione delle VM nei domini di errore e domini di aggiornamento. Si creerà un set di disponibilità per le VM. Nell'esempio seguente viene creato un set di disponibilità chiamato `myAvailabilitySet`:

```azurecli
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

I domini di errore definiscono un gruppo di macchine virtuali che condividono una fonte di alimentazione e uno switch di rete comuni. Per impostazione predefinita, le macchine virtuali configurate nell'ambito di un set di disponibilità vengono suddivise tra un massimo di tre domini di errore. L'idea è che un problema hardware in uno di questi domini di errore non abbia effetto su tutte le VM che eseguono l'app. Azure distribuisce automaticamente le VM tra i domini di errore durante il posizionamento in un set di disponibilità.

I domini di aggiornamento indicano gruppi di macchine virtuali e l'hardware fisico sottostante che possono essere riavviati nello stesso momento. I domini di aggiornamento non vengono necessariamente riavviati in ordine sequenziale durante la manutenzione pianificata, ma ne viene riavviato uno solo alla volta. D nuovo, Azure distribuisce automaticamente le VM tra i domini di aggiornamento durante il posizionamento in un sito di disponibilità.

Per altre informazioni, leggere l'articolo relativo alla [gestione della disponibilità delle VM](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="create-the-linux-vms"></a>Creare le VM di Linux
Sono state create le risorse di archiviazione e di rete per supportare le VM accessibili tramite Internet. Ora è possibile creare quelle VM e proteggerle con una chiave SSH priva di password. In questo caso, si vuole creare una VM Ubuntu basata sull'LTS più recente. Per trovare le informazioni sull'immagine si usa `azure vm image list`, come descritto nell'articolo relativo alla [ricerca di immagini di VM di Azure](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

È stata selezionata un'immagine utilizzando il comando `azure vm image list westeurope canonical | grep LTS`. In questo caso, si usa `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Per l'ultimo campo si passa a `latest` così che in futuro si ottenga sempre la build più recente. La stringa usata è `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`.

Il passaggio successivo è già noto a chiunque abbia già creato una coppia di chiavi ssh-rsa pubblica e privata in Linux o Mac usando **ssh-keygen -t rsa -b 2048**. Se non si dispone di alcuna coppia di chiavi del certificato nella directory `~/.ssh` , è possibile crearle:

* Automaticamente, utilizzando l'opzione `azure vm create --generate-ssh-keys` .
* Manualmente, usando [le istruzioni per crearle autonomamente](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

In alternativa, è possibile usare il metodo `--admin-password` per autenticare le connessioni SSH dopo aver creato la VM. Di solito, questo metodo risulta essere meno sicuro.

Per creare la VM si dovranno unire tutte le risorse e le informazioni con il comando `azure vm create` :

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM1 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic1 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

Output:

```azurecli
info:    Executing command vm create
+ Looking up the VM "myVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account mystorageaccount
+ Looking up the availability set "myAvailabilitySet"
info:    Found an Availability set "myAvailabilitySet"
+ Looking up the NIC "myNic1"
info:    Found an existing NIC "myNic1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet" in the NIC "myNic1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://mystorageaccount.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

È possibile connettersi immediatamente alla VM usando le chiavi SSH predefinite. Assicurarsi di specificare la porta appropriata poiché si passa tramite il bilanciamento del carico. Per la prima VM impostare la regola NAT per l'inoltro della porta 4222 alla VM.

```bash
ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222
```

Output:

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

Proseguire e creare la seconda VM nello stesso modo:

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM2 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic2 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

Ora è possibile usare il comando `azure vm show myResourceGroup myVM1` per esaminare ciò che è stato creato. A questo punto le VM Ubuntu sono in esecuzione dietro al bilanciamento del carico in Azure a cui è possibile accedere solo con la coppia di chiavi SSH, dal momento che le password sono disabilitate. È possibile installare nginx o httpd, distribuire un'app Web e vedere il traffico passare attraverso il servizio di bilanciamento del carico e dirigersi a entrambe le VM.

```azurecli
azure vm show --resource-group myResourceGroup --name myVM1
```

Output:

```azurecli
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "myNic1"
data:    Id                              :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :myVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LmyNic1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://mystorageaccount.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## <a name="export-the-environment-as-a-template"></a>Esportare l'ambiente come modello
Dopo aver creato questo ambiente, è possibile creare un ambiente di sviluppo aggiuntivo utilizzando gli stessi parametri oppure creare un ambiente di produzione compatibile? Azure Resource Manager utilizza i modelli JSON che definiscono tutti i parametri per l'ambiente. È possibile creare interi ambienti facendo riferimento a questo modello JSON. È possibile [creare modelli JSON manualmente](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o esportare un ambiente esistente per creare il modello JSON desiderato:

```azurecli
azure group export --name myResourceGroup
```

Questo comando crea il file `myResourceGroup.json` nella directory di lavoro corrente. Quando si crea un ambiente da questo modello, vengono richiesti i nomi di tutte le risorse, tra cui quelli del servizio di bilanciamento del carico, delle interfacce di rete o delle VM. È possibile popolare questi nomi nel file del modello aggiungendo i parametri `-p` o `--includeParameterDefaultValue` al comando `azure group export` descritto in precedenza. Modificare il modello JSON per specificare i nomi delle risorse, o [creare un file parameters.json](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nel quale vengono specificati i nomi delle risorse.

Per creare un ambiente in base al modello:

```azurecli
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Se lo si desidera, è possibile consultare [ulteriori informazioni su come eseguire la distribuzione partendo dai modelli](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ulteriori informazioni su come aggiornare gli ambienti in modo incrementale, utilizzare il file di parametri e accedere ai modelli da un unico percorso di archiviazione.

## <a name="next-steps"></a>Passaggi successivi
Ora è possibile iniziare a utilizzare più componenti di rete e VM. È possibile utilizzare questo ambiente di esempio per compilare l'applicazione utilizzando i componenti principali presentati qui.

