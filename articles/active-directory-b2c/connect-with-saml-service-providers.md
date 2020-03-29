---
title: Configurare Azure AD B2C come IDP SAML per le applicazioniConfigure Azure AD B2C as a SAML IdP to your applications
title-suffix: Azure AD B2C
description: Come configurare Azure AD B2C per fornire asserzioni di protocollo SAML alle applicazioni (provider di servizi). Azure AD B2C fungerà da singolo provider di identità (IdP) per l'applicazione SAML.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a72b5b50daaae33336de9caab5202c2bf42f5c15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051612"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Registrare un'applicazione SAML in Azure AD B2CRegister a SAML application in Azure AD B2C

In questo articolo viene illustrato come configurare Azure Active Directory B2C (Azure AD B2C) in modo che funga da provider di identità SAML (Security Assertion Markup Language) per le applicazioni.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="scenario-overview"></a>Panoramica dello scenario

Le organizzazioni che usano Azure AD B2C come soluzione di gestione delle identità e degli accessi dei clienti potrebbero richiedere l'interazione con provider di identità o applicazioni configurate per l'autenticazione tramite il protocollo SAML.

Azure AD B2C raggiunge l'interoperabilità SAML in uno dei due modi seguenti:

* Agendo come provider di *identità* (IdP) e ottenendo l'accesso Single Sign-On (SSO) con i provider di servizi basati su SAML (le tue applicazioni)
* Agendo come provider di *servizi* (SP) e interagendo con provider di identità basati su SAML come Salesforce e ADFS

![Diagramma con B2C come provider di identità a sinistra e B2C come provider di servizi a destra](media/saml-identity-provider/saml-idp-diagram-01.jpg)

Riassumendo i due scenari principali non esclusivi con SAML:

| Scenario | Ruolo B2C di Azure ADAzure AD B2C role | Procedure |
| -------- | ----------------- | ------- |
| L'applicazione prevede un'asserzione SAML per completare un'autenticazione. | **Azure AD B2C funge da provider di identità (IdP)Azure AD B2C acts as the identity provider (IdP)**<br />Azure AD B2C funge da IdP SAML per le applicazioni. | Questo articolo. |
| Gli utenti hanno bisogno dell'accesso Single Sign-On con un provider di identità conforme a SAML come ADFS, Salesforce o Shibboleth.  | **Azure AD B2C funge da provider di servizi (SP)Azure AD B2C acts as the service provider (SP)**<br />Azure AD B2C funge da provider di servizi per la connessione al provider di identità SAML. Si tratta di un proxy federativo tra l'applicazione e il provider di identità SAML.  | <ul><li>[Configurare l'accesso con ADFS come IDP SAML utilizzando criteri personalizzati](identity-provider-adfs2016-custom.md)</li><li>[Configurare l'accesso con un provider SAML Salesforce utilizzando criteri personalizzati](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>Prerequisiti

* Completare i passaggi descritti in [Introduzione ai criteri personalizzati in Azure AD B2C.](custom-policy-get-started.md) È necessario il criterio personalizzato *SocialAndLocalAccounts* dal pacchetto starter dei criteri personalizzati descritto nell'articolo.
* Conoscenza di base del protocollo SAML (Security Assertion Markup Language).
* Un'applicazione Web configurata come provider di servizi SAML (SP). Per questa esercitazione, è possibile usare [un'applicazione][samltest] di test SAML fornita.

## <a name="components-of-the-solution"></a>Componenti della soluzione

Esistono tre componenti principali necessari per questo scenario:There are three main components required for this scenario:

* Provider di **servizi** SAML con la possibilità di inviare richieste SAML e ricevere, decodificare e rispondere alle asserzioni SAML da Azure AD B2C. Questo è noto anche come la relying party.
* Endpoint di **metadati** SAML disponibile pubblicamente per il provider di servizi.
* [Tenant di Azure AD B2C](tutorial-create-tenant.md)

Se non si dispone ancora di un provider di servizi SAML e di un endpoint di metadati associato, è possibile usare questa applicazione SAML di esempio resa disponibile per il test:If you don't yet have a SAML service provider and an associated metadata endpoint, you can use this sample SAML application that we've made available for testing:

[Applicazione di prova SAML][samltest]

## <a name="1-set-up-certificates"></a>1. Impostare i certificati

Per creare una relazione di trust tra il provider di servizi e Azure AD B2C, è necessario fornire i certificati X509 dell'app Web.To build a trust relationship between your service provider and Azure AD B2C, you need to provide the web app X509 certificates.

* **Certificati del provider di servizi**
  * Certificato con una chiave privata archiviata nell'app Web. Questo certificato viene usato dal provider di servizi per firmare la richiesta SAML inviata ad Azure AD B2C. Azure AD B2C legge la chiave pubblica dai metadati del provider di servizi per convalidare la firma.
  * (Facoltativo) Certificato con una chiave privata archiviata nell'app Web. Azure AD B2C legge la chiave pubblica dai metadati del provider di servizi per crittografare l'asserzione SAML. Il provider di servizi utilizza quindi la chiave privata per decrittografare l'asserzione.
* **Certificati B2C di Azure ADAzure AD B2C certificates**
  * Certificato con una chiave privata in Azure AD B2C. Questo certificato viene usato da Azure AD B2C per firmare la risposta SAML inviata al provider di servizi. Il provider di servizi legge la chiave pubblica dei metadati B2C di Azure AD per convalidare la firma della risposta SAML.

È possibile usare un certificato emesso da un'autorità di certificazione pubblica o, per questa esercitazione, un certificato autofirmato.

### <a name="11-prepare-a-self-signed-certificate"></a>1.1 Preparare un certificato autofirmato

Se non si dispone già di un certificato, è possibile usare un certificato autofirmato per questa esercitazione. In Windows, è possibile utilizzare il cmdlet [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) di PowerShell per generare un certificato.

1. Eseguire questo comando di PowerShell per generare un certificato autofirmato. Modificare `-Subject` l'argomento in base alle esigenze dell'applicazione e del nome del tenant Di Azure AD B2C. È inoltre possibile `-NotAfter` modificare la data per specificare una scadenza diversa per il certificato.

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

1. Aprire **Gestisci certificati** > utente**Certificati** > **personali** > **utente** > *correnti yourappname.yourtenant.onmicrosoft.com*
1. Selezionare il certificato **>azione** > **Tutte le attività** > **esportazione**
1. Selezionare **Sì** > **successivo** > **Sì, esporta la chiave** > privata**Avanti**
1. Accettare le impostazioni predefinite per **Formato file** di esportazione
1. Fornire una password per il certificato

### <a name="12-upload-the-certificate"></a>1.2 Caricare il certificato

Successivamente, caricare il certificato di firma dell'asserzione e della risposta SAML in Azure AD B2C.

1. Accedere al [portale](https://portal.azure.com) di Azure e passare al tenant B2C di Azure AD.
1. In **Criteri**selezionare **Identity Experience Framework** e quindi Chiavi **criteri**.
1. Selezionare **Aggiungi**, quindi **Opzioni** > **di caricamento**.
1. Immettere un **Nome**, ad esempio *SamlIdpCert*. Verrà automaticamente aggiunto il prefisso *B2C_1A_* al nome della chiave.
1. Caricare il certificato utilizzando il controllo del file di caricamento.
1. Immettere la password del certificato.
1. Selezionare **Crea**.
1. Verificare che la chiave venga visualizzata come previsto. Ad esempio, *B2C_1A_SamlIdpCert*.

## <a name="2-prepare-your-policy"></a>2. Preparare la politica

### <a name="21-create-the-saml-token-issuer"></a>2.1 Creare l'autorità emittente di token SAML

A questo punto, aggiungere la funzionalità per il tenant di emettere token SAML, utilizzando i profili tecnici [dell'autorità emittente](saml-issuer-technical-profile.md) di token SAML e del provider di [sessione SAML.](custom-policy-reference-sso.md#samlssosessionprovider)

Aperto `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** nello starter pack dei criteri personalizzati.

Individuare `<ClaimsProviders>` la sezione e aggiungere il frammento XML seguente.

È possibile modificare il `IssuerUri` valore dei metadati. Si tratta dell'URI dell'autorità emittente restituito nella risposta SAML da Azure AD B2C. L'applicazione relying party deve essere configurata per accettare un URI dell'autorità di certificazione durante la convalida dell'asserzione SAML.

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

## <a name="3-add-the-saml-relying-party-policy"></a>3. Aggiungere il criterio della relying party SAML

Ora che il tenant può emettere asserzioni SAML, è necessario creare i criteri della relying party SAML e modificare il percorso utente in modo che emetta un'asserzione SAML anziché un token JWT.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 Creare criteri di iscrizione o di accesso

1. Creare una copia del file *SignUpOrSignin.xml* nella directory di lavoro dello starter pack e salvarla con un nuovo nome. Ad esempio, *SignUpOrSigninSAML.xml*. Questo è il file dei criteri della relying party.

1. Aprire il file *SignUpOrSigninSAML.xml* nell'editor preferito.

1. Modificare `PolicyId` la `PublicPolicyUri` e della politica `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml` in _B2C_1A_signup_signin_saml_ e come illustrato di seguito.

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

1. Aggiungere il frammento `<RelyingParty>` XML seguente appena prima dell'elemento. Questo codice XML sovrascrive il passaggio di orchestrazione numero 7 del percorso utente _SignUpOrSignIn._ Se è stato avviato da una cartella diversa nello starter pack o si è personalizzato il percorso `order` utente aggiungendo o rimuovendo passaggi di orchestrazione, assicurarsi che il numero (nell'elemento) sia allineato `LocalAccounts`a quello `SocialAccounts` specificato `SocialAndLocalAccountsWithMfa`nel percorso utente per il passaggio dell'autorità emittente di token (ad esempio, nelle altre cartelle dello starter pack è il passaggio 4 per , 6 per e 9 per ).

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. Sostituire l'intero `<TechnicalProfile>` `<RelyingParty>` elemento nell'elemento con il seguente profilo tecnico XML.

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

1. Eseguire `tenant-name` l'aggiornamento con il nome del tenant B2C di Azure AD.

Il file di criteri della relying party finale dovrebbe essere simile al seguente:

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

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 Caricare e testare i metadati dei criteri

Salvare le modifiche e caricare il nuovo file di criteri. Dopo aver caricato entrambi i criteri (l'estensione e i file della relying party), aprire un Web browser e passare ai metadati dei criteri.

I metadati IDP dei criteri B2C di Azure AD sono informazioni usate nel protocollo SAML per esporre la configurazione di un provider di identità SAML. I metadati definiscono il percorso dei servizi, ad esempio accesso e disconnessione, certificati, metodo di accesso e altro ancora. I metadati dei criteri B2C di Azure AD sono disponibili all'URL seguente. Sostituire `tenant-name` con il nome del tenant B2C di Azure AD e `policy-name` con il nome (ID) del criterio:

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

I criteri personalizzati e il tenant B2C di Azure AD sono ora pronti. Successivamente, creare una registrazione dell'applicazione in Azure AD B2C.Next, create an application registration in Azure AD B2C.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. Applicazione di installazione nella directory B2C di Azure AD

### <a name="41-register-your-application-in-azure-active-directory"></a>4.1 Registrare l'applicazione in Azure Active Directory

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Registrazioni app (anteprima)** e quindi **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *SAMLApp1*.
1. In **Tipi di account supportati**selezionare Solo account in questa directory **dell'organizzazione**
1. In **URI di reindirizzamento**selezionare **Web**e quindi immettere `https://localhost`. Questo valore verrà modificato in un secondo momento nel manifesto di registrazione dell'applicazione.
1. Selezionare **Registra**.

### <a name="42-update-the-app-manifest"></a>4.2 Aggiornare il manifesto dell'app

Per le app SAML, è necessario configurare diverse proprietà nel manifesto di registrazione dell'applicazione.

1. Nel [portale di Azure](https://portal.azure.com)passare alla registrazione dell'applicazione creata nella sezione precedente.
1. In **Gestisci**selezionare **Manifesto** per aprire l'editor del manifesto. Le proprietà vengono modificate nelle sezioni seguenti.

#### <a name="identifieruris"></a>identifierUris

Si `identifierUris` tratta di una raccolta di stringhe contenente URI definiti dall'utente che identificano in modo univoco un'app Web all'interno del tenant B2C di Azure AD. Il provider di servizi deve `Issuer` impostare questo valore nell'elemento di una richiesta SAML.

#### <a name="samlmetadataurl"></a>samlMetadataUrl

Questa proprietà rappresenta l'URL dei metadati disponibile pubblicamente del provider di servizi. L'URL dei metadati può puntare a un file di metadati caricato in qualsiasi endpoint accessibile in modo anonimo, ad esempio l'archiviazione BLOB.

I metadati sono informazioni utilizzate nel protocollo SAML per esporre la configurazione di un'entità SAML, ad esempio un provider di servizi. I metadati definiscono la posizione dei servizi, ad esempio l'accesso e la disconnessione, i certificati, il metodo di accesso e altro ancora. Azure AD B2C legge i metadati del provider di servizi e agisce di conseguenza. I metadati non sono necessari. Puoi anche specificare alcuni attributi come l'URI di risposta e l'URI di disconnessione direttamente nel manifesto dell'app.

Se sono presenti proprietà specificate sia nell'URL dei metadati SAML *che* nel manifesto di registrazione dell'applicazione, vengono **unite.** Le proprietà specificate nell'URL dei metadati vengono elaborate per prime e hanno la precedenza.

Per questa esercitazione, che usa l'applicazione di `samlMetadataUrl`test SAML, usare il valore seguente per:

```JSON
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType (facoltativo)

Se non si fornisce un URI dei metadati, è possibile specificare in modo esplicito l'URL di risposta. Questa proprietà facoltativa `AssertionConsumerServiceUrl` `SingleSignOnService` rappresenta l'URL ( nei `BindingType` metadati `HTTP POST`del provider di servizi) e si presuppone che il parametro sia .

Se si sceglie di configurare l'URL di risposta e l'URL di disconnessione nel manifesto dell'applicazione senza usare i metadati del provider di servizi, Azure AD B2C non convaliderà la firma della richiesta SAML né crittograferà la risposta SAML.

Per questa esercitazione, in cui si usa `url` l'applicazione di test SAML, impostare la proprietà di `replyUrlsWithType` sul valore illustrato nel frammento JSON seguente.

```JSON
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl (facoltativo)

Questa proprietà facoltativa `Logout` rappresenta`SingleLogoutService` l'URL ( URL nei `BindingType` metadati della `Http-Redirect`relying party) e si presuppone che per questo sia .

Per questa esercitazione, che usa l'applicazione di test SAML, lasciare `logoutUrl` impostato su `https://samltestapp2.azurewebsites.net/logout`:

```JSON
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. Aggiornare il codice dell'applicazione

L'ultimo passaggio consiste nell'abilitare Azure AD B2C come IdP SAML nell'applicazione relying party SAML. Ogni applicazione è diversa e i passaggi per farlo variano. Consulta la documentazione della tua app per i dettagli.

Alcuni o tutti gli elementi seguenti sono in genere necessari:Some or all the following are typically required:

* **Metadati**:`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **Issuer**: utilizzare entityID nel file di metadati
* **Url di accesso/endpoint SAML/URL SAML**: Controllare il valore nel file di metadati
* **Certificato**: Si tratta *di B2C_1A_SamlIdpCert*, ma senza la chiave privata. Per ottenere la chiave pubblica del certificato:

    1. Passare all'URL dei metadati specificato in precedenza.
    1. Copiare il `<X509Certificate>` valore nell'elemento.
    1. Incollarlo in un file di testo.
    1. Salvare il file di testo come file *CER.*

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 Test con l'app di prova SAML (opzionale)

Per completare questa esercitazione utilizzando [l'applicazione di test SAML:][samltest]

* Aggiornare il nome del tenant
* Aggiornare il nome del criterio, ad esempio *B2C_1A_signup_signin_saml*
* Specificare questo URI dell'autorità emittente:`https://contoso.onmicrosoft.com/app-name`

Selezionare **Login (Accedi)** e verrà visualizzata una schermata di accesso dell'utente. Al momento dell'accesso, un'asserzione SAML viene rimessa all'applicazione di esempio.

## <a name="sample-policy"></a>Criterio di esempio

Forniamo un criterio di esempio completo che puoi usare per il test con l'app samL Test.

1. Scaricare il criterio di [esempio di accesso avviato da SAML-SP](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. Aggiornare `TenantId` in modo che corrisponda al nome del tenant, ad esempio *contoso.b2clogin.com*
1. Mantenere il nome del criterio *di B2C_1A_SAML2_signup_signin*

## <a name="supported-and-unsupported-saml-modalities"></a>Modalità SAML supportate e non supportate

I seguenti scenari di relying party SAML (RP) sono supportati tramite il proprio endpoint dei metadati:

* Più URL di disconnessione o binding POST per l'URL di disconnessione nell'oggetto applicazione/entità servizio.
* Specificare la chiave di firma per verificare le richieste di codice-UTENTE nell'oggetto applicazione/entità servizio.
* Specificare la chiave di crittografia del token nell'oggetto applicazione/entità servizio.
* Gli account di accesso avviati dal provider di identità non sono attualmente supportati nella versione di anteprima.

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sul protocollo SAML sono [disponibili sul sito Web OASIS.](https://www.oasis-open.org/)
- Ottenere l'app Web di test SAML dal repository della community di [Azure AD B2C GitHub.](https://github.com/azure-ad-b2c/saml-sp-tester)

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
