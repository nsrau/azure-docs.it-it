---
title: 'Creare una rete virtuale: guida introduttiva - Interfaccia della riga di comando di Azure'
titlesuffix: Azure Virtual Network
description: Questa guida introduttiva illustra come creare una rete virtuale con l'interfaccia della riga di comando di Azure. Una rete virtuale consente alle risorse di Azure, come le macchine virtuali, di comunicare privatamente tra loro e con Internet.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/12/2018
ms.author: kumud
ms.openlocfilehash: 6306d893f491f93cc31b7e478afe5632e997285c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64692637"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Avvio rapido: Creare una rete virtuale usando l'interfaccia della riga di comando di Azure

Una rete virtuale consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente tra loro e con Internet. Questa guida introduttiva illustra come creare una rete virtuale. Dopo avere creato una rete virtuale, si distribuiscono due VM nella rete virtuale. Si effettua quindi la connessione alle VM da Internet e si stabilisce una comunicazione privata tramite la nuova rete virtuale.

Se non si ha una sottoscrizione di Azure, creare ora un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si decide di installare e usare l'interfaccia della riga di comando di Azure in locale, per questa guida introduttiva è necessario usare l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Per trovare la versione installata, eseguire `az --version`. Per informazioni sull'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group-and-a-virtual-network"></a>Creare un gruppo di risorse e una rete virtuale

Per poter creare una rete virtuale, è prima necessario creare un gruppo di risorse per l'hosting della rete virtuale. Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group). Questo esempio crea un gruppo di risorse denominato *myResourceGroup* nella località *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Creare una rete virtuale con [az network vnet create](/cli/azure/network/vnet). Questo esempio crea una rete virtuale predefinita denominata *myVirtualNetwork* con una subnet denominata *default*:

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare due VM nella rete virtuale.

### <a name="create-the-first-vm"></a>Creare la prima VM

Creare una VM con il comando [az vm create](/cli/azure/vm). Se le chiavi SSH non esistono già in una posizione predefinita, vengono create dal comando. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`. L'opzione `--no-wait` crea la VM in background, pertanto è possibile continuare con il passaggio successivo. Questo esempio crea una VM denominata *myVm1*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Creare la seconda VM

Dato che nel passaggio precedente è stata usata l'opzione `--no-wait`, si può procedere con la creazione della seconda VM, denominata *myVm2*.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Messaggio di output dell'interfaccia della riga di comando di Azure

La creazione delle macchine virtuali può richiedere alcuni minuti. Al termine della creazione delle VM in Azure, l'interfaccia della riga di comando di Azure restituisce un output simile al seguente:

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
  "zones": ""
}
```

Prendere nota di **publicIpAddress**. Questo indirizzo verrà usato per connettersi alla VM da Internet nel passaggio successivo.

## <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

In questo comando sostituire `<publicIpAddress>` con l'indirizzo IP pubblico della VM *myVm2*:

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Stabilire la comunicazione tra le macchine virtuali

Per verificare la comunicazione privata tra le VM *myVm2* e *myVm1*, immettere questo comando:

```bash
ping myVm1 -c 4
```

Si riceveranno quattro risposte da *10.0.0.4*.

Chiudere la sessione SSH con la VM *myVm2*.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile usare [az group delete](/cli/azure/group) per rimuovere il gruppo di risorse e tutte le risorse in esso contenute:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva sono state create una rete virtuale predefinita e due VM. È stata effettuata la connessione a una VM da Internet ed è stata stabilita una comunicazione privata tra le due VM. Per altre informazioni sulle impostazioni della rete virtuale, vedere [Manage a virtual network](manage-virtual-network.md) (Gestire una rete virtuale).

Azure consente comunicazioni private senza restrizioni tra le VM, ma per impostazione predefinita permette solo connessioni Desktop remoto in ingresso alle VM Windows da Internet. Per altre informazioni sui diversi tipi di comunicazioni di rete delle VM, vedere l'esercitazione [Filtrare il traffico di rete](tutorial-filter-network-traffic.md).
