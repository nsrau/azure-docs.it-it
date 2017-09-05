---
title: Caricare o copiare una macchina virtuale Linux personalizzata con l'interfaccia della riga di comando di Azure 2.0 | Microsoft Docs
description: Caricare o copiare una macchina virtuale personalizzata usando il modello di distribuzione di Resource Manager e l'interfaccia della riga di comando di Azure 2.0
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/06/2017
ms.author: cynthn
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 9a91aedf452a391f23b1e5773ec12e2de5d4a288
ms.contentlocale: it-it
ms.lasthandoff: 08/09/2017

---
# <a name="create-a-linux-vm-from-custom-disk-with-the-azure-cli-20"></a>Creare una macchina virtuale Linux da un disco personalizzato usando l'interfaccia della riga di comando di Azure 2.0

<!-- rename to create-vm-specialized -->

Questo articolo illustra come caricare un disco rigido virtuale personalizzato o copiare un disco rigido virtuale esistente in Azure e creare nuove macchine virtuali Linux dal disco personalizzato. È possibile installare e configurare una distribuzione Linux in base ai propri requisiti e quindi usare il disco rigido virtuale per creare rapidamente una nuova macchina virtuale di Azure.

Se si desidera creare più macchine virtuali dal disco personalizzato, si deve creare un'immagine dal disco rigido virtuale o dalla macchina virtuale. Per altre informazioni vedere [Creare un'immagine personalizzata di una macchina virtuale di Azure tramite l'interfaccia della riga di comando](tutorial-custom-images.md).

Sono disponibili due opzioni:
* [Caricare un VHD](#option-1-upload-a-specialized-vhd)
* [Copiare una VM di Azure esistente](#option-2-copy-an-existing-azure-vm)

## <a name="quick-commands"></a>Comandi rapidi

Quando si crea una nuova macchina virtuale usando [az vm create](/cli/azure/vm#create) da un disco specializzato o personalizzato, **collegare** il disco (--attach-os-disk) anziché specificare un'immagine personalizzata o del marketplace (--image). L'esempio seguente crea una macchina virtuale denominata *myVM* usando il disco gestito denominato *myManagedDisk* creato dal disco rigido virtuale personalizzato:

```azurecli
az vm create --resource-group myResourceGroup --location eastus --name myVM \
   --os-type linux --attach-os-disk myManagedDisk
```

## <a name="requirements"></a>Requisiti
Per completare la procedura seguente, è necessario:

* Una macchina virtuale Linux che è stata preparata per l'uso in Azure. La sezione [Preparare la macchina virtuale](#prepare-the-vm) di questo articolo descrive come trovare le informazioni specifiche della distribuzione relative all'installazione dell'Agente Linux di Azure (waagent) necessario affinché la macchina virtuale funzioni correttamente in Azure e l'utente possa connettersi tramite SSH.
* File VHD di una [distribuzione Linux approvata da Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) esistente, oppure vedere le [informazioni sulle distribuzioni non approvate](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), in un disco virtuale in formato VHD. Sono disponibili vari strumenti per la creazione di una VM e un file VHD:
  * Installare e configurare [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) o [KVM](http://www.linux-kvm.org/page/RunningKVM), prestando attenzione a usare il disco rigido virtuale come formato di immagine. Se necessario, è possibile [convertire un'immagine](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) usando **qemu-img convert**.
  * È anche possibile usare Hyper-V [in Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) o [in Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Il formato VHDX più recente non è supportato in Azure. Quando si crea una VM, specificare VHD come formato. Se necessario, è possibile convertire i dischi VHDX nel formato VHD usando [qemu-img convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) o il cmdlet di PowerShell [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx). Inoltre, Azure non supporta il caricamento di VHD dinamici, pertanto è necessario convertire tali dischi in VHD statici prima del caricamento. Per convertire dischi dinamici durante il processo di caricamento in Azure, sono disponibili strumenti come [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) .
> 
> 


* Assicurarsi di avere installato la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e di aver eseguito l'accesso a un account Azure tramite il comando [az login](/cli/azure/#login).

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono *myResourceGroup*, *mystorageaccount* e *mydisks*.

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Preparare la macchina virtuale

Azure supporta svariate distribuzioni di Linux (vedere la sezione [Distribuzioni approvate](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Gli articoli seguenti forniscono le istruzioni per preparare le diverse distribuzioni Linux supportate in Azure:

* [Distribuzioni basate su CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES e openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Altro - Distribuzioni non approvate](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Vedere anche le [Note sull'installazione di Linux](create-upload-generic.md#general-linux-installation-notes) per suggerimenti più generali sulla preparazione di immagini Linux per Azure.

> [!NOTE]
> Il [Contratto di Servizio per la piattaforma Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) è applicabile alle VM che eseguono Linux solo quando una distribuzione approvata viene usata con i dettagli di configurazione specificati nella sezione "Versioni e distribuzioni supportate" in [Distribuzioni di Linux supportate da Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Opzione 1: caricare un disco rigido virtuale

È possibile caricare un disco rigido virtuale personalizzato in esecuzione in un computer locale o che è stato esportato da un altro cloud. Per usare il disco rigido virtuale per creare una nuova macchina virtuale di Azure, è necessario caricare il disco rigido virtuale in un account di archiviazione e creare un disco gestito dal disco rigido virtuale. 

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Prima di caricare il disco personalizzato e creare le VM, è necessario creare un gruppo di risorse con [az group create](/cli/azure/group#create).

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *eastus*: [Panoramica di Azure Managed Disks](../../storage/storage-managed-disks-overview.md)
```azurecli
az group create \
    --name myResourceGroup \
    --location eastus
```

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Creare un account di archiviazione per il disco personalizzato e le VM con [az storage account create](/cli/azure/storage/account#create). 

Nell'esempio seguente viene creato un account di archiviazione denominato *mystorageaccount* nel gruppo di risorse creato in precedenza:

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --location eastus \
    --name mystorageaccount \
    --kind Storage \
    --sku Standard_LRS
```

### <a name="list-storage-account-keys"></a>Ottenere chiavi degli account di archiviazione
Azure genera due chiavi di accesso a 512 bit per ogni account di archiviazione. Queste chiavi di accesso vengono usate per autenticarsi nell'account di archiviazione, ad esempio per eseguire operazioni di scrittura. Ulteriori informazioni sulla [gestione dell'accesso all'archiviazione sono disponibili qui](../../storage/storage-create-storage-account.md#manage-your-storage-account). Visualizzare le chiavi di accesso con [az storage account keys list](/cli/azure/storage/account/keys#list).

Visualizzare le chiavi di accesso per l'account di archiviazione creato:

```azurecli
az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount
```

L'output è simile a:

```azurecli
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```
Prendere nota di **key1** perché verrà usato per interagire con l'account di archiviazione nei passaggi successivi.

### <a name="create-a-storage-container"></a>Creare un contenitore di archiviazione
Nello stesso modo in cui si creano directory diverse per organizzare in modo logico il file system locale si creano anche i contenitori con un account di archiviazione per organizzare i dischi. Un account di archiviazione può contenere un numero qualsiasi di contenitori. Creare un contenitore con [az storage container create](/cli/azure/storage/container#create).

Nell'esempio seguente viene creato un contenitore denominato *mydisks*:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

### <a name="upload-the-vhd"></a>Caricare il disco rigido virtuale
Caricare ora il disco personalizzato con [az storage blob upload](/cli/azure/storage/blob#upload). Caricare e archiviare il disco personalizzato come BLOB di pagine.

Specificare la chiave di accesso e il contenitore creato nel passaggio precedente, quindi selezionare il percorso del disco personalizzato sul computer locale:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 \
    --container-name mydisks \
    --type page \
    --file /path/to/disk/mydisk.vhd \
    --name myDisk.vhd
```
Il caricamento del disco rigido virtuale potrebbe richiedere qualche minuto.

### <a name="create-a-managed-disk"></a>Creare un disco gestito


Creare un disco gestito dal disco rigido virtuale con [az disk create](/cli/azure/disk#create). Nell'esempio seguente viene creato un disco gestito denominato *myManagedDisk* dal disco rigido virtuale caricato nell'account di archiviazione e nel contenitore denominati:

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```
## <a name="option-2-copy-an-existing-vm"></a>Opzione 2: copiare una macchina virtuale esistente

È anche possibile creare la macchina virtuale personalizzata in Azure, copiare il disco del sistema operativo e associarlo a una nuova macchina virtuale per crearne un'altra copia. Questa è l'operazione corretta per il test, ma se si desidera usare una macchina virtuale di Azure esistente come modello per più macchine virtuali nuove, è effettivamente necessario creare un'**immagine**. Per altre informazioni sulla creazione di un'immagine da una macchina virtuale di Azure esistente, vedere [Creare un'immagine personalizzata di una macchina virtuale di Azure tramite l'interfaccia della riga di comando](tutorial-custom-images.md)

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

Creare il disco gestito. In questo esempio, si creerà un disco gestito denominato *myManagedDisk* dallo snapshot che ha una dimensione di 128 GB nell'archiviazione standard.

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Creare la VM

A questo punto, creare la macchina virtuale con [az vm create](/cli/azure/vm#create) e collegare (--attach-os-disk) il disco gestito come disco del sistema operativo. L'esempio seguente crea una macchina virtuale denominata *myNewVM* usando il disco gestito creato dal disco rigido virtuale caricato:

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


