<properties
	pageTitle="Creare una macchina virtuale che esegue Windows in Azure"
	description="Imparare a creare la macchina virtuale (VM) di Windows nel portale Azure classico."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"
	tags="azure-classic-portal"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/27/2015"
	ms.author="kathydav"/>



# Creare una macchina virtuale che esegue Windows

> [AZURE.SELECTOR]
- [Azure portal](virtual-machines-windows-tutorial.md)
- [Azure classic portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-windows-vms.md)

Questa esercitazione mostra come è facile creare una macchina virtuale di Azure usando come esempio un'immagine di Windows Server, una delle numerose immagini disponibili tramite Azure. Tra queste sono disponibili immagini di sistemi operativi Windows, sistemi operativi basati su Linux e di applicazioni preinstallate. Le immagini che è possibile scegliere dipendono dal tipo di sottoscrizione di cui si dispone. Ad esempio, gli abbonati MSDN possono usufruire di immagini desktop.

È inoltre possibile creare macchine virtuali di Windows usando le [proprie immagini](../virtual-machines-create-upload-vhd-windows-server-classic-portal.md) come modelli. Per altre informazioni sulle macchine virtuali di Azure, vedere [Panoramica delle macchine virtuali di Azure](http://msdn.microsoft.com/library/azure/jj156143.aspx).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## <a id="createvirtualmachine"> </a>Come creare la macchina virtuale

Questa sezione illustra come usare l'opzione **Da raccolta** nel portale di gestione per la creazione di una macchina virtuale. Questa opzione offre un maggior numero di scelte di configurazione rispetto all'opzione **Creazione rapida**. Ad esempio, se si vuole aggiungere una macchina virtuale a una rete virtuale, sarà necessario usare l'opzione **Da raccolta**.

> [AZURE.NOTE]È anche possibile provare il [portale di anteprima di Azure](https://portal.azure.com), più avanzato e personalizzabile, per creare una macchina virtuale, automatizzare la distribuzione di modelli di applicazioni su più macchine virtuali, usare le funzionalità di diagnostica e monitoraggio delle macchine virtuali avanzate e altro ancora. Le opzioni di configurazione per le macchine virtuali disponibili nei due portali sono sostanzialmente simili ma non identiche.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

## Passaggi successivi

- Accedere alla macchina virtuale. Per informazioni, vedere [Come accedere a una macchina virtuale che esegue Windows Server](virtual-machines-log-on-windows-server.md).

- Collegare un disco per archiviare i dati. È possibile collegare sia dischi vuoti sia dischi contenenti dati. Per istruzioni, vedere il [collegare un'esercitazione su disco dati](storage-windows-attach-disk.md).

## Risorse aggiuntive

Per ulteriori informazioni su ciò che è possibile configurare per una macchina virtuale e quando a tale scopo, vedere [le impostazioni di configurazione sulla macchina virtuale di Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx).

<!--HONumber=52-->
 