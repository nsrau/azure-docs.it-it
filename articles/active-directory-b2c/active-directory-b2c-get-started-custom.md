---
title: Introduzione ai criteri personalizzati-Azure Active Directory B2C
description: Informazioni su come iniziare a usare i criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 83269a5ae0d2e5fb7ae2651dbc27926c910a0e03
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302481"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Introduzione ai criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I [criteri personalizzati](active-directory-b2c-overview-custom.md) sono file di configurazione che definiscono il comportamento del tenant di Azure Active Directory (Azure AD) B2C. In questo articolo viene creato un criterio personalizzato che supporta l'iscrizione o l'accesso all'account locale tramite un indirizzo di posta elettronica e una password. È inoltre necessario preparare l'ambiente per l'aggiunta di provider di identità.

## <a name="prerequisites"></a>Prerequisiti

- In assenza di un tenant, è necessario [creare un tenant di Azure AD B2C](tutorial-create-tenant.md) collegato alla sottoscrizione di Azure.
- [Registrare l'applicazione](tutorial-register-applications.md) nel tenant creato in modo che possa comunicare con Azure ad B2C.

## <a name="add-signing-and-encryption-keys"></a>Aggiungere le chiavi di firma e di crittografia

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
2. Assicurarsi di usare la directory che contiene il tenant del Azure AD B2C. Fare clic sul **filtro directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
4. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.

### <a name="create-the-signing-key"></a>Creazione della chiave di firma

1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
2. Per **Opzioni** scegliere `Generate`.
3. In **Nome** immettere `TokenSigningKeyContainer`. È possibile che il prefisso `B2C_1A_` venga aggiunto automaticamente.
4. Per **Tipo di chiave** selezionare **RSA**.
5. Per **Uso chiave** selezionare **Firma**.
6. Fare clic su **Create**(Crea).

### <a name="create-the-encryption-key"></a>Creazione della chiave di crittografia

1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
2. Per **Opzioni** scegliere `Generate`.
3. In **Nome** immettere `TokenEncryptionKeyContainer`. È possibile che il prefisso `B2C_1A`_ venga aggiunto automaticamente.
4. Per **Tipo di chiave** selezionare **RSA**.
5. In **Uso chiave**selezionare **Crittografia**.
6. Fare clic su **Create**(Crea).

### <a name="create-the-facebook-key"></a>Creazione della chiave Facebook

Se si dispone già di un [segreto dell'applicazione Facebook](active-directory-b2c-setup-fb-app.md), aggiungerlo come chiave dei criteri nel tenant. In caso contrario, è necessario creare la chiave con un valore segnaposto affinché i criteri superino la convalida.

1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
2. Per **Opzioni** scegliere `Manual`.
3. Per **Nome** immettere `FacebookSecret`. È possibile che il prefisso `B2C_1A_` venga aggiunto automaticamente.
4. In **Segreto** immettere il segreto di Facebook da developers.facebook.com oppure `0` come segnaposto. Questo valore è il segreto, non l'ID applicazione.
5. Per **Uso chiave** selezionare **Firma**.
6. Fare clic su **Create**(Crea).

## <a name="register-identity-experience-framework-applications"></a>Registrare le applicazioni del framework dell'esperienza di gestione delle identità

Azure AD B2C richiede di registrare due applicazioni che vengono usate per registrare e far accedere gli utenti: L'app Web IdentityExperienceFramework e l'app nativa ProxyIdentityExperienceFramework, con autorizzazione delegata dall'app IdentityExperienceFramework. Gli account locali esistono solo nel tenant. Per accedere alle applicazioni registrate nel tenant, gli utenti si iscrivono con una combinazione univoca di indirizzo di posta elettronica e password.

### <a name="register-the-identityexperienceframework-application"></a>Registrare l'applicazione IdentityExperienceFramework

1. Selezionare **tutti i servizi** nell'angolo superiore sinistro del portale di Azure.
1. Nella casella di ricerca immettere `Azure Active Directory`.
1. Selezionare **Azure Active Directory** dai risultati della ricerca.
1. In **Gestisci** nel menu a sinistra selezionare **registrazioni app (legacy)** .
1. Selezionare **Registrazione nuova applicazione**.
1. Per **Nome** immettere `IdentityExperienceFramework`.
1. Per **Tipo di applicazione** scegliere **App Web/API**.
1. Per **URL di accesso** immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, dove `your-tenant-name` è il nome di dominio del tenant di Azure AD B2C. Tutti gli URL dovrebbero ora usare [b2clogin.com](b2clogin.md).
1. Fare clic su **Create**(Crea). Dopo la creazione, copiare l'ID applicazione e salvarlo per usarlo in seguito.

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registrare l'applicazione ProxyIdentityExperienceFramework

1. In **registrazioni app (legacy)** selezionare **registrazione nuova applicazione**.
2. Per **Nome** immettere `ProxyIdentityExperienceFramework`.
3. Per **Tipo di applicazione** scegliere **Nativo**.
4. Per **URI di reindirizzamento** immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, dove `your-tenant-name` è il tenant di Azure AD B2C.
5. Fare clic su **Create**(Crea). Dopo la creazione, copiare l'ID applicazione e salvarlo per usarlo in seguito.
6. Nella pagina Impostazioni selezionare **Autorizzazioni necessarie** e selezionare **Aggiungi**.
7. Scegliere **selezionare un'API**, cercare e selezionare **IdentityExperienceFramework**e quindi fare clic su **Seleziona**.
9. Selezionare la casella di controllo accanto a **Accesso a IdentityExperienceFramework**, fare clic su **Seleziona** e quindi su **Operazione completata**.
10. Selezionare **Concedi autorizzazioni** e quindi confermare selezionando **Sì**.

## <a name="custom-policy-starter-pack"></a>Starter Pack per criteri personalizzati

I criteri personalizzati sono un set di file XML che vengono caricati nel tenant di Azure AD B2C per definire i profili tecnici e i percorsi utente. Sono disponibili Starter Pack con diversi criteri predefiniti per iniziare rapidamente. Ognuno di questi Starter Pack contiene il minor numero di profili tecnici e percorsi utente necessari per realizzare gli scenari descritti:

- **Le LocalAccounts** -Abilita l'uso solo di account locali.
- **SocialAccounts** : consente l'uso solo di account di social networking o federati.
- **SocialAndLocalAccounts** : consente l'uso di account locali e di social networking.
- **SocialAndLocalAccountsWithMFA** -Abilita le opzioni di autenticazione a più fattori, a livello locale e sociale.

Ogni pacchetto Starter contiene:

- **File di base** : alcune modifiche sono necessarie per la base. Esempio: *TrustFrameworkBase. XML*
- **File di estensione** : questo file è il punto in cui vengono apportate le modifiche alla configurazione. Esempio: *TrustFrameworkExtensions. XML*
- **File relying party** : file specifici dell'attività chiamati dall'applicazione. Esempi: *SignUpOrSignin. XML*, *ProfileEdit. XML*, *PasswordReset. XML*

In questo articolo vengono modificati i file di criteri personalizzati XML nello Starter Pack di **SocialAndLocalAccounts** . Se è necessario un editor XML, provare [Visual Studio Code](https://code.visualstudio.com/download), un editor multipiattaforma leggero.

### <a name="get-the-starter-pack"></a>Ottenere lo Starter Pack

Ottenere gli Starter Pack per i criteri personalizzati da GitHub, quindi aggiornare i file XML nello Starter Pack SocialAndLocalAccounts con il nome del tenant Azure AD B2C.

1. [Scaricare il file con estensione zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) o clonare il repository:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. In tutti i file nella directory **SocialAndLocalAccounts** sostituire la stringa `yourtenant` con il nome del tenant Azure ad B2C.

    Ad esempio, se il nome del tenant B2C è *contosotenant*, tutte le istanze di `yourtenant.onmicrosoft.com` diventano `contosotenant.onmicrosoft.com`.

### <a name="add-application-ids-to-the-custom-policy"></a>Aggiungere gli ID dell'applicazione al criterio personalizzato

Aggiungere gli ID delle applicazioni al file delle estensioni *TrustFrameworkExtensions.xml*.

1. `<TechnicalProfile Id="login-NonInteractive">`Aprire `SocialAndLocalAccounts/` **e`TrustFrameworkExtensions.xml`** trovare l'elemento.
1. Sostituire entrambe le istanze `IdentityExperienceFrameworkAppId` di con l'ID applicazione dell'applicazione IdentityExperienceFramework creata in precedenza.
1. Sostituire entrambe le istanze `ProxyIdentityExperienceFrameworkAppId` di con l'ID applicazione dell'applicazione ProxyIdentityExperienceFramework creata in precedenza.
1. Salvare il file.

## <a name="upload-the-policies"></a>Caricare i criteri

1. Selezionare la voce di menu **Framework dell'esperienza di identità** nel tenant B2C nel portale di Azure.
1. Selezionare **carica criteri personalizzati**.
1. In questo ordine, caricare i file dei criteri:
    1. *TrustFrameworkBase. XML*
    1. *TrustFrameworkExtensions. XML*
    1. *SignUpOrSignin. XML*
    1. *ProfileEdit. XML*
    1. *PasswordReset. XML*

Quando si caricano i file, Azure aggiunge il `B2C_1A_` prefisso a ognuno di essi.

> [!TIP]
> Se l'editor XML supporta la convalida, convalidare i `TrustFrameworkPolicy_0.3.0.0.xsd` file in base all'XML Schema che si trova nella directory radice dello Starter Pack. La convalida dello schema XML identifica gli errori prima del caricamento.

## <a name="test-the-custom-policy"></a>Testare i criteri personalizzati

1. In **criteri personalizzati**selezionare **B2C_1A_signup_signin**.
1. Per **selezionare l'applicazione** nella pagina Panoramica del criterio personalizzato, selezionare l'applicazione Web denominata *app Web 1* registrata in precedenza.
1. Verificare che l' **URL di risposta** sia `https://jwt.ms`.
1. Selezionare **Esegui adesso**.
1. Iscriversi usando un indirizzo di posta elettronica.
1. Selezionare **Esegui** di nuovo.
1. Accedere con lo stesso account per verificare che la configurazione sia corretta.

## <a name="add-facebook-as-an-identity-provider"></a>Aggiungere Facebook come provider di identità

1. Completare i passaggi descritti in configurare l' [iscrizione e l'accesso con un account Facebook](active-directory-b2c-setup-fb-app.md) per configurare un'applicazione Facebook.
1. Nel file sostituire il valore di `client_id` con l'ID dell'applicazione Facebook: **`TrustFrameworkExtensions.xml`** `SocialAndLocalAccounts/`

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Caricare il file *TrustFrameworkExtensions.xml* nel tenant.
1. In **criteri personalizzati**selezionare **B2C_1A_signup_signin**.
1. Selezionare **Esegui adesso** e selezionare Facebook per accedere con Facebook e testare i criteri personalizzati. In alternativa, richiamare i criteri direttamente dall'applicazione registrata.

## <a name="next-steps"></a>Passaggi successivi

Provare quindi ad aggiungere Azure Active Directory (Azure AD) come provider di identità. Il file di base usato in questa Guida introduttiva contiene già parte del contenuto necessario per l'aggiunta di altri provider di identità, ad esempio Azure AD.

Per informazioni sulla configurazione di Azure AD e del provider di identità, vedere [configurare l'iscrizione e l'accesso con un account di Azure Active Directory usando Active Directory B2C criteri personalizzati](active-directory-b2c-setup-aad-custom.md).
