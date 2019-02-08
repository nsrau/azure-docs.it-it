---
title: Domande frequenti sul servizio Importazione/Esportazione di Azure | Microsoft Docs
description: Risposte ad alcune domande frequenti sul servizio Importazione/Esportazione di Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 12/13/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: e52d5f3b30490227541e99e067c096a7df6dd911
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2019
ms.locfileid: "55566189"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Servizio Importazione/Esportazione di Azure: domande frequenti 
Di seguito vengono fornite alcune domande e risposte relative all'uso del servizio Importazione/Esportazione di Azure per trasferire dati in Archiviazione di Azure. Le domande e le risposte sono suddivise nelle categorie seguenti:

- Informazioni sul servizio Importazione/Esportazione
- Preparazione dei dischi per l'importazione/esportazione
- Processi di importazione/esportazione
- Spedizione dei dischi
- Miscellaneous 

## <a name="about-importexport-service"></a>Informazioni sul servizio Importazione/Esportazione

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>È possibile copiare dati di archiviazione file di Azure tramite il servizio Importazione/Esportazione di Azure?

Sì. Il servizio Importazione/Esportazione di Azure supporta l'importazione nell'archiviazione file di Azure. Al momento non supporta l'esportazione di File di Azure.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Il servizio Importazione/Esportazione di Azure è disponibile per sottoscrizioni CSP?

Sì. Il servizio Importazione/Esportazione di Azure supporta sottoscrizioni di provider di soluzioni cloud (CSP).

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>È possibile usare il servizio Importazione/Esportazione di Azure per copiare cassette postali PST e dati di SharePoint in Office 365?

Sì. Per altre informazioni, vedere [Importare file PST o dati di SharePoint in Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>È possibile usare il servizio Importazione/Esportazione di Azure per copiare i backup offline nel servizio Backup di Azure?

Sì. Per altre informazioni, vedere [Flusso di lavoro di backup offline in Backup di Azure](../../backup/backup-azure-backup-import-export.md).

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>È possibile acquistare da Microsoft unità per i processi di importazione/esportazione?

 No. È necessario spedire le proprie unità per i processi di importazione ed esportazione.


## <a name="preparing-disks-for-importexport"></a>Preparazione dei dischi per l'importazione/esportazione

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>È possibile ignorare il passaggio di preparazione delle unità per un processo di importazione? È possibile preparare un'unità senza copia?

 No. Qualsiasi unità usata per importare dati deve essere preparata usando lo strumento WAImportExport di Azure. Usare lo strumento anche per copiare dati nell'unità.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>È necessario eseguire attività di preparazione dei dischi durante la creazione di un processo di esportazione?

 No. Sono consigliati alcuni controlli preliminari. Per verificare il numero di dischi necessari, usare il comando PreviewExport dello strumento WAImportExport. Per altre informazioni, vedere l'articolo sull' [anteprima dell'uso del disco per un processo di esportazione](https://msdn.microsoft.com/library/azure/dn722414.aspx). Il comando aiuta a prevedere l'utilizzo delle unità per i BLOB selezionati, in base alle dimensioni delle unità che si intende usare. Controllare anche che sia possibile eseguire operazioni di lettura e scrittura nel disco rigido da spedire per il processo di esportazione.

## <a name="importexport-jobs"></a>Processi di importazione/esportazione

### <a name="can-i-cancel-my-job"></a>È possibile annullare il processo?
Sì. È possibile annullare un processo quando lo stato è **Creazione** o **Spedizione**. Oltre a queste fasi, un processo non può essere più annullato e prosegue fino alla fase finale.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Per quanto tempo è possibile visualizzare lo stato dei processi completati nel portale di Azure?
Lo stato dei processi completati può essere visualizzato per un massimo di 90 giorni. I processi completati vengono eliminati dopo 90 giorni.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Che cosa si deve fare per importare o esportare più di 10 unità?
Un singolo processo di importazione o esportazione può fare riferimento solo a 10 unità. Per spedire più di 10 unità, è necessario creare più processi. Le unità associate allo stesso processo devono essere spedite insieme nello stesso pacco. Per altre informazioni e linee guida per i casi in cui la capacità dei dati richiede più processi di importazione di dischi, contattare Microsoft all'indirizzo bulkimport@microsoft.com. 

### <a name="the-uploaded-blob-shows-status-as-lease-expired-what-should-i-do"></a>Lo stato del BLOB caricato è "Il lease è scaduto". Cosa devo fare?
È possibile ignorare il campo "Il lease è scaduto". Importazione/Esportazione accetta il lease sul BLOB durante il caricamento per assicurarsi che nessun altro processo possa aggiornare il BLOB in parallelo. Lo stato Il lease è scaduto indica che Importazione/Esportazione non sta più eseguendo il caricamento e il BLOB è disponibile per l'uso. 

## <a name="shipping-disks"></a>Spedizione dei dischi

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Qual è il numero massimo di HDD per una spedizione?
Non esiste un limite al numero di HDD in una spedizione. Se i dischi appartengono a più processi, è consigliabile: 
- Etichettare i dischi con i nomi di processo corrispondenti.
- Aggiornare i processi con un numero di tracciabilità preceduto dal suffisso -1, -2 e così via.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>È necessario includere altro nel pacco oltre all'HDD?
Includere solo i dischi rigidi nel pacco per la spedizione. Non inserire oggetti come cavi di alimentazione o cavi USB.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>È necessario spedire le unità tramite FedEx o DHL?
Per spedire le unità al data center di Azure è possibile servirsi di uno qualsiasi dei vettori più noti, come FedEx, DHL, UPS o il servizio postale nazionale. Tuttavia, per la restituzione all'utente di unità dal data center, è necessario specificare:

- Un numero di account FedEx negli Stati Uniti e in Unione europea oppure
- Un numero di account DHL nelle aree di Asia e Australia.

### <a name="are-there-any-restrictions-with-shipping-my-drive-internationally"></a>Si applicano limitazioni alla spedizione internazionale di unità?
È possibile che i supporti fisici spediti debbano passare i confini internazionali. È responsabilità del cliente assicurarsi che i supporti fisici e i dati siano importati e/o esportati conformemente alle leggi vigenti. Prima di spedire i supporti fisici, rivolgersi ai consulenti per verificare che i supporti e i dati possano essere spediti ai data center specificati in modo conforme alle normative. Ciò assicura che la spedizione raggiunga Microsoft in modo tempestivo.

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>Durante la creazione di un processo, l'indirizzo di spedizione è una località diversa dalla posizione dell'account di archiviazione. Cosa devo fare?

Alcuni percorsi dell'account di archiviazione sono mappati a indirizzi di spedizione alternativi. Le località di spedizione disponibili in precedenza possono essere temporaneamente mappate a posizioni alternative. Controllare sempre l'indirizzo di spedizione fornito durante la creazione del processo prima di spedire le unità.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>Al momento della spedizione dell'unità, il vettore richiede l'indirizzo e il numero di telefono di contatto del data center. Quali informazioni è necessario fornire?

Il numero di telefono e l'indirizzo del controller di dominio vengono forniti come parte della creazione del processo.


## <a name="miscellaneous"></a>Miscellaneous

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>Che cosa succede se si invia accidentalmente un HDD non conforme ai requisiti supportati?

Il data center di Azure restituirà all'utente l'unità non conforme ai requisiti supportati. Se solo alcune delle unità nel pacchetto soddisfano i requisiti per il supporto, tali unità verranno elaborate e le unità che non soddisfano i requisiti verranno restituite all'utente.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>Il servizio formatta le unità prima di restituirle?

 No. Tutte le unità vengono crittografate con BitLocker.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Come si accede ai dati importati dal servizio?

Usare il portale di Azure o [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) per accedere ai dati associati all'account di archiviazione di Azure.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>Al termine dell'importazione, come si presenteranno i dati nell'account di archiviazione? Verrà mantenuta la gerarchia di directory?

Quando si prepara un disco rigido per un processo di importazione, la destinazione viene specificata dal campo DstBlobPathOrPrefix nel file CSV del set di dati. Si tratta del contenitore di destinazione nell'account di archiviazione in cui vengono copiati i dati dal disco rigido. In questo contenitore di destinazione vengono create directory virtuali per le cartelle del disco rigido e BLOB per i file. 

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Se l'unità contiene file già presenti nell'account di archiviazione, il servizio sovrascrive i BLOB o i file esistenti?

Dipende. Quando si prepara l'unità, è possibile specificare se i file di destinazione devono essere sovrascritti o ignorati usando il campo denominato Disposition:<rename|no-overwrite|overwrite> nel file CSV del set di dati. Per impostazione predefinita, il servizio rinomina i nuovi file invece di sovrascrivere i BLOB o i file esistenti.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>Lo strumento WAImportExport è compatibile con i sistemi operativi a 32 bit?
 No. Lo strumento WAImportExport è compatibile solo con i sistemi operativo Windows a 64 bit. Per un elenco completo dei sistemi operativi supportati, vedere [Sistemi operativi supportati](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements). 


### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Quali sono le dimensioni massime per BLOB in blocchi e BLOB di pagine supportate da Importazione/Esportazione di Azure?

La dimensione massima dei BLOB in blocchi è circa 4768 TB o 5.000.000 MB.
La dimensione massima di un BLOB di pagine è 1 TB.


### <a name="does-azure-importexport-support-aes-256-encryption"></a>Importazione/Esportazione di Microsoft Azure supporta la crittografia AES 256?
Per impostazione predefinita, il servizio Importazione/Esportazione di Azure usa la crittografia BitLocker AES-128. È possibile passare a usare AES-256 eseguendo la crittografia manuale con BitLocker prima della copia dei dati. 

- Se si usa [WAImportExport V1](https://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip), di seguito viene indicato un comando di esempio
    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
    ```
- Se si usa [WAImportExport V2](https://www.microsoft.com/download/details.aspx?id=55280), specificare "AlreadyEncrypted" e immettere la chiave nel file CSV driveset.
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Che cos'è Importazione/Esportazione di Azure?](storage-import-export-service.md)


