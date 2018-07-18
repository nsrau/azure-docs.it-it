---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: rogara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 399479de0ce9bab29d0338b1155f8b0c1bab542c
ms.sourcegitcommit: caebf2bb2fc6574aeee1b46d694a61f8b9243198
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/12/2018
ms.locfileid: "35414587"
---
# <a name="azure-managed-disks-overview"></a>Panoramica di Azure Managed Disks

Azure Managed Disks semplifica la gestione dei dischi per le macchine virtuali IaaS di Azure grazie alla gestione degli [account di archiviazione](../articles/storage/common/storage-introduction.md) associati ai dischi delle macchine virtuali. Specificando il tipo ([HDD Standard](../articles/virtual-machines/windows/standard-storage.md), SSD Standard o [SSD Premium](../articles/virtual-machines/windows/premium-storage.md)) e le dimensioni del disco necessarie, Azure crea e gestisce automaticamente il disco.

## <a name="benefits-of-managed-disks"></a>Vantaggi dei dischi gestiti

Esaminiamo alcuni dei vantaggi che si ottengono usando dischi gestiti, a partire da questo video di Channel 9, [Better Azure VM Resiliency with Managed Disks](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency) (Migliore resilienza della macchina virtuale di Azure con Managed Disks).
<br/>
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency/player]

### <a name="simple-and-scalable-vm-deployment"></a>Distribuzione semplice e scalabile di macchine virtuali

Managed Disks gestisce automaticamente le risorse di archiviazione. In precedenza era necessario creare account di archiviazione per contenere i dischi, ovvero i file VHD, delle macchine virtuali di Azure. Per aumentare le prestazioni era necessario creare account di archiviazione aggiuntivi per non supera il limite di IOPS per l'archiviazione con uno dei dischi. Affidando a Managed Disks la gestione delle risorse di archiviazione, non è più necessario preoccuparsi dei limiti degli account di archiviazione, ad esempio di 20.000 IOPS per account. E non è più necessario copiare le immagini personalizzate, ovvero i file VHD, in più account di archiviazione. È possibile gestire tali immagini in una posizione centralizzata, un unico account di archiviazione per ogni area di Azure, e usarle per creare centinaia di macchine virtuali in una sottoscrizione.

Managed Disks consentirà di creare fino a 50.000 **dischi** di macchine virtuali di un tipo in una sottoscrizione per ogni area, offrendo quindi la possibilità di creare migliaia di **macchine virtuali** in una singola sottoscrizione. Questa funzionalità aumenta ulteriormente la scalabilità dei [set di scalabilità di macchine virtuali](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md), permettendo di creare fino a mille macchine virtuali in un set di scalabilità di macchine virtuali con un'immagine del Marketplace. 

### <a name="better-reliability-for-availability-sets"></a>Maggiore affidabilità per i set di disponibilità

Managed Disks offre una maggiore affidabilità per i set di disponibilità, perché fa in modo che [i dischi delle macchine virtuali in un set di disponibilità](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) siano sufficientemente isolati gli uni dagli altri per evitare singoli punti di errore. I dischi vengono automaticamente posizionati in unità di scala di archiviazione diverse (timbri). Se uno stamp non riesce a causa di un errore hardware o software, hanno esito negativo solo le istanze delle macchine virtuali con dischi in tali stamp. Si prenda ad esempio un'applicazione in esecuzione in cinque macchine virtuali, a loro volta inserite in un set di disponibilità. I dischi di tali macchine virtuali non vengono tutti archiviati nello stesso stamp. In caso di inattività di uno stamp, quindi, le altre istanze dell'applicazione continuano l'esecuzione.

### <a name="highly-durable-and-available"></a>Elevati livelli di durabilità e disponibilità

I dischi di Azure sono stati progettati per il 99,999% di disponibilità. È possibile quindi riposare serenamente sapendo di avere tre repliche dei dati, che consentono una durabilità elevata. In caso di problemi con una o addirittura due repliche, le repliche rimanenti contribuiscono ad assicurare la persistenza dei dati e una tolleranza di errore elevata. Questa architettura ha consentito ad Azure di offrire costantemente durabilità di livello aziendale per i dischi IaaS, con una percentuale di frequenza errori annualizzata pari a ZERO, la migliore del settore. 

### <a name="granular-access-control"></a>Controllo di accesso granulare

Per assegnare autorizzazioni specifiche per un disco gestito a uno o più utenti, è possibile usare il [controllo degli accessi in base al ruolo di Azure](../articles/role-based-access-control/overview.md). Managed Disks espone una serie di operazioni, inclusa la lettura, la scrittura (creazione/aggiornamento), l'eliminazione e il recupero di un [URI di firma di accesso condiviso](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) per il disco. È possibile consentire l'accesso solo alle operazioni che servono agli utenti per svolgere il proprio lavoro. Ad esempio, per fare in modo che un utente non possa copiare un disco gestito in un account di archiviazione, è possibile scegliere di non consentire l'accesso all'operazione di esportazione di tale disco gestito. Analogamente, per fare in modo che un utente non possa usare un URI di firma di accesso condiviso per copiare un disco gestito, è possibile scegliere di non concedere l'autorizzazione per il disco gestito.

### <a name="azure-backup-service-support"></a>Supporto del servizio Backup di Azure
Usare il servizio Backup di Azure con Managed Disks per creare un processo di backup con backup basati sul tempo, ripristino semplice delle macchine virtuali e criteri per la conservazione dei backup. Managed Disks supporta solo l'archiviazione con ridondanza locale (LRS) come opzione di replica. Vengono mantenute tre copie dei dati all'interno di una singola area. Per eseguire il ripristino di emergenza dell'area, è necessario eseguire il backup dei dischi delle macchine virtuali in un'altra area usando il [servizio Backup di Azure](../articles/backup/backup-introduction-to-azure-backup.md) e un account di archiviazione con ridondanza geografica come insieme di credenziali di backup. Backup di Azure attualmente supporta tutte le dimensioni di disco, inclusi i dischi da 4 TB. È necessario [aggiornare lo stack di backup della macchina virtuale alla versione 2](../articles/backup/backup-upgrade-to-vm-backup-stack-v2.md) per supportare dischi da 4 TB. Per altre informazioni, vedere [Using Azure Backup service for VMs with Managed Disks](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup) (Uso del servizio Backup di Azure per le macchine virtuali con Managed Disks).

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

Quando si usa Managed Disks, tenere conto delle considerazioni seguenti relative alla fatturazione:
* Tipo di archiviazione

* Dimensione disco

* Numero di transazioni

* Trasferimenti di dati in uscita

* Snapshot del disco gestito (copia dell'intero disco)

Tali considerazioni vengono ora esaminate più in dettaglio.

**Tipo di archiviazione:** Managed Disks offre tre livelli di prestazioni: [HDD Standard](../articles/virtual-machines/windows/standard-storage.md), SSD Standard (anteprima) e [Premium](../articles/virtual-machines/windows/premium-storage.md). La fatturazione di un disco gestito dipende dal tipo di archiviazione scelto per il disco.


**Dimensione disco**: per un disco di archiviazione Premium la fatturazione dipende dalle dimensioni del disco di cui è stato effettuato il provisioning. Azure associa la dimensione del disco, arrotondata per eccesso, all'opzione relativa a Managed Disks più vicina, come specificato nelle tabelle seguenti. Viene eseguito il mapping di ogni disco gestito a una delle dimensioni di provisioning supportate e viene eseguita la relativa fatturazione. Se, ad esempio, si crea un disco gestito Standard e si specifica una dimensione del disco di cui è stato effettuato il provisioning pari a 200 GB, i costi addebitati si basano sui prezzi del tipo di disco S15.

Di seguito sono indicate le dimensioni dei dischi disponibili per un disco gestito Premium:

| **Tipo di disco <br>gestito Premium** | **P4** | **P6** |**P10** | **P15** | **P20** | **P30** | **P40** | **P50** | 
|------------------|---------|---------|---------|---------|---------|----------------|----------------|----------------|  
| Dimensione disco        | 32 GiB   | 64 GiB   | 128 GiB  | 256 GiB  | 512 GiB  | 1024 GiB (1 TiB) | 2048 GiB (2 TiB) | 4095 GiB (4 TiB) | 

Di seguito sono indicate le dimensioni dei dischi disponibili per un disco gestito SSD Standard:

| **Tipo di disco gestito <br>SSD Standard** | **E10** | **E15** | **E20** | **E30** | **E40** | **E50** |
|------------------|--------|--------|--------|----------------|----------------|----------------| 
| Dimensione disco        | 128 GiB | 256 GiB | 512 GiB | 1024 GiB (1 TiB) | 2048 GiB (2 TiB) | 4095 GiB (4 TiB) | 

Di seguito sono indicate le dimensioni dei dischi disponibili per un disco gestito HDD Standard:

| **Tipo di disco gestito <br>HDD Standard** | **S4** | **S6** | **S10** | **S15** | **S20** | **S30** | **S40** | **S50** |
|------------------|---------|---------|--------|--------|--------|----------------|----------------|----------------| 
| Dimensione disco        | 32 GiB  | 64 GiB  | 128 GiB | 256 GiB | 512 GiB | 1024 GiB (1 TiB) | 2048 GiB (2 TiB) | 4095 GiB (4 TiB) | 


**Numero di transazioni**: viene addebitato il numero di transazioni eseguite in un disco gestito Standard.

I dischi SSD standard usano dimensioni dell'unità di I/O di 256 KB. Se il volume dei dati è inferiore a 256 KB, viene considerata 1 singola unità di I/O. Le dimensioni di I/O superiori vengono calcolate come più I/O da 256 KB. Ad esempio, 1.100 KB di I/O corrispondono a cinque unità di I/O.

Non sono previsti costi per le transazioni per un disco gestito Premium.

**Trasferimenti di dati in uscita**: i [trasferimenti di dati in uscita](https://azure.microsoft.com/pricing/details/data-transfers/) (dati in uscita dai data center di Azure) vengono fatturati in base all'uso della larghezza di banda.

Per informazioni dettagliate sui prezzi di Managed Disks, vedere [Prezzi di Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks).


## <a name="managed-disk-snapshots"></a>Snapshot dei dischi gestiti

Uno snapshot gestito è una copia di sola lettura di un disco gestito che viene archiviata come disco gestito Standard per impostazione predefinita. Gli snapshot permettono di eseguire il backup dei dischi gestiti in qualsiasi momento. Questi snapshot esistono indipendentemente dal disco di origine e possono essere usati per creare nuove istanze di Managed Disks. Vengano addebitati in funzione della dimensione usata. Ad esempio, se si crea uno snapshot di un disco gestito con una capacità di provisioning di 64 GiB e una dimensione di dati effettivamente usata di 10 GiB, verranno addebitati solo gli snapshot relativi alla dimensione di dati usata di 10 GiB.  

Gli [snapshot incrementali](../articles/virtual-machines/windows/incremental-snapshots.md) non sono attualmente supportati per Managed Disks.

Per altre informazioni su come creare snapshot con Managed Disks, vedere le risorse seguenti:

* [Creare una copia del disco rigido virtuale archiviato come disco gestito usando gli snapshot in Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Creare una copia del disco rigido virtuale archiviato come disco gestito usando gli snapshot in Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)


## <a name="images"></a>Immagini

Managed Disks supporta anche la creazione di un'immagine personalizzata gestita. È possibile creare un'immagine dal disco rigido virtuale personalizzato in un account di archiviazione oppure direttamente da una macchina virtuale (preparata con Sysprep) generalizzata. Questo processo acquisisce in un'unica immagine tutti ii dischi gestiti associati a una macchina virtuale, inclusi i dischi dati e del sistema operativo. Questa immagine personalizzata gestita permette di creare centinaia di macchine virtuali usando l'immagine personalizzata senza dover copiare o gestire alcun account di archiviazione.

Per informazioni sulla creazione di immagini, vedere gli articoli seguenti:
* [Come acquisire un'immagine gestita di una macchina virtuale generalizzata in Azure](../articles/virtual-machines/windows/capture-image-resource.md)
* [Come generalizzare e acquisire una macchina virtuale Linux con l'interfaccia della riga di comando di Azure 2.0](../articles/virtual-machines/linux/capture-image.md)

## <a name="images-versus-snapshots"></a>Immagini e snapshot

Il termine "immagine" viene usato spesso con le macchine virtuali e ora si sta diffondendo anche il termine "snapshot". È importante comprendere la differenza tra questi termini. Managed Disks permette di acquisire l'immagine di una macchina virtuale generalizzata che è stata deallocata. Tale immagine include tutti i dischi collegati alla macchina virtuale e può essere usata per creare una nuova macchina virtuale, che includerà tutti i dischi.

Uno snapshot è la copia di un disco nel momento in cui viene creato e si applica esclusivamente a un solo disco. Se la macchina virtuale include solo un disco, quello del sistema operativo, è possibile creare uno snapshot o acquisire un'immagine del disco e creare una macchina virtuale dallo snapshot o dall'immagine.

La situazione cambia se la macchina virtuale include cinque dischi con striping. Si potrebbe scegliere di creare uno snapshot di ogni disco, ma la macchina virtuale non include alcuna informazione sullo stato dei dischi, perché gli snapshot riguardano un solo disco. In tal caso sarebbe necessario coordinare gli snapshot, ma questa funzionalità non è attualmente supportata.

## <a name="managed-disks-and-encryption"></a>Managed Disks e crittografia

Esistono due tipi di crittografia in riferimento ai dischi gestiti. Il primo si chiama Crittografia del servizio di archiviazione (SSE) e viene eseguito dal servizio di archiviazione. Il secondo è Crittografia dischi di Azure e può essere attivato nei dischi del sistema operativo e nei dischi dati per le macchine virtuali.

### <a name="storage-service-encryption-sse"></a>Crittografia del servizio di archiviazione di Azure (SSE)

[Crittografia del servizio di archiviazione di Azure](../articles/storage/common/storage-service-encryption.md) fornisce la crittografia inattiva e salvaguarda i dati, in modo da soddisfare i criteri di sicurezza e conformità dell'organizzazione. La crittografia del servizio di archiviazione è abilitata per impostazione predefinita per tutti i Managed Disks, gli snapshot e le immagini in tutte le area in cui i dischi gestiti sono disponibili. A partire dal 10 giugno 2017, per impostazione predefinita tutti i nuovi dischi gestiti/snapshot/immagini e i nuovi dati scritti nei dischi gestiti esistenti vengono automaticamente crittografati in modo inattivo con le chiavi gestite da Microsoft. Visitare la [pagina Domande frequenti sui dischi e sui dischi Premium delle macchine virtuali IaaS di Azure (gestiti e non gestiti)](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption) per altri dettagli.


### <a name="azure-disk-encryption-ade"></a>Crittografia dischi di Azure (ADE)

Crittografia dischi di Azure consente di crittografare i dischi del sistema operativo e i dischi dati usati da una macchina virtuale IaaS. Questo tipo di crittografia include i dischi gestiti. Per Windows, le unità vengono crittografate mediante la tecnologia di crittografia BitLocker standard del settore. Per Linux, i dischi vengono crittografati mediante la tecnologia DM-Crypt. Il processo di crittografia è integrato in Azure Key Vault per consentire il controllo e la gestione delle chiavi di crittografia del disco. Per altre informazioni, vedere [Crittografia dischi di Azure per l'anteprima di macchine virtuali IaaS Windows e Linux](../articles/security/azure-security-disk-encryption.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Managed Disks, vedere gli articoli seguenti.

### <a name="get-started-with-managed-disks"></a>Informazioni di base sui Dischi gestiti

* [Creare una VM con Resource Manager e PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md)

* [Creare una VM Linux usando l'interfaccia della riga di comando di Azure 2.0](../articles/virtual-machines/linux/quick-create-cli.md)

* [Collegare un disco dati gestito a una macchina virtuale Windows con PowerShell](../articles/virtual-machines/windows/attach-disk-ps.md)

* [Aggiungere un disco gestito a una VM Linux](../articles/virtual-machines/linux/add-disk.md)

* [Script di esempio di PowerShell di Managed Disks](https://github.com/Azure-Samples/managed-disks-powershell-getting-started)

* [Usare Managed Disks nei modelli di Azure Resource Manager](../articles/virtual-machines/windows/using-managed-disks-template-deployments.md)

### <a name="compare-managed-disks-storage-options"></a>Confronto tra le opzioni di archiviazione di Managed Disks

* [Dischi SSD Premium](../articles/virtual-machines/windows/premium-storage.md)

* [Dischi SSD e HDD Standard](../articles/virtual-machines/windows/standard-storage.md)

### <a name="operational-guidance"></a>Informazioni operative

* [Eseguire la migrazione da AWS e altre piattaforme a Managed Disks in Azure](../articles/virtual-machines/windows/on-prem-to-azure.md)

* [Convertire macchine virtuali di Azure in dischi gestiti in Azure](../articles/virtual-machines/windows/migrate-to-managed-disks.md)
