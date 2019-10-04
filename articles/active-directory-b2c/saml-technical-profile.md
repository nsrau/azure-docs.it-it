---
title: Definire un profilo tecnico SAML nei criteri personalizzati in Azure Active Directory B2C | Microsoft Docs
description: Definire un profilo tecnico SAML nei criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/21/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 38215ef49bdc5788e43e4ea0fedef2efd32d8213
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063793"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definire un profilo tecnico SAML nei criteri personalizzati di Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornisce il supporto per il provider di identità SAML 2,0. Questo articolo descrive le specifiche di un profilo tecnico per l'interazione con un provider di attestazioni che supporta questo protocollo standardizzato. Con un profilo tecnico SAML è possibile attuare la federazione con un provider di identità basato su SAML, ad esempio [AD FS](active-directory-b2c-custom-setup-adfs2016-idp.md) e [Salesforce](active-directory-b2c-setup-sf-app-custom.md). Questa federazione consente agli utenti di accedere con le proprie identità aziendali o di social network.

## <a name="metadata-exchange"></a>Scambio di metadati

I metadati sono informazioni usate nella specifica del protocollo SAML per esporre la configurazione di un'entità SAML, ad esempio un provider di identità o di servizi. I metadati definiscono il percorso dei servizi, ad esempio accesso e disconnessione, certificati, metodo di accesso e altro ancora. Il provider di identità usa i metadati per conoscere le modalità di comunicazione con Azure AD B2C. I metadati sono configurati in formato XML e possono essere firmati digitalmente in modo che l'altra entità possa convalidarne l'integrità. Quando Azure AD B2C attua la federazione con un provider di identità SAML, agisce come un provider di servizi avviando una richiesta SAML e rimanendo in attesa di una risposta SAML. In alcuni casi accetta l'autenticazione SAML non richiesta, condizione detta anche "avviata dal provider di identità".

I metadati possono essere configurati in entrambe le entità come "metadati statici" o "metadati dinamici". In modalità statica i metadati vengono interamente copiati da un'entità e impostati nell'altra. In modalità dinamica l'URL viene impostato sui metadati mentre l'altra entità legge la configurazione in modo dinamico. I principi sono uguali e prevedono l'impostazione dei metadati del profilo tecnico di Azure AD B2C nel provider di identità e dei metadati del provider di identità in Azure AD B2C.

Ogni provider di identità SAML prevede passaggi diversi per esporre e impostare il provider di servizi, in questo caso Azure AD B2C, e impostare i metadati di Azure AD B2C nel provider di identità. Esaminare la documentazione del provider di identità per ottenere indicazioni su come eseguire questa operazione.

L'esempio seguente mostra un indirizzo URL che punta ai metadati SAML di un profilo tecnico di Azure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Sostituire i valori seguenti:

- **your-tenant-name** con il nome del tenant, ad esempio fabrikam.b2clogin.com.
- **your-policy** con il nome dei criteri. Usare i criteri in cui si configura il profilo tecnico del provider SAML o i criteri che ereditano da quei criteri.
- **your-technical-profile** con il nome del profilo tecnico del provider di identità SAML.

## <a name="digital-signing-certificates-exchange"></a>Scambio di certificati di firma digitale

Per creare una relazione di trust tra Azure AD B2C e il provider di identità SAML, è necessario specificare un certificato X509 valido con la chiave privata. Caricare il certificato con la chiave privata (file con estensione pfx) nell'archivio di chiavi dei criteri di Azure AD B2C. Azure AD B2C firma digitalmente la richiesta di accesso SAML con il certificato caricato.

Il certificato viene usato nei modi seguenti:

- Azure AD B2C genera e firma una richiesta SAML, usando la chiave privata di Azure AD B2C del certificato. La richiesta SAML viene inviata al provider di identità, che convalida la richiesta usando la chiave pubblica di Azure AD B2C del certificato. Il certificato pubblico di Azure AD B2C è accessibile tramite i metadati del profilo tecnico. In alternativa, è possibile caricare manualmente il file con estensione cer nel provider di identità SAML.
- Il provider di identità consente di firmare i dati inviati ad Azure AD B2C con la chiave privata del provider di identità del certificato. Azure AD B2C convalida i dati tramite il certificato pubblico del provider di identità. Ogni provider di identità prevede passaggi diversi per l'installazione. Esaminare la documentazione del provider di identità per ottenere indicazioni su come eseguire questa operazione. In Azure AD B2C i criteri devono disporre dell'accesso alla chiave pubblica del certificato usando i metadati del provider di identità.

Un certificato autofirmato è accettabile per la maggior parte degli scenari. Per gli ambienti di produzione, è consigliabile usare un certificato X509 emesso da un'autorità di certificazione. Come descritto più avanti in questo documento, per un ambiente non di produzione è anche possibile disabilitare la firma SAML per entrambe le entità.

Il diagramma seguente illustra lo scambio tra i metadati e il certificato:

![Scambio tra metadati e certificati](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)


## <a name="digital-encryption"></a>Crittografia digitale

Per crittografare l'asserzione di risposta SAML, il provider di identità usa sempre una chiave pubblica d un certificato di crittografia in un profilo tecnico di Azure AD B2C. Quando Azure AD B2C deve decrittografare i dati, usa la parte privata del certificato di crittografia.

Per crittografare l'asserzione di risposta SAML:

1. Caricare un certificato X509 valido con la chiave privata (file con estensione pfx) nell'archivio dei chiavi dei criteri di Azure AD B2C.
2. Aggiungere un elemento **CryptographicKey** con un identificatore di `SamlAssertionDecryption` al profilo tecnico della raccolta **CryptographicKeys**. Impostare l'elemento **StorageReferenceId** sul nome della chiave dei criteri creata nel passaggio 1.
3. Impostare i metadati del profilo tecnico **WantsEncryptedAssertions** su `true`.
4. Aggiornare il provider di identità con i nuovi metadati del profilo tecnico di Azure AD B2C. Viene visualizzato l'elemento **KeyDescriptor** con la proprietà **use** impostata su `encryption` contenente la chiave pubblica del certificato.

L'esempio seguente illustra la sezione di crittografia del profilo tecnico di Azure AD B2C dei metadati:

```XML
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="protocol"></a>Protocol

L'attributo **Name** dell'elemento Protocol deve essere impostato su `SAML2`.

## <a name="output-claims"></a>Attestazioni di output

L'elemento **OutputClaims** contiene un elenco di attestazioni restituite dal provider di identità SAML nella sezione `AttributeStatement`. Può essere necessario eseguire il mapping del nome dell'attestazione definito nei criteri per il nome definito nel provider di identità. È anche possibile includere le attestazioni che non vengono restituite dal provider di identità, purché sia impostato l'attributo `DefaultValue`.

Per leggere l'asserzione SAML **NamedId** in **Subject** come attestazione normalizzata, impostare l'attestazione **PartnerClaimType** su `assertionSubjectName`. Assicurarsi che **NameId** sia il primo valore nell'asserzione XML. Quando si definisce più di un'asserzione, Azure Active Directory B2C sceglie il valore del soggetto dall'ultima asserzione.

L'elemento **OutputClaimsTransformations** può contenere una raccolta di elementi **OutputClaimsTransformation** che vengono usati per modificare le attestazioni di output o per generarne di nuove.

L'esempio seguente illustra le attestazioni restituite dal provider di identità Facebook:

- Viene eseguito il mapping dell'attestazione **issuerUserId** all'attestazione **assertionSubjectName** .
- Il mapping dell'attestazione **first_name** viene eseguito per l'attestazione **givenName**.
- Il mapping dell'attestazione **last_name** viene eseguito per l'attestazione **surname**.
- L'attestazione **displayName** senza eseguire il mapping del nome.
- L'attestazione **email** senza eseguire il mapping del nome.

Il profilo tecnico restituisce anche le attestazioni che non vengono restituite dal provider di identità:

- L'attestazione **identityProvider** che contiene il nome del provider di identità.
- L'attestazione **authenticationSource** con un valore predefinito di **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email"  />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Attributo | Obbligatorio | Descrizione |
| --------- | -------- | ----------- |
| PartnerEntity | Yes | URL dei metadati del provider di identità SAML. Copiare i metadati del provider di identità e aggiungerli nell'elemento CDATA `<![CDATA[Your IDP metadata]]>` |
| WantsSignedRequests | No | Indica se il profilo tecnico richiede che tutte le richieste di autenticazione in uscita siano firmate. I valori possibili sono: `true` o `false`. Il valore predefinito è `true`. Quando il valore è impostato su `true`, la chiave di crittografia **SamlMessageSigning** deve essere specificata e tutte le richieste di autenticazione in uscita sono firmate. Se il valore è impostato su `false`, i parametri **SigAlg** e **Signature** (stringa di query o parametro Post) vengono omessi dalla richiesta. Questi metadati controllano anche l'attributo **AuthnRequestsSigned**, emesso nei metadati del profilo tecnico di Azure AD B2C condiviso con il provider di identità. Azure ad B2C non firma la richiesta se il valore di **WantsSignedRequests** nei metadati del profilo tecnico è impostato su `false` e i metadati del provider di identità **WantAuthnRequestsSigned** sono `false` impostati su o non specificati. |
| XmlSignatureAlgorithm | No | Metodo usato da Azure AD B2C per firmare la richiesta SAML. Questi metadati controllano il valore del parametro **SigAlg** (stringa di query o parametro Post) nella richiesta SAML. I valori possibili sono: `Sha256`, `Sha384`, `Sha512` o `Sha1`. Verificare di configurare l'algoritmo di firma per entrambe le parti con lo stesso valore. Usare solo l'algoritmo supportato dal certificato. |
| WantsSignedAssertions | No | Indica se il profilo tecnico richiede che tutte le asserzioni in ingresso siano firmate. I valori possibili sono: `true` o `false`. Il valore predefinito è `true`. Se il valore è impostato su `true`, tutta la sezione `saml:Assertion` delle asserzioni inviate dal provider di identità del provider ad Azure AD B2C deve essere firmata. Se il valore è impostato su `false`, il provider di identità non deve firmare le asserzioni e anche se esegue questa operazione Azure AD B2C non convalida la firma. Questi metadati controllano anche il flag **WantsAssertionsSigned** emesso nei metadati del profilo tecnico di Azure AD B2C condiviso con il provider di identità. Se si disabilita la convalida delle asserzioni, è anche possibile disabilitare la convalida della firma della risposta. Per altre informazioni, vedere **ResponsesSigned**. |
| ResponsesSigned | No | I valori possibili sono: `true` o `false`. Il valore predefinito è `true`. Se il valore è impostato su `false`, il provider di identità non deve firmare la risposta SAML e anche se esegue questa operazione Azure AD B2C non convalida la firma. Se il valore è impostato su `true`, la risposta SAML inviata dal provider di identità ad Azure AD B2C è firmata e deve essere convalidata. Se si disabilita la convalida della risposta SAML, è anche possibile disabilitare la convalida della firma di asserzione. Per altre informazioni, vedere **WantsSignedAssertions**. |
| WantsEncryptedAssertions | No | Indica se il profilo tecnico richiede che tutte le asserzioni in ingresso siano crittografate. I valori possibili sono: `true` o `false`. Il valore predefinito è `false`. Se il valore è impostato su `true`, le asserzioni inviate dal provider di identità ad Azure AD B2C devono essere firmate e la chiave di crittografia **SamlAssertionDecryption** deve essere specificata. Se il valore è impostato su `true`, i metadati del profilo tecnico di Azure AD B2C includono la sezione **encryption**. Il provider di identità legge i metadati e crittografa l'asserzione di risposta SAML con la chiave pubblica contenuta nei metadati del profilo tecnico di Azure AD B2C. Se si abilita la crittografia delle asserzioni, può anche essere necessario disabilitare la convalida della firma della risposta. Per altre informazioni, vedere **ResponsesSigned**. |
| IdpInitiatedProfileEnabled | No | Indica se è abilitato un profilo di sessione Single Sign-On avviato da un profilo del provider di identità SAML. I valori possibili sono: `true` o `false`. Il valore predefinito è `false`. Nel flusso di avviato dal provider di identità l'utente viene autenticato esternamente e viene inviata una risposta non richiesta ad Azure AD B2C, che usa il token, esegue i passaggi di orchestrazione e quindi invia una risposta all'applicazione relying party. |
| NameIdPolicyFormat | No | Specifica i vincoli sull'identificatore del nome da usare per rappresentare il soggetto richiesto. Se omesso, è possibile usare qualsiasi tipo di identificatore supportato dal provider di identità per il soggetto richiesto. Ad esempio, `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. **NameIdPolicyFormat** può essere usato con **NameIdPolicyAllowCreate**. Esaminare la documentazione del provider di identità per materiale sussidiario sui criteri di ID nome supportati. |
| NameIdPolicyAllowCreate | No | Quando si usa **NameIdPolicyFormat**, è anche possibile specificare la proprietà `AllowCreate` di **NameIDPolicy**. Il valore di questi metadati è `true` o `false` per indicare se il provider di identità è autorizzato a creare un nuovo account durante il flusso di accesso. Esaminare la documentazione del provider di identità per ottenere indicazioni su come eseguire questa operazione. |
| AuthenticationRequestExtensions | No | Elementi di estensione facoltativi del messaggio del protocollo concordati tra Azure AD B2C e il provider di identità. L'estensione viene presentata in formato XML. I dati XML si aggiungono all'interno dell'elemento CDATA `<![CDATA[Your IDP metadata]]>`. Controllare la documentazione del provider di identità per vedere se l'elemento di estensione è supportato. |
| IncludeAuthnContextClassReferences | No | Specifica uno o più riferimenti URI che identificano le classi contesto di autenticazione. Ad esempio, per consentire a un utente di accedere solo con nome utente e password, impostare il valore su `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`. Per consentire l'accesso tramite nome utente e password in una sessione protetta (SSL/TLS), specificare `PasswordProtectedTransport`. Esaminare la documentazione del provider di identità per materiale sussidiario sugli URI **AuthnContextClassRef** supportati. |
| IncludeKeyInfo | No | Indica se la richiesta di autenticazione SAML contiene la chiave pubblica del certificato quando l'associazione è impostata su `HTTP-POST`. I valori possibili sono: `true` o `false`. |

## <a name="cryptographic-keys"></a>Chiavi crittografiche

L'elemento **CryptographicKeys** contiene gli attributi seguenti:

| Attributo |Obbligatorio | Descrizione |
| --------- | ----------- | ----------- |
| SamlMessageSigning |Sì | Certificato X509 (set di chiavi RSA) da usare per firmare i messaggi SAML. Azure AD B2C usa questa chiave per firmare le richieste e inviarle al provider di identità. |
| SamlAssertionDecryption |Sì | Certificato X509 (set di chiavi RSA) da usare per decrittografare i messaggi SAML. Questo certificato deve essere fornito dal provider di identità. Azure AD B2C usa questo certificato per decrittografare i dati inviati dal provider di identità. |
| MetadataSigning |No | Certificato X509 (set di chiavi RSA) da usare per firmare i metadati SAML. Azure AD B2C usa questa chiave per firmare i metadati.  |

## <a name="examples"></a>Esempi

- [Aggiungere AD FS come provider di identità SAML tramite criteri personalizzati](active-directory-b2c-custom-setup-adfs2016-idp.md)
- [Eseguire l'accesso con account Salesforce tramite SAML](active-directory-b2c-setup-sf-app-custom.md)













