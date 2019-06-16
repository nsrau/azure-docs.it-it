---
title: Backup di SAP HANA in Azure basato su snapshot di archiviazione | Documentazione Microsoft
description: Esistono due possibilità principali per il backup di SAP HANA su macchine virtuali di Azure e questo articolo descrive il backup di SAP HANA basato su snapshot di archiviazione
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 74f47344afff630a8633b340ea4ce21db28db7ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60936611"
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>Backup di SAP HANA basato su snapshot di archiviazione

## <a name="introduction"></a>Introduzione

Questo articolo fa parte di una serie di tre articoli correlati dedicati al backup di SAP HANA. La [Guida del backup di SAP HANA in macchine virtuali di Azure](sap-hana-backup-guide.md) offre una panoramica e informazioni introduttive, mentre [SAP HANA Azure Backup on file level](sap-hana-backup-file-level.md) (Backup di SAP HANA in Azure a livello di file) tratta l'opzione relativa al backup basato su file.

Quando si usa una funzionalità di backup di VM per un sistema dimostrativo all-in-one a istanza singola, è preferibile eseguire il backup della VM anziché gestire i backup HANA a livello di sistema operativo. Un'alternativa consiste nell'acquisire snapshot BLOB di Azure per creare copie dei singoli dischi virtuali collegati a una macchina virtuale e mantenere i file di dati HANA. La coerenza delle app è molto importante durante la creazione di uno snapshot di backup o del disco di una VM mentre il sistema è in esecuzione. Vedere _Coerenza dei dati SAP HANA durante l'esecuzione degli snapshot di archiviazione_ nell'articolo correlato [Guida del backup di SAP HANA in Macchine virtuali di Azure](sap-hana-backup-guide.md). SAP HANA dispone di una funzionalità che supporta questi tipi di snapshot di archiviazione.

## <a name="sap-hana-snapshots-as-central-part-of-application-consistent-backups"></a>Snapshot di SAP HANA come parte centrale dei backup coerenti con l'applicazione

SAP HANA dispone di una funzionalità che supporta la creazione di uno snapshot di archiviazione. Esiste una limitazione per i sistemi a contenitore singolo. Scenari di accesso a SAP HANA MCS con più di un tenant non supportano questo tipo di snapshot del database SAP HANA (vedere [Creare uno Snapshot di archiviazione (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)).

Funziona come indicato di seguito:

- Preparare la creazione dello snapshot di archiviazione avviando lo snapshot di SAP HANA
- Eseguire lo snapshot di archiviazione (ad esempio uno snapshot BLOB di Azure)
- Confermare lo snapshot SAP HANA

![La schermata mostra che è possibile creare uno snapshot dei dati di SAP HANA tramite un'istruzione SQL](media/sap-hana-backup-storage-snapshots/image011.png)

La schermata mostra che è possibile creare uno snapshot dei dati di SAP HANA tramite un'istruzione SQL.

![Lo snapshot appare anche nel catalogo di backup in SAP HANA Studio](media/sap-hana-backup-storage-snapshots/image012.png)

Lo snapshot appare anche nel catalogo di backup in SAP HANA Studio.

![Sul disco lo snapshot appare nella directory dei dati di SAP HANA](media/sap-hana-backup-storage-snapshots/image013.png)

Sul disco lo snapshot appare nella directory dei dati di SAP HANA.

Prima di eseguire lo snapshot di archiviazione, mentre SAP HANA si trova nella modalità di preparazione dello snapshot, è necessario assicurarsi che sia garantita anche la coerenza del file system. Vedere _Coerenza dei dati SAP HANA durante l'esecuzione degli snapshot di archiviazione_ nell'articolo correlato [Guida del backup di SAP HANA in Macchine virtuali di Azure](sap-hana-backup-guide.md).

Dopo avere completato lo snapshot di archiviazione, è fondamentale confermare lo snapshot di SAP HANA. È necessario eseguire un'istruzione SQL corrispondente: BACKUP DATA CLOSE SNAPSHOT. Vedere [BACKUP DATA CLOSE SNAPSHOT Statement (Backup and Recovery)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm) (Istruzione BACKUP DATA CLOSE SNAPSHOT (Backup e ripristino).

> [!IMPORTANT]
> Confermare lo snapshot HANA. A causa di &quot;Copy-on-Write&quot; SAP HANA potrebbe richiedere ulteriore spazio su disco nella modalità di preparazione dello snapshot e non è possibile avviare nuovi backup fino a quando lo snapshot di SAP HANA viene confermato.

## <a name="hana-vm-backup-via-azure-backup-service"></a>Backup di VM HANA tramite il servizio Backup di Azure

L'agente di backup del servizio Backup di Azure non è disponibile per le VM Linux. Inoltre, Linux non ha una funzionalità simile a quella di cui dispone Windows con VSS.  Per usare il backup di Azure a livello di file o directory, è necessario copiare i file di backup di SAP HANA in una VM Windows e quindi usare l'agente di backup. 

Altrimenti è possibile soltanto eseguire un backup completo di una VM Linux tramite il servizio Backup di Azure. Per altre informazioni, vedere [Panoramica delle funzionalità di Backup di Azure](../../../backup/backup-introduction-to-azure-backup.md).

Il servizio Backup di Azure offre un'opzione per eseguire il backup e il ripristino di una VM. Altre informazioni su questo servizio e il suo funzionamento sono riportate nell'articolo [Pianificare l'infrastruttura di backup delle VM in Azure](../../../backup/backup-azure-vms-introduction.md).

L'articolo afferma che ci sono due fattori importanti da considerare:

_&quot;Per le macchine virtuali Linux sono possibili solo backup coerenti con i file perché Linux non ha una piattaforma equivalente al Servizio Copia Shadow del volume.&quot;_

_&quot;Le applicazioni devono implementare il proprio meccanismo di &quot;correzione&quot; sui dati ripristinati.&quot;_

Pertanto è necessario assicurarsi che SAP HANA sia in uno stato coerente sul disco all'avvio del backup. Vedere _gli snapshot di SAP HANA_ descritti in precedenza nel documento. Ma esiste un problema potenziale quando SAP HANA si trova in questa modalità di preparazione dello snapshot. Per altre informazioni, vedere [Create a Storage Snapshot - SAP HANA Studio](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) (Creare uno snapshot di archiviazione - SAP HANA Studio).

L'articolo afferma quanto segue:

_&quot;Si consiglia di confermare o abbandonare uno snapshot di archiviazione appena possibile dopo che è stato creato. Mentre lo snapshot di archiviazione viene preparato o creato, i dati interessati dallo snapshot vengono bloccati. Mentre i dati interessati dallo snapshot rimangano bloccati è comunque possibile apportare modifiche nel database. Tali modifiche non comporteranno variazioni nei dati bloccati interessati dallo snapshot. Le modifiche vengono scritte in posizioni dell'area dati separate dallo snapshot di archiviazione. Le modifiche vengono scritte anche nel log. Più i dati interessati dallo snapshot sono mantenuti bloccati, più il volume dei dati può aumentare.&quot;_

Il servizio Backup di Azure si occupa della coerenza del file system mediante le estensioni di VM di Azure. Queste estensioni non sono disponibili in modalità autonoma e funzionano solo in combinazione con il servizio Backup di Azure. È comunque necessario fornire script per creare ed eliminare uno snapshot di SAP HANA per garantire la coerenza delle app.

Backup di Azure presenta quattro fasi principali:

- Eseguire script di preparazione: lo script dovrà creare uno snapshot SAP HANA
- Creare lo snapshot
- Eseguire lo script di post-snapshot - lo script deve eliminare il servizio SAP HANA creato dallo script di preparazione
- Trasferire i dati all'insieme di credenziali

Per informazioni dettagliate sulla posizione da cui copiare gli script e sul funzionamento di Backup di Azure, vedere gli articoli seguenti:

- [Pianificare l'infrastruttura di backup delle macchine virtuali in Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Backup coerente con le applicazioni per macchine virtuali Linux in Azure](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)



In questo momento, Microsoft non ha pubblicato script di preparazione e script di post-snapshot per SAP HANA. Come cliente o integratore di sistema, sarebbe necessario creare tali script e configurare la procedura in base alla documentazione citata in precedenza.


## <a name="restore-from-application-consistent-backup-against-a-vm"></a>Eseguire il ripristino da backup coerente delle applicazioni da una macchina virtuale
Il processo di ripristino di un backup coerente delle applicazioni creati dal backup di Azure è documentato nell'articolo [Ripristinare i file dal backup di macchine virtuali di Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm). 

> [!IMPORTANT]
> Nell'articolo [Ripristinare i file da backup di macchine virtuali di Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) è riportato un elenco di eccezioni e i passaggi per usare i set di striping del disco. I dischi con striping sono probabilmente la configurazione abituale della macchina virtuale per SAP HANA. Pertanto, è essenziale leggere l'articolo e testare il processo di ripristino per questi casi, come indicato nell'articolo. 



## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>Chiave di licenza di HANA e ripristino di VM mediante il servizio Backup di Azure

Il servizio Backup di Azure è progettato in modo da creare una nuova VM durante il ripristino. Al momento non è prevista la possibilità di eseguire un ripristino &quot;sul posto&quot; di una VM di Azure esistente.

![La figura illustra l'opzione di ripristino del servizio di Azure nel portale di Azure](media/sap-hana-backup-storage-snapshots/image019.png)

La figura illustra l'opzione di ripristino del servizio di Azure nel portale di Azure. È possibile scegliere tra creare una VM durante il ripristino e ripristinare i dischi. Dopo il ripristino dei dischi è comunque necessario creare una nuova VM su tale ripristino. Ogni volta che viene creata una nuova VM in Azure, l'ID VM univoco cambia (vedere [Accesso e uso dell'ID univoco di una VM di Azure](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)).

![La figura mostra l'ID VM univoco di Azure prima e dopo il ripristino tramite il servizio Backup di Azure](media/sap-hana-backup-storage-snapshots/image020.png)

La figura mostra l'ID VM univoco di Azure prima e dopo il ripristino tramite il servizio Backup di Azure. La chiave hardware SAP, che viene usata per la gestione delle licenze SAP, utilizza questo ID VM univoco. Di conseguenza, dopo il ripristino di una VM, è necessario installare una nuova licenza SAP.

Durante la creazione di questa guida al backup è stata presentata una nuova funzionalità di Backup di Azure in modalità di anteprima. La funzionalità consente il ripristino a livello di file basato sullo snapshot di VM che è stato eseguito per il backup della VM. In questo modo si evita la necessità di distribuire una nuova VM, pertanto l'ID VM univoco rimane lo stesso e non è necessaria una nuova chiave di licenza di SAP HANA. Altre informazioni su questa funzionalità saranno fornite dopo un completo test.

Backup di Azure consentirà alla fine il backup dei singoli dischi virtuali di Azure oltre a file e directory all'interno della VM. Uno dei vantaggi principali di Backup di Azure è la gestione di tutti i backup, risparmiando al cliente di doverla eseguire lui stesso. Quando diventa necessario un ripristino, Backup di Azure seleziona il backup corretto da usare.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>Backup di VM SAP HANA mediante snapshot manuali dei dischi

Anziché usare il servizio Backup di Azure è possibile configurare una singola soluzione di backup mediante la creazione manuale di snapshot BLOB di dischi rigidi virtuali di Azure tramite PowerShell. Per la descrizione dei passaggi, vedere [Using blob snapshots with PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) (Usare gli snapshot BLOB con PowerShell).

Offre maggiore flessibilità ma non consente di risolvere i problemi descritti in precedenza in questo documento:

- È comunque necessario creare uno snapshot di SAP HANA per assicurarsi che sia in uno stato coerente
- Il disco del sistema operativo non può essere sovrascritto anche se la VM viene deallocata a causa di un errore che indica l'esistenza di un lease. Funziona solo dopo aver eliminato la VM, che potrebbe causare la creazione di un nuovo ID VM univoco e la necessità di installare una nuova licenza SAP.

![È possibile ripristinare solo i dischi di dati di una VM di Azure](media/sap-hana-backup-storage-snapshots/image021.png)

È possibile ripristinare solo i dischi di dati di una VM di Azure, evitando il problema della creazione di un nuovo ID VM univoco e, pertanto, dell'invalidamento della licenza SAP:

- Per il test sono stati collegati due dischi dati di Azure a una VM e un RAID software è stato definito su questi 
- La coerenza di SAP HANA è stata verificata mediante la funzionalità di snapshot di SAP HANA
- Blocco del file system (vedere _Coerenza dei dati SAP HANA durante l'esecuzione degli snapshot di archiviazione_ nell'articolo correlato [Guida del backup di SAP HANA in Macchine virtuali di Azure](sap-hana-backup-guide.md))
- Sono stati eseguiti snapshot BLOB da entrambi i dischi dati
- Sblocco del file system
- Conferma dello snapshot SAP HANA
- Per ripristinare i dischi dati, la VM è stata arrestata ed entrambi i dischi disconnessi
- Dopo la disconnessione dei dischi, questi sono stati sovrascritti con gli snapshot BLOB precedenti
- Quindi i dischi virtuali ripristinati sono stati collegati nuovamente alla VM
- Dopo l'avvio della VM, sul RAID software tutto ha funzionato correttamente ed è stato reimpostato sull'ora dello snapshot BLOB
- HANA è stato reimpostato sullo snapshot HANA

Se fosse possibile arrestare SAP HANA prima degli snapshot BLOB, la procedura sarebbe meno complessa. In questo caso si potrebbe saltare lo snapshot HANA e, se non succede nient'altro nel sistema, saltare anche il blocco del file system. Quando è necessario creare snapshot mentre tutto è online, la complessità è maggiore. Vedere _Coerenza dei dati SAP HANA durante l'esecuzione degli snapshot di archiviazione_ nell'articolo correlato [Guida del backup di SAP HANA in Macchine virtuali di Azure](sap-hana-backup-guide.md).

## <a name="next-steps"></a>Passaggi successivi
* La [Guida del backup di SAP HANA in Macchine virtuali di Azure](sap-hana-backup-guide.md) offre una panoramica e informazioni introduttive.
* [Backup di SAP HANA di Azure a livello di file](sap-hana-backup-file-level.md) descrive l'opzione di backup basato su file.
* Per informazioni su come stabilire la disponibilità elevata e pianificare il ripristino di emergenza di SAP HANA in Azure (istanze di grandi dimensioni), vedere [Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze di grandi dimensioni) in Azure](hana-overview-high-availability-disaster-recovery.md).
