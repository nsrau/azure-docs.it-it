<properties
	pageTitle="Collegare un disco dati a una macchina virtuale Linux | Microsoft Azure"
	description="Come collegare un disco dati nuovo o esistente a una macchina virtuale Linux nel portale di Azure tramite il modello di distribuzione di Resource Manager."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="cynthn"/>

# Come collegare un disco dati a una macchina virtuale Linux nel portale di Azure

In questo articolo viene illustrato come collegare dischi nuovi o esistenti a una macchina virtuale Linux tramite il portale di Azure. È possibile anche [collegare un disco dati a una macchina virtuale Windows nel portale di Azure](virtual-machines-windows-attach-disk-portal.md). Prima di procedere, rivedere i suggerimenti seguenti:

- La dimensione della macchina virtuale controlla il numero di dischi dati che è possibile collegare. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](virtual-machines-linux-sizes.md).
- Per utilizzare l'archiviazione Premium, è necessario utilizzare una macchina virtuale della serie DS o serie GS. È possibile utilizzare dischi dagli account di archiviazione sia Premium che Standard con queste macchine virtuali. L’archiviazione Premium è disponibile solo in determinate aree geografiche. Per ulteriori informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../storage/storage-premium-storage.md).
- Dischi collegati a macchine virtuali sono effettivamente file con estensione .vhd in un account di archiviazione di Azure. Per informazioni dettagliate, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](virtual-machines-linux-about-disks-vhds.md).
- Per un nuovo disco, non è necessario crearlo prima perché Azure lo crea quando lo si collega.
- Per un disco esistente, il file .vhd deve essere disponibile in un account di archiviazione di Azure. È possibile usare un .vhd già esistente se non è collegato a un'altra macchina virtuale o caricare il proprio file .vhd nell'account di archiviazione.


[AZURE.INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## Passaggi successivi

Dopo aver aggiunto il disco, è necessario prepararlo per l'uso. Nel sistema operativo della macchina virtuale vedere la sezione relativa alla Procedura: Inizializzare un nuovo disco dati in Linux, in questo [articolo](virtual-machines-linux-classic-attach-disk.md#how-to-initialize-a-new-data-disk-in-linux).

<!---HONumber=AcomDC_0706_2016-->