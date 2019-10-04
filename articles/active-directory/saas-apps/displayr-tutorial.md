---
title: 'Esercitazione: Integrazione di Azure Active Directory con Displayr | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Displayr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: b739b4e3-1a37-4e3c-be89-c3945487f4c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3cd1785595cf2f6b2401837780106f52fdc97e36
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67103978"
---
# <a name="tutorial-integrate-displayr-with-azure-active-directory"></a>Esercitazione: Integrazione di Displayr con Azure Active Directory

Questa esercitazione descrive come integrare Displayr con Azure Active Directory (Azure AD). Integrando Displayr con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Displayr.
* Abilitare gli utenti per l'accesso automatico a Displayr con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Displayr abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Displayr supporta l'accesso SSO avviato da **provider di servizi**.

## <a name="adding-displayr-from-the-gallery"></a>Aggiunta di Displayr dalla raccolta

Per configurare l'integrazione di Displayr in Azure AD, è necessario aggiungere Displayr dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Displayr** nella casella di ricerca.
1. Selezionare **Displayr** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso Single Sign-On di Azure AD con Displayr usando un utente di test di nome **Britta Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Displayr.

Per configurare e testare l'accesso SSO di Azure AD con Displayr, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
2. **[Configurare Displayr](#configure-displayr)** per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Displayr](#create-displayr-test-user)** : per avere una controparte di Britta Simon in Displayr collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Displayr** del [portale di Azure](https://portal.azure.com/) trovare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** eseguire il passaggio seguente:

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<YOURDOMAIN>.displayr.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `<YOURDOMAIN>.displayr.com`

    >[!NOTE]
    >Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Displayr](mailto:support@displayr.com). È anche possibile fare riferimento ai modelli mostrati nella sezione Configurazione SAML di base del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML**, trovare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

   ![Collegamento di download del certificato](common/certificatebase64.png)

1. L'applicazione Displayr prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic su **Modifica** per aprire la finestra di dialogo Attributi utente.

    ![image](common/edit-attribute.png)

1. Oltre a quanto elencato in precedenza, l'applicazione Displayr prevede il passaggio di altri attributi nella risposta SAML. Nella sezione **Attributi e attestazioni utente** della finestra di dialogo **Attestazioni gruppo (Anteprima)** seguire questa procedura:

    a. Fare clic sulla **penna** accanto a **Gruppi restituiti nell'attestazione**.

    ![image](./media/displayr-tutorial/config04.png)

    ![image](./media/displayr-tutorial/config05.png)

    b. Selezionare **Tutti i gruppi** dall'elenco dei pulsanti di opzione.

    c. Per **Attributo di origine** selezionare **ID gruppo**.

    d. Selezionare **Personalizza il nome dell'attestazione basata su gruppo**.

    e. Selezionare **Crea gruppi come attestazioni dei ruoli**.

    f. Fare clic su **Save**.

1. Nella sezione **Configura Displayr** copiare gli URL appropriati in base alle proprie esigenze.

   ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-displayr"></a>Configurare Displayr

1. Per automatizzare la configurazione all'interno di Displayr, è necessario installare l'**estensione del browser per l'accesso sicuro My Apps** facendo clic su **Installa estensione**.

    ![Estensione My Apps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Setup Displayr** (Configura Displayr) per passare direttamente all'applicazione Displayr. Da qui, fornire le credenziali di amministratore per accedere a Displayr. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 6.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare manualmente Displayr, aprire una nuova finestra del Web browser, accedere al sito aziendale di Displayr come amministratore e completare i passaggi seguenti:

4. Fare clic su **Settings** (Impostazioni) e quindi passare ad **Account**.

    ![Configurazione](./media/displayr-tutorial/config01.png)

5. Passare a **Settings** (Impostazioni) dal menu in alto e scorrere la pagina verso il basso per fare clic su **Configure Single Sign On (SAML)** (Configura Single Sign-On - SAML).

    ![Configurazione](./media/displayr-tutorial/config02.png)

6. Nella pagina **Single Sign-On (SAML)** seguire questa procedura:

    ![Configurazione](./media/displayr-tutorial/config03.png)

    a. Selezionare la casella **Enable Single Sign On (SAML)** (Abilita Single Sign-On - SAML).

    b. Copiare il valore **Identificatore** effettivo dalla sezione **Configurazione SAML di base** di Azure AD e incollarlo nella casella di testo **Autorità emittente**.

    c. Nella casella di testo **Login URL** (URL di accesso) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    d. Nella casella di testo **Logout URL** (URL di disconnessione) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    e. Aprire il certificato (Base64) nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo **Certificate** (Certificato).

    f. I valori di **Group mappings** (Mapping dei gruppi) sono facoltativi.

    g. Fare clic su **Save**.  

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `Britta Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `BrittaSimon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon verrà abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Displayr.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Displayr**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-displayr-test-user"></a>Creare un utente di test di Displayr

Per consentire agli utenti di Azure AD di accedere a Displayr, è necessario effettuarne il provisioning in Displayr. Nel caso di Displayr il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a Displayr come amministratore.

2. Fare clic su **Settings** (Impostazioni) e quindi passare ad **Account**.

    ![Configurazione di Displayr](./media/displayr-tutorial/config01.png)

3. Passare a **Settings** (Impostazioni) dal menu in alto e scorrere la pagina verso il basso fino alla sezione **Users** (Utenti) e quindi fare clic su **New User** (Nuovo utente).

    ![Configurazione di Displayr](./media/displayr-tutorial/config07.png)

4. Nella pagina **New User** (Nuovo utente) seguire questa procedura:

    ![Configurazione di Displayr](./media/displayr-tutorial/config06.png)

    a. Nella casella di testo **Name** (Nome) immettere il nome dell'utente, ad esempio **Brittasimon**.

    b. Nella casella di testo **E-mail** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio `Brittasimon@contoso.com`.

    c. Selezionare i valori appropriati per **Group membership** (Appartenenza a gruppi).

    d. Fare clic su **Save**.

### <a name="test-sso"></a>Testare l'accesso SSO

Quando si seleziona il riquadro Displayr nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Displayr per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
