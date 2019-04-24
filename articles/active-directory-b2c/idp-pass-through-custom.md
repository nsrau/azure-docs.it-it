---
title: Passare un token di accesso tramite un criterio personalizzato all'applicazione in Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come passare un token di accesso per i provider di identità OAuth 2.0 come attestazione attraverso un criterio personalizzato all'applicazione in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 784387b119bff6445015419adfd3bc0e52eee43f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60397185"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Passare un token di accesso tramite un criterio personalizzato all'applicazione in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Un [criterio personalizzato](active-directory-b2c-get-started-custom.md) in Azure Active Directory (Azure AD) B2C fornisce agli utenti dell'applicazione un'opportunità di iscriversi o accedere con un provider di identità. Quando questo si verifica, Azure AD B2C riceve un [token di accesso](active-directory-b2c-reference-tokens.md) dal provider di identità. Azure AD B2C usa tale token per recuperare informazioni sull'utente. Aggiungere un tipo di attestazione e l'attestazione di output nel criterio personalizzato per passare il token attraverso le applicazioni registrate in Azure AD B2C. 

Azure AD B2C supporta il passaggio del token di accesso dei provider di identità [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) e [OpenID Connect](active-directory-b2c-reference-oidc.md). Per tutti gli altri provider di identità, l'attestazione viene restituita vuota.

## <a name="prerequisites"></a>Prerequisiti

- I criteri personalizzati sono configurati con un provider di identità OAuth 2.0 o OpenID Connect.

## <a name="add-the-claim-elements"></a>Aggiungere gli elementi di attestazione 

1. Aprire il file *TrustframeworkExtensions.xml* e aggiungere il seguente elemento **ClaimType** con un identificatore di `identityProviderAccessToken` per l'elemento **ClaimsSchema**:

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Aggiungere l'elemento **OutputClaim** all'elemento **TechnicalProfile** per ogni provider di identità OAuth 2.0 per cui si desidera il token di accesso. L'esempio seguente illustra l'elemento aggiunto al profilo tecnico di Facebook:

    ```XML
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. Salvare il file *TrustframeworkExtensions.xml*.
4. Aprire un file dei criteri relying party come *SignUpOrSignIn.xml* e aggiungere l'elemento **OutputClaim** elemento al **TechnicalProfile**:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. Salvare il file dei criteri.

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
8. Ripetere i passaggi da 5 a 7 per il file relying party, ad esempio *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Esegui il criterio

1. Aprire il criterio che è stato modificato. Ad esempio, *B2C_1A_signup_signin*.
2. Per **Applicazione**, selezionare l'applicazione che è stata registrata in precedenza. Per visualizzare il token nell'esempio seguente, l'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
3. Fare clic su **Esegui adesso**.

    Verrà visualizzato un testo simile al seguente esempio:

    ![Token decodificato](./media/idp-pass-through-custom/idp-pass-through-custom-token.png)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui token nelle [Informazioni di riferimento sul token di Azure Active Directory](active-directory-b2c-reference-tokens.md).





