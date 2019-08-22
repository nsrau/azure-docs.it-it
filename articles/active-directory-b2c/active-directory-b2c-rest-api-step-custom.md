---
title: Scambi di attestazioni API REST-Azure Active Directory B2C
description: Aggiungere scambi di attestazioni API REST ai criteri personalizzati in Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 42129870c6ab2bb5e58bdf9aaa323a3d64b479f8
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69644915"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Aggiungere scambi di attestazioni API REST a criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

È possibile aggiungere un'interazione con un'API RESTful ai [criteri personalizzati](active-directory-b2c-overview-custom.md) in Azure Active Directory (Azure ad) B2C. Questo articolo illustra come creare un percorso utente Azure AD B2C che interagisce con i servizi RESTful.

L'interazione include uno scambio di attestazioni di informazioni tra le attestazioni API REST e Azure AD B2C. Gli scambi di attestazioni hanno le caratteristiche seguenti:

- Può essere progettato come passaggio di orchestrazione.
- Può attivare un'azione esterna. Può registrare ad esempio un evento in un database esterno.
- Consente di recuperare un valore e quindi archiviarlo nel database utente.
- Può modificare il flusso di esecuzione.

Lo scenario rappresentato in questo articolo include le azioni seguenti:

1. Cercare l'utente in un sistema esterno.
2. Ottenere la città in cui tale utente è registrato.
3. Restituire tale attributo all'applicazione come un'attestazione.

## <a name="prerequisites"></a>Prerequisiti

- Completare la procedura descritta in [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md).
- Un endpoint API REST con il quale interagire. Questo articolo usa come esempio una semplice funzione di Azure. Per creare la funzione di Azure, vedere [creare la prima funzione nel portale di Azure](../azure-functions/functions-create-first-azure-function.md).

## <a name="prepare-the-api"></a>Preparare l'API

In questa sezione si prepara la funzione di Azure per ricevere un valore per `email`e quindi si restituisce il valore per `city` che può essere usato da Azure ad B2C come attestazione.

Modificare il file run. CSX per la funzione di Azure creata per usare il codice seguente:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
  string email = req.Query["email"];
  string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
  dynamic data = JsonConvert.DeserializeObject(requestBody);
  email = email ?? data?.email;

  return email != null
    ? (ActionResult)new OkObjectResult(
      new ResponseContent
      {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        city = "Redmond"
      })
      : new BadRequestObjectResult("Please pass an email on the query string or in the request body");
}

public class ResponseContent
{
    public string version { get; set; }
    public int status { get; set; }
    public string city {get; set; }
}
```

## <a name="configure-the-claims-exchange"></a>Configurare lo scambio di attestazioni

Un profilo tecnico fornisce la configurazione per lo scambio di attestazioni.

Aprire il file *TrustFrameworkExtensions. XML* e aggiungere l'elemento XML **ClaimsProvider** seguente all'interno dell'elemento **ClaimsProviders** .

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

L'elemento **InputClaims** definisce le attestazioni inviate al servizio REST. In questo esempio, il valore dell'attestazione `givenName` viene inviato al servizio REST come attestazione. `email` L'elemento **OutputClaims** definisce le attestazioni previste dal servizio REST.

I commenti precedenti `AuthenticationType` e `AllowInsecureAuthInProduction` specificano le modifiche che è necessario apportare quando si passa a un ambiente di produzione. Per informazioni su come proteggere le API RESTful per la produzione, vedere [proteggere le API RESTful con](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) l'autenticazione di base e le [API RESTful sicure con l'autenticazione del certificato](active-directory-b2c-custom-rest-api-netfw-secure-cert.md).

## <a name="add-the-claim-definition"></a>Aggiungere la definizione di attestazione

Aggiungere una definizione per `city` all'interno dell'elemento **BuildingBlocks** . È possibile trovare questo elemento all'inizio del file TrustFrameworkExtensions.xml.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="city">
      <DisplayName>City</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your city</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

## <a name="add-an-orchestration-step"></a>Aggiungere un passaggio di orchestrazione

In molti casi d'uso la chiamata all'API REST può essere usata come passaggio di orchestrazione. Come passaggio di orchestrazione, può essere usata come aggiornamento a un sistema esterno dopo che un utente ha completato un'attività, come una prima registrazione o un aggiornamento del profilo per sincronizzare le informazioni. In questo caso viene usata per incrementare le informazioni fornite all'applicazione dopo la modifica del profilo.

Aggiungere un passaggio al percorso utente di modifica del profilo. Dopo l'autenticazione dell'utente (passaggi dell'orchestrazione 1-4 nel codice XML seguente) e l'utente ha fornito le informazioni aggiornate sul profilo (passaggio 5). Copiare il codice XML del percorso utente di modifica del profilo dal file *TrustFrameworkBase. XML* nel file *TrustFrameworkExtensions. XML* all'interno dell'elemento **UserJourneys** . Apportare quindi la modifica come passaggio 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Il codice XML finale per il percorso utente dovrebbe essere simile a questo esempio:

```XML
<UserJourney Id="ProfileEdit">
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
      <ClaimsProviderSelections>
        <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
        <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
      </ClaimsProviderSelections>
    </OrchestrationStep>
    <OrchestrationStep Order="2" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
        <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>localAccountAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="4" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>socialIdpAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="5" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <!-- Add a step 6 to the user journey before the JWT token is created-->
    <OrchestrationStep Order="6" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="add-the-claim"></a>Aggiungere l'attestazione

Modificare il file *ProfileEdit. XML* e aggiungere `<OutputClaim ClaimTypeReferenceId="city" />` all'elemento **OutputClaims** .

Dopo aver aggiunto la nuova attestazione, il profilo tecnico ha un aspetto simile a quello riportato nell'esempio seguente:

```XML
<TechnicalProfile Id="PolicyProfile">
  <DisplayName>PolicyProfile</DisplayName>
  <Protocol Name="OpenIdConnect" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
    <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    <OutputClaim ClaimTypeReferenceId="city" />
  </OutputClaims>
  <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="upload-your-changes-and-test"></a>Caricare le modifiche ed eseguire un test

1. (Facoltativo) Salvare la versione esistente (scaricando) dei file prima di procedere.
2. Caricare *TrustFrameworkExtensions. XML* e *ProfileEdit. XML* e scegliere di sovrascrivere il file esistente.
3. Selezionare **B2C_1A_ProfileEdit**.
4. Per **selezionare l'applicazione** nella pagina Panoramica del criterio personalizzato, selezionare l'applicazione Web denominata *app Web 1* registrata in precedenza. Verificare che l' **URL di risposta** sia `https://jwt.ms`.
4. Selezionare **Esegui ora**. Accedere con le credenziali dell'account e fare clic su **continue (continua**).

Se tutti gli elementi sono configurati correttamente, il token include `city`la nuova attestazione `Redmond`, con il valore.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_profileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Passaggi successivi

È anche possibile progettare l'interazione come un profilo di convalida. Per altre informazioni, vedere [Procedura dettagliata: Integrare scambi di attestazioni API REST nel percorso utente di Azure AD B2C come convalida dell'input utente](active-directory-b2c-rest-api-validation-custom.md).

[Cambiare la modifica del profilo per raccogliere altre informazioni dagli utenti](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Informazioni di riferimento: Profilo tecnico RESTful](restful-technical-profile.md)

Per informazioni su come proteggere le API, vedere gli articoli seguenti:

* [Secure your RESTful API with basic authentication (username and password)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) (Proteggere l'API RESTful con l'atenticazione di base - nome utente e password)
* [Proteggere l'API RESTful con certificati client](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
