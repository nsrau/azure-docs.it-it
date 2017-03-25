---
title: Avvio rapido in Azure - Creare l&quot;interfaccia della riga di comando della VM Windows | Documentazione Microsoft
description: Informazioni veloci su come creare una macchina virtuale Windows con l&quot;interfaccia della riga di comando di Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/14/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 3064da2a6afee007418ef6ba5ec4247e3e045c38
ms.lasthandoff: 03/15/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-cli-20"></a>Creare una macchina virtuale Windows con l'interfaccia della riga di comando di Azure 2.0

L'interfaccia della riga di comando di Azure 2.0 viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa guida descrive dettagliatamente come usare l'interfaccia della riga di comando di Azure per distribuire una macchina virtuale che esegue Windows Server 2016.

Prima di iniziare, verificare che l'interfaccia della riga di comando di Azure sia stata installata. Per altre informazioni, vedere [Azure CLI installation guide](https://docs.microsoft.com/cli/azure/install-azure-cli) (Guida all'installazione dell'interfaccia della riga di comando di Azure). 

## <a name="log-in-to-azure"></a>Accedere ad Azure 

Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/#login) e seguire le istruzioni visualizzate.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group#create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella posizione `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-virtual-machine"></a>Crea macchina virtuale

Creare una VM con il comando [az vm create](/cli/azure/vm#create). 

L'esempio seguente crea una VM denominata `myVM`. Questo esempio usa `azureuser` per un nome utente amministrativo e ` myPassword12` come password. Aggiornare i valori in modo che siano appropriati all'ambiente. Questi valori sono necessari quando si crea una connessione con la macchina virtuale.

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --admin-username azureuser --admin-password myPassword12
```

Dopo che la VM è stata creata, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente. Prendere nota dell'indirizzo IP pubblico. Questo indirizzo viene usato per accedere alla VM.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-virtual-machine"></a>Connettersi alla macchina virtuale

Usare il comando seguente per creare una sessione desktop remoto con la macchina virtuale. Sostituire l'indirizzo IP con l'indirizzo IP pubblico della macchina virtuale. Quando richiesto, immettere le credenziali utilizzate durante la creazione della macchina virtuale.

```bash 
mstsc /v:<Public IP Address>
```

## <a name="delete-virtual-machine"></a>Eliminare una macchina virtuale

Quando non servono più, il comando seguente consente di rimuovere il gruppo di risorse, la VM e tutte le risorse correlate.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

[Installare un ruolo e configurare l'esercitazione sul firewall](./virtual-machines-windows-hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Esplorare gli esempi dell'interfaccia della riga di comando per la distribuzione della VM](./virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
