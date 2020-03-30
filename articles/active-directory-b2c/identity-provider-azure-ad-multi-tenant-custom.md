---
title: Configurare l'accesso per Azure AD multi-tenant in base a criteri personalizzatiSet up sign-in for multi-tenant Azure AD by custom policies
titleSuffix: Azure AD B2C
description: Aggiungere un provider di identità di Azure AD multi-tenant usando criteri personalizzati in Azure Active Directory B2C.Add a multi-tenant Azure AD identity provider using custom policies in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9ad51e113a752e0692cb377a83d4819b4e284bb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188435"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Configurare l'accesso per Azure Active Directory multi-tenant usando criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo illustra come abilitare l'accesso per gli utenti usando l'endpoint multi-tenant per Azure Active Directory (Azure AD) usando criteri personalizzati in Azure AD B2C.This article shows you how to enable sign-in for users using the multi-tenant endpoint for Azure Active Directory (Azure AD) by using [custom policies](custom-policy-overview.md) in Azure AD B2C. Ciò consente agli utenti di più tenant di Azure AD di accedere usando Azure AD B2C, senza dover configurare un provider di identità per ogni tenant. Tuttavia, i membri guest di questi tenant **non** saranno in grado di accedere. A tale scopo, è necessario [configurare singolarmente ogni tenant](identity-provider-azure-ad-single-tenant-custom.md).

## <a name="prerequisites"></a>Prerequisiti

Completare le procedure illustrate in [Introduzione ai criteri personalizzati in Azure Active Directory B2C](custom-policy-get-started.md).

## <a name="register-an-application"></a>Registrare un'applicazione

Per abilitare l'accesso agli utenti da una specifica organizzazione di Azure AD, è necessario registrare un'applicazione all'interno del tenant aziendale di Azure AD.

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD dell'organizzazione (ad esempio, contoso.com). Nel menu superiore, selezionare il **filtro Directory e sottoscrizione** e quindi scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
1. Selezionare **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio: `Azure AD B2C App`.
1. Selezionare **Account in qualsiasi directory dell'organizzazione** per questa applicazione.
1. Per l'URI di **reindirizzamento**, accettare il valore di **Web** `your-B2C-tenant-name` e immettere l'URL seguente in lettere minuscole, dove viene sostituito con il nome del tenant B2C di Azure AD.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Ad esempio: `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

1. Selezionare **Registra**. Prendere nota del valore di **ID applicazione (client)**, che sarà necessario in un passaggio successivo.
1. Selezionare **Certificati & segreti**, quindi Nuovo **segreto client**.
1. Immettere una **Descrizione** per il segreto, selezionare una scadenza e quindi selezionare **Aggiungi**. Registrare il **valore** del segreto per l'utilizzo in un passaggio successivo.

## <a name="configuring-optional-claims"></a>Configurazione di attestazioni facoltative

Se si vuole `family_name` ottenere `given_name` le attestazioni e di Azure AD, è possibile configurare attestazioni facoltative per l'applicazione nell'interfaccia utente del portale di Azure o nel manifesto dell'applicazione. Per altre informazioni, vedere [Come fornire attestazioni facoltative all'app Azure AD.](../active-directory/develop/active-directory-optional-claims.md)

1. Accedere al [portale](https://portal.azure.com)di Azure . Cercare e selezionare **Azure Active Directory**.
1. Nella sezione **Gestisci** selezionare **Registrazioni app**.
1. Selezionare l'applicazione per cui si desidera configurare attestazioni facoltative nell'elenco.
1. Nella sezione **Gestisci** selezionare **Configurazione token (anteprima).**
1. Selezionare **Aggiungi attestazione facoltativa**.
1. Selezionare il tipo di token che si desidera configurare.
1. Selezionare le attestazioni facoltative da aggiungere.
1. Fare clic su **Aggiungi**.

## <a name="create-a-policy-key"></a>Creare una chiave dei criteri

È necessario archiviare la chiave dell'applicazione creata nel tenant di Azure AD B2C.

1. Assicurarsi di usare la directory che contiene il tenant B2C di Azure AD. Nel menu superiore, selezionare il **filtro Directory e sottoscrizione** e quindi scegliere la directory contenente il tenant B2C di Azure AD.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. In **Criteri**selezionare **Framework di esperienza identità**.
1. Selezionare **Chiavi dei criteri** e quindi **Aggiungi**.
1. Per **Opzioni** scegliere `Manual`.
1. Immettere un **nome** per la chiave dei criteri. Ad esempio: `AADAppSecret`.  Il `B2C_1A_` prefisso viene aggiunto automaticamente al nome della chiave al momento della creazione, pertanto il relativo riferimento nel codice XML nella sezione seguente deve *B2C_1A_AADAppSecret.*
1. In **Segreto**immettere il segreto client registrato in precedenza.
1. In **Uso chiave** selezionare `Signature`.
1. Selezionare **Crea**.

## <a name="add-a-claims-provider"></a>Aggiungere un provider di attestazioni

Per consentire agli utenti di accedere con Azure AD, è necessario definire Azure AD come provider di attestazioni con cui Azure AD B2C possa comunicare tramite un endpoint. L'endpoint offre un set di attestazioni che vengono usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

È possibile definire Azure AD come provider di attestazioni aggiungendo Azure AD all'elemento **ClaimsProvider** nel file di estensione dei criteri.

1. Aprire il file *TrustFrameworkExtensions.xml*.
1. Trovare l'elemento **ClaimsProviders**. Se non esiste, aggiungerlo nell'elemento radice.
1. Aggiungere un nuovo **ClaimsProvider** come illustrato di seguito:

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Nell'elemento **ClaimsProvider** aggiornare il valore di **Domain** con un valore univoco che ne consenta la distinzione rispetto ad altri provider di identità.
1. Nell'elemento **TechnicalProfile** aggiornare il valore di `Contoso Employee` **DisplayName,** ad esempio . Questo valore verrà visualizzato sul pulsante di accesso nella pagina di accesso.
1. Impostare **client_id** sull'ID applicazione dell'applicazione multi-tenant di Azure AD registrata in precedenza.
1. In **CryptographicKeys**aggiornare il valore di **StorageReferenceId** al nome della chiave dei criteri creata in precedenza. Ad esempio: `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Limitare l'accesso

> [!NOTE]
> L'uso di `https://login.microsoftonline.com/` come valore di **ValidTokenIssuerPrefixes** consente a tutti gli utenti di Azure AD di accedere all'applicazione.

È necessario aggiornare l'elenco delle autorità emittenti di token valide e limitare l'accesso a un elenco specifico di utenti dei tenant di Azure AD che potranno accedere.

Per ottenere i valori, esaminare i metadati di individuazione di OpenID Connect per ogni tenant di Azure AD da cui si vuole che gli utenti accedano. Il formato dell'URL dei `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration`metadati è simile a , dove `your-tenant` è il nome del tenant di Azure AD. Ad esempio:

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

Eseguire questi passaggi per ogni tenant di Azure AD che deve essere usato per accedere:Perform these steps for each Azure AD tenant that should be used to sign in:

1. Aprire il browser e passare all'URL dei metadati OpenID Connect per il tenant. Individuare l'oggetto **autorità emittente** e registrarne il valore. Dovrebbe essere simile `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/`a .
1. Copiare e incollare il valore nella chiave **ValidTokenIssuerPrefixes.** Separare più emittenti con una virgola. Un esempio con due emittenti viene visualizzato nell'esempio XML precedente. `ClaimsProvider`

### <a name="upload-the-extension-file-for-verification"></a>Caricare il file di estensione per la verifica

A questo punto, i criteri sono stati configurati in modo che Azure AD B2C sappia comunicare con le directory di Azure AD. Provare a caricare il file di estensione dei criteri per verificare che non siano presenti problemi.

1. Nella pagina **Criteri personalizzati** del tenant di Azure AD B2C selezionare **Carica il criterio**.
2. Abilitare **Sovrascrivi il criterio se esistente** e quindi cercare e selezionare il file *TrustFrameworkExtensions.xml*.
3. Selezionare **Carica**.

## <a name="register-the-claims-provider"></a>Registrare il provider di attestazioni

Il provider di identità è a questo punto configurato, ma non è disponibile in alcuna delle schermate di iscrizione/accesso. Per renderlo disponibile, si crea un duplicato di un percorso utente modello esistente e quindi si modifica tale duplicato in modo che includa anche il provider di identità Azure AD.

1. Aprire il file *TrustFrameworkBase.xml* dallo starter pack.
2. Trovare e copiare l'intero contenuto dell'elemento **UserJourney** che include `Id="SignUpOrSignIn"`.
3. Aprire *TrustFrameworkExtensions.xml* e trovare l'elemento **UserJourneys**. Se l'elemento non esiste, aggiungerne uno.
4. Incollare l'intero contenuto dell'elemento **UserJourney** copiato come figlio dell'elemento **UserJourneys**.
5. Rinominare l'ID del percorso utente. Ad esempio: `SignUpSignInContoso`.

### <a name="display-the-button"></a>Visualizzare il pulsante

L'elemento **ClaimsProviderSelection** è analogo a un pulsante per il provider di identità in una schermata di iscrizione/accesso. Se si aggiunge un elemento **ClaimsProviderSelection** per Azure AD, quando un utente apre la pagina viene visualizzato un nuovo pulsante.

1. Individuare l'elemento **OrchestrationStep** che include `Order="1"` il percorso utente creato in *TrustFrameworkExtensions.xml*.
1. In **ClaimsProviderSelects** aggiungere l'elemento riportato di seguito. Impostare **TargetClaimsExchangeId** su un valore appropriato, ad esempio `AzureADExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Collegare il pulsante a un'azione

Ora che il pulsante è stato posizionato, è necessario collegarlo a un'azione. L'azione, in questo caso, consiste nel far comunicare Azure AD B2C con Azure AD per ricevere un token. Collegare il pulsante a un'azione collegando il profilo tecnico per il provider di attestazioni Azure AD.

1. Trovare l'elemento **OrchestrationStep** che include `Order="2"` nel percorso utente.
2. Aggiungere l'elemento **ClaimsExchange** seguente assicurandosi di usare per **Id** lo stesso valore che è stato usato per **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    Aggiornare il valore di **TechnicalProfileReferenceId** con l'**ID** del profilo tecnico creato in precedenza. Ad esempio: `Common-AAD`.

3. Salvare il file *TrustFrameworkExtensions.xml* e caricarlo di nuovo per la verifica.

## <a name="create-an-azure-ad-b2c-application"></a>Creare un'applicazione Azure AD B2C

La comunicazione con Azure AD B2C avviene tramite un'applicazione che viene eseguita nella registrazione nel tenant B2C. Questa sezione elenca i passaggi facoltativi che è possibile completare per creare un'applicazione di test, se non è già stato fatto.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aggiornare e testare il file di relying party

Aggiornare il file della relying party (RP) che avvia il percorso utente creato:

1. Creare una copia di *SignUpOrSignIn.xml* nella directory di lavoro e rinominare la copia. Ad esempio, assegnare il nome *SignUpSignContoso.xml*.
1. Aprire il nuovo file e aggiornare il valore dell'attributo **PolicyId** per **TrustFrameworkPolicy** con un valore univoco. Ad esempio: `SignUpSignInContoso`.
1. Aggiornare il valore di **PublicPolicyUri** con l'URI dei criteri. Ad esempio: `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Aggiornare il valore dell'attributo **ReferenceId** in **DefaultUserJourney** in modo che corrisponda all'ID del percorso utente creato in precedenza. Ad esempio, *SignUpSignInContoso*.
1. Salvare le modifiche e caricare il file.
1. In **Criteri personalizzati**selezionare il nuovo criterio nell'elenco.
1. Nell'elenco a discesa **Seleziona applicazione** selezionare l'applicazione B2C di Azure AD creata in precedenza. Ad esempio, *testapp1*.
1. Copia **l'endpoint Esegui ora** e aprilo in una finestra del browser privata, ad esempio la modalità di navigazione in incognito in Google Chrome o una finestra InPrivate in Microsoft Edge. L'apertura in una finestra del browser privato consente di testare il percorso utente completo non usando le credenziali di Azure AD attualmente memorizzate nella cache.
1. Selezionare il pulsante di accesso ad Azure AD, ad esempio *Dipendente Contoso*, quindi immettere le credenziali di un utente in uno dei tenant dell'organizzazione di Azure AD. Ti viene chiesto di autorizzare l'applicazione e quindi di inserire le informazioni per il tuo profilo.

Se il processo di accesso ha esito `https://jwt.ms`positivo, il browser viene reindirizzato a , che visualizza il contenuto del token restituito da Azure AD B2C.

Per testare la funzionalità di accesso multi-tenant, eseguire gli ultimi due passaggi usando le credenziali per un utente che esiste un altro tenant di Azure AD.

## <a name="next-steps"></a>Passaggi successivi

Quando si utilizzano criteri personalizzati, talvolta potrebbero essere necessarie informazioni aggiuntive durante la risoluzione dei problemi di un criterio durante lo sviluppo.

Per facilitare la diagnosi dei problemi, è possibile attivare temporaneamente i criteri in "modalità sviluppatore" e raccogliere log con Azure Application Insights.To help diagnose issues, you can temporarily put the policy into "developer mode" and collect logs with Azure Application Insights. Informazioni su come in [Azure Active Directory B2C: raccolta di log](troubleshoot-with-application-insights.md).
