---
title: Definire un profilo tecnico per l'hint ID token in un criterio personalizzato
titleSuffix: Azure AD B2C
description: Definire un profilo tecnico hint ID token in un criterio personalizzato in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 708ec35524f25314ca568944b738ba2cdf60d55c
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132075"
---
# <a name="define-an-id-token-hint-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definire un profilo tecnico per l'hint ID token in un Azure Active Directory B2C criteri personalizzati

Azure AD B2C consente alle applicazioni relying party di inviare un JWT in ingresso come parte della richiesta di autorizzazione OAuth2. Il token JWT può essere emesso da un'applicazione relying party o da un provider di identità e può passare un suggerimento sull'utente o la richiesta di autorizzazione. Azure AD B2C convalida la firma, il nome dell'autorità emittente e il destinatario del token ed estrae l'attestazione dal token in ingresso.

## <a name="use-cases"></a>Casi d'uso

È possibile usare questa soluzione per inviare dati a Azure AD B2C incapsulati in un singolo token JWT. La [soluzione di iscrizione con invito tramite posta elettronica](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md), in cui l'amministratore del sistema può inviare un invito firmato agli utenti, si basa su id_token_hint. Solo gli utenti con accesso al messaggio di posta elettronica di invito possono creare l'account nella directory.

## <a name="token-signing-approach"></a>Approccio per la firma di token

Con id_token_hint, l'emittente del token (un'app relying party o un provider di identità) compone il token e quindi lo firma usando una chiave di firma per dimostrare che il token deriva da un'origine attendibile. La chiave di firma può essere simmetrica o asimmetrica. La crittografia simmetrica o la crittografia a chiave privata utilizza un segreto condiviso per firmare e convalidare la firma. La crittografia asimmetrica o la crittografia a chiave pubblica è un sistema crittografico che usa sia una chiave privata che una chiave pubblica. La chiave privata è nota solo all'emittente del token e viene utilizzata per firmare il token. La chiave pubblica viene condivisa con i criteri di Azure AD B2C per convalidare la firma del token.

## <a name="token-format"></a>Formato token

Il id_token_hint deve essere un token JWT valido. Nella tabella seguente sono elencate le attestazioni obbligatorie. Altre attestazioni sono facoltative.

| Nome | Attestazione | Valore di esempio | Descrizione |
| ---- | ----- | ------------- | ----------- |
| Destinatari | `aud` | `a489fc44-3cc0-4a78-92f6-e413cd853eae` | Identifica il destinatario del token. Si tratta di una stringa arbitraria definita dall'emittente del token. Azure AD B2C convalida questo valore e rifiuta il token se non corrisponde.  |
| Issuer | `iss` |`https://localhost` | Identifica il servizio token di sicurezza (emittente del token). Si tratta di un URI arbitrario definito dall'emittente del token. Azure AD B2C convalida questo valore e rifiuta il token se non corrisponde.  |
| Scadenza | `exp` | `1600087315` | Ora in cui il token non è più valido, rappresentata dal valore epoch time. Azure AD B2C non convalida questa attestazione. |
| Non prima | `nbf` | `1599482515` | L’ora in cui il token diventa valido, rappresentata dal valore epoch time. Equivale in genere all'ora di rilascio del token. Azure AD B2C non convalida questa attestazione. |

 Il token seguente è un esempio di token ID valido:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "displayName": " John Smith",
  "userId": "john.s@contoso.com",
  "nbf": 1599482515,
  "exp": 1600087315,
  "iss": "https://localhost",
  "aud": "a489fc44-3cc0-4a78-92f6-e413cd853eae"
}
```

## <a name="protocol"></a>Protocollo

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `None`. Il protocollo per il **IdTokenHint_ExtractClaims** profilo tecnico è ad esempio `None` :

```xml
<TechnicalProfile Id="IdTokenHint_ExtractClaims">
  <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
  <Protocol Name="None" />
  ...
```

Il profilo tecnico viene chiamato da un passaggio di orchestrazione con tipo `GetClaims` .

```xml
<OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
``` 

## <a name="output-claims"></a>Attestazioni di output

L'elemento **OutputClaims** contiene un elenco di attestazioni da estrarre dal token JWT. Potrebbe essere necessario eseguire il mapping del nome dell'attestazione definita nei criteri al nome definito nel token JWT. È anche possibile includere le attestazioni che non vengono restituite dal token JWT, purché sia stato impostato l' `DefaultValue` attributo.

## <a name="metadata"></a>Metadati

Quando si utilizza una chiave simmetrica, i metadati seguenti sono rilevanti. 

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| autorità di certificazione | Sì | Identifica il servizio token di sicurezza (emittente del token). Questo valore deve essere identico all' `iss` attestazione nell'attestazione del token JWT. | 
| IdTokenAudience | Sì | Identifica il destinatario del token. Deve essere identica all' `aud` attestazione con l'attestazione del token JWT. | 

Quando si utilizza una chiave asimmetrica, i metadati seguenti sono rilevanti. 

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| METADATI| Sì | URL che punta a un documento di configurazione dell'emittente del token, noto anche come endpoint di configurazione OpenID noto.   |
| autorità di certificazione | No | Identifica il servizio token di sicurezza (emittente del token). Questo valore può essere usato per sovrascrivere il valore configurato nei metadati e deve essere identico all' `iss` attestazione nell'attestazione del token JWT. |  
| IdTokenAudience | No | Identifica il destinatario del token. Deve essere identica all' `aud` attestazione con l'attestazione del token JWT. |  

## <a name="cryptographic-keys"></a>Chiavi di crittografia

Quando si usa una chiave simmetrica, l'elemento **CryptographicKeys** contiene l'attributo seguente:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| client_secret | Sì | Chiave crittografica utilizzata per convalidare la firma del token JWT.|


## <a name="how-to-guide"></a>Guida pratica

### <a name="issue-a-token-with-symmetric-keys"></a>Emettere un token con chiavi simmetriche

#### <a name="step-1-create-a-shared-key"></a>Passaggio 1. Creare una chiave condivisa 

Creare una chiave che può essere usata per firmare il token. Usare, ad esempio, il codice PowerShell seguente per generare una chiave.

```powershell
$bytes = New-Object Byte[] 32
$rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
$rand.GetBytes($bytes)
$rand.Dispose()
$newClientSecret = [System.Convert]::ToBase64String($bytes)
$newClientSecret
```

Questo codice crea una stringa segreta come `VK62QTn0m1hMcn0DQ3RPYDAr6yIiSvYgdRwjZtU5QhI=` .

#### <a name="step-2-add-the-signing-key-to-azure-ad-b2c"></a>Passaggio 2: Aggiungere la chiave di firma a Azure AD B2C

La stessa chiave usata dall'emittente del token deve essere creata nelle chiavi dei criteri Azure AD B2C.  

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. Nella pagina Panoramica, nella sezione **Criteri**, selezionare **Framework dell'esperienza di gestione delle identità**.
1. Selezionare le **chiavi dei criteri** 
1. Selezionare **manuale**.
1. Per **Nome** usare `IdTokenHintKey`.  
   È possibile che il prefisso `B2C_1A_` venga aggiunto automaticamente.
1. Nella casella **Secret (segreto** ) immettere la chiave di accesso generata in precedenza.
1. Per **Uso chiave** usare **Crittografia**.
1. Selezionare **Crea**.
1. Confermare di avere creato la chiave `B2C_1A_IdTokenHintKey`.


#### <a name="step-3-add-the-id-token-hint-technical-profile"></a>Passaggio 3. Aggiungere il profilo tecnico hint ID token

Il profilo tecnico seguente convalida il token ed estrae le attestazioni. 

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <Item Key="IdTokenAudience">a489fc44-3cc0-4a78-92f6-e413cd853eae</Item>
        <Item Key="issuer">https://localhost</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_IdTokenHintKey" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-4-prepare-your-policy"></a>Passaggio 4. Preparare i criteri

Completare il passaggio [configurare il criterio](#configure-your-policy) .

#### <a name="step-5-prepare-the-code"></a>Passaggio 5. Preparare il codice  

L' [esempio GitHub](https://github.com/azure-ad-b2c/id_token_hint/tree/master/dotnet_core_symmetric_key) è un'applicazione Web ASP.NET e un'app console che genera un token ID firmato con una chiave simmetrica. 


### <a name="issue-a-token-with-asymmetric-keys"></a>Emettere un token con chiavi asimmetriche

Con una chiave asimmetrica, il token viene firmato usando i certificati RSA. Questa applicazione ospita un endpoint dei metadati Open ID Connect e un endpoint di chiavi Web JSON (JWKs) che viene usato da Azure AD B2C per convalidare la firma del token ID.

L'emittente del token deve fornire gli endpoint seguenti:

* `/.well-known/openid-configuration` -Un endpoint di configurazione noto con informazioni rilevanti sul token, ad esempio il nome dell'autorità emittente del token e il collegamento all'endpoint JWK. 
* `/.well-known/keys` : il punto finale della chiave Web JSON (JWK) con la chiave pubblica usata per firmare la chiave (con la parte della chiave privata del certificato).

Vedere l'esempio [TokenMetadataController.cs](https://github.com/azure-ad-b2c/id-token-builder/blob/master/source-code/B2CIdTokenBuilder/Controllers/TokenMetadataController.cs) .NET MVC controller.

#### <a name="step-1-prepare-a-self-signed-certificate"></a>Passaggio 1. Preparare un certificato autofirmato

Se non si dispone già di un certificato, è possibile utilizzare un certificato autofirmato per questa guida. In Windows è possibile usare il cmdlet [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) di PowerShell per generare un certificato.

Eseguire questo comando di PowerShell per generare un certificato autofirmato. Modificare l'argomento `-Subject` in base al nome dell'applicazione e del tenant Azure AD B2C. Si può anche modificare la data `-NotAfter` per specificare una scadenza diversa per il certificato.

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


#### <a name="step-2-add-the-id-token-hint-technical-profile"></a>Passaggio 2: Aggiungere il profilo tecnico hint ID token 

Il profilo tecnico seguente convalida il token ed estrae le attestazioni. Modificare l'URI dei metadati nell'endpoint di configurazione noto dell'emittente del token.  

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <!-- Replace with your endpoint location -->
        <Item Key="METADATA">https://your-app.azurewebsites.net/.well-known/openid-configuration</Item>
        <Item Key="IdTokenAudience">your_optional_audience</Item> -->
        <!-- <Item Key="issuer">your_optional_token_issuer_override</Item> -->
      </Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-3-prepare-your-policy"></a>Passaggio 3. Preparare i criteri

Completare il passaggio [configurare il criterio](#configure-your-policy) .

#### <a name="step-4-prepare-the-code"></a>Passaggio 4. Preparare il codice 

Questa applicazione Web di esempio ASP.NET di [GitHub](https://github.com/azure-ad-b2c/id-token-builder) genera token ID e ospita gli endpoint dei metadati necessari per usare il parametro "id_token_hint" Azure ad B2C.


### <a name="configure-your-policy"></a>Configurare i criteri

Per gli approcci simmetrici e asimmetrici, il `id_token_hint` profilo tecnico viene chiamato da un passaggio di orchestrazione con tipo di `GetClaims` ed è necessario specificare le attestazioni di input del criterio di relying party.

1. Aggiungere il profilo tecnico IdTokenHint_ExtractClaims ai criteri di estensione.
1. Aggiungere il passaggio di orchestrazione seguente al percorso utente come primo elemento.  

    ```xml
    <OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
    ``` 
1. Nel criterio di relying party, ripetere le stesse attestazioni di input configurate nel profilo tecnico IdTokenHint_ExtractClaims. Ad esempio:
    ```xml
   <RelyingParty>
     <DefaultUserJourney ReferenceId="SignUp" />
     <TechnicalProfile Id="PolicyProfile">
       <DisplayName>PolicyProfile</DisplayName>
       <Protocol Name="OpenIdConnect" />
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
        </InputClaims>
       <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
       </OutputClaims>
       <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

A seconda dei requisiti aziendali, potrebbe essere necessario aggiungere convalide di token, ad esempio per controllare la scadenza del token, il formato dell'indirizzo di posta elettronica e altro ancora. A tale scopo, aggiungere i passaggi dell'orchestrazione che richiamano un [profilo tecnico della trasformazione delle attestazioni](claims-transformation-technical-profile.md). Aggiungere inoltre un [profilo tecnico autocertificato](self-asserted-technical-profile.md) per presentare un messaggio di errore. 

### <a name="create-and-sign-a-token"></a>Creare e firmare un token

Gli esempi di GitHub illustrano come creare un token di questo tipo per emettere un JWT che in un secondo momento viene inviato come `id_token_hint` parametro della stringa di query. Di seguito è riportato un esempio di una richiesta di autorizzazione con id_token_hint parametro
 
```html
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize?client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&id_token_hint=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkaXNwbGF5TmFtZSI6IiBKb2huIFNtaXRoIiwidXNlcklkIjoiam9obi5zQGNvbnRvc28uY29tIiwibmJmIjoxNTk5NDgyNTE1LCJleHAiOjE2MDAwODczMTUsImlzcyI6Imh0dHBzOi8vbG9jYWxob3N0IiwiYXVkIjoiYTQ4OWZjNDQtM2NjMC00YTc4LTkyZjYtZTQxM2NkODUzZWFlIn0.nPmLXydI83PQCk5lRBYUZRu_aX58pL1khahHyQuupig
```

## <a name="next-steps"></a>Passaggi successivi

- Controllare la soluzione [di iscrizione con invito tramite posta elettronica](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md) nel repository GitHub della community Azure ad B2C.
