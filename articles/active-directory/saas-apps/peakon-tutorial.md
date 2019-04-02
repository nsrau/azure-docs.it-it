---
title: 'Esercitazione: Integrazione di Azure Active Directory con Peakon | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Peakon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a944c397-ed3f-4d45-b9b2-6d4bcb6b0a09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: bf291f22f523756c868128cbe5595fa56cf7d109
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361677"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Esercitazione: Integrazione di Azure Active Directory con Peakon

Questa esercitazione descrive come integrare Peakon con Azure Active Directory (Azure AD).
L'integrazione di Peakon con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Peakon.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Peakon con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Peakon, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Peakon abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Peakon supporta l'accesso SSO avviato da **SP** e **IDP**

## <a name="adding-peakon-from-the-gallery"></a>Aggiunta di Peakon dalla raccolta

Per configurare l'integrazione di Peakon in Azure AD, è necessario aggiungere Peakon dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Peakon dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Peakon**, selezionare **Peakon** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Peakon nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Peakon usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Peakon.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Peakon, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Peakon](#configure-peakon-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Peakon](#create-peakon-test-user)**: per avere una controparte di Britta Simon in Peakon collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Peakon, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Peakon** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Peakon](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://app.peakon.com/saml/<companyid>/metadata`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://app.peakon.com/saml/<companyid>/assert`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Peakon](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL di accesso** digitare un URL: `https://app.peakon.com/login`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore e l'URL di risposta effettivi, come illustrato più avanti in questa esercitazione. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **Certificato (base)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificateraw.png)

7. Nella sezione **Configura Peakon** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-peakon-single-sign-on"></a>Configurare l'accesso Single Sign-On di Peakon

1. In un'altra finestra del Web browser accedere a Peakon come amministratore.

2. Nella barra dei menu sul lato sinistro della pagina fare clic su **Configuration** (Configurazione) e quindi passare a **Integrations** (Integrazioni).

    ![Configurazione](./media/peakon-tutorial/tutorial_peakon_config.png)

3. Nella pagina **Integrations** (Integrazioni) fare clic su **Single Sign-On**.

    ![Single Sign-On](./media/peakon-tutorial/tutorial_peakon_single.png)

4. Nella sezione **Single Sign-On** fare clic su **Enable** (Abilita).

    ![Abilitazione](./media/peakon-tutorial/tutorial_peakon_enable.png)

5. Nella sezione **Single sign-on for employees using SAML** (Accesso Single Sign-On per i dipendenti con SAML) seguire questa procedura:

    ![SAML](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. Nella casella di testo **SSO Login URL** (URL di accesso SSO) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Nella casella di testo **SSO Logout URL** (URL di disconnessione SSO) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    c. Fare clic su **Choose file** (Scegli file) per caricare il certificato scaricato dal portale di Azure nella casella relativa al certificato.

    d. Fare clic sull'**icona** per copiare il valore di **Entity ID** (ID entità) e incollarlo nella casella di testo **Identificatore** nella sezione **Configurazione SAML di base** del portale di Azure.

    e. Fare clic sull'**icona** per copiare il valore di **Reply URL (ACS)** (URL risposta - ACS) e incollarlo nella casella di testo **URL di risposta**  nella sezione **Configurazione SAML di base** del portale di Azure.

    f. Fare clic su **Save** (Salva).

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

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Peakon.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Peakon**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Peakon**.

    ![Collegamento di Peakon nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-peakon-test-user"></a>Creare l'utente di test di Peakon

Per consentire agli utenti di Azure AD di accedere a Peakon, è necessario effettuarne il provisioning in Peakon.  
Nel caso di Peakon, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Peakon come amministratore.

2. Nella barra dei menu sul lato sinistro della pagina fare clic su **Configuration** (Configurazione) e quindi passare a **Employees** (Dipendenti).

    ![Dipendente](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. In alto a destra nella pagina fare clic su **Add employee** (Aggiungi dipendente).

      ![Aggiungere un dipendente](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. Nella pagina della finestra di dialogo **New employee** (Nuovo dipendente) seguire questa procedura:

     ![Nuovo dipendente](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. Nella casella di testo **Name** (Nome) digitare **Britta** come nome e **simon** come cognome.

    b. Nella casella di testo **Email** (Posta elettronica) digitare l'ID di posta elettronica dell'utente, ad esempio **Brittasimon\@contoso.com**.

    c. Fare clic su **Create employee** (Crea dipendente).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Peakon nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Peakon per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

