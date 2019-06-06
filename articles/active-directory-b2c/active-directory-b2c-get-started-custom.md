---
title: Introduzione ai criteri personalizzati - Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come iniziare a usare i criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2929c033b4744ea89f8e3d711a5e2e0df6301c14
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730017"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Introduzione ai criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I [criteri personalizzati](active-directory-b2c-overview-custom.md) sono file di configurazione che definiscono il comportamento del tenant di Azure Active Directory (Azure AD) B2C. In questo articolo viene creato un criterio personalizzato che supporta l'iscrizione o l'accesso all'account locale tramite un indirizzo di posta elettronica e una password. È inoltre necessario preparare l'ambiente per l'aggiunta di provider di identità.

## <a name="prerequisites"></a>Prerequisiti

- In assenza di un tenant, è necessario [creare un tenant di Azure AD B2C](tutorial-create-tenant.md) collegato alla sottoscrizione di Azure.
- [Registrare l'applicazione](tutorial-register-applications.md) nel tenant creato in modo che possa comunicare con Azure AD B2C.

## <a name="add-signing-and-encryption-keys"></a>Aggiungere le chiavi di firma e di crittografia

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
2. Assicurarsi che si usa la directory che contiene il tenant di Azure AD B2C. Fare clic sui **Directory e sottoscrizione filtro** nel menu in alto e scegliere la directory che contiene il tenant. 
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

1. Scegli **tutti i servizi** nell'angolo superiore sinistro del portale di Azure, cercare e selezionare **Azure Active Directory**.
2. Nel menu, selezionare **registrazioni per l'App (Legacy)** .
3. Selezionare **Registrazione nuova applicazione**.
4. Per **Nome** immettere `IdentityExperienceFramework`.
5. Per **Tipo di applicazione** scegliere **App Web/API**.
6. Per **URL di accesso** immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, dove `your-tenant-name` è il nome di dominio del tenant di Azure AD B2C. Tutti gli URL dovrebbero ora usare [b2clogin.com](b2clogin.md).
7. Fare clic su **Create**(Crea). Dopo la creazione, copiare l'ID applicazione e salvarlo per usarlo in seguito.

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registrare l'applicazione ProxyIdentityExperienceFramework

1. Nelle **registrazioni per l'App (Legacy)** , selezionare **registrazione nuova applicazione**.
2. Per **Nome** immettere `ProxyIdentityExperienceFramework`.
3. Per **Tipo di applicazione** scegliere **Nativo**.
4. Per **URI di reindirizzamento** immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, dove `your-tenant-name` è il tenant di Azure AD B2C.
5. Fare clic su **Create**(Crea). Dopo la creazione, copiare l'ID applicazione e salvarlo per usarlo in seguito.
6. Nella pagina Impostazioni selezionare **Autorizzazioni necessarie** e selezionare **Aggiungi**.
7. Scegli **selezionare un'API**, cercare e selezionare **IdentityExperienceFramework**, quindi fare clic su **selezionare**.
9. Selezionare la casella di controllo accanto a **Accesso a IdentityExperienceFramework**, fare clic su **Seleziona** e quindi su **Operazione completata**.
10. Selezionare **Concedi autorizzazioni** e quindi confermare selezionando **Sì**.

## <a name="download-starter-pack-and-modify-policies"></a>Scaricare lo starter pack e modificare i criteri

I criteri personalizzati sono un set di file XML che devono essere caricati nel tenant di Azure AD B2C. Gli starter pack vengono forniti per consentire agli utenti di diventare rapidamente operativi. Ogni pacchetto Starter nell'elenco seguente contiene il più piccolo numero di profili di tecnici e i percorsi utente necessari per raggiungere gli scenari descritti:

- LocalAccounts: consente l'uso solo di account locali.
- SocialAccounts: consente l'uso solo di account di social networking (o federati).
- SocialAndLocalAccounts: consente sia l'uso di account locali che di account di social networking.
- SocialAndLocalAccountsWithMFA: abilita le opzioni di social networking, locali e di autenticazione a più fattori.

Ogni pacchetto Starter contiene:

- File di base. Alcune modifiche sono necessarie per la base.
- Aprire il file di estensione.  Questo file è quello in cui viene eseguita la maggior parte delle modifiche di configurazione.
- I file di relying party. File specifici delle attività, richiamati dall'applicazione.

>[!NOTE]
>Se l'editor XML supporta la convalida, convalidare i file rispetto allo schema XML TrustFrameworkPolicy_0.3.0.0.xsd che si trova nella directory radice del pacchetto Starter. La convalida dello schema XML identifica gli errori prima del caricamento.

1. [Scaricare il file con estensione zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) o eseguire:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

2. Nella cartella SocialAndLocalAccounts modificare tutti file sostituendo `yourtenant` con il nome del tenant. Ad esempio: `contosoTenant.onmicrosoft.com`. Se occorre un editor XML, provare [Visual Studio Code](https://code.visualstudio.com/download), un editor multipiattaforma leggero.

### <a name="add-application-ids-to-the-custom-policy"></a>Aggiungere gli ID dell'applicazione al criterio personalizzato

Aggiungere gli ID delle applicazioni al file delle estensioni *TrustFrameworkExtensions.xml*.

1. Aprire il file *TrustFrameworkExtensions.xml* file e trovare l'elemento `<TechnicalProfile Id="login-NonInteractive">`.
2. Sostituire entrambe le istanze di `IdentityExperienceFrameworkAppId` con l'ID dell'applicazione del framework dell'esperienza di gestione delle identità creata in precedenza. Sostituire entrambe le istanze di `ProxyIdentityExperienceFrameworkAppId` con l'ID dell'applicazione proxy del framework dell'esperienza di gestione delle identità creata in precedenza.
3. Salvare il file delle estensioni.

## <a name="upload-the-policies"></a>Caricare i criteri

1. Nella pagina dei criteri personalizzati del framework dell'esperienza di gestione delle identità selezionare **Carica criteri**.
1. In questo ordine, caricare *TrustFrameworkBase.xml*, *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* e *PasswordReset.xml*. Quando viene caricato un file dei criteri, all'inizio del nome viene aggiunto `B2C_1A_`.

## <a name="test-the-custom-policy"></a>Testare i criteri personalizzati

1. Nella pagina dei criteri personalizzati selezionare **B2C_1A_signup_signin**.
2. Per la **selezionare l'applicazione** nella pagina Panoramica del criterio personalizzato, selezionare l'applicazione web denominata *App Web 1* che è stato registrato in precedenza. Assicurarsi che il **URL di risposta** è `https://jwt.ms`.
3. Selezionare **Esegui adesso**.
4. Dovrebbe essere possibile iscriversi usando un indirizzo di posta elettronica.
5. Accedere con lo stesso account per verificare che la configurazione sia corretta.

## <a name="add-facebook-as-an-identity-provider"></a>Aggiungere Facebook come provider di identità

1. Configurare un'[applicazione Facebook](active-directory-b2c-setup-fb-app.md).
2. Nel file dei criteri *TrustFrameworkExtensions.xml* sostituire il valore di `client_id` con l'ID dell'applicazione Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
3. Caricare il file *TrustFrameworkExtensions.xml* nel tenant.
4. Eseguire i test usando **Esegui adesso** oppure richiamando i criteri direttamente dall'applicazione registrata.

## <a name="next-steps"></a>Passaggi successivi

- Aggiungere Azure Active Directory come provider di identità. Il file di base che è stato usato in questa Guida introduttiva contiene già parte del contenuto che è necessario per l'aggiunta di altri provider di identità. Per informazioni sull'impostazione degli accessi, vedere l'articolo [Azure Active Directory B2C: accedere usando account di Azure AD](active-directory-b2c-setup-aad-custom.md).
