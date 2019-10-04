---
title: 'Esercitazione: Integrazione di Azure Active Directory con SensoScientific Wireless Temperature Monitoring System | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SensoScientific Wireless Temperature Monitoring System.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee9a924d-ccde-45b0-ab40-877f82f5dfa2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea2f5e33859852388357526052c39fa432471efb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091276"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Esercitazione: Integrazione di Azure Active Directory con SensoScientific Wireless Temperature Monitoring System

Questa esercitazione descrive come integrare SensoScientific Wireless Temperature Monitoring System con Azure Active Directory (Azure AD).
L'integrazione di SensoScientific Wireless Temperature Monitoring System con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a SensoScientific Wireless Temperature Monitoring System.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a SensoScientific Wireless Temperature Monitoring System con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SensoScientific Wireless Temperature Monitoring System, è necessario quanto segue:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di SensoScientific Wireless Temperature Monitoring System abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SensoScientific Wireless Temperature Monitoring System supporta l'accesso SSO avviato da **IDP**

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>Aggiunta di SensoScientific Wireless Temperature Monitoring System dalla raccolta

Per configurare l'integrazione di SensoScientific Wireless Temperature Monitoring System in Azure AD, è necessario aggiungerlo all'elenco di app SaaS gestite dalla raccolta.

**Per aggiungere SensoScientific Wireless Temperature Monitoring System dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **SensoScientific Wireless Temperature Monitoring System**, selezionare **SensoScientific Wireless Temperature Monitoring System** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![SensoScientific Wireless Temperature Monitoring System nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SensoScientific Wireless Temperature Monitoring System usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SensoScientific Wireless Temperature Monitoring System.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SensoScientific Wireless Temperature Monitoring System, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di SensoScientific Wireless Temperature Monitoring System](#configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di SensoScientific Wireless Temperature Monitoring System](#create-sensoscientific-wireless-temperature-monitoring-system-test-user)** : per avere una controparte di Britta Simon in SensoScientific Wireless Temperature Monitoring System collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con SensoScientific Wireless Temperature Monitoring System, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **SensoScientific Wireless Temperature Monitoring System** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** l'utente non deve eseguire alcuna operazione perché l'app è già preintegrata in Azure.

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SensoScientific Wireless Temperature Monitoring System](common/preintegrated.png)

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura SensoScientific Wireless Temperature Monitoring System** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on"></a>Configurare l'accesso Single Sign-On di SensoScientific Wireless Temperature Monitoring System

1. Accedere all'applicazione SensoScientific Wireless Temperature Monitoring System come amministratore.

1. Nel menu di spostamento in alto fare clic su **Configuration** (Configurazione) e passare a **Configure** (Configura) in **Single Sign On** per aprire le impostazioni dell'accesso Single Sign-On e seguire questa procedura:

    ![Configure Single Sign-On](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png)

    a. Selezionare il **Nome autorità emittente** come Azure AD.

    b. Nella casella di testo **Issuer URL** (URL autorità di certificazione) incollare l'**identificatore Azure AD** copiato dal portale di Azure.

    c. Nella casella di testo **Single Sign-On Service URL** (URL del servizio SSO) incollare l'**URL di accesso** copiato dal portale di Azure.

    d. Nella casella di testo **Single Sign-Out Service URL** (URL del servizio di disconnessione singolo) incollare l'**URL di disconnessione** copiato dal portale di Azure.

    e. Cercare il certificato scaricato dal portale di Azure e caricarlo qui.

    f. Fare clic su **Save**.

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

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SensoScientific Wireless Temperature Monitoring System.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **SensoScientific Wireless Temperature Monitoring System**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **SensoScientific Wireless Temperature Monitoring System**.

    ![Collegamento di SensoScientific Wireless Temperature Monitoring System nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>Creare l'utente di test di SensoScientific Wireless Temperature Monitoring System

Per consentire agli utenti di Azure AD di accedere a SensoScientific Wireless Temperature Monitoring System, è necessario effettuarne il provisioning in SensoScientific Wireless Temperature Monitoring System. Per aggiungere utenti alla piattaforma SensoScientific Wireless Temperature Monitoring System, collaborare con il  [team di supporto di SensoScientific Wireless Temperature Monitoring System](https://www.sensoscientific.com/contact-us/) . Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di SensoScientific Wireless Temperature Monitoring System nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SensoScientific Wireless Temperature Monitoring System per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

