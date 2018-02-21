---
title: 'Azure Active B2C di Directory: scambi di attestazioni API REST come convalida | Microsoft Docs'
description: Un argomento sui criteri personalizzati di Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: mtillman
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/24/2017
ms.author: joroja
ms.openlocfilehash: dfd33a9ecdce7b21f58660fb39a5f2d7b4ce6f43
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Procedura dettagliata: Integrare scambi di attestazioni API REST nel percorso utente di Azure AD B2C come convalida dell'input utente

Il framework dell'esperienza di gestione delle identità alla base di Azure Active Directory B2C (Azure AD B2C) consente allo sviluppatore delle identità di integrare un'interazione con un'API RESTful in un percorso utente.  

Al termine di questa procedura dettagliata sarà possibile creare percorsi utente di Azure AD B2C che interagiscono con i servizi RESTful.

Il framework dell'esperienza di gestione delle identità invia i dati in attestazioni e riceve di nuovo i dati in attestazioni. L'interazione con l'API:

- Può essere progettata come scambio di attestazioni API REST o come profilo di convalida all'interno di un passaggio di orchestrazione.
- In genere viene convalidato l'input dell'utente. Se il valore fornito dall'utente viene rifiutato, l'utente può provare nuovamente a immettere un valore valido con la possibilità di restituire un messaggio di errore.

È possibile progettare l'interazione anche come passaggio di orchestrazione. Per altre informazioni, vedere [Procedura dettagliata: Integrare scambi di attestazioni API REST nei percorsi utente di Azure AD B2C come passaggio di orchestrazione](active-directory-b2c-rest-api-step-custom.md).

Per l'esempio del profilo di convalida si userà il percorso utente di modifica profilo disponibile nel file ProfileEdit.xml dello starter pack.

È possibile verificare che il nome fornito dall'utente nella modifica del profilo non faccia parte di un elenco di esclusione.

## <a name="prerequisites"></a>prerequisiti

- Un tenant di Azure AD B2C configurato per completare una procedura di iscrizione/accesso di un account locale, come descritto in [Introduzione](active-directory-b2c-get-started-custom.md).
- Un endpoint API REST con il quale interagire. Per questa procedura dettagliata è stato configurato un sito demo denominato [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) con un servizio API REST.

## <a name="step-1-prepare-the-rest-api-function"></a>Passaggio 1: Preparare la funzione API REST

> [!NOTE]
> La configurazione delle funzioni API REST non rientra nell'ambito di questo articolo. [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-reference) offre un eccellente toolkit per creare servizi RESTful nel cloud.

È stata creata una funzione di Azure che riceve un'attestazione prevista come `playerTag`. La funzione verifica che l'attestazione esista. È possibile accedere al codice completo della funzione di Azure in [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

```csharp
if (requestContentAsJObject.playerTag == null)
{
  return request.CreateResponse(HttpStatusCode.BadRequest);
}

var playerTag = ((string) requestContentAsJObject.playerTag).ToLower();

if (playerTag == "mcvinny" || playerTag == "msgates123" || playerTag == "revcottonmarcus")
{
  return request.CreateResponse<ResponseContent>(
    HttpStatusCode.Conflict,
    new ResponseContent
    {
      version = "1.0.0",
      status = (int) HttpStatusCode.Conflict,
      userMessage = $"The player tag '{requestContentAsJObject.playerTag}' is already used."
    },
    new JsonMediaTypeFormatter(),
    "application/json");
}

return request.CreateResponse(HttpStatusCode.OK);
```

L'attestazione `userMessage` restituita dalla funzione di Azure è prevista dal framework dell'esperienza di gestione delle identità e verrà visualizzata all'utente sotto forma di stringa se la convalida non riesce, ad esempio quando viene restituito lo stato di conflitto 409 nell'esempio precedente.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>Passaggio 2: Configurare lo scambio di attestazioni API RESTful come profilo tecnico nel file TrustFrameworkExtensions.xml

Un profilo tecnico è la configurazione completa dello scambio desiderato con il servizio RESTful. Aprire il file TrustFrameworkExtensions.xml e aggiungere il frammento XML seguente all'interno dell'elemento `<ClaimsProviders>`.

> [!NOTE]
> Nell'XML seguente il provider RESTful `Version=1.0.0.0` viene descritto come il protocollo. Considerarlo come la funzione che interagirà con il servizio esterno. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">
            <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="playerTag" />
            </InputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
        <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
            <ValidationTechnicalProfiles>
                <ValidationTechnicalProfile ReferenceId="AzureFunctions-CheckPlayerTagWebHook" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

L'elemento `InputClaims` definisce le attestazioni che verranno inviate dal framework dell'esperienza di gestione delle identità al servizio REST. In questo esempio il contenuto dell'attestazione `givenName` verrà inviato al servizio REST come `playerTag`. In questo esempio, il framework dell'esperienza di gestione non prevede di ricevere attestazioni, ma attende una risposta dal servizio REST e agisce in base ai codici di stato ricevuti.

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>Passaggio 3: Includere lo scambio di attestazioni del servizio RESTful nel profilo tecnico autocertificato in cui si vuole convalidare l'input dell'utente

L'uso più comune del passaggio di convalida è nell'interazione con un utente. Tutte le interazioni in cui è previsto che l'utente fornisca un input sono *profili tecnici autocertificati*. Per questo esempio si aggiungerà la convalida al profilo tecnico Self-Asserted-ProfileUpdate. Questo è il profilo tecnico usato dal file dei criteri relying party `Profile Edit`.

Per aggiungere lo scambio di attestazioni al profilo tecnico autocertificato:

1. Aprire il file TrustFrameworkBase.xml e cercare `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Esaminare la configurazione di questo profilo tecnico. Si noti che lo scambio con l'utente è definito come attestazioni che verranno chieste all'utente (attestazioni di input) e attestazioni che dovranno essere inviate dal provider autocertificato (attestazioni di output).
3. Cercare `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`. Tenere presente che questo profilo viene richiamato come passaggio di orchestrazione 4 di `<UserJourney Id="ProfileEdit">`.

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>Passaggio 4: Caricare e testare il file dei criteri relying party di modifica del profilo

1. Caricare la nuova versione del file TrustFrameworkExtensions.xml.
2. Usare **Esegui adesso** per testare il file dei criteri relying party di modifica del profilo.
3. Testare la convalida specificando uno dei nomi esistenti, ad esempio mcvinny, nel campo **Nome**. Se la configurazione è stata eseguita correttamente, verrà visualizzato un messaggio per avvisare l'utente che il tag del player è già in uso.

## <a name="next-steps"></a>Passaggi successivi

[Cambiare la modifica del profilo e la registrazione degli utenti per raccogliere informazioni dagli utenti](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Procedura dettagliata: Integrare scambi di attestazioni API REST nei percorsi utente di Azure AD B2C come passaggio di orchestrazione](active-directory-b2c-rest-api-step-custom.md)
