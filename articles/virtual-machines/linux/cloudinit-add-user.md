---
title: Usare cloud-init per aggiungere un utente a una VM Linux in Azure | Microsoft Docs
description: Come usare cloud-init per aggiungere un utente a una VM Linux durante la creazione con l'interfaccia della riga di comando di Azure 2.0
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
ms.openlocfilehash: ce4421fc8276f215564cb7a171a215cc166c8517
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Usare cloud-init per aggiungere un utente a una VM Linux in Azure
Questo articolo illustra come usare [cloud-init](https://cloudinit.readthedocs.io) per aggiungere un utente in una macchina virtuale (VM) o un set di scalabilità di macchine virtuali in fase di provisioning in Azure. Questo script cloud-init viene eseguito al primo avvio dopo il provisioning delle risorse da parte di Azure. Per altre informazioni sul funzionamento di cloud-init in modo nativo in Azure e sulle distribuzioni Linux supportate, vedere la [panoramica di cloud-init](using-cloud-init.md).

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Aggiungere un utente a una macchina virtuale con cloud init
Una delle prime attività eseguite in qualsiasi nuova VM Linux è l'aggiunta di un altro utente per evitare di usare *root*. Le chiavi SSH sono la procedura consigliata per la sicurezza e l'usabilità. Le chiavi vengono aggiunte al file *~/.ssh/authorized_keys* con questo script cloud-init.

Per aggiungere un utente a una VM Linux, creare un file denominato *cloud_init_add_user.txt* nella shell corrente e incollare la configurazione seguente. Per questo esempio, creare il file in Cloud Shell anziché nel computer locale. È possibile usare qualsiasi editor. Immettere `sensible-editor cloud_init_add_user.txt` per creare il file e visualizzare un elenco degli editor disponibili. Scegliere #1 per usare l'editor **nano**. Assicurarsi che l'intero file cloud-init venga copiato correttamente, in particolare la prima riga.  Come valore di `ssh-authorized-keys:` è necessario specificare la propria chiave pubblica (ad esempio il contenuto di *~/.ssh/id_rsa.pub*). Qui è stato abbreviato per semplificare l'esempio.

```yaml
#cloud-config
users:
  - default
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```
> [!NOTE] 
> Il file #cloud-config include il parametro `- default`. In questo modo, l'utente verrà aggiunto all'utente amministratore esistente creato durante il provisioning. Se si crea un utente senza il parametro `- default`, l'utente amministratore generato automaticamente dalla piattaforma Azure verrà sovrascritto. 

Prima di distribuire l'immagine, è necessario creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Creare ora una VM con [az vm create](/cli/azure/vm#az_vm_create) e specificare il file cloud-init con `--custom-data cloud_init_add_user.txt` come segue:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

Stabilire una connessione SSH all'indirizzo IP pubblico della VM visualizzata nell'output del comando precedente. Immettere il valore di **publicIpAddress** in questo modo:

```bash
ssh <publicIpAddress>
```

Per verificare che l'utente sia stato aggiunto alla VM e ai gruppi specificati, visualizzare il contenuto del file */etc/group* come segue:

```bash
cat /etc/group
```

L'output di esempio seguente illustra che l'utente dal file *cloud_init_add_user.txt* è stato aggiunto alla VM e al gruppo appropriato:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Passaggi successivi
Per altri esempi cloud-init di modifiche di configurazione, vedere i documenti seguenti:
 
- [Aggiungere un altro utente Linux a una VM](cloudinit-add-user.md)
- [Eseguire uno strumento di gestione di pacchetti per aggiornare pacchetti esistenti al primo avvio](cloudinit-update-vm.md)
- [Modificare il nome host locale della macchina virtuale](cloudinit-update-vm-hostname.md) 
- [Installare un pacchetto dell'applicazione, aggiornare i file di configurazione e inserire le chiavi](tutorial-automate-vm-deployment.md)