---
title: Crittografare video con AES-128
titleSuffix: Azure Media Services
description: Informazioni su come crittografare i video con la crittografia AES a 128 bit e come usare il servizio di distribuzione delle chiavi in Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 5347479d32dc9f4909483dc63891e8057fd7ff86
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89289334"
---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>Esercitazione: Crittografare video con AES-128 e usare il servizio di distribuzione delle chiavi

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Anche se l'esercitazione usa esempi di [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet), i passaggi generali sono gli stessi per [API REST](/rest/api/media/liveevents), [CLI](/cli/azure/ams/live-event?view=azure-cli-latest) o altri [SDK](media-services-apis-overview.md#sdks) supportati.

È possibile usare Servizi multimediali per distribuire flussi HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming crittografati con AES usando chiavi di crittografia a 128 bit. Servizi multimediali fornisce anche il servizio di distribuzione delle chiavi che distribuisce chiavi di crittografia agli utenti autorizzati. Per consentire a Servizi multimediali di crittografare il video in modo dinamico, è necessario associare la chiave di crittografia a un localizzatore di streaming e anche configurare i criteri di chiave simmetrica. Quando un lettore richiede un flusso, Servizi multimediali usa la chiave specificata per crittografare dinamicamente i contenuti con AES-128. Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di chiave simmetrica specificati per tale chiave.

È possibile crittografare ogni asset con più tipi di crittografia (AES-128, PlayReady, Widevine, FairPlay). Per informazioni su come combinarli efficacemente, vedere [Streaming protocols and encryption types](content-protection-overview.md#streaming-protocols-and-encryption-types) (Protocolli di streaming e tipi di crittografia). Vedere anche [Come proteggere con DRM](protect-with-drm.md).

L'output dell'esempio in questo articolo include un URL di Azure Media Player, l'URL del manifesto e il token AES necessari per riprodurre il contenuto. L'esempio imposta la scadenza del token JWT (JSON Web Token) su 1 ora. È possibile aprire un browser e incollare l'URL risultante per avviare la pagina della demo di Azure Media Player con l'URL e il token già inseriti nel formato seguente: ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```.

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Scaricare l'esempio [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) descritto nell'articolo.
> * Iniziare a usare le API di Servizi multimediali con .NET SDK.
> * Creare un asset di output.
> * Creare una trasformazione di codifica.
> * Inviare un processo.
> * Attendere il completamento del processo.
> * Creare i criteri chiave simmetrica
> * Configurare i criteri per l'uso della restrizione del token JWT.
> * Creare un localizzatore di streaming.
> * Configurare il localizzatore di streaming per crittografare il video con AES (ClearKey).
> * Ottenere un token di test.
> * Creare un URL di streaming.
> * Pulire le risorse.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione è necessario quanto segue.

* Vedere l'articolo [Panoramica della protezione del contenuto](content-protection-overview.md).
* Installare Visual Studio Code o Visual Studio.
* [Creare un account di Servizi multimediali di Azure](./create-account-howto.md).
* Ottenere le credenziali necessarie per usare le API di Servizi multimediali seguendo [Accedere alle API](./access-api-howto.md).

## <a name="download-code"></a>Scaricare il codice

Clonare nel computer un repository GitHub contenente l'esempio .NET completo trattato in questo articolo usando il comando seguente:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

L'esempio "Encrypt with AES-128" (Crittografia con AES) si trova nella cartella [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES).

> [!NOTE]
> L'esempio crea risorse univoche ogni volta che si esegue l'app. In genere si riutilizzano le risorse esistenti, ad esempio criteri e trasformazioni, se hanno le configurazioni richieste.

## <a name="start-using-media-services-apis-with-net-sdk"></a>Iniziare a usare le API di Servizi multimediali con .NET SDK

Per iniziare a usare le API di Servizi multimediali con .NET, creare un oggetto **AzureMediaServicesClient**. Per eseguire questa operazione, specificare le credenziali necessarie per consentire al client di connettersi ad Azure tramite Azure AD. Nel codice clonato all'inizio dell'articolo, la funzione **GetCredentialsAsync** crea l'oggetto ServiceClientCredentials in base alle credenziali fornite nel file di configurazione locale.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Creare un asset di output  

L'[asset](/rest/api/media/assets) di output archivia il risultato del processo di codifica.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Ottenere o creare una trasformazione di codifica

Quando si crea una nuova istanza dell'oggetto [Transform](/rest/api/media/transforms), è necessario specificare ciò che dovrà generare come output. Il parametro obbligatorio è costituito da un oggetto **TransformOutput**, come illustrato nel codice seguente. Ogni **TransformOutput** contiene un parametro **Preset**. In **Preset** sono descritte le istruzioni dettagliate delle operazioni di elaborazione di contenuti video e/o audio che devono essere usate per generare l'oggetto **TransformOutput** desiderato. L'esempio descritto in questo articolo è basato su un set di impostazioni predefinito denominato **AdaptiveStreaming**. Il set di impostazioni codifica il video di input in una tabella di coppie velocità in bit-risoluzione generata automaticamente in base alla risoluzione e alla velocità in bit dell'input e quindi genera file ISO MP4 con standard video H.264 e audio AAC corrispondente a ogni coppia velocità in bit-risoluzione.

Prima di creare un nuovo oggetto [Transform](/rest/api/media/transforms), verificare se ne esiste già uno tramite il metodo **Get**, come illustrato nel codice seguente. In Servizi multimediali v3 i metodi **Get** eseguiti su entità restituiscono **null** se determinano che l'entità non esiste, effettuando un controllo del nome senza distinzione tra maiuscole e minuscole.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Inviare il processo

Come indicato sopra, l'oggetto [Transform](/rest/api/media/transforms) è la serie di istruzioni, mentre l'oggetto [Job](/rest/api/media/jobs) è la richiesta effettiva inviata a Servizi multimediali per applicare l'oggetto **Transform** a determinati contenuti audio o video di input. Il **processo** specifica informazioni come la posizione del video di input e quella dell'output.

In questa esercitazione si crea l'input del processo in base a un file che viene inserito direttamente da un [URL di origine HTTPS](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Attendere il completamento del processo

Il completamento del processo richiede qualche istante. Al termine dell'operazione, si vorrà ricevere una notifica. L'esempio di codice seguente illustra come eseguire il polling del servizio per determinare lo stato del [processo](/rest/api/media/jobs). Il polling non è una procedura consigliata per le app di produzione a causa dei rischi di latenza. Il polling può essere limitato se usato eccessivamente su un account. In alternativa, è preferibile che gli sviluppatori usino Griglia di eventi. Per altre informazioni, [Instradare gli eventi a un endpoint Web personalizzato](job-state-events-cli-how-to.md).

L'oggetto **Job** assume progressivamente gli stati seguenti: **Scheduled**, **Queued**, **Processing**, **Finished** (stato finale). Se nel corso del processo si verifica un errore, viene restituito lo stato **Errore**. Se il processo è in fase di annullamento, vengono restituiti lo stato **Annullamento in corso** e, al termine, lo stato **Annullato**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Creare i criteri chiave simmetrica

Una chiave simmetrica consente l'accesso sicuro agli asset. È necessario creare un **criterio di chiave simmetrica** che consente di configurare la modalità di distribuzione della chiave simmetrica ai client finali. La chiave simmetrica è associata al **localizzatore di streaming**. Servizi multimediali fornisce anche il servizio di distribuzione delle chiavi che distribuisce chiavi di crittografia agli utenti autorizzati.

Quando un flusso viene richiesto da un lettore, Servizi multimediali usa la chiave specificata per crittografare dinamicamente il contenuto, in questo caso mediante AES. Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di chiave simmetrica specificati per tale chiave.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Creare un localizzatore di streaming

Al termine della codifica e dopo l'impostazione dei criteri di chiave simmetrica, il passaggio successivo consiste nel rendere disponibile ai client il video nell'asset di output per la riproduzione. Il video viene reso disponibile in due passaggi:

1. Creare un [localizzatore di streaming](/rest/api/media/streaminglocators).
2. Creare gli URL di streaming che i client possono usare.

Il processo di creazione di un **localizzatore di streaming** è detto pubblicazione. Per impostazione predefinita, il **localizzatore di streaming** diventa valido subito dopo aver effettuato le chiamate API. Rimane valido fino all'eliminazione, a meno che non si configurino le ore di inizio e di fine facoltative.

Quando si crea un [localizzatore di streaming](/rest/api/media/streaminglocators), sarà necessario specificare il parametro **StreamingPolicyName** desiderato. In questa esercitazione si usa uno dei PredefinedStreamingPolicies che indica a Servizi multimediali di Azure come pubblicare il contenuto per lo streaming. In questo esempio si applica la crittografia AES Envelope, nota anche come crittografia ClearKey perché la chiave viene distribuita al client di riproduzione tramite HTTPS e non una licenza DRM.

> [!IMPORTANT]
> Se si usa un oggetto [StreamingPolicy](/rest/api/media/streamingpolicies) personalizzato, è necessario progettare un set limitato di tali criteri per l'account di Servizi multimediali e riusare questi criteri per i localizzatori di streaming ogni volta che si devono usare gli stessi protocolli e le stesse opzioni di crittografia. L'account di Servizi multimediali prevede una quota per il numero di occorrenze di StreamingPolicy. Evitare quindi di creare nuovi criteri di streaming per ogni localizzatore di streaming.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Ottenere un token di test

In questa esercitazione si specifica una restrizione del token per i criteri di chiave simmetrica. I criteri con restrizione del token richiedono la presenza di un token rilasciato da un servizio token di sicurezza. Servizi multimediali supporta i token nel formato [JWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) e saranno configurati in questo modo nell'esempio.

Nel **criterio di chiave simmetrica** viene usato ContentKeyIdentifierClaim e ciò significa che il token presentato al servizio di distribuzione delle chiavi deve contenere l'identificatore della chiave simmetrica in esso contenuta. Nell'esempio non è stata specificata una chiave simmetrica durante la creazione del localizzatore di streaming, perché il sistema ne ha creata automaticamente una casuale. Per generare il token di test, è necessario ottenere il ContentKeyId da inserire nell'attestazione ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Creare un URL di streaming DASH

Ora che è stato creato il [localizzatore di streaming](/rest/api/media/streaminglocators), è possibile ottenere gli URL di streaming. Per creare un URL, è necessario concatenare il nome host [StreamingEndpoint](/rest/api/media/streamingendpoints) e il percorso del **localizzatore di streaming**. In questo esempio viene usato l'*endpoint di streaming* **predefinito**. Quando si crea un account di Servizi multimediali per la prima volta, l'*endpoint di streaming* **predefinito** è in stato arrestato ed è quindi necessario chiamare **Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Pulire le risorse nell'account di Servizi multimediali

Normalmente è necessario pulire tutti gli oggetti tranne quelli che si prevede di riutilizzare. In genere si riutilizzano le trasformazioni, i localizzatori di streaming e così via. Se dopo l'attività di sperimentazione si vuole pulire l'account, eliminare le risorse che non si prevede di riutilizzare. Ad esempio, il codice seguente elimina il processo, gli asset creati e i criteri di chiave simmetrica:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Pulire le risorse

Se nessuna delle risorse usate è più necessaria, compresi gli account di archiviazione e di Servizi multimediali creati per questa esercitazione, eliminare il gruppo di risorse creato in precedenza.

Eseguire il comando dell'interfaccia della riga di comando seguente:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="additional-notes"></a>Note aggiuntive

* Widevine è un servizio fornito da Google Inc. e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google Inc.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Proteggere con DRM](protect-with-drm.md)
