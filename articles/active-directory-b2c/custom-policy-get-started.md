---
title: Introduzione ai criteri personalizzati
titleSuffix: Azure AD B2C
description: Informazioni su come iniziare a usare i criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 856bd6c2a3546a438293e89a0b576e1392d9c6a5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407283"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Introduzione ai criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[I criteri personalizzati](custom-policy-overview.md) sono file di configurazione che definiscono il comportamento del tenant di Azure Active Directory B2C (Azure AD B2C). In questo articolo viene creato un criterio personalizzato che supporta l'iscrizione o l'accesso all'account locale tramite un indirizzo di posta elettronica e una password. È inoltre necessario preparare l'ambiente per l'aggiunta di provider di identità.

## <a name="prerequisites"></a>Prerequisiti

- Se non ne hai già uno, crea un tenant B2C di Azure AD collegato alla sottoscrizione di Azure.If you don't already one one, [create an Azure AD B2C tenant](tutorial-create-tenant.md) that is linked to your Azure subscription.
- [Registrare l'applicazione](tutorial-register-applications.md) nel tenant creato in modo che possa comunicare con Azure AD B2C.
- Completa i passaggi descritti in [Configurare l'iscrizione e l'accesso con un account Facebook](identity-provider-facebook.md) per configurare un'applicazione Facebook. Anche se un'applicazione Facebook non è necessaria per l'utilizzo di criteri personalizzati, viene utilizzata in questa procedura dettagliata per illustrare l'abilitazione dell'accesso social in un criterio personalizzato.

## <a name="add-signing-and-encryption-keys"></a>Aggiungere le chiavi di firma e di crittografia

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory - Sottoscrizione** nella barra degli strumenti del portale e quindi selezionare la directory che contiene il tenant B2C di Azure AD.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. Nella pagina panoramica, in **Criteri**, selezionare **Framework di esperienza identità**.

### <a name="create-the-signing-key"></a>Creazione della chiave di firma

1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere `Generate`.
1. In **Nome** immettere `TokenSigningKeyContainer`. È possibile che il prefisso `B2C_1A_` venga aggiunto automaticamente.
1. Per **Tipo di chiave** selezionare **RSA**.
1. Per **Uso chiave** selezionare **Firma**.
1. Selezionare **Create** (Crea).

### <a name="create-the-encryption-key"></a>Creazione della chiave di crittografia

1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere `Generate`.
1. In **Nome** immettere `TokenEncryptionKeyContainer`. È possibile che il prefisso `B2C_1A`_ venga aggiunto automaticamente.
1. Per **Tipo di chiave** selezionare **RSA**.
1. In **Uso chiave**selezionare **Crittografia**.
1. Selezionare **Create** (Crea).

### <a name="create-the-facebook-key"></a>Creazione della chiave Facebook

Aggiungi il [segreto](identity-provider-facebook.md) dell'app dell'app di Facebook come chiave dei criteri. È possibile utilizzare il segreto app dell'applicazione creata come parte dei prerequisiti di questo articolo.

1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere `Manual`.
1. Per **Nome** immettere `FacebookSecret`. È possibile che il prefisso `B2C_1A_` venga aggiunto automaticamente.
1. In **Segreto**, inserisci app *Secret* dell'applicazione Facebook da developers.facebook.com. Questo valore è il segreto, non l'ID applicazione.
1. Per **Uso chiave** selezionare **Firma**.
1. Selezionare **Create** (Crea).

## <a name="register-identity-experience-framework-applications"></a>Registrare le applicazioni del framework dell'esperienza di gestione delle identità

Azure AD B2C richiede la registrazione di due applicazioni che usa per accedere agli utenti con account locali: *IdentityExperienceFramework*, un'API Web e *ProxyIdentityExperienceFramework*, un'app nativa con autorizzazione delegata per l'app IdentityExperienceFramework. Gli utenti possono registrarsi con un indirizzo di posta elettronica o un nome utente e una password per accedere alle applicazioni registrate nel tenant, che crea un "account locale". Gli account locali esistono solo nel tenant B2C di Azure AD.

È necessario registrare queste due applicazioni nel tenant B2C di Azure AD una sola volta.

### <a name="register-the-identityexperienceframework-application"></a>Registrare l'applicazione IdentityExperienceFramework

Per registrare un'applicazione nel tenant B2C di Azure AD, è possibile usare l'esperienza di registrazione delle **app (legacy)** o la nuova esperienza di registrazione unificata delle **app (anteprima).** [Altre informazioni sulla nuova esperienza](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Applicazioni](#tab/applications/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel portale di Azure cercare e selezionare **Azure Active Directory**.
1. Nel menu Panoramica di **Azure Active Directory,** in **Gestisci,** selezionare **Registrazioni app (legacy).**
1. Selezionare **Registrazione nuova applicazione**.
1. Per **Nome** immettere `IdentityExperienceFramework`.
1. Per **Tipo di applicazione** scegliere **App Web/API**.
1. Per **URL di accesso** immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, dove `your-tenant-name` è il nome di dominio del tenant di Azure AD B2C. Tutti gli URL dovrebbero ora usare [b2clogin.com](b2clogin.md).
1. Selezionare **Create** (Crea). Dopo la creazione, copiare l'ID applicazione e salvarlo per usarlo in seguito.

#### <a name="app-registrations-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. Selezionare **Registrazioni app (anteprima)** e quindi **Nuova registrazione**.
1. Per **Nome** immettere `IdentityExperienceFramework`.
1. In **Tipi di account supportati**selezionare Account solo nella directory **dell'organizzazione.**
1. In **URI di reindirizzamento**selezionare `your-tenant-name` **Web**e quindi immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, dove è il nome di dominio tenant di Azure AD B2C.
1. In **Autorizzazioni**, selezionare la casella di controllo *Concedere il consenso amministratore alle autorizzazioni OpenID e offline_access*.
1. Selezionare **Registra**.
1. Prendere nota del valore di **ID applicazione (client)**, che sarà necessario in un passaggio successivo.

Esporre quindi l'API aggiungendo un ambito:Next, expose the API by adding a scope:

1. In **Gestisci** selezionare **Esponi un'API**.
1. Selezionare **Aggiungi un ambito**, quindi Salva e **continuare** ad accettare l'URI dell'ID applicazione predefinito.
1. Immettere i valori seguenti per creare un ambito che consenta l'esecuzione di criteri personalizzati nel tenant B2C di Azure AD:Enter the following values to create a scope that allows custom policy execution in your Azure AD B2C tenant:
    * **Nome ambito**:`user_impersonation`
    * **Nome visualizzato consenso amministratore**:`Access IdentityExperienceFramework`
    * **Descrizione consenso dell'amministratore**:`Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. Selezionare **Aggiungi ambito**

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registrare l'applicazione ProxyIdentityExperienceFramework

#### <a name="applications"></a>[Applicazioni](#tab/applications/)

1. In **Registrazioni app (legacy)** selezionare **Nuova registrazione applicazione**.
1. Per **Nome** immettere `ProxyIdentityExperienceFramework`.
1. Per **Tipo di applicazione** scegliere **Nativo**.
1. Per URI di `myapp://auth` **reindirizzamento**, immettere .
1. Selezionare **Create** (Crea). Dopo la creazione, copiare l'ID applicazione e salvarlo per usarlo in seguito.
1. Selezionare **Impostazioni**, quindi **Autorizzazioni necessarie**e infine **Aggiungi**.
1. Scegliere **Seleziona un'API**, cercare e selezionare **IdentityExperienceFramework**, quindi fare clic su **Seleziona**.
1. Selezionare la casella di controllo accanto a **Accesso a IdentityExperienceFramework**, fare clic su **Seleziona** e quindi su **Operazione completata**.
1. Selezionare **Concedi autorizzazioni**, quindi confermare selezionando **Sì**.

#### <a name="app-registrations-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. Selezionare **Registrazioni app (anteprima)** e quindi **Nuova registrazione**.
1. Per **Nome** immettere `ProxyIdentityExperienceFramework`.
1. In **Tipi di account supportati**selezionare Account solo nella directory **dell'organizzazione.**
1. In **URI di reindirizzamento** usare l'elenco a discesa per selezionare **Client pubblico/nativo (per dispositivi mobili e desktop)**.
1. Per URI di `myapp://auth` **reindirizzamento**, immettere .
1. In **Autorizzazioni**, selezionare la casella di controllo *Concedere il consenso amministratore alle autorizzazioni OpenID e offline_access*.
1. Selezionare **Registra**.
1. Prendere nota del valore di **ID applicazione (client)**, che sarà necessario in un passaggio successivo.

Successivamente, specificare che l'applicazione deve essere considerata come un client pubblico:Next, specify that the application should be treated as a public client:

1. In **Gestisci** selezionare **Autenticazione**.
1. Selezionare **Prova la nuova esperienza** (se visualizzato).
1. In **Impostazioni avanzate**abilitare Considera **l'applicazione come client pubblico** (selezionare **Sì**).
1. Selezionare **Salva**.

A questo punto, concedere le autorizzazioni all'ambito API esposto in precedenza nella registrazione di IdentityExperienceFramework:Now, grant permissions to the API scope you exposed earlier in the *IdentityExperienceFramework* registration:

1. In **Gestisci** selezionare **Autorizzazioni API**.
1. In **Autorizzazioni configurate** selezionare **Aggiungi un'autorizzazione**.
1. Selezionare la scheda API personali, quindi selezionare l'applicazione **IdentityExperienceFramework.Select** the **My APIs** tab, then select the IdentityExperienceFramework application.
1. In **Autorizzazione**selezionare l'ambito **user_impersonation** definito in precedenza.
1. Selezionare **Aggiungi autorizzazioni**. Come indicato, attendere alcuni minuti prima di procedere con il passaggio successivo.
1. Selezionare **Concedi consenso amministratore per (nome del tenant)**.
1. Selezionare l'account amministratore attualmente connesso oppure accedere con un account nel tenant di Azure AD B2C a cui sia stato assegnato almeno il ruolo di *amministratore applicazione cloud*.
1. Selezionare **Accetto**.
1. Selezionare **Aggiorna**, quindi verificare che "Concesso per ..." viene visualizzato in **Stato** per entrambi gli ambiti. La propagazione delle autorizzazioni potrebbe richiedere alcuni minuti.

* * *

## <a name="custom-policy-starter-pack"></a>Starter pack per criteri personalizzati

I criteri personalizzati sono un set di file XML caricati nel tenant B2C di Azure AD per definire i profili tecnici e i percorsi utente. Forniamo pacchetti di avviamento con diversi criteri predefiniti per iniziare rapidamente. Ognuno di questi starter pack contiene il minor numero di profili tecnici e percorsi utente necessari per ottenere gli scenari descritti:

- **LocalAccounts:** abilita l'utilizzo solo di account locali.
- **SocialAccounts:** consente l'utilizzo solo di account social (o federati).
- **SocialAndLocalAccounts:** consente l'utilizzo di account locali e social.
- **SocialAndLocalAccountsWithMFA** - Abilita le opzioni di autenticazione social, locale e a più fattori.

Ogni pacchetto Starter contiene:

- **File di base** - Sono necessarie poche modifiche alla base. Esempio: *TrustFrameworkBase.xml*
- **File di estensione** - Questo file è dove vengono apportate la maggior parte delle modifiche alla configurazione. Esempio: *TrustFrameworkExtensions.xml*
- **File della relying party:** file specifici dell'attività chiamati dall'applicazione. Esempi: *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*

In questo articolo si modificano i file di criteri personalizzati XML nel pacchetto di avvio **SocialAndLocalAccounts.** Se è necessario un editor XML, provare [Visual Studio Code](https://code.visualstudio.com/download), un editor multipiattaforma leggero.

### <a name="get-the-starter-pack"></a>Ottenere il pacchetto di avviamento

Ottenere gli starter pack dei criteri personalizzati da GitHub, quindi aggiornare i file XML nello starter pack SocialAndLocalAccounts con il nome del tenant B2C di Azure AD.

1. [Scaricare il file .zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) o clonare il repository:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. In tutti i file nella directory **SocialAndLocalAccounts** sostituire la stringa `yourtenant` con il nome del tenant B2C di Azure AD.

    Ad esempio, se il nome del tenant B2C `yourtenant.onmicrosoft.com` è `contosotenant.onmicrosoft.com` *contosotenant*, tutte le istanze di become .

### <a name="add-application-ids-to-the-custom-policy"></a>Aggiungere gli ID dell'applicazione al criterio personalizzato

Aggiungere gli ID delle applicazioni al file delle estensioni *TrustFrameworkExtensions.xml*.

1. Aprire `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** e trovare `<TechnicalProfile Id="login-NonInteractive">`l'elemento .
1. Sostituire entrambe `IdentityExperienceFrameworkAppId` le istanze di con l'ID applicazione dell'applicazione IdentityExperienceFramework creata in precedenza.
1. Sostituire entrambe `ProxyIdentityExperienceFrameworkAppId` le istanze di con l'ID applicazione dell'applicazione ProxyIdentityExperienceFramework creata in precedenza.
1. Salvare il file.

## <a name="upload-the-policies"></a>Caricare i criteri

1. Selezionare la voce di menu Identity Experience Framework nel tenant B2C nel portale di Azure.Select the **Identity Experience Framework** menu item item in your B2C tenant in the Azure portal.
1. Selezionare **Carica criterio personalizzato**.
1. In questo ordine, caricare i file di criteri:In this order, upload the policy files:
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *SignUpOrSignin.xml*
    1. *ProfileEdit.xml (Modifica.xml)*
    1. *PasswordReset.xml*

Quando si caricano i file, Azure aggiunge il prefisso `B2C_1A_` a ciascuno di essi.

> [!TIP]
> Se l'editor XML supporta la convalida, convalidare i file in base allo `TrustFrameworkPolicy_0.3.0.0.xsd` schema XML che si trova nella directory radice dello starter pack. La convalida dello schema XML identifica gli errori prima del caricamento.

## <a name="test-the-custom-policy"></a>Testare i criteri personalizzati

1. In **Criteri personalizzati**selezionare **B2C_1A_signup_signin**.
1. Per **Seleziona applicazione** nella pagina di panoramica del criterio personalizzato, selezionare l'applicazione Web denominata *webapp1* registrata in precedenza.
1. Assicurarsi che l'URL di **risposta** sia `https://jwt.ms`.
1. Selezionare **Esegui adesso**.
1. Registrati utilizzando un indirizzo email.
1. Selezionare Di nuovo **Esegui.**
1. Accedere con lo stesso account per verificare che la configurazione sia corretta.

## <a name="add-facebook-as-an-identity-provider"></a>Aggiungere Facebook come provider di identità

Come accennato in [Prerequisiti](#prerequisites), Facebook *non* è necessario per l'utilizzo di criteri personalizzati, ma viene utilizzato qui per dimostrare come è possibile abilitare l'accesso social federato in un criterio personalizzato.

1. `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** Nel file, sostituisci `client_id` il valore di con l'ID applicazione Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Caricare il file *TrustFrameworkExtensions.xml* nel tenant.
1. In **Criteri personalizzati**selezionare **B2C_1A_signup_signin**.
1. Seleziona **Esegui ora** e seleziona Facebook per accedere con Facebook e testare le norme personalizzate.

## <a name="next-steps"></a>Passaggi successivi

Provare quindi ad aggiungere Azure Active Directory (Azure AD) come provider di identità. Il file di base usato in questa guida introduttiva contiene già parte del contenuto necessario per l'aggiunta di altri provider di identità come Azure AD.

Per informazioni sull'impostazione di Azure AD come provider di identità, vedere [Configurare l'iscrizione e l'accesso con un account Azure Active Directory usando i criteri personalizzati di Active Directory B2C.](identity-provider-azure-ad-single-tenant-custom.md)
