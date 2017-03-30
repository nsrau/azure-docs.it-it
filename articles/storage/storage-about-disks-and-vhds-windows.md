---
title: Informazioni sui dischi e sui dischi rigidi virtuali per le VM Windows di Microsoft Azure | Documentazione Microsoft
description: Informazioni di base sui dischi e sui dischi rigidi virtuali per le macchine virtuali in Azure.
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 0142c64d-5e8c-4d62-aa6f-06d6261f485a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: fced31b28b4b8e5835033243719e6eb87aa3f0d4
ms.lasthandoff: 03/24/2017


---
# <a name="about-disks-and-vhds-for-azure-windows-vms"></a>Informazioni sui dischi e sui dischi rigidi virtuali per le VM Windows di Azure
Analogamente a qualsiasi altro computer, le macchine virtuali in Azure utilizzano i dischi come posizioni per archiviare un sistema operativo, le applicazioni e i dati. Tutte le macchine virtuali di Azure dispongono di almeno due dischi: un disco del sistema operativo Windows e un disco temporaneo. Il disco del sistema operativo viene creato da un'immagine e sia il disco del sistema operativo sia l'immagine sono dischi rigidi virtuali archiviati in un account di archiviazione di Azure. Anche le macchine virtuali possono disporre di uno o più dischi dati archiviati in dischi rigidi virtuali. 

Questo articolo illustra i diversi usi dei dischi e descrive i diversi tipi di dischi che è possibile creare e usare. Questo articolo è disponibile anche per le [macchine virtuali Linux](storage-about-disks-and-vhds-linux.md).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Dischi usati dalle VM

Esaminiamo come i dischi vengono usati dalle VM.

### <a name="operating-system-disk"></a>Disco del sistema operativo
Tutte le macchine virtuali dispongono di un disco del sistema operativo collegato. Per impostazione predefinita, è registrato come unità SATA ed etichettato come unità C:. Questo disco ha una capacità massima di 1023 GB. 

### <a name="temporary-disk"></a>Disco temporaneo
Ogni VM contiene un disco temporaneo. Il disco temporaneo offre archiviazione a breve termine per applicazioni e processi ed è destinato solo all'archiviazione di dati come file di paging o di scambio. I dati presenti nel disco temporaneo potrebbero andare persi durante un [evento di manutenzione](../virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-planned-vs-unplanned-maintenance) o la [ridistribuzione di una VM](../virtual-machines/virtual-machines-windows-redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Durante un riavvio standard della macchina virtuale, i dati nell'unità temporanea vengono mantenuti.

Per impostazione predefinita il disco temporaneo viene etichettato come unità D: e usato per l'archiviazione di pagefile.sys. Per eseguire un nuovo mapping di questo disco su un'altra lettera di unità, vedere [Modifica della lettera di unità del disco temporaneo di Windows](../virtual-machines/virtual-machines-windows-change-drive-letter.md). Le dimensioni del disco temporaneo variano in base alle dimensioni della macchina virtuale. Per maggiori informazioni, vedere [Dimensioni delle macchine virtuali di Windows](../virtual-machines/virtual-machines-windows-sizes.md).

Per altre informazioni sull'uso del disco temporaneo in Azure, vedere l'articolo relativo alle [unità temporanee nelle macchine virtuali di Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)


### <a name="data-disk"></a>Disco dati
Un disco dati è un disco rigido virtuale collegato a una macchina virtuale per archiviare i dati delle applicazioni o altri dati che è necessario conservare. I dischi dati vengono registrati come unità SCSI ed etichettati con una lettera di propria scelta. Ciascun disco dati ha una capacità massima di 1023 GB. Le dimensioni della macchina virtuale determinano il numero di dischi dati è possibile collegare e il tipo di archiviazione che è possibile utilizzare per ospitare i dischi.

> [!NOTE]
> Per altre informazioni sulle capacità delle macchine virtuali, vedere [Dimensioni delle macchine virtuali di Windows](../virtual-machines/virtual-machines-windows-sizes.md).
> 

Quando viene creata una macchina virtuale da un'immagine, Azure crea un disco del sistema operativo. Se si utilizza un'immagine che include dischi dati, anche Azure crea dischi dati quando viene creata la macchina virtuale. In caso contrario, aggiungere dischi dati dopo aver creato la macchina virtuale.

È possibile aggiungere dischi dati a una macchina virtuale in qualsiasi momento, **collegando** il disco alla macchina virtuale. È possibile utilizzare un disco rigido virtuale caricato o copiato sull'account di archiviazione o uno creato da Azure. Il collegamento di un disco dati associa il file del disco rigido virtuale alla macchina virtuale inserendo un "lease" sul disco rigido virtuale in modo che non possa essere eliminato dalla memoria se ancora collegato.


[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="one-last-recommendation-use-trim-with-unmanaged-standard-disks"></a>Un ultimo suggerimento: usare TRIM con i dischi standard non gestiti 

Se si usano dischi standard non gestiti, è consigliabile abilitare TRIM. TRIM ignora i blocchi inutilizzati nel disco facendo in modo che venga addebitato solo lo spazio di archiviazione usato effettivamente. In questo modo è possibile risparmiare sui costi quando si creano file di grandi dimensioni per poi eliminarli. 

Per verificare l'impostazione TRIM, è possibile eseguire questo comando. Aprire un prompt dei comandi nella macchina virtuale Windows e digitare:

```
fsutil behavior query DisableDeleteNotify
```

Se il comando restituisce 0, l'impostazione TRIM è abilitata correttamente. Se restituisce 1, eseguire questo comando per abilitare TRIM:

```
fsutil behavior set DisableDeleteNotify 0
```

<!-- Might want to match next-steps from overview of managed disks -->
## <a name="next-steps"></a>Passaggi successivi
* [Collegare un disco](../virtual-machines/virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) per aggiungere altro spazio di archiviazione per la VM.
* [Caricare un'immagine di VM Windows in Azure](../virtual-machines/virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) da usare durante la creazione di una nuova VM.
* [Modificare la lettera di unità del disco temporaneo di Windows](../virtual-machines/virtual-machines-windows-change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) in modo che l'applicazione possa usare l'unità D: per i dati.


