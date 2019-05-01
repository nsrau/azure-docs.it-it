---
title: Configurare la modifica delle password usando criteri personalizzati in Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come abilitare gli utenti a modificare le password usando criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 1d05a5c4fa13b83abd26ee06123028f75a725582
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64729460"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Configurare la modifica delle password usando criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In Azure Active Directory (Azure AD) B2C è possibile abilitare gli utenti che hanno eseguito l'accesso con un account locale per modificare la password, senza doverne dimostrare l'autenticità con verifica tramite posta elettronica. Se la sessione scade nel momento in cui l'utente arriva al flusso di modifica della password, viene richiesto di accedere nuovamente. Questo articolo illustra come configurare la modifica delle password usando [criteri personalizzati](active-directory-b2c-overview-custom.md). È anche possibile configurare la [reimpostazione delle password self-service](active-directory-b2c-reference-sspr.md) usando i flussi utente.

## <a name="prerequisites"></a>Prerequisiti

Completare le procedure illustrate in [Introduzione ai criteri personalizzati in Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="add-the-elements"></a>Aggiungere gli elementi 

1. Aprire il file *TrustframeworkExtensions.xml* e aggiungere il seguente elemento **ClaimType** con un identificatore di `oldPassword` per l'elemento [ClaimsSchema](claimsschema.md): 

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Un elemento [ClaimsProvider](claimsproviders.md) contiene il profilo tecnico che autentica l'utente. Aggiungere i provider di attestazioni seguenti all'elemento **ClaimsProviders**:

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <Protocol Name="OpenIdConnect" />
            <Metadata>
              <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
              <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
              <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
              <Item Key="ProviderName">https://sts.windows.net/</Item>
              <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
              <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
              <Item Key="response_types">id_token</Item>
              <Item Key="response_mode">query</Item>
              <Item Key="scope">email openid</Item>
              <Item Key="UsePolicyInRedirectUri">false</Item>
              <Item Key="HttpBinding">POST</Item>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
              <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
              <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
              <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
              <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
              <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
              <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
              <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
              <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

    Sostituire `IdentityExperienceFrameworkAppId` con l'ID dell'applicazione IdentityExperienceFramework creata nell'esercitazione indicata tra i prerequisiti. Sostituire `ProxyIdentityExperienceFrameworkAppId` con l'ID dell'applicazione ProxyIdentityExperienceFramework creata in precedenza.

3. L'elemento [UserJourney](userjourneys.md) definisce il percorso dell'utente durante l'interazione con l'applicazione. Aggiungere l'elemento **UserJourneys**, se non esiste, con **UserJourney** identificato come `PasswordChange`:

    ```XML
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. Salvare il file dei criteri *TrustFrameworkExtensions.xml*.
5. Copiare il file *ProfileEdit* scaricato con il pacchetto starter e denominarlo *ProfileEditPasswordChange.xml*.
6. Aprire il nuovo file e aggiornare l'attributo **PolicyId** con un valore univoco. Questo valore è il nome dei criteri. Ad esempio, *B2C_1A_profile_edit_password_change*.
7. Modificare l'attributo **ReferenceId** in `<DefaultUserJourney>`, in modo che corrisponda all'ID del nuovo percorso utente creato. Ad esempio, *PasswordChange*.
8. Salvare le modifiche.

È possibile trovare i criteri di esempio [qui](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change). 

## <a name="test-your-policy"></a>Verificare i criteri

Durante il test delle applicazioni in Azure AD B2C, può essere utile avere restituito il token di Azure AD B2C a `https://jwt.ms` per riuscire a esaminare le attestazioni in essa.

### <a name="upload-the-files"></a>Caricare i file

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Fare clic su **Framework dell'esperienza di gestione delle identità**.
5. Nella pagina dei criteri personalizzati, fare clic su **Carica criterio**.
6. Selezionare **Sovrascrivi il criterio se esistente**, quindi cercare e selezionare il file *TrustframeworkExtensions.xml*.
7. Fare clic su **Carica**.
8. Ripetere i passaggi da 5 a 7 per il file relying party, ad esempio *ProfileEditPasswordChange.xml*.

### <a name="run-the-policy"></a>Esegui il criterio

1. Aprire il criterio che è stato modificato. Ad esempio, *B2C_1A_profile_edit_password_change*.
2. Per **Applicazione**, selezionare l'applicazione che è stata registrata in precedenza. Per visualizzare il token, l'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
3. Fare clic su **Esegui adesso**. Accedere con l'account creato in precedenza. È ora possibile modificare la password. 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [Configurare la complessità delle password usando criteri personalizzati in Azure Active Directory B2C](active-directory-b2c-reference-password-complexity-custom.md). 
