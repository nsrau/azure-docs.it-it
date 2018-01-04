---
title: Proteggere i contenuti con Servizi multimediali di Azure | Documentazione Microsoft
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
ms.openlocfilehash: 13447fd9193374d80ed5c2e6af8543f11b95e709
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="content-protection-overview"></a>Panoramica di protezione del contenuto
 È possibile utilizzare servizi multimediali di Azure per proteggere i file multimediali dal momento in che cui lascia computer tramite l'archiviazione, elaborazione e il recapito. Con servizi multimediali, è possibile recapitare il contenuto in tempo reale e su richiesta in modo dinamico crittografato con Advanced Encryption Standard (AES-128) oppure uno dei sistemi principali tre da digital rights management (DRM): Microsoft PlayReady e Google Widevine FairPlay Apple. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati. 

L'immagine seguente illustra il flusso di lavoro di protezione del contenuto di servizi multimediali: 

![Protezione con PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Questo articolo illustra i concetti e terminologia importante per comprendere la protezione dei contenuti con servizi multimediali. L'articolo include anche collegamenti ad articoli che descrivono come proteggere i contenuti. 

## <a name="dynamic-encryption"></a>Crittografia dinamica
 Servizi multimediali consente di distribuire il contenuto crittografato in modo dinamico con chiave non crittografata AES o la crittografia DRM con PlayReady, Widevine o FairPlay. Attualmente, è possibile crittografare i formati di HTTP Live Streaming (HLS), MPEG DASH e Smooth Streaming. La crittografia sui download progressivi non è supportata. Ogni metodo di crittografia supporta i protocolli di streaming seguenti:

- AES: MPEG-DASH, Smooth Streaming e HLS
- PlayReady: MPEG-DASH, Smooth Streaming e HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

Per crittografare un asset è necessario associare una chiave simmetrica di crittografia all'asset e configurare anche i criteri di autorizzazione per la chiave. Le chiavi simmetriche possono essere specificate o generate automaticamente da Servizi multimediali.

È anche necessario configurare i criteri di distribuzione degli asset. Se si desidera eseguire il flusso di un asset crittografato di archiviazione, assicurarsi di specificare la modalità di recapitarlo configurando i criteri di recapito di asset.

Quando un flusso è richiesto da un lettore, servizi multimediali Usa la chiave specificata per crittografare dinamicamente il contenuto tramite la chiave non crittografata AES o la crittografia DRM. Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi di servizi multimediali. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di autorizzazione specificati.

## <a name="aes-128-clear-key-vs-drm"></a>Vs chiave non crittografata AES-128. DRM
I clienti spesso si chiedono se devono usare la crittografia AES o un sistema DRM. La differenza principale tra i due sistemi è che, con la crittografia AES la chiave simmetrica viene trasmesso al client in un formato non crittografato (in chiaro"). Di conseguenza, è possibile visualizzare la chiave utilizzata per crittografare il contenuto in una traccia di rete nel client in testo normale. Crittografia chiave non crittografata AES-128 è adatta per i casi di utilizzo in cui il visualizzatore è una parte attendibile (ad esempio, la crittografia video aziendali distribuiti all'interno di un'azienda per essere visualizzati dai dipendenti).

PlayReady, Widevine e FairPlay offrono tutti un livello superiore di crittografia rispetto alla crittografia della chiave non crittografata AES-128. La chiave simmetrica viene trasmessa in un formato crittografato. Inoltre, la decrittografia è gestita in un ambiente sicuro a livello di sistema operativo, in cui risulta più difficile per un utente malintenzionato può attaccare. DRM è consigliato per i casi di utilizzo in cui il visualizzatore potrebbe non essere una parte attendibile ed è necessario il massimo livello di sicurezza.

## <a name="storage-encryption"></a>Crittografia di archiviazione
È possibile utilizzare la crittografia di archiviazione per crittografare il contenuto non crittografato in locale tramite la crittografia AES 256 bit. È quindi possibile caricarlo in archiviazione di Azure, dove viene archiviato in forma crittografata. Gli asset protetti con crittografia di archiviazione vengono decrittografati automaticamente e inseriti in un file system crittografato prima della codifica. Le attività sono facoltativamente crittografare nuovamente prima di caricare nuovamente come nuovo asset di output. La crittografia di archiviazione viene usata principalmente quando si vogliono proteggere i file multimediali con input di alta qualità con una crittografia avanzata sul disco locale.

Per il recapito di un asset crittografato di archiviazione, è necessario configurare i criteri di distribuzione dell'asset in modo che sia in grado di servizi multimediali come si desidera distribuire il contenuto. Prima dell'asset può essere trasmesso, il server di flusso decrittografa e streaming del contenuto usando i criteri di recapito specificati (ad esempio, AES, crittografia comune o Nessuna crittografia).

## <a name="types-of-encryption"></a>Tipi di crittografia
PlayReady e Widevine utilizzano la crittografia comune (modalità CTR AES). FairPlay Usa la crittografia AES-modalità CBC. Crittografia chiave non crittografata AES-128 Usa la crittografia envelope.

## <a name="licenses-and-keys-delivery-service"></a>Servizio di distribuzione di licenze e chiavi
Servizi multimediali offre un servizio di distribuzione delle chiavi per la distribuzione di licenze DRM (PlayReady, Widevine, FairPlay) e di chiavi AES ai client autorizzati. È possibile utilizzare [il portale di Azure](media-services-portal-protect-content.md), l'API REST, o Media Services SDK per .NET per configurare i criteri di autenticazione e autorizzazione per le licenze e le chiavi.

## <a name="control-content-access"></a>Controllare l'accesso ai contenuti
È possibile controllare chi ha accesso ai propri contenuti configurando i criteri di autorizzazione delle chiavi simmetriche. I criteri di autorizzazione delle chiavi simmetriche possono prevedere una limitazione aperta o relativa al token.

### <a name="open-authorization"></a>Autorizzazione aperta
Con i criteri di autorizzazione aperti la chiave simmetrica viene inviata a qualsiasi client (nessuna limitazione).

### <a name="token-authorization"></a>Autorizzazione con token
Con un criterio di autorizzazione con restrizioni token, la chiave simmetrica viene inviata solo a un client che non presenta un valido JSON Web Token (JWT) o un token web semplice (SWT) nella richiesta di licenza chiave. Questo token deve essere emesso da un servizio token di sicurezza (STS). È possibile usare Azure Active Directory come un servizio token di sicurezza o distribuire un servizio token di sicurezza personalizzato. Il servizio token di sicurezza deve essere configurato in modo da creare un token firmato con la chiave specificata e rilasciare le attestazioni specificate nella configurazione della restrizione token. Il servizio di distribuzione delle chiavi di servizi multimediali restituisce la chiave o licenza richiesta al client se il token è valido e le attestazioni nel token corrispondono a quelli configurati per la chiave di licenza.

Quando si configurano i criteri con restrizione token, è necessario specificare la chiave di verifica primaria, l'autorità emittente e parametri pubblico. La chiave di verifica primaria contiene il token è stato firmato con la chiave. L'autorità emittente è il servizio token di sicurezza che emette il token. Il parametro audience (talvolta denominato scope) descrive l'ambito del token o la risorsa a cui il token autorizza l'accesso. Il servizio di distribuzione delle chiavi di Servizi multimediali verifica che i valori nel token corrispondano ai valori nel modello.

## <a name="streaming-urls"></a>URL di streaming
Se l'asset è stata crittografata con più DRM, è possibile utilizzare un tag di crittografia nell'URL di streaming: (formato = 'm3u8-aapl', crittografia = 'xxx').

Si applicano le considerazioni seguenti:

* Può essere specificato solo un tipo di crittografia.
* Tipo di crittografia non deve essere specificato nell'URL se solo uno di crittografia è stata applicata all'asset.
* Il tipo di crittografia non fa distinzione tra maiuscole e minuscole.
* Possono essere specificati i seguenti tipi di crittografia:
  * **cenc**: per PlayReady o Widevine (la crittografia comune)
  * **cbcs-aapl**: per FairPlay (crittografia CBC AES)
  * **CBC**: crittografia envelope AES per

## <a name="next-steps"></a>Passaggi successivi
Gli articoli seguenti descrivono i passaggi successivi per iniziare con la protezione del contenuto:

* [Proteggere i contenuti con la crittografia di archiviazione](media-services-rest-storage-encryption.md)
* [Proteggere i contenuti con la crittografia AES](media-services-protect-with-aes128.md)
* [Proteggere i contenuti con PlayReady e/o Widevine ](media-services-protect-with-playready-widevine.md)
* [Proteggere i contenuti con FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Collegamenti correlati

* [Prezzi della distribuzione della licenza PlayReady di Servizi multimediali di Azure](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)
* [Eseguire il debug per il flusso di crittografia AES in servizi multimediali di Azure](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)
* [Autenticazione tramite token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Integrare l'app basata su MVC OWIN servizi multimediali di Azure con Azure Active Directory e limitare il recapito del contenuto di chiave in base alle attestazioni JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
