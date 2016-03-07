<properties 
	pageTitle="Gestione della concorrenza nell'archiviazione di Microsoft Azure"
	description="Come gestire la concorrenza per i servizi BLOB, di accodamento, di tabelle e file"
	services="storage"
	documentationCenter=""
	authors="jasonnewyork"
	manager="tadb"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="jahogg"/>

# Gestione della concorrenza nell'archiviazione di Microsoft Azure

## Panoramica

Le moderne applicazioni basate su Internet sono in genere caratterizzate dalla presenza simultanea di più utenti che visualizzano e aggiornano dati. Ciò richiede agli sviluppatori di applicazioni un'attenta riflessione su come offrire un'esperienza prevedibile ai propri utenti finali, in particolare per gli scenari in cui più utenti possono aggiornare gli stessi dati. Gli sviluppatori in genere prendono in considerazione tre strategie principali di concorrenza dei dati:


1.	Concorrenza ottimistica: un'applicazione che esegue un aggiornamento verificherà, nell'ambito di tale attività, se i dati siano cambiati rispetto all'ultima lettura. Ad esempio, se due utenti che visualizzano una pagina wiki effettuano un aggiornamento alla stessa pagina, la piattaforma wiki deve garantire che il secondo aggiornamento non sovrascriva il primo e che entrambi gli utenti sappiano se il proprio aggiornamento è stato effettuato correttamente o meno. Questa strategia viene usata con maggiore frequenza nelle applicazioni Web.
2.	Concorrenza pessimistica: un'applicazione che cerca di eseguire un aggiornamento applicherà un blocco a un oggetto, impedendo ad altri utenti di aggiornare i dati fino a quando il blocco non viene rimosso. Ad esempio, in uno scenario di replica dei dati master/slave in cui solo il master eseguirà gli aggiornamenti, il master in genere deterrà un blocco esclusivo per un lungo periodo di tempo sui dati in modo che nessuno possa aggiornarli.
3.	Prevalenza dell'ultima scrittura: un approccio che consente di eseguire qualsiasi operazione di aggiornamento senza prima verificare se altre applicazioni abbiano aggiornato i dati rispetto alla prima lettura dei dati da parte dell'applicazione. Questa strategia (che in realtà denota la mancanza di una strategia ufficiale) è in genere usata quando i dati vengono partizionati in modo tale da escludere qualsiasi possibilità che gli utenti possano accedere agli stessi dati. Può inoltre essere utile per l'elaborazione di flussi dei dati di breve durata.  

In questo articolo viene fornita una panoramica del modo in cui la piattaforma di archiviazione di Azure semplifica lo sviluppo fornendo un supporto eccellente per tutte e tre le strategie di concorrenza descritte.

## Archiviazione di Azure – Semplifica lo sviluppo cloud
Il servizio di archiviazione di Azure supporta tutte e tre le strategie, sebbene si caratterizzi per la sua capacità di fornire un supporto completo per la concorrenza ottimistica e la concorrenza pessimistica. Tale servizio è stato infatti progettato per accogliere un solido modello di coerenza in grado di garantire che quando il servizio di archiviazione esegue il commit di un'operazione di inserimento o aggiornamento dei dati, tutti gli accessi successivi a tali dati visualizzeranno l'aggiornamento più recente. Le piattaforme di archiviazione che usano un modello di coerenza finale prevedono un intervallo tra il momento in cui un utente esegue una scrittura e il momento in cui i dati aggiornati possono essere visti da altri utenti, rendendo in tal modo difficile lo sviluppo di applicazioni client allo scopo di impedire che le incoerenze influiscano sugli utenti finali.

Oltre a scegliere una strategia di concorrenza appropriata, gli sviluppatori devono inoltre conoscere il modo in cui una piattaforma di archiviazione isola le modifiche, in particolare quelle apportate allo stesso oggetto in più transazioni. Il servizio di archiviazione di Azure usa l'isolamento degli snapshot per consentire l'esecuzione simultanea di operazioni di lettura e operazioni di scrittura nell'ambito di una singola partizione. A differenza di altri livelli di isolamento, l'isolamento degli snapshot garantisce che tutte le letture vedano uno snapshot coerente dei dati persino durante l'esecuzione di aggiornamenti, essenzialmente restituendo gli ultimi valori di cui è stato eseguito il commit durante l'elaborazione di una transazione di aggiornamento.

## Gestione della concorrenza nell'archiviazione BLOB
È possibile scegliere di usare modelli di concorrenza ottimistica o pessimistica per gestire l'accesso a BLOB e contenitori nel servizio BLOB. Se non specifica esplicitamente una strategia, per impostazione predefinita prevale l'ultima scrittura.

### Concorrenza ottimistica per BLOB e contenitori  

Il servizio di archiviazione assegna un identificatore a ogni oggetto archiviato, che viene aggiornato ogni volta che un'operazione di aggiornamento viene eseguita su un oggetto. L'identificatore viene restituito al client nell'ambito di una risposta HTTP GET mediante l'intestazione (tag di entità) ETag definita all'interno del protocollo HTTP. Un utente che esegua un aggiornamento su tale oggetto può inviare l'ETag insieme a un'intestazione condizionale per garantire che un aggiornamento si verificherà solo se una determinata condizione viene soddisfatta; in questo caso la condizione è che un'intestazione “If-Match” che richiede il servizio di archiviazione per garantire il valore ETag specificato nella richiesta di aggiornamento sia uguale a quella archiviata nel servizio di archiviazione.

Il processo è il seguente:

1.	Recuperare un BLOB da un servizio di archiviazione; la risposta include un valore di intestazione HTTP ETag che identifica la versione corrente dell'oggetto nel servizio di archiviazione.
2.	Quando si aggiorna il BLOB, includere il valore ETag ricevuto al passaggio 1 nell'intestazione condizionale **If-Match** della richiesta inviata al servizio.
3.	Il servizio confronta il valore ETag nella richiesta con il valore ETag corrente del BLOB.
4.	Se il valore ETag corrente del BLOB è una versione diversa dall'ETag nell'intestazione condizionale **If-Match**, il servizio restituisce un errore 412 al client. Ciò indica al client che un altro processo ha aggiornato il BLOB rispetto a quando il client lo ha recuperato.
5.	Se il valore ETag corrente del BLOB è la stessa versione dell'Etag nell'intestazione condizionale **If-Match** nella richiesta, il servizio esegue l'operazione richiesta e aggiorna il valore Etag corrente del BLOB per indicare la creazione di una nuova versione.  

Il frammento C# seguente (che usa Client Storage Library 4.2.0) mostra un esempio semplice di come costruire una **If-Match AccessCondition** in base al valore ETag al quale viene effettuato l'accesso dalle proprietà di un BLOB precedentemente recuperato o inserito. Usa quindi l'oggetto **AccessCondition** quando aggiorna il BLOB: l'oggetto **AccessCondition** aggiunge l'intestazione **If-Match** alla richiesta. Se un altro processo ha aggiornato il BLOB, il servizio BLOB restituisce un messaggio di stato HTTP 412 (Condizione preliminare non riuscita). È possibile scaricare l'intero esempio dalla pagina relativa alla [gestione della concorrenza con Archiviazione di Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

	// Retrieve the ETag from the newly created blob
	// Etag is already populated as UploadText should cause a PUT Blob call
	// to storage blob service which returns the etag in response.
	string orignalETag = blockBlob.Properties.ETag;

	// This code simulates an update by a third party.
	string helloText = "Blob updated by a third party.";

	// No etag, provided so orignal blob is overwritten (thus generating a new etag)
	blockBlob.UploadText(helloText);
	Console.WriteLine("Blob updated. Updated ETag = {0}",
	blockBlob.Properties.ETag);

	// Now try to update the blob using the orignal ETag provided when the blob was created
	try
	{
	    Console.WriteLine("Trying to update blob using orignal etag to generate if-match access condition");
	    blockBlob.UploadText(helloText,accessCondition:
	    AccessCondition.GenerateIfMatchCondition(orignalETag));
	}
	catch (StorageException ex)
	{
	    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
	    {
	        Console.WriteLine("Precondition failure as expected. Blob's orignal etag no longer matches");
	        // TODO: client can decide on how it wants to handle the 3rd party updated content.
	    }
	    else
	        throw;
	}  

Il servizio di archiviazione include anche il supporto di altre intestazioni condizionale quali **If-Modified-Since**, **If-Unmodified-Since** e **If-None-Match** nonché le relative combinazioni. Per altre informazioni, vedere [Specifica di intestazioni condizionali per le operazioni del servizio Blob](http://msdn.microsoft.com/library/azure/dd179371.aspx) su MSDN.

Nella tabella seguente sono riepilogate le operazioni contenitore che accettano intestazioni condizionali come **If-Match** nella richiesta e che restituiscono un valore ETag nella risposta.

Operazione |Restituisce il valore ETag del contenitore|	Accetta intestazioni condizionali|
------------|-----------------------|------------------------------------|
Create Container|	Sì|	No|
Get Container Properties|	Sì|	No|
Get Container Metadata|	Sì|	No|
Set Container Metadata|	Sì|	Sì|
Get Container ACL|	Sì|	No|
Set Container ACL|	Sì|	Sì (*)|Delete Container| No| Sì|Lease Container| Sì| Sì|List Blobs| No| No 

(*) Le autorizzazioni definite da SetContainerACL sono memorizzate nella cache e la propagazione degli aggiornamenti a queste autorizzazioni richiede 30 secondi durante i quali la coerenza degli aggiornamenti non è garantita.

Nella tabella seguente sono riepilogate le operazioni BLOB che accettano intestazioni condizionali come **If-Match** nella richiesta e che restituiscono un valore ETag nella risposta.

Operazione |Restituisce il valore ETag |Accetta intestazioni condizionali|
-----------|-------------------|----------------------------|
Put Blob|	Sì|	Sì|
Get Blob|	Sì|	Sì|
Get Blob Properties|	Sì|	Sì|
Set Blob Properties|	Sì|	Sì|
Get Blob Metadata|	Sì|	Sì|
Set Blob Metadata|	Sì|	Sì|
Lease Blob (*)| Sì| Sì| Snapshot Blob| Sì| Sì| Copy Blob| Sì| Sì (per BLOB di origine e di destinazione)| Abort Copy Blob| No| No| Delete Blob| No| Sì| Put Block| No| No| Put Block List| Sì| Sì| Get Block List| Sì| No| Put Page| Sì| Sì| Get Page Ranges| Sì| Sì

(*) Lease Blob non modifica l'ETag in un BLOB.

### Concorrenza pessimistica per i BLOB
Per bloccare un BLOB al fine di usarlo in modo esclusivo, è possibile acquisire un [lease](http://msdn.microsoft.com/library/azure/ee691972.aspx) su di esso. Quando si acquisisce un lease, specificarne la durata, che può essere inclusa tra 15 e 60 secondi o essere infinita, il che equivale a un blocco esclusivo. È possibile rinnovare un lease finito per estenderlo e rilasciare qualsiasi lease al termine dell'uso. Il servizio BLOB rilascia automaticamente i lease finiti alla scadenza.

I lease abilitano il supporto di strategie di sincronizzazione diverse, tra cui la scrittura esclusiva/la lettura condivisa, la scrittura esclusiva/la lettura esclusiva e la scrittura condivisa/la lettura esclusiva. In presenza di un lease il servizio di archiviazione applica scritture esclusive (operazioni Put, Set e Delete); tuttavia, garantire l'esclusività per le operazioni di lettura richiede allo sviluppatore di verificare che tutte le applicazioni client usino un ID lease e che un solo client alla volta disponga di un ID lease valido. Le operazioni di lettura che non includono un ID lease determinano letture condivise.

Il seguente frammento C# mostra un esempio relativo all'acquisizione di un lease esclusivo per 30 secondi su un BLOB, all'aggiornamento del contenuto del BLOB e al successivo rilascio del lease. Se è già presente un lease valido sul BLOB quando si prova ad acquisire un nuovo lease, il servizio BLOB restituisce un risultato di stato "HTTP 409 - Conflitto". Il frammento seguente usa un oggetto **AccessCondition** per incapsulare le informazioni relative al lease quando effettua una richiesta per aggiornare il BLOB nel servizio di archiviazione. È possibile scaricare l'intero esempio dalla pagina relativa alla [gestione della concorrenza con Archiviazione di Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Se si prova a effettuare un'operazione di scrittura su un BLOB con lease senza passare l'ID lease, la richiesta ha esito negativo con un errore 412. Si noti che la richiesta ha esito negativo con un errore **412** anche se il lease scade prima di chiamare il metodo **UploadText** ma si passa comunque l'ID. Per altre informazioni sulla gestione degli ID lease e dei tempi di scadenza del lease, vedere la documentazione REST [Lease Blob](http://msdn.microsoft.com/library/azure/ee691972.aspx).

Le seguenti operazioni BLOB possono usare i lease per gestire la concorrenza pessimistica:


-   Put Blob
-	Get Blob
-	Get Blob Properties
-	Set Blob Properties
-	Get Blob Metadata
-	Set Blob Metadata
-	Delete Blob
-	Put Block
-	Put Block List
-	Get Block List
-	Put Page
-	Get Page Ranges
-	Snapshot Blob: ID lease facoltativo se esiste un lease
-	Copy Blob: ID lease obbligatorio se esiste un lease nel BLOB di destinazione
-	Abort Copy Blob: ID lease obbligatorio se esiste un lease infinito nel BLOB di destinazione
-	Lease Blob  

### Concorrenza pessimistica per i contenitori
I lease sui contenitori abilitano il supporto delle stesse strategie di sincronizzazione dei BLOB (scrittura esclusiva/lettura condivisa, scrittura esclusiva/lettura esclusiva e scrittura condivisa/lettura esclusiva); tuttavia, a differenza dei BLOB il servizio di archiviazione applica l'esclusività solo alle operazioni Delete. Per eliminare un contenitore con un lease attivo, un client deve includere l'ID lease attivo con la richiesta di eliminazione. Tutte le altre operazioni contenitore hanno esito positivo su un contenitore con lease senza includere l'ID lease, nel qual caso sono operazioni condivise. Se è richiesta l'esclusività di un'operazione di aggiornamento (Put o Set) o di lettura, gli sviluppatori devono garantire che tutti i client usino un ID lease e che un solo client alla volta abbia un ID lease valido.

Le operazioni contenitore seguenti possono usare i lease per gestire la concorrenza pessimistica:

-	Delete Container
-	Get Container Properties
-	Get Container Metadata
-	Set Container Metadata
-	Get Container ACL
-	Set Container ACL
-	Lease Container  

Per altre informazioni, vedere:

- [Specifica di intestazioni condizionali per le operazioni del servizio BLOB](http://msdn.microsoft.com/library/azure/dd179371.aspx)
- [Lease Container](http://msdn.microsoft.com/library/azure/jj159103.aspx)
- [Lease Blob](http://msdn.microsoft.com/library/azure/ee691972.aspx)

## Gestione della concorrenza nel servizio tabelle
Il servizio tabelle usa i controlli della concorrenza ottimistica come comportamento predefinito quando si usano entità, a differenza del servizio BLOB in cui è necessario scegliere esplicitamente di eseguire controlli di concorrenza ottimistica. L'altra differenza tra il servizio tabelle e il servizio BLOB risiede nel fatto che con il primo è possibile gestire solo il comportamento di concorrenza delle entità, mentre con il servizio BLOB è possibile gestire la concorrenza sia dei contenitori sia dei BLOB.

Per usare la concorrenza ottimistica e controllare se un altro processo ha modificato un'entità da quando questa è stata recuperata dal servizio di archiviazione tabelle, è possibile usare il valore ETag ricevuto quando il servizio tabelle restituisce un'entità. Il processo è il seguente:

1.	Recuperare un'entità da un servizio di archiviazione tabelle; la risposta include un valore ETag che identifica l'identificatore corrente associato all'entità nel servizio di archiviazione.
2.	Quando si aggiorna l'entità, includere il valore ETag ricevuto al passaggio 1 nell'intestazione **If-Match** obbligatoria della richiesta inviata al servizio.
3.	Il servizio confronta il valore ETag nella richiesta con il valore ETag corrente dell'entità.
4.	Se il valore ETag corrente del BLOB è diverso dall'ETag nell'intestazione **If-Match** obbligatoria nella richiesta, il servizio restituisce un errore 412 al client. Ciò indica al cliente che un altro processo ha aggiornato l'entità da quando questa è stata recuperata dal client.
5.	Se il valore ETag corrente dell'entità è uguale all'ETag nell'intestazione **If-Match** obbligatoria della richiesta o se l'intestazione **If-Match** contiene il carattere jolly (*), il servizio esegue l'operazione richiesta e aggiorna il valore ETag corrente dell'entità per indicare che è stato aggiornato.

Si noti che, a differenza del servizio BLOB, il servizio tabelle richiede che il client includa un'intestazione **If-Match** nelle richieste di aggiornamento. Tuttavia, è possibile forzare un aggiornamento condizionale (strategia della prevalenza dell'ultima scrittura) e ignorare i controlli della concorrenza se il client imposta l'intestazione **If-Match** sul carattere jolly (*) nella richiesta.

Il seguente frammento C# mostra un'entità del cliente precedentemente creata o recuperata con l'aggiornamento dell'indirizzo di posta elettronica. L'operazione iniziale di inserimento o recupero archivia il valore ETag nell'oggetto del cliente e, poiché l'esempio usa la stessa istanza di oggetto quando esegue l'operazione di sostituzione, invia automaticamente il valore ETag alla tabella, consentendo al servizio di verificare l'eventuale presenza di violazioni della concorrenza. Se un altro processo ha aggiornato l'entità nella tabella di archiviazione, il servizio restituisce un messaggio di stato HTTP 412 (Condizione preliminare non riuscita). È possibile scaricare l'intero esempio dalla pagina relativa alla [gestione della concorrenza con Archiviazione di Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Per disabilitare esplicitamente il controllo della concorrenza, è necessario impostare la proprietà **ETag** dell'oggetto **employee** su “*” prima di eseguire l'operazione di sostituzione.

customer.ETag = "*";

Nella tabella seguente sono riepilogate le modalità in cui le operazioni delle entità di tabella usano i valori ETag:

Operazione |Restituisce il valore ETag |Richiede l'intestazione della richiesta If-Match|
------------|-------------------|--------------------------------|
Query Entities|	Sì|	No|
Insert Entity|	Sì|	No|
Update Entity|	Sì|	Sì|
Merge Entity|	Sì|	Sì|
Delete Entity|	No|	Sì|
Insert or Replace Entity|	Sì|	No|
Insert or Merge Entity|	Sì|	No

SI noti che le operazioni **Insert or Replace Entity** e **Insert or Merge Entity** *non* eseguono alcun controllo di concorrenza perché non inviano un valore ETag al servizio tabelle.

In generale gli sviluppatori che usano tabelle devono fare affidamento sulla concorrenza ottimistica quando sviluppano applicazioni scalabili. Se è necessario un blocco pessimistico, un possibile approccio che gli sviluppatori possono adottare quando effettuano l'accesso alle tabelle è di assegnare un BLOB designato per ciascuna tabella e provare ad acquisire un lease sul BLOB prima di eseguire operazioni sulla tabella. Adottando questo approccio è necessario che l'applicazione verifichi che tutti i percorsi di accesso ai dati ottengano il lease prima di eseguire operazioni sulla tabella. Occorre inoltre notare che il tempo di lease minimo è di 15 secondi, pertanto è necessario prestare una particolare attenzione alla scalabilità.

Per altre informazioni, vedere:

- [Operazioni sulle entità](http://msdn.microsoft.com/library/azure/dd179375.aspx)  

## Gestione della concorrenza nel servizio di accodamento
Uno scenario in cui la concorrenza rappresenta un problema nel servizio di accodamento è quello in cui più client recuperano i messaggi da una coda. Quando un messaggio viene recuperato dalla coda, la risposta include il messaggio e un valore di ricezione POP, necessario per eliminare il messaggio. Il messaggio non viene eliminato automaticamente dalla coda, ma dopo essere stato recuperato non è visibile agli altri client per l'intervallo di tempo specificato dal parametro visibilitytimeout. Il client che recupera il messaggio lo elimina dopo che è stato elaborato e prima dell'ora specificata dall'elemento TimeNextVisible della risposta, calcolata in base al valore del parametro visibilitytimeout. Il valore di visibilitytimeout viene aggiunto all'ora in cui viene recuperato il messaggio per determinare il valore di TimeNextVisible.

Il servizio di accodamento non offre il supporto della concorrenza ottimistica o pessimistica e per questo motivo i client che elaborano messaggi recuperati da una coda devono garantire che vengano recuperati in un modo idempotente. Una strategia in cui prevale l'ultima scrittura viene usata per aggiornare operazioni quali SetQueueServiceProperties, SetQueueMetaData, SetQueueACL e UpdateMessage.

Per altre informazioni, vedere:

- [API REST del servizio di accodamento](http://msdn.microsoft.com/library/azure/dd179363.aspx)
- [Get Messages](http://msdn.microsoft.com/library/azure/dd179474.aspx)  

## Gestione della concorrenza nel servizio file
È possibile accedere al servizio file usando due diversi endpoint di protocollo: SMB e REST. Il servizio REST non presenta il supporto per il blocco ottimistico o pessimistico e tutti gli aggiornamenti seguiranno una strategia basata sulla prevalenza dell'ultima scrittura. I client SMB che montano condivisioni file possono usare meccanismi di blocco del file system per gestire l'accesso ai file condivisi, inclusa la possibilità di eseguire il blocco pessimistico. Quando un client SMB apre un file, specifica sia l'accesso al file sia la modalità di condivisione. L'impostazione di un'opzione di accesso al file "in scrittura" o "in lettura/scrittura" insieme a una modalità di condivisione file impostata su "nessuna" determinerà il blocco del file da parte di un client SMB fino alla chiusura del file. Se si prova a eseguire l'operazione REST su un file in cui un client SMB ha applicato un blocco, il servizio REST restituirà il codice di stato 409 (Conflitto) con il codice errore SharingViolation.

Quando un client SMB apre un file per eliminarlo, lo contrassegna come in attesa dell'eliminazione fino a quando tutti gli altri handle aperti del client SMB su tale file vengono chiusi. Quando un file viene contrassegnato come in attesa di eliminazione, qualsiasi operazione REST su tale file restituirà il codice di stato 409 (Conflitto) con codice errore SMBDeletePending. Il codice di stato 404 (Non trovato) non viene restituito perché è il client SMB può rimuovere il flag di eliminazione in sospeso prima di chiudere il file. In altre parole, il codice di stato 404 (Non trovato) è previsto solo dopo la rimozione del file. Si noti che se un file si trova in uno stato di attesa dell'eliminazione da parte del client SMB non sarà incluso nei risultati degli elenchi di file. Inoltre, le operazioni di eliminazione dei file e di eliminazione della directory REST vengono sottoposte a commit in modo atomico e non determinano lo stato di eliminazione in sospeso.

Per altre informazioni, vedere:

- [Managing File Locks](http://msdn.microsoft.com/library/azure/dn194265.aspx)  

## Riepilogo e passaggi successivi
Il servizio di archiviazione di Microsoft Azure è stato progettato per soddisfare le esigenze delle applicazioni online più complesse senza obbligare gli sviluppatori a scendere a compromessi o a ridefinire presupposti di progettazione chiave dati per scontate, come la concorrenza e la coerenza dei dati.

Per l'applicazione di esempio completa alla quale si fa riferimento in questo blog, vedere:

- [Gestione della concorrenza con l'archiviazione di Azure - Applicazione di esempio](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Per altre informazioni sull'archiviazione di Azure, vedere:

- [Home page di Archiviazione di Microsoft Azure](https://azure.microsoft.com/services/storage/)
- [Introduzione ad Archiviazione di Azure](storage-introduction.md)
- Introduzione all'archiviazione per [BLOB](storage-dotnet-how-to-use-blobs.md), [tabelle](storage-dotnet-how-to-use-tables.md), [code](storage-dotnet-how-to-use-queues.md) e [file](storage-dotnet-how-to-use-files.md)
- Architettura di archiviazione - [Archiviazione di Azure: un servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

<!---HONumber=AcomDC_0224_2016-->