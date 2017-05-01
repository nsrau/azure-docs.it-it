---
title: Archiviazione Standard basata su dischi rigidi conveniente e dischi delle macchine virtuali di Azure | Microsoft Docs
description: Informazioni sul servizio economicamente conveniente Archiviazione Standard e sui dischi gestiti e non gestiti delle macchine virtuali.
services: storage
documentationcenter: 
author: yuemlu
manager: aungoo-msft
editor: tysonn
ms.assetid: e2a20625-6224-4187-8401-abadc8f1de91
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2017
ms.author: yuemlu
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 37a22be9fba7b245b2c1ea3ca6e495601d63b611
ms.lasthandoff: 04/25/2017


---
# <a name="cost-effective-standard-storage-and-unmanaged-and-managed-azure-vm-disks"></a>Archiviazione Standard conveniente e dischi gestiti e non gestiti delle macchine virtuali di Azure

Archiviazione Standard di Azure offre un supporto dei dischi affidabile e a basso costo per le VM che eseguono carichi di lavoro non sensibili alla latenza. Supporta anche BLOB, tabelle, code e file. Con Archiviazione Standard, i dati vengono archiviati in unità disco rigido. Per le VM, è possibile usare dischi di Archiviazione Standard per scenari di sviluppo/test e carichi di lavoro meno critici e dischi di Archiviazione Premium per applicazioni di produzione cruciali. Archiviazione Standard è disponibile in tutte le aree di Azure. 

Questo articolo è incentrato sull'uso di Archiviazione Standard per i dischi delle macchine virtuali. Per altre informazioni sull'uso dell'archiviazione con BLOB, tabelle, code e file, vedere [Introduzione ad Archiviazione](storage-introduction.md).

## <a name="disk-types"></a>Tipi di disco

È possibile creare dischi Standard per le VM di Azure in due modi.

**Dischi non gestiti**: si tratta del metodo originale con cui vengono gestiti gli account di archiviazione usati per archiviare i file VHD che corrispondono ai dischi delle macchine virtuali. I file VHD vengono archiviati come BLOB di pagine in account di archiviazione. I dischi non gestiti possono essere collegati a VM di Azure di qualsiasi dimensione, incluse le VM che usano principalmente Archiviazione Premium come le serie DSv2 e GS. Le VM di Azure supportano il collegamento di diversi dischi Standard, fino a un massimo di 64 TB di spazio di archiviazione per VM.

[**Azure Managed Disks**](storage-managed-disks-overview.md): questa funzionalità gestisce gli account di archiviazione usati per i dischi delle macchine virtuali. Specificando il tipo, Premium o Standard, e le dimensioni del disco necessarie, Azure crea e gestisce automaticamente il disco. Azure gestisce anche l'inserimento dei dischi in più account di archiviazione per rimanere entro i limiti di scalabilità degli account di archiviazione.

Anche se entrambi i tipi di disco sono disponibili, è consigliabile usare Managed Disks per sfruttarne appieno le varie funzionalità.

Per iniziare a usare gratuitamente Archiviazione Standard di Azure, visitare [questa pagina](https://azure.microsoft.com/pricing/free-trial/). 

Per informazioni su come creare una VM con Managed Disks, vedere uno degli articoli seguenti.

* [Creare una VM con Resource Manager e PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md)
* [Creare una VM Linux usando l'interfaccia della riga di comando di Azure 2.0](../virtual-machines/linux/quick-create-cli.md)

## <a name="standard-storage-features"></a>Funzionalità di Archiviazione Standard 

Di seguito sono illustrate alcune delle funzionalità di Archiviazione Standard. Per altri dettagli, vedere [Introduzione ad Archiviazione di Azure](storage-introduction.md).

**Archiviazione Standard**: Archiviazione Standard di Azure supporta dischi, BLOB, file, tabelle e code di Azure. Per usare i servizi Archiviazione Standard, per prima cosa [creare un account di archiviazione di Azure](storage-create-storage-account.md#create-a-storage-account).

**Dischi di Archiviazione Standard**: i dischi di Archiviazione Standard possono essere collegati a tutte le VM di Azure, incluse le VM di serie di dimensioni usate con Archiviazione Premium come DSv2 e GS. Un disco di Archiviazione Standard può essere collegato a una sola VM. È tuttavia possibile collegare a una VM uno o più dischi di questo tipo, fino al numero massimo di dischi definito per la specifica dimensione di VM. Nella sezione seguente sugli obiettivi di scalabilità e prestazioni di Archiviazione Standard verranno descritte in dettaglio le specifiche. 

**BLOB di pagine Standard**: i BLOB di pagine Standard vengono usati per contenere dischi persistenti per le VM e sono anche accessibili direttamente tramite REST come altri tipi di BLOB di Azure. I [BLOB di pagine](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) sono una raccolta di pagine di 512 byte ottimizzata per operazioni di lettura e scrittura casuali. 

**Replica di Archiviazione**: nella maggior parte delle aree, i dati in un account di archiviazione Standard possono essere replicati in locale oppure essere sottoposti a replica geografica tra più data center. Sono disponibili i quattro tipi di replica seguenti: archiviazione con ridondanza locale (LRS), archiviazione con ridondanza della zona (ZRS), archiviazione con ridondanza geografica (GRS) e archiviazione con ridondanza geografica e accesso in lettura (RA-GRS). In Archiviazione Standard, Managed Disks supporta attualmente solo l'archiviazione con ridondanza locale. Per altre informazioni, vedere [Replica di Archiviazione](storage-redundancy.md).

## <a name="scalability-and-performance-targets"></a>Obiettivi di scalabilità e prestazioni

Questa sezione descrive gli obiettivi di scalabilità e prestazioni da considerare quando si usa Archiviazione Standard.

### <a name="account-limits--does-not-apply-to-managed-disks"></a>Limiti dell'account (non applicati ai dischi gestiti)

| **Risorsa** | **Limite predefinito** |
|--------------|-------------------|
| TB per account di archiviazione  | 500 TB |
| Traffico in ingresso massimo<sup>1</sup> per account di archiviazione (aree degli Stati Uniti) | 10 Gbps con archiviazione GRS/ZRS abilitata, 20 Gbps per LRS |
| Traffico in uscita massimo<sup>1</sup> per account di archiviazione (aree degli Stati Uniti) | 20 Gbps con archiviazione RA-GRS/GRS/ZRS abilitata, 30 Gbps per LRS |
| Traffico in ingresso massimo<sup>1</sup> per account di archiviazione (aree europee e asiatiche) | 5 Gbps con archiviazione GRS/ZRS abilitata, 10 Gbps per LRS |
| Traffico in uscita massimo<sup>1</sup> per account di archiviazione (aree europee e asiatiche) | 10 Gbps con archiviazione RA-GRS/GRS/ZRS abilitata, 15 Gbps per LRS |
| Frequenza di richiesta totale (presupponendo oggetti con dimensione di 1 KB) per account di archiviazione | Fino a 20.000 IOPS, entità al secondo o messaggi al secondo |

<sup>1</sup>Il traffico in ingresso indica tutti i dati (richieste) inviati a un account di archiviazione. Il traffico in uscita indica tutti i dati (risposte) provenienti da un account di archiviazione.

Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage-scalability-targets.md).

Se le esigenze dell'applicazione superano gli obiettivi di scalabilità di un singolo account di archiviazione, compilare l'applicazione in modo da usare più account di archiviazione e partizionare i dati tra tali account. In alternativa, è possibile usare Azure Managed Disks, con cui il partizionamento e il posizionamento dei dati verranno gestiti automaticamente da Azure.

### <a name="standard-disks-limits"></a>Limiti dei dischi Standard

A differenza dei dischi Premium, per i dischi Standard non viene effettuato il provisioning delle operazioni di I/O al secondo e della velocità effettiva (larghezza di banda). Le prestazioni dei dischi Standard variano in base alla dimensione della VM a cui il disco è collegato e non dipendono dalle dimensioni del disco. La tabella seguente elenca i limiti di prestazioni che si può prevedere di raggiungere.

**Limiti dei dischi Standard (gestiti e non gestiti)**

| **Livello VM**            | **VM livello Basic** | **VM livello Standard** |
|------------------------|-------------------|----------------------|
| Dimensioni massime disco          | 1023 GB           | 1023 GB              |
| Numero massimo di operazioni di I/O al secondo da 8 KB per disco | Fino a 300         | Fino a 500            |
| Larghezza di banda massima per disco | Fino a 60 MB/s     | Fino a 60 MB/s        |

Se il carico di lavoro richiede un supporto dei dischi a bassa latenza e prestazioni elevate, è consigliabile valutare la possibilità di usare Archiviazione Premium. Per informazioni sui vantaggi aggiuntivi di Archiviazione Premium, vedere [Archiviazione Premium a prestazioni elevate e dischi delle macchine virtuali di Azure](storage-premium-storage.md). 

## <a name="snapshots-and-copy-blob"></a>Snapshot e copia di BLOB

Per il servizio Archiviazione, il file VHD è un BLOB di pagine. È possibile creare snapshot dei BLOB di pagine e copiarli in un'altra posizione, ad esempio un diverso account di archiviazione.

### <a name="unmanaged-disks"></a>Dischi non gestiti

È possibile creare [snapshot incrementali](storage-incremental-snapshots.md) dei dischi Standard non gestiti nello stesso modo in cui si usano gli snapshot con Archiviazione Standard. Se il disco di origine si trova in un account di archiviazione con ridondanza locale, è consigliabile creare gli snapshot e quindi copiarli in un account di archiviazione Standard con ridondanza geografica. Per altre informazioni, vedere [Opzioni di ridondanza di Archiviazione di Azure](storage-redundancy.md).

Se un disco è collegato a una macchina virtuale, alcune operazioni API non sono consentite sui dischi. Ad esempio, non è possibile eseguire un'operazione [Copy Blob](/rest/api/storageservices/Copy-Blob) sul BLOB finché il disco è collegato a una macchina virtuale. Al contrario, è necessario creare innanzitutto uno snapshot del BLOB usando il metodo dell'API REST [Snapshot Blob](/rest/api/storageservices/Snapshot-Blob), quindi eseguire l'operazione [Copy Blob](/rest/api/storageservices/Copy-Blob) dello snapshot per copiare il disco collegato. In alternativa, è possibile scollegare il disco e quindi eseguire le operazioni necessarie.

Per mantenere copie con ridondanza geografica degli snapshot, è possibile copiare gli snapshot da un account di archiviazione con ridondanza locale a un account di archiviazione Standard con ridondanza geografica usando AzCopy o Copy Blob. Per altre informazioni, vedere [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md) e [Copy Blob](/rest/api/storageservices/Copy-Blob).

Per informazioni dettagliate sull'esecuzione di operazioni REST sui BLOB di pagine negli account di archiviazione Standard, vedere l'articolo relativo alle [API REST per i servizi di archiviazione di Azure](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

### <a name="managed-disks"></a>Dischi gestiti

Uno snapshot di un disco gestito è una copia di sola lettura del disco gestito archiviata come disco gestito Standard. Gli snapshot incrementali non sono attualmente supportati per i dischi gestiti, ma saranno supportati in futuro.

Se un disco gestito è collegato a una VM, alcune operazioni API non sono consentite sui dischi. Ad esempio, non è possibile generare una firma di accesso condiviso per eseguire un'operazione di copia mentre il disco è collegato a una macchina virtuale. Creare invece prima di tutto uno snapshot del disco e quindi eseguire la copia dello snapshot. In alternativa, è possibile scollegare il disco e quindi generare una firma di accesso condiviso per eseguire l'operazione di copia.

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

Quando si usa Archiviazione Standard, tenere conto delle considerazioni seguenti relative alla fatturazione:

* Dimensioni dati/dischi non gestiti di Archiviazione Standard 
* Dischi gestiti Standard
* Snapshot di Archiviazione Standard
* Trasferimenti di dati in uscita
* Transazioni

**Dimensioni dei dischi e dei dati di archiviazione non gestiti:** per i dischi non gestiti e altri dati (BLOB, tabelle, code e file) viene addebitata solo la quantità di spazio usata. Se per il BLOB di pagine di una VM è stato effettuato il provisioning per 127 GB ma la VM usa in realtà solo 10 GB di spazio, verranno fatturati solo 10 GB. 

**Dischi gestiti:** la fatturazione dei dischi gestiti è basata sulle dimensioni per cui è stato effettuato il provisioning. Se per un disco è stato effettuato il provisioning come disco da 10 GB e si usano solo 5 GB, verranno comunque addebitate le dimensioni di 10 GB del provisioning.

**Snapshot**: gli snapshot dei dischi Standard vengono fatturati in base alla capacità aggiuntiva usata allo scopo. Per informazioni sugli snapshot, vedere [Creazione di uno snapshot di un BLOB](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

**Trasferimenti di dati in uscita**: i [trasferimenti di dati in uscita](https://azure.microsoft.com/pricing/details/data-transfers/) (dati in uscita dai data center di Azure) vengono fatturati in base all'uso della larghezza di banda.

**Transazioni**: per Archiviazione Standard, Azure addebita $ 0,0036 per 100.000 transazioni. Le transazioni includono le operazioni sia di lettura che di scrittura nello spazio di archiviazione.

Per informazioni dettagliate sui prezzi di Archiviazione Standard, Macchine virtuali e Managed Disks, vedere:

* [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/)
* [Prezzi di Macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Prezzi di Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks)

## <a name="azure-backup-service-support"></a>Supporto del servizio Backup di Azure 

È possibile eseguire il backup delle macchine virtuali con dischi non gestiti con Backup di Azure. [Altre informazioni](../backup/backup-azure-vms-first-look-arm.md).

È anche possibile usare il servizio Backup di Azure con Managed Disks per creare un processo di backup con backup pianificati, facile ripristino delle macchine virtuali e criteri di conservazione dei backup. Per altre informazioni in merito, vedere la sezione relativa all'[uso del servizio Backup di Azure per macchine virtuali con dischi gestiti](../backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Archiviazione di Azure](storage-introduction.md)

* [Creare un account di archiviazione](storage-create-storage-account.md)

* [Panoramica di Managed Disks](storage-managed-disks-overview.md)

* [Creare una VM con Resource Manager e PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md)

* [Creare una VM Linux usando l'interfaccia della riga di comando di Azure 2.0](../virtual-machines/linux/quick-create-cli.md)
