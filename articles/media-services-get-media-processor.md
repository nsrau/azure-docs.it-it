<properties linkid="develop-media-services-how-to-guides-create-media-processor" urlDisplayName="Create a Media Processor" pageTitle="How to Create a Media Processor - Azure" metaKeywords="" description="Learn how to create a media processor component to encode, convert format, encrypt, or decrypt media content for Azure Media Services. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Get a Media Processor Instance" authors="migree" solutions="" manager="" editor="" />

Procedura: Ottenere un'istanza del processore di contenuti multimediali
=======================================================================

Questo articolo fa parte di una serie di articoli dedicati alla programmazione in Servizi multimediali di Azure. L'argomento precedente è [Procedura: Creare e caricare un asset crittografato nell'archiviazione](http://go.microsoft.com/fwlink/?LinkID=301733&clcid=0x409).

In Servizi multimediali un processore multimediale è un componente che gestisce un'attività di elaborazione specifica, ad esempio la codifica, la conversione del formato, la crittografia o la decrittografia di contenuto multimediale. Un processore multimediale viene generalmente creato durante la creazione di un'attività per la codifica, la crittografia o la conversione di formato di contenuto multimediale.

Nella tabella seguente sono riportati il nome e la descrizione di tutti i processori multimediali disponibili.

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>Nome del processore multimediale</th>
       <th>Descrizione</th>
	<th>Ulteriori informazioni</th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Azure Media Encoder</td>
       <td>Consente di eseguire attività di codifica utilizzando il codificatore multimediale.</td>
       <td><a href="http://msdn.microsoft.com/en-us/library/jj129582.aspx"> Stringhe di set di impostazioni di attività per Azure Media Encoder</a></td>
    </tr>
    <tr>
        <td>Azure Media Packager</td>
        <td>Consente di convertire asset di file multimediali dal formato MP4 al formato Smooth Streaming. Consente inoltre di convertire asset di file multimediali dal formato Smooth Streaming al formato Apple HTTP Live Streaming (HLS).</td>
		<td><a href="http://msdn.microsoft.com/en-us/library/hh973635.aspx">Stringhe di set di impostazioni di attività per Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Azure Media Encryptor</td>
        <td>Consente di crittografare asset di file multimediali utilizzando PlayReady Protection.</td>
        <td><a href="http://msdn.microsoft.com/en-us/library/hh973610.aspx">Stringhe di set di impostazioni di attività per Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Storage Decryption</td>
        <td>Consente di decrittografare asset di file multimediali crittografati mediante la crittografia di archiviazione.</td>
		<td>N/D</td>
    </tr>
  </tbody>
</table>


Il metodo seguente illustra come ottenere un'istanza del processore di contenuti multimediali. Nell'esempio si suppone che si utilizzi una variabile a livello di modulo denominata **\_context** per fare riferimento al contesto del server descritto nella sezione [Procedura: Connettersi a Servizi multimediali a livello di codice](http://www.windowsazure.com/en-us/develop/media-services/how-to-guides/set-up-computer-for-media-services).

<pre><code>
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}
</code></pre>

Passaggi successivi
-------------------

Dopo avere ottenuto un'istanza del processore multimediale, passare all'argomento [Procedura: Codificare un asset](http://go.microsoft.com/fwlink/?LinkId=301753) che illustra come utilizzare Azure Media Encoder per codificare un asset.

