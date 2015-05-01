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
	ms.date="02/10/2015" 
	ms.author="juliako"/>


# Procedura: Ottenere un'istanza del processore di contenuti multimediali

Questo articolo fa parte della serie [Flusso di lavoro Video on Demand di Servizi multimediali](media-services-video-on-demand-workflow.md) . 

## Informazioni generali

In Servizi multimediali un processore di contenuti multimediali è un componente che gestisce un'attività di elaborazione specifica, ad esempio la codifica, la conversione del formato, la crittografia o la decrittografia di contenuti multimediali. Un processore di contenuti multimediali viene generalmente creato durante la creazione di un'attività per la codifica, la crittografia o la conversione di formato di contenuto multimediale.

La seguente tabella riporta il nome e la descrizione di tutti i processori di contenuti multimediali disponibili.

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>Nome del processore di contenuti multimediali</th>
       <th>Descrizione</th>
	<th>Altre informazioni</th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Azure Media Encoder</td>
       <td>Consente di eseguire attività di codifica usando Azure Media Encoder.</td>
       <td><a href="http://msdn.microsoft.com/library/jj129582.aspx">Stringhe di set di impostazioni di attività per Azure Media Encoder</a></td>
    </tr>
    <tr>
       <td>Flusso di lavoro Premium del codificatore multimediale</td>
       <td>Consente di eseguire attività di codifica usando il flusso di lavoro Premium del codificatore multimediale.</td>
       <td><a href="http://azure.microsoft.com/documentation/articles/media-services-encode-with-premium-workflow/">Codifica con il flusso di lavoro Premium del codificatore multimediale.</a></td>
    </tr>    
	<tr>
        <td>Azure Media Indexer</td>
        <td>Consente di rendere disponibili per la ricerca file e contenuti multimediali, oltre a generare tracce e parole chiave per i sottotitoli codificati.</td>
		<td><a href="http://azure.microsoft.com/documentation/articles/media-services-index-content/">Indicizzazione di file multimediali con Azure Media Indexer</a>.</td>
    </tr>
    <tr>
        <td>Microsoft Azure Media Packager</td>
        <td>Consente di convertire asset di file multimediali dal formato MP4 al formato Smooth Streaming. Consente inoltre di convertire asset di file multimediali dal formato Smooth Streaming al formato Apple HTTP Live Streaming (HLS).</td>
		<td><a href="http://msdn.microsoft.com/library/hh973635.aspx">Stringhe di set di impostazioni di attività per Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Microsoft Azure Media Encryptor</td>
        <td>Consente di crittografare asset di file multimediali usando PlayReady Protection.</td>
        <td><a href="http://msdn.microsoft.com/library/hh973610.aspx">Stringhe di set di impostazioni di attività per Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Storage Decryption</td>
        <td>Consente di decrittografare asset di file multimediali crittografati mediante la crittografia di archiviazione.</td>
		<td>N/D</td>
    </tr>  </tbody>
</table>

<br />

## Ottenere un'istanza di MediaProcessor

Il seguente metodo illustra come ottenere un'istanza del processore di contenuti multimediali. Nell'esempio si suppone che si usi una variabile a livello di modulo denominata **_context** per fare riferimento al contesto del server descritto nella sezione [Procedura: Connettersi a Servizi multimediali a livello di codice].

	private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
	     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
	        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
	
	    if (processor == null)
	        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
	
	    return processor;
	}

## Passaggi successivi
Dopo avere ottenuto un'istanza del processore di contenuti multimediali, passare all'argomento [Come codificare un asset][] che illustra come usare Azure Media Encoder per codificare un asset.

[Come codificare un asset]: media-services-encode-asset.md
[Stringhe di set di impostazioni di attività per Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[Procedura: Connettersi a Servizi multimediali a livello di codice]: ../media-services-set-up-computer/


<!--HONumber=52-->