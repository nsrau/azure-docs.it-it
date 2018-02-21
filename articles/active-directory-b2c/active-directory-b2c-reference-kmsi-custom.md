---
title: 'Azure Active Directory B2C: KMSI| Microsoft Docs'
description: Un argomento che illustra come impostare "Mantieni l'accesso"
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: mtillman
ms.assetid: 926e9711-71c0-49e8-b658-146ffb7386c0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2016
ms.author: vigunase
ms.openlocfilehash: a3d78945f862d1ae12cec05da0cf0ea7df511f43
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-enable-keep-me-signed-in-kmsi"></a>Azure Active Directory B2C: Abilitare "Mantieni l'accesso (KMSI)"  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C consente ora alle applicazioni Web e native di abilitare la funzionalità "Mantieni l'accesso (KMSI)". Questa funzionalità concede l'accesso agli utenti che tornano alle applicazioni senza richiedere di immettere nuovamente il nome utente e password. Quando l'utente si scollega l'accesso viene revocato. 

È consigliabile evitare di selezionare questa opzione su un computer pubblico. 

![Immagine](images/kmsi.PNG)


## <a name="prerequisites"></a>prerequisiti

Un tenant di Azure AD B2C configurato per consentire l'iscrizione/accesso a un account locale, come descritto in [Introduzione](active-directory-b2c-get-started-custom.md).

## <a name="how-to-enable-kmsi"></a>Come abilitare KMSI

Apportare le modifiche seguenti nei criteri delle estensioni dei framework attendibilità.

## <a name="adding-a-content-definition-element"></a>Aggiunta di un elemento di definizione del contenuto 

Il nodo `BuildingBlocks` del file di estensione deve includere un elemento `ContentDefinitions`. 

1. Nella sezione `ContentDefinitions` definire un nuovo elemento `ContentDefinition` con ID `api.signuporsigninwithkmsi`.
2. Il nuovo `ContentDefinition` deve includere un `LoadUri`, un `RecoveryUri` e un `DataUri` come indicato di seguito.
3. Datauri`urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` è un identificatore comprensibile di macchina che visualizza una casella di controllo KMSI nelle pagine di accesso. Assicurarsi di non modificare questo valore. 

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



## <a name="add-a--local-account-sign-in-claims-provider"></a>Aggiungere un provider di attestazioni di accesso all'account locale 

È possibile definire un accesso all'account locale (Local Account SignIn) come provider di attestazioni nel nodo `<ClaimsProvider>` nel file di estensione dei criteri:

1. Aprire il file di estensione (TrustFrameworkExtensions.xml) dalla directory di lavoro. 
2. Trovare la sezione `<ClaimsProviders>`. Se non esiste, aggiungerla nel nodo radice.
3. Il pacchetto Starter nell'argomento di [Introduzione](active-directory-b2c-get-started-custom.md) viene fornito con un provider di attestazioni per l'accesso all'account locale (Local Account SignIn). 
4. In caso contrario, aggiungere un nuovo nodo `<ClaimsProvider>` come mostrato di seguito:

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
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
           </InputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
 </ClaimsProviders>
```

### <a name="add-the-application-ids-to-your-custom-policy"></a>Aggiungere le ID dell'applicazione al criterio personalizzato

Aggiungere gli ID applicazione al file di estensione (`TrustFrameworkExtensions.xml`):

1. Nel file delle estensioni (TrustFrameworkExtensions.xml) trovare gli elementi `<TechnicalProfile Id="login-NonInteractive">` e `<TechnicalProfile Id="login-NonInteractive-PasswordChange">`

2. Sostituire tutte le istanze di `IdentityExperienceFrameworkAppId` con l'ID dell'applicazione del framework dell'esperienza di gestione delle identità come descritto in [Introduzione](active-directory-b2c-get-started-custom.md). Di seguito è fornito un esempio: 

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. Sostituire tutte le istanze di `ProxyIdentityExperienceFrameworkAppId` con l'ID dell'applicazione del framework dell'esperienza di gestione delle identità Proxy come descritto in [Introduzione](active-directory-b2c-get-started-custom.md).

4. Salvare il file delle estensioni.

## <a name="create-a-kmsi-in-enabled-user-journey"></a>Creare un KMSI nel percorso utente abilitato

È ora necessario aggiungere il provider di attestazioni di accesso all'account locale al percorso utente. 

1. Aprire il file di base dei criteri, ad esempio TrustFrameworkBase.xml.
2. Trovare l'elemento `<UserJourneys>` e copiare l'intero nodo `<UserJourney>` che include `Id="SignUpOrSignIn"`.
3. Aprire il file di estensione, ad esempio TrustFrameworkExtensions.xml, e trovare l'elemento `<UserJourneys>`. Se l'elemento non esiste, aggiungerne uno.
4. Incollare l'intero nodo `<UserJourney>` copiato come figlio dell'elemento `<UserJourneys>`.
5. Rinominare l'ID del nuovo percorso utente (ad esempio, rinominare come `SignUpOrSignInWithKmsi`).
6. Infine in `OrchestrationStep 1` modificare `ContentDefinitionReferenceId` in `api.signuporsigninwithkmsi`, come definito nei passaggi precedenti. In questo modo viene abilitata la casella di controllo nel percorso utente. 
7. La modifica del file di estensione è completata. Salvare e caricare il file. Verificare che tutte le convalide abbiano esito positivo.

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

## <a name="create-a-relying-party-rp-file"></a>Aggiungere un file relying party (RP)

È ora necessario aggiornare il file della relying party (RP) che avvierà il percorso utente appena creato:

1. Creare una copia di SignUpOrSignIn.xml nella directory di lavoro. Rinominare il file, ad esempio SignUpOrSignInWithKmsi.xml.

2. Aprire il nuovo file e aggiornare l'attributo `PolicyId` per `<TrustFrameworkPolicy>` con un valore univoco, che sarà il nome dei criteri, ad esempio SignUpOrSignInWithKmsi.

3. Modificare l'attributo `ReferenceId` in `<DefaultUserJourney>` in modo che corrisponda all'elemento `Id` del nuovo percorso utente creato, ad esempio SignUpOrSignInWithKmsi.

4. KMSI è configurato in `UserJourneyBehaviors`. 

5. **`KeepAliveInDays`** controlla per quanto tempo l'utente rimane connesso. Nell'esempio seguente la sessione KMSI scade automaticamente dopo 14 giorni indipendentemente dalla frequenza con cui l'utente esegue l'autenticazione automatica.

   Se si imposta il valore `KeepAliveInDays` su 0 la funzionalità KMSI viene disattivata. Per impostazione predefinita questo valore è 0

6. Se **`SessionExpiryType`** è *Rolling* (Continuo) la sessione KMSI viene estesa di 14 giorni ogni volta che l'utente esegue l'autenticazione automatica.  Se è selezionato *Rolling* (Continuo) si consiglia di indicare un numero di giorni minimo. 

       <RelyingParty>
       <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
       <UserJourneyBehaviors>
         <SingleSignOn Scope="Tenant" KeepAliveInDays="14" />
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

7. Salvare le modifiche e caricare il file.

8. Per testare i criteri personalizzati caricati, nel portale di Azure passare al pannello dei criteri e quindi fare clic su **Esegui ora**.


## <a name="link-to-sample-policy"></a>Collegamento ai criteri di esempio

È possibile trovare i criteri di esempio [qui](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).








