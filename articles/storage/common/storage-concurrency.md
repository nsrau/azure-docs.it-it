---
title: Gestione della concorrenza
titleSuffix: Azure Storage
description: Informazioni su come gestire la concorrenza in archiviazione di Azure per i servizi BLOB, di Accodamento, tabelle e file. Comprendere le tre strategie principali di concorrenza dei dati utilizzate.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: ac54282135759f14f17ed16b9779013f849bd8d7
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488674"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Gestione della concorrenza nell'archiviazione di Microsoft Azure

Le moderne applicazioni basate su Internet sono in genere caratterizzate dalla presenza simultanea di più utenti che visualizzano e aggiornano dati. Gli sviluppatori di applicazioni devono valutare con attenzione la possibilità di offrire un'esperienza utente prevedibile, in particolare quando più utenti possono aggiornare gli stessi dati. Gli sviluppatori in genere prendono in considerazione tre strategie principali di concorrenza dei dati:

1. Concorrenza ottimistica: un'applicazione che esegue un aggiornamento farà parte dell'aggiornamento, verificherà se i dati sono stati modificati dall'ultima lettura dei dati da parte dell'applicazione. Se, ad esempio, due utenti che visualizzano una pagina wiki effettuano un aggiornamento alla stessa pagina, la piattaforma wiki deve garantire che il secondo aggiornamento non sovrascriva il primo aggiornamento e che entrambi gli utenti conoscano se l'aggiornamento è stato completato o meno. Questa strategia viene usata con maggiore frequenza nelle applicazioni Web.
2. Concorrenza pessimistica: un'applicazione che cerca di eseguire un aggiornamento prenderà un blocco su un oggetto, impedendo ad altri utenti di aggiornare i dati fino a quando il blocco non viene rilasciato.
3. Ultimo autore WINS: un approccio che consente a qualsiasi operazione di aggiornamento di continuare senza verificare se un'altra applicazione ha aggiornato i dati dopo che l'applicazione ha prima letto i dati. Questa strategia viene spesso usata quando i dati vengono partizionati, pertanto non è possibile che più utenti accedano agli stessi dati. Può inoltre essere utile per l'elaborazione di flussi dei dati di breve durata.

Questo articolo fornisce una panoramica del modo in cui archiviazione di Azure semplifica lo sviluppo supportando tutte e tre queste strategie di concorrenza.

## <a name="azure-storage-simplifies-cloud-development"></a>Archiviazione di Azure semplifica lo sviluppo cloud

Archiviazione di Azure supporta tutte e tre le strategie, sebbene sia caratterizzata dalla capacità di offrire supporto completo per la concorrenza ottimistica e pessimistica. Archiviazione di Azure è stata progettata per adottare un modello di coerenza forte, garantendo che quando viene eseguito il commit di un'operazione di inserimento o aggiornamento di dati, tutti gli accessi a tali dati visualizzeranno l'aggiornamento più recente. Le piattaforme di archiviazione che usano un modello di coerenza finale presentano un ritardo tra il momento in cui una scrittura viene eseguita da un utente e il momento in cui i dati aggiornati possono essere visualizzati da altri utenti.

Gli sviluppatori devono anche sapere come una piattaforma di archiviazione isoli la modifica, in particolare per le modifiche allo stesso oggetto tra le transazioni. Il servizio di archiviazione di Azure usa l'isolamento dello snapshot per consentire che le operazioni di lettura avvengano simultaneamente con le operazioni di scrittura all'interno di una singola partizione. A differenza di altri livelli di isolamento, l'isolamento degli snapshot garantisce che tutte le letture vedano uno snapshot coerente dei dati persino durante l'esecuzione di aggiornamenti, essenzialmente restituendo gli ultimi valori di cui è stato eseguito il commit durante l'elaborazione di una transazione di aggiornamento.

## <a name="managing-concurrency-in-blob-storage"></a>Gestione della concorrenza nell'archiviazione BLOB

È possibile scegliere di usare modelli di concorrenza ottimistica o pessimistica per gestire l'accesso a BLOB e contenitori nel servizio BLOB. Se non si specifica in modo esplicito una strategia, l'ultima scrittura prevale è l'impostazione predefinita.

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Concorrenza ottimistica per BLOB e contenitori

Il servizio di archiviazione assegna un identificatore a ogni oggetto archiviato, Questo identificatore viene aggiornato ogni volta che viene eseguita un'operazione di aggiornamento su un oggetto. L'identificatore viene restituito al client nell'ambito di una risposta HTTP GET mediante l'intestazione (tag di entità) ETag definita all'interno del protocollo HTTP. Un utente che esegue un aggiornamento su tale oggetto può inviare l'ETag originale insieme a un'intestazione condizionale per garantire che un aggiornamento venga eseguito solo se è stata soddisfatta una determinata condizione, in questo caso la condizione è un'intestazione "If-Match", che richiede che il servizio di archiviazione accerti che il valore ETag specificato nella richiesta di aggiornamento sia uguale a quello archiviato nel servizio di archiviazione.

Il processo è il seguente:

1. Recuperare un BLOB da un servizio di archiviazione; la risposta include un valore di intestazione HTTP ETag che identifica la versione corrente dell'oggetto nel servizio di archiviazione.
2. Quando si aggiorna il BLOB, includere il valore ETag ricevuto al passaggio 1 nell'intestazione condizionale **If-Match** della richiesta inviata al servizio.
3. Il servizio confronta il valore ETag nella richiesta con il valore ETag corrente del BLOB.
4. Se il valore ETag corrente del BLOB è una versione diversa dall'ETag nell'intestazione condizionale **If-Match** , il servizio restituisce un errore 412 al client. Questo errore indica al client che un altro processo ha aggiornato il BLOB dopo che il client lo ha recuperato.
5. Se il valore ETag corrente del BLOB è la stessa versione dell'Etag nell'intestazione condizionale **If-Match** nella richiesta, il servizio esegue l'operazione richiesta e aggiorna il valore Etag corrente del BLOB per indicare la creazione di una nuova versione.

Il frammento C# seguente (che usa Client Storage Library 4.2.0) mostra un esempio semplice di come costruire una **If-Match AccessCondition** in base al valore ETag al quale viene effettuato l'accesso dalle proprietà di un BLOB precedentemente recuperato o inserito. Usa quindi l'oggetto **AccessCondition** quando aggiorna il BLOB: l'oggetto **AccessCondition** aggiunge l'intestazione **If-Match** alla richiesta. Se un altro processo ha aggiornato il BLOB, il servizio BLOB restituisce un messaggio di stato HTTP 412 (Condizione preliminare non riuscita). È possibile scaricare l'esempio completo qui: [Gestione della concorrenza con l'archiviazione di Microsoft Azure](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage Blob service which returns the ETag in response.
string originalETag = blockBlob.Properties.ETag;

// This code simulates an update by a third party.
string helloText = "Blob updated by a third party.";

// No ETag provided so original blob is overwritten (thus generating a new ETag)
blockBlob.UploadText(helloText);
Console.WriteLine("Blob updated. Updated ETag = {0}",
blockBlob.Properties.ETag);

// Now try to update the blob using the original ETag provided when the blob was created
try
{
    Console.WriteLine("Trying to update blob using original ETag to generate if-match access condition");
    blockBlob.UploadText(helloText,accessCondition:
    AccessCondition.GenerateIfMatchCondition(originalETag));
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
    {
        Console.WriteLine("Precondition failure as expected. Blob's original ETag no longer matches");
        // TODO: client can decide on how it wants to handle the 3rd party updated content.
    }
    else
        throw;
}
```

Archiviazione di Azure include anche il supporto per le intestazioni condizionali, ad esempio **If-Modified-Since**, **If-Unmodified-Since**, **If-None-Match**e le combinazioni di tali intestazioni. Per altre informazioni, vedere [Specifica di intestazioni condizionali per le operazioni del servizio BLOB](https://msdn.microsoft.com/library/azure/dd179371.aspx).

Nella tabella seguente sono riepilogate le operazioni contenitore che accettano intestazioni condizionali come **If-Match** nella richiesta e che restituiscono un valore ETag nella risposta.

| Operazione | Restituisce il valore ETag del contenitore | Accetta intestazioni condizionali |
|:--- |:--- |:--- |
| Create Container |Sì |No |
| Get Container Properties |Sì |No |
| Get Container Metadata |Sì |No |
| Set Container Metadata |Sì |Sì |
| Get Container ACL |Sì |No |
| Set Container ACL |Sì |Sì (*) |
| Delete Container |No |Sì |
| Lease Container |Sì |Sì |
| List Blobs |No |No |

(*) Le autorizzazioni definite da SetContainerACL vengono memorizzate nella cache e gli aggiornamenti a queste autorizzazioni importano 30 secondi per la propagazione durante i quali gli aggiornamenti del periodo non sono garantiti coerenti.

Nella tabella seguente sono riepilogate le operazioni BLOB che accettano intestazioni condizionali come **If-Match** nella richiesta e che restituiscono un valore ETag nella risposta.

| Operazione | Restituisce il valore ETag | Accetta intestazioni condizionali |
|:--- |:--- |:--- |
| Put Blob |Sì |Sì |
| Get Blob |Sì |Sì |
| Get Blob Properties |Sì |Sì |
| Set Blob Properties |Sì |Sì |
| Get Blob Metadata |Sì |Sì |
| Set Blob Metadata |Sì |Sì |
| Lease Blob (*) |Sì |Sì |
| Snapshot Blob |Sì |Sì |
| Copy Blob |Sì |Sì (per il BLOB di origine e destinazione) |
| Abort Copy Blob |No |No |
| Delete Blob |No |Sì |
| Put Block |No |No |
| Put Block List |Sì |Sì |
| Get Block List |Sì |No |
| Put Page |Sì |Sì |
| Get Page Ranges |Sì |Sì |

(*) Il BLOB di lease non modifica l'ETag in un BLOB.

### <a name="pessimistic-concurrency-for-blobs"></a>Concorrenza pessimistica per i BLOB

Per bloccare un BLOB per l'uso esclusivo, acquisire un [lease](https://msdn.microsoft.com/library/azure/ee691972.aspx) su di esso. Quando si acquisisce un lease, si specifica un periodo di tempo per il lease. Il periodo di tempo è compreso tra 15 e 60 secondi o infinito, che equivale a un blocco esclusivo. Rinnovare un lease finito per estenderlo. Rilasciare un lease al termine dell'operazione. L'archiviazione BLOB rilascia automaticamente i lease finiti alla scadenza.

I lease consentono di supportare diverse strategie di sincronizzazione. Le strategie includono *scrittura/lettura condivisa*, *scrittura esclusiva/lettura esclusiva*e *scrittura condivisa/lettura esclusiva*. Quando esiste un lease, l'archiviazione di Azure impone Scritture esclusive (operazioni Put, set ed Delete), garantendo tuttavia che l'esclusività per le operazioni di lettura richieda allo sviluppatore di garantire che tutte le applicazioni client usino un ID lease e che un solo client alla volta disponga di un ID lease valido. Le operazioni di lettura che non includono un ID lease generano letture condivise.

Il frammento C# seguente mostra un esempio relativo all'acquisizione di un lease esclusivo per 30 secondi su un BLOB, all'aggiornamento del contenuto del BLOB e al successivo rilascio del lease. Se è già presente un lease valido sul BLOB quando si prova ad acquisire un nuovo lease, il servizio BLOB restituisce un risultato di stato "HTTP (409) Conflict". Il frammento seguente usa un oggetto **AccessCondition** per incapsulare le informazioni relative al lease quando effettua una richiesta per aggiornare il BLOB nel servizio di archiviazione.  È possibile scaricare l'esempio completo qui: [Gestione della concorrenza con l'archiviazione di Microsoft Azure](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
// Acquire lease for 15 seconds
string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

// Update blob using lease. This operation will succeed
const string helloText = "Blob updated";
var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
blockBlob.UploadText(helloText, accessCondition: accessCondition);
Console.WriteLine("Blob updated using an exclusive lease");

//Simulate third party update to blob without lease
try
{
    // Below operation will fail as no valid lease provided
    Console.WriteLine("Trying to update blob without valid lease");
    blockBlob.UploadText("Update without lease, will fail");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
    else
        throw;
}
```

Se si prova a effettuare un'operazione di scrittura su un BLOB con lease senza passare l'ID lease, la richiesta ha esito negativo con un errore 412. Se il lease scade prima di chiamare il metodo **Uploadtext** , ma si passa ancora l'ID lease, anche la richiesta ha esito negativo con un errore **412** . Per altre informazioni sulla gestione degli ID lease e dei tempi di scadenza del lease, vedere la documentazione REST [Lease Blob](https://msdn.microsoft.com/library/azure/ee691972.aspx).

Le operazioni BLOB seguenti possono usare i lease per gestire la concorrenza pessimistica:

* Put Blob
* Get Blob
* Get Blob Properties
* Set Blob Properties
* Get Blob Metadata
* Set Blob Metadata
* Delete Blob
* Put Block
* Put Block List
* Get Block List
* Put Page
* Get Page Ranges
* Snapshot Blob: ID lease facoltativo se esiste un lease
* Copy Blob: ID lease obbligatorio se esiste un lease nel BLOB di destinazione
* Abort Copy Blob: ID lease obbligatorio se esiste un lease infinito nel BLOB di destinazione
* Lease Blob

### <a name="pessimistic-concurrency-for-containers"></a>Concorrenza pessimistica per i contenitori

I lease sui contenitori consentono di supportare le stesse strategie di sincronizzazione dei BLOB (*scrittura esclusiva/lettura condivisa*, *scrittura esclusiva/lettura esclusiva*e *scrittura condivisa/lettura esclusiva*). Tuttavia, a differenza dei BLOB il servizio di archiviazione impone solo l'esclusività sulle operazioni di eliminazione. Per eliminare un contenitore con un lease attivo, un client deve includere l'ID lease attivo con la richiesta di eliminazione. Tutte le altre operazioni contenitore hanno esito positivo su un contenitore con lease senza includere l'ID lease, nel qual caso sono operazioni condivise. Se è richiesta l'esclusività di un'operazione di aggiornamento (Put o Set) o di lettura, gli sviluppatori devono garantire che tutti i client usino un ID lease e che un solo client alla volta abbia un ID lease valido.

Le operazioni contenitore seguenti possono usare i lease per gestire la concorrenza pessimistica:

* Delete Container
* Get Container Properties
* Get Container Metadata
* Set Container Metadata
* Get Container ACL
* Set Container ACL
* Lease Container

Per altre informazioni, vedere:

* [Specifica di intestazioni condizionali per le operazioni del servizio BLOB](https://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Lease Container](https://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Lease del BLOB](https://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-table-storage"></a>Gestione della concorrenza nell'archiviazione tabelle

Il servizio tabelle usa le verifiche della concorrenza ottimistica come comportamento predefinito quando si lavora con le entità, a differenza del servizio BLOB in cui è necessario scegliere esplicitamente di eseguire verifiche della concorrenza ottimistica. L'altra differenza tra i servizi tabelle e BLOB è la possibilità di gestire solo il comportamento di concorrenza delle entità, ma con il servizio BLOB è possibile gestire la concorrenza di contenitori e BLOB.

Per usare la concorrenza ottimistica e controllare se un altro processo ha modificato un'entità da quando questa è stata recuperata dal servizio di archiviazione tabelle, è possibile usare il valore ETag ricevuto quando il servizio tabelle restituisce un'entità. Il processo è il seguente:

1. Recuperare un'entità da un servizio di archiviazione tabelle; la risposta include un valore ETag che identifica l'identificatore corrente associato all'entità nel servizio di archiviazione.
2. Quando si aggiorna l'entità, includere il valore ETag ricevuto al passaggio 1 nell'intestazione **If-Match** obbligatoria della richiesta inviata al servizio.
3. Il servizio confronta il valore ETag nella richiesta con il valore ETag corrente dell'entità.
4. Se il valore ETag corrente del BLOB è diverso dall'ETag nell'intestazione **If-Match** obbligatoria nella richiesta, il servizio restituisce un errore 412 al client. Ciò indica al cliente che un altro processo ha aggiornato l'entità da quando questa è stata recuperata dal client.
5. Se il valore ETag corrente dell'entità corrisponde all'ETag nell'intestazione **If-Match** obbligatoria della richiesta o se l'intestazione **If-Match** contiene il carattere jolly (*), il servizio esegue l'operazione richiesta e aggiorna il valore ETag corrente dell'entità per indicare che è stato aggiornato.

Il servizio tabelle richiede che il client includa un'intestazione **If-Match** nelle richieste di aggiornamento. Tuttavia, è possibile forzare un aggiornamento condizionale (strategia della prevalenza dell'ultima scrittura) e ignorare i controlli della concorrenza se il client imposta l'intestazione **If-Match** sul carattere jolly (*) nella richiesta.

Il seguente frammento C# mostra un'entità del cliente precedentemente creata o recuperata con l'aggiornamento dell'indirizzo di posta elettronica. L'operazione iniziale di inserimento o recupero archivia il valore ETag nell'oggetto del cliente e, poiché l'esempio usa la stessa istanza di oggetto quando esegue l'operazione di sostituzione, invia automaticamente il valore ETag alla tabella, consentendo al servizio di verificare l'eventuale presenza di violazioni della concorrenza. Se un altro processo ha aggiornato l'entità nella tabella di archiviazione, il servizio restituisce un messaggio di stato HTTP 412 (Condizione preliminare non riuscita).  È possibile scaricare l'esempio completo qui: [Gestione della concorrenza con l'archiviazione di Microsoft Azure](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
try
{
    customer.Email = "updatedEmail@contoso.org";
    TableOperation replaceCustomer = TableOperation.Replace(customer);
    customerTable.Execute(replaceCustomer);
    Console.WriteLine("Replace operation succeeded.");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == 412)
        Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
    else
        throw;
}
```

Per disabilitare esplicitamente il controllo della concorrenza, è necessario impostare la proprietà **ETag** dell'oggetto **employee** su "*" prima di eseguire l'operazione di sostituzione.

```csharp
customer.ETag = "*";
```

Nella tabella seguente sono riepilogate le modalità in cui le operazioni delle entità di tabella usano i valori ETag:

| Operazione | Restituisce il valore ETag | Richiede l'intestazione della richiesta If-Match |
|:--- |:--- |:--- |
| Query Entities |Sì |No |
| Insert Entity |Sì |No |
| Update Entity |Sì |Sì |
| Merge Entity |Sì |Sì |
| Delete Entity |No |Sì |
| Insert or Replace Entity |Sì |No |
| Insert or Merge Entity |Sì |No |

Si noti che le operazioni di **inserimento o sostituzione** dell'entità e dell' **entità INSERT o merge** *non* eseguono alcun controllo della concorrenza perché non inviano un valore ETag al servizio tabelle.

In generale, gli sviluppatori che utilizzano tabelle devono basarsi sulla concorrenza ottimistica. Se è necessario un blocco pessimistico durante l'accesso alle tabelle, assegnare un BLOB scelto per ogni tabella e provare a eseguire un lease sul BLOB prima di operare sulla tabella. Questo approccio richiede che l'applicazione assicuri che tutti i percorsi di accesso ai dati ottengano il lease prima di operare sulla tabella. Si noti inoltre che il tempo di lease minimo è di 15 secondi, che richiede un'attenta considerazione per la scalabilità.

Per altre informazioni, vedere:

* [Operazioni sulle entità](https://msdn.microsoft.com/library/azure/dd179375.aspx)

## <a name="managing-concurrency-in-the-queue-service"></a>Gestione della concorrenza nel servizio di accodamento

Uno scenario in cui la concorrenza rappresenta un problema nel servizio di accodamento è quello in cui più client recuperano i messaggi da una coda. Quando un messaggio viene recuperato dalla coda, la risposta include il messaggio e un valore di ricezione POP, necessario per eliminare il messaggio. Il messaggio non viene eliminato automaticamente dalla coda, ma dopo che è stato recuperato, non è visibile agli altri client per l'intervallo di tempo specificato dal parametro di timeout di visibilità. Il client che recupera il messaggio dovrebbe eliminare il messaggio dopo che è stato elaborato e prima dell'ora specificata dall'elemento TimeNextVisible della risposta, calcolata in base al valore del parametro di timeout di visibilità. Il valore di timeout di visibilità viene aggiunto al momento in cui viene recuperato il messaggio per determinare il valore di TimeNextVisible.

Il servizio di Accodamento non dispone del supporto per la concorrenza ottimistica o pessimistica e per questo motivo i client che elaborano i messaggi recuperati da una coda devono garantire che i messaggi vengano elaborati in modo idempotente. Per le operazioni di aggiornamento, ad esempio SetQueueServiceProperties, SetQueueMetaData, SetQueueACL e UpdateMessage, viene usata una strategia di WINS dell'ultimo writer.

Per altre informazioni, vedere:

* [API REST del servizio di accodamento](https://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Get Messages](https://msdn.microsoft.com/library/azure/dd179474.aspx)

## <a name="managing-concurrency-in-azure-files"></a>Gestione della concorrenza in File di Azure

È possibile accedere al servizio file usando due diversi endpoint di protocollo: SMB e REST. Il servizio REST non supporta il blocco ottimistico o il blocco pessimistico e tutti gli aggiornamenti seguiranno la strategia di WINS dell'ultimo writer. I client SMB che montano condivisioni file possono usare file system meccanismi di blocco per gestire l'accesso ai file condivisi, inclusa la possibilità di eseguire un blocco pessimistico. Quando un client SMB apre un file, specifica sia l'accesso al file sia la modalità di condivisione. L'impostazione di un'opzione di accesso al file "in scrittura" o "in lettura/scrittura" insieme a una modalità di condivisione file impostata su "nessuna" determinerà il blocco del file da parte di un client SMB fino alla chiusura del file. Se si prova a eseguire l'operazione REST su un file in cui un client SMB ha applicato un blocco, il servizio REST restituirà il codice di stato 409 (Conflitto) con il codice errore SharingViolation.

Quando un client SMB apre un file per eliminarlo, lo contrassegna come in attesa dell'eliminazione fino a quando tutti gli altri handle aperti del client SMB su tale file vengono chiusi. Quando un file viene contrassegnato come in attesa di eliminazione, qualsiasi operazione REST su tale file restituirà il codice di stato 409 (Conflitto) con codice errore SMBDeletePending. Il codice di stato 404 (non trovato) non viene restituito perché il client SMB può rimuovere il flag di eliminazione in sospeso prima di chiudere il file. In altre parole, il codice di stato 404 (Non trovato) è previsto solo dopo la rimozione del file. Se un file si trova in uno stato di attesa di eliminazione da parte del client SMB, non sarà incluso nei risultati degli elenchi di file. Le operazioni di eliminazione dei file REST e di eliminazione della directory REST vengono anche sottoposte a commit in modo atomico e non determinano lo stato di eliminazione in sospeso.

Per altre informazioni, vedere:

* [Managing File Locks](https://msdn.microsoft.com/library/azure/dn194265.aspx)

## <a name="next-steps"></a>Passaggi successivi

Per l'applicazione di esempio completa alla quale si fa riferimento in questo blog, vedere:

* [Gestione della concorrenza con l'archiviazione di Azure - Applicazione di esempio](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)

Per ulteriori informazioni sull'archiviazione di Azure, vedere:

* [Home page di Archiviazione di Microsoft Azure](https://azure.microsoft.com/services/storage/)
* [Introduzione ad Archiviazione di Azure](storage-introduction.md)
* Introduzione all'archiviazione per [BLOB](../blobs/storage-dotnet-how-to-use-blobs.md), [tabelle](../../cosmos-db/table-storage-how-to-use-dotnet.md), [code](../storage-dotnet-how-to-use-queues.md) e [file](../storage-dotnet-how-to-use-files.md)
* Architettura di archiviazione – [Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency) (Archiviazione di Microsoft Azure: un servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta)
