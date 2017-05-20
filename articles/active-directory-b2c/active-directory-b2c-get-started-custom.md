---
title: 'Azure Active Directory B2C: introduzione ai criteri personalizzati| Documentazione Microsoft'
description: Come iniziare a usare i criteri personalizzati di Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: joroja;parahk;gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: b72db6a0eb8a8621be5f05da6028615d5d24ba1e
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017

---
# <a name="azure-active-directory-b2c-getting-started-with-custom-policies"></a>Azure Active Directory B2C: introduzione ai criteri personalizzati

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dopo aver completato i passaggi descritti in questo articolo, il criterio personalizzato supporterà l'iscrizione o l'accesso all'"account locale" tramite un indirizzo di posta elettronica e una password. Si preparerà anche l'ambiente per l'aggiunta di altri provider di identità (ad esempio Facebook o Azure AD).  È consigliabile completare questi passaggi prima che vengano illustrati tutti gli altri usi del framework dell'esperienza di gestione delle identità di Azure AD B2C e molti concetti essenziali.

## <a name="prerequisites"></a>Prerequisiti

Prima di procedere, assicurarsi di avere un tenant di Azure AD B2C. Un tenant di Azure AD B2C è un contenitore per tutti gli utenti, le app, i criteri e altro ancora. Se non ne è già disponibile uno, è necessario [crearlo](active-directory-b2c-get-started.md). Prima di procedere, è consigliabile che tutti gli sviluppatori completino le procedure dettagliate relative ai criteri predefiniti di Azure AD B2C e configurino la propria applicazione con i criteri predefiniti.  Le applicazioni useranno entrambi i tipi di criteri con una semplice modifica del nome dei criteri per richiamare i criteri personalizzati.

Per accedere alle funzionalità di modifica dei criteri personalizzati è necessaria una sottoscrizione di Azure valida collegata al tenant.

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>Aggiungere chiavi di firma e di crittografia al tenant di B2C per l'uso da parte dei criteri personalizzati

1. Passare al pannello Framework dell'esperienza di gestione delle identità nelle impostazioni del tenant di Azure AD B2C.
2. Selezionare **Chiavi dei criteri** per visualizzare le chiavi disponibili nel tenant.
3. Se non esiste, creare `B2C_1A_TokenSigningKeyContainer`:
 * Fare clic su **Aggiungi**
 * Opzioni > `Generate`
 * Nome > `TokenSigningKeyContainer`. Il prefisso B2C_1A_ potrebbe essere aggiunto automaticamente.
 * Tipo di chiave > `RSA`
 * Date > Usare i valori predefiniti
 * Uso chiave > `Signature`
 * Fare clic su **Crea**
4. Se non esiste, creare `B2C_1A_TokenEncryptionKeyContainer`:
 * Fare clic su **Aggiungi**
 * Opzioni > `Generate`
 * Nome > `TokenEncryptionKeyContainer`. Il prefisso B2C_1A_ potrebbe essere aggiunto automaticamente.
 * Tipo di chiave > `RSA`
 * Date > Usare i valori predefiniti
 * Uso chiave > `Encryption`
 * Fare clic su **Crea**
5. Creare `B2C_1A_FacebookSecret`. Se si ha già un segreto dell'applicazione Facebook, aggiungerlo come chiave dei criteri nel tenant.  In caso contrario, è necessario creare la chiave con un valore segnaposto affinché i criteri superino la convalida.
 * Fare clic su **Aggiungi**
 * Opzioni > `Manual`
 * Nome > `FacebookSecret`. Il prefisso B2C_1A_ potrebbe essere aggiunto automaticamente.
 * Segreto > Immettere il segreto FacebookSecret disponibile in developers.facebook.com oppure "0" come segnaposto. *Non si tratta dell'ID dell'app Facebook*.
 * Uso chiave > Firma
 * Fare clic su **Crea** e confermare la creazione

## <a name="register-identity-experience-framework-applications"></a>Registrare le applicazioni del framework dell'esperienza di gestione delle identità

Azure AD B2C richiede di registrare due applicazioni aggiuntive che vengono usate dal motore per registrare e far accedere gli utenti.

>[!NOTE]
>È necessario creare due applicazioni per abilitare l'accesso con account locali: l'app Web IdentityExperienceFramework e l'app nativa ProxyIdentityExperienceFramework, con autorizzazione delegata dall'app IdentityExperienceFramework. Gli account locali esistono solo nel tenant. Per accedere alle applicazioni registrate nel tenant, gli utenti finali si iscrivono con una combinazione univoca di indirizzo di posta elettronica e password.

### <a name="create-the-identityexperienceframework-application"></a>Creare l'applicazione IdentityExperienceFramework

1. Nel [portale di Azure](https://portal.azure.com) [passare al contesto del tenant di Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md).
1. Aprire il pannello `Azure Active Directory` (non il pannello Azure AD B2C). Potrebbe essere necessario fare clic su **> Altri servizi** per trovarlo.
1. Selezionare **Registrazioni per l'app**.
1. Fare clic su **+ Registrazione nuova applicazione**.
   * Nome: `IdentityExperienceFramework`
   * Tipo di applicazione: `Web app/API`
   * URL di accesso: `https://login.microsoftonline.com/yourtenant.onmicrosoft.com` dove `yourtenant` è il nome di dominio del tenant di Azure AD B2C.
1. Fare clic su **Crea**.
1. Al termine della creazione, selezionare l'applicazione `IdentityExperienceFramework` appena creata, fare clic su **Proprietà**, copiare l'ID applicazione e salvarlo per un uso successivo.

### <a name="create-the-proxy-identity-experience-framework-application"></a>Creare l'applicazione proxy del framework dell'esperienza di gestione delle identità

1. Selezionare **Registrazioni per l'app**.
1. Fare clic su **+ Registrazione nuova applicazione**.
   * Nome: `ProxyIdentityExperienceFramework`
   * Tipo di applicazione: `Native`
   * URL di accesso: `https://login.microsoftonline.com/yourtenant.onmicrosoft.com` dove `yourtenant` è il tenant di Azure AD B2C.
1. Fare clic su **Crea**.
1. Al termine della creazione, selezionare l'applicazione `ProxyIdentityExperienceFramework`, fare clic su **Proprietà**, copiare l'ID applicazione e salvarlo per un uso successivo.
1. Selezionare **Autorizzazioni necessarie**, selezionare **+ Aggiungi** e quindi **Selezionare un'API**.
1. Cercare il nome `IdentityExperienceFramework`, selezionare IdentityExperienceFramework nei risultati e quindi fare clic su **Seleziona**.
1. Selezionare la casella di controllo accanto a `Access IdentityExperienceFramework` e quindi fare clic su **Seleziona**.
1. Fare clic su **Done**.
1. Fare clic su **Concedi autorizzazioni** e quindi su **Sì** per confermare.

## <a name="download-starter-pack-and-modify-policies"></a>Scaricare lo starter pack e modificare i criteri

I criteri personalizzati sono un set di file XML che devono essere caricati nel tenant di Azure AD B2C. Gli starter pack vengono forniti per consentire agli utenti di diventare rapidamente operativi. Ogni starter pack riportato di seguito include i profili tecnici e i percorsi utente minimi necessari per realizzare gli scenari descritti.
 * LocalAccounts: consente l'uso solo di account locali
 * SocialAccounts: consente l'uso solo di account di social networking (o federati)
 * **SocialAndLocalAccounts**: verrà usato in questa procedura guidata
 * SocialAndLocalAccountsWithMFA: include account locali e di social networking e opzioni MFA

Ogni starter pack contiene gli elementi seguenti:

* Il [file base](active-directory-b2c-overview-custom.md#policy-files) dei criteri. Alcune modifiche sono necessarie per la base.
* Il [file di estensione](active-directory-b2c-overview-custom.md#policy-files) dei criteri.  Questo file è quello in cui viene eseguita la maggior parte delle modifiche di configurazione.
* [File relying party](active-directory-b2c-overview-custom.md#policy-files). Si tratta di file specifici delle attività, chiamati dall'applicazione per una determinata attività.

>[!NOTE]
>Se l'editor XML supporta la convalida, si può voler convalidare i file rispetto al file dello schema XML `TrustFrameworkPolicy_0.3.0.0.xsd` che si trova nella cartella radice dello starter pack. La convalida dello schema XML identifica gli errori prima del caricamento.

Di seguito sono riportati i requisiti iniziali:

1. Scaricare "active-directory-b2c-custom-policy-starterpack" da GitHub.  [Scaricare il file zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) o eseguire

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. Aprire la cartella `SocialAndLocalAccounts`.  Il file base (`TrustFrameworkBase.xml`) in questa cartella contiene del contenuto che è necessario per gli account locali e sociali/aziendali. Il contenuto sociale non interferisce con la procedura per creare ed eseguire gli account locali.
3. Aprire `TrustFrameworkBase.xml`.  Se è necessario un editor XML, provare [Visual Studio Code](https://code.visualstudio.com/download), un editor multipiattaforma leggero.
4. Nell'elemento `TrustFrameworkPolicy` radice aggiornare gli attributi `TenantId` e `PublicPolicyUri` sostituendo `yourtenant.onmicrosoft.com` con il nome di dominio del tenant di Azure AD B2C:

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="yourtenant.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://yourtenant.onmicrosoft.com">
    ```
>[!NOTE]
>`PolicyId` è il nome dei criteri che verrà visualizzato nel portale e il nome con cui gli altri file dei criteri faranno riferimento a questo.

5. Salvare il file.
6. Aprire `TrustFrameworkExtensions.xml` e apportare le stesse due modifiche sostituendo `yourtenant.onmicrosoft.com` con il tenant di Azure AD B2C. Eseguire la stessa sostituzione nell'elemento `<TenantId>`, effettuando in totale 3 modifiche.  Salvare il file.
7. Aprire `SignUpOrSignIn.xml` e apportare le stesse modifiche sostituendo `yourtenant.onmicrosoft.com` con il tenant di Azure AD B2C in tre posizioni. Salvare il file.
8. Aprire i file di modifica dei profili e del segreto delle password e apportare le stesse modifiche sostituendo `yourtenant.onmicrosoft.com` con il tenant di Azure AD B2C in tre posizioni in ogni file. Salvare i file.

### <a name="add-the-application-ids-to-your-custom-policy"></a>Aggiungere le ID dell'applicazione al criterio personalizzato
Aggiungere gli ID applicazione al file di estensioni (`TrustFrameworkExtensions.xml`).

1. Nel file delle estensioni (`TrustFrameworkExtensions.xml`) trovare l'elemento `<TechnicalProfile Id="login-NonInteractive">`.
2. Sostituire entrambe le istanze di `IdentityExperienceFrameworkAppId` con l'ID dell'applicazione del framework dell'esperienza di gestione delle identità creata in precedenza. Di seguito è fornito un esempio:

```xml
<Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
```

3. Sostituire entrambe le istanze di `ProxyIdentityExperienceFrameworkAppId` con l'ID dell'applicazione proxy del framework dell'esperienza di gestione delle identità creata in precedenza.
4. Salvare il file delle estensioni.

## <a name="upload-the-policies-to-your-tenant"></a>Caricare i criteri nel tenant

1. Nel [portale di Azure](https://portal.azure.com) [passare al contesto del tenant di Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e aprire il pannello Azure AD B2C.
2. Fare clic su **Framework dell'esperienza di gestione delle identità**.
3. Selezionare **Carica il criterio** per caricare i file dei criteri.

    >[!WARNING]
    >I file dei criteri personalizzati devono essere caricati nell'ordine seguente:

1. Carica `TrustFrameworkBase.xml`.
2. Carica `TrustFrameworkExtensions.xml`.
3. Carica `SignUpOrSignin.xml`.
4. Caricare gli altri file dei criteri.

Quando viene caricato un file dei criteri, all'inizio del nome viene aggiunto `B2C_1A_`.

## <a name="test-the-custom-policy-using-run-now"></a>Testare i criteri personalizzati tramite "Esegui adesso"

1. Aprire **Impostazioni di Azure AD B2C** e passare a **Framework dell'esperienza di gestione delle identità**.

>[!NOTE]
>Il comando **Esegui adesso** richiede che nel tenant sia preregistrata almeno un'applicazione. Le applicazioni devono essere registrate nel tenant di B2C usando l'opzione di menu **Applicazioni** in B2C o in Framework dell'esperienza di gestione delle identità per richiamare i criteri sia predefiniti che personalizzati. È necessaria una sola registrazione per applicazione. 

Per informazioni su come registrare le applicazioni, vedere l'[introduzione](active-directory-b2c-get-started.md) ad Azure AD B2C o l'articolo relativo alla [registrazione delle applicazioni](active-directory-b2c-app-registration.md).  

2. Aprire i criteri personalizzati di relying party (RP) in cui è stato caricato `B2C_1A_signup_signin` e fare clic sul pulsante **Esegui adesso**.


3. Dovrebbe essere possibile accedere usando un indirizzo di posta elettronica.
4. Accedere con lo stesso account per verificare che la configurazione sia corretta.

>[!NOTE]
>Una causa comune degli errori di accesso è la configurazione non corretta dell'app IdentityExperienceFramework.


## <a name="next-steps"></a>Passaggi successivi

### <a name="add-facebook-as-an-identity-provider"></a>Aggiungere Facebook come provider di identità
Per configurare Facebook:
1. [Configurare un'applicazione Facebook in developers.facebook.com](active-directory-b2c-setup-fb-app.md)
2. [Aggiungere il segreto dell'applicazione Facebook al tenant di Azure AD B2C](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies)
3. Aggiungere l'ID dell'applicazione Facebook in `Item Key="client_id"` nel file dei criteri TrustFrameworkExtensions:

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  <Metadata>
  <!--Replace the value of client_id in this technical profile with your the Facebook App ID"-->
    <Item Key="client_id">00000000000000</Item>
```
4. Caricare il file dei criteri TrustFrameworkExtensions.xml nel tenant.
5. Eseguire il test con **Esegui adesso** oppure richiamare i criteri direttamente dall'applicazione registrata.

### <a name="add-azure-active-directory-as-an-identity-provider"></a>Aggiungere Azure Active Directory come provider di identità
Il file di base che è stato usato in questa Guida introduttiva contiene già parte del contenuto che è necessario per l'aggiunta di altri provider di identità. Per impostare l'account di accesso tramite gli account di Azure AD, [continua qui](active-directory-b2c-setup-aad-custom.md).


## <a name="reference"></a>Riferimento

[Panoramica](active-directory-b2c-overview-custom.md) dei criteri personalizzati in Azure AD B2C con il framework dell'esperienza di gestione delle identità

