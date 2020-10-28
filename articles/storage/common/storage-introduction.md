---
title: 'Introduzione ad Archiviazione di Azure: archiviazione cloud in Azure | Microsoft Docs'
description: La piattaforma di archiviazione di Azure principale è la soluzione di archiviazione cloud Microsoft. Archiviazione di Azure offre uno spazio di archiviazione per oggetti dati sicuro, durevole e ridondante, a disponibilità e scalabilità elevate.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 7bb4cca6f58cb4ad0722c1407d2ef3062c3747e2
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92781958"
---
# <a name="introduction-to-the-core-azure-storage-services"></a>Introduzione ai servizi di archiviazione di Azure di base

La piattaforma di archiviazione di Azure è la soluzione di archiviazione cloud Microsoft per gli scenari di archiviazione dei dati moderni. I servizi di archiviazione Core offrono un archivio oggetti altamente scalabile per oggetti dati, spazio di archiviazione su disco per macchine virtuali di Azure, un servizio file system per il cloud, un archivio di messaggistica per la messaggistica affidabile e un archivio NoSQL. I servizi sono:

- **Durabilità e disponibilità elevata.** La ridondanza garantisce che i dati siano al sicuro in caso di errori hardware temporanei. Si può anche scegliere di replicare i dati tra data center o aree geografiche per una protezione aggiuntiva da catastrofi locali o calamità naturali. Con questo tipo di replica, i dati mantengono disponibilità elevata in caso di interruzioni impreviste.
- **Proteggere.** Tutti i dati scritti in un account di archiviazione di Azure vengono crittografati dal servizio. Archiviazione di Azure offre un controllo dettagliato su chi potrà accedere ai dati.
- **Scalabile.** La soluzione Archiviazione di Azure è progettata per offrire scalabilità elevata in modo da soddisfare le esigenze di archiviazione dati e di prestazioni delle attuali applicazioni.
- **Gestito.** Azure gestisce automaticamente la manutenzione dell'hardware, gli aggiornamenti e i problemi critici.
- **Accessibilità.** I dati in Archiviazione di Azure sono accessibili da ogni parte del mondo tramite HTTP o HTTPS. Microsoft fornisce librerie client per archiviazione di Azure in un'ampia gamma di linguaggi, tra cui .NET, Java, Node.js, Python, PHP, Ruby, go e altri ancora, oltre a un'API REST matura. Archiviazione di Azure supporta l'esecuzione di script in Azure PowerShell o nell'interfaccia della riga di comando di Azure. Il portale di Azure e Azure Storage Explorer, inoltre, offrono semplici soluzioni visive per l'uso dei dati.  

## <a name="core-storage-services"></a>Servizi di archiviazione Core

La piattaforma di archiviazione di Azure include i servizi dati seguenti:

- [BLOB di Azure](../blobs/storage-blobs-introduction.md): archivio oggetti a scalabilità elevata per dati di testo e binari. Include anche il supporto per Big Data Analytics tramite Data Lake Storage Gen2.
- [File di Azure](../files/storage-files-introduction.md): condivisioni file gestite per distribuzioni cloud o locali.
- [Code di Azure](../queues/storage-queues-introduction.md): archivio di messaggistica per una messaggistica affidabile tra i componenti delle applicazioni.
- [Tabelle di Azure](../tables/table-storage-overview.md): archivio NoSQL per l'archiviazione senza schema di dati strutturati.
- [Dischi di Azure](../../virtual-machines/managed-disks-overview.md): volumi di archiviazione a livello di blocco per le macchine virtuali di Azure.

Ogni servizio è accessibile tramite un account di archiviazione. Per iniziare, vedere [Creare un account di archiviazione](storage-account-create.md).

## <a name="example-scenarios"></a>Scenari di esempio

Nella tabella seguente vengono confrontati i file, i BLOB, i dischi, le code e le tabelle e vengono illustrati gli scenari di esempio per ognuno di essi.

| Funzionalità | Descrizione | Utilizzo |
|--------------|-------------|-------------|
| **File di Azure** |Offre condivisioni file cloud completamente gestite a cui è possibile accedere da qualsiasi luogo tramite il protocollo SMB (Server Message Block) standard di settore.<br><br>È possibile montare condivisioni file di Azure da distribuzioni cloud o locali di Windows, Linux e macOS. | Si vuole "sollevare e spostare" un'applicazione nel cloud che usa già le API file system native per condividere i dati tra l'IT e altre applicazioni in esecuzione in Azure.<br/><br/>Si desidera sostituire o integrare i file server o i dispositivi NAS locali.<br><br> Si intende archiviare gli strumenti di sviluppo e di debug a cui deve essere possibile accedere da molte macchine virtuali. |
| **BLOB di Azure** | Consente di archiviare e accedere ai dati non strutturati su vasta scala nei BLOB in blocchi.<br/><br/>Supporta anche [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) per le soluzioni aziendali di analisi di Big Data. | Si desidera che la propria applicazione supporti scenari di accesso casuale e tramite flusso.<br/><br/>Si desidera poter accedere ai dati dell'applicazione ovunque ci si trovi.<br/><br/>Si vuole compilare un Data Lake aziendale in Azure ed eseguire l'analisi dei Big Data. |
| **Dischi di Azure** | Consente di archiviare e accedere in modo permanente ai dati da un disco rigido virtuale collegato. | Si vuole spostare le applicazioni che usano API file system native per leggere e scrivere i dati nei dischi permanenti.<br/><br/>Si intende archiviare i dati a cui non è necessario accedere dall'esterno della macchina virtuale a cui è collegato il disco. |
| **Code di Azure** | Consente la coda asincrona dei messaggi tra i componenti dell'applicazione. | Si desidera separare i componenti dell'applicazione e utilizzare la messaggistica asincrona per comunicare tra di essi.<br><br>Per indicazioni su quando usare l'archiviazione di Accodamento e le code del bus di servizio, vedere Code di [archiviazione e code del bus di servizio-confronto e contrasto](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md). |
| **Tabelle di Azure** | Consente di archiviare dati NoSQL strutturati nel cloud, offrendo un archivio di chiavi/attributi con una progettazione senza schema. | Si desidera archiviare set di dati flessibili come i dati utente per le applicazioni Web, le rubriche, le informazioni sui dispositivi o altri tipi di metadati richiesti dal servizio. <br/><br/>Per indicazioni su quando usare l'archiviazione tabelle rispetto alla Azure Cosmos DB API Tabella, vedere [sviluppo con Azure Cosmos DB API tabella e archiviazione tabelle di Azure](../../cosmos-db/table-support.md). |

## <a name="blob-storage"></a>Archiviazione BLOB

L'archivio BLOB di Azure è la soluzione di archiviazione di oggetti Microsoft per il cloud. L'archivio BLOB è ottimizzato per l'archiviazione di enormi quantità di dati non strutturati, come dati di testo o binari.

È ideale per:

- Invio di immagini o documenti direttamente in un browser.
- Archiviazione di file per l'accesso distribuito.
- Streaming di audio e video.
- Archiviazione di dati per backup e ripristino, ripristino di emergenza e archiviazione.
- Archiviazione di dati a scopo di analisi da parte di un servizio locale o ospitato in Azure.

È possibile accedere agli oggetti nell'archivio BLOB da ogni parte del mondo tramite HTTP o HTTPS. Gli utenti o le applicazioni client possono accedere ai BLOB tramite URL, l'[API REST di Archiviazione di Azure](/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](/powershell/module/azure.storage), l'[interfaccia della riga di comando di Azure](/cli/azure/storage) oppure una libreria client di Archiviazione di Azure. Le librerie client di archiviazione sono disponibili per molti linguaggi, tra cui [.NET](/dotnet/api/overview/azure/storage), [Java](/java/api/overview/azure/storage), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) e [Ruby](https://azure.github.io/azure-storage-ruby).

Per altre informazioni sull'archiviazione BLOB, vedere [Introduzione all'archiviazione BLOB](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>File di Azure

[File di Azure](../files/storage-files-introduction.md) consente di configurare condivisioni file di rete a disponibilità elevata a cui è possibile accedere tramite il protocollo SMB (Server Message Block) standard. Più VM possono quindi condividere gli stessi file con accesso sia in lettura che in scrittura. È possibile leggere i file anche usando l'interfaccia REST o le librerie client di archiviazione.

Una delle differenze tra File di Azure e i file in una condivisione file aziendale è la possibilità di accedere ai file da qualsiasi parte del mondo usando un URL che punta al file e include un token di firma di accesso condiviso. È possibile generare token di firma di accesso condiviso, che consentono l'accesso specifico a un asset privato per un periodo di tempo specifico.

Le condivisioni file possono essere usate per molti scenari comuni:

- Molte applicazioni locali usano condivisioni file. Questa funzionalità semplifica la migrazione ad Azure di tali applicazioni che condividono i dati. Se si monta la condivisione file nella stessa lettera di unità usata dall'applicazione locale, la parte dell'applicazione che accede alla condivisione file funzionerà con modifiche tutt'al più minime.

- È possibile archiviare i file di configurazione in una condivisione di file e accedervi da più macchine virtuali. Gli strumenti e le utilità usati da più sviluppatori in un gruppo possono essere archiviati in una condivisione file, assicurandosi che tutti riescano a trovarli e che usino la stessa versione.

- I log delle risorse, le metriche e i dump di arresto anomalo del sistema sono solo tre esempi di dati che possono essere scritti in una condivisione file ed elaborati o analizzati in un secondo momento.

Per altre informazioni su File di Azure, vedere [Introduzione a File di Azure](../files/storage-files-introduction.md).

Alcune funzionalità SMB non sono applicabili al cloud. Per altre informazioni, vedere [Features not supported by the Azure File service](/rest/api/storageservices/features-not-supported-by-the-azure-file-service) (Funzionalità non supportate da Servizio file di Azure).

## <a name="queue-storage"></a>Archiviazione code

Il Servizio di accodamento di Azure viene usato per archiviare e recuperare i messaggi. La dimensione massima dei messaggi nella coda può essere di 64 KB e una coda può contenere milioni di messaggi. Le code vengono in genere usate per archiviare elenchi di messaggi da elaborare in modo asincrono.

Si supponga ad esempio che si voglia consentire ai clienti di caricare immagini e che si voglia creare un'anteprima per ogni immagine. È possibile creare le anteprime lasciando in attesa il cliente durante il caricamento delle immagini oppure usare una coda. Quando il cliente termina il caricamento, scrive un messaggio nella coda. Funzioni di Azure recupera quindi il messaggio dalla coda e crea le anteprime. Ogni parte di questa elaborazione può essere ridimensionata separatamente, offrendo un maggiore controllo per l'ottimizzazione ai fini dell'utilizzo.

Per altre informazioni sulle code di Azure, vedere [Introduzione alle code](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Archiviazione tabelle

L'archiviazione tabelle di Azure è ora inclusa in Azure Cosmos DB. Per la documentazione sull'archiviazione tabelle di Azure, vedere [Panoramica di Archiviazione tabelle di Azure](../tables/table-storage-overview.md). Oltre al servizio di archiviazione tabelle di Azure esistente, è disponibile una nuova API Tabelle di Azure Cosmos DB che offre tabelle con ottimizzazione per la velocità effettiva, distribuzione globale e indici secondari automatici. Per ulteriori informazioni e per provare la nuova esperienza Premium, vedere [Azure Cosmos DB API tabella](../../cosmos-db/table-introduction.md).

Per altre informazioni sull'archivio tabelle, vedere [Panoramica di Archiviazione tabelle di Azure](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Archiviazione su disco

Un disco gestito di Azure è un disco rigido virtuale. È possibile considerarlo come un disco fisico in un server locale, ma virtualizzato. I dischi gestiti di Azure vengono archiviati come BLOB di pagine, ovvero un oggetto di archiviazione IO casuale in Azure. Viene chiamato un disco gestito ' gestito ' perché è un'astrazione su BLOB di pagine, contenitori BLOB e account di archiviazione di Azure. Con i dischi gestiti, è sufficiente effettuare il provisioning del disco e Azure si occupa di tutto il resto.

Per altre informazioni su Managed disks, vedere [Introduzione a Managed Disks di Azure](../../virtual-machines/managed-disks-overview.md).

## <a name="types-of-storage-accounts"></a>Tipi di account di archiviazione

Archiviazione di Azure offre diversi tipi di account di archiviazione. Ogni tipo supporta caratteristiche diverse e ha uno specifico modello di prezzi. Per altre informazioni sui tipi di account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](storage-account-overview.md).

## <a name="secure-access-to-storage-accounts"></a>Proteggere l'accesso agli account di archiviazione

Ogni richiesta ad archiviazione di Azure deve essere autorizzata. Archiviazione di Azure supporta i seguenti metodi di autorizzazione:

- **Integrazione di Azure Active Directory (Azure AD) per i dati di BLOB e di Accodamento.** Archiviazione di Azure supporta l'autenticazione e l'autorizzazione con Azure AD per i servizi BLOB e di accodamento tramite il controllo degli accessi in base al ruolo di Azure (RBAC di Azure). L'autorizzazione delle richieste con Azure AD è consigliata per una maggiore sicurezza e semplicità d'uso. Per altre informazioni, vedere [autorizzare l'accesso a BLOB e code di Azure usando Azure Active Directory](storage-auth-aad.md).
- **Azure AD autorizzazione su SMB per File di Azure.** File di Azure supporta l'autorizzazione basata sull'identità su SMB (Server Message Block) tramite Azure Active Directory Domain Services (Azure AD DS) o Active Directory Domain Services locali (anteprima). Le macchine virtuali di Windows appartenenti a un dominio possono accedere alle condivisioni file di Azure usando le credenziali Azure AD. Per ulteriori informazioni, vedere la [Panoramica del supporto per l'autenticazione file di Azure basata sull'identità per l'accesso SMB](../files/storage-files-active-directory-overview.md) e [la pianificazione di una distribuzione di file di Azure](../files/storage-files-planning.md#identity).
- **Autorizzazione con chiave condivisa.** I servizi BLOB del servizio di archiviazione di Azure, file, coda e tabella supportano l'autorizzazione con la chiave condivisa. Un client che usa l'autorizzazione della chiave condivisa passa un'intestazione con ogni richiesta firmata usando la chiave di accesso dell'account di archiviazione. Per altre informazioni, vedere [Authorize with Shared Key](/rest/api/storageservices/authorize-with-shared-key) (Autorizzazione con chiave condivisa).
- **Autorizzazione mediante le firme di accesso condiviso (SAS).** Una firma di accesso condiviso è una stringa contenente un token di sicurezza che può essere aggiunto all'URI per una risorsa di archiviazione. Il token di sicurezza incapsula vincoli quali le autorizzazioni e l'intervallo di accesso. Per altre informazioni, vedere [uso delle firme di accesso condiviso (SAS)](storage-sas-overview.md).
- **Accesso anonimo a contenitori e BLOB.** Un contenitore e i relativi BLOB possono essere disponibili pubblicamente. Quando si specifica che un contenitore o un BLOB è pubblico, chiunque può leggerlo in modo anonimo; non è necessaria alcuna autenticazione. Per altre informazioni, vedere [Gestire l'accesso in lettura anonimo a contenitori e BLOB](../blobs/anonymous-read-access-configure.md).

## <a name="encryption"></a>Crittografia

Sono disponibili due tipi di crittografia di base per i servizi di archiviazione di base. Per altre informazioni sulla sicurezza e sulla crittografia, vedere [Guida alla sicurezza di Archiviazione di Azure](../blobs/security-recommendations.md).

### <a name="encryption-at-rest"></a>Crittografia di dati inattivi

La crittografia di archiviazione di Azure protegge e salvaguarda i dati per soddisfare gli impegni di sicurezza e conformità dell'organizzazione. Archiviazione di Azure crittografa automaticamente tutti i dati prima di renderli permanente nell'account di archiviazione e li decrittografa prima del recupero. I processi di crittografia, decrittografia e gestione delle chiavi sono trasparenti per gli utenti. I clienti possono anche scegliere di gestire le proprie chiavi usando Azure Key Vault. Per altre informazioni, vedere [Crittografia di Archiviazione di Azure per dati inattivi](storage-service-encryption.md).

### <a name="client-side-encryption"></a>crittografia lato client

Le librerie client di archiviazione di Azure forniscono metodi per la crittografia dei dati dalla libreria client prima dell'invio in rete e la decrittografia della risposta. I dati crittografati tramite la crittografia lato client vengono anche crittografati a riposo da archiviazione di Azure. Per altre informazioni sulla crittografia lato client, vedere [crittografia lato client con .NET per archiviazione di Azure](storage-client-side-encryption.md).

## <a name="redundancy"></a>Ridondanza

Per assicurarsi che i dati siano durevoli, archiviazione di Azure archivia più copie dei dati. Quando si configura l'account di archiviazione, si seleziona un'opzione di ridondanza. Per altre informazioni, vedere [Ridondanza di Archiviazione di Azure](./storage-redundancy.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json).

## <a name="transfer-data-to-and-from-azure-storage"></a>Trasferire dati da e verso archiviazione di Azure

Per spostare i dati all'interno o all'esterno di Archiviazione di Azure, sono disponibili diverse opzioni. L'opzione da scegliere dipende dalle dimensioni del set di dati e dalla larghezza di banda della rete. Per altre informazioni, vedere [Scegliere una soluzione di Azure per il trasferimento dei dati](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Prezzi

Quando si decide la modalità di archiviazione e di accesso ai dati, è consigliabile valutare anche i costi coinvolti. Per altre informazioni, vedere [prezzi di archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="storage-apis-libraries-and-tools"></a>API di archiviazione, librerie e strumenti

È possibile accedere alle risorse in un account di archiviazione da qualsiasi linguaggio in grado di effettuare richieste HTTP/HTTPS. Inoltre, i servizi di archiviazione di Azure di base offrono librerie di programmazione per diversi linguaggi comuni. Tali librerie semplificano molti aspetti dell'utilizzo di Archiviazione di Azure gestendo dettagli come la chiamata sincrona e asincrona, l'esecuzione delle operazioni in batch, la gestione delle eccezioni, la ripetizione automatica dei tentativi, il comportamento operativo e così via. Le librerie attualmente disponibili riguardano i linguaggi e le piattaforme seguenti, ma presto ne saranno disponibili altre:

### <a name="azure-storage-data-api-and-library-references"></a>Informazioni di riferimento su librerie e API di dati di Archiviazione di Azure

- [API REST di Archiviazione di Azure](/rest/api/storageservices/)
- [Libreria client di archiviazione di Azure per .NET](/dotnet/api/overview/azure/storage)
- [Libreria client di archiviazione di Azure per Java/Android](/java/api/overview/azure/storage)
- [Libreria client di archiviazione di Azure per Node.js](/javascript/api/overview/azure/storage-overview)
- [Libreria client di archiviazione di Azure per Python](https://github.com/Azure/azure-storage-python)
- [Libreria client di archiviazione di Azure per PHP](https://github.com/Azure/azure-storage-php)
- [Libreria client di archiviazione di Azure per Ruby](https://github.com/Azure/azure-storage-ruby)
- [Libreria client di archiviazione di Azure per C++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Informazioni di riferimento su librerie e API di gestione di Archiviazione di Azure

- [API REST del provider di risorse di archiviazione](/rest/api/storagerp/)
- [Libreria client del provider di risorse di archiviazione per .NET](/dotnet/api/overview/azure/storage/management)
- [API REST di gestione del servizio di archiviazione (classico)](/previous-versions/azure/reference/ee460790(v=azure.100))

### <a name="azure-storage-data-movement-api-and-library-references"></a>Informazioni di riferimento su librerie e API di spostamento dati di Archiviazione di Azure

- [API REST del servizio di importazione/esportazione dell'archiviazione](/rest/api/storageimportexport/)
- [Libreria client di spostamento dei dati di archiviazione per .NET](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>Strumenti e utilità

- [Cmdlet di Azure PowerShell per Archiviazione](/powershell/module/az.storage)
- [Cmdlet dell'interfaccia della riga di comando di Azure per Archiviazione](/cli/azure/storage)
- [Utilità da riga di comando di AzCopy](https://aka.ms/downloadazcopy)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) è un'app autonoma gratuita di Microsoft che consente di interagire visivamente con i dati di Archiviazione di Azure in Windows, macOS e Linux.
- [Modelli di Azure Resource Manager per archiviazione di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare i servizi di archiviazione di Azure di base, vedere [creare un account di archiviazione](storage-account-create.md).