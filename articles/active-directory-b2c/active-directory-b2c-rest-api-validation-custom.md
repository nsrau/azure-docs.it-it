---
title: 'Azure AD B2C: integrare scambi di attestazioni API REST come convalida nei criteri personalizzati | Microsoft Docs'
description: Argomento sui criteri personalizzati di Azure Active Directory B2C
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
ms.openlocfilehash: 46abe48c3c9a7aab3fe013811d088a63957fe500
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017

---

# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journeys-as-validation-on-user-input"></a>Procedura dettagliata: Integrare scambi di attestazioni API REST nei percorsi utente di Azure AD B2C come convalida dell'input utente

Il **Framework dell'esperienza di gestione delle identità** alla base di Azure AD B2C consente allo sviluppatore delle identità di integrare un'interazione con un'API RESTful in un percorso utente.  

Al termine di questa procedura dettagliata sarà possibile creare percorsi utente di Azure AD B2C che interagiscono con i servizi RESTful.

Il Framework dell'esperienza di gestione delle identità invia i dati in attestazioni e riceve di nuovo i dati in attestazioni.  L'interazione con l'API può essere progettata come scambio di attestazioni API REST o come profilo di convalida all'interno di un passaggio di orchestrazione.

- In genere viene convalidato l'input dell'utente
- Se il valore fornito dall'utente viene rifiutato, l'utente può provare nuovamente a immettere un valore valido con la possibilità di restituire un messaggio di errore all'utente stesso.

L'interazione può anche essere progettata come passaggio di orchestrazione. Per altre informazioni, vedere [Procedura dettagliata: Integrare scambi di attestazioni API REST nei percorsi utente di Azure AD B2C come passaggio di orchestrazione](active-directory-b2c-rest-api-step-custom.md).

Per l'esempio del profilo di convalida si userà il percorso utente di modifica profilo disponibile nel file ProfileEdit.xml dello starter pack.

È possibile verificare che il nome fornito dall'utente nella modifica del profilo non faccia parte di un elenco di elementi esclusi.

## <a name="prerequisites"></a>Prerequisiti

- Un tenant di Azure AD B2C configurato per completare una procedura di iscrizione/accesso di un account locale, come descritto in [Introduzione](active-directory-b2c-get-started-custom.md).
- Un endpoint API REST con il quale interagire. È stato configurato un sito demo [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) con un servizio API REST che verrà usato per questa procedura dettagliata.

## <a name="step-1---prepare-the-rest-api-function"></a>Passaggio 1: Preparare la funzione API REST

> [!NOTE]
> La configurazione delle funzioni API REST non rientra nell'ambito di questo articolo. [App per le funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-reference) offre un eccellente toolkit per creare servizi RESTful nel cloud.

È stata creata una funzione di Azure che riceve un'attestazione prevista come "playerTag" e convalida se l'attestazione esiste o meno. È possibile accedere al codice completo della funzione di Azure in [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

```
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

L'attestazione `userMessage` restituita dalla funzione di Azure è prevista dal Framework dell'esperienza di gestione delle identità e verrà visualizzata all'utente sotto forma di stringa se la convalida non riesce, ad esempio quando viene restituito lo stato di conflitto 409 nell'esempio precedente.

## <a name="step-2---configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>Passaggio 2: Configurare lo scambio di attestazioni API RESTful come profilo tecnico nel file TrustFrameworkExtensions.xml

Un profilo tecnico è la configurazione completa dello scambio desiderato con il servizio RESTful. Aprire il file `TrustFrameworkExtensions.xml` e aggiungere il frammento XML seguente all'interno dell'elemento `<ClaimsProviders>`.

> [!NOTE]
> Considerare il provider RESTful versione 1.0.0.0 descritto di seguito come protocollo come la funzione che interagirà con il servizio esterno.  La definizione completa dello schema è disponibile in <!-- TODO: Link to RESTful Provider schema definition>-->.

```
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

L'elemento `InputClaims` definisce le attestazioni che verranno inviate dall'IEE al servizio REST. Nell'esempio precedente, il contenuto delle attestazioni `givenName` verrà inviato al servizio REST come `playerTag`. In questo esempio, l'IEE non prevede di ricevere attestazioni e attende invece una risposta dal servizio REST e agisce in base ai codici di stato ricevuti.

## <a name="step-3---include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-wish-to-validate-the-user-input"></a>Passaggio 3: Includere lo scambio di attestazioni del servizio RESTful nel profilo tecnico autocertificato in cui si vuole convalidare l'input dell'utente

L'uso più comune del passaggio di convalida è nell'interazione con un utente.  Tutte le interazioni in cui è previsto che l'utente fornisca un input sono **profili tecnici autocertificati**. Per questo esempio si aggiungerà questa convalida al profilo tecnico **Self-Asserted-ProfileUpdate**.  Si tratta del profilo tecnico usato dal file dei criteri relying party `Profile Edit`.

Per aggiungere lo scambio di attestazioni al profilo tecnico autocertificato:

1. Aprire il file TrustFrameworkBase e cercare `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Esaminare la configurazione di questo profilo tecnico. Si noti che lo scambio con l'utente è definito come attestazioni che verranno chieste all'utente (attestazioni di input) e attestazioni che dovranno essere inviate dal provider autocertificato (attestazioni di output)
3. Cercare `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`. Si noti che questo profilo viene richiamato come passaggio di orchestrazione 6 di `<UserJourney Id="ProfileEdit">`

## <a name="step-4---upload-and-test-the-profile-edit-rp-policy-file"></a>Passaggio 4: Caricare e testare il file dei criteri relying party di modifica del profilo

1. Caricare la nuova versione del file `TrustframeworkExtensions`.
2. Usare **Esegui adesso** per testare il file dei criteri relying party di modifica del profilo.
3. Testare la convalida specificando uno dei nomi esistenti, ad esempio mcvinny, nel campo **Nome**. Se la configurazione è stata eseguita correttamente, verrà visualizzato un messaggio per avvisare l'utente che `player tag` è già in uso.

## <a name="next-steps"></a>Passaggi successivi

[Come cambiare la modifica del profilo e la registrazione degli utenti per raccogliere informazioni dagli utenti](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Procedura dettagliata: Integrare scambi di attestazioni API REST nei percorsi utente di Azure AD B2C come passaggio di orchestrazione](active-directory-b2c-rest-api-step-custom.md)

