---
title: Introduzione ad Archiviazione di Azure | Documentazione Microsoft
description: Introduzione ad Archiviazione di Azure, la risorsa di archiviazione dei dati Microsoft sul cloud.
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: a4a1bc58-ea14-4bf5-b040-f85114edc1f1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/10/2017
ms.author: tamram
ms.openlocfilehash: e0da76d1c99de94762a54f552e49f7ee75eba26f
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="introduction-to-microsoft-azure-storage"></a>Introduzione ad Archiviazione di Microsoft Azure

Archiviazione di Microsoft Azure è un servizio cloud gestito da Microsoft che offre risorse di archiviazione a disponibilità, sicurezza, durabilità, scalabilità e ridondanza elevate. Microsoft si occupa della manutenzione e gestisce i problemi critici per conto dell'utente.

Archiviazione di Azure è costituito da tre servizi dati: archiviazione BLOB, archiviazione file e archiviazione code. L'archiviazione BLOB supporta le risorse di archiviazione Standard e Premium e l'archiviazione Premium usa solo unità SSD per offrire le prestazioni più veloci possibili. È disponibile anche la funzionalità delle risorse di archiviazione ad accesso sporadico, che consente di archiviare quantità elevate di dati a cui si accede raramente a un costo inferiore.

In questo articolo sono disponibili informazioni sugli argomenti seguenti:
* Servizi di Archiviazione di Azure
* Tipi di account di archiviazione
* Accesso a BLOB, code e file
* Crittografia
* Replica
* Trasferimento di dati verso e dalle risorse di archiviazione
* Numerose librerie client di archiviazione disponibili

Per iniziare a usare Archiviazione di Azure, vedere [Create a storage account](storage-quickstart-create-account.md) (Creare un account di archiviazione).

## <a name="introducing-the-azure-storage-services"></a>Introduzione ai servizi di archiviazione di Azure

Per usare i servizi forniti da Archiviazione di Azure, ovvero archiviazione BLOB, archiviazione file e archiviazione code, è prima di tutto necessario creare un account di archiviazione e quindi è possibile trasferire i dati verso/da un servizio specifico in tale account di archiviazione.

## <a name="blob-storage"></a>Archiviazione BLOB

I BLOB sono essenzialmente file simili a quelli archiviati nel computer o nel tablet, nel dispositivo mobile e così via. Possono essere file di qualsiasi tipo, ad esempio immagini, file di Microsoft Excel, file HTML, dischi rigidi virtuali, Big Data come log, backup dei database. I BLOB vengono archiviati nei contenitori, che sono simili alle cartelle.

Dopo l'archiviazione dei file nell'archivio BLOB, è possibile accedere a tali file ovunque ci si trovi, tramite URL, interfaccia REST o una delle librerie client di archiviazione di Azure SDK. Le librerie client di archiviazione sono disponibili per molti linguaggi, tra cui Node.js, Java, PHP, Ruby, Python e .NET.

Esistono tre tipi di BLOB, ovvero BLOB in blocchi, BLOB di accodamento e BLOB di pagine, usati per i file VHD.

* I BLOB in blocchi vengono usati per contenere file normali con dimensioni fino a 4,7 TB.
* I BLOB di pagine vengono usati per contenere file ad accesso casuale con dimensioni fino a 8 TB. Vengono usati per i file VHD su cui si basano le VM.
* I BLOB di aggiunta sono costituiti da blocchi, analogamente ai BLOB in blocchi, ma sono ottimizzati per le operazioni di aggiunta. Vengono usati per la registrazione di informazioni nello stesso BLOB da più VM.

Se sono presenti set di dati molto grandi e i vincoli della rete ne impediscono il caricamento o il download in archivi BLOB tramite una connessione, è possibile spedire un set di unità disco rigido a Microsoft per importare o esportare i dati direttamente dal data center. Vedere [Usare il servizio di importazione/esportazione di Microsoft Azure per trasferire i dati nell'archiviazione BLOB](../storage-import-export-service.md).

## <a name="azure-files"></a>File di Azure
[File di Azure](../files/storage-files-introduction.md) consente di configurare condivisioni file di rete a disponibilità elevata a cui è possibile accedere usando il protocollo Server Message Block (SMB) standard. Più VM possono quindi condividere gli stessi file con accesso sia in lettura che in scrittura. È possibile leggere i file usando l'interfaccia REST o le librerie dei client di archiviazione.

Una delle differenze tra File di Azure e i file in una condivisione file aziendale è la possibilità di accedere ai file da qualsiasi parte del mondo usando un URL che punta al file e include un token di firma di accesso condiviso. È possibile generare token di firma di accesso condiviso, che consentono l'accesso specifico a un asset privato per un periodo di tempo specifico.

Le condivisioni file possono essere usate per molti scenari comuni:

* Molte applicazioni locali usano condivisioni file. Questa funzionalità semplifica la migrazione ad Azure di tali applicazioni che condividono i dati. Se si monta la condivisione file nella stessa lettera di unità usata dall'applicazione locale, la parte dell'applicazione che accede alla condivisione file funzionerà con modifiche tutt'al più minime.

* È possibile archiviare i file di configurazione in una condivisione di file e accedervi da più macchine virtuali. Gli strumenti e le utilità usati da più sviluppatori in un gruppo possono essere archiviati in una condivisione file, assicurandosi che tutti riescano a trovarli e che usino la stessa versione.

* Log di diagnostica, metriche e dump di arresto anomalo del sistema sono solo tre esempi di dati che possono essere scritti in una condivisione file ed elaborati o analizzati in un secondo momento.

L'autenticazione basata su Active Directory e gli elenchi di controllo di accesso attualmente non sono supportati, ma lo saranno in futuro. Le credenziali dell'account di archiviazione vengono usate per fornire l'autenticazione per l'accesso alla condivisione file. Chiunque abbia la condivisione montata avrà quindi accesso completo in lettura/scrittura alla condivisione.

## <a name="queue-storage"></a>Archiviazione code

Il servizio di accodamento di Azure viene usato per archiviare e recuperare i messaggi. La dimensione massima dei messaggi nella coda può essere di 64 KB e una coda può contenere milioni di messaggi. Le code vengono in genere usate per archiviare elenchi di messaggi da elaborare in modo asincrono.

Si supponga ad esempio che si voglia consentire ai clienti di caricare immagini e che si voglia creare un'anteprima per ogni immagine. È possibile creare le anteprime lasciando in attesa il cliente durante il caricamento delle immagini oppure usare una coda. Quando il cliente completa il caricamento, viene scritto un messaggio alla coda. Funzioni di Azure recupera quindi il messaggio dalla coda e crea le anteprime. Ogni parte di questa elaborazione può essere ridimensionata separatamente, offrendo un maggiore controllo per l'ottimizzazione ai fini dell'utilizzo.

## <a name="table-storage"></a>Archiviazione tabelle

L'archiviazione tabelle di Azure è ora inclusa in Azure Cosmos DB. Per la documentazione sull'archiviazione tabelle di Azure, vedere [Panoramica di Archiviazione tabelle di Azure](../../cosmos-db/table-storage-overview.md). Oltre al servizio di archiviazione tabelle di Azure esistente, è disponibile una nuova API Tabelle di Azure Cosmos DB che offre tabelle con ottimizzazione per la velocità effettiva, distribuzione globale e indici secondari automatici. Per altre informazioni e per provare la nuova esperienza Premium, vedere l'articolo relativo all'[API Tabelle di Azure Cosmos DB](https://aka.ms/premiumtables).

## <a name="disk-storage"></a>Archiviazione su disco

Archiviazione di Azure include anche le funzionalità di dischi gestiti e non gestiti usate dalle macchine virtuali. Per altre informazioni su queste funzionalità, vedere la [documentazione sul servizio di calcolo](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).

## <a name="types-of-storage-accounts"></a>Tipi di account di archiviazione

Questa tabella mostra i diversi tipi di account di archiviazione e gli oggetti che possono essere usati con ogni tipo.

|**Tipo di account di archiviazione**|**Utilizzo generico Standard**|**Utilizzo generico Premium**|**Archiviazione BLOB, livelli ad accesso frequente e ad accesso sporadico**|
|-----|-----|-----|-----|
|**Servizi supportati**| Servizi BLOB, file, code | Servizio BLOB | Servizio BLOB|
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

Sono disponibili alcuni tipi di crittografia di base per i servizi di archiviazione.

### <a name="encryption-at-rest"></a>Crittografia di dati inattivi

È possibile abilitare Crittografia del servizio di archiviazione nel servizio file (anteprima) o nel servizio BLOB per un account di archiviazione di Azure. Se questa opzione è abilitata, tutti i dati scritti nel servizio specifico vengono crittografati prima della scrittura. In caso di lettura, i dati vengono decrittografati prima della restituzione.

### <a name="client-side-encryption"></a>crittografia lato client

Le librerie client di archiviazione includono metodi che possono essere chiamati per crittografare i dati a livello di codice prima del transito dal client ad Azure. I dati vengono archiviati crittografati, ovvero sono crittografati anche quando inattivi. Durante la lettura dei dati, le informazioni vengono decrittografate dopo la ricezione.

### <a name="encryption-in-transit-with-azure-file-shares"></a>Crittografia in transito con le condivisioni di File di Azure

Per altre informazioni sulle firme di accesso condiviso, vedere [Using Shared Access Signatures (SAS)](../storage-dotnet-shared-access-signature-part-1.md) (Uso di firme di accesso condiviso). Per altre informazioni sull'accesso sicuro all'account di archiviazione, vedere [Gestire l'accesso in lettura anonimo a contenitori e BLOB](../blobs/storage-manage-access-to-resources.md) e [Autenticazione per i servizi di archiviazione di Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx).

Per altre informazioni sulla protezione dell'account di archiviazione e sulla crittografia, vedere [Guida alla sicurezza di Archiviazione di Azure](storage-security-guide.md).

## <a name="replication"></a>Replica

Per assicurare che i dati siano durevoli, Archiviazione di Azure può mantenere e gestire più copie dei dati. Questo approccio viene definito replica o ridondanza. Quando si configura l'account di archiviazione, si seleziona un tipo di replica. Nella maggior parte dei casi questa impostazione può essere modificata dopo la configurazione dell'account di archiviazione.

Tutti gli account di archiviazione offrono l'**archiviazione con ridondanza locale**, progettata per garantire almeno il 99,999999999% (11 9) di durabilità degli oggetti nell'arco di un anno. Di conseguenza, Archiviazione di Azure gestisce più copie dei dati nel data center specificato durante la configurazione dell'account di archiviazione. Quando viene eseguito il commit delle modifiche, tutte le copie vengono aggiornate prima della restituzione dell'esito positivo. Le repliche sono quindi sempre sincronizzate. Le copie, inoltre, si trovano in domini di errore e di aggiornamento separati e i dati sono quindi disponibili anche in caso di errore in un nodo di archiviazione contenente i dati o se questo viene portato offline per un aggiornamento.

**Archiviazione con ridondanza locale (LRS)**

Come illustrato sopra, con l'archiviazione con ridondanza locale si hanno più copie dei dati in un singolo data center. Ciò consente di gestire il problema della mancata disponibilità dei dati in caso di errore o di spostamento offline di un nodo per l'aggiornamento, ma non in caso di mancata disponibilità di un intero data center.

**Archiviazione con ridondanza della zona**

L'archiviazione con ridondanza della zona è progettata per offrire almeno il 99,9999999999% (12 9) di durabilità degli oggetti nell'arco di un anno mantenendo le copie locali dei dati e un altro set di copie dei dati. Il secondo set di copie viene replicato in modo asincrono nei data center in una o due aree. Si noti che l'archiviazione con ridondanza della zona è disponibile solo per i BLOB in blocchi negli account di archiviazione per utilizzo generico. Dopo aver creato l'account di archiviazione e selezionato l'archiviazione con ridondanza della zona, non è inoltre possibile convertirlo per usarlo con un altro tipo di replica o viceversa.

L'archiviazione con ridondanza della zona fornisce una durabilità superiore rispetto all'archiviazione con ridondanza locale, ma gli account di archiviazione con ridondanza della zona non includono metriche o funzionalità di registrazione.

**Archiviazione con ridondanza geografica (GRS)**

L'archiviazione con ridondanza geografica è progettata per offrire il 99,99999999999999% (16 9) di durabilità degli oggetti nell'arco di un anno mantenendo le copie locali dei dati in un'area primaria e un altro set di copie dei dati in un'area secondaria distante centinaia di chilometri dall'area primaria. In caso di errore nell'area primaria, il servizio di archiviazione di Azure eseguirà il failover all'area secondaria.

**Archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).**

L'archiviazione con ridondanza geografica e accesso in lettura è esattamente uguale all'archiviazione con ridondanza geografica, ma si ottiene l'accesso in lettura ai dati nella posizione secondaria. Se il data center primario risulta temporaneamente non disponibile, è possibile continuare a leggere i dati dalla posizione secondaria. Questo approccio può risultare molto utile. È ad esempio possibile che sia presente un'applicazione Web che passa alla modalità di sola lettura e fa riferimento a una copia secondaria, consentendo l'accesso parziale, anche se gli aggiornamenti non sono disponibili.

> [!IMPORTANT]
> È possibile cambiare la modalità di replica dei dati dopo la creazione dell'account di archiviazione, a meno che non sia stata specificata l'archiviazione con ridondanza della zona al momento della creazione dell'account. Si noti tuttavia che se si passa dall'archiviazione con ridondanza locale all'archiviazione con ridondanza geografica o all'archiviazione con ridondanza geografica e accesso in lettura, potrebbe essere addebitato un costo una tantum per il trasferimento dei dati.
>

Per altre informazioni sulla replica, vedere [Replica di Archiviazione di Azure](storage-redundancy.md).

Per informazioni sul ripristino di emergenza, vedere [Cosa fare se si verifica un'interruzione di Archiviazione di Azure](storage-disaster-recovery-guidance.md).

Per un esempio su come usare l'archiviazione con ridondanza geografica e accesso in lettura per assicurare la disponibilità elevata, vedere [Progettazione di applicazioni a disponibilità elevata con RA-GRS](storage-designing-ha-apps-with-ragrs.md).

## <a name="transferring-data-to-and-from-azure-storage"></a>Trasferimento dei dati da e verso Archiviazione di Azure

È possibile usare l'utilità della riga di comando AzCopy per copiare BLOB e dati di file all'interno dell'account di archiviazione o tra account di archiviazione. Per istruzioni, vedere uno degli articoli seguenti:

* [Transfer data with AzCopy for Windows](storage-use-azcopy.md) (Trasferire dati con AzCopy per Windows)
* [Transfer data with AzCopy for Linux](storage-use-azcopy-linux.md) (Trasferire dati con AzCopy per Linux)

AzCopy si basa sulla [libreria di spostamento dei dati di Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/), attualmente disponibile in anteprima.

Il servizio Importazione/Esportazione di Azure può essere usato per importare o esportare quantità elevate di dati di BLOB verso o dall'account di archiviazione. È possibile preparare e inviare più dischi rigidi a un data center di Azure, in cui i dati verranno trasferiti verso/dai dischi rigidi e i dischi rigidi verranno restituiti all'utente. Per altre informazioni sul servizio di importazione/esportazione, vedere [Usare il servizio di importazione/esportazione di Microsoft Azure per trasferire dati nell'archivio BLOB](../storage-import-export-service.md).

## <a name="pricing"></a>Prezzi

Per informazioni dettagliate sui prezzi per Archiviazione di Azure, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>API di archiviazione, librerie e strumenti
Le risorse di archiviazione di Azure sono accessibile da qualsiasi linguaggio in grado di eseguire richieste HTTP/HTTPS. In Archiviazione di Azure sono inoltre disponibili librerie di programmazione per diversi linguaggi comuni. Tali librerie semplificano molti aspetti dell'utilizzo di Archiviazione di Azure gestendo dettagli come la chiamata sincrona e asincrona, l'esecuzione delle operazioni in batch, la gestione delle eccezioni, la ripetizione automatica dei tentativi, il comportamento operativo e così via. Le librerie attualmente disponibili riguardano i linguaggi e le piattaforme seguenti, ma presto ne saranno disponibili altre:

### <a name="azure-storage-data-services"></a>Servizi dati di Archiviazione di Azure
* [API REST dei servizi di archiviazione](/rest/api/storageservices/)
* [Libreria client di archiviazione per .NET](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [Libreria client di archiviazione per C++](https://github.com/Azure/azure-storage-cpp)
* [Libreria client di archiviazione per Java/Android](https://azure.microsoft.com/develop/java/)
* [Libreria client di archiviazione per Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Libreria client di archiviazione per PHP](https://azure.microsoft.com/develop/php/)
* [Libreria client di archiviazione per Python](https://azure.microsoft.com/develop/python/)
* [Libreria client di archiviazione per Ruby](https://azure.microsoft.com/develop/ruby/)
* [Cmdlet di archiviazione per PowerShell](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)
* [Storage Commands for CLI 2.0](/cli/azure/storage) (Comandi di archiviazione per l'interfaccia della riga di comando 2.0)

## <a name="next-steps"></a>Passaggi successivi

* [Learn more about Blob storage (Altre informazioni sull'archiviazione BLOB)](../blobs/storage-blobs-introduction.md)
* [Learn more about Blob storage (Altre informazioni sull'archiviazione file)](../storage-files-introduction.md)
* [Learn more about Blob storage (Altre informazioni sull'archiviazione code)](../queues/storage-queues-introduction.md)

Per iniziare a usare Archiviazione di Azure, vedere [Create a storage account](storage-quickstart-create-account.md) (Creare un account di archiviazione).

<!-- FIGURE OUT WHAT TO DO WITH ALL THESE LINKS.

Azure Storage resources can be accessed by any language that can make HTTP/HTTPS requests. Additionally, Azure Storage offers programming libraries for several popular languages. These libraries simplify many aspects of working with Azure Storage by handling details such as synchronous and asynchronous invocation, batching of operations, exception management, automatic retries, operational behavior and so forth. Libraries are currently available for the following languages and platforms, with others in the pipeline:

### Azure Storage data services
* [Storage Services REST API](https://docs.microsoft.com/rest/api/storageservices/)
* [Storage Client Library for .NET](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [Storage Client Library for C++](https://github.com/Azure/azure-storage-cpp)
* [Storage Client Library for Java/Android](https://azure.microsoft.com/develop/java/)
* [Storage Client Library for Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Storage Client Library for PHP](https://azure.microsoft.com/develop/php/)
* [Storage Client Library for Python](https://azure.microsoft.com/develop/python/)
* [Storage Client Library for Ruby](https://azure.microsoft.com/develop/ruby/)
* [Storage Cmdlets for PowerShell](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)

### Azure Storage management services
* [Storage Resource Provider REST API Reference](/rest/api/storagerp/)
* [Storage Resource Provider Client Library for .NET](/dotnet/api/microsoft.azure.management.storage)
* [Storage Resource Provider Cmdlets for PowerShell 1.0](/powershell/module/azure.storage)
* [Storage Service Management REST API (Classic)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### Azure Storage data movement services
* [Storage Import/Export Service REST API](../storage-import-export-service.md)
* [Storage Data Movement Client Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### Tools and utilities
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) is a free, standalone app from Microsoft that enables you to work visually with Azure Storage data on Windows, macOS, and Linux.
* [Azure Storage Client Tools](../storage-explorers.md)
* [Azure SDKs and Tools](https://azure.microsoft.com/tools/)
* [Azure Storage Emulator](http://www.microsoft.com/download/details.aspx?id=43709)
* [Azure PowerShell](/powershell/azure/overview)
* [AzCopy Command-Line Utility](http://aka.ms/downloadazcopy)

## Next steps
To learn more about Azure Storage, explore these resources:

### Documentation
* [Azure Storage Documentation](https://azure.microsoft.com/documentation/services/storage/)
* [Create a storage account](../storage-create-storage-account.md)

-->

### <a name="for-administrators"></a>Per amministratori
* [Uso di Azure PowerShell con Archiviazione di Azure](storage-powershell-guide-full.md)
* [Uso dell'interfaccia della riga di comando di Azure con Archiviazione di Azure](../storage-azure-cli.md)

### <a name="for-net-developers"></a>Per sviluppatori .NET
* [Introduzione all'archiviazione BLOB di Azure con .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Sviluppare per File di Azure con .NET](../files/storage-dotnet-how-to-use-files.md)
* [Introduzione all'archiviazione tabelle di Azure con .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Introduzione all'archiviazione code di Azure con .NET](../storage-dotnet-how-to-use-queues.md)

### <a name="for-javaandroid-developers"></a>Per sviluppatori Java/Android
* [Come usare l'archiviazione BLOB da Java](../blobs/storage-java-how-to-use-blob-storage.md)
* [Eseguire lo sviluppo per File di Azure con Java](../files/storage-java-how-to-use-file-storage.md)
* [Come usare l'archiviazione tabelle da Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Come usare l'archiviazione di accodamento da Java](../storage-java-how-to-use-queue-storage.md)

### <a name="for-nodejs-developers"></a>Per sviluppatori Node.js
* [Come usare l'archiviazione BLOB da Node.js](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Come usare l'archiviazione tabelle da Node.js](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Come usare l'archiviazione di accodamento da Node.js](../storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>Per sviluppatori PHP
* [Come usare l'archiviazione BLOB da PHP](../blobs/storage-php-how-to-use-blobs.md)
* [Come usare l'archiviazione tabelle da PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Come usare l'archiviazione di accodamento da PHP](../storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>Per sviluppatori Ruby
* [Come usare l'archiviazione BLOB da Ruby](../blobs/storage-ruby-how-to-use-blob-storage.md)
* [Come usare l'archiviazione tabelle da Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)
* [Come usare l'archiviazione di accodamento da Ruby](../storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>Per sviluppatori Python
* [Come usare l'archiviazione BLOB da Python](../blobs/storage-python-how-to-use-blob-storage.md)
* [Eseguire lo sviluppo per File di Azure con Python](../files/storage-python-how-to-use-file-storage.md)
* [Come usare l'archiviazione tabelle da Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Come usare l'archiviazione di accodamento da Python](../storage-python-how-to-use-queue-storage.md)
