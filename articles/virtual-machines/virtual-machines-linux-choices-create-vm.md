<properties
	pageTitle="Diversi modi per creare una macchina virtuale Linux | Microsoft Azure"
	description="Vengono elencati i diversi modi per creare una macchina virtuale Linux su Azure e vengono forniti collegamenti ad ulteriori istruzioni."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="01/20/2016"
	ms.author="dkshir"/>

# Diversi modi per creare una macchina virtuale Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Azure offre diversi modi per creare una VM con lo scopo di soddisfare una varietà di utenti ed esigenze. Questo articolo riepiloga le differenze e le opzioni disponibili per la creazione di macchine virtuali Linux.

I modelli di Gestione risorse di Azure possono essere usati anche per creare e gestire una macchina virtuale e le relative risorse come una singola unità logica di distribuzione. Sono incluse le istruzioni per questo approccio, se applicabili. Per altre informazioni su Gestione risorse di Azure e su come gestire le risorse come singola unità, vedere [Panoramica][].

## Opzioni dello strumento

### Interfaccia utente grafica: il portale di Azure classico o il portale di Azure

L'interfaccia utente grafica del portale è un modo semplice per provare a usare una macchina virtuale, soprattutto se si ha poca esperienza con Azure. Utilizzare il [portale di Azure classico](https://manage.windowsazure.com) oppure il [portale di Azure](https://portal.azure.com) per creare la VM. Per istruzioni generali, vedere [Creare una macchina virtuale personalizzata][] e selezionare un'immagine Linux dalla **Raccolta**. Si noti che il [portale di Azure classico](https://manage.windowsazure.com) crea macchine virtuali solo mediante il modello di distribuzione classica.

### Shell dei comandi: interfaccia della riga di comando di Azure o Azure PowerShell

Se si preferisce lavorare in una shell dei comandi, scegliere tra l'interfaccia della riga di comando (CLI) di Azure per utenti Mac, Linux e Windows o Azure PowerShell, che dispone di cmdlet Windows PowerShell per Azure e di una console personalizzata.

Per creare macchine virtuali nel modello di distribuzione di Gestione risorse tramite l'interfaccia della riga di comando di Azure, vedere l'articolo relativo alla [creazione di una macchina virtuale che esegue Linux][]. Seguire le schede o i selettori dell'articolo per leggere le istruzioni usando Azure PowerShell e i modelli.

Per il modello di distribuzione classico, vedere gli articoli relativi alla [creazione di una macchina virtuale Linux personalizzata tramite l'interfaccia della riga di comando di Azure](virtual-machines-linux-create-custom.md) e all'[uso di Azure PowerShell per creare e preconfigurare macchine virtuali basate su Linux][].


### Ambiente di sviluppo: Visual Studio

Visual Studio supporta anche la creazione di macchine virtuali di Azure. Per il modello di distribuzione classico, leggere [Creazione di una macchina virtuale per un'applicazione web con Visual Studio][]. Per il modello di distribuzione di Gestione risorse, leggere [Distribuire le risorse di Azure utilizzando librerie di calcolo, rete e archiviazione .NET][].


## Sistema operativo e opzioni dell'immagine

Scegliere un'immagine in base al sistema operativo che si desidera eseguire. Azure e i suoi partner offrono molte immagini, alcune delle quali includono strumenti e applicazioni. In alternativa, è possibile usare una delle immagini personalizzate.


### Immagini di Azure

In tutti gli articoli precedenti, è possibile usare facilmente un'immagine di Azure esistente per creare una macchina virtuale e personalizzarla per rete, bilanciamento del carico e così via. I portali offrono una raccolta o un elenco di immagini relativi alle immagini fornite da Azure. È possibile ottenere simili elenchi con la riga di comando. Ad esempio, nell'interfaccia della riga di comando di Azure, eseguire `azure vm image list` per ottenere un elenco di tutte le immagini disponibili per percorso ed entità di pubblicazione.


### Utilizzare la propria immagine

Usare un'immagine basata su una macchina virtuale di Azure esistente *acquisendo* tale VM oppure caricare un'immagine personalizzata, archiviata in un disco rigido virtuale (VHD). Per il modello di distribuzione classico, vedere l'articolo su [come acquisire una macchina virtuale Linux da usare come modello con l'interfaccia della riga di comando][] e [Creazione e caricamento di un disco rigido virtuale che contiene il sistema operativo Linux][]. Per il modello di distribuzione di Gestione risorse, vedere [Come acquisire una macchina virtuale Linux da usare come modello di Gestione risorse](virtual-machines-linux-capture-image-resource-manager.md).

## Passaggi successivi

[Accedere alla macchina virtuale][]

[Collegare un disco dati][]

## Risorse aggiuntive

[Ambiente di test di configurazione di base][]

[Ambienti di test basati su cloud ibrido di Azure][]

[Equivalente di gestione delle risorse e i comandi di Gestione servizio per le operazioni di macchina virtuale con CLI per Mac, Linux e Microsoft Azure][]

<!-- LINKS -->
[Panoramica]: ../resource-group-overview.md

[Create a Virtual Machine Running Windows]: virtual-machines-windows-tutorial.md
[Create a Virtual Machine Running Linux]: virtual-machines-linux-tutorial.md

[Equivalente di gestione delle risorse e i comandi di Gestione servizio per le operazioni di macchina virtuale con CLI per Mac, Linux e Microsoft Azure]: xplat-cli-azure-manage-vm-asm-arm.md
[Deploy and Manage Virtual Machines using Azure Resource Manager Templates and the Azure CLI]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Deploy and Manage Virtual Machines using Azure Resource Manager Templates and PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[uso di Azure PowerShell per creare e preconfigurare macchine virtuali basate su Linux]: virtual-machines-ps-create-preconfigure-linux-vms.md

[How to Create a Custom Virtual Machine Running Linux in Azure]: virtual-machines-linux-create-custom.md
[come acquisire una macchina virtuale Linux da usare come modello con l'interfaccia della riga di comando]: virtual-machines-linux-capture-image.md

[Creazione e caricamento di un disco rigido virtuale che contiene il sistema operativo Linux]: virtual-machines-linux-create-upload-vhd.md

[Creazione di una macchina virtuale per un'applicazione web con Visual Studio]: virtual-machines-dotnet-create-visual-studio-powershell.md
[Distribuire le risorse di Azure utilizzando librerie di calcolo, rete e archiviazione .NET]: virtual-machines-arm-deployment.md

[Accedere alla macchina virtuale]: virtual-machines-linux-how-to-log-on.md

[Collegare un disco dati]: virtual-machines-linux-how-to-attach-disk.md

[Ambiente di test di configurazione di base]: virtual-machines-base-configuration-test-environment.md
[Ambienti di test basati su cloud ibrido di Azure]: virtual-machines-hybrid-cloud-test-environments.md

[creazione di una macchina virtuale che esegue Linux]: virtual-machines-linux-tutorial.md
[Creare una macchina virtuale personalizzata]: virtual-machines-create-custom.md

<!---HONumber=AcomDC_0128_2016-->