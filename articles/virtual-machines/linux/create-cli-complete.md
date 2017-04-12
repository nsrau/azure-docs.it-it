---
title: Creare un ambiente Linux tramite l&quot;interfaccia della riga di comando di Azure 2.0 | Documentazione Microsoft
description: Usare l&quot;interfaccia della riga di comando di Azure 2.0 per creare da zero una risorsa di archiviazione, una VM di Linux, una rete virtuale con subnet, il bilanciamento del carico, una scheda NIC, un IP pubblico e un gruppo di sicurezza di rete.
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
ms.date: 03/07/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6552d590786859829597614d4b984f7798e38c94
ms.lasthandoff: 04/03/2017


---
# <a name="create-a-complete-linux-environment-with-the-azure-cli-20"></a>Creare un ambiente Linux completo tramite l'interfaccia della riga di comando di Azure 2.0
In questo articolo viene spiegato come creare una semplice rete con un servizio di bilanciamento del carico e un paio di VM utili per lo sviluppo e per calcoli semplici. Viene presentato in dettaglio l'intero processo, comando dopo comando, fino a creare due VM Linux funzionanti e sicure a cui è possibile connettersi da qualsiasi posizione in Internet. Si potrà quindi passare a reti e ambienti più complessi. Questo articolo illustra come creare l'ambiente con l'interfaccia della riga di comando di Azure 2.0. È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](create-cli-complete-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Durante il processo si apprenderanno informazioni sulla gerarchia delle dipendenze fornita dal modello di distribuzione di Resource Manager e sulle potenzialità che offre. Dopo aver osservato come viene realizzato il sistema, è possibile ricrearlo molto più rapidamente utilizzando i [modelli di Azure Resource Manager](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). In più, dopo aver scoperto come interagiscono le parti dell'ambiente, diventa più facile creare modelli per automatizzarle.

Nell'ambiente sono presenti:

* Due VM all'interno di un set di disponibilità.
* Un servizio di bilanciamento del carico con una regola di bilanciamento del carico sulla porta 80.
* Regole del gruppo di sicurezza di rete (NSG) per proteggere la VM dal traffico indesiderato.

![Panoramica sull'ambiente di base](./media/create-cli-complete/environment_overview.png)

## <a name="quick-commands"></a>Comandi rapidi
Se si vuole eseguire rapidamente l'attività, la sezione seguente indica in dettaglio i comandi base per caricare una VM in Azure. Altre informazioni dettagliate e il contesto per ogni passaggio sono disponibili nelle sezioni successive del documento, a partire da [qui](#detailed-walkthrough).

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. Alcuni esempi di nomi dei parametri sono `myResourceGroup`, `mystorageaccount` e `myVM`.

Per creare questo ambiente personalizzato, è necessario installare la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e connetterla a un account Azure usando [az login](/cli/azure/#login).

Creare prima il gruppo di risorse con [az group create](/cli/azure/group#create). Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella località `westeurope`:

```azurecli
az group create --name myResourceGroup --location westeurope
```

Questo passaggio è facoltativo. L'azione predefinita quando si crea una macchina virtuale con l'interfaccia della riga di comando di Azure 2.0 consiste nell'uso di Azure Managed Disks. Per altre informazioni su Azure Managed Disks, vedere [Azure Managed Disks overview](../../storage/storage-managed-disks-overview.md) (Panoramica di Azure Managed Disks). Se invece si desidera usare dischi non gestiti, è necessario creare un account di archiviazione con [az storage account create](/cli/azure/storage/account#create). L'esempio seguente crea un nuovo account di archiviazione denominato `mystorageaccount`. Il nome dell'account di archiviazione deve essere univoco; specificare quindi il proprio nome univoco.

```azurecli
az storage account create --resource-group myResourceGroup --location westeurope \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Creare la rete virtuale con [az network vnet create](/cli/azure/network/vnet#create). L'esempio seguente creata una rete virtuale denominata `myVnet` e una sottorete denominata `mySubnet`:

```azurecli
az network vnet create --resource-group myResourceGroup --location westeurope --name myVnet \
  --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
```

Creare un indirizzo IP pubblico con [az network public-ip create](/cli/azure/network/public-ip#create). Nell'esempio seguente viene aggiunto un indirizzo IP pubblico chiamato `myPublicIP` con il nome DNS `mypublicdns`. Il nome DNS deve essere univoco; specificare quindi il proprio nome univoco.

```azurecli
az network public-ip create --resource-group myResourceGroup --location westeurope \
  --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
```

Creare il bilanciamento del carico con [az network lb create](/cli/azure/network/lb#create). L'esempio seguente:

- crea un bilanciamento del carico denominato `myLoadBalancer`
- associa l'indirizzo IP pubblico `myPublicIP`
- crea un pool di indirizzi IP front-end denominato `mySubnetPool`
- crea un pool di indirizzi IP back-end denominato `myBackEndPool`

```azurecli
az network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer --public-ip-address myPublicIP \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool
```

Creare regole Network Address Translation (NAT) in ingresso SSH per il bilanciamento del carico con [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#create). Nell'esempio seguente vengono create due regole per il servizio di bilanciamento del carico chiamate `myLoadBalancerRuleSSH1` e `myLoadBalancerRuleSSH2`:

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 --protocol tcp \
  --frontend-port 4222 --backend-port 22 --frontend-ip-name myFrontEndPool
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22 --frontend-ip-name myFrontEndPool
```

Creare il probe di integrità del bilanciamento del carico con [az network lb probe create](/cli/azure/network/lb/probe#create). Nell'esempio seguente viene creato un probe TCP chiamato `myHealthProbe`:

```azurecli
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80 --interval 15 --threshold 4
```

Creare le regole NAT in ingresso Web per il bilanciamento del carico con [az network lb rule create](/cli/azure/network/lb/rule#create). L'esempio seguente crea una regola per il bilanciamento del carico denominata `myLoadBalancerRuleWeb` e la associa al probe `myHealthProbe`:

```azurecli
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myLoadBalancerRuleWeb --protocol tcp --frontend-port 80 --backend-port 80 \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool \
  --probe-name myHealthProbe
```

Verificare il servizio di bilanciamento del carico, i pool di indirizzi IP e le regole NAT con [az network lb show](/cli/azure/network/lb#show):

```azurecli
az network lb show --resource-group myResourceGroup --name myLoadBalancer
```

Creare il gruppo di sicurezza di rete con [az network nsg create](/cli/azure/network/nsg#create). L'esempio seguente crea un gruppo di sicurezza di rete denominato `myNetworkSecurityGroup`:

```azurecli
az network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Aggiungere due regole in ingresso per il gruppo di sicurezza di rete con [az network nsg rule create](/cli/azure/network/nsg/rule#create). Nell'esempio seguente vengono create due regole chiamate `myNetworkSecurityGroupRuleSSH` e `myNetworkSecurityGroupRuleHTTP`:

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleSSH \
  --protocol tcp --direction inbound --priority 1000 --source-address-prefix '*' \
  --source-port-range '*' --destination-address-prefix '*' --destination-port-range 22 \
  --access allow
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleHTTP \
  --protocol tcp --direction inbound --priority 1001 --source-address-prefix '*' \
  --source-port-range '*' --destination-address-prefix '*' --destination-port-range 80 \
  --access allow
```

Creare la prima scheda di interfaccia di rete con [az network nic create](/cli/azure/network/nic#create). L'esempio seguente crea una scheda di rete denominata `myNic1` e la associa al bilanciamento del carico `myLoadBalancer` e ai pool appropriati; la associa anche a `myNetworkSecurityGroup`:

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic1 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH1
```

Creare la seconda scheda di interfaccia di rete, usando di nuovo **az network nic create**. Nell'esempio seguente viene creata una scheda di interfaccia di rete chiamata `myNic2`:

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic2 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH2
```

Creare il set di disponibilità con [az vm availability-set create](/cli/azure/vm/availability-set#create). Nell'esempio seguente viene creato un set di disponibilità chiamato `myAvailabilitySet`:

```azurecli
az vm availability-set create --resource-group myResourceGroup --location westeurope \
  --name myAvailabilitySet \
  --platform-fault-domain-count 3 --platform-update-domain-count 2
```

Creare la prima VM Linux con [az vm create](/cli/azure/vm#create). Nell'esempio seguente viene creata una VM denominata `myVM1` usando Azure Managed Disks. Se si desidera usare dischi non gestiti, vedere la nota aggiuntiva seguente.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic1 \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Se si usa Azure Managed Disks, ignorare questo passaggio. Se si desidera usare dischi non gestiti e nei passaggi precedenti è stato creato un account di archiviazione, è necessario aggiungere alcuni parametri al comando della procedura. Aggiungere i parametri seguenti al comando della procedura per creare dischi non gestiti nell'account di archiviazione denominato `mystorageaccount`: 

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
```

Creare la seconda VM di Linux, usando di nuovo **az vm create**. Nell'esempio seguente viene creata una VM denominata `myVM2`:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic2 \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Anche in questo caso, se non si usa Azure Managed Disks predefinito, aggiungere i parametri seguenti al comando della procedura per creare dischi non gestiti nell'account di archiviazione denominato `mystorageaccount`:

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
``` 

Verificare che tutto sia creato correttamente con [az vm show](/cli/azure/vm#show):

```azurecli
az vm show --resource-group myResourceGroup --name myVM1
az vm show --resource-group myResourceGroup --name myVM2
```

Esportare il nuovo ambiente in un modello con [az group export](/cli/azure/group#export) per ricreare rapidamente nuove istanze:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

## <a name="detailed-walkthrough"></a>Procedura dettagliata
La procedura dettagliata seguente illustra il funzionamento di ciascun comando durante la creazione dell'ambiente. Questi concetti torneranno utili in fase di creazione degli ambienti personalizzati per lo sviluppo o per la produzione.

Verificare di avere installato la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e di avere eseguito la registrazione a un account di Azure con [az login](/cli/azure/#login).

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono `myResourceGroup`, `mystorageaccount` e `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Creare i gruppi di risorse e scegliere i percorsi di distribuzione
I gruppi di risorse di Azure sono entità di distribuzione logiche che contengono informazioni di configurazione e altri metadati per abilitare la gestione logica delle distribuzioni di risorse. Creare il gruppo di risorse con [az group create](/cli/azure/group#create). Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella località `westeurope`:

```azurecli
az group create --name myResourceGroup --location westeurope
```

Per impostazione predefinita, l'output è in formato JSON (JavaScript Object Notation). Per creare l'output come elenco o tabella, ad esempio, usare [az configure --output](/cli/azure/#configure). È possibile anche aggiungere `--output` a qualsiasi comando per apportare una modifica una sola volta nel formato di output. L'esempio seguente illustra l'output JSON ottenuto dal comando **az group create**:

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione
Questo passaggio è facoltativo. L'azione predefinita quando si crea una macchina virtuale con l'interfaccia della riga di comando di Azure 2.0 consiste nell'uso di Azure Managed Disks. Questi dischi vengono gestiti dalla piattaforma Azure e non richiedono alcuna pianificazione o alcuna posizione per l'archiviazione. Per altre informazioni su Azure Managed Disks, vedere [Azure Managed Disks overview](../../storage/storage-managed-disks-overview.md) (Panoramica di Azure Managed Disks). Se si desidera usare Azure Managed Disks, passare a [Creare una rete virtuale e una subnet](#create-a-virtual-network-and-subnet). 

Se si desidera usare dischi non gestiti, è necessario creare un account di archiviazione per i dischi della macchina virtuale e per eventuali altri dischi dati da aggiungere.

Viene usato il comando [az storage account create](/cli/azure/storage/account#create), viene passato il percorso dell'account, il gruppo di risorse che lo controlla e il tipo di supporto di archiviazione desiderato. Nell'esempio seguente viene creato un nuovo account di archiviazione denominato `mystorageaccount`:

```azurecli
az storage account create --resource-group myResourceGroup --location westeurope \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Output:

```json
{
  "accessTier": null,
  "creationTime": "2016-12-07T17:59:50.090092+00:00",
  "customDomain": null,
  "encryption": null,
  "id": "/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "westeurope",
  "name": "mystorageaccount",
  "primaryEndpoints": {
    "blob": "https://mystorageaccount.blob.core.windows.net/",
    "file": "https://mystorageaccount.file.core.windows.net/",
    "queue": "https://mystorageaccount.queue.core.windows.net/",
    "table": "https://mystorageaccount.table.core.windows.net/"
  },
  "primaryLocation": "westeurope",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "secondaryEndpoints": null,
  "secondaryLocation": null,
  "sku": {
    "name": "Standard_LRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "Available",
  "statusOfSecondary": null,
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

Per esaminare l'account di archiviazione usando l'interfaccia della riga di comando, è necessario innanzitutto impostare le chiavi e i nomi dell'account. Usare [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string). Sostituire il nome dell'account di archiviazione nell'esempio seguente con un nome a scelta:

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(az storage account show-connection-string --resource-group myResourceGroup --name mystorageaccount --query connectionString)"
```

È possibile visualizzare le informazioni sull'archiviazione con [az storage container list](/cli/azure/storage/container#list):

```azurecli
az storage container list
```

Output:

```azurecli
[
  {
    "metadata": null,
    "name": "vhds",
    "properties": {
      "etag": "\"0x8D41F912D472F94\"",
      "lastModified": "2016-12-08T17:39:35+00:00",
      "lease": {
        "duration": null,
        "state": null,
        "status": null
      },
      "leaseDuration": "infinite",
      "leaseState": "leased",
      "leaseStatus": "locked"
    }
  }
]
```

## <a name="create-a-virtual-network-and-subnet"></a>Creare una rete virtuale e una subnet
Dopodiché è necessario creare una rete virtuale che funziona in Azure e una subnet in cui poter creare le VM. L'esempio seguente usa [az network vnet create](/cli/azure/network/vnet#create) per creare una rete virtuale denominata `myVnet` con il prefisso di indirizzo `192.168.0.0/16` e una subnet denominata `mySubnet` con il prefisso di indirizzo della subnet `192.168.1.0/24`:

```azurecli
az network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefix 192.168.0.0/16 \
  --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
```

L'output mostra la subnet creata in modo logico all'interno della rete virtuale:

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "westeurope",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```


## <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico
A questo punto, creare l'indirizzo IP pubblico (PIP) che viene assegnato al bilanciamento del carico. Consente di connettersi alle VM da Internet usando il comando [az network public-ip create](/cli/azure/network/public-ip#create). Dato che l'indirizzo predefinito è dinamico, si crea una voce DNS nel dominio **cloudapp.azure.com** usando l'opzione `--domain-name-label`. Nell'esempio seguente viene aggiunto un indirizzo IP pubblico chiamato `myPublicIP` con il nome DNS `mypublicdns`. Il nome DNS deve essere univoco, quindi specificare il proprio nome DNS.

```azurecli
az network public-ip create --resource-group myResourceGroup --location westeurope \
  --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
```

Output:

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.westeurope.cloudapp.azure.com",
      "reverseFqdn": ""
    },
    "idleTimeoutInMinutes": 4,
    "ipAddress": "52.174.48.109",
    "provisioningState": "Succeeded",
    "publicIPAllocationMethod": "Static",
    "resourceGuid": "78218510-ea54-42d7-b2c2-44773fc14af5"
  }
}
```

La risorsa indirizzo IP pubblico è stata allocata in modo logico, ma non è ancora stato assegnato un indirizzo specifico. Per ottenere un indirizzo IP, sarà necessario un servizio di bilanciamento del carico, che non è ancora stato creato.

## <a name="create-a-load-balancer-and-ip-pools"></a>Creare un servizio di bilanciamento del carico e i pool di indirizzi IP
Quando si crea un bilanciamento del carico, questo consente di distribuire il traffico tra più VM. Fornisce anche ridondanza all'applicazione grazie all'esecuzione di più VM che rispondono alle richieste degli utenti in caso di manutenzione o carichi elevati. L'esempio seguente usa [az network lb create](/cli/azure/network/lb#create) per creare un bilanciamento del carico denominato `myLoadBalancer`, un pool IP front-end denominato `myFrontEndPool` e associare la risorsa `myPublicIP`:

```azurecli
az network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEndPool
```

Output:

```json
{
  "loadBalancer": {
    "backendAddressPools": [
      {
        "etag": "W/\"7a05df7a-5ee2-4581-b411-4b6f048ab291\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
        "name": "myLoadBalancerbepool",
        "properties": {
          "provisioningState": "Succeeded"
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "frontendIPConfigurations": [
      {
        "etag": "W/\"7a05df7a-5ee2-4581-b411-4b6f048ab291\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
        "name": "myFrontEndPool",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "provisioningState": "Succeeded",
          "publicIPAddress": {
            "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
            "resourceGroup": "myResourceGroup"
          }
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "inboundNatPools": [],
    "inboundNatRules": [],
    "loadBalancingRules": [],
    "outboundNatRules": [],
    "probes": [],
    "provisioningState": "Succeeded",
    "resourceGuid": "f4879d84-5ae8-4e06-8b9b-1419baa875d9"
  }
}
```

Si noti che è stata usata l'opzione `--public-ip-address` per passare il valore `myPublicIP` creato in precedenza. L'assegnazione dell'indirizzo IP pubblico al servizio di bilanciamento del carico consente di raggiungere le VM su Internet.

Si usa un pool di back-end come un percorso per le VM a cui connettersi. In questo modo, il traffico può fluire attraverso il bilanciamento del carico verso le VM. Creare il pool IP per il traffico di back-end con [az network lb address-pool create](/cli/azure/network/lb/address-pool#create). Nell'esempio seguente viene creato un pool di indirizzi IP back-end chiamato `myBackEndPool`:

```azurecli
az network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Cattura dell'output:

```json
  "backendAddressPools": [
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
      "loadBalancingRules": null,
      "name": "myLoadBalancerbepool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    },
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
      "loadBalancingRules": null,
      "name": "myBackEndPool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    }
  ],
  "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
```


## <a name="create-load-balancer-nat-rules"></a>Creare le regole NAT per il servizio di bilanciamento del carico
Affinché il traffico attraversi il servizio di bilanciamento del carico, è necessario creare le regole Network Address Translation (NAT) che specificano le azioni in ingresso o in uscita. È possibile specificare il protocollo da usare, quindi eseguire il mapping delle porte esterne alle porte interne desiderate. Per questo ambiente si creeranno alcune regole che consentono il passaggio di SSH attraverso il servizio di bilanciamento del carico fino alle VM con [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#create). Le porte TCP 4222 e 4223 vengono configurate in modo da puntare alla porta TCP 22 nelle VM, che vengono create successivamente. Nell'esempio seguente viene creata una regola chiamata `myLoadBalancerRuleSSH1` per eseguire il mapping della porta TCP 4222 sulla porta 22:

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 --protocol tcp \
  --frontend-port 4222 --backend-port 22 --frontend-ip-name myFrontEndPool
```

Output:

```json
{
  "backendIpConfiguration": null,
  "backendPort": 22,
  "enableFloatingIp": false,
  "etag": "W/\"72843cf8-b5fb-4655-9ac8-9cbbbbf1205a\"",
  "frontendIpConfiguration": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "frontendPort": 4222,
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
  "idleTimeoutInMinutes": 4,
  "name": "myLoadBalancerRuleSSH1",
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup"
}
```

Ripetere la procedura per la seconda regola NAT per SSH. Nell'esempio seguente viene creata una regola chiamata `myLoadBalancerRuleSSH2` per eseguire il mapping della porta TCP 4223 sulla porta 22:

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22 --frontend-ip-name myFrontEndPool
```

## <a name="create-a-load-balancer-health-probe"></a>Creare un probe di integrità per il servizio di bilanciamento del carico
Un probe di integrità verifica periodicamente le VM che si trovano dietro il servizio di bilanciamento del carico per assicurarsi che siano operative e che rispondano alle richieste, come specificato. In caso contrario ne viene disabilitata l'operatività per fare in modo che gli utenti non vengano indirizzati a esse. È possibile definire controlli personalizzati per il probe di integrità, nonché gli intervalli e i valori di timeout. Per altre informazioni sui probe di integrità, vedere [Probe di integrità del servizio di bilanciamento del carico](../../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). L'esempio seguente usa [az network lb probe create](/cli/azure/network/lb/probe#create) per creare un probe di integrità TCP denominato `myHealthProbe`:

```azurecli
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80 --interval 15 --threshold 4
```

Output:

```json
{
  "etag": "W/\"757018f6-b70a-4651-b717-48b511d82ba0\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe",
  "intervalInSeconds": 15,
  "loadBalancingRules": null,
  "name": "myHealthProbe",
  "numberOfProbes": 4,
  "port": 80,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "requestPath": null,
  "resourceGroup": "myResourceGroup"
}
```

In questo caso è stato specificato un intervallo di 15 secondi per i controlli di integrità. È consentito un massimo di quattro probe mancati (un minuto) prima che il bilanciamento del carico consideri l'host come non più funzionante.

Verrà anche creata una regola NAT per la porta TCP 80 per il traffico Web, collegando la regola ai pool di indirizzi IP. Se la regola viene associata a un pool IP, anziché essere associata singolarmente alle VM, è possibile aggiungere o rimuovere le VM dal pool di indirizzi IP. Il servizio di bilanciamento del carico regola automaticamente il flusso del traffico. L'esempio seguente usa [az network lb rule create](/cli/azure/network/lb/rule#create) per creare una regola denominata `myLoadBalancerRuleWeb` per eseguire il mapping della porta TCP 80 alla porta 80 e associa il probe di integrità denominato `myHealthProbe`:

```azurecli
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myLoadBalancerRuleWeb --protocol tcp --frontend-port 80 --backend-port 80 \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool \
  --probe-name myHealthProbe
```

Output:

```json
{
  "backendAddressPool": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "backendPort": 80,
  "enableFloatingIp": false,
  "etag": "W/\"f0d77680-bf42-4d11-bfab-5d2c541bee56\"",
  "frontendIpConfiguration": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "frontendPort": 80,
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
  "idleTimeoutInMinutes": 4,
  "loadDistribution": "Default",
  "name": "myLoadBalancerRuleWeb",
  "probe": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe",
    "resourceGroup": "myResourceGroup"
  },
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="verify-the-load-balancer"></a>Verificare il bilanciamento del carico
La configurazione del bilanciamento del carico è completa. Ecco i passaggi effettuati:

1. È stato creato un bilanciamento del carico.
2. È stato creato un pool di indirizzi IP front-end a cui è stato assegnato un indirizzo IP pubblico.
3. È stato creato un pool IP back-end a cui possono connettersi le VM.
4. Sono state create le regole NAT che consentono alle macchine virtuali di usare il protocollo SSH per la gestione, oltre a una regola che consente all'app Web di usare la porta TCP 80.
5. È stato aggiunto un probe di integrità per verificare periodicamente lo stato delle VM. Questo probe di integrità garantisce agli utenti di accedere solo alle VM non più funzionanti o con contenuti non più disponibili.

Ecco il bilanciamento del carico risultante con [az network lb show](/cli/azure/network/lb#show):

```azurecli
az network lb show --resource-group myResourceGroup --name myLoadBalancer
```

Output:

```json
{
  "backendAddressPools": [
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
      "loadBalancingRules": null,
      "name": "myLoadBalancerbepool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    },
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
      "loadBalancingRules": null,
      "name": "myBackEndPool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    }
  ],
  "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
  "frontendIpConfigurations": [
    {
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/LoadBalancerFrontEnd",
      "inboundNatPools": null,
      "inboundNatRules": null,
      "loadBalancingRules": null,
      "name": "LoadBalancerFrontEnd",
      "outboundNatRules": null,
      "privateIpAddress": null,
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Succeeded",
      "publicIpAddress": {
        "dnsSettings": null,
        "etag": null,
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/PublicIPmyLoadBalancer",
        "idleTimeoutInMinutes": null,
        "ipAddress": null,
        "ipConfiguration": null,
        "location": null,
        "name": null,
        "provisioningState": null,
        "publicIpAddressVersion": null,
        "publicIpAllocationMethod": null,
        "resourceGroup": "myResourceGroup",
        "resourceGuid": null,
        "tags": null,
        "type": null
      },
      "resourceGroup": "myResourceGroup",
      "subnet": null
    },
    {
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
      "inboundNatPools": null,
      "inboundNatRules": null,
      "loadBalancingRules": null,
      "name": "myFrontEndPool",
      "outboundNatRules": null,
      "privateIpAddress": null,
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Succeeded",
      "publicIpAddress": {
        "dnsSettings": null,
        "etag": null,
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
        "idleTimeoutInMinutes": null,
        "ipAddress": null,
        "ipConfiguration": null,
        "location": null,
        "name": null,
        "provisioningState": null,
        "publicIpAddressVersion": null,
        "publicIpAllocationMethod": null,
        "resourceGroup": "myResourceGroup",
        "resourceGuid": null,
        "tags": null,
        "type": null
      },
      "resourceGroup": "myResourceGroup",
      "subnet": null
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "inboundNatPools": [],
  "inboundNatRules": [],
  "loadBalancingRules": [],
  "location": "westeurope",
  "name": "myLoadBalancer",
  "outboundNatRules": [],
  "probes": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "b5815801-b53d-4c22-aafc-2a9064f90f3c",
  "tags": {},
  "type": "Microsoft.Network/loadBalancers"
}
```

## <a name="create-a-network-security-group-and-rules"></a>Creare un gruppo di sicurezza di rete e le regole corrispondenti
Vengono ora creati il gruppo di sicurezza di rete e le regole in ingresso che regolano l'accesso alla scheda di interfaccia di rete. Per una scheda di interfaccia di rete o una subnet è possibile usare un gruppo di sicurezza di rete. Si definiscono regole per controllare il flusso del traffico da e verso le VM. L'esempio seguente usa [az network nsg create](/cli/azure/network/nsg#create) per creare un gruppo di sicurezza di rete denominato `myNetworkSecurityGroup`:

```azurecli
az network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Si aggiungerà la regola in ingresso per il gruppo di sicurezza di rete con [az network nsg rule create](/cli/azure/network/nsg/rule#create) per consentire le connessioni in ingresso sulla porta 22 (per supportare SSH). Nell'esempio seguente viene creata una regola denominata `myNetworkSecurityGroupRuleSSH` per consentire il traffico TCP sulla porta 22:

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleSSH \
  --protocol tcp --direction inbound --priority 1000 \
  --source-address-prefix '*' --source-port-range '*' \
  --destination-address-prefix '*' --destination-port-range 22 --access allow
```

Si aggiungerà la regola in ingresso per il gruppo di sicurezza di rete per consentire le connessioni in ingresso sulla porta 80 (per supportare il traffico Web). Nell'esempio seguente viene creata una regola denominata `myNetworkSecurityGroupRuleHTTP` per consentire il traffico TCP sulla porta 80:

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleHTTP \
  --protocol tcp --direction inbound --priority 1001 \
  --source-address-prefix '*' --source-port-range '*' \
  --destination-address-prefix '*' --destination-port-range 80 --access allow
```

> [!NOTE]
> La regola in ingresso è un filtro per le connessioni di rete in ingresso. In questo esempio il gruppo di sicurezza di rete viene associato alla scheda di rete virtuale delle VM, di conseguenza qualsiasi richiesta per la porta 22 viene passata alla scheda di rete nella VM. Questa regola è relativa alla connessione di rete anziché all'endpoint, come invece sarebbe nelle distribuzioni classiche. Per aprire una porta, è necessario lasciare `--source-port-range` impostato su '\*' (valore predefinito) per accettare le richieste in ingresso da **qualsiasi** porta richiedente. Le porte sono solitamente dinamiche.

Esaminare il gruppo di sicurezza di rete e le regole con [az network nsg show](/cli/azure/network/nsg#show):

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Output:

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBound",
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "westeurope",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "79c2c293-ee3e-4616-bf9c-4741ff1f708a",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleHTTP",
      "name": "myNetworkSecurityGroupRuleHTTP",
      "priority": 1001,
      "protocol": "tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": {},
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Creare una scheda di interfaccia di rete da usare con la VM di Linux
Le schede di interfaccia di rete sono disponibili a livello di programmazione in quanto è possibile applicare delle regole di utilizzo. È inoltre possibile averne più di una. Nel comando [az network nic create](https://docs.microsoft.com/en-us/cli/azure/network/nic#create) seguente la scheda di interfaccia di rete viene associata al pool IP back-end del carico e alla regola NAT per consentire il traffico SSH e il gruppo di sicurezza di rete.

Nell'esempio seguente viene creata una scheda di interfaccia di rete chiamata `myNic1`:

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic1 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH1
```

Output:

```json
{
  "newNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": []
    },
    "enableIPForwarding": false,
    "ipConfigurations": [
      {
        "etag": "W/\"a76b5c0d-14e1-4a99-afd4-5cd8ac0465ca\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/ipconfig1",
        "name": "ipconfig1",
        "properties": {
          "loadBalancerBackendAddressPools": [
            {
              "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
              "resourceGroup": "myResourceGroup"
            }
          ],
          "loadBalancerInboundNatRules": [
            {
              "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
              "resourceGroup": "myResourceGroup"
            }
          ],
          "primary": true,
          "privateIPAddress": "192.168.1.4",
          "privateIPAllocationMethod": "Dynamic",
          "provisioningState": "Succeeded",
          "subnet": {
            "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
            "resourceGroup": "myResourceGroup"
          }
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "networkSecurityGroup": {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "resourceGroup": "myResourceGroup"
    },
    "provisioningState": "Succeeded",
    "resourceGuid": "838977cb-cf4b-4c4a-9a32-0ddec7dc818b"
  }
}
```

Ora è possibile creare la seconda scheda di interfaccia di rete, collegandola di nuovo al pool IP back-end. Questa volta la seconda regola NAT consente il traffico SSH. Nell'esempio seguente viene creata una scheda di interfaccia di rete chiamata `myNic2`:

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic2 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH2
```


## <a name="create-an-availability-set"></a>Creare un set di disponibilità
I set di disponibilità agevolano la distribuzione delle VM nei domini di errore e domini di aggiornamento. Creare un set di disponibilità per le VM con [az vm availability-set create](/cli/azure/vm/availability-set#create). Nell'esempio seguente viene creato un set di disponibilità chiamato `myAvailabilitySet`:

```azurecli
az vm availability-set create --resource-group myResourceGroup --location westeurope \
  --name myAvailabilitySet \
  --platform-fault-domain-count 3 --platform-update-domain-count 2
```

I domini di errore definiscono un gruppo di macchine virtuali che condividono una fonte di alimentazione e uno switch di rete comuni. Per impostazione predefinita, le macchine virtuali configurate nell'ambito di un set di disponibilità vengono suddivise tra un massimo di tre domini di errore. L'idea è che un problema hardware in uno di questi domini di errore non abbia effetto su tutte le VM che eseguono l'app. Azure distribuisce automaticamente le VM tra i domini di errore durante il posizionamento in un set di disponibilità.

I domini di aggiornamento indicano gruppi di macchine virtuali e l'hardware fisico sottostante che possono essere riavviati nello stesso momento. I domini di aggiornamento non vengono necessariamente riavviati in ordine sequenziale durante la manutenzione pianificata, ma ne viene riavviato uno solo alla volta. D nuovo, Azure distribuisce automaticamente le VM tra i domini di aggiornamento durante il posizionamento in un sito di disponibilità.

Per altre informazioni, leggere l'articolo relativo alla [gestione della disponibilità delle VM](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="create-the-linux-vms"></a>Creare le VM di Linux
Sono state create le risorse di rete per supportare le VM accessibili tramite Internet. Ora è possibile creare quelle VM e proteggerle con una chiave SSH priva di password. In questo caso, si vuole creare una VM Ubuntu basata sull'LTS più recente. Per trovare le informazioni sull'immagine si usa [az vm image list](/cli/azure/vm/image#list), come descritto nell'articolo relativo alla [ricerca di immagini di VM di Azure](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

È possibile anche specificare una chiave SSH da usare per l'autenticazione. Se non si dispone di chiavi SSH, è possibile crearle usando [queste istruzioni](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). In alternativa, è possibile usare il metodo `--admin-password` per autenticare le connessioni SSH dopo aver creato la VM. Di solito, questo metodo risulta essere meno sicuro.

Per creare la VM si dovranno unire tutte le risorse e le informazioni con il comando [az vm create](/cli/azure/vm#create). Nell'esempio seguente viene creata una VM denominata `myVM1` usando Azure Managed Disks. Se si desidera usare dischi non gestiti, vedere la nota aggiuntiva seguente.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic1 \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Se si usa Azure Managed Disks, ignorare questo passaggio. Se si desidera usare dischi non gestiti e nei passaggi precedenti è stato creato un account di archiviazione, è necessario aggiungere alcuni parametri al comando della procedura. Aggiungere i parametri seguenti al comando della procedura per creare dischi non gestiti nell'account di archiviazione denominato `mystorageaccount`: 

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
```

Output:

```json
{
  "fqdn": "",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1",
  "macAddress": "",
  "privateIpAddress": "",
  "publicIpAddress": "",
  "resourceGroup": "myResourceGroup"
}
```

È possibile connettersi immediatamente alla VM usando le chiavi SSH predefinite. Assicurarsi di specificare la porta appropriata poiché si passa tramite il bilanciamento del carico. Per la prima VM impostare la regola NAT per l'inoltro della porta 4222 alla VM.

```bash
ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222 -i ~/.ssh/id_rsa.pub
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
az vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic2 \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Anche in questo caso, se non si usa Azure Managed Disks predefinito, aggiungere i parametri seguenti al comando della procedura per creare dischi non gestiti nell'account di archiviazione denominato `mystorageaccount`:

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
``` 

A questo punto le VM Ubuntu sono in esecuzione dietro al bilanciamento del carico in Azure a cui è possibile accedere solo con la coppia di chiavi SSH, dal momento che le password sono disabilitate. È possibile installare nginx o httpd, distribuire un'app Web e vedere il traffico passare attraverso il servizio di bilanciamento del carico e dirigersi a entrambe le VM.


## <a name="export-the-environment-as-a-template"></a>Esportare l'ambiente come modello
Dopo aver creato questo ambiente, è possibile creare un ambiente di sviluppo aggiuntivo utilizzando gli stessi parametri oppure creare un ambiente di produzione compatibile? Azure Resource Manager utilizza i modelli JSON che definiscono tutti i parametri per l'ambiente. È possibile creare interi ambienti facendo riferimento a questo modello JSON. È possibile [creare modelli JSON manualmente](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o esportare un ambiente esistente per creare il modello JSON desiderato. Usare [az group export](/cli/azure/group#export) per esportare il gruppo di risorse come segue:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

Questo comando crea il file `myResourceGroup.json` nella directory di lavoro corrente. Quando si crea un ambiente da questo modello, vengono richiesti i nomi di tutte le risorse, tra cui quelli del servizio di bilanciamento del carico, delle interfacce di rete o delle VM. È possibile popolare questi nomi nel file del modello aggiungendo il parametro `--include-parameter-default-value` al comando **az group export** descritto in precedenza. Modificare il modello JSON per specificare i nomi delle risorse, o [creare un file parameters.json](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nel quale vengono specificati i nomi delle risorse.

Per creare un ambiente in base al modello, usare [az group deployment create](/cli/azure/group/deployment#create) come indicato di seguito:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Se lo si desidera, è possibile consultare [ulteriori informazioni su come eseguire la distribuzione partendo dai modelli](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ulteriori informazioni su come aggiornare gli ambienti in modo incrementale, utilizzare il file di parametri e accedere ai modelli da un unico percorso di archiviazione.

## <a name="next-steps"></a>Passaggi successivi
Ora è possibile iniziare a utilizzare più componenti di rete e VM. È possibile utilizzare questo ambiente di esempio per compilare l'applicazione utilizzando i componenti principali presentati qui.

