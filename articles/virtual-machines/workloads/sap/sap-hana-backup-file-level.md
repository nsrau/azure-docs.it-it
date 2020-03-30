---
title: Backup di SAP HANA di Azure a livello di file | Documentazione Microsoft
description: Esistono due opzioni principali per il backup di SAP HANA su macchine virtuali di Azure e questo articolo descrive il backup di SAP HANA di Azure a livello di file.
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 93b67936166eb73db5e9a15db42c2c6135794108
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271392"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>Backup di SAP HANA di Azure a livello di file

## <a name="introduction"></a>Introduzione

Questo articolo è un articolo correlato alla guida di [backup per SAP HANA in Macchine virtuali](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)di Azure , che fornisce una panoramica e informazioni su come iniziare e altri dettagli sul servizio Backup di Azure e sugli snapshot di archiviazione. 

Diversi tipi di VM in Azure consentono un numero diverso di dischi rigidi virtuali associati. I dettagli esatti sono documentati in [Dimensioni delle macchine virtuali Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes). Per i test a cui si fa riferimento in questa documentazione è stata usata una macchina virtuale di Azure GS5 che consente 64 dischi dati collegati. Per sistemi SAP HANA di grandi dimensioni, un numero significativo di dischi potrebbe essere già usato per i file di log e di dati, possibilmente in combinazione con striping di software per la velocità effettiva IO ottimale dei dischi. Per altre informazioni sulle configurazioni del disco consigliate per le distribuzioni SAP HANA nelle macchine virtuali di Azure, vedere l'articolo Configurazioni di [archiviazione delle macchine virtuali SAP HANA Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) Gli elementi consigliati includono le raccomandazioni di spazio su disco per i backup locali.

Il metodo standard per gestire il backup e il ripristino a livello di file è attraverso un backup basato su file, usando SAP HANA Studio o le istruzioni SQL SAP HANA. Per ulteriori informazioni, leggere l'articolo [SAP HANA SQL and System Views Reference](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf).

![Nella figura viene mostrata la finestra di dialogo della voce di menu del backup in SAP HANA Studio](media/sap-hana-backup-file-level/backup-menue-dialog.png)

Nella figura viene mostrata la finestra di dialogo della voce di menu del backup in SAP HANA Studio. Quando si sceglie il &quot;file&quot; del tipo, esso deve specificare un percorso nel file system in cui SAP HANA scrive i file di backup. La funzione di ripristino funziona nello stesso modo.

Sebbene questa scelta sia semplice e diretta, è opportuno presentare alcune considerazioni. Una macchina virtuale di Azure ha una limitazione del numero di dischi dati che possono essere collegati. Potrebbe non essere disponibile spazio per archiviare i file di backup di SAP HANA nei file system della macchina virtuale, a seconda delle dimensioni del database e dei requisiti di velocità effettiva del disco, che potrebbe comportare lo striping di software su più dischi di dati. Più avanti in questo articolo vengono fornite varie opzioni per lo spostamento di questi file di backup e per la gestione delle limitazioni relative alle dimensioni dei file e delle prestazioni durante la gestione di terabyte di dati.

Un'altra opzione, che offre più libertà in relazione alla capacità totale, è l'Archiviazione BLOB di Azure. Mentre un singolo BLOB è limitato a 1 TB, la capacità totale di un singolo contenitore di BLOB è attualmente pari a 500 TB. In aggiunta, questa opzione offre ai clienti la possibilità di selezionare la cosiddetta archiviazione BLOB ad &quot;accesso sporadico&quot;, che presenta vantaggi sui costi. Per informazioni dettagliate sull'archiviazione BLOB cool, vedere Archiviazione BLOB di Azure: livelli di [accesso a caldo, ad accesso a freddo e ad archivio.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal)

Per una maggiore sicurezza, usare un account di archiviazione con replica geografica per archiviare i backup di SAP HANA. Per informazioni dettagliate sulla ridondanza dell'archiviazione e sulla replica dell'archiviazione, [vedere Ridondanza di Archiviazione di Azure.See Azure Storage redundancy](https://docs.microsoft.com/azure/storage/common/storage-redundancy) for details about storage redundancy and storage replication.

È possibile posizionare dischi rigidi virtuali dedicati per i backup di SAP HANA in un account di archiviazione di backup apposito con replica geografica. È anche possibile copiare i dischi rigidi virtuali che mantengono i backup di SAP HANA in un account di archiviazione con replica geografica o in un account di archiviazione in un'area diversa.

## <a name="azure-blobxfer-utility-details"></a>Dettagli sull'utilità di blobxfer di Azure

Per archiviare le directory e i file in archiviazione di Azure, è possibile usare PowerShell, l'interfaccia della riga di comando o sviluppare uno strumento usando uno degli [SDK di Azure](https://azure.microsoft.com/downloads/). È inoltre disponibile un'utilità pronta all'uso, AzCopy, per la copia dei dati nell'archiviazione di Azure.There is also a ready-to-use utility, AzCopy, for copying data to Azure storage. Consultate [Trasferire dati con l'utilità della riga di comando AzCopy](../../../storage/common/storage-use-azcopy.md).

Perciò, Blobxfer è stato pertanto usato per la copia dei file di backup di SAP HANA. Si tratta di una risorsa open source, usata da molti clienti negli ambienti di produzione e disponibile su [GitHub](https://github.com/Azure/blobxfer). Questo strumento consente di copiare i dati direttamente nell'archiviazione BLOB di Azure o nella condivisione file di Azure. Offre anche una gamma di funzionalità utili, come l'hash md5 o il parallelismo automatico durante la copia di una directory con più file.

## <a name="sap-hana-backup-performance"></a>Prestazioni del backup SAP HANA
In questo capitolo vengono illustrate le considerazioni sulle prestazioni. I numeri ottenuti potrebbero non rappresentare lo stato più recente poiché è in corso uno sviluppo costante per ottenere una migliore velocità effettiva nell'archiviazione di Azure.The numbers achieved may not represent most recent state since there is steady development to achieve better throughput to Azure storage. Di conseguenza, è necessario eseguire singoli test per la configurazione e l'area di Azure.As a result, you should perform individual tests for your configuration and Azure region.

![Questa schermata appartiene alla console di backup di SAP HANA in SAP HANA Studio](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

Questa schermata mostra la console di backup SAP HANA di SAP HANA Studio. Sono stati impiegati circa 42 minuti per eseguire un backup di 230 GB su un singolo disco di archiviazione HDD Standard di Azure collegato alla macchina virtuale HANA usando il file system XFS su un disco.

![Questa schermata è tratta da YaST nella macchina virtuale di test di SAP HANA](media/sap-hana-backup-file-level/one-backup-disk.png)

Questa schermata è tratta da YaST nella macchina virtuale di test di SAP HANA. È possibile visualizzare il singolo disco da 1 TB per il backup SAP HANA. Per il backup di 230 GB sono necessari 42 minuti. Sono stati anche associati cinque dischi da 200 GB ed è stato creato il RAID software md0 RAID, con striping su questi cinque dischi di dati di Azure.

![Ripetere lo stesso backup sul RAID software con striping in cinque dischi di dati di archiviazione standard di Azure collegati](media/sap-hana-backup-file-level/five-backup-disks.png)

Ripetere lo stesso RAID software di backup con striping in cinque dischi di dati di archiviazione standard di Azure collegati riduce il tempo necessario per il backup da 42 a 10 minuti. I dischi sono stati collegati senza la memorizzazione nella cache della macchina virtuale. Questo esercizio dimostra l'importanza della velocità effettiva di scrittura su disco per ottenere un buon tempo di backup. È possibile passare all'archiviazione SSD standard di Azure o ad Archiviazione premium di Azure per accelerare ulteriormente il processo per ottenere prestazioni ottimali. In generale, l'archiviazione standard di Azure per HDD non è consigliata ed è stata usata solo a scopo dimostrativo. È consigliabile usare almeno l'archiviazione SSD standard di Azure o Archiviazione Premium di Azure per i sistemi di produzione.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Copiare i file di backup di SAP HANA in Archiviazione BLOB di Azure
I numeri delle prestazioni, i numeri di durata del backup e i numeri di durata di copia indicati potrebbero non rappresentare lo stato più recente della tecnologia di Azure.The performance numbers, backup duration numbers, and copy duration numbers might not represent most recent di Azure technology. Microsoft sta migliorando costantemente l'archiviazione di Azure per offrire più velocità effettiva e latenze inferiori. Pertanto i numeri sono solo a scopo dimostrativo. È necessario testare le esigenze individuali nell'area di Azure di propria scelta per essere in grado di giudicare con il metodo è il migliore per l'utente.

Un'altra opzione per archiviare rapidamente i file di backup di SAP HANA è Archiviazione BLOB di Azure. Un singolo contenitore BLOB ha un limite di circa 500 TB, sufficiente per i sistemi SAP HANA, che usano M32ts, M32ls, M64ls e tipi di VM GS5 di Azure, per mantenere sufficienti backup SAP HANA. I clienti possono &quot;&quot; scegliere &quot;&quot; tra l'archiviazione BLOB a caldo e a freddo (vedere Archiviazione BLOB di Azure: livelli di [accesso a caldo, ad accesso e archiviazione).](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal)

Con lo strumento blobxfer, è semplice copiare i file di backup di SAP HANA direttamente nell'Archiviazione BLOB di Azure.

![In questo caso si possono osservare i file di un backup completo di file SAP HANA](media/sap-hana-backup-file-level/list-of-backups.png)

È possibile visualizzare i file di un backup completo dei file SAP HANA. Dei quattro file, il più grande ha circa 230 GB di dimensione.

![La copia di 230 GB in un contenitore di BLOB dell'account di archiviazione standard di Azure ha richiesto circa 3000 secondi](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

Non usando l'hash md5 nel test iniziale, la copia di 230 GB in un contenitore di BLOB dell'account di archiviazione standard di Azure ha richiesto circa 3000 secondi.

La console di backup HANA Studio consente di limitare la dimensione massima dei file di backup di HANA. Nell'ambiente di esempio, ha migliorato le prestazioni con più file di backup più piccoli, anziché un file di grandi dimensioni da 230 GB.

L'impostazione del limite di dimensione del file di backup sul lato HANA non&#39;migliora il tempo di backup, poiché i file vengono scritti in sequenza. Il limite sulla dimensione del file è stato impostato su 60 GB, pertanto il backup ha creato quattro file di dati di grandi dimensioni invece del singolo file da 230 GB. L'uso di più file di backup può diventare una necessità per eseguire il backup dei database HANA se le destinazioni di backup presentano limitazioni sulle dimensioni dei file delle dimensioni dei BLOB.

![Per testare il parallelismo dello strumento blobxfer, la dimensione massima del file per i backup HANA è stata quindi impostata su 15 GB](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

Per testare il parallelismo dello strumento blobxfer, la dimensione massima del file per i backup HANA è stata quindi impostata su 15 GB, con la conseguente creazione di 19 file di backup. Questa configurazione ha consentito al blobxfer di copiare 230 GB di Archiviazione BLOB di Azure non più in 3000 secondi, ma in 875 secondi.

Durante l'esplorazione della copia dei backup eseguiti su dischi locali in altri percorsi, ad esempio l'archiviazione BLOB di Azure, tenere presente che la larghezza di banda utilizzata da un eventuale processo di copia parallela viene eseguita in base alla quota di rete o di archiviazione del tipo di macchina virtuale singola. Di conseguenza, è necessario bilanciare la durata della copia rispetto alla rete e alla larghezza di banda di archiviazione richiesta dal normale carico di lavoro in esecuzione nella macchina virtuale. 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Copiare i file di backup di SAP HANA in condivisione NFS

Microsoft Azure offre condivisioni NFS native tramite [File NetApp](https://azure.microsoft.com/services/netapp/)di Azure. È possibile creare volumi diversi di dozzine di TB in grado di archiviare e gestire i backup. È inoltre possibile creare un'istantanea di tali volumi in base alla tecnologia di NetApp. File Di Rete di Azure (ANF) è offerto in tre diversi livelli di servizio che offrono velocità effettiva di archiviazione diversa. Per altre informazioni, vedere l'articolo Livelli di servizio per i file [NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)di Azure . È possibile creare e montare un volume NFS da ANF come descritto nell'articolo [Guida introduttiva: Configurare i file di Azure NetApp e creare un volume NFS.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes?tabs=azure-portal)

Oltre a usare volumi NFS nativi di Azure tramite ANF, esistono varie possibilità di creare distribuzioni proprie che forniscono condivisioni NFS in Azure.Oltre a usare i volumi NFS nativi di Azure tramite ANF, esistono varie possibilità di creare distribuzioni proprie che forniscono condivisioni NFS in Azure.Oltre a usare volumi NFS nativi di Azure tramite ANF, esistono varie possibilità di creare proprie distribuzioni che forniscono condivisioni NFS in Tutti hanno lo svantaggio necessario per distribuire e gestire tali soluzioni manualmente. Alcune di queste possibilità sono documentate in questi articoli:

- [Disponibilità elevata per NFS in macchine virtuali di Azure su SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- [GlusterFS in VM di Azure in Red Hat Enterprise Linux per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)

Le condivisioni NFS create mediante i mezzi descritti in precedenza possono essere utilizzate per eseguire direttamente i backup HANA o per copiare i backup eseguiti su dischi locali in tali condivisioni NFS.

> [!NOTE]
> SAP HANA supporta NFS v3 e NFS v4.x. Qualsiasi altro formato come SMB con file system CIFS non è supportato per scrivere backup HANA. Vedere anche [nota di supporto SAP #1820529](https://launchpad.support.sap.com/#/notes/1820529)

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Copiare i file di backup di SAP HANA nei File di Azure

È possibile montare una condivisione di File di Azure all'interno di una macchina virtuale Linux di Azure. L'articolo [Come usare l'archiviazione dei file](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-linux) di Azure con Linux fornisce informazioni dettagliate su come eseguire la configurazione. Per le limitazioni relative ai file di Azure File o Azure premium, leggere l'articolo Obiettivi di [scalabilità e prestazioni di File di Azure.For](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets)limitations of on Azure Files or Azure premium files, read the article Azure Files scalability and performance targets .

> [!NOTE]
> SMB con file system CIFS non è supportato da SAP HANA per scrivere backup HANA. Vedere anche [nota di supporto SAP #1820529](https://launchpad.support.sap.com/#/notes/1820529). Di conseguenza, è possibile utilizzare questa soluzione solo come destinazione finale di un backup del database HANA che è stato eseguito direttamente su dischi collegati locali
> 

In un test condotto su File di Azure, non nei file premium di Azure, sono stati impiegati circa 929 secondi per copiare 19 file di backup con un volume complessivo di 230 GB. Ci aspettiamo che il tempo utilizzando file Premium di Azure sia molto migliore. Tuttavia, è necessario tenere presente che è necessario bilanciare gli interessi di una copia veloce con i requisiti del carico di lavoro sulla larghezza di banda della rete. Poiché ogni tipo di macchina virtuale di Azure applica la quota della larghezza di banda di rete, è necessario rimanere entro l'intervallo di tale quota con il carico di lavoro più la copia dei file di backup.

![La figura seguente mostra che sono stati necessari circa 929 secondi per copiare 19 file di backup SAP HANA](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


L'archiviazione dei file di backup SAP HANA nei file di Azure potrebbe essere un'opzione interessante. Soprattutto con la latenza migliorata e la velocità effettiva dei file Premium di Azure.Especially with the improved latency and throughput of Azure Premium Files.

## <a name="next-steps"></a>Passaggi successivi
* La [Guida del backup di SAP HANA in Macchine virtuali di Azure](sap-hana-backup-guide.md) offre una panoramica e informazioni introduttive.
* Per informazioni su come stabilire la disponibilità elevata e pianificare il ripristino di emergenza di SAP HANA in Azure (istanze di grandi dimensioni), vedere Disponibilità elevata SAP HANA (istanze di grandi dimensioni) e ripristino di emergenza in Azure.To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure (large instances), see [SAP HANA (large instances) high availability and disaster recovery on Azure.](hana-overview-high-availability-disaster-recovery.md)
