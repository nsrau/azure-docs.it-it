---
title: "Avvio rapido: Creare una macchina virtuale Linux con l'interfaccia della riga di comando di Azure 2.0 | Microsoft Docs"
description: In questo avvio rapido si apprenderà come usare l'interfaccia della riga di comando di Azure 2.0 per creare una macchina virtuale Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6536860bb75d068a96899f2d30ec7a6126a28436
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37927632"
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli-20"></a>Avvio rapido: Creare una macchina virtuale Linux con l'interfaccia della riga di comando di Azure 2.0

L'interfaccia della riga di comando di Azure 2.0 viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questo avvio rapido illustra come usare l'interfaccia della riga di comando di Azure 2.0 per distribuire una macchina virtuale Linux in Azure che esegue Ubuntu. Per vedere la macchina virtuale in azione, eseguire SSH nella macchina virtuale e installare il server Web NGINX.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo avvio rapido richiede la versione 2.0.30 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Crea macchina virtuale

Creare una VM con il comando [az vm create](/cli/azure/vm#az_vm_create).

L'esempio seguente crea una macchina virtuale denominata *myVM*, aggiunge un account utente denominato *azureuser* e genera le chiavi SSH, se non esistono già in un percorso predefinito (*~/.ssh*). Per usare un set specifico di chiavi, usare l'opzione `--ssh-key-value`:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

La creazione della macchina virtuale e delle risorse di supporto richiede alcuni minuti. L'output di esempio seguente mostra che l'operazione di creazione della macchina virtuale ha avuto esito positivo.

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Si noti `publicIpAddress` nell'output della macchina virtuale. Questo indirizzo viene usato per l'accesso alla macchina virtuale nei passaggi successivi.

## <a name="open-port-80-for-web-traffic"></a>Aprire la porta 80 per il traffico Web

Per impostazione predefinita, le connessioni SSH vengono aperte solo quando si crea una macchina virtuale Linux in Azure. Usare [az vm open-port](/cli/azure/vm#az_vm_open_port) per aprire la porta TCP 80 per l'uso con il server Web NGINX:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Connettersi alla macchina virtuale

Eseguire una connessione SSH alla VM nel modo usuale. Sostituire **publicIpAddress** con l'indirizzo IP pubblico della macchina virtuale, come annotato negli output precedenti dalla VM:

```bash
ssh azureuser@publicIpAddress
```

## <a name="install-web-server"></a>Installare il server Web

Per visualizzare la macchina virtuale in azione, installare il server Web NGINX. Per aggiornare le origini dei pacchetti e installare il pacchetto NGINX più recente, eseguire i comandi seguenti dalla sessione SSH:

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Al termine usare il comando `exit` per uscire dalla sessione SSH.

## <a name="view-the-web-server-in-action"></a>Visualizzare il server Web in azione

Dopo l'installazione di NGINX e l'apertura della porta 80 nella macchina virtuale da Internet, è possibile usare il Web browser preferito per visualizzare la home page predefinita di NGINX. Usare l'indirizzo IP pubblico della macchina virtuale ottenuto in un passaggio precedente. L'esempio seguente mostra il sito Web NGINX predefinito:

![Sito NGINX predefinito](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [az group delete](/cli/azure/group#az_group_delete) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate. Verificare di essere usciti dalla sessione SSH alla macchina virtuale, quindi eliminare le risorse nel modo seguente:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata distribuita una macchina virtuale semplice, è stata aperta una porta di rete per il traffico Web ed è stato installato un server Web di base. Per altre informazioni sulle macchine virtuali di Azure, passare all'esercitazione per le VM di Linux.


> [!div class="nextstepaction"]
> [Esercitazioni per le macchine virtuali di Linux in Azure](./tutorial-manage-vm.md)
