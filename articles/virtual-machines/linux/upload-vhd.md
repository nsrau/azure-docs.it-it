---
title: Caricare o copiare una VM Linux personalizzata con l'interfaccia della riga di comando di Azure
description: Caricare o copiare una macchina virtuale personalizzata usando il modello di distribuzione di Resource Manager e l'interfaccia della riga di comando di Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: 95486208f52b2faa2fbb3db5bf1ef968c330dab6
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034295"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Creare una macchina virtuale Linux da un disco personalizzato tramite l'interfaccia della riga di comando di Azure

<!-- rename to create-vm-specialized -->

Questo articolo descrive come caricare un disco rigido virtuale personalizzato (VHD) e come copiare un disco rigido virtuale esistente in Azure. Il disco rigido virtuale appena creato viene quindi usato per creare nuove macchine virtuali Linux (VM). È possibile installare e configurare una distribuzione Linux in base ai propri requisiti e quindi usare il disco rigido virtuale per creare una nuova macchina virtuale di Azure.

Per creare più macchine virtuali dal disco personalizzato, creare prima di tutto un'immagine dalla macchina virtuale o dal disco rigido virtuale. Per altre informazioni, vedere [Creare un'immagine personalizzata di una macchina virtuale di Azure tramite l'interfaccia della riga di comando](tutorial-custom-images.md).

Sono disponibili due opzioni per creare un disco personalizzato:
* Caricare il VHD
* Copiare una VM di Azure esistente


## <a name="requirements"></a>Requisiti
Per completare la procedura seguente, è necessario:

- Una macchina virtuale Linux che è stata preparata per l'uso in Azure. La sezione [Preparare la macchina virtuale](#prepare-the-vm) di questo articolo descrive come trovare le informazioni specifiche della distribuzione relative all'installazione dell'agente Linux di Azure (waagent), necessario per connettersi a una macchina virtuale tramite SSH.
- File VHD di una [distribuzione Linux approvata da Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) esistente, oppure vedere le [informazioni sulle distribuzioni non approvate](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), in un disco virtuale in formato VHD. Sono disponibili vari strumenti per la creazione di una VM e un file VHD:
  - Installare e configurare [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) o [KVM](https://www.linux-kvm.org/page/RunningKVM), prestando attenzione a usare il disco rigido virtuale come formato di immagine. Se necessario, è possibile [convertire un'immagine](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) usando `qemu-img convert`.
  - È anche possibile usare Hyper-V [in Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) o [in Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Il formato VHDX più recente non è supportato in Azure. Quando si crea una VM, specificare VHD come formato. Se necessario, è possibile convertire i dischi VHDX usando [qemu-img convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) o il cmdlet [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) di PowerShell. Poiché Azure non supporta il caricamento di dischi rigidi virtuali dinamici, è necessario convertire questi dischi in dischi rigidi virtuali statici prima del caricamento. Per convertire dischi dinamici durante il processo di caricamento in Azure, è possibile usare strumenti come [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go).
> 
> 


- Assicurarsi di avere installato la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-az-cli2) e di aver eseguito l'accesso a un account Azure tramite [az login](/cli/azure/reference-index#az-login).

Negli esempi seguenti sostituire i nomi dei parametri di esempio con i propri valori, ad esempio `myResourceGroup`, `mystorageaccount`e `mydisks`.

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Preparare la macchina virtuale

Azure supporta svariate distribuzioni di Linux (vedere la sezione [Distribuzioni approvate](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Gli articoli seguenti descrivono come preparare le diverse distribuzioni Linux supportate in Azure:

* [Distribuzioni basate su CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES e openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Altre: distribuzioni non approvate](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Vedere anche le [Note sull'installazione di Linux](create-upload-generic.md#general-linux-installation-notes) per suggerimenti più generali sulla preparazione di immagini Linux per Azure.

> [!NOTE]
> Il [contratto di Servizio per la piattaforma Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) è applicabile alle macchine virtuali che eseguono Linux solo quando una distribuzione approvata viene usata con i dettagli di configurazione specificati nella sezione "Versioni e distribuzioni supportate" in [Distribuzioni di Linux supportate da Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Opzione 1: caricare un disco rigido virtuale

È ora possibile caricare VHD direttamente in un disco gestito. Per istruzioni, vedere [caricare un disco rigido virtuale in Azure tramite l'interfaccia della riga di comando di Azure](disks-upload-vhd-to-managed-disk-cli.md).

## <a name="option-2-copy-an-existing-vm"></a>Opzione 2: copiare una macchina virtuale esistente

È anche possibile creare una macchina virtuale personalizzata in Azure, copiare il disco del sistema operativo e quindi collegarlo a una nuova macchina virtuale per crearne un'altra copia. Questa è l'operazione corretta per il test, ma se si vuole usare una macchina virtuale di Azure esistente come modello per più macchine virtuali nuove, creare invece un'*immagine*. Per altre informazioni sulla creazione di un'immagine da una macchina virtuale di Azure esistente, vedere [Creare un'immagine personalizzata di una macchina virtuale di Azure tramite l'interfaccia della riga di comando](tutorial-custom-images.md).

Se si vuole copiare una macchina virtuale esistente in un'altra area, potrebbe essere necessario usare azcopy per [creare una copia di un disco in un'altra area](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk). 

In caso contrario, è necessario eseguire uno snapshot della macchina virtuale e quindi creare un nuovo VHD del sistema operativo dallo snapshot.

### <a name="create-a-snapshot"></a>Creare uno snapshot

Questo esempio crea uno snapshot di una macchina virtuale denominata *myVM* nel gruppo di risorse *myResourceGroup* e uno snapshot denominato *osDiskSnapshot*.

```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Creare il disco gestito

Creare un nuovo disco gestito dallo snapshot.

Ottenere l'ID dello snapshot. In questo esempio, lo snapshot è denominato *osDiskSnapshot* ed è nel gruppo di risorse *myResourceGroup*.

```azure-cli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Creare il disco gestito. In questo esempio si creerà un disco gestito denominato *myManagedDisk* dallo snapshot. Il disco si trova nell'archiviazione standard e ha dimensioni pari a 128 GB.

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Creare la VM

Creare la macchina virtuale con [az vm create](/cli/azure/vm#az-vm-create) e collegare (--attach-os-disk) il disco gestito come disco del sistema operativo. L'esempio seguente crea una macchina virtuale denominata *myNewVM* usando il disco gestito creato dal disco rigido virtuale caricato:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Dovrebbe essere possibile accedere con SSH nella macchina virtuale usando le credenziali della macchina virtuale di origine. 

## <a name="next-steps"></a>Passaggi successivi
Dopo avere preparato e caricato il disco rigido virtuale, è possibile ottenere altre informazioni sull' [uso di Azure Resource Manager e dei modelli](../../azure-resource-manager/resource-group-overview.md). È anche consigliabile [aggiungere un disco dati](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) alle nuove VM. Se nelle VM sono in esecuzione applicazioni a cui si deve accedere, assicurarsi di [aprire le porte e gli endpoint](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
