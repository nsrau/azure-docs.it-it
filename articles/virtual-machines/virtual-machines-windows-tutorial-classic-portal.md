<properties
	pageTitle="Creare una VM che esegue Windows nel portale classico | Microsoft Azure"
	description="Creare una macchina virtuale di Windows nel portale di Azure classico."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="cynthn"/>

# Creare una macchina virtuale con Windows nel portale di Azure classico

> [AZURE.SELECTOR]
- [Azure portal](virtual-machines-windows-tutorial.md)
- [Azure classic portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell: Resource Manager deployment](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell: Classic deployment](virtual-machines-ps-create-preconfigure-windows-vms.md)

<!-- HHTML comment in to break between the selector and the note in the include below-->

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager deployment model](virtual-machines-windows-tutorial.md).

Questa esercitazione illustra la facilità di creazione di una macchina virtuale (VM) di Azure che esegue Windows nel portale di Azure classico. Si utilizzerà un'immagine di Windows Server come esempio, ma questa è solo una delle molte immagini disponibili in Azure. Si noti che le opzioni dell’immagine dipendono dalla sottoscrizione. Ad esempio, i sottoscrittori MSDN possono usufruire di immagini desktop Windows.

È inoltre possibile creare VM usando le [proprie immagini](virtual-machines-create-upload-vhd-windows-server.md). Per altre informazioni su questo e altri metodi, vedere [Diversi modi per creare una macchina virtuale Windows](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Procedura dettagliata video

Di seguito è riportata una procedura dettagliata di questa esercitazione.

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"></a>Come creare la macchina virtuale

Questa sezione illustra come usare l'opzione **Da raccolta** nel portale di gestione per la creazione di una macchina virtuale. Questa opzione offre un maggior numero di scelte di configurazione rispetto all'opzione **Creazione rapida**. Ad esempio, se si vuole aggiungere una macchina virtuale a una rete virtuale, sarà necessario usare l'opzione **Da raccolta**.

> [AZURE.NOTE]È anche possibile provare il portale di Azure più avanzato e personalizzabile per creare una macchina virtuale, usare le funzionalità migliorate di diagnostica e monitoraggio, usare l'archiviazione Premium e altro ancora. Le opzioni disponibili per la configurazione di una macchina virtuale nei due portali sono sostanzialmente simili ma non identiche. Ad esempio, usare il portale di Azure per configurare una macchina virtuale con l'archiviazione Premium.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## Passaggi successivi

- Accedere alla macchina virtuale. Per istruzioni, vedere l'articolo su [come accedere a una macchina virtuale che esegue Windows Server](virtual-machines-log-on-windows-server.md).

- Collegare un disco per archiviare i dati. È possibile collegare sia dischi vuoti sia dischi contenenti dati. Per istruzioni, vedere [Collegare un disco dati da una macchina virtuale di Windows creata con il modello di distribuzione classico](storage-windows-attach-disk.md).

<!---HONumber=AcomDC_0114_2016-->