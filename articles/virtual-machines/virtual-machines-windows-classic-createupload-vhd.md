<properties
	pageTitle="Creare e caricare un'immagine di Windows Server con Powershell | Microsoft Azure"
	description="Informazioni su come creare e caricare un'immagine Windows Server generalizzata (VHD) utilizzando il modello di distribuzione classica e Azure Powershell."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="cynthn"/>

# Creazione e caricamento di un disco rigido virtuale con Windows Server in Azure

Questo articolo illustra come caricare la propria immagine VM generalizzata come un disco rigido virtuale (VHD) in modo da usarlo per la creazione di macchine virtuali. Per informazioni dettagliate sui dischi e sui dischi rigidi virtuali in Microsoft Azure, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](virtual-machines-linux-about-disks-vhds.md).


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]. È anche possibile [acquisire](virtual-machines-windows-capture-image.md) e [caricare](virtual-machines-windows-upload-image.md) una macchina virtuale usando il modello di Resource Manager.

## Prerequisiti

Questo articolo presuppone che l'utente abbia:

1. **Una sottoscrizione di Azure**: se non si ha tale sottoscrizione, [è possibile aprire un account Azure per ottenerne gratuitamente una](/pricing/free-trial/?WT.mc_id=A261C142F).

2. **[Microsoft Azure PowerShell](../powershell-install-configure.md)**: è necessario che il modulo di Microsoft Azure PowerShell sia installato e configurato per l'uso della sottoscrizione.

3. **Un file .VHD**: una versione supportata del sistema operativo Windows archiviata in un file con .vhd e collegata a una macchina virtuale.

> [AZURE.IMPORTANT] il formato VHDX non è supportato in Microsoft Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il [cmdlet Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx). Per informazioni dettagliate, vedere questo [post di blog](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).

## Passaggio 1: Preparare il disco rigido virtuale 

Prima di caricare il disco rigido virtuale in Azure, è necessario generalizzarlo usando lo strumento Sysprep, che prepara il disco rigido virtuale in modo che possa essere usato come immagine. Per ulteriori informazioni su Sysprep, vedere [Introduzione: Introduzione](http://technet.microsoft.com/library/bb457073.aspx).

Dalla macchina virtuale su cui è stato installato il sistema operativo, completare la seguente procedura:

1. Accedere al sistema operativo.

2. Aprire una finestra del prompt dei comandi come amministratore. Impostare la directory su **%windir%\\system32\\sysprep**, quindi eseguire `sysprep.exe`.

	![Apertura della finestra del Prompt dei comandi](./media/virtual-machines-windows-classic-createupload-vhd/sysprep_commandprompt.png)

3.	Verrà visualizzata la finestra di dialogo **Utilità preparazione sistema**.

	![Avvio di Sysprep](./media/virtual-machines-windows-classic-createupload-vhd/sysprepgeneral.png)

4.  In **Utilità preparazione sistema** selezionare **Passare alla Configurazione guidata** e assicurarsi che l'opzione **Generalizza** sia selezionata.

5.  In **Opzioni di arresto del sistema** selezionare **Arresta il sistema**.

6.  Fare clic su **OK**.

## Passaggio 2: Creare un account di archiviazione e un contenitore

È necessario un account di archiviazione di Azure in cui caricare il file con estensione vhd. Questo passaggio illustra come creare un account o come ottenere le informazioni necessarie da un account esistente. Sostituire le variabili tra &lsaquo; parentesi &rsaquo; con le proprie informazioni.

1. Login

		Add-AzureAccount

1. Impostare la sottoscrizione di Azure.

    	Select-AzureSubscription -SubscriptionName <SubscriptionName> 

2. Creare un nuovo account di archiviazione. Il nome dell'account di archiviazione deve essere univoco e composto da minimo 3 e massimo 24 caratteri. Il nome può essere qualsiasi combinazione di lettere e numeri. È inoltre necessario specificare una posizione come "East US"
    	
		New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>

3. Impostare il nuovo account di archiviazione come predefinito.
    	
		Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>

4. Creare un nuovo contenitore.

    	New-AzureStorageContainer -Name <ContainerName> -Permission Off

 

## Passaggio 3: Caricare il file .vhd

Usare [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) per caricare il file VHD.

Nella finestra di Azure PowerShell usata nel passaggio precedente, digitare il comando seguente e sostituire le variabili tra &lsaquo; parentesi &rsaquo; con le proprie informazioni.

		Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>


## Passaggio 4: Aggiungere l'immagine all'elenco di immagini personalizzate

Utilizzare il cmdlet [Add-AzureVMImage]) (https://msdn.microsoft.com/library/mt589167.aspx) per aggiungere l'immagine all'elenco di immagini personalizzate.

		Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"


## Passaggi successivi

È ora possibile [creare una macchina virtuale personalizzata](virtual-machines-windows-classic-createportal.md) utilizzando l'immagine caricata.

<!---HONumber=AcomDC_0727_2016-->