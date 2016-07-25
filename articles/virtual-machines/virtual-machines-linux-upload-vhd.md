<properties
	pageTitle="Creazione e caricamento di un VHD Linux | Microsoft Azure"
	description="Creare e caricare in Azure un disco rigido virtuale usando il modello di distribuzione Azure Resource Manager."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2016"
	ms.author="iainfou"/>

# Caricamento di un disco rigido virtuale

Questo articolo illustra come caricare un disco rigido virtuale usando il modello di distribuzione Azure Resource Manager. Questa funzionalità consente di installare e configurare una distribuzione Linux in base ai propri requisiti e quindi di usare il disco rigido virtuale per creare rapidamente macchine virtuali di Azure.

## Comandi rapidi
Assicurarsi di avere effettuato l'accesso tramite l'[ interfaccia della riga di comando di Azure](../xplat-cli-install.md) e di usare la modalità Resource Manager (`azure config mode arm`).

Creare prima un gruppo di risorse:

```bash
azure group create TestRG --location "WestUS"
```

Creare un account di archiviazione in cui salvare i dischi virtuali:

```bash
azure storage account create testuploadedstorage --resource-group TestRG \
	--location "WestUS" --kind Storage --sku-name LRS
```

Elencare le chiavi di archiviazione per l'account di archiviazione appena creato e prendere nota di `key1`:

```bash
azure storage account keys list testuploadedstorage --resource-group TestRG
```

L'output sarà analogo al seguente:

```
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```

Creare un contenitore nell'account di archiviazione usando la chiave di archiviazione appena ottenuta:

```bash
azure storage container create --account-name testuploadedstorage \
	--account-key <key1> --container vm-images
```

Caricare infine il disco rigido virtuale nel contenitore appena creato:

```bash
azure storage blob upload --blobtype page --account-name testuploadedstorage \
	--account-key <key1> --container vm-images /path/to/disk/yourdisk.vhd
```

È ora possibile creare una macchina virtuale dal disco rigido virtuale caricato [usando un modello di Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) oppure tramite l'interfaccia della riga di comando, specificando l'URI per il disco come indicato di seguito:

```bash
azure vm create TestVM -l "WestUS" --resource-group TestRG \
	-Q https://testuploadedstorage.blob.core.windows.net/vm-images/yourdisk.vhd
```

Si noti che saranno comunque necessari tutti i parametri aggiuntivi richiesti dal comando `azure vm create`, ad esempio rete virtuale, indirizzo IP pubblico, nome utente, chiavi SSH e così via. Sono disponibili altre informazioni sui [parametri di Resource Manager per l'interfaccia della riga di comando](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## Requisiti
Ecco i requisiti per completare i passaggi precedenti:

- **Sistema operativo Linux installato in un file VHD**: installare una [distribuzione Linux approvata da Azure](virtual-machines-linux-endorsed-distros.md) (oppure vedere le [informazioni sulle distribuzioni non approvate](virtual-machines-linux-create-upload-generic.md)) in un disco virtuale in formato VHD. Sono disponibili vari strumenti per la creazione di una VM e un file VHD:
	- Installare e configurare [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) o [KVM](http://www.linux-kvm.org/page/RunningKVM), prestando attenzione a usare VHD come formato di immagine. È possibile [convertire un'immagine](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) usando `qemu-img convert`, se necessario.
	- È anche possibile usare Hyper-V [in Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) o [in Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Il formato VHDX più recente non è supportato in Azure. Quando si crea una macchina virtuale, specificare il formato VHD originale. Se necessario, è possibile convertire un file VHDX nel formato VHD usando [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) o il cmdlet [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) di PowerShell. Inoltre, Azure non supporta il caricamento di VHD dinamici, pertanto è necessario convertire tali dischi in VHD statici prima del caricamento. Per convertire dischi dinamici durante il processo di caricamento in Azure, sono disponibili strumenti come [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go).


<a id="prepimage"> </a>
## Preparare l'immagine da caricare

Azure supporta numerose distribuzioni di Linux (vedere la sezione [Distribuzioni approvate](virtual-machines-linux-endorsed-distros.md)). Gli articoli seguenti forniscono le istruzioni per preparare le diverse distribuzioni Linux supportate in Azure. Assicurarsi di preparare in modo corretto il disco virtuale prima del caricamento:

- **[Distribuzioni basate su CentOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES e openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Altro - Distribuzioni non approvate](virtual-machines-linux-create-upload-generic.md)**

Vedere anche le **[Note generali sull'installazione di Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** per suggerimenti più generali sulla preparazione di immagini Linux per Azure.

> [AZURE.NOTE] Il [Contratto di Servizio per Macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/) è applicabile alle macchine virtuali che eseguono Linux solo quando una distribuzione approvata viene usata con i dettagli di configurazione specificati nella sezione 'Versioni e distribuzioni supportate' in [Distribuzioni di Linux supportate da Azure](virtual-machines-linux-endorsed-distros.md).

## Passaggi successivi
Dopo avere preparato e caricato il disco rigido virtuale, è possibile ottenere altre informazioni sull'[uso di Azure Resource Manager e dei modelli](../resource-group-overview.md). È anche consigliabile [aggiungere un disco dati](virtual-machines-linux-add-disk.md) alle nuove macchine virtuali. Se nelle macchine virtuali sono in esecuzione applicazioni a cui si deve accedere, assicurarsi di [aprire porte endpoint](virtual-machines-linux-nsg-quickstart.md).

<!---HONumber=AcomDC_0713_2016-->