<properties
	pageTitle="Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali Linux | Microsoft Azure"
	description="Informazioni di base sui dischi e sui dischi rigidi virtuali delle macchine virtuali in Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="cynthn"/>

# Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali di Azure

Analogamente a qualsiasi altro computer, le macchine virtuali in Azure utilizzano i dischi come posizioni per archiviare un sistema operativo, le applicazioni e i dati. Tutte le macchine virtuali di Azure hanno almeno due dischi: un disco del sistema operativo Linux, in questo caso una macchina virtuale Linux, e un disco temporaneo. Il disco del sistema operativo viene creato da un'immagine e sia il disco del sistema operativo sia l'immagine sono effettivamente dischi rigidi virtuali archiviati in un account di archiviazione di Azure. Anche le macchine virtuali possono disporre di uno o più dischi dati archiviati in dischi rigidi virtuali. Questo articolo è disponibile anche per le [macchine virtuali Windows](virtual-machines-windows-about-disks-vhds.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-about-disks-vhds](../../includes/virtual-machines-common-about-disks-vhds.md)]

## Risoluzione dei problemi
[AZURE.INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]

## Passaggi successivi

-  [Collegare un disco](virtual-machines-linux-attach-disk-portal.md) per aggiungere altro spazio di archiviazione per la VM.
-  [Configurare RAID software](virtual-machines-linux-configure-raid.md) per la ridondanza.
-  [Acquisire una macchina virtuale Linux](virtual-machines-linux-classic-capture-image.md) per poter distribuire rapidamente macchine virtuali aggiuntive.

<!---HONumber=AcomDC_0803_2016-->