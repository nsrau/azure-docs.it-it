---
title: Configurare Azure AD B2C come IdP SAML per le applicazioni
title-suffix: Azure AD B2C
description: Come configurare Azure AD B2C per fornire asserzioni di protocollo SAML alle applicazioni (provider di servizi). Azure AD B2C fungerà da provider di identità singolo (IdP) per l'applicazione SAML.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 1c362cd2924de73b2e40e634fe554ff1526e09d8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189651"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Registrare un'applicazione SAML in Azure AD B2C

Questo articolo illustra come configurare Azure Active Directory B2C (Azure AD B2C) per fungere da provider di identità (IdP) Security Assertion Markup Language (SAML) per le applicazioni.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="scenario-overview"></a>Panoramica dello scenario

Le organizzazioni che usano Azure AD B2C come soluzione di gestione delle identità e degli accessi dei clienti possono richiedere l'interazione con i provider di identità o le applicazioni configurate per l'autenticazione tramite il protocollo SAML.

Azure AD B2C Ottiene l'interoperabilità SAML in uno dei due modi seguenti:

* Agendo come provider di *identità* (IDP) e ottenendo l'accesso Single Sign-on (SSO) con i provider di servizi basati su SAML (le applicazioni)
* Agendo come provider di *Servizi* (SP) e interagendo con provider di identità basati su SAML come Salesforce e ADFS

![Diagramma con B2C come provider di identità a sinistra e B2C come provider di servizi a destra](media/saml-identity-provider/saml-idp-diagram-01.jpg)

Riepilogo dei due scenari di base non esclusivi con SAML:

| Scenario | Ruolo Azure AD B2C | Procedure |
| -------- | ----------------- | ------- |
| L'applicazione prevede che un'asserzione SAML completi un'autenticazione. | **Azure AD B2C funge da provider di identità (IdP)**<br />Azure AD B2C funge da IdP SAML per le applicazioni. | Questo articolo. |
| Gli utenti hanno bisogno dell'accesso Single Sign-on con un provider di identità conforme a SAML, ad esempio ADFS, Salesforce o Shibboleth.  | **Azure AD B2C funge da provider di servizi (SP)**<br />Azure AD B2C funge da provider di servizi per la connessione al provider di identità SAML. Si tratta di un proxy federativo tra l'applicazione e il provider di identità SAML.  | <ul><li>[Configurare l'accesso con ADFS come IdP SAML usando criteri personalizzati](identity-provider-adfs2016-custom.md)</li><li>[Configurare l'accesso con un provider SAML Salesforce usando criteri personalizzati](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>Prerequisites

* Completare i passaggi descritti in [Introduzione ai criteri personalizzati in Azure ad B2C](custom-policy-get-started.md). Sono necessari i criteri personalizzati di *SocialAndLocalAccounts* dello Starter Pack per i criteri personalizzati descritti nell'articolo.
* Conoscenza di base del protocollo Security Assertion Markup Language (SAML).
* Applicazione Web configurata come provider di servizi SAML (SP). Per questa esercitazione, è possibile usare un' [applicazione di test SAML][samltest] fornita dall'utente.

## <a name="components-of-the-solution"></a>Componenti della soluzione

Per questo scenario sono necessari tre componenti principali:

* **Provider di servizi** SAML con la possibilità di inviare richieste SAML e ricevere, decodificare e rispondere alle asserzioni saml da Azure ad B2C. Questa operazione è nota anche come relying party.
* **Endpoint dei metadati** SAML disponibile pubblicamente per il provider di servizi.
* [Tenant di Azure AD B2C](tutorial-create-tenant.md)

Se non si dispone ancora di un provider di servizi SAML e di un endpoint di metadati associato, è possibile usare questa applicazione SAML di esempio che è stata resa disponibile per il test:

[Applicazione di test SAML][samltest]

## <a name="1-set-up-certificates"></a>1. impostare i certificati

Per creare una relazione di trust tra il provider di servizi e Azure AD B2C, è necessario fornire i certificati X509 dell'app Web.

* **Certificati del provider di servizi**
  * Certificato con una chiave privata archiviata nell'app Web. Questo certificato viene usato dal provider di servizi per firmare la richiesta SAML inviata a Azure AD B2C. Azure AD B2C legge la chiave pubblica dai metadati del provider di servizi per convalidare la firma.
  * Opzionale Certificato con una chiave privata archiviata nell'app Web. Azure AD B2C legge la chiave pubblica dai metadati del provider di servizi per crittografare l'asserzione SAML. Il provider di servizi USA quindi la chiave privata per decrittografare l'asserzione.
* **Certificati Azure AD B2C**
  * Certificato con una chiave privata in Azure AD B2C. Questo certificato viene usato da Azure AD B2C per firmare la risposta SAML inviata al provider di servizi. Il provider di servizi legge la chiave pubblica dei metadati Azure AD B2C per convalidare la firma della risposta SAML.

È possibile utilizzare un certificato emesso da un'autorità di certificazione pubblica oppure, per questa esercitazione, un certificato autofirmato.

### <a name="11-prepare-a-self-signed-certificate"></a>1,1 preparare un certificato autofirmato

Se non si dispone già di un certificato, è possibile usare un certificato autofirmato per questa esercitazione. In Windows è possibile usare il cmdlet [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) di PowerShell per generare un certificato.

1. Eseguire questo comando di PowerShell per generare un certificato autofirmato. Modificare l'argomento `-Subject` nel modo appropriato per l'applicazione e Azure AD B2C nome del tenant. È anche possibile modificare la data di `-NotAfter` per specificare una scadenza diversa per il certificato.

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

1. Aprire **Gestisci certificati utente** > **utente corrente** > **certificati** > **personali** > *YourAppName.yourtenant.onmicrosoft.com*
1. Selezionare l' **azione** > certificato > **tutte le attività** > **esportazione**
1. Selezionare **sì** > **Avanti** > **Sì, Esporta la chiave privata** > **Avanti**
1. Accetta le impostazioni predefinite per il **formato del file di esportazione**
1. Specificare una password per il certificato

### <a name="12-upload-the-certificate"></a>1,2 caricare il certificato

Successivamente, caricare il certificato di firma della risposta e dell'asserzione SAML in Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com) e passare al tenant di Azure ad B2C.
1. In **criteri**selezionare **Framework dell'esperienza di identità** e quindi chiavi dei **criteri**.
1. Selezionare **Aggiungi**e quindi selezionare le **Opzioni** > **carica**.
1. Immettere un **nome**, ad esempio *SamlIdpCert*. Verrà automaticamente aggiunto il prefisso *B2C_1A_* al nome della chiave.
1. Caricare il certificato usando il controllo carica file.
1. Immettere la password del certificato.
1. Selezionare **Create** (Crea).
1. Verificare che la chiave venga visualizzata come previsto. Ad esempio, *B2C_1A_SamlIdpCert*.

## <a name="2-prepare-your-policy"></a>2. preparare i criteri

### <a name="21-create-the-saml-token-issuer"></a>2,1 creare l'emittente del token SAML

A questo punto, aggiungere la funzionalità per il tenant per emettere token SAML.

Aprire `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** nello Starter Pack del criterio personalizzato.

Individuare la sezione `<ClaimsProviders>` e aggiungere il frammento di codice XML seguente.

È possibile modificare il valore dei metadati del `IssuerUri`. Si tratta dell'URI dell'autorità emittente restituito nella risposta SAML da Azure AD B2C. L'applicazione relying party deve essere configurata in modo da accettare un URI dell'autorità emittente durante la convalida dell'asserzione SAML.

```XML
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="None"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <!-- The issuer contains the policy name; it should be the same name as configured in the relying party application. B2C_1A_signup_signin_SAML is used below. -->
        <!--<Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>-->
      </Metadata>
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-sp">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. aggiungere i criteri di relying party SAML

Ora che il tenant può emettere asserzioni SAML, è necessario creare i criteri di relying party SAML e modificare il percorso utente in modo da emettere un'asserzione SAML anziché un JWT.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3,1 creare i criteri di iscrizione o di accesso

1. Creare una copia del file *SignUpOrSignin. XML* nella directory di lavoro dello Starter Pack e salvarla con un nuovo nome. Ad esempio, *SignUpOrSigninSAML. XML*. Si tratta del file di criteri relying party.

1. Aprire il file *SignUpOrSigninSAML. XML* nell'editor preferito.

1. Modificare il `PolicyId` e il `PublicPolicyUri` dei criteri per _B2C_1A_signup_signin_saml_ e `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml` come illustrato di seguito.

    ```XML
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Aggiungere il frammento di codice XML seguente immediatamente prima dell'elemento `<RelyingParty>`. Il codice XML sovrascrive il passaggio di orchestrazione numero 7 del percorso utente _SignUpOrSignIn_ . Se è stato avviato da una cartella diversa nello Starter Pack oppure è stato personalizzato il percorso utente aggiungendo o rimuovendo passaggi di orchestrazione, verificare che il numero (nell'elemento `order`) sia allineato con quello specificato nel percorso utente per il passaggio dell'emittente del token (ad esempio, nelle altre cartelle dello Starter Pack è il passaggio 4 per `LocalAccounts`, 6 per `SocialAccounts` e 9 per `SocialAndLocalAccountsWithMfa`).

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. Sostituire l'intero elemento `<TechnicalProfile>` nell'elemento `<RelyingParty>` con il seguente XML del profilo tecnico.

    ```XML
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

1. Aggiornare `tenant-name` con il nome del tenant del Azure AD B2C.

Il file dei criteri di relying party finale dovrebbe essere simile al seguente:

```XML
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

### <a name="32-upload-and-test-your-policy-metadata"></a>3,2 caricare e testare i metadati dei criteri

Salvare le modifiche e caricare il nuovo file dei criteri. Dopo aver caricato entrambi i criteri (estensione e file di relying party), aprire un Web browser e passare ai metadati dei criteri.

I metadati IDP dei criteri Azure AD B2C sono informazioni usate nel protocollo SAML per esporre la configurazione di un provider di identità SAML. I metadati definiscono il percorso dei servizi, ad esempio accesso e disconnessione, certificati, metodo di accesso e altro ancora. I metadati dei criteri Azure AD B2C sono disponibili nel seguente URL. Sostituire `tenant-name` con il nome del tenant di Azure AD B2C e `policy-name` con il nome (ID) del criterio:

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

I criteri personalizzati e Azure AD B2C tenant sono ora pronti. Successivamente, creare una registrazione dell'applicazione in Azure AD B2C.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. configurare l'applicazione nella directory Azure AD B2C

### <a name="41-register-your-application-in-azure-active-directory"></a>4,1 registrare l'applicazione in Azure Active Directory

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Registrazioni app (anteprima)** e quindi **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *SAMLApp1*.
1. In **tipi di account supportati**selezionare **account solo in questa directory organizzativa**
1. In **URI di reindirizzamento**selezionare **Web**, quindi immettere `https://localhost`. Questo valore verrà modificato in un secondo momento nel manifesto della registrazione dell'applicazione.
1. Selezionare **Registra**.

### <a name="42-update-the-app-manifest"></a>4,2 aggiornare il manifesto dell'applicazione

Per le app SAML, è necessario configurare diverse proprietà nel manifesto della registrazione dell'applicazione.

1. Nella [portale di Azure](https://portal.azure.com)passare alla registrazione dell'applicazione creata nella sezione precedente.
1. In **Gestisci**selezionare **manifesto** per aprire l'editor manifesto. Nelle sezioni seguenti vengono modificate diverse proprietà.

#### <a name="identifieruris"></a>identifierUris

Il `identifierUris` è una raccolta di stringhe contenente URI definiti dall'utente che identificano in modo univoco un'app Web all'interno del tenant di Azure AD B2C. Il provider di servizi deve impostare questo valore nell'elemento `Issuer` di una richiesta SAML.

#### <a name="samlmetadataurl"></a>samlMetadataUrl

Questa proprietà rappresenta l'URL dei metadati disponibili pubblicamente del provider di servizi. L'URL dei metadati può puntare a un file di metadati caricato in qualsiasi endpoint accessibile in modo anonimo, ad esempio l'archivio BLOB.

I metadati sono informazioni utilizzate nel protocollo SAML per esporre la configurazione di un'entità SAML, ad esempio un provider di servizi. I metadati definiscono il percorso dei servizi quali l'accesso e la disconnessione, i certificati, il metodo di accesso e altro ancora. Azure AD B2C legge i metadati del provider di servizi e agisce di conseguenza. I metadati non sono obbligatori. È anche possibile specificare alcuni attributi, ad esempio l'URI di risposta e l'URI di disconnessione direttamente nel manifesto dell'applicazione.

Se sono presenti proprietà *specificate nell'URL dei metadati* SAML e nel manifesto della registrazione dell'applicazione, vengono **Unite**. Le proprietà specificate nell'URL dei metadati vengono elaborate per prime e hanno la precedenza.

Per questa esercitazione che usa l'applicazione di test SAML, usare il valore seguente per `samlMetadataUrl`:

```JSON
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType (facoltativo)

Se non si specifica un URI di metadati, è possibile specificare in modo esplicito l'URL di risposta. Questa proprietà facoltativa rappresenta la `AssertionConsumerServiceUrl` (`SingleSignOnService` URL nei metadati del provider di servizi) e si presuppone che il `BindingType` sia `HTTP POST`.

Se si sceglie di configurare l'URL di risposta e l'URL di disconnessione nel manifesto dell'applicazione senza usare i metadati del provider di servizi, Azure AD B2C non convaliderà la firma della richiesta SAML né crittografare la risposta SAML.

Per questa esercitazione, in cui si usa l'applicazione di test SAML, impostare la proprietà `url` di `replyUrlsWithType` sul valore mostrato nel frammento di codice JSON seguente.

```JSON
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl (facoltativo)

Questa proprietà facoltativa rappresenta l'URL di `Logout` (`SingleLogoutService` URL nei metadati del relying party) e il `BindingType` per questo si presuppone `Http-Redirect`.

Per questa esercitazione che usa l'applicazione di test SAML, lasciare `logoutUrl` impostato su `https://samltestapp2.azurewebsites.net/logout`:

```JSON
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. aggiornare il codice dell'applicazione

L'ultimo passaggio consiste nell'abilitare Azure AD B2C come IdP SAML nell'applicazione SAML relying party. Ogni applicazione è diversa e i passaggi per eseguire questa operazione variano. Per informazioni dettagliate, vedere la documentazione dell'app.

In genere sono necessari alcuni o tutti gli elementi seguenti:

* **Metadati**: `https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **Autorità emittente**: `https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name`
* **URL di accesso/endpoint SAML/URL SAML**: controllare il valore nel file di metadati
* **Certificate**: questo è *B2C_1A_SamlIdpCert*, ma senza la chiave privata. Per ottenere la chiave pubblica del certificato:

    1. Consente di passare all'URL dei metadati specificato in precedenza.
    1. Copiare il valore nell'elemento `<X509Certificate>`.
    1. Incollarlo in un file di testo.
    1. Salvare il file di testo come file con *estensione cer* .

### <a name="51-test-with-the-saml-test-app-optional"></a>5,1 testare con l'app di test SAML (facoltativo)

Per completare questa esercitazione usando l' [applicazione di test SAML][samltest]:

* Aggiornare il nome del tenant
* Aggiornare il nome dei criteri, ad esempio *B2C_1A_signup_signin_saml*
* Specificare l'URI dell'autorità emittente: `https://contoso.onmicrosoft.com/app-name`

Selezionare **account di accesso** . verrà visualizzata una schermata di accesso dell'utente finale. Al momento dell'accesso, un'asserzione SAML viene rilasciata all'applicazione di esempio.

## <a name="sample-policy"></a>Criterio di esempio

Sono disponibili criteri di esempio completi che è possibile usare per il test con l'app di test SAML.

1. Scaricare i [criteri di esempio per l'accesso avviato da SAML-SP](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. Aggiornare `TenantId` in modo che corrisponda al nome del tenant, ad esempio *contoso.b2clogin.com*
1. Mantieni il nome dei criteri di *B2C_1A_SAML2_signup_signin*

## <a name="supported-and-unsupported-saml-modalities"></a>Modalità SAML supportate e non supportate

Gli scenari SAML relying party (RP) seguenti sono supportati tramite un endpoint di metadati personalizzato:

* Più URL di disconnessione o POST-binding per l'URL di disconnessione nell'oggetto applicazione/entità servizio.
* Specificare la chiave di firma per verificare le richieste RP nell'oggetto applicazione/entità servizio.
* Specificare la chiave di crittografia del token nell'oggetto applicazione/entità servizio.
* Gli account di accesso avviati dal provider di identità non sono attualmente supportati nella versione di anteprima.

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare altre informazioni sul [protocollo SAML nel sito Web Oasis](https://www.oasis-open.org/).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
