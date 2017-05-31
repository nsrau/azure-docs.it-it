---
title: 'Procedura dettagliata: Scambi di attestazioni API REST come passaggio nei criteri personalizzati B2C | Microsoft Docs'
description: Articolo relativo all&quot;integrazione dei criteri personalizzati di Azure Active Directory B2C con l&quot;API
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/24/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 27d066cc6f985565a765989837e4a2744387a628
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017

---

# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journeys-as-an-orchestration-step"></a>Procedura dettagliata: Integrare scambi di attestazioni API REST nei percorsi utente di Azure AD B2C come passaggio di orchestrazione

Il **framework dell'esperienza di gestione delle identità** alla base di Azure AD B2C consente allo sviluppatore delle identità di integrare un'interazione con un'API RESTful in un percorso utente.  

Al termine di questa procedura dettagliata sarà possibile creare percorsi utente di Azure AD B2C che interagiscono con i servizi RESTful.

Il framework dell'esperienza di gestione delle identità invia i dati in attestazioni e riceve di nuovo i dati in attestazioni.  Lo scambio di attestazioni API REST può essere progettato come passaggio di orchestrazione.

- Questo può attivare un'azione esterna, ad esempio, può registrare un evento in un database esterno.
- Può anche essere usato per recuperare un valore e successivamente archiviarlo nel database utente.
- Le attestazioni ricevute possono essere usate in un secondo momento per modificare il flusso di esecuzione.

Anche l'interazione può essere progettata come passaggio di orchestrazione. Per altre informazioni in proposito, vedere [Procedura dettagliata: Integrare scambi di attestazioni API REST nei percorsi utente di Azure AD B2C come convalida dell'input utente](active-directory-b2c-rest-api-validation-custom.md).

In base allo scenario esaminato, un utente esegue una modifica del profilo e si vuole cercare l'utente in un sistema esterno, ottenere la città in cui è registrato e restituire tale attributo come attestazione all'applicazione.

## <a name="prerequisites"></a>Prerequisiti

- Un tenant di Azure AD B2C configurato per completare una procedura di iscrizione/accesso di un account locale, come descritto in [Introduzione](active-directory-b2c-get-started-custom.md).
- Un endpoint API REST con cui interagire. Questa procedura dettagliata usa come esempio un webhook di app per le funzioni di Azure molto semplice.
- **Scelta consigliata**: completare la [Procedura dettagliata: Integrare scambi di attestazioni API REST nei percorsi utente di Azure AD B2C come convalida dell'input utente](active-directory-b2c-rest-api-validation-custom.md).

## <a name="step-1---prepare-the-rest-api-function"></a>Passaggio 1: Preparare la funzione API REST

> [!NOTE]
> La configurazione delle funzioni API REST non rientra nell'ambito di questo articolo. [App per le funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-reference) offre un eccellente toolkit per creare servizi RESTful nel cloud.

È stata configurata una funzione di Azure che riceve un'attestazione `email` e restituisce semplicemente l'attestazione `city` con il valore assegnato di `Redmond`. La funzione di Azure di esempio è disponibile in [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

L'attestazione `userMessage` restituita dalla funzione di Azure è facoltativa in questo contesto e verrà ignorata dal framework dell'esperienza di gestione delle identità.  Potrebbe essere usata come messaggio passato all'applicazione e presentata all'utente in un secondo momento.

```csharp
if (requestContentAsJObject.email == null)
{
    return request.CreateResponse(HttpStatusCode.BadRequest);
}

var email = ((string) requestContentAsJObject.email).ToLower();

return request.CreateResponse<ResponseContent>(
    HttpStatusCode.OK,
    new ResponseContent
    {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        userMessage = "User Found",
        city = "Redmond"
    },
    new JsonMediaTypeFormatter(),
    "application/json");
```

**App per le funzioni di Azure** semplifica il recupero dell'URL della funzione, che include l'identificatore della funzione specifica.  In questo caso l'URL è: https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw== ed è possibile usarlo a scopo di test.

## <a name="step-2---configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworextensionsxml-file"></a>Passaggio 2: Configurare lo scambio di attestazioni API RESTful come profilo tecnico nel file TrustFrameworkExtensions.xml

Un profilo tecnico è la configurazione completa dello scambio desiderato con il servizio RESTful. Aprire il file `TrustFrameworkExtensions.xml` e aggiungere il frammento XML seguente all'interno dell'elemento `<ClaimsProvider>`.

> [!NOTE]
> Considerare il provider RESTful versione 1.0.0.0 descritto di seguito come protocollo come la funzione che interagirà con il servizio esterno.  La definizione completa dello schema è disponibile in <!-- TODO: Link to RESTful Provider schema definition>-->.

```XML
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-LookUpLoyaltyWebHook">
            <DisplayName>Check LookUpLoyalty Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
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

L'elemento `<InputClaims>` definisce le attestazioni che verranno inviate dal framework dell'esperienza di gestione delle identità al servizio REST. Nell'esempio precedente, il contenuto dell'attestazione `givenName` verrà inviato al servizio REST come attestazione `email`.  

L'elemento `<OutputClaims>` definisce le attestazioni che il framework dell'esperienza di gestione delle identità deve ricevere dal servizio REST. Indipendentemente dal numero di attestazioni ricevute, il framework dell'esperienza di gestione delle identità usa soltanto le attestazioni qui indicate. In questo esempio viene eseguito il mapping dell'attestazione ricevuta come `city` a un'attestazione `city` del framework dell'esperienza di gestione delle identità.

## <a name="step-3---add-a-new-claim-city-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Passaggio 3: Aggiungere una nuova attestazione `city` allo schema del file TrustFrameworkExtensions.xml

L'attestazione `city` non è definita nello schema. Verrà quindi aggiunta una definizione all'interno dell'elemento `<BuildingBlocks>`, che si trova all'inizio del file `TrustFrameworkExtensions.xml`.

```XML
<BuildingBlocks>
    <!--The claimtype city must be added to the TrustFrameworkPolicy-->
    <!-- You can add new claims in the BASE file Section III, or in the extensions file-->
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

## <a name="step-4---include-the-rest-service-claims-exchange-as-an-orchestration-step-in-your-profile-edit-user-journey-in-your-trustframeworkextensionsxml"></a>Passaggio 4: Includere lo scambio di attestazioni del servizio REST come passaggio di orchestrazione nel percorso utente di modifica del profilo nel file TrustFrameworkExtensions.xml

Si è deciso di aggiungere il passaggio al percorso utente di modifica del profilo, dopo che l'utente ha eseguito l'autenticazione, nei passaggi di orchestrazione da 1 a 4 riportati di seguito, e ha specificato le informazioni sul profilo aggiornato, nel passaggio 5.

> [!NOTE]
> In molti casi d'uso la chiamata all'API REST può essere usata come passaggio di orchestrazione.  Come passaggio di orchestrazione, può essere usata come aggiornamento a un sistema esterno dopo che l'utente ha completato un'attività, come una prima registrazione o un aggiornamento del profilo per sincronizzare le informazioni.  In questo caso viene usata per incrementare le informazioni fornite all'applicazione dopo la modifica del profilo.

Copiare il codice XML del percorso utente di modifica del profilo dal file `TrustFrameworkBase.xml` al file `TrustFrameworkExtensions.xml` nell'elemento `<UserJourneys>`, quindi apportare la modifica nel passaggio 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
    <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
    </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Se l'ordine non corrisponde alla versione usata, assicurarsi di inserirlo come passaggio prima del tipo ClaimsExchange `SendClaims`.

Il codice XML UserJourney finale avrà un aspetto simile al seguente:

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
        <!-- Add a step 6 to the user journey before the jwt token is created-->
        <OrchestrationStep Order="6" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    </OrchestrationSteps>
    <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="step-5---add-the-claim-city-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Passaggio 5: Aggiungere l'attestazione "city" al file dei criteri relying party in modo che l'attestazione venga inviata all'applicazione

A tale scopo, modificare il file relying party `ProfileEdit.xml` e l'elemento `<TechnicalProfile Id="PolicyProfile">` per aggiungere quanto segue: `<OutputClaim ClaimTypeReferenceId="city" />`.

Dopo aver aggiunto la nuova attestazione, TechnicalProfile avrà un aspetto simile al seguente:

```XML
<DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="step-6---upload-your-changes-and-test"></a>Passaggio 6: Caricare le modifiche ed eseguire un test

Verranno sovrascritte le versioni esistenti dei criteri.

1.    (FACOLTATIVO) Salvare, scaricandola, la versione esistente del file delle estensioni prima di procedere.  Evitare di caricare più versioni del file delle estensioni per tenere bassa la complessità iniziale.
2.    (FACOLTATIVO) Per rinominare la nuova versione del file di modifica dei criteri PolicyId è possibile modificare PolicyId="B2C_1A_TrustFrameworkProfileEdit".
3.    Caricare il file delle estensioni.
4.    Caricare il file relying party di modifica dei criteri.
5.    Usare **Esegui adesso** per testare i criteri.  Esaminare il token restituito dal framework dell'esperienza di gestione delle identità all'applicazione.

Se tutte le impostazioni sono corrette, il token include la nuova attestazione `city`, con il valore `Redmond`.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Passaggi successivi

[Usare un'API REST come passaggio di convalida](active-directory-b2c-rest-api-validation-custom.md)

[Come cambiare la modifica del profilo per raccogliere altre informazioni dagli utenti](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

