---
title: Usare cloud-init per aggiornare e installare i pacchetti in una macchina virtuale Linux in Azure | Microsoft Docs
description: Come usare cloud-init per aggiornare e installare i pacchetti in una macchina virtuale Linux durante la creazione con l'interfaccia della riga di comando di Azure 2.0
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
ms.openlocfilehash: e45bec2a71f94c66ce3044fb81bd2d7cefdf53a5
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Usare cloud-init per aggiornare e installare i pacchetti in una macchina virtuale Linux in Azure
Questo articolo descrive come usare [cloud-init](https://cloudinit.readthedocs.io) per aggiornare i pacchetti in una macchina virtuale o in un set di scalabilità di macchine virtuali Linux in fase di provisioning in Azure. Questi script cloud-init vengono eseguiti al primo avvio dopo il provisioning delle risorse da parte di Azure. Per altre informazioni sul funzionamento di cloud-init in modo nativo in Azure e sulle distribuzioni Linux supportate, vedere la [panoramica di cloud-init](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>Aggiornare una macchina virtuale con cloud init
Per motivi di sicurezza, potrebbe essere necessario configurare una VM per applicare gli aggiornamenti più recenti al primo avvio. Poiché cloud-init funziona in distribuzioni Linux diverse, non è necessario specificare `apt` o `yum` per la gestione pacchetti. È invece necessario definire `package_upgrade` e consentire al processo cloud-init di determinare il meccanismo appropriato per la distribuzione in uso. Questo flusso di lavoro consente di usare gli stessi script cloud-init nelle distribuzioni.

Per osservare il processo di aggiornamento in azione, nella shell corrente creare un file denominato *cloud_init_upgrade.txt* e incollare la configurazione seguente. Per questo esempio, creare il file in Cloud Shell anziché nel computer locale. È possibile usare qualsiasi editor. Immettere `sensible-editor cloud_init_upgrade.txt` per creare il file e visualizzare un elenco degli editor disponibili. Scegliere #1 per usare l'editor **nano**. Assicurarsi che l'intero file cloud-init venga copiato correttamente, in particolare la prima riga.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Prima di distribuire l'immagine, è necessario creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Creare ora una VM con [az vm create](/cli/azure/vm#az_vm_create) e specificare il file cloud-init con `--custom-data cloud_init_upgrade.txt` come segue:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

Stabilire una connessione SSH all'indirizzo IP pubblico della VM visualizzata nell'output del comando precedente. Immettere il valore di **publicIpAddress** in questo modo:

```bash
ssh <publicIpAddress>
```

Eseguire lo strumento di gestione pacchetti e cercare gli aggiornamenti. L'esempio seguente usa `apt-get` in una VM Ubuntu:

```bash
sudo apt-get upgrade
```

Poiché cloud-init ha cercato e installato gli aggiornamenti all'avvio, non devono essere presenti aggiornamenti da applicare, come illustrato nell'output di esempio seguente:

```bash
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

È inoltre possibile verificare l'installazione di `httpd` eseguendo `yum history` ed esaminare l'output che fa riferimento a `httpd`. 

## <a name="next-steps"></a>Passaggi successivi
Per altri esempi cloud-init di modifiche di configurazione, vedere i documenti seguenti:
 
- [Aggiungere un altro utente Linux a una VM](cloudinit-add-user.md)
- [Eseguire uno strumento di gestione di pacchetti per aggiornare pacchetti esistenti al primo avvio](cloudinit-update-vm.md)
- [Modificare il nome host locale della macchina virtuale](cloudinit-update-vm-hostname.md) 
- [Installare un pacchetto dell'applicazione, aggiornare i file di configurazione e inserire le chiavi](tutorial-automate-vm-deployment.md)