---
title: 'Esercitazione: Integrazione di Azure Active Directory con dmarcian | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e dmarcian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4768c38b2e76bfa8cdff3187c32c03fdaaf57bbf
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56207654"
---
# <a name="tutorial-azure-active-directory-integration-with-dmarcian"></a>Esercitazione: Integrazione di Azure Active Directory con dmarcian

Questa esercitazione descrive come integrare dmarcian con Azure Active Directory (Azure AD).
L'integrazione di dmarcian con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a dmarcian.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a dmarcian con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con dmarcian, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di dmarcian abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* dmarcian supporta l'accesso SSO avviato da **SP** e **IDP**

## <a name="adding-dmarcian-from-the-gallery"></a>Aggiunta di dmarcian dalla raccolta

Per configurare l'integrazione di dmarcian in Azure AD, è necessario aggiungere dmarcian dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere dmarcian dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **dmarcian**, selezionare **dmarcian** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![dmarcian nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con dmarcian usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in dmarcian.

Per configurare e testare l'accesso Single Sign-On di Azure AD con dmarcian, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per dmarcian](#configure-dmarcian-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di dmarcian](#create-dmarcian-test-user)**: per avere una controparte di Britta Simon in dmarcian collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con dmarcian, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **dmarcian** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di dmarcian](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:
    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente:
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di dmarcian](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente:
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, È necessario aggiornare tali valori con i valori reali di identificatore, URL di risposta e URL di accesso. La procedura è descritta più avanti nell'esercitazione. 

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

### <a name="configure-dmarcian-single-sign-on"></a>Configurare l'accesso Single Sign-On per dmarcian

1. In un'altra finestra del Web browser accedere a dmarcian come amministratore della sicurezza.

2. Fare clic su **Profile** (Profilo) in alto a destra e passare a **Preferences** (Preferenze).

    ![Preferenze ](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

3. Scorrere verso il basso e fare clic sulla sezione **Single Sign-On** e quindi fare clic su **Configure** (Configura).

    ![Single ](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

4. Nella sezione **SAML Single Sign-On** (Single Sign-On SAML) impostare **Status** (Stato) su **Enabled** (Abilitato) e seguire questa procedura:

    ![Autenticazione ](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * Nella sezione **Add dmarcian to your Identity Provider** (Aggiungi dmarcian al provider di identità) fare clic su **COPY** (COPIA) per copiare il valore di **Assertion Consumer Service URL** (URL del servizio consumer di asserzione) per l'istanza e incollarlo nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

    * Nella sezione **Add dmarcian to your Identity Provider** (Aggiungi dmarcian al provider di identità) fare clic su **COPY** (COPIA) per copiare il valore di **Entity ID** (ID entità) per l'istanza e incollarlo nella casella di testo **Identificatore** nella sezione **Configurazione SAML di base** del portale di Azure.

    * Nella casella di testo **Identity Provider Metadata** (Metadati provider di identità) della sezione **Set up Authentication** (Configura autenticazione) incollare il valore di **URL dei metadati di federazione dell'app** copiato dal portale di Azure.

    * Nella casella di testo **Attribute Statements** (Dichiarazioni di attributo) della sezione **Set up Authentication** (Configura autenticazione) incollare l'URL `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * Nella sezione **Set up Login URL** (Configura URL di accesso) copiare il valore dell'**URL di accesso** per l'istanza e incollarlo nella casella di testo **URL accesso** nella sezione **Configurazione SAML di base** del portale di Azure.

        > [!Note]
        > È possibile modificare l'**URL di accesso** in base all'organizzazione.

    * Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente di test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a dmarcian.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **dmarcian**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **dmarcian**.

    ![Collegamento di dmarcian nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-dmarcian-test-user"></a>Creare l'utente di test di dmarcian

Per consentire agli utenti di Azure AD di accedere a dmarcian, è necessario effettuarne il provisioning in dmarcian. Nel caso di dmarcian il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a dmarcian come amministratore della sicurezza.

2. Fare clic su **Profile** (Profilo) in alto a destra e passare a **Manage Users** (Gestisci utenti).

    ![Utente ](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. Sul lato destro della sezione **SSO Users** (Utenti SSO) fare clic su **Aggiungi nuovo utente**.

    ![Aggiungi utente ](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. Nella finestra popup **Aggiungi nuovo utente** seguire questa procedura:

    ![Nuovo utente ](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. Nella casella di testo **New User Email** (Posta elettronica nuovo utente) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **brittasimon@contoso.com**.

    b. Se si vogliono assegnare diritti di amministratore all'utente, selezionare **Make User an Admin** (Imposta utente come amministratore).

    c. Fare clic su **Add User** (Aggiungi utente).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di dmarcian nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione dmarcian per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

