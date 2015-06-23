<properties 
	pageTitle="Informazioni generali sulla protezione dei contenuti" 
	description="Questi articoli forniscono informazioni generali sulla protezione dei contenuti con Servizi multimediali." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/04/2015" 
	ms.author="juliako"/>

#Informazioni generali sulla protezione dei contenuti

Servizi multimediali di Microsoft Azure consente di distribuire contenuti crittografati dinamicamente con AES (Advanced Encryption Standard), mediante chiavi di crittografia a 128 bit e con PlayReady DRM. Servizi multimediali fornisce inoltre un servizio per la distribuzione di chiavi e licenze PlayReady ai client autorizzati. 

![content-protection][content-protection]

Per poter usare la crittografia dinamica, è necessario innanzitutto ottenere almeno un'unità riservata di streaming nell'endpoint di streaming da cui si desidera trasmettere il contenuto crittografato.

##Concetti

###Crittografia dinamica

Servizi multimediali di Microsoft Azure consente di distribuire contenuti crittografati dinamicamente con AES (Advanced Encryption Standard), mediante chiavi di crittografia a 128 bit e con PlayReady DRM. 

È attualmente possibile crittografare i seguenti formati di streaming: HLS, MPEG DASH e Smooth Streaming. Non è possibile crittografare il formato di streaming HDS o i download progressivi.

Per consentire a Servizi multimediali di crittografare un asset, è necessario associare una chiave di crittografia (CommonEncryption o EnvelopeEncryption) all'asset e configurare anche i criteri di autorizzazione per la chiave.

È anche necessario configurare i criteri di distribuzione degli asset. Se si vuole trasmettere in streaming un asset crittografato di archiviazione, assicurarsi di specificare come si intende recapitarlo configurando i criteri di distribuzione degli asset.  

Quando un flusso viene richiesto da un lettore, Servizi multimediali usa la chiave specificata per crittografare dinamicamente i contenuti mediante AES o PlayReady. Per decrittografare il flusso, il lettore richiederà la chiave dal servizio di distribuzione delle chiavi. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di autorizzazione specificati.

###Servizi per la distribuzione di licenze PlayReady e chiavi non crittografate AES

Servizi multimediali offre un servizio per la distribuzione di licenze PlayReady e chiavi non crittografate AES ai client autorizzati. È possibile usare il portale di gestione di Azure, l'API REST o l'SDK di Servizi multimediali per .NET per configurare i criteri di autorizzazione e autenticazione per le licenze e le chiavi.

Se si usa il portale, sarà possibile configurare un criterio AES, che verrà applicato a tutti i contenuti con crittografia AES, e un criterio PlayReady, che verrà applicato a tutti i contenuti con crittografia PlayReady. Usare l'SDK di Servizi multimediali per .NET se si vuole ottenere maggiore controllo sulle configurazioni.

###Modello di licenza PlayReady

Servizi multimediali offre un servizio per la distribuzione di licenze PlayReady. Quando il lettore dell'utente finale (ad esempio Silverlight) cerca di riprodurre il contenuto protetto con PlayReady, viene inviata una richiesta al servizio di distribuzione di licenze per ottenere una licenza. Se il servizio licenze approva la richiesta, la licenza viene rilasciata e inviata al client e potrà essere usata per decrittografare e riprodurre il contenuto specificato.

Le licenze contengono i diritti e le limitazioni che il runtime di PlayReady DSM imporrà quando un utente cerca di riprodurre contenuto protetto. Servizi multimediali fornisce API che permettono di configurare le licenze PlayReady. Per altre informazioni, vedere [Panoramica del modello di licenza PlayReady di Servizi multimediali](https://msdn.microsoft.com/library/azure/dn783459.aspx)

###Restrizione Token

I criteri di autorizzazione delle chiavi simmetriche possono avere una o più restrizioni di tipo Open, Token o IP. I criteri con restrizione Token devono essere accompagnati da un token rilasciato da un servizio STS (Secure Token Service, servizio token di sicurezza). Servizi multimediali supporta i token nei formati Simple Web Tokens (SWT) e JSON Web Token (JWT). Servizi multimediali non fornisce servizi token di sicurezza. Per il rilascio di token è possibile creare un servizio token di sicurezza personalizzato oppure usare il Servizio di controllo di accesso di Microsoft Azure. Il servizio token di sicurezza deve essere configurato in modo da creare un token firmato con la chiave specificata e rilasciare le attestazioni specificate nella configurazione della restrizione token. Il servizio di distribuzione di chiavi di Servizi multimediali restituirà al client la chiave o la licenza richiesta se il token è valido e le attestazioni del token corrispondono a quelle configurate per la chiave o la licenza.
Quando si configurano i criteri di restrizione Token, è necessario specificare i parametri primary verification key, issuer e audience. Il parametro primary verification key include la chiave usata per firmare il token. Il parametro issuer è il servizio token di sicurezza che emette il token. Il parametro audience (talvolta denominato scope) descrive l'ambito del token o la risorsa a cui il token autorizza l'accesso. Il servizio di distribuzione delle chiavi di Servizi multimediali verifica che i valori nel token corrispondano ai valori nel modello.

##Scenari comuni

###Proteggere i contenuti nella risorsa di archiviazione e distribuire dinamicamente i flussi multimediali crittografati  

1. Inserire un file in formato intermedio di qualità elevata in un asset. Applicare l'opzione di crittografia di archiviazione all'asset.
2. Configurare gli endpoint di streaming
1. Eseguire la codifica in Set MP4 a velocità in bit adattiva. Applicare l'opzione di crittografia di archiviazione all'asset di output.
1. Creare la chiave simmetrica di crittografia per l'asset che si desidera crittografare in modo dinamico durante la riproduzione.
2. Configurare i criteri di autorizzazione della chiave simmetrica.
1. Configurare il criterio di invio degli asset (usato per la creazione dinamica dei pacchetti e per la crittografia dinamica).
1. Pubblicare l'asset creando un localizzatore OnDemand.
1. Trasmettere in streaming i contenuti pubblicati.

###Usare il servizio di distribuzione di licenze e chiavi di Servizi multimediali con i propri servizi di crittografia e streaming

Per altre informazioni, vedere l'articolo relativo all'[integrazione del servizio licenze Azure PlayReady con il server del componente di crittografia/streaming](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server).

##Attività comuni di crittografia dei contenuti

>[AZURE.NOTE]Se i contenuti sono protetti con crittografia di archiviazione, assicurarsi di configurare i criteri di distribuzione degli asset.

###Configurazione degli endpoint di streaming

Per informazioni generali sugli endpoint di streaming e sulla relativa gestione, vedere [Come gestire gli endpoint di streaming in un account di Servizi multimediali](media-services-manage-origins.md).

###Creazione della chiave simmetrica

Creare una chiave simmetrica con cui crittografare l'asset usando **.NET** o l'**API REST**.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../../includes/media-services-selector-create-contentkey.md)]

###Configurazione dei criteri di autorizzazione della chiave simmetrica 

Configurare la protezione dei contenuti e i criteri di autorizzazione della chiave simmetrica usando **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

###Configurare i criteri di distribuzione degli asset

Configurare i criteri di distribuzione degli asset usando **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]


##Collegamenti correlati

[Annuncio di PlayReady come servizio e della crittografia dinamica AES con Servizi multimediali di Azure](http://mingfeiy.com/playready)

[Prezzi della distribuzione della licenza PlayReady di Servizi multimediali di Azure](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Debug del flusso con crittografia AES in Servizi multimediali di Azure](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[Autenticazione dei token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrazione dell'app basata su OWIN MVC di Servizi multimediali di Azure con Azure Active Directory e limitazione della distribuzione di chiavi simmetriche in base ad attestazioni JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[Uso del Servizio di controllo di accesso di Azure per il rilascio di token](http://mingfeiy.com/acs-with-key-services).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png


<!--HONumber=52--> 