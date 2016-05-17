<properties 
	pageTitle="Configurare i criteri di autorizzazione della chiave simmetrica mediante il portale" 
	description="Informazioni su come configurare i criteri di autorizzazione per una chiave simmetrica." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="05/03/2016"   
	ms.author="juliako"/>



#Configurare i criteri di autorizzazione della chiave simmetrica 
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]


##Panoramica

Servizi multimediali di Microsoft Azure consente di distribuire flussi MPEG-DASH, Smooth Streaming e HTTP-Live-Streaming (HLS) protetti con Advanced Encryption Standard (AES) con chiavi di crittografia a 128 bit o [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). AMS consente anche di recapitare flussi DASH crittografati con Widevine DRM. PlayReady e Widewine vengono crittografati in base alle specifiche della crittografia comune (ISO/IEC 23001-7 CENC).

Servizi multimediali offre anche un **servizio di distribuzione di chiavi/licenze** dal quale i client possono ottenere chiavi AES o licenze PlayReady/Widevine per riprodurre contenuti crittografati.

Questo argomento illustra come usare il **portale di Azure classico** per configurare i criteri di autorizzazione della chiave simmetrica. La chiave può essere usata in un secondo momento per crittografare i contenuti dinamicamente. Si noti che attualmente è possibile crittografare i formati di streaming seguenti: HLS, MPEG DASH e Smooth Streaming. Non è possibile crittografare il formato di streaming HDS o i download progressivi.
 
Quando un lettore richiede un flusso impostato per la crittografia dinamica, Servizi multimediali usa la chiave configurata per crittografare dinamicamente i contenuti con la crittografia DRM o AES. Per decrittografare il flusso, il lettore richiederà la chiave dal servizio di distribuzione delle chiavi. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di autorizzazione specificati.


Se si prevede di usare più chiavi di contenuti o si desidera specificare un URL di **servizio di distribuzione di chiavi/licenze** diverso dal servizio di distribuzione delle chiavi di Servizi multimediali, usare Media Services SDK per .NET o le API REST.

[Configurare i criteri di autorizzazione della chiave simmetrica mediante Media Services .NET SDK](media-services-dotnet-configure-content-key-auth-policy.md)

[Configurare i criteri di autorizzazione della chiave simmetrica mediante l'API REST di Servizi multimediali](media-services-rest-configure-content-key-auth-policy.md)

###Considerazioni applicabili:

- Per usare la funzionalità di creazione dinamica dei pacchetti e la crittografia dinamica, è necessario assicurarsi di avere almeno un'unità riservata di streaming. Per altre informazioni, vedere la sezione relativa al [ridimensionamento di un servizio multimediale](media-services-manage-origins.md#scale_streaming_endpoints). 
- L'asset deve contenere un set di file MP4 o Smooth Streaming a velocità in bit adattiva. Per altre informazioni, vedere l'articolo relativo alla[ codifica di un asset](media-services-encode-asset.md).  
- Il servizio di distribuzione delle chiavi memorizza nella cache l'oggetto ContentKeyAuthorizationPolicy e gli oggetti correlati (opzioni e restrizioni) per 15 minuti. Se si crea un oggetto ContentKeyAuthorizationPolicy e si specifica di usare una restrizione Token, quindi si esegue il test della configurazione e si aggiornano i criteri impostando una restrizione Open, il passaggio dei criteri alla versione Open richiede circa 15 minuti.


##Procedura: Configurare i criteri di autorizzazione della chiave simmetrica

Per configurare i criteri di autorizzazione della chiave simmetrica, selezionare la pagina **PROTEZIONE DEL CONTENUTO**.
	
Servizi multimediali supporta più modalità di autenticazione degli utenti che eseguono richieste di chiavi. I criteri di autorizzazione della chiave simmetrica possono avere restrizioni di autorizzazione di tipo **open**, **token** o **IP**. (La restrizione **IP** può essere configurata con REST o con .NET SDK.)

###Restrizione Open

Se si applica una restrizione **open**, il sistema distribuirà la chiave a chiunque ne faccia richiesta. Questa restrizione può essere utile a scopo di test.

![OpenPolicy][open_policy]

###Restrizione Token

Per scegliere il criterio con restrizione token, premere il pulsante **TOKEN**.

I criteri con restrizione **Token** devono essere accompagnati da un token rilasciato da un **servizio STS** (Secure Token Service, servizio token di sicurezza). Servizi multimediali supporta i token nei formati **Simple Web Token** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) e **JSON Web Token** (JWT). Per informazioni, vedere [Autenticazione dei token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Servizi multimediali non fornisce **servizi token di sicurezza**. Per il rilascio di token è possibile creare un servizio token di sicurezza personalizzato oppure usare il Servizio di controllo di accesso di Microsoft Azure. Il servizio token di sicurezza deve essere configurato in modo da creare un token firmato con la chiave specificata e rilasciare le attestazioni specificate nella configurazione della restrizione token. Il servizio di distribuzione delle chiavi di Servizi multimediali restituisce la chiave di crittografia al client se il token è valido e le attestazioni nel token corrispondono a quelle configurate per la chiave simmetrica. Per altre informazioni, vedere l'articolo relativo all'[uso di Servizio di controllo di accesso di Azure per il rilascio di token](http://mingfeiy.com/acs-with-key-services).

Quando si configurano i criteri di restrizione **TOKEN**, è necessario specificare i parametri **verification key**, **issuer** e **audience**. Il parametro primary verification key include la chiave usata per firmare il token. Il parametro issuer è il servizio token di sicurezza che emette il token. Il parametro audience (talvolta denominato scope) descrive l'ambito del token o la risorsa a cui il token autorizza l'accesso. Il servizio di distribuzione delle chiavi di Servizi multimediali verifica che i valori nel token corrispondano ai valori nel modello.

###PlayReady

Quando si protegge il contenuto con **PlayReady**, è necessario includere nei criteri di autorizzazione una stringa XML che definisce il modello di licenza PlayReady. Per impostazione predefinita, vengono definiti i seguenti criteri:
		
	<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
	  <LicenseTemplates>
	    <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
	      <ContentKey i:type="ContentEncryptionKeyFromHeader" />
	      <LicenseType>Nonpersistent</LicenseType>
	      <PlayRight>
	        <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
	      </PlayRight>
	    </PlayReadyLicenseTemplate>
	  </LicenseTemplates>
	</PlayReadyLicenseResponseTemplate>

È possibile fare clic sul pulsante **importa xml criterio** e specificare codice XML differente, conforme allo schema XML definito [qui](https://msdn.microsoft.com/library/azure/dn783459.aspx).


##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Passaggi successivi
Dopo la configurazione dei criteri di autorizzazione della chiave, passare all'argomento [Procedura: Usare il portale di Azure classico per abilitare la crittografia](media-services-manage-content.md#encrypt).


[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

 

<!---HONumber=AcomDC_0504_2016-->