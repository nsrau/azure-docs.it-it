---
title: Caricare un disco Linux personalizzato con l&quot;interfaccia della riga di comando di Azure 2.0 | Documentazione Microsoft
description: Creare e caricare un disco rigido virtuale in Azure usando il modello di distribuzione di Resource Manager e l&quot;interfaccia della riga di comando di Azure 2.0
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 374b2601857b3983bcd7b2f2e11d22b187fe7105
ms.openlocfilehash: 732ebaaf5bf16c02cfc2185d9e7138daf74c71dd
ms.lasthandoff: 02/27/2017


---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli-20"></a>Caricare e creare una VM Linux da un disco personalizzato usando l'interfaccia della riga di comando di Azure 2.0
Questo articolo illustra come caricare un disco rigido virtuale su Azure con l'interfaccia della riga di comando di Azure 2.0 e come creare le VM Linux da questo disco personalizzato. È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](virtual-machines-linux-upload-vhd-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Questa funzionalità consente di installare e configurare una distribuzione Linux in base ai propri requisiti e quindi di usare il disco rigido virtuale per creare rapidamente macchine virtuali di Azure.

## <a name="quick-commands"></a>Comandi rapidi
Se si vuole eseguire rapidamente l'attività, la sezione seguente indica in dettaglio i comandi base per caricare un disco rigido virtuale in Azure. Altre informazioni dettagliate e il contesto per ogni passaggio sono disponibili nelle sezioni successive del documento, [a partire da qui](#requirements).

Assicurarsi di avere installato la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e di aver eseguito l'accesso a un account Azure tramite il comando [az login](/cli/azure/#login).

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono `myResourceGroup`, `mystorageaccount` e `mydisks`.

Creare prima un gruppo di risorse con [az group create](/cli/azure/group#create). Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella località `WestUs`:

```azurecli
az group create --name myResourceGroup --location westus
```

Creare un account di archiviazione in cui salvare i dischi virtuali con [az storage account create](/cli/azure/storage/account#create). Anche se si desidera usare [Azure Managed Disks Overview](../storage/storage-managed-disks-overview.md) (Panoramica di Azure Managed Disks), è necessario creare un account di archiviazione in cui caricare il disco rigido virtuale prima della conversione in un disco gestito. Nell'esempio seguente viene creato un nuovo account di archiviazione denominato `mystorageaccount`:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Elencare le chiavi di accesso per l'account di archiviazione con [az storage account keys list](/cli/azure/storage/account/keys#list). Annotare il valore di `key1`:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Creare un contenitore nell'account di archiviazione con [az storage container create](/cli/azure/storage/container#create) usando la chiave di archiviazione ottenuta. Nell'esempio seguente viene creato un contenitore denominato `mydisks` usando il valore della chiave di archiviazione da `key1`:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Usare infine [az storage blob upload](/cli/azure/storage/blob#upload) per caricare il disco rigido virtuale nel contenitore creato. Specificare il percorso locale per il disco rigido virtuale in `/path/to/disk/mydisk.vhd`:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

### <a name="azure-managed-disks"></a>Azure Managed Disks
È possibile creare una VM usando Azure Managed Disks o dischi non gestiti. La funzionalità Managed Disks viene gestita dalla piattaforma Azure e non richiede alcuna pianificazione o posizione per l'archiviazione. Per altre informazioni su Azure Managed Disks, vedere [Azure Managed Disks overview](../storage/storage-managed-disks-overview.md) (Panoramica di Azure Managed Disks). Per creare una macchina virtuale dal disco rigido virtuale, convertire innanzitutto il disco rigido virtuale in un disco gestito con [az disk create](/cli/azure/disk/create):

```azurecli
az disk create --resource-group myResourceGroup --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```

Ottenere l'URI del disco gestito creato con [az disk list](/cli/azure/disk/list):

```azurecli
az disk list --resource-group myResourceGroup \
  --query '[].{Name:name,URI:creationData.sourceUri}' --output table
```

L'output è simile all'esempio seguente:

```azurecli
Name               URI
-----------------  ----------------------------------------------------------------------------------------------------
myUMDiskFromVHD    https://vhdstoragezw9.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/my_image-osDisk.vhd
```

Ora, creare la VM con [az vm create](/cli/azure/vm#create) e specificare l'URI del disco gestito (`--image`). L'esempio seguente crea una VM denominata `myVM` utilizzando il disco gestito creato dal disco rigido virtuale caricato:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --attach-os-disk https://vhdstoragezw9.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/my_image-osDisk.vhd
```

### <a name="unmanaged-disks"></a>Dischi non gestiti
Per creare una macchina virtuale con dischi non gestiti, specificare l'URI del disco (`--image`) con [az vm create](/cli/azure/vm#create). L'esempio seguente crea una VM denominata `myVM` usando il disco virtuale caricato in precedenza:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

L'account di archiviazione di destinazione deve essere lo stesso in cui è stato caricato il disco virtuale. È anche necessario specificare tutti i parametri aggiuntivi richiesti dal comando **az vm create**, ad esempio rete virtuale, indirizzo IP pubblico, nome utente e chiavi SSH, oppure rispondere ai messaggi inerenti. Sono disponibili altre informazioni sui [parametri di Resource Manager per l'interfaccia della riga di comando](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Requisiti
Per completare la procedura seguente, è necessario:

* **Sistema operativo Linux installato in un file VHD**: installare una [distribuzione Linux approvata da Azure](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), oppure vedere le [informazioni sulle distribuzioni non approvate](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), in un disco virtuale in formato VHD. Sono disponibili vari strumenti per la creazione di una VM e un file VHD:
  * Installare e configurare [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) o [KVM](http://www.linux-kvm.org/page/RunningKVM), prestando attenzione a usare il disco rigido virtuale come formato di immagine. Se necessario, è possibile [convertire un'immagine](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) usando `qemu-img convert`.
  * È anche possibile usare Hyper-V [in Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) o [in Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Il formato VHDX più recente non è supportato in Azure. Quando si crea una VM, specificare VHD come formato. Se necessario, è possibile convertire i dischi VHDX nel formato VHD usando [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) o il cmdlet [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) di PowerShell. Inoltre, Azure non supporta il caricamento di VHD dinamici, pertanto è necessario convertire tali dischi in VHD statici prima del caricamento. Per convertire dischi dinamici durante il processo di caricamento in Azure, sono disponibili strumenti come [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) .
> 
> 

* Le VM create da un disco personalizzato devono trovarsi nello stesso account di archiviazione del disco stesso
  * Creare un account di archiviazione e un contenitore in cui inserire il disco personalizzato e le VM create
  * Una volta create tutte le VM, è possibile eliminare il disco

Assicurarsi di avere installato la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e di aver eseguito l'accesso a un account Azure tramite il comando [az login](/cli/azure/#login).

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono `myResourceGroup`, `mystorageaccount` e `mydisks`.

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Preparare il disco da caricare
Azure supporta svariate distribuzioni di Linux (vedere la sezione [Distribuzioni approvate](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Gli articoli seguenti forniscono le istruzioni per preparare le diverse distribuzioni Linux supportate in Azure:

* **[Distribuzioni basate su CentOS](virtual-machines-linux-create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES e openSUSE](virtual-machines-linux-suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Altro - Distribuzioni non approvate](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Vedere anche le **[Note generali sull'installazione di Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** per suggerimenti più generali sulla preparazione di immagini Linux per Azure.

> [!NOTE]
> Il [Contratto di Servizio per la piattaforma Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) è applicabile alle VM che eseguono Linux solo quando una distribuzione approvata viene usata con i dettagli di configurazione specificati nella sezione "Versioni e distribuzioni supportate" in [Distribuzioni di Linux supportate da Azure](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
I gruppi di risorse riuniscono in modo logico tutte le risorse di Azure a supporto delle macchine virtuali, come l'archiviazione e la rete virtuale. Per altre informazioni sui gruppi di risorse, vedere la [panoramica dei gruppi di risorse](../azure-resource-manager/resource-group-overview.md). Prima di caricare il disco personalizzato e creare le VM, è necessario creare un gruppo di risorse con [az group create](/cli/azure/group#create).

Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella località `westus`: [Azure Managed Disks Overview](../storage/storage-managed-disks-overview.md) (Panoramica di Azure Managed Disks)
```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione
È possibile creare una VM usando Azure Managed Disks o dischi non gestiti. La funzionalità Managed Disks viene gestita dalla piattaforma Azure e non richiede alcuna pianificazione o posizione per l'archiviazione. I dischi non gestiti vengono archiviati come BLOB di pagine all'interno di un account di archiviazione. Per altre informazioni, vedere [Azure Managed Disks Overview](../storage/storage-managed-disks-overview.md) (Panoramica di Azure Managed Disks) o [Archiviazione BLOB di Azure qui](../storage/storage-introduction.md#blob-storage). Anche se si desidera utilizzare dischi gestiti, è necessario creare un account di archiviazione in cui caricare il disco rigido virtuale prima della conversione in un disco gestito.

Creare un account di archiviazione per il disco personalizzato e le VM con [az storage account create](/cli/azure/storage/account#create). Le VM con dischi non gestiti create partendo dal disco personalizzato devono trovarsi nello stesso account di archiviazione del disco. È possibile creare VM con dischi gestiti in qualsiasi gruppo di risorse all'interno della sottoscrizione.

Nell'esempio seguente viene creato un account di archiviazione denominato `mystorageaccount` nel gruppo di risorse creato in precedenza:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Ottenere chiavi degli account di archiviazione
Azure genera due chiavi di accesso a 512 bit per ogni account di archiviazione. Queste chiavi di accesso vengono utilizzate per autenticarsi nell'account di archiviazione, ad esempio per eseguire operazioni di scrittura. Ulteriori informazioni sulla [gestione dell'accesso all'archiviazione sono disponibili qui](../storage/storage-create-storage-account.md#manage-your-storage-account). Visualizzare le chiavi di accesso con [az storage account keys list](/cli/azure/storage/account/keys#list).

Visualizzare le chiavi di accesso per l'account di archiviazione creato:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
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
Prendere nota dell'elemento `key1` perché verrà utilizzato per interagire con l'account di archiviazione nei passaggi successivi.

## <a name="create-a-storage-container"></a>Creare un contenitore di archiviazione
Nello stesso modo in cui si creano directory diverse per organizzare in modo logico il file system locale si creano anche i contenitori con un account di archiviazione per organizzare i dischi. Un account di archiviazione può contenere un numero qualsiasi di contenitori. Creare un contenitore con [az storage container create](/cli/azure/storage/container#create).

L'esempio seguente crea un nuovo contenitore denominato `mydisks`, specificando la chiave di accesso ricavata nel passaggio precedente (`key1`):

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

## <a name="upload-vhd"></a>Caricare il file VHD.
Caricare ora il disco personalizzato con [az storage blob upload](/cli/azure/storage/blob#upload). Caricare e archiviare il disco personalizzato come BLOB di pagine.

Specificare la chiave di accesso e il contenitore creato nel passaggio precedente, quindi selezionare il percorso del disco personalizzato sul computer locale:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-vm-from-custom-disk"></a>Creare una VM in base a un disco personalizzato
È possibile creare una VM usando Azure Managed Disks o dischi non gestiti. Per entrambi i tipi, specificare l'URI per il disco gestito o non gestito quando si crea una VM. Per i dischi non gestiti, assicurarsi che l'account di archiviazione corrisponda alla destinazione in cui viene archiviato il disco personalizzato. È possibile creare la macchina virtuale tramite Azure 2.0 o il modello JSON di Resource Manager.

### <a name="azure-cli-20---azure-managed-disks"></a>Interfaccia della riga di comando di Azure 2.0 - Managed Disks di Azure
Per creare una macchina virtuale dal disco rigido virtuale, convertire innanzitutto il disco rigido virtuale in un disco gestito con [az disk create](/cli/azure/disk/create). Nell'esempio seguente viene creato un disco gestito denominato `myManagedDisk` dal disco rigido virtuale caricato nell'account di archiviazione e nel contenitore denominati:

```azurecli
az disk create --resource-group myResourceGroup --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```

Ottenere l'URI del disco gestito creato con [az disk list](/cli/azure/disk/list):

```azurecli
az disk list --resource-group myResourceGroup \
  --query '[].{Name:name,URI:creationData.sourceUri}' --output table
```

L'output è simile all'esempio seguente:

```azurecli
Name               URI
-----------------  ----------------------------------------------------------------------------------------------------
myUMDiskFromVHD    https://vhdstoragezw9.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/my_image-osDisk.vhd
```

Ora, creare la VM con [az vm create](/cli/azure/vm#create) e specificare l'URI del disco gestito (`--image`). L'esempio seguente crea una VM denominata `myVM` utilizzando il disco gestito creato dal disco rigido virtuale caricato:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --attach-os-disk https://vhdstoragezw9.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/my_image-osDisk.vhd
```

### <a name="azure-20---unmanaged-disks"></a>Interfaccia della riga di comando di Azure 2.0 - Dischi non gestiti
Per creare una macchina virtuale con dischi non gestiti, specificare l'URI del disco (`--image`) con [az vm create](/cli/azure/vm#create). L'esempio seguente crea una VM denominata `myVM` usando il disco virtuale caricato in precedenza:

Specificare il parametro `--image` con il comando [az vm create](/cli/azure/vm#create) in modo da puntare al disco personalizzato. Assicurarsi che `--storage-account` corrisponda all'account di archiviazione in cui è archiviato il disco personalizzato. Non è necessario utilizzare lo stesso contenitore come disco personalizzato per archiviare le VM. Assicurarsi di creare qualsiasi contenitore aggiuntivo seguendo la stessa procedura utilizzata in precedenza prima di caricare il disco personalizzato.

L'esempio seguente crea una VM denominata `myVM` dal disco personalizzato:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

È ancora necessario specificare tutti i parametri aggiuntivi richiesti dal comando **az vm create**, ad esempio nome utente e chiavi SSH.


### <a name="resource-manager-template---unmanaged-disks"></a>Modello di Resource Manager - Dischi non gestiti
I modelli di Azure Resource Manager sono file JavaScript Object Notation (JSON) che definiscono l'ambiente che si desidera generare. I modelli sono suddivisi in diversi provider di risorse, ad esempio calcolo o rete. È possibile utilizzare i modelli esistenti o crearne di nuovi. Altre informazioni sull' [uso di Resource Manager e relativi modelli](../azure-resource-manager/resource-group-overview.md).

Nel provider `Microsoft.Compute/virtualMachines` del modello, è presente un nodo `storageProfile` contenente i dettagli sulla configurazione della VM. I due parametri principali da modificare sono gli URI `image` e `vhd`, che puntano al disco personalizzato e al disco virtuale della nuova VM. Di seguito viene mostrato un esempio di JSON per l'utilizzo di un disco personalizzato:

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

È possibile usare [questo modello esistente per creare una VM da un'immagine personalizzata](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) oppure consultare le informazioni sulla [creazione di modelli di Azure Resource Manager personalizzati](../resource-group-authoring-templates.md). 

Dopo aver configurato un modello, creare le VM tramite il comando [az group deployment create](/cli/azure/group/deployment#create). Specificare l'URI del modello JSON con il parametro `--template-uri` :

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Se si dispone di un file JSON archiviato localmente nel computer in uso, è possibile utilizzare il parametro alternativo `--template-file` :

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Passaggi successivi
Dopo avere preparato e caricato il disco rigido virtuale, è possibile ottenere altre informazioni sull' [uso di Azure Resource Manager e dei modelli](../azure-resource-manager/resource-group-overview.md). È anche consigliabile [aggiungere un disco dati](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) alle nuove VM. Se nelle VM sono in esecuzione applicazioni a cui si deve accedere, assicurarsi di [aprire le porte e gli endpoint](virtual-machines-linux-nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


