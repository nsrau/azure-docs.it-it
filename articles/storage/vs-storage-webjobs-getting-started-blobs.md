<properties
	pageTitle="Introduzione all'archiviazione di BLOB e ai servizi relativi a Visual Studio (progetti WebJob) | Microsoft Azure"
	description="Informazioni su come iniziare a usare il servizio di archiviazione di BLOB di Azure in un progetto WebJob dopo aver eseguito la connessione a un account di archiviazione di Azure con i servizi connessi di Visual Studio."
	services="storage"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-getting-started"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/18/2016"
	ms.author="tarcher"/>

# Introduzione all'archiviazione BLOB di Azure e ai servizi relativi a Visual Studio (progetti WebJob)

[AZURE.INCLUDE [storage-try-azure-tools](../../includes/storage-try-azure-tools.md)]

## Panoramica

In questo articolo vengono forniti esempi di codice C# che illustrano come attivare un processo quando viene creato o aggiornato un BLOB di Azure. Gli esempi di codice usano [WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md) versione 1.x. Quando si aggiunge un account di archiviazione a un progetto WebJob tramite la finestra di dialogo **Aggiungi servizi connessi** di Visual Studio, viene installato il pacchetto NuGet di archiviazione di Azure appropriato, i riferimenti .NET appropriati vengono aggiunti al progetto e le stringhe di connessione per l'account di archiviazione vengono aggiornate nel file App.config.



## Come attivare una funzione quando viene creato o aggiornato un BLOB

Questa sezione illustra come usare l'attributo **BlobTrigger**.

 **Nota:** WebJobs SDK esegue la scansione dei file di log per verificare la presenza di BLOB nuovi o modificati. Questo processo è particolarmente lento: una funzione potrebbe non essere attivata per diversi minuti o più dopo la creazione del BLOB. Se l'applicazione deve elaborare BLOB immediatamente, si consiglia di creare un messaggio nella coda quando si crea il BLOB e usare l'attributo [QueueTrigger](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger) anziché l'attributo **BlobTrigger** sulla funzione che elabora il BLOB.

### Singolo segnaposto per il nome di BLOB con estensione  

L'esempio di codice seguente copia i BLOB di testo del contenitore di *input* nel contenitore di *output*:

		public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
		    [Blob("output/{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

Il costruttore dell'attributo usa un parametro di stringa che specifica il nome del contenitore e un segnaposto per il nome del BLOB. In questo esempio, se viene creato un BLOB denominato *Blob1.txt* nel contenitore di *input* la funzione crea un BLOB denominato *Blob1.txt* nel contenitore di *output*.

È possibile specificare un modello di nome con il segnaposto del nome del BLOB, come illustrato nel seguente esempio di codice:

		public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
		    [Blob("output/copy-{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

Questo codice copia solo BLOB con nomi che iniziano con "original-". Ad esempio, *original-Blob1.txt* nel contenitore di *input* viene copiato in *copy-Blob1.txt* nel contenitore di *output*.

Se è necessario specificare un modello di nome per i nomi di BLOB con parentesi graffe nel nome, raddoppiare le parentesi graffe. Se ad esempio si desidera trovare i BLOB nel contenitore *images* con nomi simile al seguente:

		{20140101}-soundfile.mp3

usare questa soluzione per il modello:

		images/{{20140101}}-{name}

Nell'esempio il valore del segnaposto *name* sarebbe *soundfile.mp3*.

### Segnaposto di nome ed estensione di BLOB separati

L'esempio di codice seguente modifica l'estensione del file mentre copia nel contenitore di *input* i BLOB visualizzati nel contenitore di *output*. Il codice registra l'estensione del BLOB di *input* e imposta l'estensione del BLOB di *output* su *.txt*.

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

## Tipi che possono essere associati a BLOB

È possibile usare l'attributo **BlobTrigger** per i tipi seguenti:

* **string**
* **TextReader**
* **Stream**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Altri tipi deserializzati da [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Se si desidera usare direttamente l'account di archiviazione di Azure, è anche possibile aggiungere un parametro **CloudStorageAccount** alla firma del metodo.

## Ottenere contenuto di BLOB di testo tramite associazione alla stringa

Se sono previsti BLOB di testo, è possibile applicare **BlobTrigger** a un parametro **stringa**. L'esempio di codice seguente associa un BLOB di testo a un parametro **stringa** denominato **IogMessage**. La funzione usa tale parametro per scrivere il contenuto del BLOB nel dashboard WebJobs SDK.

		public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
		    string name,
		    TextWriter logger)
		{
		     logger.WriteLine("Blob name: {0}", name);
		     logger.WriteLine("Content:");
		     logger.WriteLine(logMessage);
		}

## Ottenere contenuto di BLOB serializzato tramite ICloudBlobStreamBinder

L'esempio di codice seguente usa una classe che implementa **ICloudBlobStreamBinder** per consentire all'attributo **BlobTrigger** di associare un BLOB al tipo **WebImage**.

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

Il codice di associazione **WebImage** viene fornito in una classe **WebImageBinder** che deriva da **ICloudBlobStreamBinder**.

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

## Come gestire i BLOB non elaborabili

Quando una funzione **BlobTrigger** ha esito negativo, l'SDK la chiama nuovamente in caso in cui il problema sia stato causato da un errore temporaneo. Se il problema è causato dal contenuto del BLOB, la funzione ha esito negativo ogni volta che tenta di elaborare il BLOB. Per impostazione predefinita, l'SDK chiama una funzione fino a cinque volte per un determinato BLOB. Se il quinto tentativo ha esito negativo, l'SDK aggiunge un messaggio a una coda denominata *webjobs-blobtrigger-poison*.

Il numero massimo di tentativi è configurabile. La stessa impostazione [MaxDequeueCount](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue) viene usata per la gestione dei BLOB non elaborabili e per la gestione dei messaggi della coda non elaborabile.

Il messaggio di coda per i BLOB non elaborabili è un oggetto JSON che contiene le seguenti proprietà:

* FunctionId (nel formato *{Nome WebJob}*.Functions.*{Nome funzione}*, ad esempio: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" o "PageBlob")
* ContainerName
* BlobName
* ETag (identificatore di versione del BLOB, ad esempio: "0x8D1DC6E70A277EF")

Nell'esempio di codice seguente la funzione **CopyBlob** contiene codice che ne determina l'esito negativo ogni volta che viene chiamata. Dopo che l'SDK chiama la funzione per il numero massimo di tentativi, nella coda di BLOB non elaborabili viene creato un messaggio elaborato dalla funzione **LogPoisonBlob**.

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

L'SDK deserializza automaticamente il messaggio JSON. Ecco la classe **PoisonBlobMessage**:

		public class PoisonBlobMessage
		{
		    public string FunctionId { get; set; }
		    public string BlobType { get; set; }
		    public string ContainerName { get; set; }
		    public string BlobName { get; set; }
		    public string ETag { get; set; }
		}

### Algoritmo di polling di BLOB

WebJobs SDK analizza tutti i contenitori specificati da attributi **BlobTrigger** all'avvio dell'applicazione. In un account di archiviazione di grandi dimensioni l'analisi può richiedere tempo, pertanto l'individuazione di nuovi BLOB e l'esecuzione delle funzioni **BlobTrigger** potrebbero non essere immediate.

Per rilevare BLOB nuovi o modificati dopo l'avvio dell'applicazione, l'SDK legge periodicamente i log di archiviazione dei BLOB. Tali log dei BLOB vengono inseriti nel buffer e vengono scritti fisicamente solo ogni 10 minuti circa, pertanto si può riscontrare un ritardo significativo tra la creazione o l'aggiornamento di un BLOB e l'esecuzione della funzione **BlobTrigger** corrispondente.

Si verifica un'eccezione per i BLOB creati tramite l'attributo **Blob**. Quando WebJobs SDK crea un nuovo BLOB, lo passa immediatamente a tutte le funzioni **BlobTrigger** corrispondenti. Se pertanto si dispone di una catena di input e output di BLOB, l'SDK può elaborarli in modo efficiente. Se invece si desidera una bassa latenza per l'esecuzione delle funzioni di elaborazione dei BLOB creati o aggiornati in altri modi, è consigliabile usare **QueueTrigger** anziché **BlobTrigger**.

### Conferme di BLOB

WebJobs SDK verifica che nessuna funzione **BlobTrigger** venga chiamata più volte per lo stesso BLOB nuovo o aggiornato. A tale scopo, gestisce *conferme di BLOB* per determinare se una versione di BLOB specifica è stata elaborata.

Le conferme di BLOB vengono archiviate in un contenitore denominato *azure-webjobs-hosts* nell'account di archiviazione di Azure specificato dalla stringa di connessione AzureWebJobsStorage. Una conferma di BLOB contiene le seguenti informazioni:

* La funzione chiamata per il BLOB ("*{Nome WebJob}*.Functions.*{Nome funzione}*", ad esempio: "WebJob1.Functions.CopyBlob")
* Il nome del contenitore
* Il tipo di BLOB ("BlockBlob" o "PageBlob")
* Il nome del BLOB
* Il valore ETag (identificatore di versione del BLOB, ad esempio: "0x8D1DC6E70A277EF")

Se si desidera forzare la rielaborazione di un BLOB, è possibile eliminare manualmente la conferma per tale BLOB dal contenitore *azure-webjobs-hosts*.

## Argomenti correlati trattati dall'articolo sulle code

Per informazioni su come gestire l'elaborazione di BLOB attivata da un messaggio di coda o per scenari di WebJobs SDK non specifici dell'elaborazione di BLOB, vedere [Come usare il servizio di archiviazione di accodamento di Azure con WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md).

Tra gli argomenti correlati trattati nell'articolo sono inclusi i seguenti:

* Funzioni asincrone
* Più istanze
* Arresto normale
* Usare gli attributi di WebJobs SDK nel corpo di una funzione
* Impostare le stringhe di connessione SDK nel codice.
* Impostare i valori per i parametri del costruttore WebJobs SDK nel codice
* Configurare **MaxDequeueCount** per la gestione dei blob non elaborabili.
* Attivare manualmente una funzione
* Scrivere i log

## Passaggi successivi

Questo articolo ha fornito esempi di codice che illustrano come gestire scenari comuni per l'uso di tabelle di Azure. Per altre informazioni su come usare Processi Web di Azure e WebJobs SDK, vedere le [risorse di documentazione di Processi Web di Azure](http://go.microsoft.com/fwlink/?linkid=390226).

<!---HONumber=AcomDC_0720_2016-->