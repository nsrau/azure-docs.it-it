<properties
	pageTitle="Dischi e dischi rigidi virtuali | Microsoft Azure"
	description="Leggere le informazioni di base sui dischi e sui dischi rigidi virtuali per le macchine virtuali in Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/04/2015"
	ms.author="cynthn"/>

# Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali di Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



Analogamente a qualsiasi altro computer, le macchine virtuali in Azure utilizzano i dischi come posizioni per archiviare un sistema operativo, le applicazioni e i dati. Tutte le macchine virtuali di Azure dispongono di almeno due dischi: un disco del sistema operativo e un disco temporaneo. Il disco del sistema operativo viene creato da un'immagine e sia il disco del sistema operativo sia l'immagine sono effettivamente dischi rigidi virtuali archiviati in un account di archiviazione di Azure. Anche le macchine virtuali possono disporre di uno o più dischi dati archiviati in dischi rigidi virtuali.

## Disco del sistema operativo

Tutte le macchine virtuali dispongono di un disco del sistema operativo collegato. È registrato come unità SATA ed etichettato come unità C. Questo disco ha una capacità massima di 1023 GB. Quando in Azure viene creato un disco del sistema operativo, vengono create tre copie del disco per garantire una durabilità elevata. Inoltre, se la macchina virtuale viene configurata per la replica geografica, il disco rigido virtuale viene anche replicato in diversi siti distanti più di 643 km.

##Disco temporaneo

Il disco temporaneo viene creato automaticamente. Nelle macchine virtuali di Windows, il disco viene etichettato come unità D. Nelle macchine virtuali di Linux, il disco è in genere /dev/sdb e viene formattato e montato in /mnt/resource dall'agente Linux di Azure.

>[AZURE.WARNING] Non archiviare sul disco temporaneo. Quest’ultimo fornisce l'archiviazione temporanea per applicazioni e processi e consente di archiviare solo dati come file di paging o di scambio. Per eseguire un nuovo mapping di questo disco per una macchina virtuale di Windows, vedere [Modifica della lettera di unità del disco temporaneo di Windows](virtual-machines-windows-change-drive-letter.md).

## Disco dati

Un disco dati è un disco rigido virtuale collegato a una macchina virtuale per archiviare i dati delle applicazioni o altri dati che è necessario conservare. I dischi dati vengono registrati come unità SCSI ed etichettati con una lettera di propria scelta. Ciascun disco dati ha una capacità massima di 1023 GB. Le dimensioni della macchina virtuale determinano il numero di dischi dati è possibile collegare e il tipo di archiviazione che è possibile utilizzare per ospitare i dischi.

>[AZURE.NOTE] Per ulteriori dettagli sulle capacità delle macchine virtuali, vedere [Dimensioni per le macchine virtuali](virtual-machines-size-specs.md).

Quando viene creata una macchina virtuale da un'immagine, Azure crea un disco del sistema operativo. Se si utilizza un'immagine che include dischi dati, anche Azure crea dischi dati quando viene creata la macchina virtuale. (È possibile utilizzare un'immagine da Azure o un partner o un’immagine fornita.) In caso contrario, aggiungere dischi dati dopo aver creato la macchina virtuale.

È possibile aggiungere dischi dati a una macchina virtuale in qualsiasi momento, «collegando» il disco alla macchina virtuale. È possibile utilizzare un disco rigido virtuale caricato o copiato sull'account di archiviazione o uno creato da Azure. Il collegamento di un disco dati associa il file del disco rigido virtuale dell'account di archiviazione alla macchina virtuale, inserendo un «lease» sul disco rigido virtuale in modo che non possa essere eliminato dalla memoria se collegato a una macchina virtuale.

## Informazioni sui dischi rigidi virtuali

I dischi rigidi virtuali utilizzati in Azure sono file con estensione .vhd archiviati come BLOB di pagine in un account di archiviazione Standard o Premium in Azure. L’archiviazione Premium è disponibile solo in determinate aree geografiche. Per ulteriori dettagli sui BLOB di pagine, vedere [Informazioni sui BLOB in blocchi e sui BLOB di pagine](https://msdn.microsoft.com/library/ee691964.aspx). Per ulteriori dettagli sull’archiviazione Premium, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../storage/storage-premium-storage.md).

Esternamente ad Azure, per i dischi rigidi virtuali può essere usato un formato VHD o VHDX. I dischi possono inoltre essere fissi, espansi dinamicamente o dischi differenze. Azure supporta dischi fissi di formato VHD. In caso di formato fisso, il disco logico viene disposto in modo lineare all'interno del file, in modo che l'offset del disco X venga archiviato in corrispondenza dell'offset del BLOB X. In un piè di pagina alla fine del BLOB vengono descritte le proprietà del disco rigido virtuale. Spesso, con il formato fisso si verifica uno spreco di spazio poiché nella maggior parte dei dischi sono presenti intervalli inutilizzati di grandi dimensioni. Tuttavia, in Azure i file con estensione vhd vengono archiviati in un formato di tipo sparse, pertanto si ottengono contemporaneamente i vantaggi sia dei dischi fissi sia di quelli dinamici. Per ulteriori dettagli, vedere [Introduzione ai dischi rigidi virtuali](https://technet.microsoft.com/library/dd979539.aspx).

Tutti i file con estensione .vhd in Azure che si desidera utilizzare come origine per creare dischi o immagini sono di sola lettura. Quando viene creato un disco o un'immagine, Azure crea copie dei file con estensione .vhd. Queste copie possono essere lette oppure lette e scritte, a seconda di come viene utilizzato il disco rigido virtuale.

 Quando viene creata una macchina virtuale da un'immagine, Azure crea un disco per la macchina virtuale che è una copia del file con estensione .vhd di origine. Per impedire eliminazioni accidentali, Azure inserisce un lease su qualsiasi file con estensione .vhd di origine utilizzato per creare un'immagine, un disco del sistema operativo o un disco dati.

Prima di poter eliminare un file con estensione .vhd di origine, sarà necessario rimuovere il lease eliminando il disco o l'immagine. Per eliminare un file con estensione .vhd utilizzato da una macchina virtuale come disco del sistema operativo, è possibile eliminare la macchina virtuale, il disco del sistema operativo e il file con estensione .vhd di origine in una sola volta eliminando la macchina virtuale e tutti i dischi associati. Tuttavia, l'eliminazione di un file con estensione .vhd che rappresenta l’origine di un disco dati richiede diversi passaggi in un determinato ordine: lo scollegamento del disco dalla macchina virtuale, l'eliminazione del disco, quindi l’eliminazione del file con estensione .vhd.

>[AZURE.WARNING] Se si elimina un file con estensione .vdh di origine dalla memoria o l'account di archiviazione, Microsoft non può recuperare tali dati.

## Passaggi successivi

Macchine virtuali Linux:

-  [Collegare un disco e prepararlo per l'utilizzo](virtual-machines-linux-how-to-attach-disk.md)
-  [Acquisire una macchina virtuale di Linux](virtual-machines-linux-capture-image.md)
-  [Scollegare un disco](virtual-machines-linux-how-to-detach-disk.md)

Macchine virtuali Windows:

-  [Collegare un disco e prepararlo per l'utilizzo](storage-windows-attach-disk.md)
- [Modificare la lettera di unità del disco temporaneo di Windows](virtual-machines-windows-change-drive-letter.md)
-  [Acquisire una macchina virtuale Windows](virtual-machines-capture-image-windows-server.md)
-  [Scollegare un disco](storage-windows-detach-disk.md)

<!---HONumber=AcomDC_0302_2016-->