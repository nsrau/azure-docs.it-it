---
title: Backup di SAP HANA di Azure a livello di file | Documentazione Microsoft
description: Esistono due opzioni principali per il backup di SAP HANA su macchine virtuali di Azure e questo articolo descrive il backup di SAP HANA di Azure a livello di file.
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 3/13/2017
ms.author: rclaus
ms.openlocfilehash: 5db0ceb1648b5afa278e1cbe1c42fce8033bfdc1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="sap-hana-azure-backup-on-file-level"></a>Backup di SAP HANA di Azure a livello di file

## <a name="introduction"></a>Introduzione

Questo articolo fa parte di una serie di tre articoli correlati dedicati al backup di SAP HANA. La [Guida del backup per SAP HANA nelle macchine virtuali di Azure](./sap-hana-backup-guide.md) offre una panoramica e le informazioni su come iniziare, mentre [Backup di SAP HANA basato su snapshot di archiviazione](./sap-hana-backup-storage-snapshots.md) riguarda l'opzione di backup basato su snapshot di archiviazione.

Osservando le dimensioni della macchina virtuale di Azure, è possibile notare che un GS5 consente 64 dischi di dati collegati. Per sistemi SAP HANA di grandi dimensioni, un numero significativo di dischi potrebbe essere già usato per i file di log e di dati, possibilmente in combinazione con RAID software per la velocità effettiva IO ottimale dei dischi. La domanda è dunque dove archiviare i file di backup di SAP HANA e quali file potrebbero riempire i dischi di dati collegati col passare del tempo. Vedere [Sizes for Linux virtual machines in Azure](../../linux/sizes.md) (Dimensioni delle macchine virtuali Linux in Azure) per le tabelle delle dimensioni della macchina virtuale di Azure.

Al momento, non è disponibile alcuna integrazione del backup di SAP HANA con il servizio di Backup di Azure. Il metodo standard per gestire il backup e il ripristino a livello di file è attraverso un backup basato su file, usando SAP HANA Studio o le istruzioni SQL SAP HANA. Per altre informazioni, vedere [SAP HANA SQL and System Views Reference](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf) (Riferimento alle visualizzazioni del sistema e di SQL SAP HANA).

![Nella figura viene mostrata la finestra di dialogo della voce di menu del backup in SAP HANA Studio](media/sap-hana-backup-file-level/image022.png)

Nella figura viene mostrata la finestra di dialogo della voce di menu del backup in SAP HANA Studio. Quando si sceglie il &quot;file&quot; del tipo, esso deve specificare un percorso nel file system in cui SAP HANA scrive i file di backup. La funzione di ripristino funziona nello stesso modo.

Sebbene questa scelta sia semplice e diretta, è opportuno presentare alcune considerazioni. Come accennato in precedenza, una macchina virtuale di Azure presenta una limitazione nel numero di dischi di dati che è possibile collegare. Potrebbe non essere disponibile spazio per archiviare i file di backup di SAP HANA nei file system della macchina virtuale, a seconda delle dimensioni del database e dei requisiti di velocità effettiva del disco, che potrebbe comportare che il RAID software usi lo striping su più dischi di dati. Più avanti in questo articolo vengono fornite varie opzioni per lo spostamento di questi file di backup e per la gestione delle limitazioni relative alle dimensioni dei file e delle prestazioni durante la gestione di terabyte di dati.

Un'altra opzione, che offre più libertà in relazione alla capacità totale, è l'Archiviazione BLOB di Azure. Mentre un singolo BLOB è limitato a 1 TB, la capacità totale di un singolo contenitore di BLOB è attualmente pari a 500 TB. In aggiunta, questa opzione offre ai clienti la possibilità di selezionare la cosiddetta archiviazione BLOB ad &quot;accesso sporadico&quot;, che presenta vantaggi sui costi. Vedere [Archivio BLOB di Azure: livelli di archiviazione ad accesso frequente e sporadico](../../../storage/blobs/storage-blob-storage-tiers.md) per altre informazioni sull'archiviazione BLOB ad accesso sporadico.

Per una maggiore sicurezza, usare un account di archiviazione con replica geografica per archiviare i backup di SAP HANA. Per dettagli sulla replica dell'account di archiviazione, vedere [Replica di Archiviazione di Azure](../../../storage/common/storage-redundancy.md).

È possibile posizionare dischi rigidi virtuali dedicati per i backup di SAP HANA in un account di archiviazione di backup apposito con replica geografica. È anche possibile copiare i dischi rigidi virtuali che mantengono i backup di SAP HANA in un account di archiviazione con replica geografica o in un account di archiviazione in un'area diversa.

## <a name="azure-backup-agent"></a>Agente di Backup di Azure

Backup di Azure offre la possibilità di non eseguire solo il backup di macchine virtuali complete, ma anche di file e directory tramite l'agente di backup, che deve essere installato nel sistema operativo guest. Da dicembre 2016, l'agente è supportato solo in Windows (vedere [Eseguire il backup di un client o server Windows in Azure con Backup di Azure usando il modello di distribuzione Resource Manager](../../../backup/backup-configure-vault.md)).

Una soluzione alternativa consiste innanzitutto nel copiare i file di backup di SAP HANA in una macchina virtuale di Windows in Azure, ad esempio, tramite una condivisione SAMBA, e quindi usare l'agente di backup di Azure da questa posizione. Sebbene sia tecnicamente possibile, questa operazione potrebbe aggiungere complessità e rallentare il processo di backup o di ripristino a causa della copia tra Linux e la macchina virtuale di Windows. Non è consigliabile seguire questo approccio.

## <a name="azure-blobxfer-utility-details"></a>Dettagli sull'utilità di blobxfer di Azure

Per archiviare le directory e i file in archiviazione di Azure, è possibile usare PowerShell, l'interfaccia della riga di comando o sviluppare uno strumento usando uno degli [SDK di Azure](https://azure.microsoft.com/downloads/). È anche disponibile un'utilità pronta all'uso, AzCopy, che consente di copiare i dati nell'archiviazione di Azure, ma è dedicata esclusivamente a Windows (vedere [Trasferire dati con l'utilità della riga di comando AzCopy](../../../storage/common/storage-use-azcopy.md)).

Blobxfer è stato pertanto usato per la copia dei file di backup di SAP HANA. Si tratta di una risorsa open source, usata da molti clienti negli ambienti di produzione e disponibile su [GitHub](https://github.com/Azure/blobxfer). Questo strumento consente di copiare i dati direttamente nell'archiviazione BLOB di Azure o nella condivisione file di Azure. Offre anche una gamma di funzionalità utili, ad esempio hash md5 o parallelismo automatico durante la copia di una directory con più file.

## <a name="sap-hana-backup-performance"></a>Prestazioni del backup SAP HANA

![Questa schermata appartiene alla console di backup di SAP HANA in SAP HANA Studio](media/sap-hana-backup-file-level/image023.png)

Questa schermata appartiene alla console di backup di SAP HANA in SAP HANA Studio. Per eseguire il backup di 230 GB in un disco singolo di archiviazione di Azure standard collegato alla macchina virtuale HANA usando file system XFS sono necessari circa 42 minuti.

![Questa schermata è tratta da YaST nella macchina virtuale di test di SAP HANA](media/sap-hana-backup-file-level/image024.png)

Questa schermata è tratta da YaST nella macchina virtuale di test di SAP HANA. Come accennato in precedenza, si può osservare il disco singolo da 1 TB per il backup di SAP HANA. Per il backup di 230 GB sono necessari 42 minuti. Sono stati anche associati cinque dischi da 200 GB ed è stato creato il RAID software md0 RAID, con striping su questi cinque dischi di dati di Azure.

![Ripetere lo stesso backup sul RAID software con striping in cinque dischi di dati di archiviazione standard di Azure collegati](media/sap-hana-backup-file-level/image025.png)

Ripetere lo stesso RAID software di backup con striping in cinque dischi di dati di archiviazione standard di Azure collegati riduce il tempo necessario per il backup da 42 a 10 minuti. I dischi sono stati collegati senza la memorizzazione nella cache della macchina virtuale. È ovvio quanto sia importante la velocità effettiva di scrittura sul disco per il tempo del backup. Si potrebbe quindi passare al servizio di archiviazione premium di Azure per accelerare ulteriormente il processo per prestazioni ottimali. In generale, l'Archiviazione Premium di Azure deve essere usata per i sistemi di produzione.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Copiare i file di backup di SAP HANA in Archiviazione BLOB di Azure

A partire da dicembre 2016, l'opzione migliore per archiviare rapidamente i file di backup di SAP HANA è Archiviazione BLOB di Azure. Un contenitore di BLOB singolo ha un limite di 500 TB, sufficiente per la maggior parte dei sistemi SAP HANA in esecuzione in una macchina virtuale GS5 in Azure, per mantenere un numero di backup di SAP HANA sufficiente. I clienti possono scegliere tra Archiviazione BLOB &quot;ad accesso frequente&quot; e &quot;ad accesso sporadico&quot; (vedere [Archivio BLOB di Azure: livelli di archiviazione ad accesso frequente e sporadico](../../../storage/blobs/storage-blob-storage-tiers.md)).

Con lo strumento blobxfer, è semplice copiare i file di backup di SAP HANA direttamente nell'Archiviazione BLOB di Azure.

![In questo caso si possono osservare i file di un backup completo di file SAP HANA](media/sap-hana-backup-file-level/image026.png)

In questo caso si possono osservare i file di un backup completo di file SAP HANA. Esistono quattro file e il più grande ha una dimensione approssimativa di 230 GB.

![La copia di 230 GB in un contenitore di BLOB dell'account di archiviazione standard di Azure ha richiesto circa 3000 secondi](media/sap-hana-backup-file-level/image027.png)

Non usando l'hash md5 nel test iniziale, la copia di 230 GB in un contenitore di BLOB dell'account di archiviazione standard di Azure ha richiesto circa 3000 secondi.

![In questa schermata si può osservare come viene visualizzato nel portale di Azure](media/sap-hana-backup-file-level/image028.png)

In questa schermata si può osservare come viene visualizzato nel portale di Azure. È stato creato un contenitore BLOB denominato &quot;sap-hana-backups&quot; contenente i quattro BLOB che rappresentano i file di backup di SAP HANA. Uno di essi ha una dimensione di circa 230 GB.

La console di backup HANA Studio consente di limitare la dimensione massima dei file di backup di HANA. Nell'ambiente di esempio sono state migliorate le prestazioni rendendo possibile disporre di più file di backup più piccoli, invece di un file grande di 230 GB.

![L'impostazione del limite sulla dimensione del file di backup sul lato HANA non migliora il tempo del backup](media/sap-hana-backup-file-level/image029.png)

L'impostazione del limite sulla dimensione del file di backup sul lato HANA non migliora il tempo del backup, perché i file vengono scritti in modo sequenziale, come illustrato nella figura seguente. Il limite sulla dimensione del file è stato impostato su 60 GB, pertanto il backup ha creato quattro file di dati di grandi dimensioni invece del singolo file da 230 GB.

![Per testare il parallelismo dello strumento blobxfer, la dimensione massima del file per i backup HANA è stata quindi impostata su 15 GB](media/sap-hana-backup-file-level/image030.png)

Per testare il parallelismo dello strumento blobxfer, la dimensione massima del file per i backup HANA è stata quindi impostata su 15 GB, con la conseguente creazione di 19 file di backup. Questa configurazione ha consentito al blobxfer di copiare 230 GB di Archiviazione BLOB di Azure non più in 3000 secondi, ma in 875 secondi.

Il risultato è dovuto al limite di 60 MB/sec per la scrittura di un BLOB di Azure. Il parallelismo tramite più BLOB consente di risolvere il problema, ma con uno svantaggio: migliorare le prestazioni dello strumento blobxfer per copiare tutti i file di backup HANA in Archiviazione BLOB di Azure aumenta il carico sulle macchine virtuali e sulla rete HANA. Ne è interessato il funzionamento del sistema HANA.

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>Copia di BLOB dei dischi di dati di Azure dedicati nel RAID software di backup

A differenza del backup manuale sul disco di dati della macchina virtuale, in questo caso non si esegue il backup di tutti i dischi di dati in una macchina virtuale per salvare l'intera installazione di SAP, compresi i dati HANA, i file di log di HANA e i file di configurazione. Al contrario, l'idea è quella di avere un RAID software dedicato con striping tra più dischi rigidi virtuali dei dati di Azure per archiviare un backup completo dei file SAP HANA. Vengono copiati solo questi dischi contenenti il backup di SAP HANA. Questi possono facilmente essere conservati in un account di archiviazione del backup HANA dedicato oppure collegati a una &quot;macchina virtuale di gestione del backup&quot; dedicata per elaborazioni aggiuntive.

![Sono stati copiati tutti i dischi rigidi virtuali coinvolti usando il comando PowerShell **start-azurestorageblobcopy**](media/sap-hana-backup-file-level/image031.png)

Dopo aver completato il backup per il RAID software locale, tutti i dischi rigidi virtuali coinvolti sono stati copiati tramite il comando di PowerShell **start-azurestorageblobcopy** (vedere [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy)). Poiché la conservazione dei file di backup influisce solo sul file system dedicato, non ci sono dubbi sulla coerenza dei file di dati e log SAP HANA sul disco. Un vantaggio di questo comando consiste nel fatto che funziona anche mentre la macchina virtuale è online. Per essere certi che nessun processo scriva al set di striping di backup, assicurarsi di disinstallarlo prima della copia di BLOB e di installarlo nuovamente in un secondo momento. In alternativa, è possibile usare un modo appropriato per &quot;bloccare&quot; il file system. Ad esempio, tramite xfs\_freeze per il file system XFS.

![La schermata mostra l'elenco di BLOB nel contenitore di dischi rigidi virtuali nel portale di Azure](media/sap-hana-backup-file-level/image032.png)

La schermata mostra l'elenco di BLOB nel contenitore di &quot;dischi rigidi virtuali&quot; nel portale di Azure. La schermata mostra i cinque dischi rigidi virtuali che sono stati collegati alla macchina virtuale del server SAP HANA come RAID software per conservare i file di backup di SAP HANA. Descrive anche le cinque copie che acquisite tramite il comando di copia del BLOB.

![A scopo di test, le copie dei dischi del RAID software di backup di SAP HANA sono state collegate alla macchina virtuale del server dell'app](media/sap-hana-backup-file-level/image033.png)

A scopo di test, le copie dei dischi del RAID software di backup di SAP HANA sono state collegate alla macchina virtuale del server dell'app.

![La macchina virtuale del server dell'app è stata arrestata per collegare le copie del disco](media/sap-hana-backup-file-level/image034.png)

La macchina virtuale del server dell'app è stata arrestata per collegare le copie del disco. Dopo l'avvio della macchina virtuale, i dischi e i RAID sono stati rilevati correttamente (montati tramite UUID). Mancava solo il punto di montaggio, che è stato creato tramite il partitioner YaST. In un secondo momento le copie dei file di backup di SAP HANA sono diventate visibili a livello di sistema operativo.

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Copiare i file di backup di SAP HANA in condivisione NFS

Per ridurre il potenziale impatto sul sistema SAP HANA in termini di prestazioni o di spazio su disco, è possibile archiviare i file di backup di SAP HANA in una condivisione NFS. Tecnicamente questa operazione funziona, ma significa usare una seconda macchina virtuale di Azure come host della condivisione NFS. Non deve essere una macchina virtuale di dimensioni ridotte, a causa della larghezza di banda della rete della macchina virtuale. Sarebbe dunque utile arrestare questa &quot;macchina virtuale di backup&quot; e riavviarla solo per l'esecuzione del backup di SAP HANA. La scrittura in una condivisione NFS sovraccarica la rete e ha un impatto sul sistema SAP HANA, ma la semplice gestione dei file di backup in un secondo momento sulla &quot;macchina virtuale di backup&quot; non influenza affatto il sistema SAP HANA.

![Una condivisione NFS da un'altra macchina virtuale di Azure è stata montata sulla macchina virtuale del server SAP HANA](media/sap-hana-backup-file-level/image035.png)

Per verificare il caso dell'uso di NFS, una condivisione NFS da un'altra macchina virtuale di Azure è stata montata sulla macchina virtuale del server SAP HANA. Non è stata applicata alcuna ottimizzazione NFS particolare.

![Il backup diretto ha richiesto 1 ora e 46 minuti.](media/sap-hana-backup-file-level/image036.png)

La condivisione NFS era un set di striping veloce, come quella nel server SAP HANA. Tuttavia, il backup diretto sulla condivisione NFS ha richiesto 1 ora e 46 minuti invece dei 10 minuti necessari per scrivere in un set di striping locale.

![L'alternativa non è stata molto più rapida e ha impiegato 1 ora e 43 minuti.](media/sap-hana-backup-file-level/image037.png)

L'alternativa di eseguire un backup in un set di striping locale e di copiarlo nella condivisione NFS a livello di sistema operativo (un semplice comando **cp - avr**) non è stata molto più rapida. Ha impiegato 1 ora e 43 minuti.

Funziona, ma le prestazioni non sono state buone per il test di backup di 230 GB. La situazione dovrebbe essere ancora più grave per più terabyte.

## <a name="copy-sap-hana-backup-files-to-azure-file-service"></a>Copiare i file di backup di SAP HANA nel Servizio file di Azure

È possibile montare una condivisione di file di Azure all'interno di una macchina virtuale Linux di Azure. L'articolo [Come usare l'archiviazione file di Azure con Linux](../../../storage/files/storage-how-to-use-files-linux.md) descrive l'uso in dettaglio. Tenere presente che esiste attualmente un limite di quota di 5 TB di una condivisione di file di Azure e un limite di dimensione del file di 1 TB per ogni file. Per altre informazioni sui limiti di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../../../storage/common/storage-scalability-targets.md).

I test hanno dimostrato, tuttavia, che il backup di SAP HANA non funziona direttamente con questo tipo di montaggio CIFS. Nella [Nota SAP 1820529](https://launchpad.support.sap.com/#/notes/1820529) si dichiara anche che il CIFS non è consigliato.

![In questa figura è mostrato un errore nella finestra di dialogo del backup in SAP HANA Studio](media/sap-hana-backup-file-level/image038.png)

In questa figura è mostrato un errore nella finestra di dialogo del backup in SAP HANA Studio, quando si tenta di eseguire il backup direttamente in una condivisione di file di Azure montata su CIFS. Pertanto, è necessario innanzitutto eseguire un backup standard di SAP HANA in un file system della macchina virtuale e quindi copiare i file di backup da questa posizione nel servizio file di Azure.

![La figura seguente mostra che sono stati necessari circa 929 secondi per copiare 19 file di backup SAP HANA](media/sap-hana-backup-file-level/image039.png)

La figura seguente mostra che sono stati necessari circa 929 secondi per copiare 19 file di backup SAP HANA con una dimensione totale di circa 230 GB nella condivisione di file di Azure.

![La struttura della directory di origine nella macchina virtuale SAP HANA è stata copiata nella condivisione di file di Azure](media/sap-hana-backup-file-level/image040.png)

In questa schermata si può osservare che la struttura della directory di origine nella macchina virtuale SAP HANA è stata copiata nella condivisione di file di Azure: una directory (hana\_backup\_fsl\_15 gb) e 19 singoli file di backup.

Archiviare i file di backup di SAP HANA nei file di Azure può rappresentare un'opzione interessante in futuro, quando i backup dei file di SAP HANA supporteranno direttamente questa archiviazione. oppure quando sarà possibile montare i file di Azure tramite NFS e il limite massimo di quota sarà nettamente superiore a 5 TB.

## <a name="next-steps"></a>Passaggi successivi
* La [Guida del backup di SAP HANA in Macchine virtuali di Azure](sap-hana-backup-guide.md) offre una panoramica e informazioni introduttive.
* [Backup di SAP HANA basato su snapshot di archiviazione](sap-hana-backup-storage-snapshots.md) descrive l'opzione di backup basato su snapshot di archiviazione.
* Per informazioni su come stabilire la disponibilità elevata e un piano di ripristino di emergenza di SAP HANA in Azure (istanze di grandi dimensioni), vedere [Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze di grandi dimensioni) in Azure](hana-overview-high-availability-disaster-recovery.md).
