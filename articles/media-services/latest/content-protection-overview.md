---
title: Proteggere il contenuto usando la crittografia dinamica di servizi multimediali - Azure | Microsoft Docs
description: Questo articolo offre una panoramica di protezione del contenuto con la crittografia dinamica. Vengono affrontati anche i tipi di crittografia e protocolli di streaming.
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
ms.custom: seodec18
ms.openlocfilehash: e13bcb7d4eeded691669277b64aba9048f3bbefa
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150424"
---
# <a name="content-protection-with-dynamic-encryption"></a>Protezione del contenuto con la crittografia dinamica

È possibile usare Servizi multimediali di Azure per proteggere i file multimediali dal momento in cui escono dal computer fino alle fasi di archiviazione, elaborazione e recapito. Con Servizi multimediali è possibile distribuire contenuti live e on demand crittografati dinamicamente con AES-128 (Advanced Encryption Standard) o con uno dei principali sistemi DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati. 

L'immagine seguente illustra il flusso di lavoro di protezione dei contenuti di Servizi multimediali: 

![Proteggere il contenuto](./media/content-protection/content-protection.svg)

&#42; *la crittografia dinamica supporta AES-128 "con chiave non crittografata", CBCS e CENC. Per altre informazioni, vedere la matrice del supporto [qui](#streaming-protocols-and-encryption-types).*

Questo articolo usa una terminologia e illustra concetti importanti per comprendere la protezione dei contenuti con Servizi multimediali.

## <a name="main-components-of-a-content-protection-system"></a>Componenti principali di un sistema di protezione del contenuto

Per completare la progettazione del sistema o dell'applicazione con "protezione del contenuto", è necessario comprendere a fondo l'ambito del lavoro richiesto. L'elenco seguente offre una panoramica di tre parti che è necessario implementare. 

1. Codice di Servizi multimediali di Azure
  
   Il [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) esempio illustra come implementare il sistema DRM multiplo con servizi multimediali v3 e anche usare servizio di recapito licenze/chiavi di servizi multimediali. È possibile crittografare ogni asset con più tipi di crittografia (AES-128, PlayReady, Widevine, FairPlay). Per informazioni su come combinarli efficacemente, vedere [Streaming protocols and encryption types](#streaming-protocols-and-encryption-types) (Protocolli di streaming e tipi di crittografia).
  
   L'esempio illustra come:

   1. Creare e configurare [criteri di contenuto chiave](https://docs.microsoft.com/rest/api/media/contentkeypolicies).

      * Definire l'autorizzazione di recapito di licenza, che specifica la logica del controllo dell'autorizzazione in base ad attestazioni nel token JWT.
      * Configurare la crittografia DRM specificando la chiave simmetrica.
      * Configurare [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md), e/o [FairPlay](fairplay-license-overview.md) licenze. I modelli consentono di configurare diritti e autorizzazioni per ognuno dei Digital Rights Management utilizzati.

        ```
        ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
        ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
        ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
        ```
   2. Creare un [localizzatore di Streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) configurata per trasmettere l'asset crittografato. 
  
      Il **localizzatore di Streaming** deve essere associato un [Streaming criteri](https://docs.microsoft.com/rest/api/media/streamingpolicies). Nell'esempio, impostiamo StreamingLocator.StreamingPolicyName al criterio di "Predefined_MultiDrmCencStreaming". Questo criterio indica che si desidera per due chiavi simmetriche (envelope e CENC) per ottenere generato e impostato sull'indicatore di posizione. Viene quindi applicata la crittografia per la busta, PlayReady e Widevine (la chiave viene distribuita al client di riproduzione in base alle licenze DRM configurate). Se si vuole anche crittografare il flusso con CBCS (FairPlay), usare "Predefined_MultiDrmStreaming".
    
      Poiché si desidera crittografare il video, il **criteri di chiave simmetrica** che è stato configurato in precedenza ha inoltre può essere associato il **localizzatore di Streaming**. 
    
   3. Creare un token di test.

      Il metodo **GetTokenAsync** illustra come creare un token di test.
   4. Compilare l'URL di streaming.

      Il metodo **GetDASHStreamingUrlAsync** illustra come compilare l'URL di streaming. In questo caso, l'URL esegue lo streaming del contenuto **DASH**.

2. Lettore con un client DRM o AES. Un'app lettore video basata su un SDK per lettori, nativa o basata su browser, deve soddisfare i requisiti seguenti:
   * L'SDK per lettori supporta i client DRM necessari
   * L'SDK per lettori supporta i protocolli di streaming necessari: Smooth, DASH e/o HLS
   * L'SDK per lettori deve essere in grado di gestire il passaggio di un token JWT nella richiesta di acquisizione della licenza
  
     È possibile creare un lettore usando l'[API di Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/). Usare l'[API ProtectionInfo di Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/) per specificare la tecnologia DRM da usare in varie piattaforme DRM.

     Per testare il contenuto crittografato con AES o CENC (Widevine e/o PlayReady), è possibile usare [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html). Assicurarsi di fare clic su "Opzioni avanzate" e controllare le opzioni d crittografia.

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

Il protocollo HLS supporta i seguenti formati di contenitore e gli schemi di crittografia.

|Formato del contenitore|Schema di crittografia|Esempio di URL|
|---|---|---|
|Tutti|AES|`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) ||
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) ||
|CMAF(fmp4) |CENC (PlayReady) ||

/ CMAF HLS + FairPlay (tra cui HEVC / H.265) è supportato nei dispositivi seguenti:

* iOS versione 11 o versione successiva 
* iPhone 8 o versione successiva
* MacOS high Sierra con Intel 7 della CPU di ultima generazione

### <a name="mpeg-dash"></a>MPEG-DASH

Il protocollo MPEG-DASH supporta i seguenti formati di contenitore e gli schemi di crittografia.

|Formato del contenitore|Schema di crittografia|Esempi di URL
|---|---|---|
|Tutti|AES|`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)||

### <a name="smooth-streaming"></a>Smooth Streaming

Il protocollo Smooth Streaming supporta i seguenti formati di contenitore e gli schemi di crittografia.

|Protocol|Formato del contenitore|Schema di crittografia|
|---|---|---|
|fMP4|AES||
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Browser

Browser comuni supportano il client DRM seguenti:

|Browser|Crittografia|
|---|---|
|Chrome|Widevine|
|Edge, IE 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="aes-128-clear-key-vs-drm"></a>Chiave non crittografata AES-128 e DRM

I clienti spesso si chiedono se devono usare la crittografia AES o un sistema DRM. La differenza principale tra i due sistemi è che con la crittografia AES la chiave simmetrica viene trasmessa al client su TLS in modo che la chiave viene crittografata in transito, ma senza crittografia aggiuntiva ("in chiaro"). Di conseguenza, la chiave usata per decrittografare il contenuto è accessibile al lettore client e può essere visualizzata in una traccia di rete sul client in testo normale. La crittografia con chiave non crittografata AES-128 è adatta nei casi in cui il visualizzatore è attendibile (ad esempio, la crittografia di video aziendali distribuiti all'interno di un'azienda per essere visualizzati dai dipendenti).

Sistemi DRM, ad esempio PlayReady, Widevine e FairPlay che offrono tutti un livello aggiuntivo di crittografia per la chiave usata per decrittografare il contenuto rispetto alla chiave non crittografata AES-128. La chiave simmetrica viene crittografata da una chiave protetta tramite il runtime DRM in aggiuntivi rispetto a qualsiasi crittografia a livello di trasporto fornita da TLS. Inoltre, la decrittografia viene gestita in un ambiente sicuro a livello di sistema operativo, più difficilmente attaccabile da un utente malintenzionato. Il DRM è consigliato per i casi d'uso in cui il visualizzatore potrebbe non essere attendibile ed è necessario il massimo livello di sicurezza.

## <a name="dynamic-encryption-and-key-delivery-service"></a>Crittografia dinamica e del servizio di distribuzione delle chiavi

In servizi multimediali v3, una chiave simmetrica è associata il localizzatore di Streaming (vedere [in questo esempio](protect-with-aes128.md)). Se si usa il servizio di distribuzione delle chiavi di servizi multimediali, è possibile consentire servizi multimediali di Azure di generare la chiave simmetrica per l'utente. È necessario generare la chiave simmetrica autonomamente se si usa un servizio di distribuzione delle chiavi o se è necessario gestire uno scenario di disponibilità elevata in cui occorre avere la stessa chiave simmetrica in due data center.

Quando un flusso viene richiesto da un lettore, Servizi multimediali usa la chiave specificata per crittografare dinamicamente i contenuti mediante la chiave non crittografata AES o la crittografia DRM. Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi di Servizi multimediali o il servizio di distribuzione delle chiavi specificato. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di chiave simmetrica specificati per tale chiave.

Servizi multimediali offre un servizio di distribuzione delle chiavi per la distribuzione di licenze DRM (PlayReady, Widevine, FairPlay) e di chiavi AES ai client autorizzati. È possibile usare l'API REST o una libreria client di Servizi multimediali per configurare i criteri di autorizzazione e autenticazione per le licenze e le chiavi.

### <a name="custom-key-and-license-acquisition-url"></a>URL di acquisizione di chiavi e licenze personalizzati

Usare i modelli seguenti se si desidera specificare un diverso chiavi e licenze servizio di distribuzione (non di servizi multimediali). I due campi sostituibili nei modelli sono presenti in modo che è possibile condividere i criteri di flusso tra molte delle risorse invece di creare un criterio di Streaming per ogni Asset. 

* EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate - modello per l'URL del servizio personalizzato distribuzione delle chiavi ai lettori di dati gestito dall'utente. Non è necessario quando si usa servizi multimediali di Azure per il rilascio delle chiavi. Il modello supporta i token sostituibili che il servizio verrà aggiornata in fase di esecuzione con il valore specifico per la richiesta.  I valori dei token attualmente supportati sono {AlternativeMediaId}, che verrà sostituito con il valore di StreamingLocatorId.AlternativeMediaId e {ContentKeyId}, che verrà sostituito con il valore dell'identificatore della chiave richiesta.
* StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate - modello per l'URL del servizio personalizzato distribuzione di licenze ai lettori di dati gestito dall'utente. Non è necessario quando si usa servizi multimediali di Azure per rilasciare le licenze. Il modello supporta i token sostituibili che il servizio verrà aggiornata in fase di esecuzione con il valore specifico per la richiesta. I valori dei token attualmente supportati sono {AlternativeMediaId}, che verrà sostituito con il valore di StreamingLocatorId.AlternativeMediaId e {ContentKeyId}, che verrà sostituito con il valore dell'identificatore della chiave richiesta. 
* StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate - come descritto precedentemente, solo per Widevine. 
* StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate - come descritto precedentemente, solo per FairPlay.  

Ad esempio: 

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

Il `ContentKeyId` ha un valore della chiave richiesta e `AlternativeMediaId` può essere usato se si desidera eseguire il mapping della richiesta a un'entità sul lato dell'utente. Ad esempio, il `AlternativeMediaId` può essere utilizzato per consentire l'individuazione di autorizzazioni.

Per esempi REST che usano personalizzato della chiave e URL di acquisizione delle licenze, vedere [creare criteri di Streaming:](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)

## <a name="control-content-access"></a>Controllare l'accesso ai contenuti

È possibile controllare chi ha accesso ai propri contenuti configurando i criteri di chiave simmetrica. Servizi multimediali supporta più modalità di autenticazione degli utenti che richiedono le chiavi. È necessario configurare i criteri di chiave simmetrica. Il client (lettore) deve soddisfare i criteri prima che la chiave possa essere distribuita al client stesso. I criteri di chiave simmetrica possono avere restrizioni **open** o **token**. 

Con i criteri di chiave simmetrica con restrizione token, la chiave simmetrica viene inviata solamente a un client che fornisca un token JSON Web (JWT) o un token Web semplice (SWT) valido nella richiesta di licenza/chiave. Questo token deve essere emesso da un servizio token di sicurezza. È possibile usare Azure Active Directory come servizio token di sicurezza oppure distribuire un servizio token di sicurezza personalizzato. Il servizio token di sicurezza deve essere configurato in modo da creare un token firmato con la chiave specificata e rilasciare le attestazioni specificate nella configurazione della restrizione token. Il servizio di distribuzione delle chiavi di Servizi multimediali restituisce al client la chiave o la licenza richiesta se il token è valido e le attestazioni del token corrispondono a quelle configurate per la chiave o la licenza.

Quando si configurano i criteri di restrizione del token, è necessario specificare i parametri primary verification key, issuer e audience. Il parametro primary verification key include la chiave usata per firmare il token. Il parametro issuer è il servizio token di sicurezza che rilascia il token. Il parametro audience (talvolta denominato scope) descrive l'ambito del token o la risorsa a cui il token autorizza l'accesso. Il servizio di distribuzione delle chiavi di Servizi multimediali verifica che i valori nel token corrispondano ai valori nel modello.

I clienti usano spesso un servizio token di sicurezza personalizzati per includere le attestazioni personalizzate nel token per scegliere tra diversi ContentKeyPolicyOptions con parametri di licenza DRM diversi (una licenza di sottoscrizione rispetto a una licenza di noleggio) o da includere un'attestazione che rappresenta la chiave simmetrica Identificatore della chiave di cui il token concede l'accesso.
 
## <a name="storage-side-encryption"></a>Crittografia lato archiviazione

Per proteggere gli asset inattivi, è necessario crittografarli tramite crittografia lato archiviazione. La tabella seguente illustra il funzionamento della crittografia lato archiviazione in Servizi multimediali versione 3:

|Opzione di crittografia|DESCRIZIONE|Servizi multimediali v3|
|---|---|---|
|Crittografia di archiviazione di Servizi multimediali| Crittografia AES-256, chiave gestita da Servizi multimediali|Non supportato<sup>(1)</sup>|
|[Crittografia del servizio di archiviazione per dati inattivi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Crittografia lato server offerta da Archiviazione di Azure, chiave gestita da Azure o dal cliente|Supportato|
|[Crittografia lato client di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Crittografia lato client offerta da Archiviazione di Azure, chiave gestita dal cliente in Key Vault|Non supportate|

<sup>1</sup> In Servizi multimediali versione 3 la crittografia di archiviazione (crittografia AES-256) è supportata per la compatibilità con le versioni precedenti solo se gli asset sono stati creati con Servizi multimediali versione 2. In altre parole, la versione 3 funziona con asset con crittografia di archiviazione esistenti, ma non consente la creazione di nuovi asset di questo tipo.

## <a name="troubleshoot"></a>Risolvere problemi

Se viene visualizzato il `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` errore, assicurarsi di specificare i criteri di flusso appropriata.

Se si verificano errori che terminano con `_NOT_SPECIFIED_IN_URL`, assicurarsi di specificare il formato di crittografia nell'URL. Ad esempio: `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Visualizzare [protocolli e tipi di crittografia di Streaming](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire commenti e suggerimenti, ottenere gli aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere i contenuti con la crittografia AES](protect-with-aes128.md)
* [Proteggere con DRM](protect-with-drm.md)
* [Progettare il sistema di protezione del contenuto con DRM multiplo con controllo degli accessi](design-multi-drm-system-with-access-control.md)
* [Domande frequenti](frequently-asked-questions.md)

