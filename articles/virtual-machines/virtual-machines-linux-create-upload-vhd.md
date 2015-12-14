<properties
	pageTitle="Creazione e caricamento di un VHD Linux | Microsoft Azure"
	description="Creare e caricare un disco rigido virtuale Azure (VHD) con il modello di distribuzione classica che contiene il sistema operativo Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/04/2015"
	ms.author="dkshir"/>

# Creazione e caricamento di un disco rigido virtuale che contiene il sistema operativo Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.


Questo articolo illustra come creare e caricare un disco rigido virtuale (VHD) in modo da usarlo come immagine per la creazione di macchine virtuali in Azure. L'articolo fornisce istruzioni su come preparare il sistema operativo in modo da usarlo per la creazione di più macchine virtuali basate sull'immagine specificata.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Una macchina virtuale in Azure esegue il sistema operativo basato sull'immagine scelta al momento della creazione della macchina virtuale. Le immagini vengono archiviate in formato VHD, nei file vhd in un account di archiviazione. Per i dettagli, vedere [Dischi in Azure](virtual-machines-disks-vhds.md) e [immagini in Azure](virtual-machines-images.md).

Quando si crea una macchina virtuale, è possibile personalizzare le impostazioni del sistema operativo in modo che siano appropriate all'applicazione che si desidera eseguire. Per istruzioni, vedere [Come creare una macchina virtuale personalizzata](virtual-machines-create-custom.md).

**Importante**: il contratto di servizio della piattaforma Azure si applica alle macchine virtuali che eseguono il sistema operativo Linux solo quando una distribuzione approvata viene usata con i dettagli di configurazione specificati in 'Versioni supportate' in [Linux in Azure - Distribuzioni supportate](virtual-machines-../linux-endorsed-distributions.md). Tutte le distribuzioni di Linux disponibili nella raccolta immagini di Azure sono distribuzioni approvate con la configurazione richiesta.


## Prerequisiti
In questo articolo si presuppone che l'utente disponga degli elementi seguenti:

- **Un certificato di gestione**: l'utente deve avere creato un certificato di gestione per la sottoscrizione per cui si desidera caricare un file VHD e avere esportato il certificato in un file con estensione cer. Per altre informazioni sulla creazione di certificati, vedere [Panoramica dei certificati per Azure](../cloud-services/cloud-services-certs-create.md).

- **Sistema operativo Linux installato in un file VHD**: l'utente deve avere installato un sistema operativo Linux supportato in un disco rigido virtuale. Sono disponibili diversi strumenti per creare file VHD, ad esempio per creare il file VHD e installare il sistema operativo, è possibile usare una soluzione di virtualizzazione come Hyper-V. Per istruzioni, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx).

	**Importante**: il formato VHDX più recente non è supportato in Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il cmdlet convert-vhd.

	Per un elenco delle distribuzioni approvate, vedere [Linux in Azure - Distribuzioni approvate](../linux-endorsed-distributions.md). Per un elenco generale delle distribuzioni di Linux, vedere [Informazioni per le distribuzioni non-approvate](virtual-machines-linux-create-upload-vhd-generic.md).

- **Interfaccia della riga di comando di Azure**: se si usa un sistema operativo Linux per creare l'immagine, usare l’[interfaccia della riga di comando di Azure](../virtual-machines-command-line-tools.md) per caricare il file VHD.

- **Strumenti di Azure PowerShell**: il cmdlet `Add-AzureVhd` può essere utilizzato per caricare il VHD. Per scaricare i cmdlet di Azure Powershell, visitare la pagina [Download di Azure](http://azure.microsoft.com/downloads/). Per informazioni di riferimento, vedere [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx).

<a id="prepimage"> </a>
## Passaggio 1: preparare l'immagine da caricare

Azure supporta numerose distribuzioni di Linux (vedere la sezione [Distribuzioni approvate](../linux-endorsed-distributions.md)). Gli articoli seguenti forniscono le istruzioni per preparare le diverse distribuzioni Linux supportate in Azure:

- **[Distribuzioni basate su CentOS](virtual-machines-linux-create-upload-vhd-centos.md)**
- **[Oracle Linux](virtual-machines-linux-create-upload-vhd-oracle.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-create-upload-vhd-redhat.md)**
- **[SLES e openSUSE](../virtual-machines-linux-create-upload-vhd-suse)**
- **[Ubuntu](virtual-machines-linux-create-upload-vhd-ubuntu.md)**
- **[Altro - Distribuzioni non approvate](virtual-machines-linux-create-upload-vhd-generic.md)**

Vedere inoltre le **[note sull'installazione di Linux](virtual-machines-linux-create-upload-vhd-generic.md#linuxinstall)** per ulteriori suggerimenti sulla preparazione di immagini Linux per Azure.

Una volta eseguite le procedure nelle guide precedenti, si dovrebbe avere un file VHD pronto per essere caricato in Azure.

<a id="connect"> </a>
## Passaggio 2: preparare la connessione ad Azure

Prima di poter caricare un file VHD, è necessario stabilire una connessione sicura tra il computer e la sottoscrizione in Azure.


### Se si utilizza l'interfaccia della riga di comando di Azure

Le impostazioni predefinite dell’interfaccia della riga di comando di Azure più recenti nel modello di distribuzione di Gestione risorse, quindi verificare che ci si trovi nel modello di distribuzione classica mediante questo comando:

		azure change mode asm  

Successivamente, utilizzare uno qualsiasi dei seguenti metodi di accesso per connettersi alla sottoscrizione di Azure.

Utilizzare il metodo di Azure AD per accedere:

1. Aprire una finestra della riga di comando di Azure

2. Digitare:

	`azure login`

	Quando richiesto, digitare nome utente e password.

**OPPURE**, utilizzare invece un file di impostazioni di pubblicazione:

1. Aprire una finestra della riga di comando di Azure

2. Digitare:

	`azure account download`

	Questo comando apre una finestra del browser e scarica automaticamente un file .publishsettings che contiene un certificato e le informazioni relative alla sottoscrizione di Azure.

3. Salvare il file .publishsettings

4. Digitare:

	`azure account import <PathToFile>`

	Dove `<PathToFile>` è il percorso completo del file .publishsettings.

	Per ulteriori informazioni, leggere [connettersi ad Azure da Azure CLI](../xplat-cli-connect.md).


### Se si utilizza Azure PowerShell

Utilizzare il metodo di Azure AD per accedere:

1. Aprire una finestra di Azure PowerShell.

2. Digitare:

	`Add-AzureAccount`

	Quando richiesto, immettere l'id utente dell'organizzazione e la password.

**O**, in alternativa, utilizzare i file di impostazioni di pubblicazione:

1. Aprire una finestra di Azure PowerShell.

2. Digitare:

	`Get-AzurePublishSettingsFile`

	Questo comando apre una finestra del browser e scarica automaticamente un file .publishsettings che contiene un certificato e le informazioni relative alla sottoscrizione di Azure.

3. Salvare il file .publishsettings.

4. Digitare:

	`Import-AzurePublishSettingsFile <PathToFile>`

	Dove `<PathToFile>` è il percorso completo del file .publishsettings.

	Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md).

> [AZURE.NOTE]È consigliabile che utilizzare il metodo più recente di Azure Active Directory per l'accesso alla sottoscrizione di Azure, dal CLI Azure o Azure PowerShell.

<a id="upload"> </a>
## Passaggio 3: caricare l'immagine in Azure

### Se si utilizza l'interfaccia della riga di comando di Azure

Utilizzare l’interfaccia della riga di comando di Azure per caricare l'immagine. È possibile caricare un'immagine usando il comando seguente:

		azure vm image create <image-name> --location <location-of-the-data-center> --os Linux <source-path-to the vhd>

### Se si utilizza PowerShell

È necessario per caricare un account di archiviazione in cui caricare il file VHD. È possibile sceglierne uno esistente o crearne uno nuovo. Per creare un account di archiviazione, vedere [Creare un account di archiviazione](../storage-create-storage-account.md)

È possibile caricare il file VHD in qualsiasi posizione all'interno dell'archiviazione BLOB. Negli esempi di comandi seguenti, **BlobStorageURL** è l'URL per l'account di archiviazione che si intende utilizzare e **YourImagesFolder** è il contenitore all'interno dell'archiviazione BLOB in cui si desidera archiviare le immagini. **VHDName** è l'etichetta che identifica il disco rigido virtuale visualizzata nel [portale di Azure classico](http://manage.windowsazure.com). **PathToVHDFile** è il percorso completo e il nome del file VHD.

Nella finestra di Azure PowerShell utilizzata nel passaggio precedente, digitare:

		Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>

Per ulteriori informazioni, vedere [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx).

> [AZURE.NOTE]La [versione di anteprima di Azure Powershell 1.0](https://azure.microsoft.com/blog/azps-1-0-pre/) cambia notevolmente il modo in cui gestisce i cmdlet per il modello di distribuzione classica e di Gestione risorse. Questo articolo non usa ancora la versione di anteprima.


[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload

<!---HONumber=AcomDC_1203_2015-->