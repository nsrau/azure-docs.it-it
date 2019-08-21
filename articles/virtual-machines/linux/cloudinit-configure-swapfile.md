---
title: Usare cloud-init per configurare una partizione di scambio in una VM Linux | Microsoft Docs
description: Come usare cloud-init per configurare una partizione di scambio in una VM Linux durante la creazione con l'interfaccia della riga di comando di Azure
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: d8ce12b931b6a30fa375588b73a1140ed4697c2f
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640778"
---
# <a name="use-cloud-init-to-configure-a-swap-partition-on-a-linux-vm"></a>Usare cloud-init per configurare una partizione di scambio in una VM Linux
Questo articolo illustra come usare [cloud-init](https://cloudinit.readthedocs.io) per configurare la partizione di scambio in diverse distribuzioni di Linux. La partizione swap è stata tradizionalmente configurata dall'agente Linux (WALA) in base alle distribuzioni necessarie.  Questo documento descrive il processo di creazione della partizione di scambio su richiesta durante il tempo di provisioning tramite cloud-init.  Per altre informazioni sul funzionamento di cloud-init in modo nativo in Azure e sulle distribuzioni Linux supportate, vedere la [panoramica di cloud-init](using-cloud-init.md)

## <a name="create-swap-partition-for-ubuntu-based-images"></a>Creare una partizione di swap per immagini basate su Ubuntu
Per impostazione predefinita, in Azure le immagini della raccolta Ubuntu non creano partizioni di scambio. Per abilitare la configurazione della partizione di scambio durante il tempo di provisioning della macchina virtuale usando cloud-init, vedere il [documento AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions) sul wiki di Ubuntu.

## <a name="create-swap-partition-for-red-hat-and-centos-based-images"></a>Creare una partizione di swap per le immagini basate su Red Hat e CentOS

Nella shell corrente creare un file denominato *cloud_init_swappart. txt* e incollare la configurazione seguente. Per questo esempio, creare il file in Cloud Shell anziché nel computer locale. È possibile usare qualsiasi editor. Immettere `sensible-editor cloud_init_swappart.txt` per creare il file e visualizzare un elenco degli editor disponibili. Scegliere #1 per usare l'editor **nano**. Assicurarsi che l'intero file cloud-init venga copiato correttamente, in particolare la prima riga.  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

Prima di distribuire l'immagine, è necessario creare un gruppo di risorse con il comando [az group create](/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Creare ora una VM con [az vm create](/cli/azure/vm) e specificare il file cloud-init con `--custom-data cloud_init_swappart.txt` come segue:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swappart.txt \
  --generate-ssh-keys 
```

## <a name="verify-swap-partition-was-created"></a>Verifica della creazione della partizione di scambio
Stabilire una connessione SSH all'indirizzo IP pubblico della VM visualizzata nell'output del comando precedente. Immettere il valore di **publicIpAddress** in questo modo:

```bash
ssh <publicIpAddress>
```

Dopo aver SSH ' ed nella macchina virtuale, verificare se la partizione di scambio è stata creata

```bash
swapon -s
```

L'output di questo comando dovrebbe essere simile al seguente:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Se è presente un'immagine di Azure in cui è configurata una partizione di scambio e si vuole modificare la configurazione della partizione di scambio per le nuove immagini, è necessario rimuovere la partizione di scambio esistente. Per altre informazioni, vedere il documento "Personalizzare le immagini per il provisioning tramite cloud-init".

## <a name="next-steps"></a>Passaggi successivi
Per altri esempi cloud-init di modifiche di configurazione, vedere i documenti seguenti:
 
- [Aggiungere un altro utente Linux a una VM](cloudinit-add-user.md)
- [Eseguire uno strumento di gestione di pacchetti per aggiornare pacchetti esistenti al primo avvio](cloudinit-update-vm.md)
- [Modificare il nome host locale della macchina virtuale](cloudinit-update-vm-hostname.md) 
- [Installare un pacchetto dell'applicazione, aggiornare i file di configurazione e inserire le chiavi](tutorial-automate-vm-deployment.md)
