---
title: TypingDNA con Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Informazioni su come integrare Azure AD B2C autenticazione con TypingDNA per semplificare la verifica dell'identità e la correzione basata sul modello di digitazione degli utenti, fornisce soluzioni di verifica ID che forzano l'autenticazione a più fattori e contribuisce a conformarsi ai requisiti SCA per la direttiva 2 (PSD2) dei servizi di pagamento.
author: gargi-sinha
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: f7d89942ad5209b854b8df486ad3e59a3976edfc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259052"
---
# <a name="tutorial-for-configuring-typingdna-with-azure-active-directory-b2c"></a>Esercitazione per la configurazione di TypingDNA con Azure Active Directory B2C

Questa procedura dettagliata illustra come integrare un'app di pagamento online di esempio in Azure Active Directory B2C con l'APP TypingDNA. Usando l'app TypingDNA, i clienti Azure AD B2C possono conformarsi ai requisiti delle transazioni PSD2 ( [Payment Services Directive 2](https://www.typingdna.com/use-cases/sca-strong-customer-authentication) ) tramite la dinamica della sequenza di tasti e l'autenticazione avanzata del cliente. Altre informazioni su TypingDNA sono disponibili [qui](https://www.typingdna.com/).

 Azure AD B2C usa le tecnologie di TypingDNA per acquisire le caratteristiche di digitazione degli utenti e registrarle e analizzarle per familiarizzare con ogni autenticazione. In questo modo viene aggiunto un livello di protezione correlato all'rischiosità di un'autenticazione e vengono valutati i livelli di rischio. Azure AD B2C possibile richiamare altri meccanismi per fornire ulteriore confidenza all'utente che affermano di essere richiamando l'autenticazione a più fattori di Azure, forzando la verifica tramite posta elettronica o qualsiasi altra logica personalizzata per lo scenario.

>[!NOTE]
> Questo criterio di esempio è basato su [SocialAndLocalAccountsWithMfa](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) Starter Pack.

## <a name="scenario-description"></a>Descrizione dello scenario

![Screenshot del diagramma dell'architettura di TypingDNA](./media/partner-typingdna/typingdna-architecture-diagram.png)

### <a name="sign-up"></a>Iscrizione

1. Azure AD B2C pagine usano la libreria JavaScript di TypingDNA per registrare il modello di digitazione dell'utente. Ad esempio, il nome utente e la password vengono registrati al momento dell'iscrizione per la registrazione iniziale e quindi a ogni accesso per la verifica.

2. Quando l'utente invia la pagina, tramite la libreria TypingDNA viene calcolata la caratteristica di digitazione dell'utente. Successivamente, inserire le informazioni in un campo di testo nascosto di cui Azure AD B2C ne è stato eseguito il rendering. Questo campo è nascosto con CSS.  

    Nell' [esempio sono inclusi i file HTML](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignUp.cshtml) con le modifiche JavaScript e CSS, a cui fanno riferimento `api.selfasserted.tdnasignin` le `api.selfasserted.tdnasignup` definizioni del contenuto e. Vedere [hosting del contenuto della pagina](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#hosting-the-page-content) per ospitare i file HTML.

3. Azure AD B2C ora dispone del modello di digitazione all'interno dell'elenco di attestazioni quando l'utente invia le proprie credenziali. Deve chiamare un'API (your) per passare questi dati all'endpoint dell'API REST di TypingDNA. Questa API è inclusa nell' [esempio (typingDNA-API-Interface)](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface).
4. L'API del livello intermedio passa quindi i dati del modello di digitazione all'API REST di TypingDNA. Al momento dell'iscrizione, viene chiamato l' [endpoint utente check](https://api.typingdna.com/index.html#api-API_Services-GetUser) per confermare che l'utente non esiste e quindi viene chiamato l'endpoint [Save pattern](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) per salvare il primo modello di digitazione dell'utente.

> [!NOTE]
> Tutte le chiamate all'endpoint dell'API REST TypingDNA inviano un UserId. Deve essere un valore con hash. Azure AD B2C usa la `HashObjectIdWithEmail` trasformazione delle attestazioni per eseguire l'hashing del messaggio di posta elettronica con un Salt e un segreto casuale.  

Le chiamate all'API REST sono modellate con `validationTechnicalProfiles` all'interno di `LocalAccountSignUpWithLogonEmail-TDNA` :

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail-TDNA" />
    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="true"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser"/>

</ValidationTechnicalProfiles>

```

### <a name="sign-in"></a>Accesso

Al successivo accesso, il modello di digitazione dell'utente viene calcolato nello stesso modo in cui si effettua l'iscrizione usando il [codice HTML personalizzato](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignIn.cshtml). Quando il profilo di digitazione si trova all'interno dell'elenco di attestazioni Azure AD B2C, Azure AD B2C chiamerà l'API per chiamare l'endpoint dell'API REST di TypingDNA. L'endpoint [Check User](https://api.typingdna.com/index.html#api-API_Services-GetUser) viene chiamato per confermare che l'utente esiste. Quindi, [Verify endpoint pattern](https://api.typingdna.com/index.html#api-API_Services-verifyTypingPattern) viene chiamato per restituire l'oggetto `net_score` . `net_score`Si tratta di un'indicazione del modo in cui il modello di tipizzazione è stato chiuso all'originale al momento dell'iscrizione.

Questo modello di tipizzazione viene modellato con `validationTechnicalProfiles` all'interno di `SelfAsserted-LocalAccountSignin-Email-TDNA` :

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="login-NonInteractive"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="false"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-VerifyUser"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser">

        <Preconditions>

            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>saveTypingPattern</Value>

            <Value>False</Value>

            <Action>SkipThisValidationTechnicalProfile</Action>

            </Precondition>

        </Preconditions>

    </ValidationTechnicalProfile>

</ValidationTechnicalProfiles>

```

 Se l'utente ottiene un modello di digitazione con un valore elevato `net_score` , è possibile salvarlo usando l'endpoint TypingDNA [Save Typing pattern](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) .  

L'API deve restituire un'attestazione  `saveTypingPattern` se si vuole che l'endpoint TypingDNA Save Typing pattern venga chiamato da Azure ad B2C (tramite l'API).

L'esempio nel repository contiene un'API (TypingDNA-API-Interface) che viene configurata con le proprietà seguenti.

- Modalità di training: se l'utente dispone di meno di due modelli salvati, richiedere sempre l'autenticazione a più fattori.

- Se per l'utente sono stati salvati 2-5 modelli e il `net_score` valore di è inferiore a 50, richiedere l'autenticazione a più fattori.

- Se per l'utente sono stati salvati 5 modelli e il `net_score` valore di è inferiore a 65, richiedere l'autenticazione a più fattori.

Queste soglie devono essere modificate in caso di utilizzo.

- Dopo che l'API ha valutato `net_score` , deve restituire un'attestazione booleana a B2C- `promptMFA` .

- L' `promptMFA` attestazione viene usata in una condizione preliminare per eseguire in modo condizionale l'autenticazione a più fattori di Azure.

```xml

<OrchestrationStep Order="3" Type="ClaimsExchange">

    <Preconditions>

        <Precondition Type="ClaimsExist" ExecuteActionsIf="true">

            <Value>isActiveMFASession</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>promptMFA</Value>

            <Value>False</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

    </Preconditions>

    <ClaimsExchanges>

        <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" />

    </ClaimsExchanges>

</OrchestrationStep>

```

## <a name="onboard-with-typingdna"></a>Onboarding con TypingDNA

1. Iscriversi a TypingDNA [qui](https://www.typingdna.com/)
2. Accedere al dashboard di TypingDNA e ottenere la **chiave API** e il **segreto API**. Questa operazione sarà necessaria per l'installazione dell'interfaccia API in un secondo momento

## <a name="integrate-typingdna-with-azure-ad-b2c"></a>Integrare TypingDNA con Azure AD B2C

1. Ospitare l' [interfaccia TypingDNA-API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) nel provider di hosting preferito
2. Sostituire tutte le istanze di `apiKey` e `apiSecret` nella soluzione [TypingDNA-API-Interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) con le credenziali del dashboard di TypingDNA
3. Ospitare i file HTML nel provider desiderato seguendo i requisiti di CORS [qui](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#3-configure-cors)
4. Sostituire rispettivamente gli elementi elemento loaduri per `api.selfasserted.tdnasignup` le `api.selfasserted.tdnasignin` definizioni di contenuto e nel `TrustFrameworkExtensions.xml` file con l'URI dei file HTML ospitati.
5. Creare una chiave dei criteri B2C nel Framework dell'esperienza di identità nel pannello Azure AD della **portale di Azure**. Usare l' `Generate` opzione e assegnare un nome alla chiave `tdnaHashedId` .
6. Sostituire i TenantId nei file dei criteri
7. Sostituire ServiceURLs in tutti i profili tecnici dell'API REST di TypingDNA (REST-TDNA-VerifyUser, REST-TDNA-SaveUser, REST-TDNA-CheckUser) con l'endpoint per l' [API dell'interfaccia TypingDNA-API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface).
8. Caricare [i file dei criteri](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/policy) nel tenant.

## <a name="test-the-user-flow"></a>Testare il flusso utente

1. Aprire il tenant B2C e scegliere Framework dell'esperienza di identità.
2. Selezionare il **flusso utente**creato in precedenza.
3. Selezionare **Esegui** flusso utente

    a. **Applicazione** : selezionare l'app registrata (l'esempio è JWT)

    b. **URL di risposta** -selezionare l'URL di Reindirizzamento

    c. Selezionare **Esegui il flusso utente**.
  
4. Esaminare il flusso di iscrizione e creare un account
5. Disconnetti
6. Esaminare il flusso di accesso
7. Il risultato JWT risultante visualizzerà i risultati di TypingDNA

## <a name="live-version"></a>Versione live

• L'autenticazione a più fattori è stata disabilitata in questa versione di test, ma è possibile vedere il risultato che indica se l'autenticazione a più fattori è stata richiesta dall'attestazione `promptMFA` dopo l'autenticazione.

• Iscriviti [qui](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SU_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login) e accedi [qui](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SI_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli seguenti:

- [Criteri personalizzati in AAD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Introduzione ai criteri personalizzati in AAD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
