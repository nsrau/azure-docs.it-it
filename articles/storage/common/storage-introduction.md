---
title: 'Introduzione ad Archiviazione di Azure: archiviazione cloud in Azure | Microsoft Docs'
description: La piattaforma di archiviazione di Azure di base è la soluzione di archiviazione cloud di Microsoft.The core Azure Storage platform is Microsoft's cloud storage solution. Archiviazione di Azure offre uno spazio di archiviazione per oggetti dati sicuro, durevole e ridondante, a disponibilità e scalabilità elevate.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 1cc047ee60cf8287f32a42b878371c5fc9680b7a
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985746"
---
# <a name="introduction-to-the-core-azure-storage-services"></a>Introduzione ai servizi di archiviazione di Azure di baseIntroduction to the core Azure Storage services

La piattaforma di archiviazione di Azure è la soluzione di archiviazione cloud di Microsoft per i moderni scenari di archiviazione dei dati. I servizi di archiviazione di base offrono un archivio oggetti scalabile enormemente per gli oggetti dati, l'archiviazione su disco per le macchine virtuali (VM) di Azure, un servizio di file system per il cloud, un archivio di messaggistica per la messaggistica affidabile e un archivio NoSQL.Core storage services offer a massiccily scalable object store for data objects, disk storage for Azure virtual machines (VMs), a file system service for the cloud, a messaging store for reliable messaging, and a NoSQL store. I servizi sono:

- **Durabilità e disponibilità elevata.** La ridondanza garantisce che i dati siano al sicuro in caso di errori hardware temporanei. Si può anche scegliere di replicare i dati tra data center o aree geografiche per una protezione aggiuntiva da catastrofi locali o calamità naturali. Con questo tipo di replica, i dati mantengono disponibilità elevata in caso di interruzioni impreviste.
- **Sicuro.** Tutti i dati scritti in un account di archiviazione di Azure vengono crittografati dal servizio. Archiviazione di Azure offre un controllo dettagliato su chi potrà accedere ai dati.
- **Scalabilità.** La soluzione Archiviazione di Azure è progettata per offrire scalabilità elevata in modo da soddisfare le esigenze di archiviazione dati e di prestazioni delle attuali applicazioni.
- **Soluzione gestita.** Azure gestisce automaticamente la manutenzione hardware, gli aggiornamenti e i problemi critici.
- **Accessibilità.** I dati in Archiviazione di Azure sono accessibili da ogni parte del mondo tramite HTTP o HTTPS. Microsoft fornisce librerie client per Archiviazione di Azure in diversi linguaggi, tra cui .NET, Java, Node.js, Python, PHP, Ruby, Go e altri, nonché un'API REST matura. Archiviazione di Azure supporta l'esecuzione di script in Azure PowerShell o nell'interfaccia della riga di comando di Azure. Il portale di Azure e Azure Storage Explorer, inoltre, offrono semplici soluzioni visive per l'uso dei dati.  

## <a name="core-storage-services"></a>Servizi di archiviazione di base

La piattaforma di Archiviazione di Azure include i servizi dati seguenti:The Azure Storage platform includes the following data services:

- [BLOB di Azure](../blobs/storage-blobs-introduction.md): archivio oggetti a scalabilità elevata per dati di testo e binari. Include anche il supporto per l'analisi dei Big Data tramite Data Lake Storage Gen2.
- [File di Azure](../files/storage-files-introduction.md): condivisioni file gestite per distribuzioni cloud o locali.
- [Code di Azure](../queues/storage-queues-introduction.md): archivio di messaggistica per una messaggistica affidabile tra i componenti delle applicazioni.
- [Tabelle di Azure](../tables/table-storage-overview.md): archivio NoSQL per l'archiviazione senza schema di dati strutturati.
- [Dischi di Azure:](../../virtual-machines/windows/managed-disks-overview.md)volumi di archiviazione a livello di blocco per le macchine virtuali di Azure.Azure Disks: Block-level storage volumes for Azure VMs.

Ogni servizio è accessibile tramite un account di archiviazione. Per iniziare, vedere [Creare un account di archiviazione](storage-account-create.md).

## <a name="example-scenarios"></a>Scenari di esempio

Nella tabella seguente vengono confrontati file, BLOB, dischi, code e tabelle e vengono illustrati scenari di esempio per ognuno di essi.

| Funzionalità | Descrizione | Utilizzo |
|--------------|-------------|-------------|
| **File di Azure** |Offre condivisioni di file cloud completamente gestite a cui è possibile accedere da qualsiasi luogo tramite il protocollo SMB (Server Message Block) standard del settore.<br><br>È possibile montare condivisioni file di Azure da distribuzioni cloud o locali di Windows, Linux e macOS.You can mount Azure file shares from cloud or on-premises deployments of Windows, Linux, and macOS. | Si vuole "sollevare e spostare" un'applicazione nel cloud che usa già le API del file system nativo per condividere i dati tra l'utente e altre applicazioni in esecuzione in Azure.You want to "lift and shift" an application to the cloud that already uses the native file system APIs to share data between it and other applications running in Azure.<br/><br/>Si desidera sostituire o integrare file server locali o dispositivi NAS.<br><br> Si intende archiviare gli strumenti di sviluppo e di debug a cui deve essere possibile accedere da molte macchine virtuali. |
| **BLOB di Azure** | Consente l'archiviazione e l'accesso ai dati non strutturati su larga scala in BLOB in blocchi.<br/><br/>Supporta anche [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) per le soluzioni aziendali di analisi di Big Data. | Si desidera che la propria applicazione supporti scenari di accesso casuale e tramite flusso.<br/><br/>Si desidera poter accedere ai dati dell'applicazione ovunque ci si trovi.<br/><br/>Si vuole compilare un Data Lake aziendale in Azure ed eseguire l'analisi dei Big Data. |
| **Dischi di Azure** | Consente l'archiviazione permanente dei dati e l'accesso da un disco rigido virtuale collegato. | Si desidera "sollevare e spostare" le applicazioni che utilizzano le API del file system nativo per leggere e scrivere dati su dischi persistenti.<br/><br/>Si intende archiviare i dati a cui non è necessario accedere dall'esterno della macchina virtuale a cui è collegato il disco. |
| **Code di Azure** | Consente la coda di messaggi asincroni tra i componenti dell'applicazione. | Si desidera separare i componenti dell'applicazione e utilizzare la messaggistica asincrona per comunicare tra di essi.<br><br>Per indicazioni su quando usare le code di archiviazione delle code e del bus di servizio, vedere [Code di archiviazione e code del bus di servizio, confrontate e con contrasto](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted). |
| **Tabelle di Azure** | Consentono di archiviare dati NoSQL strutturati nel cloud, fornendo un archivio chiave/attributo con una progettazione senza schema. | Si desidera archiviare set di dati flessibili come i dati utente per applicazioni Web, rubriche, informazioni sui dispositivi o altri tipi di metadati necessari dal servizio. <br/><br/>Per indicazioni su quando usare l'archiviazione tabelle e l'API tabella di Azure Cosmos, vedere [Sviluppo con l'API tabella di Azure Cosmos DB e Archiviazione tabelle](../../cosmos-db/table-support.md)di Azure.For guidance around when to use Table storage versus the Azure Cosmos DB Table API, see Developing with Azure Cosmos DB Table API and Azure Table storage . |

## <a name="blob-storage"></a>Archiviazione BLOB

L'archivio BLOB di Azure è la soluzione di archiviazione di oggetti Microsoft per il cloud. L'archivio BLOB è ottimizzato per l'archiviazione di enormi quantità di dati non strutturati, come dati di testo o binari.

L'archivio BLOB è ideale per le operazioni seguenti:

- Invio di immagini o documenti direttamente in un browser.
- Archiviazione di file per l'accesso distribuito.
- Flussi audio e video.
- Archiviazione di dati per backup e ripristino, ripristino di emergenza e archiviazione.
- Archiviazione di dati a scopo di analisi da parte di un servizio locale o ospitato in Azure.

È possibile accedere agli oggetti nell'archivio BLOB da ogni parte del mondo tramite HTTP o HTTPS. Gli utenti o le applicazioni client possono accedere ai BLOB tramite URL, l'[API REST di Archiviazione di Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/storage) oppure una libreria client di Archiviazione di Azure. Le librerie client di archiviazione sono disponibili per molti linguaggi, tra cui [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet), [Java](https://docs.microsoft.com/java/api/overview/azure/storage), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) e [Ruby](https://azure.github.io/azure-storage-ruby).

Per altre informazioni sull'archiviazione BLOB, vedere [Introduzione all'archiviazione BLOB](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>File di Azure

[File di Azure](../files/storage-files-introduction.md) consente di configurare condivisioni file di rete a disponibilità elevata a cui è possibile accedere usando il protocollo Server Message Block (SMB) standard. Più VM possono quindi condividere gli stessi file con accesso sia in lettura che in scrittura. È possibile leggere i file usando l'interfaccia REST o le librerie dei client di archiviazione.

Una delle differenze tra File di Azure e i file in una condivisione file aziendale è la possibilità di accedere ai file da qualsiasi parte del mondo usando un URL che punta al file e include un token di firma di accesso condiviso. È possibile generare token di firma di accesso condiviso, che consentono l'accesso specifico a un asset privato per un periodo di tempo specifico.

Le condivisioni file possono essere usate per molti scenari comuni:

- Molte applicazioni locali usano condivisioni file. Questa funzionalità semplifica la migrazione ad Azure di tali applicazioni che condividono i dati. Se si monta la condivisione file nella stessa lettera di unità usata dall'applicazione locale, la parte dell'applicazione che accede alla condivisione file funzionerà con modifiche tutt'al più minime.

- È possibile archiviare i file di configurazione in una condivisione di file e accedervi da più macchine virtuali. Gli strumenti e le utilità usati da più sviluppatori in un gruppo possono essere archiviati in una condivisione file, assicurandosi che tutti riescano a trovarli e che usino la stessa versione.

- Log di diagnostica, metriche e dump di arresto anomalo del sistema sono solo tre esempi di dati che possono essere scritti in una condivisione file ed elaborati o analizzati in un secondo momento.

Per altre informazioni su File di Azure, vedere [Introduzione a File di Azure](../files/storage-files-introduction.md).

Alcune funzionalità SMB non sono applicabili al cloud. Per altre informazioni, vedere [Features not supported by the Azure File service](/rest/api/storageservices/features-not-supported-by-the-azure-file-service) (Funzionalità non supportate da Servizio file di Azure).

## <a name="queue-storage"></a>Archiviazione code

Il servizio di accodamento di Azure viene usato per archiviare e recuperare i messaggi. La dimensione massima dei messaggi nella coda può essere di 64 KB e una coda può contenere milioni di messaggi. Le code vengono in genere usate per archiviare elenchi di messaggi da elaborare in modo asincrono.

Si supponga ad esempio che si voglia consentire ai clienti di caricare immagini e che si voglia creare un'anteprima per ogni immagine. È possibile creare le anteprime lasciando in attesa il cliente durante il caricamento delle immagini oppure usare una coda. Quando il cliente termina il caricamento, scrivere un messaggio nella coda. Funzioni di Azure recupera quindi il messaggio dalla coda e crea le anteprime. Ogni parte di questa elaborazione può essere ridimensionata separatamente, offrendo un maggiore controllo per l'ottimizzazione ai fini dell'utilizzo.

Per altre informazioni sulle code di Azure, vedere [Introduzione alle code](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Archiviazione tabelle

L'archiviazione tabelle di Azure è ora inclusa in Azure Cosmos DB. Per la documentazione sull'archiviazione tabelle di Azure, vedere [Panoramica di Archiviazione tabelle di Azure](../tables/table-storage-overview.md). Oltre al servizio di archiviazione tabelle di Azure esistente, è disponibile una nuova API Tabelle di Azure Cosmos DB che offre tabelle con ottimizzazione per la velocità effettiva, distribuzione globale e indici secondari automatici. Per altre informazioni e provare la nuova esperienza Premium, vedere [API tabella DB di Azure Cosmos](https://aka.ms/premiumtables).

Per altre informazioni sull'archivio tabelle, vedere [Panoramica di Archiviazione tabelle di Azure](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Archiviazione su disco

Un disco gestito di Azure è un disco rigido virtuale. È possibile considerarlo come un disco fisico in un server locale, ma virtualizzato. I dischi gestiti da Azure vengono archiviati come BLOB di pagine, che sono un oggetto di archiviazione I/O casuale in Azure.Azure-managed disks are stored as page blobs, which are a random IO storage object in Azure. Viene chiamato un disco gestito 'gestito' perché è un'astrazione su BLOB di pagine, contenitori BLOB e account di archiviazione di Azure.We call a managed disk 'managed' because it is an abstraction over page blobs, blob containers, and Azure storage accounts. Con i dischi gestiti, è sufficiente effettuare il provisioning del disco e Azure si occupa di tutto il resto.

Per altre informazioni sui dischi gestiti, vedere [Introduzione ai dischi gestiti](../../virtual-machines/windows/managed-disks-overview.md)di Azure.For more information about managed disks, see Introduction to Azure managed disks .

## <a name="types-of-storage-accounts"></a>Tipi di account di archiviazione

Archiviazione di Azure offre diversi tipi di account di archiviazione. Ogni tipo supporta caratteristiche diverse e ha uno specifico modello di prezzi. Per altre informazioni sui tipi di account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](storage-account-overview.md).

## <a name="secure-access-to-storage-accounts"></a>Accesso sicuro agli account di archiviazioneSecure access to storage accounts

Ogni richiesta ad Archiviazione di Azure deve essere autorizzata. Archiviazione di Azure supporta i metodi di autorizzazione seguenti:Azure Storage supports the following authorization methods:

- **Integrazione di Azure Active Directory (Azure AD) per i dati BLOB e coda.** Archiviazione di Azure supporta l'autenticazione e l'autorizzazione con Azure AD per i servizi BLOB e coda tramite il controllo degli accessi in base al ruolo. L'autorizzazione delle richieste con Azure AD è consigliata per una maggiore sicurezza e facilità d'uso. Per altre informazioni, vedere [Autorizzare l'accesso a BLOB e code](storage-auth-aad.md)di Azure usando Azure Active Directory.For more information, see Authorize access to Azure blobs and queues using Azure Active Directory.
- **Azure AD authorization over SMB for Azure Files.** File di Azure supporta l'autorizzazione basata sull'identità tramite SMB (Server Message Block) tramite Servizi di dominio Azure Active Directory (Azure AD DS) o Servizi di dominio Active Directory locale (anteprima). Le macchine virtuali Windows aggiunte al dominio possono accedere alle condivisioni file di Azure usando le credenziali di Azure AD. Per altre informazioni, vedere [Panoramica del supporto dell'autenticazione basata sull'identità di File di Azure per l'accesso SMB](../files/storage-files-active-directory-overview.md) e Pianificazione di una distribuzione di File di Azure.For more information, see Overview of Azure Files identity-based authentication support for SMB access and [Planning for an Azure Files deployment.](../files/storage-files-planning.md#identity)
- **Autorizzazione con chiave condivisa.** I servizi Blob, File, Coda e Tabella di Archiviazione di Azure supportano l'autorizzazione con chiave condivisa. Un client che usa l'autorizzazione della chiave condivisa passa un'intestazione con ogni richiesta firmata usando la chiave di accesso dell'account di archiviazione. Per altre informazioni, vedere [Authorize with Shared Key](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) (Autorizzazione con chiave condivisa).
- **Autorizzazione tramite firme di accesso condiviso.** Una firma di accesso condiviso è una stringa contenente un token di sicurezza che può essere aggiunto all'URI per una risorsa di archiviazione. Il token di sicurezza incapsula vincoli quali le autorizzazioni e l'intervallo di accesso. Per ulteriori informazioni, vedere [Utilizzo delle firme](storage-sas-overview.md)di accesso condiviso.
- **Accesso anonimo a contenitori e BLOB.** Un contenitore e i relativi BLOB possono essere disponibili pubblicamente. Quando si specifica che un contenitore o un BLOB è pubblico, chiunque può leggerlo in modo anonimo. non è richiesta alcuna autenticazione. Per altre informazioni, vedere [Gestire l'accesso in lettura anonimo a contenitori e BLOB](../blobs/storage-manage-access-to-resources.md).

## <a name="encryption"></a>Crittografia

Sono disponibili due tipi di crittografia di base per i servizi di archiviazione di base. Per altre informazioni sulla sicurezza e sulla crittografia, vedere [Guida alla sicurezza di Archiviazione di Azure](../blobs/security-recommendations.md).

### <a name="encryption-at-rest"></a>Crittografia di dati inattivi

La crittografia di Archiviazione di Azure protegge e salvaguarda i dati per soddisfare gli impegni di conformità e sicurezza dell'organizzazione. Archiviazione di Azure crittografa automaticamente tutti i dati prima della persistenza nell'account di archiviazione e li decrittografa prima del recupero. I processi di crittografia, decrittografia e gestione delle chiavi sono trasparenti per gli utenti. I clienti possono anche scegliere di gestire le proprie chiavi usando l'insieme di credenziali delle chiavi di Azure.Customers can also choose to manage their own keys using Azure Key Vault. Per altre informazioni, vedere Crittografia di Archiviazione di Azure per i [dati inattivi](storage-service-encryption.md).

### <a name="client-side-encryption"></a>crittografia lato client

Le librerie client di Archiviazione di Azure forniscono metodi per crittografare i dati dalla libreria client prima di inviarli in rete e decrittografare la risposta. I dati crittografati tramite la crittografia lato client vengono crittografati anche inattivi da Archiviazione di Azure.Data encrypted via client-side encryption is also encrypted at restby by Azure Storage. Per altre informazioni sulla crittografia lato client, vedere Crittografia lato client con .NET per Archiviazione di Azure.For more information about [client-side encryption, see Client-side encryption with .NET for Azure Storage](storage-client-side-encryption.md).

## <a name="redundancy"></a>Ridondanza

Per garantire che i dati siano durevoli, Archiviazione di Azure archivia più copie dei dati. Quando si configura l'account di archiviazione, si seleziona un'opzione di ridondanza. Per altre informazioni, vedere [Ridondanza di Archiviazione di Azure](/storage-redundancy?toc=/azure/storage/blobs/toc.json).

## <a name="transfer-data-to-and-from-azure-storage"></a>Trasferire dati da e verso Archiviazione di AzureTransfer data from and from Azure Storage

Per spostare i dati all'interno o all'esterno di Archiviazione di Azure, sono disponibili diverse opzioni. L'opzione da scegliere dipende dalle dimensioni del set di dati e dalla larghezza di banda della rete. Per altre informazioni, vedere [Scegliere una soluzione di Azure per il trasferimento dei dati](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Prezzi

Quando si decide la modalità di archiviazione e di accesso ai dati, è consigliabile valutare anche i costi coinvolti. Per altre informazioni, vedere Prezzi di Archiviazione di Azure .For [more information,](https://azure.microsoft.com/pricing/details/storage/)see Azure Storage pricing .

## <a name="storage-apis-libraries-and-tools"></a>API di archiviazione, librerie e strumenti

È possibile accedere alle risorse in un account di archiviazione da qualsiasi lingua che può effettuare richieste HTTP/HTTPS. Inoltre, i servizi di archiviazione di Azure di base offrono librerie di programmazione per diversi linguaggi comuni. Tali librerie semplificano molti aspetti dell'utilizzo di Archiviazione di Azure gestendo dettagli come la chiamata sincrona e asincrona, l'esecuzione delle operazioni in batch, la gestione delle eccezioni, la ripetizione automatica dei tentativi, il comportamento operativo e così via. Le librerie attualmente disponibili riguardano i linguaggi e le piattaforme seguenti, ma presto ne saranno disponibili altre:

### <a name="azure-storage-data-api-and-library-references"></a>Informazioni di riferimento su librerie e API di dati di Archiviazione di Azure

- [API REST di Archiviazione di AzureAzure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/)
- [Libreria client di Archiviazione di Azure per .NETAzure Storage client library for .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
- [Azure Storage client library for Java/Android](https://docs.microsoft.com/java/api/overview/azure/storage)
- [Azure Storage client library for Node.js](https://docs.microsoft.com/javascript/api/overview/azure/storage-overview)
- [Azure Storage client library for Python](https://github.com/Azure/azure-storage-python)
- [Azure Storage client library for PHP](https://github.com/Azure/azure-storage-php)
- [Azure Storage client library for Ruby](https://github.com/Azure/azure-storage-ruby)
- [Libreria client di Archiviazione di Azure per C](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Informazioni di riferimento su librerie e API di gestione di Archiviazione di Azure

- [API REST del provider di risorse di archiviazione](https://docs.microsoft.com/rest/api/storagerp/)
- [Libreria client del provider di risorse di archiviazione per .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
- [API REST di gestione del servizio di archiviazione (classico)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>Informazioni di riferimento su librerie e API di spostamento dati di Archiviazione di Azure

- [API REST del servizio di importazione/esportazione dell'archiviazione](https://docs.microsoft.com/rest/api/storageimportexport/)
- [Libreria client di spostamento dei dati di archiviazione per .NET](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>Strumenti e utilità

- [Cmdlet di Azure PowerShell per Archiviazione](https://docs.microsoft.com/powershell/module/az.storage)
- [Cmdlet dell'interfaccia della riga di comando di Azure per Archiviazione](https://docs.microsoft.com/cli/azure/storage)
- [Utilità da riga di comando di AzCopy](https://aka.ms/downloadazcopy)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) è un'app autonoma gratuita di Microsoft che consente di interagire visivamente con i dati di Archiviazione di Azure in Windows, macOS e Linux.
- [Strumenti client di Archiviazione di AzureAzure Storage Client Tools](../storage-explorers.md)
- [Strumenti di sviluppo di Azure](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a utilizzare i servizi di archiviazione di Azure di base, vedere [Creare un account di archiviazione.](storage-account-create.md)
