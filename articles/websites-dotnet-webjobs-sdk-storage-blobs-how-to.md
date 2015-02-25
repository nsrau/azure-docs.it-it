<properties 
	pageTitle="Come usare il servizio di archiviazione BLOB di Azure con WebJobs SDK" 
	description="Informazioni su come usare il servizio di archiviazione BLOB di Azure con WebJobs SDK. Attivare un processo quando viene visualizzato un nuovo BLOB in un contenitore e gestire  'poison blobs'." 
	services="web-sites, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/15/2014" 
	ms.author="tdykstra"/>

# Come usare il servizio di archiviazione BLOB di Azure con WebJobs SDK

Questa guida fornisce esempi di codice C# che illustrano come attivare un processo quando viene creato o aggiornato un BLOB di Azure. Gli esempi di codice usano [WebJobs SDK](../websites-dotnet-webjobs-sdk/) versione 1.x.

Per gli esempi di codice che illustrano come creare BLOB, vedere [Come usare il servizio di archiviazione di accodamento di Azure con WebJobs SDK](../websites-dotnet-webjobs-sdk-storage-queues-how-to/). 
		
La guida presuppone che si sappia [come creare un progetto di processo Web in Visual Studio con stringhe di connessione che puntano all'account di archiviazione](../websites-dotnet-webjobs-sdk-get-started/).

## Sommario

-   [Come attivare una funzione quando viene creato o aggiornato un BLOB](#trigger)
	- Singolo segnaposto per il nome di BLOB con estensione
	- Segnaposto di nome ed estensione di BLOB separati
-   [Tipi usati da BlobTrigger](#types)
-   [Ottenere contenuto di BLOB di testo tramite associazione alla stringa](#string)
-   [Ottenere contenuto di BLOB serializzato tramite ICloudBlobStreamBinder](#icbsb)
-   [Come gestire i BLOB non elaborabili](#poison)
-   [Algoritmo di polling di BLOB](#polling)
-   [Conferme di BLOB](#receipts)
-   [Argomenti correlati trattati dall'articolo sulle code](#queues)
-   [Passaggi successivi](#nextsteps)

## <a id="trigger"></a> Come attivare una funzione quando viene creato o aggiornato un BLOB

Questa sezione illustra come usare l'attributo  `BlobTrigger`. 

### Singolo segnaposto per il nome di BLOB con estensione  

Il seguente esempio di codice copia BLOB di testo visualizzati nel contenitore  *input* nel contenitore  *output*:

		public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
		    [Blob("output/{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

Il costruttore di attributo usa un parametro di stringa che specifica il nome del contenitore e un segnaposto per il nome del BLOB. Se in questo esempio viene creato un BLOB denominato  *Blob1.txt* nel contenitore  *input*, la funzione crea un BLOB denominato  *Blob1.txt* nel contenitore  *output*. 

È possibile specificare un modello di nome con il segnaposto del nome di BLOB, come illustrato nel seguente esempio di codice:

		public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
		    [Blob("output/copy-{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

Questo codice copia solo BLOB con nomi che iniziano con "original-". Ad esempio, *original-Blob1.txt* nel contenitore  *input* viene copiato in  *copy-Blob1.txt* nel contenitore  *output*.

Se è necessario specificare un modello di nome per i nomi di BLOB con parentesi graffe nel nome, raddoppiare le parentesi graffe. Se ad esempio si desidera trovare i BLOB nel contenitore  *images* che hanno nomi simile al seguente:

		{20140101}-soundfile.mp3

Usare questa soluzione per il modello:

		images/{{20140101}}-{name}

Nell'esempio il valore del segnaposto  *name* sarebbe  *soundfile.mp3*. 

### Segnaposto di nome ed estensione di BLOB separati

Il seguente esempio di codice modifica l'estensione del file mentre copia i BLOB che vengono visualizzati nel contenitore  *input* nel contenitore  *output*. Il codice registra l'estensione del BLOB  *input* e imposta l'estensione del BLOB  *output* su *.txt*.

		public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
		    [Blob("output/{name}.txt")] out string output,
		    string name,
		    string ext,
		    TextWriter logger)
		{
		    logger.WriteLine("Blob name:" + name);
		    logger.WriteLine("Blob extension:" + ext);
		    output = input.ReadToEnd();
		}

## <a id="types"></a> Tipi che possono essere associati a BLOB

È possibile usare l'attributo  `BlobTrigger` sui seguenti tipi:

* `string`
* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* Altri tipi deserializzati da [ICloudBlobStreamBinder](#icbsb) 

Se si desidera usare direttamente l'account di archiviazione di Azure, è anche possibile aggiungere un parametro  `CloudStorageAccount` alla firma del metodo.

## <a id="string"></a> Ottenere contenuto di BLOB di testo tramite associazione alla stringa

Se sono previsti BLOB di testo, è possibile applicare  `BlobTrigger` a un parametro  `string`. Il seguente esempio di codice associa un BLOB di testo a un parametro  `string` denominato  `logMessage`. La funzione usa tale parametro per scrivere il contenuto del BLOB nel dashboard WebJobs SDK. 
 
		public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
		    string name, 
		    TextWriter logger)
		{
		     logger.WriteLine("Blob name: {0}", name);
		     logger.WriteLine("Content:");
		     logger.WriteLine(logMessage);
		}

## <a id="icbsb"></a> Ottenere contenuto di BLOB serializzato tramite ICloudBlobStreamBinder

Il seguente esempio di codice usa una classe che implementa  `ICloudBlobStreamBinder` per abilitare l'attributo  `BlobTrigger` per associare un BLOB al tipo  `WebImage`.

		public static void WaterMark(
		    [BlobTrigger("images3/{name}")] WebImage input,
		    [Blob("images3-watermarked/{name}")] out WebImage output)
		{
		    output = input.AddTextWatermark("WebJobs SDK", 
		        horizontalAlign: "Center", verticalAlign: "Middle",
		        fontSize: 48, opacity: 50);
		}
		public static void Resize(
		    [BlobTrigger("images3-watermarked/{name}")] WebImage input,
		    [Blob("images3-resized/{name}")] out WebImage output)
		{
		    var width = 180;
		    var height = Convert.ToInt32(input.Height * 180 / input.Width);
		    output = input.Resize(width, height);
		}

Il codice di associazione  `WebImage` viene fornito in una classe  `WebImageBinder` che deriva da  `ICloudBlobStreamBinder`.

		public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
		{
		    public Task<WebImage> ReadFromStreamAsync(Stream input, 
		        System.Threading.CancellationToken cancellationToken)
		    {
		        return Task.FromResult<WebImage>(new WebImage(input));
		    }
		    public Task WriteToStreamAsync(WebImage value, Stream output,
		        System.Threading.CancellationToken cancellationToken)
		    {
		        var bytes = value.GetBytes();
		        return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
		    }
		}

## <a id="poison"></a> Come gestire i BLOB non elaborabili

Quando una funzione  `BlobTrigger` ha esito negativo, l'SDK la chiama nuovamente, nel caso in cui il problema sia stato causato da un errore temporaneo. Se l'errore è causato dal contenuto del BLOB, la funzione ha esito negativo ogni volta che tenta di elaborare il BLOB. Per impostazione predefinita, l'SDK chiama una funzione fino a 5 volte per uno specifico BLOB. Se il quinto tentativo ha esito negativo, l'SDK aggiunge un messaggio a una coda denominata  *webjobs-blobtrigger-poison*.

Il numero massimo di tentativi è configurabile. La stessa impostazione [MaxDequeueCount](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#configqueue)  viene usata per la gestione dei BLOB non elaborabili e dei messaggi di coda non elaborabili. 

Il messaggio di coda per i BLOB non elaborabili è un oggetto JSON che contiene le seguenti proprietà:

* FunctionId (nel formato *{WebJob name}*.Functions.*{Function name}*, ad esempio: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" o "PageBlob")
* ContainerName
* BlobName
* ETag (identificatore di versione BLOB, ad esempio: "0x8D1DC6E70A277EF")

Nel seguente esempio di codice la funzione  `CopyBlob` contiene codice che ne determina l'esito negativo ogni volta che viene chiamata. Dopo che la funzione è stata chiamata dall'SDK per il numero massimo di tentativi, viene creato nella coda di BLOB non elaborabili un messaggio che viene elaborato dalla funzione  `LogPoisonBlob`. 

		public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
		    [Blob("textblobs/output-{name}")] out string output)
		{
		    throw new Exception("Exception for testing poison blob handling");
		    output = input.ReadToEnd();
		}
		
		public static void LogPoisonBlob(
		[QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
		    TextWriter logger)
		{
		    logger.WriteLine("FunctionId: {0}", message.FunctionId);
		    logger.WriteLine("BlobType: {0}", message.BlobType);
		    logger.WriteLine("ContainerName: {0}", message.ContainerName);
		    logger.WriteLine("BlobName: {0}", message.BlobName);
		    logger.WriteLine("ETag: {0}", message.ETag);
		}

L'SDK deserializza automaticamente il messaggio JSON. Questa è la classe  `PoisonBlobMessage`: 

		public class PoisonBlobMessage
		{
		    public string FunctionId { get; set; }
		    public string BlobType { get; set; }
		    public string ContainerName { get; set; }
		    public string BlobName { get; set; }
		    public string ETag { get; set; }
		}

### <a id="polling"></a> Algoritmo di polling di BLOB

WebJobs SDK analizza tutti i contenitori specificati da attributi  `BlobTrigger` all'avvio dell'applicazione. In un account di archiviazione di grandi dimensioni l'analisi può richiedere molto tempo, pertanto potrebbe trascorrere del tempo prima che vengono trovati nuovi BLOB e vengano eseguite le funzioni  `BlobTrigger`.

Per rilevare BLOB nuovi o modificati dopo l'avvio dell'applicazione, l'SDK legge periodicamente i log di archiviazione dei BLOB. I log dei BLOB vengono inseriti nel buffer e vengono scritti fisicamente solo ogni 10 minuti circa, pertanto può esistere un ritardo significativo tra la creazione o l'aggiornamento di un BLOB e l'esecuzione della funzione  `BlobTrigger` corrispondente. 

Si verifica un'eccezione per i BLOB creati tramite l'attributo  `Blob`. Quando WebJobs SDK crea un nuovo BLOB, lo passa immediatamente a tutte le funzioni  `BlobTrigger` corrispondenti. Se pertanto si dispone di una catena di input e output di BLOB, l'SDK può elaborarli in modo efficiente. Se invece si desidera una bassa latenza per l'esecuzione delle funzioni di elaborazione dei BLOB creati o aggiornati in altri modi, è consigliabile usare  `QueueTrigger` anziché  `BlobTrigger`.

### <a id="receipts"></a> Conferme di BLOB

WebJobs SDK verifica che nessuna funzione  `BlobTrigger` venga chiamata più volte per lo stesso BLOB nuovo o aggiornato. Questa operazione viene eseguita gestendo  *blob receipts* per determinare se una versione di BLOB specifica è stata elaborata.

Le conferme di BLOB vengono archiviate in un contenitore denominato  *azure-webjobs-hosts* nell'account di archiviazione di Azure specificato dalla stringa di connessione AzureWebJobsStorage. Una conferma di BLOB contiene le seguenti informazioni:

* La funzione chiamata per il BLOB ("*{WebJob name}*.Functions.*{Function name}*", ad esempio: "WebJob1.Functions.CopyBlob")
* Il nome del contenitore
* Il tipo di BLOB ("BlockBlob" o "PageBlob")
* Il nome del BLOB
* Il valore ETag (identificatore di valore di BLOB, ad esempio: "0x8D1DC6E70A277EF")

Se si desidera forzare la rielaborazione di un BLOB, è possibile eliminare manualmente la conferma di BLOB per tale BLOB dal contenitore  *azure-webjobs-hosts*.

## <a id="queues"></a>Argomenti correlati trattati dall'articolo sulle code

Per informazioni su come gestire l'elaborazione di BLOB attivata da un messaggio di coda o per scenari di WebJobs SDK non specifici dell'elaborazione di BLOB, vedere [Come usare il servizio di archiviazione di accodamento di Azure con WebJobs SDK](../websites-dotnet-webjobs-sdk-storage-queues-how-to/). 

Tra gli argomenti correlati trattati nell'articolo sono inclusi i seguenti:

* Funzioni asincrone
* Istanze multiple
* Arresto normale
* Usare gli attributi WebJobs SDK nel corpo di una funzione
* Impostare le stringhe di connessione SDK nel codice.
* Impostare i valori per i parametri del costruttore WebJobs SDK nel codice
* Configurare  `MaxDequeueCount` per la gestione di BLOB non elaborabili
* Attivare manualmente una funzione
* Scrivere log

## <a id="nextsteps"></a> Passaggi successivi

Questa guida ha fornito esempi di codice che illustrano come gestire scenari comuni per l'uso di BLOB di Azure. Per altre informazioni su come usare i processi Web Azure e su WebJobs SDK, vedere le [risorse consigliate per i processi Web Azure](http://go.microsoft.com/fwlink/?linkid=390226).


<!--HONumber=42-->
