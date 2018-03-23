---
title: Avvio rapido in Azure - Creare l'interfaccia della riga di comando per una VM | Microsoft Docs
description: Informazioni veloci su come creare macchine virtuali con l'interfaccia della riga di comando di Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/13/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 71310111ef0766b878a8a9572f4803320b65b705
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-linux-virtual-machine-with-the-azure-cli"></a>Creare una macchina virtuale Linux con l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa guida introduttiva descrive dettagliatamente come usare l'interfaccia della riga di comando di Azure per distribuire una macchina virtuale che esegue Server Ubuntu. Dopo aver distribuito il server viene creata una connessione SSH e viene installato un server Web NGINX.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Crea macchina virtuale

Creare una VM con il comando [az vm create](/cli/azure/vm#az_vm_create). 

L'esempio seguente crea una macchina virtuale denominata *myVM* e le chiavi SSH, se non esistono già in un percorso predefinito. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`.  

```azurecli-interactive 
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

Dopo che la VM è stata creata, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente. Prendere nota di `publicIpAddress`. Questo indirizzo viene usato per accedere alla VM.

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="open-port-80-for-web-traffic"></a>Aprire la porta 80 per il traffico Web 

Per impostazione predefinita nelle macchine virtuali Linux distribuite in Azure sono consentite solo le connessioni SSH. Se si intende usare questa macchina virtuale come un server Web, è necessario aprire la porta 80 da Internet. Usare il comando [az vm open-port](/cli/azure/vm#az_vm_open_port) per aprire la porta.  
 
 ```azurecli-interactive 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>Usare SSH per connettersi alla macchina virtuale

Usare il comando seguente per creare una sessione SSH con la macchina virtuale. Sostituire **publicIpAddress** con l'indirizzo IP pubblico corretto della macchina virtuale.  Nell'esempio riportato sopra l'indirizzo IP era *40.68.254.142*.

```bash 
ssh publicIpAddress
```

## <a name="install-nginx"></a>Installare NGINX

Usare i comandi seguenti per aggiornare le origini dei pacchetti e installare il pacchetto NGINX più recente. 

```bash 
# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Visualizzare la pagina iniziale di NGINX

Dopo l'installazione di NGINX e l'apertura della porta 80 nella macchina virtuale da Internet, è possibile usare il Web browser preferito per visualizzare la pagina iniziale predefinita di NGINX. Assicurarsi di usare l'indirizzo *publicIpAddress* descritto in precedenza per passare alla pagina predefinita. 

![Sito NGINX predefinito](./media/quick-create-cli/nginx.png) 


## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [az group delete](/cli/azure/group#az_group_delete) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate. Chiudere la sessione SSH per la macchina virtuale, quindi eliminare le risorse come segue:

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata distribuita una macchina virtuale semplice, è stata creata una regola del gruppo di sicurezza di rete ed è stato installato un server Web. Per altre informazioni sulle macchine virtuali di Azure, passare all'esercitazione per le VM di Linux.


> [!div class="nextstepaction"]
> [Esercitazioni per le macchine virtuali di Linux in Azure](./tutorial-manage-vm.md)
