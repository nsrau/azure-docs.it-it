---
title: Eseguire il caricamento, la codifica e lo streaming con Servizi multimediali di Azure | Microsoft Docs
description: Seguire i passaggi di questa esercitazione per caricare un file, codificare il video ed eseguire lo streaming dei contenuti con Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 404a40f8949add77153d3fbf53b5c68dfb866128
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377881"
---
# <a name="tutorial-upload-encode-and-stream-videos-using-apis"></a>Esercitazione: Eseguire il caricamento, la codifica e lo streaming di video tramite API

Servizi multimediali consente di codificare i file multimediali nei formati che possono essere riprodotti su una vasta gamma di browser e dispositivi. Ad esempio, potrebbe essere necessario trasmettere il contenuto nei formati HLS o MPEG DASH di Apple. Prima dello streaming, è consigliabile codificare il file multimediale digitale di alta qualità. Per indicazioni per la codifica, vedere i [concetti correlati alla codifica](encoding-concept.md). Questa esercitazione descrive come caricare un file video locale e codificare il file caricato. È anche possibile codificare contenuti resi accessibili tramite un URL HTTPS. Per altre informazioni, vedere [Creare un input del processo da un URL HTTP(s)](job-input-from-http-how-to.md).

![Riprodurre il video](./media/stream-files-tutorial-with-api/final-video.png)

Questa esercitazione illustra come:    

> [!div class="checklist"]
> * Creare un account di Servizi multimediali
> * Accedere all'API di Servizi multimediali
> * Configurare l'app di esempio
> * Esaminare il codice per caricamento, codifica e streaming
> * Esecuzione dell'app
> * Testare l'URL di streaming
> * Pulire le risorse

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Se Visual Studio non è installato, è possibile scaricare [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).

## <a name="download-the-sample"></a>Scaricare l'esempio

Clonare nel computer un repository GitHub contenente l'esempio .NET di streaming usando il comando seguente:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

L'esempio è disponibile nella cartella [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>Esaminare il codice per caricamento, codifica e streaming

Questa sezione esamina le funzioni definite nel file [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) del progetto *UploadEncodeAndStreamFiles*.

L'esempio esegue le azioni seguenti:

1. Crea una nuova trasformazione (in primo luogo, controlla se esiste la trasformazione specificata). 
2. Crea un asset di output usato come output del processo di codifica.
3. Crea un asset di input e carica il file video locale specificato al suo interno. L'asset viene usato come input del processo. 
4. Invia il processo di codifica usando l'input e output creati.
5. Controlla lo stato del processo.
6. Crea un oggetto StreamingLocator.
7. Crea gli URL di streaming.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Iniziare a usare le API di Servizi multimediali con .NET SDK

Per iniziare a usare le API di Servizi multimediali con .NET, è necessario creare un oggetto **AzureMediaServicesClient**. Per eseguire questa operazione, specificare le credenziali necessarie per consentire al client di connettersi ad Azure tramite Azure AD. Nel codice clonato all'inizio dell'articolo, la funzione **GetCredentialsAsync** crea l'oggetto ServiceClientCredentials in base alle credenziali fornite nel file di configurazione locale. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Creare un asset di input e caricare un file locale nell'asset 

La funzione **CreateInputAsset** crea un nuovo [asset](https://docs.microsoft.com/rest/api/media/assets) di input e carica al suo interno il file video locale specificato. Questo asset viene usato come input per il processo di codifica. In Servizi multimediali v3 l'input di un processo può essere costituito da un asset oppure da contenuti resi disponibili all'account di Servizi multimediali tramite URL HTTPS. Per informazioni su come codificare contenuti da un URL HTTPS, vedere [questo](job-input-from-http-how-to.md) articolo.  

In Servizi multimediali v3 si usano le API di Archiviazione di Azure per caricare i file. Il frammento di codice .NET seguente illustra come eseguire questa operazione.

La funzione esegue queste azioni:

* Crea un asset. 
* Ottiene un [URL di firma di accesso condiviso](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) scrivibile al [contenitore nel servizio di archiviazione](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container) dell'asset.
* Carica il file nel contenitore nel servizio di archiviazione usando l'URL di firma di accesso condiviso.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Creare un asset di output per archiviare il risultato di un processo 

L'[asset](https://docs.microsoft.com/rest/api/media/assets) di output archivia il risultato del processo di codifica. Il progetto definisce la funzione **DownloadResults** che scarica i risultati dall'asset di output nella cartella "output", consentendo così di visualizzare ciò che si è ottenuto.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Creare una trasformazione e un processo che codifica il file caricato
Quando si codificano o si elaborano contenuti in Servizi multimediali, è prassi comune configurare le impostazioni di codifica come una serie di istruzioni e quindi inviare un oggetto **Job**, ovvero il processo per applicare tali istruzioni a un video. Inviando nuovi processi per ogni nuovo video, si applicano le istruzioni a tutti i video nella libreria. In Servizi multimediali una serie di istruzioni di questo tipo è definita trasformazione ed è rappresentata dall'oggetto **Transform**. Per altre informazioni, vedere [Trasformazioni e processi](transform-concept.md). L'esempio illustrato in questa esercitazione definisce una serie di istruzioni che codifica il video per eseguirne lo streaming a diversi tipi di dispositivi iOS e Android. 

#### <a name="transform"></a>Trasformare

Quando si crea una nuova istanza dell'oggetto [Transform](https://docs.microsoft.com/rest/api/media/transforms), è necessario specificare ciò che dovrà generare come output. Il parametro obbligatorio è costituito da un oggetto **TransformOutput**, come illustrato nel codice seguente. Ogni **TransformOutput** contiene un parametro **Preset**. In **Preset** sono descritte le istruzioni dettagliate delle operazioni di elaborazione di contenuti video e/o audio che devono essere usate per generare l'oggetto **TransformOutput** desiderato. L'esempio descritto in questo articolo è basato su un set di impostazioni predefinito denominato **AdaptiveStreaming**. Il set di impostazioni codifica il video di input in una tabella di coppie velocità in bit-risoluzione generata automaticamente in base alla risoluzione e alla velocità in bit dell'input e genera file ISO MP4 con standard video H.264 e audio AAC corrispondente a ogni coppia velocità in bit-risoluzione. Per informazioni su questo set di impostazioni, vedere [Generazione automatica di una tabella di coppie velocità in bit-risoluzione](autogen-bitrate-ladder.md).

È possibile usare un set di impostazioni predefinito EncoderNamedPreset oppure usare set di impostazioni personalizzati. Per altre informazioni, vedere [How to customize encoder presets](customize-encoder-presets-how-to.md) (Come personalizzare i set di impostazioni del codificatore).

Quando si crea un oggetto [Transform](https://docs.microsoft.com/rest/api/media/transforms), è necessario verificare se ne esiste già uno tramite il metodo **Get**, come illustrato nel codice seguente.  In Servizi multimediali v3 i metodi **Get** eseguiti su entità restituiscono **null** se determinano che l'entità non esiste, effettuando un controllo del nome senza distinzione tra maiuscole e minuscole.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Processo

Come indicato sopra, l'oggetto [Transform](https://docs.microsoft.com/rest/api/media/transforms) è la serie di istruzioni, mentre l'oggetto [Job](https://docs.microsoft.com/rest/api/media/jobs) è la richiesta effettiva inviata a Servizi multimediali per applicare l'oggetto **Transform** a determinati contenuti audio o video di input. L'oggetto **Job** specifica informazioni come la posizione del video di input e quella dell'output.

In questo esempio il video di input è stato caricato dal computer locale. Per informazioni su come codificare contenuti da un URL HTTPS, vedere [questo](job-input-from-http-how-to.md) articolo.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Attendere il completamento del processo

Il completamento del processo richiede tempo e al termine dell'elaborazione può essere opportuno ricevere una notifica. L'esempio di codice seguente illustra come eseguire il polling del servizio per determinare lo stato del [processo](https://docs.microsoft.com/rest/api/media/jobs). Il polling non è una procedura consigliata per le applicazioni di produzione poiché comporta rischi di latenza. Il polling può essere limitato se usato eccessivamente su un account. In alternativa, è preferibile che gli sviluppatori usino Griglia di eventi.

Griglia di eventi è un servizio progettato per garantire disponibilità elevata, coerenza nelle prestazioni e scalabilità dinamica. Con Griglia di eventi, le app possono rimanere in ascolto e reagire agli eventi praticamente da tutti i servizi di Azure, oltre che da origini personalizzate. Questo semplice servizio di gestione degli eventi, reattivo e basato su HTTP, consente di creare soluzioni efficienti tramite funzioni intelligenti di filtraggio e routing di eventi.  Vedere [Instradare gli eventi verso un endpoint Web personalizzato](job-state-events-cli-how-to.md).

L'oggetto **Job** assume progressivamente gli stati seguenti: **Scheduled**, **Queued**, **Processing**, **Finished** (lo stato finale). Se nel corso del processo si verifica un errore, viene restituito lo stato **Error**. Se il processo è in fase di annullamento, vengono restituiti lo stato **Canceling** e, al termine, lo stato **Canceled**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="get-a-streaminglocator"></a>Ottenere un oggetto StreamingLocator

Al termine della codifica, il passaggio successivo consiste nel rendere disponibile ai client il video nell'asset di output per la riproduzione. È possibile eseguire questa operazione in due passaggi: creare prima un oggetto [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) e dopo gli URL di streaming che possono essere usati dai client. 

Il processo di creazione di un oggetto **StreamingLocator** è detto pubblicazione. Per impostazione predefinita, l'oggetto **StreamingLocator** è valido immediatamente dopo l'esecuzione delle chiamate API e rimane tale finché non viene eliminato, a meno che non si configurino le ore di inizio e fine facoltative. 

Quando si crea un oggetto [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), è necessario specificare il parametro **StreamingPolicyName** desiderato. In questo esempio si eseguirà lo streaming di contenuti in chiaro, ovvero non crittografati, in modo da usare i criteri predefiniti per lo streaming non crittografato, **PredefinedStreamingPolicy.ClearStreamingOnly**.

> [!IMPORTANT]
> Quando si usa un oggetto [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) personalizzato, è necessario progettare un set limitato di tali criteri per l'account di Servizi multimediali e riusarli per gli oggetti StreamingLocator ogni volta che si devono usare gli stessi protocolli e opzioni di crittografia. L'account di Servizi multimediali prevede una quota per il numero di occorrenze di StreamingPolicy. Evitare quindi di creare un nuovo oggetto StreamingPolicy per ogni StreamingLocator.

Il codice seguente presuppone che la funzione venga chiamata con un parametro locatorName univoco.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Anche se l'esempio in questo argomento illustra un processo di streaming, è possibile usare la stessa chiamata per creare un oggetto StreamingLocator per la distribuzione di video tramite download progressivo.

### <a name="get-streaming-urls"></a>Ottenere URL di streaming

Ora che è stato creato un oggetto [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), è possibile ottenere gli URL di streaming, come illustrato in **GetStreamingURLs**. Per creare un URL, è necessario concatenare il nome host [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) e il percorso **StreamingLocator**. In questo esempio, per **StreamingEndpoint** viene usato il valore *default*. Quando si crea un account di Servizi multimediali per la prima volta, l'oggetto **StreamingEndpoint** *default* è in stato arrestato ed è quindi necessario chiamare **Start**.

> [!NOTE]
> In questo metodo è necessario specificare il parametro locatorName usato per la creazione dell'oggetto **StreamingLocator** per l'asset di output.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Pulire le risorse nell'account di Servizi multimediali

Normalmente è necessario pulire tutti gli oggetti tranne quelli che si prevede di riutilizzare. In genere si riutilizzano gli oggetti Transform e si salvano in modo permanente oggetti come StreamingLocator. Se dopo l'attività di sperimentazione si vuole pulire il proprio account, è necessario eliminare le risorse che non si prevede di riutilizzare.  Il codice seguente, ad esempio, elimina gli oggetti Job.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

1. Premere CTRL+F5 per eseguire l'applicazione *EncodeAndStreamFiles*.
2. Copiare uno degli URL di streaming dalla console.

Questo esempio visualizza gli URL che è possibile usare per riprodurre il video usando protocolli diversi:

![Output](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>Testare l'URL di streaming

Per testare lo streaming in questo articolo viene usato Azure Media Player. 

> [!NOTE]
> Se un lettore è ospitato in un sito https, assicurarsi di aggiornare l'URL impostandolo su "https".

1. Aprire un Web browser e passare [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Nella casella **URL** incollare uno degli URL di streaming ottenuto quando si è eseguita l'applicazione. 
3. Scegliere il pulsante **Update Player** (Aggiorna il lettore).

Azure Media Player può essere usato a scopo di test ma non deve essere usato in un ambiente di produzione. 

## <a name="clean-up-resources"></a>Pulire le risorse

Se nessuna delle risorse usate è più necessaria, compresi gli account di archiviazione e di Servizi multimediali creati per questa esercitazione, eliminare il gruppo di risorse creato in precedenza. A tal fine è possibile usare lo strumento **CloudShell**.

In **CloudShell** eseguire il comando seguente:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Gli SDK di Servizi multimediali di Azure v3 non sono thread-safe. Quando si sviluppa un'applicazione multithreading, è necessario generare e usare un nuovo oggetto AzureMediaServicesClient per ogni thread.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come eseguire il caricamento, la codifica e lo streaming del video, vedere l'articolo seguente: 

> [!div class="nextstepaction"]
> [Analizzare i video](analyze-videos-tutorial-with-api.md)
