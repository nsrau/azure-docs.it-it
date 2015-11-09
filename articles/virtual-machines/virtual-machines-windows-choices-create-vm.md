<properties
	pageTitle="Diversi modi per creare una macchina virtuale Windows | Microsoft Azure"
	description="Elenca i diversi modi per creare una macchina virtuale Windows con Gestione risorse."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="index-page"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="10/22/2015"
	ms.author="cynthn"/>

# Diversi modi per creare una macchina virtuale Windows con Gestione risorse

Azure offre diversi modi per creare una macchina virtuale perché le macchine virtuali sono adatte a scopi e utenti diversi. Di conseguenza è necessario effettuare alcune scelte sulla macchina virtuale e su come crearla. In questo articolo viene fornito un riepilogo di queste opzioni e i collegamenti alle istruzioni.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.

Modelli di Gestione risorse di Azure sono stati introdotti recentemente per consentire di creare e gestire una macchina virtuale e le relative risorse diverse come una singola unità logica di distribuzione. Istruzioni per questo approccio, se disponibili, sono fornite di seguito. Per altre informazioni su Gestione risorse di Azure e su come gestire le risorse come singola unità, vedere [Panoramica][].

## Opzioni dello strumento

### GUI: portale di anteprima di Azure

L'interfaccia utente grafica del portale di Azure è un modo semplice per provare a usare una macchina virtuale, soprattutto se si ha poca esperienza con Azure. Usare il portale di anteprima di Azure per creare la macchina virtuale:

[Creare una macchina virtuale che esegue Windows][]

### Shell dei comandi: Interfaccia della riga di comando di Azure o Azure PowerShell

Se si preferisce lavorare in una shell dei comandi, scegliere tra l'interfaccia della riga di comando (CLI) di Azure per gli utenti Mac e Linux o Azure PowerShell, che dispone di cmdlet per Azure e di una console personalizzata.

Per l’interfaccia della riga di comando di Azure, vedere:

- [Equivalente dei comandi di gestione delle risorse e di gestione dei servizi per le operazioni di macchina virtuale con CLI di Azure per Mac, Linux e Windows][].
- [Distribuzione e gestione delle macchine virtuali utilizzando i modelli di Gestione risorse di Azure e l'interfaccia della riga di comando di Azure][]

Per Azure PowerShell, vedere:

- [Creare una macchina virtuale di Windows PowerShell con Gestione risorse][]
- [Creare e preconfigurare una macchina virtuale Windows con Gestione risorse e Azure PowerShell][]
- [Distribuire e gestire le macchine virtuali utilizzando modelli di Gestione risorse di Azure e PowerShell][]
- [Creare una macchina virtuale Windows con un modello di Gestione risorse e PowerShell][]

### Ambiente di sviluppo: Visual Studio

[Distribuire le risorse di Azure usando librerie di calcolo, rete e .NET di archiviazione][]

## Sistema operativo e opzioni dell'immagine

Scegliere un'immagine in base al sistema operativo che si desidera eseguire. Azure e i suoi partner offrono molte immagini, alcune delle quali includono strumenti e applicazioni. In alternativa, è possibile usare una delle immagini personalizzate. Trovare l'immagine della piattaforma necessaria per l'applicazione utilizzando le informazioni in: [Esplorazione e selezione di immagini delle macchine virtuali di Azure con Windows PowerShell e l'interfaccia della riga di comando di Azure][].

<!-- LINKS -->
[Panoramica]: ../resource-group-overview.md

[Creare una macchina virtuale che esegue Windows]: virtual-machines-windows-tutorial.md

[Equivalente dei comandi di gestione delle risorse e di gestione dei servizi per le operazioni di macchina virtuale con CLI di Azure per Mac, Linux e Windows]: xplat-cli-azure-manage-vm-asm-arm.md
[Distribuzione e gestione delle macchine virtuali utilizzando i modelli di Gestione risorse di Azure e l'interfaccia della riga di comando di Azure]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Creare e preconfigurare una macchina virtuale Windows con Gestione risorse e Azure PowerShell]: virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md
[Distribuire e gestire le macchine virtuali utilizzando modelli di Gestione risorse di Azure e PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[Creare una macchina virtuale di Windows PowerShell con Gestione risorse]: virtual-machines-create-windows-powershell-resource-manager.md
[Creare una macchina virtuale Windows con un modello di Gestione risorse e PowerShell]: virtual-machines-create-windows-powershell-resource-manager-template-simple.md


[Esplorazione e selezione di immagini delle macchine virtuali di Azure con Windows PowerShell e l'interfaccia della riga di comando di Azure]: resource-groups-vm-searching.md
[Distribuire le risorse di Azure usando librerie di calcolo, rete e .NET di archiviazione]: virtual-machines-arm-deployment.md

[Sign in to the virtual machine]: virtual-machines-log-on-windows-server.md

[Base configuration test environment]: virtual-machines-base-configuration-test-environment.md

[Azure hybrid cloud test environments]: virtual-machines-hybrid-cloud-test-environments.md

<!---HONumber=Nov15_HO1-->