---
title: Panoramica dell'account di archiviazione
titleSuffix: Azure Storage
description: Informazioni sulle opzioni per la creazione e l'uso di un account di archiviazione di Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 3694a1d04ec25f44cfcf9395bdd148e2fd3c0d9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371555"
---
# <a name="storage-account-overview"></a>Panoramica dell'account di archiviazione

Un account di archiviazione di Azure contiene tutti gli oggetti dati di Archiviazione di Azure: BLOB, file, code, tabelle e dischi. L'account di archiviazione fornisce uno spazio dei nomi univoco per i dati di Archiviazione di Azure accessibile da qualsiasi parte del mondo tramite HTTP o HTTPS. I dati nell'account di archiviazione di Azure sono durevoli e altamente disponibili, sicuri e scalabili in modo massiccio.

Per informazioni su come creare un account di archiviazione di Azure, vedere [Creare un account di archiviazione](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Tipi di account di archiviazione

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>Account per utilizzo generico v2

Gli account di archiviazione per utilizzo generico v2 supportano le funzionalità di archiviazione di Azure più recenti e incorporano tutte le funzionalità degli account di archiviazione BLOB e per utilizzo generico v1. Gli account per utilizzo generico v2 offrono i prezzi per gigabyte più bassi per Archiviazione di Azure, oltre a prezzi per transazione competitivi a livello di settore. Gli account di archiviazione per utilizzo generico v2 supportano i servizi di archiviazione di Azure seguenti:

- BLOB (tutti i tipi: blocchi, accodamento, pagina)
- Data Lake Gen2
- File
- Dischi
- Code
- Tabelle

> [!NOTE]
> Per la maggior parte degli scenari è consigliabile usare un account di archiviazione per utilizzo generico v2. È possibile eseguire facilmente l'aggiornamento di un account di archiviazione per utilizzo generico v1 o un account di archiviazione BLOB a un account per utilizzo generico v2, senza tempi di inattività e senza la necessità copiare i dati.
>
> Per altre informazioni sull'aggiornamento a un account per utilizzo generico v2, vedere [Eseguire l'aggiornamento a un account di archiviazione per utilizzo generico v2](storage-account-upgrade.md).

Gli account di archiviazione per utilizzo generico v2 offrono più livelli di accesso per l'archiviazione dei dati in base ai modelli di utilizzo. Per altre informazioni, vedere [Livelli di accesso per i dati BLOB in blocchi](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>Account per utilizzo generico v1

Gli account di archiviazione generici v1 forniscono l'accesso a tutti i servizi di Archiviazione di Azure, ma potrebbero non avere le funzionalità più recenti o i prezzi più bassi per gigabyte. Gli account di archiviazione per utilizzo generico v1 supportano i servizi di archiviazione di Azure seguenti:

- BLOB (tutti i tipi)
- File
- Dischi
- Code
- Tabelle

Nella maggior parte dei casi è consigliabile utilizzare account v2 generici. È possibile utilizzare account v1 generici per questi scenari:You can use general-purpose v1 accounts for these scenarios:

- Le applicazioni richiedono il modello di distribuzione classica di Azure. Gli account per utilizzo generico v2 e gli account di archiviazione BLOB supportano solo il modello di distribuzione Azure Resource Manager.

- Le applicazioni richiedono un uso intensivo delle transazioni o utilizzano una larghezza di banda di replica geografica significativa, ma non richiedono una capacità di grandi dimensioni. In questo caso, un account per utilizzo generico v1 può essere la scelta più economica.

- Si utilizza una versione [dell'API REST](https://msdn.microsoft.com/library/azure/dd894041.aspx) di Servizi di archiviazione precedente al 2014-02-14 o una libreria client con una versione inferiore alla 4.x. Non è possibile aggiornare l'applicazione.

### <a name="blockblobstorage-accounts"></a>Account BlockBlobStorage

Un account BlockBlobStorage è un account di archiviazione specializzato nel livello di prestazioni Premium per l'archiviazione di dati oggetto non strutturati come BLOB a blocchi o di aggiunta di BLOB. Rispetto agli account general-purpose v2 e BlobStorage, gli account BlockBlobStorage offrono una latenza bassa e coerente e tassi di transazione più elevati.

Gli account BlockBlobStorage non supportano attualmente la suddivisione in livelli a livelli di accesso a caldo, ad accesso a freddo o ad archivio. Questo tipo di account di archiviazione non supporta BLOB di pagine, tabelle o code.

### <a name="filestorage-accounts"></a>Account FileStorage

Un account FileStorage è un account di archiviazione specializzato usato per archiviare e creare condivisioni file premium. Questo tipo di account di archiviazione supporta i file ma non i BLOB in blocchi, i BLOB di aggiunta, i BLOB di pagine, le tabelle o le code.

Gli account FileStorage offrono caratteristiche dedicate alle prestazioni univoche, ad esempio il bursting delle operazioni di I/O al secondo. Per altre informazioni su queste caratteristiche, vedere la sezione [Livelli di archiviazione condivisione file](../files/storage-files-planning.md#storage-tiers) della Guida alla pianificazione dei file.

## <a name="naming-storage-accounts"></a>Denominazione degli account di archiviazione

Quando si assegna un nome all'account di archiviazione, tenere presenti queste regole:

- I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole.
- Nome dell'account di archiviazione deve essere univoco all'interno di Azure. Due account di archiviazione non possono avere lo stesso nome.

## <a name="performance-tiers"></a>Livelli di prestazioni

A seconda del tipo di account di archiviazione creato, è possibile scegliere tra i livelli di prestazioni standard e premium.

### <a name="general-purpose-storage-accounts"></a>Account di archiviazione per utilizzo generico

Gli account di archiviazione per utilizzo generico possono essere configurati per uno dei livelli di prestazioni seguenti:

- Un livello di prestazioni standard per l'archiviazione di BLOB, file, tabelle, code e dischi delle macchine virtuali di Azure. Per altre informazioni sugli obiettivi di scalabilità per gli account di archiviazione standard, vedere [Obiettivi di scalabilità per gli account di archiviazione standard.](scalability-targets-standard-account.md)
- Livello di prestazioni premium per l'archiviazione di dischi di macchine virtuali non gestiti. Microsoft consiglia di usare dischi gestiti con macchine virtuali di Azure anziché dischi non gestiti. Per altre informazioni sugli obiettivi di scalabilità per il livello di prestazioni Premium, vedere Obiettivi di scalabilità per gli account di archiviazione BLOB di pagine Premium.For more information about scalability targets for the premium performance tier, see [Scalability targets for premium page blob storage accounts.](../blobs/scalability-targets-premium-page-blobs.md)

### <a name="blockblobstorage-storage-accounts"></a>Account di archiviazione BlockBlobStorageBlockBlobStorage storage accounts

Gli account di archiviazione BlockBlobStorage offrono un livello di prestazioni premium per l'archiviazione di BLOB di blocchi e l'aggiunta di BLOB. Per altre informazioni, vedere Obiettivi di scalabilità per gli account di archiviazione BLOB con blocchi Premium.For more information, see [Scalability targets for premium block blob storage accounts.](../blobs/scalability-targets-premium-block-blobs.md)

### <a name="filestorage-storage-accounts"></a>Account di archiviazione FileStorage

Gli account di archiviazione FileStorage offrono un livello di prestazioni Premium per le condivisioni file di Azure.FileStorage storage accounts provide a premium performance tier for Azure file shares. Per altre informazioni, vedere Obiettivi di scalabilità e prestazioni di File di Azure.For more information, see [Azure Files scalability and performance targets.](../files/storage-files-scale-targets.md)

## <a name="access-tiers-for-block-blob-data"></a>Livelli di accesso per i dati BLOB in blocchi

Archiviazione di Azure offre diverse opzioni per l'accesso ai dati BLOB in blocchi in base ai modelli di utilizzo. Ogni livello di accesso in Archiviazione di Azure è ottimizzato per un determinato modello di utilizzo dei dati. Selezionando il livello di accesso corretto per le proprie esigenze, è possibile archiviare i dati BLOB in blocchi nel modo economicamente più conveniente.

I livelli di accesso disponibili sono i seguenti:

- Livello di accesso **a caldo.** Questo livello è ottimizzato per l'accesso frequente degli oggetti nell'account di archiviazione. L'accesso ai dati nel livello caldo è più conveniente, mentre i costi di archiviazione sono maggiori. I nuovi account di archiviazione vengono creati nel livello ad accesso frequente per impostazione predefinita.
- Il livello di accesso **Cool.** Questo livello è ottimizzato per l'archiviazione di grandi quantità di dati a cui si accede e che vengono archiviati raramente per almeno 30 giorni. L'archiviazione dei dati nel livello interessante è più conveniente, ma l'accesso a tali dati può essere più costoso rispetto all'accesso ai dati nel livello a caldo.
- Il livello **archivio.** Questo livello è disponibile solo per i singoli BLOB di blocchi. Il livello di archiviazione è ottimizzato per i dati che possono tollerare diverse ore di latenza di recupero e che rimarranno nel livello di archiviazione per almeno 180 giorni. Il livello di archiviazione è l'opzione più conveniente per l'archiviazione dei dati. Tuttavia, l'accesso a tali dati è più costoso rispetto all'accesso ai dati nei livelli di caldo o cool.

Se viene apportata una modifica al modello di utilizzo dei dati, è possibile passare da un livello di accesso all'altro in qualsiasi momento. Per altre informazioni sui livelli di accesso, vedere Archiviazione BLOB di Azure: livelli di [accesso hot, cool e archiviazione.](../blobs/storage-blob-storage-tiers.md)

> [!IMPORTANT]
> La modifica del livello di accesso per un account di archiviazione o un BLOB esistente può comportare costi aggiuntivi. Per altre informazioni, vedere la sezione [Fatturazione dell'account di archiviazione](#storage-account-billing).

## <a name="redundancy"></a>Ridondanza

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

## <a name="encryption"></a>Crittografia

Tutti i dati nell'account di archiviazione vengono crittografati sul lato del servizio. Per altre informazioni sulla crittografia, vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Endpoint dell'account di archiviazione

Un account di archiviazione offre uno spazio dei nomi univoco in Azure per i dati. Tutti gli oggetti archiviati in Archiviazione di Azure hanno un indirizzo che include il nome univoco dell'account. La combinazione del nome dell'account e dell'endpoint di servizio di Archiviazione di Azure costituisce gli endpoint per l'account di archiviazione.

Ad esempio, se l'account di archiviazione per utilizzo generico si chiama *mystorageaccount*, gli endpoint predefiniti per tale account sono:

- Archiviazione BLOB:Blob storage:`https://*mystorageaccount*.blob.core.windows.net`
- Archiviazione tabella:`https://*mystorageaccount*.table.core.windows.net`
- Archiviazione coda:`https://*mystorageaccount*.queue.core.windows.net`
- File di Azure:`https://*mystorageaccount*.file.core.windows.net`

> [!NOTE]
> Gli account di archiviazione BLOB e BLOB dei blocchi espongono solo l'endpoint del servizio BLOB.

Costruire l'URL per l'accesso a un oggetto in un account di archiviazione aggiungendo il percorso dell'oggetto nell'account di archiviazione all'endpoint. Ad esempio, il formato di un indirizzo è simile al seguente: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

È anche possibile configurare l'account di archiviazione per l'uso di un dominio personalizzato per i BLOB. Per altre informazioni, vedere [Configurare un nome di dominio personalizzato per l'account di Archiviazione di Azure](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Controllare l'accesso ai dati dell'account

Per impostazione predefinita, i dati nel proprio account sono accessibili solo all'utente, ovvero al proprietario dell'account. È possibile controllare chi può accedere ai dati e quali autorizzazioni assegnare.

Ogni richiesta effettuata all'account di archiviazione deve essere autorizzata. A livello del servizio, la richiesta deve includere un'intestazione *Authorization* valida. In particolare, questa intestazione include tutte le informazioni necessarie al servizio per convalidare la richiesta prima di eseguirla.

È possibile concedere l'accesso ai dati nell'account di archiviazione usando uno degli approcci seguenti:

- **Azure Active Directory:** Usare le credenziali di Azure Active Directory (Azure AD) per autenticare un utente, un gruppo o un'altra identità per l'accesso ai dati BLOB e coda. Se l'autenticazione di un'identità ha esito positivo, Azure AD restituisce un token da usare per l'autorizzazione della richiesta con l'archiviazione BLOB di Azure o con l'archiviazione code. Per altre informazioni, vedere [Autenticare l'accesso ad Archiviazione di Azure tramite Azure Active Directory](storage-auth-aad.md).
- **Autorizzazione con chiave condivisa:** usare la chiave di accesso dell'account di archiviazione per creare una stringa di connessione usata dall'applicazione in fase di esecuzione per accedere ad Archiviazione di Azure. I valori nella stringa di connessione vengono usati per creare l'intestazione *Autorizzazione* che viene passata ad Archiviazione di Azure. Per altre informazioni, vedere [Configurare le stringhe di connessione di Archiviazione di Azure](storage-configure-connection-string.md).
- **Firma di accesso condiviso:** Usare una firma di accesso condiviso per delegare l'accesso alle risorse nell'account di archiviazione, se non si usa l'autorizzazione di Azure AD. Una firma di accesso condiviso è un token che incapsula nell'URL tutte le informazioni necessarie per autorizzare una richiesta ad Archiviazione di Azure. È possibile specificare la risorsa di archiviazione, le autorizzazioni concesse e l'intervallo per cui sono valide le autorizzazioni come parte della firma di accesso condiviso. Per altre informazioni, vedere [Uso delle firme di accesso condiviso](storage-sas-overview.md).

> [!NOTE]
> L'autenticazione degli utenti o delle applicazioni tramite le credenziali di Azure AD offre un livello superiore di sicurezza e facilità d'uso rispetto ad altri metodi di autorizzazione. Mentre con le applicazioni è possibile continuare a usare l'autorizzazione con chiave condivisa, l'uso di Azure AD consente di evitare la necessità di archiviare la chiave di accesso dell'account con il codice. È anche possibile continuare a usare le firme di accesso condiviso per concedere accesso specifico alle risorse dell'account di archiviazione, ma Azure AD offre funzionalità simili senza la necessità di gestire i token di firma di accesso condiviso o di occuparsi della revoca di una di firma di accesso condiviso compromessa.
>
> Microsoft consiglia di usare l'autorizzazione di Azure AD per le applicazioni BLOB e coda di Archiviazione di Azure quando possibile.

## <a name="copying-data-into-a-storage-account"></a>Copia dei dati in un account di archiviazione

Microsoft fornisce utilità e librerie per l'importazione dei dati da dispositivi di archiviazione locali o provider di archiviazione cloud di terze parti. La soluzione utilizzata dipende dalla quantità di dati che si sta trasferendo.

Quando effettua l'aggiornamento a un account per utilizzo generico v2 da un account per utilizzo generico v1 o un account di archiviazione BLOB, la migrazione dei dati viene eseguita automaticamente. Microsoft consiglia questo percorso per l'aggiornamento dell'account. Tuttavia, se si decide di spostare i dati da un account v1 generico a un account di archiviazione BLOB, eseguire la migrazione dei dati manualmente, usando gli strumenti e le librerie descritti di seguito.

### <a name="azcopy"></a>AzCopy

AzCopy è un'utilità da riga di comando Windows progettata per offrire prestazioni elevate di copia dei dati da e verso Archiviazione di Azure. È possibile usare AzCopy per copiare i dati in un account di archiviazione BLOB da un account di archiviazione per utilizzo generico esistente o per caricare i dati da dispositivi di archiviazione locali. Per ulteriori informazioni, consultate [Trasferire dati con l'utilità della riga](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)di comando AzCopy .

### <a name="data-movement-library"></a>Libreria di spostamento dei dati

La libreria di spostamento dei dati di Archiviazione di Azure per .NET si basa sul framework di spostamento dei dati principali alla base di AzCopy. La libreria è progettata per operazioni di trasferimento dei dati affidabili, semplici e a prestazioni elevate simili a quelle di AzCopy. È possibile utilizzare la libreria di spostamento dati per sfruttare i vantaggi delle funzionalità di AzCopy in modo nativo. Per altre informazioni, vedere Libreria di [spostamento dati di Archiviazione di Azure per .NETFor](https://github.com/Azure/azure-storage-net-data-movement) more information, see Azure Storage Data Movement Library for .NET

### <a name="rest-api-or-client-library"></a>API REST o libreria client

È possibile creare un'applicazione personalizzata per eseguire la migrazione dei dati da un account di archiviazione v1 generico in un account di archiviazione BLOB. Usare una delle librerie client di Azure o l'API REST dei servizi di archiviazione di Azure.Use one of the Azure client libraries or the Azure storage services REST API. Archiviazione di Azure fornisce librerie client avanzate per più linguaggi e piattaforme, ad esempio .NET, Java, C++, Node.js, PHP, Ruby e Python. Le librerie client offrono funzionalità avanzate, ad esempio la logica di ripetizione dei tentativi, la registrazione e i caricamenti paralleli. È possibile sviluppare usando direttamente l'API REST, che può essere chiamata da qualsiasi linguaggio in grado di eseguire richieste HTTP/HTTPS.

Per altre informazioni sull'API REST di Archiviazione di Azure, vedere [Informazioni di riferimento sulle API REST dei servizi di archiviazione di Azure](https://docs.microsoft.com/rest/api/storageservices/).

> [!IMPORTANT]
> I BLOB crittografati con la crittografia lato client archiviano i metadati correlati alla crittografia con il BLOB. Se si copia un BLOB crittografato con la crittografia lato client, verificare che l'operazione di copia mantenga i metadati del BLOB e soprattutto quelli correlati alla crittografia. Se si copia un BLOB senza i metadati di crittografia, il contenuto del BLOB non sarà più recuperabile. Per altre informazioni sui metadati correlati alla crittografia, vedere [Crittografia lato client e Insieme di credenziali chiave Azure per Archiviazione di Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="storage-account-billing"></a>Fatturazione dell'account di archiviazione

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Creare un account di archiviazioneCreate a storage account](storage-account-create.md)
- [Creare un account di archiviazione BLOB in blocchi](../blobs/storage-blob-create-account-block-blob.md)
