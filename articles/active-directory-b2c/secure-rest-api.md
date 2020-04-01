---
title: Proteggere un servizio Restful in Azure AD B2CSecure a Restful service in your Azure AD B2C
titleSuffix: Azure AD B2C
description: Proteggere gli scambi di attestazioni dell'API REST personalizzata in Azure AD B2C.Secure your custom REST API claims exchanges in your Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37d1c181c18f69c040040da2be138eaad3a61693
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396848"
---
# <a name="secure-your-restful-services"></a>Proteggi i tuoi servizi RESTful 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Quando si integra un'API REST all'interno di un percorso utente B2C di Azure AD, è necessario proteggere l'endpoint dell'API REST con l'autenticazione. In questo modo si garantisce che solo i servizi con credenziali appropriate, ad esempio Azure AD B2C, possano effettuare chiamate all'endpoint dell'API REST.

Informazioni su come integrare un'API REST all'interno del percorso utente B2C di Azure AD negli articoli [Convalidare l'input dell'utente](custom-policy-rest-api-claims-validation.md) e [Aggiungere attestazioni dell'API REST agli](custom-policy-rest-api-claims-exchange.md) articoli sui criteri personalizzati.

Questo articolo esplorerà come proteggere l'API REST con l'autenticazione di base HTTP, del certificato client o OAuth2.This article explore how to secure your REST API with either HTTP basic, client certificate or OAuth2 authentication. 

## <a name="prerequisites"></a>Prerequisiti

Completare i passaggi in una delle seguenti guide "Procedura":

- [Integrare gli scambi di attestazioni dell'API REST nel percorso utente B2C di Azure AD per convalidare l'input dell'utente.](custom-policy-rest-api-claims-validation.md)
- [Aggiungere scambi di attestazioni dell'API REST ai criteri personalizzatiAdd REST API claims exchanges to custom policies](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>Autenticazione di base HTTP

L'autenticazione di base HTTP è definita in [RFC 2617](https://tools.ietf.org/html/rfc2617). L'autenticazione di base funziona come segue: Azure AD B2C invia una richiesta HTTP con le credenziali client nell'intestazione Authorization. Le credenziali vengono formattate come stringa con codifica base64 "name:password".  

### <a name="add-rest-api-username-and-password-policy-keys"></a>Aggiungere le chiavi dei criteri nome utente e password dell'API RESTAdd REST API username and password policy keys

Per configurare un profilo tecnico dell'API REST con l'autenticazione di base HTTP, creare le chiavi di crittografia seguenti per archiviare il nome utente e la password:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Assicurarsi di usare la directory che contiene il tenant B2C di Azure AD. Nel menu **superiore,** selezionare il filtro Directory e sottoscrizione e scegliere la directory B2C di Azure AD.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **Policy Keys**, quindi **scegliere Add**.
1. Selezionare **Manuale** in **Opzioni**.
1. In **Nome**digitare **RestApiUsername**.
    È possibile che il prefisso *B2C_1A_* venga aggiunto automaticamente.
1. Nella casella **Segreto** immettere il nome utente dell'API REST.
1. In **Uso chiave**selezionare **Crittografia**.
1. Selezionare **Create** (Crea).
1. Selezionare nuovamente **Chiavi del criterio.**
1. Selezionare **Aggiungi**.
1. Selezionare **Manuale** in **Opzioni**.
1. In **Nome**digitare **RestApiPassword**.
    È possibile che il prefisso *B2C_1A_* venga aggiunto automaticamente.
1. Nella casella **Segreto** immettere la password dell'API REST.
1. In **Uso chiave**selezionare **Crittografia**.
1. Selezionare **Create** (Crea).

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>Configurare il profilo tecnico dell'API REST per l'utilizzo dell'autenticazione di base HTTPConfigure your REST API technical profile to use HTTP basic authentication

Dopo aver creato le chiavi necessarie, configurare i metadati del profilo tecnico dell'API REST in modo che facciano riferimento alle credenziali.

1. Aprire il file dei criteri di estensione (TrustFrameworkExtensions.xml) nella directory di lavoro.
1. Cercare il profilo tecnico dell'API REST. ad esempio `REST-ValidateProfile` o `REST-GetProfile`.
1. Individuare l'elemento `<Metadata>`.
1. Impostare *AuthenticationType* su `Basic`.
1. Impostare *AllowInsecureAuthInProduction* su `false`.
1. Aggiungere il frammento XML seguente subito dopo l'elemento `</Metadata>` di chiusura:
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

Di seguito è riportato un esempio di profilo tecnico RESTful configurato con l'autenticazione di base HTTP:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="https-client-certificate-authentication"></a>Autenticazione del certificato client HTTPS

L'autenticazione del certificato client è un'autenticazione reciproca basata su certificati, in cui il client Azure AD B2C fornisce il proprio certificato client al server per dimostrare la propria identità. Questo accade come parte dell'handshake SSL. Solo i servizi che dispongono di certificati appropriati, ad esempio Azure AD B2C, possono accedere al servizio API REST. Il certificato client è un certificato digitale X.509.The client certificate is an X.509 digital certificate. Negli ambienti di produzione, deve essere firmato da un'autorità di certificazione.

### <a name="prepare-a-self-signed-certificate-optional"></a>Preparare un certificato autofirmato (facoltativo)Prepare a self-signed certificate (optional)

Per gli ambienti non di produzione, se non si dispone già di un certificato, è possibile usare un certificato autofirmato. In Windows, è possibile utilizzare il cmdlet [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) di PowerShell per generare un certificato.

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
1. Aprire **Gestisci certificati** > utente**Certificati** > **personali** > **utente** > *yourappname.yourtenant.onmicrosoft.com .*
1. Selezionare il certificato > **Azione** > **Esportazione****di tutte le attività** > .
1. Selezionare **Sì** > **successivo** > **Sì, esporta la chiave** > privata**Avanti**.
1. Accettare le impostazioni predefinite per **Formato file**di esportazione .
1. Specificare una password per il certificato.

### <a name="add-a-client-certificate-policy-key"></a>Aggiungere una chiave dei criteri del certificato clientAdd a client certificate policy key

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Assicurarsi di usare la directory che contiene il tenant B2C di Azure AD. Nel menu **superiore,** selezionare il filtro Directory e sottoscrizione e scegliere la directory B2C di Azure AD.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **Policy Keys**, quindi **scegliere Add**.
1. Nella casella **Opzioni** selezionare **Carica**.
1. Nella casella **Nome** digitare **RestApiClientCertificate**.
    Il prefisso *B2C_1A_* viene aggiunto automaticamente.
1. Nella casella **Caricamento file** selezionare il file del certificato con estensione pfx con una chiave privata.
1. Nella casella **Password** digitare la password del certificato.
1. Selezionare **Create** (Crea).

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Configurare il profilo tecnico dell'API REST per l'utilizzo dell'autenticazione del certificato clientConfigure your REST API technical profile to use client certificate authentication

Dopo aver creato la chiave necessaria, configurare i metadati del profilo tecnico dell'API REST in modo che facciano riferimento al certificato client.

1. Aprire il file dei criteri di estensione (TrustFrameworkExtensions.xml) nella directory di lavoro.
1. Cercare il profilo tecnico dell'API REST. ad esempio `REST-ValidateProfile` o `REST-GetProfile`.
1. Individuare l'elemento `<Metadata>`.
1. Impostare *AuthenticationType* su `ClientCertificate`.
1. Impostare *AllowInsecureAuthInProduction* su `false`.
1. Aggiungere il frammento XML seguente subito dopo l'elemento `</Metadata>` di chiusura:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

Di seguito è riportato un esempio di profilo tecnico RESTful configurato con un certificato client HTTP:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="oauth2-bearer-authentication"></a>Autenticazione dell'operatore OAuth2 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

L'autenticazione del token Bearer è definita in [OAuth2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). Nell'autenticazione con token di connessione, Azure AD B2C invia una richiesta HTTP con un token nell'intestazione di autorizzazione.

```http
Authorization: Bearer <token>
```

Un token di connessione è una stringa opaca. Può trattarsi di un token di accesso JWT o di qualsiasi stringa che l'API REST prevede che Azure AD B2C invii nell'intestazione di autorizzazione. Azure AD B2C supporta i tipi seguenti:Azure AD B2C supports the following types:

- **Gettone bearer**. Per poter inviare il token di connessione nel profilo tecnico Restful, la politica deve prima acquisire il token di connessione e quindi utilizzarlo nel profilo tecnico RESTful.  
- **Gettone di portatore statico**. Usare questo approccio quando l'API REST emette un token di accesso a lungo termine. Per usare un token di connessione statico, creare una chiave dei criteri ed creare un riferimento dal profilo tecnico RESTful alla chiave dei criteri. 


## <a name="using-oauth2-bearer"></a>Utilizzo di OAuth2 Bearer  

I passaggi seguenti illustrano come usare le credenziali client per ottenere un token di connessione e passarlo nell'intestazione Authorization delle chiamate all'API REST.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Definire un'attestazione per archiviare il token di connessioneDefine a claim to store the bearer token

Un'attestazione fornisce l'archiviazione temporanea dei dati durante l'esecuzione di criteri B2C di Azure AD. Lo [schema delle attestazioni](claimsschema.md) è la posizione in cui si dichiarano le attestazioni. Il token di accesso deve essere archiviato in un'attestazione da utilizzare in un secondo momento. 

1. Aprire il file delle estensioni del criterio. Ad esempio, <em> `SocialAndLocalAccounts/` </em>.
1. Cercare l'elemento [BuildingBlocks](buildingblocks.md). Se l'elemento non esiste, aggiungerlo.
1. Individuare l'elemento [ClaimsSchema.](claimsschema.md) Se l'elemento non esiste, aggiungerlo.
1. Aggiungere city bearerToken all'elemento **ClaimsSchema.**  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>Acquisizione di un token di accesso 

È possibile ottenere un token di accesso in uno dei diversi modi seguenti: ottenendolo da un provider di [identità federato,](idp-pass-through-custom.md)chiamando un'API REST che restituisce un token di accesso, utilizzando un [flusso ROPC](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc)o utilizzando il [flusso delle credenziali client.](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)  

L'esempio seguente usa un profilo tecnico dell'API REST per effettuare una richiesta all'endpoint del token di Azure AD usando le credenziali client passate come autenticazione di base HTTP. Per configurarlo in Azure AD, vedere Piattaforma di identità Microsoft e flusso di [credenziali client OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow). Potrebbe essere necessario modificarlo per interfacciarlo con il provider di identità. 

Per ServiceUrl, sostituire il proprio-tenant-name con il nome del tenant di Azure AD. Vedere il riferimento del [profilo tecnico RESTful](restful-technical-profile.md) per tutte le opzioni disponibili.

```xml
<TechnicalProfile Id="SecureREST-AccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.microsoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://secureb2cfunction.azurewebsites.net/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>Modificare il profilo tecnico REST per usare l'autenticazione del token di connessioneChange the REST technical profile to use bearer token authentication

Per supportare l'autenticazione del token di connessione nei criteri personalizzati, modificare il profilo tecnico dell'API REST con quanto segue:

1. Aprire il file dei criteri di estensione *TrustFrameworkExtensions.xml* nella directory di lavoro.
1. Cercare il nodo `<TechnicalProfile>` che include `Id="REST-API-SignUp"`.
1. Individuare l'elemento `<Metadata>`.
1. Modificare *AuthenticationType* in *Bearer*, come segue:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Modificare o aggiungere *UseClaimAsBearerToken* a *bearerToken*, come indicato di seguito. Il *bearerToken* è il nome dell'attestazione da cui verrà recuperato il token di connessione (l'attestazione di output da `SecureREST-AccessToken`).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Assicurarsi di aggiungere l'attestazione usata in precedenza come attestazione di input:Ensure you add the claim used above as an input claim:

    ```xml
    <InputClaim ClaimTyeReferenceId="bearerToken"/>
    ```    

Dopo aver aggiunto i frammenti di codice precedenti, il profilo tecnico dovrebbe essere simile al codice XML seguente:After you add the above snippets, your technical profile should look like the following XML code:

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTyeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>Utilizzo di un portatore OAuth2 staticoUsing a static OAuth2 bearer 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>Aggiungere la chiave dei criteri del token di connessione OAuth2Add the OAuth2 bearer token policy key

Creare una chiave dei criteri per archiviare il valore del token di connessione.

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Assicurarsi di usare la directory che contiene il tenant B2C di Azure AD. Nel menu **superiore,** selezionare il filtro Directory e sottoscrizione e scegliere la directory B2C di Azure AD.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **Policy Keys**, quindi **scegliere Add**.
1. Per **Opzioni** scegliere `Manual`.
1. Immettere un **nome** per la chiave dei criteri. Ad esempio: `RestApiBearerToken`. Verrà aggiunto automaticamente il prefisso `B2C_1A_` al nome della chiave.
1. In **Segreto** immettere il segreto client registrato in precedenza.
1. In **Uso chiave** selezionare `Encryption`.
1. Selezionare **Create** (Crea).

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Configurare il profilo tecnico dell'API REST per l'utilizzo della chiave dei criteri del token di connessioneConfigure your REST API technical profile to use the bearer token policy key

Dopo aver creato la chiave necessaria, configurare i metadati del profilo tecnico dell'API REST in modo che facciano riferimento al token di connessione.

1. Aprire il file dei criteri di estensione (TrustFrameworkExtensions.xml) nella directory di lavoro.
1. Cercare il profilo tecnico dell'API REST. ad esempio `REST-ValidateProfile` o `REST-GetProfile`.
1. Individuare l'elemento `<Metadata>`.
1. Impostare *AuthenticationType* su `Bearer`.
1. Impostare *AllowInsecureAuthInProduction* su `false`.
1. Aggiungere il frammento XML seguente subito dopo l'elemento `</Metadata>` di chiusura:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

Di seguito è riportato un esempio di profilo tecnico RESTful configurato con l'autenticazione del token di connessione:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sull'elemento [del profilo tecnico Restful](restful-technical-profile.md) sono riportate nel riferimento IEF. 
