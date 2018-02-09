---
title: Creare una rete virtuale - Interfaccia della riga di comando di Azure | Microsoft Docs
description: Apprendere a creare rapidamente una rete virtuale usando l'interfaccia della riga di comando di Azure. Una rete virtuale consente a molti tipi di risorse di Azure di comunicare privatamente tra loro.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: b2fc8a622549a9858c6c769a7e648fe07a3d01c1
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Creare una rete virtuale usando l'interfaccia della riga di comando di Azure

Questo articolo illustra come creare una rete virtuale. Dopo aver creato una rete virtuale, si distribuiranno nella rete due macchine virtuali affinché comunichino privatamente tra loro.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione installata, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*. Tutte le risorse di Azure vengono create all'interno di una posizione (o area) di Azure.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Creare una rete virtuale con il comando [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). L'esempio seguente crea una rete virtuale predefinita denominata *myVirtualNetwork* con una subnet denominata *default*. Poiché non è specificata una posizione, Azure crea la rete virtuale nella stessa posizione del gruppo di risorse.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

Dopo la creazione della rete virtuale, viene visualizzata la parte seguente delle informazioni restituite.

```azurecli
"newVNet": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/16"
```

A tutte le reti virtuali vengono assegnati uno o più prefissi indirizzo. Poiché durante la creazione della rete virtuale non è stato specificato un prefisso indirizzo, Azure ha definito lo spazio indirizzi 10.0.0.0/16 per impostazione predefinita. Lo spazio indirizzi è specificato nella notazione CIDR. Lo spazio indirizzi 10.0.0.0/16 include 10.0.0.0-10.0.255.254.

Un'altra parte delle informazioni restituite è il prefisso indirizzo **addressPrefix** di *10.0.0.0/24* per la subnet *default* specificata nel comando. Una rete virtuale contiene zero o più subnet. Il comando ha creato una sola subnet denominata *default*, ma non è stato specificato alcun prefisso indirizzo per la subnet. Quando non viene specificato un prefisso indirizzo per una rete virtuale o una subnet, Azure definisce 10.0.0.0/24 come prefisso indirizzo per la prima subnet per impostazione predefinita. Di conseguenza, la subnet include 10.0.0.0-10.0.0.254, ma sono disponibili solo gli indirizzi 10.0.0.4-10.0.0.254 poiché Azure riserva i primi quattro indirizzi (0-3) e l'ultimo indirizzo di ogni subnet.

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Una rete virtuale consente a vari tipi di risorse di Azure di comunicare privatamente tra loro. Uno dei tipi di risorsa che è possibile distribuire in una rete virtuale è una macchina virtuale. Creare due macchine virtuali nella rete virtuale per poter convalidare e comprendere il funzionamento delle comunicazioni tra macchine virtuali in una rete virtuale in un passaggio successivo.

Crea una macchina virtuale usando il comando [az vm create](/cli/azure/vm#az_vm_create). L'esempio seguente crea una rete virtuale denominata *myVm1*. Il comando crea le chiavi SSH, se non esistono già in una posizione predefinita. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`. L'opzione `--no-wait` crea la macchina virtuale in background, pertanto è possibile continuare con il passaggio successivo.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

Azure crea automaticamente la macchina virtuale nella subnet *default* della rete virtuale *myVirtualNetwork*, in quanto la rete virtuale è presente nel gruppo di risorse e nel comando non è specificata alcuna rete virtuale o subnet. Azure DHCP ha assegnato automaticamente 10.0.0.4 alla macchina virtuale durante la creazione poiché è il primo indirizzo disponibile nella subnet *default*. La posizione in cui viene creata una macchina virtuale deve essere la stessa in cui si trova la rete virtuale. Sebbene sia così in questo articolo, non è necessario che la macchina virtuale si trovi nello stesso gruppo di risorse della rete virtuale.

Creare una seconda macchina virtuale. Per impostazione predefinita, Azure crea anche questa macchina virtuale nella subnet *default*.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

La creazione della macchina virtuale richiede alcuni minuti. Dopo la creazione della macchina virtuale, l'interfaccia della riga di comando di Azure restituisce un output simile all'esempio seguente: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm1",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Nell'esempio si nota che **privateIpAddress** è *10.0.0.5*. Azure DHCP ha assegnato automaticamente *10.0.0.5* alla macchina virtuale poiché era il primo indirizzo disponibile nella subnet *default*. Prendere nota di **publicIpAddress**. Questo indirizzo viene usato per accedere alla macchina virtuale da Internet in un passaggio successivo. L'indirizzo IP pubblico non viene assegnato dalla rete virtuale o dai prefissi indirizzo della subnet. Gli indirizzi IP pubblici vengono assegnati da un [pool di indirizzi assegnato a ogni area di Azure](https://www.microsoft.com/download/details.aspx?id=41653). Azure sa quale indirizzo IP pubblico viene assegnato a una macchina virtuale, mentre il sistema operativo in esecuzione in una macchina virtuale non è a conoscenza di eventuali indirizzi IP pubblici assegnati.

## <a name="connect-to-a-virtual-machine"></a>Connettersi a una macchina virtuale

Usare il comando seguente per creare una sessione SSH con la macchina virtuale *myVm2*. Sostituire `<publicIpAddress>` con l'indirizzo IP pubblico della macchina virtuale. Nell'esempio precedente l'indirizzo IP è *40.68.254.142*.

```bash 
ssh <publicIpAddress>
```

## <a name="validate-communication"></a>Convalidare la comunicazione

Per verificare le comunicazioni con *myVm1* da *myVm2*, immettere il comando seguente:

```bash
ping myVm1 -c 4
```

Si riceveranno quattro risposte da *10.0.0.4*. È possibile comunicare con *myVm1* da *myVm2* perché a entrambe le macchine virtuali sono stati assegnati indirizzi IP privati dalla subnet *default*. È possibile eseguire il ping mediante il nome host poiché Azure garantisce automaticamente la risoluzione dei nomi DNS per tutti gli host all'interno di una rete virtuale.

Per verificare le comunicazioni verso l'esterno su Internet, usare il comando seguente:

```bash
ping bing.com -c 4
```

Si riceveranno quattro risposte da bing.com. Per impostazione predefinita, tutte le macchine virtuali in una rete virtuale possono comunicare verso l'esterno su Internet.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, usare il comando [az group delete](/cli/azure/group#az_group_delete) per rimuoverli. Chiudere la sessione SSH per la macchina virtuale e quindi eliminare le risorse.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata distribuita una rete virtuale predefinita con una subnet e due macchine virtuali. Per informazioni su come creare una rete virtuale personalizzata con più subnet ed eseguire attività di gestione di base, procedere all'esercitazione per la creazione e la gestione di una rete virtuale personalizzata.


> [!div class="nextstepaction"]
> [Create a custom virtual network and manage it](virtual-networks-create-vnet-arm-pportal.md#azure-cli) (Creare e gestire una rete virtuale personalizzata)
