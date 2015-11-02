<properties
	pageTitle="Diversi modi per creare una macchina virtuale Windows"
	description="Vengono elencati i diversi modi per creare una macchina virtuale Windows e vengono forniti collegamenti alle istruzioni."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="index-page"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="09/15/2015"
	ms.author="cynthn"/>

# Diversi modi per creare una macchina virtuale Windows

Azure offre diversi modi per creare una macchina virtuale perché le macchine virtuali sono adatte a scopi e utenti diversi. Di conseguenza è necessario effettuare alcune scelte sulla macchina virtuale e su come crearla. In questo articolo viene fornito un riepilogo di queste opzioni e i collegamenti alle istruzioni.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Modelli di Gestione risorse di Azure sono stati introdotti recentemente per consentire di creare e gestire una macchina virtuale e le relative risorse diverse come una singola unità logica di distribuzione. Istruzioni per questo approccio, se disponibili, sono fornite di seguito. Per altre informazioni su Gestione risorse di Azure e su come gestire le risorse come singola unità, vedere [Panoramica][].

## Opzioni dello strumento

### Interfaccia utente grafica: il portale di Azure o il portale di anteprima di Azure

L'interfaccia utente grafica del portale di Azure è un modo semplice per provare a usare una macchina virtuale, soprattutto se si ha poca esperienza con Azure. Usare il portale di Azure o il portale di anteprima di Azure per creare la macchina virtuale:

[Creare una macchina virtuale che esegue Windows][]

### Shell dei comandi: Interfaccia della riga di comando di Azure o Azure PowerShell

Se si preferisce lavorare in una shell dei comandi, scegliere tra l'interfaccia della riga di comando (CLI) di Azure per gli utenti Mac e Linux o Azure PowerShell, che dispone di cmdlet Windows PowerShell per Azure e una console personalizzata.

Per l'interfaccia della riga di comando di Azure, vedere [Equivalente dei comandi di gestione delle risorse e di gestione dei servizi per le operazioni di macchina virtuale con CLI di Azure per Mac, Linux e Windows][]. Per usare un modello, vedere [Distribuire e gestire le macchine virtuali usando modelli di Gestione risorse di Azure e l'interfaccia della riga di comando di Azure][].

Per Azure PowerShell vedere [Uso di Azure PowerShell per creare e preconfigurare macchine virtuali Windows][]. Per usare un modello, vedere [Distribuire e gestire le macchine virtuali usando modelli di Gestione risorse di Azure e PowerShell][]. Per creare macchine virtuali nello stack di gestione dei servizi, vedere [Uso di Azure PowerShell per creare e preconfigurare macchine virtuali Windows][].

### Ambiente di sviluppo: Visual Studio

[Creare una macchina virtuale per un sito Web con Visual Studio][]

[Distribuire le risorse di Azure usando librerie di calcolo, rete e .NET di archiviazione][]

## Sistema operativo e opzioni dell'immagine

Scegliere un'immagine in base al sistema operativo che si desidera eseguire. Azure e i suoi partner offrono molte immagini, alcune delle quali includono strumenti e applicazioni. In alternativa, è possibile usare una delle immagini personalizzate.

### Immagini di Azure

Con queste istruzioni viene illustrato come usare un'immagine di Azure per creare una macchina virtuale personalizzata con opzioni per la rete, il bilanciamento del carico e così via. Vedere [Come creare una macchina virtuale personalizzata che esegue Windows][].

### Usare la propria immagine

Usare un'immagine basata su una macchina virtuale di Azure esistente *acquisendo* tale macchina virtuale oppure caricare un'immagine personalizzata, archiviata in un disco rigido virtuale (VHD):

- [Come acquisire una macchina virtuale Windows da usare come immagine.][]
- [Creazione e caricamento di un disco rigido virtuale con Windows Server in Azure][]

## Passaggi successivi

[Accedere alla macchina virtuale][]

[Collegare un disco dati][]

## Risorse aggiuntive
[Ambiente di test di configurazione di base][]

[Ambienti di test basati su cloud ibrido di Azure][]

<!-- LINKS -->
[Panoramica]: ../resource-group-overview.md

[Creare una macchina virtuale che esegue Windows]: virtual-machines-windows-tutorial.md

[Equivalente dei comandi di gestione delle risorse e di gestione dei servizi per le operazioni di macchina virtuale con CLI di Azure per Mac, Linux e Windows]: xplat-cli-azure-manage-vm-asm-arm.md
[Distribuire e gestire le macchine virtuali usando modelli di Gestione risorse di Azure e l'interfaccia della riga di comando di Azure]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Create and preconfigure a Windows virtual machine with Resource Manager and Azure PowerShell]: virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md
[Distribuire e gestire le macchine virtuali usando modelli di Gestione risorse di Azure e PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[Uso di Azure PowerShell per creare e preconfigurare macchine virtuali Windows]: virtual-machines-ps-create-preconfigure-windows-vms.md
[Come creare una macchina virtuale personalizzata che esegue Windows]: virtual-machines-windows-create-custom.md

[Come acquisire una macchina virtuale Windows da usare come immagine.]: virtual-machines-capture-image-windows-server.md

[Creazione e caricamento di un disco rigido virtuale con Windows Server in Azure]: virtual-machines-create-upload-vhd-windows-server.md


[Creare una macchina virtuale per un sito Web con Visual Studio]: virtual-machines-dotnet-create-visual-studio-powershell.md
[Distribuire le risorse di Azure usando librerie di calcolo, rete e .NET di archiviazione]: virtual-machines-arm-deployment.md

[Accedere alla macchina virtuale]: virtual-machines-log-on-windows-server.md

[Collegare un disco dati]: storage-windows-attach-disk.md

[Ambiente di test di configurazione di base]: virtual-machines-base-configuration-test-environment.md

[Ambienti di test basati su cloud ibrido di Azure]: virtual-machines-hybrid-cloud-test-environments.md

<!---HONumber=Oct15_HO4-->