---
title: Creare una rete virtuale di Azure con più subnet - Interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come creare una rete virtuale con più subnet usando l'interfaccia della riga di comando di Azure.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 0b0bfae02147910d98231d7c93f5ab260f26364f
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2018
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-cli"></a>Creare una rete virtuale con più subnet usando l'interfaccia della riga di comando di Azure

Una rete virtuale consente a vari tipi di risorse di Azure di comunicare con Internet e privatamente tra loro. La creazione di più subnet in una rete virtuale consente di segmentare la rete in modo da filtrare o controllare il flusso del traffico tra le subnet. In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Crea rete virtuale
> * Creare una subnet
> * Testare la comunicazione di rete tra le macchine virtuali

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli.md). 

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group#az_group_create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Creare una rete virtuale con [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). L'esempio seguente crea una rete virtuale denominata *myVirtualNetwork* con il prefisso dell'indirizzo *10.0.0.0/16*. Il comando crea una subnet denominata *Public*, con il prefisso dell'indirizzo *10.0.0.0/24*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Poiché non è stata specificata una posizione nel comando precedente, Azure crea la rete virtuale nella stessa posizione del gruppo di risorse *myResourceGroup*. I prefissi **address-prefixes** e **subnet-prefix** sono specificati nella notazione CIDR. Il prefisso dell'indirizzo specificato include gli indirizzi IP 10.0.0.0-10.0.255.254. Il prefisso specificato per la subnet deve essere compreso nel prefisso dell'indirizzo definito per la rete virtuale. Il server DHCP di Azure assegna gli indirizzi IP da un prefisso dell'indirizzo della subnet alle risorse distribuite in una subnet. Azure assegna solo gli indirizzi 10.0.0.4-10.0.0.254 alle risorse distribuite all'interno della subnet **Public**, poiché Azure riserva i primi quattro indirizzi (10.0.0.0-10.0.0.3 per la subnet, in questo esempio) e l'ultimo indirizzo (10.0.0.255 per la subnet, in questo esempio) in ogni subnet.

## <a name="create-a-subnet"></a>Creare una subnet

Creare una subnet con [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). L'esempio seguente crea una subnet denominata *Private* all'interno della rete virtuale *myVirtualNetwork* con il prefisso dell'indirizzo *10.0.1.0/24*. Il prefisso dell'indirizzo deve essere compreso nel prefisso dell'indirizzo definito per la rete virtuale e non può sovrapporsi al prefisso dell'indirizzo di eventuali altre subnet nella rete virtuale.

```azurecli-interactive 
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24
```

Prima di distribuire reti virtuali e subnet di Azure per la produzione, è consigliabile acquisire familiarità con le [considerazioni](manage-virtual-network.md#create-a-virtual-network) relative allo spazio degli indirizzi e i [limiti della rete virtuale](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Dopo che le risorse sono state distribuite nelle subnet, alcune modifiche a livello di rete virtuale e di subnet, come quelle degli intervalli di indirizzi, possono richiedere la ridistribuzione delle risorse di Azure esistenti distribuite all'interno delle subnet.

## <a name="test-network-communication"></a>Testare la comunicazione di rete

Una rete virtuale consente a vari tipi di risorse di Azure di comunicare con Internet e privatamente tra loro. Uno dei tipi di risorsa che è possibile distribuire in una rete virtuale è una macchina virtuale. Creare due macchine virtuali nella rete virtuale allo scopo di testare la comunicazione di rete tra di esse e con Internet in un passaggio successivo.

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare una macchina virtuale con [az vm create](/cli/azure/vm#az_vm_create). L'esempio seguente crea una macchina virtuale denominata *myVmWeb* nella subnet *Public*. Il parametro `--no-wait` consente ad Azure di eseguire il comando in background, pertanto è possibile continuare con il comando successivo. Per semplificare questa esercitazione, viene usata una password. Le chiavi in genere vengono usate per le distribuzioni di produzione. Se si usano chiavi, occorre configurare anche l'inoltro dell'agente SSH per completare i passaggi rimanenti. Per altre informazioni sull'inoltro dell'agente SSH, vedere la documentazione per il client SSH. Sostituire `<replace-with-your-password>` nel comando seguente con una password a scelta.

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

Azure ha assegnato automaticamente 10.0.0.4 come indirizzo IP privato della macchina virtuale, perché 10.0.0.4 è il primo indirizzo IP disponibile nella subnet *Public*. 

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
La creazione della macchina virtuale richiede alcuni minuti. Dopo la creazione della macchina virtuale, l'interfaccia della riga di comando di Azure restituisce un output simile all'esempio seguente: 

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

Nell'output di esempio si nota che **privateIpAddress** è *10.0.1.4*. Azure ha creato un'[interfaccia di rete](virtual-network-network-interface.md), l'ha collegata alla macchina virtuale e ha assegnato all'interfaccia di rete un indirizzo IP privato e un indirizzo **macAddress**. Il server DHCP di Azure ha assegnato automaticamente 10.0.1.4 all'interfaccia di rete poiché è il primo indirizzo IP disponibile nella subnet *Private*. Gli indirizzi MAC e IP privato rimangono assegnati all'interfaccia di rete fino a quando quest'ultima non viene eliminata. 

Prendere nota di **publicIpAddress**. Questo indirizzo viene usato per accedere alla macchina virtuale da Internet in un passaggio successivo. Anche se non è richiesto che a una macchina virtuale sia assegnato un indirizzo IP pubblico, per impostazione predefinita Azure assegna un indirizzo IP pubblico a ogni macchina virtuale creata. Per comunicare da Internet a una macchina virtuale, alla macchina virtuale deve essere assegnato un indirizzo IP pubblico. Tutte le macchine virtuali possono comunicare in uscita con Internet, indipendentemente dal fatto che un indirizzo IP pubblico sia assegnato o meno alla macchina virtuale. Per altre informazioni sulle connessioni Internet in uscita in Azure, vedere [Connessioni in uscita in Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Le macchine virtuali create in questo articolo hanno un'[interfaccia di rete](virtual-network-network-interface.md) con un indirizzo IP assegnato dinamicamente a tale interfaccia. Dopo avere distribuito la macchina virtuale, è possibile [aggiungere più indirizzi IP pubblici e privati o modificare il metodo di assegnazione degli indirizzi IP impostandolo su statico](virtual-network-network-interface-addresses.md#add-ip-addresses). È possibile [aggiungere interfacce di rete](virtual-network-network-interface-vm.md#vm-add-nic) fino al limite supportato dalle [dimensioni della macchina virtuale](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) selezionate al momento della creazione della macchina virtuale. È anche possibile [abilitare Single Root I/O Virtualization (SR-IOV)](create-vm-accelerated-networking-cli.md) per una macchina virtuale, ma solo quando si crea una macchina virtuale con dimensioni in grado di supportare questa funzionalità.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>Comunicare tra macchine virtuali e con Internet

Usare il comando seguente per creare una sessione SSH con la macchina virtuale *myVmMgmt*. Sostituire `<publicIpAddress>` con l'indirizzo IP pubblico della macchina virtuale. Nell'esempio precedente l'indirizzo IP pubblico è *13.90.242.231*. Quando viene richiesto di immettere una password, specificare la password immessa in [Creare macchine virtuali](#create-virtual-machines).

```bash 
ssh azureuser@<publicIpAddress>
```

Per motivi di sicurezza è comune limitare il numero di macchine virtuali a cui è possibile eseguire la connessione in remoto in una rete virtuale. In questa esercitazione viene usata la macchina virtuale *myVmMgmt* per gestire la macchina virtuale *myVmWeb* nella rete virtuale. Usare il comando seguente per connettersi tramite SSH alla macchina virtuale *myVmWeb* dalla macchina virtuale *myVmMgmt*:

```bash 
ssh azureuser@myVmWeb
```

Per comunicare con la macchina virtuale *myVmMgmt* dalla macchina virtuale *myVmWeb*, immettere il comando seguente da un prompt dei comandi:

```
ping -c 4 myvmmgmt
```

L'output è simile all'output di esempio seguente:
    
```
PING myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net (10.0.1.4) 56(84) bytes of data.
64 bytes from 10.0.1.4: icmp_seq=1 ttl=64 time=1.45 ms
64 bytes from 10.0.1.4: icmp_seq=2 ttl=64 time=0.628 ms
64 bytes from 10.0.1.4: icmp_seq=3 ttl=64 time=0.529 ms
64 bytes from 10.0.1.4: icmp_seq=4 ttl=64 time=0.674 ms

--- myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3029ms
rtt min/avg/max/mdev = 0.529/0.821/1.453/0.368 ms
```
      
Si noti che l'indirizzo della macchina virtuale *myVmMgmt* è 10.0.1.4. 10.0.1.4 è il primo indirizzo IP disponibile nell'intervallo di indirizzi della subnet *Private* in cui è stata distribuita la macchina virtuale *myVmMgmt* in un passaggio precedente.  Il nome di dominio completo della macchina virtuale è *myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net*. Anche se la parte *hxehizax3z1udjnrx1r4gr30pg* del nome di dominio è diversa per la propria macchina virtuale, le parti restanti del nome di dominio sono le stesse. Per impostazione predefinita, tutte le macchine virtuali di Azure usano il servizio DNS di Azure predefinito. Tutte le macchine virtuali all'interno di una rete virtuale possono risolvere i nomi di tutte le altre macchine virtuali nella stessa rete virtuale usando il servizio DNS predefinito di Azure. Invece di usare il servizio DNS predefinito di Azure, è possibile usare il proprio server DNS o la funzionalità di dominio privato del servizio DNS di Azure. Per maggiori dettagli, vedere [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) o [Uso di DNS di Azure per i domini privati](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Usare i comandi seguenti per installare il server Web nginx nella macchina virtuale *myVmWeb*:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

Al termine dell'installazione di nginx, chiudere la sessione SSH *myVmWeb*, rimanendo nel prompt per la macchina virtuale *myVmMgmt*. Immettere il comando seguente per recuperare la schermata iniziale di nginx dalla macchina virtuale *myVmWeb*.

```bash
curl myVmWeb
```

Viene visualizzata la schermata iniziale di nginx.

Chiudere la sessione SSH con la macchina virtuale *myVmMgmt*.

Quando in Azure è stata creata la macchina virtuale *myVmWeb*, anche un indirizzo IP pubblico denominato *myVmWebPublicIP* è stato creato e assegnato alla macchina virtuale. Ottenere l'indirizzo pubblico assegnato da Azure con [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show).

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myVmWebPublicIP \
  --query ipAddress
```

Dal computer, immettere il comando seguente, sostituendo `<publicIpAddress>` con l'indirizzo restituito dal comando precedente:

```bash
curl <publicIpAddress>
```

Il tentativo di accedere tramite curl alla schermata iniziale di nginx dal computer fallisce. Il tentativo non riesce perché quando le macchine virtuali sono state distribuite, per impostazione predefinita Azure ha creato un gruppo di sicurezza di rete per ogni macchina virtuale. 

Un gruppo di sicurezza di rete contiene regole di sicurezza che consentono o impediscono il traffico di rete in ingresso e in uscita in base alla porta e all'indirizzo IP. Il gruppo di sicurezza di rete predefinito creato da Azure consente la comunicazione su tutte le porte tra le risorse nella stessa rete virtuale. Per le macchine virtuali Linux, il gruppo di sicurezza di rete predefinito impedisce tutto il traffico in ingresso da Internet su tutte le porte, fatta eccezione per la porta TCP 22 (SSH). Di conseguenza, per impostazione predefinita è anche possibile creare una sessione SSH per connettersi direttamente alla macchina virtuale *myVmWeb* da Internet, anche se non si vuole che la porta 22 sia aperta per un server Web. Dal momento che il comando `curl` comunica sulla porta 80, la comunicazione non riesce da Internet, poiché non esiste alcuna regola nel gruppo di sicurezza di rete predefinito che consenta il traffico sulla porta 80.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, usare [az group delete](/cli/azure/group#az_group_delete) per rimuoverli.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come distribuire una rete virtuale con più subnet. È stato inoltre spiegato che quando si crea una macchina virtuale Linux, Azure crea un'interfaccia di rete che viene collegata alla macchina virtuale e crea un gruppo di sicurezza di rete che consente solo il traffico sulla porta 22 da Internet. Passare all'esercitazione successiva per informazioni su come filtrare il traffico di rete verso le subnet, anziché verso singole macchine virtuali.

> [!div class="nextstepaction"]
> [Filtrare il traffico di rete verso le subnet](./virtual-networks-create-nsg-arm-cli.md)
