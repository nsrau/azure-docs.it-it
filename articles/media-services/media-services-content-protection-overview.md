---
title: Proteggere i contenuti con Servizi multimediali di Azure | Microsoft Docs
description: Questi articoli forniscono informazioni generali sulla protezione dei contenuti con Servizi multimediali.
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 6475a865b9d1b263bd7cc68c99acdb5f6959531e
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2017
---
# <a name="protecting-content-overview"></a>Informazioni generali sulla protezione dei contenuti
Servizi multimediali di Microsoft Azure consente di proteggere i file multimediali dal momento in cui escono dal computer fino alle fasi di archiviazione, elaborazione e recapito. Servizi multimediali consente di distribuire contenuti live e on demand crittografati dinamicamente con AES-128 (Advanced Encryption Standard), o con uno dei principali sistemi DRM: Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati. 

L'immagine seguente illustra il flusso di lavoro di protezione dei contenuti di Servizi multimediali di Azure. 

![Protezione con PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Questo articolo illustra concetti e terminologie importanti per comprendere la protezione dei contenuti con AMS. L'articolo include anche collegamenti ad articoli che descrivono come proteggere i contenuti. 

## <a name="dynamic-encryption"></a>Crittografia dinamica
Servizi multimediali di Microsoft Azure consente di distribuire i contenuti crittografati dinamicamente con chiave non crittografata AES o crittografia DRM: Microsoft PlayReady, Google Widevine e Apple FairPlay. Attualmente è possibile crittografare i formati di streaming seguenti: HLS, MPEG DASH e Smooth Streaming. La crittografia sui download progressivi non è supportata. Ogni metodo di crittografia supporta i protocolli di streaming seguenti:
- AES: MPEG-DASH, Smooth Streaming e HLS
- PlayReady: MPEG-DASH, Smooth Streaming e HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

Per crittografare un asset è necessario associare una chiave simmetrica di crittografia all'asset e configurare anche i criteri di autorizzazione per la chiave. Le chiavi simmetriche possono essere specificate o generate automaticamente da Servizi multimediali.

È anche necessario configurare i criteri di distribuzione degli asset. Se si vuole trasmettere in streaming un asset crittografato di archiviazione assicurarsi di specificare come si intende recapitarlo configurando i criteri di distribuzione degli asset.

Quando un flusso viene richiesto da un lettore, Servizi multimediali usa la chiave specificata per crittografare dinamicamente i contenuti mediante la chiave non crittografata AES o la crittografia DRM. Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi AMS. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di autorizzazione specificati.

## <a name="aes-128-clear-key-vs-drm"></a>Chiave non crittografata AES-128 rispetto a un sistema DRM
I clienti spesso si chiedono se devono usare la crittografia AES o un sistema DRM. La differenza principale tra l'uso della crittografia AES e dei sistemi DRM è che con la crittografia AES la chiave simmetrica viene trasmessa al client in un formato non crittografato ("in chiaro"). Di conseguenza è possibile visualizzare la chiave usata per crittografare i contenuti in una traccia di rete sul client in testo non crittografato. La chiave non crittografata AES-128 è adatta nei casi in cui il visualizzatore è attendibile (ad esempio: la crittografia di video aziendali distribuiti all'interno di un'azienda per essere visualizzati dai dipendenti).

PlayReady, Widevine e FairPlay offrono tutti un livello superiore di crittografia rispetto alla crittografia della chiave non crittografata AES-128. La chiave simmetrica viene trasmessa in un formato crittografato. Inoltre, la decrittografia viene gestita in un ambiente sicuro a livello di sistema operativo in cui è molto più difficile che un utente malintenzionato possa attaccare. Il DRM è consigliato per i casi d'uso in cui il visualizzatore potrebbe non essere attendibile ed è necessario il massimo livello di sicurezza.

## <a name="storage-encryption"></a>Crittografia di archiviazione
Usare la crittografia di archiviazione per crittografare i contenuti localmente tramite crittografia AES a 256 bit, quindi caricarli nel servizio Archiviazione di Azure dove vengono archiviati in forma crittografata. Gli asset protetti con la crittografia di archiviazione vengono decrittografati automaticamente e inseriti in un file system crittografato prima della codifica. Se necessario, inoltre, possono essere ricrittografati prima del successivo caricamento come nuovo asset di output. La crittografia di archiviazione viene usata principalmente quando si vogliono proteggere i file multimediali con input di alta qualità con una crittografia avanzata sul disco locale.

Per poter trasmettere l'asset crittografato di archiviazione, è necessario configurare i criteri di distribuzione dell'asset in modo da informare Servizi multimediali della modalità di distribuzione del contenuto. Per permettere lo streaming dell'asset, il server di streaming rimuove la crittografia ed esegue lo streaming dei contenuti usando i criteri di recapito specificati (ad esempio AES, crittografia comune o nessuna crittografia).

## <a name="types-of-encryption"></a>Tipi di crittografia
PlayReady e Widevine usano la crittografia comune (modalità CTR AES). FairPlay usa la crittografia della modalità CBC AES. La crittografia chiave non crittografata AES-128 usa la crittografia envelope.

## <a name="licenses-and-keys-delivery-service"></a>Servizio di distribuzione di licenze e chiavi
Servizi multimediali offre un servizio di distribuzione delle chiavi per la distribuzione di licenze DRM (PlayReady, Widevine, FairPlay) e di chiavi AES ai client autorizzati. È possibile usare il [portale di Azure](media-services-portal-protect-content.md), l'API REST o Media Services SDK per .NET per configurare i criteri di autorizzazione e autenticazione per le licenze e le chiavi.

## <a name="control-content-access"></a>Controllare l'accesso ai contenuti
È possibile controllare chi ha accesso ai propri contenuti configurando i criteri di autorizzazione delle chiavi simmetriche. I criteri di autorizzazione delle chiavi simmetriche possono prevedere una limitazione aperta o relativa al token.

### <a name="open-authorization"></a>Autorizzazione aperta
Con i criteri di autorizzazione aperti la chiave simmetrica viene inviata a qualsiasi client (nessuna limitazione).

### <a name="token-authorization"></a>Autorizzazione con token
Con i criteri di autorizzazione con limitazione relativa al token, la chiave simmetrica verrà inviata solamente a un client che fornisca un token JSON Web (JWT) o un token Web semplice (SWT) validi nella richiesta di licenza/chiave. Questo token deve essere emesso da un servizio token di sicurezza (STS). Microsoft Active Directory può essere usato come servizio token di sicurezza oppure è possibile distribuire un servizio token di sicurezza personalizzato. Il servizio token di sicurezza deve essere configurato in modo da creare un token firmato con la chiave specificata e rilasciare le attestazioni specificate nella configurazione della restrizione token. Il servizio di distribuzione di chiavi di Servizi multimediali restituirà al client la chiave o la licenza richiesta se il token è valido e le attestazioni del token corrispondono a quelle configurate per la chiave o la licenza.

Quando si configurano i criteri di restrizione Token, è necessario specificare i parametri primary verification key, issuer e audience. Il parametro primary verification key include la chiave usata per firmare il token. Il parametro issuer è il servizio token di sicurezza che emette il token. Il parametro audience (talvolta denominato scope) descrive l'ambito del token o la risorsa a cui il token autorizza l'accesso. Il servizio di distribuzione delle chiavi di Servizi multimediali verifica che i valori nel token corrispondano ai valori nel modello.

## <a name="streaming-urls"></a>URL di streaming
Se l'asset è stato crittografato con più soluzioni DRM, è necessario usare un tag di crittografia nell'URL di streaming (format='m3u8-aapl', encryption='xxx').

Si applicano le considerazioni seguenti:
* Può essere specificato solo un tipo di crittografia.
* Il tipo di crittografia non deve essere specificato nell'URL se all'asset è stata applicata una sola crittografia.
* Il tipo di crittografia non fa distinzione tra maiuscole e minuscole.
* Possono essere specificati i seguenti tipi di crittografia:  
  * **cenc**: per PlayReady or Widevine (crittografia comune)
  * **cbcs-aapl**: per FairPlay (crittografia CBC AES)
  * **cbc**: per la crittografia envelope AES (crittografia Envelope)

## <a name="next-steps"></a>Passaggi successivi
Gli articoli seguenti descrivono i passaggi successivi per iniziare con la protezione dei contenuti:
* [Proteggere i contenuti con la crittografia di archiviazione](media-services-rest-storage-encryption.md)
* [Proteggere i contenuti con la crittografia AES](media-services-protect-with-aes128.md)
* [Proteggere i contenuti con PlayReady e/o Widevine ](media-services-protect-with-playready-widevine.md)
* [Proteggere i contenuti con FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Collegamenti correlati
[Prezzi della distribuzione della licenza PlayReady di Servizi multimediali di Azure](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Debug del flusso con crittografia AES in Servizi multimediali di Azure](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[Autenticazione tramite token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrare l'app basata su OWIN MVC di Servizi multimediali di Azure con Azure Active Directory e limitare la distribuzione di chiavi simmetriche in base ad attestazioni JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
