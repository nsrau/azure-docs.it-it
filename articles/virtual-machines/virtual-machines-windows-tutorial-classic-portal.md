<properties
	pageTitle="Creare una macchina virtuale che esegue Windows in Azure"
	description="Creare una macchina virtuale di Windows (VM) nel portale di Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/05/2015"
	ms.author="kathydav"/>

# Creare una macchina virtuale con Windows nel portale di Azure

> [AZURE.SELECTOR]
- [Azure preview portal](virtual-machines-windows-tutorial.md)
- [Azure portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell - Resource management](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell - Service management](virtual-machines-ps-create-preconfigure-windows-vms.md)


Questa esercitazione illustra la facilità di creazione di un macchina virtuale di Azure (VM) nel Portale Azure. Si utilizzerà un'immagine di Windows Server come esempio, ma questa è solo una delle molte immagini disponibili in Azure. Si noti che le opzioni dell’immagine dipendono dalla sottoscrizione. Ad esempio, gli abbonati MSDN possono usufruire di immagini desktop.

È inoltre possibile creare VM usando le [proprie immagini](virtual-machines-create-upload-vhd-windows-server.md). Per ulteriori informazioni su questo e altri metodi, vedere [Diversi modi per creare una macchina virtuale Windows](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Video con la procedura dettagliata

Di seguito è riportata una procedura dettagliata di questa esercitazione.

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"></a>Come creare la macchina virtuale

Questa sezione illustra come usare l'opzione **Da Galleria** nel portale di gestione per la creazione di una macchina virtuale. Questa opzione offre un maggior numero di scelte di configurazione rispetto all'opzione **Creazione rapida**. Ad esempio, se si vuole aggiungere una macchina virtuale a una rete virtuale, sarà necessario usare l'opzione **Da raccolta**.

> [AZURE.NOTE]È anche possibile provare il [portale di anteprima di Azure](https://portal.azure.com), più avanzato e personalizzabile, per creare una macchina virtuale, automatizzare la distribuzione di modelli di applicazioni su più macchine virtuali, usare le funzionalità migliorate di diagnostica e monitoraggio delle macchine virtuali avanzate e altro ancora. Le opzioni di configurazione per le macchine virtuali disponibili nei due portali sono sostanzialmente simili ma non identiche.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

## Passaggi successivi

- Accedere alla macchina virtuale. Per informazioni, vedere [Come accedere a una macchina virtuale che esegue Windows Server](virtual-machines-log-on-windows-server.md).

- Collegare un disco per archiviare i dati. È possibile collegare sia dischi vuoti sia dischi contenenti dati. Per istruzioni, vedere [Collegare un'esercitazione su disco dati](storage-windows-attach-disk.md).

## Risorse aggiuntive

Per ulteriori informazioni su ciò che è possibile configurare per una macchina virtuale e quando a tale scopo, vedere [le impostazioni di configurazione sulla macchina virtuale di Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx).

<!---HONumber=August15_HO6-->