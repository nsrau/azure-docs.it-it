---
title: Avvio rapido in Azure - Creare l&quot;interfaccia della riga di comando per una VM | Microsoft Docs
description: Informazioni veloci su come creare macchine virtuali con l&quot;interfaccia della riga di comando di Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/03/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 303cb9950f46916fbdd58762acd1608c925c1328
ms.openlocfilehash: 67b4af9f3ef7fb078d6d125e0d3257ea85e288b0
ms.lasthandoff: 04/04/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-cli"></a>Creare una macchina virtuale Linux con l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa guida illustra in dettaglio l'uso dell'interfaccia della riga di comando di Azure per distribuire una macchina virtuale che esegue Ubuntu 16.04 LTS. Dopo aver distribuito il server, usare SSH per connettersi alla macchina virtuale e installare NGINX. 

Prima di iniziare, verificare che l'interfaccia della riga di comando di Azure sia stata installata. Per altre informazioni, vedere [Azure CLI installation guide](https://docs.microsoft.com/cli/azure/install-azure-cli) (Guida all'installazione dell'interfaccia della riga di comando di Azure). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Accedere ad Azure 

Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/#login) e seguire le istruzioni visualizzate.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella posizione `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-virtual-machine"></a>Crea macchina virtuale

Creare una VM con il comando [az vm create](/cli/azure/vm#create). 

L'esempio seguente crea una VM denominata `myVM` e crea le chiavi SSH se non esistono già in una posizione predefinita. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`.  

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

Dopo che la VM è stata creata, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente. Prendere nota di `publicIpAddress`. Questo indirizzo viene usato per accedere alla VM.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="open-port-80-for-web-traffic"></a>Aprire la porta 80 per il traffico Web 

Per impostazione predefinita nelle macchine virtuali Linux distribuite in Azure sono consentite solo le connessioni SSH. Se si intende usare questa macchina virtuale come un server Web, è necessario aprire la porta 80 da Internet.  Per aprire la porta da usare è necessario un unico comando.  
 
 ```azurecli 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>Usare SSH per connettersi alla macchina virtuale

Usare il comando seguente per creare una sessione SSH con la macchina virtuale. Sostituire `<publicIpAddress>` con l'indirizzo IP pubblico corretto della macchina virtuale.  Nell'esempio riportato sopra l'indirizzo IP era `40.68.254.142`.

```bash 
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>Installare NGINX

Usare lo script bash seguente per aggiornare le origini dei pacchetti e installare il pacchetto NGINX più recente. 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-ngix-welcome-page"></a>Visualizzare la pagina iniziale di NGINX

Dopo l'installazione di NGINX e l'apertura della porta 80 nella macchina virtuale da Internet, è possibile usare il Web browser preferito per visualizzare la pagina iniziale predefinita di NGINX. Assicurarsi di usare il valore `publicIpAddress` indicato in precedenza per visitare la pagina predefinita. 

![Sito NGINX predefinito](./media/quick-create-cli/nginx.png) 


## <a name="delete-virtual-machine"></a>Eliminare una macchina virtuale

Quando non servono più, il comando seguente consente di rimuovere il gruppo di risorse, la VM e tutte le risorse correlate.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

[Creare esercitazioni per macchine virtuali a disponibilità elevata](create-cli-complete.md)

[Esplorare gli esempi dell'interfaccia della riga di comando per la distribuzione della VM](cli-samples.md)

