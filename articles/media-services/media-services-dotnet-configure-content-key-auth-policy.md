---
title: Configurare un criterio di autorizzazione chiave del contenuto con Media Services .NET SDK | Documenti Microsoft
description: Informazioni su come configurare un criterio di autorizzazione per una chiave simmetrica mediante Media Services .NET SDK.
services: media-services
documentationcenter: 
author: mingfeiy
manager: cfowler
editor: 
ms.assetid: 1a0aedda-5b87-4436-8193-09fc2f14310c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;mingfeiy
ms.openlocfilehash: 39782bd687c9c1b50699c05e61e57d9c767a8d32
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="dynamic-encryption-configure-a-content-key-authorization-policy"></a>Crittografia dinamica: configurare un criterio di autorizzazione chiave del contenuto
[!INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Panoramica
 È possibile utilizzare servizi multimediali di Azure per distribuire i flussi MPEG-DASH, Smooth Streaming e HTTP Live Streaming (HLS) protetti con Advanced Encryption Standard (AES) con chiavi di crittografia a 128 bit o [PlayReady gestione dei diritti digitali (DRM) ](https://www.microsoft.com/playready/overview/). Con servizi multimediali, è anche possibile recapitare i flussi DASH crittografati con DRM Widevine. PlayReady sia Widevine vengono crittografati in base alla specifica di crittografia (ISO/IEC 23001-7 CENC) comuni.

Servizi multimediali fornisce anche un servizio di recapito chiave/licenza da cui i client possono ottenere le chiavi AES o PlayReady/Widevine licenze per riprodurre il contenuto crittografato.

Se si desidera servizi multimediali di crittografare un asset, è necessario associare una chiave di crittografia (CommonEncryption o EnvelopeEncryption) con la risorsa. Per ulteriori informazioni, vedere [creare entità Contentkey con .NET](media-services-dotnet-create-contentkey.md). È inoltre necessario configurare i criteri di autorizzazione per la chiave (come descritto in questo articolo).

Quando un flusso è richiesto da un lettore, servizi multimediali Usa la chiave specificata per crittografare dinamicamente il contenuto mediante la crittografia AES o DRM. Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi. Per determinare se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di autorizzazione per la chiave specificata.

Servizi multimediali supporta più modalità di autenticazione degli utenti che eseguono richieste di chiavi. I criteri di autorizzazione chiave del contenuto possono avere una o più restrizioni di autorizzazione. Le opzioni sono restrizione token o aperti. I criteri di token con restrizioni devono essere accompagnato da un token rilasciato da un servizio token di sicurezza (STS). Servizi multimediali supporta i token nel token web semplice ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) formato e il Token Web JSON ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)) formato.

Servizi multimediali non fornisce servizio token di sicurezza. È possibile creare un servizio token di sicurezza personalizzato o utilizzare Azure Access Control Service al rilascio di token. Il servizio token di sicurezza deve essere configurato in modo da creare un token firmato con la chiave specificata e rilasciare le attestazioni specificate nella configurazione della restrizione Token, come descritto in questo articolo. Se il token è valido e le attestazioni nel token corrispondono a quelli configurati per la chiave simmetrica, il servizio di distribuzione delle chiavi di servizi multimediali restituisce la chiave di crittografia al client.

Per altre informazioni, vedere gli articoli seguenti:

- [Autenticazione tramite token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Integrare l'app basata su MVC OWIN servizi multimediali di Azure con Azure Active Directory e limitare il recapito del contenuto di chiave in base alle attestazioni JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>Le considerazioni
* Quando viene creato l'account di servizi multimediali, endpoint di streaming predefinito viene aggiunto al tuo account nello stato "Stopped". Per avviare lo streaming del contenuto e sfruttare i vantaggi di creazione dinamica dei pacchetti e la crittografia dinamica, l'endpoint di streaming deve essere nello stato "Running". 
* L'asset deve contenere un set di file MP4 o Smooth Streaming a velocità in bit adattiva. Per altre informazioni, vedere l'articolo relativo alla [codifica di un asset](media-services-encode-asset.md).
* Caricare e codificare gli asset con l'opzione AssetCreationOptions.StorageEncrypted.
* Se si prevede di avere più chiavi simmetriche che richiedono la stessa configurazione di criteri, è consigliabile creare un singolo criterio di autorizzazione e applicarlo a più chiavi simmetriche.
* Il servizio di distribuzione delle chiavi memorizza nella cache ContentKeyAuthorizationPolicy e gli oggetti correlati (opzioni di criteri e restrizioni) per 15 minuti. È possibile creare ContentKeyAuthorizationPolicy e specificare se usare una restrizione token, testarlo e quindi aggiornare i criteri di restrizione open. Questo processo richiede circa 15 minuti prima le opzioni di criteri per la versione aperta del criterio.
* Se si aggiungono o si aggiornano i criteri di distribuzione dell'asset, è necessario eliminare qualsiasi indicatore di posizione esistente e crearne uno nuovo.
* Attualmente, non è possibile crittografare il download progressivo.
* Un endpoint di streaming di servizi multimediali imposta il valore dell'intestazione CORS 'Access-Control-Allow-Origin' nella risposta preliminare come il carattere jolly '\*'. Questo valore funziona bene con la maggior parte dei lettori, tra cui Azure Media Player, Roku e JWPlayer e altri. Tuttavia, alcuni lettori che utilizzano dashjs non funzionano perché, con la modalità di credenziali impostata su "include", in loro dashjs XMLHttpRequest non consente il carattere jolly "\*" come valore di 'Access-Control-Allow-Origin'. Come soluzione alternativa per questo limite di dashjs, se si ospita il client da un singolo dominio, servizi multimediali può specificare tale dominio nell'intestazione della risposta preliminare. Per assistenza, aprire un ticket di supporto tramite il portale di Azure.

## <a name="aes-128-dynamic-encryption"></a>Crittografia dinamica AES-128
### <a name="open-restriction"></a>Restrizione Open
Se si applica una restrizione open, il sistema distribuisce la chiave a chiunque ne faccia richiesta. Questa restrizione può essere utile a scopo di test.

Nell'esempio seguente viene creato un criterio open authorization e lo aggiunge alla chiave simmetrica:

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


### <a name="token-restriction"></a>Restrizione Token
Questa sezione descrive come creare un criterio di autorizzazione per una chiave simmetrica e associarlo a tale chiave. I criteri di autorizzazione vengono descritti i requisiti di autorizzazione devono essere soddisfatte per determinare se l'utente è autorizzato a ricevere la chiave. Ad esempio, l'elenco di chiavi di verifica contiene la chiave di cui è stato firmato il token con?

Per configurare l'opzione di limitazione del token, è necessario usare un file XML per descrivere i requisiti di autorizzazione del token. Il file XML di configurazione della restrizione Token deve essere conforme allo schema XML seguente:

#### <a name="token-restriction-schema"></a>Schema di restrizione Token
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

Quando si configura il criterio con restrizioni token, è necessario specificare la chiave di verifica primaria, l'autorità emittente e parametri pubblico. La chiave di verifica primaria contiene il token è stato firmato con la chiave. L'autorità emittente è il servizio token di sicurezza che emette il token. Il parametro audience (talvolta denominato scope) descrive l'ambito del token o la risorsa a cui il token autorizza l'accesso. Il servizio di distribuzione delle chiavi di Servizi multimediali verifica che i valori nel token corrispondano ai valori nel modello.

Quando si utilizza Media Services SDK per .NET, è possibile utilizzare la classe TokenRestrictionTemplate per generare il token della restrizione.
Il seguente esempio crea un criterio di autorizzazione con una restrizione Token. In questo esempio, il client deve presentare un token che contiene una chiave di firma (VerificationKey), autorità emittente del token e attestazioni necessarie.

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

#### <a name="test-token"></a>Token di test
Per ottenere un token di test basato sulla restrizione token utilizzato per i criteri di autorizzazione della chiave, eseguire le operazioni seguenti:

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


## <a name="playready-dynamic-encryption"></a>Crittografia dinamica PlayReady
Servizi multimediali consente di configurare i diritti e le restrizioni che si desidera che il runtime di PlayReady DRM imporrà quando un utente tenta di riprodurre contenuto protetto. 

Quando si proteggono i contenuti con PlayReady, una delle operazioni è necessario specificare nei criteri di autorizzazione è una stringa XML che definisce il [modello di licenza PlayReady](media-services-playready-license-template-overview.md). In Media Services SDK per .NET, le classi PlayReadyLicenseResponseTemplate e PlayReadyLicenseTemplate consentono di definire il modello di licenza PlayReady.

Per informazioni su come crittografare i contenuti con PlayReady e Widevine, vedere [usare PlayReady e/o Widevine crittografia comune dinamica](media-services-protect-with-playready-widevine.md).

### <a name="open-restriction"></a>Restrizione Open
Se si applica una restrizione open, il sistema distribuisce la chiave a chiunque ne faccia richiesta. Questa restrizione può essere utile a scopo di test.

Nell'esempio seguente viene creato un criterio open authorization e lo aggiunge alla chiave simmetrica:

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

### <a name="token-restriction"></a>Restrizione Token
Per configurare l'opzione di limitazione del token, è necessario usare un file XML per descrivere i requisiti di autorizzazione del token. Configurazione della restrizione token XML deve essere conforme allo schema XML illustrato nel "[schema restrizione Token](#token-restriction-schema)" sezione.

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
        // It grants the user the ability to play back the content subject to the zero or more restrictions 
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


Per ottenere un token di test basato sulla restrizione token utilizzato per i criteri di autorizzazione della chiave, vedere la "[Test token](#test-token)" sezione. 

## <a id="types"></a>Tipi utilizzati quando si definisce ContentKeyAuthorizationPolicy
### <a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType
    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }

### <a id="TokenType"></a>TokenType
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passaggi successivi
Ora che sono stati configurati criteri di autorizzazione della chiave simmetrica, vedere [configurare un criterio di recapito di asset](media-services-dotnet-configure-asset-delivery-policy.md).

