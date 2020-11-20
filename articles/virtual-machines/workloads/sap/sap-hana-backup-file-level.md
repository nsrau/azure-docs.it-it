---
title: Backup di SAP HANA di Azure a livello di file | Documentazione Microsoft
description: Esistono due opzioni principali per il backup di SAP HANA su macchine virtuali di Azure e questo articolo descrive il backup di SAP HANA di Azure a livello di file.
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 13bdf4b10ec586bf83a70434f003cc1573aca469
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950262"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>Backup di SAP HANA di Azure a livello di file

## <a name="introduction"></a>Introduzione

Questo articolo è un articolo correlato alla [Guida al backup per SAP Hana in macchine virtuali di Azure](./sap-hana-backup-guide.md), che offre una panoramica e informazioni introduttive e altri dettagli sul servizio backup di Azure e sugli snapshot di archiviazione. 

Diversi tipi di VM in Azure consentono un numero diverso di dischi rigidi virtuali associati. I dettagli esatti sono documentati in [Dimensioni delle macchine virtuali Linux in Azure](../../sizes.md). Per i test a cui si fa riferimento in questa documentazione è stata usata una VM GS5 di Azure, che consente 64 dischi dati collegati. Per sistemi SAP HANA di grandi dimensioni, un numero significativo di dischi potrebbe essere già usato per i file di log e di dati, possibilmente in combinazione con striping di software per la velocità effettiva IO ottimale dei dischi. Per informazioni dettagliate sulle configurazioni del disco suggerite per SAP HANA le distribuzioni in macchine virtuali di Azure, vedere l'articolo [SAP Hana configurazioni di archiviazione delle macchine virtuali di Azure](./hana-vm-operations-storage.md). Gli elementi consigliati includono le raccomandazioni di spazio su disco per i backup locali.

Il metodo standard per gestire il backup e il ripristino a livello di file è attraverso un backup basato su file, usando SAP HANA Studio o le istruzioni SQL SAP HANA. Per ulteriori informazioni, leggere l'articolo [SAP Hana riferimento alle viste di sistema e SQL](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf).

![Nella figura viene mostrata la finestra di dialogo della voce di menu del backup in SAP HANA Studio](media/sap-hana-backup-file-level/backup-menue-dialog.png)

Nella figura viene mostrata la finestra di dialogo della voce di menu del backup in SAP HANA Studio. Quando si sceglie il &quot;file&quot; del tipo, esso deve specificare un percorso nel file system in cui SAP HANA scrive i file di backup. La funzione di ripristino funziona nello stesso modo.

Sebbene questa scelta sia semplice e diretta, è opportuno presentare alcune considerazioni. Una macchina virtuale di Azure presenta una limitazione del numero di dischi dati che possono essere collegati. Potrebbe non essere disponibile spazio per archiviare i file di backup di SAP HANA nei file system della macchina virtuale, a seconda delle dimensioni del database e dei requisiti di velocità effettiva del disco, che potrebbe comportare lo striping di software su più dischi di dati. Più avanti in questo articolo vengono fornite varie opzioni per lo spostamento di questi file di backup e per la gestione delle limitazioni relative alle dimensioni dei file e delle prestazioni durante la gestione di terabyte di dati.

Un'altra opzione, che offre più libertà in relazione alla capacità totale, è l'Archiviazione BLOB di Azure. Mentre un singolo BLOB è limitato a 1 TB, la capacità totale di un singolo contenitore di BLOB è attualmente pari a 500 TB. In aggiunta, questa opzione offre ai clienti la possibilità di selezionare la cosiddetta archiviazione BLOB ad &quot;accesso sporadico&quot;, che presenta vantaggi sui costi. Per informazioni dettagliate sull'archiviazione BLOB ad accesso sporadico [, vedere Archiviazione BLOB di Azure: livelli di accesso ad accesso frequente, sporadico e archivio](../../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal) .

Per una maggiore sicurezza, usare un account di archiviazione con replica geografica per archiviare i backup di SAP HANA. Per informazioni dettagliate sulla ridondanza di archiviazione e la replica di archiviazione, vedere [ridondanza](../../../storage/common/storage-redundancy.md) di archiviazione di Azure.

È possibile posizionare dischi rigidi virtuali dedicati per i backup di SAP HANA in un account di archiviazione di backup apposito con replica geografica. È anche possibile copiare i dischi rigidi virtuali che mantengono i backup di SAP HANA in un account di archiviazione con replica geografica o in un account di archiviazione in un'area diversa.

## <a name="azure-blobxfer-utility-details"></a>Dettagli sull'utilità di blobxfer di Azure

Per archiviare le directory e i file in archiviazione di Azure, è possibile usare PowerShell, l'interfaccia della riga di comando o sviluppare uno strumento usando uno degli [SDK di Azure](https://azure.microsoft.com/downloads/). Per la copia dei dati in archiviazione di Azure è disponibile anche un'utilità pronta per l'uso, AzCopy. (vedere [trasferire dati con l'utilità AzCopy Command-Line](../../../storage/common/storage-use-azcopy-v10.md)).

Perciò, Blobxfer è stato pertanto usato per la copia dei file di backup di SAP HANA. Si tratta di una risorsa open source, usata da molti clienti negli ambienti di produzione e disponibile su [GitHub](https://github.com/Azure/blobxfer). Questo strumento consente di copiare i dati direttamente nell'archiviazione BLOB di Azure o nella condivisione file di Azure. Offre inoltre una gamma di funzionalità utili, ad esempio hash MD5 o parallelismo automatico quando si copia una directory con più file.

## <a name="sap-hana-backup-performance"></a>Prestazioni del backup SAP HANA
In questo capitolo vengono illustrate le considerazioni sulle prestazioni. I numeri raggiunti potrebbero non rappresentare lo stato più recente poiché lo sviluppo è stabile per ottenere una velocità effettiva migliore per archiviazione di Azure. Di conseguenza, è necessario eseguire singoli test per la configurazione e l'area di Azure.

![Questa schermata appartiene alla console di backup di SAP HANA in SAP HANA Studio](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

Questa schermata mostra la console di backup SAP HANA di SAP HANA Studio. Sono necessari circa 42 minuti per eseguire un backup di 230 GB in un singolo disco di archiviazione di Azure HDD Standard collegato alla VM HANA usando il file system XFS sul disco.

![Questa schermata è tratta da YaST nella macchina virtuale di test di SAP HANA](media/sap-hana-backup-file-level/one-backup-disk.png)

Questa schermata è tratta da YaST nella macchina virtuale di test di SAP HANA. Per SAP HANA backup è possibile visualizzare il disco singolo da 1 TB. Per il backup di 230 GB sono necessari 42 minuti. Sono stati anche associati cinque dischi da 200 GB ed è stato creato il RAID software md0 RAID, con striping su questi cinque dischi di dati di Azure.

![Ripetere lo stesso backup sul RAID software con striping in cinque dischi di dati di archiviazione standard di Azure collegati](media/sap-hana-backup-file-level/five-backup-disks.png)

Ripetere lo stesso RAID software di backup con striping in cinque dischi di dati di archiviazione standard di Azure collegati riduce il tempo necessario per il backup da 42 a 10 minuti. I dischi sono stati collegati senza la memorizzazione nella cache della macchina virtuale. Questo esercizio illustra l'importanza della velocità effettiva di scrittura su disco per ottenere tempi di backup soddisfacenti. È possibile passare ad Azure SDD Standard archiviazione o archiviazione Premium di Azure per accelerare ulteriormente il processo per ottenere prestazioni ottimali. In generale, l'archiviazione HDD standard di Azure non è consigliata ed è stata usata solo a scopo dimostrativo. Si consiglia di usare almeno Azure SDD Standard archiviazione o archiviazione Premium di Azure per i sistemi di produzione.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Copiare i file di backup di SAP HANA in Archiviazione BLOB di Azure
I numeri di prestazioni, i numeri di durata del backup e i numeri di durata della copia indicati potrebbero non rappresentare lo stato più recente della tecnologia Azure. Microsoft sta migliorando costantemente l'archiviazione di Azure per offrire maggiore velocità effettiva e latenze più basse. Pertanto, i numeri sono solo a scopo dimostrativo. Per essere in grado di giudicare con il metodo, è consigliabile eseguire test per le esigenze individuali nell'area di Azure di propria scelta.

Un'altra opzione per archiviare rapidamente i file di backup di SAP HANA è Archiviazione BLOB di Azure. Un singolo contenitore BLOB ha un limite di circa 500 TB, sufficiente per i sistemi di SAP HANA, usando i tipi di VM M32ts, M32ls, M64ls e GS5 di Azure, per contenere backup SAP HANA sufficienti. I clienti hanno la possibilità di scegliere tra &quot; &quot; archiviazione BLOB ad accesso frequente e a &quot; freddo &quot; (vedere [archiviazione BLOB di Azure: livelli di accesso ad accesso frequente, sporadico e archivio](../../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)).

Con lo strumento blobxfer, è semplice copiare i file di backup di SAP HANA direttamente nell'Archiviazione BLOB di Azure.

![In questo caso si possono osservare i file di un backup completo di file SAP HANA](media/sap-hana-backup-file-level/list-of-backups.png)

È possibile visualizzare i file di un backup completo dei file di SAP HANA. Tra i quattro file, il più grande è di circa 230 GB di dimensioni.

![La copia di 230 GB in un contenitore di BLOB dell'account di archiviazione standard di Azure ha richiesto circa 3000 secondi](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

Non usando l'hash md5 nel test iniziale, la copia di 230 GB in un contenitore di BLOB dell'account di archiviazione standard di Azure ha richiesto circa 3000 secondi.

La console di backup HANA Studio consente di limitare la dimensione massima dei file di backup di HANA. Nell'ambiente di esempio, ha migliorato le prestazioni con più file di backup più piccoli, anziché un file di 230 GB di grandi dimensioni.

L'impostazione del limite delle dimensioni del file di backup sul lato HANA non&#39;migliorare l'ora del backup, perché i file vengono scritti in modo sequenziale. Il limite sulla dimensione del file è stato impostato su 60 GB, pertanto il backup ha creato quattro file di dati di grandi dimensioni invece del singolo file da 230 GB. L'uso di più file di backup può diventare una necessità per il backup dei database HANA se le destinazioni di backup presentano limitazioni sulle dimensioni dei file delle dimensioni dei BLOB.

![Per testare il parallelismo dello strumento blobxfer, la dimensione massima del file per i backup HANA è stata quindi impostata su 15 GB](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

Per testare il parallelismo dello strumento blobxfer, la dimensione massima del file per i backup HANA è stata quindi impostata su 15 GB, con la conseguente creazione di 19 file di backup. Questa configurazione ha consentito al blobxfer di copiare 230 GB di Archiviazione BLOB di Azure non più in 3000 secondi, ma in 875 secondi.

Quando si Esplora la copia dei backup eseguiti sui dischi locali in altre posizioni, ad esempio archiviazione BLOB di Azure, tenere presente che la larghezza di banda usata da un processo di copia parallela finale è la contabilità rispetto alla quota di rete o di archiviazione del tipo di macchina virtuale specifico. Di conseguenza, è necessario bilanciare la durata della copia rispetto alla larghezza di banda di rete e di archiviazione in cui è richiesto il normale carico di lavoro in esecuzione nella macchina virtuale. 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Copiare i file di backup di SAP HANA in condivisione NFS

Microsoft Azure offrire condivisioni NFS native tramite [Azure NetApp files](https://azure.microsoft.com/services/netapp/). È possibile creare volumi diversi di dozzine di TBs in capacità per archiviare e gestire i backup. È anche possibile eseguire lo snapshot di questi volumi in base alla tecnologia NetApp. Azure NetApp Files (e) è disponibile in tre diversi livelli di servizio che offrono una velocità effettiva di archiviazione diversa. Per informazioni dettagliate, leggere l'articolo [livelli di servizio per Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). È possibile creare e montare un volume NFS da e, come descritto nell'articolo [Guida introduttiva: configurare Azure NetApp files e creare un volume NFS](../../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md?tabs=azure-portal).

Oltre a usare i volumi NFS nativi di Azure tramite e, esistono diverse possibilità di creare distribuzioni personalizzate che forniscono condivisioni NFS in Azure. Tutti gli svantaggi necessari per la distribuzione e la gestione di tali soluzioni. Alcune di queste possibilità sono documentate in questi articoli:

- [Disponibilità elevata per NFS in macchine virtuali di Azure su SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md)
- [GlusterFS in VM di Azure in Red Hat Enterprise Linux per SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md)

Le condivisioni NFS create per mezzo di quelle descritte in precedenza possono essere usate per eseguire direttamente backup HANA in o per copiare i backup eseguiti sui dischi locali in tali condivisioni NFS.

> [!NOTE]
> SAP HANA supportano NFS v3 e NFS v4. x. Qualsiasi altro formato come SMB con CIFS file system non è supportato per la scrittura di backup HANA. Vedere anche la [Nota di supporto SAP #1820529](https://launchpad.support.sap.com/#/notes/1820529)

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Copiare i file di backup di SAP HANA nei File di Azure

È possibile montare una condivisione di File di Azure all'interno di una macchina virtuale Linux di Azure. L'articolo [come usare archiviazione file di Azure con Linux](../../../storage/files/storage-how-to-use-files-linux.md) fornisce informazioni dettagliate su come eseguire la configurazione. Per le limitazioni dei file File di Azure o Azure Premium, vedere l'articolo [file di Azure obiettivi di scalabilità e prestazioni](../../../storage/files/storage-files-scale-targets.md).

> [!NOTE]
> SMB con file system CIFS non è supportato da SAP HANA per la scrittura di backup HANA. Vedere anche la [Nota di supporto SAP #1820529](https://launchpad.support.sap.com/#/notes/1820529). Di conseguenza, è possibile usare questa soluzione solo come destinazione finale di un backup del database HANA eseguito direttamente su dischi collegati locali
> 

In un test eseguito su File di Azure, non su file Premium di Azure sono stati impiegati circa 929 secondi per copiare 19 file di backup con un volume complessivo di 230 GB. Si prevede che il tempo di utilizzo dei file Premium di Azure sia migliore. Tuttavia, è necessario tenere presente che è necessario bilanciare gli interessi di una copia veloce con i requisiti del carico di lavoro nella larghezza di banda di rete. Poiché ogni tipo di VM di Azure impone la quota della larghezza di banda di rete, è necessario rimanere entro l'intervallo di tale quota con il carico di lavoro e la copia dei file di backup.

![La figura seguente mostra che sono stati necessari circa 929 secondi per copiare 19 file di backup SAP HANA](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


L'archiviazione dei file di backup SAP HANA in file di Azure potrebbe essere un'opzione interessante. Soprattutto con la latenza e la velocità effettiva migliorate dei file Premium di Azure.

## <a name="next-steps"></a>Passaggi successivi
* La [Guida del backup di SAP HANA in Macchine virtuali di Azure](sap-hana-backup-guide.md) offre una panoramica e informazioni introduttive.
* Per informazioni su come stabilire la disponibilità elevata e pianificare il ripristino di emergenza di SAP HANA in Azure (istanze di grandi dimensioni), vedere [disponibilità elevata e ripristino di emergenza di SAP Hana (istanze large) in Azure](hana-overview-high-availability-disaster-recovery.md).