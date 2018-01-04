---
title: 'Azure Active Directory B2C: introduzione ai criteri personalizzati | Microsoft Docs'
description: Come iniziare a usare i criteri personalizzati di Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: mtillman
editor: rojasja
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: joroja;parahk;gsacavdm
ms.openlocfilehash: 826211dca59128a8b87ace44348dd5e2764bc0c3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-get-started-with-custom-policies"></a>Azure Active Directory B2C: introduzione ai criteri personalizzati

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dopo avere completato i passaggi descritti in questo articolo, il criterio personalizzato supporterà l'iscrizione o l'accesso all'"account locale" tramite un indirizzo di posta elettronica e una password. Si preparerà anche l'ambiente per l'aggiunta di provider di identità (ad esempio Facebook o Azure Active Directory). È consigliabile completare questi passaggi prima di leggere informazioni su altri usi del Framework dell'esperienza di gestione delle identità di Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Prerequisiti

Prima di procedere, assicurarsi di disporre di un tenant di Azure AD B2C, che è un contenitore per tutti gli utenti, le app, i criteri e altro ancora. In assenza di un tenant, è necessario [creare un tenant di Azure AD B2C](active-directory-b2c-get-started.md). Prima di procedere, è consigliabile che tutti gli sviluppatori completino le procedure dettagliate relative ai criteri predefiniti di Azure AD B2C e configurino la propria applicazione con i criteri predefiniti. Le applicazioni useranno entrambi i tipi di criteri con una semplice modifica del nome dei criteri per richiamare i criteri personalizzati.

>[!NOTE]
>Per accedere alla funzionalità di modifica dei criteri personalizzati è necessaria una sottoscrizione di Azure valida collegata al tenant. Se il [tenant di Azure AD B2C non è collegato a una sottoscrizione di Azure](active-directory-b2c-how-to-enable-billing.md) oppure la sottoscrizione di Azure è disabilitata, il pulsante Framework dell'esperienza di gestione delle identità non è disponibile.

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>Aggiungere chiavi di crittografia e di firma al tenant di B2C per l'uso da parte dei criteri personalizzati

1. Aprire il pannello **Framework dell'esperienza di gestione delle identità** nelle impostazioni del tenant di Azure AD B2C.
2. Selezionare **Chiavi dei criteri** per visualizzare le chiavi disponibili nel tenant.
3. Se non esiste, creare B2C_1A_TokenSigningKeyContainer:<br>
    a. Selezionare **Aggiungi**. <br>
    b. Selezionare **Genera**.<br>
    c. Per **Nome** usare `TokenSigningKeyContainer`. <br> 
    È possibile che il prefisso `B2C_1A_` venga aggiunto automaticamente.<br>
    d. For **Tipo di chiave** usare **RSA**.<br>
    e. Per **Date** usare le impostazioni predefinite. <br>
    f. Per **Uso chiave** usare **Firma**.<br>
    g. Selezionare **Create**.<br>
4. Se non esiste, creare B2C_1A_TokenEncryptionKeyContainer:<br>
 a. Selezionare **Aggiungi**.<br>
 b. Selezionare **Genera**.<br>
 c. Per **Nome** usare `TokenEncryptionKeyContainer`. <br>
   È possibile che il prefisso `B2C_1A`_ venga aggiunto automaticamente.<br>
 d. For **Tipo di chiave** usare **RSA**.<br>
 e. Per **Date** usare le impostazioni predefinite.<br>
 f. Per **Uso chiave** usare **Crittografia**.<br>
 g. Selezionare **Create**.<br>
5. Creare B2C_1A_FacebookSecret. <br>
Se si ha già un segreto dell'applicazione Facebook, aggiungerlo come chiave dei criteri nel tenant. In caso contrario, è necessario creare la chiave con un valore segnaposto affinché i criteri superino la convalida.<br>
 a. Selezionare **Aggiungi**.<br>
 b. Per **Opzioni** usare **Manuale**.<br>
 c. Per **Nome** usare `FacebookSecret`. <br>
 È possibile che il prefisso `B2C_1A_` venga aggiunto automaticamente.<br>
 d. Nella casella **Segreto** immettere il FacebookSecret disponibile in developers.facebook.com oppure `0` come segnaposto. *Non si tratta dell'ID dell'app Facebook*. <br>
 e. Per **Uso chiave** usare **Firma**. <br>
 f. Selezionare **Crea** e confermare la creazione.

## <a name="register-identity-experience-framework-applications"></a>Registrare le applicazioni del framework dell'esperienza di gestione delle identità

Azure AD B2C richiede di registrare due applicazioni aggiuntive che vengono usate dal motore per registrare e far accedere gli utenti.

>[!NOTE]
>È necessario creare due applicazioni per abilitare l'accesso con account locali: l'app Web IdentityExperienceFramework e l'app nativa ProxyIdentityExperienceFramework, con autorizzazione delegata dall'app IdentityExperienceFramework. Gli account locali esistono solo nel tenant. Per accedere alle applicazioni registrate nel tenant, gli utenti si iscrivono con una combinazione univoca di indirizzo di posta elettronica e password.

### <a name="create-the-identityexperienceframework-application"></a>Creare l'applicazione IdentityExperienceFramework

1. Nel [portale di Azure](https://portal.azure.com) passare al [contesto del tenant di Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md).
2. Aprire il pannello **Azure Active Directory** (non il pannello **Azure AD B2C**). Potrebbe essere necessario selezionare **Altri servizi** per individuarlo.
3. Selezionare **Registrazioni per l'app**.
4. Selezionare **Registrazione nuova applicazione**.
   * Per **Nome** usare `IdentityExperienceFramework`.
   * Per **Tipo di applicazione** usare **App Web/API**.
   * Per **URL di accesso** usare `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, dove `yourtenant` è il nome del dominio del tenant di Azure AD B2C.
5. Selezionare **Create**.
6. Dopo avere completato la creazione, selezionare l'applicazione appena creata **IdentityExperienceFramework**.<br>
   * Selezionare **Proprietà**.<br>
   * Copiare l'ID dell'applicazione e salvarlo per un uso successivo.

### <a name="create-the-proxyidentityexperienceframework-application"></a>Creare l'applicazione ProxyIdentityExperienceFramework

1. Selezionare **Registrazioni per l'app**.
1. Selezionare **Registrazione nuova applicazione**.
   * Per **Nome** usare `ProxyIdentityExperienceFramework`.
   * Per **Tipo di applicazione** usare **Nativo**.
   * Per **URI di reindirizzamento** usare `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, dove `yourtenant` è il tenant di Azure AD B2C.
1. Selezionare **Create**.
1. Dopo avere completato la creazione, selezionare l'applicazione **ProxyIdentityExperienceFramework**.<br>
   * Selezionare **Proprietà**. <br>
   * Copiare l'ID dell'applicazione e salvarlo per un uso successivo.
1. Selezionare **Autorizzazioni necessarie**.
1. Selezionare **Aggiungi**.
1. Fare clic su **Selezionare un'API**.
1. Cercare il nome IdentityExperienceFramework. Selezionare **IdentityExperienceFramework** nei risultati e quindi fare clic su **Seleziona**.
1. Selezionare la casella di controllo accanto a **Accesso a IdentityExperienceFramework** e quindi fare clic su **Seleziona**.
1. Selezionare **Operazione completata**.
1. Selezionare **Concedi autorizzazioni** e quindi confermare selezionando **Sì**.

## <a name="download-starter-pack-and-modify-policies"></a>Scaricare lo starter pack e modificare i criteri

I criteri personalizzati sono un set di file XML che devono essere caricati nel tenant di Azure AD B2C. Gli starter pack vengono forniti per consentire agli utenti di diventare rapidamente operativi. Ogni pacchetto Starter nell'elenco seguente contiene il più piccolo numero di profili di tecnici e i percorsi utente necessari per raggiungere gli scenari descritti:
 * LocalAccounts. Consente l'uso solo di account locali.
 * SocialAccounts. Consente l'uso solo di account di social networking (o federati).
 * **SocialAndLocalAccounts**. Si userà questo file per la procedura dettagliata.
 * SocialAndLocalAccountsWithMFA. Qui sono incluse le opzioni social, locale e Multi-Factor Authentication.

Ogni pacchetto Starter contiene:

* Il [file base](active-directory-b2c-overview-custom.md#policy-files) dei criteri. Alcune modifiche sono necessarie per la base.
* Il [file di estensione](active-directory-b2c-overview-custom.md#policy-files) dei criteri.  Questo file è quello in cui viene eseguita la maggior parte delle modifiche di configurazione.
* I [file relying party](active-directory-b2c-overview-custom.md#policy-files) sono file specifici delle attività chiamati dall'applicazione.

>[!NOTE]
>Se l'editor XML supporta la convalida, convalidare i file rispetto allo schema XML TrustFrameworkPolicy_0.3.0.0.xsd che si trova nella directory radice del pacchetto Starter. La convalida dello schema XML identifica gli errori prima del caricamento.

 Ecco come procedere:

1. Scaricare active-directory-b2c-custom-policy-starterpack da GitHub. [Scaricare il file con estensione zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) o eseguire

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. Aprire la cartella SocialAndLocalAccounts.  Il file di base (TrustFrameworkBase.xml) in questa cartella include il contenuto necessario agli account locali, aziendali e di social networking. Il contenuto sociale non interferisce con la procedura per creare ed eseguire gli account locali.
3. Aprire TrustFrameworkBase.xml. Se occorre un editor XML, provare [Visual Studio Code](https://code.visualstudio.com/download), un editor multipiattaforma leggero.
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
   >`PolicyId` è il nome dei criteri visualizzato nel portale e il nome con cui gli altri file dei criteri fanno riferimento ad esso.

5. Salvare il file.
6. Aprire TrustFrameworkExtensions.xml. Apportare le stesse due modifiche sostituendo `yourtenant.onmicrosoft.com` con il tenant di Azure AD B2C. Eseguire la stessa sostituzione nell'elemento `<TenantId>`, effettuando così in totale di tre modifiche. Salvare il file.
7. Aprire SignUpOrSignIn.xml. Apportare le stesse modifiche sostituendo `yourtenant.onmicrosoft.com` con il tenant di Azure AD B2C in tre posizioni. Salvare il file.
8. Aprire i file di modifica del profilo e di reimpostazione delle password. Apportare le stesse modifiche sostituendo `yourtenant.onmicrosoft.com` con il tenant di Azure AD B2C in tre posizioni in ogni file. Salvare i file.

### <a name="add-the-application-ids-to-your-custom-policy"></a>Aggiungere le ID dell'applicazione al criterio personalizzato
Aggiungere gli ID applicazione al file di estensione (`TrustFrameworkExtensions.xml`):

1. Nel file di estensione (TrustFrameworkExtensions.xml) trovare l'elemento `<TechnicalProfile Id="login-NonInteractive">`.
2. Sostituire entrambe le istanze di `IdentityExperienceFrameworkAppId` con l'ID dell'applicazione del framework dell'esperienza di gestione delle identità creata in precedenza. Di seguito è fornito un esempio: 

   ```xml
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```
3. Sostituire entrambe le istanze di `ProxyIdentityExperienceFrameworkAppId` con l'ID dell'applicazione proxy del framework dell'esperienza di gestione delle identità creata in precedenza.
4. Salvare il file delle estensioni.

## <a name="upload-the-policies-to-your-tenant"></a>Caricare i criteri nel tenant

1. Nel [portale di Azure](https://portal.azure.com) passare al [contesto del tenant di Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e aprire il pannello **Azure AD B2C**.
2. Fare clic su **Framework dell'esperienza di gestione delle identità**.
3. Selezionare **Carica criteri**.

    >[!WARNING]
    >I file dei criteri personalizzati devono essere caricati nell'ordine seguente:

1. Caricare TrustFrameworkBase.xml.
2. Caricare TrustFrameworkExtensions.xml.
3. Caricare SignUpOrSignin.xml.
4. Caricare gli altri file dei criteri.

Quando viene caricato un file dei criteri, all'inizio del nome viene aggiunto `B2C_1A_`.

## <a name="test-the-custom-policy-by-using-run-now"></a>Testare i criteri personalizzati tramite Esegui adesso

1. Aprire **Impostazioni di Azure AD B2C** e passare a **Framework dell'esperienza di gestione delle identità**.

   >[!NOTE]
   >Il comando **Esegui adesso** richiede che nel tenant sia preregistrata almeno un'applicazione. Le applicazioni devono essere registrate nel tenant di B2C usando l'opzione di menu **Applicazioni** in Azure AD B2C o usando il Framework dell'esperienza di gestione delle identità per richiamare i criteri sia predefiniti che personalizzati. È necessaria una sola registrazione per applicazione.<br><br>
   Per informazioni su come registrare le applicazioni, vedere l'articolo di [introduzione](active-directory-b2c-get-started.md) ad Azure AD B2C o l'articolo relativo alla [registrazione delle applicazioni](active-directory-b2c-app-registration.md).  

2. Aprire B2C_1A_signup_signin, i criteri personalizzati dalla relying party (RP) caricati in precedenza. Selezionare **Esegui adesso**.

3. Dovrebbe essere possibile iscriversi usando un indirizzo di posta elettronica.

4. Accedere con lo stesso account per verificare che la configurazione sia corretta.

>[!NOTE]
>Una causa comune degli errori di accesso è la configurazione non corretta dell'app IdentityExperienceFramework.


## <a name="next-steps"></a>Passaggi successivi

### <a name="add-facebook-as-an-identity-provider"></a>Aggiungere Facebook come provider di identità
Per configurare Facebook:
1. [Configurare un'applicazione Facebook in developers.facebook.com](active-directory-b2c-setup-fb-app.md).
2. [Aggiungere il segreto dell'applicazione Facebook al tenant di Azure AD B2C](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies).
3. Nel file dei criteri TrustFrameworkExtensions sostituire il valore di `client_id` con l'ID dell'applicazione Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
4. Caricare il file dei criteri TrustFrameworkExtensions.xml nel tenant.
5. Eseguire i test usando **Esegui adesso** oppure richiamando i criteri direttamente dall'applicazione registrata.

### <a name="add-azure-active-directory-as-an-identity-provider"></a>Aggiungere Azure Active Directory come provider di identità
Il file di base che è stato usato in questa Guida introduttiva contiene già parte del contenuto che è necessario per l'aggiunta di altri provider di identità. Per informazioni sull'impostazione degli accessi, vedere l'articolo [Azure Active Directory B2C: accesso con account di Azure AD](active-directory-b2c-setup-aad-custom.md).

Per una panoramica dei criteri personalizzati di Azure AD B2C che usano il Framework dell'esperienza di gestione delle identità, vedere l'articolo [Azure Active Directory B2C: criteri personalizzati](active-directory-b2c-overview-custom.md). 
