---
title: Usare la crittografia dinamica AES di Servizi multimediali di Azure | Microsoft Docs
description: Distribuire i contenuti crittografati con chiavi di crittografia AES a 128 bit mediante Servizi multimediali di Microsoft Azure. Servizi multimediali fornisce anche il servizio di distribuzione delle chiavi che distribuisce chiavi di crittografia agli utenti autorizzati. In questo argomento viene illustrato come crittografare con AES-128 e utilizzare il servizio di distribuzione delle chiavi in modo dinamico.
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
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 8d071f64df0097b4029884a9efa84c6f2708fd44
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376602"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Usare la crittografia dinamica AES-128 e il servizio di distribuzione delle chiavi

È possibile usare Servizi multimediali per distribuire flussi HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming crittografati con AES usando chiavi di crittografia a 128 bit. Servizi multimediali fornisce anche il servizio di distribuzione delle chiavi che distribuisce chiavi di crittografia agli utenti autorizzati. Per consentire a Servizi multimediali di crittografare un asset, è necessario associare la chiave di crittografia a StreamingLocator e anche configurare i criteri di chiave simmetrica. Quando un flusso viene richiesto da un lettore, Servizi multimediali usa la chiave specificata per crittografare dinamicamente il contenuto mediante AES. Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di chiave simmetrica specificati per tale chiave.

L'articolo si basa sull'esempio [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES). L'esempio illustra come creare una trasformazione di codifica che usa un set di impostazioni integrato per la codifica bitrate adattiva e inserisce un file direttamente da un [URL di origine HTTPS](job-input-from-http-how-to.md). L'asset di output viene quindi pubblicato usando la crittografia AES (ClearKey). L'output dell'esempio è un URL di Azure Media Player che include sia il manifesto DASH che il token AES necessari per riprodurre il contenuto. L'esempio imposta la scadenza del token JWT su 1 ora. È possibile aprire un browser e incollare l'URL risultante per avviare la pagina della demo di Azure Media Player con l'URL e il token già inseriti nel formato seguente: ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```.

> [!NOTE]
> È possibile crittografare ogni asset con più tipi di crittografia (AES-128, PlayReady, Widevine, FairPlay). Per informazioni su come combinarli efficacemente, vedere [Streaming protocols and encryption types](content-protection-overview.md#streaming-protocols-and-encryption-types) (Protocolli di streaming e tipi di crittografia).

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione è necessario quanto segue.

* Vedere l'articolo [Panoramica della protezione del contenuto](content-protection-overview.md).
* Installare Visual Studio Code o Visual Studio
* Creare un nuovo account di Servizi multimediali in Azure, come descritto in [questa guida introduttiva](create-account-cli-quickstart.md).
* Ottenere le credenziali necessarie per usare le API di Servizi multimediali seguendo [Accedere alle API](access-api-cli-how-to.md)

## <a name="download-code"></a>Scaricare il codice

Clonare nel computer un repository GitHub contenente l'esempio .NET completo trattato in questo articolo usando il comando seguente:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
L'esempio "Encrypt with AES-128" (Crittografia con AES) si trova nella cartella [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES).

> [!NOTE]
> L'esempio crea risorse univoche ogni volta che si esegue l'applicazione. In genere si riutilizzano le risorse esistenti, ad esempio criteri e trasformazioni, se hanno le configurazioni richieste. 

## <a name="start-using-media-services-apis-with-net-sdk"></a>Iniziare a usare le API di Servizi multimediali con .NET SDK

Per iniziare a usare le API di Servizi multimediali con .NET, è necessario creare un oggetto **AzureMediaServicesClient**. Per eseguire questa operazione, specificare le credenziali necessarie per consentire al client di connettersi ad Azure tramite Azure AD. Nel codice clonato all'inizio dell'articolo, la funzione **GetCredentialsAsync** crea l'oggetto ServiceClientCredentials in base alle credenziali fornite nel file di configurazione locale. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Creare un asset di output  

L'[asset](https://docs.microsoft.com/rest/api/media/assets) di output archivia il risultato del processo di codifica.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Ottenere o creare una trasformazione di codifica

Quando si crea una nuova istanza dell'oggetto [Transform](https://docs.microsoft.com/rest/api/media/transforms), è necessario specificare ciò che dovrà generare come output. Il parametro obbligatorio è costituito da un oggetto **TransformOutput**, come illustrato nel codice seguente. Ogni **TransformOutput** contiene un parametro **Preset**. In **Preset** sono descritte le istruzioni dettagliate delle operazioni di elaborazione di contenuti video e/o audio che devono essere usate per generare l'oggetto **TransformOutput** desiderato. L'esempio descritto in questo articolo è basato su un set di impostazioni predefinito denominato **AdaptiveStreaming**. Il set di impostazioni codifica il video di input in una tabella di coppie velocità in bit-risoluzione generata automaticamente in base alla risoluzione e alla velocità in bit dell'input e genera file ISO MP4 con standard video H.264 e audio AAC corrispondente a ogni coppia velocità in bit-risoluzione. 

Prima di creare un nuovo oggetto [Transform](https://docs.microsoft.com/rest/api/media/transforms), è necessario verificare se ne esiste già uno tramite il metodo **Get**, come illustrato nel codice seguente.  In Servizi multimediali v3 i metodi **Get** eseguiti su entità restituiscono **null** se determinano che l'entità non esiste, effettuando un controllo del nome senza distinzione tra maiuscole e minuscole.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Inviare il processo

Come indicato sopra, l'oggetto [Transform](https://docs.microsoft.com/rest/api/media/transforms) è la serie di istruzioni, mentre l'oggetto [Job](https://docs.microsoft.com/rest/api/media/jobs) è la richiesta effettiva inviata a Servizi multimediali per applicare l'oggetto **Transform** a determinati contenuti audio o video di input. L'oggetto **Job** specifica informazioni come la posizione del video di input e quella dell'output.

In questa esercitazione si crea l'input del processo in base a un file che viene inserito direttamente da un [URL di origine HTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Attendere il completamento del processo

Il completamento del processo richiede tempo e al termine dell'elaborazione può essere opportuno ricevere una notifica. L'esempio di codice seguente illustra come eseguire il polling del servizio per determinare lo stato del [processo](https://docs.microsoft.com/rest/api/media/jobs). Il polling non è una procedura consigliata per le applicazioni di produzione poiché comporta rischi di latenza. Il polling può essere limitato se usato eccessivamente su un account. In alternativa, è preferibile che gli sviluppatori usino Griglia di eventi. Vedere [Instradare gli eventi verso un endpoint Web personalizzato](job-state-events-cli-how-to.md).

L'oggetto **Job** assume progressivamente gli stati seguenti: **Scheduled**, **Queued**, **Processing**, **Finished** (lo stato finale). Se nel corso del processo si verifica un errore, viene restituito lo stato **Error**. Se il processo è in fase di annullamento, vengono restituiti lo stato **Canceling** e, al termine, lo stato **Canceled**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-contentkeypolicy"></a>Creare un ContentKeyPolicy

Una chiave simmetrica consente l'accesso sicuro agli asset. È necessario creare un **ContentKeyPolicy** che consente di configurare la modalità in cui la chiave simmetrica viene distribuita per i client finali. La chiave simmetrica è associata a **StreamingLocator**. Servizi multimediali fornisce anche il servizio di distribuzione delle chiavi che distribuisce chiavi di crittografia agli utenti autorizzati. 

Quando un flusso viene richiesto da un lettore, Servizi multimediali usa la chiave specificata per crittografare dinamicamente il contenuto, in questo caso mediante AES. Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di chiave simmetrica specificati per tale chiave.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaminglocator"></a>Creare un StreamingLocator

Al termine della codifica e dopo l'impostazione dei criteri di chiave simmetrica, il passaggio successivo consiste nel rendere disponibile ai client il video nell'asset di output per la riproduzione. Per eseguire questa operazione sono previsti due passaggi: 

1. Creare uno [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)
2. Creare gli URL di streaming che i client possono usare. 

Il processo di creazione di uno **StreamingLocator** è detto pubblicazione. Per impostazione predefinita, l'oggetto **StreamingLocator** è valido immediatamente dopo l'esecuzione delle chiamate API e rimane tale finché non viene eliminato, a meno che non si configurino le ore di inizio e fine facoltative. 

Quando si crea un oggetto [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), è necessario specificare il parametro **StreamingPolicyName** desiderato. In questa esercitazione si usa uno dei PredefinedStreamingPolicies che indica a Servizi multimediali di Azure come pubblicare il contenuto per lo streaming. In questo esempio si applica la crittografia AES Envelope, nota anche come crittografia ClearKey perché la chiave viene distribuita al client di riproduzione tramite HTTPS e non una licenza DRM.

> [!IMPORTANT]
> Quando si usa un oggetto [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) personalizzato, è necessario progettare un set limitato di tali criteri per l'account di Servizi multimediali e riusarli per gli oggetti StreamingLocator ogni volta che si devono usare gli stessi protocolli e opzioni di crittografia. L'account di Servizi multimediali prevede una quota per il numero di occorrenze di StreamingPolicy. Evitare quindi di creare un nuovo oggetto StreamingPolicy per ogni StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Ottenere un token di test
        
In questa esercitazione si specifica una restrizione del token per i criteri di chiave simmetrica. I criteri con restrizione del token richiedono la presenza di un token rilasciato da un servizio token di sicurezza. Servizi multimediali supporta i token nei formati [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) e saranno configurati in questo modo nell'esempio.

In ContentKeyPolicy viene usato ContentKeyIdentifierClaim, il che significa che il token presentato al servizio di distribuzione delle chiavi deve contenere l'identificatore dell'entità ContentKey. Nell'esempio non si specifica una chiave simmetrica quando si crea StreamingLocator, ma il sistema crea un valore casuale. Per generare il token di test, è necessario ottenere il ContentKeyId da inserire nell'attestazione ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Creare un URL di streaming DASH

Ora che è stato creato l'oggetto [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), è possibile ottenere gli URL di streaming. Per creare un URL, è necessario concatenare il nome host [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) e il percorso **StreamingLocator**. In questo esempio, per **StreamingEndpoint** viene usato il valore *default*. Quando si crea un account di Servizi multimediali per la prima volta, l'oggetto **StreamingEndpoint** *default* è in stato arrestato ed è quindi necessario chiamare **Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Pulire le risorse nell'account di Servizi multimediali

Normalmente è necessario pulire tutti gli oggetti tranne quelli che si prevede di riutilizzare. In genere si riutilizzano gli oggetti Transform e si salvano in modo permanente oggetti come StreamingLocator. Se dopo l'attività di sperimentazione si vuole pulire il proprio account, è necessario eliminare le risorse che non si prevede di riutilizzare.  Il codice seguente, ad esempio, elimina gli oggetti Job.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="next-steps"></a>Passaggi successivi

Scoprire come [proteggere con DRM](protect-with-drm.md)
