---
title: Usare cloud-init per impostare il nome host per una macchina virtuale Linux in Azure | Microsoft Docs
description: Come usare cloud-init per personalizzare una VM Linux durante la creazione con l'interfaccia della riga di comando di Azure 2.0
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: a858a12ec81db7ae1c0a7b7cfea06fa2abdcdcc6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Usare cloud-init per impostare il nome host per una macchina virtuale Linux in Azure
Questo articolo descrive come usare [cloud-init](https://cloudinit.readthedocs.io) per configurare un nome host specifico in una macchina virtuale o un set di scalabilità di macchine virtuali Linux in fase di provisioning in Azure. Questi script cloud-init vengono eseguiti al primo avvio dopo il provisioning delle risorse da parte di Azure. Per altre informazioni sul funzionamento di cloud-init in modo nativo in Azure e sulle distribuzioni Linux supportate, vedere la [panoramica di cloud-init](using-cloud-init.md)

## <a name="set-the-hostname-with-cloud-init"></a>Impostare il nome host con cloud-init
Per impostazione predefinita, il nome host corrisponde al nome della macchina virtuale quando si crea una nuova macchina virtuale in Azure.  Per eseguire uno script cloud-init in modo da modificare il nome host predefinito durante la creazione di una macchina virtuale in Azure con [az vm create](/cli/azure/vm#az_vm_create), specificare il file cloud-init con l'opzione `--custom-data`.  

Per osservare il processo di aggiornamento in azione, nella shell corrente creare un file denominato *cloud_init_hostname.txt* e incollare la configurazione seguente. Per questo esempio, creare il file in Cloud Shell anziché nel computer locale. È possibile usare qualsiasi editor. Immettere `sensible-editor cloud_init_hostname.txt` per creare il file e visualizzare un elenco degli editor disponibili. Scegliere #1 per usare l'editor **nano**. Assicurarsi che l'intero file cloud-init venga copiato correttamente, in particolare la prima riga.  

```yaml
#cloud-config
hostname: myhostname
```

Prima di distribuire l'immagine, è necessario creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Creare ora una VM con [az vm create](/cli/azure/vm#az_vm_create) e specificare il file cloud-init con `--custom-data cloud_init_hostname.txt` come segue:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Dopo la creazione della VM, l'interfaccia della riga di comando di Azure visualizza le relative informazioni. Usare `publicIpAddress` per stabilire una connessione SSH alla VM. Immettere il proprio indirizzo come di seguito:

```bash
ssh <publicIpAddress>
```

Per visualizzare il nome della VM, usare il comando `hostname` come segue:

```bash
hostname
```

La VM segnalerà il nome host come il valore impostato nel file cloud-init, come illustrato nell'output di esempio seguente:

```bash
myhostname
```

## <a name="next-steps"></a>Passaggi successivi
Per altri esempi cloud-init di modifiche di configurazione, vedere i documenti seguenti:
 
- [Aggiungere un altro utente Linux a una VM](cloudinit-add-user.md)
- [Eseguire uno strumento di gestione di pacchetti per aggiornare pacchetti esistenti al primo avvio](cloudinit-update-vm.md)
- [Modificare il nome host locale della macchina virtuale](cloudinit-update-vm-hostname.md) 
- [Installare un pacchetto dell'applicazione, aggiornare i file di configurazione e inserire le chiavi](tutorial-automate-vm-deployment.md)