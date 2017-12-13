---
title: Usare cloud-init per configurare un file di scambio in una macchina virtuale Linux | Microsoft Docs
description: Come usare cloud-init per configurare un file di scambio in una macchina virtuale Linux durante la creazione con l'interfaccia della riga di comando di Azure 2.0
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
ms.openlocfilehash: a8ccec0dc8ff100c5d067cd50f2a6fa8cb4871fb
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="use-cloud-init-to-configure-a-swapfile-on-a-linux-vm"></a>Usare cloud-init per configurare un file di scambio in una macchina virtuale Linux
Questo articolo descrive come usare [cloud-init](https://cloudinit.readthedocs.io) per configurare il file di scambio in diverse distribuzioni Linux. Il file di scambio viene tradizionalmente configurato dall'agente Linux (WALA) in base alle distribuzioni in cui è necessario.  Questo documento descrive il processo di creazione del file di scambio su richiesta durante la fase di provisioning usando cloud-init.  Per altre informazioni sul funzionamento di cloud-init in modo nativo in Azure e sulle distribuzioni Linux supportate, vedere la [panoramica di cloud-init](using-cloud-init.md)

## <a name="create-swapfile-for-ubuntu-based-images"></a>Creare un file di scambio per immagini basate su Ubuntu
Per impostazione predefinita, le immagini della raccolta Ubuntu non creano file di scambio. Per abilitare la configurazione dei file di scambio durante la fase di provisioning delle macchine virtuali usando cloud-init, vedere il [documento su AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions) nel wiki di Ubuntu.

## <a name="create-swapfile-for-redhat-and-centos-based-images"></a>Creare un file di scambio per immagini basate su RedHat e CentOS

Nella shell corrente creare un file denominato *cloud_init_swapfile.txt* e incollare la configurazione seguente. Per questo esempio, creare il file in Cloud Shell anziché nel computer locale. È possibile usare qualsiasi editor. Immettere `sensible-editor cloud_init_swapfile.txt` per creare il file e visualizzare un elenco degli editor disponibili. Scegliere #1 per usare l'editor **nano**. Assicurarsi che l'intero file cloud-init venga copiato correttamente, in particolare la prima riga.  

```yaml
#cloud-config
disk_setup:
ephemeral0:
table_type: gpt
layout: [66, [33,82]]
overwrite: True
fs_setup:
- device: ephemeral0.1
filesystem: ext4
- device: ephemeral0.2
filesystem: swap
mounts:
- ["ephemeral0.1", "/mnt"]
- ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

Prima di distribuire l'immagine, è necessario creare un gruppo di risorse con il comando [az group create](/cli/azure/group#create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Creare ora una VM con [az vm create](/cli/azure/vm#create) e specificare il file cloud-init con `--custom-data cloud_init_swapfile.txt` come segue:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swapfile.txt \
  --generate-ssh-keys 
```

## <a name="verify-swapfile-was-created"></a>Verificare che il file di scambio sia stato creato
Stabilire una connessione SSH all'indirizzo IP pubblico della VM visualizzata nell'output del comando precedente. Immettere il valore di **publicIpAddress** in questo modo:

```bash
ssh <publicIpAddress>
```

Dopo aver eseguito SSH nella macchina virtuale, verificare che il file di scambio sia stato creato

```bash
swapon -s
```

L'output di questo comando dovrebbe essere simile al seguente:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Se è presente un'immagine esistente per cui è configurato un file di scambio e si vuole modificare la configurazione del file di scambio per nuove immagini, è necessario rimuovere il file di scambio esistente. Per altre informazioni, vedere il documento "Personalizzare le immagini per il provisioning tramite cloud-init".

## <a name="next-steps"></a>Passaggi successivi
Per altri esempi cloud-init di modifiche di configurazione, vedere i documenti seguenti:
 
- [Aggiungere un altro utente Linux a una VM](cloudinit-add-user.md)
- [Eseguire uno strumento di gestione di pacchetti per aggiornare pacchetti esistenti al primo avvio](cloudinit-update-vm.md)
- [Modificare il nome host locale della macchina virtuale](cloudinit-update-vm-hostname.md) 
- [Installare un pacchetto dell'applicazione, aggiornare i file di configurazione e inserire le chiavi](tutorial-automate-vm-deployment.md)