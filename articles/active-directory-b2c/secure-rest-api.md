---
title: Proteggere un servizio RESTful nel Azure AD B2C
titleSuffix: Azure AD B2C
description: Proteggere gli scambi di attestazioni dell'API REST personalizzati nel Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 34ed6d043f713aa55bfe464c48d4332364df805d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680370"
---
# <a name="secure-your-restful-services"></a>Proteggere i servizi RESTful 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Quando si integra un'API REST in un percorso utente di Azure AD B2C, è necessario proteggere l'endpoint dell'API REST con l'autenticazione di. In questo modo si garantisce che solo i servizi con credenziali appropriate, ad esempio Azure AD B2C, possano effettuare chiamate all'endpoint dell'API REST.

Informazioni su come integrare un'API REST nel percorso utente Azure AD B2C negli articoli [convalidare l'input dell'utente](custom-policy-rest-api-claims-validation.md) e [aggiungere gli scambi di attestazioni API REST a criteri personalizzati](custom-policy-rest-api-claims-exchange.md) .

Questo articolo illustra come proteggere l'API REST con l'autenticazione HTTP Basic, client certificate o OAuth2. 

## <a name="prerequisites"></a>Prerequisiti

Completare i passaggi in una delle guide seguenti:

- [Integrare scambi di attestazioni API REST nel percorso utente Azure ad B2C per convalidare l'input dell'utente](custom-policy-rest-api-claims-validation.md).
- [Aggiungere scambi di attestazioni API REST a criteri personalizzati](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>Autenticazione di base HTTP

L'autenticazione di base HTTP è definita nella [specifica RFC 2617](https://tools.ietf.org/html/rfc2617). L'autenticazione di base funziona nel modo seguente: Azure AD B2C invia una richiesta HTTP con le credenziali client nell'intestazione dell'autorizzazione. Le credenziali vengono formattate come stringa con codifica Base64 "nome: password".  

### <a name="add-rest-api-username-and-password-policy-keys"></a>Aggiungere le chiavi dei criteri nome utente e password dell'API REST

Per configurare un profilo tecnico dell'API REST con l'autenticazione di base HTTP, creare le seguenti chiavi crittografiche per archiviare il nome utente e la password:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Assicurarsi di usare la directory che contiene il tenant del Azure AD B2C. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e scegliere la directory Azure ad B2C.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **chiavi dei criteri**, quindi selezionare **Aggiungi**.
1. Selezionare **Manuale** in **Opzioni**.
1. Per **nome**digitare **RestApiUsername**.
    È possibile che il prefisso *B2C_1A_* venga aggiunto automaticamente.
1. Nella casella **Secret (segreto** ) immettere il nome utente dell'API REST.
1. In **Uso chiave**selezionare **Crittografia**.
1. Selezionare **Crea**.
1. Selezionare di nuovo **chiavi dei criteri** .
1. Selezionare **Aggiungi**.
1. Selezionare **Manuale** in **Opzioni**.
1. Per **nome**digitare **RestApiPassword**.
    È possibile che il prefisso *B2C_1A_* venga aggiunto automaticamente.
1. Nella casella **Secret (segreto** ) immettere la password dell'API REST.
1. In **Uso chiave**selezionare **Crittografia**.
1. Selezionare **Crea**.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>Configurare il profilo tecnico dell'API REST per l'uso dell'autenticazione di base HTTP

Dopo aver creato le chiavi necessarie, configurare i metadati del profilo tecnico dell'API REST per fare riferimento alle credenziali.

1. Aprire il file dei criteri di estensione (TrustFrameworkExtensions.xml) nella directory di lavoro.
1. Cercare il profilo tecnico dell'API REST. ad esempio `REST-ValidateProfile` o `REST-GetProfile`.
1. Individuare l'elemento `<Metadata>`.
1. Modificare il *AuthenticationType* in `Basic`.
1. Modificare il *AllowInsecureAuthInProduction* in `false`.
1. Aggiungere il frammento XML seguente subito dopo l'elemento `</Metadata>` di chiusura:
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

Di seguito è riportato un esempio di un profilo tecnico RESTful configurato con l'autenticazione di base HTTP:

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

L'autenticazione del certificato client è un'autenticazione reciproca basata su certificati, in cui il client Azure AD B2C, fornisce il certificato client al server per dimostrare la propria identità. Ciò avviene come parte dell'handshake SSL. Solo i servizi con certificati appropriati, ad esempio Azure AD B2C, possono accedere al servizio API REST. Il certificato client è un certificato digitale X. 509. Negli ambienti di produzione deve essere firmato da un'autorità di certificazione.

### <a name="prepare-a-self-signed-certificate-optional"></a>Preparare un certificato autofirmato (facoltativo)

Per gli ambienti non di produzione, se non si dispone già di un certificato, è possibile usare un certificato autofirmato. In Windows è possibile usare il cmdlet [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) di PowerShell per generare un certificato.

1. Eseguire questo comando di PowerShell per generare un certificato autofirmato. Modificare l' `-Subject` argomento in modo appropriato per l'applicazione e Azure ad B2C nome del tenant. È anche possibile modificare la `-NotAfter` data per specificare una scadenza diversa per il certificato.
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
1. Aprire **Gestisci certificati utente** > **attuali** > **Personal** > **certificati** > personali*YourAppName.yourtenant.onmicrosoft.com*.
1. Selezionare il certificato > **azione** > **All Tasks** > **Esporta**tutte le attività.
1. Selezionare **Sì** > **Avanti** > Sì **, Esporta la chiave** > privata**Avanti**.
1. Accettare le impostazioni predefinite per il **formato del file di esportazione**.
1. Specificare una password per il certificato.

### <a name="add-a-client-certificate-policy-key"></a>Aggiungere una chiave dei criteri del certificato client

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Assicurarsi di usare la directory che contiene il tenant del Azure AD B2C. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e scegliere la directory Azure ad B2C.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **chiavi dei criteri**, quindi selezionare **Aggiungi**.
1. Nella casella **Opzioni** selezionare **Carica**.
1. Nella casella **nome** digitare **RestApiClientCertificate**.
    Il prefisso *B2C_1A_* viene aggiunto automaticamente.
1. Nella casella **Caricamento file** selezionare il file del certificato con estensione pfx con una chiave privata.
1. Nella casella **Password** digitare la password del certificato.
1. Selezionare **Crea**.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Configurare il profilo tecnico dell'API REST per l'uso dell'autenticazione del certificato client

Dopo aver creato la chiave necessaria, configurare i metadati del profilo tecnico dell'API REST per fare riferimento al certificato client.

1. Aprire il file dei criteri di estensione (TrustFrameworkExtensions.xml) nella directory di lavoro.
1. Cercare il profilo tecnico dell'API REST. ad esempio `REST-ValidateProfile` o `REST-GetProfile`.
1. Individuare l'elemento `<Metadata>`.
1. Modificare il *AuthenticationType* in `ClientCertificate`.
1. Modificare il *AllowInsecureAuthInProduction* in `false`.
1. Aggiungere il frammento XML seguente subito dopo l'elemento `</Metadata>` di chiusura:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

Di seguito è riportato un esempio di un profilo tecnico RESTful configurato con un certificato client HTTP:

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

## <a name="oauth2-bearer-authentication"></a>Autenticazione di OAuth2 Bearer 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

L'autenticazione del token di gestione è definita nel [Framework di autorizzazione OAuth 2.0: utilizzo token di porta (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). In bearer token Authentication Azure AD B2C invia una richiesta HTTP con un token nell'intestazione Authorization.

```http
Authorization: Bearer <token>
```

Una bearer token è una stringa opaca. Può essere un token di accesso JWT o qualsiasi stringa che l'API REST prevede Azure AD B2C inviare nell'intestazione dell'autorizzazione. Azure AD B2C supporta i tipi seguenti:

- **Token di porta**. Per essere in grado di inviare il bearer token nel profilo tecnico RESTful, è necessario che i criteri acquisiscano prima il bearer token e quindi lo usino nel profilo tecnico RESTful.  
- **Bearer token statico**. Usare questo approccio quando l'API REST rilascia un token di accesso a lungo termine. Per usare un bearer token statico, creare una chiave dei criteri e creare un riferimento dal profilo tecnico RESTful alla chiave del criterio. 


## <a name="using-oauth2-bearer"></a>Uso di OAuth2 Bearer  

I passaggi seguenti illustrano come usare le credenziali client per ottenere un bearer token e passarlo nell'intestazione dell'autorizzazione delle chiamate all'API REST.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Definire un'attestazione per archiviare il bearer token

Un'attestazione fornisce l'archiviazione temporanea dei dati durante l'esecuzione di un Azure AD B2C criteri. Lo [schema delle attestazioni](claimsschema.md) è il punto in cui vengono dichiarate le attestazioni. Il token di accesso deve essere archiviato in un'attestazione da usare in un secondo momento. 

1. Aprire il file delle estensioni dei criteri. Ad esempio, <em> `SocialAndLocalAccounts/` </em>.
1. Cercare l'elemento [BuildingBlocks](buildingblocks.md). Se l'elemento non esiste, aggiungerlo.
1. Individuare l'elemento [ClaimsSchema](claimsschema.md) . Se l'elemento non esiste, aggiungerlo.
1. Aggiungere le attestazioni seguenti all'elemento **ClaimsSchema** .  

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

È possibile ottenere un token di accesso in uno dei modi seguenti: ottenendolo [da un provider di identità federato](idp-pass-through-custom.md), chiamando un'API REST che restituisce un token di accesso, usando un [flusso ROPC](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc)o il [flusso di credenziali client](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow).  

Nell'esempio seguente viene usato un profilo tecnico dell'API REST per effettuare una richiesta all'endpoint del token Azure AD usando le credenziali client passate come autenticazione di base HTTP. Per configurarlo in Azure AD, vedere [Microsoft Identity Platform e il flusso di credenziali client OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow). Potrebbe essere necessario modificarlo per interfacciarsi con il provider di identità. 

Per ServiceUrl, sostituire-tenant-Name con il nome del tenant Azure AD. Vedere le informazioni di riferimento sul [profilo tecnico RESTful](restful-technical-profile.md) per tutte le opzioni disponibili.

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

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>Modificare il profilo tecnico REST per usare l'autenticazione bearer token

Per supportare l'autenticazione bearer token nei criteri personalizzati, modificare il profilo tecnico dell'API REST con il codice seguente:

1. Aprire il file dei criteri di estensione *TrustFrameworkExtensions.xml* nella directory di lavoro.
1. Cercare il nodo `<TechnicalProfile>` che include `Id="REST-API-SignUp"`.
1. Individuare l'elemento `<Metadata>`.
1. Modificare il *AuthenticationType* in *Bearer*, come indicato di seguito:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Modificare o aggiungere *UseClaimAsBearerToken* a *bearerToken*, come indicato di seguito. *BearerToken* è il nome dell'attestazione da cui verrà recuperato il Bearer token (l'attestazione di output da `SecureREST-AccessToken`).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Assicurarsi di aggiungere l'attestazione usata in precedenza come attestazione di input:

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

Una volta aggiunti i frammenti precedenti, il profilo tecnico dovrebbe essere simile al codice XML seguente:

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

## <a name="using-a-static-oauth2-bearer"></a>Uso di un portatore OAuth2 statico 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>Aggiungere la chiave dei criteri bearer token OAuth2

Creare una chiave dei criteri per archiviare il valore bearer token.

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Assicurarsi di usare la directory che contiene il tenant del Azure AD B2C. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e scegliere la directory Azure ad B2C.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **chiavi dei criteri**, quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere `Manual`.
1. Immettere un **nome** per la chiave dei criteri. Ad esempio: `RestApiBearerToken`. Verrà aggiunto automaticamente il prefisso `B2C_1A_` al nome della chiave.
1. In **Segreto** immettere il segreto client registrato in precedenza.
1. In **Uso chiave** selezionare `Encryption`.
1. Selezionare **Crea**.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Configurare il profilo tecnico dell'API REST per usare la chiave dei criteri di bearer token

Dopo aver creato la chiave necessaria, configurare i metadati del profilo tecnico dell'API REST per fare riferimento al bearer token.

1. Aprire il file dei criteri di estensione (TrustFrameworkExtensions.xml) nella directory di lavoro.
1. Cercare il profilo tecnico dell'API REST. ad esempio `REST-ValidateProfile` o `REST-GetProfile`.
1. Individuare l'elemento `<Metadata>`.
1. Modificare il *AuthenticationType* in `Bearer`.
1. Modificare il *AllowInsecureAuthInProduction* in `false`.
1. Aggiungere il frammento XML seguente subito dopo l'elemento `</Metadata>` di chiusura:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

Di seguito è riportato un esempio di un profilo tecnico RESTful configurato con l'autenticazione bearer token:

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

- Altre informazioni sull'elemento [profilo tecnico RESTful](restful-technical-profile.md) sono disponibili nella Guida di riferimento a Framework dell'esperienza. 
