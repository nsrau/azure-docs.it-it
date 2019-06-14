---
title: Usare servizio recapito licenza e la crittografia dinamico DRM con servizi multimediali di Azure | Microsoft Docs
description: È possibile usare Servizi multimediali di Azure per distribuire i flussi crittografati con licenze Microsoft PlayReady, Google Widevine o Apple FairPlay.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ce3b7a29f6f57b2bc309c719dbbab6c4574f0a46
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66306483"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Esercitazione: Usare il servizio di crittografia dinamica e di distribuzione di licenze DRM

È possibile usare Servizi multimediali di Azure per distribuire i flussi crittografati con licenze Microsoft PlayReady, Google Widevine o Apple FairPlay. Per informazioni dettagliate, vedere [protezione con la crittografia dinamica del contenuto](content-protection-overview.md).

Servizi multimediali offre inoltre un servizio per la distribuzione di licenze DRM PlayReady, Widevine e FairPlay. Quando un utente richiede contenuto protetto tramite DRM, l'applicazione lettore richiede una licenza dal servizio licenze di Servizi multimediali. Se l'applicazione lettore viene autorizzata, il servizio licenze di Servizi multimediali rilascia una licenza per il lettore. Una licenza contiene la chiave di decrittografia che può essere usata dal lettore client per decrittografare e trasmettere il contenuto.

Questo articolo si basa sull'esempio [Encrypting with DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM) (Crittografia con DRM). 

L'esempio descritto in questo articolo genera il risultato seguente:

![AMS con video protetto con DRM](./media/protect-with-drm/ams_player.png)

Questa esercitazione illustra come:    

> [!div class="checklist"]
> * Creare una trasformazione di codifica
> * Impostare la chiave di firma utilizzata per la verifica del token di
> * Impostare requisiti per i criteri di chiave simmetrica
> * Creare un StreamingLocator con i criteri di streaming specificati
> * Creare un URL utilizzato per la riproduzione di file

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione è necessario quanto segue.

* Vedere l'articolo [Panoramica della protezione del contenuto](content-protection-overview.md).
* Esaminare il [progettare il sistema di protezione del contenuto con DRM multiplo con controllo degli accessi](design-multi-drm-system-with-access-control.md)
* Installare Visual Studio Code o Visual Studio
* Creare un nuovo account di Servizi multimediali in Azure, come descritto in [questa guida introduttiva](create-account-cli-quickstart.md).
* Ottenere le credenziali necessarie per usare le API di Servizi multimediali seguendo [Accedere alle API](access-api-cli-how-to.md)
* Impostare i valori appropriati nel file di configurazione dell'applicazione (appsettings.json).

## <a name="download-code"></a>Scaricare il codice

Clonare nel computer un repository GitHub contenente l'esempio .NET completo trattato in questo articolo usando il comando seguente:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
L'esempio "Encrypt with DRM" (Crittografia con DRM) si trova nella cartella [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

> [!NOTE]
> L'esempio crea risorse univoche ogni volta che si esegue l'applicazione. In genere si riutilizzano le risorse esistenti, ad esempio criteri e trasformazioni, se hanno le configurazioni richieste. 

## <a name="start-using-media-services-apis-with-net-sdk"></a>Iniziare a usare le API di Servizi multimediali con .NET SDK

Per iniziare a usare le API di Servizi multimediali con .NET, è necessario creare un oggetto **AzureMediaServicesClient**. Per eseguire questa operazione, specificare le credenziali necessarie per consentire al client di connettersi ad Azure tramite Azure AD. Nel codice clonato all'inizio dell'articolo, la funzione **GetCredentialsAsync** crea l'oggetto ServiceClientCredentials in base alle credenziali fornite nel file di configurazione locale. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Creare un asset di output  

L'[asset](assets-concept.md) di output archivia il risultato del processo di codifica.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Ottenere o creare una trasformazione di codifica

Quando si crea una nuova istanza dell'oggetto [Transform](transforms-jobs-concept.md), è necessario specificare ciò che dovrà generare come output. Il parametro obbligatorio è un `transformOutput` dell'oggetto, come illustrato nel codice seguente. Ogni TransformOutput contiene un **preimpostato**. Set di impostazioni descrive le procedure dettagliate di operazioni di elaborazione video e/o audio che devono essere utilizzati per generare il TransformOutput desiderato. L'esempio descritto in questo articolo è basato su un set di impostazioni predefinito denominato **AdaptiveStreaming**. Il set di impostazioni codifica il video di input in una tabella di coppie velocità in bit-risoluzione generata automaticamente in base alla risoluzione e alla velocità in bit dell'input e genera file ISO MP4 con standard video H.264 e audio AAC corrispondente a ogni coppia velocità in bit-risoluzione. 

Prima di creare un nuovo oggetto **Transform**, è necessario verificare se ne esiste già uno tramite il metodo **Get**, come illustrato nel codice seguente.  In Servizi multimediali v3 i metodi **Get** eseguiti su entità restituiscono **null** se determinano che l'entità non esiste, effettuando un controllo del nome senza distinzione tra maiuscole e minuscole.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Inviare il processo

Come indicato sopra, l'oggetto **Transform** è la serie di istruzioni, mentre l'oggetto [Job](transforms-jobs-concept.md) è la richiesta effettiva inviata a Servizi multimediali per applicare l'oggetto **Transform** a determinati contenuti audio o video di input. L'oggetto **Job** specifica informazioni come la posizione del video di input e quella dell'output.

In questa esercitazione si crea l'input del processo in base a un file che viene inserito direttamente da un [URL di origine HTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Attendere il completamento del processo

Il completamento del processo richiede tempo e al termine dell'elaborazione può essere opportuno ricevere una notifica. L'esempio di codice seguente illustra come eseguire il polling del servizio per determinare lo stato del **processo**. Il polling non è una procedura consigliata per le applicazioni di produzione poiché comporta rischi di latenza. Il polling può essere limitato se usato eccessivamente su un account. In alternativa, è preferibile che gli sviluppatori usino Griglia di eventi. Vedere [Instradare gli eventi verso un endpoint Web personalizzato](job-state-events-cli-how-to.md).

L'oggetto **Job** assume progressivamente gli stati seguenti: **Scheduled**, **Queued**, **Processing**, **Finished** (stato finale). Se nel corso del processo si verifica un errore, viene restituito lo stato **Error**. Se il processo è in fase di annullamento, vengono restituiti lo stato **Canceling** e, al termine, lo stato **Canceled**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Creare un criteri di chiave simmetrica

Una chiave simmetrica consente l'accesso sicuro agli asset. È necessario creare un [criteri di contenuto chiave](content-key-policy-concept.md) per crittografare il contenuto con un DRM. Consente di configurare i criteri come la chiave simmetrica venga distribuita per i client finali. La chiave simmetrica è associata a un localizzatore di Streaming. Servizi multimediali fornisce anche il servizio di distribuzione delle chiavi che distribuisce chiavi di crittografia e licenze agli utenti autorizzati. 

È necessario impostare i requisiti (restrizioni) sul **criteri di chiave simmetrica** che devono essere soddisfatte per fornire le chiavi con la configurazione specificata. In questo esempio vengono impostate le configurazioni e i requisiti seguenti:

* Configurazione 

    Le licenze [PlayReady](playready-license-template-overview.md) e [Widevine](widevine-license-template-overview.md) sono configurate in modo che possano essere distribuite dal servizio di distribuzione delle licenze di Servizi multimediali. Anche se questa app di esempio non configura la licenza [FairPlay](fairplay-license-overview.md), contiene un metodo che è possibile usare per configurare FairPlay. È possibile aggiungere la configurazione FairPlay come un'altra opzione.

* Restrizione

    L'app imposta una restrizione di tipo token JWT nei criteri.

Quando un flusso viene richiesto da un lettore, Servizi multimediali usa la chiave specificata per crittografare dinamicamente i contenuti. Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di chiave simmetrica specificati per tale chiave.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Creare un localizzatore di streaming

Al termine della codifica e dopo l'impostazione dei criteri di chiave simmetrica, il passaggio successivo consiste nel rendere disponibile ai client il video nell'asset di output per la riproduzione. Per eseguire questa operazione sono previsti due passaggi: 

1. Creare un [localizzatore di streaming](streaming-locators-concept.md)
2. Creare gli URL di streaming che i client possono usare. 

Il processo di creazione di **localizzatore di Streaming** viene chiamata la pubblicazione. Per impostazione predefinita, il **localizzatore di streaming** è valido immediatamente dopo l'esecuzione delle chiamate API e rimane tale finché non viene eliminato, a meno che non si configurino le ore di inizio e fine facoltative. 

Quando si crea una **localizzatore di Streaming**, è necessario specificare il valore desiderato `StreamingPolicyName`. In questa esercitazione viene usato uno di Streaming criteri predefiniti, che indica come pubblicare il contenuto per lo streaming di servizi multimediali di Azure. In questo esempio, StreamingLocator.StreamingPolicyName verrà impostato sul criterio "Predefined_MultiDrmCencStreaming". La crittografia PlayReady e Widevine viene applicate, la chiave venga distribuita al client di riproduzione di licenze DRM configurate in base. Se si vuole anche crittografare il flusso con CBCS (FairPlay), usare "Predefined_MultiDrmStreaming". 

> [!IMPORTANT]
> Se si usano [criteri di streaming](streaming-policy-concept.md) personalizzati, è necessario progettare un set limitato di tali criteri per l'account di Servizi multimediali e riusare questi criteri per i localizzatori di streaming ogni volta che si devono usare gli stessi protocolli e le stesse opzioni di crittografia. L'account di Servizi multimediali prevede una quota per il numero di occorrenze di StreamingPolicy. Evitare quindi di creare un nuovo oggetto StreamingPolicy per ogni StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Ottenere un token di test
        
In questa esercitazione si specifica una restrizione del token per i criteri di chiave simmetrica. I criteri con restrizione del token richiedono la presenza di un token rilasciato da un servizio token di sicurezza. Servizi multimediali supporta i token nei formati [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) e saranno configurati in questo modo nell'esempio.

In ContentKeyPolicy viene usato ContentKeyIdentifierClaim, il che significa che il token presentato al servizio di distribuzione delle chiavi deve contenere l'identificatore dell'entità ContentKey. Nell'esempio, una chiave simmetrica non viene specificato quando si crea il localizzatore di Streaming, il sistema crea un valore casuale per noi. Per generare il token di test, è necessario ottenere il ContentKeyId da inserire nell'attestazione ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>Creare un URL di streaming

Ora che è stato creato l'oggetto [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), è possibile ottenere gli URL di streaming. Per compilare un URL, è necessario concatenare la [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) nome host e il **localizzatore di Streaming** percorso. In questo esempio, viene usato l'*endpoint di streaming* **predefinito**. Quando si crea un account di Servizi multimediali per la prima volta, il **localizzatore di streaming** *predefinito* è in stato arrestato ed è quindi necessario chiamare **Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

Quando si esegue l'app, viene visualizzato quanto segue:

![Proteggere con DRM](./media/protect-with-drm/playready_encrypted_url.png)

È possibile aprire un browser e incollare l'URL risultante per avviare la pagina della demo di Azure Media Player con l'URL e il token già inseriti. 
 
## <a name="clean-up-resources-in-your-media-services-account"></a>Pulire le risorse nell'account di Servizi multimediali

Normalmente è necessario pulire tutti gli oggetti tranne quelli che si prevede di riutilizzare. In genere si riutilizzano gli oggetti Transform e si salvano in modo permanente oggetti come StreamingLocator. Se dopo l'attività di sperimentazione si vuole pulire il proprio account, è necessario eliminare le risorse che non si prevede di riutilizzare.  Il codice seguente, ad esempio, elimina gli oggetti Job.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Pulire le risorse

Se nessuna delle risorse usate è più necessaria, compresi gli account di archiviazione e di Servizi multimediali creati per questa esercitazione, eliminare il gruppo di risorse creato in precedenza. 

Eseguire il comando dell'interfaccia della riga di comando seguente:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback e ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

Scopri

> [!div class="nextstepaction"]
> [Protezione con AES-128](protect-with-aes128.md)

