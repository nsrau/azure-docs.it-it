---
title: Connettere reti virtuali con il peering di rete virtuale - Interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come connettere reti virtuali con il peering di rete virtuale.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: df56f2e3e13f80e7ce2c2b6c9cffeac3d03776e5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Connettere reti virtuali con il peering di rete virtuale usando l'interfaccia della riga di comando di Azure

È possibile connettere due reti virtuali tra loro con il peering di rete virtuale. Dopo la connessione, le risorse delle due reti virtuali possono comunicare tra loro con la stessa larghezza di banda e la stessa latenza che si avrebbe se fossero nella stessa rete virtuale. Questo articolo illustra i processi di creazione e peering di due reti virtuali. Si apprenderà come:

> [!div class="checklist"]
> * Creare due reti virtuali
> * Creare un peering tra reti virtuali
> * Testare il peering

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="create-virtual-networks"></a>Creare reti virtuali

Prima di creare una rete virtuale, è necessario creare un gruppo di risorse per la rete virtuale e tutte le altre risorse create in questo articolo. Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group#az_group_create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Creare una rete virtuale con [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). L'esempio seguente crea una rete virtuale denominata *myVirtualNetwork1* con il prefisso dell'indirizzo *10.0.0.0/16*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

Creare una rete virtuale denominata *myVirtualNetwork2* con il prefisso dell'indirizzo *10.1.0.0/16*. Il prefisso dell'indirizzo non si sovrappone al prefisso dell'indirizzo della rete virtuale *myVirtualNetwork1*. Non è possibile creare un peering di reti virtuali i cui prefissi degli indirizzi si sovrappongono.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Creare un peering di reti virtuali

I peering vengono stabiliti tra gli ID delle reti virtuali, quindi occorre prima di tutto ottenere l'ID di ogni rete virtuale con [az network vnet show](/cli/azure/network/vnet#az_network_vnet_show) e memorizzarlo in una variabile.

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

Creare un peering da *myVirtualNetwork1* a *myVirtualNetwork2* con [az network vnet peering create](/cli/azure/network/vnet/peering#az_network_vnet_peering_create). Se non si specifica il parametro `--allow-vnet-access`, il peering viene stabilito, ma non è possibile alcuna comunicazione attraverso di esso.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet-id $vNet2Id \
  --allow-vnet-access
```

Come si può vedere nell'output restituito dopo l'esecuzione del comando precedente, lo stato **peeringState** è *Initiated*. Il peering rimane nello stato *Initiated* finché non si crea il peering da *myVirtualNetwork2* a *myVirtualNetwork1*. Creare un peering da *myVirtualNetwork2* a *myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet-id $vNet1Id \
  --allow-vnet-access
```

Come si può vedere nell'output restituito dopo l'esecuzione del comando precedente, lo stato **peeringState** è ora *Connected*. Azure ha cambiato anche lo stato del peering di *myVirtualNetwork1-myVirtualNetwork2* in *Connected*. Verificare che lo stato del peering di *myVirtualNetwork1-myVirtualNetwork2* sia cambiato in *Connected* con [az network vnet peering show](/cli/azure/network/vnet/peering#az_network_vnet_peering_show).

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Le risorse in una rete virtuale non possono comunicare con le risorse nell'altra rete virtuale finché il parametro **peeringState** relativo ai peering in entrambe le reti virtuali non cambia in *Connected*. 

I peering vengono stabiliti tra due reti virtuali ma non sono transitivi. Pertanto se, ad esempio, si vuole creare un peering anche da *myVirtualNetwork2* a *myVirtualNetwork3*, occorre creare un peering aggiuntivo tra le reti virtuali *myVirtualNetwork2* e *myVirtualNetwork3*. Anche se *myVirtualNetwork1* è connessa tramite peering a *myVirtualNetwork2*, le risorse all'interno di *myVirtualNetwork1* potrebbero accedere alle risorse di *myVirtualNetwork3* solo se *myVirtualNetwork1* fosse connessa tramite peering anche a *myVirtualNetwork3*. 

Prima di creare un peering di reti virtuali in ambiente di produzione, è consigliabile acquisire familiarità con i concetti di [peering](virtual-network-peering-overview.md), [gestione del peering](virtual-network-manage-peering.md) e [limiti delle reti virtuali](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Questo articolo illustra un peering tra due reti virtuali nella stessa sottoscrizione e posizione, ma è possibile creare un peering anche tra reti virtuali in [aree diverse](#register) e in [sottoscrizioni di Azure diverse](create-peering-different-subscriptions.md#cli). Si possono anche creare [progettazioni di reti hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) con il peering.

## <a name="test-peering"></a>Testare il peering

Per testare la comunicazione di rete tra macchine virtuali in reti virtuali diverse tramite un peering, distribuire una macchina virtuale in ogni subnet e quindi stabilire la comunicazione tra le macchine virtuali. 

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare una macchina virtuale con [az vm create](/cli/azure/vm#az_vm_create). L'esempio seguente crea una macchina virtuale denominata *myVm1* nella rete virtuale *myVirtualNetwork1*. Il comando crea le chiavi SSH, se non esistono già in una posizione predefinita. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`. L'opzione `--no-wait` crea la macchina virtuale in background, pertanto è possibile continuare con il passaggio successivo.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

Azure assegna automaticamente 10.0.0.4 come indirizzo IP privato della macchina virtuale, perché 10.0.0.4 è il primo indirizzo IP disponibile nella subnet *Subnet1* della rete virtuale *myVirtualNetwork1*. 

Creare una macchina virtuale nella rete virtuale *myVirtualNetwork2*.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

La creazione della macchina virtuale richiede alcuni minuti. Dopo la creazione della macchina virtuale, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Come si può vedere nell'output di esempio, **privateIpAddress** è *10.1.0.4*. Azure DHCP ha assegnato automaticamente 10.1.0.4 alla macchina virtuale perché è il primo indirizzo disponibile nella subnet *Subnet1* della rete virtuale *myVirtualNetwork2*. Prendere nota di **publicIpAddress**. Questo indirizzo viene usato per accedere alla macchina virtuale da Internet in un passaggio successivo.

### <a name="test-virtual-machine-communication"></a>Testare la comunicazione tra le macchine virtuali

Usare il comando seguente per creare una sessione SSH con la macchina virtuale *myVm2*. Sostituire `<publicIpAddress>` con l'indirizzo IP pubblico della macchina virtuale. Nell'esempio precedente l'indirizzo IP pubblico è *13.90.242.231*.

```bash 
ssh <publicIpAddress>
```

Effettuare il ping della macchina virtuale in *myVirtualNetwork1*.

```bash 
ping 10.0.0.4 -c 4
```

Si riceveranno quattro risposte. Se si effettua il ping in base al nome della macchina virtuale (*myVm1*) anziché in base all'indirizzo IP, il ping non riesce poiché *myVm1* è un nome host sconosciuto. La risoluzione dei nomi predefinita di Azure funziona tra macchine virtuali all'interno della stessa rete virtuale, ma non tra macchine virtuali in reti virtuali diverse. Per risolvere i nomi tra reti virtuali, è necessario [distribuire il proprio server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md) o usare [domini privati del DNS di Azure](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Chiudere la sessione SSH alla macchina virtuale *myVm2*. 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, usare [az group delete](/cli/azure/group#az_group_delete) per rimuoverli.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

**<a name="register"></a>Eseguire la registrazione per l'anteprima del peering di reti virtuali globale**

Il peering reti virtuali nella stessa area è disponibile a livello generale. Il peering di reti virtuali in aree diverse è attualmente disponibile in anteprima. Vedere gli [aggiornamenti delle reti virtuali](https://azure.microsoft.com/updates/?product=virtual-network) per le aree disponibili. Per eseguire il peering di reti virtuali tra aree diverse, è prima necessario registrarsi per l'anteprima completando i passaggi seguenti (all'interno della sottoscrizione in cui si trova ogni rete virtuale di cui si vuole eseguire il peering):

1. Registrare all'anteprima immettendo i comandi seguenti:

  ```azurecli-interactive
  az feature register --name AllowGlobalVnetPeering --namespace Microsoft.Network
  az provider register --name Microsoft.Network
  ```

2. Verificare di essere registrati per l'anteprima immettendo il comando seguente:

  ```azurecli-interactive
  az feature show --name AllowGlobalVnetPeering --namespace Microsoft.Network
  ```

  Se si cerca di eseguire il peering tra reti virtuali di aree diverse prima che l'output di **RegistrationState** ricevuto dopo l'esecuzione del comando precedente diventi **Registered** per entrambe le sottoscrizioni, il peering non riesce.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come connettere due reti virtuali con il peering di rete virtuale. È possibile [connettere il proprio computer a una rete virtuale](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) tramite una VPN e interagire con le risorse di una rete virtuale o di reti virtuali associate tramite peering.

Vedere gli esempi di script riutilizzabili per completare molte delle attività illustrate negli articoli relativi alle reti virtuali.

> [!div class="nextstepaction"]
> [Esempi di script sulle reti virtuali](../networking/cli-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
