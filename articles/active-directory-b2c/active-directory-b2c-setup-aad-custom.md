---
title: 'Azure Active Directory B2C: aggiunta del provider di Azure AD mediante i criteri personalizzati | Microsoft Docs'
description: Un argomento sui criteri personalizzati di Azure Active Directory B2C
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
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 652a2eb0db0e41c4706e06cd0cb2e97ce1eb6ab2
ms.contentlocale: it-it
ms.lasthandoff: 05/12/2017


---
# <a name="azure-active-directory-b2c-log-in-using-azure-ad-accounts"></a>Azure Active Directory B2C: accesso tramite gli account di Azure AD

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In questo articolo viene illustrato come abilitare l'accesso per gli utenti da una specifica organizzazione di Azure AD tramite l'uso dei [criteri personalizzati](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Prerequisiti

Accertarsi di aver completato tutti i passaggi che illustrano come a [iniziare ad usare i criteri personalizzati](active-directory-b2c-get-started-custom.md).

Sono inclusi:

1. Creazione di un tenant di Azure AD B2C.
1. Creazione di un'applicazione Azure AD B2C.
1. Registrazione di due applicazioni del motore di criteri.
1. Configurazione delle chiavi.
1. Configurazione del pacchetto Starter.

## <a name="create-an-azure-ad-app"></a>Creare un'app di Azure AD

Per abilitare l'accesso agli utenti da una determinata organizzazione di Azure AD, è necessario registrare un'applicazione all'interno del tenant aziendale di Azure AD.

>[!NOTE]
> Si farà riferimento al tenant aziendale di Azure AD con `contoso.com` e al tenant di Azure AD B2C con `fabrikamb2c.onmicrosoft.com`.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nella barra in alto fare clic sull'account e nell'elenco **Directory** scegliere il tenant aziendale di Azure AD in cui si vuole registrare l'applicazione, ad esempio contoso.com.
1. Fare clic su **Altri servizi** nella barra di spostamento a sinistra e scegliere **Registrazioni per l'app**.
1. Scegliere **Registrazione nuova applicazione**.
1. Digitare un **nome** per l'applicazione, ad esempio App Azure AD B2C
1. Selezionare **App Web/API** come Tipo di applicazione.
1. Per "URL accesso" immettere l'URL seguente, dove `yourtenant` deve essere sostituito dal nome del tenant di Azure AD B2C, ad esempio fabrikamb2c.onmicrosoft.com.

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Salvare **l'ID applicazione**.
1. Fare clic sull'app appena creata.
1. Nel pannello Impostazioni fare clic su **Chiavi**.
1. Creare una nuova chiave e salvarla per la sezione successiva.

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>Aggiungere la chiave di Azure AD in Azure AD B2C

È necessario archiviare la chiave dell'applicazione `contoso.com` nel tenant di Azure AD B2C. A tale scopo, seguire questa procedura:

1. Passare al tenant di Azure AD B2C e aprire B2C Settings (Impostazioni B2C)>Identity Experience Framework (Framework dell'esperienza delle identità)>Policy Keys (Chiavi dei criteri)
1. Fare clic su +Aggiungi
1. Opzioni:
 * Selezionare Opzione > `Manual`
 * Nome: > `ContosoAppSecret` Scegliere un nome corrispondente al nome del tenant di Azure AD.  Il prefisso B2C_1A_ verrà aggiunto automaticamente al nome della chiave.
 * Incollare la chiave applicazione nella casella di testo `Secret`
 * Selezionare la firma
1. Fare clic su`Create`.
1. Confermare che sia stata creata la chiave: `B2C_1A_ContosoAppSecret`


## <a name="add-a-claims-provider-in-your-base-policy"></a>Aggiungere un provider di attestazioni nel criterio di base

Per consentire agli utenti di accedere con Azure AD, è necessario definire Azure AD come provider di attestazioni. In altre parole, è necessario specificare un endpoint con cui Azure AD B2C comunicherà. L'endpoint *offrirà* un set di *attestazioni* che vengono usate da Azure AD B2C per verificare che un utente specifico è stato autenticato. È possibile farlo aggiungendo Azure AD come `<ClaimsProvider>` nel file di estensione dei criteri.

1. Aprire il file di estensione dalla directory di lavoro (TrustFrameworkExtensions.xml).
1. Trovare la sezione `<ClaimsProviders>`. Se non esiste, aggiungerla nel nodo radice.
1. Aggiungere un nuovo `<ClaimsProvider>` come illustrato di seguito:

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

1. Aggiornare l'ID del nodo `<TechnicalProfile>`. Questo ID viene usato per fare riferimento al profilo tecnico in altre parti dei criteri.
1. Aggiornare il valore di `<DisplayName>`. Questo valore verrà visualizzato sul pulsante di accesso nella schermata di accesso.
1. Aggiornare il valore per `<Description>`.
1. Azure AD usa il protocollo OpenID Connect, pertanto è necessario verificare che `<Protocol>` sia "OpenIDConnect".

È necessario aggiornare la sezione `<Metadata>` nel file XML precedente in modo da riflettere le impostazioni di configurazione per il tenant di Azure AD specifico. Nel codice XML, aggiornare i valori di metadati come indicato di seguito:

1. Impostare `<Item Key="METADATA">` su `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, dove `yourAzureADtenant` è il nome del tenant di Azure AD, ad esempio, contoso.com.
1. Aprire il browser e passare all'URL `Metadata` appena aggiornato.
1. Nel browser cercare l'oggetto "emittente" e copiarne il valore. Il suo aspetto dovrebbe essere simile al seguente `https://sts.windows.net/{tenantId}/`.
1. Incollare il valore `<Item Key="ProviderName">` nel file XML.
1. Impostare `<Item Key="client_id">` su `Application ID` al momento della registrazione dell'app.
1. Impostare `<Item Key="IdTokenAudience">` su `Application ID` al momento della registrazione dell'app.
1. Assicurarsi che `<Item Key="response_types">` sia impostato su `id_token`.
1. Assicurarsi che `<Item Key="UsePolicyInRedirectUri">` sia impostato su `false`.

È anche necessario collegare il segreto di Azure AD registrato nel tenant di Azure AD B2C ad Azure AD `<ClaimsProvider>`.

* Nella sezione `<CryptographicKeys>` del file XML precedente, aggiornare il valore di `StorageReferenceId` all'ID di riferimento del segreto definito, ad esempio ContosoAppSecret.

### <a name="upload-the-extension-file-for-verification"></a>Caricare il file di estensione per la verifica

A questo punto, i criteri sono stati configurati in modo che Azure AD B2C possa comunicare con la directory di Azure AD. Provare a caricare il file di estensione dei criteri per verificare che non siano presenti problemi. A tale scopo, procedere come segue:

1. Passare al pannello **Tutti i criteri** nel tenant di Azure AD B2C.
1. Selezionare la casella **Sovrascrivi il criterio se esistente**.
1. Caricare il file di estensione (TrustFrameworkExtensions.xml) e assicurarsi che non presenti errori di convalida.

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>Registrare il provider di attestazioni di Azure AD in un percorso utente

È ora necessario aggiungere il provider di identità Azure AD in uno dei percorsi utente. A questo punto, è stato configurato il provider di identità, ma non è disponibile in alcuna delle schermate di iscrizione/accesso. A tale scopo, si creerà un duplicato di un percorso utente modello esistente e quindi si modificherà il duplicato in modo che includa anche il provider di identità Azure AD.

1. Aprire il file di base dei criteri, ad esempio TrustFrameworkBase.xml.
1. Trovare l'elemento `<UserJourneys>` e copiare l'intero nodo `<UserJourney>` con ID="SignUpOrSignIn".
1. Aprire il file di estensione, ad esempio TrustFrameworkExtensions.xml, e trovare l'elemento `<UserJourneys>`. Se l'elemento non esiste, aggiungerne uno.
1. Incollare l'intero nodo `<UserJourney>` copiato come figlio dell'elemento `<UserJourneys>`.
1. Rinominare l'ID del nuovo nodo `<UserJourney>`, ad esempio SignUpOrSignUsingContoso.

### <a name="display-the-button"></a>Visualizzare il "pulsante"

L'elemento `<ClaimsProviderSelection>` è analogo a un pulsante del provider di identità in una schermata di iscrizione/accesso. Aggiungendo un elemento `<ClaimsProviderSelection>` per Azure AD,verrà visualizzato un nuovo pulsante quando un utente apre la pagina. A tale scopo, seguire questa procedura:

1. Trovare l'elemento `<OrchestrationStep>` con `Order="1"` nel nodo `<UserJourney>` appena creato.
1. Aggiungere quanto segue:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. Impostare `TargetClaimsExchangeId` su un valore appropriato. È consigliabile seguire la stessa convenzione degli altri: *\[NomeProviderAttestazioni\]Exchange*.

### <a name="link-the-button-to-an-action"></a>Collegare il "pulsante" a un'azione

Ora che il "pulsante" è stato posizionato, è necessario collegarlo a un'azione. L'azione, in questo caso, consiste nel far comunicare Azure AD B2C con Azure AD per ricevere un token. È possibile farlo collegando il profilo tecnico per il provider di attestazioni di Azure AD.

1. Trovare `<OrchestrationStep>` con `Order="2"` nel nodo `<UserJourney>`
1. Aggiungere quanto segue:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. Aggiornare `Id` con lo stesso valore di `TargetClaimsExchangeId` riportato sopra.
1. Aggiornare `TechnicalProfileReferenceId` con l'`Id` del profilo tecnico creato in precedenza, ad esempio ContosoProfile.

### <a name="upload-the-updated-extension-file"></a>Caricare il file di estensione aggiornato

La modifica del file di estensione è completata. Salvare e caricare il file e verificare che tutte le convalide abbiano esito positivo.

### <a name="update-the-rp-file"></a>Aggiornare il file RP

È ora necessario aggiornare il file RP che avvierà il percorso utente appena creato.

1. Creare una copia di SignUpOrSignIn.xml nella directory di lavoro e rinominare la copia, ad esempio SignUpOrSignInWithAAD.xml.
1. Aprire il nuovo file e aggiornare l'attributo `PolicyId` per `<TrustFrameworkPolicy>` con un valore univoco, ad esempio SignUpOrSignInWithAAD, che sarà il nome dei criteri, ad esempio B2C\_1A\_SignUpOrSignInWithAAD.
1. Modificare l'attributo `ReferenceId` in `<DefaultUserJourney>` in modo che corrisponda all'ID del nuovo percorso utente creato, ad esempio SignUpOrSignUsingContoso.
1. Salvare le modifiche e caricare il file.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Testare i criteri personalizzati appena caricati aprendo il relativo pannello e facendo clic su "Esegui adesso". In caso di errore, cercare una [soluzione](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Passaggi successivi

Inserire commenti e suggerimenti per AADB2CPreview@microsoft.com.

