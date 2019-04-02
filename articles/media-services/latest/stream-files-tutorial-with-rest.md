---
title: Codificare un file remoto basato su URL ed eseguire lo streaming con Servizi multimediali di Azure - REST | Microsoft Docs
description: Seguire i passaggi di questa esercitazione per codificare un file basato su un URL ed eseguire lo streaming dei contenuti con Servizi multimediali di Azure usando REST.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/18/2019
ms.author: juliako
ms.openlocfilehash: 704c26670f9fe2a3d7d0011fee4621a8e8c33028
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314961"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---rest"></a>Esercitazione: Codificare un file remoto basato su URL ed eseguire lo streaming del video - REST

Servizi multimediali di Azure consente di codificare i file multimediali nei formati che possono essere riprodotti in una vasta gamma di browser e dispositivi. Ad esempio, potrebbe essere necessario trasmettere il contenuto nei formati HLS o MPEG DASH di Apple. Prima dello streaming, è consigliabile codificare il file multimediale digitale di alta qualità. Per indicazioni per la codifica, vedere i [concetti correlati alla codifica](encoding-concept.md).

Questa esercitazione illustra come codificare un file basato su un URL ed eseguire lo streaming del video con Servizi multimediali di Azure usando REST. 

![Riprodurre il video](./media/stream-files-tutorial-with-api/final-video.png)

Questa esercitazione illustra come:    

> [!div class="checklist"]
> * Creare un account di Servizi multimediali
> * Accedere all'API di Servizi multimediali
> * Scaricare i file Postman
> * Configurare Postman
> * Inviare richieste con Postman
> * Testare l'URL di streaming
> * Pulire le risorse

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

- [Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md).

    Assicurarsi di ricordare i valori usati per il nome del gruppo di risorse e il nome dell'account di Servizi multimediali.

- Installare il client REST di [Postman](https://www.getpostman.com/) per eseguire le API REST mostrate in alcune delle esercitazioni REST di AMS. 

    Si sta usando **Postman** ma si può usare qualsiasi strumento REST. Altre alternative possibili: **Visual Studio Code** con il plug-in REST o **Telerik Fiddler**. 

## <a name="download-postman-files"></a>Scaricare i file Postman

Clonare un repository di GitHub che contiene i file di raccolta e ambiente Postman.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="configure-postman"></a>Configurare Postman

Questa sezione consente di configurare Postman.

### <a name="configure-the-environment"></a>Configurare l'ambiente 

1. Aprire **Postman**.
2. A destra dello schermo, selezionare l'opzione **Manage environment** (Gestisci ambiente).

    ![Gestire l'ambiente](./media/develop-with-postman/postman-import-env.png)
4. Dalla finestra di dialogo **Manage environment** (Gestisci ambiente), fare clic su **Import** (Importa).
2. Individuare il file `Azure Media Service v3 Environment.postman_environment.json` scaricato quando è stato clonato `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. Viene aggiunto l'ambiente **Azure Media Service v3 Environment**.

    > [!Note]
    > Aggiornare le variabili di accesso con i valori ottenuti dalla sezione **Accedere all'API di Servizi multimediali** precedente.

7. Fare doppio clic sul file selezionato e immettere i valori ottenuti seguendo la procedura per l'[accesso all'API](#access-the-media-services-api).
8. Chiudere la finestra di dialogo.
9. Selezionare l'ambiente **Azure Media Service v3 Environment** nell'elenco a discesa.

    ![Scegliere l'ambiente](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Configurare la raccolta

1. Fare clic su **Import** (Importa) per importare il file di raccolta.
1. Individuare il file `Media Services v3.postman_collection.json` scaricato quando è stato clonato `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
3. Scegliere il file **Media Services v3.postman_collection.json**.

    ![Importare un file](./media/develop-with-postman/postman-import-collection.png)

## <a name="send-requests-using-postman"></a>Inviare richieste con Postman

In questa sezione vengono inviate le richieste rilevanti per la codifica e la creazione di URL in modo da poter trasmettere il file. In particolare, vengono inviate le richieste seguenti:

1. Ottenere il token di Azure AD per l'autenticazione di un'entità servizio
2. Creare un asset di output
3. Creare una **trasformazione**
4. Creare un **processo**
5. Creare un **localizzatore di streaming**
6. Elencare i percorsi del **localizzatore di streaming**

> [!Note]
>  In questa esercitazione si presuppone che tutte le risorse vengano create con nomi univoci.  

### <a name="get-azure-ad-token"></a>Ottenere il token di Azure AD 

1. Nella finestra a sinistra di Postman selezionare "Step 1: Get AAD Auth token" (Passaggio 1: Ottenere un token di autenticazione AAD).
2. Selezionare quindi "Get Azure AD Token for Service Principal Authentication" (Ottieni token Azure AD per autenticazione basata su entità servizio).
3. Fare clic su **Invia**.

    Viene inviata l'operazione **POST** seguente.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. La risposta viene restituita con il token e imposta la variabile di ambiente "AccessToken" sul valore del token. Per visualizzare il codice che imposta "AccessToken", fare clic sulla scheda **Test**. 

    ![Ottenere il token AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)

### <a name="create-an-output-asset"></a>Creare un asset di output

L'[asset](https://docs.microsoft.com/rest/api/media/assets) di output archivia il risultato del processo di codifica. 

1. Nella finestra a sinistra di Postman selezionare "Assets" (Asset).
2. Selezionare quindi "Create or update an Asset" (Crea o aggiorna un asset).
3. Fare clic su **Invia**.

    * Viene inviata l'operazione **PUT** seguente:

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/assets/:assetName?api-version={{api-version}}
        ```
    * Il corpo dell'operazione è il seguente:

        ```json
        {
        "properties": {
            "description": "My Asset",
            "alternateId" : "some GUID"
         }
        }
        ```

### <a name="create-a-transform"></a>Creare una trasformazione

Quando si codificano o si elaborano contenuti in Servizi multimediali, è prassi comune configurare le impostazioni di codifica come una serie di istruzioni e quindi inviare un oggetto **Job**, ovvero il processo per applicare tali istruzioni a un video. Inviando nuovi processi per ogni nuovo video, si applicano le istruzioni a tutti i video nella libreria. In Servizi multimediali una serie di istruzioni di questo tipo è definita trasformazione ed è rappresentata dall'oggetto **Transform**. Per altre informazioni, vedere [Trasformazioni e processi](transform-concept.md). L'esempio illustrato in questa esercitazione definisce una serie di istruzioni che codifica il video per eseguirne lo streaming a diversi tipi di dispositivi iOS e Android. 

Quando si crea una nuova istanza dell'oggetto [Transform](https://docs.microsoft.com/rest/api/media/transforms), è necessario specificare ciò che dovrà generare come output. Il parametro obbligatorio è un oggetto **TransformOutput**. Ogni **TransformOutput** contiene un parametro **Preset**. In **Preset** sono descritte le istruzioni dettagliate delle operazioni di elaborazione di contenuti video e/o audio che devono essere usate per generare l'oggetto **TransformOutput** desiderato. L'esempio descritto in questo articolo è basato su un set di impostazioni predefinito denominato **AdaptiveStreaming**. Il set di impostazioni codifica il video di input in una tabella di coppie velocità in bit-risoluzione generata automaticamente in base alla risoluzione e alla velocità in bit dell'input e genera file ISO MP4 con standard video H.264 e audio AAC corrispondente a ogni coppia velocità in bit-risoluzione. Per informazioni su questo set di impostazioni, vedere [Generazione automatica di una tabella di coppie velocità in bit-risoluzione](autogen-bitrate-ladder.md).

È possibile usare un set di impostazioni predefinito EncoderNamedPreset oppure usare set di impostazioni personalizzati. 

> [!Note]
> Quando si crea un oggetto [Transform](https://docs.microsoft.com/rest/api/media/transforms), è prima necessario verificare se ne esiste già uno tramite il metodo **Get**. In questa esercitazione si presuppone che la trasformazione venga creata con un nome univoco.

1. Nella finestra a sinistra di Postman selezionare "Encoding and Analysis" (Codifica e analisi).
2. Selezionare quindi "Create Transform" (Crea trasformazione).
3. Fare clic su **Invia**.

    * Viene inviata l'operazione **PUT** seguente.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
        ```
    * Il corpo dell'operazione è il seguente:

        ```json
        {
            "properties": {
                "description": "Standard Transform using an Adaptive Streaming encoding preset from the library of built-in Standard Encoder presets",
                "outputs": [
                    {
                    "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                    "preset": {
                        "@odata.type": "#Microsoft.Media.BuiltInStandardEncoderPreset",
                        "presetName": "AdaptiveStreaming"
                    }
                    }
                ]
            }
        }
        ```

### <a name="create-a-job"></a>Creare un processo

L'oggetto [Job](https://docs.microsoft.com/rest/api/media/jobs) è la richiesta effettiva inviata a Servizi multimediali per applicare l'oggetto **Transform** creato a determinati contenuti audio o video di input. L'oggetto **Job** specifica informazioni come la posizione del video di input e quella dell'output.

In questo esempio l'input del processo è basato su un URL HTTPS ("https:\//nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/").

1. Nella finestra a sinistra di Postman selezionare "Encoding and Analysis" (Codifica e analisi).
2. Selezionare quindi "Create or Update Job" (Crea o aggiorna il processo).
3. Fare clic su **Invia**.

    * Viene inviata l'operazione **PUT** seguente.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
        ```
    * Il corpo dell'operazione è il seguente:

        ```json
        {
        "properties": {
            "input": {
            "@odata.type": "#Microsoft.Media.JobInputHttp",
            "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
            "files": [
                    "Ignite-short.mp4"
                ]
            },
            "outputs": [
            {
                "@odata.type": "#Microsoft.Media.JobOutputAsset",
                "assetName": "testAsset1"
            }
            ]
        }
        }
        ```

Il completamento del processo richiede tempo e al termine dell'elaborazione può essere opportuno ricevere una notifica. Per visualizzare lo stato del processo, è consigliabile usare Griglia di eventi. Si tratta di un servizio progettato per garantire disponibilità elevata, coerenza nelle prestazioni e scalabilità dinamica. Con Griglia di eventi, le app possono rimanere in ascolto e reagire agli eventi praticamente da tutti i servizi di Azure, oltre che da origini personalizzate. Questo semplice servizio di gestione degli eventi, reattivo e basato su HTTP, consente di creare soluzioni efficienti tramite funzioni intelligenti di filtraggio e routing di eventi.  Vedere [Instradare gli eventi verso un endpoint Web personalizzato](job-state-events-cli-how-to.md).

L'oggetto **Job** assume progressivamente gli stati seguenti: **Scheduled**, **Queued**, **Processing**, **Finished** (stato finale). Se nel corso del processo si verifica un errore, viene restituito lo stato **Error**. Se il processo è in fase di annullamento, vengono restituiti lo stato **Canceling** e, al termine, lo stato **Canceled**.

#### <a name="job-error-codes"></a>Codici errore dei processi

Vedere i [codici di errore](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

### <a name="create-a-streaming-locator"></a>Creare un localizzatore di streaming

Al termine della codifica del processo, il passaggio successivo consiste nel rendere disponibile ai client il video nell'**asset** di output per la riproduzione. È possibile eseguire questa operazione in due passaggi: creare prima un [localizzatore di streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) e dopo gli URL di streaming che possono essere usati dai client. 

Il processo di creazione di un **localizzatore di streaming** è detto pubblicazione. Per impostazione predefinita, il **localizzatore di streaming** è valido immediatamente dopo l'esecuzione delle chiamate API e rimane tale finché non viene eliminato, a meno che non si configurino le ore di inizio e fine facoltative. 

Quando si crea un [localizzatore di streaming](https://docs.microsoft.com/rest/api/media/streaminglocators), è necessario specificare il parametro **StreamingPolicyName** desiderato. In questo esempio si eseguirà lo streaming di contenuti in chiaro, ovvero non crittografati, in modo da usare i criteri predefiniti per lo streaming non crittografato, **PredefinedStreamingPolicy.ClearStreamingOnly**.

> [!IMPORTANT]
> Quando si usa un oggetto [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) personalizzato, è necessario progettare un set limitato di tali criteri per l'account di Servizi multimediali e riusarli per gli oggetti StreamingLocator ogni volta che si devono usare gli stessi protocolli e opzioni di crittografia. 

L'account di Servizi multimediali prevede una quota per il numero di occorrenze di **criteri di streaming**. Evitare quindi di creare nuovi **criteri di streaming** per ogni **localizzatore di streaming**.

1. Nella finestra a sinistra di Postman selezionare "Streaming Policies" (Criteri di streaming).
2. Selezionare quindi "Create a Streaming Locator" (Crea un localizzatore di streaming).
3. Fare clic su **Invia**.

    * Viene inviata l'operazione **PUT** seguente.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingPolicies/:streamingPolicyName?api-version={{api-version}}
        ```
    * Il corpo dell'operazione è il seguente:

        ```json
        {
            "properties":{
            "assetName": "{{assetName}}",
            "streamingPolicyName": "{{streamingPolicyName}}"
            }
        }
        ```

### <a name="list-paths-and-build-streaming-urls"></a>Elencare i percorsi e generare gli URL di streaming

#### <a name="list-paths"></a>Elencare i percorsi

Ora che è stato creato il [localizzatore di streaming](https://docs.microsoft.com/rest/api/media/streaminglocators), è possibile ottenere gli URL di streaming

1. Nella finestra a sinistra di Postman selezionare "Streaming Policies" (Criteri di streaming).
2. Selezionare quindi "List Paths" (Elenca percorsi).
3. Fare clic su **Invia**.

    * Viene inviata l'operazione **POST** seguente.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingLocators/:streamingLocatorName/listPaths?api-version={{api-version}}
        ```
        
    * L'operazione è senza corpo:
        
4. Prendere nota di uno dei percorsi che si vuole usare per lo streaming. Verrà usato nella sezione successiva. In questo caso, sono stati restituiti i percorsi seguenti:
    
    ```
    "streamingPaths": [
        {
            "streamingProtocol": "Hls",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)"
            ]
        },
        {
            "streamingProtocol": "Dash",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=mpd-time-csf)"
            ]
        },
        {
            "streamingProtocol": "SmoothStreaming",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest"
            ]
        }
    ]
    ```

#### <a name="build-the-streaming-urls"></a>Creare gli URL di streaming

In questa sezione verrà creato un URL di streaming HLS. Gli URL sono costituiti dai valori seguenti:

1. Il protocollo usato per l'invio dei dati. In questo caso "https".

    > [!NOTE]
    > Se un lettore è ospitato in un sito https, assicurarsi di aggiornare l'URL impostandolo su "https".

2. Nome host di StreamingEndpoint. In questo caso, il nome è "amsaccount-usw22.streaming.media.azure.net".

    Per ottenere il nome host, è possibile usare l'operazione GET seguente:
    
    ```
    https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingEndpoints/default?api-version={{api-version}}
    ```
    
3. Un percorso ottenuto nella sezione precedente (Elencare i percorsi).  

L'URL HLS generato risultante è il seguente:

```
https://amsaccount-usw22.streaming.media.azure.net/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)
```

## <a name="test-the-streaming-url"></a>Testare l'URL di streaming


> [!NOTE]
> Verificare che l'**endpoint di streaming** da cui si vuole trasmettere il contenuto sia in esecuzione.

Per testare lo streaming, in questo articolo viene usato Azure Media Player. 

1. Aprire un Web browser e passare [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Nella casella **URL** incollare l'URL generato. 
3. Scegliere il pulsante **Update Player** (Aggiorna il lettore).

Azure Media Player può essere usato a scopo di test ma non deve essere usato in un ambiente di produzione. 

## <a name="clean-up-resources-in-your-media-services-account"></a>Pulire le risorse nell'account di Servizi multimediali

Normalmente è necessario pulire tutti gli oggetti tranne quelli che si prevede di riutilizzare. In genere si riutilizzano le **trasformazioni** e si salvano in modo permanente i **localizzatori di streaming**. Se dopo l'attività di sperimentazione si vuole pulire il proprio account, è necessario eliminare le risorse che non si prevede di riutilizzare.  

Per eliminare una risorsa, selezionare l'operazione "Elimina" per qualsiasi risorsa che si vuole eliminare.

## <a name="clean-up-resources"></a>Pulire le risorse

Se nessuna delle risorse usate è più necessaria, compresi gli account di archiviazione e di Servizi multimediali creati per questa esercitazione, eliminare il gruppo di risorse creato in precedenza.  

Eseguire il comando dell'interfaccia della riga di comando seguente:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come eseguire il caricamento, la codifica e lo streaming del video, vedere l'articolo seguente: 

> [!div class="nextstepaction"]
> [Analizzare i video](analyze-videos-tutorial-with-api.md)
