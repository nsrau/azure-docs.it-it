<properties
	pageTitle="Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali Linux | Microsoft Azure"
	description="Informazioni di base sui dischi e sui dischi rigidi virtuali per le macchine virtuali in Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="cynthn"/>

# Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali di Azure

Analogamente a qualsiasi altro computer, le macchine virtuali in Azure utilizzano i dischi come posizioni per archiviare un sistema operativo, le applicazioni e i dati. Tutte le macchine virtuali di Azure hanno almeno due dischi: un disco del sistema operativo Windows, in questo caso una macchina virtuale Windows, e un disco temporaneo. Il disco del sistema operativo viene creato da un'immagine e sia il disco del sistema operativo sia l'immagine sono effettivamente dischi rigidi virtuali archiviati in un account di archiviazione di Azure. Anche le macchine virtuali possono disporre di uno o più dischi dati archiviati in dischi rigidi virtuali. Questo articolo è disponibile anche per le [macchine virtuali Linux](virtual-machines-linux-about-disks-vhds.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-about-disks-vhds](../../includes/virtual-machines-common-about-disks-vhds.md)]

## Passaggi successivi
-  [Acquisire una macchina virtuale Windows](virtual-machines-windows-capture-image.md) per aumentare il numero di istanze di distribuzione delle macchine virtuali.
-  [Caricare un'immagine di macchina virtuale Windows in Azure](virtual-machines-windows-upload-image.md) da usare durante la creazione di una nuova macchina virtuale.
-  [Modificare la lettera di unità del disco temporaneo di Windows](virtual-machines-windows-classic-change-drive-letter.md) in modo che l'applicazione possa usare l'unità D: per i dati.

<!---HONumber=AcomDC_0622_2016-->