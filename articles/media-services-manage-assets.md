<properties 
	pageTitle="Come gestire asset in Servizi multimediali - Azure" 
	description="Informazioni su come gestire asset in Servizi multimediali. È anche possibile gestire processi, attività, criteri di accesso, localizzatori e altro ancora. Negli esempi di codice, scritti in C#, viene usato l'SDK di Servizi multimediali per .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/15/2015" 
	ms.author="juliako"/>


# Procedura: Gestire gli asset in archivio

Questo articolo fa parte delle serie [Flusso di lavoro Video on Demand di Servizi multimediali](media-services-video-on-demand-workflow.md) e [Flusso di lavoro Live Streaming di Servizi multimediali](media-services-live-streaming-workflow.md).  


Dopo avere creato gli asset multimediali, è possibile accedervi e gestirli sul server. Sul server è possibile gestire anche altri oggetti che fanno parte di Servizi multimediali, tra cui processi, attività, criteri di accesso, localizzatori e altro ancora.

Nel seguente esempio viene mostrato come eseguire una query per ottenere un asset in base all'ID asset. 

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

Per elencare tutti gli asset disponibili sul server, è possibile usare il metodo riportato di seguito, che scorre la raccolta di asset e visualizza i dettagli su ogni asset.
	
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

Il seguente frammento di codice elimina tutti gli asset dall'account di Servizi multimediali. Si noti che se un asset è associato a un programma, è innanzitutto necessario eliminare il programma.

	foreach (IAsset asset in _context.Assets)
	{
	    asset.Delete();
	}


<!--HONumber=52-->