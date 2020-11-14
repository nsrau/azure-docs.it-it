---
title: Domande frequenti per File di Azure | Microsoft Docs
description: Risposte alle File di Azure Domande frequenti. È possibile montare condivisioni file di Azure simultaneamente in distribuzioni di Windows, Linux o macOS locali o nel cloud.
author: roygara
ms.service: storage
ms.date: 02/23/2020
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 1eddca26152f27ced20bbae66b3265639b97e64e
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629496"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Domande frequenti su File di Azure
[File di Azure](storage-files-introduction.md) offre condivisioni file completamente gestite nel cloud accessibili tramite il [protocollo SMB (Server Message Block)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) standard del settore e il [protocollo NFS (Network File System](https://en.wikipedia.org/wiki/Network_File_System) ) (anteprima). È possibile montare le condivisioni file di Azure simultaneamente da distribuzioni cloud o locali di Windows, Linux e macOS. È anche possibile memorizzare nella cache le condivisioni file di Azure nei computer Windows Server tramite Sincronizzazione file di Azure per l'accesso rapido in prossimità della posizione in cui vengono usati i dati.

Questo articolo risponde ad alcune domande frequenti sulle caratteristiche e funzionalità di File di Azure, tra cui l'uso di Sincronizzazione file di Azure con File di Azure. Se non è presente la risposta a una domanda specifica, è possibile contattare Microsoft tramite i seguenti canali (in ordine di escalation):

1. Sezione dei commenti di questo articolo.
2. [Forum di archiviazione di Azure](/answers/topics/azure-file-storage.html).
3. [UserVoice per File di Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Supporto tecnico Microsoft. Per creare una nuova richiesta di supporto, nel portale di Azure, nella scheda **Guida** , selezionare **Guida e supporto** e quindi selezionare **Nuova richiesta di supporto**.

## <a name="general"></a>Generale
* <a id="why-files-useful"></a>
  **Perché File di Azure è utile?**  
   È possibile usare File di Azure per creare condivisioni file nel cloud, senza la responsabilità di gestire l'overhead di un server fisico, un dispositivo o un'appliance. Si possono così delegare le operazioni monotone, incluse l'installazione degli aggiornamenti del sistema operativo e la sostituzione di dischi danneggiati. Per altre informazioni sugli scenari in cui è utile usare File di Azure, vedere [Vantaggi offerti da File di Azure](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
  **Quali sono i vari modi per accedere ai file in File di Azure?**  
    Le condivisioni file SMB possono essere montate nel computer locale usando il protocollo SMB 3,0 oppure è possibile usare strumenti come [Storage Explorer](https://storageexplorer.com/) per accedere ai file nella condivisione file. È possibile montare le condivisioni file NFS nel computer locale copiando e incollando lo script fornito dal portale di Azure. Dall'applicazione è possibile usare le librerie client di archiviazione, le API REST, PowerShell o l'interfaccia della riga di comando di Azure per accedere ai file nella condivisione file di Azure.

* <a id="what-is-afs"></a>
  **Che cos'è Sincronizzazione file di Azure?**  
    È possibile usare Sincronizzazione file di Azure per centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Sincronizzazione file di Azure trasforma i computer Windows Server in una cache rapida della condivisione file di Azure. È possibile usare qualsiasi protocollo disponibile in Windows Server per accedere ai dati in locale, tra cui SMB, NFS (Network File System) e FTPS (File Transfer Protocol Service). Si può usare qualsiasi numero di cache necessario in tutto il mondo.

* <a id="files-versus-blobs"></a>
  **Perché usare una condivisione file di Azure invece di Archiviazione BLOB di Azure per i dati?**  
    File di Azure e Archiviazione BLOB di Azure consentono entrambi di archiviare grandi quantità di dati nel cloud, ma sono utili per scopi leggermente diversi. 
    
    Archiviazione BLOB di Azure è utile per le applicazioni native del cloud su vasta scala che necessitano di archiviare dati non strutturati. Per ottimizzare le prestazioni e la scalabilità, Archiviazione BLOB di Azure è un'astrazione di archiviazione più semplice rispetto a un vero e proprio file system. È possibile accedere ad Archiviazione BLOB di Azure solo tramite librerie client basate su REST (o direttamente tramite il protocollo basato su REST).

    File di Azure è nello specifico un file system. File di Azure racchiude tutte le funzionalità per la gestione dei file che gli utenti hanno imparato a conoscere e ad apprezzare grazie ad anni di esperienza con i sistemi operativi in locale. Come Archiviazione BLOB di Azure, File di Azure offre un'interfaccia REST e librerie client basate su REST. Diversamente dall'archiviazione BLOB di Azure, File di Azure offre l'accesso SMB o NFS alle condivisioni file di Azure. Le condivisioni file possono essere montate direttamente in Windows, Linux o macOS, in locale o in macchine virtuali cloud, senza scrivere codice né alleghino driver speciali al file system. È anche possibile memorizzare nella cache le condivisioni file SMB di Azure nei file server locali usando Sincronizzazione file di Azure per l'accesso rapido, vicino alla posizione in cui vengono usati i dati. 
   
    Per una descrizione più dettagliata delle differenze tra File di Azure e Archiviazione BLOB di Azure, vedere [Introduzione ai servizi di archiviazione di Azure di base](../common/storage-introduction.md). Per altre informazioni su Archiviazione BLOB di Azure, vedere [Introduzione all'archiviazione BLOB](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Perché usare una condivisione file di Azure invece di Dischi di Azure?**  
    Un disco in Dischi di Azure è semplicemente un disco. Per ottenere valore da Dischi di Azure, è necessario collegare un disco a una macchina virtuale in esecuzione in Azure. È possibile usare Dischi di Azure per qualsiasi scopo per cui si può usare un disco per un server locale, come disco del sistema operativo, come spazio di swapping per un sistema operativo o come spazio di archiviazione dedicato per un'applicazione. Un uso interessante di Dischi di Azure consiste nella creazione di un file server nel cloud per usarlo esattamente negli stessi scenari in cui si potrebbe usare una condivisione file di Azure. La distribuzione di un file server in macchine virtuali di Azure è un modo a prestazioni elevate per ottenere l'archiviazione di file in Azure quando sono necessarie opzioni di distribuzione che attualmente non sono supportate da File di Azure. 

    L'esecuzione di un file server con Dischi di Azure come archiviazione back-end, tuttavia, risulta in genere molto più onerosa rispetto all'uso di una condivisione file di Azure per diversi motivi. Per prima cosa, oltre a dover pagare per l'archiviazione su disco, è anche necessario sostenere il costo dell'esecuzione di una o più macchine virtuali di Azure. In secondo luogo, è anche necessario gestire le macchine virtuali usate per eseguire il file server. Ad esempio, si mantiene la responsabilità degli aggiornamenti del sistema operativo. Infine, se si vuole che i dati siano memorizzati nella cache in locale, occorre configurare e gestire le tecnologie di replica, come Replica DFS (DFSR) a tale scopo.

    Un approccio per sfruttare al meglio File di Azure e un file server ospitato in Macchine virtuali di Azure (oltre a usare Dischi di Azure come risorsa di archiviazione back-end) consiste nell'installare Sincronizzazione file di Azure in un file server ospitato in una macchina virtuale nel cloud. Se la condivisione file di Azure è nella stessa area del file server, è possibile abilitare la suddivisione in livelli nel cloud e impostare la percentuale di spazio disponibile nel volume sul massimo (99%). Ciò assicura una duplicazione minima dei dati. È anche possibile usare qualsiasi applicazione con i file server, ad esempio applicazioni che richiedono il supporto del protocollo NFS.

    Per informazioni su un'opzione per configurare un file server ad alte prestazioni e a disponibilità elevata in Azure, vedere [Deploying IaaS VM Guest Clusters in Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/) (Distribuzione di cluster guest di macchine virtuali IaaS in Microsoft Azure). Per una descrizione più dettagliata sulle differenze tra File di Azure e Dischi di Azure, vedere [Introduzione ai servizi di archiviazione di Azure di base](../common/storage-introduction.md). Per altre informazioni su Dischi di Azure, vedere [Panoramica di Azure Managed Disks](../../virtual-machines/managed-disks-overview.md).

* <a id="get-started"></a>
  **Come iniziare a usare File di Azure?**  
   Iniziare a usare File di Azure è semplice. Per prima cosa, [creare una condivisione file SMB](storage-how-to-create-file-share.md) o [come creare una condivisione NFS](storage-files-how-to-create-nfs-shares.md), quindi montarla nel sistema operativo preferito: 

  * [Montare una condivisione SMB in Windows](storage-how-to-use-files-windows.md)
  * [Montare una condivisione SMB in Linux](storage-how-to-use-files-linux.md)
  * [Montare una condivisione SMB in macOS](storage-how-to-use-files-mac.md)
  * [Montare una condivisione file NFS](storage-files-how-to-mount-nfs-shares.md)

    Per una guida più dettagliata sulla distribuzione di una condivisione file di Azure per sostituire le condivisioni file di produzione all'interno dell'organizzazione, vedere [Pianificazione per la distribuzione dei file di Azure](storage-files-planning.md).

* <a id="redundancy-options"></a>
  **Quali opzioni di ridondanza di archiviazione sono supportate da File di Azure?**  
    File di Azure supporta attualmente l'archiviazione con ridondanza locale, l'archiviazione con ridondanza della zona, l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza geografica della zona. File di Azure livello Premium supporta attualmente solo con ridondanza locale e ZRS.

* <a id="tier-options"></a>
  **Quali livelli di archiviazione sono supportati in File di Azure?**  
    File di Azure supporta due livelli di archiviazione, Premium e Standard. Le condivisioni Standard vengono create in account di archiviazione per utilizzo generico (GPv1 o GPv2) e le condivisioni Premium vengono create negli account di archiviazione FileStorage. Altre informazioni su come creare [condivisioni standard di file](storage-how-to-create-file-share.md) e [condivisioni Premium di file](storage-how-to-create-premium-fileshare.md). 
    
    > [!NOTE]
    > Non è possibile creare condivisioni file di Azure dagli account di archiviazione BLOB o dagli account di archiviazione per utilizzo generico (GPv1 o GPv2) *Premium*. Le condivisioni file di Azure standard devono essere create solo in account *Standard* per utilizzo generico e le condivisioni file di Azure premium devono essere create solo negli account di archiviazione FileStorage. Gli account di archiviazione *Premium* per utilizzo generico (GPv1 e GPv2) sono solo per i blob di pagine Premium. 

* <a id="file-locking"></a>
  **File di Azure supporta il blocco di file?**  
    Sì, File di Azure supporta pienamente il blocco di file di tipo SMB/Windows, [vedere dettagli](/rest/api/storageservices/managing-file-locks).

* <a id="give-us-feedback"></a>
  **È possibile aggiungere una funzionalità specifica a File di Azure?**  
    Il team di File di Azure è interessato a tutti i commenti e i suggerimenti dei clienti sul servizio offerto. Gli utenti sono invitati a votare le richieste di funzionalità in [UserVoice per File di Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files). Il team di File di Azure farà il possibile per soddisfare le richieste di nuove funzionalità.

## <a name="azure-file-sync"></a>Sincronizzazione file di Azure

* <a id="afs-region-availability"></a>
  **Quali sono le aree supportate per Sincronizzazione file di Azure?**  
    L'elenco delle aree disponibili è reperibile nella sezione [Disponibilità delle aree](storage-sync-files-planning.md#azure-file-sync-region-availability) della guida alla pianificazione di Sincronizzazione file di Azure. Sarà aggiunto continuativamente il supporto per aree aggiuntive, comprese aree non pubbliche.

* <a id="cross-domain-sync"></a>
  **È possibile avere server aggiunti a un dominio e server non aggiunti a un dominio nello stesso gruppo di sincronizzazione?**  
    Sì. Un gruppo di sincronizzazione può contenere endpoint server con appartenenze diverse ad Active Directory, anche se non aggiunti a un dominio. Anche se questa configurazione funziona dal punto di vista tecnico, non è consigliabile usarla come configurazione tipica dato che gli elenchi di controllo di accesso (ACL) definiti per i file e le cartelle in un server potrebbero non essere applicati da altri server nel gruppo di sincronizzazione. Per ottenere risultati ottimali, è consigliabile la sincronizzazione tra server nella stessa foresta di Active Directory, server in foreste di Active Directory diverse ma con relazioni di trust stabilite o server non inclusi in un dominio. È consigliabile evitare di usare una combinazione di queste configurazioni.

* <a id="afs-change-detection"></a>
  **È stato creato un file direttamente nella condivisione file di Azure su SMB o nel portale. Quanto tempo occorre per la sincronizzazione del file nei server nel gruppo di sincronizzazione?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]


* <a id="afs-sync-time"></a>
  **Quanto tempo è necessario per Sincronizzazione file di Azure caricare 1TiB di dati?**
  
    Le prestazioni variano in base alle impostazioni ambientali, alla configurazione e al fatto che si tratti di una sincronizzazione iniziale o di una sincronizzazione continua. Per altre informazioni, vedere [metriche delle prestazioni sincronizzazione file di Azure](storage-files-scale-targets.md#azure-file-sync-performance-metrics)

* <a id="afs-conflict-resolution"></a>**Cosa accade quando lo stesso file viene modificato in due server approssimativamente nello stesso momento?**  
    Sincronizzazione file di Azure usa una semplice strategia di risoluzione dei conflitti: si conservano entrambe le modifiche ai file che vengono modificati contemporaneamente in due endpoint. Per la modifica più di recente viene mantenuto il nome del file originale. Il file precedente, determinato da LastWriteTime, ha il nome dell'endpoint e il numero di conflitto aggiunto al nome del file. Per gli endpoint server, il nome dell'endpoint è il nome del server. Per gli endpoint cloud, il nome dell'endpoint è **cloud**. Il nome segue questa tassonomia: 
   
    \<FileNameWithoutExtension\>-\<endpointName\>\[-#\].\<ext\>  

    Ad esempio, il primo conflitto per ReportAziendale.docx diventerebbe ReportAziendale-ServerCentrale.docx se ServerCentrale è il computer in cui è stata eseguita l'operazione di scrittura meno recente. Il nome per il secondo conflitto sarebbe ReportAziendale-ServerCentrale-1.docx. Sincronizzazione file di Azure supporta 100 file in conflitto per file. Dopo aver raggiunto il numero massimo di file in conflitto, il file non verrà sincronizzato finché il numero di file in conflitto è inferiore a 100.

* <a id="afs-storage-redundancy"></a>
  **L'archiviazione con ridondanza geografica è supportata per Sincronizzazione file di Azure?**  
    Sì. File di Azure supporta sia l'archiviazione con ridondanza locale (LRS) che l'archiviazione con ridondanza geografica (GRS). Se si avvia un failover dell'account di archiviazione tra aree associate da un account configurato per l'archiviazione con ridondanza geografica, Microsoft consiglia di considerare la nuova area solo come backup dei dati. Sincronizzazione file di Azure non avvia automaticamente la sincronizzazione con la nuova area primaria. 

* <a id="sizeondisk-versus-size"></a>
  **Perché la proprietà *Dimensioni su disco* per un file non corrisponde alla proprietà *Dimensioni* dopo l'uso di Sincronizzazione file di Azure?**  
  Vedere [Informazioni sulla suddivisione in livelli nel cloud](storage-sync-cloud-tiering.md#sizeondisk-versus-size).

* <a id="is-my-file-tiered"></a>
  **Come si può stabilire se un file è archiviato a livelli?**  
  Vedere [Informazioni sulla suddivisione in livelli nel cloud](storage-sync-cloud-tiering.md#is-my-file-tiered).

* <a id="afs-recall-file"></a>**Un file da usare è stato archiviato a livelli. Come è possibile richiamare il file su disco per usarlo in locale?**  
  Vedere [Informazioni sulla suddivisione in livelli nel cloud](storage-sync-cloud-tiering.md#afs-recall-file).

* <a id="afs-force-tiering"></a>
  **Come è possibile forzare l'archiviazione a livelli di un file o una directory?**  
  Vedere [Informazioni sulla suddivisione in livelli nel cloud](storage-sync-cloud-tiering.md#afs-force-tiering).

* <a id="afs-effective-vfs"></a>
  **Come viene interpretato lo *spazio disponibile del volume* quando in un volume sono presenti più endpoint server?**  
  Vedere [Informazioni sulla suddivisione in livelli nel cloud](storage-sync-cloud-tiering.md#afs-effective-vfs).
  
* <a id="afs-tiered-files-tiering-disabled"></a>
  **La suddivisione in livelli nel cloud è disabilitata perché sono presenti file a livelli nel percorso dell'endpoint server?**  
  Vedere [Informazioni sulla suddivisione in livelli nel cloud](storage-sync-cloud-tiering.md#afs-tiering-disabled).

* <a id="afs-files-excluded"></a>
  **Quali file o cartelle vengono automaticamente esclusi da Sincronizzazione file di Azure?**  
  Vedere [File ignorati](storage-sync-files-planning.md#files-skipped).

* <a id="afs-os-support"></a>
  **È possibile usare Sincronizzazione file di Azure con Windows Server 2008 R2, Linux o un dispositivo NAS?**  
    Attualmente, Sincronizzazione file di Azure supporta solo Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2. In questo momento, non sono previsti altri tipi di supporto, ma Microsoft è interessata a supportare ulteriori piattaforme in base alle esigenze dei clienti. Gli utenti sono invitati a comunicare le piattaforme per cui desiderano supporto in [UserVoice per File di Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Perché sono presenti file a livelli all'esterno dello spazio dei nomi dell'endpoint server?**  
    Prima dell'agente di Sincronizzazione file di Azure versione 3, Sincronizzazione file di Azure bloccava lo spostamento dei file a livelli all'esterno dell'endpoint server e ne consentiva lo spostamento solo nello stesso volume dell'endpoint server. Operazioni di copia, spostamenti di file non a livelli e spostamenti di file a livelli in altri volumi non erano interessati. Il motivo di questo comportamento è il presupposto implicito, da parte di Esplora file e di altre API di Windows, che le operazioni di spostamento nello stesso volume siano operazioni di ridenominazione (quasi) istantanee. Durante gli spostamenti, Esplora file o altri metodi di spostamento (ad esempio tramite riga di comando o PowerShell) sembrano quindi non rispondere mentre Sincronizzazione file di Azure richiama i dati dal cloud. A partire dall'[agente di Sincronizzazione file di Azure versione 3.0.12.0](storage-files-release-notes.md#supported-versions), Sincronizzazione file di Azure consente di spostare un file a livelli all'esterno dell'endpoint server. Gli effetti negativi indicati in precedenza vengono evitati consentendo la presenza del file a livelli all'esterno dell'endpoint server e richiamando quindi il file in background. Ciò determina che gli spostamenti nello stesso volume siano istantanei e che al termine dello spostamento il file venga richiamato automaticamente su disco. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **Si è verificato un problema di Sincronizzazione file di Azure nel server (sincronizzazione, suddivisione in livelli cloud e così via). È consigliabile rimuovere l'endpoint server e ricrearlo?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse, una sottoscrizione o un tenant Azure AD diversi?**  
   Sì, il servizio di sincronizzazione archiviazione e/o l'account di archiviazione possono essere spostati in un altro gruppo di risorse, sottoscrizione o Azure AD tenant. Dopo lo spostamento dell'account di archiviazione o del servizio di sincronizzazione archiviazione, è necessario concedere all'applicazione Microsoft. StorageSync l'accesso all'account di archiviazione (vedere [verificare che sincronizzazione file di Azure abbia accesso all'account di archiviazione](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Quando si crea l'endpoint cloud, il servizio di sincronizzazione archiviazione e l'account di archiviazione devono trovarsi nello stesso tenant di Azure AD. Dopo aver creato l'endpoint cloud, il servizio di sincronizzazione archiviazione e l'account di archiviazione possono essere spostati in tenant di Azure AD diversi.
    
* <a id="afs-ntfs-acls"></a>
  **Sincronizzazione file di Azure consente di mantenere gli ACL NTFS a livello di directory/file insieme ai dati archiviati in File di Azure?**

    A partire dal 24 febbraio 2020, gli ACL nuovi ed esistenti a livello di Sincronizzazione file di Azure verranno mantenuti in formato NTFS e le modifiche ACL apportate direttamente alla condivisione file di Azure verranno sincronizzate in tutti i server del gruppo di sincronizzazione. Tutte le modifiche apportate agli ACL apportate a File di Azure si sincronizzano tramite sincronizzazione file di Azure. Quando si copiano dati in File di Azure, assicurarsi di usare uno strumento di copia che supporta la "fedeltà" necessaria per copiare gli attributi, i timestamp e gli ACL in una condivisione file di Azure, tramite SMB o REST. Quando si usano gli strumenti di copia di Azure, ad esempio AzCopy, è importante usare la versione più recente. Controllare la [tabella strumenti copia file](storage-files-migration-overview.md#file-copy-tools) per ottenere una panoramica degli strumenti di copia di Azure per assicurarsi che sia possibile copiare tutti i metadati importanti di un file.

    Se Backup di Azure è stato abilitato nelle condivisioni file gestite da sincronizzazione file, gli ACL dei file possono continuare a essere ripristinati come parte del flusso di lavoro di ripristino del backup. Questa operazione può essere eseguita per l'intera condivisione o per singoli file/directory.

    Se si usano gli snapshot come parte della soluzione di backup autogestita per le condivisioni file gestite da sincronizzazione file, gli ACL potrebbero non essere ripristinati correttamente negli ACL NTFS se gli snapshot sono stati eseguiti prima del 24 febbraio 2020. In tal caso, è consigliabile contattare il supporto di Azure.
    
## <a name="security-authentication-and-access-control"></a>Sicurezza, autenticazione e controllo di accesso
* <a id="ad-support"></a>
**L'autenticazione basata su identità e il controllo di accesso sono supportati da File di Azure?**  
    
    Sì, File di Azure supporta l'autenticazione basata su identità e il controllo di accesso. È possibile scegliere uno dei due modi per usare il controllo degli accessi in base all'identità: Active Directory Domain Services locale o Azure Active Directory Domain Services (Azure AD DS). Active Directory Domain Services locale (AD DS) supporta l'autenticazione tramite macchine aggiunte al dominio AD DS, in locale o in Azure, per accedere alle condivisioni file di Azure tramite SMB. L'autenticazione di Azure AD DS tramite SMB per File di Azure consente alle macchine virtuali aggiunte al dominio di Azure AD DS di accedere a condivisioni, directory e file usando le credenziali di Azure AD. Per informazioni dettagliate, vedere [Panoramica del supporto dell'autenticazione basata su identità File di Azure per l'accesso SMB](storage-files-active-directory-overview.md). 

    File di Azure offre altri due modi per gestire il controllo di accesso:

    - È possibile usare le firme di accesso condiviso (SAS) per generare token con autorizzazioni specifiche e validi per un intervallo di tempo specificato. Ad esempio, è possibile generare un token con accesso di sola lettura a un file specifico e con scadenza di 10 minuti. Tutti coloro che possiedono il token nel periodo in cui è valido hanno accesso in sola lettura a tale file nell'intervallo di 10 minuti. Le chiavi di firma di accesso condiviso sono supportate solo tramite l'API REST o nelle librerie client. È necessario montare la condivisione file di Azure tramite SMB usando le chiavi dell'account di archiviazione.

    - Sincronizzazione file di Azure consente di mantenere e replicare tutti gli elenchi DACL, ovvero gli elenchi di controllo di accesso discrezionale, (basati su Active Directory o locali) in tutti gli endpoint server con cui viene eseguita la sincronizzazione. 
    
    Per una rappresentazione completa di tutti i protocolli supportati nei servizi di Archiviazione di Azure, è possibile fare riferimento a [Autorizzazione dell'accesso ai dati in Archiviazione di Azure](../common/storage-auth.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json). 
    
* <a id="encryption-at-rest"></a>
**Come è possibile garantire che la condivisione file di Azure usi la crittografia dei dati inattivi?**  

    Sì. Per altre informazioni, vedere [Crittografia del servizio di archiviazione di Azure](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Come è possibile fornire accesso a un file specifico tramite un Web browser?**  

    È possibile usare le firme di accesso condiviso per generare token con autorizzazioni specifiche e validi per un intervallo di tempo specificato. Ad esempio, è possibile generare un token con accesso in sola lettura a un file specifico per un periodo di tempo impostato. Tutti gli utenti in possesso dell'URL possono accedere al file direttamente da qualsiasi Web browser nel periodo di validità del token. È possibile generare facilmente una chiave di firma di accesso condiviso da un'interfaccia utente simile ad Azure Storage Explorer.

* <a id="file-level-permissions"></a>
**È possibile specificare autorizzazioni di sola lettura o di sola scrittura per le cartelle all'interno della condivisione?**  

    Se la condivisione file viene montata tramite SMB, non è disponibile il controllo a livello di cartella sulle autorizzazioni. Se si crea una firma di accesso condiviso con l'API REST o le librerie client, è comunque possibile specificare autorizzazioni di sola lettura di sola scrittura per le cartelle all'interno della condivisione.

* <a id="ip-restrictions"></a>
**È possibile implementare restrizioni IP per una condivisione file di Azure?**  

    Sì. L'accesso alla condivisione file di Azure può essere limitato a livello di account di archiviazione. Per altre informazioni, vedere [Configurare i firewall e le reti virtuali di Archiviazione di Azure](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**Quali criteri di conformità dei dati sono supportati da File di Azure?**  

   L'esecuzione di File di Azure si basa sulla stessa architettura di archiviazione usata in altri servizi di archiviazione in Archiviazione di Azure. File di Azure applica gli stessi criteri di conformità dei dati usati in altri servizi di archiviazione di Azure. Per ulteriori informazioni sulla conformità dei dati di Archiviazione di Azure, è possibile fare riferimento a [Offerte di conformità dell’archiviazione di Azure](../common/storage-compliance-offerings.md) e al [Centro protezione Microsoft](https://microsoft.com/trustcenter/default.aspx).

* <a id="file-auditing"></a>
**Come è possibile controllare l'accesso ai file e le modifiche in File di Azure?**

  Sono disponibili due opzioni che forniscono funzionalità di controllo per File di Azure:
  - Se gli utenti accedono direttamente alla condivisione file di Azure, i [log di archiviazione di Azure (anteprima)](../blobs/monitor-blob-storage.md?tabs=azure-powershell#analyzing-logs) possono essere usati per tenere traccia delle modifiche ai file e dell'accesso utente. Questi log possono essere usati per la risoluzione dei problemi e le richieste vengono registrate in base al massimo sforzo.
  - Se gli utenti accedono alla condivisione file di Azure tramite un server Windows in cui è installato l'agente Sincronizzazione file di Azure, usare un [criterio di controllo](/windows/security/threat-protection/auditing/apply-a-basic-audit-policy-on-a-file-or-folder) o un prodotto di terze parti per rilevare le modifiche ai file e l'accesso utente in Windows Server. 
   
### <a name="ad-ds--azure-ad-ds-authentication"></a>Autenticazione di servizi di dominio Active Directory & Azure AD DS
* <a id="ad-support-devices"></a>
**L'autenticazione di Azure Active Directory Domain Services (Azure AD DS) di File di Azure supporta l'accesso SMB con credenziali di Azure AD da dispositivi aggiunti o registrati in Azure AD?**

    No, questo scenario non è supportato.

* <a id="ad-vm-subscription"></a>
**È possibile accedere a condivisione file di Azure con le credenziali di Azure AD da una macchina virtuale in una sottoscrizione diversa?**

    Se la sottoscrizione in cui è distribuita la condivisione file è associata allo stesso tenant di Azure AD della distribuzione di Azure AD DS in cui la macchina virtuale è aggiunta a un dominio, sarà possibile accedere a condivisioni file di Azure usando le stesse credenziali di Azure AD. La limitazione si applica non alla sottoscrizione ma al tenant di Azure AD associato.
    
* <a id="ad-support-subscription"></a>
**È possibile abilitare l'autenticazione Azure AD DS o AD DS locale per le condivisioni file di Azure usando un tenant di Azure AD diverso dal tenant primario della condivisione file di Azure?**

    No, File di Azure supporta solo l'integrazione di Azure AD con un tenant di Azure AD DS o AD DS locale che si trova nella stessa sottoscrizione della condivisione file. È possibile associare una sola sottoscrizione a un tenant di Azure AD. Questa limitazione si applica sia ai metodi di autenticazione Azure AD DS sia AD DS locali. Quando si usa Active Directory Domain Services locale per l'autenticazione, [le credenziali di Active Directory Domain Services devono essere sincronizzate con Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md) a cui è associato l'account di archiviazione.

* <a id="ad-linux-vms"></a>
**Azure AD DS o l'autenticazione AD DS locale per le condivisioni file di Azure supportano le macchine virtuali Linux?**

    No, l'autenticazione dalle macchine virtuali Linux non è supportata.

* <a id="ad-aad-smb-afs"></a>
**Le condivisioni file gestite da Sincronizzazione file di Azure supportano Azure AD DS o l'autenticazione AD DS locale?**

    Sì, è possibile abilitare l'autenticazione di Azure AD DS o AD DS locale in una condivisione file gestita da Sincronizzazione file di Azure. Le modifiche apportate agli ACL NTFS di directory/file nei file server locali verranno suddivise a livelli in File di Azure e viceversa.

* <a id="ad-aad-smb-files"></a>
**Come è possibile verificare se è stata abilitata l'autenticazione AD DS nell'account di archiviazione e recuperare le informazioni sul dominio?**

    Per istruzioni, vedere [qui](./storage-files-identity-ad-ds-enable.md#confirm-the-feature-is-enabled).

* <a id=""></a>
**L'autenticazione Azure AD di File di Azure supporta macchine virtuali Linux?**

    No, l'autenticazione dalle macchine virtuali Linux non è supportata.

* <a id="ad-multiple-forest"></a>
**L'autenticazione AD DS locale per le condivisioni file di Azure supporta l'integrazione con un ambiente AD DS usando più foreste?**    

    L'autenticazione AD DS locale di File di Azure si integra solo con la foresta del servizio del dominio in cui è registrato l'account di archiviazione. Per supportare l'autenticazione da un'altra foresta, è necessario che nell'ambiente sia configurato correttamente un trust della foreste. Il modo in cui File di Azure registra in AD DS è quasi uguale a un normale file server, in cui crea un'identità (computer o account di accesso del computer o del servizio) in AD DS per l'autenticazione. L'unica differenza è che l'SPN dell'account di archiviazione registrato termina con "file.core.windows.net" che non corrisponde al suffisso del dominio. Rivolgersi all'amministratore di dominio per verificare se è necessario aggiornare i criteri di routing dei suffissi per abilitare l'autenticazione a più foreste a causa del suffisso di dominio diverso. Di seguito viene fornito un esempio per configurare i criteri di routing dei suffissi.
    
    Esempio: quando gli utenti nella foresta di un dominio desiderano raggiungere una condivisione file con l'account di archiviazione registrato in un dominio nella foresta B, questa operazione non funzionerà automaticamente perché l'entità servizio dell'account di archiviazione non ha un suffisso corrispondente al suffisso di un dominio nella foresta A. È possibile risolvere questo problema configurando manualmente una regola di routing del suffisso dalla foresta A alla foresta B per un suffisso personalizzato "file.core.windows.net".
    In primo luogo, è necessario aggiungere un nuovo suffisso personalizzato nella foresta B. Assicurarsi di disporre delle autorizzazioni amministrative appropriate per modificare la configurazione, quindi attenersi alla procedura seguente:   
    1. Accesso a un dominio computer aggiunto alla foresta B
    2.  Aprire la console "Active Directory domini e trust"
    3.  Fare clic con il pulsante destro del mouse su "domini e trust Active Directory"
    4.  Fare clic su "proprietà"
    5.  Fare clic su "Aggiungi"
    6.  Aggiungere "file.core.windows.net" come suffissi UPN
    7.  Fare clic su "Apply" (Applica) e quindi su "OK" per chiudere la procedura guidata
    
    Successivamente, aggiungere la regola di routing suffisso nella foresta A, in modo che venga reindirizzata alla foresta B.
    1.  Accedere a un dominio computer aggiunto alla foresta A
    2.  Aprire la console "Active Directory domini e trust"
    3.  Fare clic con il pulsante destro del mouse sul dominio a cui si desidera accedere alla condivisione file, quindi fare clic sulla scheda "Trust" e selezionare il dominio della foresta B dai trust in uscita. Se non è stata configurata la relazione di trust tra le due foreste, è necessario configurare prima il trust
    4.  Fare clic su "Properties..." quindi "nome suffisso routing"
    5.  Controllare se viene visualizzato il surffix "*. file.core.windows.net". In caso contrario, fare clic su' Aggiorna '
    6.  Selezionare "*. file.core.windows.net", quindi fare clic su "Abilita" e "applica"

* <a id=""></a>
**Quali aree sono disponibili per l'autenticazione File di Azure servizi di dominio Active Directory?**

    Per informazioni dettagliate, vedere [Disponibilità a livello di area](storage-files-identity-auth-active-directory-enable.md#regional-availability).
    
* <a id="ad-aad-smb-afs"></a>
**È possibile sfruttare l'autenticazione File di Azure Active Directory (AD) sulle condivisioni file gestite da Sincronizzazione file di Azure?**

    Sì, è possibile abilitare l'autenticazione AD in una condivisione file gestita da Sincronizzazione file di Azure. Le modifiche apportate agli ACL NTFS di directory/file nei file server locali verranno suddivise a livelli in File di Azure e viceversa.

* <a id="ad-aad-smb-files"></a>
**C'è una differenza nella creazione di un account computer o di un account di accesso al servizio per rappresentare l'account di archiviazione in AD?**

    La creazione di un [account computer](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (impostazione predefinita) o di un [account di accesso del servizio](/windows/win32/ad/about-service-logon-accounts) non ha alcuna differenza sulla modalità di funzionamento dell'autenticazione con File di Azure. È possibile scegliere l'identità di account di archiviazione nell'ambiente AD. Il valore predefinito DomainAccountType impostato nel cmdlet Join-AzStorageAccountForAuth è account computer. Tuttavia, la scadenza della password configurata nell'ambiente AD può essere diversa per l'account di accesso del computer o del servizio ed è necessario prendere in considerazione questo aspetto per [aggiornare la password dell'identità dell'account di archiviazione in AD](./storage-files-identity-ad-ds-update-password.md).
 
* <a id="ad-support-rest-apis"></a>
**Sono presenti API REST per supportare gli ACL di Windows directory/file.**

    Sì, sono supportate le API REST che ottengono, impostano o copiano ACL NTFS per directory o file quando si usa l'API REST di [2019-07-07](/rest/api/storageservices/versioning-for-the-azure-storage-services#version-2019-07-07) (o versione successiva). È inoltre supportata la conservazione degli ACL Windows negli strumenti basati su REST: [AzCopy v 10.4 +](https://github.com/Azure/azure-storage-azcopy/releases).

## <a name="network-file-system"></a>NFS (Network File System)

* <a id="when-to-use-nfs"></a>
**Quando è consigliabile usare File di Azure NFS?**

    Vedere [condivisioni NFS (anteprima)](storage-files-compare-protocols.md#nfs-shares-preview).

* <a id="backup-nfs-data"></a>
**Ricerca per categorie i dati di backup archiviati nelle condivisioni NFS?**

    Il backup dei dati nelle condivisioni NFS può essere orchestrato usando strumenti familiari come rsync o prodotti da uno dei nostri partner di backup di terze parti. Più partner di backup, tra cui [CommVault](https://documentation.commvault.com/commvault/v11/article?p=92634.htm), [Veeam](https://www.veeam.com/blog/?p=123438)e [Veritas](https://players.brightcove.net/4396107486001/default_default/index.html?videoId=6189967101001) , facevano parte della nostra anteprima iniziale e hanno esteso le loro soluzioni per l'uso sia di SMB 3,0 che NFS 4,1 per file di Azure.

* <a id="migrate-nfs-data"></a>
**È possibile eseguire la migrazione dei dati esistenti a una condivisione NFS?**

    All'interno di un'area, è possibile usare strumenti standard come SCP, rsync o SSHFS per spostare i dati. Poiché è possibile accedere a File di Azure NFS da più istanze di calcolo simultaneamente, è possibile migliorare le velocità di copia con caricamenti paralleli. Se si vogliono importare dati dall'esterno di un'area, usare una VPN o un Expressroute per montarli nel file system dall'data center locale.

## <a name="on-premises-access"></a>Accesso locale

* <a id="port-445-blocked"></a>
**L'ISP o l'IT blocca la porta 445 che crea un errore con il montaggio di File di Azure. Cosa devo fare?**

    È possibile ottenere informazioni sui [diversi modi per aggirare la porta bloccata 445 qui](./storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked). File di Azure consente solo connessioni che usano SMB 3.0 (con supporto della crittografia) dall'esterno dell'area o del data center. Il protocollo SMB 3.0 ha introdotto numerose funzionalità di sicurezza, tra cui la crittografia dei canali, che è molto sicura da usare in Internet. Tuttavia, è possibile che la porta 445 sia stata bloccata a causa di motivi cronologici di vulnerabilità rilevati in versioni SMB precedenti. In teoria, la porta dovrebbe essere bloccata solo per il traffico SMB 1.0 e SMB 1.0 dovrebbe essere disattivato su tutti i client.

* <a id="expressroute-not-required"></a>
**È necessario usare Azure ExpressRoute per connettersi a File di Azure o per usare Sincronizzazione file di Azure in locale?**  

    No. ExpressRoute non è richiesto per accedere a una condivisione file di Azure. Se si esegue il montaggio di una condivisione file di Azure direttamente in locale, è necessario solo che la porta 445 (TCP in uscita), ovvero la porta usata per le comunicazioni da SMB, sia aperta per l'accesso a Internet. Se si usa Sincronizzazione file di Azure, occorre solo la porta 443 (TCP in uscita) per l'accesso HTTPS (non è richiesto SMB). È comunque *possibile* usare ExpressRoute con queste opzioni di accesso.

* <a id="mount-locally"></a>
**Come è possibile montare una condivisione file di Azure nel computer locale?**  

    È possibile montare la condivisione file usando il protocollo SMB, se la porta 445 (TCP in uscita) è aperta e il client supporta il protocollo SMB 3.0, ad esempio su Windows 10 o Windows Server 2016. Se la porta 445 è bloccata dai criteri dell'organizzazione o dall'ISP, è possibile usare Sincronizzazione file di Azure per accedere alla condivisione file di Azure.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Come è possibile eseguire il backup nella condivisione file di Azure?**  
    È possibile usare [snapshot di condivisione](storage-snapshots-files.md) periodici per la protezione da eliminazioni accidentali. È anche possibile usare AzCopy, RoboCopy o uno strumento di backup di terze parti per eseguire il backup di una condivisione file montata. Backup di Azure offre il backup di File di Azure. Altre informazioni su come [eseguire il backup di condivisioni file di Azure con Backup di Azure](../../backup/backup-afs.md).

## <a name="share-snapshots"></a>Snapshot di condivisione

### <a name="share-snapshots-general"></a>Snapshot di condivisione: Generale
* <a id="what-are-snaphots"></a>
**Cosa sono gli snapshot di condivisione file?**  
    È possibile usare gli snapshot di condivisione file di Azure per creare versioni di sola lettura delle condivisioni file. È anche possibile usare File di Azure per copiare una versione precedente del contenuto nella stessa condivisione o in un percorso alternativo in Azure o in locale per ulteriori modifiche. Per altre informazioni sugli snapshot di condivisione, vedere [Panoramica degli snapshot di condivisione per File di Azure (anteprima)](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**Dove vengono archiviati gli snapshot di condivisione?**  
    Gli snapshot di condivisione vengono archiviati nello stesso account di archiviazione della condivisione file.

* <a id="snapshot-consistency"></a>
**Gli snapshot di condivisione sono coerenti con l'applicazione?**  
    No, gli snapshot di condivisione non sono coerenti con l'applicazione. È necessario scaricare le operazioni di scrittura dall'applicazione nella condivisione prima di acquisire lo snapshot di condivisione.

* <a id="snapshot-limits"></a>
**Esistono limiti al numero degli snapshot di condivisione che è possibile usare?**  
    Sì. File di Azure consente di mantenere al massimo 200 snapshot di condivisione. Gli snapshot di condivisione non vengono inclusi nel conteggio per la quota di condivisione, pertanto non esiste alcun limite per condivisione dello spazio totale usato da tutti gli snapshot di condivisione. Si applicano i limiti dell'account di archiviazione. Dopo 200 snapshot di condivisione, è necessario eliminare gli snapshot meno recenti per crearne di nuovi.

* <a id="snapshot-cost"></a>
**Qual è il costo degli snapshot di condivisione?**  
    Allo snapshot si applica il costo di transazione e archiviazione Standard. Gli snapshot sono di tipo incrementale. Lo snapshot di base corrisponde alla condivisione stessa. Tutti gli snapshot successivi sono incrementali e archiviano solo le differenze rispetto allo snapshot precedente. Questo significa che le modifiche differenziali visibili in fattura saranno minime se la varianza del carico di lavoro è molto limitata. Per informazioni sui prezzi Standard di File di Azure, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage/files/). Attualmente, per sapere le dimensioni utilizzate dallo snapshot di condivisione è necessario confrontare la capacità fatturata con quella usata. Sono in fase di sviluppo strumenti utili per creare report più dettagliati.

* <a id="ntfs-acls-snaphsots"></a>
**Gli ACL NTFS per directory e file vengono mantenuti negli snapshot di condivisione?**  
    Gli ACL NTFS per directory e file vengono mantenuti negli snapshot di condivisione.

### <a name="create-share-snapshots"></a>Creare snapshot di condivisione
* <a id="file-snaphsots"></a>
**È possibile creare snapshot di condivisione di singoli file?**  
    Gli snapshot di condivisione vengono creati a livello di condivisione file. È possibile ripristinare singoli file dallo snapshot di condivisione file, ma non è possibile creare snapshot di condivisione a livello di file. Tuttavia, se è stato acquisito uno snapshot di condivisione a livello di condivisione e si vogliono elencare gli snapshot di condivisione in cui un determinato file è stato modificato, è possibile farlo in **Versioni precedenti** in una condivisione montata in Windows. 
    
    Se è necessaria una funzionalità specifica per gli snapshot di file, è possibile farlo sapere a Microsoft tramite [UserVoice per File di Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encrypted-snapshots"></a>
**È possibile creare snapshot di condivisione di una condivisione file crittografata?**  
    È possibile creare uno snapshot di condivisione delle condivisioni file di Azure con la crittografia dei dati inattivi abilitata. È possibile ripristinare i file da uno snapshot di condivisione in una condivisione file crittografata. Se la condivisione è crittografata, anche lo snapshot di condivisione sarà crittografato.

* <a id="geo-redundant-snaphsots"></a>
**Gli snapshot di condivisione hanno ridondanza geografica?**  
    Gli snapshot di condivisione hanno la stessa ridondanza della condivisione file di Azure per cui sono stati creati. Se è stata scelta l'archiviazione con ridondanza geografica per l'account, anche lo snapshot di condivisione verrà archiviato in modo ridondante nell'area associata.

### <a name="manage-share-snapshots"></a>Gestire snapshot di condivisione
* <a id="browse-snapshots-linux"></a>
**È possibile esplorare gli snapshot di condivisione da Linux?**  
    È possibile usare l'interfaccia della riga di comando di Azure per creare, elencare, esplorare e ripristinare snapshot di condivisione in Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
**È possibile copiare gli snapshot di condivisione in un altro account di archiviazione?**  
    È possibile copiare file dagli snapshot di condivisione in un altro percorso, ma non è possibile copiare gli snapshot di condivisione stessi.

### <a name="restore-data-from-share-snapshots"></a>Eseguire il ripristino dei dati da snapshot di condivisione
* <a id="promote-share-snapshot"></a>
**È possibile alzare di livello uno snapshot di condivisione alla condivisione di base?**  
    È possibile copiare i dati da uno snapshot di condivisione in qualsiasi altra destinazione. Non è possibile alzare di livello uno snapshot di condivisione alla condivisione di base.

* <a id="restore-snapshotted-file-to-other-share"></a>
**È possibile ripristinare dati da uno snapshot di condivisione in un account di archiviazione diverso?**  
    Sì. I file da uno snapshot di condivisione possono essere copiati nella posizione originale o in una posizione alternativa che include lo stesso account di archiviazione o un account di archiviazione diverso, nella stessa area o in aree diverse. È anche possibile copiare i file in una posizione locale o in qualsiasi altro cloud.    
  
### <a name="clean-up-share-snapshots"></a>Pulire gli snapshot di condivisione
* <a id="delete-share-keep-snapshots"></a>
**È possibile eliminare la condivisione senza eliminare gli snapshot di condivisione?**  
    Non sarà possibile eliminare la condivisione se nella stessa vi sono snapshot di condivisione attivi. È possibile usare un'API per eliminare gli snapshot di condivisione, insieme alla condivisione. È anche possibile eliminare sia gli snapshot di condivisione che la condivisione nel portale di Azure.

* <a id="delete-share-with-snapshots"></a>
**Cosa accade agli snapshot di condivisione se si elimina l'account di archiviazione?**  
    Se si elimina l'account di archiviazione, vengono eliminati anche gli snapshot di condivisione.

## <a name="billing-and-pricing"></a>Prezzi e fatturazione
* <a id="vm-file-share-network-traffic"></a>
**Il traffico di rete tra una macchina virtuale di Azure e una condivisione file di Azure viene conteggiato come larghezza di banda esterna e addebitato alla sottoscrizione?**  
    Se la condivisione file e la macchina virtuale si trovano nella stessa area di Azure, non sono previsti costi aggiuntivi per il traffico tra la condivisione file e la macchina virtuale. Se la condivisione file e la macchina virtuale si trovano in aree diverse, il traffico viene addebitato come larghezza di banda esterna.

* <a id="share-snapshot-price"></a>
**Qual è il costo degli snapshot di condivisione?**  
     Nel periodo di anteprima non è previsto alcun addebito per la capacità degli snapshot di condivisione. Vengono applicati i costi di transazione e di uscita dell'archiviazione Standard. Quando la funzionalità sarà disponibile a livello generale, verranno addebitate sia la capacità che le transazioni per gli snapshot di condivisione.
     
     Gli snapshot di condivisione sono di natura incrementale. Lo snapshot di condivisione di base è la condivisione stessa. Tutti gli snapshot di condivisione successivi sono incrementali e archiviano solo le differenze rispetto allo snapshot di condivisione precedente. È previsto un addebito solo per il contenuto modificato. Se è disponibile una condivisione con 100 GiB di dati, ma solo 5 GiB sono stati modificati dall'ultimo snapshot di condivisione, lo snapshot di condivisione consuma solo 5 GiB aggiuntivi e vengono addebitati solo 105 GiB. Per altre informazioni sui costi di transazione e di uscita standard, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Scalabilità e prestazioni
* <a id="files-scale-limits"></a>
**Quali sono i limiti di scalabilità di File di Azure?**  
    Per informazioni sugli obiettivi di scalabilità e prestazioni di File per Azure, vedere [Obiettivi di scalabilità e prestazioni di File di Azure](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**Quali dimensioni sono disponibili per le condivisioni file di Azure?**  
    Le dimensioni delle condivisioni file di Azure (Premium e Standard) possono essere aumentate fino a 100 TiB. Per le istruzioni di onboarding sulle condivisioni file più grandi per il livello standard, vedere la sezione sull'[onboarding in condivisioni file di dimensioni maggiori (livello standard)](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) della guida di piano.

* <a id="lfs-performance-impact"></a>
**L'espansione della quota di condivisione file ha un effetto sui carichi di lavoro o su Sincronizzazione file di Azure?**
    
    No. L'espansione della quota non causerà alcun effetto sui carichi di lavoro o su Sincronizzazione file di Azure.

* <a id="open-handles-quota"></a>
**Quanti client possono accedere allo stesso file contemporaneamente?**    
    È prevista una quota pari a 2.000 handle aperti su un singolo file. In presenza di 2.000 handle aperti viene visualizzato un messaggio di errore che segnala il raggiungimento della quota.

* <a id="zip-slow-performance"></a>
**Le prestazioni sono ridotte durante la decompressione dei file in File di Azure. Cosa devo fare?**  
    Per trasferire grandi quantità di file in File di Azure, è consigliabile usare AzCopy (per Windows, in anteprima per Linux e UNIX) o Azure PowerShell. Questi strumenti sono stati ottimizzati per il trasferimento in rete.

* <a id="slow-perf-windows-81-2012r2"></a>
**Perché le prestazioni sono ridotte dopo aver montato una condivisione file di Azure in Windows Server 2012 R2 o Windows 8.1?**  
    Esiste un problema noto con il montaggio di una condivisione file di Azure in Windows Server 2012 R2 e Windows 8.1. Per questo problema è stata inclusa una patch nell'aggiornamento cumulativo di aprile 2014 per Windows 8.1 e Windows Server 2012 R2. Per ottenere prestazioni ottimali, verificare che questa patch sia stata applicata a tutte le istanze di Windows Server 2012 R2 e Windows 8.1. (La ricezione delle patch di Windows tramite Windows Update dovrebbe essere sempre attiva.) Per altre informazioni, vedere l'articolo della Microsoft Knowledge Base associato [Rallentamento delle prestazioni quando si accede a File di Azure da Windows 8.1 o Windows Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funzionalità e interoperabilità con altri servizi
* <a id="cluster-witness"></a>
**È possibile usare una condivisione file di Azure come *Controllo di condivisione file* per un cluster di failover di Windows Server?**  
    Questa configurazione non è attualmente supportata per una condivisione file di Azure. Per altre informazioni su come configurare questo servizio per Archiviazione BLOB di Azure, vedere [Distribuire un cloud di controllo per un cluster di failover](/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**È possibile montare una condivisione file di Azure in un'istanza di contenitore di Azure?**  
    Sì, le condivisioni file di Azure sono un'opzione utile per salvare in modo permanente le informazioni oltre la durata di un'istanza del contenitore. Per altre informazioni vedere [Mount an Azure file share with Azure Container Instances](../../container-instances/container-instances-volume-azure-files.md) (Montare una condivisione file di Azure con Istanze di Azure Container).

* <a id="rest-rename"></a>
**Esiste un'operazione di ridenominazione nell'API REST?**  
    Attualmente non è possibile.

* <a id="nested-shares"></a>
**È possibile configurare una condivisione annidata, ovvero una condivisione in una condivisione?**  
    No. La condivisione file *è* il driver virtuale che è possibile montare, quindi le condivisioni annidate non sono supportate.

* <a id="ibm-mq"></a>
**Come si può usare File di Azure con IBM MQ?**  
    IBM ha pubblicato un documento per supportare i clienti di IBM MQ nella configurazione di File di Azure con il servizio IBM. Per altre informazioni, vedere [How to set up an IBM MQ multi-instance queue manager with Microsoft Azure Files service](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service) (Come configurare IBM MQ Multi Instance Queue Manager (MIQM) con il servizio File di Microsoft Azure).

## <a name="see-also"></a>Vedere anche
* [Risolvere i problemi relativi a File di Azure in Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Risolvere i problemi relativi a File di Azure in Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md)