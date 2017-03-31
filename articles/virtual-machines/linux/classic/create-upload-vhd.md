---
title: Creazione e caricamento di un VHD Linux in Azure | Documentazione Microsoft
description: Creare e caricare un disco rigido virtuale di Azure (VHD) contenente il sistema operativo Linux con il modello di distribuzione classica
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 8058ff98-db03-4309-9bf4-69842bd64dd4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: fab3f539ddbc35b19a819c5b01403b6b1b6437fc
ms.lasthandoff: 03/27/2017


---
# <a name="creating-and-uploading-a-virtual-hard-disk-that-contains-the-linux-operating-system"></a>Creazione e caricamento di un disco rigido virtuale che contiene il sistema operativo Linux
> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. È anche possibile [caricare un'immagine disco personalizzata tramite Azure Resource Manager](../../virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Questo articolo illustra come creare e caricare un disco rigido virtuale (VHD) in modo da usarlo come immagine per la creazione di macchine virtuali in Azure. L'articolo fornisce istruzioni su come preparare il sistema operativo in modo da usarlo per la creazione di più macchine virtuali basate sull'immagine specificata. 


## <a name="prerequisites"></a>Prerequisiti
In questo articolo si presuppone che l'utente disponga degli elementi seguenti:

* **Sistema operativo Linux installato in un file .vhd**: è stata installata una [distribuzione di Linux approvata da Azure](../../virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (oppure consultare le [informazioni sulle distribuzioni non approvate](../../virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) in un disco virtuale in formato VHD. Sono disponibili vari strumenti per la creazione di una VM e un file VHD:
  * Installare e configurare [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) o [KVM](http://www.linux-kvm.org/page/RunningKVM), prestando attenzione a usare il disco rigido virtuale come formato di immagine. Se necessario, è possibile [convertire un'immagine](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) usando `qemu-img convert`.
  * È anche possibile usare Hyper-V [in Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) o [in Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Il formato VHDX più recente non è supportato in Azure. Quando si crea una VM, specificare VHD come formato. Se necessario, è possibile convertire i dischi VHDX nel formato VHD usando [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) o il cmdlet [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) di PowerShell. Inoltre, Azure non supporta il caricamento di VHD dinamici, pertanto è necessario convertire tali dischi in VHD statici prima del caricamento. Per convertire dischi dinamici durante il processo di caricamento in Azure, sono disponibili strumenti come [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) .

* **Interfaccia della riga di comando di Azure** : installare l' [interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) più recente per caricare il VHD.

<a id="prepimage"> </a>

## <a name="step-1-prepare-the-image-to-be-uploaded"></a>Passaggio 1: preparare l'immagine da caricare
Azure supporta svariate distribuzioni di Linux (vedere la sezione [Distribuzioni approvate](../../virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Gli articoli seguenti forniscono le istruzioni per preparare le diverse distribuzioni Linux supportate in Azure. Dopo aver eseguito le procedure nelle guide seguenti, tornare qui quando è disponibile un file VHD pronto per essere caricato in Azure:

* **[Distribuzioni basate su CentOS](../../virtual-machines-linux-create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](../../virtual-machines-linux-debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](../../virtual-machines-linux-oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](../../virtual-machines-linux-redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES e openSUSE](../../virtual-machines-linux-suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](../../virtual-machines-linux-create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Altro - Distribuzioni non approvate](../../virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

> [!NOTE]
> Il contratto di servizio della piattaforma Azure si applica alle macchine virtuali che eseguono il sistema operativo Linux solo quando una distribuzione approvata viene usata con i dettagli di configurazione specificati in "Versioni supportate" in [Linux in Azure - Distribuzioni supportate](../../virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Tutte le distribuzioni di Linux disponibili nella raccolta immagini di Azure sono distribuzioni approvate con la configurazione richiesta.
> 
> 

Vedere anche le **[Note generali sull'installazione di Linux](../../virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** per suggerimenti più generali sulla preparazione di immagini Linux per Azure.

<a id="connect"> </a>

## <a name="step-2-prepare-the-connection-to-azure"></a>Passaggio 2: preparare la connessione ad Azure
Assicurarsi di usare l'interfaccia della riga di comando di Azure nel modello di distribuzione classica (`azure config mode asm`), quindi accedere al proprio account:

```azurecli
azure login
```


<a id="upload"> </a>

## <a name="step-3-upload-the-image-to-azure"></a>Passaggio 3: caricare l'immagine in Azure
È necessario un account di archiviazione in cui caricare il file VHD. È possibile usare un account di archiviazione esistente o [crearne uno nuovo](../../../storage/storage-create-storage-account.md).

Usare l'interfaccia della riga di comando di Azure per caricare l'immagine tramite il comando seguente:

```azurecli
azure vm image create <ImageName> `
    --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
    --os Linux <PathToVHDFile>
```

Nell'esempio precedente:

* **BlobStorageURL** è l'URL dell'account di archiviazione che si prevede di usare.
* **YourImagesFolder** è il contenitore all'interno dell'archiviazione BLOB in cui si vogliono archiviare le immagini.
* **VHDName** è l'etichetta che identifica il disco rigido virtuale visualizzata nel portale.
* **PathToVHDFile** è il percorso completo e il nome del file con estensione .vhd della macchina.

Il comando seguente illustra un esempio completo:

```azurecli
azure vm image create myImage `
    --blob-url https://mystorage.blob.core.windows.net/vhds/myimage.vhd `
    --os Linux /home/ahmet/myimage.vhd
```

## <a name="step-4-create-a-vm-from-the-image"></a>Passaggio 4: Creare una VM dall'immagine
Creare una VM usando `azure vm create` come per una normale VM. Specificare il nome assegnato all'immagine nel passaggio precedente. Nell'esempio seguente viene usato il nome dell'immagine **myImage** assegnato nel passaggio precedente:

```azurecli
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
    --location "West US" "myDeployedVM" myImage
```

Per creare le proprie VM, fornire nome utente e password, posizione, nome DNS e nome dell'immagine.

## <a name="next-steps"></a>Passaggi successivi
Per altri dettagli, vedere [Riferimento all'interfaccia della riga di comando di Azure per il modello di distribuzione classica](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

[Step 1: Prepare the image to be uploaded]:#prepimage
[Step 2: Prepare the connection to Azure]:#connect
[Step 3: Upload the image to Azure]:#upload

