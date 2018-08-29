---
title: Creare una VM Linux in Azure con più schede di interfaccia di rete | Documentazione Microsoft
description: Informazioni su come creare una VM Linux con più schede di interfaccia di rete collegate usando l'interfaccia della riga di comando di Azure 2.0 o i modelli di Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: iainfou
ms.openlocfilehash: 77feb52a4ba2013bd6ec0afcd30a20f05227031e
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2018
ms.locfileid: "42143464"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Come creare una macchina virtuale Linux in Azure con più schede di interfaccia di rete
È possibile creare una macchina virtuale (VM) in Azure con più interfacce di rete virtuale (NIC) collegate. Uno scenario comune è quello di avere subnet diverse per la connettività front-end e back-end oppure una rete dedicata a una soluzione di monitoraggio o backup. Questo articolo illustra come creare una VM con più schede di interfaccia di rete collegate e come aggiungere o rimuovere le schede di interfaccia di rete da una VM esistente. Le differenti [dimensioni della macchina virtuale](sizes.md) supportano un numero variabile di schede di rete, pertanto scegliere le dimensioni della macchina virtuale di conseguenza.

Questo articolo illustra come creare una macchina virtuale con più schede di interfaccia di rete usando l'interfaccia della riga di comando di Azure 2.0. È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](multiple-nics-nodejs.md).


## <a name="create-supporting-resources"></a>Creare risorse di supporto
Installare la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e accedere a un account Azure tramite il comando [az login](/cli/azure/reference-index#az_login).

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono *myResourceGroup*, *mystorageaccount* e *myVM*.

Creare prima un gruppo di risorse con [az group create](/cli/azure/group#az_group_create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Creare la rete virtuale con [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). L'esempio seguente crea una rete virtuale denominata *myVnet* e una subnet denominata *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Creare una subnet per il traffico di back-end con il comando [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). L'esempio seguente crea una subnet denominata *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Creare un gruppo di sicurezza di rete con il comando [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create). L'esempio seguente crea un gruppo di sicurezza di rete denominato *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Creare e configurare più schede di interfaccia di rete
Creare due schede di interfaccia di rete con il comando [az network nic create](/cli/azure/network/nic#az_network_nic_create). L'esempio seguente crea due schede di interfaccia di rete, denominate *myNic1* e *myNic2*, connesse al gruppo di sicurezza di rete, con una scheda che si connette a ogni subnet:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Creare una macchina virtuale e collegare le schede di interfaccia di rete
Quando si crea la macchina virtuale, specificare le schede di interfaccia di rete create con `--nics`. L'utente deve anche fare attenzione quando seleziona la dimensione della macchina virtuale. Esistono dei limiti per quanto riguarda il numero totale di schede di rete che è possibile aggiungere. Ulteriori informazioni sulle [dimensioni delle macchine virtuali di Linux](sizes.md).

Creare una VM con il comando [az vm create](/cli/azure/vm#az_vm_create). L'esempio seguente crea una macchina virtuale denominata *myVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

Aggiungere le tabelle di routing al sistema operativo guest. A tale scopo, completare i passaggi descritti in [Configurare il sistema operativo guest per più schede di interfaccia di rete](#configure-guest-os-for- multiple-nics).

## <a name="add-a-nic-to-a-vm"></a>Aggiungere una scheda di interfaccia di rete a una VM
I passaggi precedenti hanno consentito di creare una VM con più schede di interfaccia di rete. È anche possibile aggiungere schede di interfaccia di rete a una VM esistente con l'interfaccia della riga di comando di Azure 2.0. Le differenti [dimensioni della macchina virtuale](sizes.md) supportano un numero variabile di schede di rete, pertanto scegliere le dimensioni della macchina virtuale di conseguenza. Se necessario, è possibile [ridimensionare una VM](change-vm-size.md).

Creare un'altra scheda di interfaccia di rete con [az network nic create](/cli/azure/network/nic#az_network_nic_create). L'esempio seguente crea una scheda di interfaccia rete denominata *myNic3* connessa alla subnet back-end e al gruppo di sicurezza di rete creato nei passaggi precedenti:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Per aggiungere una scheda di interfaccia di rete a una VM esistente, deallocare prima di tutto la VM con [az vm deallocate](/cli/azure/vm#az_vm_deallocate). L'esempio seguente dealloca la VM denominata *myVM*:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Aggiungere la scheda di interfaccia di rete con [az vm nic add](/cli/azure/vm/nic#az_vm_nic_add). L'esempio seguente aggiunge *myNic3* a *myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Avviare la VM con [az vm start](/cli/azure/vm#az_vm_start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Aggiungere le tabelle di routing al sistema operativo guest. A tale scopo, completare i passaggi descritti in [Configurare il sistema operativo guest per più schede di interfaccia di rete](#configure-guest-os-for- multiple-nics).

## <a name="remove-a-nic-from-a-vm"></a>Rimuovere una scheda di interfaccia di rete da una VM
Per rimuovere una scheda di interfaccia di rete da una VM esistente, deallocare prima di tutto la VM con [az vm deallocate](/cli/azure/vm#az_vm_deallocate). L'esempio seguente dealloca la VM denominata *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Rimuovere la scheda di interfaccia di rete con [az vm nic remove](/cli/azure/vm/nic#az_vm_nic_remove). L'esempio seguente rimuove *myNic3* da *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Avviare la VM con [az vm start](/cli/azure/vm#az_vm_start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Creare più schede di interfaccia di rete usando i modelli di Resource Manager
I modelli di Azure Resource Manager utilizzano i file JSON dichiarativi per definire l'ambiente. È possibile consultare una [panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). I modelli di Resource Manager offrono un modo di creare più istanze di una risorsa durante la distribuzione, come ad esempio la creazione di più schede di rete. Utilizzare *Copia* per specificare il numero di istanze da creare:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Ulteriori informazioni sulla [creazione di più istanze utilizzando *Copia*](../../resource-group-create-multiple.md). 

È inoltre possibile utilizzare un `copyIndex()` per poi aggiungere un numero al nome di una risorsa, che consente di creare `myNic1`, `myNic2`, e così via. Di seguito viene riportato un esempio di aggiunta del valore di indice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

È possibile consultare un esempio completo di [creazione di più schede di rete utilizzando i modelli di Resource Manager](../../virtual-network/template-samples.md).

Aggiungere le tabelle di routing al sistema operativo guest. A tale scopo, completare i passaggi descritti in [Configurare il sistema operativo guest per più schede di interfaccia di rete](#configure-guest-os-for- multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Configurare il sistema operativo guest per più schede di rete

I passaggi precedenti hanno consentito di creare una rete virtuale e una subnet, collegare le schede di rete e quindi creare una macchina virtuale. Non sono stati creati un indirizzo IP pubblico e le regole del gruppo di sicurezza di rete che consentono il traffico SSH. Per configurare il sistema operativo guest per più schede di rete, è necessario consentire le connessioni remote ed eseguire i comandi in locale nella macchina virtuale.

Per consentire il traffico SSH, creare una regola del gruppo di sicurezza di rete con [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create), come indicato di seguito:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Creare un indirizzo IP pubblico con [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) e assegnarla alla prima scheda di rete con [az network nic ip-config update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update):

```azurecli
az network public-ip-address create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip-addres myPublicIP
```

Per visualizzare l'indirizzo IP pubblico della macchina virtuale, usare [az vm show](/cli/azure/vm#az-vm-show), come indicato di seguito:

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Connettersi all'indirizzo IP pubblico della macchina virtuale tramite SSH. Il nome utente predefinito specificato in un passaggio precedente era *azureuser*. Specificare il proprio nome utente e l'indirizzo IP pubblico:

```bash
ssh azureuser@137.117.58.232
```

Per consentire l'invio a o da un'interfaccia di rete secondaria, è necessario aggiungere manualmente route persistenti al sistema operativo per ogni interfaccia di rete secondaria. In questo articolo, *eth1* è l'interfaccia secondaria. Le istruzioni per l'aggiunta di route persistenti al sistema operativo variano in base alla distribuzione. Per istruzioni, vedere la documentazione relativa alla distribuzione.

Quando si aggiunge la route al sistema operativo, è l'indirizzo del gateway è *.1* per qualsiasi subnet in cui è presente l'interfaccia di rete. Se ad esempio all'interfaccia di rete è assegnato l'indirizzo *10.0.2.4*, il gateway specificato per la route è *10.0.2.1*. È possibile definire una rete specifica per la destinazione della route o specificare *0.0.0.0* come destinazione, se si vuole che tutto il traffico per l'interfaccia venga inviato attraverso il gateway specificato. Il gateway per ogni subnet viene gestito dalla rete virtuale.

Dopo aver aggiunto la route per un'interfaccia secondaria, verificare che la route sia presente nella tabella di route con `route -n`. L'output di esempio seguente è relativo alla tabella di route che contiene le due interfacce di rete aggiunte alla macchina virtuale in questo articolo:

```bash
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.1.1        0.0.0.0         UG    0      0        0 eth0
0.0.0.0         10.0.2.1        0.0.0.0         UG    0      0        0 eth1
10.0.1.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 eth1
168.63.129.16   10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
169.254.169.254 10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
```

Verificare che la route aggiunta sia persistente tra un riavvio e l'altro controllando nuovamente la tabella di route dopo un riavvio. Per testare la connettività, è ad esempio possibile immettere il comando seguente, dove *eth1* è il nome di un'interfaccia di rete secondaria:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Passaggi successivi
Quando si cerca di creare una macchina virtuale con più schede di rete, consultare [Dimensioni per le macchine virtuali di Linux](sizes.md). Prestare attenzione al numero massimo di schede di rete supportato per ogni dimensione della macchina virtuale.

Per proteggere ulteriormente le macchine virtuali, usare l'accesso Just-In-Time alle macchine virtuali. Questa funzionalità consente di aprire le regole del gruppo di sicurezza di rete per il traffico SSH quando necessario e per un periodo di tempo definito. Per altre informazioni, vedere [Gestire l'accesso alle macchine virtuali con la funzionalità JIT (Just-in-Time)](../../security-center/security-center-just-in-time.md).
