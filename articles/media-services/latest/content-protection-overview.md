---
title: Proteggi i tuoi contenuti con la crittografia dinamica di Servizi multimediali v3
titleSuffix: Azure Media Services
description: Informazioni sulla protezione dei contenuti con crittografia dinamica, protocolli di streaming e tipi di crittografia in Servizi multimediali di Azure.Learn about content protection with dynamic encryption, streaming protocols, and encryption types in Azure Media Services.
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
ms.date: 03/17/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: c1c9440f7ec70cea98f270f04c3030c800dd0fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461113"
---
# <a name="protect-your-content-with-media-services-dynamic-encryption"></a>Proteggi i tuoi contenuti con la crittografia dinamica di Servizi multimediali

Usare Servizi multimediali di Azure per proteggere i contenuti multimediali dal momento in cui lascia il computer fino all'archiviazione, all'elaborazione e al recapito. Con Servizi multimediali è possibile distribuire contenuti live e on demand crittografati dinamicamente con AES-128 (Advanced Encryption Standard) o con uno dei principali sistemi DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati. Se il contenuto è crittografato con una chiave non crittografata AES e viene inviato tramite HTTPS, non è chiaro fino a quando non raggiunge il client. 

In Servizi multimediali v3, una chiave simmetrica è associata a Streaming Locator (vedere [questo esempio).](protect-with-aes128.md) Se si usa il servizio di distribuzione delle chiavi di Servizi multimediali, è possibile consentire a Servizi multimediali di Azure di generare automaticamente la chiave simmetrica. La chiave simmetrica deve essere generata manualmente se si usa il proprio servizio di distribuzione delle chiavi o se è necessario gestire uno scenario di disponibilità elevata in cui è necessario disporre della stessa chiave simmetrica in due data center.

Quando un flusso viene richiesto da un lettore, Servizi multimediali usa la chiave specificata per crittografare dinamicamente i contenuti mediante la chiave non crittografata AES o la crittografia DRM. Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi di Servizi multimediali o il servizio di distribuzione delle chiavi specificato. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri della chiave simmetrica specificati per la chiave.

È possibile usare l'API REST o una libreria client di Servizi multimediali per configurare i criteri di autorizzazione e autenticazione per le licenze e le chiavi.

L'immagine seguente illustra il flusso di lavoro per la protezione del contenuto di Servizi multimediali:The following image illustrates the workflow for Media Services content protection:

![Flusso di lavoro per la protezione del contenuto di Servizi multimedialiWorkflow for Media Services content protection](./media/content-protection/content-protection.svg)
  
&#42; *la crittografia dinamica supporta la chiave non crittografata AES-128, CBCS e CENC. Per informazioni dettagliate, vedere la [matrice](#streaming-protocols-and-encryption-types)* di supporto .

In questo articolo vengono illustrati concetti e terminologia che consentono di comprendere la protezione dei contenuti con Servizi multimediali.

## <a name="main-components-of-a-content-protection-system"></a>Componenti principali di un sistema di protezione dei contenuti

Per completare correttamente il sistema di protezione dei contenuti, è necessario comprendere appieno l'ambito dello sforzo. Nelle sezioni seguenti viene fornita una panoramica di tre parti che è necessario implementare.

> [!NOTE]
> Si consiglia vivamente di mettere a fuoco e testare completamente ogni parte nelle sezioni seguenti prima di passare alla parte successiva. Per testare il sistema di protezione dei contenuti, utilizzare gli strumenti specificati nelle sezioni.

### <a name="media-services-code"></a>Codice di Servizi multimediali
  
[Nell'esempio DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) viene illustrato come implementare un sistema multi-DRM con Servizi multimediali v3 tramite .NET. Viene inoltre illustrato come utilizzare il servizio di licenza/consegna delle chiavi di Servizi multimediali.
  
È possibile crittografare ogni asset con più tipi di crittografia (AES-128, PlayReady, Widevine, FairPlay). Per vedere cosa è opportuno combinare, vedere [Protocolli di streaming e tipi di crittografia](#streaming-protocols-and-encryption-types).

L'esempio illustra come:

1. Creare e configurare un [criterio chiave simmetrica.](content-key-policy-concept.md)

   Si crea un criterio della chiave simmetrica per configurare il modo in cui la chiave simmetrica (che fornisce l'accesso sicuro alle risorse) viene recapitata ai client finali:  

   * Definire l'autorizzazione per il recapito delle licenze. Specificare la logica del controllo di autorizzazione in base alle attestazioni in JSON Web Token (JWT).
   * Configurare le licenze [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md)e/o [FairPlay.](fairplay-license-overview.md) I modelli consentono di configurare diritti e autorizzazioni per ogni RDM.

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```

2. Creare un [localizzatore](streaming-locators-concept.md) di streaming configurato per lo streaming dell'asset crittografato.
  
   Il localizzatore di streaming deve essere associato a un criterio di [streaming.](streaming-policy-concept.md) Nell'esempio, è `StreamingLocator.StreamingPolicyName` stato impostato il criterio "Predefined_MultiDrmCencStreaming".

   Vengono applicate le crittografie PlayReady e Widevine e la chiave viene recapitata al client di riproduzione in base alle licenze DRM configurate. Se vuoi anche crittografare il tuo flusso con CBCS (FairPlay), usa il criterio "Predefined_MultiDrmStreaming".

   Il localizzatore di streaming è inoltre associato ai criteri della chiave simmetrica definiti.

3. Creare un token di test.

   Il `GetTokenAsync` metodo mostra come creare un token di test.
4. Compilare l'URL di streaming.

   Il `GetDASHStreamingUrlAsync` metodo mostra come compilare l'URL di streaming. In questo caso, l'URL esegue lo streaming del contenuto DASH.

### <a name="player-with-an-aes-or-drm-client"></a>Lettore con un client AES o DRM

Un'app lettore video basata su un SDK per lettori, nativa o basata su browser, deve soddisfare i requisiti seguenti:

* L'SDK del lettore supporta i client DRM necessari.
* L'SDK del lettore supporta i protocolli di streaming richiesti: Smooth, DASH e/o HTTP Live Streaming (HLS).
* L'SDK del lettore può gestire il passaggio di un token JWT in una richiesta di acquisizione della licenza.

È possibile creare un lettore usando l'[API di Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/). Usare l'[API ProtectionInfo di Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/) per specificare la tecnologia DRM da usare in varie piattaforme DRM.

Per testare il contenuto crittografato con AES o CENC (Widevine e/o PlayReady), è possibile usare [Azure Media Player](https://aka.ms/azuremediaplayer). Assicurarsi di selezionare **Opzioni avanzate** e controllare le opzioni di crittografia.

Se si vuole testare il contenuto crittografato di FairPlay, usare [questo lettore di test](https://aka.ms/amtest). Il giocatore supporta i DRM Widevine, PlayReady e FairPlay, insieme alla crittografia a chiave pulita AES-128.

Scegli il browser giusto per testare diverse DRM:

* Chrome, Opera o Firefox per Widevine.
* Microsoft Edge o Internet Explorer 11 per PlayReady.
* Safari su macOS per FairPlay.

### <a name="security-token-service"></a>Servizio token di sicurezza

Un servizio token di sicurezza (STS) rilascia JWT come token di accesso per l'accesso alle risorse back-end. È possibile usare la licenza di Servizi multimediali di Azure come risorsa back-end. Un servizio token di sicurezza deve definire le operazioni seguenti:An STS has to define the following things:

* Emittente e pubblico (o ambito).
* Attestazioni, che dipendono dai requisiti aziendali nella protezione dei contenuti.
* Verifica simmetrica o asimmetrica per la verifica della firma.
* Supporto per il rollover delle chiavi (se necessario).

È possibile utilizzare [questo strumento servizio token di sicurezza](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) per testare il servizio token di sicurezza. Supporta tutti e tre i tipi di chiavi di verifica: simmetriche, asimmetriche o Azure Active Directory (Azure AD) con rollover delle chiavi.

## <a name="streaming-protocols-and-encryption-types"></a>Protocolli di streaming e tipi di crittografia

È possibile usare Servizi multimediali per distribuire i contenuti crittografati dinamicamente con chiave non crittografata AES o con crittografia DRM mediante PlayReady, Widevine o FairPlay. Attualmente, è possibile crittografare i formati HLS, MPEG DASH e Smooth Streaming. Ogni protocollo supporta i seguenti metodi di crittografia.

### <a name="hls"></a>HLS

Il protocollo HLS supporta i seguenti formati di contenitore e schemi di crittografia:

|Formato del contenitore|Schema di crittografia|Esempio di URL|
|---|---|---|
|Tutti|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF - FairPlay (incluso HEVC/H.265) è supportato sui seguenti dispositivi:

* iOS 11 o versioni successive.
* iPhone 8 o versioni successive.
* MacOS High Sierra con CPU Intel di settima generazione.

### <a name="mpeg-dash"></a>MPEG-DASH

Il protocollo MPEG-DASH supporta i seguenti formati di contenitore e schemi di crittografia:

|Formato del contenitore|Schema di crittografia|Esempi di URL
|---|---|---|
|Tutti|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Smooth Streaming

Il protocollo Smooth Streaming supporta i seguenti formati di contenitore e schemi di crittografia.

|Protocollo|Formato del contenitore|Schema di crittografia|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Browser

I browser comuni supportano i seguenti client DRM:

|Browser|Crittografia|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>Controllo dell'accesso ai contenuti

È possibile controllare chi ha accesso ai propri contenuti configurando i criteri di chiave simmetrica. Servizi multimediali supporta più modalità di autenticazione degli utenti che richiedono le chiavi. Il client (lettore) deve soddisfare i criteri prima che la chiave possa essere distribuita al client stesso. I criteri di chiave simmetrica possono avere restrizioni *open* o *token*.

Un criterio chiave simmetrica con restrizioni aperte può essere utilizzato quando si desidera rilasciare la licenza a chiunque senza autorizzazione. Ad esempio, se le entrate sono basate su annunci e non su abbonamento.  

Con un criterio chiave simmetrica con restrizioni di token, la chiave simmetrica viene inviata solo a un client che presenta un token JWT valido o un token Web semplice (SWT) nella richiesta di licenza/chiave. Questo token deve essere emesso da un servizio token di sicurezza.

È possibile usare Azure AD come servizio token di sicurezza o distribuire un servizio token di [sicurezza personalizzato.](#using-a-custom-sts) Il servizio token di sicurezza deve essere configurato in modo da creare un token firmato con la chiave specificata e rilasciare le attestazioni specificate nella configurazione della restrizione token. Il servizio di consegna licenze/chiavi di Servizi multimediali restituisce la licenza o la chiave richiesta al client se si verificano entrambe le condizioni:

* Il token è valido.
* Le attestazioni nel token corrispondono a quelle configurate per la licenza o la chiave.

Quando si configurano i criteri di restrizione del token, è necessario specificare i parametri primary verification key, issuer e audience. Il parametro primary verification key include la chiave usata per firmare il token. Il parametro issuer è il servizio token di sicurezza che rilascia il token. Il gruppo di destinatari, talvolta denominato ambito, descrive lo scopo del token o della risorsa a cui il token autorizza l'accesso. La licenza di Servizi multimediali/servizio di distribuzione delle chiavi verifica che questi valori nel token corrispondano ai valori nel modello.

### <a name="token-replay-prevention"></a>Prevenzione della riproduzione dei token

La funzionalità *Di prevenzione* della riproduzione di token consente ai clienti di Servizi multimediali di impostare un limite sul numero di volte in cui lo stesso token può essere utilizzato per richiedere una chiave o una licenza. Il cliente può aggiungere `urn:microsoft:azure:mediaservices:maxuses` un'attestazione di tipo nel token, dove il valore è il numero di volte in cui il token può essere utilizzato per acquisire una licenza o una chiave. Tutte le richieste successive con lo stesso token a Key Delivery restituiranno una risposta non autorizzata. Vedere come aggiungere l'attestazione [nell'esempio DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Considerazioni

* I clienti devono avere il controllo sulla generazione di token. L'attestazione deve essere inserita nel token stesso.
* Quando si utilizza questa funzionalità, le richieste con token il cui tempo di scadenza è a più di un'ora dal momento in cui la richiesta viene ricevuta vengono rifiutate con una risposta non autorizzata.
* I token sono identificati in modo univoco dalla loro firma. Qualsiasi modifica al payload (ad esempio, l'aggiornamento al tempo di scadenza o all'attestazione) modifica la firma del token e verrà conteggiata come un nuovo token che la consegna delle chiavi non ha mai incontrato prima.
* La riproduzione non riesce `maxuses` se il token ha superato il valore impostato dal cliente.
* Questa funzionalità può essere utilizzata per tutto il contenuto protetto esistente (solo il token rilasciato deve essere modificato).
* Questa funzione funziona sia con JWT che con SWT.

## <a name="using-a-custom-sts"></a>Utilizzo di un servizio token di sicurezza personalizzatoUsing a custom STS

Un cliente può scegliere di usare un servizio token di protezione personalizzato per fornire token. I motivi includono:

* Il provider di identità (IDP) utilizzato dal cliente non supporta il servizio token di sicurezza. In questo caso, può essere opportuno usare un servizio token di sicurezza personalizzato.
* Il cliente può avere bisogno di un controllo più flessibile o più rigido nell'integrazione del servizio token di sicurezza con il sistema di fatturazione sottoscrittore del cliente.

   Ad esempio, un operatore di servizio [OTT](https://en.wikipedia.org/wiki/Over-the-top_media_services) potrebbe offrire più pacchetti per abbonati, ad esempio premium, basic e sport. L'operatore può associare le attestazioni in un token al pacchetto di un sottoscrittore in modo che siano disponibili solo i contenuti del pacchetto corretto. In questo caso, un servizio token di sicurezza personalizzato fornisce la flessibilità e il controllo necessari.

* Per includere attestazioni personalizzate nel token per la selezione tra Diversi ContentKeyPolicyOptions con parametri di licenza DRM diversi (una licenza di sottoscrizione rispetto a una licenza di noleggio).
* Includere un'attestazione che rappresenta l'identificatore di chiave simmetrica della chiave a cui il token concede l'accesso.

Quando si usa un servizio token di sicurezza personalizzato, è necessario apportare due modifiche:

* Quando si configura un servizio di distribuzione delle licenze per un asset, è necessario specificare la chiave di sicurezza usata per la verifica dal servizio token di sicurezza personalizzato invece della chiave corrente di Azure AD.
* Quando viene generato un token JTW, viene specificata una chiave di sicurezza invece della chiave privata del certificato X509 corrente in Azure AD.

Esistono due tipi di chiavi di sicurezza:

* Chiave simmetrica: la stessa chiave viene usata per generare e verificare un token JWT.
* Chiave asimmetrica: un coppia di chiavi pubblica-privata in un certificato X509 viene usata con una chiave privata per la crittografia/generazione di un token JWT e con la chiave pubblica per la verifica del token.

Se si usa .NET Framework/C# come piattaforma di sviluppo, il certificato X509 usato per la chiave di sicurezza asimmetrica deve avere una lunghezza della chiave pari almeno a 2048 bit. Questa lunghezza di chiave è un requisito della classe System.IdentityModel.Tokens.X509AsymmetricSecurityKey in .NET Framework. In caso contrario, viene generata l'eccezione seguente: IDX10630: 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' per la firma non può essere inferiore ai bit '2048'.

## <a name="custom-key-and-license-acquisition-url"></a>URL di acquisizione di chiavi e licenze personalizzati

Utilizzare i modelli seguenti se si desidera specificare un servizio di distribuzione di licenze/chiavi diverso (non Servizi multimediali). I due campi sostituibili nei modelli sono presenti in modo che tu possa condividere i criteri di streaming tra molte risorse invece di creare un criterio di streaming per ogni asset. 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`: modello per l'URL del servizio personalizzato che fornisce le chiavi ai lettori finali. Non è necessario quando si usa Servizi multimediali di Azure per l'emissione di chiavi. 

   Il modello supporta i token sostituibili che il servizio aggiornerà in fase di esecuzione con il valore specifico della richiesta.  I valori del token attualmente supportati sono:The currently supported token values are:
   * `{AlternativeMediaId}`, che viene sostituito con il valore di StreamingLocatorId.AlternativeMediaId.
   * `{ContentKeyId}`, che viene sostituito con il valore dell'identificatore della chiave richiesta.
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`: modello per l'URL del servizio personalizzato che fornisce le licenze ai lettori finali. Non è necessario quando si usa Servizi multimediali di Azure per l'emissione di licenze.

   Il modello supporta i token sostituibili che il servizio aggiornerà in fase di esecuzione con il valore specifico della richiesta. I valori del token attualmente supportati sono:The currently supported token values are:  
   * `{AlternativeMediaId}`, che viene sostituito con il valore di StreamingLocatorId.AlternativeMediaId.
   * `{ContentKeyId}`, che viene sostituito con il valore dell'identificatore della chiave richiesta. 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`: uguale al modello precedente, solo per Widevine. 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`: Uguale al modello precedente, solo per FairPlay.  

Ad esempio:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId`ha un valore della chiave richiesta. È possibile `AlternativeMediaId` utilizzare se si desidera eseguire il mapping della richiesta a un'entità sul lato. Ad esempio, `AlternativeMediaId` può essere utilizzato per facilitare la ricerca delle autorizzazioni.

Per esempi REST che usano URL di acquisizione di licenze/chiavi personalizzati, vedere [Criteri di streaming - Crea](https://docs.microsoft.com/rest/api/media/streamingpolicies/create).

> [!NOTE]
> Widevine è un servizio fornito da Google Inc. e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google Inc.

## <a name="troubleshoot"></a>Risolvere i problemi

Se viene `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` visualizzato l'errore, assicurarsi di specificare i criteri di streaming appropriati.

Se vengono visualizzati `_NOT_SPECIFIED_IN_URL`errori che terminano con , assicurarsi di specificare il formato di crittografia nell'URL. Un esempio è `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Consultate [Protocolli di streaming e tipi di crittografia.](#streaming-protocols-and-encryption-types)

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere i contenuti con la crittografia AES](protect-with-aes128.md)
* [Proteggere con DRM](protect-with-drm.md)
* [Progettare un sistema di protezione dei contenuti multi-DRM con controllo degli accessi](design-multi-drm-system-with-access-control.md)
* [Crittografia lato archiviazione](storage-account-concept.md#storage-side-encryption)
* [Domande frequenti](frequently-asked-questions.md)
* [Gestore del token Web JSON](https://docs.microsoft.com/dotnet/framework/security/json-web-token-handler)
