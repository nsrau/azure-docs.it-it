---
title: Segnalare tracce audio descrittive con servizi multimediali di Azure V3 | Microsoft Docs
description: Seguire i passaggi di questa esercitazione per caricare un file, codificare il video, aggiungere tracce audio descrittive e trasmettere i contenuti con servizi multimediali V3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 09/25/2019
ms.author: juliako
ms.openlocfilehash: 0d8f88e6c2fe273efa969278146de67ba18eaecf
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392189"
---
# <a name="signal-descriptive-audio-tracks"></a>Segnala tracce audio descrittive

È possibile aggiungere un tracking per la narrazione al video per aiutare i client che non hanno apportato la propria visione a seguire la registrazione video ascoltando la narrazione. In servizi multimediali V3 è possibile segnalare tracce audio descrittive annotando la traccia audio nel file manifesto.

Questo articolo illustra come codificare un video, caricare un file MP4 solo audio (codec AAC) contenente audio descrittivo nell'asset di output e modificare il file con estensione ISM per includere l'audio descrittivo.

## <a name="prerequisites"></a>Prerequisiti

- [Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md).
- Seguire la procedura descritta in [Accedere all'API di Servizi multimediali di Azure usando l'interfaccia della riga di comando di Azure](access-api-cli-how-to.md) e salvare le credenziali. Sarà necessario usarle per accedere all'API.
- Esaminare la creazione [dinamica dei pacchetti](dynamic-packaging-overview.md).
- Vedere l'esercitazione [caricare, codificare e trasmettere video](stream-files-tutorial-with-api.md) .

## <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Creare un asset di input e caricare un file locale nell'asset 

La funzione **CreateInputAsset** crea un nuovo [asset](https://docs.microsoft.com/rest/api/media/assets) di input e carica al suo interno il file video locale specificato. Questo **asset** viene usato come input per il processo di codifica. In Servizi multimediali v3 l'input di un **processo** può essere costituito da un **asset** oppure da contenuti resi disponibili all'account di Servizi multimediali tramite URL HTTPS. 

Per informazioni su come eseguire la codifica da un URL HTTPS, vedere [questo articolo](job-input-from-http-how-to.md) .  

In Servizi multimediali v3 si usano le API di Archiviazione di Azure per caricare i file. Il frammento di codice .NET seguente illustra come eseguire questa operazione.

La funzione esegue queste azioni:

* Crea un **asset** 
* Ottiene un [URL di firma di accesso condiviso](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) scrivibile al [contenitore nel servizio di archiviazione](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container) dell'asset
* Carica il file nel contenitore nel servizio di archiviazione usando l'URL di firma di accesso condiviso.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Se è necessario passare il nome dell'asset di input creato ad altri metodi, assicurarsi di usare la proprietà `Name` nell'oggetto asset restituito da `CreateInputAssetAsync`, ad esempio inputAsset.Name. 

## <a name="create-an-output-asset-to-store-the-result-of-the-encoding-job"></a>Creare un asset di output per archiviare il risultato del processo di codifica

L'[asset](https://docs.microsoft.com/rest/api/media/assets) di output archivia il risultato del processo di codifica. La funzione seguente mostra come creare un asset di output.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Se è necessario passare il nome dell'asset di output creato ad altri metodi, assicurarsi di usare la proprietà `Name` nell'oggetto asset restituito da `CreateIOutputAssetAsync`, ad esempio outputAsset.Name. 

Nel caso di questo articolo, passare il valore `outputAsset.Name` alle funzioni `SubmitJobAsync` e `UploadAudioIntoOutputAsset`.

## <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Creare una trasformazione e un processo che codifica il file caricato

Quando si codificano o si elaborano contenuti in Servizi multimediali, è prassi comune configurare le impostazioni di codifica come una serie di istruzioni e quindi inviare un oggetto **Job**, ovvero il processo per applicare tali istruzioni a un video. Inviando nuovi processi per ogni nuovo video, si applicano le istruzioni a tutti i video nella libreria. In Servizi multimediali una serie di istruzioni di questo tipo è definita trasformazione ed è rappresentata dall'oggetto **Transform**. Per altre informazioni, vedere [Trasformazioni e processi](transform-concept.md). L'esempio illustrato in questa esercitazione definisce una serie di istruzioni che codifica il video per eseguirne lo streaming a diversi tipi di dispositivi iOS e Android. 

Nell'esempio seguente viene creata una trasformazione, se non ne esiste una.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

La funzione seguente invia un processo.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Attendere il completamento del processo

Il completamento del processo richiede tempo e al termine dell'elaborazione può essere opportuno ricevere una notifica. È consigliabile usare griglia di eventi per attendere il completamento del processo.

Il processo in genere passa attraverso gli Stati seguenti: **pianificato**, in **coda**, **elaborazione**, **completato** (stato finale). Se nel corso del processo si verifica un errore, viene restituito lo stato **Error**. Se il processo è in fase di annullamento, vengono restituiti lo stato **Canceling** e, al termine, lo stato **Canceled**.

Per ulteriori informazioni, vedere [gestione degli eventi di griglia di eventi](reacting-to-media-services-events.md).

## <a name="upload-the-audio-only-mp4-file"></a>Caricare il file MP4 solo audio

Caricare il file MP4 aggiuntivo solo audio (codec AAC) contenente audio descrittivo nell'asset di output.  

```csharp
private static async Task UpoadAudioIntoOutputAsset(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string outputAssetName,
    string fileToUpload)
{
    // Use the Assets.Get method to get the existing asset. 
    // In Media Services v3, the Get method on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).

    // Call Media Services API to create an Asset.
    // This method creates a container in storage for the Asset.
    // The files (blobs) associated with the asset will be stored in this container.
    Asset asset = await client.Assets.GetAsync(resourceGroupName, accountName, outputAssetName);
    
    if (asset != null)
    {
      // Use Media Services API to get back a response that contains
      // SAS URL for the Asset container into which to upload blobs.
      // That is where you would specify read-write permissions 
      // and the exparation time for the SAS URL.
      var response = await client.Assets.ListContainerSasAsync(
          resourceGroupName,
          accountName,
          outputAssetName,
          permissions: AssetContainerPermission.ReadWrite,
          expiryTime: DateTime.UtcNow.AddHours(4).ToUniversalTime());

      var sasUri = new Uri(response.AssetContainerSasUrls.First());

      // Use Storage API to get a reference to the Asset container
      // that was created by calling Asset's CreateOrUpdate method.  
      CloudBlobContainer container = new CloudBlobContainer(sasUri);
      var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));

      // Use Strorage API to upload the file into the container in storage.
      await blob.UploadFromFileAsync(fileToUpload);
    }
}
```

Di seguito è riportato un esempio di una chiamata alla funzione `UpoadAudioIntoOutputAsset`:

```csharp
await UpoadAudioIntoOutputAsset(client, config.ResourceGroup, config.AccountName, outputAsset.Name, "audio_description.m4a");
```

## <a name="edit-the-ism-file"></a>Modificare il file con estensione ISM

Al termine del processo di codifica, l'asset di output conterrà i file generati dal processo di codifica. 

1. Nella portale di Azure passare all'account di archiviazione associato all'account di servizi multimediali. 
1. Trovare il contenitore con il nome dell'asset di output. 
1. Nel contenitore trovare il file con estensione ISM e fare clic su **modifica BLOB** nella finestra a destra. 
1. Modificare il file con estensione ISM aggiungendo le informazioni sul file MP4 solo audio caricato (codec AAC) contenente audio descrittivo e fare clic su **Salva** al termine.

    Per segnalare le tracce audio descrittive, è necessario aggiungere i parametri "Accessibility" e "Role" al file con estensione ISM. È responsabilità dell'utente impostare correttamente questi parametri per segnalare una traccia audio come descrizione audio. Ad esempio, aggiungere `<param name="accessibility" value="description" />` e `<param name="role" value="alternate" />` al file con estensione ISM per una traccia audio specifica, come illustrato nell'esempio seguente.
 
```xml
<?xml version="1.0" encoding="utf-8"?>
<smil xmlns="http://www.w3.org/2001/SMIL20/Language">
  <head>
    <meta name="clientManifestRelativePath" content="ignite.ismc" />
    <meta name="formats" content="mp4-v3" />
  </head>
  <body>
    <switch>
      <audio src="ignite_320x180_AACAudio_381.mp4" systemBitrate="128041" systemLanguage="eng">
        <param name="systemBitrate" value="128041" valuetype="data" />
        <param name="trackID" value="2" valuetype="data" />
        <param name="trackName" value="aac_eng_2_128041_2_1" valuetype="data" />
        <param name="systemLanguage" value="eng" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_2.mpi" valuetype="data" />
      </audio>
      <audio src="audio_description.m4a" systemBitrate="194000" systemLanguage="eng">
        <param name="trackName" value="aac_eng_audio_description" />
        <param name="accessibility" value="description" />
        <param name="role" value="alternate" />     
      </audio>          
      <video src="ignite_1280x720_AACAudio_3549.mp4" systemBitrate="3549855">
        <param name="systemBitrate" value="3549855" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_1280x720_AACAudio_3549_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_960x540_AACAudio_2216.mp4" systemBitrate="2216764">
        <param name="systemBitrate" value="2216764" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_960x540_AACAudio_2216_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_640x360_AACAudio_1154.mp4" systemBitrate="1154569">
        <param name="systemBitrate" value="1154569" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_640x360_AACAudio_1154_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_480x270_AACAudio_721.mp4" systemBitrate="721893">
        <param name="systemBitrate" value="721893" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_480x270_AACAudio_721_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_320x180_AACAudio_381.mp4" systemBitrate="381027">
        <param name="systemBitrate" value="381027" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_1.mpi" valuetype="data" />
      </video>
    </switch>
  </body>
</smil>
```

## <a name="get-a-streaming-locator"></a>Ottenere un localizzatore di streaming

Al termine della codifica, il passaggio successivo consiste nel rendere disponibile ai client il video nell'asset di output per la riproduzione. È possibile eseguire questa operazione in due passaggi: creare prima un [localizzatore di streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) e dopo gli URL di streaming che possono essere usati dai client. 

Il processo di creazione di un **localizzatore di streaming** è detto pubblicazione. Per impostazione predefinita, il **localizzatore di streaming** è valido immediatamente dopo l'esecuzione delle chiamate API e rimane tale finché non viene eliminato, a meno che non si configurino le ore di inizio e fine facoltative. 

Quando si crea un oggetto [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), è necessario specificare il parametro **StreamingPolicyName** desiderato. In questo esempio si eseguirà lo streaming di contenuti in chiaro, ovvero non crittografati, in modo da usare i criteri predefiniti per lo streaming non crittografato, **PredefinedStreamingPolicy.ClearStreamingOnly**.

> [!IMPORTANT]
> Se si usano [criteri di streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies) personalizzati, è necessario progettare un set limitato di tali criteri per l'account di Servizi multimediali e riusare questi criteri per i localizzatori di streaming ogni volta che si devono usare gli stessi protocolli e le stesse opzioni di crittografia. L'account di Servizi multimediali prevede una quota per il numero di occorrenze di criteri di streaming. Evitare quindi di creare nuovi criteri di streaming per ogni localizzatore di streaming.

Il codice seguente presuppone che la funzione venga chiamata con un parametro locatorName univoco.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Anche se l'esempio in questo argomento illustra un processo di streaming, è possibile usare la stessa chiamata per creare un localizzatore di streaming per la distribuzione di video tramite download progressivo.

### <a name="get-streaming-urls"></a>Ottenere URL di streaming

Ora che è stato creato il [localizzatore di streaming](https://docs.microsoft.com/rest/api/media/streaminglocators), è possibile ottenere gli URL di streaming, come illustrato in **GetStreamingURLs**. Per creare un URL, è necessario concatenare il nome host dell'[endpoint di streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints) e il percorso del **localizzatore di streaming**. In questo esempio, viene usato l'*endpoint di streaming* **predefinito**. Quando si crea un account di Servizi multimediali per la prima volta, il **localizzatore di streaming** *predefinito* è in stato arrestato ed è quindi necessario chiamare **Start**.

> [!NOTE]
> In questo metodo è necessario specificare il parametro locatorName usato per la creazione del **localizzatore di streaming** per l'asset di output.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

## <a name="test-with-azure-media-player"></a>Eseguire il test con Azure Media Player

Per testare lo streaming in questo articolo viene usato Azure Media Player. 

> [!NOTE]
> Se un lettore è ospitato in un sito https, assicurarsi di aggiornare l'URL impostandolo su "https".

1. Aprire un Web browser e passare [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Nella casella **URL:** incollare uno dei valori di URL di streaming ottenuti dall'applicazione. 
 
     È possibile incollare l'URL in formato HLS, Dash o Smooth e Azure Media Player passerà automaticamente a un protocollo di streaming appropriato per la riproduzione sul dispositivo.
3. Scegliere il pulsante **Update Player** (Aggiorna il lettore).

Azure Media Player può essere usato a scopo di test ma non deve essere usato in un ambiente di produzione. 

## <a name="next-steps"></a>Passaggi successivi

[Analizzare i video](analyze-videos-tutorial-with-api.md)
