<properties
	pageTitle="Diversi modi per creare una macchina virtuale Linux"
	description="Vengono elencati i diversi modi per creare una macchina virtuale Linux e vengono forniti collegamenti alle istruzioni."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""/>

<tags
ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="07/20/2015"
	ms.author="dkshir"/>

# Diversi modi per creare una macchina virtuale Linux

Azure offre diversi modi per creare una macchina virtuale perché le macchine virtuali sono adatte a scopi e utenti diversi. Ciò significa che è necessario effettuare alcune scelte sulla macchina virtuale e su come verrà creata. In questo articolo viene fornito un riepilogo di queste opzioni e i collegamenti alle istruzioni.

Modelli di Gestione risorse di Azure sono stati introdotti recentemente per consentire di creare e gestire una macchina virtuale e le relative risorse diverse come una singola unità logica di distribuzione. Istruzioni per questo approccio, se disponibili, sono fornite di seguito. Per altre informazioni su Gestione risorse di Azure e su come gestire le risorse come singola unità, vedere [Panoramica][].

## Opzioni dello strumento

### Interfaccia utente grafica: il portale di Azure o il portale di anteprima

L'interfaccia utente grafica del portale di Azure è un modo semplice per provare a utilizzare una macchina virtuale, soprattutto se si ha poca esperienza con Azure. Utilizzare il portale di Azure o il portale di anteprima di Azure per creare la macchina virtuale. Per istruzioni generali, vedere [Creazione di una macchina virtuale personalizzata][] e selezionare un'immagine Linux dalla **raccolta**.

### Shell dei comandi: interfaccia della riga di comando di Azure o Azure PowerShell

Se si preferisce lavorare in una shell dei comandi, scegliere tra l'interfaccia della riga di comando (CLI) di Azure per gli utenti Mac e Linux o Azure PowerShell, che dispone di cmdlet Windows PowerShell per Azure e una console personalizzata.

Per l'interfaccia della riga di comando di Azure, vedere [Creazione di una macchina virtuale che esegue Linux][]. Per usare un modello, vedere [Distribuire e gestire le macchine virtuali usando modelli di Gestione risorse di Azure e l'interfaccia della riga di comando di Azure][].

Per Azure PowerShell, vedere [Uso di Azure PowerShell per creare e preconfigurare macchine virtuali basate su Linux][]. Per utilizzare un modello, vedere [Distribuire e gestire le macchine virtuali utilizzando modelli di Gestione risorse di Azure e PowerShell][].

### Ambiente di sviluppo: Visual Studio

[Creazione di una macchina virtuale per un sito Web con Visual Studio][]

[Distribuire le risorse di Azure usando librerie di calcolo, rete e .NET di archiviazione][]

## Sistema operativo e opzioni dell'immagine

Scegliere un'immagine in base al sistema operativo che si desidera eseguire. Azure e i suoi partner offrono molte immagini, alcune delle quali includono strumenti e applicazioni. In alternativa, è possibile usare una delle immagini personalizzate.

### Immagini di Azure

Con queste istruzioni viene illustrato come utilizzare un'immagine di Azure per creare una macchina virtuale personalizzata con opzioni per la rete, il bilanciamento del carico e così via. [Come creare una macchina virtuale personalizzata su cui è in esecuzione Linux in Azure][]

### Utilizzare la propria immagine

Utilizzare un'immagine basata su una macchina virtuale di Azure esistente *acquisendo* tale macchina virtuale oppure caricare un'immagine personalizzata, archiviata in un disco rigido virtuale (VHD):

- [Come acquisire una macchina virtuale Linux da utilizzare come modello con l'interfaccia CLI][]
- [Creazione e caricamento di un disco rigido virtuale contenente il sistema operativo Linux][]

## Passaggi successivi

[Accedere alla macchina virtuale][]

[Collegare un disco dati][]

## Risorse aggiuntive
[Informazioni sulle impostazioni di configurazione delle macchine virtuali di Azure][]

[Ambiente di test di configurazione di base][]

[Ambienti di test basati su cloud ibrido di Azure][]

[Equivalente di gestione delle risorse e i comandi di Gestione servizio per le operazioni di macchina virtuale con CLI per Mac, Linux e Windows Azure][]

<!-- LINKS -->
[Panoramica]: ../resource-group-overview.md

[Create a Virtual Machine Running Windows]: virtual-machines-windows-tutorial.md
[Create a Virtual Machine Running Linux]: virtual-machines-linux-tutorial.md

[Equivalente di gestione delle risorse e i comandi di Gestione servizio per le operazioni di macchina virtuale con CLI per Mac, Linux e Windows Azure]: xplat-cli-azure-manage-vm-asm-arm.md
[Distribuire e gestire le macchine virtuali usando modelli di Gestione risorse di Azure e l'interfaccia della riga di comando di Azure]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Distribuire e gestire le macchine virtuali utilizzando modelli di Gestione risorse di Azure e PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[Uso di Azure PowerShell per creare e preconfigurare macchine virtuali basate su Linux]: virtual-machines-ps-create-preconfigure-linux-vms.md

[Come creare una macchina virtuale personalizzata su cui è in esecuzione Linux in Azure]: virtual-machines-linux-create-custom.md
[Come acquisire una macchina virtuale Linux da utilizzare come modello con l'interfaccia CLI]: virtual-machines-linux-capture-image.md

[Creazione e caricamento di un disco rigido virtuale contenente il sistema operativo Linux]: virtual-machines-linux-create-upload-vhd.md

[Creazione di una macchina virtuale per un sito Web con Visual Studio]: virtual-machines-dotnet-create-visual-studio-powershell.md
[Distribuire le risorse di Azure usando librerie di calcolo, rete e .NET di archiviazione]: virtual-machines-arm-deployment.md

[Accedere alla macchina virtuale]: virtual-machines-linux-how-to-log-on.md

[Collegare un disco dati]: virtual-machines-linux-how-to-attach-disk.md

[Informazioni sulle impostazioni di configurazione delle macchine virtuali di Azure]: http://msdn.microsoft.com/library/azure/dn763935.aspx
[Ambiente di test di configurazione di base]: virtual-machines-base-configuration-test-environment.md
[Ambienti di test basati su cloud ibrido di Azure]: virtual-machines-hybrid-cloud-test-environments.md

[Creazione di una macchina virtuale che esegue Linux]: virtual-machines-linux-tutorial.md
[Creazione di una macchina virtuale personalizzata]: virtual-machines-create-custom.md

<!---HONumber=July15_HO4-->