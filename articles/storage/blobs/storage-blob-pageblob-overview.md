---
title: "Funzionalità univoche dei BLOB di pagine di Azure | Microsoft Docs"
description: Panoramica dei BLOB di pagine di Azure, dei vantaggi e dei casi d'uso con script di esempio.
services: storage
author: anasouma
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 01/10/2018
ms.author: wielriac
ms.openlocfilehash: 56e8c4c9f7ab9b40a210f284960f959a437a4e20
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="unique-features-of-azure-page-blobs"></a>Funzionalità univoche dei BLOB di pagine di Azure

Archiviazione di Azure offre tre tipi di archivio BLOB: BLOB in blocchi, BLOB di accodamento e BLOB di pagine. I BLOB in blocchi sono costituiti da blocchi e sono ideali per l'archiviazione di file di testo o binari e per il caricamento di file di grandi dimensioni in modo efficiente. Anche i BLOB di accodamento sono costituiti da blocchi, ma sono ottimizzati per le operazioni di accodamento e per questo sono ideali per gli scenari di registrazione. I BLOB di pagine sono costituiti da pagine da 512 byte fino a 8 TB di dimensione totale e sono più efficienti per operazioni frequenti di lettura/scrittura casuali. I BLOB di pagine sono alla base dei dischi IaaS di Azure. Questo articolo descrive le funzionalità e i vantaggi dei BLOB di pagine.

## <a name="overview"></a>Panoramica
I BLOB di pagine sono una raccolta di pagine da 512 byte, che offrono la possibilità di leggere/scrivere intervalli arbitrari di byte. I BLOB di pagine sono quindi ideali per l'archiviazione di strutture di dati di tipo sparse e basati sull'indice, ad esempio i dischi del sistema operativo e di dati per le macchine virtuali e i database. Il database SQL di Azure, ad esempio, usa i BLOB di pagine come risorsa di archiviazione permanente sottostante per i database. I BLOB di pagine vengono spesso usati anche per i file con aggiornamenti basati su intervalli.  

Le funzionalità principali dei BLOB di pagine di Azure sono l'interfaccia REST, la durabilità della risorsa di archiviazione sottostante e le funzionalità di migrazione senza problemi in Azure. Queste funzionalità sono illustrate in modo più dettagliato nella sezione successiva. I BLOB di pagine di Azure sono inoltre attualmente supportati in due tipi di risorsa di archiviazione: Archiviazione Premium e Archiviazione Standard. Archiviazione Premium è progettato specificamente per i carichi di lavoro che richiedono prestazioni elevate coerenti e bassa latenza e rende i BLOB di pagine Premium ideali per i database di archiviazione dati ad alte prestazioni.  Archiviazione Standard è economicamente più conveniente per l'esecuzione di carichi di lavoro non sensibili alla latenza.

## <a name="sample-use-cases"></a>Casi d'uso di esempio

Verranno ora esaminati un paio di casi d'uso per i BLOB di pagine a partire dai dischi IaaS di Azure. I BLOB di pagine di Azure sono alla base della piattaforma di dischi virtuali per il servizio IaaS di Azure. Sia i dischi dati che quelli del sistema operativo di Azure vengono implementati come dischi virtuali in cui i dati vengono resi persistenti nella piattaforma di archiviazione di Azure e quindi recapitati alle macchine virtuali per ottenere prestazioni ottimali. I dischi di Azure vengono resi persistenti nel [formato VHD](https://technet.microsoft.com/library/dd979539.aspx) di Hyper-V e archiviati come [BLOB di pagine](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) in Archiviazione di Azure. Oltre a usare i dischi virtuali per le VM IaaS di Azure, i BLOB di pagine abilitano anche scenari PaaS e DBaaS, ad esempio il servizio database SQL di Azure, che usa attualmente i BLOB di pagine per l'archiviazione dei dati SQL, consentendo operazioni casuali rapide di lettura/scrittura per il database. Un altro esempio è quello in cui si ha un servizio PaaS per l'accesso multimediale condiviso per applicazioni di collaborazione per la modifica di video e i BLOB di pagine consentono di accedere rapidamente a percorsi casuali nei file multimediali. Consente anche a più utenti di modificare e unire in modo rapido ed efficiente gli stessi file multimediali. 

I servizi forniti da Microsoft, ad esempio Azure Site Recovery e Backup di Azure, e molti sviluppatori di terze parti hanno implementato le innovazioni leader del settore usando l'interfaccia REST del BLOB di pagine. Di seguito sono elencati alcuni degli scenari univoci implementati in Azure: 
* Gestione degli snapshot incrementali controllata dell'applicazione: le applicazioni possono sfruttare gli snapshot dei BLOB di pagine e le API REST per il salvataggio dei checkpoint dell'applicazione senza dover sostenere la costosa duplicazione dei dati. Archiviazione di Azure supporta gli snapshot locali per i BLOB di pagine, che non richiedono la copia dell'intero BLOB. Queste API snapshot pubbliche consentono anche l'accesso e la copia dei valori differenziali tra gli snapshot.
* Migrazione in tempo reale dell'applicazione e dei dati dall'ambiente locale al cloud: copiare i dati locali e usare le API REST per scrivere direttamente nel BLOB di pagine di Azure mentre la VM locale è ancora in esecuzione. Dopo l'aggiornamento della destinazione, è possibile effettuare rapidamente il failover nella VM di Azure usando tali dati. In questo modo, è possibile eseguire la migrazione delle VM e dei dischi virtuali dall'ambiente locale al cloud con un tempo di inattività minimo perché la migrazione dei dati avviene in background mentre si continua a usare la VM e il tempo di inattività necessario per il failover sarà di pochi minuti.
* Accesso condiviso [basato sulla firma di accesso condiviso](../common/storage-dotnet-shared-access-signature-part-1.md), che consente scenari come quelli con più lettori e un singolo writer con il supporto per il controllo della concorrenza.

## <a name="page-blob-features"></a>Funzionalità dei BLOB di pagine

### <a name="rest-api"></a>API REST
Per un'introduzione, vedere il documento relativo allo [sviluppo con i BLOB di pagine](storage-dotnet-how-to-use-blobs.md). Come esempio, verrà esaminato l'accesso ai BLOB di pagine tramite la libreria client di archiviazione per .NET. 

Il diagramma seguente illustra le relazioni generali tra account, contenitori e BLOB di pagine.

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-certain-size"></a>Creazione di un BLOB di pagine vuoto di una determinata dimensione
Per creare un BLOB di pagine, viene prima creato un oggetto **CloudBlobClient**, con l'URI di base per l'accesso all'archivio BLOB per l'account di archiviazione (*pbaccount* nella figura 1) con l'oggetto **StorageCredentialsAccountAndKey**, come illustrato nell'esempio seguente. L'esempio illustra quindi la creazione di un riferimento a un oggetto **CloudBlobContainer** e quindi la creazione del contenitore (*testvhds*) se non esiste già. Usando l'oggetto **CloudBlobContainer**, creare un riferimento a un oggetto **CloudPageBlob** specificando il nome del BLOB di pagine (os4.vhd) a cui accedere. Per creare il BLOB di pagine, chiamare [CloudPageBlob.Create](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.create?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_Create_System_Int64_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) passando le dimensioni massime per il BLOB da creare. *BlobSize* deve essere un multiplo di 512 byte.

```csharp
using Microsoft.WindowsAzure.StorageClient;
long OneGigabyteAsBytes = 1024 * 1024 * 1024;
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("testvhds");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();

CloudPageBlob pageBlob = container.GetPageBlobReference("os4.vhd");
pageBlob.Create(16 * OneGigabyteAsBytes);
```

#### <a name="resizing-a-page-blob"></a>Ridimensionamento di un BLOB di pagine
Per ridimensionare un BLOB di pagine dopo la creazione, usare l'API [Resize](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.resize?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_Resize_System_Int64_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_). Le dimensioni richieste devono essere un multiplo di 512 byte.
```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes); 
```

#### <a name="writing-pages-to-a-page-blob"></a>Scrittura di pagine in un BLOB di pagine
Per scrivere le pagine, usare il metodo [CloudPageBlob.WritePages](/library/microsoft.windowsazure.storageclient.cloudpageblob.writepages.aspx).  In questo modo è possibile scrivere un set sequenziale di pagine con dimensioni massime pari a 4 MB. L'offset in cui si scrive deve iniziare da un limite di 512 byte (startingOffset % 512 == 0) e terminare con un limite di 512 - 1.  L'esempio di codice seguente mostra come chiamare **WritePages** per un BLOB:

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

Non appena una richiesta di scrittura per un set sequenziale di pagine ha esito positivo nel servizio BLOB e viene replicata per la durabilità e la resilienza, viene effettuato il commit della scrittura e l'esito positivo viene restituito al client.  

Il diagramma seguente mostra 2 operazioni di scrittura separate:

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  Un'operazione di scrittura che inizia dall'offset 0 di lunghezza pari a 1024 byte 
2.  Un'operazione di scrittura che inizia dall'offset 4096 di lunghezza pari a 1024 byte 

#### <a name="reading-pages-from-a-page-blob"></a>Lettura di pagine da un BLOB di pagine
Per leggere le pagine, usare il metodo [CloudPageBlob.DownloadRangeToByteArray](/dotnet/api/microsoft.windowsazure.storage.blob.icloudblob.downloadrangetobytearray?view=azure-dotnet) per leggere un intervallo di byte dal BLOB di pagine. In questo modo è possibile scaricare l'intero BLOB o un intervallo di byte a partire da un offset nel BLOB. Durante la lettura, non è necessario che l'offset inizi da un multiplo di 512. Quando si leggono byte da una pagina NUL, il servizio restituisce zero byte.
```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```
La figura seguente illustra un'operazione di lettura con BlobOffSet pari a 256 e rangeSize pari a 4352. I dati restituiti sono evidenziati in arancione. Per le pagine NUL vengono restituiti zeri

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Se si ha un BLOB scarsamente popolato, potrebbe essere necessario scaricare solo le aree della pagina valide per evitare addebiti per l'uscita di zero byte e per ridurre la latenza di download.  Per determinare le pagine supportate dai dati, usare [CloudPageBlob.GetPageRanges](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.getpageranges?view=azure-dotnet). È quindi possibile enumerare gli intervalli restituiti e scaricare i dati in ogni intervallo. 
```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the rangeSize
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // TODO: use the buffer for the page range just read
} 
```

#### <a name="leasing-a-page-blob"></a>Leasing di un BLOB di pagine
L'operazione Lease BLOB stabilisce e gestisce un blocco su un BLOB per le operazioni di scrittura e di eliminazione. Questa operazione è utile negli scenari in cui più client accedono a un BLOB di pagine, per assicurarsi che un solo client per volta possa scrivere nel BLOB. Dischi di Azure, ad esempio, sfrutta questo meccanismo di leasing per assicurare che il disco venga gestito da una singola VM. La durata del blocco può variare da 15 a 60 secondi o può essere infinita. Per altri dettagli, vedere la documentazione [qui](/rest/api/storageservices/lease-blob).

> Usare il collegamento seguente per ottenere [esempi di codice](/resources/samples/?service=storage&term=blob&sort=0 ) per molti altri scenari applicativi. 

Oltre alle API REST avanzate, i BLOB di pagine forniscono anche accesso condiviso, durabilità e sicurezza avanzata. Tali vantaggi verranno illustrati in modo più dettagliato nei paragrafi successivi. 

### <a name="concurrent-access"></a>Accesso simultaneo
L'API REST BLOB di pagine e il relativo meccanismo di leasing consentono alle applicazioni di accedere ai BLOB di pagine da più client. Si supponga, ad esempio, di dover compilare un servizio cloud distribuito che condivide gli oggetti di archiviazione con più utenti. Potrebbe trattarsi di un'applicazione Web che gestisce un raccolta di immagini di grandi dimensioni per più utenti. Un'opzione per l'implementarla consiste nell'usare una VM con dischi connessi. Tra gli svantaggi di tale opzione è incluso il vincolo che un disco possa essere connesso a una singola VM, limitando così la scalabilità e la flessibilità e aumentando i rischi. Se si verifica un problema con la VM o il servizio in esecuzione nella VM, dovuto quindi al lease, l'immagine non è accessibile fino a quando il lease scade o viene interrotto. Un altro svantaggio è il costo aggiuntivo di una VM IaaS. 

Un'opzione alternativa consiste nell'usare i BLOB di pagine direttamente tramite le API REST di Archiviazione di Azure. Questa opzione elimina la necessità di costose VM IaaS, offre la flessibilità completa derivante dall'accesso diretto da più client, semplifica il modello di distribuzione classica non essendo più necessario connettere/disconnettere i dischi ed elimina il rischio di problemi nella VM. Offre anche lo stesso livello di prestazioni di un disco per le operazioni casuali di lettura/scrittura

### <a name="durability-and-high-availability"></a>Durabilità e disponibilità elevata
Le archiviazioni Standard e Premium sono risorse di archiviazione durevoli in cui i dati dei BLOB di pagine vengono sempre replicati per garantire la durabilità e la disponibilità elevata. Per altre informazioni sulla ridondanza di Archiviazione di Azure, vedere questa [documentazione](../common/storage-redundancy.md). Azure ha offerto in modo costante una durabilità di livello aziendale per dischi IaaS e BLOB di pagine, con una percentuale di [frequenza di errori annualizzata](https://en.wikipedia.org/wiki/Annualized_failure_rate) pari a ZERO, ovvero la migliore del settore, vale a dire che Azure non ha mai perso i dati dei BLOB di pagine di un cliente. 

### <a name="seamless-migration-to-azure"></a>Migrazione senza problemi ad Azure
Per i clienti e gli sviluppatori interessati a implementare la propria soluzione di backup personalizzata, Azure offre anche snapshot incrementali che includono solo valori differenziali. Questa funzionalità elimina il costo della copia iniziale completa, riducendo considerevolmente il costo del backup. Si tratta di un'altra caratteristica avanzata, che si aggiunge alla possibilità di leggere e copiare in modo efficiente i dati differenziali e che consente agli sviluppatori di realizzare sempre più innovazioni e offre un'esperienza all'avanguardia di backup e ripristino di emergenza in Azure. È possibile configurare la propria soluzione di backup o ripristino di emergenza per le VM in Azure usando [Blob Snapshot](/rest/api/storageservices/snapshot-blob) con l'API [Get Page Ranges](/rest/api/storageservices/get-page-ranges) e l'API [Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob), che è possibile usare per copiare facilmente i dati incrementali per il ripristino di emergenza. 

Diverse aziende hanno inoltre carichi di lavoro critici già in esecuzione in data center locali. Per la migrazione del carico di lavoro al cloud, due delle principali preoccupazioni sono il tempo di inattività necessario per copiare i dati e il rischio che si verifichino problemi imprevisti dopo il cambio. In diversi casi, il tempo di inattività può essere un ostacolo per la migrazione al cloud. Usando l'API REST BLOB di pagine, Azure risolve questo problema consentendo la migrazione cloud con un'interruzione minima per i carichi di lavoro critici. 

Per esempi di come creare uno snapshot e ripristinare un BLOB di pagine da uno snapshot, vedere l'articolo [Configurare un processo di backup usando snapshot incrementali](../../virtual-machines/windows/incremental-snapshots.md).
