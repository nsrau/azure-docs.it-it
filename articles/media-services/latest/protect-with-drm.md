---
title: Usare il servizio di crittografia dinamica e di distribuzione di licenze DRM con Servizi multimediali di Azure | Microsoft Docs
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
ms.topic: get-started-article
ms.date: 02/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: f1fa78c9345d5bbe0120ad203c738c441b2e1917
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2019
ms.locfileid: "55991549"
---
# <a name="use-drm-dynamic-encryption-and-license-delivery-service"></a>Usare il servizio di crittografia dinamica e di distribuzione di licenze DRM

È possibile usare Servizi multimediali per distribuire flussi MPEG-DASH, Smooth Streaming e HTTP Live Streaming (HLS) protetti con [PlayReady DRM (Digital Rights Management)](https://www.microsoft.com/playready/overview/). È anche possibile usare Servizi multimediali per distribuire flussi DASH crittografati con licenze DRM di **Google Widevine**. Sia per PlayReady che per Widevine la crittografia avviene in base alla specifica di crittografia comune (ISO/IEC 23001-7 CENC). Servizi multimediali consente anche di crittografare il contenuto HLS con **Apple FairPlay** (AES-128 CBC). 

Servizi multimediali offre inoltre un servizio per la distribuzione di licenze DRM PlayReady, Widevine e FairPlay. Quando un utente richiede contenuto protetto tramite DRM, l'applicazione lettore richiede una licenza dal servizio licenze di Servizi multimediali. Se l'applicazione lettore viene autorizzata, il servizio licenze di Servizi multimediali rilascia una licenza per il lettore. Una licenza contiene la chiave di decrittografia che può essere usata dal lettore client per decrittografare e trasmettere il contenuto.

Questo articolo si basa sull'esempio [Encrypting with DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM) (Crittografia con DRM). Tra le altre cose, l'esempio illustra come:

* Creare una trasformazione di codifica che usa un set di impostazioni predefinito per la codifica a bitrate adattiva e inserisce un file direttamente da un [URL di origine HTTPS](job-input-from-http-how-to.md).
* Impostare la chiave di firma usata per la verifica del token.
* Impostare i requisiti (restrizioni) per i criteri di chiave del contenuto che devono essere soddisfatti per la distribuzione delle chiavi con la configurazione specificata. 

    * Configurazione 
    
        In questo esempio, le licenze [PlayReady](playready-license-template-overview.md) e [Widevine](widevine-license-template-overview.md) vengono configurate in modo che possano essere distribuite dal servizio di distribuzione delle licenze di Servizi multimediali. Anche se questa app di esempio non configura la licenza [FairPlay](fairplay-license-overview.md), contiene un metodo che è possibile usare per configurare FairPlay. Se si desidera, è possibile aggiungere la configurazione FairPlay come un'altra opzione.

    * Restrizione

        L'app imposta una restrizione di tipo token JWT nei criteri.

* Creare uno StreamingLocator per l'asset specificato con il nome dei criteri di flusso specificati. In questo caso vengono usati i criteri predefiniti. Imposta due chiavi del contenuto nello StreamingLocator: AES-128 (envelope) e CENC (PlayReady e Widevine).  
    
    Dopo aver creato lo StreamingLocator, l'asset di output viene pubblicato e diventa disponibile per i client per la riproduzione.

    > [!NOTE]
    > Assicurarsi che lo StreamingEndpoint da cui si vuole trasmettere il contenuto sia in esecuzione.

* Creare un URL, per Azure Media Player, che include sia il manifesto DASH sia il token di PlayReady necessari per riprodurre i contenuti crittografati con PlayReady. L'esempio imposta la scadenza del token su 1 ora. 

    È possibile aprire un browser e incollare l'URL risultante per avviare la pagina della demo di Azure Media Player con l'URL e il token già inseriti.  

    ![Proteggere con DRM](./media/protect-with-drm/playready_encrypted_url.png)

> [!NOTE]
> È possibile crittografare ogni asset con più tipi di crittografia (AES-128, PlayReady, Widevine, FairPlay). Per informazioni su come combinarli efficacemente, vedere [Streaming protocols and encryption types](content-protection-overview.md#streaming-protocols-and-encryption-types) (Protocolli di streaming e tipi di crittografia).

L'esempio descritto in questo articolo genera il risultato seguente:

![AMS con video protetto con DRM](./media/protect-with-drm/ams_player.png)

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione è necessario quanto segue.

* Vedere l'articolo [Panoramica della protezione del contenuto](content-protection-overview.md).
* Fare riferimento a [Progettazione di un sistema di protezione del contenuto con il controllo di accesso](design-multi-drm-system-with-access-control.md)
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

L'[asset](https://docs.microsoft.com/rest/api/media/assets) di output archivia il risultato del processo di codifica.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Ottenere o creare una trasformazione di codifica

Quando si crea una nuova istanza dell'oggetto [Transform](https://docs.microsoft.com/rest/api/media/transforms), è necessario specificare ciò che dovrà generare come output. Il parametro obbligatorio è costituito da un oggetto **TransformOutput**, come illustrato nel codice seguente. Ogni **TransformOutput** contiene un parametro **Preset**. In **Preset** sono descritte le istruzioni dettagliate delle operazioni di elaborazione di contenuti video e/o audio che devono essere usate per generare l'oggetto **TransformOutput** desiderato. L'esempio descritto in questo articolo è basato su un set di impostazioni predefinito denominato **AdaptiveStreaming**. Il set di impostazioni codifica il video di input in una tabella di coppie velocità in bit-risoluzione generata automaticamente in base alla risoluzione e alla velocità in bit dell'input e genera file ISO MP4 con standard video H.264 e audio AAC corrispondente a ogni coppia velocità in bit-risoluzione. 

Prima di creare un nuovo oggetto [Transform](https://docs.microsoft.com/rest/api/media/transforms), è necessario verificare se ne esiste già uno tramite il metodo **Get**, come illustrato nel codice seguente.  In Servizi multimediali v3 i metodi **Get** eseguiti su entità restituiscono **null** se determinano che l'entità non esiste, effettuando un controllo del nome senza distinzione tra maiuscole e minuscole.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Inviare il processo

Come indicato sopra, l'oggetto [Transform](https://docs.microsoft.com/rest/api/media/transforms) è la serie di istruzioni, mentre l'oggetto [Job](https://docs.microsoft.com/rest/api/media/jobs) è la richiesta effettiva inviata a Servizi multimediali per applicare l'oggetto **Transform** a determinati contenuti audio o video di input. L'oggetto **Job** specifica informazioni come la posizione del video di input e quella dell'output.

In questa esercitazione si crea l'input del processo in base a un file che viene inserito direttamente da un [URL di origine HTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Attendere il completamento del processo

Il completamento del processo richiede tempo e al termine dell'elaborazione può essere opportuno ricevere una notifica. L'esempio di codice seguente illustra come eseguire il polling del servizio per determinare lo stato del [processo](https://docs.microsoft.com/rest/api/media/jobs). Il polling non è una procedura consigliata per le applicazioni di produzione poiché comporta rischi di latenza. Il polling può essere limitato se usato eccessivamente su un account. In alternativa, è preferibile che gli sviluppatori usino Griglia di eventi. Vedere [Instradare gli eventi verso un endpoint Web personalizzato](job-state-events-cli-how-to.md).

L'oggetto **Job** assume progressivamente gli stati seguenti: **Scheduled**, **Queued**, **Processing**, **Finished** (stato finale). Se nel corso del processo si verifica un errore, viene restituito lo stato **Error**. Se il processo è in fase di annullamento, vengono restituiti lo stato **Canceling** e, al termine, lo stato **Canceled**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-contentkeypolicy"></a>Creare un ContentKeyPolicy

Una chiave simmetrica consente l'accesso sicuro agli asset. È necessario creare un criterio di chiave simmetrica che consente di configurare la modalità in cui la chiave simmetrica viene distribuita per i client finali. La chiave simmetrica è associata a StreamingLocator. Servizi multimediali fornisce anche il servizio di distribuzione delle chiavi che distribuisce chiavi di crittografia e licenze agli utenti autorizzati. 

È necessario impostare i requisiti (restrizioni) nei criteri delle chiavi simmetriche che devono essere soddisfatti per la distribuzione delle chiavi con la configurazione specificata. In questo esempio vengono impostate le configurazioni e i requisiti seguenti:

* Configurazione 

    Le licenze [PlayReady](playready-license-template-overview.md) e [Widevine](widevine-license-template-overview.md) sono configurate in modo che possano essere distribuite dal servizio di distribuzione delle licenze di Servizi multimediali. Anche se questa app di esempio non configura la licenza [FairPlay](fairplay-license-overview.md), contiene un metodo che è possibile usare per configurare FairPlay. È possibile aggiungere la configurazione FairPlay come un'altra opzione.

* Restrizione

    L'app imposta una restrizione di tipo token JWT nei criteri.

Quando un flusso viene richiesto da un lettore, Servizi multimediali usa la chiave specificata per crittografare dinamicamente i contenuti. Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di chiave simmetrica specificati per tale chiave.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaminglocator"></a>Creare un StreamingLocator

Al termine della codifica e dopo l'impostazione dei criteri di chiave simmetrica, il passaggio successivo consiste nel rendere disponibile ai client il video nell'asset di output per la riproduzione. Per eseguire questa operazione sono previsti due passaggi: 

1. Creare uno [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)
2. Creare gli URL di streaming che i client possono usare. 

Il processo di creazione di uno **StreamingLocator** è detto pubblicazione. Per impostazione predefinita, l'oggetto **StreamingLocator** è valido immediatamente dopo l'esecuzione delle chiamate API e rimane tale finché non viene eliminato, a meno che non si configurino le ore di inizio e fine facoltative. 

Quando si crea uno [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), è necessario specificare il parametro **StreamingPolicyName** desiderato. In questa esercitazione si userà uno degli StreamingPolicies predefiniti per indicare a Servizi multimediali di Azure come pubblicare il contenuto per lo streaming. In questo esempio, StreamingLocator.StreamingPolicyName verrà impostato sul criterio "Predefined_MultiDrmCencStreaming". Questo criterio indica che si vuole che vengano generate e impostate due chiavi simmetriche (busta e CENC) per il localizzatore. Viene quindi applicata la crittografia per la busta, PlayReady e Widevine (la chiave viene distribuita al client di riproduzione in base alle licenze DRM configurate). Se si vuole anche crittografare il flusso con CBCS (FairPlay), usare "Predefined_MultiDrmStreaming". 

> [!IMPORTANT]
> Quando si usa un oggetto [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) personalizzato, è necessario progettare un set limitato di tali criteri per l'account di Servizi multimediali e riusarli per gli oggetti StreamingLocator ogni volta che si devono usare gli stessi protocolli e opzioni di crittografia. L'account di Servizi multimediali prevede una quota per il numero di occorrenze di StreamingPolicy. Evitare quindi di creare un nuovo oggetto StreamingPolicy per ogni StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Ottenere un token di test
        
In questa esercitazione si specifica una restrizione del token per i criteri di chiave simmetrica. I criteri con restrizione del token richiedono la presenza di un token rilasciato da un servizio token di sicurezza. Servizi multimediali supporta i token nei formati [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) e saranno configurati in questo modo nell'esempio.

In ContentKeyPolicy viene usato ContentKeyIdentifierClaim, il che significa che il token presentato al servizio di distribuzione delle chiavi deve contenere l'identificatore dell'entità ContentKey. Nell'esempio non si specifica una chiave simmetrica quando si crea StreamingLocator, ma il sistema crea un valore casuale. Per generare il token di test, è necessario ottenere il ContentKeyId da inserire nell'attestazione ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Creare un URL di streaming DASH

Ora che è stato creato l'oggetto [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), è possibile ottenere gli URL di streaming. Per creare un URL, è necessario concatenare il nome host [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) e il percorso **StreamingLocator**. In questo esempio, per **StreamingEndpoint** viene usato il valore *default*. Quando si crea un account di Servizi multimediali per la prima volta, l'oggetto **StreamingEndpoint** *default* è in stato arrestato ed è quindi necessario chiamare **Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Pulire le risorse nell'account di Servizi multimediali

Normalmente è necessario pulire tutti gli oggetti tranne quelli che si prevede di riutilizzare. In genere si riutilizzano gli oggetti Transform e si salvano in modo permanente oggetti come StreamingLocator. Se dopo l'attività di sperimentazione si vuole pulire il proprio account, è necessario eliminare le risorse che non si prevede di riutilizzare.  Il codice seguente, ad esempio, elimina gli oggetti Job.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="next-steps"></a>Passaggi successivi

Scoprire come applicare la [protezione con AES-128](protect-with-aes128.md)
