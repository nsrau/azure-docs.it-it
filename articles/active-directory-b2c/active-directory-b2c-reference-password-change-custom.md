---
title: 'Azure Active Directory B2C: modifica della password self-service| Microsoft Docs'
description: Un argomento che illustra come configurare la modifica della password self-service per gli utenti in Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: ajalexander
ms.assetid: 712a7128-5788-4914-8a52-24e200aa4de1
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2016
ms.author: vigunase
ms.openlocfilehash: b152c22c96da38f8724010504cc2711ab82af00a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-configure-password-change-in-custom-policies"></a>Azure Active Directory B2C: configurare la modifica della password nei criteri personalizzati  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Con la funzionalità di modifica della password i consumer connessi (che usano account locali) possono modificare le proprie password senza la necessità di dimostrare l'autenticità con verifica di posta elettronica, come descritto nel [self-service password reset flow](active-directory-b2c-reference-sspr.md) (Flusso di reimpostazione della password self-service) Se la sessione scade nel momento in cui il consumer arriva al flusso di modifica della password gli viene richiesto di accedere nuovamente. 

## <a name="prerequisites"></a>Prerequisiti

Un tenant di Azure AD B2C configurato per completare una procedura di iscrizione/accesso di un account locale, come descritto in [Introduzione](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-change-in-custom-policy"></a>Come configurare la modifica delle password nei criteri personalizzati

Per configurare la modifica della password nei criteri personalizzati apportare le modifiche seguenti nei criteri delle estensioni dei framework attendibilità, 

## <a name="define-a-claimtype-oldpassword"></a>Definire un elemento ClaimType 'oldPassword'

La struttura generale dei criteri personalizzati deve includere uno `ClaimsSchema` e definire un nuovo `ClaimType` 'oldPassword' come indicato di seguito, 

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

Di seguito viene descritto lo scopo di questi elementi:

- L'elemento `ClaimsSchema` definisce l'attestazione da convalidare  In questo caso 'old password' verrà convalidata. 

## <a name="add-a-password-change-claims-provider-with-its-supporting-elements"></a>Aggiungere un provider di attestazioni di modifica password con i relativi elementi di supporto

Il provider di attestazioni di modifica password

1. Autenticherà l'utente con la vecchia password
2. E se 'new password' corrisponde a 'confirm new password', questo valore viene archiviato nell'archivio dati B2C e la password viene modificata correttamente. 

![Immagine](images/passwordchange.jpg)

Aggiungere il provider di attestazioni seguente ai criteri delle estensioni. 

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



### <a name="add-the-application-ids-to-your-custom-policy"></a>Aggiungere le ID dell'applicazione al criterio personalizzato

Aggiungere gli ID applicazione al file di estensione (`TrustFrameworkExtensions.xml`):

1. Nel file delle estensioni (TrustFrameworkExtensions.xml) trovare gli elementi `<TechnicalProfile Id="login-NonInteractive">` e `<TechnicalProfile Id="login-NonInteractive-PasswordChange">`

2. Sostituire tutte le istanze di `IdentityExperienceFrameworkAppId` con l'ID dell'applicazione del framework dell'esperienza di gestione delle identità come descritto in [Introduzione](active-directory-b2c-get-started-custom.md). Di seguito è fornito un esempio:

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. Sostituire tutte le istanze di `ProxyIdentityExperienceFrameworkAppId` con l'ID dell'applicazione del framework dell'esperienza di gestione delle identità Proxy come descritto in [Introduzione](active-directory-b2c-get-started-custom.md).

4. Salvare il file delle estensioni.



## <a name="create-a-password-change-user-journey"></a>Creare un percorso utente di modifica della password

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

La modifica del file di estensione è completata. Salvare e caricare il file. Verificare che tutte le convalide abbiano esito positivo.



## <a name="create-a-relying-party-rp-file"></a>Aggiungere un file relying party (RP)

È ora necessario aggiornare il file della relying party (RP) che avvierà il percorso utente appena creato:

1. Creare una copia di ProfileEdit.xml nella directory di lavoro. Quindi rinominare la cartella, ad esempio, PasswordChange.xml.
2. Aprire il nuovo file e aggiornare l'attributo `PolicyId` per `<TrustFrameworkPolicy>` con un valore univoco, che sarà il nome dei criteri, ad esempio PasswordChange.
3. Modificare l'attributo `ReferenceId` in `<DefaultUserJourney>` in modo che corrisponda all'elemento `Id` del nuovo percorso utente creato, ad esempio PasswordChange.
4. Salvare le modifiche e caricare il file.
5. Per testare i criteri personalizzati caricati, nel portale di Azure passare al pannello dei criteri e quindi fare clic su **Esegui ora**.




## <a name="link-to-password-change-sample-policy"></a>Collegamento ai criteri di esempio di modifica della password

È possibile trovare i criteri di esempio [qui](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change). 










