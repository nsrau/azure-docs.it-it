---
title: Aggiungere un provider di identità di Azure AD multi-tenant mediante criteri personalizzati - Azure Active Directory B2C | Microsoft Docs
description: Aggiungere un provider di identità di Azure AD multi-tenant mediante criteri personalizzati - Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: parakhj
manager: alexsi
editor: parakhj
ms.assetid: 33c64001-5261-4ed9-8f46-b09839165250
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/14/2018
ms.author: parakhj
ms.openlocfilehash: d5e5ab1262a9d33fcf34cce91113f39c8c8936f4
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2018
ms.locfileid: "33200519"
---
# <a name="azure-active-directory-b2c-allow-users-to-sign-in-to-a-multi-tenant-azure-ad-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: consentire agli utenti di accedere a un provider di identità di AD Azure multi-tenant mediante criteri personalizzati

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo illustra come abilitare l'accesso per gli utenti usando l'endpoint multi-tenant per Azure Active Directory (Azure AD) tramite l'uso di [criteri personalizzati](active-directory-b2c-overview-custom.md). Ciò consente agli utenti da più tenant di Azure AD di accedere ad Azure Active Directory B2C senza configurare un provider tecnico per ogni tenant. Tuttavia, i membri guest di questi tenant **non** saranno in grado di accedere. A tal fine, è necessario [configurare singolarmente ogni tenant](active-directory-b2c-setup-aad-custom.md).

>[!NOTE]
> Nelle istruzioni seguenti vengono usati "contoso.com" per il tenant di Azure AD dell'organizzazione e "fabrikamb2c.onmicrosoft.com" come tenant di Azure AD B2C.

## <a name="prerequisites"></a>prerequisiti

Completare la procedura descritta nell'articolo [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md).

La procedura include i passaggi seguenti:
     
1. Creazione di un tenant di Azure Active Directory B2C (Azure AD B2C).
1. Creazione di un'applicazione Azure AD B2C.    
1. Registrazione di due applicazioni del motore di criteri.  
1. Configurazione delle chiavi. 
1. Configurazione del pacchetto Starter.

## <a name="step-1-create-a-multi-tenant-azure-ad-app"></a>Passaggio 1. Creare un'app Azure AD multi-tenant

Per abilitare l'accesso per gli utenti che usano l'endpoint multi-tenant di Azure AD, è necessario disporre di un'applicazione multi-tenant registrata in uno dei tenant di Azure AD. In questo articolo verrà illustrato come creare un'applicazione di Azure AD multi-tenant nel tenant di Azure AD B2C. Sarà quindi possibile consentire l'accesso per gli utenti tramite l'uso di tale applicazione di AD Azure multi-tenant.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nella barra in alto selezionare il proprio account. Dall'elenco **Directory** scegliere il tenant di Azure AD B2C per registrare l'applicazione di Azure AD (fabrikamb2c.onmicrosoft.com).
1. Fare clic su **Altri servizi** nel riquadro a sinistra e cercare "Registrazioni per l'app".
1. Selezionare **Registrazione nuova applicazione**.
1. Immettere un nome per l'applicazione, ad esempio `Azure AD B2C App`.
1. Selezionare **App Web/API** per il tipo di applicazione.
1. Per **URL accesso** immettere l'URL seguente, dove `yourtenant` viene sostituito dal nome del tenant di Azure AD B2C (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >Il valore di "yourtenant" deve essere in formato minuscolo in **URL accesso**.

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Salvare l'ID applicazione.
1. Selezionare l'applicazione appena creata.
1. Nel pannello **Impostazioni** selezionare **Proprietà**.
1. Impostare **Multi-tenant** su **Sì**.
1. Nel pannello **Impostazioni** selezionare **Chiavi**.
1. Creare una nuova chiave e salvarla. Questa chiave sarà necessaria nei passaggi della prossima sezione.

## <a name="step-2-add-the-azure-ad-key-to-azure-ad-b2c"></a>Passaggio 2. Aggiungere la chiave di Azure AD in Azure AD B2C

È necessario registrare la chiave dell'applicazione nelle impostazioni di Azure AD B2C. A tale scopo, effettuare l'operazione seguente:

1. Passare al menu Impostazioni per Azure AD B2C
1. Fare clic su **Framework dell'esperienza di gestione delle identità** > **Chiavi dei criteri**.
1. Selezionare **+Aggiungi**.
1. Selezionare o immettere queste opzioni:
   * Selezionare **Manuale**.
   * Per **Nome** scegliere un nome che corrisponda al nome del tenant di Azure AD, ad esempio `AADAppSecret`.  Verrà aggiunto automaticamente il prefisso `B2C_1A_` al nome della chiave.
   * Incollare la chiave dell'applicazione nella casella **Segreto**.
   * Selezionare **Firma**.
1. Selezionare **Create**.
1. Confermare di avere creato la chiave `B2C_1A_AADAppSecret`.

## <a name="step-3-add-a-claims-provider-in-your-base-policy"></a>Passaggio 3. Aggiungere un provider di attestazioni nel criterio di base

Per consentire agli utenti di accedere con Azure AD, è necessario definire Azure AD come provider di attestazioni. In altre parole, è necessario specificare un endpoint con cui Azure AD B2C comunicherà. L'endpoint offrirà un set di attestazioni che verranno usate da Azure AD B2C per verificare se un utente specifico è stato autenticato. 

È possibile definire Azure AD come provider di attestazioni aggiungendo Azure AD al nodo `<ClaimsProvider>` nel file di estensione dei criteri:

1. Aprire il file di estensione (TrustFrameworkExtensions.xml) dalla directory di lavoro.
1. Trovare la sezione `<ClaimsProviders>`. Se non esiste, aggiungerla nel nodo radice.
1. Aggiungere un nuovo nodo `<ClaimsProvider>` come mostrato di seguito:

```XML
<ClaimsProvider>
  <Domain>commonaad</Domain>
  <DisplayName>Common AAD</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Common-AAD">
      <DisplayName>Multi-Tenant AAD</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <!-- Update the Client ID below to the Application ID -->
        <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
        <Item Key="response_types">code</Item>
        <Item Key="scope">openid</Item>
        <Item Key="response_mode">form_post</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        
        <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

        <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
        <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->

      </Metadata>
      <CryptographicKeys>
      <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
        <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
        <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
        <OutputClaim ClaimTypeReferenceId="email" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
        <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
        <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. Nel nodo `<ClaimsProvider>` aggiornare il valore di `<Domain>` con un valore univoco che consenta di distinguerlo da altri provider di identità.
1. Nel nodo `<TechnicalProfile>` aggiornare il valore per `<DisplayName>`. Questo valore verrà visualizzato sul pulsante di accesso nella schermata di accesso.
1. Aggiornare il valore di `<Description>`.
1. Impostare `<Item Key="client_id">` sull'ID dell'applicazione al momento della registrazione dell'app Azure AD multi-tenant.

### <a name="step-31-restrict-access-to-a-specific-list-of-azure-ad-tenants"></a>Passaggio 3.1 Limitare l'accesso a un elenco specifico di tenant di Azure AD

> [!NOTE]
> L'uso di `https://sts.windows.net` come valore per **ValidTokenIssuerPrefixes** consentirà a TUTTI gli utenti di Azure AD di accedere all'applicazione.

È necessario aggiornare l'elenco delle autorità emittenti di token valide e limitare l'accesso a un elenco specifico di tenant di Azure AD a cui gli utenti possono accedere. Per ottenere i valori, è necessario esaminare i metadati per ognuno degli specifici tenant di Azure AD da cui si vuole che gli utenti possano accedere. Il formato dei dati simile al seguente: `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, dove `yourAzureADtenant` è il nome di tenant di Azure AD (contoso.com o un altro tenant di Azure AD).
1. Aprire il browser e passare all'URL dei metadati.
1. Nel browser cercare l'oggetto "emittente" e copiarne il valore. L'aspetto dovrebbe essere simile al seguente: `https://sts.windows.net/{tenantId}/`.
1. Incollare il valore per la chiave `ValidTokenIssuerPrefixes`. È possibile aggiungere più valori separandoli con una virgola. Un esempio è impostato come commento nell'esempio di codice XML precedente.

## <a name="step-4-register-the-azure-ad-account-claims-provider"></a>Passaggio 4. Registrare il provider di attestazioni dell'account Azure AD

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Passaggio 4.1: Creare una copia del percorso utente

È ora necessario aggiungere il provider di identità Azure AD in uno dei percorsi utente. Il provider di identità è a questo punto configurato, ma non è disponibile in alcuna delle schermate di iscrizione/accesso.

Perché diventi disponibile, occorre creare un duplicato di un percorso utente modello esistente e quindi modificarlo in modo che includa anche il provider di identità Azure AD:

1. Aprire il file di base dei criteri, ad esempio TrustFrameworkBase.xml.
1. Trovare l'elemento `<UserJourneys>` e copiare l'intero nodo `<UserJourney>` che include `Id="SignUpOrSignIn"`.
1. Aprire il file di estensione, ad esempio TrustFrameworkExtensions.xml, e trovare l'elemento `<UserJourneys>`. Se l'elemento non esiste, aggiungerne uno.
1. Incollare l'intero nodo `<UserJourney>` copiato come figlio dell'elemento `<UserJourneys>`.
1. Rinominare l'ID del nuovo percorso utente (ad esempio, rinominare come `SignUpOrSignUsingAzureAD`). 

### <a name="step-42-display-the-button"></a>Passaggio 4.2: Visualizzare il "pulsante"

L'elemento `<ClaimsProviderSelection>` è analogo a un pulsante del provider di identità in una schermata di iscrizione/accesso. Se si aggiunge un elemento `<ClaimsProviderSelection>` per Azure AD, verrà visualizzato un nuovo pulsante quando un utente apre la pagina. Per aggiungere questo elemento:

1. Trovare il nodo `<OrchestrationStep>` che include `Order="1"` nel percorso utente creato.
1. Aggiungere quanto segue:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

1. Impostare `TargetClaimsExchangeId` su un valore appropriato. È consigliabile seguire la stessa convenzione degli altri: *\[NomeProviderAttestazioni\]Exchange*.

### <a name="step-43-link-the-button-to-an-action"></a>Passaggio 4.3: Collegare il pulsante a un'azione

Ora che il pulsante è stato posizionato, è necessario collegarlo a un'azione. L'azione, in questo caso, consiste nel far comunicare Azure AD B2C con Azure AD per ricevere un token. È possibile farlo collegando il profilo tecnico per il provider di attestazioni di Azure AD:

1. Trovare l'oggetto `<OrchestrationStep>` che include `Order="2"` nel nodo `<UserJourney>`.
1. Aggiungere quanto segue:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

1. Aggiornare `Id` allo stesso valore di `TargetClaimsExchangeId` nella sezione precedente.
1. Aggiornare `TechnicalProfileReferenceId` all'ID del profilo tecnico creato in precedenza, ad esempio Common-AAD.

## <a name="step-5-create-a-new-rp-policy"></a>Passaggio 5: Creare nuovi criteri della relying party

È ora necessario aggiornare il file della relying party (RP) che avvierà il percorso utente appena creato:
 
1. Creare una copia di SignUpOrSignIn.xml nella directory di lavoro e rinominarla, ad esempio in SignUpOrSignInWithAAD.xml.  
1. Aprire il nuovo file e aggiornare l'attributo `PolicyId` per `<TrustFrameworkPolicy>` con un valore univoco, ad esempio SignUpOrSignInWithAAD, che sarà il nome dei criteri, ad esempio B2C\_1A\_SignUpOrSignInWithAAD. 
1. Modificare l'attributo `ReferenceId` in `<DefaultUserJourney>`, in modo che corrisponda all'ID del nuovo percorso utente creato, ad esempio SignUpOrSignUsingAzureAD. 
1. Salvare le modifiche e caricare il file. 

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Passaggio 6: Caricare i criteri nel tenant

1. Nel [portale di Azure](https://portal.azure.com) passare al [contesto del tenant Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e quindi selezionare **Azure AD B2C**.
1. Fare clic su **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **Tutti i criteri**.
1. Selezionare **Carica criteri**.
1. Selezionare la casella di controllo **Sovrascrivi il criterio se esistente**.
1. Caricare il file `TrustFrameworkExtensions.xml` e il file RP (ad esempio, `SignUpOrSignInWithAAD.xml`) e verificare che superino la convalida.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Passaggio 7: Testare i criteri personalizzati tramite Esegui adesso

1. Selezionare **Impostazioni di Azure AD B2C** e quindi **Framework dell'esperienza di gestione delle identità**.
    > [!NOTE]
    > Il comando Esegui adesso richiede che nel tenant sia preregistrata almeno un'applicazione. Per informazioni su come registrare le applicazioni, vedere l'articolo di [introduzione](active-directory-b2c-get-started.md) ad Azure AD B2C o l'articolo relativo alla [registrazione delle applicazioni](active-directory-b2c-app-registration.md).

1. Aprire i criteri personalizzati della relying party caricati in precedenza (*B2C\_1A\_SignUpOrSignInWithAAD*) e quindi selezionare **Esegui adesso**.
1. Ora dovrebbe essere possibile accedere usando l'account Azure AD.

## <a name="optional-step-8-register-the-azure-ad-account-claims-provider-to-the-profile-edit-user-journey"></a>(Facoltativo) Passaggio 8: Registrare il provider di attestazioni dell'account Azure AD nel percorso utente Profile-Edit

È anche possibile aggiungere il provider di identità dell'account Azure AD al percorso utente `ProfileEdit`. Per rendere disponibile il percorso utente, ripetere i passaggi da 4 a 6. Selezionare questa volta il nodo `<UserJourney>` che contiene `Id="ProfileEdit"`. Salvare, caricare e verificare i criteri.

## <a name="troubleshooting"></a>risoluzione dei problemi

Per diagnosticare i problemi, leggere l'articolo relativo alla [risoluzione dei problemi](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Passaggi successivi

Inviare commenti e suggerimenti all'indirizzo [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).
