---
title: Proteggere i contenuti usando la crittografia dinamica di servizi multimediali-Azure | Microsoft Docs
description: Questo articolo offre una panoramica della protezione dei contenuti con la crittografia dinamica. Vengono inoltre illustrati i protocolli di streaming e i tipi di crittografia.
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
ms.date: 07/25/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 1d95d14398bc6b5acdec89428ebe22a672551a8a
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338792"
---
# <a name="protect-your-content-by-using-media-services-dynamic-encryption"></a>Proteggere i contenuti usando la crittografia dinamica di servizi multimediali

È possibile usare servizi multimediali di Azure per proteggere i file multimediali dal momento in cui si lascia il computer attraverso archiviazione, elaborazione e recapito. Con Servizi multimediali è possibile distribuire contenuti live e on demand crittografati dinamicamente con AES-128 (Advanced Encryption Standard) o con uno dei principali sistemi DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati.  

In servizi multimediali V3, una chiave simmetrica è associata a un localizzatore di streaming (vedere [questo esempio](protect-with-aes128.md)). Se si usa il servizio di distribuzione delle chiavi di servizi multimediali, è possibile consentire a servizi multimediali di Azure di generare automaticamente la chiave simmetrica. È necessario generare la chiave simmetrica se si usa il servizio di distribuzione delle chiavi o se è necessario gestire uno scenario a disponibilità elevata in cui è necessario avere la stessa chiave simmetrica in due data center.

Quando un flusso viene richiesto da un lettore, Servizi multimediali usa la chiave specificata per crittografare dinamicamente i contenuti mediante la chiave non crittografata AES o la crittografia DRM. Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi di Servizi multimediali o il servizio di distribuzione delle chiavi specificato. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di chiave simmetrica specificati per tale chiave.

È possibile usare l'API REST o una libreria client di Servizi multimediali per configurare i criteri di autorizzazione e autenticazione per le licenze e le chiavi.

La figura seguente illustra il flusso di lavoro per la protezione del contenuto di servizi multimediali:

![Flusso di lavoro per la protezione del contenuto di servizi multimediali](./media/content-protection/content-protection.svg)
  
&#42;la crittografia *Dynamic supporta la chiave non crittografata AES-128, CBCS e CENC. Per informazioni dettagliate, vedere la [matrice di supporto](#streaming-protocols-and-encryption-types).*

Questo articolo illustra i concetti e la terminologia che consentono di comprendere la protezione del contenuto con servizi multimediali.

## <a name="main-components-of-a-content-protection-system"></a>Componenti principali di un sistema di protezione del contenuto

Per completare correttamente il sistema di protezione del contenuto, è necessario comprendere completamente l'ambito del lavoro richiesto. Le sezioni seguenti forniscono una panoramica di tre parti che è necessario implementare. 

> [!NOTE]
> Si consiglia vivamente di concentrarsi e testare completamente ogni parte delle sezioni seguenti prima di passare alla parte successiva. Per testare il sistema di protezione del contenuto, usare gli strumenti specificati nelle sezioni.

### <a name="media-services-code"></a>Codice di servizi multimediali
  
L' [esempio DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) Mostra come implementare un sistema con DRM multiplo con servizi multimediali v3 usando .NET. Viene inoltre illustrato come utilizzare il servizio di distribuzione di licenze/chiavi di servizi multimediali.   
  
È possibile crittografare ogni asset con più tipi di crittografia (AES-128, PlayReady, Widevine, FairPlay). Per capire cosa è sensato combinare, vedere [protocolli di streaming e tipi di crittografia](#streaming-protocols-and-encryption-types).

L'esempio illustra come:

1. Creare e configurare un [criterio della chiave](content-key-policy-concept.md)simmetrica.    

   È possibile creare un criterio della chiave simmetrica per configurare la modalità di distribuzione della chiave simmetrica, che fornisce l'accesso sicuro agli asset, ai client finali:  
 
   * Definire l'autorizzazione per la distribuzione delle licenze. Specificare la logica del controllo dell'autorizzazione in base alle attestazioni nel token Web JSON (JWT).
   * Configurare le licenze [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md)e/o [Fairplay](fairplay-license-overview.md) . I modelli consentono di configurare i diritti e le autorizzazioni per ogni DRM.

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```
2. Creare un [localizzatore di streaming](streaming-locators-concept.md) configurato per lo streaming dell'asset crittografato. 
  
   Il localizzatore di streaming deve essere associato a un [criterio di streaming](streaming-policy-concept.md). Nell'esempio è stato impostato `StreamingLocator.StreamingPolicyName` sul criterio "Predefined_MultiDrmCencStreaming". 
      
   Verranno applicate le crittografie PlayReady e Widevine e la chiave verrà recapitata al client di riproduzione in base alle licenze DRM configurate. Se si vuole anche crittografare il flusso con CBCS (FairPlay), usare il criterio "Predefined_MultiDrmStreaming".

   Il localizzatore di streaming è associato anche ai criteri della chiave simmetrica definiti.
3. Creare un token di test.

   Il metodo `GetTokenAsync` Mostra come creare un token di test.
4. Compilare l'URL di streaming.

   Il metodo `GetDASHStreamingUrlAsync` Mostra come compilare l'URL di streaming. In questo caso, l'URL trasmette il contenuto del TRATTIno.

### <a name="player-with-an-aes-or-drm-client"></a>Lettore con un client AES o DRM 

Un'app lettore video basata su un SDK per lettori, nativa o basata su browser, deve soddisfare i requisiti seguenti:

* Player SDK supporta i client DRM necessari.
* L'SDK per lettori supporta i protocolli di streaming necessari: Smooth, DASH e/o HLS.
* Il lettore SDK può gestire il passaggio di un token JWT in una richiesta di acquisizione della licenza.

È possibile creare un lettore usando l'[API di Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/). Usare l'[API ProtectionInfo di Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/) per specificare la tecnologia DRM da usare in varie piattaforme DRM.

Per testare il contenuto crittografato con AES o CENC (Widevine e/o PlayReady), è possibile usare [Azure Media Player](https://aka.ms/azuremediaplayer). Assicurarsi di selezionare **Opzioni avanzate** e controllare le opzioni di crittografia.

Se si vuole testare il contenuto crittografato di FairPlay, usare [questo lettore di test](https://aka.ms/amtest). Il lettore supporta Widevine, PlayReady e FairPlay DRM, insieme alla crittografia della chiave non crittografata AES-128. 

Scegliere il browser giusto per testare diversi DRM:

* Chrome, opera o Firefox per Widevine
* Microsoft Edge o Internet Explorer 11 per PlayReady
* Safari su macOS per FairPlay

### <a name="security-token-service"></a>Servizio token di sicurezza

Un servizio token di sicurezza (STS) rilascia JWT come token di accesso per l'accesso alle risorse back-end. È possibile usare il servizio di distribuzione di licenze/chiavi di servizi multimediali di Azure come risorsa back-end. Un STS deve definire gli elementi seguenti:

* Autorità di certificazione e destinatari o ambito
* Attestazioni, che dipendono dai requisiti aziendali di protezione del contenuto
* Verifica simmetrica o asimmetrica per la verifica della firma
* Supporto del rollover della chiave, se necessario

È possibile utilizzare [questo strumento STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) per testare il servizio token di servizio. Supporta tutti e tre i tipi di chiavi di verifica: simmetrico, asimmetrico o Azure Active Directory (Azure AD) con rollover della chiave. 

## <a name="streaming-protocols-and-encryption-types"></a>Protocolli di streaming e tipi di crittografia

È possibile usare Servizi multimediali per distribuire i contenuti crittografati dinamicamente con chiave non crittografata AES o con crittografia DRM mediante PlayReady, Widevine o FairPlay. Attualmente è possibile crittografare i formati HLS (HTTP Live Streaming), MPEG DASH e Smooth Streaming. Ogni protocollo supporta i seguenti metodi di crittografia.

### <a name="hls"></a>HLS

Il protocollo HLS supporta i formati di contenitore e gli schemi di crittografia seguenti.

|Formato del contenitore|Schema di crittografia|Esempio di URL|
|---|---|---|
|Tutti|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF + FairPlay (incluso HEVC/H. 265) è supportato nei dispositivi seguenti:

* iOS 11 o versione successiva 
* iPhone 8 o versione successiva
* MacOS High Sierra con CPU Intel 7 Generation

### <a name="mpeg-dash"></a>MPEG-DASH

Il protocollo MPEG-DASH supporta i formati di contenitore e gli schemi di crittografia seguenti.

|Formato del contenitore|Schema di crittografia|Esempi di URL
|---|---|---|
|Tutti|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Smooth Streaming

Il protocollo Smooth Streaming supporta i formati di contenitore e gli schemi di crittografia seguenti.

|Protocol|Formato del contenitore|Schema di crittografia|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Browser

I browser comuni supportano i client DRM seguenti:

|Browser|Crittografia|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>Controllo dell'accesso al contenuto

È possibile controllare chi ha accesso ai propri contenuti configurando i criteri di chiave simmetrica. Servizi multimediali supporta più modalità di autenticazione degli utenti che richiedono le chiavi. È necessario configurare i criteri di chiave simmetrica. Il client (lettore) deve soddisfare i criteri prima che la chiave possa essere distribuita al client stesso. I criteri di chiave simmetrica possono avere restrizioni *open* o *token*. 

Quando si vuole rilasciare la licenza a chiunque senza autorizzazione, è possibile usare un criterio di chiave simmetrica con restrizioni aperte. Ad esempio, se i ricavi sono basati su Active Directory e non basati su sottoscrizioni.  

Con i criteri della chiave simmetrica con restrizioni dei token, la chiave simmetrica viene inviata solo a un client che presenta un token JWT valido o un token SWT (Simple Web Token) nella richiesta di licenza/chiave. Questo token deve essere emesso da un servizio token di servizio. 

È possibile utilizzare Azure AD come STS o distribuire un [servizio token di servizio personalizzato](#using-a-custom-sts). Il servizio token di sicurezza deve essere configurato in modo da creare un token firmato con la chiave specificata e rilasciare le attestazioni specificate nella configurazione della restrizione token. Il servizio di distribuzione di licenze/chiavi di servizi multimediali restituisce la licenza o la chiave richiesta al client se sono presenti entrambe le condizioni seguenti:

* Il token è valido. 
* Le attestazioni nel token corrispondono a quelle configurate per la licenza o la chiave.

Quando si configurano i criteri di restrizione del token, è necessario specificare i parametri primary verification key, issuer e audience. Il parametro primary verification key include la chiave usata per firmare il token. Il parametro issuer è il servizio token di sicurezza che rilascia il token. Il gruppo di destinatari, talvolta denominato ambito, descrive lo scopo del token o la risorsa a cui il token autorizza l'accesso. Il servizio di distribuzione di licenze/chiavi di servizi multimediali verifica che i valori nel token corrispondano ai valori nel modello.

### <a name="token-replay-prevention"></a>Prevenzione della riproduzione del token

La funzionalità di *prevenzione della riproduzione dei token* consente ai clienti di servizi multimediali di impostare un limite per il numero di volte in cui è possibile usare lo stesso token per richiedere una chiave o una licenza. Il cliente può aggiungere un'attestazione di `urn:microsoft:azure:mediaservices:maxuses` tipo nel token, dove il valore è il numero di volte in cui il token può essere usato per acquisire una licenza o una chiave. Tutte le richieste successive con lo stesso token al recapito della chiave restituiranno una risposta non autorizzata. Vedere come aggiungere l'attestazione nell' [esempio di DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Considerazioni

* I clienti devono avere il controllo sulla generazione dei token. L'attestazione deve essere inserita nel token stesso.
* Quando si usa questa funzionalità, le richieste con token la cui ora di scadenza è più di un'ora a partire dal momento in cui la richiesta viene ricevuta vengono rifiutate con una risposta non autorizzata.
* I token sono identificati in modo univoco dalla firma. Qualsiasi modifica apportata al payload (ad esempio, l'aggiornamento all'ora di scadenza o l'attestazione) modifica la firma del token e viene conteggiata come un nuovo token non rilevato prima dal recapito della chiave.
* La riproduzione ha esito negativo se il token `maxuses` ha superato il valore impostato dal cliente.
* Questa funzionalità può essere usata per tutto il contenuto protetto esistente (è necessario modificare solo il token emesso).
* Questa funzionalità funziona sia con JWT che con SWT.

## <a name="using-a-custom-sts"></a>Uso di un servizio token di servizio personalizzato

Un cliente può scegliere di usare un STS personalizzato per fornire i token. I motivi includono:

* Il provider di identità (IDP) utilizzato dal cliente non supporta STS. In questo caso, può essere opportuno usare un servizio token di sicurezza personalizzato.
* Il cliente può avere bisogno di un controllo più flessibile o più rigido nell'integrazione del servizio token di sicurezza con il sistema di fatturazione sottoscrittore del cliente. 

   Ad esempio, un operatore di servizio [ott](https://en.wikipedia.org/wiki/Over-the-top_media_services) può offrire più pacchetti di sottoscrittori, ad esempio Premium, Basic e Sports. L'operatore può associare le attestazioni in un token al pacchetto di un sottoscrittore in modo che siano disponibili solo i contenuti del pacchetto corretto. In questo caso, un servizio token di sicurezza personalizzato fornisce la flessibilità e il controllo necessari.
* Per includere le attestazioni personalizzate nel token per scegliere tra ContentKeyPolicyOptions diversi con diversi parametri di licenza DRM (una licenza di sottoscrizione e una licenza di noleggio).
* Per includere un'attestazione che rappresenta l'identificatore della chiave simmetrica della chiave a cui il token concede l'accesso.

Quando si usa un servizio token di sicurezza personalizzato, è necessario apportare due modifiche:

* Quando si configura un servizio di distribuzione delle licenze per un asset, è necessario specificare la chiave di sicurezza usata per la verifica dal servizio token di sicurezza personalizzato invece della chiave corrente di Azure AD.
* Quando viene generato un token JTW, viene specificata una chiave di sicurezza invece della chiave privata del certificato X509 corrente in Azure AD.

Esistono due tipi di chiavi di sicurezza:

* Chiave simmetrica: la stessa chiave viene usata per generare e verificare un token JWT.
* Chiave asimmetrica: una coppia di chiavi pubblica-privata in un certificato X509 viene usata con una chiave privata per la crittografia/generazione di un token JWT e con la chiave pubblica per la verifica del token.

Se si usa .NET Framework/C# come piattaforma di sviluppo, il certificato X509 usato per la chiave di sicurezza asimmetrica deve avere una lunghezza della chiave pari almeno a 2048 bit. È un requisito della classe System.IdentityModel.Tokens.X509AsymmetricSecurityKey in .NET Framework. In caso contrario, viene generata un'eccezione simile alla seguente: IDX10630: 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' per la firma non può essere inferiore a 2048 bit.

## <a name="custom-key-and-license-acquisition-url"></a>URL di acquisizione di licenze e chiavi personalizzate

Usare i modelli seguenti se si vuole specificare un servizio di distribuzione di licenze/chiavi diverso (non servizi multimediali). I due campi sostituibili nei modelli sono disponibili per poter condividere i criteri di streaming tra più asset anziché creare criteri di flusso per ogni asset. 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`: Modello per l'URL del servizio personalizzato che fornisce le chiavi ai giocatori degli utenti finali. Non è obbligatorio quando si usa servizi multimediali di Azure per l'emissione di chiavi. 

   Il modello supporta i token sostituibili che il servizio aggiornerà in fase di esecuzione con il valore specifico della richiesta.  I valori dei token attualmente supportati sono:
   * `{AlternativeMediaId}`, che viene sostituito con il valore di StreamingLocatorId. AlternativeMediaId.
   * `{ContentKeyId}`, che viene sostituito con il valore dell'identificatore della chiave richiesta.
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`: Modello per l'URL del servizio personalizzato che fornisce le licenze ai giocatori degli utenti finali. Non è obbligatorio quando si usa servizi multimediali di Azure per emettere licenze. 

   Il modello supporta i token sostituibili che il servizio aggiornerà in fase di esecuzione con il valore specifico della richiesta. I valori dei token attualmente supportati sono:  
   * `{AlternativeMediaId}`, che viene sostituito con il valore di StreamingLocatorId. AlternativeMediaId.
   * `{ContentKeyId}`, che viene sostituito con il valore dell'identificatore della chiave richiesta. 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`: Uguale al modello precedente, solo per Widevine. 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`: Uguale al modello precedente, solo per FairPlay.  

Esempio:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId` ha un valore della chiave richiesta. È possibile utilizzare `AlternativeMediaId` se si desidera eseguire il mapping della richiesta a un'entità da parte dell'utente. Ad esempio, è possibile usare `AlternativeMediaId` per cercare le autorizzazioni.

 Per esempi REST che usano URL di acquisizione di licenze/chiavi personalizzati, vedere [criteri di streaming-crea](https://docs.microsoft.com/rest/api/media/streamingpolicies/create).

## <a name="troubleshoot"></a>Risolvere problemi

Se si ottiene l'errore `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY`, assicurarsi di specificare i criteri di flusso appropriati.

Se vengono visualizzati errori che terminano `_NOT_SPECIFIED_IN_URL`con, assicurarsi di specificare il formato di crittografia nell'URL. Un esempio è `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Vedere [protocolli di streaming e tipi di crittografia](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere i contenuti con la crittografia AES](protect-with-aes128.md)
* [Proteggere con DRM](protect-with-drm.md)
* [Progettare un sistema di protezione dei contenuti con DRM multiplo con controllo di accesso](design-multi-drm-system-with-access-control.md)
* [Crittografia lato archiviazione](storage-account-concept.md#storage-side-encryption)
* [Domande frequenti](frequently-asked-questions.md)
* [Gestore del token Web JSON](https://docs.microsoft.com/dotnet/framework/security/json-web-token-handler)
