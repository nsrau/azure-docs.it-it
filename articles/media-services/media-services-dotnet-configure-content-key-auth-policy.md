<properties 
	pageTitle="Configurare i criteri di autorizzazione della chiave simmetrica mediante l'SDK di Servizi multimediali per .NET" 
	description="Informazioni su come configurare i criteri di autorizzazione per una chiave simmetrica utilizzando .NET SDK di Servizi multimediali." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako,Mingfeiy" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="04/08/2016" 
	ms.author="juliako"/>



#Crittografia dinamica: configurare i criteri di autorizzazione della chiave simmetrica 
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

##Panoramica

Servizi multimediali di Microsoft Azure consente di distribuire flussi MPEG-DASH, Smooth Streaming e HTTP-Live-Streaming (HLS) protetti con Advanced Encryption Standard (AES) con chiavi di crittografia a 128 bit o [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). AMS consente anche di recapitare flussi DASH crittografati con Widevine DRM. PlayReady e Widewine vengono crittografati in base alle specifiche della crittografia comune (ISO/IEC 23001-7 CENC).

Servizi multimediali offre anche un **servizio di distribuzione di chiavi/licenze** dal quale i client possono ottenere chiavi AES o licenze PlayReady/Widevine per riprodurre contenuti crittografati.

Se si desidera crittografare un asset per Servizi multimediali, è necessario associare una chiave di crittografia (**CommonEncryption** o **EnvelopeEncryption**) all'asset (come descritto [qui](media-services-dotnet-create-contentkey.md)) e anche configurare criteri di autorizzazione per la chiave, (come descritto in questo articolo).

Quando un flusso viene richiesto da un lettore, Servizi multimediali usa la chiave specificata per crittografare dinamicamente i contenuti mediante la crittografia DRM o AES. Per decrittografare il flusso, il lettore richiederà la chiave dal servizio di distribuzione delle chiavi. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di autorizzazione specificati.

Servizi multimediali supporta più modalità di autenticazione degli utenti che eseguono richieste di chiavi. I criteri di autorizzazione delle chiavi simmetriche possono avere una o più restrizioni di tipo **open** o **token**. I criteri con restrizione Token devono essere accompagnati da un token rilasciato da un servizio STS (Secure Token Service, servizio token di sicurezza). Servizi multimediali supporta i token nei formati **Simple Web Tokens** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) e **JSON Web Token** ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)).

Servizi multimediali non fornisce servizi token di sicurezza. Per il rilascio di token è possibile creare un servizio token di sicurezza personalizzato oppure usare il Servizio di controllo di accesso di Microsoft Azure. Il servizio token di sicurezza deve essere configurato in modo da creare un token firmato con la chiave specificata e rilasciare le attestazioni specificate nella configurazione della restrizione Token, come descritto in questo articolo. Il servizio di distribuzione delle chiavi di Servizi multimediali restituisce la chiave di crittografia al client se il token è valido e le attestazioni nel token corrispondono a quelle configurate per la chiave simmetrica.

Per altre informazioni, vedere

[Autenticazione dei token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrare l'app basata su OWIN MVC di Servizi multimediali di Azure con Azure Active Directory e limitare la distribuzione di chiavi simmetriche in base ad attestazioni JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Usare il Servizio di controllo di accesso di Azure per il rilascio di token](http://mingfeiy.com/acs-with-key-services).

###Considerazioni applicabili:

- Per usare la funzionalità di creazione dinamica dei pacchetti e la crittografia dinamica, è necessario assicurarsi di avere almeno un'unità riservata di streaming. Per altre informazioni, vedere la sezione relativa al [ridimensionamento di un servizio multimediale](media-services-manage-origins.md#scale_streaming_endpoints). 
- L'asset deve contenere un set di file MP4 o Smooth Streaming a velocità in bit adattiva. Per altre informazioni, vedere l'articolo relativo alla[ codifica di un asset](media-services-encode-asset.md).  
- Caricare e codificare gli asset mediante l'opzione **AssetCreationOptions.StorageEncrypted**.
- Se si prevede di avere più chiavi simmetriche che richiedono una stessa configurazione di criteri, è consigliabile creare un singolo criterio di autorizzazione e applicarlo a più chiavi simmetriche.
- Il servizio di distribuzione delle chiavi memorizza nella cache l'oggetto ContentKeyAuthorizationPolicy e gli oggetti correlati (opzioni e restrizioni) per 15 minuti. Se si crea un oggetto ContentKeyAuthorizationPolicy e si specifica di usare una restrizione Token, quindi si esegue il test della configurazione e si aggiornano i criteri impostando una restrizione Open, il passaggio dei criteri alla versione Open richiede circa 15 minuti.
- Se si aggiungono o si aggiornano i criteri di distribuzione dell'asset, è necessario eliminare l'eventuale localizzatore esistente e creare un nuovo localizzatore.
- Attualmente non è possibile crittografare il formato di streaming HDS o i download progressivi.


##Crittografia dinamica AES-128 

###Restrizione Open

Se si applica una restrizione Open, il sistema distribuirà la chiave a chiunque ne faccia richiesta. Questa restrizione può essere utile a scopo di test.

Il seguente esempio crea un criterio di autorizzazione Open e lo aggiunge alla chiave simmetrica.
	
	static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
	{
	    // Create ContentKeyAuthorizationPolicy with Open restrictions 
	    // and create authorization policy             
	    IContentKeyAuthorizationPolicy policy = _context.
	                            ContentKeyAuthorizationPolicies.
	                            CreateAsync("Open Authorization Policy").Result;
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
	        new List<ContentKeyAuthorizationPolicyRestriction>();
	
	    ContentKeyAuthorizationPolicyRestriction restriction =
	        new ContentKeyAuthorizationPolicyRestriction
	        {
	            Name = "HLS Open Authorization Policy",
	            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
	            Requirements = null // no requirements needed for HLS
	        };
	
	    restrictions.Add(restriction);
	
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create(
	        "policy", 
	        ContentKeyDeliveryType.BaselineHttp, 
	        restrictions, 
	        "");
	
	    policy.Options.Add(policyOption);
	
	    // Add ContentKeyAutorizationPolicy to ContentKey
	    contentKey.AuthorizationPolicyId = policy.Id;
	    IContentKey updatedKey = contentKey.UpdateAsync().Result;
	    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
	}


###Restrizione Token

Questa sezione descrive come creare un criterio di autorizzazione per una chiave simmetrica e associarlo a tale chiave. I criteri di autorizzazione definiscono i requisiti di autorizzazione che devono essere soddisfatti per determinare se l'utente è autorizzato a ricevere la chiave (ad esempio, se l'elenco di chiavi di verifica contiene la chiave con cui è stato firmato il token).

Per configurare l'opzione di restrizione Token, è necessario usare un file XML per descrivere i requisiti di autorizzazione del token. Il file XML di configurazione della restrizione Token deve essere conforme al seguente schema XML.

####<a id="schema"></a>Schema di restrizione Token
	
	<?xml version="1.0" encoding="utf-8"?>
	<xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
	  <xs:complexType name="TokenClaim">
	    <xs:sequence>
	      <xs:element name="ClaimType" nillable="true" type="xs:string" />
	      <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
	  <xs:complexType name="TokenRestrictionTemplate">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
	      <xs:element name="Audience" nillable="true" type="xs:anyURI" />
	      <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
	      <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	      <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
	  <xs:complexType name="ArrayOfTokenVerificationKey">
	    <xs:sequence>
	      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
	  <xs:complexType name="TokenVerificationKey">
	    <xs:sequence />
	  </xs:complexType>
	  <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	  <xs:complexType name="ArrayOfTokenClaim">
	    <xs:sequence>
	      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
	  <xs:complexType name="SymmetricVerificationKey">
	    <xs:complexContent mixed="false">
	      <xs:extension base="tns:TokenVerificationKey">
	        <xs:sequence>
	          <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
	        </xs:sequence>
	      </xs:extension>
	    </xs:complexContent>
	  </xs:complexType>
	  <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
	</xs:schema>

Quando si configurano i criteri di restrizione **Token**, è necessario specificare i parametri **primary verification key**, **issuer** e **audience**. Il parametro **primary verification key** include la chiave usata per firmare il token. Il parametro **issuer** è il servizio token di sicurezza che emette il token. Il parametro **audience** (talvolta denominato **scope**) descrive l'ambito del token o la risorsa a cui il token autorizza l'accesso. Il servizio di distribuzione delle chiavi di Servizi multimediali verifica che i valori nel token corrispondano ai valori nel modello.

Se si usa **Media Services SDK per .NET**, è possibile usare la classe **TokenRestrictionTemplate** per generare il token della restrizione. Il seguente esempio crea un criterio di autorizzazione con una restrizione Token. In questo esempio il client deve presentare un token contenente i seguenti dati: chiave di firma (VerificationKey), autorità emittente del token e attestazioni richieste.
	
	public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
	{
	    string tokenTemplateString = GenerateTokenRequirements();
	
	    IContentKeyAuthorizationPolicy policy = _context.
	                            ContentKeyAuthorizationPolicies.
	                            CreateAsync("HLS token restricted authorization policy").Result;
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
	            new List<ContentKeyAuthorizationPolicyRestriction>();
	
	    ContentKeyAuthorizationPolicyRestriction restriction =
	            new ContentKeyAuthorizationPolicyRestriction
	            {
	                Name = "Token Authorization Policy",
	                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
	                Requirements = tokenTemplateString
	            };
	
	    restrictions.Add(restriction);
	
	    //You could have multiple options 
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create(
	            "Token option for HLS",
	            ContentKeyDeliveryType.BaselineHttp,
	            restrictions,
	            null  // no key delivery data is needed for HLS
	            );
	
	    policy.Options.Add(policyOption);
	
	    // Add ContentKeyAutorizationPolicy to ContentKey
	    contentKey.AuthorizationPolicyId = policy.Id;
	    IContentKey updatedKey = contentKey.UpdateAsync().Result;
	    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
	
	    return tokenTemplateString;
	}
	
	static private string GenerateTokenRequirements()
	{
	    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
	
	    template.PrimaryVerificationKey = new SymmetricVerificationKey();
	    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
	
	    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
	
	    return TokenRestrictionTemplateSerializer.Serialize(template);
	}

####<a id="test"></a>Token di test

Per ottenere un token di test basato sulla restrizione Token usata per i criteri di autorizzazione della chiave, seguire questa procedura.
	
	// Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
	// back into a TokenRestrictionTemplate class instance.
	TokenRestrictionTemplate tokenTemplate =
	    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
	
	// Generate a test token based on the the data in the given TokenRestrictionTemplate.
	// Note, you need to pass the key id Guid because we specified 
	// TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
	Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
	
	//The GenerateTestToken method returns the token without the word “Bearer” in front
	//so you have to add it in front of the token string. 
	string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
	Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
	Console.WriteLine();


##Crittografia dinamica PlayReady 

Servizi multimediali consente di configurare i diritti e le restrizioni che il runtime di PlayReady DRM deve applicare quando l'utente prova a riprodurre contenuti protetti.

Quando si protegge il contenuto con PlayReady, è necessario includere nei criteri di autorizzazione una stringa XML che definisce il [modello di licenza PlayReady](media-services-playready-license-template-overview.md). Per definire più facilmente questo modello, è possibile usare le classi **PlayReadyLicenseResponseTemplate** e **PlayReadyLicenseTemplate** incluse in Media Services SDK per .NET.

[Questo argomento](media-services-protect-with-drm.md) illustra come crittografare i contenuti con **PlayReady** e **Widevine**.

###Restrizione Open
	
Se si applica una restrizione Open, il sistema distribuirà la chiave a chiunque ne faccia richiesta. Questa restrizione può essere utile a scopo di test.

Il seguente esempio crea un criterio di autorizzazione Open e lo aggiunge alla chiave simmetrica.

	static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
	{
	
	    // Create ContentKeyAuthorizationPolicy with Open restrictions 
	    // and create authorization policy          
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
	    {
	        new ContentKeyAuthorizationPolicyRestriction 
	        { 
	            Name = "Open", 
	            KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
	            Requirements = null
	        }
	    };
	
	    // Configure PlayReady license template.
	    string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
	
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create("",
	            ContentKeyDeliveryType.PlayReadyLicense,
	                restrictions, newLicenseTemplate);
	
	    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
	                ContentKeyAuthorizationPolicies.
	                CreateAsync("Deliver Common Content Key with no restrictions").
	                Result;
	
	
	    contentKeyAuthorizationPolicy.Options.Add(policyOption);
	
	    // Associate the content key authorization policy with the content key.
	    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
	    contentKey = contentKey.UpdateAsync().Result;
	}

###Restrizione Token

Per configurare l'opzione di restrizione Token, è necessario usare un file XML per descrivere i requisiti di autorizzazione del token. Il file XML di configurazione della restrizione Token deve essere conforme allo schema XML illustrato in [questa](#schema) sezione.
	
	public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
	{
	    string tokenTemplateString = GenerateTokenRequirements();
	
	    IContentKeyAuthorizationPolicy policy = _context.
	                            ContentKeyAuthorizationPolicies.
	                            CreateAsync("HLS token restricted authorization policy").Result;
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
	    {
	        new ContentKeyAuthorizationPolicyRestriction 
	        { 
	            Name = "Token Authorization Policy", 
	            KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
	            Requirements = tokenTemplateString, 
	        }
	    };
	
	    // Configure PlayReady license template.
	    string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
	
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
	            ContentKeyDeliveryType.PlayReadyLicense,
	                restrictions, newLicenseTemplate);
	
	    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
	                ContentKeyAuthorizationPolicies.
	                CreateAsync("Deliver Common Content Key with no restrictions").
	                Result;
	            
	    policy.Options.Add(policyOption);
	
	    // Add ContentKeyAutorizationPolicy to ContentKey
	    contentKeyAuthorizationPolicy.Options.Add(policyOption);
	
	    // Associate the content key authorization policy with the content key
	    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
	    contentKey = contentKey.UpdateAsync().Result;
	
	    return tokenTemplateString;
	}
	
	static private string GenerateTokenRequirements()
	{
	
	    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
	
	    template.PrimaryVerificationKey = new SymmetricVerificationKey();
	    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
	
	
	    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
	
	    return TokenRestrictionTemplateSerializer.Serialize(template);
	} 
	
    static private string ConfigurePlayReadyLicenseTemplate()
    {
        // The following code configures PlayReady License Template using .NET classes
        // and returns the XML string.

        //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
        //It contains a field for a custom data string between the license server and the application 
        //(may be useful for custom app logic) as well as a list of one or more license templates.
        PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

        // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
        // to be returned to the end users. 
        //It contains the data on the content key in the license and any rights or restrictions to be 
        //enforced by the PlayReady DRM runtime when using the content key.
        PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
        //Configure whether the license is persistent (saved in persistent storage on the client) 
        //or non-persistent (only held in memory while the player is using the license).  
        licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;
       
        // AllowTestDevices controls whether test devices can use the license or not.  
        // If true, the MinimumSecurityLevel property of the license
        // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
        licenseTemplate.AllowTestDevices = true;


        // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
        // It grants the user the ability to playback the content subject to the zero or more restrictions 
        // configured in the license and on the PlayRight itself (for playback specific policy). 
        // Much of the policy on the PlayRight has to do with output restrictions 
        // which control the types of outputs that the content can be played over and 
        // any restrictions that must be put in place when using a given output.
        // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
        //then the DRM runtime will only allow the video to be displayed over digital outputs 
        //(analog video outputs won’t be allowed to pass the content).

        //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
        // If the output protections are configured too restrictive, 
        // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

        // For example:
        //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

        responseTemplate.LicenseTemplates.Add(licenseTemplate);

        return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
    }


Per ottenere un token di test basato sulla restrizione Token usata per i criteri di autorizzazione della chiave, vedere [questa](#test) sezione.

##<a id="types"></a>Tipi usati durante la definizione di ContentKeyAuthorizationPolicy

###<a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }

###<a id="TokenType"></a>TokenType

    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }



##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##Passaggi successivi
Dopo aver configurato i criteri di autorizzazione della chiave simmetrica, passare all'argomento [Come configurare i criteri di distribuzione degli asset](media-services-dotnet-configure-asset-delivery-policy.md).
 

<!---HONumber=AcomDC_0601_2016-->