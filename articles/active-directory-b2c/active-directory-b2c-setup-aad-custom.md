---
title: 'Azure Active Directory B2C: aggiungere un provider di Azure AD usando i criteri personalizzati | Microsoft Docs'
description: Informazioni sui criteri personalizzati di Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 31f0dfe5-1ad0-4a25-a53b-8acc71bcea72
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: parakhj
ms.openlocfilehash: 6c073d70debfdc3560405955d65fa9ccaa7d8b1f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="azure-active-directory-b2c-sign-in-by-using-azure-ad-accounts"></a>Azure Active Directory B2C: accedere usando account di Azure AD

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo illustra come abilitare l'accesso per gli utenti da una specifica organizzazione di Azure Active Directory (Azure AD) tramite l'uso di [criteri personalizzati](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Prerequisiti

Completare la procedura descritta nell'articolo [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md).

La procedura include i passaggi seguenti:

1. Creazione di un tenant di Azure Active Directory B2C (Azure AD B2C).
2. Creazione di un'applicazione Azure AD B2C.
3. Registrazione di due applicazioni del motore di criteri.
4. Configurazione delle chiavi.
5. Configurazione del pacchetto Starter.

## <a name="create-an-azure-ad-app"></a>Creare un'app di Azure AD

Per abilitare l'accesso agli utenti da una specifica organizzazione di Azure AD, è necessario registrare un'applicazione all'interno del tenant aziendale di Azure AD.

>[!NOTE]
> Nelle istruzioni seguenti vengono usati "contoso.com" per il tenant di Azure AD dell'organizzazione e "fabrikamb2c.onmicrosoft.com" come tenant di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nella barra in alto selezionare il proprio account. Nell'elenco **Directory** scegliere il tenant di Azure AD dell'organizzazione in cui si desidera registrare l'applicazione (contoso.com).
1. Fare clic su **Altri servizi** nel riquadro a sinistra e cercare "Registrazioni per l'app".
1. Selezionare **Registrazione nuova applicazione**.
1. Immettere un nome per l'applicazione, ad esempio `Azure AD B2C App`.
1. Selezionare **App Web/API** per il tipo di applicazione.
1. Per **URL accesso** immettere l'URL seguente, dove `yourtenant` viene sostituito dal nome del tenant di Azure AD B2C (`fabrikamb2c.onmicrosoft.com`):

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Salvare l'ID applicazione.
1. Selezionare l'applicazione appena creata.
1. Nel pannello **Impostazioni** selezionare **Chiavi**.
1. Creare una nuova chiave e salvarla. Questa chiave sarà necessaria nei passaggi della prossima sezione.

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>Aggiungere la chiave di Azure AD in Azure AD B2C

È necessario archiviare la chiave dell'applicazione contoso.com nel tenant di Azure AD B2C. A tale scopo, seguire questa procedura:
1. Passare al tenant di Azure AD B2C e selezionare **B2C Settings** (Impostazioni B2C) > **Framework dell'esperienza di gestione delle identità** > **Chiavi dei criteri**.
1. Selezionare **+Aggiungi**.
1. Selezionare o immettere queste opzioni:
   * Selezionare **Manuale**.
   * Per **Nome** scegliere un nome che corrisponda al nome del tenant di Azure AD, ad esempio `ContosoAppSecret`.  Verrà aggiunto automaticamente il prefisso `B2C_1A_` al nome della chiave.
   * Incollare la chiave dell'applicazione nella casella **Segreto**.
   * Selezionare **Firma**.
1. Selezionare **Crea**.
1. Confermare di avere creato la chiave `B2C_1A_ContosoAppSecret`.


## <a name="add-a-claims-provider-in-your-base-policy"></a>Aggiungere un provider di attestazioni nel criterio di base

Per consentire agli utenti di accedere con Azure AD, è necessario definire Azure AD come provider di attestazioni. In altre parole, è necessario specificare un endpoint con cui Azure AD B2C comunicherà. L'endpoint offrirà un set di attestazioni che verranno usate da Azure AD B2C per verificare se un utente specifico è stato autenticato. 

È possibile definire Azure AD come provider di attestazioni aggiungendo Azure AD al nodo `<ClaimsProvider>` nel file di estensione dei criteri:

1. Aprire il file di estensione (TrustFrameworkExtensions.xml) dalla directory di lavoro.
1. Trovare la sezione `<ClaimsProviders>`. Se non esiste, aggiungerla nel nodo radice.
1. Aggiungere un nuovo nodo `<ClaimsProvider>` come mostrato di seguito:

    ```XML
    <ClaimsProvider>
        <Domain>Contoso</Domain>
        <DisplayName>Login using Contoso</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="ContosoProfile">
                <DisplayName>Contoso Employee</DisplayName>
                <Description>Login with your Contoso account</Description>
                <Protocol Name="OpenIdConnect"/>
                <OutputTokenFormat>JWT</OutputTokenFormat>
                <Metadata>
                    <Item Key="METADATA">https://login.windows.net/contoso.com/.well-known/openid-configuration</Item>
                    <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
                    <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="IdTokenAudience">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="response_types">id_token</Item>
                    <Item Key="UsePolicyInRedirectUri">false</Item>
                </Metadata>
                <CryptographicKeys>
                    <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
                </CryptographicKeys>
                <OutputClaims>
                    <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="oid"/>
                    <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
                    <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
                    <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
                    <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
                    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="contosoAuthentication" />
                    <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="AzureADContoso" />
                </OutputClaims>
                <OutputClaimsTransformations>
                    <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
                    <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
                    <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
                    <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
                </OutputClaimsTransformations>
                <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
            </TechnicalProfile>
        </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Nel nodo `<ClaimsProvider>` aggiornare il valore di `<Domain>` con un valore univoco che consenta di distinguerlo da altri provider di identità.
1. Nel nodo `<ClaimsProvider>` aggiornare il valore di `<DisplayName>` con un nome descrittivo per il provider di attestazioni. Questo valore non è attualmente usato.

### <a name="update-the-technical-profile"></a>Aggiornare il profilo tecnico

Per ottenere un token dall'endpoint di Azure AD, è necessario definire i protocolli che Azure AD B2C deve usare per comunicare con Azure AD. Questa operazione viene eseguita all'interno dell'elemento `<TechnicalProfile>` di `<ClaimsProvider>`.
1. Aggiornare l'ID del nodo `<TechnicalProfile>`. Questo ID viene usato per fare riferimento al profilo tecnico da altre parti dei criteri.
1. Aggiornare il valore di `<DisplayName>`. Questo valore verrà visualizzato sul pulsante di accesso nella schermata di accesso.
1. Aggiornare il valore di `<Description>`.
1. Azure AD usa il protocollo OpenID Connect, pertanto è necessario verificare che il valore di `<Protocol>` sia `"OpenIdConnect"`.

È necessario aggiornare la sezione `<Metadata>` nel file XML precedente in modo da riflettere le impostazioni di configurazione per il tenant di Azure AD specifico. Nel file XML aggiornare i valori dei metadati come mostrato di seguito:

1. Impostare `<Item Key="METADATA">` su `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, dove `yourAzureADtenant` è il nome del tenant di Azure AD, ad esempio, contoso.com.
1. Aprire il browser e passare all'URL `METADATA` appena aggiornato.
1. Nel browser cercare l'oggetto "emittente" e copiarne il valore. L'aspetto dovrebbe essere simile al seguente: `https://sts.windows.net/{tenantId}/`.
1. Incollare il valore per `<Item Key="ProviderName">` nel file XML.
1. Impostare `<Item Key="client_id">` sull'ID dell'applicazione al momento della registrazione dell'app.
1. Impostare `<Item Key="IdTokenAudience">` sull'ID dell'applicazione al momento della registrazione dell'app.
1. Assicurarsi che `<Item Key="response_types">` sia impostato su `id_token`.
1. Assicurarsi che `<Item Key="UsePolicyInRedirectUri">` sia impostato su `false`.

È anche necessario collegare il segreto di Azure AD registrato nel tenant di Azure AD B2C alle informazioni `<ClaimsProvider>` di Azure AD:

* Nella sezione `<CryptographicKeys>` del file XML precedente, aggiornare il valore di `StorageReferenceId` all'ID di riferimento del segreto definito, ad esempio `ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Caricare il file di estensione per la verifica

I criteri sono stati a questo punto configurati in modo che Azure AD B2C possa comunicare con la directory di Azure AD. Provare a caricare il file di estensione dei criteri per verificare che non siano presenti problemi. A tale scopo, procedere come segue:

1. Aprire il pannello **Tutti i criteri** nel tenant di Azure AD B2C.
1. Selezionare la casella **Sovrascrivi il criterio se esistente**.
1. Caricare il file di estensione (TrustFrameworkExtensions.xml) e assicurarsi che non presenti errori di convalida.

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>Registrare il provider di attestazioni di Azure AD in un percorso utente

È ora necessario aggiungere il provider di identità Azure AD in uno dei percorsi utente. Il provider di identità è a questo punto configurato, ma non è disponibile in alcuna delle schermate di iscrizione/accesso. Perché diventi disponibile, occorre creare un duplicato di un percorso utente modello esistente e quindi modificarlo in modo che includa anche il provider di identità Azure AD:

1. Aprire il file di base dei criteri, ad esempio TrustFrameworkBase.xml.
1. Trovare l'elemento `<UserJourneys>` e copiare l'intero nodo `<UserJourney>` che include `Id="SignUpOrSignIn"`.
1. Aprire il file di estensione, ad esempio TrustFrameworkExtensions.xml, e trovare l'elemento `<UserJourneys>`. Se l'elemento non esiste, aggiungerne uno.
1. Incollare l'intero nodo `<UserJourney>` copiato come figlio dell'elemento `<UserJourneys>`.
1. Rinominare l'ID del nuovo percorso utente (ad esempio, rinominare come `SignUpOrSignUsingContoso`).

### <a name="display-the-button"></a>Visualizzare il "pulsante"

L'elemento `<ClaimsProviderSelection>` è analogo a un pulsante del provider di identità in una schermata di iscrizione/accesso. Se si aggiunge un elemento `<ClaimsProviderSelection>` per Azure AD, verrà visualizzato un nuovo pulsante quando un utente apre la pagina. Per aggiungere questo elemento:

1. Trovare il nodo `<OrchestrationStep>` che include `Order="1"` nel percorso utente appena creato.
1. Aggiungere quanto segue:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. Impostare `TargetClaimsExchangeId` su un valore appropriato. È consigliabile seguire la stessa convenzione degli altri: *\[NomeProviderAttestazioni\]Exchange*.

### <a name="link-the-button-to-an-action"></a>Collegare il pulsante a un'azione

Ora che il pulsante è stato posizionato, è necessario collegarlo a un'azione. L'azione, in questo caso, consiste nel far comunicare Azure AD B2C con Azure AD per ricevere un token. È possibile farlo collegando il profilo tecnico per il provider di attestazioni di Azure AD:

1. Trovare l'oggetto `<OrchestrationStep>` che include `Order="2"` nel nodo `<UserJourney>`.
1. Aggiungere quanto segue:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. Aggiornare `Id` allo stesso valore di `TargetClaimsExchangeId` nella sezione precedente.
1. Aggiornare `TechnicalProfileReferenceId` all'ID del profilo tecnico creato in precedenza, ad esempio ContosoProfile.

### <a name="upload-the-updated-extension-file"></a>Caricare il file di estensione aggiornato

La modifica del file di estensione è completata. Salvare il file. Caricare il file e assicurarsi che tutte le convalide abbiano esito positivo.

### <a name="update-the-rp-file"></a>Aggiornare il file RP

È ora necessario aggiornare il file della relying party (RP) che avvierà il percorso utente appena creato:

1. Creare una copia di SignUpOrSignIn.xml nella directory di lavoro e rinominarla, ad esempio in SignUpOrSignInWithAAD.xml.
1. Aprire il nuovo file e aggiornare l'attributo `PolicyId` per `<TrustFrameworkPolicy>` con un valore univoco, ad esempio SignUpOrSignInWithAAD, <br> che sarà il nome dei criteri, ad esempio B2C\_1A\_SignUpOrSignInWithAAD.
1. Modificare l'attributo `ReferenceId` in `<DefaultUserJourney>` in modo che corrisponda all'ID del nuovo percorso utente creato, ad esempio SignUpOrSignUsingContoso.
1. Salvare le modifiche e caricare il file.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Testare i criteri personalizzati appena caricati aprendo il relativo pannello e facendo clic su **Esegui adesso**. Per diagnosticare i problemi, leggere l'articolo relativo alla [risoluzione dei problemi](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Passaggi successivi

Inviare commenti e suggerimenti all'indirizzo [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).
