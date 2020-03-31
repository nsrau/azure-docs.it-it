---
title: Crittografare i video con AES-128
titleSuffix: Azure Media Services
description: Informazioni su come crittografare i video con la crittografia a 128 bit di AES e come usare il servizio di distribuzione delle chiavi in Servizi multimediali di Azure.Learn how to encrypt video with AES 128-bit encryption and how to use the key delivery service in Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: 126700e6290650221a9cb9711b22472301409fca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974173"
---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>Esercitazione: Crittografare i video con AES-128 e utilizzare il servizio di distribuzione delle chiaviTutorial: Encrypt video with AES-128 and use the key delivery service

> [!NOTE]
> Anche se l'esercitazione usa esempi di [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet), i passaggi generali sono gli stessi per [API REST](https://docs.microsoft.com/rest/api/media/liveevents), [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest) o altri [SDK](media-services-apis-overview.md#sdks) supportati.

È possibile usare Servizi multimediali per distribuire flussi HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming crittografati con AES usando chiavi di crittografia a 128 bit. Servizi multimediali fornisce anche il servizio di distribuzione delle chiavi che distribuisce chiavi di crittografia agli utenti autorizzati. Se si desidera che Servizi multimediali eserizza il video in modo dinamico, la chiave di crittografia viene associata a un localizzatore di flusso e si configurano anche i criteri della chiave simmetrica. Quando un lettore richiede un flusso, Servizi multimediali utilizza la chiave specificata per crittografare dinamicamente il contenuto con AES-128. Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di chiave simmetrica specificati per tale chiave.

È possibile crittografare ogni asset con più tipi di crittografia (AES-128, PlayReady, Widevine, FairPlay). Per informazioni su come combinarli efficacemente, vedere [Streaming protocols and encryption types](content-protection-overview.md#streaming-protocols-and-encryption-types) (Protocolli di streaming e tipi di crittografia). Vedere anche [Come proteggere con DRM](protect-with-drm.md).

L'output dell'esempio in questo articolo include un URL per Azure Media Player, l'URL del manifesto e il token AES necessari per riprodurre il contenuto. L'esempio imposta la scadenza del token JSON Web (JWT) su 1 ora. È possibile aprire un browser e incollare l'URL risultante per avviare la pagina della demo di Azure Media Player con l'URL e il token già inseriti nel formato seguente: ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```.

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Scaricare l'esempio [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) descritto nell'articolo.
> * Iniziare a usare le API di Servizi multimediali con .NET SDK.
> * Creare un asset di output.
> * Creare una trasformazione di codifica.
> * Inviare un processo.
> * Attendere il completamento del processo.
> * Creare un criterio chiave simmetricaCreate a Content Key Policy
> * Configurare il criterio per l'utilizzo della restrizione del token JWT.
> * Creare un localizzatore di flusso.
> * Configurare Streaming Locator per crittografare il video con AES (ClearKey).
> * Ottenere un token di test.
> * Creare un URL di streaming.
> * Pulire le risorse.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione è necessario quanto segue.

* Vedere l'articolo [Panoramica della protezione del contenuto](content-protection-overview.md).
* Installare Visual Studio Code o Visual Studio.
* [Creare un account di Servizi multimediali di Azure](create-account-cli-quickstart.md).
* Ottenere le credenziali necessarie per utilizzare le API di Servizi multimediali seguendo [le API](access-api-cli-how-to.md)di Access .

## <a name="download-code"></a>Scaricare il codice

Clonare nel computer un repository GitHub contenente l'esempio .NET completo trattato in questo articolo usando il comando seguente:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

L'esempio "Encrypt with AES-128" (Crittografia con AES) si trova nella cartella [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES).

> [!NOTE]
> L'esempio crea risorse univoche ogni volta che si esegue l'app. In genere, si riutilizzeranno le risorse esistenti, ad esempio trasformazioni e criteri (se le risorse esistenti dispongono di configurazioni necessarie).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Iniziare a usare le API di Servizi multimediali con .NET SDK

Per iniziare a usare le API di Servizi multimediali con .NET, creare un oggetto **AzureMediaServicesClient.To** start using Media Services APIs with .NET, create an AzureMediaServicesClient object. Per eseguire questa operazione, specificare le credenziali necessarie per consentire al client di connettersi ad Azure tramite Azure AD. Nel codice clonato all'inizio dell'articolo, la funzione **GetCredentialsAsync** crea l'oggetto ServiceClientCredentials in base alle credenziali fornite nel file di configurazione locale.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Creare un asset di output  

L'[asset](https://docs.microsoft.com/rest/api/media/assets) di output archivia il risultato del processo di codifica.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Ottenere o creare una trasformazione di codifica

Quando si crea una nuova istanza dell'oggetto [Transform](https://docs.microsoft.com/rest/api/media/transforms), è necessario specificare ciò che dovrà generare come output. Il parametro obbligatorio è costituito da un oggetto **TransformOutput**, come illustrato nel codice seguente. Ogni **TransformOutput** contiene un parametro **Preset**. In **Preset** sono descritte le istruzioni dettagliate delle operazioni di elaborazione di contenuti video e/o audio che devono essere usate per generare l'oggetto **TransformOutput** desiderato. L'esempio descritto in questo articolo è basato su un set di impostazioni predefinito denominato **AdaptiveStreaming**. Il Preset codifica il video di input in una scala bitrate generata automaticamente (coppie a risoluzione bitrate) in base alla risoluzione di input e alla velocità in bitrate, quindi produce file MP4 ISO con audio H.264 e AAC corrispondenti a ogni coppia di risoluzione bitrate.

Prima di creare una nuova [trasformazione](https://docs.microsoft.com/rest/api/media/transforms), verificare se ne esiste già una utilizzando il metodo **Get** , come illustrato nel codice seguente. In Servizi multimediali v3 i metodi **Get** eseguiti su entità restituiscono **null** se determinano che l'entità non esiste, effettuando un controllo del nome senza distinzione tra maiuscole e minuscole.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Inviare il processo

Come indicato sopra, l'oggetto [Transform](https://docs.microsoft.com/rest/api/media/transforms) è la serie di istruzioni, mentre l'oggetto [Job](https://docs.microsoft.com/rest/api/media/jobs) è la richiesta effettiva inviata a Servizi multimediali per applicare l'oggetto **Transform** a determinati contenuti audio o video di input. Il **processo** specifica informazioni come la posizione del video di input e quella dell'output.

In questa esercitazione viene creato l'input del processo in base a un file inserito direttamente da un URL di [origine hTTPs.](job-input-from-http-how-to.md)

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Attendere il completamento del processo

Il completamento del processo richiede qualche istante. Al termine dell'operazione, si vorrà ricevere una notifica. L'esempio di codice seguente illustra come eseguire il polling del servizio per determinare lo stato del [processo](https://docs.microsoft.com/rest/api/media/jobs). Il polling non è una procedura consigliata per le app di produzione a causa dei rischi di latenza. Il polling può essere limitato se usato eccessivamente su un account. In alternativa, è preferibile che gli sviluppatori usino Griglia di eventi. Per altre informazioni, [Instradare gli eventi a un endpoint Web personalizzato](job-state-events-cli-how-to.md).

L'oggetto **Job** assume progressivamente gli stati seguenti: **Scheduled**, **Queued**, **Processing**, **Finished** (lo stato finale). Se nel corso del processo si verifica un errore, viene restituito lo stato **Errore**. Se il processo è in fase di annullamento, vengono restituiti lo stato **Annullamento in corso** e, al termine, lo stato **Annullato**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Creare un criterio chiave simmetricaCreate a Content Key Policy

Una chiave simmetrica consente l'accesso sicuro agli asset. È necessario creare un **criterio chiave di contenuto** che configuri la modalità di recapito della chiave simmetrica ai client finali. La chiave simmetrica è associata a **Streaming Locator**. Servizi multimediali fornisce anche il servizio di distribuzione delle chiavi che distribuisce chiavi di crittografia agli utenti autorizzati.

Quando un lettore richiede un flusso, Servizi multimediali utilizza la chiave specificata per crittografare dinamicamente il contenuto (in questo caso, utilizzando la crittografia AES). Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di chiave simmetrica specificati per tale chiave.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Creare un localizzatore di streaming

Al termine della codifica e dopo l'impostazione dei criteri di chiave simmetrica, il passaggio successivo consiste nel rendere disponibile ai client il video nell'asset di output per la riproduzione. Rendere il video disponibile in due passaggi:

1. Creare un [localizzatore di flusso](https://docs.microsoft.com/rest/api/media/streaminglocators).
2. Creare gli URL di streaming che i client possono usare.

Il processo di creazione del **localizzatore** di flusso è denominato pubblicazione. Per impostazione predefinita, il **localizzatore** di flusso è valido immediatamente dopo aver eseguito le chiamate API. Dura fino a quando non viene eliminato, a meno che non si configurino l'ora di inizio e di fine facoltativa.

Quando si crea un [localizzatore](https://docs.microsoft.com/rest/api/media/streaminglocators)di flusso , è necessario specificare **l'oggetto StreamingPolicyName**desiderato. In questa esercitazione viene usato uno dei Criteri di gruppo PredefinedStreamingPolicies, che indica a Servizi multimediali di Azure come pubblicare il contenuto per lo streaming. In questo esempio viene applicata la crittografia della busta AES (questa crittografia è nota anche come crittografia ClearKey perché la chiave viene recapitata al client di riproduzione tramite HTTPS e non una licenza DRM).

> [!IMPORTANT]
> Se si usa un oggetto [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) personalizzato, è necessario progettare un set limitato di tali criteri per l'account di Servizi multimediali e riusare questi criteri per i localizzatori di streaming ogni volta che si devono usare gli stessi protocolli e le stesse opzioni di crittografia. L'account di Servizi multimediali prevede una quota per il numero di occorrenze di StreamingPolicy. Non è consigliabile creare un nuovo StreamingPolicy per ogni localizzatore di flusso.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Ottenere un token di test

In questa esercitazione si specifica una restrizione del token per i criteri di chiave simmetrica. I criteri con restrizione del token richiedono la presenza di un token rilasciato da un servizio token di sicurezza. Servizi multimediali supporta i token nel formato [JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) ed è quello che configuriamo nell'esempio.

ContentKeyIdentifierClaim viene utilizzato nei criteri **chiave del contenuto**, il che significa che il token presentato al servizio di distribuzione delle chiavi deve avere l'identificatore della chiave simmetrica. Nell'esempio non è stata specificata una chiave simmetrica durante la creazione del localizzatore di streaming, il sistema ne ha creata una casuale. Per generare il token di test, è necessario ottenere il ContentKeyId per inserire il ContentKeyIdentifierClaim attestazione.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Creare un URL di streaming DASH

Ora che [il localizzatore](https://docs.microsoft.com/rest/api/media/streaminglocators) di streaming è stato creato, è possibile ottenere gli URL di streaming. Per creare un URL, è necessario concatenare il nome host [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) e il percorso **streaming Locator.** In questo esempio, viene usato l'*endpoint di streaming* **predefinito**. Quando si crea per la prima volta un account del servizio multimediale, questo endpoint di **streaming** *predefinito* si troverà in uno stato arrestato, pertanto è necessario chiamare **Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Pulire le risorse nell'account di Servizi multimediali

In genere, è consigliabile pulire tutto tranne gli oggetti che si prevede di riutilizzare (in genere, si riutilizzeranno le trasformazioni, i localizzatori di streaming e così via). Se dopo l'attività di sperimentazione si vuole pulire l'account, eliminare le risorse che non si prevede di riutilizzare. Il codice seguente, ad esempio, elimina gli oggetti Job:

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
