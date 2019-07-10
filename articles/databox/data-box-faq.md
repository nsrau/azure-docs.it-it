---
title: Domande frequenti su Microsoft Azure Data Box | Microsoft Docs
description: Contiene domande frequenti e risposte per Azure Data Box, una soluzione cloud che consente di trasferire grandi quantità di dati in Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 8f090a3f07eba529fd39976545eb654083e637eb
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561406"
---
# <a name="azure-data-box-frequently-asked-questions"></a>Azure Data Box: Domande frequenti

La soluzione ibrida Microsoft Azure Data Box consente di inviare terabyte di dati in Azure in modo rapido, conveniente e affidabile tramite un dispositivo di trasferimento. L'articolo contiene le domande frequenti che possono sorgere quando si usa Data Box nel portale di Azure e le relative risposte. 

Le domande e le risposte sono suddivise nelle categorie seguenti:

- Informazioni sul servizio
- Ordinare il dispositivo
- Configurazione e connessione 
- Monitoraggio dello stato
- Copiare i dati 
- Spedire il dispositivo
- Verifica e caricamento dei dati 
- Supporto per la catena di custodia

## <a name="about-the-service"></a>Informazioni sul servizio

### <a name="q-what-is-azure-data-box-service"></a>D: Che cos'è il servizio Azure Data Box? 
R.  Il servizio Azure Data Box è progettato per l'inserimento di dati offline. Il servizio gestisce una gamma di prodotti per capacità di archiviazione diverse, tutti realizzati su misura per il trasporto dei dati. 

### <a name="q-what-is-azure-data-box"></a>D: Che cos'è Azure Data Box?
R. Azure Data Box consente di trasferire terabyte di dati in Azure in modo rapido, conveniente e sicuro. È possibile ordinare il dispositivo Data Box tramite il portale di Azure. Microsoft invia un dispositivo di archiviazione con capacità usabile pari a 80 TB tramite un corriere regionale. 

Dopo aver ricevuto il dispositivo, è possibile configurarlo rapidamente usando l'interfaccia utente Web locale. Copiare i dati dai server in uso nel dispositivo e rispedire il dispositivo ad Azure. Nel data center di Azure i dati vengono caricati automaticamente dal dispositivo in Azure. L'intero processo viene monitorato per tutta la durata dal servizio Data Box nel portale di Azure.

### <a name="q-when-should-i-use-data-box"></a>D: Quando è consigliabile usare Data Box?
R. Quando si ha l'esigenza di trasferire 40 - 500 TB di dati in Azure, l'uso di Data Box può risultare vantaggioso. Per una quantità di dati minore di 40 TB, usare Data Box Disk, mentre per una quantità di dati maggiore di 500 TB, iscriversi a [Data Box Heavy](data-box-heavy-overview.md).

### <a name="q-what-is-the-price-of-data-box"></a>D: Qual è il prezzo di Data Box?
R. Data Box è disponibile a un costo nominale per 10 giorni. Quando si seleziona il modello di prodotto durante la creazione di un ordine nel portale di Azure, vengono visualizzati i costi per il dispositivo. Anche la spedizione è gratuita, ma vengono comunque addebitati i costi per l'archiviazione di Azure. Per altre informazioni, vedere [Prezzi per la famiglia di prodotti Azure Data Box](https://azure.microsoft.com/pricing/details/storage/databox/). 

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-in-one-instance"></a>D: Qual è la quantità massima di dati che è possibile trasferire con Data Box in una sola volta?
R. Data Box ha una capacità nominale di 100 TB e una capacità usabile di 80 TB. Con Data Box è possibile trasferire fino a 80 TB di dati. Per trasferire più dati, è necessario ordinare altri dispositivi.

### <a name="q-how-can-i-check-if-data-box-is-available-in-my-region"></a>D: Come è possibile verificare se Data Box è disponibile nell'area di appartenenza dell'utente? 
R.  Per informazioni sui paesi o sulle aree geografiche in cui Data Box è disponibile, vedere [Aree di disponibilità](data-box-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box"></a>D: In quali aree è possibile archiviare dati con Data Box?
R. Data Box è supportato in tutte le aree negli Stati Uniti, in Europa occidentale e settentrionale, in Francia, nel Regno Unito, in Giappone, in Australia e in Canada. Per altre informazioni, vedere [Disponibilità a livello di area](data-box-overview.md#region-availability).

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues-with-data-box"></a>D: Chi è possibile contattare se si verificano problemi con Data Box?
R. Se si verificano problemi con Data Box, [contattare il supporto tecnico Microsoft](data-box-disk-contact-microsoft-support.md).

### <a name="q-i-have-lost-my-data-box-is-there-a-lost-device-charge"></a>D: Ho smarrito il Data Box. È previsto un addebito per lo smarrimento dei dispositivi?
R. Sì. È previsto un addebito per lo smarrimento o il danneggiamento dei dispositivi. Questo addebito è indicato nella [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage/databox/) e nelle [condizioni d'uso del prodotto](https://www.microsoft.com/licensing/product-licensing/products).


## <a name="order-device"></a>Ordinare il dispositivo

### <a name="q-how-do-i-get-data-box"></a>D: Come è possibile ottenere Data Box? 
R.  Per ottenere Azure Data Box, accedere al portale di Azure e creare un ordine per Data Box. Fornire le informazioni di contatto e i dettagli per le notifiche. Dopo l'emissione dell'ordine, Data Box viene spedito all'utente entro 10 giorni in base alla disponibilità. Per altre informazioni, vedere [Order a Data Box](data-box-deploy-ordered.md) (Ordinare un Data Box).

### <a name="q-i-was-not-able-to-create-a-data-box-order-in-the-azure-portal-why-would-this-be"></a>D: La creazione di un ordine di Data Box nel portale di Azure non è riuscita. Quale può essere il motivo?
R. Se non è stato possibile creare un ordine per Data Box, si è verificato un problema in relazione al tipo di sottoscrizione o all'accesso. 

Controllare la sottoscrizione. Data Box è disponibile solo per le offerte di sottoscrizione Contratto Enterprise (EA) e Cloud Solution Provider (CSP). Se il tipo di sottoscrizione non è elencato, contattare il supporto tecnico Microsoft per aggiornare la sottoscrizione.

Se si dispone di un tipo di offerta supportato per la sottoscrizione, controllare il livello di accesso della sottoscrizione stessa. Per creare un ordine, è necessario essere un collaboratore o un proprietario della sottoscrizione.

### <a name="q-i-ordered-a-couple-of-data-box-devices-i-am-not-able-to-create-any-additional-orders-why-would-this-be"></a>D: Ho ordinato un paio di dispositivi Data Box. Non sono in grado di creare altri ordini. Quale può essere il motivo?
R. È consentito inserire un massimo di cinque ordini attivi per sottoscrizione per limite commerciale (combinazione di paese e area selezionati). Se è necessario ordinare un dispositivo aggiuntivo, contattare il supporto tecnico Microsoft per aumentare il limite correlato alla sottoscrizione.

### <a name="q-when-i-try-to-create-an-order-i-receive-a-notification-that-the-data-box-service-is-not-available-what-does-this-mean"></a>D: Quando si tenta di creare un ordine, si riceve una notifica relativa alla non disponibilità del servizio Data Box. Che cosa significa?
R. Il servizio Data Box non è disponibile per la combinazione di paese e area selezionati. La modifica di questa combinazione può consentire l'uso del servizio Data Box. Per un elenco di aree in cui il servizio è disponibile, vedere [Region availability for Data Box](data-box-overview.md#region-availability) (Disponibilità a livello di area per Data Box).

### <a name="q-i-placed-my-data-box-order-few-days-back-when-will-i-receive-my-data-box"></a>D: Ho inserito l'ordine di Data Box alcuni giorni fa. Quando riceverò il dispositivo Data Box?
R. Quando l'utente inserisce un ordine, Microsoft controlla se un dispositivo è disponibile per l'ordine stesso. Se un dispositivo è disponibile, Microsoft lo spedisce entro 10 giorni. È plausibile che vi siano periodi di domanda elevata. In questo caso l'ordine viene accodato ed è possibile monitorare la modifica dello stato nel portale di Azure. Se l'ordine non viene evaso entro 90 giorni, viene annullato automaticamente.

### <a name="q-i-have-filled-up-my-data-box-with-data-and-need-to-order-another-one-is-there-a-way-to-quickly-place-the-order"></a>D: Ho inserito i dati nel dispositivo Data Box e devo ordinarne un altro. è possibile ordinare rapidamente altri dischi?
R. È possibile clonare l'ordine precedente. La clonazione consente di creare un ordine identico e di modificare solo i dettagli dell'ordine senza dover digitare di nuovo indirizzo, informazioni di contatto e dettagli per le notifiche.

## <a name="configure-and-connect"></a>Configurazione e connessione

### <a name="q-how-do-i-unlock-the-data-box"></a>D: Come si sblocca Data Box? 
R.  Nel portale di Azure accedere all'ordine di Data Box e andare a **Dettagli dispositivo**. Copiare la password di sblocco. Usare questa password per accedere all'interfaccia utente Web locale di Data Box. Per altre informazioni, vedere l'[Esercitazione: Disimballare, cablare e connettersi ad Azure Data Box](data-box-deploy-set-up.md).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box"></a>D: È possibile usare un computer host Linux per connettersi e copiare i dati in Data Box?
R.  Sì. È possibile usare Data Box per connettersi a client SMB e NFS. Per altre informazioni, passare all'elenco dei [sistemi operativi supportati](data-box-system-requirements.md) per il computer host.

### <a name="q-my-data-box-is-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>D: Il dispositivo Data Box è stato inviato, ma ora desidero annullare l'ordine. Per quale motivo il pulsante di annullamento non è disponibile?
R.  È possibile annullare l'ordine solo dopo l'ordine di Data Box e prima che l'ordine venga elaborato. Dopo l'elaborazione dell'ordine di Data Box, non è più possibile annullarlo. 

### <a name="q-can-i-connect-a-data-box-at-the-same-to-multiple-host-computers-to-transfer-data"></a>D: È possibile connettere un Data Box contemporaneamente a più computer host per trasferire i dati?
R. Sì. Più computer host possono connettersi a Data Box per trasferire i dati e più processi di copia possono essere eseguiti in parallelo. Per altre informazioni, vedere l'[Esercitazione: Copiare i dati in Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-can-i-connect-to-both-the-10-gbe-interfaces-on-the-data-box-to-transfer-data"></a>D: È possibile connettersi a entrambe le interfacce a 10 GbE del Data Box per trasferire i dati?
R. Sì. È possibile connettersi a entrambe le interfacce a 10 GbE del Data Box per copiare i dati contemporaneamente. Per altre informazioni su come copiare i dati, passare a [Esercitazione: Copiare i dati in Azure Data Box](data-box-deploy-copy-data.md).

<!--### Q. The network interface on my Data Box is not working. What should I do? 
A. 

### Q. I could not set up Data Box using a Dynamic (DHCP) IP address. Why would this be?
A.

### Q. I could not set up Data Box using a Static IP address. Why would this be?
A.

### Q. I could not set up Data Box on a private network. Why would this be?
A.-->

### <a name="q-the-system-fault-indicator-led-on-the-front-operating-panel-is-on-what-should-i-do"></a>D: Il LED indicatore di errore di sistema sul pannello operativo anteriore è acceso. Cosa devo fare?
R. Se il LED indicatore di errore di sistema è acceso, il sistema non è integro. [Contattare il supporto tecnico Microsoft](data-box-disk-contact-microsoft-support.md) per i passaggi successivi.

### <a name="q-i-cant-access-the-data-box-unlock-password-in-the-azure-portal-why-would-this-be"></a>D: Non riesco ad accedere alla password di sblocco di Data Box nel portale di Azure. Quale può essere il motivo?
R. Se non si è in grado di accedere alla password di sblocco nel portale di Azure, controllare le autorizzazioni per la sottoscrizione e per l'account di archiviazione. Verificare di disporre dell'autorizzazione di collaboratore o proprietario a livello di gruppo di risorse. In caso contrario, è necessario avere almeno l'autorizzazione di ruolo operatore di Data Box per visualizzare le credenziali di accesso.

### <a name="q-is-port-channel-configuration-supported-on-data-box-how-about-mpio"></a>D: La configurazione del canale delle porte è supportata in Data Box? E MPIO?
R. In Data Box le configurazioni del canale delle porte, di MPIO (Multipath IO) o della vLAN non sono supportate.

## <a name="track-status"></a>Monitoraggio dello stato

### <a name="q-how-do-i-track-the-data-box-from-when-i-placed-the-order-to-shipping-the-device-back"></a>D: Come è possibile monitorare Data Box dal momento dell'inserimento dell'ordine alla spedizione del dispositivo? 
R.  È possibile monitorare lo stato dell'ordine di Data Box nel portale di Azure. Quando si crea l'ordine, viene richiesto anche di fornire un indirizzo di posta elettronica per le notifiche. Se ne è stato specificato uno, si riceverà una notifica tramite posta elettronica per tutte le modifiche di stato dell'ordine. Altre informazioni su come [configurare i messaggi di posta elettronica di notifica](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-device"></a>D: Qual è la procedura per restituire il dispositivo? 
R.  Microsoft consente di visualizzare un'etichetta di spedizione sullo schermo E-ink. Se l'etichetta di spedizione non viene visualizzato sullo schermo E-ink, passare a **Panoramica > Scarica etichetta di spedizione**. Scaricare e stampare l'etichetta, inserire l'etichetta nella busta di plastica trasparente applicata sul dispositivo e consegnare il dispositivo presso la sede del vettore di spedizione. 

### <a name="q-i-received-an-email-notification-that-my-device-has-reached-the-azure-datacenter-how-do-i-find-out-if-the-data-upload-is-in-progress"></a>D: Ho ricevuto una notifica di posta elettronica che indica che il dispositivo è arrivato al data center di Azure. Come posso scoprire se il caricamento dei dati è in corso?
R. È possibile accedere all'ordine nel portale di Azure e andare a **Panoramica**. Se il caricamento dei dati in Azure è stato avviato, l'avanzamento della copia viene visualizzato nel riquadro a destra. 

## <a name="migrate-data"></a>Eseguire la migrazione dei dati

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box"></a>D: Quali sono le dimensioni massime dei dati che possono essere usate con Data Box?  
R.  Data Box ha una capacità di archiviazione usabile di 80 TB. È possibile usare un singolo dispositivo Data Box per una quantità di dati compresa tra 40 e 80 TB. Per dimensioni di dati maggiori, fino a 500 TB, è possibile ordinare più dispositivi Data Box. Per dimensioni dei dati superiori a 500 TB, iscriversi a Data Box Heavy.  

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box"></a>D: Quali sono le dimensioni massime di BLOB in blocchi e BLOB di pagine supportate da Data Box? 
R.  Le dimensioni massime dipendono dai limiti di Archiviazione di Azure. Le dimensioni massime dei BLOB in blocchi sono di circa 4,768 TiB e le dimensioni massime dei BLOB di pagine sono di 8 TiB. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../storage/common/storage-scalability-targets.md). 

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>D: In quale modo si può essere certi della sicurezza dei dati in transito? 
R. Sono disponibili più funzionalità di sicurezza implementate per garantire che Data Box sia protetto durante il transito. Alcune funzionalità includono sigilli antimanomissione, rilevamento di manomissione hardware e software, password di sblocco dei dispositivi. Per altre informazioni, vedere [Azure Data Box security and data protection](data-box-security.md) (Sicurezza e protezione dei dati in Azure Data Box).

### <a name="q-how-do-i-copy-the-data-to-the-data-box"></a>D: Qual è la procedura per copiare i dati in Data Box? 
R.  Se si usa un client SMB, è possibile usare uno strumento di copia SMB, ad esempio Robocopy, Diskboss o anche la funzione di trascinamento Esplora file di Windows, per copiare i dati nel dispositivo. 

Se si usa un client NFS, è possibile usare [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) o [Ultracopier](https://ultracopier.first-world.info/). 

Per altre informazioni, vedere l'[Esercitazione: Copiare i dati in Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>D: Ci sono suggerimenti per velocizzare la copia dei dati?
R.  Per velocizzare il processo di copia:

- Usare più flussi di copia dei dati. Con Robocopy, ad esempio, usare l'opzione a thread multipli. Per altre informazioni sul comando esatto da usare, vedere [Esercitazione: Copiare dati in Azure Data Box Disk ed eseguire la verifica](data-box-deploy-copy-data.md).
- Usare più sessioni.
- È opportuno non eseguire la copia tramite una condivisione di rete (operazione che potrebbe essere limitata dalle velocità di rete), ma assicurarsi che i dati si trovino in locale nel computer a cui è collegato il dispositivo Data Box.
- Valutare le prestazioni del computer usato per copiare i dati. Scaricare e usare lo [strumento Bluestop FIO](https://bluestop.org/fio/) per ottenere un benchmark delle prestazioni dell'hardware del server.

<!--### Q. How to speed up the data copy if the source data has small files (KBs or few MBs)?
A.  To speed up the copy process:

- Create a local VHDx on fast storage or create an empty VHD on the HDD/SSD (slower).
- Mount it to a VM.
- Copy files to the VM’s disk.-->


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box"></a>D: È possibile usare più account di archiviazione con Data Box?
R.  Sì. Con Data Box è supportato un massimo di 10 account di archiviazione, per utilizzo generico, classico o BLOB. Sono supportati BLOB sia ad accesso frequente che sporadico. 


## <a name="ship-device"></a>Spedire il dispositivo

<!--### Q. How do I schedule a pickup for my Data Box?--> 

### <a name="q-my-device-was-delivered-but-the-device-seems-to-be-damaged-what-should-i-do"></a>D: Il dispositivo è stato consegnato, ma sembra che sia danneggiato. Cosa devo fare?
R. Non usare il dispositivo se è arrivato danneggiato o se è evidente una prova di manomissione. [Contattare il supporto tecnico Microsoft](data-box-disk-contact-microsoft-support.md) e restituire il dispositivo appena possibile. È anche possibile creare un nuovo ordine di Data Box per un dispositivo sostitutivo. In questo caso per il dispositivo sostitutivo non viene addebitato alcun importo.

### <a name="q-can-i-use-my-own-shipping-carrier-to-ship-data-box"></a>D: È possibile usare un corriere per spedire Data Box?
R. Per il servizio Data Box, Microsoft gestisce la spedizione da e verso il data center di Azure. Se si intende usare il proprio corriere, è possibile usare il servizio di importazione/esportazione di Azure. Per altre informazioni, vedere [Che cos'è il servizio Importazione/Esportazione di Azure?](../storage/common/storage-import-export-service.md)

### <a name="q-will-my-data-box-devices-cross-country-borders-during-shipping"></a>D: I dispositivi Data Box possono essere spediti oltre i confini nazionali?
R. Tutti i dispositivi Data Box vengono spediti all'interno dello stesso paese e non possono superare i confini internazionali. L'unica eccezione è data dagli ordini nell'Unione Europea (UE), in cui i dispositivi possono essere spediti da e verso i paesi dell'Unione Europea. Questo approccio si applica ai dispositivi Data Box e Data Box Heavy.

### <a name="q-e-ink-display-is-not-showing-the-return-shipment-label-what-should-i-do"></a>D: Nello schermo E-ink non viene visualizzata l'etichetta di spedizione di restituzione. Cosa devo fare?
R. Se nello schermo E-ink non viene visualizzata l'etichetta di spedizione di restituzione, seguire questa procedura:
- Rimuovere l'etichetta di spedizione e qualsiasi adesivo della spedizione precedente.
- Accedere al proprio ordine nel portale di Azure. Passare a **Panoramica** e selezionare **Scarica etichetta di spedizione**. Per altre informazioni, vedere [Download shipping label](data-box-portal-admin.md#download-shipping-label) (Scaricare l'etichetta di spedizione).
- Stampare l'etichetta di spedizione e inserirla nella busta in plastica applicata al dispositivo. 
- Verificare che l'etichetta di spedizione sia chiaramente visibile. 

### <a name="q-how-is-my-data-protected-during-transit"></a>D: Come vengono protetti i dati durante il transito? 
R.  Durante il transito le funzionalità seguenti in Data Box consentono di proteggere i dati.
 - I dischi di Data Box vengono crittografati con tecnologia AES a 256 bit. 
 - Il dispositivo è bloccato e richiede una password di sblocco per inserire i dati e per accedervi.
Per altre informazioni, vedere [Data Box security features](data-box-security.md) (Funzionalità di sicurezza di Data Box).  

### <a name="q-i-have-finished-prepare-to-ship-and-shut-down-the-device-can-i-still-add-more-data-to-data-box"></a>D: Dopo aver terminato la fase di preparazione per la spedizione e aver arrestato il dispositivo, è possibile aggiungere altri dati a Data Box?
R. Sì. È possibile accendere il dispositivo e aggiungere altri dati. È necessario eseguire nuovamente l'operazione **Prepara per la spedizione** dopo avere completato la copia dei dati.

### <a name="q-i-received-my-device-and-it-is-not-booting-up-how-do-i-ship-the-device-back"></a>D: Il dispositivo non si avvia. Qual è la procedura per restituire il dispositivo?
R. Se il dispositivo non si avvia, andare all'ordine nel portale di Azure. Scarica un'etichetta di spedizione e applicarla al dispositivo. Per altre informazioni, vedere [Download shipping label](data-box-portal-admin.md#download-shipping-label) (Scaricare l'etichetta di spedizione).

## <a name="verify-and-upload"></a>Verifica e caricamento

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-data-box-back"></a>D: Dopo quanto tempo è possibile accedere ai dati in Azure dopo aver restituito Data Box? 
R.  Quando lo stato dell'ordine per l'operazione **Copia dati** è **Completato**, dovrebbe essere possibile accedere immediatamente ai dati.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>D: Dove vengono collocati i dati in Azure dopo il caricamento?
R.  Quando si copiano i dati in Data Box, a seconda che siano BLOB in blocchi, BLOB di pagine o file di Azure, i dati vengono caricati in uno dei percorsi seguenti nell'account di archiviazione di Azure.
- `https://<storage_account_name>.blob.core.windows.net/<containername>` 
- `https://<storage_account_name>.file.core.windows.net/<sharename>`
 
  In alternativa, è possibile passare all'account di archiviazione di Azure nel portale di Azure e procedere da questa posizione.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>D: Se non si seguono i requisiti di denominazione di Azure per i contenitori, i dati non verranno caricati in Azure?
R.  Se i nomi dei contenitori includono lettere maiuscole, tali nomi verranno convertiti automaticamente in lettere minuscole. Se i nomi non sono conformi per altri aspetti (caratteri speciali, altre lingue e così via), il caricamento non viene eseguito in modo corretto. Per altre informazioni sulle procedure consigliate per la denominazione di condivisioni, contenitori e file, vedere:
- [Naming and referencing shares](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (Assegnazione di nomi e riferimenti a condivisioni)
- [Block blobs and page blob conventions](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) (Convenzioni su BLOB in blocchi e BLOB di pagine)

### <a name="q-how-do-i-verify-the-data-i-copied-onto-data-box"></a>D: Come è possibile verificare i dati copiati in Data Box?
R.  Dopo aver completato la copia dei dati, quando si esegue l'operazione **Prepara per la spedizione** i dati vengono convalidati. Data Box genera un elenco di file e di valori di checksum per i dati durante il processo di convalida. È possibile scaricare l'elenco di file e confrontarlo con i file nei dati di origine. Per altre informazioni, vedere [Prepare to ship](data-box-deploy-picked-up.md#prepare-to-ship) (Preparare per la spedizione).

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-data-box"></a>D: Cosa succede ai dati dopo la restituzione di Data Box?
R.  Dopo che la copia dei dati in Azure è stata completata, i dati vengono cancellati dai dischi di Data Box in modo sicuro in base alle linee guida NIST SP 800-88 revisione 1. Per altre informazioni, vedere [Erasure of data from Data Box](data-box-deploy-picked-up.md#erasure-of-data-from-data-box) (Cancellazione dei dati da Data Box).

## <a name="audit-report"></a>Report di controllo

### <a name="how-does-azure-data-box-service-help-support-customers-chain-of-custody-procedure"></a>In che modo il servizio Azure Data Box supporta i clienti nelle procedure correlate alla catena di custodia?
R.  Il servizio Azure Data Box offre in modo nativo report che è possibile usare per la documentazione della catena di custodia. I log di copia e di controllo sono disponibili nell'account di archiviazione in Azure ed è possibile [scaricare la cronologia ordini](data-box-portal-admin.md#download-order-history) nel portale di Azure dopo il completamento dell'ordine.


### <a name="what-type-of-reporting-is-available-to-support-chain-of-custody"></a>Quale tipo di documentazione è disponibile per supportare la catena di custodia?
R.  Per supportare la catena di custodia, è disponibile la documentazione seguente:

- Logistica di trasporto da UPS.
- Registrazione dell'accensione e dell'accesso condiviso degli utenti.
- File manifesto con un controllo di ridondanza ciclico a 64 bit (CRC-64) o checksum per ogni file inserito correttamente in Data Box.
- Segnalazione di file che non è stato possibile caricare nell'account di archiviazione di Azure.
- Bonifica dei processi del dispositivo Data Box (in base alle norme NIST 800 88R1) dopo che la data è stata copiata nell'account di archiviazione di Azure.

### <a name="are-the-carrier-tracking-logs-from-ups-available"></a>I documenti di accompagnamento del corriere (UPS) sono disponibili? 
R.  I documenti di accompagnamento del corriere vengono acquisiti nella cronologia ordini di Data Box. Tale report è disponibile all'utente dopo la restituzione del dispositivo al data center di Azure e dopo l'eliminazione dei dati dai dischi del dispositivo stesso. Per esigenze di controllo immediate, è anche possibile accedere direttamente al sito Web del corriere con il numero di tracciabilità dell'ordine e ottenere le informazioni necessarie.

### <a name="can-i-transport-the-data-box-to-azure-datacenter"></a>È possibile trasportare direttamente il dispositivo Data Box al data center di Azure? 
R.  No. I data center di Azure non accettano attualmente la consegna di Data Box da parte di clienti o di corrieri diversi da UPS.


## <a name="next-steps"></a>Passaggi successivi

- Vedere i [requisiti di sistema per Data Box](data-box-system-requirements.md).
- Comprendere i [limiti di Data Box](data-box-limits.md).
- Distribuire rapidamente [Azure Data Box](data-box-quickstart-portal.md) nel portale di Azure.
