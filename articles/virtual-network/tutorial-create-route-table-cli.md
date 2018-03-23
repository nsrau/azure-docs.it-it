---
title: Instradare il traffico di rete - Interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come instradare il traffico di rete con una tabella di route usando l'interfaccia della riga di comando di Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/02/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 3c16c774fa1c8a5c8bf50b4f4f9d0bfb283318e3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Instradare il traffico di rete con una tabella di route usando l'interfaccia della riga di comando di Azure

Per impostazione predefinita, Azure instrada automaticamente il traffico tra tutte le subnet di una rete virtuale. È possibile creare le proprie route per eseguire l'override del routing predefinito di Azure. La possibilità di creare route personalizzate è utile se, ad esempio, si vuole instradare il traffico tra subnet attraverso un firewall. In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare una tabella di route
> * Creare una route
> * Associare una tabella di route a una subnet della rete virtuale
> * Testare il routing
> * Risolvere gli errori di routing

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questa guida introduttiva richiede la versione 2.0.28 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Creare una tabella di route

Per impostazione predefinita, Azure instrada il traffico tra tutte le subnet in una rete virtuale. Per altre informazioni sulle route predefinite di Azure, vedere [Route di sistema](virtual-networks-udr-overview.md). Per eseguire l'override del routing predefinito di Azure, si crea una tabella di route contenente route, quindi la si associa a una subnet della rete virtuale.

Prima di poter creare una tabella di route, creare un gruppo di risorse con [az group create](/cli/azure/group#az_group_create) per tutte le risorse create in questo articolo. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
``` 

Creare una tabella di route con [az network route-table create](/cli/azure/network/route#az_network_route_table_create). L'esempio seguente crea una tabella di route denominata *myRouteTablePublic*. 

```azurecli-interactive 
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Creare una route

Una tabella di route può contenere nessuna o più route. Creare una route nella tabella di route con [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create). 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

La route indirizzerà tutto il traffico destinato al prefisso dell'indirizzo 10.0.1.0/24 attraverso un'appliance virtuale di rete con l'indirizzo IP 10.0.2.4. L'appliance virtuale di rete e la subnet con il prefisso dell'indirizzo specificato verranno create nei passaggi successivi. La route esegue l'override del routing predefinito di Azure, che instrada il traffico tra subnet direttamente. Ogni route specifica un tipo di hop successivo. Il tipo di hop successivo indica ad Azure come instradare il traffico. In questo esempio il tipo di hop successivo è *VirtualAppliance*. Per altre informazioni su tutti i tipi di hop successivi disponibili in Azure e quando usarli, vedere [Tipi di hop successivi](virtual-networks-udr-overview.md#custom-routes).

## <a name="associate-a-route-table-to-a-subnet"></a>Associare una route a una subnet

Prima di poter associare una tabella di route a una subnet, è necessario creare una rete virtuale e una subnet. Creare una rete virtuale con una subnet con [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Creare due subnet aggiuntive con [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create).

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

È possibile associare una tabella di route a zero o più subnet. A una subnet può essere associata una o nessuna tabella di route. Il traffico in uscita da una subnet viene instradato in base alle route predefinite di Azure e alle eventuali route personalizzate che sono state aggiunte a una tabella di route associata a una subnet. Associare la tabella di route *myRouteTablePublic* alla subnet *Public* con [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

Prima di distribuire tabelle di route per l'uso in produzione, è consigliabile familiarizzare con il [routing in Azure](virtual-networks-udr-overview.md) e i [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="test-routing"></a>Testare il routing

Per testare il routing, si creerà una macchina virtuale che funge da appliance virtuale di rete attraverso la quale viene instradata la route creata in un passaggio precedente. Dopo aver creato l'appliance virtuale di rete, si distribuirà una macchina virtuale nelle subnet *Public* e *Private*. Verrà quindi instradato il traffico dalla subnet *Public* alla subnet *Private* attraverso l'appliance virtuale di rete.

### <a name="create-a-network-virtual-appliance"></a>Creare un'appliance virtuale di rete

In un passaggio precedente è stata creata una route che ha specificato un'appliance virtuale di rete come tipo di hop successivo. Una macchina virtuale che esegue un'applicazione di rete è spesso definita "appliance virtuale di rete". Negli ambienti di produzione l'appliance virtuale di rete distribuita è spesso una macchina virtuale configurata in precedenza. Sono disponibili diverse appliance virtuali di rete in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1). In questo articolo viene creata una macchina virtuale di base. 

Creare un'appliance virtuale di rete nella subnet *DMZ* con [az vm create](/cli/azure/vm#az_vm_create). Quando si crea una macchina virtuale, Azure crea e assegna un indirizzo IP pubblico alla macchina virtuale, per impostazione predefinita. Il parametro `--public-ip-address ""` indica ad Azure di non creare e assegnare un indirizzo IP pubblico alla macchina virtuale, perché non è necessario connettersi alla macchina virtuale da Internet. Il comando crea le chiavi SSH, se non esistono già in una posizione predefinita. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`.

```azure-cli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

La creazione della macchina virtuale richiede alcuni minuti. Prima di continuare con il passaggio successivo, attendere che Azure finisca di creare la macchina virtuale e restituisca l'output sulla macchina virtuale. Negli ambienti di produzione l'appliance virtuale di rete distribuita è spesso una macchina virtuale configurata in precedenza. Sono disponibili diverse appliance virtuali di rete in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1).

È necessario abilitare l'inoltro IP per ogni [interfaccia di rete](virtual-network-network-interface.md) di Azure associata a una macchina virtuale che inoltra il traffico destinato a indirizzi IP non assegnati all'interfaccia di rete. Quando è stata creata l'appliance virtuale di rete nella subnet *DMZ*, Azure ha creato automaticamente un'interfaccia di rete denominata *myVmNvaVMNic*, ha associato l'interfaccia di rete alla macchina virtuale e ha assegnato l'indirizzo IP privato *10.0.2.4* all'interfaccia di rete. Abilitare l'inoltro IP per l'interfaccia di rete con [az network nic update](/cli/azure/network/nic#az_network_nic_update).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

All'interno della macchina virtuale, il sistema operativo o un'applicazione in esecuzione all'interno della macchina virtuale deve anche poter trasferire il traffico. Quando si implementa un'appliance virtuale di rete in un ambiente di produzione, l'appliance in genere filtra, registra o esegue un'altra funzione prima di trasferire il traffico. In questo articolo, tuttavia, il sistema operativo trasferisce semplicemente tutto il traffico ricevuto. Abilitare l'inoltro IP nel sistema operativo della macchina virtuale con [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set), che esegue un comando che abilita l'inoltro IP nel sistema operativo.

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
L'esecuzione del comando può richiedere fino a un minuto.

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare due macchine virtuali nella rete virtuale per poter verificare che il traffico dalla subnet *Public* venga indirizzato alla subnet *Private* attraverso l'appliance virtuale di rete in un passaggio successivo. 

Creare una macchina virtuale nella subnet *Public* con [az vm create](/cli/azure/vm#az_vm_create). Il parametro `--no-wait` consente ad Azure di eseguire il comando in background, pertanto è possibile continuare con il comando successivo. Per semplificare questo articolo, viene usata una password. Le chiavi in genere vengono usate per le distribuzioni di produzione. Se si usano chiavi, occorre configurare anche l'inoltro dell'agente SSH. Per altre informazioni, vedere la documentazione del client SSH. Sostituire `<replace-with-your-password>` nel comando seguente con una password a scelta.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

Creare una macchina virtuale nella subnet *Private*.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

La creazione della macchina virtuale richiede alcuni minuti. Dopo la creazione della macchina virtuale, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmMgmt",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```
Prendere nota di **publicIpAddress**. Questo indirizzo viene usato per accedere alla macchina virtuale da Internet in un passaggio successivo.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>Instradare il traffico attraverso un'appliance virtuale di rete

Usare il comando seguente per creare una sessione SSH con la macchina virtuale *myVmMgmt*. Sostituire *<publicIpAddress>* con l'indirizzo IP pubblico della macchina virtuale. Nell'esempio precedente l'indirizzo IP è *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Quando viene richiesto di immettere una password, specificare la password selezionata in [Creare macchine virtuali](#create-virtual-machines).

Usare il comando seguente per installare traceroute nella macchina virtuale *myVmMgmt*:

```bash 
sudo apt-get install traceroute
```

Usare il comando seguente per testare il routing per il traffico di rete alla macchina virtuale *myVmWeb* dalla macchina virtuale *myVmMgmt*.

```bash
traceroute myvmweb
```

La risposta restituita è simile all'esempio seguente:

```bash
traceroute to myvmweb (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Come si può osservare, il traffico viene instradato direttamente dalla macchina virtuale *myVmMgmt* alla macchina virtuale *myVmWeb*. Le route predefinite di Azure instradano il traffico direttamente tra subnet. 

Usare il comando seguente per connettersi tramite SSH alla macchina virtuale *myVmWeb* dalla macchina virtuale *myVmMgmt*:

```bash 
ssh azureuser@myVmWeb
```

Usare il comando seguente per installare traceroute nella macchina virtuale *myVmWeb*:

```bash 
sudo apt-get install traceroute
```

Usare il comando seguente per testare il routing per il traffico di rete alla macchina virtuale *myVmMgmt* dalla macchina virtuale *myVmWeb*.

```bash
traceroute myvmmgmt
```

La risposta restituita è simile all'esempio seguente:

```bash
traceroute to myvmmgmt (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
È possibile notare che il primo hop è 10.0.2.4, cioè l'indirizzo IP privato dell'appliance virtuale di rete. Il secondo hop è 10.0.1.4, l'indirizzo IP della macchina virtuale *myVmMgmt*. La route aggiunta alla tabella di route *myRouteTablePublic* e associata alla subnet *Public* ha causato il routing del traffico di Azure attraverso l'appliance virtuale di rete, invece che direttamente alla subnet *Private*.

Chiudere le sessioni SSH a entrambe le macchine virtuali *myVmWeb* e *myVmMgmt*.

## <a name="troubleshoot-routing"></a>Risolvere gli errori di routing

Come illustrato nei passaggi precedenti, Azure applica route predefinite, di cui è possibile, facoltativamente, eseguire l'override con route personalizzate. In alcuni casi, il traffico non può essere instradato come previsto. Usare [az network watcher show-next-hop](/cli/azure/network/watcher#az_network_watcher_show_next_hop) per determinare come viene instradato il traffico tra due macchine virtuali. Ad esempio, il comando seguente testa il routing del traffico dalla macchina virtuale *myVmWeb* (10.0.0.4) alla macchina virtuale *myVmMgmt* (10.0.1.4):

```azurecli-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region.
az network watcher configure --locations eastus --resource-group myResourceGroup --enabled true

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 10.0.1.4 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVmWeb \
  --out table
```
Dopo una breve attesa viene restituito l'output seguente:

```azurecli
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

L'output indica che l'indirizzo IP dell'hop successivo per il traffico proveniente dalla macchina virtuale *myVmWeb* alla macchina virtuale *myVmMgmt* è 10.0.2.4 (la macchina virtuale *myVmNva*), che il tipo di hop successivo è *VirtualAppliance* e che la tabella di route che determina il routing è *myRouteTablePublic*.

Le route valide per ogni interfaccia di rete sono una combinazione delle route predefinite di Azure e di qualsiasi route definita dall'utente. Visualizzare tutte le route valide per un'interfaccia di rete in una macchina virtuale con [az network nic show-effective-route-table](/cli/azure/network/nic#az_network_nic_show_effective_route_table). Ad esempio, per visualizzare le route valide per l'interfaccia di rete *MyVmWebVMNic* nella macchina virtuale *myVmWeb*, immettere il comando seguente:

```azurecli-interactive
az network nic show-effective-route-table \
  --name MyVmWebVMNic \
  --resource-group myResourceGroup
```

Vengono restituite tutte le route predefinite e la route che è stata aggiunta in un passaggio precedente.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, usare [az group delete](/cli/azure/group#az_group_delete) per rimuoverli.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata creata una tabella di route per poi associarla a una subnet. È stata creata un'appliance virtuale di rete che ha instradato il traffico da una subnet pubblica a una subnet privata. Nonostante sia possibile implementare molte risorse di Azure all'interno di una rete virtuale, non è possibile implementare le risorse per alcuni servizi PaaS di Azure in una rete virtuale. È comunque possibile limitare l'accesso alle risorse di alcuni servizi PaaS di Azure al traffico da una sola subnet della rete virtuale. Passare all'esercitazione successiva per informazioni su come limitare l'accesso alla rete alle risorse PaaS di Azure.

> [!div class="nextstepaction"]
> [Limitare l'accesso alla rete alle risorse PaaS](virtual-network-service-endpoints-configure.md#azure-cli)
