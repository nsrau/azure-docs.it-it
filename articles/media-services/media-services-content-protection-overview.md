<properties 
	pageTitle="Informazioni generali sulla protezione dei contenuti | Microsoft Azure" 
	description="Questi articoli forniscono informazioni generali sulla protezione dei contenuti con Servizi multimediali." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>  

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/27/2016" 
	ms.author="juliako"/>  

#Informazioni generali sulla protezione dei contenuti


Servizi multimediali di Microsoft Azure consente di proteggere i file multimediali dal momento in cui escono dal computer fino alle fasi di archiviazione, elaborazione e recapito. Servizi multimediali consente di distribuire contenuti live e on demand crittografati dinamicamente con AES (Advanced Encryption Standard), mediante chiavi di crittografia a 128 bit, o con una delle principali soluzioni DRM: Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati.

L'immagine seguente illustra i flussi di lavoro di protezione del contenuto supportati da AMS.

![Protezione con PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)  

>[AZURE.NOTE]Per poter usare la crittografia dinamica, è prima necessario ottenere almeno un'unità riservata di streaming nell'endpoint di streaming da cui si desidera trasmettere in streaming i contenuti crittografati.

Questo argomento illustra [concetti e terminologia](media-services-content-protection-overview.md) importanti per comprendere la protezione del contenuto con AMS. Contiene anche [collegamenti](media-services-content-protection-overview.md#common-scenarios) ad argomenti che illustrano come eseguire attività di protezione del contenuto.

##Crittografia dinamica

Servizi multimediali di Microsoft Azure consente di distribuire contenuti crittografati dinamicamente con chiave non crittografata AES (Advanced Encryption Standard) o crittografia DRM: PlayReady, Google Widevine e Apple FairPlay.

Attualmente è possibile crittografare i formati di streaming seguenti: HLS, MPEG DASH e Smooth Streaming. Non è possibile crittografare il formato di streaming HDS o i download progressivi.

Per consentire a Servizi multimediali di crittografare un asset, è necessario associare una chiave di crittografia (CommonEncryption o EnvelopeEncryption) all'asset e configurare anche i criteri di autorizzazione per la chiave.

È anche necessario configurare i criteri di distribuzione degli asset. Se si vuole trasmettere in streaming un asset crittografato di archiviazione, assicurarsi di specificare come si intende recapitarlo configurando i criteri di distribuzione degli asset.

Quando un flusso viene richiesto da un lettore, Servizi multimediali usa la chiave specificata per crittografare dinamicamente i contenuti mediante la chiave non crittografata AES o la crittografia DRM. Per decrittografare il flusso, il lettore richiederà la chiave dal servizio di distribuzione delle chiavi. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di autorizzazione specificati.

>[AZURE.NOTE]Per avvalersi della crittografia dinamica, è necessario ottenere almeno un'unità di streaming su richiesta per l'endpoint di streaming da cui si intende distribuire i contenuti crittografati. Per altre informazioni, vedere la sezione relativa al [ridimensionamento di Servizi multimediali](media-services-portal-manage-streaming-endpoints.md).

##Crittografia di archiviazione

Usare la crittografia di archiviazione per crittografare il contenuto non crittografato localmente tramite crittografia AES a 256 bit, quindi caricarlo nel servizio Archiviazione di Azure dove viene archiviato in forma crittografata. Gli asset protetti con la crittografia di archiviazione vengono decrittografati automaticamente e inseriti in un file system crittografato prima della codifica. Se necessario, inoltre, possono essere ricrittografati prima del successivo caricamento come nuovo asset di output. La crittografia di archiviazione viene usata principalmente per proteggere file multimediali di input di alta qualità archiviati su disco applicando una crittografia avanzata.

Per poter trasmettere l'asset crittografato di archiviazione, è necessario configurare i criteri di distribuzione dell'asset in modo da informare Servizi multimediali della modalità di distribuzione del contenuto. Per potere permettere lo streaming dell'asset, il server di streaming rimuove la crittografia di archiviazione ed esegue lo streaming dei contenuti usando i criteri di recapito specificati (ad esempio, AES, crittografia comune o nessuna crittografia).

###Dettagli dell'implementazione 

La crittografia di archiviazione AMS applica la crittografia in modalità **AES-CTR** all'intero file. La modalità CTR-AES è una crittografia a blocchi in grado di crittografare dati di lunghezza arbitraria senza bisogno di spaziatura interna. Funziona mediante la crittografia di un blocco di contatori con l'algoritmo AES e l'applicazione di XOR sull'output di AES con i dati da crittografare o decrittografare. Il blocco di contatori usato viene creato copiando il valore di InitializationVector nei byte da 0 a 7 del valore del contatore, mentre i byte da 8 a 15 del valore del contatore vengono impostati su zero. Nel blocco del contatore a 16 byte, i byte da 8 a 15, vale a dire i byte meno significativi, vengono usati come semplice numero intero a 64 bit senza firma che viene aumentato di uno per ogni blocco di dati elaborato successivo e viene mantenuto nell'ordine dei byte di rete. Si noti che quando il numero intero raggiunge il valore massimo 0xFFFFFFFFFFFFFFFF, un incremento azzera i byte da 8 a 15 del contatore del blocco, senza influenzare gli altri 64 bit del contatore, vale a dire i byte da 0 a 7. Per mantenere la sicurezza della crittografia in modalità AES-CTR, il valore InitializationVector di un KID specificato deve essere univoco per ogni file e i file devono essere di lunghezza inferiore a 2^64 blocchi. Questo metodo serve a garantire che un valore del contatore non venga mai riutilizzato con una chiave specificata. Per altre informazioni sulla modalità CTR, vedere questa [pagina wiki](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR). L'articolo wiki usa il termine "Nonce" anziché "InitializationVector".

Per vedere come funziona l'algoritmo di base, vedere l'implementazione di AMS .NET dei metodi seguenti:

- [ApplyEncryptionTransform](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.BlobTransfer/BlobTransferBase.cs)
- [AesCtr](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/FileEncryptionTransform.cs)


## Crittografia comune (CENC)

La crittografia comune viene usata per crittografare il contenuto con PlayReady e/o Widewine.

## Uso della crittografia cbcs-aapl

La crittografia cbcs-aapl viene usata per crittografare il contenuto con FairPlay.

## Crittografia envelope 

Usare questa opzione per proteggere i contenuti con chiave non crittografata AES-128. Se si vuole un'opzione più sicura, scegliere una delle soluzioni DRM elencate in questo argomento.

##Servizio di distribuzione di licenze e chiavi

Servizi multimediali offre un servizio per la distribuzione di licenze DRM (PlayReady, Widevine, FairPlay) e di chiavi non crittografate AES ai client autorizzati. È possibile usare il [portale di Azure](media-services-portal-protect-content.md), l'API REST o Media Services SDK per .NET per configurare i criteri di autorizzazione e autenticazione per le licenze e le chiavi.

##Restrizione Token

I criteri di autorizzazione delle chiavi simmetriche possono avere una o più restrizioni di tipo Open o Token. I criteri con restrizione Token devono essere accompagnati da un token rilasciato da un servizio STS (Secure Token Service, servizio token di sicurezza). Servizi multimediali supporta i token nei formati Simple Web Tokens (SWT) e JSON Web Token (JWT). Servizi multimediali non fornisce servizi token di sicurezza. Per il rilascio di token è possibile creare un servizio token di sicurezza personalizzato oppure usare il Servizio di controllo di accesso di Microsoft Azure. Il servizio token di sicurezza deve essere configurato in modo da creare un token firmato con la chiave specificata e rilasciare le attestazioni specificate nella configurazione della restrizione token. Il servizio di distribuzione di chiavi di Servizi multimediali restituirà al client la chiave o la licenza richiesta se il token è valido e le attestazioni del token corrispondono a quelle configurate per la chiave o la licenza.

Quando si configurano i criteri di restrizione Token, è necessario specificare i parametri primary verification key, issuer e audience. Il parametro primary verification key include la chiave usata per firmare il token. Il parametro issuer è il servizio token di sicurezza che emette il token. Il parametro audience (talvolta denominato scope) descrive l'ambito del token o la risorsa a cui il token autorizza l'accesso. Il servizio di distribuzione delle chiavi di Servizi multimediali verifica che i valori nel token corrispondano ai valori nel modello.

##URL di streaming

Se l'asset è stato crittografato con più soluzioni DRM, è necessario usare un tag di crittografia nell'URL di streaming (format='m3u8-aapl', encryption='xxx').

Si applicano le considerazioni seguenti:

- Può essere specificato solo un tipo di crittografia oppure nessuno.
- Il tipo di crittografia non deve essere specificato nell'URL se all'asset è stata applicata una sola crittografia.
- Il tipo di crittografia non fa distinzione tra maiuscole e minuscole.
- Possono essere specificati i seguenti tipi di crittografia:
	- **cenc**: crittografia comune (Playready o Widevine)
	- **cbcs-aapl**: Fairplay
	- **cbc**: crittografia AES envelope.

##Scenari comuni

Gli argomenti seguenti descrivono come proteggere i contenuti nella memoria, distribuire i flussi multimediali crittografati dinamicamente, usare il servizio di distribuzione di licenze e chiavi di AMS

- [Proteggere i contenuti con AES](media-services-protect-with-aes128.md)
- [Proteggere i contenuti con PlayReady e/o Widevine](media-services-protect-with-drm.md)
- [Trasmettere i contenuti HLS in modo protetto con Apple FairPlay e/o PlayReady](media-services-protect-hls-with-fairplay.md)

### Scenari aggiuntivi

- [How to integrate Azure PlayReady License service with your own encryptor/streaming server](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server) (Come integrare il servizio licenze Azure PlayReady con il server del componente di crittografia/streaming).
- [Uso di castLabs per distribuire licenze DRM a Servizi multimediali di Azure](media-services-castlabs-integration.md)
 
##Percorsi di apprendimento di Servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Collegamenti correlati

[Annuncio di PlayReady come servizio e della crittografia dinamica AES con Servizi multimediali di Azure](http://mingfeiy.com/playready)

[Prezzi della distribuzione della licenza PlayReady di Servizi multimediali di Azure](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Debug del flusso con crittografia AES in Servizi multimediali di Azure](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[Autenticazione dei token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrare l'app basata su OWIN MVC di Servizi multimediali di Azure con Azure Active Directory e limitare la distribuzione di chiavi simmetriche in base ad attestazioni JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Usare il Servizio di controllo di accesso di Azure per il rilascio di token](http://mingfeiy.com/acs-with-key-services).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png

<!---HONumber=AcomDC_0928_2016-->