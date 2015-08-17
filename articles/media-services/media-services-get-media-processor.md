<properties 
	pageTitle="Come creare un processore di contenuti multimediali - Azure" 
	description="Informazioni su come creare un componente del processore di contenuti multimediali per codificare, decodificare, convertire il formato, crittografare o decrittografare contenuti multimediali per Servizi multimediali di Azure. Negli esempi di codice, scritti in C#, viene usato l'SDK di Servizi multimediali per .NET." 
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
	ms.date="05/13/2015" 
	ms.author="juliako"/>



#Procedura: Ottenere un'istanza del processore di contenuti multimediali

Questo articolo fa parte della serie relativa al [flusso di lavoro Video on Demand di Servizi multimediali](media-services-video-on-demand-workflow.md).

##Panoramica

In Servizi multimediali un processore di contenuti multimediali è un componente che gestisce un'attività di elaborazione specifica, ad esempio la codifica, la conversione del formato, la crittografia o la decrittografia di contenuti multimediali. Un processore multimediale viene generalmente creato durante la creazione di un'attività per la codifica, la crittografia o la conversione di formato di contenuto multimediale.

Nella tabella seguente sono riportati il nome e la descrizione di tutti i processori multimediali disponibili.

Nome del processore multimediale|Descrizione|Ulteriori informazioni
---|---|---
Azure Media Encoder|Consente di eseguire attività di codifica usando Azure Media Encoder.| [Stringhe di set di impostazioni di attività per Azure Media Encoder](http://msdn.microsoft.com/library/jj129582.aspx)
Flusso di lavoro Premium del codificatore multimediale|Consente di eseguire attività di codifica usando il flusso di lavoro Premium del codificatore multimediale.|[Codificare con il flusso di lavoro Premium del codificatore multimediale.](media-services-encode-with-premium-workflow.md)
Azure Media Indexer|Consente di rendere disponibili per la ricerca file e contenuti multimediali, oltre a generare tracce e parole chiave per i sottotitoli codificati.|[Indicizzazione di file multimediali con Azure Media Indexer](media-services-index-content.md).
Microsoft Azure Media Packager|Consente di convertire asset di file multimediali dal formato MP4 al formato Smooth Streaming. Consente inoltre di convertire asset di file multimediali dal formato Smooth Streaming al formato Apple HTTP Live Streaming (HLS).|
Microsoft Azure Media Encryptor|Consente di crittografare asset di file multimediali usando PlayReady Protection.|
Azure Media Hyperlapse (anteprima)|Consente di ridurre le imperfezioni del video con la stabilizzazione video. Consente inoltre di velocizzare il contenuto in una clip utilizzabile.|
Storage Decryption|Consente di decrittografare asset di file multimediali crittografati mediante la crittografia di archiviazione.|N/D

##Ottenere un'istanza di MediaProcessor

Il seguente metodo illustra come ottenere un'istanza del processore di contenuti multimediali. Nell'esempio si suppone che si usi una variabile a livello di modulo denominata **\_context** per fare riferimento al contesto del server descritto nella sezione [Procedura: Connettersi a Servizi multimediali a livello di codice].

	private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
	     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
	        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
	
	    if (processor == null)
	        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
	
	    return processor;
	}

##Passaggi successivi
Dopo avere ottenuto un'istanza del processore multimediale, passare all'argomento [Procedura: Codificare un asset][] che illustra come utilizzare Azure Media Encoder per codificare un asset.

[Procedura: Codificare un asset]: media-services-encode-asset.md
[Task Preset Strings for the Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[Procedura: Connettersi a Servizi multimediali a livello di codice]: ../media-services-set-up-computer/

<!---HONumber=August15_HO6-->