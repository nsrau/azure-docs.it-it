<properties 
	pageTitle="Come creare un processore multimediale | Microsoft Azure" 
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
	ms.date="09/07/2015" 
	ms.author="juliako"/>


#Procedura: Ottenere un'istanza del processore di contenuti multimediali

> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [REST](media-services-rest-get-media-processor.md)
 

##Panoramica

In Servizi multimediali un processore di contenuti multimediali è un componente che gestisce un'attività di elaborazione specifica, ad esempio la codifica, la conversione del formato, la crittografia o la decrittografia di contenuti multimediali. Un processore multimediale viene generalmente creato durante la creazione di un'attività per la codifica, la crittografia o la conversione di formato di contenuto multimediale.

Nella tabella seguente sono riportati il nome e la descrizione di tutti i processori multimediali disponibili.

Nome del processore multimediale|Descrizione|Ulteriori informazioni
---|---|---
Azure Media Encoder|Consente di eseguire attività di codifica usando Azure Media Encoder.|[Azure Media Encoder](media-services-encode-asset.md#azure_media_encoder)
Codificatore multimediale standard|Consente di eseguire attività di codifica utilizzando il codificatore multimediale standard.|[Azure Media Encoder](media-services-encode-asset.md#media_encoder_standard)
Flusso di lavoro Premium del codificatore multimediale|Consente di eseguire attività di codifica usando il flusso di lavoro Premium del codificatore multimediale.|[Flusso di lavoro Premium del codificatore multimediale](media-services-encode-asset.md#media_encoder_premium_wokrflow)
Azure Media Indexer| Consente di rendere disponibili per la ricerca file e contenuti multimediali, oltre a generare tracce e parole chiave per i sottotitoli codificati.|[Indicizzazione di file multimediali con Azure Media Indexer](media-services-index-content.md)
Azure Media Hyperlapse (anteprima)|Consente di ridurre le imperfezioni del video con la stabilizzazione video. Consente inoltre di velocizzare il contenuto in una clip utilizzabile.|		[Azure Media Hyperlapse](http://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)</a>
Storage Decryption| Consente di decrittografare asset di file multimediali crittografati mediante la crittografia di archiviazione.|N/D
Microsoft Azure Media Packager|Consente di convertire asset di file multimediali dal formato MP4 al formato Smooth Streaming. Consente inoltre di convertire asset di file multimediali dal formato Smooth Streaming al formato Apple HTTP Live Streaming (HLS).|[Stringhe di set di impostazioni di attività per Azure Media Packager](http://msdn.microsoft.com/library/hh973635.aspx)
Microsoft Azure Media Encryptor|Consente di crittografare asset di file multimediali usando PlayReady Protection.|[Stringhe di set di impostazioni di attività per Azure Media Packager](http://msdn.microsoft.com/library/hh973610.aspx)

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


##Percorsi di apprendimento di Media Services

È possibile visualizzare i percorsi di apprendimento AMS qui:

- [Flusso di lavoro AMS Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Flusso di lavoro AMS Streaming su richiesta](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)


##Passaggi successivi
Dopo avere ottenuto un'istanza del processore multimediale, passare all'argomento [Procedura: Codificare un asset][] che illustra come utilizzare Azure Media Encoder per codificare un asset.

[Procedura: Codificare un asset]: media-services-encode-asset.md
[Task Preset Strings for the Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[Procedura: Connettersi a Servizi multimediali a livello di codice]: ../media-services-set-up-computer/

<!---HONumber=Sept15_HO2-->