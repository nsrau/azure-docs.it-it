---
title: Aggiungere AD FS come provider di identità SAML tramite criteri personalizzati in Azure Active Directory B2C | Microsoft Docs
description: Impostare AD FS 2016 usando il protocollo SAML e i criteri personalizzati in Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2c2e6861fda42a9e8c1aabcba303bfede47ac3c1
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669227"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Aggiungere AD FS come provider di identità SAML tramite criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo illustra come abilitare l'accesso per gli account utente AD FS usando [criteri personalizzati](active-directory-b2c-overview-custom.md) in Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Prerequisiti

Completare la procedura descritta nell'articolo [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md).

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Aggiungere la chiave dell'applicazione di account AD FS in Azure AD B2C

La federazione con un account AD FS richiede un segreto client per consentire all'account di considerare Azure AD B2C attendibile per conto dell'applicazione. È necessario quindi archiviare il certificato AD FS nel tenant di Azure AD B2C. 

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Assicurarsi di usare la directory contenente il tenant Azure AD B2C passando a tale directory nell'angolo in alto a destra del portale di Azure. Selezionare **Cambia directory** e quindi scegliere la directory che contiene il tenant creato. In questa esercitazione viene usata la directory *contoso* che contiene il tenant denominato *contoso0522Tenant.onmicrosoft.com*.

    ![Cambiare directory](./media/active-directory-b2c-custom-setup-adfs2016-idp/switch-directories.png)

3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo. È consigliabile a questo punto usare il tenant.
4. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
5. Selezionare **Chiavi dei criteri** per visualizzare le chiavi disponibili nel tenant e quindi fare clic su **Aggiungi**.
6. Scegliere **Carica** come opzione.
7. Immettere `ADFSSamlCert` per il nome. È possibile che il prefisso `B2C_1A_` venga aggiunto automaticamente.
8. Individuare e selezionare il file di certificato con estensione pfx con la chiave privata. Questo certificato con la chiave privata deve essere lo stesso che è stato rilasciato e usato per la relying party di AD FS.
9. Fare clic su **Crea** e confermare di avere creato la chiave `B2C_1A_ADFSSamlCert`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Aggiungere un provider di attestazioni nei criteri di estensione

Per consentire agli utenti di accedere con un account AD FS, è necessario definire l'account AD FS come provider di attestazioni. È quindi necessario specificare un endpoint con cui Azure AD B2C comunica. L'endpoint offre un set di attestazioni che vengono usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

Definire AD FS come provider di attestazioni aggiungendo l'elemento **ClaimsProvider** nel file dei criteri di estensione.

1. Aprire il file dei criteri *TrustFrameworkExtensions.xml* nella directory di lavoro. Se occorre un editor XML, [provare Visual Studio Code](https://code.visualstudio.com/download), un editor multipiattaforma leggero.
2. Aggiungere il codice XML seguente sotto l'elemento **ClaimsProviders** e sostituire **your-ADFS-domain** con il nome di dominio AD FS. Sostituire inoltre il valore dell'attestazione di output **identityProvider** con il DNS (valore arbitrario che indica il dominio personale) e salvare il file. 

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
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

## <a name="register-the-claims-provider-for-sign-up-and-sign-in"></a>Registrare il provider di attestazioni per la registrazione e l'accesso

Per rendere il provider di identità dell'account AD FS disponibile nelle pagine di iscrizione e di accesso, è necessario aggiungerlo al percorso utente **SignUpOrSignIn**. 

Creare una copia di un percorso utente modello esistente e quindi modificarlo in modo che includa il provider di identità AD FS:

>[!NOTE]
>Se l'elemento **UserJourneys** è stato copiato in precedenza dal file di base dei criteri nel file di estensione (*TrustFrameworkExtensions.xml*), è possibile ignorare questa sezione.

1. Aprire il file di base dei criteri, ad esempio *TrustFrameworkBase.xml*.
2. Copiare l'intero contenuto dell'elemento **UserJourneys**.
3. Aprire il file di estensione (*TrustFrameworkExtensions.xml*) e incollare l'intero contenuto dell'elemento **UserJourneys** elemento che è stato copiato nel file di estensione.

### <a name="display-the-button"></a>Visualizzare il pulsante

L'elemento **ClaimsProviderSelections** definisce l'elenco di selezioni di provider di attestazioni e il relativo ordine.  L'elemento **ClaimsProviderSelection** è analogo a un pulsante per il provider di identità in una pagina di iscrizione o di accesso. Se si aggiunge un elemento **ClaimsProviderSelection** per un account AD FS, viene visualizzato un nuovo pulsante quando un utente visualizza la pagina. Per aggiungere questo elemento:

1. Nell'elemento **UserJourney** con un identificatore di `SignUpOrSignIn` nei percorsi utente che sono stati copiati individuare l'elemento **OrchestrationStep** di `Order="1"`.
2. Aggiungere l'elemento **ClaimsProviderSelection** sotto l'elemento **ClaimsProviderSelections**:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Collegare il pulsante a un'azione

Ora che il pulsante è stato posizionato, è necessario collegarlo a un'azione. L'azione, in questo caso, consiste nel far comunicare Azure AD B2C con l'account AD FS per ricevere un token. È possibile farlo collegando il profilo tecnico per il provider di attestazioni dell'account AD FS:

1. Trovare l'elemento **OrchestrationStep** di `Order="2"` sotto l'elemento **UserJourney**.
2. Aggiungere l'elemento **ClaimsExchange** seguente sotto l'elemento **ClaimsExchanges**:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

> [!NOTE]
> * Verificare che `Id` sia impostato sullo stesso valore di `TargetClaimsExchangeId` riportato nella sezione precedente.
> * Assicurarsi che `TechnicalProfileReferenceId` sia impostato sul profilo tecnico creato in precedenza (Contoso-SAML2).


## <a name="optional-register-the-claims-provider-for-profile-edit"></a>[Facoltativo] Registrare il provider di attestazioni per la modifica del profilo

È possibile anche aggiungere il provider di identità dell'account AD FS anche al percorso utente di modifica del profilo.

### <a name="display-the-button"></a>Visualizzare il pulsante

1. Aprire il file di estensione dei criteri, ad esempio *TrustFrameworkExtensions.xml*.
2. Nell'elemento **UserJourney** con un identificatore `ProfileEdit` nei percorsi utente che sono stati copiati individuare l'elemento **OrchestrationStep** di `Order="1"`.
3. Aggiungere l'elemento **ClaimsProviderSelection** seguente sotto l'elemento **ClaimsProviderSelections**:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Collegare il pulsante a un'azione

1. Trovare l'elemento **OrchestrationStep** di `Order="2"` sotto l'elemento **UserJourney**.
2. Aggiungere l'elemento **ClaimsExchange** seguente sotto l'elemento **ClaimsExchanges**:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

## <a name="upload-the-policy-to-your-tenant"></a>Caricare i criteri nel tenant

1. Nel portale di Azure selezionare **Tutti i criteri**.
2. Selezionare **Carica criteri**.
3. Abilitare la casella **Sovrascrivi il criterio se esistente**.
4. Individuare e selezionare il file dei criteri *TrustFrameworkExtensions.xml* e quindi selezionare **Carica**. Assicurarsi che la convalida abbia esito positivo.


## <a name="configure-an-adfs-relying-party-trust"></a>Configurare un trust della relying party di AD FS

Per usare AD FS come provider di identità in Azure AD B2C, è necessario creare un trust della relying party di AD FS con i metadati SAML di Azure AD B2C. L'esempio seguente mostra un indirizzo URL che punta ai metadati SAML di un profilo tecnico di Azure AD B2C:

```
https://login.microsoftonline.com/te/your-tenant/your-policy/samlp/metadata?idptp=your-technical-profile
```

Sostituire i valori seguenti:

- **your-tenant** con il nome del tenant, ad esempio your-tenant.onmicrosoft.com.
- **your-policy** con il nome dei criteri. Usare i criteri in cui si configura il profilo tecnico del provider SAML o i criteri che ereditano da quei criteri.
- **your-technical-profile** con il nome del profilo tecnico del provider di identità SAML.
 
Aprire un browser e passare all'URL. Assicurarsi di digitare l'URL corretto e di avere accesso al file dei metadati XML.

Per aggiungere un nuovo un trust della relying party usando lo snap-in di gestione di AD FS e configurare manualmente le impostazioni, eseguire la procedura seguente in un server federativo. L'appartenenza al gruppo **Amministratori** o a un gruppo equivalente nel computer locale è il requisito minimo necessario per completare questa procedura. Informazioni dettagliate sull'utilizzo delle appartenenze ai gruppi e degli account appropriati sono disponibili in [Local and Domain Default Groups](http://go.microsoft.com/fwlink/?LinkId=83477) (Gruppi predefiniti locali e di dominio).

1. In Server Manager selezionare **Strumenti** e quindi **Gestione AD FS**.
2. Scegliere **Aggiungi attendibilità componente**.
3. Nella pagina **iniziale** scegliere **In grado di riconoscere attestazioni** e quindi fare clic su **Avvia**.
4. Nella pagina **Seleziona origine dati** selezionare **Import data about the relying party publish online or on a local network** (Importa dati sul relying party pubblicata online o in una rete locale), specificare l'URL dei metadati di Azure AD B2C e quindi fare clic su **Avanti**.
5. Nella pagina **Specifica nome visualizzato** digitare un nome in **Nome visualizzato**, in **Note** digitare una descrizione del trust della relying party e quindi fare clic su **Avanti**.
6. Nella pagina **Scegli criteri di controllo di accesso** selezionare i criteri desiderati e fare clic su **Avanti**.
7. Nella pagina **Aggiunta attendibilità** rivedere le impostazioni e quindi fare clic su **Avanti** per salvare le informazioni sul trust della relying party.
8. Nella pagina **Fine** fare clic su **Chiudi**. Viene automaticamente visualizzata la finestra di dialogo **Modifica regole attestazione**.
9. Selezionare **Aggiungi regola**.  
10. In **Modello di regola attestazione** selezionare **Inviare attributi LDAP come attestazioni**.
11. Specificare un nome in **Nome regola attestazione**. Per **Archivio attributi** selezionare **Select Active Directory** (Seleziona Active Directory), aggiungere le attestazioni seguenti e quindi fare clic su **Fine** e su **OK**.

    ![Impostare le proprietà di regola](./media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)

12.  In base al tipo di certificato è possibile che sia necessario impostare l'algoritmo HASH. Nella finestra delle proprietà del trust della relying party (B2C Demo) selezionare la scheda **Avanzate** e impostare **Algoritmo hash sicuro** su `SHA-1` o `SHA-256` e quindi fare clic su **OK**.  

### <a name="update-the-relying-party-metadata"></a>Aggiornare i metadati della relying party

La modifica del profilo tecnico SAML richiede che si aggiorni AD FS con la versione dei metadati aggiornata. Non è necessario aggiornare i metadati quando si crea l'applicazione relying party, ma quando si apporta una modifica, occorre aggiornare i metadati in AD FS.

1. In Server Manager selezionare **Strumenti** e quindi **Gestione AD FS**.
2. Selezionare il trust della relying party creato, selezionare **Update from Federation Metadata** (Aggiorna da metadati federazione) e quindi fare clic su **Aggiorna**. 

### <a name="test-the-policy-by-using-run-now"></a>Testare i criteri usando Esegui adesso

1.  Aprire **Impostazioni di Azure AD B2C** e passare a **Framework dell'esperienza di gestione delle identità**.
2.  Aprire **B2C_1A_ProfileEdit**, i criteri personalizzati dalla relying party caricati in precedenza. Selezionare **Esegui adesso**. Dovrebbe essere possibile accedere usando un account AD FS.

## <a name="download-the-complete-policy-files"></a>Scaricare i file dei criteri completi

Facoltativo: è possibile creare lo scenario personale usando file di criteri personalizzati dopo avere completato la procedura descritta in [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md). Per i file di esempio, vedere [File dei criteri di esempio solo per riferimento](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app).
