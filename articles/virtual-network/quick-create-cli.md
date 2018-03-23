---
title: Creare una rete virtuale di Azure - Interfaccia della riga di comando di Azure | Microsoft Docs
description: Apprendere a creare rapidamente una rete virtuale usando l'interfaccia della riga di comando di Azure. Una rete virtuale consente alle risorse di Azure, come le macchine virtuali, di comunicare privatamente tra loro e con Internet.
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
ms.date: 03/09/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 46fec48720c817072ce838dd2e4c07725be5a7fe
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Creare una rete virtuale usando l'interfaccia della riga di comando di Azure

Una rete virtuale consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente tra loro e con Internet. Questo articolo illustra come creare una rete virtuale. Dopo avere creato una rete virtuale, si distribuiscono due VM nella rete virtuale. Quindi si effettua la connessione a una VM da Internet e si comunica privatamente con l'altra VM.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo articolo richiede la versione 2.0.28 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione installata, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). 


## <a name="create-a-virtual-network"></a>Crea rete virtuale

Prima di poter creare una rete virtuale, è necessario creare un gruppo di risorse per contenere la rete virtuale. Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group#az_group_create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Creare una rete virtuale con [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). L'esempio seguente crea una rete virtuale predefinita denominata *myVirtualNetwork* con una subnet denominata *default*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare due VM nella rete virtuale:

### <a name="create-the-first-vm"></a>Creare la prima VM

Creare una VM con il comando [az vm create](/cli/azure/vm#az_vm_create). Il comando crea le chiavi SSH, se non esistono già in una posizione predefinita. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`. L'opzione `--no-wait` crea la VM in background, pertanto è possibile continuare con il passaggio successivo. L'esempio seguente crea una VM denominata *myVm1*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Creare la seconda VM

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

La creazione della VM richiede alcuni minuti. Dopo la creazione della VM, l'interfaccia della riga di comando di Azure restituisce un output simile all'esempio seguente: 

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

Prendere nota di **publicIpAddress**. Questo indirizzo viene usato per connettersi alla VM da Internet nel passaggio successivo.

## <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

Sostituire `<publicIpAddress>` con l'indirizzo IP pubblico della VM *myVm2* nel comando riportato di seguito e quindi immettere il comando seguente:

```bash 
ssh <publicIpAddress>
```

## <a name="communicate-privately-between-vms"></a>Comunicare privatamente tra VM

Per confermare le comunicazioni private tra le VM *myVm2* e *myVm1*, immettere il comando seguente:

```bash
ping myVm1 -c 4
```

Si riceveranno quattro risposte da *10.0.0.4*.

Chiudere la sessione SSH con la VM *myVm2*.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, è possibile usare [az group delete](/cli/azure/group#az_group_delete) per rimuoverli:

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state create una rete virtuale predefinita e due VM. È stata effettuata la connessione a una VM da Internet e si è comunicato privatamente tra le due VM. Per altre informazioni sulle impostazioni della rete virtuale, vedere [Manage a virtual network](manage-virtual-network.md) (Gestire una rete virtuale). 

Per impostazione predefinita, Azure consente comunicazioni private senza restrizioni tra le macchine virtuali, ma permette solo sessioni SSH in ingresso alle VM Linux da Internet. Per informazioni su come consentire o limitare i diversi tipi di comunicazione di rete da e verso le VM, passare all'esercitazione successiva.

> [!div class="nextstepaction"]
> [Filtrare il traffico di rete](virtual-networks-create-nsg-arm-cli.md)
