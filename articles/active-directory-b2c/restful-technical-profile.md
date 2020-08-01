---
title: Definire un profilo tecnico RESTful in un criterio personalizzato
titleSuffix: Azure AD B2C
description: Definire un profilo tecnico RESTful nei criteri personalizzati di Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1875c9147f62619d8961096adb6a0f3986496b41
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87459442"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definire un profilo tecnico RESTful nei criteri personalizzati di Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornisce il supporto per l'integrazione di un servizio RESTful. Azure AD B2C invia dati al servizio RESTful in una raccolta di attestazioni di input e riceve dati in una raccolta di attestazioni di output. Per altre informazioni, vedere [integrare scambi di attestazioni API REST nei criteri personalizzati Azure ad B2C](custom-policy-rest-api-intro.md).  

## <a name="protocol"></a>Protocollo

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `Proprietary`. L'attributo **gestore** deve contenere il nome completo dell'assembly del gestore di protocollo usato da Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Nell'esempio seguente viene illustrato un profilo tecnico RESTful:

```xml
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Attestazioni di input

L'elemento **InputClaims** contiene un elenco di attestazioni da inviare all'API REST. È anche possibile eseguire il mapping del nome dell'attestazione per il nome definito nell'API REST. L'esempio seguente illustra il mapping tra i criteri e l'API REST. L'attestazione **givenName** viene inviata all'API REST come **firstName**, mentre **surname** viene inviato come **lastName**. L'attestazione **messaggio di posta elettronica** viene impostata così com'è.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

L'elemento **InputClaimsTransformations** può contenere una raccolta di elementi **InputClaimsTransformation** che vengono usati per modificare le attestazioni di input o per generarne di nuove prima dell'invio all'API REST.

## <a name="send-a-json-payload"></a>Inviare un payload JSON

Il profilo tecnico dell'API REST consente di inviare un payload JSON complesso a un endpoint.

Per inviare un payload JSON complesso:

1. Compilare il payload JSON con la trasformazione delle attestazioni [GenerateJson](json-transformations.md) .
1. Nel profilo tecnico dell'API REST:
    1. Aggiungere una trasformazione delle attestazioni di input con un riferimento alla `GenerateJson` trasformazione delle attestazioni.
    1. Impostare l' `SendClaimsIn` opzione metadati su`body`
    1. Impostare l' `ClaimUsedForRequestPayload` opzione Metadata sul nome dell'attestazione contenente il payload JSON.
    1. Nell'attestazione di input aggiungere un riferimento all'attestazione di input contenente il payload JSON.

Nell'esempio seguente viene `TechnicalProfile` inviato un messaggio di posta elettronica di verifica utilizzando un servizio di posta elettronica di terze parti (in questo caso, SendGrid).

```xml
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
    <Item Key="DefaultUserMessageIfRequestFailed">Cannot process your request right now, please try again later.</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridApiKey" />
  </CryptographicKeys>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="output-claims"></a>Attestazioni di output

L'elemento **OutputClaims** contiene un elenco di attestazioni restituite dall'API REST. Potrebbe essere necessario eseguire il mapping del nome dell'attestazione definito nei criteri per il nome definito nell'API REST. È anche possibile includere le attestazioni che non vengono restituite dal provider di identità dell'API REST, purché venga impostato l'attributo `DefaultValue`.

L'elemento **OutputClaimsTransformations** può contenere una raccolta di elementi **OutputClaimsTransformation** che vengono usati per modificare le attestazioni di output o per generarne di nuove.

L'esempio seguente illustra l'attestazione restituita dall'API REST:

- L'attestazione**MembershipId** di cui viene eseguito il mapping per il nome di attestazione **loyaltyNumber**.

Il profilo tecnico restituisce anche le attestazioni che non vengono restituite dal provider di identità:

- L'attestazione **loyaltyNumberIsNew** con valore predefinito impostato su `true`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ServiceUrl | Sì | L'URL dell'endpoint API REST. |
| AuthenticationType | Sì | Tipo di autenticazione eseguita dal provider di attestazioni RESTful. I valori possibili sono: `None`, `Basic`, `Bearer` o `ClientCertificate`. Il `None` valore indica che l'API REST è anonima. Il valore`Basic` indica che l'API REST viene protetta con l'autenticazione di base HTTP. Solo gli utenti verificati, tra cui Azure AD B2C, possono accedere all'API. Il `ClientCertificate` valore (consigliato) indica che l'API REST limita l'accesso usando l'autenticazione del certificato client. Solo i servizi con i certificati appropriati, ad esempio Azure AD B2C, possono accedere all'API. Il `Bearer` valore indica che l'API REST limita l'accesso tramite il token di connessione del client OAuth2. |
| AllowInsecureAuthInProduction| No| Indica se `AuthenticationType` può essere impostato su `none` in un ambiente di produzione ( `DeploymentMode` di [TrustFrameworkPolicy](trustframeworkpolicy.md) è impostato su `Production` o non è stato specificato). Valori possibili: true o false (impostazione predefinita). |
| SendClaimsIn | No | Specifica la modalità di invio di attestazioni di input al provider di attestazioni RESTful. I valori possibili sono: `Body` (impostazione predefinita), `Form`, `Header` o `QueryString`. Il valore `Body` è l'attestazione di input che viene inviata nel corpo della richiesta in formato JSON. Il valore`Form` è l'attestazione di input che viene inviata nel corpo della richiesta nel formato valore di chiave e commerciale "&" separata. Il valore`Header` è l'attestazione di input che viene inviata nell'intestazione della richiesta. Il valore`QueryString` è l'attestazione di input che viene inviata nella stringa di query della richiesta. I verbi HTTP richiamati da ciascuno di essi sono i seguenti:<br /><ul><li>`Body`: POST</li><li>`Form`: POST</li><li>`Header`: GET</li><li>`QueryString`: GET</li></ul> |
| ClaimsFormat | No | Attualmente non usato; può essere ignorato. |
| ClaimUsedForRequestPayload| No | Nome di un'attestazione di stringa che contiene il payload da inviare all'API REST. |
| DebugMode | No | Il profilo tecnico viene eseguito in modalità debug. Valori possibili: `true` o `false` (impostazione predefinita). In modalità debug, l'API REST può restituire altre informazioni. Vedere la sezione [restituzione del messaggio di errore](#returning-validation-error-message) . |
| IncludeClaimResolvingInClaimsHandling  | No | Per le attestazioni di input e output, specifica se la [risoluzione delle attestazioni](claim-resolver-overview.md) è inclusa nel profilo tecnico. Valori possibili: `true` , o `false`   (impostazione predefinita). Se si desidera utilizzare un resolver di attestazioni nel profilo tecnico, impostare questa impostazione su `true` . |
| ResolveJsonPathsInJsonTokens  | No | Indica se il profilo tecnico risolve i percorsi JSON. Valori possibili: `true` o `false` (impostazione predefinita). Usare questi metadati per leggere i dati da un elemento JSON annidato. In un [OutputClaim](technicalprofiles.md#outputclaims)impostare sull' `PartnerClaimType` elemento JSON Path che si vuole restituire. Ad esempio: `firstName.localized` o `data.0.to.0.email` .|
| UseClaimAsBearerToken| No| Nome dell'attestazione che contiene il bearer token.|

## <a name="error-handling"></a>Gestione degli errori

I metadati seguenti possono essere usati per configurare i messaggi di errore visualizzati quando si verifica un errore nell'API REST. I messaggi di errore possono essere [localizzati](localization-string-ids.md#restful-service-error-messages).

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| DefaultUserMessageIfRequestFailed | No | Un messaggio di errore personalizzato predefinito per tutte le eccezioni dell'API REST.|
| UserMessageIfCircuitOpen | No | Messaggio di errore quando l'API REST non è raggiungibile. Se non è specificato, verrà restituito DefaultUserMessageIfRequestFailed. |
| UserMessageIfDnsResolutionFailed | No | Messaggio di errore per l'eccezione di risoluzione DNS. Se non è specificato, verrà restituito DefaultUserMessageIfRequestFailed. | 
| UserMessageIfRequestTimeout | No | Messaggio di errore quando si verifica il timeout della connessione. Se non è specificato, verrà restituito DefaultUserMessageIfRequestFailed. | 

## <a name="cryptographic-keys"></a>Chiavi di crittografia

Se il tipo di autenticazione è impostato su `None`, l'elemento **CryptographicKeys** non viene usato.

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

Se il tipo di autenticazione è impostato su `Basic`, l'elemento **CryptographicKeys** contiene i seguenti attributi:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Sì | Il nome utente usato per l'autenticazione. |
| BasicAuthenticationPassword | Sì | La password usata per l'autenticazione. |

Nell'esempio seguente viene illustrato un profilo tecnico con autenticazione di base:

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

Se il tipo di autenticazione è impostato su `ClientCertificate`, l'elemento **CryptographicKeys** contiene i seguenti attributi:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ClientCertificate | Sì | Il certificato X509 (set di chiavi RSA) da usare per l'autenticazione. |

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

Se il tipo di autenticazione è impostato su `Bearer`, l'elemento **CryptographicKeys** contiene i seguenti attributi:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| BearerAuthenticationToken | No | Token di porta OAuth 2,0. |

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_B2cRestClientAccessToken" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-validation-error-message"></a>Restituzione del messaggio di errore di convalida

L'API REST può restituire un messaggio di errore, ad esempio "Utente nel sistema CRM non trovato". Se si verifica un errore, l'API REST deve restituire un messaggio di errore HTTP 4xx, ad esempio, 400 (richiesta non valida) o codice di stato della risposta 409 (conflitto). Il corpo della risposta contiene il messaggio di errore formattato in JSON:

```json
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| version | Sì | Versione dell'API REST. Ad esempio: 1.0.1 |
| status | Sì | Deve essere 409 |
| codice | No | Un codice di errore del provider di endpoint RESTful, visualizzato quando `DebugMode` è abilitato. |
| requestId | No | Un identificatore della richiesta del provider di endpoint RESTful, visualizzato quando `DebugMode` è abilitato. |
| userMessage | Sì | Un messaggio di errore visualizzato dall'utente. |
| developerMessage | No | La descrizione dettagliata del problema e della sua risoluzione, visualizzata quando `DebugMode` è abilitato. |
| moreInfo | No | Un URI che rimana alle informazioni aggiuntive, visualizzato quando `DebugMode` è abilitata. |


L'esempio seguente illustra una classe C# che restituisce un messaggio di errore:

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="next-steps"></a>Passaggi successivi

Per esempi relativi all'uso di un profilo tecnico RESTful, vedere gli articoli seguenti:

- [Integrare scambi di attestazioni API REST nel criterio personalizzato di Azure AD B2C](custom-policy-rest-api-intro.md)
- [Procedura dettagliata: Integrare scambi di attestazioni API REST nel percorso utente di Azure AD B2C come convalida dell'input utente](custom-policy-rest-api-claims-validation.md)
- [Procedura dettagliata: Aggiungere scambi di attestazioni API REST ai criteri personalizzati in Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Proteggere i servizi API REST](secure-rest-api.md)

