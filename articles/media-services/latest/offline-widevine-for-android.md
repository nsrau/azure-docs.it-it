---
title: Streaming di Widevine Android offline con servizi multimediali di Azure V3
description: Questo argomento illustra come configurare l'account di Servizi multimediali di Azure per lo streaming offline di contenuto protetto da Widevine.
services: media-services
keywords: DASH, DRM, modalità offline Widevine, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2020
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: b603b800dfdfb96e9b6b1074dc1e39d31b994c06
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997779"
---
# <a name="offline-widevine-streaming-for-android-with-media-services-v3"></a>Streaming Widevine offline per Android con servizi multimediali V3

Oltre a proteggere il contenuto per lo streaming online, i servizi di sottoscrizione e noleggio di contenuti multimediali offrono la possibilità di scaricare contenuto utilizzabile anche quando non si è connessi a Internet. Può ad esempio essere necessario scaricare contenuto sullo smartphone o sul tablet per la riproduzione in modalità aereo quando si è disconnessi dalla rete durante il volo. Il download di contenuto può essere utile anche in altri scenari, come nei casi seguenti:

- Alcuni provider di contenuti possono impedire la distribuzione di licenze DRM oltre il bordo di un paese/area geografica. Se un utente vuole consultare il contenuto durante una trasferta all'estero, è necessario il download offline.
- In alcuni paesi o aree geografiche la disponibilità Internet e/o la larghezza di banda sono limitate. Gli utenti possono quindi scegliere di scaricare il contenuto per ottenere una risoluzione sufficientemente elevata a garantire un'esperienza di visualizzazione soddisfacente.

Questo articolo illustra come implementare la riproduzione in modalità offline per contenuto DASH protetto da Widevine su dispositivi Android. Con la tecnologia DRM offline è possibile fornire modelli di sottoscrizione, noleggio e acquisto per i servizi di contenuto offerti in modo da consentire ai clienti di usare il contenuto scaricato anche quando non sono connessi a Internet.

Per creare un'app lettore per Android vengono presentate tre opzioni:

> [!div class="checklist"]
> * L'API Java di ExoPlayer SDK
> * Il binding Xamarin di ExoPlayer SDK
> * Le estensioni EME (Encrypted Media Extension) ed MSE (Media Source Extension) nel browser Chrome per dispositivi mobili versione 62 o successive

L'articolo include inoltre le risposte ad alcune domande frequenti relative allo streaming offline di contenuto protetto da Widevine.

> [!NOTE]
> Il DRM offline viene addebitato solo per l'esecuzione di una singola richiesta di licenza quando si Scarica il contenuto. Eventuali errori non vengono addebitati.

## <a name="prerequisites"></a>Prerequisiti 

Prima di implementare la tecnologia DRM offline per Widevine su dispositivi Android, è necessario:

- Acquisire familiarità con i concetti relativi alla protezione di contenuto online con Widevine DRM. Questi concetti sono illustrati in dettaglio nei documenti ed esempi seguenti:
    - [Progettazione di un sistema di protezione del contenuto con DRM multiplo e controllo di accesso](design-multi-drm-system-with-access-control.md)
    - [Usare la crittografia dinamica DRM e il servizio di distribuzione delle licenze](protect-with-drm.md)
- Clonare https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git.

    È necessario modificare il codice in [Encrypt with DRM using .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) (Eseguire la crittografia con DRM usando .NET) per aggiungere configurazioni Widevine.  
- Acquisire familiarità con Google ExoPlayer SDK per Android, un SDK per lettori video open source in grado di supportare la riproduzione offline con tecnologia Widevine DRM. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [Guida per sviluppatori di ExoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [Blog per sviluppatori di ExoPlayer](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Configurare la protezione dei contenuti in Servizi multimediali di Azure

Nel metodo [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) eseguire queste operazioni obbligatorie:

1. Specificare in che modo la distribuzione della chiave simmetrica viene autorizzata nel servizio di distribuzione di licenze: 

    ```csharp
    ContentKeyPolicySymmetricTokenKey primaryKey = new ContentKeyPolicySymmetricTokenKey(tokenSigningKey);
    List<ContentKeyPolicyTokenClaim> requiredClaims = new List<ContentKeyPolicyTokenClaim>()
    {
        ContentKeyPolicyTokenClaim.ContentKeyIdentifierClaim
    };
    List<ContentKeyPolicyRestrictionTokenKey> alternateKeys = null;
    ContentKeyPolicyTokenRestriction restriction 
        = new ContentKeyPolicyTokenRestriction(Issuer, Audience, primaryKey, ContentKeyPolicyRestrictionTokenType.Jwt, alternateKeys, requiredClaims);
    ```
2. Configurare il modello di licenza Widevine:  

    ```csharp
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ```

3. Creare ContentKeyPolicyOptions:

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>Abilitare la modalità offline

Per abilitare la modalità **offline** per le licenze Widevine, è necessario configurare il [modello di licenza Widevine](widevine-license-template-overview.md). Nell'oggetto **policy_overrides** impostare la proprietà **can_persist** su **true** come illustrato in [ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563). Il valore predefinito è false. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>Configurazione del lettore Android per la riproduzione offline

Il modo più semplice per sviluppare un'app lettore nativa per dispositivi Android è quello di usare [Google ExoPlayer SDK](https://github.com/google/ExoPlayer), un SDK per lettori video open source. ExoPlayer supporta funzionalità non attualmente supportate dall'API MediaPlayer nativa di Android, inclusi i protocolli di recapito MPEG-DASH e Microsoft Smooth Streaming.

ExoPlayer versione 2.6 e successive include molte classi che supportano la riproduzione offline con Widevine DRM. In particolare, la classe OfflineLicenseHelper fornisce funzioni di utilità per facilitare l'uso di DefaultDrmSessionManager per scaricare, rinnovare e rilasciare licenze offline. Le classi fornite nella cartella "library/core/src/main/java/com/google/android/exoplayer2/offline/" dell'SDK supportano il download di contenuto video offline.

Di seguito sono elencate le classi di ExoPlayer SDK per Android che facilitano la modalità offline:

- library/core/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- library/core/src/main/java/com/google/android/exoplayer2/offline/Downloader.java
- library/dash/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

Quando creano un'applicazione, gli sviluppatori possono fare riferimento alla [guida per sviluppatori di ExoPlayer](https://google.github.io/ExoPlayer/guide.html) e al [blog](https://medium.com/google-exoplayer) corrispondente. Per il momento Google non ha rilasciato un'implementazione di riferimento o un esempio di codice completamente documentato per il supporto della modalità offline per Widevine nelle app ExoPlayer e pertanto le informazioni sono limitate alla guida e al blog per sviluppatori. 

### <a name="working-with-older-android-devices"></a>Uso con dispositivi Android meno recenti

Per alcuni dispositivi Android meno recenti, è necessario impostare i valori per le proprietà **policy_overrides** seguenti, definite nel [modello di licenza Widevine](widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds** e **license_duration_seconds**. In alternativa, è possibile impostare queste proprietà su zero, ovvero su una durata infinita o illimitata.  

I valori devono essere impostati per evitare un bug di overflow di numero intero. Per altre informazioni sul problema, vedere https://github.com/google/ExoPlayer/issues/3150 e https://github.com/google/ExoPlayer/issues/3112. <br/>Se non si impostano i valori in modo esplicito, a **PlaybackDurationRemaining** e **LicenseDurationRemaining** verranno assegnati valori molto grandi, ad esempio 9223372036854775807, ovvero il massimo valore positivo per un numero intero a 64 bit. Di conseguenza, la licenza Widevine risulterà scaduta e la decrittografia non verrà eseguita. 

Questo problema non si verifica in Android 5.0 Lollipop o versioni successive perché la 5.0 è la prima versione di Android progettata per il supporto completo di ARMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) e piattaforme a 64 bit, mentre Android 4.4 KitKat è stato originariamente progettato per il supporto di ARMv7 e piattaforme a 32 bit, come altre versioni di Android precedenti.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Uso di Xamarin per creare un'app Android per la riproduzione di contenuto

È possibile trovare i binding Xamarin per ExoPlayer usando i collegamenti seguenti:

- [Libreria di binding Xamarin per la libreria di Google ExoPlayer](https://github.com/martijn00/ExoPlayerXamarin)
- [Binding Xamarin per il pacchetto NuGet ExoPlayer](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Vedere anche il thread seguente relativo al [binding Xamarin](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>App lettore su browser Chrome per Android

A partire dalla versione di [Chrome per Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates), è supportata la licenza permanente in EME. In Chrome per Android è ora supportato anche [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1). È così possibile creare applicazioni per la riproduzione offline in Chrome che gli utenti finali possono usare se dispongono di questa o di una versione successiva del browser. 

Google ha inoltre realizzato un esempio di PWA (Progressive Web App) e lo ha reso disponibile in open source: 

- [Codice sorgente](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Versione ospitata su Google](https://biograf-155113.appspot.com/ttt/episode-2/) (funziona solo in Chrome versione 62 e successive su dispositivi Android)

Se si aggiorna il browser Chrome per dispositivi mobili alla versione 62 o successiva su uno smartphone Android e si testa l'app di esempio ospitata su Google, si noterà che è possibile usare entrambe le modalità di streaming online e riproduzione offline.

Questa app PWA open source è stata creata in Node.js. Se si vuole ospitare una versione personalizzata su un server Ubuntu, tenere presenti i seguenti problemi comunemente riscontrati che possono impedire la riproduzione:

1. Problema CORS: il video di esempio nell'app di esempio è ospitato in https://storage.googleapis.com/biograf-video-files/videos/. Google ha configurato CORS per tutti gli esempi di test ospitati nel bucket di Google Cloud Storage. Questi sono resi disponibili con intestazioni CORS, che specificano in modo esplicito la voce CORS `https://biograf-155113.appspot.com` (il dominio in cui Google ospita l'esempio), impedendo l'accesso da altri siti. Se si prova, verrà visualizzato il seguente errore HTTP: `Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`
2. Problema di certificato: a partire da Chrome versione 58, EME per Widevine richiede HTTPS. È pertanto necessario ospitare l'app di esempio su HTTPS con un certificato X509. Un normale certificato di test non è in grado di soddisfare i requisiti. È necessario ottenere un certificato che soddisfi i requisiti minimi seguenti:
    - Per Chrome e Firefox, nel certificato deve essere definita l'impostazione relativa al nome alternativo del soggetto (SAN).
    - Il certificato deve avere una CA attendibile. Non è possibile usare un certificato di sviluppo autofirmato.
    - Il certificato deve inoltre avere un nome comune (CN) corrispondente al nome DNS del server Web o del gateway.

## <a name="faqs"></a>Domande frequenti

Per altre informazioni, vedere [domande frequenti su Widevine](frequently-asked-questions.md#widevine-streaming-for-android).

## <a name="additional-notes"></a>Note aggiuntive

Widevine è un servizio fornito da Google Inc. e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google Inc.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come implementare la riproduzione in modalità offline per contenuto DASH protetto da Widevine su dispositivi Android.  Sono state inoltre fornite le risposte ad alcune domande frequenti relative allo streaming offline di contenuto protetto da Widevine.
