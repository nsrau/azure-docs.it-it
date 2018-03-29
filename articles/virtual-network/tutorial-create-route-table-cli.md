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
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 871b562fa12b93d1b65e23ca58615d35ef6bb34b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Instradare il traffico di rete con una tabella di route usando l'interfaccia della riga di comando di Azure

Per impostazione predefinita, Azure instrada automaticamente il traffico tra tutte le subnet di una rete virtuale. È possibile creare le proprie route per eseguire l'override del routing predefinito di Azure. La possibilità di creare route personalizzate è utile se, ad esempio, si vuole indirizzare il traffico tra subnet attraverso un'appliance virtuale di rete. In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare una tabella di route
> * Creare una route
> * Creare una rete virtuale con più subnet
> * Associare una route a una subnet
> * Creare un'appliance virtuale di rete che indirizza il traffico
> * Distribuire le macchine virtuali in subnet diverse
> * Indirizzare il traffico da una subnet a un'altra attraverso un'appliance virtuale di rete

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questa guida introduttiva richiede la versione 2.0.28 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Creare una tabella di route

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

Creare una route nella tabella di route con [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create). 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

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

Associare la tabella di route *myRouteTablePublic* alla subnet *Public* con [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>Creare un'appliance virtuale di rete

Un'appliance virtuale di rete è una macchina virtuale che svolge una funzione di rete, ad esempio il routing, la protezione tramite firewall o l'ottimizzazione della WAN.

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

La creazione della VM richiede alcuni minuti. Prima di continuare con il passaggio successivo, attendere che Azure finisca di creare la macchina virtuale e restituisca il relativo output. 

Perché un'interfaccia di rete possa inoltrare il traffico ricevuto, non destinato al rispettivo indirizzo IP, occorre abilitare l'inoltro IP per l'interfaccia di rete. Abilitare l'inoltro IP per l'interfaccia di rete con [az network nic update](/cli/azure/network/nic#az_network_nic_update).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

All'interno della macchina virtuale è necessario che il sistema operativo o un'applicazione in esecuzione nella macchina virtuale sia anche in grado di inoltrare il traffico di rete. Abilitare l'inoltro IP all'interno del sistema operativo della macchina virtuale con [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set):

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
L'esecuzione del comando può richiedere fino a un minuto.

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare due macchine virtuali nella rete virtuale per poter verificare che il traffico dalla subnet *Public* venga indirizzato alla subnet *Private* attraverso l'appliance virtuale di rete in un passaggio successivo. 

Creare una macchina virtuale nella subnet *Public* con [az vm create](/cli/azure/vm#az_vm_create). Il parametro `--no-wait` consente ad Azure di eseguire il comando in background, pertanto è possibile continuare con il comando successivo. Per semplificare questo articolo, viene usata una password. Le chiavi in genere vengono usate per le distribuzioni di produzione. Se si usano chiavi, occorre configurare anche l'inoltro dell'agente SSH. Per altre informazioni, vedere la documentazione del client SSH. Sostituire `<replace-with-your-password>` nel comando seguente con una password a scelta.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
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
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

La creazione della VM richiede alcuni minuti. Dopo aver creato la macchina virtuale, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPrivate",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```
Prendere nota di **publicIpAddress**. Questo indirizzo viene usato per accedere alla macchina virtuale da Internet in un passaggio successivo.

## <a name="route-traffic-through-an-nva"></a>Indirizzare il traffico attraverso un'appliance virtuale di rete

Usare il comando seguente per creare una sessione SSH con la macchina virtuale *myVmPrivate*. Sostituire *<publicIpAddress>* con l'indirizzo IP pubblico della macchina virtuale. Nell'esempio precedente l'indirizzo IP è *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Quando viene richiesto di immettere una password, specificare la password selezionata in [Creare macchine virtuali](#create-virtual-machines).

Usare il comando seguente per installare traceroute nella macchina virtuale *myVmPrivate*:

```bash 
sudo apt-get install traceroute
```

Usare il comando seguente per testare il routing del traffico di rete alla macchina virtuale *myVmPublic* dalla macchina virtuale *myVmPrivate*.

```bash
traceroute myVmPublic
```

La risposta restituita è simile all'esempio seguente:

```bash
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Come si può osservare, il traffico viene indirizzato direttamente dalla macchina virtuale *myVmPrivate* alla macchina virtuale *myVmPublic*. Le route predefinite di Azure indirizzano il traffico direttamente tra subnet. 

Usare il comando seguente per stabilire una connessione SSH alla macchina virtuale *myVmPublic* dalla macchina virtuale *myVmPrivate*:

```bash 
ssh azureuser@myVmPublic
```

Usare il comando seguente per installare traceroute nella macchina virtuale *myVmPublic*:

```bash 
sudo apt-get install traceroute
```

Usare il comando seguente per testare il routing del traffico di rete alla macchina virtuale *myVmPrivate* dalla macchina virtuale *myVmPublic*.

```bash
traceroute myVmPrivate
```

La risposta restituita è simile all'esempio seguente:

```bash
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
Come si può notare, il primo hop è 10.0.2.4, cioè l'indirizzo IP privato dell'appliance virtuale di rete. Il secondo hop è 10.0.1.4, ossia l'indirizzo IP privato della macchina virtuale *myVmPrivate*. A causa della route aggiunta alla tabella di route *myRouteTablePublic* e associata alla subnet *Public*, Azure ha indirizzato il traffico di Azure attraverso l'appliance virtuale di rete, invece che direttamente alla subnet *Private*.

Chiudere le sessioni SSH a entrambe le macchine virtuali *myVmPublic* e *myVmPrivate*.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, usare [az group delete](/cli/azure/group#az_group_delete) per rimuoverli.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata creata una tabella di route per poi associarla a una subnet. È stata creata una semplice appliance virtuale di rete che ha indirizzato il traffico da una subnet pubblica a una subnet privata. Distribuire una varietà di appliance virtuali di rete preconfigurate che svolgono funzioni di rete come la protezione tramite firewall e l'ottimizzazione della WAN da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Prima di distribuire tabelle di route per l'uso in produzione, è consigliabile familiarizzare con il [routing in Azure](virtual-networks-udr-overview.md), la [gestione delle tabelle di route](manage-route-table.md) e i [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

Benché sia possibile distribuire molte risorse di Azure all'interno di una rete virtuale, non è possibile distribuire le risorse per alcuni servizi PaaS di Azure in una rete virtuale. È comunque possibile limitare l'accesso alle risorse di alcuni servizi PaaS di Azure al traffico da una sola subnet della rete virtuale. Passare all'esercitazione successiva per informazioni su come limitare l'accesso alla rete alle risorse PaaS di Azure.

> [!div class="nextstepaction"]
> [Limitare l'accesso alla rete alle risorse PaaS](tutorial-restrict-network-access-to-resources-cli.md)
