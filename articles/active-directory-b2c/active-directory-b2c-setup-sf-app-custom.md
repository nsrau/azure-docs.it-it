---
title: 'Azure Active Directory B2C: aggiunta del provider SAML Salesforce con criteri personalizzati | Microsoft Docs'
description: Argomento sui criteri personalizzati di Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: gsacavdm
manager: krassk
editor: gsacavdm
ms.assetid: d7f4143f-cd7c-4939-91a8-231a4104dc2c
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/30/2017
ms.author: gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 1d97c75f3130ea6fdacbc6335b6e70677b4d226e
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---
# <a name="azure-active-directory-b2c-log-in-using-salesforce-accounts-via-saml"></a>Azure Active Directory B2C: eseguire l'accesso con account Salesforce tramite SAML

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo illustra come abilitare l'accesso degli utenti da un'organizzazione Salesforce specifica usando [criteri personalizzati](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Prerequisiti

### <a name="azure-ad-b2c-setup"></a>Configurazione di Azure AD B2C
Assicurarsi di aver completato tutti i passaggi illustrati nell'[introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md).

Sono inclusi:

1. Creazione di un tenant di Azure AD B2C.
1. Creazione di un'applicazione Azure AD B2C.
1. Registrazione di due applicazioni del motore di criteri.
1. Configurazione delle chiavi.
1. Configurazione dello starter pack.

### <a name="salesforce-setup"></a>Configurazione di Salesforce
Questa esercitazione presuppone che siano già state eseguite queste operazioni:
1. Iscrizione a un account Salesforce. È possibile iscriversi per ottenere una [versione Developer Edition gratuita](https://developer.salesforce.com/signup). 
1. [Configurazione di un dominio personalizzato](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) per la propria organizzazione Salesforce.

## <a name="get-the-salesforce-saml-metadata"></a>Ottenere i metadati SAML di Salesforce
>[!NOTE]
> Questa esercitazione presuppone che si usi [Salesforce Lighting Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ).

1. [Eseguire l'accesso a Salesforce](https://login.salesforce.com/). 
1. Nel menu a sinistra espandere **Identity** (Identità) in **Settings** (Impostazioni) e fare clic su **Identity Provider** (Provider di identità).
1. Fare clic su **Enable Identity Provider** (Abilita provider di identità).
1. **Selezionare il certificato** che Salesforce dovrà usare per la comunicazione con Azure AD B2C e fare clic su **Save** (Salva). È possibile usare il certificato predefinito.
1. Fare clic sul pulsante **Download Metadata** (Scarica metadati) ora disponibile e salvare il file di metadati che verrà usato in un passaggio successivo.

## <a name="add-a-saml-signing-certificate-to-azure-ad-b2c"></a>Aggiungere un certificato di firma SAML ad Azure AD B2C
È necessario archiviare tramite caricamento un certificato nel tenant di Azure AD B2C da usare durante la forma delle rispettive richieste SAML. A tale scopo, seguire questa procedura:

1. Passare al tenant di Azure AD B2C e aprire **B2C Settings (Impostazioni B2C) > Framework dell'esperienza di gestione delle identità > Chiavi dei criteri**
1. Fare clic su **+Aggiungi**
1. Opzioni:
 * Selezionare **Opzioni > Carica**
 * **Nome**: > `ContosoIdpSamlCert`.  Il prefisso B2C_1A_ verrà aggiunto automaticamente al nome della chiave. Annotare il nome completo (con B2C_1A_), perché verrà usato in seguito come riferimento nel criterio.
 * Usare il **controllo di caricamento del file** per selezionare il certificato e fornire la password del certificato, se applicabile.
1. Fare clic su **Crea**
1. Confermare che sia stata creata la chiave: `B2C_1A_ContosoIdpSamlCert`

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>Creare il provider di attestazioni SAML Salesforce nei criteri di base

Per consentire agli utenti di accedere usando Salesforce, è necessario definire Salesforce come provider di attestazioni. In altri termini, è necessario specificare l'endpoint con cui comunicherà Azure AD B2C. L'endpoint *fornirà* un set di *attestazioni* che verrà usato da Azure AD B2C per verificare che un utente specifico sia stato autenticato. A tale scopo, è possibile aggiungere un nodo `<ClaimsProvider>` per Salesforce nel file di estensione dei criteri.

1. Aprire il file di estensione dalla directory di lavoro (TrustFrameworkExtensions.xml).
1. Trovare la sezione `<ClaimsProviders>`. Se non esiste, aggiungerla nel nodo radice.
1. Aggiungere un nuovo `<ClaimsProvider>` come illustrato di seguito:

    ```XML
    <ClaimsProvider>
      <Domain>contoso</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">
    <![CDATA[ <md:EntityDescriptor xmlns:md="urn:oasis:names:tc:SAML:2.0:metadata" entityID="https://contoso.com" validUntil="2026-10-05T23:57:13.854Z" xmlns:ds="http://www.w3.org/2000/09/xmldsig#"><md:IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"><md:KeyDescriptor use="signing"><ds:KeyInfo><ds:X509Data><ds:X509Certificate>MIIErDCCA….qY9SjVXdu7zy8tZ+LqnwFSYIJ4VkE9UR1vvvnzO</ds:X509Certificate></ds:X509Data></ds:KeyInfo></md:KeyDescriptor><md:NameIDFormat>urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified</md:NameIDFormat><md:SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://contoso.com/idp/endpoint/HttpPost"/><md:SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://contoso.com/idp/endpoint/HttpRedirect"/></md:IDPSSODescriptor></md:EntityDescriptor>]]>
            </Item>
          </Metadata>       
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ContosoIdpSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ContosoIdpSamlCert "/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="externalIdp"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
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

Per ottenere un token SAML da Salesforce, è necessario definire i protocolli che dovranno essere usati da Azure AD B2C per comunicare con Azure AD. Tale definizione viene eseguita all'interno dell'elemento `<TechnicalProfile>` di `<ClaimsProvider>`.

1. Aggiornare l'ID del nodo `<TechnicalProfile>`. Questo ID viene usato per fare riferimento al profilo tecnico in altre parti dei criteri.
1. Aggiornare il valore di `<DisplayName>`. Questo valore verrà visualizzato sul pulsante di accesso nella schermata di accesso.
1. Aggiornare il valore di `<Description>`.
1. Dato che Azure AD usa il protocollo OpenID Connect, verificare che il valore di `<Protocol>` sia "SAML2".

È necessario aggiornare la sezione `<Metadata>` nel codice XML riportato sopra in modo da riflettere le impostazioni di configurazione per il tenant di Azure AD specifico. Nel codice XML aggiornare i valori dei metadati come indicato di seguito:

1. Aggiornare il valore di `<Item Key="PartnerEntity">` con il contenuto del file Metadata.xml scaricato da Salesforce. **Assicurarsi di incapsularlo con <![CDATA[ …metadata… ]]>**.

1. Nella sezione `<CryptographicKeys>` del codice XML riportato sopra, aggiornare il valore di entrambe le occorrenze di `StorageReferenceId` con l'ID certificato che è stato definito, ad esempio ContosoSalesforceCert.

### <a name="upload-the-extension-file-for-verification"></a>Caricare il file di estensione per la verifica

A questo punto, i criteri sono stati configurati in modo che Azure AD B2C possa comunicare con la directory di Azure AD. Provare a caricare il file di estensione dei criteri per verificare che non siano presenti problemi. A tale scopo, procedere come segue:

1. Passare al pannello **Tutti i criteri** nel tenant di Azure AD B2C.
1. Selezionare la casella **Sovrascrivi il criterio se esistente**.
1. Caricare il file di estensione (TrustFrameworkExtensions.xml) e verificare che la convalida abbia esito positivo.

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>Registrare il provider di attestazioni SAML Salesforce in un percorso utente

È ora necessario aggiungere il provider di identità SAML Salesforce in uno dei percorsi utente. A questo punto, il provider di identità è stato configurato, ma non è disponibile nelle schermate di iscrizione/accesso. A tale scopo, si creerà un duplicato di un percorso utente modello esistente e quindi si modificherà il duplicato in modo che includa anche il provider di identità Azure AD.

1. Aprire il file di base dei criteri, ad esempio TrustFrameworkBase.xml.
1. Trovare l'elemento `<UserJourneys>` e copiare l'intero nodo `<UserJourney>` con ID="SignUpOrSignIn".
1. Aprire il file di estensione, ad esempio TrustFrameworkExtensions.xml, e trovare l'elemento `<UserJourneys>`. Se l'elemento non esiste, aggiungerne uno.
1. Incollare l'intero nodo `<UserJourney>` copiato come figlio dell'elemento `<UserJourneys>`.
1. Rinominare l'ID del nuovo nodo `<UserJourney>`, ad esempio SignUpOrSignUsingContoso.

### <a name="display-the-button"></a>Visualizzare il "pulsante"

L'elemento `<ClaimsProviderSelection>` è analogo a un pulsante del provider di identità in una schermata di iscrizione/accesso. Aggiungendo un elemento `<ClaimsProviderSelection>` per Salesforce, quando un utente apre la pagina verrà visualizzato un nuovo pulsante. A tale scopo, seguire questa procedura:

1. Trovare l'elemento `<OrchestrationStep>` con `Order="1"` nel nodo `<UserJourney>` appena creato.
1. Aggiungere quanto segue:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. Impostare `TargetClaimsExchangeId` su un valore appropriato. È consigliabile seguire la stessa convenzione degli altri: *\[NomeProviderAttestazioni\]Exchange*.

### <a name="link-the-button-to-an-action"></a>Collegare il "pulsante" a un'azione

Ora che è stato implementato un "pulsante", è necessario collegarlo a un'azione. In questo caso, l'azione riguarda la comunicazione di Azure AD B2C con Salesforce per ricevere un token SAML. A tale scopo, collegare il profilo tecnico per il provider di attestazioni SAML Salesforce.

1. Trovare l'elemento `<OrchestrationStep>` con `Order="2"` nel nodo `<UserJourney>`.
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
1. Aprire il nuovo file e aggiornare l'attributo `PolicyId` per `<TrustFrameworkPolicy>` con un valore univoco, che sarà il nome dei criteri, ad esempio SignUpOrSignInWithAAD.
1. Modificare l'attributo `ReferenceId` in `<DefaultUserJourney>` in modo che corrisponda all'ID del nuovo percorso utente creato, ad esempio SignUpOrSignUsingContoso.
1. Salvare le modifiche e caricare il file.

## <a name="create-a-connected-app-in-salesforce"></a>Creare un'app connessa in Salesforce
È necessario registrare Azure AD B2C come app connessa in Salesforce.

1. [Eseguire l'accesso a Salesforce](https://login.salesforce.com/). 
1. Nel menu a sinistra espandere **Identity** (Identità) in **Settings** (Impostazioni) e fare clic su **Identity Provider** (Provider di identità).
1. Nella sezione **Service Providers** (Provider di servizi) in basso fare clic su **Service Providers are now created via Connected Apps. Click here** (I provider di servizi vengono ora creati tramite app connesse. Fare clic qui).
1. Specificare le informazioni di base obbligatorie per l'app connessa in **Basic Information** (Informazioni di base).
1. Nella sezione **Web App Settings** (Impostazioni app Web):
    1. Selezionare **Enable SAML** (Abilita SAML).
    1. Immettere l'URL seguente nel campo **Entity ID** (ID entità), assicurandosi di sostituire `tenantName`. 
    
        ```
        https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase
        ```

    1. Immettere l'URL seguente nel campo **ACS URL** (URL ACS), assicurandosi di sostituire `tenantName`. 
        ```
        https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
        ```

    1. Per tutte le altre impostazioni lasciare i valori predefiniti.
1. Scorrere fino in fondo e fare clic sul pulsante **Save** (Salva).


## <a name="troubleshooting"></a>Risoluzione dei problemi

Testare i criteri personalizzati appena caricati aprendo il relativo pannello e facendo clic su "Esegui adesso". In caso di errore, vedere l'articolo relativo alla [risoluzione dei problemi](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Passaggi successivi
 
Inviare commenti e suggerimenti all'indirizzo [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).


