<properties
	pageTitle="Creazione e caricamento di un VHD Linux | Microsoft Azure"
	description="Creare e caricare un disco rigido virtuale Azure (VHD) con il modello di distribuzione classica che contiene il sistema operativo Linux."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/12/2016"
	ms.author="iainfou"/>

# Creazione e caricamento di un disco rigido virtuale che contiene il sistema operativo Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.

Questo articolo illustra come creare e caricare un disco rigido virtuale (VHD) in modo da usarlo come immagine per la creazione di macchine virtuali in Azure. L'articolo fornisce istruzioni su come preparare il sistema operativo in modo da usarlo per la creazione di più macchine virtuali basate sull'immagine specificata.

**Importante**: il contratto di servizio della piattaforma Azure si applica alle macchine virtuali che eseguono il sistema operativo Linux solo quando una distribuzione approvata viene usata con i dettagli di configurazione specificati in 'Versioni supportate' in [Linux in Azure - Distribuzioni supportate](virtual-machines-linux-endorsed-distros.md). Tutte le distribuzioni di Linux disponibili nella raccolta immagini di Azure sono distribuzioni approvate con la configurazione richiesta.


## Prerequisiti
In questo articolo si presuppone che l'utente disponga degli elementi seguenti:

- **Sistema operativo Linux installato in un file VHD**: l'utente deve avere installato un sistema operativo Linux supportato in un disco rigido virtuale. Sono disponibili diversi strumenti per creare file VHD, ad esempio per creare il file VHD e installare il sistema operativo, è possibile usare una soluzione di virtualizzazione come Hyper-V. Per istruzioni, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx).

	> [AZURE.NOTE] Il formato VHDX più recente non è supportato in Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il cmdlet convert-vhd.

Per un elenco delle distribuzioni approvate, vedere [Linux in Azure - Distribuzioni approvate](virtual-machines-linux-endorsed-distros.md). Per un elenco generale delle distribuzioni di Linux, vedere [Informazioni per le distribuzioni non-approvate](virtual-machines-linux-create-upload-generic.md).

- **Interfaccia della riga di comando di Azure**: installare e usare l'[interfaccia della riga di comando di Azure](../virtual-machines-command-line-tools.md) per caricare il VHD.

> [AZURE.TIP] Azure non supporta il caricamento di VHD dinamici, pertanto è necessario convertire tali dischi in VHD statici prima del caricamento. Per convertire dischi dinamici, sono disponibili strumenti come [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go).

<a id="prepimage"> </a>
## Passaggio 1: preparare l'immagine da caricare

Azure supporta numerose distribuzioni di Linux (vedere la sezione [Distribuzioni approvate](virtual-machines-linux-endorsed-distros.md)). Gli articoli seguenti forniscono le istruzioni per preparare le diverse distribuzioni Linux supportate in Azure:

- **[Distribuzioni basate su CentOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES e openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Altro - Distribuzioni non approvate](virtual-machines-linux-create-upload-generic.md)**

Vedere inoltre le **[note sull'installazione di Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** per ulteriori suggerimenti sulla preparazione di immagini Linux per Azure.

Una volta eseguite le procedure nelle guide precedenti, si dovrebbe avere un file VHD pronto per essere caricato in Azure.

<a id="connect"> </a>
## Passaggio 2: preparare la connessione ad Azure

Assicurarsi di usare l'interfaccia della riga di comando di Azure nel modello di distribuzione classico (`azure config mode asm`), quindi accedere al proprio account:

```
azure login
```


<a id="upload"> </a>
## Passaggio 3: caricare l'immagine in Azure

È necessario per caricare un account di archiviazione in cui caricare il file VHD. È possibile sceglierne uno esistente o crearne uno nuovo. Per creare un account di archiviazione, fare riferimento a [Creare un account di archiviazione](../storage/storage-create-storage-account.md).

È possibile caricare il file VHD in qualsiasi posizione all'interno dell'archiviazione BLOB. Negli esempi di comandi seguenti, **BlobStorageURL** è l'URL per l'account di archiviazione che si intende utilizzare e **YourImagesFolder** è il contenitore all'interno dell'archiviazione BLOB in cui si desidera archiviare le immagini. **VHDName** è l'etichetta visualizzata nel [portale di Azure](http://portal.azure.com) o nel [portale di Azure classico](http://manage.windowsazure.com) per identificare il disco rigido virtuale. **PathToVHDFile** è il percorso completo e il nome del file con estensione vhd della macchina.

Usare l'interfaccia della riga di comando di Azure per caricare l'immagine tramite il comando seguente:

		azure vm image create <ImageName> --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> --os Linux <PathToVHDFile>

Per altri dettagli, vedere [Comandi dell'interfaccia della riga di comando di Azure in modalità Gestione servizi di Azure (asm)](../virtual-machines-command-line-tools.md).

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload

<!---HONumber=AcomDC_0518_2016-->