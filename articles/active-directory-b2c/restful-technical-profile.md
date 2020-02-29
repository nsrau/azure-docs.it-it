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
ms.date: 02/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b83a6bacf1c6e392db9dfc65fd737ea28416a6b5
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183823"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definire un profilo tecnico RESTful nei criteri personalizzati di Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornisce il supporto per il servizio RESTful. Azure AD B2C invia dati al servizio RESTful in una raccolta di attestazioni di input e riceve dati in una raccolta di attestazioni di output. Con l'integrazione del servizio RESTful, è possibile eseguire queste operazioni:

- **Convalidare i dati utente di input**: questa azione consente di evitare che i dati in formato non corretto vengano resi permanenti in Azure AD B2C. Se il valore immesso dall'utente non è valido, il servizio RESTful restituisce un messaggio di errore che indica all'utente di specificare una voce. È possibile ad esempio verificare che l'indirizzo di posta elettronica indicato dall'utente sia presente nel database del cliente.
- **Sovrascrivere attestazioni di input**: consente di riformattare i valori nelle attestazioni di input. Ad esempio, se un utente immette il nome in lettere tutte minuscole o tutte maiuscole, è possibile formattare il nome con solo la prima lettera maiuscola.
- **Arricchire i dati utente**: consente di integrare ulteriormente applicazioni line-of-business aziendali. Ad esempio, il servizio RESTful può ricevere l'indirizzo di posta elettronica dell'utente, eseguire una query sul database del cliente e restituire il numero del programma fedeltà dell'utente ad Azure AD B2C. Le attestazioni restituite possono essere archiviate, valutate nei passaggi di orchestrazione successivi o incluse nel token di accesso.
- **Eseguire logica di business personalizzata**: consente di inviare notifiche push, aggiornare i database aziendali, eseguire un processo di migrazione utente, gestire le autorizzazioni, controllare i database ed eseguire altre azioni.

Il criterio può inviare attestazioni di input all'API REST. L'API REST può anche restituire attestazioni di output che possono essere usate successivamente nei criteri oppure può generare un messaggio di errore. È possibile progettare l'integrazione con i servizi RESTful nei modi seguenti:

- **Profilo tecnico di convalida**: un profilo tecnico di convalida chiama il servizio RESTful. Il profilo tecnico di convalida consente la convalida i dati specificati dall'utente prima che il percorso utente proceda. Con il profilo tecnico di convalida, un messaggio di errore viene visualizzato in una pagina autocertificata e restituito nelle attestazioni di output.
- **Scambio di attestazioni**: viene effettuata una chiamata al servizio RESTful tramite un passaggio di orchestrazione. In questo scenario, non vi è alcuna interfaccia utente che esegue il rendering del messaggio di errore. Se l'API REST restituisce un messaggio di errore, l'utente viene reindirizzato all'applicazione basata su attestazioni con un messaggio di errore.

## <a name="protocol"></a>Protocollo

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `Proprietary`. L'attributo **gestore** deve contenere il nome completo dell'assembly del gestore di protocollo usato da Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Nell'esempio seguente viene illustrato un profilo tecnico RESTful:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Attestazioni di input

L'elemento **InputClaims** contiene un elenco di attestazioni da inviare all'API REST. È anche possibile eseguire il mapping del nome dell'attestazione per il nome definito nell'API REST. L'esempio seguente illustra il mapping tra i criteri e l'API REST. L'attestazione **givenName** viene inviata all'API REST come **firstName**, mentre **surname** viene inviato come **lastName**. L'attestazione **messaggio di posta elettronica** viene impostata così com'è.

```XML
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
    1. Aggiungere una trasformazione delle attestazioni di input con un riferimento alla trasformazione delle attestazioni `GenerateJson`.
    1. Impostare l'opzione metadati `SendClaimsIn` su `body`
    1. Impostare l'opzione `ClaimUsedForRequestPayload` Metadata sul nome dell'attestazione contenente il payload JSON.
    1. Nell'attestazione di input aggiungere un riferimento all'attestazione di input contenente il payload JSON.

Nell'esempio seguente `TechnicalProfile` Invia un messaggio di posta elettronica di verifica utilizzando un servizio di posta elettronica di terze parti (in questo caso, SendGrid).

```XML
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
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
| AuthenticationType | Sì | Tipo di autenticazione eseguita dal provider di attestazioni RESTful. I valori possibili sono: `None`, `Basic`, `Bearer` o `ClientCertificate`. Il valore `None` indica che l'API REST non è anonimo. Il valore`Basic` indica che l'API REST viene protetta con l'autenticazione di base HTTP. Solo gli utenti verificati, tra cui Azure AD B2C, possono accedere all'API. Il valore `ClientCertificate` (scelta consigliata) indica che l'API REST limita l'accesso usando l'autenticazione del certificato client. Solo i servizi con i certificati appropriati, ad esempio Azure AD B2C, possono accedere all'API. Il valore `Bearer` indica che l'API REST limita l'accesso tramite il token di connessione del client OAuth2. |
| SendClaimsIn | No | Specifica la modalità di invio di attestazioni di input al provider di attestazioni RESTful. I valori possibili sono: `Body` (impostazione predefinita), `Form`, `Header` o `QueryString`. Il valore `Body` è l'attestazione di input che viene inviata nel corpo della richiesta in formato JSON. Il valore`Form` è l'attestazione di input che viene inviata nel corpo della richiesta nel formato valore di chiave e commerciale "&" separata. Il valore`Header` è l'attestazione di input che viene inviata nell'intestazione della richiesta. Il valore`QueryString` è l'attestazione di input che viene inviata nella stringa di query della richiesta. I verbi HTTP richiamati da ciascuno di essi sono i seguenti:<br /><ul><li>`Body`: POST</li><li>`Form`: POST</li><li>`Header`: GET</li><li>`QueryString`: GET</li></ul> |
| ClaimsFormat | No | Specifica il formato per le attestazioni di output. I valori possibili sono: `Body` (impostazione predefinita), `Form`, `Header` o `QueryString`. Il valore `Body` è l'attestazione di output che viene inviata nel corpo della richiesta in formato JSON. Il valore`Form` è l'attestazione di output che viene inviata nel corpo della richiesta nel formato valore di chiave e commerciale "&" separata. Il valore `Header` è l'attestazione di output che viene inviata nell'intestazione della richiesta. Il valore `QueryString` è l'attestazione di output che viene inviata nella stringa di query della richiesta. |
| ClaimUsedForRequestPayload| No | Nome di un'attestazione di stringa che contiene il payload da inviare all'API REST. |
| DebugMode | No | Il profilo tecnico viene eseguito in modalità debug. Valori possibili: `true`o `false` (impostazione predefinita). In modalità debug, l'API REST può restituire altre informazioni. Vedere la sezione [restituzione del messaggio di errore](#returning-error-message) . |
| IncludeClaimResolvingInClaimsHandling  | No | Per le attestazioni di input e output, specifica se la [risoluzione delle attestazioni](claim-resolver-overview.md) è inclusa nel profilo tecnico. Valori possibili: `true`o `false` (impostazione predefinita). Se si desidera utilizzare un resolver di attestazioni nel profilo tecnico, impostare questo valore su `true`. |
| ResolveJsonPathsInJsonTokens  | No | Indica se il profilo tecnico risolve i percorsi JSON. Valori possibili: `true`o `false` (impostazione predefinita). Usare questi metadati per leggere i dati da un elemento JSON annidato. In un [OutputClaim](technicalprofiles.md#outputclaims)impostare il `PartnerClaimType` sull'elemento del percorso JSON di cui si vuole eseguire l'output. Ad esempio: `firstName.localized`o `data.0.to.0.email`.|

## <a name="cryptographic-keys"></a>Chiavi crittografiche

Se il tipo di autenticazione è impostato su `None`, l'elemento **CryptographicKeys** non viene usato.

```XML
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

```XML
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

```XML
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

```XML
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

## <a name="returning-error-message"></a>Restituzione messaggio di errore

L'API REST può restituire un messaggio di errore, ad esempio "Utente nel sistema CRM non trovato". Se si verifica un errore, l'API REST deve restituire un messaggio di errore HTTP 409 (codice di stato della risposta in conflitto) con gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| version | Sì | 1.0.0 |
| status | Sì | 409 |
| codice | No | Un codice di errore del provider di endpoint RESTful, visualizzato quando `DebugMode` è abilitato. |
| requestId | No | Un identificatore della richiesta del provider di endpoint RESTful, visualizzato quando `DebugMode` è abilitato. |
| userMessage | Sì | Un messaggio di errore visualizzato dall'utente. |
| developerMessage | No | La descrizione dettagliata del problema e della sua risoluzione, visualizzata quando `DebugMode` è abilitato. |
| moreInfo | No | Un URI che rimana alle informazioni aggiuntive, visualizzato quando `DebugMode` è abilitata. |

L'esempio seguente illustra un'API REST che restituisce un messaggio di errore in formato JSON:

```JSON
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

- [Integrare scambi di attestazioni API REST nel percorso utente di Azure AD B2C come convalida dell'input utente](rest-api-claims-exchange-dotnet.md)
- [Proteggere i servizi RESTful usando l'autenticazione di base HTTP](secure-rest-api-dotnet-basic-auth.md)
- [Proteggere il servizio RESTful usando certificati client](secure-rest-api-dotnet-certificate-auth.md)
- [Procedura dettagliata: Integrare scambi di attestazioni API REST nel percorso utente di Azure AD B2C come convalida dell'input utente](custom-policy-rest-api-claims-validation.md)
