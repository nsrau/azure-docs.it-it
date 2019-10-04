---
title: 'Esercitazione: Integrazione di Azure Active Directory con SpaceIQ | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SpaceIQ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5b55ae29-491f-401f-9299-d3a6b64a1b99
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: b3358ad473ede9e8d78a835e8c68e690e5340638
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67090100"
---
# <a name="tutorial-azure-active-directory-integration-with-spaceiq"></a>Esercitazione: Integrazione di Azure Active Directory con SpaceIQ

Questa esercitazione descrive come integrare SpaceIQ con Azure Active Directory (Azure AD).
L'integrazione di SpaceIQ con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a SpaceIQ.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a SpaceIQ con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SpaceIQ, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di SpaceIQ abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SpaceIQ supporta l'accesso SSO avviato da **IDP**

## <a name="adding-spaceiq-from-the-gallery"></a>Aggiunta di SpaceIQ dalla raccolta

Per configurare l'integrazione di SpaceIQ in Azure AD, è necessario aggiungere SpaceIQ dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere SpaceIQ dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **SpaceIQ**, nel pannello dei risultati selezionare **SpaceIQ** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![SpaceIQ nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SpaceIQ usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SpaceIQ.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SpaceIQ, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di SpaceIQ](#configure-spaceiq-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di SpaceIQ](#create-spaceiq-test-user)** : per avere una controparte di Britta Simon in SpaceIQ collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con SpaceIQ, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **SpaceIQ** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** eseguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SpaceIQ](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare l'URL: `https://api.spaceiq.com`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://api.spaceiq.com/saml/<instanceid>/callback`

    > [!NOTE]
    > Aggiornare i valori con l'URL di risposta e l'identificatore effettivi, come illustrato più avanti nell'esercitazione.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura SpaceIQ** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-spaceiq-single-sign-on"></a>Configurare l'accesso Single Sign-On di SpaceIQ

1. Aprire una nuova finestra del browser e quindi accedere all'ambiente di SpaceIQ come amministratore.

1. Una volta connessi, fare clic sul simbolo del puzzle in alto a destra e quindi fare clic su **Integrations** (Integrazioni)

    ![Impostazioni dell'account](./media/spaceiq-tutorial/setting1.png) 

1. In **All PROVISIONING & SSO** (Tutto PROVISIONING E SSO) fare clic sul riquadro **Azure** per aggiungere un'istanza di Azure come IDP.

    ![Icona SAML](./media/spaceiq-tutorial/setting2.png)

1. Nella finestra di dialogo **SSO** seguire questa procedura:

    ![Impostazioni di autenticazione SAML](./media/spaceiq-tutorial/setting3.png)

    a. Nella casella **SAML Issuer URL** (URL autorità di certificazione SAML) incollare il valore di **Identificatore Azure AD** copiato dalla finestra di configurazione dell'applicazione di Azure AD.

    b. Copiare il valore **SAML CallBack Endpoint URL (read-only)** (URL dell'endpoint di callback SAML - sola lettura) e incollarlo nella casella **URL di risposta** del portale di Azure, nella sezione **Configurazione SAML di base**.

    c. Copiare il valore **SAML Audience URI (read-only)** (URI destinatari SAML - sola lettura) e incollarlo nella casella **Identificatore** del portale di Azure, nella sezione **Configurazione SAML di base**.

    d. Aprire il file del certificato scaricato nel Blocco note, copiarne il contenuto e quindi incollarlo nella casella **Certificato X.509**.

    e. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`.  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SpaceIQ.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **SpaceIQ**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **SpaceIQ**.

    ![Collegamento di SpaceIQ nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-spaceiq-test-user"></a>Creare un utente di test di SpaceIQ

In questa sezione viene creato un utente di nome Britta Simon in SpaceIQ. Per aggiungere utenti alla piattaforma SpaceIQ, collaborare con il [team di supporto di SpaceIQ](mailto:eng@spaceiq.com) . Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Facendo clic sul riquadro SpaceIQ nel pannello di accesso si dovrebbe accedere automaticamente all'applicazione SpaceIQ per cui è stato configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

