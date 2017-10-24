---
title: Domande frequenti su File di Azure | Microsoft Docs
description: Risposte ad alcune domande frequenti sul servizio File di Azure.
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/08/2017
ms.author: renash
ms.openlocfilehash: cbe3c3a8f223a4bcc05e19b7a723cf5aff413527
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-about-azure-files"></a>Domande frequenti su File di Azure

## <a name="general"></a>Generale
* **Che cos'è File di Azure?**  
    File di Azure offre condivisioni file completamente gestite nel cloud accessibili tramite il protocollo [SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) standard di settore, anche noto come CIFS o Common Internet File System. Le condivisioni di File di Azure possono essere montate simultaneamente da distribuzioni cloud o locali di Windows, macOS e Linux.

* **Perché File di Azure è utile?**  
   File di Azure consente di creare condivisioni file nel cloud senza dover gestire il sovraccarico di un server fisico o di un dispositivo o un'appliance. Ciò significa che è possibile dedicare meno tempo all'applicazione degli aggiornamenti del sistema operativo e alla sostituzione dei dischi danneggiati, perché queste operazioni vengono eseguite automaticamente. Per altre informazioni sugli scenari in cui è utile usare File di Azure, vedere [Vantaggi offerti da File di Azure](storage-files-introduction.md#why-azure-files-is-useful).

* **Quali sono i vari modi per accedere ai file in File di Azure?**  
    È possibile montare la condivisione file nel computer locale usando il protocollo SMB 3.0 o usare strumenti come [Storage Explorer](http://storageexplorer.com/) per accedere ai file nella condivisione file. Dall'applicazione è possibile usare le librerie client di archiviazione, l'API REST o PowerShell per accedere ai file nella condivisione file di Azure.

* **Che cos'è Sincronizzazione file di Azure?**  
    Sincronizzazione file di Azure (anteprima) consente di centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale, Tutto questo avviene trasformando i sistemi Windows Server in una cache rapida della condivisione file di Azure. È possibile usare qualsiasi protocollo disponibile in Windows Server per accedere ai dati in locale (tra cui SMB, NFS e FTPS) e sono disponibili tutte le cache di cui si ha bisogno in tutto il mondo.

* **Perché usare una condivisione file di Azure rispetto all'archiviazione BLOB di Azure per i dati?**  
    File di Azure e Archiviazione BLOB di Azure consentono entrambi di archiviare grandi quantità di dati nel cloud, ma sono utili per scopi leggermente diversi. Archiviazione BLOB di Azure è utile per le applicazioni native del cloud su vasta scala che necessitano di archiviare dati non strutturati. Per ottimizzare le prestazioni e la scalabilità, Archiviazione BLOB di Azure è un'astrazione di archiviazione più semplice rispetto a un vero e proprio file system. Inoltre, Archiviazione BLOB di Azure è accessibile solo tramite librerie client basate su REST (o direttamente tramite il protocollo basato su REST).

    File di Azure, invece, tende a essere un file system, con tutti i livelli di astrazione dei file rispetto ai sistemi operativi in locale. Come Archiviazione BLOB di Azure, File di Azure offre un'interfaccia REST e librerie client basate su REST ma, a differenza di Archiviazione BLOB di Azure, File di Azure offre anche l'accesso SMB alle condivisioni file di Azure. Ciò significa che è possibile montare direttamente una condivisione file di Azure in macchine virtuali locali o cloud Windows, Linux o Mac OS, senza dover scrivere alcun codice o collegare driver specifici al file system. Inoltre, le condivisioni file di Azure possono essere memorizzate nella cache in file server locali usando Sincronizzazione file di Azure per l'accesso rapido nelle vicinanze del punto in cui vengono usati i dati. 
   
    Per una discussione più dettagliata sulle differenze tra File di Azure e Archiviazione BLOB di Azure, vedere [Decidere quando usare BLOB di Azure, File di Azure o Dischi di Azure](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Per altre informazioni su Archiviazione BLOB di Azure, vedere [Introduzione all'archiviazione BLOB](../blobs/storage-blobs-introduction.md).

* **Perché usare una condivisione file di Azure rispetto a Dischi di Azure?**  
    Un disco di Azure è semplicemente un disco e, di per sé, non è molto utile. Per trarre vantaggio da un disco di Azure, è necessario collegarlo alla macchina virtuale in esecuzione in Azure. La funzionalità Dischi di Azure può essere usata come qualsiasi altro disco in un server locale: come disco di sistema per il sistema operativo, come spazio di swapping per un sistema operativo o come risorsa di archiviazione dedicata per un'applicazione. Un utilizzo interessante di Dischi di Azure consiste nella creazione di un file server nel cloud per l'uso esattamente negli stessi scenari in cui potrebbe essere usata una condivisione file di Azure. La distribuzione di un file server in una macchina virtuale di Azure è una soluzione ottimale ad alte prestazioni per l'archiviazione di file in Azure quando si richiedono opzioni di distribuzione attualmente non supportate da File di Azure (ad esempio, il supporto per il protocollo NFS o l'archiviazione Premium). 

    D'altra parte, l'esecuzione di un file server con Dischi di Azure come archiviazione back-end risulterà in genere molto più costosa rispetto all'uso di una condivisione file di Azure per diversi motivi. Per prima cosa, oltre a dover pagare per l'archiviazione su disco, è anche necessario sostenere il costo dell'esecuzione di una o più macchine virtuali di Azure. In secondo luogo, è anche necessario gestire le macchine virtuali usate per eseguire il file server, ad esempio essere responsabili per gli aggiornamenti del sistema operativo e così via. Infine, se si vuole che i dati siano memorizzati nella cache in locale, occorre configurare e gestire le tecnologie di replica (ad esempio, Replica DFS) a tale scopo.

    Un approccio interessante per sfruttare al meglio File di Azure e un file server ospitato in una macchina virtuale di Azure con Dischi di Azure come risorse di archiviazione back-end consiste nell'installare Sincronizzazione file di Azure nel file server ospitato nella macchina virtuale. Se la condivisione file di Azure è nella stessa area del file server, è possibile abilitare la suddivisione in livelli cloud e impostare la percentuale di spazio disponibile nel volume al massimo (99%). Ciò garantisce la duplicazione minima dei dati consentendo l'uso di tutte le applicazioni desiderate con i file server, ad esempio qualsiasi applicazione che richieda il supporto del protocollo NFS.

    Per informazioni su uno dei modi per configurare una soluzione ad alte prestazioni e a disponibilità elevata in Azure, vedere [Deploying IaaS VM Guest Clusters in Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/) (Distribuzione di cluster guest di VM IaaS in Microsoft Azure). Per una discussione più dettagliata sulle differenze tra File di Azure e Dischi di Azure, vedere [Decidere quando usare BLOB di Azure, File di Azure o Dischi di Azure](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Per altre informazioni su Dischi di Azure, vedere [Panoramica di Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* **Come iniziare a usare File di Azure?**  
    Iniziare a usare File di Azure è semplice: è sufficiente [creare una condivisione file](storage-how-to-create-file-share.md) e montarla nel sistema operativo preferito: 

    - [Eseguire il montaggio in Windows](storage-how-to-use-files-windows.md)
    - [Eseguire il montaggio in Linux](storage-how-to-use-files-linux.md)
    - [Eseguire il montaggio in macOS](storage-how-to-use-files-mac.md)

    Per una guida più dettagliata su come distribuire una condivisione file di Azure per sostituire le condivisioni file di produzione all'interno dell'organizzazione, vedere [Pianificazione per la distribuzione dei file di Azure](storage-files-planning.md).

* **Quali opzioni di ridondanza di archiviazione sono supportate da File di Azure?**  
    File di Azure attualmente supporta solo l'archiviazione con ridondanza locale e l'archiviazione con ridondanza geografica. Per il futuro è prevista l'introduzione del supporto per l'archiviazione con ridondanza della zona e l'archiviazione con ridondanza geografica e accesso in lettura, ma non sono ancora disponibili indicazioni precise sui tempi di attuazione.

* **È possibile aggiungere la funzionalità *x* a File di Azure?**  
    Assolutamente sì, il team di File di Azure è interessato a tutti i commenti e i suggerimenti dei clienti sul servizio offerto. Gli utenti sono invitati a votare le richieste di funzionalità sulla piattaforma [UserVoice per File di Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files). Il team di File di Azure farà il possibile per soddisfare le richieste di nuove funzionalità.

## <a name="azure-file-sync"></a>Sincronizzazione file di Azure
* **Quali sono le aree attualmente supportate per Sincronizzazione file di Azure (anteprima)?**  
    Sincronizzazione file di Azure è attualmente disponibile negli Stati Uniti occidentali, in Europa occidentale, in Australia orientale e in Asia sud-orientale. Di pari passo con la maggiore disponibilità generale, verrà aggiunto il supporto per altre aree geografiche. Per altre informazioni, vedere [Aree di disponibilità](storage-sync-files-planning.md#region-availability).

* **È possibile avere server aggiunti a un dominio e server non inclusi nel dominio nello stesso gruppo di sincronizzazione?**  
    Sì, un gruppo di sincronizzazione può contenere endpoint server con diversa appartenenza ad Active Directory, inclusi server non aggiunti a un dominio. Anche se questa configurazione funziona dal punto di vista tecnico, non è consigliabile usarla come normale configurazione dal momento che gli elenchi di controllo di accesso definiti per i file e le cartelle in un server potrebbero non essere applicabili da altri server nel gruppo di sincronizzazione. Per ottenere risultati ottimali, è consigliabile la sincronizzazione tra server nella stessa foresta di Active Directory, server in foreste di Active Directory diverse con relazioni di trust stabilite o server non inclusi in un dominio, ma non una combinazione di tutti gli elementi precedenti.

* **È stato creato un file direttamente nella condivisione file di Azure su SMB o tramite il portale. Quanto tempo richiede la sincronizzazione del file con i server nel gruppo di sincronizzazione?** 
    Quando si creano o si modificano file in una condivisione file di Azure tramite il portale di Azure, SMB o REST, possono essere necessarie fino a 24 ore per la sincronizzazione delle modifiche nei server nel gruppo di sincronizzazione.

* **Quando lo stesso file viene modificato in due server approssimativamente nello stesso momento, cosa accade?**  
    Sincronizzazione file di Azure usa una semplice strategia di risoluzione dei conflitti: mantenere entrambe le modifiche. Il file modificato più di recente mantiene il nome del file originale. Al nome del file precedente viene aggiunto il nome della macchina di "origine" e il numero del conflitto con questa tassonomia: 
   
    `<FileNameWithoutExtension>-<MachineName>[-#].<ext>`  

    Ad esempio, il primo conflitto di `CompanyReport.docx` diventerebbe `CompanyReport-CentralServer.docx` se `CentralServer` è la macchina in cui si è verificata l'operazione di scrittura meno recente. Il secondo conflitto verrebbe identificato come `CompanyReport-CentralServer-1.docx`.

* **Perché quando si registra un server si ricevono numerose risposte di tipo "sito Web non attendibile"?**  
    Questo errore si verifica perché durante la registrazione del server è stato abilitato il criterio di **sicurezza avanzata di Internet Explorer**. Per altre informazioni su come disabilitare correttamente il criterio di **sicurezza avanzata di Internet Explorer**, vedere [Preparare Windows Server per l'uso con Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md#prepare-windows-servers-for-use-with-azure-file-sync) e [Come distribuire Sincronizzazione file di Azure (anteprima)](storage-sync-files-deployment-guide.md).

* **L'archiviazione con ridondanza geografica è supportata per Sincronizzazione file di Azure?**  
    Sì, sia l'archiviazione con ridondanza locale che l'archiviazione con ridondanza geografica sono supportate da Sincronizzazione file di Azure. In caso di failover dell'archiviazione con ridondanza geografica tra aree abbinate, è consigliabile considerare la nuova area solo come un backup dei dati. Sincronizzazione file di Azure non avvierà automaticamente la sincronizzazione con la nuova area primaria. 

* **Perché la proprietà *Dimensioni su disco* per un file non corrisponde alla proprietà *Dimensioni* dopo l'uso di Sincronizzazione file di Azure?**  
    Esplora file di Windows espone due proprietà, **Dimensioni** e **Dimensioni su disco** per rappresentare le dimensioni di un file. Queste proprietà differiscono leggermente nel significato; **Dimensioni** rappresenta le dimensioni complete del file, mentre **Dimensioni su disco** rappresenta le dimensioni del flusso di file attualmente archiviato su disco. Questi valori possono essere diversi per svariati motivi, ad esempio la compressione, l'uso della deduplicazione dei dati o, in questo caso, la suddivisione in livelli nel cloud con Sincronizzazione file di Azure. Se un file è a livelli in una condivisione file di Azure, le dimensioni su disco saranno pari a zero perché il flusso di file viene archiviato nella condivisione file di Azure, non su disco. È inoltre possibile che un file sia parzialmente a livelli (o parzialmente richiamato), vale a dire che una parte del file è su disco. Questa situazione può verificarsi quando i file vengono letti parzialmente da applicazioni come i lettori multimediali o da utilità di compressione. 

* **Come stabilire se un file è a livelli?**  
    Esistono diversi modi per verificare se un file è a livelli in una condivisione file di Azure:
    
    1. **Controllare gli attributi del file nel file stesso.** A tale scopo, fare clic con il pulsante destro del mouse su un file, passare a **Dettagli** e scorrere verso il basso fino alla proprietà **Attributi**. Un file a livelli avrà il set di attributi seguente:     
        
        | Lettera di attributo | Attributo | Definizione |
        |:----------------:|-----------|------------|
        | Una  | Archiviazione | Indica che deve essere eseguito un backup del file tramite il software di backup. Questo attributo è sempre impostato indipendentemente dal fatto che il file sia a livelli o completamente archiviato su disco. |
        | P | File sparse | Indica che il file è un file sparse, ovvero un tipo specializzato di file disponibile in NTFS per un uso efficiente quando il flusso di file su disco è pressoché vuoto. Sincronizzazione file di Azure usa file sparse perché un file è completamente a livelli, vale a dire che il flusso di file è archiviato solo nel cloud, o parzialmente richiamato, ovvero che parte del file è già su disco. Se un file è completamente richiamato su disco, Sincronizzazione file di Azure lo convertirà da file sparse in un file regolare. |
        | L | Reparse point | Indica che il file ha un reparse point, ovvero un puntatore speciale utilizzabile da un filtro del file system. Sincronizzazione file di Azure usa i reparse point per definire la posizione nel cloud dove è archiviato il file per il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys). Ciò garantisce l'accesso trasparente senza che l'utente finale debba necessariamente sapere che Sincronizzazione file di Azure è in uso o come ottenere l'accesso al file nella condivisione file di Azure. Quando un file viene richiamato completamente, Sincronizzazione file di Azure rimuove il reparse point dal file. |
        | O | Offline | Indica che il contenuto del file non è archiviato interamente o parzialmente su disco. Quando un file viene richiamato completamente, Sincronizzazione file di Azure rimuove questo attributo. |

        ![Finestra di dialogo Proprietà per un file con la scheda Dettagli selezionata](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        È anche possibile visualizzare gli attributi per tutti i file in una cartella aggiungendo il campo **Attributi** alla visualizzazione tabella di Esplora file. A tale scopo, fare clic con il pulsante destro del mouse su una colonna esistente (ad esempio, Dimensioni), selezionare **Altro** e selezionare **Attributi** dall'elenco a discesa.
        
    2. **Usare `fsutil` per verificare la presenza di reparse point in un file.** Come indicato nell'opzione precedente, un file a livelli avrà sempre impostato un reparse point o un puntatore speciale per il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys). È possibile verificare se un file contiene un reparse point con l'utilità `fsutil` in un prompt dei comandi con privilegi elevati o in una sessione di PowerShell:
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        Se il file contiene un reparse point, dovrebbe essere visualizzato **Valore tag di reparse: 0x8000001e**. Questo valore esadecimale è il valore del reparse point di proprietà di Sincronizzazione file di Azure. L'output conterrà inoltre i dati di reparse che rappresentano il percorso del file nella condivisione file di Azure.

        > [!Warning]  
        > Il comando dell'utilità `fsutil reparsepoint` offre anche la possibilità di eliminare un reparse point. Non eseguire questo comando a meno che non sia richiesto dal team di progettazione di Sincronizzazione file di Azure. La sua esecuzione può comportare la perdita di dati. 

* **Un file che si vuole usare è stato suddiviso in livelli. In che modo è possibile richiamarlo su disco per l'uso in locale?**  
    Il modo più semplice per richiamare un file su disco è aprirlo. Il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys) scaricherà automaticamente il file dalla condivisione file di Azure senza che sia necessario eseguire alcuna operazione. Per i tipi di file che possono essere letti parzialmente, ad esempio i file ZIP o multimediali, l'apertura di un file non comporterà il download dell'intero file.

    È inoltre possibile forzare un file perché venga richiamato usando PowerShell. Ad esempio, ciò potrebbe essere utile se si vogliono richiamare molti file contemporaneamente (ad esempio tutti i file all'interno di una cartella). Aprire una sessione di PowerShell per il nodo server in cui è installato Sincronizzazione file di Azure ed eseguire i comandi di PowerShell seguenti:
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

* **Come è possibile forzare la suddivisione in livelli di un file o una directory?**  
    Quando abilitata, la funzionalità di suddivisione in livelli nel cloud consentirà di suddividere automaticamente i file in livelli in base all'ora dell'ultimo accesso e modifica per ottenere la percentuale di spazio disponibile nel volume specificata nell'endpoint cloud. Tuttavia, a volte potrebbe essere necessario forzare manualmente la suddivisione in livelli di un file. Ad esempio, ciò potrebbe essere utile se si salva un file di grandi dimensioni che non si intende riutilizzare per molto tempo e si vuole destinare lo spazio libero sul volume ad altri file e cartelle. È possibile forzare la suddivisione in livelli con i seguenti comandi di PowerShell:

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

* **Perché un file o una cartella è esclusa da Sincronizzazione file di Azure?**
    Per impostazione predefinita, Sincronizzazione file di Azure esclude i file seguenti:
    
    - desktop.ini
    - thumbs.db
    - ehthumbs.db
    - ~$\*.\*
    - \*.laccdb
    - \*.tmp
    - 635D02A9D91C401B97884B82B3BCDAEA.\*

    Per impostazione predefinita, sono escluse anche le cartelle seguenti:

    - \System Volume Information
    - \$RECYCLE.BIN
    - \SyncShareState

* **Sincronizzazione file di Azure supporta Windows Server 2008 R2, Linux o i dispositivi NAS?**
    Attualmente, Sincronizzazione file di Azure supporta solo Windows Server 2016 e Windows Server 2012 R2. In questo momento, non sono previsti altri tipi di supporto, ma Microsoft è interessata a supportare ulteriori piattaforme in base alle esigenze dei clienti. Gli utenti sono invitati a comunicare le piattaforme per cui desiderano supporto sulla piattaforma [UserVoice per File di Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).

## <a name="security-authentication-and-access-control"></a>Sicurezza, autenticazione e controllo di accesso
* **L'autenticazione basata su Active Directory e il controllo di accesso sono supportati da File di Azure?**  
    Attualmente File di Azure non supporta l'autenticazione basata su Active Directory o gli elenchi di controllo di accesso, ma si tratta di un aspetto su cui il team sta lavorando attivamente. Al momento, esistono due modi per aggirare la mancanza dell'autenticazione basata su Active Directory e del controllo di accesso:

    - Le firme di accesso condiviso consentono di generare token con autorizzazioni specifiche che restano valide per un periodo di tempo definito. Ad esempio, è possibile generare un token con accesso di sola lettura a un determinato file con scadenza di 10 minuti. Tutti coloro che possiedono il token nel periodo in cui è valido hanno l'accesso in sola lettura a tale file nell'intervallo di 10 minuti. Le chiavi di firma di accesso condiviso sono attualmente supportate solo tramite API REST o librerie client; è necessario montare la condivisione file di Azure su SMB con chiavi dell'account di archiviazione.

    - Sincronizzazione file di Azure manterrà e replicherà tutti gli elenchi di controllo di accesso (basati su Active Directory o locali) in tutti gli endpoint server con cui viene eseguita la sincronizzazione. Poiché Windows Server è già in grado di eseguire l'autenticazione con Active Directory, Sincronizzazione file di Azure può costituire una valida soluzione temporanea finché non è disponibile il supporto completo per l'autenticazione basata su Active Directory e gli elenchi di controllo di accesso.

* **Come è possibile garantire che la condivisione file di Azure disponga della crittografia dei dati inattivi?**  
    Non è necessario eseguire alcuna operazione per abilitare la crittografia, la [crittografia lato server](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) è attualmente abilitata per impostazione predefinita per File di Azure in tutte le aree pubbliche e i cloud nazionali. 

* **Come è possibile fornire accesso a un file specifico tramite un Web browser?**  
    Le firme di accesso condiviso consentono di generare token con autorizzazioni specifiche che restano valide per un periodo di tempo definito. Ad esempio, è possibile generare un token con accesso in lettura a un determinato file per un periodo di tempo specifico. Tutti gli utenti che hanno l'URL in corso di validità possono accedere al file direttamente da qualsiasi Web browser. È possibile generare facilmente le chiavi di firma di accesso condiviso dall'interfaccia utente, ad esempio con Storage Explorer.

* **È possibile specificare autorizzazioni di sola lettura o di sola scrittura per le cartelle all'interno della condivisione?**  
    Se la condivisione file viene montata tramite SMB, non è disponibile questo livello di controllo sulle autorizzazioni. È tuttavia possibile ottenere lo stesso risultato creando una firma di accesso condiviso tramite l'API REST o le librerie client.
    
* **Quali sono i criteri di conformità dei dati supportati per File di Azure?**  
   L'esecuzione di File di Azure si basa sulla stessa architettura di archiviazione di altri servizi di Archiviazione di Azure e applica gli stessi criteri di conformità dei dati. Altre informazioni sulla conformità dei dati di archiviazione di Azure sono disponibili per il download; fare riferimento al [documento di Protezione dati di Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409).

## <a name="on-premises-access"></a>Accesso locale
* **È necessario usare Azure ExpressRoute per connettersi a File di Azure o per usare Sincronizzazione file di Azure in locale?**  
    No, ExpressRoute non è richiesto per accedere a una condivisione file di Azure. Se si esegue il montaggio di una condivisione file di Azure direttamente in locale, è necessario solo che la porta 445 (TCP in uscita), ovvero la porta su cui SMB comunica, sia aperta per l'accesso a Internet. Se si usa Sincronizzazione file di Azure, occorre solo la porta 443 (TCP in uscita) per l'accesso HTTPS (non è richiesto alcun SMB). Tuttavia, ExpressRoute può essere usato con entrambe le opzioni di accesso, se richiesto.

* **Come è possibile montare una condivisione file di Azure nel computer locale?**  
    Se la porta 445 (TCP in uscita) è aperta e il client supporta il protocollo SMB 3.0, ad esempio su Windows 10 o Windows Server 2016, è possibile montare la condivisione file usando il protocollo SMB. Se la porta 445 è bloccata dai criteri dell'organizzazione o dall'ISP, è possibile usare Sincronizzazione file di Azure per accedere alla condivisione file di Azure.

## <a name="backup"></a>Backup
* **Come eseguire il backup nella condivisione file di Azure?**  
    È possibile usare snapshot di condivisione periodici (anteprima) per la protezione da eliminazioni accidentali. È possibile usare AzCopy, RoboCopy o uno strumento di backup di terze parti per eseguire il backup di una condivisione di file montati.

## <a name="share-snapshots"></a>Snapshot di condivisione
### <a name="share-snapshots---general"></a>Snapshot di condivisione - Generale
* **Che cos'è uno snapshot di condivisione?**
    Gli snapshot di condivisione file di Azure consentono di creare versioni di sola lettura delle condivisioni file. Permettono inoltre di copiare una versione precedente del contenuto nella stessa condivisione o in un percorso alternativo in Azure o in locale per ulteriori modifiche. Per altre informazioni sugli snapshot di condivisione, vedere [Azure Files share snapshot (preview) overview](storage-snapshots-files.md) (Panoramica sugli snapshot di condivisione file di Azure (anteprima)).

* **Dove vengono archiviati gli snapshot di condivisione?**
    Gli snapshot di condivisione vengono archiviati nello stesso account di archiviazione della condivisione file.

* **Ci sono implicazioni per le prestazioni?**
    Gli snapshot di condivisione non comportano alcun overhead delle prestazioni.

* **Gli snapshot di condivisione sono coerenti con l'applicazione?**
    No. Gli snapshot di condivisione non sono coerenti con l'applicazione. Sarà necessario scaricare le operazioni di scrittura dall'applicazione nella condivisione prima di acquisire lo snapshot di condivisione.

* **Esistono limiti al numero degli snapshot di condivisione?**
    È previsto un limite di 200 snapshot di condivisione che File di Azure è in grado di mantenere. Gli snapshot di condivisione non vengono conteggiati per la quota di condivisione, pertanto non esiste alcun limite per condivisione dello spazio totale usato da tutti gli snapshot di condivisione. Si applicano i limiti dell'account di archiviazione. Dopo 200 snapshot di condivisione, quelli meno recenti dovranno essere eliminati per poter creare nuovi snapshot di condivisione.

### <a name="create-share-snapshots"></a>Creare snapshot di condivisione
* **È possibile creare snapshot di condivisione di singoli file?**
    Gli snapshot di condivisione vengono creati a livello di condivisione file. È possibile ripristinare singoli file dallo snapshot di condivisione file, ma non è possibile creare snapshot di condivisione a livello di file. Tuttavia, se è stato acquisito uno snapshot di condivisione a livello di condivisione e si vogliono elencare gli snapshot di condivisione in cui un determinato file è stato modificato, è possibile farlo dall'esperienza "Versioni precedenti" in una condivisione montata in Windows.

* **È possibile creare snapshot di condivisione di una condivisione file crittografata?**
    È possibile creare uno snapshot di condivisione delle condivisioni file di Azure con la crittografia dei dati inattivi abilitata. È possibile ripristinare i file da uno snapshot di condivisione in una condivisione file crittografata. Se la condivisione è crittografata, anche lo snapshot di condivisione sarà crittografato.

* **Gli snapshot di condivisione hanno ridondanza geografica?**
    Uno snapshot di condivisione avrà la stessa ridondanza della condivisione file di Azure per cui è stato creato. Se è stata scelta l'archiviazione con ridondanza geografica per l'account, anche lo snapshot di condivisione verrà archiviato in modo ridondante nell'area abbinata.

### <a name="manage-share-snapshots"></a>Gestire snapshot di condivisione
* **È possibile esplorare gli snapshot di condivisione da Linux?**
    È possibile usare l'interfaccia della riga di comando di Azure 2.0 per creare, elencare, esplorare ed eseguire ripristini in Linux.

* **È possibile copiare gli snapshot di condivisione in un altro account di archiviazione?**
    È possibile copiare file dagli snapshot di condivisione in un altro percorso, ma non gli snapshot di condivisione stessi.

### <a name="restore-data-from-share-snapshots"></a>Eseguire il ripristino dei dati da snapshot di condivisione
* **È possibile alzare di livello uno snapshot di condivisione alla condivisione di base?**
    È possibile solo copiare i dati da uno snapshot di condivisione in qualsiasi destinazione desiderata. Tuttavia, non è possibile alzare di livello uno snapshot di condivisione alla condivisione di base.

* **È possibile ripristinare dati da uno snapshot di condivisione in un account di archiviazione diverso?**
    Sì. I file di uno snapshot di condivisione possono essere copiati nel percorso originale o in uno alternativo, incluso lo stesso account di archiviazione o uno diverso nelle stesse aree o in aree diverse. È inoltre possibile copiare i file in un cloud locale o in qualsiasi altro cloud.    
  
### <a name="cleanup-share-snapshot"></a>Pulizia di uno snapshot di condivisione
* **È possibile eliminare la condivisione ma non gli snapshot di condivisione?**
    Non sarà possibile eliminare la condivisione se nella stessa vi sono snapshot di condivisione attivi. È disponibile un'API per l'eliminazione degli snapshot di condivisione insieme alla condivisione stessa ed è possibile ottenere lo stesso risultato anche dal portale di Azure.

* **Cosa accade agli snapshot di condivisione se si elimina l'account di archiviazione?**
    Se si elimina l'account di archiviazione, verranno eliminati anche gli snapshot di condivisione.

## <a name="billing-and-pricing"></a>Prezzi e fatturazione
* **Il traffico di rete tra una macchina virtuale di Azure e una condivisione file viene conteggiato come larghezza di banda esterna e addebitato alla sottoscrizione?**  
    Se la condivisione file e la macchina virtuale si trovano nella stessa area di Azure, il traffico tra di esse è disponibile. Se si trovano in aree diverse, il traffico che viene scambiato viene addebitato come larghezza di banda esterna.

* **Qual è il costo degli snapshot di condivisione?**
     Durante la fase di anteprima non è previsto alcun addebito per la capacità di snapshot di condivisione. Saranno applicati i costi di transazione e di uscita dell'archiviazione Standard. Dopo la disponibilità generale, sia la capacità che le transazioni in uno snapshot di condivisione verranno addebitate.
     
     Gli snapshot di condivisione sono di natura incrementale. Lo snapshot di condivisione di base è la condivisione stessa. Tutti gli snapshot di condivisione successivi sono incrementali e archivieranno solo le differenze rispetto allo snapshot di condivisione precedente. È previsto un addebito solo per il contenuto modificato. Se si dispone di una condivisione con 100 GB di dati, ma solo 5 GB sono stati modificati dall'ultimo snapshot di condivisione, lo snapshot di condivisione consumerà solo 5 GB aggiuntivi e verranno addebitati solo 105 GB. Vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage/files/) per altre informazioni sui costi di transazione e di uscita standard.

## <a name="scale-and-performance"></a>Scalabilità e prestazioni
* **Quali sono i limiti di scalabilità di File di Azure?**  
    Per informazioni sugli obiettivi di scalabilità e prestazioni di File di Azure, vedere [Obiettivi di scalabilità e prestazioni](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#scalability-targets-for-blobs-queues-tables-and-files).

* **Quanti client possono accedere allo stesso file contemporaneamente?**  
    C'è una quota pari a 2000 handle aperti su un singolo file. Al raggiungimento di 2000 handle aperti, viene restituito un errore che indica che la quota è stata raggiunta.

* **Le prestazioni sono ridotte quando si prova a decomprimere i file in File di Azure. Cosa devo fare?**  
    Per trasferire grandi quantità di file in File di Azure, è consigliabile usare AzCopy (Windows, anteprima per Linux/Unix) o Azure PowerShell che sono strumenti ottimizzati per il trasferimento in rete.

* **È stata montata una condivisione file di Azure in Windows Server 2012 R2 o Windows 8.1 e le prestazioni sono ridotte. Perché?**  
    Si riscontra un problema noto durante il montaggio di una condivisione file di Azure in Windows Server 2012 R2 e Windows 8.1 che è stato corretto nell'aggiornamento cumulativo di aprile 2014 per Windows 8.1 e Windows Server 2012 R2. Verificare che tutte le istanze di Windows Server 2012 R2 e Windows 8.1 abbiano questa patch applicata per ottenere prestazioni ottimali. Naturalmente, è sempre consigliabile ottenere tutte le patch di Windows tramite Windows Update. Per altre informazioni, vedere l'articolo della Knowledge Base associato, [Rallentamento delle prestazioni quando si accede a File di Azure da Windows 8.1 o Windows Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funzionalità e interoperabilità con altri servizi
* **Il controllo di condivisione file per un cluster di failover è uno dei casi d'uso per File di Azure?**  
    Questo servizio non è attualmente supportato per una condivisione file di Azure. Per altre informazioni su come configurare questo servizio per Archiviazione BLOB di Azure, vedere [Distribuire un cloud di controllo per un cluster di failover](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* **Esiste un'operazione di ridenominazione nell'API REST?**  
    Attualmente non è possibile.

* **È possibile usare condivisioni annidate, ovvero una condivisione all'interno di un'altra condivisione?**  
    No. La condivisione file è il driver virtuale che è possibile montare, quindi le condivisioni annidate non sono supportate.

* **Uso di File di Azure con IBM MQ**  
    IBM ha rilasciato un documento per guidare i clienti di IBM MQ nella configurazione di File di Azure con tale servizio. Per altre informazioni, vedere la pagina relativa alla [How to setup IBM MQ Multi instance queue manager with Microsoft Azure File Service](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)(Configurazione di IBM MQ Multi Instance Queue Manager (MIQM) con il servizio file di Microsoft Azure).

## <a name="see-also"></a>Vedere anche
* [Risolvere i problemi di File di Azure in Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Risolvere i problemi di File di Azure in Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Risolvere i problemi di Sincronizzazione file di Azure (anteprima)](storage-sync-files-troubleshoot.md)
