---
title: Introduzione ai criteri personalizzati
titleSuffix: Azure AD B2C
description: Informazioni su come iniziare a usare criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 32ec55a2ed6e0158a05f81067dc834fdc1e6e765
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738252"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Introduzione ai criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I [criteri personalizzati](custom-policy-overview.md) sono file di configurazione che definiscono il comportamento del tenant di Azure Active Directory B2C (Azure AD B2C). In questo articolo viene creato un criterio personalizzato che supporta l'iscrizione o l'accesso all'account locale tramite un indirizzo di posta elettronica e una password. È inoltre necessario preparare l'ambiente per l'aggiunta di provider di identità.

## <a name="prerequisites"></a>Prerequisiti

- In assenza di un tenant, [creare un tenant di Azure AD B2C](tutorial-create-tenant.md) collegato alla sottoscrizione di Azure.
- [Registrare l'applicazione](tutorial-register-applications.md) nel tenant creato in modo che possa comunicare con Azure AD B2C.
- Completare la procedura descritta in [Configurare l'iscrizione e l'accesso con un account Facebook](identity-provider-facebook.md) per configurare un'applicazione Facebook. Sebbene la disponibilità di un'applicazione Facebook non sia un requisito da soddisfare per poter usare criteri personalizzati, viene usata in questa procedura dettagliata per dimostrare come abilitare l'accesso dai social network nei criteri personalizzati.

## <a name="add-signing-and-encryption-keys"></a>Aggiungere le chiavi di firma e di crittografia

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. Nella pagina Panoramica, nella sezione **Criteri**, selezionare **Framework dell'esperienza di gestione delle identità**.

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

Aggiungere la [Chiave privata app](identity-provider-facebook.md) dell'applicazione Facebook come chiave dei criteri. È possibile usare la chiave privata dell'applicazione creata nell'ambito dei prerequisiti di questo articolo.

1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere `Manual`.
1. Per **Nome** immettere `FacebookSecret`. È possibile che il prefisso `B2C_1A_` venga aggiunto automaticamente.
1. In **Segreto** immettere la *Chiave privata app* dell'applicazione Facebook da developers.facebook.com. Questo valore è la chiave privata, non l'ID applicazione.
1. Per **Uso chiave** selezionare **Firma**.
1. Selezionare **Create** (Crea).

## <a name="register-identity-experience-framework-applications"></a>Registrare le applicazioni del framework dell'esperienza di gestione delle identità

Azure AD B2C richiede di registrare due applicazioni che verranno usate per registrare e far accedere gli utenti con account locali: L'API Web *IdentityExperienceFramework* e l'app nativa *ProxyIdentityExperienceFramework*, con autorizzazione delegata dall'app IdentityExperienceFramework. Gli utenti possono iscriversi con un indirizzo di posta elettronica o un nome utente e una password per accedere alle applicazioni registrate nel tenant, in modo da creare un "account locale". Gli account locali esistono solo nel tenant di Azure AD B2C.

È necessario registrare queste due applicazioni nel tenant di Azure AD B2C una sola volta.

### <a name="register-the-identityexperienceframework-application"></a>Registrare l'applicazione IdentityExperienceFramework

Per registrare un'applicazione nel tenant di Azure AD B2C, è possibile usare l'esperienza **Registrazioni app (legacy)** o la nuova esperienza **Registrazioni app (anteprima)** unificata. [Altre informazioni sulla nuova esperienza](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Applicazioni](#tab/applications/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel portale di Azure cercare e selezionare **Azure Active Directory**.
1. Nel menu della panoramica di **Azure Active Directory**, nella sezione **Gestisci**, selezionare **Registrazioni app (legacy)** .
1. Selezionare **Registrazione nuova applicazione**.
1. Per **Nome** immettere `IdentityExperienceFramework`.
1. Per **Tipo di applicazione** scegliere **App Web/API**.
1. Per **URL di accesso** immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, dove `your-tenant-name` è il nome di dominio del tenant di Azure AD B2C. Tutti gli URL dovrebbero ora usare [b2clogin.com](b2clogin.md).
1. Selezionare **Create** (Crea). Dopo la creazione, copiare l'ID applicazione e salvarlo per usarlo in seguito.

#### <a name="app-registrations-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. Selezionare **Registrazioni app (anteprima)** e quindi **Nuova registrazione**.
1. Per **Nome** immettere `IdentityExperienceFramework`.
1. In **Tipi di account supportati** selezionare **Account solo in questa directory organizzativa**.
1. In **URI di reindirizzamento** selezionare **Web** e quindi immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, dove `your-tenant-name` è il nome di dominio del tenant di Azure AD B2C.
1. In **Autorizzazioni**, selezionare la casella di controllo *Concedere il consenso amministratore alle autorizzazioni OpenID e offline_access*.
1. Selezionare **Registra**.
1. Prendere nota del valore di **ID applicazione (client)** , che sarà necessario in un passaggio successivo.

Esporre quindi l'API aggiungendo un ambito:

1. In **Gestisci** selezionare **Esponi un'API**.
1. Selezionare **Aggiungi un ambito** e quindi **Salva e continua** per accettare l'URI dell'ID applicazione predefinito.
1. Immettere i valori seguenti per creare un ambito che consenta l'esecuzione di criteri personalizzati nel tenant di Azure AD B2C:
    * **Nome ambito**: `user_impersonation`
    * **Nome visualizzato per il consenso amministratore**: `Access IdentityExperienceFramework`
    * **Descrizione del consenso amministratore**: `Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. Selezionare **Aggiungi ambito**

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registrare l'applicazione ProxyIdentityExperienceFramework

#### <a name="applications"></a>[Applicazioni](#tab/applications/)

1. In **Registrazioni app (legacy)** selezionare **Registrazione nuova applicazione**.
1. Per **Nome** immettere `ProxyIdentityExperienceFramework`.
1. Per **Tipo di applicazione** scegliere **Nativo**.
1. In **URI di reindirizzamento** immettere `myapp://auth`.
1. Selezionare **Create** (Crea). Dopo la creazione, copiare l'ID applicazione e salvarlo per usarlo in seguito.
1. Selezionare **Impostazioni**, quindi **Autorizzazioni necessarie** e infine **Aggiungi**.
1. Scegliere **Selezionare un'API**, cercare e selezionare **IdentityExperienceFramework** e quindi fare clic su **Seleziona**.
1. Selezionare la casella di controllo accanto a **Accesso a IdentityExperienceFramework**, fare clic su **Seleziona** e quindi su **Operazione completata**.
1. Selezionare **Concedi autorizzazioni** e quindi confermare selezionando **Sì**.

#### <a name="app-registrations-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. Selezionare **Registrazioni app (anteprima)** e quindi **Nuova registrazione**.
1. Per **Nome** immettere `ProxyIdentityExperienceFramework`.
1. In **Tipi di account supportati** selezionare **Account solo in questa directory organizzativa**.
1. In **URI di reindirizzamento** usare l'elenco a discesa per selezionare **Client pubblico/nativo (per dispositivi mobili e desktop)** .
1. In **URI di reindirizzamento** immettere `myapp://auth`.
1. In **Autorizzazioni**, selezionare la casella di controllo *Concedere il consenso amministratore alle autorizzazioni OpenID e offline_access*.
1. Selezionare **Registra**.
1. Prendere nota del valore di **ID applicazione (client)** , che sarà necessario in un passaggio successivo.

Specificare quindi che l'applicazione deve essere considerata come un client pubblico:

1. In **Gestisci** selezionare **Autenticazione**.
1. Selezionare **Prova la nuova esperienza** (se visualizzato).
1. In **Impostazioni avanzate** abilitare **Gestire l'applicazione come un client pubblico** (selezionare **Sì**). Verificare che nel manifesto dell'applicazione sia impostato **"allowPublicClient": true**. 
1. Selezionare **Salva**.

È ora necessario concedere le autorizzazioni all'ambito dell'API esposto in precedenza nella registrazione di *IdentityExperienceFramework*:

1. In **Gestisci** selezionare **Autorizzazioni API**.
1. In **Autorizzazioni configurate** selezionare **Aggiungi un'autorizzazione**.
1. Selezionare la scheda **Le mie API** e quindi selezionare l'applicazione **IdentityExperienceFramework**.
1. In **Autorizzazione** selezionare l'ambito **user_impersonation** definito in precedenza.
1. Selezionare **Aggiungi autorizzazioni**. Come indicato, attendere alcuni minuti prima di procedere con il passaggio successivo.
1. Selezionare **Concedi consenso amministratore per (nome del tenant)** .
1. Selezionare l'account amministratore attualmente connesso oppure accedere con un account nel tenant di Azure AD B2C a cui sia stato assegnato almeno il ruolo di *amministratore applicazione cloud*.
1. Selezionare **Accetto**.
1. Selezionare **Aggiorna** e quindi verificare che il testo "Concesso per" sia visualizzato in **Stato** per entrambi gli ambiti. La propagazione delle autorizzazioni potrebbe richiedere alcuni minuti.

* * *

## <a name="custom-policy-starter-pack"></a>Pacchetto Starter per i criteri personalizzati

I criteri personalizzati sono costituiti da un set di file XML che viene caricato nel tenant di Azure AD B2C per definire i profili tecnici e i percorsi utente. Per ridurre i tempi, sono già disponibili pacchetti Starter con alcuni criteri predefiniti. Ogni pacchetto Starter contiene il numero minimo di profili tecnici e percorsi utente necessario per conseguire gli scenari descritti:

- **LocalAccounts** - Consente l'uso solo di account locali.
- **SocialAccounts** - Consente l'uso solo di account di social networking (o federati).
- **SocialAndLocalAccounts** - Consente sia l'uso di account locali che di account di social networking.
- **SocialAndLocalAccountsWithMFA** - Consente l'uso di opzioni di social networking, locali e di autenticazione a più fattori.

Ogni pacchetto Starter contiene:

- **File di base** - Sono necessarie alcune modifiche alla configurazione di base. Esempio: *TrustFrameworkBase.xml*
- **Extension file** - Questo è il file in cui deve essere apportata la maggior parte delle modifiche di configurazione. Esempio: *TrustFrameworkExtensions.xml*
- **File relying party** - File di attività specifiche chiamati dall'applicazione. Esempi: *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*

In questo articolo si modificheranno i file dei criteri personalizzati XML presenti nel pacchetto Starter **SocialAndLocalAccounts**. Se occorre un editor XML, provare [Visual Studio Code](https://code.visualstudio.com/download), un editor multipiattaforma leggero.

### <a name="get-the-starter-pack"></a>Ottenere il pacchetto Starter

Ottenere i pacchetti Starter dei criteri personalizzati da GitHub e aggiornare quindi i file XML del pacchetto Starter SocialAndLocalAccounts con il nome del tenant di Azure AD B2C.

1. È possibile [scaricare il file ZIP](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) o clonare il repository:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. In tutti i file nella directory **SocialAndLocalAccounts** sostituire la stringa `yourtenant` con il nome del tenant di Azure AD B2C.

    Se, ad esempio, il nome del tenant B2C è *contosotenant*, tutte le istanze di `yourtenant.onmicrosoft.com` diventeranno `contosotenant.onmicrosoft.com`.

### <a name="add-application-ids-to-the-custom-policy"></a>Aggiungere gli ID dell'applicazione al criterio personalizzato

Aggiungere gli ID delle applicazioni al file delle estensioni *TrustFrameworkExtensions.xml*.

1. Aprire `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** e trovare l'elemento `<TechnicalProfile Id="login-NonInteractive">`.
1. Sostituire entrambe le istanze di `IdentityExperienceFrameworkAppId` con l'ID dell'applicazione IdentityExperienceFramework creata in precedenza.
1. Sostituire entrambe le istanze di `ProxyIdentityExperienceFrameworkAppId` con l'ID dell'applicazione ProxyIdentityExperienceFramework creata in precedenza.
1. Salvare il file.

## <a name="upload-the-policies"></a>Caricare i criteri

1. Selezionare la voce di menu **Identity Experience Framework** nel tenant B2C nel portale di Azure.
1. Selezionare **Carica criteri personalizzati**.
1. Caricare i file dei criteri, nell'ordine in cui sono elencati:
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *SignUpOrSignin.xml*
    1. *ProfileEdit.xml*
    1. *PasswordReset.xml*

Mentre si caricano i file, Azure aggiunge il prefisso `B2C_1A_` a ognuno di essi.

> [!TIP]
> Se l'editor XML in uso supporta la convalida, convalidare i file rispetto allo schema XML `TrustFrameworkPolicy_0.3.0.0.xsd` che si trova nella directory radice del pacchetto Starter. La convalida dello schema XML identifica gli errori prima del caricamento.

## <a name="test-the-custom-policy"></a>Testare i criteri personalizzati

1. In **Criteri personalizzati** selezionare **B2C_1A_signup_signin**.
1. Per **Seleziona applicazione** nella pagina di panoramica dei criteri personalizzati, selezionare l'applicazione Web denominata *webapp1* registrata in precedenza.
1. Verificare che l'**URL di risposta** sia `https://jwt.ms`.
1. Selezionare **Esegui adesso**.
1. Iscriversi usando un indirizzo di posta elettronica.
1. Selezionare di nuovo **Esegui adesso**.
1. Accedere con lo stesso account per verificare che la configurazione sia corretta.

## <a name="add-facebook-as-an-identity-provider"></a>Aggiungere Facebook come provider di identità

Come specificato nella sezione [Prerequisiti](#prerequisites), Facebook *non* è necessario per l'uso dei criteri personalizzati, ma viene usato qui per dimostrare come poter abilitare l'accesso ai criteri personalizzati tramite un account di social networking federato.

1. Nel file `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** sostituire il valore di `client_id` con l'ID dell'applicazione Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Caricare il file *TrustFrameworkExtensions.xml* nel tenant.
1. In **Criteri personalizzati** selezionare **B2C_1A_signup_signin**.
1. Selezionare **Esegui ora** e quindi Facebook per accedere con Facebook e testare i criteri personalizzati.

## <a name="next-steps"></a>Passaggi successivi

Provare ad aggiungere Azure Active Directory (Azure AD) come provider di identità. Il file di base che è stato usato in questa Guida introduttiva contiene già parte del contenuto necessario per aggiungere altri provider di identità come Azure AD.

Per informazioni sull'impostazione di Azure AD come provider di identità, vedere l'articolo [Configurare l'accesso con un account Azure Active Directory usando criteri personalizzati in Active Directory B2C](identity-provider-azure-ad-single-tenant-custom.md).
