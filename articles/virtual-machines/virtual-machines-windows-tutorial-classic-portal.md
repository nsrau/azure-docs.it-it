<properties
	pageTitle="Creare una macchina virtuale che esegue Windows in Azure"
	description="Creare una macchina virtuale di Windows nel portale di Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="kathydav"/>

# Creare una macchina virtuale con Windows nel portale di Azure

> [AZURE.SELECTOR]
- [Azure preview portal](virtual-machines-windows-tutorial.md)
- [Azure portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell - Resource management](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell - Service management](virtual-machines-ps-create-preconfigure-windows-vms.md)


Questa esercitazione illustra la facilità di creazione di un macchina virtuale di Azure (VM) nel Portale Azure. Si utilizzerà un'immagine di Windows Server come esempio, ma questa è solo una delle molte immagini disponibili in Azure. Si noti che le opzioni dell’immagine dipendono dalla sottoscrizione. Ad esempio, gli abbonati MSDN possono usufruire di immagini desktop.

È inoltre possibile creare VM usando le [proprie immagini](virtual-machines-create-upload-vhd-windows-server.md). Per altre informazioni su questo e altri metodi, vedere [Diversi modi per creare una macchina virtuale Windows](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Video con la procedura dettagliata

Di seguito è riportata una procedura dettagliata di questa esercitazione.

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"></a>Come creare la macchina virtuale

Questa sezione illustra come usare l'opzione **Da Galleria** nel portale di gestione per la creazione di una macchina virtuale. Questa opzione offre un maggior numero di scelte di configurazione rispetto all'opzione **Creazione rapida**. Ad esempio, se si vuole aggiungere una macchina virtuale a una rete virtuale, sarà necessario usare l'opzione **Da raccolta**.

> [AZURE.NOTE]È anche possibile provare il [portale di anteprima di Azure](https://portal.azure.com), più avanzato e personalizzabile, per creare una macchina virtuale, usare le funzionalità migliorate di diagnostica e monitoraggio, usare l'archiviazione Premium e altro ancora. Le opzioni disponibili per la configurazione di una macchina virtuale nei due portali sono sostanzialmente simili ma non identiche. Ad esempio, usare il portale di anteprima per configurare una macchina virtuale con l'archiviazione Premium.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

## Passaggi successivi

- Accedere alla macchina virtuale. Per istruzioni, vedere [Come accedere a una macchina virtuale che esegue Windows Server](virtual-machines-log-on-windows-server.md).

- Collegare un disco per archiviare i dati. È possibile collegare sia dischi vuoti sia dischi contenenti dati. Per istruzioni, vedere [Collegare un'esercitazione su disco dati](storage-windows-attach-disk.md).

## Risorse aggiuntive

Per altre informazioni su ciò che è possibile configurare per una macchina virtuale e quando farlo, vedere [Informazioni sulle impostazioni di configurazione delle macchine virtuali di Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx).

<!---HONumber=August15_HO7-->