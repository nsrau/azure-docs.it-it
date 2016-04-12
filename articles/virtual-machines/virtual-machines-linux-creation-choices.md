<properties
	pageTitle="Diversi modi per creare una macchina virtuale Linux | Microsoft Azure"
	description="Elenca i diversi modi per creare una macchina virtuale Linux in Azure e fornisce collegamenti ad altre istruzioni."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="03/07/2016"
	ms.author="dkshir"/>

# Diversi modi per creare una macchina virtuale Linux con Gestione risorse

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.

Azure offre diversi modi per creare una VM con lo scopo di soddisfare una varietà di utenti ed esigenze. Questo articolo riepiloga le differenze e le opzioni disponibili per la creazione di macchine virtuali Linux.

Modelli di Gestione risorse di Azure sono stati introdotti recentemente per consentire di creare e gestire una macchina virtuale e le relative risorse diverse come una singola unità logica di distribuzione. Istruzioni per questo approccio, se disponibili, sono fornite di seguito. Per altre informazioni su Gestione risorse di Azure e su come gestire le risorse come singola unità, vedere [Panoramica](../resource-group-overview.md).

## Opzioni dello strumento

### Interfaccia utente grafica: Nuovo portale di Azure

L'interfaccia utente grafica del [portale di Azure](https://portal.azure.com) è un modo semplice per provare a usare una macchina virtuale, soprattutto se si ha poca esperienza con Azure. Usare il portale di Azure per creare la VM:

* [Creare una macchina virtuale che esegue Linux tramite il portale di Azure](virtual-machines-linux-portal-create.md) 

### Shell dei comandi: interfaccia della riga di comando di Azure 

Se si preferisce utilizzare una shell dei comandi, usare l'interfaccia della riga di comando di Azure per gli utenti Mac, Linux e Windows.

Per l'interfaccia della riga di comando di Azure, vedere queste esercitazioni:

* [Creare una VM Linux dall'interfaccia della riga di comando per sviluppo e test](virtual-machines-linux-quick-create-cli.md) 

* [Creare una VM Linux protetta usando un modello di Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

## Sistema operativo e opzioni dell'immagine

Scegliere un'immagine in base al sistema operativo che si desidera eseguire. Azure e i suoi partner offrono molte immagini, alcune delle quali includono strumenti e applicazioni. In alternativa, è possibile usare una delle immagini personalizzate.

### Immagini di Azure

In tutti gli articoli precedenti, è possibile usare facilmente un'immagine di Azure esistente per creare una macchina virtuale e personalizzarla per rete, bilanciamento del carico e così via. Il portale offre Azure Marketplace per le immagini fornite da Azure. È possibile ottenere simili elenchi con la riga di comando. Ad esempio, nell'interfaccia della riga di comando di Azure eseguire `azure vm image list` per ottenere un elenco di tutte le immagini disponibili per posizione ed editore. Vedere [Esplorare e selezionare immagini delle macchine virtuali di Azure con l'interfaccia della riga di comando di Azure](virtual-machines-linux-cli-ps-findimage.md).

### Usare la propria immagine

Usare un'immagine basata su una macchina virtuale di Azure esistente mediante l'*acquisizione* di tale VM oppure caricare un'immagine personalizzata e archiviata in un disco rigido virtuale (VHD). Per altre informazioni, vedere:

* [Distribuzioni approvate per Azure](virtual-machines-linux-endorsed-distros.md)

* [Informazioni per distribuzioni non approvate](virtual-machines-linux-create-upload-generic.md)

* [Come acquisire una macchina virtuale Linux da usare come modello di Gestione risorse](virtual-machines-linux-capture-image.md).

## Passaggi successivi

* Provare a eseguire una delle esercitazioni per creare una VM Linux dal [portale](virtual-machines-linux-portal-create.md), con l[interfaccia della riga di comando](virtual-machines-linux-quick-create-cli.md) o tramite un [modello](virtual-machines-linux-cli-deploy-templates.md) di Azure Resource Manager.

* Dopo aver creato una VM Linux, è possibile [aggiungere un disco dati](virtual-machines-linux-add-disk.md) con facilità.

<!---HONumber=AcomDC_0323_2016-->