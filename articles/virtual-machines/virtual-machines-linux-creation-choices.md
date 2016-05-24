<properties
	pageTitle="Diversi modi per creare una macchina virtuale Linux | Microsoft Azure"
	description="Elenca i diversi modi per creare una macchina virtuale Linux in Azure e fornisce collegamenti ad altre istruzioni."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="04/12/2016"
	ms.author="iainfou"/>

# Diversi modi per creare una macchina virtuale Linux con Resource Manager

In Azure sono disponibili diversi modi per creare una macchina virtuale con il modello di distribuzione Resource Manager, adatti a obiettivi e utenti diversi. Questo articolo riepiloga le differenze e le opzioni disponibili per la creazione di macchine virtuali (VM) Linux.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.

## Opzioni dello strumento

### Shell dei comandi: interfaccia della riga di comando di Azure 

Dall'interfaccia della riga di comando usare l'interfaccia della riga di comando di Azure. Sono disponibili altre informazioni su [come installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md) tramite npm, contenitore Docker o script di installazione. Le esercitazioni seguenti offrono esempi relativi all'uso dell'interfaccia della riga di comando di Azure:

* [Creare una VM Linux dall'interfaccia della riga di comando di Azure per sviluppo e test](virtual-machines-linux-quick-create-cli.md) 

* [Creare una VM Linux protetta usando un modello di Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

* [Creare una VM Linux da zero con l'interfaccia della riga di comando di Azure](virtual-machines-linux-create-cli-complete.md)

### Interfaccia utente grafica: Nuovo portale di Azure

L'interfaccia utente grafica del [portale di Azure](https://portal.azure.com) è un modo semplice per provare a usare una macchina virtuale, soprattutto se si ha poca esperienza con Azure, perché non richiede installazioni nel sistema. Usare il portale di Azure per creare la VM:

* [Creare una macchina virtuale che esegue Linux tramite il portale di Azure](virtual-machines-linux-portal-create.md) 

## Sistema operativo e opzioni dell'immagine

Con entrambi i metodi, scegliere un'immagine in base al sistema operativo da eseguire. Azure e i relativi partner mettono a disposizione diverse immagini, alcune delle quali includono applicazioni e strumenti preinstallati. In alternativa, è possibile caricare una delle proprie immagini.

### Immagini di Azure

In tutti gli articoli indicati in precedenza è possibile usare con facilità un'immagine di Azure esistente per creare una macchina virtuale e personalizzarne la rete, il bilanciamento del carico e così via. Il portale offre Azure Marketplace per le immagini fornite da Azure. È possibile ottenere simili elenchi con la riga di comando. Ad esempio, nell'interfaccia della riga di comando di Azure eseguire `azure vm image list` per ottenere un elenco di tutte le immagini disponibili per posizione ed editore. Per esempi sull'esplorazione e l'uso di immagini disponibili, vedere [Trovare e selezionare immagini di macchine virtuali di Azure con l'interfaccia della riga di comando di Azure](virtual-machines-linux-cli-ps-findimage.md).

### Usare la propria immagine

Per personalizzazioni specifiche, è possibile usare un'immagine basata su una macchina virtuale di Azure esistente *acquisendo* tale VM oppure caricare un'immagine personalizzata e archiviata in un disco rigido virtuale (VHD). Per altre informazioni sulle distribuzioni supportate e su come usare immagini personalizzate, vedere gli articoli seguenti:

* [Distribuzioni approvate per Azure](virtual-machines-linux-endorsed-distros.md)

* [Informazioni per distribuzioni non approvate](virtual-machines-linux-create-upload-generic.md)

* [Come acquisire una macchina virtuale Linux da usare come modello di Resource Manager](virtual-machines-linux-capture-image.md).

## Passaggi successivi

* Provare a eseguire una delle esercitazioni per creare una VM Linux dal [portale](virtual-machines-linux-portal-create.md), con l'[interfaccia della riga di comando](virtual-machines-linux-quick-create-cli.md) o usando un [modello](virtual-machines-linux-cli-deploy-templates.md) di Azure Resource Manager.

* Dopo aver creato una VM Linux, è possibile [aggiungere un disco dati](virtual-machines-linux-add-disk.md) con facilità.

* Procedura rapida per [reimpostare una password o chiavi SSH e gestire gli utenti](virtual-machines-linux-using-vmaccess-extension.md).

<!---HONumber=AcomDC_0518_2016-->