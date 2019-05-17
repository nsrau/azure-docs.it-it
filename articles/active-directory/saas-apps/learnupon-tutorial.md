---
title: 'Esercitazione: Integrazione di Azure Active Directory con LearnUpon | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e LearnUpon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 574c21dc2713f10513ac296e7db538e20a94c9d6
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406522"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Esercitazione: Integrazione di Azure Active Directory con LearnUpon

Questa esercitazione descrive come integrare LearnUpon con Azure Active Directory (Azure AD).
L'integrazione di LearnUpon con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a LearnUpon.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a LearnUpon con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con LearnUpon, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di LearnUpon abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.


* LearnUpon supporta l'accesso SSO avviato da **IDP**

* LearnUpon supporta il provisioning utenti **JIT**


## <a name="adding-learnupon-from-the-gallery"></a>Aggiunta di LearnUpon dalla raccolta

Per configurare l'integrazione di LearnUpon in Azure AD, è necessario aggiungere LearnUpon dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere LearnUpon dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **LearnUpon**, selezionare **LearnUpon** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![LearnUpon nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con LearnUpon in base a un utente test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in LearnUpon.

Per configurare e testare l'accesso Single Sign-On di Azure AD con LearnUpon, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di LearnUpon](#configure-learnupon-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di LearnUpon](#create-learnupon-test-user)** : per avere una controparte di Britta Simon in LearnUpon collegata alla relativa rappresentazione in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare Single Sign-On di Azure AD con LearnUpon, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione [LearnUpon](https://portal.azure.com/) del **portale di Azure** fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di LearnUpon](common/idp-reply.png)

    Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di risposta effettivo. Per ottenere il valore, contattare il [team di supporto client di LearnUpon](https://www.learnupon.com/features/support/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML** individuare il valore di **Identificazione digitale**. Questo valore verrà aggiunto alle impostazioni SAML di LearnUpon.

    ![Collegamento di download del certificato](common/certificateraw.png)

6. Nella sezione **Configura LearnUpon** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-learnupon-single-sign-on"></a>Configurare Single Sign-On per LearnUpon

1. Aprire un'altra istanza del browser e accedere a LearnUpon con un account amministratore.

1. Fare clic sulla scheda **Impostazioni** .

    ![Configure Single Sign-On](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. Fare clic su **Single Sign On - SAML** e quindi fare clic su **General Settings** (Impostazioni generali) per configurare le impostazioni SAML.
   
    ![Configure Single Sign-On](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. Nella sezione **General Settings** (Impostazioni generali), seguire questa procedura:
   
    ![Configure Single Sign-On](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Selezionare **Enabled**.

    b. Per **Version** selezionare **2.0**.

    c. Per **Skip conditions** (Ignorare le condizioni) selezionare **No**.

    d. Nella casella di testo **SAML Token Post param name** (Nome parametro POST di richiesta token SAML) digitare il nome del parametro POST di richiesta all'URL del consumer SAML indicato in precedenza che contiene l'asserzione SAML da verificare e autenticare, ad esempio **SAMLResponse**.

    e. Nella casella di testo **Name Identifier Format** (Formato identificatore nome) digitare il valore che indica la posizione dell'identificatore degli utenti (indirizzo di posta elettronica) nell'asserzione SAML, ad esempio `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.
  
    f. Nella casella di testo **Identify Provider Location** (Identificare il percorso del provider) digitare il valore che indica il percorso a cui verranno indirizzati gli utenti quando fanno clic sull'icona caricata dalla schermata di accesso del portale di Azure.
  
    g. Nella casella di testo **URL disconnessione** incollare il valore di **Sign-Out URL** (URL di disconnessione) copiato dal portale di Azure.

    h. Fare clic su **Manage finger prints**(Gestione impronte digitali) e quindi caricare l'impronta digitale del certificato scaricato.

1. Fare clic su **User Settings**(Impostazioni utente), e seguire questa procedura:

     ![Configure Single Sign-On](./media/learnupon-tutorial/tutorial_learnupon_11.png)  

    a. Nella casella di testo **First Name Identifier Format** (Formato identificatore nome) digitare il valore che indica la posizione del nome degli utenti dell'asserzione SAML, ad esempio `https://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
  
    b. Nella casella di testo **Last Name Identifier Format** (Formato identificatore cognome) digitare il valore che indica la posizione del cognome degli utenti dell'asserzione SAML, ad esempio `https://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`. Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a LearnUpon.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **LearnUpon**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **LearnUpon**.

    ![Collegamento LearnUpon nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-learnupon-test-user"></a>Creare un utente di test di LearnUpon

In questa sezione viene creato un utente di nome Britta Simon in LearnUpon. LearnUpon supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in LearnUpon, ne viene creato uno nuovo dopo l'autenticazione. Per creare un utente manualmente, è necessario contattare il [team di supporto di LearnUpon](https://www.learnupon.com/features/support/).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di LearnUpon nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione LearnUpon per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)