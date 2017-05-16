---
title: 'Azure Active Directory B2C: introduzione ai criteri personalizzati| Documentazione Microsoft'
description: Un argomento su come iniziare a usare i criteri personalizzati di Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: gsacavdm
manager: krassk
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 93fc922433f0ba60ee438db8d4dcb14527c208fc
ms.contentlocale: it-it
ms.lasthandoff: 05/02/2017

---
# <a name="azure-active-directory-b2c-getting-started-with-custom-policies"></a>Azure Active Directory B2C: introduzione ai criteri personalizzati

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dopo aver completato i passaggi descritti in questo articolo, il criterio personalizzato supporterà l'iscrizione o l'accesso all'"account locale" tramite un indirizzo di posta elettronica e una password. Si preparerà anche l'ambiente per l'aggiunta di altri provider di identità (ad esempio Facebook o Azure AD).  È necessario il completamento di questi passaggi prima che vengano illustrati tutti gli altri usi del motore di esperienza di identità di Azure AD B2C e molti concetti essenziali.

## <a name="prerequisites"></a>Prerequisiti

Prima di procedere, assicurarsi di avere un tenant di Azure AD B2C. Un tenant di Azure AD B2C è un contenitore per tutti gli utenti, le app, i criteri e altro ancora. Se non ne è già disponibile uno, è necessario [crearlo](active-directory-b2c-get-started.md).

### <a name="confirming-your-b2c-tenant"></a>Conferma del tenant B2C

Poiché i criteri personalizzati sono ancora in anteprima privata, confermare che il tenant di Azure AD B2C sia abilitato per il caricamento dei criteri personalizzati:

1. Nel [portale di Azure](https://portal.azure.com) [passare al contesto del tenant di Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e aprire il pannello Azure AD B2C.
1. Fare clic su **Tutti i criteri**.
1. Assicurarsi che **Carica il criterio** sia disponibile.  Se il pulsante è disabilitato, inviare tramite posta elettronica AADB2CPreview@microsoft.com.

## <a name="set-up-keys-for-your-custom-policy"></a>Impostare le chiavi per il criterio personalizzato

Il primo passaggio necessario per usare i criteri personalizzati è l'impostazione delle chiavi.

[!INCLUDE [active-directory-b2c-setup-keys-custom.md](../../includes/active-directory-b2c-setup-keys-custom.md)]

## <a name="download-starter-pack-and-modify-policies"></a>Scaricare lo starter pack e modificare i criteri

I criteri personalizzati sono un set di file XML che devono essere caricati nel tenant di Azure AD B2C. Offriamo uno starter pack che è possibile usare per iniziare. Lo starter pack contiene:

* Il [file base](active-directory-b2c-overview-custom.md#policy-files) dei criteri. Alcune modifiche sono necessarie per la base.
* Il [file di estensione](active-directory-b2c-overview-custom.md#policy-files) dei criteri.  Questo file è quello in cui viene eseguita la maggior parte delle modifiche di configurazione.

Di seguito sono riportati i requisiti iniziali:

1. Scaricare lo "Starter pack dei criteri personalizzati di Azure AD B2C" da GitHub.  [Scaricare il file zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) o eseguire

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. Aprire la cartella `SocialIDPAndLocalAccounts`.  Il file base (`TrustFrameworkBase.xml`) in questa cartella contiene del contenuto che è necessario per gli account locali e sociali/aziendali. Il contenuto sociale non interferisce con la procedura per creare ed eseguire gli account locali.
1. Aprire `TrustFrameworkBase.xml`.  Se serve un editor XML, provare a [Scaricare Visual Studio Code](https://code.visualstudio.com/download), un editor multi-piattaforma leggero.
1. Nell'elemento della radice `TrustFrameworkPolicy`, aggiornare gli attributi `TenantId` e `PublicPolicyUri`, sostituendo `{tenantName}` con il tenant di Azure AD B2C:

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="{tenantName}.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://{tenantName}.onmicrosoft.com">
    ```

1. Salvare il file.
1. Aprire `TrustFrameworkExtensions.xml` e apportare le stesse modifiche sostituendo `{tenantName}` con il tenant di Azure AD B2C. Salvare il file.
1. Aprire `SignUpOrSignIn.xml` e apportare le stesse modifiche sostituendo `{tenantName}` con il tenant di Azure AD B2C. Salvare il file.

>[!NOTE]
>Se l'editor XML supporta la convalida, si può voler convalidare i file rispetto al file dello schema XML `TrustFrameworkPolicy_0.3.0.0.xsd` che si trova nella cartella radice dello starter pack. La convalida dello schema XML identifica gli errori prima del caricamento.

## <a name="register-policy-engine-applications"></a>Registrare le applicazioni del motore di criteri

Azure AD B2C richiede di registrare due applicazioni aggiuntive che vengono usate dal motore per registrare e far accedere gli utenti.

>[!NOTE]
>Di seguito, si creano le due applicazioni che consentono l'accesso usando account locali: PolicyEngine (un'app web) e PolicyEngineProxy (un'app nativa) con le autorizzazioni delegate da PolicyEngine. Questa sezione è necessaria esclusivamente per i tenant di Azure AD B2C laddove sia previsto l'uso di account locali.

### <a name="create-the-policy-engine-application"></a>Creare l'applicazione del motore di criteri

1. Nel [portale di Azure](https://portal.azure.com) [passare al contesto del tenant di Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md).
1. Aprire il pannello `Azure Active Directory` (non il pannello Azure AD B2C). Potrebbe essere necessario fare clic su **> Altri servizi** per trovarlo.
1. Selezionare **Registrazioni per l'app**.
1. Fare clic su **+ Registrazione nuova applicazione**.
   * Nome: `PolicyEngine`
   * Tipo di applicazione: `Web app/API`
   * URL di accesso: `https://login.microsoftonline.com/{tenantName}.onmicrosoft.com` dove `{tenantName}` è il tenant di Azure AD B2C.
1. Fare clic su **Crea**.
1. Dopo averla creata, selezionare l'applicazione appena creata `PolicyEngine`, copiare l'ID dell'applicazione e salvarlo per un momento successivo.

### <a name="create-the-policy-engine-proxy-application"></a>Creare l'applicazione proxy del motore di criteri

1. Selezionare **Registrazioni per l'app**.
1. Fare clic su **+ Registrazione nuova applicazione**.
   * Nome: `PolicyEngineProxy`
   * Tipo di applicazione: `Native`
   * URL di accesso: `https://login.microsoftonline.com/{tenantName}.onmicrosoft.com` dove `{tenantName}` è il tenant di Azure AD B2C.
1. Fare clic su **Crea**.
1. Dopo averla creata, selezionare l'applicazione `PolicyEngineProxy`, copiare l'ID dell'applicazione e salvarlo per un momento successivo.
1. Selezionare **Autorizzazioni necessarie**, selezionare **+ Aggiungi** e quindi **Selezionare un'API**.
1. Cercare il nome `PolicyEngine`, selezionare PolicyEngine nei risultati e quindi fare clic su **Seleziona**.
1. Selezionare la casella di controllo accanto a `Access PolicyEngine` e quindi fare clic su **Seleziona**.
1. Fare clic su **Done**.

### <a name="add-the-application-ids-to-your-custom-policy"></a>Aggiungere le ID dell'applicazione al criterio personalizzato

Per creare un criterio personalizzato con gli account locali abilitati, è necessario aggiungere gli ID dell'applicazione al file delle estensioni (`TrustFrameworkExtensions.xml`).

1. Nel file delle estensioni (`TrustFrameworkExtensions.xml`) trovare l'elemento `<TechnicalProfile Id="login-NonInteractive">`.
1. Sostituire entrambe le istanze di `{Policy Engine Proxy Application ID}` con l'ID applicazione dell'[applicazione proxy del motore di criteri che è stata creata](#create-the-policy-engine-proxy-application).
1. Sostituire entrambe le istanze di `{Policy Engine Application ID}` con l'ID applicazione dell'[applicazione del motore di criteri che è stata creata](#create-the-policy-engine-application).
1. Salvare il file delle estensioni.

## <a name="upload-the-policies-to-your-tenant"></a>Caricare i criteri nel tenant

1. Nel [portale di Azure](https://portal.azure.com) [passare al contesto del tenant di Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e aprire il pannello Azure AD B2C.
1. Fare clic su **Tutti i criteri**.
1. Selezionare **Carica il criterio**.

    >[!WARNING]
    >I file dei criteri personalizzati devono essere caricati nell'ordine seguente:

1. Carica `TrustFrameworkBase.xml`.
1. Carica `TrustFrameworkExtensions.xml`.
1. Carica `SignUpOrSignin.xml`.

Quando viene caricato un file, il nome è preceduto da `B2C_1A_`.  I criteri predefiniti invece cominciano con `B2C_1_`.

## <a name="test-the-custom-policy-using-run-now"></a>Testare i criteri personalizzati tramite "Esegui adesso"

1. Aprire il **Pannello Azure AD B2C** e passare a **Tutti i criteri**.
1. Selezionare il criterio personalizzato che è stato caricato e fare clic sul pulsante **Esegui adesso**.
1. Dovrebbe essere possibile accedere usando un indirizzo di posta elettronica.

## <a name="next-steps"></a>Passaggi successivi

Il file di base che è stato usato in questa Guida introduttiva contiene già parte del contenuto che è necessario per l'aggiunta di altri provider di identità. Per impostare l'account di accesso tramite gli account di Azure AD, [continua qui](active-directory-b2c-setup-aad-custom.md).

## <a name="reference"></a>Riferimento

* Un **Profilo tecnico (TP)** è un elemento che definisce il nome di un endpoint, i relativi metadati, il protocollo e i dettagli dello scambio di attestazioni che devono essere eseguiti dal motore di esperienza di identità.  L'accesso dell'Account locale è il TechnicalProfile usato dal motore di esperienza di identità per eseguire un accesso su account locale.

