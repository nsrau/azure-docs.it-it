<properties urlDisplayName="Create a Media Processor" pageTitle="Come creare un processore di contenuti multimediali - Azure" metaKeywords="" description="Informazioni su come creare un componente del processore di contenuti multimediali per codificare, decodificare, convertire il formato, crittografare o decrittografare contenuti multimediali per Servizi multimediali di Azure. Negli esempi di codice, scritti in C#, viene usato Media Services SDK per .NET." metaCanonical="" services="media-services" documentationCenter="" title="Procedura: Ottenere un'istanza del processore di contenuti multimediali" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/26/2014" ms.author="juliako" />

# Procedura: Ottenere un'istanza del processore di contenuti multimediali

Questo articolo fa parte di una serie di articoli dedicati alla programmazione in Servizi multimediali di Azure. L'argomento precedente è [Procedura: Creare e caricare un asset crittografato nell'archiviazione][Procedura: Creare e caricare un asset crittografato nell'archiviazione].

In Servizi multimediali un processore multimediale è un componente che gestisce un'attività di elaborazione specifica, ad esempio la codifica, la conversione del formato, la crittografia o la decrittografia di contenuto multimediale. Un processore multimediale viene generalmente creato durante la creazione di un'attività per la codifica, la crittografia o la conversione di formato di contenuto multimediale.

Nella tabella seguente sono riportati il nome e la descrizione di tutti i processori multimediali disponibili.

| Nome del processore multimediale | Descrizione                                                                                                                                                                                                                       | Altre informazioni                                                        |
|----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------|
| Azure Media Encoder              | Consente di eseguire attività di codifica usando il codificatore multimediale.                                                                                                                                                    | [Stringhe di set di impostazioni di attività per Azure Media Encoder][Stringhe di set di impostazioni di attività per Azure Media Encoder]   |
| Microsoft Azure Media Packager   | Consente di convertire asset di file multimediali dal formato MP4 al formato Smooth Streaming. Consente inoltre di convertire asset di file multimediali dal formato Smooth Streaming al formato Apple HTTP Live Streaming (HLS). | [Stringhe di set di impostazioni di attività per Azure Media Packager][Stringhe di set di impostazioni di attività per Azure Media Packager]  |
| Microsoft Azure Media Encryptor  | Consente di crittografare asset di file multimediali usando PlayReady Protection.                                                                                                                                                 | [Stringhe di set di impostazioni di attività per Azure Media Packager][1] |
| Azure Media Indexer              | Consente di rendere disponibili per la ricerca file e contenuti multimediali, oltre a generare tracce e parole chiave per i sottotitoli codificati.                                                                               | N/D                                                                       |
| Storage Decryption               | Consente di decrittografare asset di file multimediali crittografati mediante la crittografia di archiviazione.                                                                                                                   | N/D                                                                       |

Il metodo seguente illustra come ottenere un'istanza del processore di contenuti multimediali. Nell'esempio si suppone che si usi una variabile a livello di modulo denominata \*\*\_context\*\* per fare riferimento al contesto del server descritto nella sezione [Procedura: Connettersi a Servizi multimediali a livello di codice][Procedura: Connettersi a Servizi multimediali a livello di codice].

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
         var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }

## Passaggi successivi

Dopo avere ottenuto un'istanza del processore multimediale, passare all'argomento [Procedura: Codificare un asset][Procedura: Codificare un asset] che illustra come usare Azure Media Encoder per codificare un asset.

  [Procedura: Creare e caricare un asset crittografato nell'archiviazione]: ../media-services-create-encrypted-asset-upload-storage/
  [Stringhe di set di impostazioni di attività per Azure Media Encoder]: http://msdn.microsoft.com/it-it/library/jj129582.aspx
  [Stringhe di set di impostazioni di attività per Azure Media Packager]: http://msdn.microsoft.com/it-it/library/hh973635.aspx
  [1]: http://msdn.microsoft.com/it-it/library/hh973610.aspx
  [Procedura: Connettersi a Servizi multimediali a livello di codice]: ../media-services-set-up-computer/
  [Procedura: Codificare un asset]: ../media-services-encode-asset/
