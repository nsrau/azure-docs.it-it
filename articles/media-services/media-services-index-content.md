<properties
	pageTitle="Indicizzazione di file multimediali con Azure Media Indexer"
	description="Azure Media Indexer consente di rendere disponibile per la ricerca il contenuto dei file multimediali e di generare una trascrizione full-text per i sottotitoli codificati e le parole chiave. Questo argomento illustra come usare Media Indexer."
	services="media-services"
	documentationCenter=""
	authors="Asolanki"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/22/2016"   
	ms.author="adsolank;juliako;johndeu"/>


# Indicizzazione di file multimediali con Azure Media Indexer


Azure Media Indexer consente di rendere disponibile per la ricerca il contenuto dei file multimediali e di generare una trascrizione full-text per i sottotitoli codificati e le parole chiave. È possibile elaborare un file multimediale o più file multimediali in un batch.

>[AZURE.IMPORTANT] Durante l'indicizzazione dei contenuti, assicurarsi di usare file multimediali con contenuto vocale molto chiaro (senza musica, rumore, effetti o fruscio del microfono). Alcuni esempi di contenuto appropriato includono riunioni registrate, lezioni o presentazioni. Il seguente contenuto potrebbe non essere adatto per l'indicizzazione: film, programmi televisivi, contenuto con una combinazione di audio ed effetti sonori e contenuto registrato di scarsa qualità che presenta rumori di fondo (fruscio).


Un processo di indicizzazione può generare i seguenti output:

- Chiusura di file di sottotitoli nei formati seguenti: **SAMI**, **TTML**, e **WebVTT**.

	I file di sottotitoli codificati includono un tag denominato Recognizability, che assegna un punteggio a un processo di indicizzazione in base alla riconoscibilità del contenuto vocale nel video di origine. È possibile usare il valore di Recognizability per esaminare i file di output ai fini dell'usabilità. Un punteggio basso indica che i risultati dell'indicizzazione sono scarsi a causa della qualità dell'audio.
- File di parole chiave (XML).
- File BLOB di indicizzazione audio (AIB, Audio Indexing Blob) da usare con SQL Server.

	Per altre informazioni, vedere [Uso dei file AIB con Azure Media Indexer e SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).


Questo argomento illustra come creare processi di indicizzazione per **indicizzare un asset** e **indicizzare più file**.

Per gli aggiornamenti più recenti relativi ad Azure Media Indexer, vedere i [blog di Servizi multimediali](#preset).

## Uso di file configurazione e manifesto per l'indicizzazione delle attività

È possibile specificare più informazioni per le attività di indicizzazione usando una configurazione di attività. Ad esempio, è possibile specificare quali metadati usare per il file multimediale. Questi metadati vengono usati dal modulo di gestione del linguaggio per espandere il vocabolario e migliorano notevolmente la precisione del riconoscimento vocale. È anche possibile specificare i file di output desiderati.

È anche possibile elaborare più file multimediali contemporaneamente usando un file manifesto.

Per altre informazioni, vedere [Set di impostazioni di attività per Azure Media Indexer](#).

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
	    string MediaProcessorName = "Azure Media Indexer";
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
<!-- __ -->
### <a id="output_files"></a>File di output

Per impostazione definita, il processo di indicizzazione genera i seguenti file di output. I file verranno archiviati nel primo asset di output.

Quando è presente più di un file multimediale di input, Indetex genera un file manifesto per gli output del processo, denominato ‘JobResult.txt’. Per ogni file multimediale di input, i file AIB, SAMI, TTML, WebVTT e di parole chiave risultanti verranno numerati in sequenza e denominati usando l'"alias".

Nome file | Descrizione
----------|------------
__InputFileName.aib__ | File BLOB di indicizzazione audio. <br /><br /> Un file AIB (Audio Indexing Blob) è un file binario in cui è possibile eseguire ricerche full-text con Microsoft SQL Server. Questo file è più potente dei semplici file di sottotitoli perché contiene alternative per ogni parola, offrendo funzioni di ricerca più avanzate. <br/> <br/>Per il file è necessaria l'installazione del componente aggiuntivo Indexer SQL in un computer che esegue Microsoft SQL Server 2008 o versioni successive. La ricerca full-text nel file AIB con Microsoft SQL Server restituisce risultati più precisi rispetto ai file di sottotitoli codificati generati da WAMI. Questo perché il file AIB contiene parole alternative con un suono simile, mentre i file di sottotitoli codificati contengono la parola con la massima probabilità per ogni segmento dell'audio. Se la ricerca di descrizioni vocali ha un'importanza molto rilevante, si consiglia di usare il file AIB insieme a Microsoft SQL Server.<br/><br/> Per scaricare il componente aggiuntivo, fare clic su <a href="http://aka.ms/indexersql">Componente aggiuntivo SQL Azure Media Indexer</a>. <br/><br/>È anche possibile usare altri motori di ricerca come Apache Lucene/Solr per indicizzare semplicemente il video basato sui file di sottotitoli codificati e sui file XML di parole chiave, ma questo produrrà risultati della ricerca meno precisi.
__InputFileName.smi__<br />__InputFileName.ttml__<br />__InputFileName.vtt__ |File di sottotitoli (CC, Closed Caption) in formato SAMI, TTML e WebVTT.<br/><br/>Possono essere usati per rendere i file audio e video accessibili alle persone con problemi di udito.<br/><br/>I file di sottotitoli includono un tag <b>Recognizability</b> che assegna un punteggio a un processo di indicizzazione in base alla riconoscibilità del contenuto vocale nel video di origine. È possibile usare il valore di <b>Recognizability</b> per esaminare i file di output ai fini dell'usabilità. Un punteggio basso indica che i risultati dell'indicizzazione sono scarsi a causa della qualità dell'audio.
__InputFileName.kw.xml<br />InputFileName.info__ |Parola chiave e file di informazioni. <br/><br/>Un file di parole chiave è un file XML che contiene parole chiave estratte da contenuti vocali, con informazioni sulla frequenza e sull'offset. <br/><br/>Il file di informazioni è un file di testo che contiene informazioni granulari su ogni termine riconosciuto. La prima riga è speciale e contiene il punteggio Recognizability. Ogni riga successiva è un elenco separato da tabulazioni dei dati seguenti: ora di inizio, ora di fine, parola/frase, sicurezza. I tempi sono espressi in secondi e la sicurezza è indicata come un numero da 0 a 1 <br/><br/>Riga di esempio: "1.20 1.45 word 0.67" <br/><br/>È possibile usare questi file per vari scopi, ad esempio per eseguire analisi vocali o esporli ai motori di ricerca come Bing, Google o Microsoft SharePoint per rendere i file multimediali più individuabili o persino usarli per produrre annunci pubblicitari più pertinenti.
__JobResult.txt__ |Manifesto di output, presente solo quando si indicizzano più file, contenente le informazioni seguenti:<br/><br/><table border="1"><tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>Error</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media\_3</td><td>600</td><td>0</td></tr></table><br/>



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

### Processo parzialmente completato

Se non tutti i file multimediali di input vengono indicizzati correttamente, il processo di indicizzazione ha esito negativo con codice errore 4000. Per altre informazioni, vedere [Codici di errore](#error_codes).


Vengono generati gli stessi output dei processi completati. È possibile fare riferimento al file manifesto di output per scoprire quali file di input hanno avuto esito negativo in base ai valori presenti nella colonna Error. Per i file di input con esito negativo, NON verranno generati i file AIB, SAMI, TTML, Web TT e delle parole chiave risultanti.

### <a id="preset"></a> Set di impostazioni di attività per Azure Media Indexer

L'elaborazione in Azure Media Indexer può essere personalizzata fornendo un set di impostazioni facoltativo insieme all'attività. La tabella seguente descrive il formato di questo file xml di configurazione.

Nome | Valore richiesto | Descrizione
----|----|---
__input__ | false | File di asset da indicizzare.</p><p>Azure Media Indexer supporta i formati di file multimediale seguenti: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>È possibile specificare uno o più nomi file nell'attributo **name** o **list** dell'elemento **input** (come mostrato sotto). Se non si specifica il file di asset da indicizzare, viene selezionato il file primario. Se non viene impostato alcun file di asset primario, viene indicizzato il primo file nell'asset di input.</p><p>Per specificare in modo esplicito il nome file di asset, eseguire:<br />`<input name="TestFile.wmv">`<br /><br />È anche possibile indicizzare più file di asset per volta (fino a 10 file). A questo scopo:<br /><br /><ol class="ordered"><li><p>Creare un file di testo (file manifesto) con l'estensione lst. </p></li><li><p>Aggiungere un elenco di tutti i nomi file di asset nell'asset di input a questo file manifesto. </p></li><li><p>Aggiungere (caricare) il file manifesto all'asset. </p></li><li><p>Specificare il nome del file manifesto nell'attributo list dell'input.<br />`<input list="input.lst">`</li></ol><br /><br />Nota: se si aggiungono più di 10 file al file manifesto, il processo di indicizzazione avrà esito negativo con codice di errore 2006.
__metadata__ | false | Metadati per uno o più file di asset specificati usati per l'adattamento al vocabolario. Utili per preparare l'indicizzatore per riconoscere le parole di vocabolario non standard, ad esempio i nomi propri.<br />`<metadata key="..." value="..."/>` <br /><br />È possibile specificare i __valori__ per le __chiavi__ predefinite. Attualmente sono supportate le chiavi seguenti:<br /><br />"title" e "description", usate per l'adattamento al vocabolario per perfezionare il modello linguistico del processo e migliorare la precisione del riconoscimento vocale I valori alimentano le ricerche per trovare documenti di testo pertinenti a livello di contesto, usando i contenuti per ampliare il dizionario interno per tutta la durata dell'attività di indicizzazione.<br />`<metadata key="title" value="[Title of the media file]" />`<br />`<metadata key="description" value="[Description of the media file] />"`
__features__ <br /><br /> Aggiunto nella versione 1.2. Attualmente la sola funzionalità supportata è il riconoscimento vocale ("ASR").| false | La funzionalità di riconoscimento vocale ha le chiavi di impostazioni seguenti:<table><tr><th><p>Chiave</p></th> <th><p>Descrizione</p></th><th><p>Valore di esempio</p></th></tr><tr><td><p>Language</p></td><td><p>Linguaggio naturale da riconoscere nel file multimediale.</p></td><td><p>English, Spanish</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>Elenco di valori separati da punto e virgola dei formati di sottotitolo desiderati (se presenti)</p></td><td><p>ttml;sami;webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>Flag booleano che specifica se un file AIB sia obbligatorio o meno (da usare con SQL Server e l'IFilter indicizzatore del cliente). Per altre informazioni, vedere <a href="http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">Uso dei file AIB con Azure Media Indexer e SQL Server</a>.</p></td><td><p>True; False</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Flag booleano che specifica se un file XML di parole chiave sia obbligatorio o meno.</p></td><td><p>True; False. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Flag booleano che specifica se forzare o meno i sottotitoli completi (indipendentemente dal livello di probabilità). </p><p>Il valore predefinito è false e in questo caso le parole e le frasi con un livello di probabilità inferiore al 50% vengono omesse dagli output finali dei sottotitoli e sostituite da puntini di sospensione ("..."). I puntini di sospensione sono utili per il controllo della qualità dei sottotitoli.</p></td><td><p>True; False. </p></td></tr></table>

### <a id="error_codes"></a>Codici di errore

In caso di errore, Azure Media Indexer dovrebbe segnalare uno dei codici di errore seguente:

Codice | Nome | Possibili cause
-----|------|------------------
2000 | Configurazione non valida. | Configurazione non valida.
2001 | Asset di input non valido | Asset di input mancanti o vuoti.
2002 | File manifesto non valido | Il manifesto è vuoto oppure contiene elementi non validi.
2003 | Impossibile scaricare il file multimediale | URL non valido nel file manifesto.
2004 | Protocollo non supportato | Il protocollo dell'URL multimediale non è supportato.
2005 | Tipo di file non supportato | Il tipo di file multimediale di input non è supportato.
2006 | Troppi file di input | Sono presenti oltre 10 file nel manifesto di input.
3000 | Impossibile decodificare il file multimediale | Codec multimediale non supportato <br/>oppure<br/> File multimediale danneggiato <br/>oppure<br/> Nessun flusso audio nei file multimediali di input.
4000 | Indicizzazione batch parzialmente completata | Non è stato possibile indicizzare alcuni file multimediali di input. Per altre informazioni, vedere <a href="#output_files">File di output</a>.
Altro | Errori interni | Contattare il team di supporto. indexer@microsoft.com


## <a id="supported_languages"></a>Lingue supportate

Attualmente, sono supportate le lingue inglese e spagnolo. Per altre informazioni, vedere il [post di blog sulla versione 1.2](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).


##Percorsi di apprendimento di Servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## Collegamenti correlati

[Panoramica di Analisi servizi multimediali di Azure](media-services-analytics-overview.md)

[Uso dei file AIB con Azure Media Indexer e SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[Indicizzazione dei file multimediali con Azure Media Indexer 2 Preview](media-services-process-content-with-indexer2.md)

<!---HONumber=AcomDC_0831_2016-->