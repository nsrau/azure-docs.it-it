<properties 
	pageTitle="Come creare un processore multimediale | Microsoft Azure" 
	description="Informazioni su come creare un componente del processore di contenuti multimediali per codificare, decodificare, convertire il formato, crittografare o decrittografare contenuti multimediali per Servizi multimediali di Azure. Negli esempi di codice, scritti in C#, viene usato l'SDK di Servizi multimediali per .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/01/2016" 
	ms.author="juliako"/>


#Procedura: Ottenere un'istanza del processore di contenuti multimediali

> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [REST](media-services-rest-get-media-processor.md)


##Panoramica

In Servizi multimediali un processore di contenuti multimediali è un componente che gestisce un'attività di elaborazione specifica, ad esempio la codifica, la conversione del formato, la crittografia o la decrittografia di contenuti multimediali. Un processore multimediale viene generalmente creato durante la creazione di un'attività per la codifica, la crittografia o la conversione di formato di contenuto multimediale.

Nella tabella seguente sono riportati il nome e la descrizione di tutti i processori multimediali disponibili.

Nome del processore multimediale|Descrizione|Altre informazioni
---|---|---
Codificatore multimediale standard|Fornisce funzionalità standard per la codifica su richiesta. |[Panoramica e confronto dei codificatori multimediali su richiesta di Azure](media-services-encode-asset.md)
Flusso di lavoro Premium del codificatore multimediale|Consente di eseguire attività di codifica usando il flusso di lavoro Premium del codificatore multimediale.|[Panoramica e confronto dei codificatori multimediali su richiesta di Azure](media-services-encode-asset.md)
Azure Media Indexer| Consente di rendere disponibili per la ricerca file e contenuti multimediali, oltre a generare tracce e parole chiave per i sottotitoli codificati.|[Azure Media Indexer](media-services-index-content.md)
Azure Media Hyperlapse (anteprima)|Consente di ridurre le imperfezioni del video con la stabilizzazione video. Consente inoltre di velocizzare il contenuto in una clip utilizzabile.|[Azure Media Hyperlapse](media-services-hyperlapse-content.md)
Azure Media Encoder|Ammortizzato
Storage Decryption| Ammortizzato|
Azure Media Packager|Ammortizzato|
Azure Media Encryptor|Ammortizzato|

##Ottenere un processore di contenuti multimediali

Il seguente metodo illustra come ottenere un'istanza del processore di contenuti multimediali. Nell'esempio si suppone che si usi una variabile a livello di modulo denominata **\_context** per fare riferimento al contesto del server descritto nella sezione [Procedura: Connettersi a Servizi multimediali a livello di codice](media-services-dotnet-connect_programmatically.md).

	private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
		var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
		ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
		
		if (processor == null)
		throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
		
		return processor;
	}


##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Passaggi successivi

Dopo avere ottenuto un'istanza del processore di contenuti multimediali, passare all'argomento [Come codificare un asset](media-services-dotnet-encode-with-media-encoder-standard.md) che illustra come utilizzare Media Encoder Standard per codificare un asset.

<!---HONumber=AcomDC_0302_2016-->