---
title: 'Introduzione ad Archiviazione di Azure: archiviazione cloud in Azure | Microsoft Docs'
description: Archiviazione di Azure è la soluzione di archiviazione cloud Microsoft. Archiviazione di Azure offre uno spazio di archiviazione per oggetti dati sicuro, durevole e ridondante, a disponibilità e scalabilità elevate.
services: storage
author: tamram
ms.service: storage
ms.topic: get-started-article
ms.date: 07/11/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: e483997140efc1d75466d887e42383d887f8a6f4
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963250"
---
# <a name="introduction-to-azure-storage"></a>Introduzione ad Archiviazione di Azure

Archiviazione di Azure è la soluzione di archiviazione cloud Microsoft per i moderni scenari di archiviazione dati. Archiviazione di Azure offre un archivio a scalabilità elevata per oggetti dati, un servizio di file system per il cloud, un archivio di messaggistica per messaggistica affidabile e un archivio NoSQL. Archiviazione di Azure presenta le caratteristiche seguenti:

- **Durabilità e disponibilità elevata.** La ridondanza garantisce che i dati siano al sicuro in caso di errori hardware temporanei. Si può anche scegliere di replicare i dati tra data center o aree geografiche per una protezione aggiuntiva da catastrofi locali o calamità naturali. Con questo tipo di replica, i dati mantengono disponibilità elevata in caso di interruzioni impreviste. 
- **Sicurezza.** Tutti i dati scritti in Archiviazione di Azure vengono crittografati dal servizio. Archiviazione di Azure offre un controllo dettagliato su chi potrà accedere ai dati.
- **Scalabilità.** La soluzione Archiviazione di Azure è progettata per offrire scalabilità elevata in modo da soddisfare le esigenze di archiviazione dati e di prestazioni delle attuali applicazioni. 
- **Soluzione gestita.** Microsoft Azure gestisce la manutenzione e i problemi critici per conto dell'utente.
- **Accessibilità.** I dati in Archiviazione di Azure sono accessibili da ogni parte del mondo tramite HTTP o HTTPS. Microsoft offre SDK per Archiviazione di Azure in diversi linguaggi, come .NET, Java, Node.js, Python, PHP, Ruby, Go e altri, nonché un'avanzata API REST. Archiviazione di Azure supporta l'esecuzione di script in Azure PowerShell o nell'interfaccia della riga di comando di Azure. Il portale di Azure e Azure Storage Explorer, inoltre, offrono semplici soluzioni visive per l'uso dei dati.  

## <a name="azure-storage-services"></a>Servizi di Archiviazione di Azure

Archiviazione di Azure include i servizi dati seguenti. 

- [BLOB di Azure](../blobs/storage-blobs-introduction.md): archivio oggetti a scalabilità elevata per dati di testo e binari.
- [File di Azure](../files/storage-files-introduction.md): condivisioni file gestite per distribuzioni cloud o locali.
- [Code di Azure](../queues/storage-queues-introduction.md): archivio di messaggistica per una messaggistica affidabile tra i componenti delle applicazioni. 
- [Tabelle di Azure](../tables/table-storage-overview.md): archivio NoSQL per l'archiviazione senza schema di dati strutturati.

Ogni servizio è accessibile tramite un account di archiviazione. Per iniziare, vedere [Creare un account di archiviazione](storage-quickstart-create-account.md).

## <a name="blob-storage"></a>Archiviazione BLOB

L'archivio BLOB di Azure è la soluzione di archiviazione di oggetti Microsoft per il cloud. L'archivio BLOB è ottimizzato per l'archiviazione di enormi quantità di dati non strutturati, come dati di testo o binari. 

L'archivio BLOB è ideale per le operazioni seguenti:

* Invio di immagini o documenti direttamente in un browser.
* Archiviazione di file per l'accesso distribuito.
* Flussi audio e video.
* Archiviazione di dati per backup e ripristino, ripristino di emergenza e archiviazione.
* Archiviazione di dati a scopo di analisi da parte di un servizio locale o ospitato in Azure.

È possibile accedere agli oggetti nell'archivio BLOB da ogni parte del mondo tramite HTTP o HTTPS. Gli utenti o le applicazioni client possono accedere ai BLOB tramite URL, l'[API REST di Archiviazione di Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/storage) oppure una libreria client di Archiviazione di Azure. Le librerie client di archiviazione sono disponibili per molti linguaggi, tra cui [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](http://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](http://azure.github.io/azure-storage-php/) e [Ruby](http://azure.github.io/azure-storage-ruby).

Per altre informazioni sull'archiviazione BLOB, vedere [Introduzione all'archiviazione BLOB](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>File di Azure
[File di Azure](../files/storage-files-introduction.md) consente di configurare condivisioni file di rete a disponibilità elevata a cui è possibile accedere usando il protocollo Server Message Block (SMB) standard. Più VM possono quindi condividere gli stessi file con accesso sia in lettura che in scrittura. È possibile leggere i file usando l'interfaccia REST o le librerie dei client di archiviazione.

Una delle differenze tra File di Azure e i file in una condivisione file aziendale è la possibilità di accedere ai file da qualsiasi parte del mondo usando un URL che punta al file e include un token di firma di accesso condiviso. È possibile generare token di firma di accesso condiviso, che consentono l'accesso specifico a un asset privato per un periodo di tempo specifico.

Le condivisioni file possono essere usate per molti scenari comuni:

* Molte applicazioni locali usano condivisioni file. Questa funzionalità semplifica la migrazione ad Azure di tali applicazioni che condividono i dati. Se si monta la condivisione file nella stessa lettera di unità usata dall'applicazione locale, la parte dell'applicazione che accede alla condivisione file funzionerà con modifiche tutt'al più minime.

* È possibile archiviare i file di configurazione in una condivisione di file e accedervi da più macchine virtuali. Gli strumenti e le utilità usati da più sviluppatori in un gruppo possono essere archiviati in una condivisione file, assicurandosi che tutti riescano a trovarli e che usino la stessa versione.

* Log di diagnostica, metriche e dump di arresto anomalo del sistema sono solo tre esempi di dati che possono essere scritti in una condivisione file ed elaborati o analizzati in un secondo momento.

L'autenticazione basata su Active Directory e gli elenchi di controllo di accesso attualmente non sono supportati, ma lo saranno in futuro. Le credenziali dell'account di archiviazione vengono usate per fornire l'autenticazione per l'accesso alla condivisione file. Chiunque abbia la condivisione montata avrà quindi accesso completo in lettura/scrittura alla condivisione.

Per altre informazioni su File di Azure, vedere [Introduzione a File di Azure](../files/storage-files-introduction.md).

## <a name="queue-storage"></a>Archiviazione code

Il servizio di accodamento di Azure viene usato per archiviare e recuperare i messaggi. La dimensione massima dei messaggi nella coda può essere di 64 KB e una coda può contenere milioni di messaggi. Le code vengono in genere usate per archiviare elenchi di messaggi da elaborare in modo asincrono.

Si supponga ad esempio che si voglia consentire ai clienti di caricare immagini e che si voglia creare un'anteprima per ogni immagine. È possibile creare le anteprime lasciando in attesa il cliente durante il caricamento delle immagini oppure usare una coda. Quando il cliente completa il caricamento, viene scritto un messaggio alla coda. Funzioni di Azure recupera quindi il messaggio dalla coda e crea le anteprime. Ogni parte di questa elaborazione può essere ridimensionata separatamente, offrendo un maggiore controllo per l'ottimizzazione ai fini dell'utilizzo.

Per altre informazioni sulle code di Azure, vedere [Introduzione alle code](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Archiviazione tabelle

L'archiviazione tabelle di Azure è ora inclusa in Azure Cosmos DB. Per la documentazione sull'archiviazione tabelle di Azure, vedere [Panoramica di Archiviazione tabelle di Azure](../tables/table-storage-overview.md). Oltre al servizio di archiviazione tabelle di Azure esistente, è disponibile una nuova API Tabelle di Azure Cosmos DB che offre tabelle con ottimizzazione per la velocità effettiva, distribuzione globale e indici secondari automatici. Per altre informazioni e per provare la nuova esperienza Premium, vedere l'articolo relativo all'[API Tabelle di Azure Cosmos DB](https://aka.ms/premiumtables).

Per altre informazioni sull'archivio tabelle, vedere [Panoramica di Archiviazione tabelle di Azure](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Archiviazione su disco

Archiviazione di Azure include anche le funzionalità di dischi gestiti e non gestiti usate dalle macchine virtuali. Per altre informazioni su queste funzionalità, vedere la [documentazione sul servizio di calcolo](https://docs.microsoft.com/azure/#pivot=products&panel=Compute).

## <a name="types-of-storage-accounts"></a>Tipi di account di archiviazione

Questa tabella mostra i diversi tipi di account di archiviazione e gli oggetti che possono essere usati con ogni tipo.

|**Tipo di account di archiviazione**|**Utilizzo generico Standard**|**Utilizzo generico Premium**|**Archiviazione BLOB, livelli ad accesso frequente e ad accesso sporadico**|
|-----|-----|-----|-----|
|**Servizi supportati**| Servizi BLOB, file, code e tabelle | Servizio BLOB | Servizio BLOB|
|**Tipi di BLOB supportati**|BLOB in blocchi, BLOB di pagine e BLOB di aggiunta | BLOB di pagine | BLOB in blocchi e BLOB di aggiunta|

### <a name="general-purpose-storage-accounts"></a>Account di archiviazione per utilizzo generico

Sono disponibili due tipi di account di archiviazione per utilizzo generico.

#### <a name="standard-storage"></a>Archiviazione standard

Gli account di archiviazione più usati sono account di archiviazione Standard, che possono essere usati per tutti i tipi di dati. Gli account di archiviazione Standard usano supporti di memorizzazione magnetici per archiviare i dati.

#### <a name="premium-storage"></a>Archiviazione Premium

L'Archiviazione Premium fornisce risorse di archiviazione a prestazioni elevate per i BLOB di pagine, usati principalmente per i file VHD. Gli account di archiviazione Premium usano unità SSD per archiviare i dati. Microsoft consiglia di usare l'Archiviazione Premium per tutte le VM.

### <a name="blob-storage-accounts"></a>Account di archiviazione BLOB

L'account di archiviazione BLOB è un account di archiviazione specializzato, usato per archiviare BLOB in blocchi e BLOB di aggiunta. Non è possibile archiviare i BLOB di pagine in questi account e quindi non è possibile archiviare file VHD. Questi account consentono di impostare il livello di accesso su accesso frequente o accesso sporadico. È possibile modificare il livello in qualsiasi momento.

Il livello ad accesso frequente viene usato per i file a cui si accede spesso. Le risorse di archiviazione hanno un costo superiore, ma il costo per l'accesso ai BLOB è decisamente inferiore. Per i BLOB archiviati nel livello ad accesso sporadico il costo per l'accesso ai BLOB è superiore, ma le risorse di archiviazione hanno un costo decisamente inferiore.

## <a name="accessing-your-blobs-files-and-queues"></a>Accesso a BLOB, file e code

Ogni account di archiviazione include due chiavi di autenticazione, che possono essere usate per qualsiasi operazione. Sono disponibili due chiavi per consentire il rollover occasionale delle chiavi per migliorare la sicurezza. È fondamentale mantenere protette le chiavi, perché il loro possesso, insieme al nome dell'account, consente l'accesso illimitato a tutti i dati nell'account di archiviazione.

Questa sezione esamina due modi per proteggere l'account di archiviazione e i rispettivi dati. Per informazioni dettagliate sulla protezione dell'account di archiviazione e dei dati, vedere [Azure Storage security guide](storage-security-guide.md) (Guida alla sicurezza di Archiviazione di Azure).

### <a name="securing-access-to-storage-accounts-using-azure-ad"></a>Protezione dell'accesso agli account di archiviazione tramite Azure AD

Uno dei modi per proteggere l'accesso ai dati di archiviazione è costituito dal controllo dell'accesso alle chiavi degli account di archiviazione. Il controllo degli accessi in base al ruolo di Resource Manager consente di assegnare ruoli a utenti, gruppi o applicazioni. Questi ruoli sono associati a un insieme specifico di azioni consentite o non consentite. L'uso del controllo degli accessi in base al ruolo per concedere l'accesso a un account di archiviazione consente solo di eseguire le operazioni di gestione per tale account di archiviazione, ad esempio la modifica del livello di accesso. Non è possibile usare il controllo degli accessi in base al ruolo per concedere l'accesso a oggetti dati come un contenitore specifico o una condivisione di file. È tuttavia possibile usare il controllo degli accessi in base al ruolo per concedere l'accesso alle chiavi dell'account di archiviazione, che possono essere quindi usate per leggere gli oggetti dati.

### <a name="securing-access-using-shared-access-signatures"></a>Protezione dell'accesso tramite le firme di accesso condiviso

È possibile usare le firme di accesso condiviso e i criteri di accesso archiviati per proteggere gli oggetti dati. Una firma di accesso condiviso è una stringa contenente un token di sicurezza, che può essere collegato a un URI per un asset che consente di delegare l'accesso a oggetti di archiviazione specifici e specificare limitazioni, ad esempio le autorizzazioni e l'intervallo di data/ora di accesso. Questa funzionalità offre capacità estese. Per informazioni dettagliate, vedere [Using Shared Access Signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md) (Uso delle firme di accesso condiviso).

### <a name="public-access-to-blobs"></a>Accesso pubblico ai BLOB

Il servizio BLOB consente di fornire l'accesso pubblico a un contenitore e ai rispettivi BLOB o a un BLOB specifico. Quando si indica che un contenitore o un BLOB è pubblico, qualsiasi utente lo potrà leggere in forma anonima, in quanto non è necessaria l'autenticazione. Questo approccio è ideale ad esempio se si ha un sito Web che usa immagini, video o documenti dall'archivio BLOB. Per altre informazioni, vedere [Gestire l'accesso in lettura anonimo a contenitori e BLOB](../blobs/storage-manage-access-to-resources.md).

## <a name="encryption"></a>Crittografia

Sono disponibili due tipi di crittografia di base per i servizi di archiviazione. Per altre informazioni sulla sicurezza e sulla crittografia, vedere [Guida alla sicurezza di Archiviazione di Azure](storage-security-guide.md).

### <a name="encryption-at-rest"></a>Crittografia di dati inattivi

Crittografia del servizio di archiviazione di Azure per dati inattivi (SSE) consente di proteggere e salvaguardare i dati, in modo da soddisfare i criteri di sicurezza e conformità dell'organizzazione. Questa funzionalità consente ad Archiviazione di Azure di crittografare automaticamente i dati prima della persistenza nella risorsa di archiviazione e di decrittografarli prima del recupero. La crittografia, la decrittografia e la gestione delle chiavi sono completamente trasparenti per gli utenti.


La crittografia del servizio di archiviazione crittografa automaticamente i dati in tutti i livelli di prestazioni (Standard e Premium), tutti i modelli di distribuzione (distribuzione classica e Azure Resource Manager) e tutti i servizi di Archiviazione di Azure (BLOB, code, tabelle e file). La crittografia del servizio di archiviazione non influisce sulle prestazioni di Archiviazione di Azure.

Per altre informazioni sulla crittografia SSE dei dati inattivi, vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md).

### <a name="client-side-encryption"></a>crittografia lato client

Le librerie client di archiviazione includono metodi che possono essere chiamati per crittografare i dati a livello di codice prima del transito dal client ad Azure. I dati vengono archiviati crittografati, ovvero sono crittografati anche quando inattivi. Durante la lettura dei dati, le informazioni vengono decrittografate dopo la ricezione.

Per altre informazioni sulla crittografia lato client, vedere [Crittografia lato client con .NET per Archiviazione di Microsoft Azure](storage-client-side-encryption.md) .

## <a name="replication"></a>Replica

Per assicurare che i dati siano durevoli, Archiviazione di Azure esegue la replica di più copie dei dati. Quando si configura l'account di archiviazione, si seleziona un tipo di replica. Nella maggior parte dei casi questa impostazione può essere modificata dopo la creazione dell'account di archiviazione. 

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Per informazioni sul ripristino di emergenza, vedere [Cosa fare se si verifica un'interruzione di Archiviazione di Azure](storage-disaster-recovery-guidance.md).

## <a name="transferring-data-to-and-from-azure-storage"></a>Trasferimento dei dati da e verso Archiviazione di Azure

È possibile usare l'utilità della riga di comando AzCopy per copiare BLOB e dati di file all'interno dell'account di archiviazione o tra account di archiviazione. Per istruzioni, vedere uno degli articoli seguenti:

* [Transfer data with AzCopy for Windows](storage-use-azcopy.md) (Trasferire dati con AzCopy per Windows)
* [Transfer data with AzCopy for Linux](storage-use-azcopy-linux.md) (Trasferire dati con AzCopy per Linux)

AzCopy si basa sulla [libreria di spostamento dei dati di Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/), attualmente disponibile in anteprima.

Il servizio Importazione/Esportazione di Azure può essere usato per importare o esportare quantità elevate di dati di BLOB verso o dall'account di archiviazione. È possibile preparare e inviare più dischi rigidi a un data center di Azure, in cui i dati verranno trasferiti verso/dai dischi rigidi e i dischi rigidi verranno restituiti all'utente. Per altre informazioni sul servizio di importazione/esportazione, vedere [Usare il servizio di importazione/esportazione di Microsoft Azure per trasferire dati nell'archivio BLOB](../storage-import-export-service.md).

Per importare quantità elevate di dati di BLOB nell'account di archiviazione in modo veloce, economico e affidabile, è anche possibile usare Azure Data Box Disk. Microsoft spedisce fino a 5 dischi SSD (Solid State Disk) crittografati con capacità di 40 TB al data center del cliente tramite un corriere regionale. È quindi possibile configurare i dischi, copiarvi i dati tramite una connessione USB e rispedirli ad Azure rapidamente. Nel data center di Azure, i dati vengono caricati automaticamente dalle unità al cloud. Per altre informazioni su questa soluzione, vedere la [panoramica di Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview).

## <a name="pricing"></a>Prezzi

Per informazioni dettagliate sui prezzi per Archiviazione di Azure, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>API di archiviazione, librerie e strumenti
Le risorse di archiviazione di Azure sono accessibile da qualsiasi linguaggio in grado di eseguire richieste HTTP/HTTPS. In Archiviazione di Azure sono inoltre disponibili librerie di programmazione per diversi linguaggi comuni. Tali librerie semplificano molti aspetti dell'utilizzo di Archiviazione di Azure gestendo dettagli come la chiamata sincrona e asincrona, l'esecuzione delle operazioni in batch, la gestione delle eccezioni, la ripetizione automatica dei tentativi, il comportamento operativo e così via. Le librerie attualmente disponibili riguardano i linguaggi e le piattaforme seguenti, ma presto ne saranno disponibili altre:

### <a name="azure-storage-data-api-and-library-references"></a>Informazioni di riferimento su librerie e API di dati di Archiviazione di Azure
* [API REST dei servizi di archiviazione](https://docs.microsoft.com/rest/api/storageservices/)
* [Libreria client di archiviazione per .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
* [Libreria client di archiviazione per Java/Android](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Libreria client di archiviazione per Node.js](https://docs.microsoft.com/javascript/api/azure-storage)
* [Libreria client di archiviazione per Python](https://github.com/Azure/azure-storage-python)
* [Libreria client di archiviazione per PHP](https://github.com/Azure/azure-storage-php)
* [Libreria client di archiviazione per Ruby](https://github.com/Azure/azure-storage-ruby)
* [Libreria client di archiviazione per C++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Informazioni di riferimento su librerie e API di gestione di Archiviazione di Azure
* [API REST del provider di risorse di archiviazione](https://docs.microsoft.com/rest/api/storagerp/)
* [Libreria client del provider di risorse di archiviazione per .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
* [API REST di gestione del servizio di archiviazione (classico)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>Informazioni di riferimento su librerie e API di spostamento dati di Archiviazione di Azure
* [API REST del servizio di importazione/esportazione dell'archiviazione](https://docs.microsoft.com/rest/api/storageimportexport/)
* [Libreria client di spostamento dei dati di archiviazione per .NET](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.datamovement)

### <a name="tools-and-utilities"></a>Strumenti e utilità
* [Cmdlet di Azure PowerShell per Archiviazione](https://docs.microsoft.com/powershell/module/azure.storage)
* [Cmdlet dell'interfaccia della riga di comando di Azure per Archiviazione](https://docs.microsoft.com/cli/azure/storage)
* [Utilità da riga di comando di AzCopy](https://aka.ms/downloadazcopy)
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) è un'app autonoma gratuita di Microsoft che consente di interagire visivamente con i dati di Archiviazione di Azure in Windows, macOS e Linux.
* [Strumento client di Archiviazione di Azure](../storage-explorers.md)
* [Strumenti di sviluppo di Azure](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare Archiviazione di Azure, vedere [Create a storage account](storage-quickstart-create-account.md) (Creare un account di archiviazione).
