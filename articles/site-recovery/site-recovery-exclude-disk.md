---
title: Escludere dischi dalla protezione tramite Azure Site Recovery | Microsoft Docs
description: "Descrive come e perché escludere dalla replica uno o più dischi di macchina virtuale per scenari da VMware in Azure e da Hyper-V in Azure."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 1/24/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: af0d66d92ca542f415de779fb638db166ba5f26a
ms.openlocfilehash: 5e0527fb0a41d8892c9e22d6d6d2f252972e69d0
ms.lasthandoff: 02/17/2017


---
#<a name="exclude-disk-from-replication"></a>Escludere dischi dalla replica
Questo articolo illustra come escludere uno o più dischi dalla replica per ottimizzare la larghezza di banda di replica utilizzata o le risorse lato destinazione utilizzate da tali dischi. La funzionalità è supportata per scenari da VMware ad Azure e da Hyper-V ad Azure.

##<a name="prerequisites"></a>Prerequisiti

Per impostazione predefinita, vengono replicati tutti i dischi in un computer. Per escludere un disco dalla replica, è necessario installare manualmente il servizio Mobility nel computer prima di abilitare la replica, se si esegue la replica **da VMware ad Azure**.


## <a name="why-exclude-disks-from-replication"></a>Perché escludere dischi dalla replica
Spesso è necessario escludere dischi dalla replica per i motivi riportati di seguito:

1. I dati di varianza nel disco escluso non sono importanti o non è necessario replicarli.

2. È possibile salvare le risorse di archiviazione e di rete non eseguendo la replica di questa varianza.

##<a name="what-are-the-typical-scenarios"></a>Scenari tipici
È possibile escludere alcuni esempi specifici di varianza dei dati facilmente identificabile, come le scritture nei file di paging, le scritture nel tempdb di Microsoft SQL Server e così via. A seconda del carico di lavoro e del sottosistema di archiviazione, il file di paging può registrare una varianza significativa. Tuttavia, la replica di questo tipo di dati dal sito primario ad Azure richiederebbe un utilizzo intensivo delle risorse. È quindi possibile ottimizzare la replica di una macchina virtuale con un disco virtuale singolo con sistema operativo e file di paging eseguendo queste operazioni:

1. Suddivisione del disco virtuale singolo in due dischi virtuali, uno con il sistema operativo e uno con il file di paging.
2. Esclusione del disco del file di paging dalla replica.

Analogamente, per ottimizzare Microsoft SQL Server con tempdb e file del database di sistema nello stesso disco, è possibile eseguire queste operazioni:

1. Archiviazione del database di sistema e del tempdb in due dischi diversi.
2. Esclusione del disco del tempdb dalla replica.

##<a name="how-to-exclude-disk-from-replication"></a>Come escludere dischi dalla replica

###<a name="vmware-to-azure"></a>Da VMware ad Azure
Per proteggere una macchina virtuale dal portale di Azure Site Recovery, seguire il flusso di lavoro [Abilitare la replica](site-recovery-vmware-to-azure.md#enable-replication). Nel passaggio 4 della sezione Abilitare la replica è presente la colonna **DISCHI DA REPLICARE**, che permette di escludere dischi dalla replica. Per impostazione predefinita, sono selezionati tutti i dischi. Deselezionare il disco che si vuole escludere dalla replica e seguire la procedura per abilitare la replica. 

![Abilitare la replica](./media/site-recovery-exclude-disk/v2a-enable-replication-exclude-disk1.png)
    
    
>[!NOTE]
> 
> * È possibile escludere solo i dischi in cui è già installato il servizio Mobility. È necessario installare manualmente il servizio Mobility perché viene installato solo tramite il meccanismo di push dopo l'abilitazione della replica.
> * Solo i dischi di base possono essere esclusi dalla replica. Non è possibile escludere i dischi del sistema operativo o dinamici.
> * Dopo aver abilitato la replica, non è più possibile aggiungere o rimuovere dischi da replicare. Se si vuole aggiungere o escludere un disco, è necessario disabilitare la protezione per il computer e quindi riabilitarla.
> * Se si esclude un disco necessario per il funzionamento di un'applicazione, dopo il failover in Azure è necessario crearlo manualmente in Azure per consentire l'esecuzione dell'applicazione replicata. In alternativa, è possibile integrare Automazione di Azure in un piano di ripristino per creare il disco durante il failover del computer.
> * Macchine virtuali Windows: per i dischi creati manualmente in Azure non viene eseguito il failback. Ad esempio, se si esegue il failover di tre dischi e se ne creano due direttamente in una macchina virtuale di Azure, viene eseguito il failback soltanto dei tre dischi sottoposti a failover. Non è possibile includere i dischi creati manualmente nel failback o nella riprotezione da locale ad Azure.
> * Macchine virtuali Linux: per i dischi creati manualmente in Azure viene eseguito il failback. Ad esempio, se si esegue il failover di tre dischi e se ne creano due direttamente in Azure, verrà eseguito il failback di tutti e cinque. Non è possibile escludere i dischi creati manualmente dall'operazione di failback.
> 

###<a name="hyper-v-to-azure"></a>Da Hyper-V ad Azure
Per proteggere una macchina virtuale dal portale di Azure Site Recovery, seguire il flusso di lavoro [Abilitare la replica](site-recovery-hyper-v-site-to-azure.md#step-6-enable-replication). Nel passaggio 4 della sezione Abilitare la replica è presente la colonna **DISCHI DA REPLICARE**, che permette di escludere dischi dalla replica. Per impostazione predefinita, tutti i dischi sono selezionati per la replica. Deselezionare il disco che si vuole escludere dalla replica e seguire la procedura per abilitare la replica. 

![Abilitare la replica](./media/site-recovery-vmm-to-azure/enable-replication6-with-exclude-disk.png)
    
>[!NOTE]
> 
> * Solo i dischi di base possono essere esclusi dalla replica. Non è possibile escludere il disco del sistema operativo e non è consigliabile escludere i dischi dinamici. ASR non è in grado di identificare quale disco rigido virtuale è un disco di base o un disco dinamico all'interno della macchina virtuale guest.  Se non vengono esclusi tutti i volumi dinamici dipendenti, in caso di failover della macchina virtuale il disco dinamico protetto risulta in stato di errore e non è possibile accedere ai dati in esso contenuti.    
> * Dopo aver abilitato la replica, non è più possibile aggiungere o rimuovere dischi da replicare. Se si vuole aggiungere o escludere un disco, è necessario disabilitare la protezione per la macchina virtuale e quindi riabilitarla.
> * Se si esclude un disco necessario per il funzionamento di un'applicazione, dopo il failover in Azure è necessario crearlo manualmente in Azure per consentire l'esecuzione dell'applicazione replicata. In alternativa, è possibile integrare Automazione di Azure in un piano di ripristino per creare il disco durante il failover del computer.
> * Per i dischi creati manualmente in Azure non viene eseguito il failback. Ad esempio, se si esegue il failover di tre dischi e se ne creano due direttamente in Azure, verrà eseguito il failback da Azure a Hyper-V soltanto di tre dischi. Non è possibile includere nel failback o nella replica inversa da Hyper-V ad Azure i dischi creati manualmente.
 


##<a name="end-to-end-scenarios-of-exclude-disks"></a>Scenari end-to-end di esclusione dei dischi
Per comprendere meglio la funzionalità di esclusione dischi, si prendano in considerazione i due scenari seguenti:

1. Disco del tempdb di SQL Server
2. Disco del file di paging

###<a name="excluding-the-sql-server-tempdb-disk"></a>Esclusione del disco del tempdb di SQL Server
Si prenda ad esempio una macchina virtuale di SQL Server con un tempdb che è possibile escludere.

Nome della macchina virtuale: SalesDB Dischi nella macchina virtuale di origine:


**Nome del disco** | **N. disco del SO guest** | **Lettera di unità** | **Tipo di dati nel disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disco del sistema operativo
DB-Disk1| Disk1 | D:\ | Database di sistema SQL e Database1 dell'utente
DB-Disk2 (disco escluso dalla protezione) | Disk2 | E:\ | File temporanei
DB-Disk3 (disco escluso dalla protezione) | Disk3 | F:\ | Database tempdb di SQL (percorso della cartella, F:\MSSQL\Data\), annotare il percorso della cartella prima del failover
DB-Disk4 | Disk4 |G:\ |Database2 dell'utente

Data la natura temporanea della varianza dei dati nei due dischi della macchina virtuale, escludere Disk2 e Disk3 dalla replica quando si protegge la macchina virtuale SalesDB. Azure Site Recovery non esegue la replica di tali dischi, che durante il failover non sono presenti nella macchina virtuale di failover in Azure.

Dischi nella macchina virtuale di Azure dopo il failover:

**N. disco del SO guest** | **Lettera di unità** | **Tipo di dati nel disco**
--- | --- | ---
DISK0 |    C:\ | Disco del sistema operativo
Disk1 |    E:\ | Archiviazione temporanea [Azure aggiunge questo disco e assegna la prima lettera di unità disponibile]
Disk2 | D:\ | Database di sistema SQL e Database1 dell'utente
Disk3 | G:\ | Database2 dell'utente

Dato che Disk2 e Disk3 sono stati esclusi dalla macchina virtuale SalesDB, la prima lettera di unità disponibile è E:. Azure assegna la lettera E: al volume di archiviazione temporanea. Per tutti i dischi replicati, la lettera di unità rimane invariata.

Disk3 era il disco del tempdb di SQL (percorso della cartella del tempdb, F:\MSSQL\Data\) ed è stato escluso dalla replica, quindi non è disponibile nella macchina virtuale di failover. Di conseguenza, il servizio SQL è nello stato arrestato e necessita del percorso F:\MSSQL\Data.

Per creare questo percorso, è possibile procedere in due modi:

1. Aggiungere un nuovo disco e assegnare il percorso della cartella del tempdb.
2. Usare il disco di archiviazione temporanea esistente per il percorso della cartella del tempdb.

####<a name="add-a-new-disk"></a>Aggiungere un nuovo disco:

1. Annotare il percorso di tempdb.mdf e tempdb.ldf di SQL prima del failover.
2. Dal portale di Azure aggiungere un nuovo disco alla macchina virtuale di failover con dimensioni pari o superiori a quelle del disco di origine del tempdb SQL, Disk3.
3. Accedere alla macchina virtuale di Azure. Dalla console di gestione del disco, diskmgmt.msc, inizializzare e formattare il disco appena aggiunto.
4. Assegnare la stessa lettera di unità usata dal disco del tempdb SQL, in questo caso F:.
5. Creare la cartella del tempdb nel volume F: (F:\MSSQL\Data).
6. Avviare il servizio SQL dalla console del servizio.

####<a name="use-existing-temporary-storage-disk-for-sql-tempdb-folder-path"></a>Usare il disco di archiviazione temporanea esistente per il percorso della cartella del tempdb SQL:

1. Aprire una console della riga di comando.
2. Eseguire SQL Server in modalità di ripristino dalla console della riga di comando.

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

Vedere le linee guida di Azure riportate di seguito per il disco di archiviazione temporanea:

* Using SSDs in Azure VMs to store SQL Server TempDB and Buffer Pool Extensions (Uso di SSD in Macchine virtuali di Azure per archiviare estensioni del pool di buffer e tempdb di SQL Server)
* Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure

###<a name="failback-from-azure-to-on-premises"></a>Failback da Azure a locale
Questa sezione illustra quali dischi vengono replicati quando si esegue il failover da Azure all'host Hyper-V o VMware in locale. Per i dischi creati manualmente in Azure non viene eseguita la replica. Ad esempio, se si esegue il failover di tre dischi e se ne creano due direttamente in Azure, verrà eseguito il failback soltanto di tre dischi. Non è possibile includere i dischi creati manualmente nel failback o nella riprotezione da locale ad Azure. Non viene eseguita neppure la replica in locale del disco di archiviazione temporanea.

####<a name="failback-to-original-location-recovery-olr"></a>Failback nel ripristino del percorso originale

Configurazione del disco della macchina virtuale di Azure nell'esempio precedente:

**N. disco del SO guest** | **Lettera di unità** | **Tipo di dati nel disco** 
--- | --- | --- 
DISK0 | C:\ | Disco del sistema operativo
Disk1 |    E:\ | Archiviazione temporanea [Azure aggiunge questo disco e assegna la prima lettera di unità disponibile]
Disk2 |    D:\ | Database di sistema SQL e Database1 dell'utente
Disk3 |    G:\ | Database2 dell'utente


####<a name="vmware-to-azure"></a>Da VMware ad Azure
Al termine il failback nel percorso originale, la configurazione del disco della macchina virtuale di failback non prevede l'esclusione di dischi. Ciò significa che i dischi esclusi dalla replica da VMware ad Azure non saranno disponibili nella macchina virtuale di failback. 

Dopo il failover pianificato da Azure a VMware in locale, i dischi della macchina virtuale VMWare (percorso originale) avranno la configurazione seguente:

**N. disco del SO guest** | **Lettera di unità** | **Tipo di dati nel disco** 
--- | --- | --- 
DISK0 | C:\ | Disco del sistema operativo
Disk1 |    D:\ | Database di sistema SQL e Database1 dell'utente
Disk2 |    G:\ | Database2 dell'utente

####<a name="hyper-v-to-azure"></a>Da Hyper-V ad Azure
Al termine del failback nel percorso originale, la configurazione del disco della macchina virtuale di failback rimane uguale a quella della macchina virtuale originale per Hyper-V. Ciò significa che i dischi esclusi dalla replica dal sito Hyper-V ad Azure saranno disponibili nella macchina virtuale di failback.

Dopo il failover pianificato da Azure a Hyper-V in locale, i dischi della macchina virtuale Hyper-V (percorso originale) avranno la configurazione seguente:

**Nome del disco** | **N. disco del SO guest** | **Lettera di unità** | **Tipo di dati nel disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 |    C:\ | Disco del sistema operativo
DB-Disk1 | Disk1 | D:\ | Database di sistema SQL e Database1 dell'utente
DB-Disk2 (disco escluso) | Disk2 | E:\ | File temporanei
DB-Disk3 (disco escluso) | Disk3 | F:\ | Database tempdb SQL (percorso della cartella, F:\MSSQL\Data\)
DB-Disk4 | Disk4 | G:\ | Database2 dell'utente


####<a name="exclude-paging-file-disk"></a>Escludere il disco del file di paging

Si prenda ad esempio una macchina virtuale con un disco del file di paging che è possibile escludere.
I casi possibili sono due:

####<a name="case-1-pagefile-is-configured-on-the-d-drive"></a>Caso 1: file di paging configurato nell'unità D:
Configurazione dei dischi:


**Nome del disco** | **N. disco del SO guest** | **Lettera di unità** | **Tipo di dati nel disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disco del sistema operativo
DB-Disk1 (disco escluso dalla protezione) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Dati utente 1
DB-Disk3 | Disk3 | F:\ | Dati utente 2

Impostazioni del file di paging nella macchina virtuale di origine:

![Abilitare la replica](./media/site-recovery-exclude-disk/pagefile-on-d-drive-sourceVM.png)
    

Dopo il failover della macchina virtuale da VMware ad Azure o da Hyper-V ad Azure, i dischi nella macchina virtuale di Azure hanno la configurazione seguente:
**Nome del disco** | **N. disco del SO guest** | **Lettera di unità** | **Tipo di dati nel disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disco del sistema operativo
DB-Disk1 | Disk1 | D:\ | Archiviazione temporanea – > pagefile.sys
DB-Disk2 | Disk2 | E:\ | Dati utente 1
DB-Disk3 | Disk3 | F:\ | Dati utente 2

Dal momento che Disk1 (D:) è stato escluso, D: è la prima lettera di unità disponibile e viene assegnata da Azure al volume di archiviazione temporanea.  L'unità D: è disponibile nella macchina virtuale di Azure, quindi le impostazioni del file di paging della macchina virtuale rimangono invariate.

Impostazioni del file di paging nella macchina virtuale di Azure:

![Abilitare la replica](./media/site-recovery-exclude-disk/pagefile-on-Azure-vm-after-failover.png)

####<a name="case-2-pagefile-file-is-configured-on-any-other-driveother-than-d-drive"></a>Caso 2: file di paging configurata in qualsiasi altra unità diversa dall'unità D:

Configurazione dei dischi della macchina virtuale di origine:

**Nome del disco** | **N. disco del SO guest** | **Lettera di unità** | **Tipo di dati nel disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disco del sistema operativo
DB-Disk1 (disco escluso dalla protezione) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Dati utente 1
DB-Disk3 | Disk3 | F:\ | Dati utente 2

Impostazioni del file di paging nella macchina virtuale locale:

![Abilitare la replica](./media/site-recovery-exclude-disk/pagefile-on-g-drive-sourceVM.png)

Dopo il failover della macchina virtuale da VMware ad Azure o da Hyper-V ad Azure, i dischi nella macchina virtuale di Azure hanno la configurazione seguente:

**Nome del disco**| **N. disco del SO guest**| **Lettera di unità** | **Tipo di dati nel disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0  |C:\ |Disco del sistema operativo
DB-Disk1 | Disk1 | D:\ | Archiviazione temporanea – > pagefile.sys
DB-Disk2 | Disk2 | E:\ | Dati utente 1
DB-Disk3 | Disk3 | F:\ | Dati utente 2

Dal momento che D: è la prima lettera di unità disponibile dall'elenco, viene assegnata da Azure al volume di archiviazione temporanea. Per tutti i dischi replicati, la lettera di unità rimane invariata. Dato che il disco G: non è disponibile, il sistema usa l'unità C: per file di paging.

Impostazioni del file di paging nella macchina virtuale di Azure:

![Abilitare la replica](./media/site-recovery-exclude-disk/pagefile-on-Azure-vm-after-failover-2.png)

## <a name="next-steps"></a>Passaggi successivi
Dopo aver configurato correttamente la distribuzione, vedere [altre informazioni](site-recovery-failover.md) sui diversi tipi di failover.

