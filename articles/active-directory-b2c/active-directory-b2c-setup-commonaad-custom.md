---
title: Configurare l'accesso per un provider di identità di Azure AD multi-tenant usando criteri personalizzati in Azure Active Directory B2C
description: Aggiungere un provider di identità di Azure AD multi-tenant tramite criteri personalizzati - Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fdad2f2dfec6f13fe4a40641db3417f29273349c
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315042"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Configurare l'accesso per Azure Active Directory multi-tenant usando criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo illustra come consentire agli utenti l'accesso con l'endpoint multi-tenant per Azure Active Directory (Azure AD) usando [criteri personalizzati](active-directory-b2c-overview-custom.md) in Azure AD B2C. Questo consente agli utenti di più tenant Azure AD di accedere usando Azure AD B2C, senza dover configurare un provider di identità per ogni tenant. Tuttavia, i membri guest di questi tenant **non** saranno in grado di accedere. A tale scopo, è necessario [configurare singolarmente ogni tenant](active-directory-b2c-setup-aad-custom.md).

## <a name="prerequisites"></a>Prerequisiti

Completare le procedure illustrate in [Introduzione ai criteri personalizzati in Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Registra un'applicazione

Per abilitare l'accesso agli utenti da una specifica organizzazione di Azure AD, è necessario registrare un'applicazione all'interno del tenant aziendale di Azure AD.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant del Azure AD aziendale (ad esempio, contoso.com). Selezionare il **filtro directory + sottoscrizione** nel menu in alto e quindi scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
1. Selezionare **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio `Azure AD B2C App`.
1. Selezionare gli **account in qualsiasi directory organizzativa** per questa applicazione.
1. Per l' **URI di reindirizzamento**accettare il valore di **Web**e immettere l'URL seguente in lettere minuscole, dove `your-B2C-tenant-name` viene sostituito con il nome del tenant Azure ad B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Ad esempio `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

1. Selezionare **Registra**. Registrare l' **ID dell'applicazione (client)** per l'uso in un passaggio successivo.
1. Selezionare **certificati & segreti**, quindi selezionare **nuovo segreto client**.
1. Immettere una **Descrizione** per il segreto, selezionare una scadenza e quindi selezionare **Aggiungi**. Registrare il **valore** del segreto da usare in un passaggio successivo.

## <a name="create-a-policy-key"></a>Creare una chiave dei criteri

È necessario archiviare la chiave dell'applicazione creata nel tenant di Azure AD B2C.

1. Assicurarsi di usare la directory che contiene il tenant del Azure AD B2C. Selezionare il **filtro directory + sottoscrizione** nel menu in alto e quindi scegliere la directory che contiene il tenant Azure ad B2C.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. In **criteri**selezionare **Framework esperienza di identità**.
1. Selezionare **chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere `Manual`.
1. Immettere un **nome** per la chiave dei criteri. Ad esempio `AADAppSecret`.  Il prefisso `B2C_1A_` viene aggiunto automaticamente al nome della chiave quando viene creato, quindi il riferimento nel codice XML nella sezione seguente viene *B2C_1A_AADAppSecret*.
1. In **Secret**immettere il segreto client registrato in precedenza.
1. In **Uso chiave** selezionare `Signature`.
1. Selezionare **Create**.

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
            <Item Key="METADATA">https://login.windows.net/common/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->
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
1. Nell'elemento **TechnicalProfile** aggiornare il valore di **DisplayName**, `Contoso Employee`ad esempio. Questo valore verrà visualizzato sul pulsante di accesso nella pagina di accesso.
1. Impostare **client_id** sull'ID applicazione dell'Azure ad applicazione multi-tenant registrata in precedenza.
1. In **CryptographicKeys**aggiornare il valore di **ID riferimento archiviazione** con il nome della chiave dei criteri creata in precedenza. Ad esempio `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Limitare l'accesso

> [!NOTE]
> L'uso di `https://sts.windows.net` come valore di **ValidTokenIssuerPrefixes** consente a tutti gli utenti di Azure AD di accedere all'applicazione.

È necessario aggiornare l'elenco delle autorità emittenti di token valide e limitare l'accesso a un elenco specifico di utenti dei tenant di Azure AD che potranno accedere.

Per ottenere i valori, esaminare i metadati di individuazione di OpenID Connect per ognuno dei tenant di Azure AD a cui si vuole accedere gli utenti. Il formato dell'URL dei metadati è simile a `https://login.windows.net/your-tenant/.well-known/openid-configuration`, dove `your-tenant` è il nome del tenant Azure ad. Esempio:

`https://login.windows.net/fabrikam.onmicrosoft.com/.well-known/openid-configuration`

Eseguire questi passaggi per ogni tenant Azure AD da usare per l'accesso:

1. Aprire il browser e passare all'URL dei metadati di OpenID Connect per il tenant. Individuare l'oggetto **emittente** e registrarne il valore. Dovrebbe essere simile a `https://sts.windows.net/00000000-0000-0000-0000-000000000000/`.
1. Copiare e incollare il valore nella chiave **ValidTokenIssuerPrefixes** . Separare più autorità emittenti con una virgola. Nell'esempio XML precedente `ClaimsProvider` viene visualizzato un esempio con due autorità di certificazione.

### <a name="upload-the-extension-file-for-verification"></a>Caricare il file di estensione per la verifica

A questo punto, i criteri sono stati configurati in modo che Azure AD B2C sappiano come comunicare con le directory Azure AD. Provare a caricare il file di estensione dei criteri per verificare che non siano presenti problemi.

1. Nella pagina **Criteri personalizzati** del tenant di Azure AD B2C selezionare **Carica il criterio**.
2. Abilitare **Sovrascrivi il criterio se esistente** e quindi cercare e selezionare il file *TrustFrameworkExtensions.xml*.
3. Selezionare **Carica**.

## <a name="register-the-claims-provider"></a>Registrare il provider di attestazioni

Il provider di identità è a questo punto configurato, ma non è disponibile in alcuna delle schermate di iscrizione/accesso. Per renderlo disponibile, si crea un duplicato di un percorso utente modello esistente e quindi si modifica tale duplicato in modo che includa anche il provider di identità Azure AD.

1. Aprire il file *TrustFrameworkBase.xml* dallo starter pack.
2. Trovare e copiare l'intero contenuto dell'elemento **UserJourney** che include `Id="SignUpOrSignIn"`.
3. Aprire *TrustFrameworkExtensions.xml* e trovare l'elemento **UserJourneys**. Se l'elemento non esiste, aggiungerne uno.
4. Incollare l'intero contenuto dell'elemento **UserJourney** copiato come figlio dell'elemento **UserJourneys**.
5. Rinominare l'ID del percorso utente. Ad esempio `SignUpSignInContoso`.

### <a name="display-the-button"></a>Visualizzare il pulsante

L'elemento **ClaimsProviderSelection** è analogo a un pulsante per il provider di identità in una schermata di iscrizione/accesso. Se si aggiunge un elemento **ClaimsProviderSelection** per Azure AD, quando un utente apre la pagina viene visualizzato un nuovo pulsante.

1. Trovare l'elemento **OrchestrationStep** che include `Order="1"` nel percorso utente creato in *TrustFrameworkExtensions. XML*.
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

    Aggiornare il valore di **TechnicalProfileReferenceId** con l'**ID** del profilo tecnico creato in precedenza. Ad esempio `Common-AAD`.

3. Salvare il file *TrustFrameworkExtensions.xml* e caricarlo di nuovo per la verifica.

## <a name="create-an-azure-ad-b2c-application"></a>Creare un'applicazione Azure AD B2C

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aggiornare e testare il file di relying party

Aggiornare il file di relying party (RP) che avvia il percorso utente creato:

1. Creare una copia di *SignUpOrSignIn.xml* nella directory di lavoro e rinominare la copia. Ad esempio, assegnare il nome *SignUpSignContoso.xml*.
1. Aprire il nuovo file e aggiornare il valore dell'attributo **PolicyId** per **TrustFrameworkPolicy** con un valore univoco. Ad esempio `SignUpSignInContoso`.
1. Aggiornare il valore di **PublicPolicyUri** con l'URI dei criteri. Ad esempio `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Aggiornare il valore dell'attributo **ReferenceId** in **DefaultUserJourney** in modo che corrisponda all'ID del percorso utente creato in precedenza. Ad esempio, *SignUpSignInContoso*.
1. Salvare le modifiche e caricare il file.
1. In **criteri personalizzati**selezionare il nuovo criterio nell'elenco.
1. Nell'elenco a discesa **Seleziona applicazione** selezionare l'applicazione Azure ad B2C creata in precedenza. Ad esempio, *testapp1*.
1. Copiare l' **endpoint Run Now** e aprirlo in una finestra del browser privata, ad esempio in modalità in incognito in Google Chrome o in una finestra InPrivate in Microsoft Edge. L'apertura in una finestra del browser privata consente di testare il percorso utente completo senza usare le credenziali Azure AD attualmente memorizzate nella cache.
1. Selezionare il pulsante Azure AD accedi, ad esempio *Contoso Employee*, quindi immettere le credenziali per un utente in uno dei tenant dell'organizzazione Azure ad. Viene richiesto di autorizzare l'applicazione, quindi immettere le informazioni per il profilo.

Se il processo di accesso ha esito positivo, il browser viene `https://jwt.ms`reindirizzato a, che Visualizza il contenuto del token restituito da Azure ad B2C.

Per testare la funzionalità di accesso multi-tenant, eseguire gli ultimi due passaggi usando le credenziali per un utente che esiste un altro tenant Azure AD.

## <a name="next-steps"></a>Passaggi successivi

Quando si usano criteri personalizzati, a volte è necessario disporre di informazioni aggiuntive per la risoluzione dei problemi relativi a un criterio durante lo sviluppo.

Per semplificare la diagnosi dei problemi, è possibile impostare temporaneamente i criteri in "modalità di sviluppo" e raccogliere i log con applicazione Azure Insights. Scopri come [Azure Active Directory B2C: Raccolta dei](active-directory-b2c-troubleshoot-custom.md)log.
