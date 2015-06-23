<properties 
	pageTitle="Indicizzazione di file multimediali con Azure Media Indexer" 
	description="Azure Media Indexer consente di rendere disponibile per la ricerca il contenuto dei file multimediali e di generare una trascrizione full-text per i sottotitoli codificati e le parole chiave. Questo argomento illustra come usare Media Indexer." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/25/2015" 
	ms.author="juliako"/>


# Indicizzazione di file multimediali con Azure Media Indexer

Questo articolo fa parte della serie [Flusso di lavoro Video on Demand di Servizi multimediali](media-services-video-on-demand-workflow.md) . 

Azure Media Indexer consente di rendere disponibile per la ricerca il contenuto dei file multimediali e di generare una trascrizione full-text per i sottotitoli codificati e le parole chiave. È possibile elaborare un file multimediale o più file multimediali in un batch.  

>[AZURE.NOTE] Durante l'indicizzazione dei contenuti, assicurarsi di usare file multimediali con contenuto vocale molto chiaro (senza musica, rumore, effetti o fruscio del microfono). Alcuni esempi di contenuto appropriato includono riunioni registrate, lezioni o presentazioni. Il seguente contenuto potrebbe non essere adatto per l'indicizzazione: film, programmi televisivi, contenuto con una combinazione di audio ed effetti sonori e contenuto registrato di scarsa qualità che presenta rumori di fondo (fruscio).


Un processo di indicizzazione genera quattro output per ogni file di indicizzazione:

- File CC (Closed Caption) in formato SAMI.
- File CC (Closed Caption) in formato TTML (Timed Text Markup Language).

	I file SAMI e TTML includono un tag denominato Recognizability, che assegna un punteggio a un processo di indicizzazione in base alla riconoscibilità del contenuto vocale nel video di origine.  È possibile usare il valore di Recognizability per esaminare i file di output ai fini dell'usabilità. Un punteggio basso indica che i risultati dell'indicizzazione sono scarsi a causa della qualità dell'audio.
- File di parole chiave (XML).
- File BLOB di indicizzazione audio (AIB, Audio Indexing Blob) da usare con SQL Server.
	
	Per altre informazioni, vedere [Uso dei file AIB con Azure Media Indexer e SQL Server](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).


Questo argomento illustra come creare processi di indicizzazione per **indicizzare un asset** e **indicizzare più file**.

Per gli aggiornamenti più recenti relativi ad Azure Media Indexer, vedere i [blog di Servizi multimediali](http://azure.microsoft.com/blog/topics/media-services/).

## Uso di file configurazione e manifesto per l'indicizzazione delle attività

È possibile specificare più informazioni per le attività di indicizzazione usando una configurazione di attività. Ad esempio, è possibile specificare quali metadati usare per il file multimediale. Questi metadati vengono usati dal modulo di gestione del linguaggio per espandere il vocabolario e migliorano notevolmente la precisione del riconoscimento vocale.

È anche possibile elaborare più file multimediali contemporaneamente usando un file manifesto.

Per altre informazioni, vedere [Set di impostazioni per Azure Media Indexer](https://msdn.microsoft.com/library/azure/dn783454.aspx).

## Indicizzare un asset

Il seguente metodo carica un file multimediale come asset e crea un processo per indicizzare l'asset.

Si noti che, qualora non venga specificato un file di configurazione, il file multimediale verrà indicizzato con tutte le impostazioni predefinite.
	
	static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
	{
	    // Create an asset and upload the input media file to storage.
	    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
	        "My Indexing Input Asset",
	        AssetCreationOptions.None);
	
	    // Declare a new job.
	    IJob job = _context.Jobs.Create("My Indexing Job");
	
	    // Get a reference to the Azure Media Indexer.
	    string MediaProcessorName = "Azure Media Indexer",
	    IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);
	
	    // Read configuration from file if specified.
	    string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);
	
	    // Create a task with the encoding details, using a string preset.
	    ITask task = job.Tasks.AddNew("My Indexing Task",
	        processor,
	        configuration,
	        TaskOptions.None);
	
	    // Specify the input asset to be indexed.
	    task.InputAssets.Add(asset);
	
	    // Add an output asset to contain the results of the job. 
	    task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);
	
	    // Use the following event handler to check job progress.  
	    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
	
	    // Launch the job.
	    job.Submit();
	
	    // Check job execution and wait for job to finish. 
	    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
	    progressJobTask.Wait();
	
	    // If job state is Error, the event handling 
	    // method for job progress should log errors.  Here we check 
	    // for error state and exit if needed.
	    if (job.State == JobState.Error)
	    {
	        Console.WriteLine("Exiting method due to job error.");
	        return false;
	    }
	
	    // Download the job outputs.
	    DownloadAsset(task.OutputAssets.First(), outputFolder);
	
	    return true;
	}
	
	static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
	{
	    IAsset asset = _context.Assets.Create(assetName, options);
	
	    var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
	    assetFile.Upload(filePath);
	
	    return asset;
	}
	        
	static void DownloadAsset(IAsset asset, string outputDirectory)
	{
	    foreach (IAssetFile file in asset.AssetFiles)
	    {
	        file.Download(Path.Combine(outputDirectory, file.Name));
	    }
	}
	
	static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
	    var processor = _context.MediaProcessors
	    .Where(p => p.Name == mediaProcessorName)
	    .ToList()
	    .OrderBy(p => new Version(p.Version))
	    .LastOrDefault();
	
	    if (processor == null)
	        throw new ArgumentException(string.Format("Unknown media processor",
	                                                   mediaProcessorName));
	
	    return processor;
	} 
	
### <a id="output_files"></a>File di output

Il processo di indicizzazione genera i seguenti file di output. I file verranno archiviati nel primo asset di output.


<table border="1">
<tr><th>Nome file</th><th>Descrizione</th></tr>
<tr><td>InputFileName.aib </td>
<td>File BLOB di indicizzazione audio.<br/><br/>
Un file AIB (Audio Indexing Blob) è un file binario in cui è possibile eseguire ricerche full-text con Microsoft SQL Server.  Questo file è più potente dei semplici file di sottotitoli perché contiene alternative per ogni parola, offrendo funzioni di ricerca più avanzate.
<br/>
<br/>
Per il file è necessaria l'installazione del componente aggiuntivo Indexer SQL su un computer che esegue Microsoft SQL Server 2008 o versioni successive. La ricerca full-text nel file AIB con Microsoft SQL Server restituisce risultati più precisi rispetto ai file di sottotitoli codificati generati da WAMI. Questo perché il file AIB contiene parole alternative con un suono simile, mentre i file di sottotitoli codificati contengono la parola con la massima probabilità per ogni segmento dell'audio. Se la ricerca di descrizioni vocali ha un'importanza molto rilevante, si consiglia di usare il file AIB insieme a Microsoft SQL Server.
<br/><br/>
Per scaricare il componente aggiuntivo, fare clic su <a href="http://aka.ms/indexersql">Componente aggiuntivo SQL Azure Media Indexer</a>.
<br/><br/>
È anche possibile usare altri motori di ricerca come Apache Lucene/Solr per indicizzare semplicemente il video basato sui file di sottotitoli codificati e sui file XML di parole chiave, ma questo produrrà risultati della ricerca meno precisi.</td></tr>
<tr><td>InputFileName.smi<br/>InputFileName.ttml</td>
<td>File CC (Closed Caption) in formato SAMI e TTML.
<br/><br/>
Possono essere utili per rendere i file audio e video accessibili alle persone con problemi uditivi.
<br/><br/>
I file SAMI e TTML includono un tag denominato <b>Recognizability</b>, che assegna un punteggio a un processo di indicizzazione in base alla riconoscibilità del contenuto vocale nel video di origine.  È possibile usare il valore di <b>Recognizability</b> per esaminare i file di output ai fini dell'usabilità. Un punteggio basso indica che i risultati dell'indicizzazione sono scarsi a causa della qualità dell'audio.</td></tr>
<tr><td>InputFileName.kw.xml</td>
<td>File di parole chiave.
<br/><br/>
Un file di parole chiave è un file XML che contiene parole chiave estratte da contenuti vocali, con informazioni sulla frequenza e sull'offset.
<br/><br/>
È possibile usare il file per vari scopi, ad esempio per eseguire analisi vocali o esporlo ai motori di ricerca come Bing, Google o Microsoft SharePoint per rendere i file multimediali più individuabili o ancora usarlo per produrre annunci pubblicitari più pertinenti.</td></tr>
</table>

Se non tutti i file multimediali di input vengono indicizzati correttamente, il processo di indicizzazione ha esito negativo con codice errore 4000. Per altre informazioni, vedere [Codici di errore](#error_codes).

## Indicizzare più file

Il seguente metodo carica più file multimediali come asset e crea un processo per indicizzare tutti i file in un batch.

Viene creato un file manifesto con estensione lst, che viene caricato nell'asset. Il file manifesto contiene l'elenco di tutti i file di asset. Per altre informazioni, vedere [Set di impostazioni per Azure Media Indexer](https://msdn.microsoft.com/library/azure/dn783454.aspx).
	
	static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
	{
	    // Create an asset and upload to storage.
	    IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
	        "My Indexing Input Asset - Batch Mode",
	        AssetCreationOptions.None);
	
	    // Create a manifest file that contains all the asset file names and upload to storage.
	    string manifestFile = "input.lst";            
	    File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
	    var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
	    assetFile.Upload(manifestFile);
	
	    // Declare a new job.
	    IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");
	
	    // Get a reference to the Azure Media Indexer.
	    string MediaProcessorName = "Azure Media Indexer";
	    IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);
	
	    // Read configuration.
	    string configuration = File.ReadAllText("batch.config");
	
	    // Create a task with the encoding details, using a string preset.
	    ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
	        processor,
	        configuration,
	        TaskOptions.None);
	
	    // Specify the input asset to be indexed.
	    task.InputAssets.Add(asset);
	
	    // Add an output asset to contain the results of the job.
	    task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);
	
	    // Use the following event handler to check job progress.  
	    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
	
	    // Launch the job.
	    job.Submit();
	
	    // Check job execution and wait for job to finish. 
	    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
	    progressJobTask.Wait();
	
	    // If job state is Error, the event handling 
	    // method for job progress should log errors.  Here we check 
	    // for error state and exit if needed.
	    if (job.State == JobState.Error)
	    {
	        Console.WriteLine("Exiting method due to job error.");
	        return false;
	    }
	
	    // Download the job outputs.
	    DownloadAsset(task.OutputAssets.First(), outputFolder);
	
	    return true;
	}
	
	private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
	{
	    IAsset asset = _context.Assets.Create(assetName, options);
	
	    foreach (string filePath in filePaths)
	    {
	        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
	        assetFile.Upload(filePath);
	    }
	
	    return asset;
	}


### File di output

Quando sono presenti più file multimediali di input, WAMI genera un file manifesto per gli output del processo, denominato 'JobResult.txt'. Per ogni file multimediale di input, i file AIB, SAMI, TTML e di parole chiave risultanti verranno numerati in sequenza, come elencato di seguito.

Per le descrizioni dei file di output, vedere [File di output](#output_files). 


<table border="1">
<tr><th>Nome file</th><th>Descrizione</th></tr>
<tr><td>JobResult.txt</td>
<td>Manifesto di output
<br/><br/>Di seguito è riportato il formato del file manifesto di output (JobResult.txt).
<br/><br/>

<table border="1">
<tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>Errore</th></tr>
<tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr>
<tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr>
<tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr>
</table><br/>
Ogni riga rappresenta un singolo file multimediale di input:
<br/><br/>
InputFile: nome del file di asset o URL del file multimediale di input.
<br/><br/>
Alias: nome del file di output corrispondente.
<br/><br/>
MediaLength: lunghezza del file multimediale di input, espressa in secondi. Può essere pari a 0 se l'input è interessato da un errore.
<br/><br/>
Error: indica se il file multimediale viene indicizzato correttamente. 0 in caso di esito positivo, altro valore in caso di esito negativo. Per gli errori concreti, fare riferimento a <a href="#error_codes">Codici di errore</a>.
</td></tr>
<tr><td>Media_1.aib </td>
<td>File #0: file BLOB di indicizzazione audio.</td></tr>
<tr><td>Media_1.smi<br/>Media_1.ttml</td>
<td>File #0: file CC (Closed Caption) in formato SAMI e TTML.</td></tr>
<tr><td>Media_1.kw.xml</td>
<td>File #0: file di parole chiave.</td></tr>
<tr><td>Media_2.aib </td>
<td>File #1: file BLOB di indicizzazione audio.</td></tr>
</table>

Se non tutti i file multimediali di input vengono indicizzati correttamente, il processo di indicizzazione ha esito negativo con codice errore 4000. Per altre informazioni, vedere [Codici di errore](#error_codes).

### Processo parzialmente completato

Se non tutti i file multimediali di input vengono indicizzati correttamente, il processo di indicizzazione ha esito negativo con codice errore 4000. Per altre informazioni, vedere [Codici di errore](#error_codes).


Vengono generati gli stessi output dei processi completati. È possibile fare riferimento al file manifesto di output per scoprire quali file di input hanno avuto esito negativo in base ai valori presenti nella colonna Error. Per i file di input con esito negativo, NON verranno generati i file AIB, SAMI, TTML e delle parole chiave risultanti.


### <a id="error_codes"></a>Codici di errore


<table border="1">
<tr><th>Codice</th><th>Nome</th><th>Possibili cause</th></tr>
<tr><td>2000</td><td>Configurazione non valida.</td><td>Configurazione non valida.</td></tr>
<tr><td>2001</td><td>Asset di input non valido</td><td>Asset di input mancanti o vuoti.</td></tr>
<tr><td>2002</td><td>File manifesto non valido</td><td>Il manifesto è vuoto oppure contiene elementi non validi.</td></tr>
<tr><td>2003</td><td>Impossibile scaricare il file multimediale</td><td>URL non valido nel file manifesto.</td></tr>
<tr><td>2004</td><td>Protocollo non supportato</td><td>Il protocollo dell'URL multimediale non è supportato.</td></tr>
<tr><td>2005</td><td>Tipo di file non supportato</td><td>Il tipo di file multimediale di input non è supportato.</td></tr>
<tr><td>2006</td><td>Troppi file di input</td><td>Sono presenti oltre 10 file nel manifesto di input. </td></tr>
<tr><td>3000</td><td>Impossibile decodificare il file multimediale</td>
<td>Codec multimediale non supportato.
<br/>o<br/>
File multimediale danneggiato.
<br/>o<br/>
Nessun flusso audio nei file multimediali di input.</td></tr>
<tr><td>4000</td><td>Indicizzazione batch parzialmente completata</td><td>Non è stato possibile indicizzare alcuni file multimediali di input. Per altre informazioni, vedere <a href="output_files">File di output</a>.</td></tr>
<tr><td>Altro</td><td>Errori interni</td><td>Contattare il team di supporto.</td></tr>
</table>


## <a id="supported_languages"></a>Lingue supportate

Al momento è supportata solo la lingua inglese.

## Collegamenti correlati

[Uso dei file AIB con Azure Media Indexer e SQL Server](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->


<!--HONumber=52--> 