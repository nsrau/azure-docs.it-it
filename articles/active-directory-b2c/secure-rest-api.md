---
title: Proteggere un servizio RESTful in Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Proteggere gli scambi di attestazioni di API REST personalizzate in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1f1897a4f58276bbac2a7de673544e592a562562
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826673"
---
# <a name="secure-your-restful-services"></a>Proteggere i servizi RESTful 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Quando si integra un'API REST in un percorso utente di Azure AD B2C, è necessario proteggere l'endpoint dell'API REST con l'autenticazione. In questo modo si garantisce che solo i servizi con credenziali appropriate, come Azure AD B2C, possano effettuare chiamate all'endpoint dell'API REST.

Per informazioni su come integrare un'API REST nel percorso utente di Azure AD B2C, vedere gli articoli relativi alla [convalida dell'input utente](custom-policy-rest-api-claims-validation.md) e all'[aggiunta di scambi di attestazioni di API REST ai criteri personalizzati](custom-policy-rest-api-claims-exchange.md).

Questo articolo illustra come proteggere l'API REST con l'autenticazione HTTP di base, basata su certificato client o OAuth2. 

## <a name="prerequisites"></a>Prerequisiti

Completare le procedure descritte in una delle guide pratiche seguenti:

- [Integrare scambi di attestazioni API REST nel percorso utente di Azure AD B2C come convalida dell'input utente](custom-policy-rest-api-claims-validation.md)
- [Aggiungere scambi di attestazioni API REST ai criteri personalizzati](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>Autenticazione HTTP di base

L'autenticazione HTTP di base è definita in [RFC 2617](https://tools.ietf.org/html/rfc2617). L'autenticazione di base funziona nel modo seguente: Azure AD B2C invia una richiesta HTTP con le credenziali client nell'intestazione dell'autorizzazione. Le credenziali vengono formattate come stringa con codifica Base64 "nome:password".  

### <a name="add-rest-api-username-and-password-policy-keys"></a>Aggiungere le chiavi dei criteri di nome utente e password dell'API REST

Per configurare un profilo tecnico dell'API REST con l'autenticazione HTTP di base, creare le seguenti chiavi crittografiche in cui archiviare il nome utente e la password:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory Azure AD B2C.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Selezionare **Manuale** in **Opzioni**.
1. In **Nome** digitare **RestApiUsername**.
    È possibile che il prefisso *B2C_1A_* venga aggiunto automaticamente.
1. Nella casella **Segreto** immettere il nome utente dell'API REST.
1. In **Uso chiave**selezionare **Crittografia**.
1. Selezionare **Create** (Crea).
1. Selezionare di nuovo **Chiavi dei criteri**.
1. Selezionare **Aggiungi**.
1. Selezionare **Manuale** in **Opzioni**.
1. In **Nome** digitare **RestApiPassword**.
    È possibile che il prefisso *B2C_1A_* venga aggiunto automaticamente.
1. Nella casella **Segreto** immettere la password dell'API REST.
1. In **Uso chiave**selezionare **Crittografia**.
1. Selezionare **Create** (Crea).

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>Configurare il profilo tecnico dell'API REST per l'uso dell'autenticazione HTTP di base

Dopo aver creato le chiavi necessarie, configurare i metadati del profilo tecnico dell'API REST in modo da fare riferimento alle credenziali.

1. Aprire il file dei criteri di estensione (TrustFrameworkExtensions.xml) nella directory di lavoro.
1. Cercare il profilo tecnico dell'API REST. Può essere ad esempio `REST-ValidateProfile` o `REST-GetProfile`.
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

Di seguito è riportato un esempio di profilo tecnico RESTful configurato con l'autenticazione HTTP di base:

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

## <a name="https-client-certificate-authentication"></a>Autenticazione HTTPS con certificato client

L'autenticazione con certificato client è un'autenticazione reciproca basata su certificati, in cui il client, ossia Azure AD B2C, fornisce il certificato client al server per dimostrare la propria identità. Questo avviene nell'ambito dell'handshake SSL. Solo i servizi con i certificati appropriati, come Azure AD B2C, possono accedere al servizio API REST. Il certificato client è un certificato digitale X.509. Negli ambienti di produzione deve essere firmato da un'autorità di certificazione.

### <a name="prepare-a-self-signed-certificate-optional"></a>Preparare un certificato autofirmato (facoltativo)

Per gli ambienti non di produzione, se non si ha già un certificato è possibile usarne uno autofirmato. In Windows è possibile usare il cmdlet [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) di PowerShell per generare un certificato.

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
1. Aprire **Gestire i certificati utente** > **Utente corrente** > **Personale** > **Certificati** > *nomeapp.nometenant.onmicrosoft.com*.
1. Selezionare il certificato > **Azione** > **Tutte le attività** > **Esporta**.
1. Selezionare **Sì** > **Avanti** > **Sì, esporta la chiave privata** > **Avanti**.
1. Accettare le impostazioni predefinite di **Formato file di esportazione**.
1. Specificare una password per il certificato.

### <a name="add-a-client-certificate-policy-key"></a>Aggiungere una chiave dei criteri del certificato client

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory Azure AD B2C.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Nella casella **Opzioni** selezionare **Carica**.
1. Nella casella **Nome** digitare **RestApiClientCertificate**.
    Il prefisso *B2C_1A_* viene aggiunto automaticamente.
1. Nella casella **Caricamento file** selezionare il file del certificato con estensione pfx con una chiave privata.
1. Nella casella **Password** digitare la password del certificato.
1. Selezionare **Create** (Crea).

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Configurare il profilo tecnico dell'API REST per l'uso dell'autenticazione con certificato client

Dopo aver creato le chiavi necessarie, configurare i metadati del profilo tecnico dell'API REST in modo da fare riferimento al certificato client.

1. Aprire il file dei criteri di estensione (TrustFrameworkExtensions.xml) nella directory di lavoro.
1. Cercare il profilo tecnico dell'API REST. Può essere ad esempio `REST-ValidateProfile` o `REST-GetProfile`.
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

## <a name="oauth2-bearer-authentication"></a>Autenticazione basata su token di connessione OAuth2 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

L'autenticazione basata su token di connessione è definita in [OAuth2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) (Framework di autorizzazione di OAuth 2.0: uso dei bearer token - RFC 6750). Nell'autenticazione basata su token di connessione Azure AD B2C invia una richiesta HTTP con un token nell'intestazione dell'autorizzazione.

```http
Authorization: Bearer <token>
```

Un token di connessione è una stringa opaca. Può essere un token di accesso JWT o qualsiasi stringa che l'API REST prevede venga inviata da Azure AD B2C nell'intestazione dell'autorizzazione. Azure AD B2C supporta i tipi seguenti:

- **Token di connessione**. Per essere in grado di inviare il token di connessione nel profilo tecnico RESTful, i criteri devono prima acquisire il token di connessione e quindi usarlo nel profilo tecnico RESTful.  
- **Token di connessione statico**. Usare questo approccio quando l'API REST rilascia un token di accesso a lungo termine. Per usare un token di connessione statico, creare una chiave dei criteri e quindi creare un riferimento dal profilo tecnico RESTful alla chiave dei criteri. 


## <a name="using-oauth2-bearer"></a>Uso della connessione OAuth2  

I passaggi seguenti illustrano come usare le credenziali client per ottenere un token di connessione e passarlo nell'intestazione dell'autorizzazione delle chiamate API REST.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Definire un'attestazione in cui archiviare il token di connessione

Un'attestazione fornisce un'archiviazione temporanea dei dati durante l'esecuzione dei criteri di Azure AD B2C. Lo [schema di attestazioni](claimsschema.md) è la posizione in cui si dichiarano le attestazioni. Il token di accesso deve essere archiviato in un'attestazione per poter essere usato in un secondo momento. 

1. Aprire il file di estensioni dei criteri, ad esempio <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Cercare l'elemento [BuildingBlocks](buildingblocks.md). Se l'elemento non esiste, aggiungerlo.
1. Individuare l'elemento [ClaimsSchema](claimsschema.md). Se l'elemento non esiste, aggiungerlo.
1. Aggiungere le attestazioni seguenti all'elemento **ClaimsSchema**.  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>Bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="grant_type">
  <DisplayName>Grant type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="scope">
  <DisplayName>scope</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>Acquisizione di un token di accesso 

È possibile ottenere un token di accesso in diversi modi: ottenendolo da un [provider di identità federato](idp-pass-through-custom.md), chiamando un'API REST che restituisce un token di accesso, usando un [flusso ROPC](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc) o usando il [flusso di credenziali client](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow).  

L'esempio seguente usa un profilo tecnico dell'API REST per effettuare una richiesta all'endpoint del token Azure AD usando le credenziali client passate come autenticazione HTTP di base. Per informazioni su come eseguire la configurazione in Azure AD, vedere [Microsoft Identity Platform e il flusso di credenziali client OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow). Potrebbe essere necessario apportare alcune modifiche per consentire l'interazione con il provider di identità. 

Per ServiceUrl, sostituire your-tenant-name con il nome del tenant di Azure AD. Per informazioni su tutte le opzioni disponibili, vedere [Definire un profilo tecnico RESTful nei criteri personalizzati di Azure Active Directory B2C](restful-technical-profile.md).

```xml
<TechnicalProfile Id="SecureREST-AccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token</Item>
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

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>Modificare il profilo tecnico RESTful in modo da usare l'autenticazione basata su token di connessione

Per supportare l'autenticazione basata su token di connessione nei criteri personalizzati, modificare il profilo tecnico dell'API REST nel modo seguente:

1. Aprire il file dei criteri di estensione *TrustFrameworkExtensions.xml* nella directory di lavoro.
1. Cercare il nodo `<TechnicalProfile>` che include `Id="REST-API-SignUp"`.
1. Individuare l'elemento `<Metadata>`.
1. Impostare *AuthenticationType* su *Bearer*, come segue:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Modificare o aggiungere *UseClaimAsBearerToken* in *bearerToken*, come segue. *bearerToken* è il nome dell'attestazione da cui verrà recuperato il token di connessione (l'attestazione di output da `SecureREST-AccessToken`).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Assicurarsi di aggiungere l'attestazione usata in precedenza come attestazione di input:

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

Dopo aver aggiunto i frammenti di codice riportati sopra, il profilo tecnico dovrebbe essere simile al codice XML seguente:

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
        <InputClaim ClaimTypeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>Uso di una connessione OAuth2 statica 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>Aggiungere la chiave dei criteri del token di connessione OAuth2

Creare una chiave dei criteri in cui archiviare il valore del token di connessione.

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory Azure AD B2C.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere `Manual`.
1. Immettere un **nome** per la chiave dei criteri. Ad esempio: `RestApiBearerToken`. Verrà aggiunto automaticamente il prefisso `B2C_1A_` al nome della chiave.
1. In **Segreto** immettere il segreto client registrato in precedenza.
1. In **Uso chiave** selezionare `Encryption`.
1. Selezionare **Create** (Crea).

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Configurare il profilo tecnico dell'API REST per l'uso della chiave dei criteri del token di connessione

Dopo aver creato le chiavi necessarie, configurare i metadati del profilo tecnico dell'API REST in modo da fare riferimento al token di connessione.

1. Aprire il file dei criteri di estensione (TrustFrameworkExtensions.xml) nella directory di lavoro.
1. Cercare il profilo tecnico dell'API REST. Può essere ad esempio `REST-ValidateProfile` o `REST-GetProfile`.
1. Individuare l'elemento `<Metadata>`.
1. Impostare *AuthenticationType* su `Bearer`.
1. Impostare *AllowInsecureAuthInProduction* su `false`.
1. Aggiungere il frammento XML seguente subito dopo l'elemento `</Metadata>` di chiusura:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

Di seguito è riportato un esempio di profilo tecnico RESTful configurato con l'autenticazione basata su token di connessione:

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

- Altre informazioni sul [profilo tecnico Restful](restful-technical-profile.md) sono disponibili nella documentazione di riferimento di Identity Experience Framework. 
