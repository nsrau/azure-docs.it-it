---
title: Configurare Azure AD B2C come IdP SAML per le applicazioni
title-suffix: Azure AD B2C
description: Come configurare Azure AD B2C per fornire asserzioni del protocollo SAML alle applicazioni (provider di servizi). Azure AD B2C fungerà da provider di identità (IdP) singolo per l'applicazione SAML.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 80ecd02f9aebbca66169d64d6c6d0302d58ca439
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647665"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Registrare un'applicazione SAML in Azure AD B2C

Questo articolo illustra come configurare Azure Active Directory B2C (Azure AD B2C) perché funga da provider di identità (IdP) SAML (Security Assertion Markup Language) per le applicazioni.

## <a name="scenario-overview"></a>Panoramica dello scenario

Le organizzazioni che usano Azure AD B2C come soluzione di gestione delle identità e degli accessi dei clienti possono richiedere l'interazione con i provider di identità o con le applicazioni configurate per l'autenticazione tramite il protocollo SAML.

Azure AD B2C ottiene l'interoperabilità con SAML in uno dei due modi seguenti:

* Fungendo da *provider di identità* (IdP) e ottenendo l'accesso SSO (Single Sign-On) con i provider di servizi basati su SAML (le applicazioni)
* Fungendo da *provider di servizi* (SP) e interagendo con provider di identità basati su SAML come Salesforce e ADFS

![Diagramma con B2C come provider di identità a sinistra e B2C come provider di servizi a destra](media/saml-identity-provider/saml-idp-diagram-01.jpg)

Riepilogo dei due scenari di base non esclusivi con SAML:

| Scenario | Ruolo di Azure AD B2C | Procedure |
| -------- | ----------------- | ------- |
| L'applicazione prevede che un'asserzione SAML completi un'autenticazione. | **Azure AD B2C funge da provider di identità (IdP)**<br />Azure AD B2C funge da IdP SAML per le applicazioni. | Questo articolo. |
| Gli utenti hanno bisogno dell'accesso Single Sign-On con un provider di identità conforme a SAML, ad esempio ADFS, Salesforce o Shibboleth.  | **Azure AD B2C funge da provider di servizi (SP)**<br />Azure AD B2C funge da provider di servizi per la connessione al provider di identità SAML. Si tratta di un proxy federativo tra l'applicazione e il provider di identità SAML.  | <ul><li>[Configurare l'accesso con ADFS come IdP SAML usando criteri personalizzati](identity-provider-adfs2016-custom.md)</li><li>[Configurare l'accesso con un provider SAML Salesforce usando criteri personalizzati](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>Prerequisiti

* Completare la procedura descritta in [Introduzione ai criteri personalizzati in Azure AD B2C](custom-policy-get-started.md). Per i criteri personalizzati descritti nell'articolo, sono necessari i criteri personalizzati *SocialAndLocalAccounts* del pacchetto Starter.
* Una conoscenza di base del protocollo SAML (Security Assertion Markup Language).
* Un'applicazione Web configurata come provider di servizi (SP) SAML. Per questa esercitazione, è possibile usare un'[applicazione di test SAML][samltest] fornita.

## <a name="components-of-the-solution"></a>Componenti della soluzione

Per questo scenario sono necessari tre componenti principali:

* Un **provider di servizi** SAML in grado di inviare richieste SAML e di ricevere, decodificare e rispondere alle asserzioni SAML di Azure AD B2C. Il provider di servizi è noto anche come applicazione relying party.
* Un **endpoint di metadati** SAML disponibile pubblicamente per il provider di servizi.
* [Tenant di Azure AD B2C](tutorial-create-tenant.md)

In assenza di un provider di servizi SAML e di un endpoint di metadati associato, è possibile usare questa applicazione SAML di esempio resa disponibile per i test:

[Applicazione di test SAML][samltest]

## <a name="1-set-up-certificates"></a>1. Configurare i certificati

Per creare una relazione di trust tra il provider di servizi e Azure AD B2C, è necessario fornire i certificati X509 dell'app Web.

* **Certificati del provider di servizi**
  * Certificato con una chiave privata archiviata nell'app Web. Questo certificato viene usato dal provider di servizi per firmare la richiesta SAML inviata ad Azure AD B2C. Azure AD B2C legge la chiave pubblica dai metadati del provider di servizi per convalidare la firma.
  * (Facoltativo) Certificato con una chiave privata archiviata nell'app Web. Azure AD B2C legge la chiave pubblica dai metadati del provider di servizi per crittografare l'asserzione SAML. Il provider di servizi usa quindi la chiave privata per decrittografare l'asserzione.
* **Certificati di Azure AD B2C**
  * Certificato con una chiave privata in Azure AD B2C. Questo certificato viene usato da Azure AD B2C per firmare la risposta SAML inviata al provider di servizi. Il provider di servizi legge la chiave pubblica dei metadati di Azure AD B2C per convalidare la firma della risposta SAML.

È possibile usare un certificato emesso da un'autorità di certificazione pubblica oppure, per questa esercitazione, un certificato autofirmato.

### <a name="11-prepare-a-self-signed-certificate"></a>1.1 Preparare un certificato autofirmato

Se non si ha già un certificato, per questa esercitazione è possibile usarne uno autofirmato. In Windows è possibile usare il cmdlet [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) di PowerShell per generare un certificato.

1. Eseguire questo comando di PowerShell per generare un certificato autofirmato. Modificare l'argomento `-Subject` in base al nome dell'applicazione e del tenant Azure AD B2C. Si può anche modificare la data `-NotAfter` per specificare una scadenza diversa per il certificato.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. Aprire **Gestire i certificati utente** > **Utente corrente** > **Personale** > **Certificati** > *nomeapp.nometenant.onmicrosoft.com*
1. Selezionare il certificato > **Azione** > **Tutte le attività** > **Esporta**
1. Selezionare **Sì** > **Avanti** > **Sì, esporta la chiave privata** > **Avanti**
1. Accettare le impostazioni predefinite di **Formato file di esportazione**
1. Specificare una password per il certificato

### <a name="12-upload-the-certificate"></a>1.2 Caricare il certificato

Successivamente, caricare l'asserzione SAML e il certificato di firma della risposta in Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com) e passare al tenant di Azure AD B2C.
1. In **Criteri** selezionare **Identity Experience Framework** e quindi **Chiavi dei criteri**.
1. Selezionare **Aggiungi** e quindi **Opzioni** > **Carica**.
1. Immettere un valore per **Nome**, ad esempio *SamlIdpCert*. Verrà automaticamente aggiunto il prefisso *B2C_1A_* al nome della chiave.
1. Caricare il certificato usando il controllo di caricamento file.
1. Immettere la password del certificato.
1. Selezionare **Create** (Crea).
1. Verificare che la chiave venga visualizzata come previsto. Ad esempio, *B2C_1A_SamlIdpCert*.

## <a name="2-prepare-your-policy"></a>2. Preparare i criteri

### <a name="21-create-the-saml-token-issuer"></a>2.1 Creare un'autorità emittente di token SAML

A questo punto, aggiungere nel tenant la funzionalità per emettere token SAML, usando i profili tecnici [autorità emittente del token SAML](saml-issuer-technical-profile.md) e [provider di sessioni SAML](custom-policy-reference-sso.md#samlssosessionprovider).

Aprire `SocialAndLocalAccounts\`**`TrustFrameworkExtensions.xml`** nel pacchetto Starter di criteri personalizzati.

Individuare la sezione `<ClaimsProviders>` e aggiungere il frammento XML seguente.

È possibile cambiare il valore dei metadati `IssuerUri`. Si tratta dell'URI dell'autorità emittente restituito nella risposta SAML da Azure AD B2C. L'applicazione relying party deve essere configurata in modo da accettare un URI dell'autorità emittente durante la convalida dell'asserzione SAML.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <!-- The issuer contains the policy name; it should be the same name as configured in the relying party application. B2C_1A_signup_signin_SAML is used below. -->
        <!--<Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml</Item>-->
      </Metadata>
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. Aggiungere i criteri della relying party SAML

Ora che il tenant può emettere asserzioni SAML, è necessario creare i criteri della relying party SAML e modificare il percorso utente in modo che emetta un'asserzione SAML invece di un token JWT.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 Creare i criteri di iscrizione o di accesso

1. Creare una copia del file *SignUpOrSignin.xml* nella directory di lavoro del pacchetto Starter e salvarla con un nuovo nome. Ad esempio, *SignUpOrSigninSAML.xml*. Questo è il file di criteri della relying party.

1. Aprire il file *SignUpOrSigninSAML.xml* in un editor a scelta.

1. Cambiare i valori `PolicyId` e `PublicPolicyUri` dei criteri in _B2C_1A_signup_signin_saml_ e `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml`, come illustrato di seguito.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Aggiungere il frammento XML seguente prima dell'elemento `<RelyingParty>`. Questo codice XML sovrascrive il passaggio di orchestrazione numero 7 del percorso utente _SignUpOrSignIn_. Se il percorso utente è stato avviato da una cartella diversa del pacchetto Starter o è stato personalizzato con l'aggiunta o la rimozione di passaggi di orchestrazione, assicurarsi che il numero nell'elemento `order` sia allineato a quello specificato nel percorso utente per il passaggio dell'autorità emittente del token (ad esempio, nelle altre cartelle del pacchetto Starter si tratta del numero 4 per `LocalAccounts`, del numero 6 per `SocialAccounts` e del numero 9 per `SocialAndLocalAccountsWithMfa`).

    ```xml
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. Sostituire l'intero elemento `<TechnicalProfile>` nell'elemento `<RelyingParty>` con il seguente codice XML del profilo tecnico.

    ```xml
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
    ```

1. Aggiornare `tenant-name` con il nome del tenant di Azure AD B2C.

Il file dei criteri di relying party finale dovrebbe essere simile al codice XML seguente:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

> [!NOTE]
> Quando si implementano altri tipi di flussi utente, ad esempio l'accesso, la reimpostazione della password o la modifica del profilo, il processo è essenzialmente lo stesso descritto in questa sezione. Nel passaggio 4 precedente verrà modificato l'ultimo passaggio del percorso utente da `JWTIssuer` a `Saml2AssertionIssuer` . Nel passaggio 6 precedente, nella sezione relying party, si modificherà il **protocollo** da `OpenIdConnect` a `SAML2` .

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 Caricare e testare i metadati dei criteri

Salvare le modifiche e caricare il nuovo file dei criteri. Dopo aver caricato entrambi i criteri (i file dell'estensione e della relying party), aprire un Web browser e passare ai metadati dei criteri.

I metadati IDP dei criteri di Azure AD B2C sono informazioni usate nel protocollo SAML per esporre la configurazione di un provider di identità SAML. I metadati definiscono il percorso dei servizi, ad esempio accesso e disconnessione, certificati, metodo di accesso e altro ancora. I metadati dei criteri di Azure AD B2C sono disponibili all'URL seguente. Sostituire `tenant-name` con il nome del tenant Azure ad B2C e `policy-name` con il nome (ID) del criterio, ad esempio.../B2C_1A_signup_signin_saml/samlp/Metadata:

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

I criteri personalizzati e il tenant di Azure AD B2C sono ora pronti. Successivamente, creare una registrazione dell'applicazione in Azure AD B2C.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. Configurare l'applicazione nella directory di Azure AD B2C

### <a name="41-register-your-application-in-azure-ad-b2c"></a>4,1 registrare l'applicazione in Azure AD B2C

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Registrazioni app** e quindi **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *SAMLApp1*.
1. In **Tipi di account supportati** selezionare **Account solo in questa directory organizzativa**
1. In **URI di reindirizzamento** selezionare **Web** e quindi immettere `https://localhost`. Questo valore verrà modificato in un secondo momento nel manifesto della registrazione dell'applicazione.
1. Selezionare **Registra**.

### <a name="42-update-the-app-manifest"></a>4.2 Aggiornare il manifesto dell'app

Per le app SAML, è necessario configurare diverse proprietà nel manifesto della registrazione dell'applicazione.

1. Nel [portale di Azure](https://portal.azure.com) passare alla registrazione dell'applicazione creata nella sezione precedente.
1. In **Gestisci** selezionare **Manifesto** per aprire l'editor manifesto. Nelle sezioni seguenti vengono modificate diverse proprietà.

#### <a name="identifieruris"></a>identifierUris

`identifierUris` è una raccolta di stringhe contenente uno o più URI definiti dall'utente che identificano in modo univoco un'app Web all'interno del tenant di Azure AD B2C. L'URI deve corrispondere al nome della richiesta SAML `Issuer` . L'URI definito dall'utente è in genere lo stesso valore dei metadati del provider di servizi `entityID` .

#### <a name="samlmetadataurl"></a>samlMetadataUrl

Questa proprietà rappresenta l'URL dei metadati del provider di servizi disponibili pubblicamente. L'URL dei metadati può puntare a un file di metadati caricato in qualsiasi endpoint accessibile in modalità anonima, ad esempio archiviazione BLOB.

I metadati sono informazioni usate nel protocollo SAML per esporre la configurazione di un'entità SAML, ad esempio un provider di servizi. I metadati definiscono la posizione dei servizi, come accesso e disconnessione, certificati, metodo di accesso e altro ancora. Azure AD B2C legge i metadati del provider di servizi e agisce di conseguenza. I metadati non sono obbligatori. È anche possibile specificare alcuni attributi, ad esempio l'URI di risposta e l'URI di disconnessione, direttamente nel manifesto dell'app.

Se sono specificate *sia* nell'URL dei metadati SAML che nel manifesto della registrazione dell'applicazione, le proprietà vengono **unite**. Le proprietà specificate nell'URL dei metadati vengono elaborate per prime e hanno la precedenza.

Per questa esercitazione, che usa l'applicazione di test SAML, usare il valore seguente per `samlMetadataUrl`:

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType (facoltativo)

Se non si specifica un URI di metadati, è possibile specificare esplicitamente l'URL di risposta. Questa proprietà facoltativa rappresenta `AssertionConsumerServiceUrl` (URL `SingleSignOnService` nei metadati del provider di servizi) e si presuppone che `BindingType` sia `HTTP POST`.

Se si sceglie di configurare l'URL di risposta e l'URL di disconnessione nel manifesto dell'applicazione senza usare i metadati del provider di servizi, Azure AD B2C non convaliderà la firma della richiesta SAML né crittograferà la risposta SAML.

Per questa esercitazione, in cui si usa l'applicazione di test SAML, impostare la proprietà `url` di `replyUrlsWithType` sul valore mostrato nel frammento di codice JSON seguente.

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl (facoltativo)

Questa proprietà facoltativa rappresenta l'URL `Logout` (URL `SingleLogoutService` nei metadati della relying party) e si presuppone che `BindingType` sia `Http-Redirect`.

Per questa esercitazione, che usa l'applicazione di test SAML, lasciare il valore di `https://samltestapp2.azurewebsites.net/logout` impostato su `logoutUrl`:

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. Aggiornare il codice dell'applicazione

L'ultimo passaggio consiste nell'abilitare Azure AD B2C come IdP SAML nell'applicazione relying party SAML. Ogni applicazione è diversa e i passaggi per eseguire questa operazione variano. Per informazioni dettagliate, vedere la documentazione dell'app.

I metadati possono essere configurati nel provider di servizi come "metadati statici" o "metadati dinamici". In modalità statica è possibile copiare tutti i metadati o parte di essi dai metadati dei criteri Azure AD B2C. In modalità dinamica è possibile impostare l'URL sui metadati e consentire all'applicazione di leggere i metadati in modo dinamico.

In genere sono necessari alcuni o tutti gli elementi seguenti:

* **Metadati**: `https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **Emittente**: il valore della richiesta SAML `issuer` deve corrispondere a uno degli URI configurati nell' `identifierUris` elemento del manifesto di registrazione dell'applicazione. Se il nome della richiesta SAML `issuer` non esiste nell' `identifierUris` elemento, [aggiungerlo al manifesto di registrazione dell'applicazione](#identifieruris). Ad esempio, `https://contoso.onmicrosoft.com/app-name` 
* **URL di accesso/endpoint SAML/URL SAML**: controllare il valore nel file di metadati dei criteri SAML Azure ad B2C per l' `<SingleSignOnService>` elemento XML
* **Certificato**: *B2C_1A_SamlIdpCert*, ma senza la chiave privata. Per ottenere la chiave pubblica del certificato:

    1. Passare all'URL dei metadati specificato sopra.
    1. Copiare il valore nell'elemento `<X509Certificate>`.
    1. Incollarlo in un file di testo.
    1. Salvare il file di testo come file con estensione *cer*.

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 Testare la soluzione con l'app di test SAML (facoltativo)

Per completare questa esercitazione, usare l'[applicazione di test SAML][samltest] per:

* Aggiornare il nome del tenant
* Aggiornare il nome dei criteri, ad esempio *B2C_1A_signup_signin_saml*
* Specificare l'URI dell'autorità emittente. Usare uno degli URI trovati nell' `identifierUris` elemento nel manifesto di registrazione dell'applicazione, ad esempio `https://contoso.onmicrosoft.com/app-name` .

Selezionare **Accedi** e dovrebbe essere visualizzata una schermata di accesso utente. Dopo l'accesso, all'applicazione di esempio viene restituita un'asserzione SAML.

## <a name="enable-encrypted-assertions-optional"></a>Abilita asserzioni crittografate (facoltativo)

Per crittografare le asserzioni SAML restituite al provider di servizi, Azure AD B2C utilizzerà il certificato di chiave pubblica del provider di servizi. La chiave pubblica deve esistere nei metadati SAML descritti in precedenza ["samlMetadataUrl"](#samlmetadataurl) come descrittore di chiave con l'uso di "Encryption".

Il codice XML seguente è un esempio del descrittore di metadati SAML con un valore use impostato su Encryption:

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Per consentire Azure AD B2C di inviare asserzioni crittografate, impostare l'elemento dei metadati **WantsEncryptedAssertion** su `true` nel [profilo tecnico relying party](relyingparty.md#technicalprofile). È anche possibile configurare l'algoritmo usato per crittografare l'asserzione SAML. Per ulteriori informazioni, vedere [relying party metadati del profilo tecnico](relyingparty.md#metadata). 

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="enable-identity-provider-initiated-flow-optional"></a>Abilita flusso avviato dal provider di identità (facoltativo)

Nel flusso avviato dal provider di identità, il processo di accesso viene avviato dal provider di identità (Azure AD B2C), che invia una risposta SAML non richiesta al provider di servizi (l'applicazione relying party). Attualmente non sono supportati scenari in cui il provider di identità di avvio è un provider di identità esterno, ad esempio [ad FS](identity-provider-adfs2016-custom.md)o [Salesforce](identity-provider-salesforce-custom.md).

Per abilitare il flusso avviato dal provider di identità (Azure AD B2C), impostare l'elemento dei metadati **IdpInitiatedProfileEnabled** su `true` nel [profilo tecnico relying party](relyingparty.md#technicalprofile).

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="IdpInitiatedProfileEnabled">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

Per eseguire l'accesso o l'iscrizione di un utente tramite il flusso avviato dal provider di identità, usare l'URL seguente:

```
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/generic/login?EntityId=app-identifier-uri 
```

Sostituire i valori seguenti:

* **nome-tenant** con il nome del tenant
* **Policy-Name** con il nome del criterio di relying party SAML
* **app-Identifier-URI** con l'oggetto `identifierUris` nel file di metadati, ad esempio `https://contoso.onmicrosoft.com/app-name`
## <a name="sample-policy"></a>Criterio di esempio

Sono disponibili criteri di esempio completi che è possibile usare per i test con l'app di test SAML.

1. Scaricare i [criteri di esempio di accesso SAML-SP-initiated](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. Aggiornare `TenantId` in base al nome del tenant, ad esempio *contoso.b2clogin.com*
1. Mantieni il nome dei criteri di *B2C_1A_signup_signin_saml*

## <a name="supported-and-unsupported-saml-modalities"></a>Modalità SAML supportate e non

Gli scenari di relying party SAML seguenti sono supportati tramite un endpoint di metadati personalizzato:

* Più URL di disconnessione o binding POST per l'URL di disconnessione nell'oggetto applicazione/entità servizio.
* Specificare la chiave di firma per verificare le richieste della relying party nell'oggetto applicazione/entità servizio.
* Specificare la chiave di crittografia del token nell'oggetto applicazione/entità servizio.
* Accesso avviato dal provider di identità, in cui il provider di identità viene Azure AD B2C.

## <a name="saml-token"></a>Token SAML

Un token SAML è un token di sicurezza emesso da Azure AD B2C dopo un accesso riuscito. Contiene informazioni sull'utente, il provider di servizi per il quale il token è destinato, la firma e il tempo di validità. La tabella seguente elenca le attestazioni e le proprietà che è possibile prevedere in un token SAML emesso da Azure AD B2C.

|Elemento  |Proprietà  |Note  |
|---------|---------|---------|
|`<Response>`| `ID` | Identificatore univoco generato automaticamente della risposta. | 
|`<Response>`| `InResponseTo` | ID della richiesta SAML a cui questo messaggio è in risposta. | 
|`<Response>` | `IssueInstant` | Istante temporale del problema della risposta. Il valore di ora è codificato in formato UTC.  Per modificare le impostazioni per la durata dei token, impostare i `TokenNotBeforeSkewInSeconds` [metadati](saml-issuer-technical-profile.md#metadata) del profilo tecnico dell'emittente del token SAML. | 
|`<Response>` | `Destination`| Riferimento URI che indica l'indirizzo a cui è stata inviata la risposta. Il valore è identico a quello della richiesta SAML `AssertionConsumerServiceURL` . | 
|`<Response>` `<Issuer>` | |Identifica l'emittente del token. Si tratta di un URI arbitrario definito dai metadati del problema del token SAML `IssuerUri` [metadata](saml-issuer-technical-profile.md#metadata)     |
|`<Response>` `<Assertion>` `<Subject>` `<NameID>`     |         |Entità su cui il token asserisce informazioni, ad esempio l'ID dell'oggetto utente. Questo valore non è modificabile e non può essere riassegnato o riutilizzato. Può essere usato per eseguire controlli di autorizzazione in modo sicuro, ad esempio quando il token viene usato per accedere a una risorsa. Per impostazione predefinita, l'attestazione dell'oggetto viene popolata con l'ID oggetto dell'utente nella directory.|
|`<Response>` `<Assertion>` `<Subject>` `<NameID>`     | `Format` | Riferimento URI che rappresenta la classificazione delle informazioni sugli identificatori basati su stringa. Per impostazione predefinita, questa proprietà viene omessa. È possibile impostare l'relying party [SubjectNamingInfo](relyingparty.md#subjectnaminginfo) per specificare il `NameID` formato, ad esempio `urn:oasis:names:tc:SAML:2.0:nameid-format:transient` . |
|`<Response>` `<Assertion>` `<Subject>` `<Conditions>` |`NotBefore` |Istante temporale in cui il token diventa valido. Il valore di ora è codificato in formato UTC. L'applicazione deve usare questa attestazione per verificare la validità della durata del token. Per modificare le impostazioni per la durata dei token, impostare i `TokenNotBeforeSkewInSeconds` [metadati](saml-issuer-technical-profile.md#metadata) del profilo tecnico relativo al problema del token SAML. |
|`<Response>` `<Assertion>` `<Subject>` `<Conditions>` | `NotOnOrAfter` | Ora in cui il token diventa non valido. L'applicazione deve usare questa attestazione per verificare la validità della durata del token. Il valore è 15 minuti dopo `NotBefore` e non può essere modificato.|
|`<Response>` `<Assertion>` `<Conditions>` `<AudienceRestriction>` `<Audience>` | |Riferimento URI che identifica i destinatari desiderati. Identifica il destinatario del token. Il valore è identico a quello della richiesta SAML `AssertionConsumerServiceURL` .|
|`<Response>``<Assertion>` `<AttributeStatement>` raccolta di`<Attribute>` | | Raccolta di asserzioni (attestazioni), come configurato nell'relying party attestazioni di output del [profilo tecnico](relyingparty.md#technicalprofile) . È possibile configurare il nome dell'asserzione impostando il valore `PartnerClaimType` dell'attestazione di output. |

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sul protocollo SAML, visitare il [sito Web OASIS](https://www.oasis-open.org/).
- Ottenere l'app Web di test SAML nel [repository della community GitHub per Azure AD B2C](https://github.com/azure-ad-b2c/saml-sp-tester).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
