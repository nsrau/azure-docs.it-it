---
title: Proteggere i contenuti con Servizi multimediali di Azure | Documentazione Microsoft
description: Questi articoli forniscono informazioni generali sulla protezione dei contenuti con Servizi multimediali.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2018
ms.author: juliako
ms.openlocfilehash: 1568ea3431f18b7a7a020d34d803f883904e18b4
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39115231"
---
# <a name="content-protection-overview"></a>Panoramica della protezione del contenuto

È possibile usare Servizi multimediali di Azure per proteggere i file multimediali dal momento in cui escono dal computer fino alle fasi di archiviazione, elaborazione e recapito. Con Servizi multimediali è possibile distribuire contenuti live e on demand crittografati dinamicamente con AES-128 (Advanced Encryption Standard) o con uno dei principali sistemi DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati. 

L'immagine seguente illustra il flusso di lavoro di protezione dei contenuti di Servizi multimediali: 

![Proteggere il contenuto](./media/content-protection/content-protection.png)

&#42; *la crittografia dinamica supporta AES-128 "con chiave non crittografata", CBCS e CENC. Per altre informazioni, vedere la matrice del supporto [qui](#streaming-protocols-and-encryption-types).*

Questo articolo usa una terminologia e illustra concetti importanti per comprendere la protezione dei contenuti con Servizi multimediali. L'articolo include anche collegamenti ad articoli che descrivono come proteggere i contenuti. 

## <a name="main-components-of-the-content-protection-system"></a>Componenti principali del sistema di protezione del contenuto

Per completare la progettazione del sistema o dell'applicazione con "protezione del contenuto", è necessario comprendere a fondo l'ambito del lavoro richiesto. L'elenco seguente offre una panoramica di tre parti che è necessario implementare. 

1. Codice di Servizi multimediali di Azure
  
  * Modelli di licenza per PlayReady, Widevine e/o FairPlay. I modelli consentono di configurare diritti e autorizzazioni per ognuno dei DRM utilizzati
  * Autorizzazione di recapito di licenza, che specifica la logica del controllo dell'autorizzazione in base ad attestazioni in JWT
  * Chiavi simmetriche, protocolli di streaming e DRM corrispondenti applicati, che definiscono la crittografia DRM

  > [!NOTE]
  > È possibile crittografare ogni asset con più tipi di crittografia (AES-128, PlayReady, Widevine, FairPlay). Per informazioni su come combinarli efficacemente, vedere [Streaming protocols and encryption types](#streaming-protocols-and-encryption-types) (Protocolli di streaming e tipi di crittografia).
  
  L'articolo seguente mostra i passaggi per la crittografia dei contenuti con AES e/o DRM: 
  
  * [Proteggere i contenuti con la crittografia AES](protect-with-aes128.md)
  * [Proteggere con DRM](protect-with-drm.md)

2. Lettore con un client DRM o AES. Un'app lettore video basata su un SDK per lettori, nativa o basata su browser, deve soddisfare i requisiti seguenti:
  * L'SDK per lettori supporta i client DRM necessari
  * L'SDK per lettori supporta i protocolli di streaming necessari: Smooth, DASH e HLS
  * L'SDK per lettori deve essere in grado di gestire il passaggio di un token JWT nella richiesta di acquisizione della licenza
  
    È possibile creare un lettore usando l'[API di Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). Usare l'[API ProtectionInfo di Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/) per specificare la tecnologia DRM da usare in varie piattaforme DRM.

    Per testare il contenuto crittografato con AES o CENC (Widevine e/o PlayReady), è possibile usare [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html). Assicurarsi di fare clic su "Opzioni avanzate" e controllare le opzioni d crittografia.

    Se si vuole testare il contenuto crittografato di FairPlay, usare [questo lettore di test](http://aka.ms/amtest). Il lettore supporta Widevine, PlayReady e DRM FairPlay oltre alla crittografia AES-128 a chiave non crittografata. È necessario scegliere il browser giusto per testare DRM diversi: Opera/Chrome o Firefox per Widevine, MS Edge/Internet Explorer 11 per PlayReady, Safari su maOS per FairPlay.

3. Secure Token Service (STS) che rilascia JSON Web Token (JWT) come token di accesso per l'accesso alle risorse back-end. È possibile usare i servizi di distribuzione di licenze di AMS come risorsa di back-end. Un STS deve definire gli elementi seguenti:

  * Autorità di certificazione e destinatari o ambito
  * Attestazioni, che dipendono dai requisiti aziendali di protezione del contenuto
  * Verifica simmetrica o asimmetrica per la verifica della firma
  * Supporto del rollover della chiave, se necessario

    È possibile usare [questo strumento STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) per testare STS, che supporta tutti e 3 i tipi di chiave di verifica: simmetrico, asimmetrico o AAD con rollover della chiave. 

> [!NOTE]
> È altamente consigliabile concentrarsi ed eseguire un test completo su ogni parte, come descritto in precedenza, prima di procedere alla parte successiva. Per testare il sistema di "protezione del contenuto", usare gli strumenti specificati nell'elenco precedente.  

## <a name="streaming-protocols-and-encryption-types"></a>Protocolli di streaming e tipi di crittografia

È possibile usare Servizi multimediali per distribuire i contenuti crittografati dinamicamente con chiave non crittografata AES o con crittografia DRM mediante PlayReady, Widevine o FairPlay. Attualmente è possibile crittografare i formati HLS (HTTP Live Streaming), MPEG DASH e Smooth Streaming. Ogni protocollo supporta i metodi di crittografia seguenti:

|Protocollo|Formato del contenitore|Schema di crittografia|
|---|---|---|---|
|MPEG-DASH|Tutti|AES|
||CSF(fmp4) |CENC (Widevine + PlayReady) |
||CMAF(fmp4)|CENC (Widevine + PlayReady)|
|HLS|Tutti|AES|
||MPG2-TS |CBCS (Fairplay) |
||MPG2-TS |CENC (PlayReady) |
||CMAF(fmp4) |CENC (PlayReady) |
|Smooth Streaming|fMP4|AES|
||fMP4 | CENC (PlayReady) |

## <a name="dynamic-encryption"></a>Crittografia dinamica

In Servizi multimediali versione 3 una chiave simmetrica è associata a StreamingLocator: vedere [questo esempio](protect-with-aes128.md). Se si usa il servizio di distribuzione delle chiavi di Servizi multimediali, si deve generare automaticamente la chiave simmetrica. È necessario generare la chiave simmetrica autonomamente se si usa un servizio di distribuzione delle chiavi o se è necessario gestire uno scenario di disponibilità elevata in cui occorre avere la stessa chiave simmetrica in due data center.

Quando un flusso viene richiesto da un lettore, Servizi multimediali usa la chiave specificata per crittografare dinamicamente i contenuti mediante la chiave non crittografata AES o la crittografia DRM. Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi di Servizi multimediali o il servizio di distribuzione delle chiavi specificato. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di chiave simmetrica specificati per tale chiave.

## <a name="aes-128-clear-key-vs-drm"></a>Chiave non crittografata AES-128 e DRM

I clienti spesso si chiedono se devono usare la crittografia AES o un sistema DRM. La differenza principale tra i due sistemi è che con la crittografia AES la chiave simmetrica viene trasmessa al client in un formato non crittografato ("in chiaro"). Di conseguenza, è possibile visualizzare la chiave usata per crittografare i contenuti in una traccia di rete sul client in testo non crittografato. La crittografia con chiave non crittografata AES-128 è adatta nei casi in cui il visualizzatore è attendibile (ad esempio, la crittografia di video aziendali distribuiti all'interno di un'azienda per essere visualizzati dai dipendenti).

PlayReady, Widevine e FairPlay offrono tutti un livello superiore di crittografia rispetto alla crittografia della chiave non crittografata AES-128. La chiave simmetrica viene trasmessa in un formato crittografato. Inoltre, la decrittografia viene gestita in un ambiente sicuro a livello di sistema operativo, più difficilmente attaccabile da un utente malintenzionato. Il DRM è consigliato per i casi d'uso in cui il visualizzatore potrebbe non essere attendibile ed è necessario il massimo livello di sicurezza.

## <a name="storage-side-encryption"></a>Crittografia lato archiviazione

Per proteggere gli asset inattivi, è necessario crittografarli tramite crittografia lato archiviazione. La tabella seguente illustra il funzionamento della crittografia lato archiviazione in Servizi multimediali versione 3:

|Opzione di crittografia|DESCRIZIONE|Servizi multimediali v3|
|---|---|---|---|
|Crittografia di archiviazione di Servizi multimediali| Crittografia AES-256, chiave gestita da Servizi multimediali|Non supportato<sup>(1)</sup>|
|[Crittografia del servizio di archiviazione per dati inattivi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Crittografia lato server offerta da Archiviazione di Azure, chiave gestita da Azure o dal cliente|Supportato|
|[Crittografia lato client di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Crittografia lato client offerta da Archiviazione di Azure, chiave gestita dal cliente in Key Vault|Non supportate|

<sup>1</sup> In Servizi multimediali versione 3 la crittografia di archiviazione (crittografia AES-256) è supportata per la compatibilità con le versioni precedenti solo se gli asset sono stati creati con Servizi multimediali versione 2. In altre parole, la versione 3 funziona con asset con crittografia di archiviazione esistenti, ma non consente la creazione di nuovi asset di questo tipo.

## <a name="licenses-and-keys-delivery-service"></a>Servizio di distribuzione di licenze e chiavi

Servizi multimediali offre un servizio di distribuzione delle chiavi per la distribuzione di licenze DRM (PlayReady, Widevine, FairPlay) e di chiavi AES ai client autorizzati. È possibile usare l'API REST o una libreria client di Servizi multimediali per configurare i criteri di autorizzazione e autenticazione per le licenze e le chiavi.

## <a name="control-content-access"></a>Controllare l'accesso ai contenuti

È possibile controllare chi ha accesso ai propri contenuti configurando i criteri di chiave simmetrica. Servizi multimediali supporta più modalità di autenticazione degli utenti che eseguono richieste di chiavi. È necessario configurare i criteri di chiave simmetrica. Il client (lettore) deve soddisfare i criteri prima che la chiave possa essere distribuita al client stesso. I criteri di chiave simmetrica possono avere restrizioni **open** o **token**. 

Con i criteri di chiave simmetrica con restrizione token, la chiave simmetrica viene inviata solamente a un client che fornisca un token JSON Web (JWT) o un token Web semplice (SWT) valido nella richiesta di licenza/chiave. Questo token deve essere emesso da un servizio token di sicurezza. È possibile usare Azure Active Directory come servizio token di sicurezza oppure distribuire un servizio token di sicurezza personalizzato. Il servizio token di sicurezza deve essere configurato in modo da creare un token firmato con la chiave specificata e rilasciare le attestazioni specificate nella configurazione della restrizione token. Il servizio di distribuzione delle chiavi di Servizi multimediali restituisce al client la chiave o la licenza richiesta se il token è valido e le attestazioni del token corrispondono a quelle configurate per la chiave o la licenza.

Quando si configurano i criteri di restrizione del token, è necessario specificare i parametri primary verification key, issuer e audience. Il parametro primary verification key include la chiave usata per firmare il token. Il parametro issuer è il servizio token di sicurezza che rilascia il token. Il parametro audience (talvolta denominato scope) descrive l'ambito del token o la risorsa a cui il token autorizza l'accesso. Il servizio di distribuzione delle chiavi di Servizi multimediali verifica che i valori nel token corrispondano ai valori nel modello.


## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti:

  * [Proteggere i contenuti con la crittografia AES](protect-with-aes128.md)
  * [Proteggere con DRM](protect-with-drm.md)

Altre informazioni sono reperibili nell'articolo sulla [progettazione e l'implementazione di DRM](../previous/media-services-cenc-with-multidrm-access-control.md)


