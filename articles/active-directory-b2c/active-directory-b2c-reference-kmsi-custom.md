---
title: Funzionalità Mantieni l'accesso in Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come configurare Mantieni l'accesso (KMSI) in Active Directory B2C di Azure.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: e2aa52e8ad19274d45f648978e7b2f021139fe4a
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812303"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Abilitare "Mantieni l'accesso (KMSI)" in Active Directory B2C di Azure

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

La funzionalità "Mantieni l'accesso (KMSI)" può essere abilitata per il Web e le applicazioni native in Active Directory B2C di Azure (Azure AD). Questa funzionalità permette l'accesso agli utenti che tornano alle applicazioni senza dover immettere nuovamente il nome utente e la password. Quando l'utente si scollega, l'accesso viene revocato. 

Gli utenti dovrebbero evitare di abilitare questa funzione su un computer pubblico. 

![Abilitare Mantieni l'accesso](./media/active-directory-b2c-reference-kmsi-custom/kmsi.PNG)

## <a name="prerequisites"></a>Prerequisiti

Un tenant di Azure AD B2C configurato per consentire l'iscrizione/accesso a un account locale. Se non si dispone di un tenant, è possibile crearne uno seguendo la procedura descritta in [Esercitazione: Creare un tenant di Azure Active Directory B2C](tutorial-create-tenant.md).

## <a name="add-a-content-definition-element"></a>Aggiungere un elemento di definizione del contenuto 

Sotto l'elemento **BuildingBlocks** del file di estensione, aggiungere un elemento **ContentDefinitions**. 

1. Sotto l'elemento **ContentDefinitions**, aggiungere un elemento **ContentDefinition** con un identificatore di `api.signuporsigninwithkmsi`.
2. Sotto l'elemento **ContentDefinition**, aggiungere gli elementi **LoadUri**, **RecoveryUri** e **DataUri**. Il valore `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` dell'elemento **Datauri** è un identificatore comprensibile di macchina che visualizza una casella di controllo KMSI nelle pagine di accesso. Questo valore non deve essere modificato. 

    ```XML
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsigninwithkmsi">
          <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>                       
    ```

## <a name="add-a-sign-in-claims-provider-for-a-local-account"></a>Aggiungere un provider di attestazioni di accesso per l'account locale  

È possibile definire un accesso all'account locale (Local Account SignIn) come provider di attestazioni usando l'elemento **ClaimsProvider** nel file di estensione dei criteri:

1. Aprire il file *TrustFrameworkExtensions.xml* dalla directory di lavoro. 
2. Trovare l'elemento **ClaimsProviders**. Se non esiste, aggiungerlo nell'elemento radice. Lo [starter pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) include un provider di attestazioni di accesso all'account locale. 
3. Aggiungere un elemento **ClaimsProvider** con **DisplayName** e **TechnicalProfile** come illustrato nell'esempio seguente:

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive">
            <Metadata>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

### <a name="add-the-application-identifiers-to-your-custom-policy"></a>Aggiungere gli identificatori dell'applicazione al criterio personalizzato

Aggiungere gli identificatori dell'applicazione per il file *trustframeworkextensions. XML*.

1. Nel file *trustframeworkextensions. XML*, trovare l'elemento **TechnicalProfile** con l'identificatore di `login-NonInteractive` e l'elemento **TechnicalProfile** con un identificatore di `login-NonInteractive-PasswordChange` e sostituire tutti i valori di `IdentityExperienceFrameworkAppId` con l'identificatore di applicazione dell'applicazione Framework dell'esperienza di gestione delle identità come descritto in [Introduzione](active-directory-b2c-get-started-custom.md).

    ```
    <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
    ```

2. Sostituire tutti valori di `ProxyIdentityExperienceFrameworkAppId` con l'identificatore dell'applicazione Framework dell'esperienza di gestione delle identità proxy, come descritto in [Introduzione](active-directory-b2c-get-started-custom.md).
3. Salvare il file delle estensioni.

## <a name="create-a-kmsi-enabled-user-journey"></a>Creare un percorso utente abilitato KMSI

Aggiungere il provider di attestazioni di accesso per un account locale al percorso utente. 

1. Aprire il file di base dei criteri. Per esempio, *TrustFrameworkBase.xml*.
2. Trovare l'elemento **UserJourneys** e copiare l'intero contenuto dell'elemento **UserJourney** che usa l'identificatore di `SignUpOrSignIn`.
3. Aprire il file di estensione. Ad esempio, aprire il file di estensione *TrustFrameworkExtensions.xml* e trovare l'elemento **UserJourneys**. Se l'elemento non esiste, aggiungerne uno.
4. Incollare l'intero elemento **UserJourney** copiato come figlio dell'elemento **UserJourneys**.
5. Modificare il valore dell'identificatore per il nuovo percorso utente. Ad esempio: `SignUpOrSignInWithKmsi`.
6. Infine, nel primo passaggio di orchestrazione modificare il valore di **ContentDefinitionReferenceId** a `api.signuporsigninwithkmsi`. L'impostazione di questo valore abilita la casella di controllo nel percorso utente. 
7. Salvare e caricare il file di estensione e verificare che tutte le convalide abbiano esito positivo.

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignInWithKmsi">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsigninwithkmsi">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <Preconditions>
              <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
                <Value>objectId</Value>
                <Action>SkipThisOrchestrationStep</Action>
              </Precondition>
            </Preconditions>
            <ClaimsExchanges>
              <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <!-- This step reads any user attributes that we may not have received when in the token. -->
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

## <a name="create-a-relying-party-file"></a>Creare un file relying party

Aggiornare il file della relying party (RP) che avvierà il percorso utente appena creato.

1. Creare una copia del file *SignUpOrSignIn.xml* nella directory di lavoro, quindi rinominarla. Ad esempio, *SignUpOrSignInWithKmsi.xml*.
2. Aprire il nuovo file e aggiornare l'attributo **PolicyId** per il **TrustFrameworkPolicy** con un valore univoco. Questo è il nome dei criteri. Ad esempio: `SignUpOrSignInWithKmsi`.
3. Modificare l'attributo **ReferenceId** per l'elemento **DefaultUserJourney** per corrispondere all'identificatore del nuovo percorso utente creato. Ad esempio: `SignUpOrSignInWithKmsi`.

    Il server di gestione delle chiavi è configurato usando l'elemento **UserJourneyBehaviors** con **SingleSignOn**, **SessionExpiryType** e **SessionExpiryInSeconds** come suoi primi elementi figlio. L'attributo **KeepAliveInDays** consente di controllare per quanto tempo l'utente rimane connesso. Nell'esempio seguente la sessione KMSI scade automaticamente dopo `7` giorni, indipendentemente dalla frequenza con cui l'utente esegue l'autenticazione automatica. Impostare il valore **KeepAliveInDays** a `0` disattiva la funzionalità KMSI. Per impostazione predefinita, questo valore è `0`. Se il valore di **SessionExpiryType** è `Rolling`, la sessione KMSI viene estesa di `7` giorni ogni volta che l'utente esegue l'autenticazione automatica.  Se `Rolling` è selezionato, è consigliabile mantenere il numero di giorni al minimo. 

    Il valore di **SessionExpiryInSeconds** rappresenta l'ora di scadenza di una sessione SSO. Questo viene usato internamente da Azure Active Directory B2C per verificare se la sessione KMSI è scaduta oppure no. Il valore di **KeepAliveInDays** determina il valore di scadenza/validità massima del cookie SSO nel browser Web. A differenza di **SessionExpiryInSeconds**, **KeepAliveInDays** viene usato per impedire che il browser cancelli i cookie quando viene chiuso. Un utente può accedere automaticamente solo se il cookie di sessione SSO esiste (verificato da **KeepAliveInDays**) e non è scaduto (verificato da **SessionExpiryInSeconds**). 
    
    Se l'utente non abilita **Mantieni l'accesso** nella pagina di iscrizione e di accesso, la sessione scade dopo che è trascorso il tempo indicato da **SessionExpiryInSeconds** o dopo la chiusura del browser. Se l'utente abilita **Mantieni l'accesso**, il valore di **KeepAliveInDays** sostituisce il valore di **SessionExpiryInSeconds** e determina la scadenza della sessione. Se chiude il browser e lo riapre, l'utente può ancora eseguire l'accesso entro l'intervallo di tempo indicato da **KeepAliveInDays**. È consigliabile impostare il valore di **SessionExpiryInSeconds** su un periodo breve (1200 secondi), mentre il valore di **KeepAliveInDays** può essere impostato su un periodo relativamente lungo (7 giorni), come illustrato nell'esempio seguente:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
      <UserJourneyBehaviors>
        <SingleSignOn Scope="Tenant" KeepAliveInDays="7" />
        <SessionExpiryType>Absolute</SessionExpiryType>
        <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
      </UserJourneyBehaviors>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName" />
          <OutputClaim ClaimTypeReferenceId="givenName" />
          <OutputClaim ClaimTypeReferenceId="surname" />
          <OutputClaim ClaimTypeReferenceId="email" />
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

4. Salvare le modifiche e caricare il file.
5. Per testare i criteri personalizzati caricati, nel portale di Azure passare alla pagina dei criteri e quindi selezionare **Esegui ora**.

È possibile trovare i criteri di esempio [qui](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).








