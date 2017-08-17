---
title: 'Azure Active Directory B2C: aggiunta di un provider SAML Salesforce usando i criteri personalizzati | Microsoft Docs'
description: Informazioni su come creare e gestire i criteri personalizzati di Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: d7f4143f-cd7c-4939-91a8-231a4104dc2c
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 06/11/2017
ms.author: parakhj
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 269cbd80fb6e861fa8588025eec70b6c6e2890d7
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="azure-active-directory-b2c-sign-in-by-using-salesforce-accounts-via-saml"></a>Azure Active Directory B2C: eseguire l'accesso con account Salesforce tramite SAML

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo illustra come usare i [criteri personalizzati](active-directory-b2c-overview-custom.md) per impostare l'accesso per gli utenti da un'organizzazione Salesforce specifica.

## <a name="prerequisites"></a>Prerequisiti

### <a name="azure-ad-b2c-setup"></a>Configurazione di Azure AD B2C

Assicurarsi di avere completato tutti i passaggi di [introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md) in Azure Active Directory B2C (Azure AD B2C),

inclusi i seguenti:

* Creare un tenant di Azure AD B2C
* Creare un'applicazione Azure AD B2C
* Registrare due applicazioni con motore di criteri.
* Impostare le chiavi.
* installare il pacchetto Starter.

### <a name="salesforce-setup"></a>Configurazione di Salesforce

In questo articolo si presuppone che siano già state eseguite le operazioni seguenti:

* Iscrizione a un account Salesforce. È possibile iscriversi per ottenere un [account Developer Edition gratuito](https://developer.salesforce.com/signup).
* [Configurazione di un dominio personale](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) per la propria organizzazione Salesforce.

## <a name="set-up-salesforce-so-users-can-federate"></a>Configurare Salesforce in modo che gli utenti possano eseguire la federazione

Per agevolare la comunicazione tra Azure AD B2C e Salesforce, è necessario ottenere l'URL dei metadati di Salesforce.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Configurare Salesforce come provider di identità

> [!NOTE]
> Questo articolo presuppone che si stia usando [Salesforce Lightning Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ).

1. [Eseguire l'accesso a Salesforce](https://login.salesforce.com/). 
2. In **Settings** (Impostazioni) nel menu di sinistra espandere **Identity** (Identità) e quindi fare clic su **Identity Provider** (Provider di identità).
3. Fare clic su **Enable Identity Provider** (Abilita provider di identità).
4. In **Select the certificate** (Selezionare il certificato) selezionare il certificato che si desidera venga usato da Salesforce per comunicare con Azure AD B2C. È possibile usare il certificato predefinito. Fare clic su **Salva**. 

### <a name="create-a-connected-app-in-salesforce"></a>Creare un'app connessa in Salesforce

1. Nella pagina **Identity Provider** (Provider di identità) passare a **Service Providers** (Provider di servizi).
2. Fare clic su **Service Providers are now created via Connected Apps. Click here** (I provider di servizi vengono ora creati tramite app connesse. Fare clic qui).
3. In **Basic Information** (Informazioni di base) immettere i valori richiesti per l'app connessa.
4. In **Web App Settings** (Impostazioni app Web), selezionare la casella di controllo **Enable SAML** (Abilita SAML).
5. Nel campo **Entity ID** (ID entità) immettere l'URL seguente. Assicurarsi di sostituire il valore per `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```
6. Nel campo **ACS URL** (URL ACS) immettere l'URL seguente. Assicurarsi di sostituire il valore per `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Lasciare i valori predefiniti per le altre impostazioni.
8. Scorrere l'elenco fino alla fine e quindi fare clic su **Save** (Salva).

### <a name="get-the-metadata-url"></a>Ottenere l'URL dei metadati

1. Nella pagina di panoramica dell'app connessa fare clic su **Manage** (Gestisci).
2. Copiare il valore per **Metadata Discovery Endpoint** (Endpoint di individuazione dei metadati) e salvarlo. Questo valore sarà usato più avanti in questo articolo.

### <a name="set-up-salesforce-users-to-federate"></a>Configurare gli utenti di Salesforce per la federazione

1. Nella pagina **Manage** (Gestisci) dell'app connessa passare a **Profiles** (Profili).
2. Fare clic su **Manage Profiles** (Gestisci profili).
3. Selezionare i profili (o gruppi di utenti) di cui si intende eseguire la federazione con Azure AD B2C. Come amministratore di sistema, è necessario selezionare la casella per **System Administrator** (Amministratore di sistema) in modo che sia possibile eseguire la federazione con l'account di Salesforce.

## <a name="generate-a-signing-certificate-for-azure-ad-b2c"></a>Generare un certificato di firma per Azure AD B2C

Le richieste inviate a Salesforce devono essere firmate da Azure AD B2C. Per generare un certificato di firma, aprire Azure PowerShell ed eseguire i comandi seguenti.

> [!NOTE]
> Assicurarsi di aggiornare il nome del tenant e la password nelle prime due righe.

```PowerShell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="add-the-saml-signing-certificate-to-azure-ad-b2c"></a>Aggiungere un certificato di firma SAML ad Azure AD B2C

Caricare il certificato di firma nel tenant di Azure AD B2C: 

1. Passare al tenant di Azure AD B2C. Fare clic su **Impostazioni** > **Framework dell'esperienza di gestione delle identità** > **Chiavi dei criteri**.
2. Fare clic su **+Aggiungi** e quindi eseguire le operazioni seguenti:
    1. Fare clic su **Opzioni** > **Carica**.
    2. Immettere un **Nome**, ad esempio SAMLSigningCert. Verrà automaticamente aggiunto il prefisso *B2C_1A_* al nome della chiave.
    3. Per selezionare il certificato, selezionare **upload file control** (controllo carica file). 
    4. Immettere la password del certificato impostato nello script di PowerShell.
3. Fare clic su **Crea**.
4. Verificare di avere creato una chiave, ad esempio B2C_1A_SAMLSigningCert. Prendere nota del nome completo, incluso *B2C_1A_*. Si farà riferimento a questa chiave in un secondo momento nei criteri.

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>Creare il provider di attestazioni SAML Salesforce nei criteri di base

Per consentire agli utenti di accedere usando Salesforce, è necessario definire Salesforce come provider di attestazioni. In altri termini, è necessario specificare l'endpoint con cui comunicherà Azure AD B2C. L'endpoint *offrirà* un set di *attestazioni* che Azure AD B2C userà per verificare se un utente specifico è stato autenticato. È possibile a tale scopo aggiungere un nodo `<ClaimsProvider>` per Salesforce nel file di estensione dei criteri:

1. Nella directory di lavoro aprire il file di estensione (TrustFrameworkExtensions.xml).
2. Trovare la sezione `<ClaimsProviders>`. Se non esiste, crearla nel nodo radice.
3. Aggiungere un nuovo `<ClaimsProvider>`:

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
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

Nel nodo `<ClaimsProvider>`:

1. Modificare il valore per `<Domain>` in un valore univoco che distingua `<ClaimsProvider>` dagli altri provider di identità.
2. Aggiornare il valore per `<DisplayName>` con un nome visualizzato per il provider di attestazioni. Questo valore non viene usato attualmente.

### <a name="update-the-technical-profile"></a>Aggiornare il profilo tecnico

Per ottenere un token SAML da Salesforce, è necessario definire i protocolli che Azure AD B2C userà per comunicare con Azure Active Directory (Azure AD). Nell'elemento `<TechnicalProfile>` di `<ClaimsProvider>` eseguire le operazioni seguenti:

1. Aggiornare l'ID del nodo `<TechnicalProfile>`. Questo ID viene usato per fare riferimento al profilo tecnico da altre parti dei criteri.
2. Aggiornare il valore di `<DisplayName>`. Questo valore verrà visualizzato sul pulsante di accesso nella pagina di accesso.
3. Aggiornare il valore di `<Description>`.
4. Salesforce usa il protocollo SAML 2.0. Assicurarsi che il valore per `<Protocol>` sia **SAML2**.

Aggiornare la sezione `<Metadata>` nel file XML precedente in modo da riflettere le impostazioni per l'account di Salesforce specifico. Nel codice XML aggiornare i valori di metadati come indicato di seguito:

1. Aggiornare il valore di `<Item Key="PartnerEntity">` con l'URL dei metadati di Salesforce copiato in precedenza. L'URL si presenta nel formato seguente: 

    `https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp/connectedapp.xml`

2. Nella sezione `<CryptographicKeys>` aggiornare il valore per entrambe le istanze di `StorageReferenceId` al nome della chiave del certificato di firma, ad esempio B2C_1A_SAMLSigningCert.

### <a name="upload-the-extension-file-for-verification"></a>Caricare il file di estensione per la verifica

I criteri sono a questo punto configurati in modo che Azure AD B2C possa comunicare con Salesforce. Provare a caricare il file di estensione dei criteri per verificare che non vi siano problemi. Per caricare il file di estensione dei criteri:

1. Nel tenant di Azure AD B2C passare al pannello **Tutti i criteri**.
2. Selezionare la casella di controllo **Sovrascrivi il criterio se esistente**.
3. Caricare il file di estensione (TrustFrameworkExtensions.xml). Verificare che la relativa convalida abbia esito positivo.

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>Registrare il provider di attestazioni SAML Salesforce in un percorso utente

È ora necessario aggiungere il provider di identità SAML Salesforce in uno dei percorsi utente. Il provider di identità è a questo punto configurato, ma non è disponibile in alcuna delle pagine di iscrizione o di accesso. Per aggiungere il provider di identità a una pagina di accesso, creare prima di tutto un duplicato di un percorso utente di modello esistente. Modificare quindi il modello in modo che includa anche il provider di identità di Azure AD.

1. Aprire il file di base dei criteri, ad esempio TrustFrameworkBase.xml.
2. Individuare l'elemento `<UserJourneys>` e quindi copiare l'intero valore `<UserJourney>`, incluso Id=”SignUpOrSignIn”.
3. Aprire il file di estensione, ad esempio TrustFrameworkExtensions.xml. Trovare l'elemento `<UserJourneys>`. Se l'elemento non esiste, crearne uno.
4. Incollare l'intero `<UserJourney>` copiato come figlio dell'elemento `<UserJourneys>`.
5. Rinominare l'ID del nuovo `<UserJourney>`, ad esempio, SignUpOrSignUsingContoso.

### <a name="display-the-identity-provider-button"></a>Visualizzare il pulsante del provider di identità

L'elemento `<ClaimsProviderSelection>` è analogo a un pulsante di provider di identità in una pagina di iscrizione o accesso. Se si aggiunge un elemento `<ClaimsProviderSelection>` per Salesforce, viene visualizzato un nuovo pulsante quando l'utente accede alla pagina. Per visualizzare il pulsante del provider di identità:

1. Nell'elemento `<UserJourney>` creato trovare l'elemento `<OrchestrationStep>` con `Order="1"`.
2. Aggiungere il codice XML seguente:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

3. Impostare `TargetClaimsExchangeId` su un valore logico. È consigliabile seguire la stessa convenzione degli altri, ad esempio *\[ClaimProviderName\]Exchange*.

### <a name="link-the-identity-provider-button-to-an-action"></a>Collegare il pulsante del provider di identità a un'azione:

Il pulsante è a questo punto posizionato ed è necessario collegarlo a un'azione. L'azione in questo caso riguarda la possibilità da parte di Azure AD B2C di comunicare con Salesforce per ricevere un token SAML. Collegare a tal fine il profilo tecnico per il provider di attestazioni SAML Salesforce:

1. Nel nodo `<UserJourney>` trovare l'elemento `<OrchestrationStep>` con `Order="2"`.
2. Aggiungere il codice XML seguente:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

3. Aggiornare l'elemento `Id` allo stesso valore usato in precedenza per `TargetClaimsExchangeId`.
4. Aggiornare `TechnicalProfileReferenceId` all'elemento `Id` del profilo tecnico creato in precedenza, ad esempio ContosoProfile.

### <a name="upload-the-updated-extension-file"></a>Caricare il file di estensione aggiornato

La modifica del file di estensione è completata. Salvare e caricare il file. Verificare che tutte le convalide abbiano esito positivo.

### <a name="update-the-relying-party-file"></a>Aggiornare il file della relying party

È ora necessario aggiornare il file della relying party (RP) che avvierà il percorso utente appena creato:

1. Creare una copia di SignUpOrSignIn.xml nella directory di lavoro. Rinominare quindi il file, ad esempio SignUpOrSignInWithAAD.xml.
2. Aprire il nuovo file e aggiornare l'attributo `PolicyId` per `<TrustFrameworkPolicy>` con un valore univoco, che sarà il nome dei criteri, ad esempio SignUpOrSignInWithAAD.
3. Modificare l'attributo `ReferenceId` in `<DefaultUserJourney>` in modo che corrisponda all'elemento `Id` del nuovo percorso utente creato, ad esempio SignUpOrSignUsingContoso.
4. Salvare le modifiche e caricare il file.

## <a name="test-and-troubleshoot"></a>Testare e risolvere i problemi

Per testare i criteri personalizzati appena caricati, nel portale di Azure passare al pannello dei criteri e quindi fare clic su **Esegui ora**. Se i test hanno esito negativo, vedere l'articolo sulla [risoluzione dei problemi relativi a criteri personalizzati](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Passaggi successivi

Inviare commenti e suggerimenti all'indirizzo [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).

