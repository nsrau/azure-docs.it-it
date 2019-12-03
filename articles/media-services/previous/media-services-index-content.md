---
title: Indicizzazione di file multimediali con Azure Media Indexer
description: Azure Media Indexer consente di rendere disponibile per la ricerca il contenuto dei file multimediali e di generare una trascrizione full-text per i sottotitoli codificati e le parole chiave. Questo argomento illustra come usare Media Indexer.
services: media-services
documentationcenter: ''
author: Asolanki
manager: femila
editor: ''
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/22/2019
ms.author: juliako
ms.reviewer: johndeu
ms.openlocfilehash: dea31e350ddf4b9dbebfa6a9f802edd256adf2ce
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706426"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Indicizzazione di file multimediali con Azure Media Indexer

> [!NOTE]
> Il processore di contenuti multimediali [Azure Media Indexer](media-services-index-content.md) verrà ritirato il 1 ° ottobre del 2020. [Servizi multimediali di Azure video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) sostituisce questo processore di contenuti multimediali legacy. Per altre informazioni, vedere [eseguire la migrazione da Azure Media Indexer e Azure Media Indexer 2 a servizi multimediali di Azure video Indexer](migrate-indexer-v1-v2.md).

Azure Media Indexer consente di rendere disponibile per la ricerca il contenuto dei file multimediali e di generare una trascrizione full-text per i sottotitoli codificati e le parole chiave. Puoi elaborare un solo file multimediale o più file multimediali in un batch.  

Durante l'indicizzazione dei contenuti, assicurarsi di usare file multimediali con contenuto vocale chiaro (senza musica, rumore, effetti o fruscio del microfono). Alcuni esempi di contenuto appropriato includono riunioni registrate, lezioni o presentazioni. Il seguente contenuto potrebbe non essere adatto per l'indicizzazione: film, programmi televisivi, contenuto con una combinazione di audio ed effetti sonori e contenuto registrato di scarsa qualità che presenta rumori di fondo (fruscio).

Un processo di indicizzazione può generare i seguenti output:

* File didascalia chiusi nei formati seguenti: **TTML**e **WEBVTT**.
  
    I file di sottotitoli codificati includono un tag denominato Recognizability, che assegna un punteggio a un processo di indicizzazione in base alla riconoscibilità del contenuto vocale nel video di origine.  È possibile usare il valore di Recognizability per esaminare i file di output ai fini dell'usabilità. Un punteggio basso indica che i risultati dell'indicizzazione sono scarsi a causa della qualità dell'audio.
* File di parole chiave (XML).

Questo articolo illustra come creare processi di indicizzazione per **indicizzare un asset** e **indicizzare più file**.

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Uso di file configurazione e manifesto per l'indicizzazione delle attività
È possibile specificare più informazioni per le attività di indicizzazione usando una configurazione di attività. Ad esempio, è possibile specificare quali metadati usare per il file multimediale. Questi metadati vengono usati dal modulo di gestione del linguaggio per espandere il vocabolario e migliorano notevolmente la precisione del riconoscimento vocale.  È anche possibile specificare i file di output desiderati.

È anche possibile elaborare più file multimediali contemporaneamente usando un file manifesto.

Per altre informazioni, vedere [Set di impostazioni di attività per Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Indicizzare un asset
Il seguente metodo carica un file multimediale come asset e crea un processo per indicizzare l'asset.

Qualora non venga specificato un file di configurazione, il file multimediale verrà indicizzato con tutte le impostazioni predefinite.

```csharp
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
```

<!-- __ -->
### <a id="output_files"></a>File di output
Per impostazione definita, il processo di indicizzazione genera i seguenti file di output. I file verranno archiviati nel primo asset di output.

Quando è presente più di un file multimediale di input, Indetex genera un file manifesto per gli output del processo, denominato ‘JobResult.txt’. Per ogni file multimediale di input, i file TTML, WebVTT e keyword risultanti sono numerati in modo sequenziale e vengono denominati usando l'"alias".

| Nome file | Description |
| --- | --- |
| **InputFileName.ttml**<br/>**InputFileName.vtt** |File didascalia (CC) chiusi nei formati TTML e WebVTT.<br/><br/>Possono essere utili per rendere i file audio e video accessibili alle persone con problemi uditivi.<br/><br/>I file di sottotitoli codificati includono un tag denominato <b>Recognizability</b>, che assegna un punteggio a un processo di indicizzazione in base alla riconoscibilità del contenuto vocale nel video di origine.  È possibile usare il valore di <b>Recognizability</b> per esaminare i file di output ai fini dell'usabilità. Un punteggio basso indica che i risultati dell'indicizzazione sono scarsi a causa della qualità dell'audio. |
| **InputFileName.kw.xml<br/>InputFileName.info** |Parola chiave e file di informazioni. <br/><br/>Un file di parole chiave è un file XML che contiene parole chiave estratte da contenuti vocali, con informazioni sulla frequenza e sull'offset. <br/><br/>Il file di informazioni è un file di testo che contiene informazioni granulari su ogni termine riconosciuto. La prima riga è speciale e contiene il punteggio Recognizability. Ogni riga successiva è un elenco separato da tabulazioni dei dati seguenti: ora di inizio, ora di fine, parola/frase, sicurezza. I tempi sono espressi in secondi e la sicurezza è indicata come un numero da 0 a 1 <br/><br/>Riga di esempio: "1.20    1.45    word    0.67" <br/><br/>È possibile usare questi file per vari scopi, ad esempio per eseguire analisi vocali o esporli ai motori di ricerca come Bing, Google o Microsoft SharePoint per rendere i file multimediali più individuabili o persino usarli per produrre annunci pubblicitari più pertinenti. |
| **JobResult.txt** |Manifesto di output, presente solo quando si indicizzano più file, contenente le informazioni seguenti:<br/><br/><table border="1"><tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>Errore</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Se non tutti i file multimediali di input vengono indicizzati correttamente, il processo di indicizzazione ha esito negativo con codice errore 4000. Per altre informazioni, vedere [Codici di errore](#error_codes).

## <a name="index-multiple-files"></a>indicizzare più file
Il seguente metodo carica più file multimediali come asset e crea un processo per indicizzare tutti i file in un batch.

Viene creato un file manifesto con estensione LST, che viene caricato nell'asset. Il file manifesto contiene l'elenco di tutti i file di asset. Per altre informazioni, vedere [Set di impostazioni di attività per Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

```csharp
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
```

### <a name="partially-succeeded-job"></a>Processo parzialmente completato
Se non tutti i file multimediali di input vengono indicizzati correttamente, il processo di indicizzazione ha esito negativo con codice errore 4000. Per altre informazioni, vedere [Codici di errore](#error_codes).

Vengono generati gli stessi output dei processi completati. È possibile fare riferimento al file manifesto di output per scoprire quali file di input hanno avuto esito negativo in base ai valori presenti nella colonna Error. Per i file di input non riusciti, i file TTML, WebVTT e keyword risultanti non verranno generati.

### <a id="preset"></a> Set di impostazioni di attività per Azure Media Indexer
L'elaborazione in Azure Media Indexer può essere personalizzata fornendo un set di impostazioni facoltativo insieme all'attività.  La tabella seguente descrive il formato di questo file xml di configurazione.

| name | Valore richiesto | Description |
| --- | --- | --- |
| **input** |false |File di asset che si desidera indicizzare.</p><p>Azure Media Indexer supporta i seguenti formati di file multimediali: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>È possibile specificare il nome di file nell'attributo **name** o **list** dell'elemento **input** (come illustrato di seguito). Se non si specifica il file di asset da indicizzare, viene selezionato il file primario. Se non è impostato alcun file di asset primario, viene indicizzato il primo file dell'asset di input.</p><p>Per specificare in modo esplicito il nome del file di asset, eseguire:<br/>`<input name="TestFile.wmv">`<br/><br/>È anche possibile indicizzare più file di asset contemporaneamente (fino a 10). A tale scopo, effettuare l'operazione seguente:<br/><br/><ol class="ordered"><li><p>Creare un file di testo (file manifesto) con estensione .lst. </p></li><li><p>Aggiungere un elenco di tutti i nomi file di asset nell'asset di input a questo file manifesto. </p></li><li><p>Aggiungere (caricare) il file manifesto nella risorsa.  </p></li><li><p>Specificare il nome del file manifesto nell'attributo dell'elenco di input.<br/>`<input list="input.lst">`</li></ol><br/><br/>Nota: se si aggiungono più di 10 file al file manifesto, il processo di indicizzazione avrà esito negativo con codice di errore 2006. |
| **metadata** |false |Metadati per uno o più file di asset specificati usati per l'adattamento al vocabolario.  Utili per preparare l'indicizzatore per riconoscere le parole di vocabolario non standard, ad esempio i nomi propri.<br/>`<metadata key="..." value="..."/>` <br/><br/>È possibile assegnare i **valori** delle **chiavi** predefinite. Attualmente sono supportate le chiavi seguenti:<br/><br/>"title" e "description", usate per l'adattamento al vocabolario per perfezionare il modello linguistico del processo e migliorare la precisione del riconoscimento vocale.  I valori alimentano le ricerche per trovare documenti di testo pertinenti a livello di contesto, usando i contenuti per ampliare il dizionario interno per tutta la durata dell'attività di indicizzazione.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **Funzionalità** <br/><br/> Aggiunto nella versione 1.2. Attualmente la sola funzionalità supportata è il riconoscimento vocale ("ASR"). |false |La funzionalità di riconoscimento vocale ha le chiavi di impostazioni seguenti:<table><tr><th><p>Chiave</p></th>        <th><p>Description</p></th><th><p>Valore di esempio</p></th></tr><tr><td><p>Linguaggio</p></td><td><p>Linguaggio naturale da riconoscere nel file multimediale,</p></td><td><p>Inglese, spagnolo</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>Un elenco separato da punto e virgola dei formati desiderati per l'output dell'eventuale sottotitolo.</p></td><td><p>TTML; WEBVTT</p></td></tr><tr><td><p></p></td><td><p> </p></td><td><p>True; False</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Flag booleano che specifica se sia o meno necessario un file XML di parole chiave.</p></td><td><p>True; False. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Flag booleano che specifica se forzare o meno i sottotitoli completi (indipendentemente dal livello di probabilità).  </p><p>Il valore predefinito è false e in questo caso le parole e le frasi con un livello di probabilità inferiore al 50% vengono omesse dagli output finali dei sottotitoli e sostituite da puntini di sospensione ("...").  I puntini di sospensione sono utili per il controllo della qualità dei sottotitoli.</p></td><td><p>True; False. </p></td></tr></table> |

### <a id="error_codes"></a>Codici di errore
In caso di errore, Azure Media Indexer dovrebbe segnalare uno dei codici di errore seguente:

| Codice | name | Possibili cause |
| --- | --- | --- |
| 2000 |Configurazione non valida. |Configurazione non valida. |
| 2001 |Asset di input non valido |Asset di input mancanti o vuoti. |
| 2002 |File manifesto non valido |Il manifesto è vuoto oppure contiene elementi non validi. |
| 2003 |Impossibile scaricare il file multimediale |URL non valido nel file manifesto. |
| 2004 |Protocollo non supportato |Il protocollo dell'URL multimediale non è supportato. |
| 2005 |Tipo di file non supportato |Il tipo di file multimediale di input non è supportato. |
| 2006 |Troppi file di input |Sono presenti oltre 10 file nel manifesto di input. |
| 3000 |Impossibile decodificare il file multimediale |Codec multimediale non supportato <br/>Oppure<br/> File multimediale danneggiato <br/>Oppure<br/> Nessun flusso audio nei file multimediali di input. |
| 4000 |Indicizzazione batch parzialmente completata |Non è stato possibile indicizzare alcuni file multimediali di input. Per altre informazioni, vedere <a href="#output_files">File di output</a>. |
| other |Errori interni |Contattare il team di supporto. indexer@microsoft.com |

## <a id="supported_languages"></a>Lingue supportate
Attualmente, sono supportate le lingue inglese e spagnolo.  

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Invia commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Collegamenti correlati
[Panoramica di Analisi servizi multimediali di Azure](media-services-analytics-overview.md)

[Indicizzazione dei file multimediali con Azure Media Indexer 2 Preview](media-services-process-content-with-indexer2.md)

