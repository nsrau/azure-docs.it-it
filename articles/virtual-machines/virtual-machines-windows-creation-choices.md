<properties
	pageTitle="Diversi modi per creare una macchina virtuale Windows | Microsoft Azure"
	description="Elenca i diversi modi per creare una macchina virtuale Windows con Gestione risorse."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="03/11/2016"
	ms.author="cynthn"/>

# Diversi modi per creare una macchina virtuale Windows con Gestione risorse

Azure offre diversi modi per creare una macchina virtuale perché le macchine virtuali sono adatte a scopi e utenti diversi. Di conseguenza è necessario effettuare alcune scelte sulla macchina virtuale e su come crearla. In questo articolo viene fornito un riepilogo di queste opzioni e i collegamenti alle istruzioni.

## Portale di Azure

L’utilizzo del portale di Azure è un modo semplice per provare a usare una macchina virtuale, soprattutto se si ha poca esperienza con Azure.

[Creare una macchina virtuale con Windows tramite il portale](virtual-machines-windows-hero-tutorial.md)

## Modello

Le macchine virtuali richiedono una combinazione di risorse (come set di disponibilità e account di archiviazione). Anziché distribuire e gestire separatamente ogni risorsa, è possibile creare un modello Azure Resource Manager che distribuisce e fornisce tutte le risorse in un'unica operazione coordinata.

- [Creare una macchina virtuale Windows con un modello di Gestione risorse](virtual-machines-windows-ps-template.md)


## Azure PowerShell

Se si preferisce lavorare in una shell dei comandi, è possibile utilizzare Azure PowerShell.

- [Creare una VM Windows tramite PowerShell](virtual-machines-windows-ps-create.md)


## Visual Studio

Usare Visual Studio per creare, gestire e distribuire VM con gli strumenti di Azure per Visual Studio e Azure SDK.

[Strumenti di Azure per Visual Studio](https://www.visualstudio.com/features/azure-tools-vs)

<!---HONumber=AcomDC_0622_2016-->