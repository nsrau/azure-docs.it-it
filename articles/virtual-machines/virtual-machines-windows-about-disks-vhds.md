---
title: Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali Linux | Microsoft Docs
description: Informazioni di base sui dischi e sui dischi rigidi virtuali per le macchine virtuali in Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn

---
# <a name="about-disks-and-vhds-for-azure-virtual-machines"></a>Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali di Azure
Analogamente a qualsiasi altro computer, le macchine virtuali in Azure utilizzano i dischi come posizioni per archiviare un sistema operativo, le applicazioni e i dati. Tutte le macchine virtuali di Azure dispongono di almeno due dischi: un disco del sistema operativo Windows e un disco temporaneo. Il disco del sistema operativo viene creato da un'immagine e sia il disco del sistema operativo sia l'immagine sono dischi rigidi virtuali archiviati in un account di archiviazione di Azure. Anche le macchine virtuali possono disporre di uno o più dischi dati archiviati in dischi rigidi virtuali. Questo articolo è disponibile anche per le [macchine virtuali Linux](virtual-machines-linux-about-disks-vhds.md).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="operating-system-disk"></a>Disco del sistema operativo
Tutte le macchine virtuali dispongono di un disco del sistema operativo collegato. Per impostazione predefinita, è registrato come unità SATA ed etichettato come unità C:. Questo disco ha una capacità massima di 1023 GB. 

## <a name="temporary-disk"></a>Disco temporaneo
Il disco temporaneo viene creato automaticamente. Per impostazione predefinita il disco temporaneo viene etichettato come unità D: e usato per l'archiviazione di pagefile.sys. 

Le dimensioni del disco temporaneo variano in base alle dimensioni della macchina virtuale. Per maggiori informazioni, vedere [Dimensioni delle macchine virtuali di Windows](virtual-machines-windows-sizes.md).

> [!WARNING]
> Non archiviare sul disco temporaneo. Quest’ultimo fornisce l'archiviazione temporanea per applicazioni e processi e consente di archiviare solo dati come file di paging o di scambio. Per eseguire un nuovo mapping di questo disco su un'altra lettera di unità, vedere [Modifica della lettera di unità del disco temporaneo di Windows](virtual-machines-windows-classic-change-drive-letter.md).
> 
> 

Per altre informazioni sull'uso del disco temporaneo in Azure, vedere l'articolo relativo alle [unità temporanee nelle macchine virtuali di Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>Disco dati
Un disco dati è un disco rigido virtuale collegato a una macchina virtuale per archiviare i dati delle applicazioni o altri dati che è necessario conservare. I dischi dati vengono registrati come unità SCSI ed etichettati con una lettera di propria scelta.  Ciascun disco dati ha una capacità massima di 1023 GB. Le dimensioni della macchina virtuale determinano il numero di dischi dati è possibile collegare e il tipo di archiviazione che è possibile utilizzare per ospitare i dischi.

> [!NOTE]
> Per altre informazioni sulle capacità delle macchine virtuali, vedere [Dimensioni delle macchine virtuali di Windows](virtual-machines-windows-sizes.md).
> 
> 

Quando viene creata una macchina virtuale da un'immagine, Azure crea un disco del sistema operativo. Se si utilizza un'immagine che include dischi dati, anche Azure crea dischi dati quando viene creata la macchina virtuale. In caso contrario, aggiungere dischi dati dopo aver creato la macchina virtuale.

È possibile aggiungere dischi dati a una macchina virtuale in qualsiasi momento, **collegando** il disco alla macchina virtuale. È possibile utilizzare un disco rigido virtuale caricato o copiato sull'account di archiviazione o uno creato da Azure. Il collegamento di un disco dati associa il file del disco rigido virtuale alla macchina virtuale inserendo un "lease" sul disco rigido virtuale in modo che non possa essere eliminato dalla memoria se ancora collegato.

## <a name="about-vhds"></a>Informazioni sui dischi rigidi virtuali
I dischi rigidi virtuali utilizzati in Azure sono file con estensione .vhd archiviati come BLOB di pagine in un account di archiviazione Standard o Premium in Azure. Per altre informazioni sui BLOB, vedere [Informazioni su BLOB in blocchi e BLOB di pagine](https://msdn.microsoft.com/library/ee691964.aspx). Per altre informazioni sull'archiviazione Premium, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../storage/storage-premium-storage.md).

Azure supporta il formato di disco fisso VHD. In caso di formato fisso, il disco logico viene disposto in modo lineare all'interno del file, in modo che l'offset del disco X venga archiviato in corrispondenza dell'offset del BLOB X. In un piè di pagina alla fine del BLOB vengono descritte le proprietà del disco rigido virtuale. Con il formato fisso si verifica spesso uno spreco di spazio poiché nella maggior parte dei dischi sono presenti intervalli inutilizzati di grandi dimensioni. Tuttavia, in Azure i file con estensione vhd vengono archiviati in un formato di tipo sparse, pertanto si ottengono contemporaneamente i vantaggi sia dei dischi fissi sia di quelli dinamici. Per altre informazioni, vedere la [Guida introduttiva ai dischi rigidi virtuali](https://technet.microsoft.com/library/dd979539.aspx).

Tutti i file con estensione .vhd in Azure che si desidera utilizzare come origine per creare dischi o immagini sono di sola lettura. Quando viene creato un disco o un'immagine, Azure crea copie dei file con estensione .vhd. Queste copie possono essere lette oppure lette e scritte, a seconda di come viene utilizzato il disco rigido virtuale.

Quando viene creata una macchina virtuale da un'immagine, Azure crea un disco per la macchina virtuale che è una copia del file con estensione .vhd di origine. Per impedire eliminazioni accidentali, Azure inserisce un lease su qualsiasi file con estensione .vhd di origine utilizzato per creare un'immagine, un disco del sistema operativo o un disco dati.

Prima di poter eliminare un file con estensione .vhd di origine, sarà necessario rimuovere il lease eliminando il disco o l'immagine. È possibile eliminare la macchina virtuale, il disco del sistema operativo e il file con estensione vhd di origine in una sola volta eliminando la macchina virtuale e tutti i dischi associati. Tuttavia, l'eliminazione di un file con estensione vhd che rappresenta l'origine di un disco dati richiede l'esecuzione di diversi passaggi in un determinato ordine: scollegare innanzitutto il disco dalla macchina virtuale, quindi eliminare il disco e infine eliminare il file con estensione vhd.

> [!WARNING]
> Se si elimina un file con estensione .vdh di origine dalla memoria o l'account di archiviazione, Microsoft non può recuperare tali dati.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* [Collegare un disco](virtual-machines-windows-attach-disk-portal.md) per aggiungere altro spazio di archiviazione per la VM.
* [Caricare un'immagine di VM Windows in Azure](virtual-machines-windows-upload-image.md) da usare durante la creazione di una nuova VM.
* [Modificare la lettera di unità del disco temporaneo di Windows](virtual-machines-windows-classic-change-drive-letter.md) in modo che l'applicazione possa usare l'unità D: per i dati.

<!--HONumber=Oct16_HO2-->


