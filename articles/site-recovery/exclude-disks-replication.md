---
title: Escludere dischi dalla replica con Azure Site RecoveryExclude disks from replication with Azure Site Recovery
description: Come escludere i dischi dalla replica in Azure con Azure Site Recovery.How to exclude disks from replication to Azure with Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 57bf06f0fde85714530c06cbd008db08de7460d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281847"
---
# <a name="exclude-disks-from-disaster-recovery"></a>Escludere i dischi dal ripristino di emergenzaExclude disks from disaster recovery

Questo articolo descrive come escludere i dischi dalla replica durante il ripristino di emergenza da locale ad Azure con [Azure Site Recovery.](site-recovery-overview.md) È possibile escludere i dischi dalla replica per diversi motivi:You might exclude disks from replication for a number of reasons:

- Pertanto, i dati non importanti sfornati sul disco escluso non vengono replicati.
- Per ottimizzare la larghezza di banda di replica utilizzata o le risorse sul lato destinazione.
- Per salvare le risorse di archiviazione e di rete non replicando i dati non necessari.
- Le macchine virtuali di Azure hanno raggiunto i limiti di replica di Site Recovery.Azure VMs have reached Site Recovery replication limits.


## <a name="supported-scenarios"></a>Scenari supportati

È possibile escludere i dischi dalla replica come riepilogati nella tabella.

**Da Azure ad Azure** | **Da VMware ad Azure** | **Da Hyper-V ad Azure** 
--- | --- | ---
Sì (tramite PowerShell) | Sì | Sì 

## <a name="exclude-limitations"></a>Escludere le limitazioni

**Limitazione** | **Macchine virtuali di AzureAzure VMs** | **VM VMware** | **VM Hyper-V**
--- | --- | ---
**Tipi di disco** | È possibile escludere i dischi di base dalla replica.<br/><br/> Non è possibile escludere dischi del sistema operativo o dischi dinamici. I dischi temporanei sono esclusi per impostazione predefinita. | È possibile escludere i dischi di base dalla replica.<br/><br/> Non è possibile escludere dischi del sistema operativo o dischi dinamici. | È possibile escludere i dischi di base dalla replica.<br/><br/> Non è possibile escludere dischi del sistema operativo e non è consigliabile escludere dischi dinamici. Site Recovery non è in grado di identificare quale VHS è di base o dinamico nella macchina virtuale guest. Se tutti i dischi dinamici del volume dipendenti non vengono esclusi, il disco dinamico protetto diventa un disco danneggiato in una macchina virtuale di failover e i dati in tale disco non sono accessibili.
**Server di replica** | Non è possibile escludere un disco in replica.<br/><br/> Disabilitare e riattivare la replica per la macchina virtuale. |  Non è possibile escludere un disco in replica. |  Non è possibile escludere un disco in replica.
**Servizio Mobility (VMware)** | Non rilevante | È possibile escludere i dischi solo nelle macchine virtuali in cui è installato il servizio Mobility.You can exclude disks only on VMs that have the Mobility service installed.<br/><br/> Ciò significa che è necessario installare manualmente il servizio Mobility nelle macchine virtuali per cui si desidera escludere i dischi. Non è possibile usare il meccanismo di installazione push perché installa il servizio Mobility solo dopo l'abilitazione della replica. | Non è rilevante.
**Aggiungi/Rimuovi** | È possibile aggiungere e rimuovere dischi nelle macchine virtuali di Azure con dischi gestiti. | Non è possibile aggiungere o rimuovere dischi dopo l'abilitazione della replica. Disabilitare e quindi riattivare la replica per aggiungere un disco. | Non è possibile aggiungere o rimuovere dischi dopo l'abilitazione della replica. Disabilitare e riattivare la replica.
**Failover** | Se un'app richiede un disco escluso, dopo il failover è necessario creare il disco manualmente in modo che l'app replicata possa essere eseguita.<br/><br/> In alternativa, è possibile creare il disco durante il failover della macchina virtuale, integrando l'automazione di Azure in un piano di ripristino. | Se si esclude un disco necessario per un'app, crearlo manualmente in Azure dopo il failover. | Se si esclude un disco necessario per un'app, crearlo manualmente in Azure dopo il failover.
**Dischi di failback locali creati manualmente** | Non rilevante | **Macchine virtuali Windows:** non viene eseguito il failback dei dischi creati manualmente in Azure.Windows VMs : Disks created manually in Azure are't fail back. Ad esempio, se si esegue il failover di tre dischi e si creano due dischi direttamente in una macchina virtuale di Azure, viene eseguito il failback solo dei tre dischi di cui è stato eseguito il failover.<br/><br/> **Macchine virtuali Linux:** viene eseguito il failback dei dischi creati manualmente in Azure.Linux VMs : Disks created manually in Azure are fail back. Ad esempio, se si esegue il failover di tre dischi e si creano due dischi in una macchina virtuale di Azure, verrà eseguito il failback di tutti e cinque. Non è possibile escludere i dischi creati manualmente dall'operazione di failback. | I dischi creati manualmente in Azure non vengono eseguito il failback. Ad esempio, se si esegue il failover di tre dischi e si creano due dischi direttamente in una macchina virtuale di Azure, verrà eseguito il failback solo di tre dischi di cui è stato eseguito il failover.
**Dischi di failback locale esclusi** | Non rilevante | Se si esegue il failback al computer originale, la configurazione del disco della macchina virtuale di failback non include i dischi esclusi. I dischi esclusi da VMware alla replica di Azure non sono disponibili nella macchina virtuale di failback. | Quando il failback è al percorso Hyper-V originale, la configurazione del disco della macchina virtuale di failback rimane uguale a quella del disco della macchina virtuale di origine. I dischi esclusi dal sito Hyper-V alla replica di Azure sono disponibili nella macchina virtuale di failback.



## <a name="typical-scenarios"></a>Scenari tipici

Esempi di varianza di dati che sono ottimi candidati per l'esclusione includono le scritture in un file di paging (pagefile.sys) e le scritture nel file tempdb di Microsoft SQL Server. A seconda del carico di lavoro e del sottosistema di archiviazione, i file di paging e tempdb possono registrare una quantità significativa di varianza. La replica di questo tipo di dati in Azure richiede un utilizzo intensivo delle risorse.

- Per ottimizzare la replica per una macchina virtuale con un singolo disco virtuale che include sia il sistema operativo che il file di paging, è possibile:To optimize replication for a VM with a single virtual disk that includes both the operating system and the paging file, you could:
    1. Suddividere il singolo disco virtuale in due dischi virtuali, uno con il sistema operativo e l'altro con il file di paging.
    2. Escludere il disco del file di paging dalla replica.

- Per ottimizzare la replica per un disco che include sia il file tempdb di Microsoft SQL Server che il file di database di sistema, è possibile:
    1. Archiviare il database di sistema e il tempdb in due dischi distinti.
    2. Escludere il disco del tempdb dalla replica.

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>Esempio 1: Escludere il disco del tempdb di SQL Server

Diamo un'occhiata a come gestire l'esclusione del disco, il failover e il failover per una macchina virtuale windows di SQL Server SQL Server di origine, ovvero il server di distribuzione di SQL Server, per il quale si vuole escludere tempdb. 

### <a name="exclude-disks-from-replication"></a>Escludere dischi dalla replica

Questi dischi sono disponibili nel database vendite di Windows VM di origine.

**Nome del disco** | **Disco del sistema operativo guest** | **Lettera di unità** | **Tipo di dati del disco**
--- | --- | --- | ---
DB-Disk0-OS | Disco0 | C:\ | Disco del sistema operativo.
DB-Disk1| Disk1 | D:\ | database di sistema SQL e Database utente1.
DB-Disk2 (disco escluso dalla protezione) | Disk2 | E:\ | File temporanei.
DB-Disk3 (disco escluso dalla protezione) | Disk3 | F:\ | Database tempdb SQL.<br/><br/> Percorso della cartella - F: Prendere nota del percorso della cartella prima del failover.
DB-Disk4 | Disk4 |G:\ | Database2 dell'utente

1. Viene abilitata la replica per la macchina virtuale SalesDB.We enable replication for the SalesDB VM.
2. Escludiamo Disk2 e Disk3 dalla replica perché la varianza dei dati su tali dischi è temporanea. 


### <a name="handle-disks-during-failover"></a>Gestire i dischi durante il failover

Poiché i dischi non vengono replicati, quando si esegue il failover in Azure questi dischi non sono presenti nella macchina virtuale di Azure creata dopo il failover. La macchina virtuale di Azure contiene i dischi riepilogati in questa tabella.

**Disco del sistema operativo guest** | **Lettera di unità** | **Tipo di dati del disco**
--- | --- | ---
Disco0 | C:\ | Disco del sistema operativo.
Disk1 | E:\ | Archiviazione temporanea<br/><br/>Azure aggiunge questo disco. Poiché Disk2 e Disk3 sono stati esclusi dalla replica, E: è la prima lettera di unità dall'elenco disponibile. Azure assegna quindi la lettera E: al volume di archiviazione temporanea. Le altre lettere di unità per i dischi replicati rimangono invariate.
Disk2 | D:\ | Database di sistema SQL e Database1 dell'utente
Disk3 | G:\ | Database2 dell'utente

Nel nostro esempio, poiché Disk3, il disco tempdb SQL, è stato escluso dalla replica e non è disponibile nella macchina virtuale di Azure, il servizio SQL è in stato di arresto e richiede il percorso F: È possibile creare questo percorso in due modi:You can create this path in a couple of ways: 

- Aggiungere un nuovo disco dopo il failover e assegnare il percorso della cartella tempdb.
- Usare un disco di archiviazione temporanea esistente per il percorso della cartella del tempdb.

#### <a name="add-a-new-disk-after-failover"></a>Aggiungere un nuovo disco dopo il failoverAdd a new disk after failover

1. Prima del failover, annotare i percorsi di tempdb.mdf e tempdb.ldf di SQL.
2. Dal portale di Azure aggiungere un nuovo disco alla macchina virtuale di Azure di failover. Il disco deve avere le stesse dimensioni (o più grandi) del disco tempdb SQL di origine (Disk3).
3. Accedere alla macchina virtuale di Azure.Sign in to the Azure VM.
4. Dalla console di gestione del disco, diskmgmt.msc, inizializzare e formattare il disco appena aggiunto.
5. Assegnare la stessa lettera di unità utilizzata dal disco TEMPdb SQL (F:)
6. Creare una cartella del tempdb nel volume F: (F:\MSSQL\Data).
7. Avviare il servizio SQL dalla console del servizio.

#### <a name="use-an-existing-temporary-storage-disk"></a>Utilizzare un disco di archiviazione temporanea esistente 

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



### <a name="vmware-vms-disks-during-failback-to-original-location"></a>Macchine virtuali VMware: dischi durante il failback nel percorso originaleVMware VMware VMs: Disks during failback to original location

Vediamo ora come gestire i dischi nelle macchine virtuali VMware quando si esegue il failback alla posizione locale originale.

- **Dischi creati in Azure:** poiché nell'esempio viene usata una macchina virtuale Windows, i dischi creati manualmente in Azure non vengono replicati nel sito quando si esegue il failback o si riprotegge una macchina virtuale.
- **Disco di archiviazione temporaneo in Azure:** il disco di archiviazione temporaneo non viene replicato negli host locali.
- **Dischi esclusi: i dischi esclusi**dalla replica da VMware ad Azure non sono disponibili nella macchina virtuale locale dopo il failback.

Prima di eseguire il failback delle macchine virtuali VMware nel percorso originale, le impostazioni del disco della macchina virtuale di Azure sono le seguenti.

**Disco del sistema operativo guest** | **Lettera di unità** | **Tipo di dati del disco**
--- | --- | ---
Disco0 | C:\ | Disco del sistema operativo.
Disk1 | E:\ | Archiviazione temporanea.
Disk2 | D:\ | database di sistema SQL e Database utente1.
Disk3 | G:\ | Database utente2.

Dopo il failback, la macchina virtuale VMware nella posizione originale contiene i dischi riepilogati nella tabella.

**Disco del sistema operativo guest** | **Lettera di unità** | **Tipo di dati del disco**
--- | --- | ---
Disco0 | C:\ | Disco del sistema operativo.
Disk1 | D:\ | database di sistema SQL e Database utente1.
Disk2 | G:\ | Database utente2.


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Macchine virtuali Hyper-V: dischi durante il failback nel percorso originale

Vediamo ora come gestire i dischi nelle macchine virtuali Hyper-V quando si esegue il failback alla posizione locale originale.

- **I dischi creati in Azure:** i dischi creati manualmente in Azure non vengono replicati nel sito quando si esegue il failback o si riprotegge una macchina virtuale.
- **Disco di archiviazione temporaneo in Azure:** il disco di archiviazione temporaneo non viene replicato negli host locali.
- **Dischi esclusi:** dopo il failback la configurazione del disco della macchina virtuale è la stessa della configurazione del disco della macchina virtuale originale. I dischi esclusi dalla replica da Hyper-V ad Azure sono disponibili nella macchina virtuale di failback.

Prima di eseguire il failback delle macchine virtuali Hyper-V nel percorso originale, le impostazioni del disco della macchina virtuale di Azure sono le seguenti.

**Disco del sistema operativo guest** | **Lettera di unità** | **Tipo di dati del disco**
--- | --- | ---
Disco0 | C:\ | Disco del sistema operativo.
Disk1 | E:\ | Archiviazione temporanea.
Disk2 | D:\ | database di sistema SQL e Database utente1.
Disk3 | G:\ | Database utente2.

Dopo il failover pianificato (failback) da Azure a Hyper-V locale, la macchina virtuale Hyper-V nella posizione originale include i dischi riepilogati nella tabella.

**Nome disco** | **N. disco del SO guest** | **Lettera di unità** | **Tipo di dati del disco**
 --- | --- | --- | ---
DB-Disk0-OS | Disco0 |   C:\ | Disco del sistema operativo.
DB-Disk1 | Disk1 | D:\ | database di sistema SQL e Database utente1.
DB-Disk2 (disco escluso) | Disk2 | E:\ | File temporanei.
DB-Disk3 (disco escluso) | Disk3 | F:\ | Database di TEMPdb SQL<br/><br/> Percorso della cartella (F:.MSSQL .\)
DB-Disk4 | Disk4 | G:\ | Database2 dell'utente


## <a name="example-2-exclude-the-paging-file-disk"></a>Esempio 2: Escludere il disco del file di pagingExample 2: Exclude the paging file disk

Esaminiamo come gestire l'esclusione del disco, il failover e il failover per una macchina virtuale Windows di origine, per la quale si vuole escludere il disco del file pagefile.sys sia nell'unità D che in un'unità alternativa.


### <a name="paging-file-on-the-d-drive"></a>File di paging nell'unità D

Questi dischi sono presenti nella macchina virtuale di origine.

**Nome del disco** | **Disco del sistema operativo guest** | **Lettera di unità** | **Tipo di dati del disco**
--- | --- | --- | ---
DB-Disk0-OS | Disco0 | C:\ | Disco del sistema operativo
DB-Disk1 (Escluso dalla replica) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Dati utente 1
DB-Disk3 | Disk3 | F:\ | Dati utente 2

Le impostazioni del file di paging nella macchina virtuale di origine sono le seguenti:Our paging file settings on the source VM are as follows:

![Impostazioni del file di paging nella macchina virtuale di origine](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. È abilitata la replica per la macchina virtuale.
2. È possibile escludere DB-Disk1 dalla replica.

#### <a name="disks-after-failover"></a>Dischi dopo il failover

Dopo il failover, la macchina virtuale di Azure include i dischi riepilogati nella tabella.

**Nome del disco** | **N. disco sistema operativo guest** | **Lettera di unità** | **Tipo di dati nel disco**
--- | --- | --- | ---
DB-Disk0-OS | Disco0 | C:\ | Disco del sistema operativo
DB-Disk1 | Disk1 | D:\ | Archiviazione temporanea/filedipagina.sys <br/><br/> Poiché DB-Disk1 (D:) è stata esclusa, D: è la prima lettera di unità dall'elenco disponibile.<br/><br/> e viene assegnata da Azure al volume di archiviazione temporanea.<br/><br/> Poiché D: è disponibile, l'impostazione del file di paging della macchina virtuale rimane invariata).
DB-Disk2 | Disk2 | E:\ | Dati utente 1
DB-Disk3 | Disk3 | F:\ | Dati utente 2

Le impostazioni del file di paging nella macchina virtuale di Azure sono le seguenti:Our paging file settings on the Azure VM are as follows:

![Impostazioni del file di paging nella macchina virtuale di Azure](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>File di paging su un'altra unità (non D:)

Esaminiamo l'esempio in cui il file di paging non si è nell'unità D.  

Questi dischi sono presenti nella macchina virtuale di origine.

**Nome del disco** | **Disco del sistema operativo guest** | **Lettera di unità** | **Tipo di dati del disco**
--- | --- | --- | ---
DB-Disk0-OS | Disco0 | C:\ | Disco del sistema operativo
DB-Disk1 (Escluso dalla replica) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Dati utente 1
DB-Disk3 | Disk3 | F:\ | Dati utente 2

Le impostazioni del file di paging nella macchina virtuale locale sono le seguenti:Our paging file settings on the on-premises VM are as follows:

![Impostazioni del file di paging nella macchina virtuale locale](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. È abilitata la replica per la macchina virtuale.
2. È possibile escludere DB-Disk1 dalla replica.

#### <a name="disks-after-failover"></a>Dischi dopo il failover

Dopo il failover, la macchina virtuale di Azure include i dischi riepilogati nella tabella.

**Nome del disco** | **N. disco del SO guest** | **Lettera di unità** | **Tipo di dati del disco**
--- | --- | --- | ---
DB-Disk0-OS | Disco0  |C:\ | Disco del sistema operativo
DB-Disk1 | Disk1 | D:\ | Archiviazione temporanea<br/><br/> Poiché D: è la prima lettera di unità disponibile dall'elenco, Azure assegna D: al volume di archiviazione temporanea.<br/><br/> Per tutti i dischi replicati, la lettera di unità rimane invariata.<br/><br/> Poiché il disco G: non è disponibile, il sistema utilizzerà l'unità C: per il file di paging.
DB-Disk2 | Disk2 | E:\ | Dati utente 1
DB-Disk3 | Disk3 | F:\ | Dati utente 2

Le impostazioni del file di paging nella macchina virtuale di Azure sono le seguenti:Our paging file settings on the Azure VM are as follows:

![Impostazioni del file di paging nella macchina virtuale di Azure](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle linee guida per il disco di archiviazione temporaneo:Learn more about guidelines for the temporary storage disk:
    - [Informazioni sull'uso](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) di SSD nelle macchine virtuali di Azure per archiviare SQL Server TempDB e le estensioni del pool di bufferLearn about using SSDs in Azure VMs to store SQL Server TempDB and Buffer Pool Extensions
    - [Esaminare](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance) le procedure consigliate per le prestazioni per SQL Server nelle macchine virtuali di Azure.Review performance best practices for SQL Server in Azure VMs.
- Dopo aver configurato correttamente la distribuzione, vedere [altre informazioni](failover-failback-overview.md) sui diversi tipi di failover.

