---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Check Point CloudGuard Dome9 Arc | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Check Point CloudGuard Dome9 Arc.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75f0669a474c24647e71eae8b5e0e0830b7c0bef
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533089"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-cloudguard-dome9-arc"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Check Point CloudGuard Dome9 Arc

Questa esercitazione descrive come integrare Check Point CloudGuard Dome9 Arc con Azure Active Directory (Azure AD). Integrando Check Point CloudGuard Dome9 Arc con Azure Active Directory, è possibile:

* Controllare in Azure AD chi può accedere a Check Point CloudGuard Dome9 Arc.
* Abilitare gli utenti per l'accesso automatico a Check Point CloudGuard Dome9 Arc con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Check Point CloudGuard Dome9 Arc abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Check Point CloudGuard Dome9 Arc supporta l'accesso SSO avviato da **SP e IDP**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Aggiunta di Check Point CloudGuard Dome9 Arc dalla raccolta

Per configurare l'integrazione di Check Point CloudGuard Dome9 Arc in Azure AD, è necessario aggiungere Check Point CloudGuard Dome9 Arc dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Check Point CloudGuard Dome9 Arc** nella casella di ricerca.
1. Selezionare **Check Point CloudGuard Dome9 Arc** dal riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-check-point-cloudguard-dome9-arc"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Check Point CloudGuard Dome9 Arc

Configurare e testare l'accesso SSO di Azure AD con Check Point CloudGuard Dome9 Arc usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Check Point CloudGuard Dome9 Arc.

Per configurare e testare l'accesso SSO di Azure AD con Check Point CloudGuard Dome9 Arc, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Check Point CloudGuard Dome9 Arc](#configure-check-point-cloudguard-dome9-arc-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Check Point CloudGuard Dome9 Arc](#create-check-point-cloudguard-dome9-arc-test-user)** : per avere una controparte di B.Simon in Check Point CloudGuard Dome9 Arc collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Check Point CloudGuard Dome9 Arc** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL: `https://secure.dome9.com/`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di risposta e l'URL di accesso effettivi. Il valore di `<company name>` si otterrà nella sezione **Configurare l'accesso Single Sign-On di Check Point CloudGuard Dome9 Arc**, illustrata più avanti nell'esercitazione. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione Check Point CloudGuard Dome9 Arc prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/edit-attribute.png)

1. Oltre quelli elencati in precedenza, l'applicazione Check Point CloudGuard Dome9 Arc prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.
    
    | Nome |  Attributo di origine|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    >[!NOTE]
    >Fare clic [qui](https://docs.microsoft.com/en-us/azure/active-directory/saas-apps/apptio-tutorial) per informazioni su come configurare i ruoli in Azure AD.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Check Point CloudGuard Dome9 Arc** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Check Point CloudGuard Dome9 Arc.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Check Point CloudGuard Dome9 Arc**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-check-point-cloudguard-dome9-arc-sso"></a>Configurare l'accesso Single Sign-On di Check Point CloudGuard Dome9 Arc

1. Per automatizzare la configurazione all'interno di Check Point CloudGuard Dome9 Arc, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione**.

    ![Estensione per App personali](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Check Point CloudGuard Dome9 Arc** per passare direttamente all'applicazione Check Point CloudGuard Dome9 Arc. Da qui, fornire le credenziali di amministratore per accedere a Check Point CloudGuard Dome9 Arc. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 6.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare manualmente Check Point CloudGuard Dome9 Arc, aprire una nuova finestra del Web browser, accedere al sito aziendale di Check Point CloudGuard Dome9 Arc come amministratore e seguire questa procedura:

2. Fare clic su **Impostazioni del profilo** nell'angolo superiore destro e quindi fare clic su **Impostazioni account**. 

    ![Configurazione di Check Point CloudGuard Dome9 Arc](./media/dome9arc-tutorial/configure1.png)

3. Passare a **SSO** e quindi fare clic su **Abilita**.

    ![Configurazione di Check Point CloudGuard Dome9 Arc](./media/dome9arc-tutorial/configure2.png)

4. Nella sezione di configurazione di SSO eseguire la procedura seguente:

    ![Configurazione di Check Point CloudGuard Dome9 Arc](./media/dome9arc-tutorial/configure3.png)

    a. Immettere il nome della società nella casella di testo **ID account**. Questo valore deve essere usato in **URL di risposta** e in **URL di accesso** menzionati nella sezione **Configurazione SAML di base** del portale di Azure.

    b. Nella casella di testo **Issuer** (Autorità di certificazione) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c. Nella casella di testo **Idp endpoint url** (URL endpoint IdP) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    d. Aprire il certificato con codifica Base64 scaricato nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **X.509 certificate** (Certificato X.509).

    e. Fare clic su **Save**.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Creare l'utente di test di Check Point CloudGuard Dome9 Arc

Per consentire agli utenti di Azure AD di accedere a Check Point CloudGuard Dome9 Arc, è necessario effettuarne il provisioning nell'applicazione. Check Point CloudGuard Dome9 Arc supporta il provisioning JIT, ma per garantire il corretto funzionamento di tale provisioning è necessario selezionare un **ruolo** specifico e assegnarlo all'utente.

   >[!Note]
   >Per la creazione del **ruolo** e per altre informazioni, contattare il [team di supporto clienti di Check Point CloudGuard Dome9 Arc](mailto:Dome9@checkpoint.com).

**Per eseguire il provisioning di un account utente manualmente, seguire questa procedura:**

1. Accedere al sito aziendale di Check Point CloudGuard Dome9 Arc come amministratore.

2. Fare clic su **Utenti e ruoli** e quindi su **Utenti**.

    ![Aggiungere un dipendente](./media/dome9arc-tutorial/user1.png)

3. Fare clic su **Aggiungi utente**.

    ![Aggiungere un dipendente](./media/dome9arc-tutorial/user2.png)

4. Nella sezione **Crea utente**, seguire la procedura seguente:

    ![Aggiungere un dipendente](./media/dome9arc-tutorial/user3.png)

    a. Nella casella di testo **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica di un utente, ad esempio B.Simon@contoso.com.

    b. Digitare il nome dell'utente, ad esempio B., nella casella di testo **First Name** (Nome).

    c. Digitare il cognome dell'utente, ad esempio Simon, nella casella di testo **Last Name** (Cognome).

    d. Impostare l'opzione **SSO User** (Utente SSO) su **On** (Abilitato).

    e. Fare clic su **Create** (Crea).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Check Point CloudGuard Dome9 Arc nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Check Point CloudGuard Dome9 Arc per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Check Point CloudGuard Dome9 Arc con Azure AD](https://aad.portal.azure.com/)