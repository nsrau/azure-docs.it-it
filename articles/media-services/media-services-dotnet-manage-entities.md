
<properties 
	pageTitle="Gestione di asset ed entità correlate con Media Services .NET SDK" 
	description="Informazioni su come gestire asset ed entità correlate con Media Services SDK for .NET." 
	authors="juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="juliako"/>


#Gestione di asset ed entità correlate con Media Services .NET SDK

Questo argomento illustra come eseguire le seguenti attività di gestione di Servizi multimediali:

- Ottenere un riferimento a un asset 
- Ottenere un riferimento a un processo 
- Elencare tutti gli asset 
- Elencare i processi e gli asset 
- Elencare tutti i criteri di accesso 
- Elencare tutti i localizzatori 
- Eliminare un asset 
- Eliminare un processo 
- Eliminare un criterio di accesso 

##Prerequisiti 

Vedere [Configurare l'ambiente](media-services-set-up-computer.md)

##Ottenere un riferimento a un asset

Un'attività comune consiste nell'ottenere un riferimento a un asset esistente in Servizi multimediali. L'esempio di codice seguente mostra come ottenere un riferimento a un asset dalla raccolta Assets sull'oggetto contesto del server, in base all'ID dell'asset. L'esempio di codice seguente usa una query Linq per ottenere un riferimento a un oggetto IAsset.

	static IAsset GetAsset(string assetId)
	{
	    // Use a LINQ Select query to get an asset.
	    var assetInstance =
	        from a in _context.Assets
	        where a.Id == assetId
	        select a;
	    // Reference the asset as an IAsset.
	    IAsset asset = assetInstance.FirstOrDefault();
	
	    return asset;
	}

##Ottenere un riferimento a un processo

Quando si usano attività di elaborazione nel codice di Servizi multimediali, è spesso necessario ottenere un riferimento a un processo esistente in base a un ID. L'esempio di codice seguente mostra come ottenere un riferimento a un oggetto IJob dalla raccolta Jobs. Avviso Può essere necessario ottenere un riferimento a un processo quando si avvia un processo di codifica di lunga esecuzione e si vuole verificare lo stato del processo su un thread. In casi come questo, quando il metodo è restituito da un thread, è necessario recuperare un riferimento aggiornato a un processo.

	static IJob GetJob(string jobId)
	{
	    // Use a Linq select query to get an updated 
	    // reference by Id. 
	    var jobInstance =
	        from j in _context.Jobs
	        where j.Id == jobId
	        select j;
	    // Return the job reference as an Ijob. 
	    IJob job = jobInstance.FirstOrDefault();
	
	    return job;
	}

##Elencare tutti gli asset

Man mano che aumenta il numero degli asset archiviati, è utile elencarli tutti. L'esempio di codice seguente mostra come scorrere la raccolta Assets nell'oggetto contesto del server. Con ogni asset, l'esempio di codice scrive anche alcuni valori delle relative proprietà nella console. Ogni asset, ad esempio, può includere numerosi file multimediali. L'esempio di codice elenca tutti i file associati a ogni asset.



	static void ListAssets()
	{
	    string waitMessage = "Building the list. This may take a few "
	        + "seconds to a few minutes depending on how many assets "
	        + "you have."
	        + Environment.NewLine + Environment.NewLine
	        + "Please wait..."
	        + Environment.NewLine;
	    Console.Write(waitMessage);
	
	    // Create a Stringbuilder to store the list that we build. 
	    StringBuilder builder = new StringBuilder();
	
	    foreach (IAsset asset in _context.Assets)
	    {
	        // Display the collection of assets.
	        builder.AppendLine("");
	        builder.AppendLine("******ASSET******");
	        builder.AppendLine("Asset ID: " + asset.Id);
	        builder.AppendLine("Name: " + asset.Name);
	        builder.AppendLine("==============");
	        builder.AppendLine("******ASSET FILES******");
	
	        // Display the files associated with each asset. 
	        foreach (IAssetFile fileItem in asset.AssetFiles)
	        {
	            builder.AppendLine("Name: " + fileItem.Name);
	            builder.AppendLine("Size: " + fileItem.ContentFileSize);
	            builder.AppendLine("==============");
	        }
	    }
	
	    // Display output in console.
	    Console.Write(builder.ToString());
	}

##Elencare i processi e gli asset

Un'importante attività correlata consiste nell'elencare gli asset con il relativo processo associato in Servizi multimediali. L'esempio di codice seguente mostra come elencare ogni oggetto IJob e quindi, per ogni processo, visualizzare le proprietà relative al processo, tutte le attività correlate, tutti gli asset di input e tutti gli asset di output. Il codice di questo esempio può essere utile per molte altre attività. Se ad esempio si vuole elencare gli asset di output di uno o più processi di codifica eseguiti in precedenza, questo codice mostra come accedere agli asset di output. Quando è disponibile un riferimento a un asset di output, è quindi possibile distribuire il contenuto ad altri utenti o applicazioni scaricandolo o specificando gli URL.

Per altre informazioni sulle opzioni per la distribuzione degli asset, vedere [Distribuire asset con Media Services SDK for .NET](media-services-deliver-streaming-content.md).

	// List all jobs on the server, and for each job, also list 
	// all tasks, all input assets, all output assets.

	static void ListJobsAndAssets()
	{
	    string waitMessage = "Building the list. This may take a few "
	        + "seconds to a few minutes depending on how many assets "
	        + "you have."
	        + Environment.NewLine + Environment.NewLine
	        + "Please wait..."
	        + Environment.NewLine;
	    Console.Write(waitMessage);
	
	    // Create a Stringbuilder to store the list that we build. 
	    StringBuilder builder = new StringBuilder();
	
	    foreach (IJob job in _context.Jobs)
	    {
	        // Display the collection of jobs on the server.
	        builder.AppendLine("");
	        builder.AppendLine("******JOB*******");
	        builder.AppendLine("Job ID: " + job.Id);
	        builder.AppendLine("Name: " + job.Name);
	        builder.AppendLine("State: " + job.State);
	        builder.AppendLine("Order: " + job.Priority);
	        builder.AppendLine("==============");
	
	
	        // For each job, display the associated tasks (a job  
	        // has one or more tasks). 
	        builder.AppendLine("******TASKS*******");
	        foreach (ITask task in job.Tasks)
	        {
	            builder.AppendLine("Task Id: " + task.Id);
	            builder.AppendLine("Name: " + task.Name);
	            builder.AppendLine("Progress: " + task.Progress);
	            builder.AppendLine("Configuration: " + task.Configuration);
	            if (task.ErrorDetails != null)
	            {
	                builder.AppendLine("Error: " + task.ErrorDetails);
	            }
	            builder.AppendLine("==============");
	        }
	
	        // For each job, display the list of input media assets.
	        builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
	        foreach (IAsset inputAsset in job.InputMediaAssets)
	        {
	
	            if (inputAsset != null)
	            {
	                builder.AppendLine("Input Asset Id: " + inputAsset.Id);
	                builder.AppendLine("Name: " + inputAsset.Name);
	                builder.AppendLine("==============");
	            }
	        }
	
	        // For each job, display the list of output media assets.
	        builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
	        foreach (IAsset theAsset in job.OutputMediaAssets)
	        {
	            if (theAsset != null)
	            {
	                builder.AppendLine("Output Asset Id: " + theAsset.Id);
	                builder.AppendLine("Name: " + theAsset.Name);
	                builder.AppendLine("==============");
	            }
	        }
	
	    }
	
	    // Display output in console.
	    Console.Write(builder.ToString());
	}

##Elencare tutti i criteri di accesso

In Servizi multimediali è possibile definire un criterio di accesso per un asset o i relativi file. Un criterio di accesso definisce le autorizzazioni per un file o un asset, ovvero il tipo di accesso e la durata. Nel codice di Servizi multimediali, in genere si definisce un criterio di accesso creando un oggetto IAccessPolicy e associandolo a un asset esistente. È quindi necessario creare un oggetto ILocator, che permette di fornire l'accesso diretto agli asset in Servizi multimediali. Il progetto di Visual Studio fornito con questa serie di argomenti include diversi esempi di codice in cui è illustrato come creare e assegnare criteri di accesso e localizzatori agli asset.

L'esempio di codice seguente illustra come elencare tutti i criteri di accesso nel server e mostra il tipo di autorizzazioni associato a ognuno. Un altro modo utile per visualizzare i criteri di accesso consiste nell'elencare tutti gli oggetti ILocator nel server e quindi, per ogni localizzatore, elencare il relativo criterio di accesso associato usando la relativa proprietà AccessPolicy.

	static void ListAllPolicies()
	{
	    foreach (IAccessPolicy policy in _context.AccessPolicies)
	    {
	        Console.WriteLine("");
	        Console.WriteLine("Name:  " + policy.Name);
	        Console.WriteLine("ID:  " + policy.Id);
	        Console.WriteLine("Permissions: " + policy.Permissions);
	        Console.WriteLine("==============");
	
	    }
	}

##Elencare tutti i localizzatori

Un localizzatore è un URL che fornisce un percorso diretto per accedere a un asset, insieme alle autorizzazioni per l'asset definite dal criterio di accesso associato del localizzatore. A ogni asset può essere associata una raccolta di oggetti ILocator per la relativa proprietà Locators. Anche nel contesto del server è disponibile una raccolta Locators contenente tutti i localizzatori.

Nell'esempio di codice seguente sono elencati tutti i localizzatori nel server. Per ogni localizzatore, sono mostrati l'ID per l'asset e il criterio di accesso correlati. Sono anche visualizzati il tipo di autorizzazioni, la data di scadenza e il percorso completo dell'asset.

Si noti che il percorso localizzatore per un asset è solo un URL di base per l'accesso all'asset. Per creare un percorso diretto per i singoli file a cui un utente o un'applicazione potrebbe accedere, il codice deve aggiungere il percorso del file specifico al percorso localizzatore. Per altre informazioni su come eseguire questa operazione, vedere l'argomento [Distribuire asset con Media Services SDK for .NET](media-services-deliver-streaming-content.md).

	static void ListAllLocators()
	{
	    foreach (ILocator locator in _context.Locators)
	    {
	        Console.WriteLine("***********");
	        Console.WriteLine("Locator Id: " + locator.Id);
	        Console.WriteLine("Locator asset Id: " + locator.AssetId);
	        Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
	        Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
	        Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
	        // The locator path is the base or parent path (with included permissions) to access  
	        // the media content of an asset. To create a full URL to a specific media file, take 
	        // the locator path and then append a file name and info as needed.  
	        Console.WriteLine("Locator base path: " + locator.Path);
	        Console.WriteLine("");
	    }
	}


##Eliminare un asset

Nell'esempio seguente sarà eliminato un asset.

	static void DeleteAsset( IAsset asset)
	{
	    // delete the asset
	    asset.Delete();
	
	    // Verify asset deletion
	    if (GetAsset(asset.Id) == null)
	        Console.WriteLine("Deleted the Asset");
	
	}

##Eliminare un processo

Per eliminare un processo, è necessario verificarne lo stato indicato nella proprietà State. I processi completati o annullati possono essere eliminati direttamente, mentre i processi che hanno altri stati, ad esempio che sono accodati, pianificati o in elaborazione, devono essere annullati prima di poter essere eliminati. L'esempio di codice seguente illustra un metodo per eliminare un processo verificandone lo stato e procedendo con l'eliminazione quando lo stato è completato o annullato. Questo codice dipende dalla sezione precedente di questo argomento per ottenere un riferimento a un processo: Ottenere un riferimento a un processo.

	static void DeleteJob(string jobId)
	{
	    bool jobDeleted = false;
	
	    while (!jobDeleted)
	    {
	        // Get an updated job reference.  
	        IJob job = GetJob(jobId);
	
	        // Check and handle various possible job states. You can 
	        // only delete a job whose state is Finished, Error, or Canceled.   
	        // You can cancel jobs that are Queued, Scheduled, or Processing,  
	        // and then delete after they are canceled.
	        switch (job.State)
	        {
	            case JobState.Finished:
	            case JobState.Canceled:
	            case JobState.Error:
	                // Job errors should already be logged by polling or event 
	                // handling methods such as CheckJobProgress or StateChanged.
	                // You can also call job.DeleteAsync to do async deletes.
	                job.Delete();
	                Console.WriteLine("Job has been deleted.");
	                jobDeleted = true;
	                break;
	            case JobState.Canceling:
	                Console.WriteLine("Job is cancelling and will be deleted "
	                    + "when finished.");
	                Console.WriteLine("Wait while job finishes canceling...");
	                Thread.Sleep(5000);
	                break;
	            case JobState.Queued:
	            case JobState.Scheduled:
	            case JobState.Processing:
	                job.Cancel();
	                Console.WriteLine("Job is scheduled or processing and will "
	                    + "be deleted.");
	                break;
	            default:
	                break;
	        }
	
	    }
	}


##Eliminare un criterio di accesso

L'esempio di codice seguente illustra come ottenere un riferimento a un criterio di accesso in base all'ID del criterio e quindi come eliminare il criterio.

	static void DeleteAccessPolicy(string existingPolicyId)
	{
	    // To delete a specific access policy, get a reference to the policy.  
	    // based on the policy Id passed to the method.
	    var policyInstance =
	            from p in _context.AccessPolicies
	            where p.Id == existingPolicyId
	            select p;
	    IAccessPolicy policy = policyInstance.FirstOrDefault();
	
	    policy.Delete();
	
	}
	

<!---HONumber=August15_HO6-->