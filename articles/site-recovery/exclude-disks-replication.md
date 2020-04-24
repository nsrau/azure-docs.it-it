---
title: Escludere i dischi dalla replica con Azure Site Recovery
description: Come escludere i dischi dalla replica in Azure con Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 57bf06f0fde85714530c06cbd008db08de7460d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281847"
---
# <a name="exclude-disks-from-disaster-recovery"></a>Escludere dischi dal ripristino di emergenza

Questo articolo descrive come escludere i dischi dalla replica durante il ripristino di emergenza da locale ad Azure con [Azure Site Recovery](site-recovery-overview.md). È possibile escludere i dischi dalla replica per diversi motivi:

- In modo che i dati non importanti che vengono rilevati nel disco escluso non vengano replicati.
- Per ottimizzare la larghezza di banda di replica utilizzata o le risorse lato destinazione.
- Per salvare le risorse di archiviazione e di rete non eseguendo la replica dei dati non necessari.
- Le macchine virtuali di Azure hanno raggiunto Site Recovery limiti di replica.


## <a name="supported-scenarios"></a>Scenari supportati

È possibile escludere i dischi dalla replica come riepilogati nella tabella.

**Da Azure ad Azure** | **Da VMware ad Azure** | **Da Hyper-V ad Azure** 
--- | --- | ---
Sì (con PowerShell) | Sì | Sì 

## <a name="exclude-limitations"></a>Escludi limitazioni

**Limitazione** | **Macchine virtuali di Azure** | **VM VMware** | **VM Hyper-V**
--- | --- | ---
**Tipi di disco** | È possibile escludere dalla replica i dischi di base.<br/><br/> Non è possibile escludere dischi del sistema operativo o dischi dinamici. Per impostazione predefinita, i dischi temporanei sono esclusi. | È possibile escludere dalla replica i dischi di base.<br/><br/> Non è possibile escludere dischi del sistema operativo o dischi dinamici. | È possibile escludere dalla replica i dischi di base.<br/><br/> Non è possibile escludere dischi del sistema operativo e non è consigliabile escludere dischi dinamici. Site Recovery non è in grado di identificare quale VHS è di base o dinamico nella macchina virtuale guest. Se tutti i dischi del volume dinamico dipendenti non sono esclusi, il disco dinamico protetto diventa un disco danneggiato in una macchina virtuale di failover e i dati su tale disco non sono accessibili.
**Disco di replica** | Non è possibile escludere un disco che sta eseguendo la replica.<br/><br/> Disabilitare e riabilitare la replica per la macchina virtuale. |  Non è possibile escludere un disco che sta eseguendo la replica. |  Non è possibile escludere un disco che sta eseguendo la replica.
**Servizio Mobility (VMware)** | Non rilevante | È possibile escludere i dischi solo nelle macchine virtuali in cui è installato il servizio Mobility.<br/><br/> Ciò significa che è necessario installare manualmente il servizio Mobility nelle macchine virtuali per cui si vogliono escludere i dischi. Non è possibile usare il meccanismo di installazione push perché installa il servizio Mobility solo dopo l'abilitazione della replica. | Non pertinente.
**Aggiungi/Rimuovi** | È possibile aggiungere e rimuovere dischi in macchine virtuali di Azure con Managed Disks. | Non è possibile aggiungere o rimuovere dischi dopo l'abilitazione della replica. Disabilitare e quindi riabilitare la replica per aggiungere un disco. | Non è possibile aggiungere o rimuovere dischi dopo l'abilitazione della replica. Disabilitare e riabilitare la replica.
**Failover** | Se un'app richiede un disco che è stato escluso, dopo il failover è necessario creare manualmente il disco in modo che l'app replicata possa essere eseguita.<br/><br/> In alternativa, è possibile creare il disco durante il failover della macchina virtuale, integrando automazione di Azure in un piano di ripristino. | Se si esclude un disco necessario per un'app, crearlo manualmente in Azure dopo il failover. | Se si esclude un disco necessario per un'app, crearlo manualmente in Azure dopo il failover.
**Failback locale-dischi creati manualmente** | Non rilevante | **Macchine virtuali Windows**: non è stato eseguito il failback dei dischi creati manualmente in Azure. Se, ad esempio, si esegue il failover di tre dischi e si creano due dischi direttamente in una macchina virtuale di Azure, viene eseguito il failback solo dei tre dischi di cui è stato eseguito il failover.<br/><br/> **VM Linux**: il failback dei dischi creati manualmente in Azure non è stato eseguito. Ad esempio, se si esegue il failover di tre dischi e si creano due dischi in una macchina virtuale di Azure, verrà eseguito il failback di tutti e cinque. Non è possibile escludere i dischi creati manualmente dall'operazione di failback. | Non viene eseguito il failback dei dischi creati manualmente in Azure. Se, ad esempio, si esegue il failover di tre dischi e si creano due dischi direttamente in una macchina virtuale di Azure, verrà eseguito il failback solo di tre dischi di cui è stato eseguito il failover.
**Failback locale-dischi esclusi** | Non rilevante | Se si esegue il failback nel computer originale, la configurazione del disco della macchina virtuale di failback non include i dischi esclusi. I dischi esclusi dalla replica da VMware ad Azure non sono disponibili nella macchina virtuale di failback. | Quando il failback è il percorso originale di Hyper-V, la configurazione del disco della macchina virtuale di failback rimane identica a quella del disco della VM di origine originale. I dischi esclusi dalla replica dal sito Hyper-V ad Azure sono disponibili nella macchina virtuale di failback.



## <a name="typical-scenarios"></a>Scenari tipici

Esempi di varianza dei dati che sono candidati eccezionali per l'esclusione includono Scritture in un file di paging (pagefile. sys) e scritture nel file tempdb del Microsoft SQL Server. A seconda del carico di lavoro e del sottosistema di archiviazione, i file di paging e tempdb possono registrare una quantità significativa di varianza. La replica di questo tipo di dati in Azure richiede un utilizzo intensivo delle risorse.

- Per ottimizzare la replica per una macchina virtuale con un singolo disco virtuale che include sia il sistema operativo che il file di paging, è possibile:
    1. Suddividere il singolo disco virtuale in due dischi virtuali, uno con il sistema operativo e l'altro con il file di paging.
    2. Escludere il disco del file di paging dalla replica.

- Per ottimizzare la replica per un disco che include sia il file di Microsoft SQL Server tempdb sia il file di database di sistema, è possibile:
    1. Archiviare il database di sistema e il tempdb in due dischi distinti.
    2. Escludere il disco del tempdb dalla replica.

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>Esempio 1: Escludere il disco del tempdb di SQL Server

Si esaminerà ora come gestire l'esclusione, il failover e il failover del disco per un'origine SQL Server VM Windows-* * SalesDB * * *, per cui si vuole escludere tempdb. 

### <a name="exclude-disks-from-replication"></a>Escludere dischi dalla replica

Questi dischi sono disponibili nella macchina virtuale Windows di origine SalesDB.

**Nome del disco** | **Disco del sistema operativo guest** | **Lettera di unità** | **Tipo di dati disco**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Disco del sistema operativo.
DB-Disk1| Disk1 | D:\ | Database di sistema SQL e utente Database1.
DB-Disk2 (disco escluso dalla protezione) | Disk2 | E:\ | File temporanei.
DB-Disk3 (disco escluso dalla protezione) | Disk3 | F:\ | Database tempdb SQL.<br/><br/> Percorso cartella-F:\MSSQL\Data\. Prendere nota del percorso della cartella prima del failover.
DB-Disk4 | Disk4 |G:\ | Database2 dell'utente

1. Viene abilitata la replica per la macchina virtuale SalesDB.
2. Si escludono disk2 e Disk3 dalla replica perché la varianza dei dati in tali dischi è temporanea. 


### <a name="handle-disks-during-failover"></a>Gestire i dischi durante il failover

Poiché i dischi non vengono replicati, quando si esegue il failover in Azure questi dischi non sono presenti nella macchina virtuale di Azure creata dopo il failover. La macchina virtuale di Azure include i dischi riepilogati in questa tabella.

**Disco del sistema operativo guest** | **Lettera di unità** | **Tipo di dati disco**
--- | --- | ---
Disk0 | C:\ | Disco del sistema operativo.
Disk1 | E:\ | Archiviazione temporanea<br/><br/>Questo disco è stato aggiunto da Azure. Poiché disk2 e Disk3 sono stati esclusi dalla replica, E: è la prima lettera di unità disponibile nell'elenco. Azure assegna quindi la lettera E: al volume di archiviazione temporanea. Le altre lettere di unità per i dischi replicati rimangono invariate.
Disk2 | D:\ | Database di sistema SQL e Database1 dell'utente
Disk3 | G:\ | Database2 dell'utente

In questo esempio, dal momento che Disk3, il disco tempdb SQL è stato escluso dalla replica e non è disponibile nella macchina virtuale di Azure, il servizio SQL si trova nello stato interrotto e richiede il percorso F:\MSSQL\Data. Questo percorso può essere creato in due modi: 

- Aggiungere un nuovo disco dopo il failover e assegnare il percorso della cartella tempdb.
- Usare un disco di archiviazione temporanea esistente per il percorso della cartella del tempdb.

#### <a name="add-a-new-disk-after-failover"></a>Aggiungi un nuovo disco dopo il failover

1. Prima del failover, annotare i percorsi di tempdb.mdf e tempdb.ldf di SQL.
2. Dalla portale di Azure aggiungere un nuovo disco alla macchina virtuale di Azure per il failover. Il disco deve avere le stesse dimensioni (o più grandi) del disco tempdb SQL di origine (Disk3).
3. Accedere alla macchina virtuale di Azure.
4. Dalla console di gestione del disco, diskmgmt.msc, inizializzare e formattare il disco appena aggiunto.
5. Assegnare la stessa lettera di unità usata dal disco tempdb SQL (F:)
6. Creare una cartella del tempdb nel volume F: (F:\MSSQL\Data).
7. Avviare il servizio SQL dalla console del servizio.

#### <a name="use-an-existing-temporary-storage-disk"></a>Usare un disco di archiviazione temporanea esistente 

1. Aprire un prompt dei comandi.
2. Eseguire SQL Server in modalità di ripristino dal prompt dei comandi.

        Net start MSSQLSERVER /f / T3608

3. Eseguire il comando sqlcmd seguente per sostituire il percorso del tempdb con il nuovo percorso.

        sqlcmd -A -S SalesDB        **Use your SQL DBname**
        USE master;     
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
        GO


4. Arrestare il servizio Microsoft SQL Server.

        Net stop MSSQLSERVER
5. Avviare il servizio Microsoft SQL Server.

        Net start MSSQLSERVER



### <a name="vmware-vms-disks-during-failback-to-original-location"></a>VM VMware: dischi durante il failback nel percorso originale

Viene ora illustrato come gestire i dischi nelle macchine virtuali VMware quando si esegue il failback nel percorso locale originale.

- **Dischi creati in Azure**: poiché l'esempio usa una macchina virtuale Windows, i dischi creati manualmente in Azure non vengono replicati nel sito quando si esegue il failback o si protegge nuovamente una macchina virtuale.
- **Disco di archiviazione temporaneo in Azure**: il disco di archiviazione temporanea non viene replicato in host locali.
- **Dischi esclusi**: i dischi esclusi dalla replica da VMware ad Azure non sono disponibili nella macchina virtuale locale dopo il failback.

Prima di eseguire il failback delle macchine virtuali VMware nel percorso originale, le impostazioni del disco della macchina virtuale di Azure sono le seguenti.

**Disco del sistema operativo guest** | **Lettera di unità** | **Tipo di dati disco**
--- | --- | ---
Disk0 | C:\ | Disco del sistema operativo.
Disk1 | E:\ | Archiviazione temporanea.
Disk2 | D:\ | Database di sistema SQL e utente Database1.
Disk3 | G:\ | Database2 utente.

Dopo il failback, la macchina virtuale VMware nella posizione originale include i dischi riepilogati nella tabella.

**Disco del sistema operativo guest** | **Lettera di unità** | **Tipo di dati disco**
--- | --- | ---
Disk0 | C:\ | Disco del sistema operativo.
Disk1 | D:\ | Database di sistema SQL e utente Database1.
Disk2 | G:\ | Database2 utente.


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Macchine virtuali Hyper-V: dischi durante il failback nel percorso originale

Viene ora illustrato come gestire i dischi nelle macchine virtuali Hyper-V quando si esegue il failback al percorso locale originale.

- **Dischi creati in Azure**: i dischi creati manualmente in Azure non vengono replicati nel sito quando si esegue il failback o si protegge nuovamente una macchina virtuale.
- **Disco di archiviazione temporaneo in Azure**: il disco di archiviazione temporanea non viene replicato in host locali.
- **Dischi esclusi**: dopo il failback la configurazione del disco della macchina virtuale corrisponde alla configurazione originale del disco della macchina virtuale. I dischi esclusi dalla replica da Hyper-V ad Azure sono disponibili nella macchina virtuale di failback.

Prima di eseguire il failback delle macchine virtuali Hyper-V nel percorso originale, le impostazioni del disco della macchina virtuale di Azure sono le seguenti.

**Disco del sistema operativo guest** | **Lettera di unità** | **Tipo di dati disco**
--- | --- | ---
Disk0 | C:\ | Disco del sistema operativo.
Disk1 | E:\ | Archiviazione temporanea.
Disk2 | D:\ | Database di sistema SQL e utente Database1.
Disk3 | G:\ | Database2 utente.

Dopo il failover pianificato (failback) da Azure a Hyper-V locale, la macchina virtuale Hyper-V nel percorso originale include i dischi riepilogati nella tabella.

**Nome del disco** | **N. disco del SO guest** | **Lettera di unità** | **Tipo di dati disco**
 --- | --- | --- | ---
DB-Disk0-OS | Disk0 |   C:\ | Disco del sistema operativo.
DB-Disk1 | Disk1 | D:\ | Database di sistema SQL e utente Database1.
DB-Disk2 (disco escluso) | Disk2 | E:\ | File temporanei.
DB-Disk3 (disco escluso) | Disk3 | F:\ | Database tempdb SQL<br/><br/> Percorso cartella (F:\MSSQL\Data\).
DB-Disk4 | Disk4 | G:\ | Database2 dell'utente


## <a name="example-2-exclude-the-paging-file-disk"></a>Esempio 2: escludere il disco del file di paging

Si esaminerà come gestire l'esclusione, il failover e il failover del disco per una VM Windows di origine, per cui si vuole escludere il disco del file Pagefile. sys nell'unità D e un'unità alternativa.


### <a name="paging-file-on-the-d-drive"></a>File di paging nell'unità D

Questi dischi sono disponibili nella macchina virtuale di origine.

**Nome del disco** | **Disco del sistema operativo guest** | **Lettera di unità** | **Tipo di dati disco**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Disco del sistema operativo
DB-disk1 (escluso dalla replica) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Dati utente 1
DB-Disk3 | Disk3 | F:\ | Dati utente 2

Le impostazioni del file di paging nella macchina virtuale di origine sono le seguenti:

![Impostazioni del file di paging nella macchina virtuale di origine](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. Viene abilitata la replica per la macchina virtuale.
2. È escluso DB-disk1 dalla replica.

#### <a name="disks-after-failover"></a>Dischi dopo il failover

Al termine del failover, la macchina virtuale di Azure include i dischi riepilogati nella tabella.

**Nome del disco** | **N. disco sistema operativo guest** | **Lettera di unità** | **Tipo di dati nel disco**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Disco del sistema operativo
DB-Disk1 | Disk1 | D:\ | Archiviazione temporanea/pagefile. sys <br/><br/> Poiché DB-disk1 (D:) è stato escluso, D: è la prima lettera di unità dall'elenco disponibile.<br/><br/> e viene assegnata da Azure al volume di archiviazione temporanea.<br/><br/> Poiché D: è disponibile, l'impostazione del file di paging della VM rimane invariata.
DB-Disk2 | Disk2 | E:\ | Dati utente 1
DB-Disk3 | Disk3 | F:\ | Dati utente 2

Le impostazioni del file di paging nella macchina virtuale di Azure sono le seguenti:

![Impostazioni del file di paging nella macchina virtuale di Azure](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>File di paging in un'altra unità (non D:)

Viene ora esaminato un esempio in cui il file di paging non si trova nell'unità D.  

Questi dischi sono disponibili nella macchina virtuale di origine.

**Nome del disco** | **Disco del sistema operativo guest** | **Lettera di unità** | **Tipo di dati disco**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Disco del sistema operativo
DB-disk1 (escluso dalla replica) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Dati utente 1
DB-Disk3 | Disk3 | F:\ | Dati utente 2

Le impostazioni del file di paging nella macchina virtuale locale sono le seguenti:

![Impostazioni del file di paging nella macchina virtuale locale](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. Viene abilitata la replica per la macchina virtuale.
2. È escluso DB-disk1 dalla replica.

#### <a name="disks-after-failover"></a>Dischi dopo il failover

Al termine del failover, la macchina virtuale di Azure include i dischi riepilogati nella tabella.

**Nome del disco** | **N. disco del SO guest** | **Lettera di unità** | **Tipo di dati disco**
--- | --- | --- | ---
DB-Disk0-OS | Disk0  |C:\ | Disco del sistema operativo
DB-Disk1 | Disk1 | D:\ | Archiviazione temporanea<br/><br/> Poiché D: è la prima lettera di unità disponibile dall'elenco, Azure assegna D: al volume di archiviazione temporanea.<br/><br/> Per tutti i dischi replicati, la lettera di unità rimane invariata.<br/><br/> Poiché il disco G: non è disponibile, il sistema userà l'unità C: per il file di paging.
DB-Disk2 | Disk2 | E:\ | Dati utente 1
DB-Disk3 | Disk3 | F:\ | Dati utente 2

Le impostazioni del file di paging nella macchina virtuale di Azure sono le seguenti:

![Impostazioni del file di paging nella macchina virtuale di Azure](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle linee guida per il disco di archiviazione temporanea:
    - Informazioni [sull'](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) uso di SSD nelle macchine virtuali di Azure per archiviare SQL Server tempdb e le estensioni del pool di buffer
    - [Esaminare](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance) le procedure consigliate per le prestazioni SQL Server nelle VM di Azure.
- Dopo aver configurato correttamente la distribuzione, vedere [altre informazioni](failover-failback-overview.md) sui diversi tipi di failover.

