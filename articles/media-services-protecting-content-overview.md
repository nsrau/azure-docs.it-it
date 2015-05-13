<properties 
	pageTitle="Protezione del contenuto" 
	description="Questo argomento fornisce informazioni generali su come proteggere il contenuto con Servizi multimediali." 
	authors="Juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/15/2015" 
	ms.author="juliako"/>


#Protezione del contenuto

##Panoramica

Servizi multimediali di Azure consente di proteggere i file multimediali dal momento in cui escono dal computer fino alle fasi di archiviazione, elaborazione e recapito. Quando si usa Servizi multimediali, si eseguono in genere i seguenti passaggi:

###Protezione dei contenuti nella risorsa di archiviazione e distribuzione di flussi multimediali con crittografia dinamica.  

Per poter usare la crittografia dinamica, è prima necessario ottenere almeno un'unità riservata di streaming nell'endpoint di streaming da cui si desidera trasmettere in streaming i contenuti crittografati.

1. Caricare un file in formato intermedio di qualità elevata in un asset. Applicare all'asset l'opzione di crittografia di archiviazione.
1. Eseguire la codifica in Set MP4 a velocità in bit adattiva. Applicare all'asset di output l'opzione di crittografia di archiviazione.
1. Creare una chiave di crittografia simmetrica per l'asset a cui si desidera applicare la crittografia dinamica durante la riproduzione.
2. Configurare i criteri di autorizzazione della chiave simmetrica.
1. Configurare i criteri di distribuzione degli asset \(usati per la creazione dinamica dei pacchetti e la crittografia dinamica\).
1. Pubblicare l'asset creando un localizzatore OnDemand.
1. Trasmettere in streaming i contenuti pubblicati.  

Questo argomento offre una panoramica dei [concetti](media-services-protecting-content-overview.md#concepts) principali e fornisce collegamenti ad altri argomenti che illustrano procedure per eseguire [attività](media-services-protecting-content-overview.md#tasks) relative alla distribuzione di contenuti.

##<a id="concepts"></a>Concetti

###Opzioni di crittografia di asset

A seconda del tipo di contenuto che si vuole caricare, archiviare e distribuire, in Servizi multimediali è possibile scegliere tra varie opzioni di crittografia.

**None**: non viene usata alcuna crittografia. Si tratta del valore predefinito. Quando si usa questa opzione, il contenuto non è protetto durante il transito, né nell'archiviazione locale.

Se si prevede di distribuire un file MP4 tramite download progressivo, usare questa opzione per caricare il contenuto.

**StorageEncrypted**: usare questa opzione per crittografare localmente il contenuto non crittografato applicando la crittografia AES a 256 bit e quindi caricarlo nel servizio Archiviazione di Azure, dove viene archiviato in forma crittografata. Gli asset protetti con la crittografia di archiviazione vengono decrittografati automaticamente e inseriti in un file system crittografato prima della codifica. Se necessario, inoltre, possono essere ricrittografati prima del successivo caricamento come nuovo asset di output. La crittografia di archiviazione viene usata principalmente per proteggere file multimediali di input di alta qualità archiviati su disco applicando una crittografia avanzata.

Per poter trasmettere l'asset crittografato di archiviazione, è necessario configurare i criteri di distribuzione dell'asset in modo da informare Servizi multimediali della modalità di distribuzione del contenuto. Per potere permettere lo streaming dell'asset, il server di streaming rimuove la crittografia di archiviazione ed esegue lo streaming dei contenuti usando i criteri di recapito specificati \(ad esempio, AES, PlayReady o nessuna crittografia\).

**CommonEncryptionProtected**: usare questa opzione se si vuole crittografare o caricare contenuto già crittografato e protetto con la crittografia comune o con DRM PlayReady, ad esempio Smooth Streaming protetto con DRM PlayReady.

**EnvelopeEncryptionProtected**: usare questa opzione se si vuole proteggere o caricare contenuto HLS \(HTTP Live Streaming\) già protetto, crittografato con AES. Si noti che se si sta caricando contenuto HLS già crittografato con AES, la crittografia deve essere stata eseguita con Transform Manager.

###Crittografia dinamica

Servizi multimediali di Microsoft Azure consente di distribuire contenuti crittografati dinamicamente con AES \(Advanced Encryption Standard\), mediante chiavi di crittografia a 128 bit e con PlayReady DRM.

Attualmente è possibile crittografare i formati di streaming seguenti: HLS, MPEG DASH e Smooth Streaming. Non è possibile crittografare il formato di streaming HDS o i download progressivi.

Per consentire a Servizi multimediali di crittografare un asset, è necessario associare una chiave di crittografia \(CommonEncryption o EnvelopeEncryption\) all'asset e configurare anche i criteri di autorizzazione per la chiave.

È anche necessario configurare i criteri di distribuzione degli asset. Se si vuole trasmettere in streaming un asset crittografato di archiviazione, assicurarsi di specificare come si intende recapitarlo configurando i criteri di distribuzione degli asset.

Quando un flusso viene richiesto da un lettore, Servizi multimediali usa la chiave specificata per crittografare dinamicamente i contenuti mediante AES o PlayReady. Per decrittografare il flusso, il lettore richiederà la chiave dal servizio di distribuzione delle chiavi. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di autorizzazione specificati.

>[AZURE.NOTE]Per avvalersi della crittografia dinamica, è necessario ottenere almeno un'unità di streaming su richiesta per l'endpoint di streaming da cui si intende distribuire i contenuti crittografati. Per altre informazioni, vedere la sezione relativa al [ridimensionamento di Servizi multimediali](media-services-manage-origins.md#scale_streaming_endpoints).

###Servizi per la distribuzione di licenze PlayReady e chiavi non crittografate AES

Servizi multimediali offre un servizio per la distribuzione di licenze PlayReady e chiavi non crittografate AES ai client autorizzati. È possibile usare il portale di gestione di Azure, l'API REST o l'SDK di Servizi multimediali per .NET per configurare i criteri di autorizzazione e autenticazione per le licenze e le chiavi.

Se si usa il portale, sarà possibile configurare un criterio AES, che verrà applicato a tutti i contenuti con crittografia AES, e un criterio PlayReady, che verrà applicato a tutti i contenuti con crittografia PlayReady. Usare l'SDK di Servizi multimediali per .NET se si vuole ottenere maggiore controllo sulle configurazioni.

###Modello di licenza PlayReady

Servizi multimediali offre un servizio per la distribuzione di licenze PlayReady. Quando il lettore dell'utente finale \(ad esempio Silverlight\) cerca di riprodurre il contenuto protetto con PlayReady, viene inviata una richiesta al servizio di distribuzione di licenze per ottenere una licenza. Se il servizio licenze approva la richiesta, la licenza viene rilasciata e inviata al client e potrà essere usata per decrittografare e riprodurre il contenuto specificato.

Le licenze contengono i diritti e le limitazioni che il runtime di PlayReady DSM imporrà quando un utente cerca di riprodurre contenuto protetto. Servizi multimediali fornisce API che permettono di configurare le licenze PlayReady. Per altre informazioni, vedere [Panoramica del modello di licenza PlayReady di Servizi multimediali](https://msdn.microsoft.com/library/azure/dn783459.aspx).

###Restrizione Token

I criteri di autorizzazione delle chiavi simmetriche possono avere una o più restrizioni di tipo Open, Token o IP. I criteri con restrizione Token devono essere accompagnati da un token rilasciato da un servizio STS \(Secure Token Service, servizio token di sicurezza\). Servizi multimediali supporta i token nei formati Simple Web Tokens \(SWT\) e JSON Web Token \(JWT\). Servizi multimediali non fornisce servizi token di sicurezza. Per il rilascio di token è possibile creare un servizio token di sicurezza personalizzato oppure usare il Servizio di controllo di accesso di Microsoft Azure. Il servizio token di sicurezza deve essere configurato in modo da creare un token firmato con la chiave specificata e rilasciare le attestazioni specificate nella configurazione della restrizione token. Il servizio di distribuzione di chiavi di Servizi multimediali restituirà al client la chiave o la licenza richiesta se il token è valido e le attestazioni del token corrispondono a quelle configurate per la chiave o la licenza.

Quando si configurano i criteri di restrizione Token, è necessario specificare i parametri primary verification key, issuer e audience. Il parametro primary verification key include la chiave usata per firmare il token. Il parametro issuer è il servizio token di sicurezza che emette il token. Il parametro audience \(talvolta denominato scope\) descrive l'ambito del token o la risorsa a cui il token autorizza l'accesso. Il servizio di distribuzione delle chiavi di Servizi multimediali verifica che i valori nel token corrispondano ai valori nel modello.

##<a id="tasks"></a>Attività correlate

###Configurazione degli endpoint di streaming

Per informazioni generali sugli endpoint di streaming e su come gestirli, vedere [Come gestire gli endpoint di streaming in un account di Servizi multimediali](media-services-manage-origins.md).

###Caricamento di file multimediali 

Caricare i file usando il **portale di gestione di Azure**, **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-upload-files](../includes/media-services-selector-upload-files.md)]

###Codifica di asset

Codificare con **Azure Media Encoder** tramite il **portale di gestione di Azure**, **.NET** o **API REST**.
 
[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

###Creazione di una chiave simmetrica

Creare una chiave simmetrica da usare per crittografare l'asset mediante **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../includes/media-services-selector-create-contentkey.md)]

###Configurazione dei criteri di autorizzazione della chiave simmetrica 

Configurare la protezione dei contenuti e i criteri di autorizzazione della chiave simmetrica mediante **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

###Configurazione dei criteri di distribuzione degli asset

Configurare i criteri di distribuzione degli asset usando **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]


<!--HONumber=52-->
