---
title: Proteggere i contenuti con la crittografia dinamica di servizi multimediali-Azure | Microsoft Docs
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
ms.date: 07/17/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 5d31e4a523fdedf9907e33c70638f07a08461ed1
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310308"
---
# <a name="content-protection-with-dynamic-encryption"></a>Protezione del contenuto con la crittografia dinamica

È possibile usare Servizi multimediali di Azure per proteggere i file multimediali dal momento in cui escono dal computer fino alle fasi di archiviazione, elaborazione e recapito. Con Servizi multimediali è possibile distribuire contenuti live e on demand crittografati dinamicamente con AES-128 (Advanced Encryption Standard) o con uno dei principali sistemi DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati. 

In servizi multimediali V3, una chiave simmetrica è associata a un localizzatore di streaming (vedere [questo esempio](protect-with-aes128.md)). Se si usa il servizio di distribuzione delle chiavi di servizi multimediali, è possibile consentire a servizi multimediali di Azure di generare automaticamente la chiave simmetrica. È necessario generare la chiave simmetrica se si usa il servizio di distribuzione delle chiavi o se è necessario gestire uno scenario a disponibilità elevata in cui è necessario avere la stessa chiave simmetrica in due data center.

Quando un flusso viene richiesto da un lettore, Servizi multimediali usa la chiave specificata per crittografare dinamicamente i contenuti mediante la chiave non crittografata AES o la crittografia DRM. Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi di Servizi multimediali o il servizio di distribuzione delle chiavi specificato. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di chiave simmetrica specificati per tale chiave.

È possibile usare l'API REST o una libreria client di Servizi multimediali per configurare i criteri di autorizzazione e autenticazione per le licenze e le chiavi.

L'immagine seguente illustra il flusso di lavoro di protezione dei contenuti di Servizi multimediali: 

![Proteggere il contenuto](./media/content-protection/content-protection.svg)

&#42; *la crittografia dinamica supporta AES-128 "con chiave non crittografata", CBCS e CENC. Per altre informazioni, vedere la matrice del supporto [qui](#streaming-protocols-and-encryption-types).*

Questo articolo usa una terminologia e illustra concetti importanti per comprendere la protezione dei contenuti con Servizi multimediali.

## <a name="main-components-of-a-content-protection-system"></a>Componenti principali di un sistema di protezione del contenuto

Per completare la progettazione del sistema o dell'applicazione con "protezione del contenuto", è necessario comprendere a fondo l'ambito del lavoro richiesto. L'elenco seguente offre una panoramica di tre parti che è necessario implementare. 

1. Codice di Servizi multimediali di Azure
  
   L'esempio [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) Mostra come implementare un sistema con DRM multiplo con servizi multimediali v3 usando .NET. Viene inoltre illustrato come utilizzare il servizio di distribuzione di licenze/chiavi di servizi multimediali. È possibile crittografare ogni asset con più tipi di crittografia (AES-128, PlayReady, Widevine, FairPlay). Per informazioni su come combinarli efficacemente, vedere [Streaming protocols and encryption types](#streaming-protocols-and-encryption-types) (Protocolli di streaming e tipi di crittografia).
  
   L'esempio illustra come:

   1. Creare e configurare [criteri della chiave](content-key-policy-concept.md)simmetrica. È possibile creare un **criterio della chiave** simmetrica per configurare il modo in cui la chiave simmetrica (che fornisce l'accesso sicuro agli asset) viene fornita ai client finali.    

      * Definire l'autorizzazione di recapito di licenza, che specifica la logica del controllo dell'autorizzazione in base ad attestazioni nel token JWT.
      * Configurare le licenze [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md)e/o [Fairplay](fairplay-license-overview.md) . I modelli consentono di configurare diritti e autorizzazioni per ognuno dei Digital Rights Management utilizzati.

        ```
        ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
        ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
        ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
        ```
   2. Creare un [localizzatore di streaming](streaming-locators-concept.md) configurato per lo streaming dell'asset crittografato. 
  
      Il **localizzatore di streaming** deve essere associato a un [criterio di streaming](streaming-policy-concept.md). Nell'esempio, si imposta StreamingLocator. StreamingPolicyName sul criterio "Predefined_MultiDrmCencStreaming". Vengono applicate le crittografie PlayReady e Widevine, la chiave viene recapitata al client di riproduzione in base alle licenze DRM configurate. Se si vuole anche crittografare il flusso con CBCS (FairPlay), usare "Predefined_MultiDrmStreaming".
      
      Il localizzatore di streaming è associato anche ai **criteri della chiave** simmetrica definiti.
    
   3. Creare un token di test.

      Il metodo **GetTokenAsync** illustra come creare un token di test.
   4. Compilare l'URL di streaming.

      Il metodo **GetDASHStreamingUrlAsync** illustra come compilare l'URL di streaming. In questo caso, l'URL esegue lo streaming del contenuto **DASH**.

2. Lettore con un client DRM o AES. Un'app lettore video basata su un SDK per lettori, nativa o basata su browser, deve soddisfare i requisiti seguenti:
   * L'SDK per lettori supporta i client DRM necessari
   * L'SDK per lettori supporta i protocolli di streaming necessari: Smooth, DASH e/o HLS
   * L'SDK per lettori deve essere in grado di gestire il passaggio di un token JWT nella richiesta di acquisizione della licenza
  
     È possibile creare un lettore usando l'[API di Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/). Usare l'[API ProtectionInfo di Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/) per specificare la tecnologia DRM da usare in varie piattaforme DRM.

     Per testare il contenuto crittografato con AES o CENC (Widevine e/o PlayReady), è possibile usare [Azure Media Player](https://aka.ms/azuremediaplayer). Assicurarsi di fare clic su "Opzioni avanzate" e controllare le opzioni d crittografia.

     Se si vuole testare il contenuto crittografato di FairPlay, usare [questo lettore di test](https://aka.ms/amtest). Il lettore supporta Widevine, PlayReady e DRM FairPlay oltre alla crittografia AES-128 a chiave non crittografata. 
    
     È necessario scegliere il browser appropriato per testare i vari DRM: Chrome, Opera o Firefox per Widevine, Microsoft Edge o Internet Explorer 11 per PlayReady, Safari su macOS per FairPlay.

3. Secure Token Service (STS) che rilascia JSON Web Token (JWT) come token di accesso per l'accesso alle risorse back-end. È possibile usare i servizi di distribuzione di licenze di AMS come risorsa di back-end. Un STS deve definire gli elementi seguenti:

   * Autorità di certificazione e destinatari o ambito
   * Attestazioni, che dipendono dai requisiti aziendali di protezione del contenuto
   * Verifica simmetrica o asimmetrica per la verifica della firma
   * Supporto del rollover della chiave, se necessario

     È possibile usare [questo strumento del ](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) per testare il servizio token di sicurezza, che supporta tutti e 3 i tipi di chiave di verifica: simmetrica, asimmetrica o Azure AD con rollover della chiave. 

> [!NOTE]
> È altamente consigliabile concentrarsi ed eseguire un test completo su ogni parte, come descritto in precedenza, prima di procedere alla parte successiva. Per testare il sistema di "protezione del contenuto", usare gli strumenti specificati nell'elenco precedente.  

## <a name="streaming-protocols-and-encryption-types"></a>Protocolli di streaming e tipi di crittografia

È possibile usare Servizi multimediali per distribuire i contenuti crittografati dinamicamente con chiave non crittografata AES o con crittografia DRM mediante PlayReady, Widevine o FairPlay. Attualmente è possibile crittografare i formati HLS (HTTP Live Streaming), MPEG DASH e Smooth Streaming. Ogni protocollo supporta i metodi di crittografia seguenti:

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

* iOS V11 o versione successiva 
* iPhone 8 o versione successiva
* MacOS High Sierra con CPU Intel 7th Gen

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
|Microsoft Edge, IE 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="control-content-access"></a>Controllare l'accesso ai contenuti

È possibile controllare chi ha accesso ai propri contenuti configurando i criteri di chiave simmetrica. Servizi multimediali supporta più modalità di autenticazione degli utenti che richiedono le chiavi. È necessario configurare i criteri di chiave simmetrica. Il client (lettore) deve soddisfare i criteri prima che la chiave possa essere distribuita al client stesso. I criteri di chiave simmetrica possono avere restrizioni **open** o **token**. 

Con i criteri di chiave simmetrica con restrizione token, la chiave simmetrica viene inviata solamente a un client che fornisca un token JSON Web (JWT) o un token Web semplice (SWT) valido nella richiesta di licenza/chiave. Questo token deve essere emesso da un servizio token di sicurezza. È possibile usare Azure Active Directory come servizio token di sicurezza oppure distribuire un servizio token di sicurezza personalizzato. Il servizio token di sicurezza deve essere configurato in modo da creare un token firmato con la chiave specificata e rilasciare le attestazioni specificate nella configurazione della restrizione token. Il servizio di distribuzione delle chiavi di Servizi multimediali restituisce al client la chiave o la licenza richiesta se il token è valido e le attestazioni del token corrispondono a quelle configurate per la chiave o la licenza.

Quando si configurano i criteri di restrizione del token, è necessario specificare i parametri primary verification key, issuer e audience. Il parametro primary verification key include la chiave usata per firmare il token. Il parametro issuer è il servizio token di sicurezza che rilascia il token. Il parametro audience (talvolta denominato scope) descrive l'ambito del token o la risorsa a cui il token autorizza l'accesso. Il servizio di distribuzione delle chiavi di Servizi multimediali verifica che i valori nel token corrispondano ai valori nel modello.

I clienti spesso usano un servizio token di servizio personalizzato per includere le attestazioni personalizzate nel token per scegliere tra ContentKeyPolicyOptions diversi con diversi parametri di licenza DRM (una licenza di sottoscrizione rispetto a una licenza di noleggio) o includere un'attestazione che rappresenta la chiave simmetrica Identificatore della chiave a cui il token concede l'accesso.

### <a name="token-replay-prevention"></a>Prevenzione della riproduzione del token

La funzionalità di *prevenzione della riproduzione dei token* consente ai clienti di servizi multimediali di impostare un limite per il numero di volte in cui è possibile usare lo stesso token per richiedere una chiave o una licenza. Il cliente può aggiungere un'attestazione di `urn:microsoft:azure:mediaservices:maxuses` tipo nel token, dove il valore è il numero di volte in cui il token può essere usato per acquisire una licenza o una chiave. Tutte le richieste successive con lo stesso token al recapito della chiave restituiranno una risposta non autorizzata. Vedere come aggiungere l'attestazione nell' [esempio di DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Considerazioni

* I clienti devono avere il controllo sulla generazione dei token. L'attestazione deve essere inserita nel token stesso.
* Quando si usa questa funzionalità, le richieste con token la cui ora di scadenza è più di un'ora a partire dal momento in cui la richiesta viene ricevuta vengono rifiutate con una risposta non autorizzata.
* I token sono identificati in modo univoco dalla firma. Qualsiasi modifica apportata al payload (ad esempio, l'aggiornamento all'ora di scadenza o l'attestazione) modifica la firma del token e viene conteggiata come un nuovo token non rilevato prima dal recapito della chiave.
* La riproduzione ha esito negativo se il token `maxuses` ha superato il valore impostato dal cliente.
* Questa funzionalità può essere usata per tutto il contenuto protetto esistente (è necessario modificare solo il token emesso).
* Questa funzionalità funziona sia con JWT che con SWT.

## <a name="custom-key-and-license-acquisition-url"></a>URL di acquisizione di licenze e chiavi personalizzate

Usare i modelli seguenti se si vuole specificare un servizio di distribuzione di chiavi e licenze diverso (non servizi multimediali). I due campi sostituibili nei modelli sono disponibili per poter condividere i criteri di streaming tra più asset anziché creare criteri di flusso per ogni asset. 

* EnvelopeEncryption. CustomKeyAcquisitionUrlTemplate: modello per l'URL del servizio personalizzato che fornisce le chiavi ai giocatori degli utenti finali. Non richiesto quando si usa servizi multimediali di Azure per l'emissione di chiavi. Il modello supporta i token sostituibili che il servizio aggiornerà in fase di esecuzione con il valore specifico della richiesta.  I valori di token attualmente supportati sono {AlternativeMediaId}, che viene sostituito con il valore di StreamingLocatorId. AlternativeMediaId e {Idchiavesimmetrica}, che viene sostituito con il valore dell'identificatore della chiave richiesta.
* StreamingPolicyPlayReadyConfiguration. CustomLicenseAcquisitionUrlTemplate: modello per l'URL del servizio personalizzato che fornisce le licenze ai giocatori degli utenti finali. Non richiesto quando si usa servizi multimediali di Azure per emettere licenze. Il modello supporta i token sostituibili che il servizio aggiornerà in fase di esecuzione con il valore specifico della richiesta. I valori di token attualmente supportati sono {AlternativeMediaId}, che viene sostituito con il valore di StreamingLocatorId. AlternativeMediaId e {Idchiavesimmetrica}, che viene sostituito con il valore dell'identificatore della chiave richiesta. 
* StreamingPolicyWidevineConfiguration. CustomLicenseAcquisitionUrlTemplate: uguale a quello riportato sopra, solo per Widevine. 
* StreamingPolicyFairPlayConfiguration. CustomLicenseAcquisitionUrlTemplate: uguale a quello riportato sopra, solo per FairPlay.  

Ad esempio:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

Ha un valore della chiave richiesta `AlternativeMediaId` e può essere usato se si vuole eseguire il mapping della richiesta a un'entità sul lato. `ContentKeyId` Ad esempio, può `AlternativeMediaId` essere usato per semplificare la ricerca delle autorizzazioni.

Per esempi REST che usano URL di acquisizione di licenze e chiavi personalizzate, vedere [criteri di streaming-crea](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)

## <a name="troubleshoot"></a>Risolvere problemi

Se viene ricevuto l' `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` errore, assicurarsi di specificare i criteri di flusso appropriati.

Se vengono visualizzati errori che terminano `_NOT_SPECIFIED_IN_URL`con, assicurarsi di specificare il formato di crittografia nell'URL. Ad esempio `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Vedere [protocolli di streaming e tipi di crittografia](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere i contenuti con la crittografia AES](protect-with-aes128.md)
* [Proteggere con DRM](protect-with-drm.md)
* [Progettare un sistema di protezione dei contenuti con DRM multiplo con controllo di accesso](design-multi-drm-system-with-access-control.md)
* [Crittografia lato archiviazione](storage-account-concept.md#storage-side-encryption)
* [Domande frequenti](frequently-asked-questions.md)

