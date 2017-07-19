---
title: Soluzioni di archiviazione per macchine virtuali Windows in Azure | Microsoft Docs
description: Informazioni sulle principali linee guida di progettazione e implementazione per la distribuzione di soluzioni di archiviazione nei servizi di infrastruttura di Azure.
documentationcenter: 
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ceb7d32-7a0d-4004-b701-c2bbcaff6b0b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: c0fabf155d4feb6d88ef7d7e087cc1654f44978b
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017


---
# <a name="azure-storage-infrastructure-guidelines-for-windows-vms"></a>Linee guida per l'infrastruttura di archiviazione di Azure per macchine virtuali Windows

[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

Questo articolo è incentrato sulla comprensione delle esigenze di archiviazione e sulle considerazioni di progettazione per il raggiungimento di livelli di prestazioni ottimali delle macchine virtuali (VM).

## <a name="implementation-guidelines-for-storage"></a>Linee guida di implementazione per l'archiviazione
Decisioni:

* Si intende usare Managed Disks di Azure o i dischi non gestiti?
* È necessario usare l'archiviazione standard o Premium per il carico di lavoro?
* È necessario lo striping del disco per creare dischi più grandi di 4TB?
* È necessario lo striping del disco per ottenere prestazioni di I/O ottimali per il carico di lavoro?
* Quali sono i set di account di archiviazione necessari per l’hosting dell’infrastruttura o del carico di lavoro IT?

Attività:

* Esaminare le richieste di I/O delle applicazioni da distribuire e pianificare il numero appropriato e il tipo di account di archiviazione.
* Creare il set di account di archiviazione usando la convenzione di denominazione scelta. È possibile usare Azure PowerShell o il portale.

## <a name="storage"></a>Archiviazione
Archiviazione di Azure è una parte fondamentale della distribuzione e della gestione di applicazioni e macchine virtuali. Archiviazione di Azure fornisce servizi per l'archiviazione di dati dei file, dati non strutturati e messaggi ed è anche parte dell'infrastruttura di supporto delle macchine virtuali.

[Managed Disks di Azure](../../storage/storage-managed-disks-overview.md) gestisce automaticamente le risorse di archiviazione. Con i dischi non gestiti è necessario creare account di archiviazione per contenere i dischi (file VHD) delle macchine virtuali di Azure. Per aumentare le prestazioni è necessario creare account di archiviazione aggiuntivi per non superare il limite di IOPS per l'archiviazione con uno dei dischi. Affidando a Managed Disks la gestione delle risorse di archiviazione, non è più necessario preoccuparsi dei limiti degli account di archiviazione, ad esempio di 20.000 IOPS per account. E non è più necessario copiare le immagini personalizzate, ovvero i file VHD, in più account di archiviazione. È possibile gestire tali immagini in una posizione centralizzata, un unico account di archiviazione per ogni area di Azure, e usarle per creare centinaia di macchine virtuali in una sottoscrizione. È consigliabile usare Managed Disks per le nuove distribuzioni.

Per il supporto delle macchine virtuali sono disponibili due tipi di archiviazione:

* Un account di archiviazione standard consente di accedere all'archivio BLOB (usato per archiviare i dischi delle macchine virtuali di Azure), all'archivio tabelle, all'archivio code e all'archivio file.
* [archiviazione Premium](../../storage/storage-premium-storage.md) offrono prestazioni elevate e supporto per dischi a bassa latenza per carichi di lavoro con uso intensivo di I/O, ad esempio istanze di SQL Server in un cluster AlwaysOn. Attualmente l'archiviazione Premium di Azure supporta solo i dischi di VM di Azure.

Azure consente di creare macchine virtuali con un disco del sistema operativo, un disco temporaneo e nessuno o più dischi dati facoltativi. Il disco del sistema operativo e i dischi dati sono BLOB di Azure, mentre il disco temporaneo è archiviato localmente sul nodo in cui si trova il computer. Prestare attenzione quando si progettano applicazioni in modo da usare solo questo disco temporaneo per i dati non persistenti, in quanto la macchina virtuale può eseguire la migrazione tra host durante un evento di manutenzione. Tutti i dati archiviati sul disco temporaneo andranno persi.

Per garantire che i dati rimangano protetti da eventi di manutenzione non pianificata o errori hardware, l'ambiente di Archiviazione di Azure sottostante offre durabilità ed elevata disponibilità. Quando si progetta l'ambiente di Archiviazione di Azure, è possibile scegliere di replicare l'archiviazione VM:

* in locale all'interno di un data center di Azure
* tra data center di Azure all'interno di una determinata area
* tra data center di Azure all'interno di aree diverse

Sono disponibili altre informazioni sulle [opzioni di replica per la disponibilità elevata](../../storage/storage-introduction.md#replication-for-durability-and-high-availability).

I dischi dati e del sistema operativo hanno una dimensione massima di 4TB. È possibile usare gli spazi di archiviazione in Windows Server 2012 o versione successiva per superare questo limite tramite il pooling dei dischi dati per la presentazione di volumi logici superiori a 4TB per la VM.

Quando si progettano le distribuzioni di Archiviazione di Azure, esistono alcuni limiti di scalabilità. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../../azure-subscription-service-limits.md#storage-limits). Vedere anche [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../../storage/storage-scalability-targets.md).

Quanto all'archiviazione delle applicazioni, è possibile archiviare dati oggetto non strutturati, ad esempio documenti, immagini, backup, dati di configurazione, log e così via usando l'archiviazione BLOB. Anziché la scrittura in un disco virtuale collegato alla macchina virtuale, l'applicazione può scrivere direttamente nell'archiviazione BLOB di Azure. L'archiviazione BLOB offre anche la possibilità di [livelli di archiviazione per accesso frequente e accesso sporadico](../../storage/storage-blob-storage-tiers.md) in base alle esigenze di disponibilità e ai vincoli di costo.

## <a name="striped-disks"></a>Dischi con striping
Oltre a consentire di creare dischi di dimensioni superiori a 4TB, in molti casi l'uso dello striping per i dischi dati contribuisce a migliorare le prestazioni, consentendo a più BLOB di supportare l'archiviazione per un singolo volume. Con lo striping, le operazioni I/O necessarie per scrivere e leggere dati da un singolo disco logico procedono in parallelo.

Azure impone limiti riguardo al numero di dischi dati e alla quantità della larghezza di banda disponibile a seconda delle dimensioni della macchina virtuale. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](sizes.md).

Se si usa lo striping del disco per i dischi dati di Azure, considerare le linee guida seguenti:

* Collegare i dischi dati delle dimensioni massime consentite per le dimensioni della macchina virtuale.
* Usare gli spazi di archiviazione.
* Evitare di usare le opzioni di caching del disco di dati di Azure (criterio di caching = Nessuno).

Per altre informazioni, vedere l’articolo sugli [spazi di archiviazione - progettazione della prestazione](http://social.technet.microsoft.com/wiki/contents/articles/15200.storage-spaces-designing-for-performance.aspx).

## <a name="multiple-storage-accounts"></a>Account di archiviazione multipli
Questa sezione non si applica a [Managed Disks di Azure](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), in quanto non si creano account di archiviazione separati. 

Quando si progetta l'ambiente di Archiviazione di Azure per i dischi non gestiti, è possibile usare più account di archiviazione in base al maggior numero di macchine virtuali distribuite. Questo approccio consente di distribuire i carichi di lavoro I/O attraverso l'infrastruttura di Archiviazione di Azure sottostante al fine di garantire un livello di prestazioni ottimale per le macchine virtuali e le applicazioni. Durante la progettazione delle applicazioni da distribuire, considerare i requisiti di I/O di ogni VM e bilanciare queste ultime fra gli account di Archiviazione di Azure. Cercare di evitare di raggruppare tutte le VM con valori di I/O elevati in uno o due account di archiviazione soltanto.

Per altre informazioni sulle funzionalità di I/O delle diverse opzioni di Archiviazione di Azure e sui valori massimi consigliati, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../../storage/storage-scalability-targets.md).

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]


