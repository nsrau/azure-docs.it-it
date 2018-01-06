---
title: Domande frequenti per File di Azure | Microsoft Docs
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
ms.date: 12/04/2017
ms.author: renash
ms.openlocfilehash: e25a6555e06a437259cddcc46c27add5f8b2ad8b
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="frequently-asked-questions-about-azure-files"></a>Domande frequenti su File di Azure
[File di Azure](storage-files-introduction.md) offre condivisioni file completamente gestite nel cloud, accessibili tramite il [protocollo SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) standard del settore, anche noto come CIFS o Common Internet File System. È possibile montare le condivisioni file di Azure simultaneamente da distribuzioni cloud o locali di Windows, Linux e macOS. È anche possibile memorizzare nella cache le condivisioni file di Azure nei computer Windows Server tramite Sincronizzazione file di Azure (anteprima) per l'accesso rapido in prossimità della posizione in cui vengono usati i dati.

Questo articolo risponde ad alcune domande frequenti sulle caratteristiche e funzionalità di File di Azure, tra cui l'uso di Sincronizzazione file di Azure con File di Azure. Se non è presente la risposta a una domanda specifica, è possibile contattare Microsoft tramite i seguenti canali (in ordine di escalation):

1. Sezione dei commenti di questo articolo.
2. [Forum di Archiviazione di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [UserVoice per File di Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Supporto tecnico Microsoft. Per creare una nuova richiesta di supporto, nel portale di Azure, nella scheda **Guida**, selezionare **Guida e supporto** e quindi selezionare **Nuova richiesta di supporto**.

## <a name="general"></a>Generale
* <a id="why-files-useful"></a>
**Il file di Azure è utile?**  
   È possibile usare File di Azure per creare condivisioni file nel cloud, senza la responsabilità di gestire l'overhead di un server fisico, un dispositivo o un'appliance. Si possono così delegare le operazioni monotone, incluse l'installazione degli aggiornamenti del sistema operativo e la sostituzione di dischi danneggiati. Per altre informazioni sugli scenari in cui è utile usare File di Azure, vedere [Vantaggi offerti da File di Azure](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
**Quali sono diversi modi per accedere ai file nel file di Azure?**  
    È possibile montare la condivisione file nel computer locale usando il protocollo SMB 3.0 o usare strumenti come [Azure Storage Explorer](http://storageexplorer.com/) per accedere ai file nella condivisione file. Dall'applicazione è possibile usare le librerie client di archiviazione, le API REST, PowerShell o l'interfaccia della riga di comando di Azure per accedere ai file nella condivisione file di Azure.

* <a id="what-is-afs"></a>
**Che cos'è Azure File di sincronizzazione?**  
    È possibile usare Sincronizzazione file di Azure per centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Sincronizzazione file di Azure trasforma i computer Windows Server in una cache rapida della condivisione file di Azure. È possibile usare qualsiasi protocollo disponibile in Windows Server per accedere ai dati in locale, tra cui SMB, NFS (Network File System) e FTPS (File Transfer Protocol Service). Si può usare qualsiasi numero di cache necessario in tutto il mondo.

* <a id="files-versus-blobs"></a>
**Perché utilizzare una condivisione di file di Azure e archiviazione Blob di Azure per i dati?**  
    File di Azure e Archiviazione BLOB di Azure consentono entrambi di archiviare grandi quantità di dati nel cloud, ma sono utili per scopi leggermente diversi. 
    
    Archiviazione BLOB di Azure è utile per le applicazioni native del cloud su vasta scala che necessitano di archiviare dati non strutturati. Per ottimizzare le prestazioni e la scalabilità, Archiviazione BLOB di Azure è un'astrazione di archiviazione più semplice rispetto a un vero e proprio file system. È possibile accedere ad Archiviazione BLOB di Azure solo tramite librerie client basate su REST (o direttamente tramite il protocollo basato su REST).

    File di Azure è nello specifico un file system. File di Azure racchiude tutte le funzionalità per la gestione dei file che gli utenti hanno imparato a conoscere e ad apprezzare grazie ad anni di esperienza con i sistemi operativi in locale. Come Archiviazione BLOB di Azure, File di Azure offre un'interfaccia REST e librerie client basate su REST. Diversamente da Archiviazione BLOB di Azure, File di Azure consente l'accesso SMB alle condivisioni file di Azure. L'uso di SMB consente di montare direttamente una condivisione file di Azure in Windows, Linux o macOS, sia in macchine virtuali locali che nel cloud, senza dover scrivere codice o collegare driver speciali al file system. È anche possibile memorizzare nella cache le condivisioni file di Azure in file server locali tramite Sincronizzazione file di Azure (anteprima), per l'accesso rapido in prossimità della posizione in cui vengono usati i dati. 
   
    Per una descrizione più dettagliata delle differenze tra File di Azure e Archiviazione BLOB di Azure, vedere [Decidere quando usare BLOB di Azure, File di Azure o Dischi di Azure](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Per altre informazioni su Archiviazione BLOB di Azure, vedere [Introduzione all'archiviazione BLOB](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Perché usare una condivisione file di Azure invece di Dischi di Azure?**  
    Un disco in Dischi di Azure è semplicemente un disco. Un disco autonomo non è moto utile di per sé. Per ottenere valore da Dischi di Azure, è necessario collegare un disco a una macchina virtuale in esecuzione in Azure. È possibile usare Dischi di Azure per qualsiasi scopo per cui si può usare un disco per un server locale, come disco del sistema operativo, come spazio di swapping per un sistema operativo o come spazio di archiviazione dedicato per un'applicazione. Un uso interessante di Dischi di Azure consiste nella creazione di un file server nel cloud per usarlo esattamente negli stessi scenari in cui si potrebbe usare una condivisione file di Azure. La distribuzione di un file server in Macchine virtuali di Azure è una soluzione ad alte prestazioni per ottenere spazio di archiviazione per i file in Azure quando sono richieste opzioni di distribuzione attualmente non supportate da File di Azure (ad esempio, il supporto per il protocollo NFS o l'archiviazione Premium). 

    L'esecuzione di un file server con Dischi di Azure come archiviazione back-end, tuttavia, risulta in genere molto più onerosa rispetto all'uso di una condivisione file di Azure per diversi motivi. Per prima cosa, oltre a dover pagare per l'archiviazione su disco, è anche necessario sostenere il costo dell'esecuzione di una o più macchine virtuali di Azure. In secondo luogo, è anche necessario gestire le macchine virtuali usate per eseguire il file server. Ad esempio, si mantiene la responsabilità degli aggiornamenti del sistema operativo. Infine, se si vuole che i dati siano memorizzati nella cache in locale, occorre configurare e gestire le tecnologie di replica, come Replica DFS (DFSR) a tale scopo.

    Un approccio per sfruttare al meglio File di Azure e un file server ospitato in Macchine virtuali di Azure (oltre a usare Dischi di Azure come risorsa di archiviazione back-end) consiste nell'installare Sincronizzazione file di Azure in un file server ospitato in una macchina virtuale nel cloud. Se la condivisione file di Azure è nella stessa area del file server, è possibile abilitare la suddivisione in livelli nel cloud e impostare la percentuale di spazio disponibile nel volume sul massimo (99%). Ciò assicura una duplicazione minima dei dati. È anche possibile usare qualsiasi applicazione con i file server, ad esempio applicazioni che richiedono il supporto del protocollo NFS.

    Per informazioni su un'opzione per configurare un file server ad alte prestazioni e a disponibilità elevata in Azure, vedere [Deploying IaaS VM Guest Clusters in Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/) (Distribuzione di cluster guest di macchine virtuali IaaS in Microsoft Azure). Per una descrizione più dettagliata sulle differenze tra File di Azure e Dischi di Azure, vedere [Decidere quando usare BLOB di Azure, File di Azure o Dischi di Azure](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Per altre informazioni su Dischi di Azure, vedere [Panoramica di Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>
**Come si può iniziare con i file di Azure?**  
   Iniziare a usare File di Azure è semplice. Prima di tutto, [creare una condivisione file](storage-how-to-create-file-share.md) e quindi montarla nel sistema operativo preferito: 

    * [Montare in Windows](storage-how-to-use-files-windows.md)
    * [Montare in Linux](storage-how-to-use-files-linux.md)
    * [Montare in macOS](storage-how-to-use-files-mac.md)

   Per una guida più dettagliata sulla distribuzione di una condivisione file di Azure per sostituire le condivisioni file di produzione all'interno dell'organizzazione, vedere [Pianificazione per la distribuzione dei file di Azure](storage-files-planning.md).

* <a id="redundancy-options"></a>
**Quali opzioni di ridondanza di archiviazione sono supportate da file di Azure?**  
    File di Azure supporta attualmente solo l'archiviazione con ridondanza locale (LRS) e l'archiviazione con ridondanza geografica (GRS). Per il futuro è prevista l'introduzione del supporto per l'archiviazione con ridondanza della zona (ZRS) e l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS), ma non sono ancora disponibili indicazioni precise sui tempi di attuazione.

* <a id="tier-options"></a>
**I livelli di archiviazione sono supportati in file di Azure?**  
    File di Azure supporta attualmente solo il livello di archiviazione Standard. Al momento non sono disponibili indicazioni precise sui tempi di attuazione per il supporto dell'archiviazione Premium e dell'archiviazione ad accesso sporadico. 
    
    > [!NOTE]
    > Non è possibile creare condivisioni file di Azure dagli account di archiviazione solo BLOB o dagli account di archiviazione Premium.

* <a id="give-us-feedback"></a>
**Si desidera effettivamente una funzionalità specifica aggiunto al file di Azure. File di Azure?**  
    Il team di File di Azure è interessato a tutti i commenti e i suggerimenti dei clienti sul servizio offerto. Gli utenti sono invitati a votare le richieste di funzionalità in [UserVoice per File di Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files). Il team di File di Azure farà il possibile per soddisfare le richieste di nuove funzionalità.

## <a name="azure-file-sync"></a>Sincronizzazione file di Azure

* <a id="afs-region-availability"></a>
**Le aree sono supportate per la sincronizzazione di File di Azure (anteprima)?**  
    Sincronizzazione di File di Azure è attualmente disponibile in Stati Uniti orientali, Stati Uniti occidentali, Europa occidentale, Australia orientale e Asia sudorientale. Verrà aggiunto il supporto per altre aree man mano che si procede verso il rilascio della versione con disponibilità a livello generale. Per altre informazioni, vedere [Disponibilità a livello di area](storage-sync-files-planning.md#region-availability).

* <a id="cross-domain-sync"></a>
**È possibile installare i server appartenenti a un dominio e non appartenenti a un dominio nello stesso gruppo di sincronizzazione?**  
    Sì. Un gruppo di sincronizzazione può contenere endpoint server con appartenenze diverse ad Active Directory, anche se non aggiunti a un dominio. Anche se questa configurazione funziona dal punto di vista tecnico, non è consigliabile usarla come configurazione tipica dato che gli elenchi di controllo di accesso (ACL) definiti per i file e le cartelle in un server potrebbero non essere applicati da altri server nel gruppo di sincronizzazione. Per ottenere risultati ottimali, è consigliabile la sincronizzazione tra server nella stessa foresta di Active Directory, server in foreste di Active Directory diverse ma con relazioni di trust stabilite o server non inclusi in un dominio. È consigliabile evitare di usare una combinazione di queste configurazioni.

* <a id="afs-change-detection"></a>
**Aver creato un file direttamente nella condivisione file di Azure tramite SMB o nel portale. Quanto tempo occorre per la sincronizzazione del file nei server nel gruppo di sincronizzazione?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Cosa accade quando lo stesso file viene modificato in due server approssimativamente nello stesso momento?**  
    Sincronizzazione file di Azure usa una semplice strategia di risoluzione dei conflitti: vengono mantenute entrambe le modifiche ai file modificati nei due server contemporaneamente. Per la modifica più di recente viene mantenuto il nome del file originale. Al nome del file precedente viene aggiunto il nome del computer di "origine" e il numero del conflitto con questa tassonomia: 
   
    \<NomeFileSenzaEstensione\>-\<NomeComputer\>\[-#\].\<est\>  

    Ad esempio, il primo conflitto per ReportAziendale.docx diventerebbe ReportAziendale-ServerCentrale.docx se ServerCentrale è il computer in cui è stata eseguita l'operazione di scrittura meno recente. Il nome per il secondo conflitto sarebbe ReportAziendale-ServerCentrale-1.docx.

* <a id="afs-storage-redundancy"></a>
**Archiviazione con ridondanza geografica è supportato per la sincronizzazione di File di Azure?**  
    Sì. File di Azure supporta sia l'archiviazione con ridondanza locale (LRS) che l'archiviazione con ridondanza geografica (GRS). In caso di failover dell'archiviazione con ridondanza geografica tra aree abbinate, è consigliabile considerare la nuova area solo come backup dei dati. Sincronizzazione file di Azure non avvia automaticamente la sincronizzazione con la nuova area primaria. 

* <a id="sizeondisk-versus-size"></a>
**Perché non di *dimensioni su disco* proprietà per una corrispondenza di file di *dimensioni* proprietà dopo l'utilizzo di sincronizzazione di File di Azure?**  
    Esplora file di Windows espone due proprietà per rappresentare le dimensioni di un file: **Dimensioni** e **Dimensioni su disco**. Queste proprietà hanno un significato leggermente diverso. **Dimensioni** rappresenta le dimensioni complete del file. **Dimensioni su disco** rappresenta le dimensioni del flusso di file archiviato sul disco. I valori di queste proprietà possono essere diversi per svariati motivi, ad esempio la compressione, l'uso della deduplicazione dei dati o la suddivisione in livelli nel cloud con Sincronizzazione file di Azure. Se un file è archiviato a livelli in una condivisione file di Azure, le dimensioni su disco sono pari a zero perché il flusso di file viene archiviato nella condivisione file di Azure e non sul disco. È anche possibile che un file sia parzialmente a livelli (o parzialmente richiamato). In un file parzialmente a livelli, parte del file è su disco. Questa situazione può verificarsi quando i file vengono letti parzialmente da applicazioni come i lettori multimediali o le utilità di compressione. 

* <a id="is-my-file-tiered"></a>
**Come stabilire se un file è stato a più livelli**  
    Esistono diversi modi per verificare se un file è archiviato a livelli in una condivisione file di Azure:
    
   *  **Controllare gli attributi del file nel file stesso.**
     A tale scopo, fare clic con il pulsante destro del mouse su un file, scegliere **Dettagli** e quindi scorrere verso il basso fino alla proprietà **Attributi**. Un file archiviato a livelli avrà il set di attributi seguente:     
        
        | Lettera di attributo | Attributo | Definizione |
        |:----------------:|-----------|------------|
        | Una  | Archiviazione | Indica che deve essere eseguito un backup del file tramite il software di backup. Questo attributo è sempre impostato indipendentemente dal fatto che il file sia archiviato a livelli o archiviato completamente su disco. |
        | P | File sparse | Indica che il file è un file sparse. Un file sparse è un tipo specializzato di file offerto da NTFS per un uso efficiente quando il flusso di file su disco è pressoché vuoto. Sincronizzazione file di Azure usa i file sparse perché un file è completamente archiviato a livelli o parzialmente richiamato. In un file completamente archiviato a livelli, il flusso di file viene archiviato nel cloud. In un file parzialmente richiamato, tale parte del file è già su disco. Se un file è completamente richiamato su disco, Sincronizzazione file di Azure lo converte da file sparse in un file regolare. |
        | L | Reparse point | Indica che il file contiene un reparse point. Un reparse point è un puntatore speciale utilizzabile da un filtro del file system. Sincronizzazione file di Azure usa i reparse point per definire la posizione nel cloud dove è archiviato il file per il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys). È supportato l'accesso facile. Non è necessario che gli utenti sappiano che è in uso Sincronizzazione file di Azure o come ottenere l'accesso al file nella condivisione file di Azure. Quando un file viene richiamato completamente, Sincronizzazione file di Azure rimuove il reparse point dal file. |
        | O | Offline | Indica che il contenuto del file non è archiviato interamente o parzialmente su disco. Quando un file viene richiamato completamente, Sincronizzazione file di Azure rimuove questo attributo. |

        ![Finestra di dialogo Proprietà per un file con la scheda Dettagli selezionata](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        È possibile visualizzare gli attributi per tutti i file in una cartella aggiungendo il campo **Attributi** alla visualizzazione tabella di Esplora file. A tale scopo, fare clic con il pulsante destro del mouse su una colonna esistente (ad esempio, **Dimensioni**), scegliere **Altro** e quindi selezionare **Attributi** nell'elenco a discesa.
        
   * **Usare `fsutil` per verificare la presenza di reparse point in un file.**
       Come descritto per l'opzione precedente, per un file archiviato a più livelli è sempre impostato un reparse point. Un reparse point è un puntatore speciale per il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys). Per verificare se un file contiene un reparse point, eseguire l'utilità `fsutil` in un prompt dei comandi con privilegi elevati o in una sessione di PowerShell:
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        Se il file contiene un reparse point, dovrebbe essere visualizzato **Valore tag di reparse: 0x8000001e**. Questo valore esadecimale è il valore del reparse point di proprietà di Sincronizzazione file di Azure. L'output contiene anche i dati di reparse che rappresentano il percorso del file nella condivisione file di Azure.

        > [!WARNING]  
        > Il comando dell'utilità `fsutil reparsepoint` offre anche la possibilità di eliminare un reparse point. Non eseguire questo comando, a meno che non venga richiesto dal team di progettazione di Sincronizzazione file di Azure. L'esecuzione di questo comando può causare la perdita di dati. 

* <a id="afs-recall-file"></a>**Un file da usare è stato archiviato a livelli. Come è possibile richiamare il file su disco per usarlo in locale?**  
    Il modo più semplice per richiamare un file su disco è aprirlo. Il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys) scarica automaticamente il file dalla condivisione file di Azure senza che sia necessario eseguire alcuna operazione. Per i tipi di file che possono essere letti parzialmente, ad esempio i file ZIP o multimediali, l'apertura di un file non comporta il download dell'intero file.

    È anche possibile usare PowerShell per forzare il richiamo di un file. Questa opzione potrebbe essere utile per richiamare più file contemporaneamente, ad esempio tutti i file all'interno di una cartella. Aprire una sessione di PowerShell per il nodo server in cui è installato Sincronizzazione file di Azure e quindi eseguire i comandi di PowerShell seguenti:
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

* <a id="afs-force-tiering"></a>
**Come forzare un file o directory in livelli?**  
    Quando la funzionalità di suddivisione in livelli nel cloud è abilitata, i file vengono archiviati automaticamente a livelli in base all'ora dell'ultimo accesso e dell'ultima modifica per ottenere la percentuale di spazio disponibile nel volume specificata nell'endpoint cloud. Tuttavia, a volte potrebbe essere necessario forzare manualmente l'archiviazione a livelli di un file. Ciò potrebbe essere utile se si salva un file di grandi dimensioni che non si intende riutilizzare per molto tempo e si vuole destinare lo spazio libero sul volume ad altri file e cartelle. È possibile forzare la suddivisione in livelli con i comandi seguenti di PowerShell:

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

* <a id="afs-effective-vfs"></a>
**Come è *spazio disponibile nel volume* interpretate quando si dispone di più endpoint di server in un volume?**  
    Quando in un volume sono presenti più endpoint server, la soglia valida dello spazio disponibile del volume è data dal valore più elevato specificato per tale spazio su qualsiasi endpoint server in tale volume. I file vengono suddivisi in livelli in base ai criteri di uso indipendentemente dall'endpoint server di appartenenza. Ad esempio, se sono presenti due endpoint server in un volume, Endpoint1 ed Endpoint2, dove Endpoint1 è associato a una soglia di spazio disponibile del volume pari a 25% mentre Endpoint2 è associato a una soglia di spazio disponibile del volume pari a 50%, la soglia valida per entrambi gli endpoint server sarà pari a 50%.

* <a id="afs-files-excluded"></a>
**I file o cartelle vengono automaticamente esclusi dalla sincronizzazione File di Azure?**  
    Per impostazione predefinita, Sincronizzazione file di Azure esclude i file seguenti:
    * desktop.ini
    * thumbs.db
    * ehthumbs.db
    * ~$\*.\*
    * \*.laccdb
    * \*.tmp
    * 635D02A9D91C401B97884B82B3BCDAEA.\*

    Per impostazione predefinita, sono escluse anche le cartelle seguenti:

    * \System Volume Information
    * \$RECYCLE.BIN
    * \SyncShareState

* <a id="afs-os-support"></a>
**È possibile utilizzare sincronizzazione File di Azure con Windows Server 2008 R2, Linux o il dispositivo di archiviazione collegato alla rete (NAS)?**  
    Attualmente, Sincronizzazione file di Azure supporta solo Windows Server 2016 e Windows Server 2012 R2. In questo momento, non sono previsti altri tipi di supporto, ma Microsoft è interessata a supportare ulteriori piattaforme in base alle esigenze dei clienti. Gli utenti sono invitati a comunicare le piattaforme per cui desiderano supporto in [UserVoice per File di Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).

## <a name="security-authentication-and-access-control"></a>Sicurezza, autenticazione e controllo di accesso
* <a id="ad-support"></a>
**È l'autenticazione basata su Active Directory e il controllo di accesso supportati dal file di Azure?**  
    File di Azure consente di gestire il controllo di accesso in due modi:

    - È possibile usare le firme di accesso condiviso (SAS) per generare token con autorizzazioni specifiche e validi per un intervallo di tempo specificato. Ad esempio, è possibile generare un token con accesso di sola lettura a un file specifico e con scadenza di 10 minuti. Tutti coloro che possiedono il token nel periodo in cui è valido hanno accesso in sola lettura a tale file nell'intervallo di 10 minuti. Attualmente, le chiavi di firma di accesso condiviso sono supportate solo tramite l'API REST o nelle librerie client. È necessario montare la condivisione file di Azure tramite SMB usando le chiavi dell'account di archiviazione.

    - Sincronizzazione file di Azure consente di mantenere e replicare tutti gli elenchi DACL, ovvero gli elenchi di controllo di accesso discrezionale, (basati su Active Directory o locali) in tutti gli endpoint server con cui viene eseguita la sincronizzazione. Dato che Windows Server è già in grado di eseguire l'autenticazione con Active Directory, Sincronizzazione file di Azure rappresenta un'efficace soluzione temporanea fino a quando non sarà disponibile il supporto completo per l'autenticazione basata su Active Directory e gli elenchi di controllo di accesso.

    Attualmente File di Azure non supporta Active Directory direttamente.

* <a id="encryption-at-rest"></a>
**Come è possibile garantire che la condivisione di file di Azure verrà crittografata a riposo?**  
    L'abilitazione della crittografia per il servizio di archiviazione di Azure è in corso per impostazione predefinita in tutte le aree. Per queste aree non è necessario eseguire alcuna operazione per abilitare la crittografia. Per altre regioni, vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Come posso a fornire l'accesso a un file specifico tramite un web browser?**  
    È possibile usare le firme di accesso condiviso per generare token con autorizzazioni specifiche e validi per un intervallo di tempo specificato. Ad esempio, è possibile generare un token con accesso in sola lettura a un file specifico per un periodo di tempo impostato. Tutti gli utenti in possesso dell'URL possono accedere al file direttamente da qualsiasi Web browser nel periodo di validità del token. È possibile generare facilmente una chiave di firma di accesso condiviso da un'interfaccia utente simile ad Azure Storage Explorer.

* <a id="file-level-permissions"></a>
**È possibile specificare di sola lettura o autorizzazioni di sola scrittura per le cartelle all'interno della condivisione?**  
    Se la condivisione file viene montata tramite SMB, non è disponibile il controllo a livello di cartella sulle autorizzazioni. Se si crea una firma di accesso condiviso con l'API REST o le librerie client, è comunque possibile specificare autorizzazioni di sola lettura di sola scrittura per le cartelle all'interno della condivisione.

* <a id="ip-restrictions"></a>
**È possibile implementare le restrizioni IP per una condivisione di file di Azure?**  
    Sì. L'accesso alla condivisione file di Azure può essere limitato a livello di account di archiviazione. Per altre informazioni, vedere [Configurare i firewall e le reti virtuali di Archiviazione di Azure](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**I criteri di conformità di dati supporta i file di Azure?**  
   L'esecuzione di File di Azure si basa sulla stessa architettura di archiviazione usata in altri servizi di archiviazione in Archiviazione di Azure. File di Azure applica gli stessi criteri di conformità dei dati usati in altri servizi di archiviazione di Azure. Per altre informazioni sulla conformità dei dati di Archiviazione di Azure, è possibile scaricare e fare riferimento al [documento sulla protezione dei dati di Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409) e al [Centro protezione Microsoft](https://www.microsoft.com/TrustCenter/default.aspx).

## <a name="on-premises-access"></a>Accesso locale
* <a id="expressroute-not-required"></a>
**È necessario usare Azure ExpressRoute per connettersi al file di Azure o per utilizzare la sincronizzazione di File di Azure in locale?**  
    di serie ExpressRoute non è richiesto per accedere a una condivisione file di Azure. Se si esegue il montaggio di una condivisione file di Azure direttamente in locale, è necessario solo che la porta 445 (TCP in uscita), ovvero la porta usata per le comunicazioni da SMB, sia aperta per l'accesso a Internet. Se si usa Sincronizzazione file di Azure, occorre solo la porta 443 (TCP in uscita) per l'accesso HTTPS (non è richiesto SMB). È comunque *possibile* usare ExpressRoute con queste opzioni di accesso.

* <a id="mount-locally"></a>
**Come è possibile montare una condivisione di file di Azure nel computer locale?**  
    È possibile montare la condivisione file usando il protocollo SMB, se la porta 445 (TCP in uscita) è aperta e il client supporta il protocollo SMB 3.0, ad esempio su Windows 10 o Windows Server 2016. Se la porta 445 è bloccata dai criteri dell'organizzazione o dall'ISP, è possibile usare Sincronizzazione file di Azure per accedere alla condivisione file di Azure.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Come è possibile eseguire il backup del file di Azure condividere?**  
    È possibile usare [snapshot di condivisione (anteprima)](storage-how-to-use-files-snapshots.md) periodici per la protezione da eliminazioni accidentali. È anche possibile usare AzCopy, RoboCopy o uno strumento di backup di terze parti per eseguire il backup di una condivisione file montata. 

## <a name="share-snapshots"></a>Snapshot di condivisione
### <a name="share-snapshots-general"></a>Snapshot di condivisione: informazioni generali
* <a id="what-are-snaphots"></a>
**Quali sono gli snapshot di condivisione file?**  
    È possibile usare gli snapshot di condivisione file di Azure per creare versioni di sola lettura delle condivisioni file. È anche possibile usare File di Azure per copiare una versione precedente del contenuto nella stessa condivisione o in un percorso alternativo in Azure o in locale per ulteriori modifiche. Per altre informazioni sugli snapshot di condivisione, vedere [Panoramica degli snapshot di condivisione per File di Azure (anteprima)](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**Dove vengono archiviati il condivisione snapshot**  
    Gli snapshot di condivisione vengono archiviati nello stesso account di archiviazione della condivisione file.

* <a id="snapshot-perf-impact"></a>
**Sono presenti le implicazioni sulle prestazioni per l'utilizzo di condivisione snapshot?**  
    Gli snapshot di condivisione non comportano alcun overhead delle prestazioni.

* <a id="snapshot-consistency"></a>
**Sono condivisione snapshot coerenti con l'applicazione?**  
    No, gli snapshot di condivisione non sono coerenti con l'applicazione. È necessario scaricare le operazioni di scrittura dall'applicazione nella condivisione prima di acquisire lo snapshot di condivisione.

* <a id="snapshot-limits"></a>
**Sono previsti limiti al numero di snapshot di condivisione che è possibile usare?**  
    Sì. File di Azure consente di mantenere al massimo 200 snapshot di condivisione. Gli snapshot di condivisione non vengono inclusi nel conteggio per la quota di condivisione, pertanto non esiste alcun limite per condivisione dello spazio totale usato da tutti gli snapshot di condivisione. Si applicano i limiti dell'account di archiviazione. Dopo 200 snapshot di condivisione, è necessario eliminare gli snapshot meno recenti per crearne di nuovi.

### <a name="create-share-snapshots"></a>Creare snapshot di condivisione
* <a id="file-snaphsots"></a>
**È possibile creare snapshot di condivisione dei singoli file?**  
    Gli snapshot di condivisione vengono creati a livello di condivisione file. È possibile ripristinare singoli file dallo snapshot di condivisione file, ma non è possibile creare snapshot di condivisione a livello di file. Tuttavia, se è stato acquisito uno snapshot di condivisione a livello di condivisione e si vogliono elencare gli snapshot di condivisione in cui un determinato file è stato modificato, è possibile farlo in **Versioni precedenti** in una condivisione montata in Windows. 
    
    Se è necessaria una funzionalità specifica per gli snapshot di file, è possibile farlo sapere a Microsoft tramite [UserVoice per File di Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encypted-snapshots"></a>
**È possibile creare snapshot di condivisione di una condivisione di file crittografati?**  
    È possibile creare uno snapshot di condivisione delle condivisioni file di Azure con la crittografia dei dati inattivi abilitata. È possibile ripristinare i file da uno snapshot di condivisione in una condivisione file crittografata. Se la condivisione è crittografata, anche lo snapshot di condivisione sarà crittografato.

* <a id="geo-redundant-snaphsots"></a>
**Sono snapshot la condivisione con ridondanza geografica?**  
    Gli snapshot di condivisione hanno la stessa ridondanza della condivisione file di Azure per cui sono stati creati. Se è stata scelta l'archiviazione con ridondanza geografica per l'account, anche lo snapshot di condivisione verrà archiviato in modo ridondante nell'area associata.

### <a name="manage-share-snapshots"></a>Gestire snapshot di condivisione
* <a id="browse-snapshots-linux"></a>
**Ricercare il condivisione snapshot Linux?**  
    È possibile usare l'interfaccia della riga di comando di Azure 2.0 per creare, elencare, esplorare e ripristinare snapshot di condivisione in Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Copiare gli snapshot di condivisione a un account di archiviazione differente?**  
    È possibile copiare file dagli snapshot di condivisione in un altro percorso, ma non è possibile copiare gli snapshot di condivisione stessi.

### <a name="restore-data-from-share-snapshots"></a>Eseguire il ripristino dei dati da snapshot di condivisione
* <a id="promote-share-snapshot"></a>
**È possibile promuove uno snapshot di condivisione per la condivisione di base?**  
    È possibile copiare i dati da uno snapshot di condivisione in qualsiasi altra destinazione. Non è possibile alzare di livello uno snapshot di condivisione alla condivisione di base.

* <a id="restore-snapshotted-file-to-other-share"></a>
**È possibile ripristinare dati dal mio Condividi snapshot per un account di archiviazione diverso?**  
    Sì. I file da uno snapshot di condivisione possono essere copiati nella posizione originale o in una posizione alternativa che include lo stesso account di archiviazione o un account di archiviazione diverso, nella stessa area o in aree diverse. È anche possibile copiare i file in una posizione locale o in qualsiasi altro cloud.    
  
### <a name="clean-up-share-snapshots"></a>Pulire gli snapshot di condivisione
* <a id="delete-share-keep-snapshots"></a>
**È possibile eliminare la condivisione ma non eliminare il condivisione snapshot?**  
    Non sarà possibile eliminare la condivisione se nella stessa vi sono snapshot di condivisione attivi. È possibile usare un'API per eliminare gli snapshot di condivisione, insieme alla condivisione. È anche possibile eliminare sia gli snapshot di condivisione che la condivisione nel portale di Azure.

* <a id="delete-share-with-snapshots"></a>
**Cosa accade per il condivisione snapshot se si elimina l'account di archiviazione?**  
    Se si elimina l'account di archiviazione, vengono eliminati anche gli snapshot di condivisione.

## <a name="billing-and-pricing"></a>Prezzi e fatturazione
* <a id="vm-file-share-network-traffic"></a>
**Il traffico di rete tra una macchina virtuale di Azure e un conteggio di condivisione file di Azure come larghezza di banda esterna viene addebitato alla sottoscrizione?**  
    Se la condivisione file e la macchina virtuale si trovano nella stessa area di Azure, non sono previsti costi aggiuntivi per il traffico tra la condivisione file e la macchina virtuale. Se la condivisione file e la macchina virtuale si trovano in aree diverse, il traffico viene addebitato come larghezza di banda esterna.

* <a id="share-snapshot-price"></a>
**Quanto condividono costo snapshot?**  
     Nel periodo di anteprima non è previsto alcun addebito per la capacità degli snapshot di condivisione. Vengono applicati i costi di transazione e di uscita dell'archiviazione Standard. Quando la funzionalità sarà disponibile a livello generale, verranno addebitate sia la capacità che le transazioni per gli snapshot di condivisione.
     
     Gli snapshot di condivisione sono di natura incrementale. Lo snapshot di condivisione di base è la condivisione stessa. Tutti gli snapshot di condivisione successivi sono incrementali e archiviano solo le differenze rispetto allo snapshot di condivisione precedente. È previsto un addebito solo per il contenuto modificato. Se è disponibile una condivisione con 100 GiB di dati, ma solo 5 GiB sono stati modificati dall'ultimo snapshot di condivisione, lo snapshot di condivisione consuma solo 5 GiB aggiuntivi e vengono addebitati solo 105 GiB. Per altre informazioni sui costi di transazione e di uscita standard, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Scalabilità e prestazioni
* <a id="files-scale-limits"></a>
**Quali sono i limiti di scalabilità dei file di Azure?**  
    Per informazioni sugli obiettivi di scalabilità e prestazioni di File per Azure, vedere [Obiettivi di scalabilità e prestazioni di File di Azure](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**Ho bisogno di una condivisione di file più grande rispetto a file di Azure offre attualmente. è possibile aumentare le dimensioni della condivisione file di Azure?**  
    di serie La dimensione massima di una condivisione file di Azure è 5 TiB. Attualmente non è possibile modificare questo limite. Microsoft sta lavorando a una soluzione per aumentare la dimensione della condivisione a 100 TiB, ma non sono ancora disponibili indicazioni precise sui tempi di attuazione.

* <a id="open-handles-quota"></a>
**Quanti client possono accedere simultaneamente allo stesso file?**   
    È prevista una quota pari a 2.000 handle aperti su un singolo file. In presenza di 2.000 handle aperti viene visualizzato un messaggio di errore che segnala il raggiungimento della quota.

* <a id="zip-slow-performance"></a>
**My prestazioni sono lente quando decomprimere i file nel file di Azure. Cosa devo fare?**  
    Per trasferire grandi quantità di file in File di Azure, è consigliabile usare AzCopy (per Windows, in anteprima per Linux e UNIX) o Azure PowerShell. Questi strumenti sono stati ottimizzati per il trasferimento in rete.

* <a id="slow-perf-windows-81-2012r2"></a>
**Perché è my prestazioni lente dopo montare la condivisione di file di Azure in Windows Server 2012 R2 o Windows 8.1?**  
    Esiste un problema noto con il montaggio di una condivisione file di Azure in Windows Server 2012 R2 e Windows 8.1. Per questo problema è stata inclusa una patch nell'aggiornamento cumulativo di aprile 2014 per Windows 8.1 e Windows Server 2012 R2. Per ottenere prestazioni ottimali, verificare che questa patch sia stata applicata a tutte le istanze di Windows Server 2012 R2 e Windows 8.1. (La ricezione delle patch di Windows tramite Windows Update dovrebbe essere sempre attiva.) Per altre informazioni, vedere l'articolo della Microsoft Knowledge Base associato [Rallentamento delle prestazioni quando si accede a File di Azure da Windows 8.1 o Windows Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funzionalità e interoperabilità con altri servizi
* <a id="cluster-witness"></a>
**È possibile utilizzare la condivisione di file di Azure come un *Witness di condivisione File* per il Cluster di Failover di Windows Server?**  
    Questa configurazione non è attualmente supportata per una condivisione file di Azure. Per altre informazioni su come configurare questo servizio per Archiviazione BLOB di Azure, vedere [Distribuire un cloud di controllo per un cluster di failover](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**È possibile montare una condivisione di file di Azure in un'istanza di contenitore di Azure?**  
    Sì, le condivisioni file di Azure sono un'opzione utile per salvare in modo permanente le informazioni oltre la durata di un'istanza del contenitore. Per altre informazioni vedere [Mount an Azure file share with Azure Container Instances](../../container-instances/container-instances-mounting-azure-files-volume.md) (Montare una condivisione file di Azure con istanze di contenitore di Azure).

* <a id="rest-rename"></a>
**È un'operazione di ridenominazione nell'API REST?**  
    Attualmente non è possibile.

* <a id="nested-shares"></a>
**È possibile impostare quote nidificate? ovvero una condivisione in una condivisione?**  
    di serie La condivisione file *è* il driver virtuale che è possibile montare, quindi le condivisioni annidate non sono supportate.

* <a id="ibm-mq"></a>
**Utilizzo di file di Azure con IBM MQ**  
    IBM ha pubblicato un documento per supportare i clienti di IBM MQ nella configurazione di File di Azure con il servizio IBM. Per altre informazioni, vedere [How to set up an IBM MQ multi-instance queue manager with Microsoft Azure Files service](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service) (Come configurare IBM MQ Multi Instance Queue Manager (MIQM) con il servizio File di Microsoft Azure).

## <a name="see-also"></a>Vedere anche 
* [Risolvere i problemi relativi a File di Azure in Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Risolvere i problemi relativi a File di Azure in Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Risolvere i problemi di Sincronizzazione file di Azure (anteprima)](storage-sync-files-troubleshoot.md)
