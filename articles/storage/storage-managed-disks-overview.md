---
title: Panoramica di Azure Managed Disks Standard e Premium | Microsoft Docs
description: Panoramica di Azure Managed Disks, che gestisce automaticamente gli account di archiviazione quando si usano macchine virtuali di Azure
services: storage
documentationcenter: na
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 272250b3-fd4e-41d2-8e34-fd8cc341ec87
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 339df6e5ff05c66e898254f2cd4bb5b596d0c537
ms.lasthandoff: 03/14/2017


---

# <a name="azure-managed-disks-overview"></a>Panoramica di Azure Managed Disks

Azure Managed Disks semplifica la gestione dei dischi per le macchine virtuali IaaS di Azure grazie alla gestione degli [account di archiviazione](storage-introduction.md) associati ai dischi delle macchine virtuali. Specificando il tipo, [Premium](storage-premium-storage.md) o [Standard](storage-standard-storage.md), e le dimensioni del disco necessarie, Azure crea e gestisce automaticamente il disco.

>[!NOTE]
>Le macchine virtuali con Managed Disks richiedono traffico in uscita sulla porta 8443 per segnalare lo stato [estensioni di VM](../virtual-machines/virtual-machines-windows-extensions-features.md) installate sulla piattaforma Azure. Il provisioning di una macchina virtuale con estensioni ha esito negativo se questa porta non è disponibile. Lo stato di distribuzione di un'estensione è sconosciuto anche se questa viene installata in una macchina virtuale in esecuzione. Se non è possibile sbloccare la porta 8443, è necessario usare i dischi non gestiti. Microsoft sta lavorando attivamente per risolvere il problema. Per altri dettagli, consultare le [Domande frequenti sui dischi e sui dischi Premium delle macchine virtuali IaaS di Azure (gestiti e non gestiti)](storage-faq-for-disks.md#managed-disks-and-port-8443). 
>
>

## <a name="benefits-of-managed-disks"></a>Vantaggi dei dischi gestiti

Di seguito sono illustrati alcuni dei vantaggi legati all'uso dei dischi gestiti.

### <a name="simple-and-scalable-vm-deployment"></a>Distribuzione semplice e scalabile di macchine virtuali

Managed Disks gestisce automaticamente le risorse di archiviazione. In precedenza era necessario creare account di archiviazione per contenere i dischi, ovvero i file VHD, delle macchine virtuali di Azure. Per aumentare le prestazioni era necessario creare account di archiviazione aggiuntivi per non supera il limite di IOPS per l'archiviazione con uno dei dischi. Affidando a Managed Disks la gestione delle risorse di archiviazione, non è più necessario preoccuparsi dei limiti degli account di archiviazione, ad esempio di 20.000 IOPS per account. E non è più necessario copiare le immagini personalizzate, ovvero i file VHD, in più account di archiviazione. È possibile gestire tali immagini in una posizione centralizzata, un unico account di archiviazione per ogni area di Azure, e usarle per creare centinaia di macchine virtuali in una sottoscrizione.

Managed Disks permette di creare fino a 10.000 **dischi** di macchine virtuali in una sottoscrizione, consentendo quindi di creare migliaia di **macchine virtuali** in una singola sottoscrizione. Questa funzionalità aumenta ulteriormente la scalabilità dei [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md), permettendo di creare fino a mille macchine virtuali in un set di scalabilità di macchine virtuali con un'immagine del Marketplace.

### <a name="better-reliability-for-availability-sets"></a>Maggiore affidabilità per i set di disponibilità

Managed Disks offre una maggiore affidabilità per i set di disponibilità, perché fa in modo che i dischi delle macchine virtuali in un set di disponibilità siano sufficientemente isolati gli uni dagli altri per evitare singoli punti di errore. Ciò avviene mediante l'inserimento automatico dei dischi in unità di scala di archiviazione diverse, dette stamp. Se uno stamp non riesce a causa di un errore hardware o software, hanno esito negativo solo le istanze delle macchine virtuali con dischi in tali stamp. Si prenda ad esempio un'applicazione in esecuzione in cinque macchine virtuali, a loro volta inserite in un set di disponibilità. I dischi di tali macchine virtuali non vengono tutti archiviati nello stesso stamp. In caso di inattività di uno stamp, quindi, le altre istanze dell'applicazione continuano l'esecuzione.

### <a name="granular-access-control"></a>Controllo di accesso granulare

Per assegnare autorizzazioni specifiche per un disco gestito a uno o più utenti, è possibile usare il [controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-what-is.md). Managed Disks espone una serie di operazioni, inclusa la lettura, la scrittura (creazione/aggiornamento), l'eliminazione e il recupero di un [URI di firma di accesso condiviso](storage-dotnet-shared-access-signature-part-1.md) per il disco. È possibile consentire l'accesso solo alle operazioni che servono agli utenti per svolgere il proprio lavoro. Ad esempio, per fare in modo che un utente non possa copiare un disco gestito in un account di archiviazione, è possibile scegliere di non consentire l'accesso all'operazione di esportazione di tale disco gestito. Analogamente, per fare in modo che un utente non possa usare un URI di firma di accesso condiviso per copiare un disco gestito, è possibile scegliere di non concedere l'autorizzazione per il disco gestito.

## <a name="pricing-and-billing"></a>Prezzi e fatturazione 

Quando si usa Managed Disks, tenere conto delle considerazioni seguenti relative alla fatturazione:

* Tipo di archiviazione

* Dimensione disco

* Numero di transazioni

* Trasferimenti di dati in uscita

* Snapshot del disco gestito (copia dell'intero disco)

Tali considerazioni vengono ora esaminate più in dettaglio.

**Tipo di archiviazione:** Managed Disks offre due livelli di prestazioni, [Premium](storage-premium-storage.md), basato su unità SSD, e [Standard](storage-standard-storage.md), basato su unità disco rigido. La fatturazione di un disco gestito dipende dal tipo di archiviazione scelto per il disco.


**Dimensione disco**: per un disco di archiviazione Premium la fatturazione dipende dalle dimensioni del disco di cui è stato effettuato il provisioning. Azure associa la dimensione del disco, arrotondata per eccesso, all'opzione relativa a Managed Disks più vicina, come specificato nelle tabelle seguenti. Viene eseguito il mapping di ogni disco gestito a una delle dimensioni di provisioning supportate e viene eseguita la relativa fatturazione. Se, ad esempio, si crea un disco gestito Standard e si specifica una dimensione del disco di cui è stato effettuato il provisioning pari a 200 GB, i costi addebitati si basano sui prezzi del tipo di disco S20.

Di seguito sono indicate le dimensioni dei dischi disponibili per un disco gestito Premium:

| **Tipo di disco <br>gestito Premium**  | **P10** | **P20** | **P30**        |
|------------------|---------|---------|----------------|
| Dimensione disco        | 128 GB  | 512 GB  | 1024 GB (1 TB) |

Di seguito sono indicate le dimensioni dei dischi disponibili per un disco gestito Standard: 

| **Tipo di disco <br>gestito Standard** | **S4**  | **S6**  | **S10**        | **S20** | **S30**        |
|------------------|---------|---------|----------------|---------|----------------|
| Dimensione disco        | 32 GB   | 64 GB   | 128 GB  | 512 GB  | 1024 GB (1 TB) |

**Numero di transazioni**: viene addebitato il numero di transazioni eseguite in un disco gestito Standard. Non sono previsti costi per le transazioni per un disco gestito Premium.

**Trasferimenti di dati in uscita**: i [trasferimenti di dati in uscita](https://azure.microsoft.com/pricing/details/data-transfers/) (dati in uscita dai data center di Azure) vengono fatturati in base all'uso della larghezza di banda.

**Snapshot del disco gestito (copia dell'intero disco)**: uno snapshot gestito è una copia di sola lettura di un disco gestito che viene archiviata come disco gestito Standard. Gli snapshot permettono di eseguire il backup dei dischi gestiti in qualsiasi momento. Questi snapshot esistono indipendentemente dal disco di origine e possono essere usati per creare nuove istanze di Managed Disks. Il costo di uno snapshot gestito corrisponde a quello di un disco gestito Standard. Ad esempio, se si acquisisce uno snapshot di un disco gestito Premium da 128 GB, il costo dello snapshot gestito sarà equivalente a quello di un disco gestito Standard da 128 GB.

Gli [snapshot incrementali](storage-incremental-snapshots.md) non sono attualmente supportati per Managed Disks, ma lo saranno in futuro.

Per altre informazioni su come creare snapshot con Managed Disks, vedere le risorse seguenti:

* [Creare una copia del disco rigido virtuale archiviato come disco gestito usando gli snapshot in Windows](../virtual-machines/virtual-machines-windows-snapshot-copy-managed-disk.md)
* [Creare una copia del disco rigido virtuale archiviato come disco gestito usando gli snapshot in Linux](../virtual-machines/linux/virtual-machines-linux-snapshot-copy-managed-disk.md)


Per informazioni dettagliate sui prezzi di Managed Disks, vedere [Prezzi di Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks).

## <a name="images"></a>Immagini

Managed Disks supporta anche la creazione di un'immagine personalizzata gestita. È possibile creare un'immagine dal disco rigido virtuale personalizzato in un account di archiviazione oppure direttamente da una macchina virtuale (preparata con Sysprep) generalizzata. Tutti i dischi gestiti associati a una macchina virtuale, inclusi i dischi dati e del sistema operativo, vengono acquisiti in un'unica immagine. Questo permette di creare centinaia di macchine virtuali usando l'immagine personalizzata senza dover copiare o gestire alcun account di archiviazione.

Per informazioni sulla creazione di immagini, vedere gli articoli seguenti:
* [Come acquisire un'immagine gestita di una macchina virtuale generalizzata in Azure](../virtual-machines/virtual-machines-windows-capture-image-resource.md)
* [Come generalizzare e acquisire una macchina virtuale Linux con l'interfaccia della riga di comando di Azure 2.0](../virtual-machines/virtual-machines-linux-capture-image.md)

## <a name="images-versus-snapshots"></a>Immagini e snapshot

Il termine "immagine" viene usato spesso con le macchine virtuali e ora si sta diffondendo anche il termine "snapshot". È importante comprendere la differenza tra immagini e snapshot. Managed Disks permette di acquisire l'immagine di una macchina virtuale generalizzata che è stata deallocata. Tale immagine include tutti i dischi collegati alla macchina virtuale e può essere usata per creare una nuova macchina virtuale, che includerà tutti i dischi.

Uno snapshot è la copia di un disco nel momento in cui viene creato e si applica esclusivamente a un solo disco. Se la macchina virtuale include solo un disco, quello del sistema operativo, è possibile creare uno snapshot o acquisire un'immagine del disco e creare una macchina virtuale dallo snapshot o dall'immagine.

La situazione cambia se la macchina virtuale include cinque dischi con striping. Si potrebbe scegliere di creare uno snapshot di ogni disco, ma la macchina virtuale non include alcuna informazione sullo stato dei dischi, perché gli snapshot riguardano un solo disco. In tal caso sarebbe necessario coordinare gli snapshot, ma questa funzionalità non è attualmente supportata.

## <a name="azure-backup-service-support"></a>Supporto del servizio Backup di Azure 

È possibile eseguire il backup delle macchine virtuali con dischi non gestiti con Backup di Azure. [Altre informazioni](../backup/backup-azure-vms-first-look-arm.md).

È anche possibile usare il servizio Backup di Azure con Managed Disks per creare un processo di backup con backup pianificati, facile ripristino delle macchine virtuali e criteri di conservazione dei backup. Per altre informazioni in merito, vedere la sezione relativa all'[uso del servizio Backup di Azure per macchine virtuali con dischi gestiti](../backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). 

## <a name="managed-disks-and-storage-service-encryption-sse"></a>Managed Disks e crittografia del servizio di archiviazione (SSE)

Archiviazione di Azure supporta la crittografia automatica dei dati scritti in un account di archiviazione. Per altre informazioni, vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md). Per quanto riguarda i dati nei dischi gestiti, attualmente non è possibile abilitare la crittografia del servizio di archiviazione per Managed Disks, ma questa funzionalità sarà presto disponibile. Nel frattempo è necessario sapere come usare un file VHD crittografato che risiede in un account di archiviazione crittografato. 

La crittografia del servizio di archiviazione crittografa i dati man mano che vengono scritti nell'account di archiviazione. Non è possibile usare un file VHD crittografato in precedenza con la crittografia del servizio di archiviazione per creare una macchina virtuale che usa Managed Disks. Non è possibile neppure convertire un disco non gestito crittografato in un disco gestito. Infine, se si disabilita la crittografia nell'account di archiviazione, il file VHD non viene più decrittografato. 

Per usare un disco crittografato, è prima necessario copiare il file VHD in un account di archiviazione che non è mai stato crittografato. È quindi possibile creare una macchina virtuale con Managed Disks e specificare tale file VHD durante la creazione. In alternativa, è possibile collegare il file VHD copiato a una macchina virtuale in esecuzione con Managed Disks. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Managed Disks, vedere gli articoli seguenti.

### <a name="get-started-with-managed-disks"></a>Informazioni di base sui Dischi gestiti 

* [Creare una VM con Resource Manager e PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md)

* [Creare una VM Linux usando l'interfaccia della riga di comando di Azure 2.0](../virtual-machines/virtual-machines-linux-quick-create-cli.md)

* [Collegare un disco dati gestito a una macchina virtuale Windows con PowerShell](../virtual-machines/virtual-machines-windows-attach-disk-ps.md)

* [Aggiungere un disco gestito a una VM Linux](../virtual-machines/virtual-machines-linux-quick-create-cli.md)

### <a name="compare-managed-disks-storage-options"></a>Confronto tra le opzioni di archiviazione di Managed Disks 

* [Dischi e Archiviazione Premium](storage-premium-storage.md)

* [Dischi e Archiviazione Standard](storage-standard-storage.md)

### <a name="operational-guidance"></a>Informazioni operative

* [Eseguire la migrazione da AWS e altre piattaforme a Managed Disks in Azure](../virtual-machines/virtual-machines-windows-on-prem-to-azure.md)

* [Convertire macchine virtuali di Azure in dischi gestiti in Azure](../virtual-machines/virtual-machines-windows-migrate-to-managed-disks.md)

