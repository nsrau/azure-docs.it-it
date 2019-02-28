---
title: 'Esercitazione: Integrazione di Azure Active Directory con AlertOps | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e AlertOps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3db13ed4-35c2-4b1e-bed8-9b5977061f93
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1874950e733df1cf14fba2f53fdb49d478cb6f8a
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56866046"
---
# <a name="tutorial-azure-active-directory-integration-with-alertops"></a>Esercitazione: Integrazione di Azure Active Directory con AlertOps

Questa esercitazione descrive come integrare AlertOps con Azure Active Directory (Azure AD).
L'integrazione di AlertOps con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere ad AlertOps.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) ad AlertOps con gli account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con AlertOps, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di AlertOps abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* AlertOps supporta l'accesso SSO avviato da **SP e IDP**

## <a name="adding-alertops-from-the-gallery"></a>Aggiunta di AlertOps dalla raccolta

Per configurare l'integrazione di AlertOps in Azure AD, è necessario aggiungere AlertOps dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere AlertOps dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **AlertOps**, selezionare **AlertOps** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![AlertOps nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con AlertOps usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in AlertOps.

Per configurare e testare l'accesso Single Sign-On di Azure AD con AlertOps, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per AlertOps](#configure-alertops-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di AlertOps](#create-alertops-test-user)**: per avere una controparte di Britta Simon in AlertOps collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con AlertOps, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **AlertOps** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di AlertOps](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.alertops.com`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<SUBDOMAIN>.alertops.com/login.aspx`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di AlertOps](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di AlertOps](mailto:support@alertops.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

7. Nella sezione **Set up AlertOps** (Configura AlertOps) copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-alertops-single-sign-on"></a>Configurare l'accesso Single Sign-On per AlertOps

1. In un'altra finestra del browser accedere al sito aziendale di AlertOps come amministratore.

2. Fare clic su **Account settings** (Impostazioni account) dal pannello di spostamento sinistro.

    ![Configurazione di AlertOps](./media/alertops-tutorial/configure1.png)

3. Nella pagina **Subscription Settings** (Impostazioni sottoscrizione) selezionare **SSO** e seguire questa procedura:

    ![Configurazione di AlertOps](./media/alertops-tutorial/configure2.png)

    a. Selezionare la casella di controllo **Use Single Sign-On(SSO)** (Usa Single Sign-On (SSO)).

    b. Nell'elenco a discesa selezionare **Azure Active Directory** come valore di **SSO Provider** (Provider SSO).

    c. Nella casella di testo **Issuer URL** (URL autorità di certificazione) usare il valore dell'identificatore usato nella sezione **Configurazione SAML di base** del portale di Azure.

    d. Nella casella di testo **SAML endpoint URL** (URL endpoint SAML) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    e. Nella casella di testo **SLO endpoint URL** (URL endpoint SLO) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    f. Nell'elenco a discesa selezionare **SHA256** come valore di **SAML Signature Algorithm** (Algoritmo di firma SAML).

    g. Aprire il file del certificato (Base64) scaricato nel blocco note. Copiare il contenuto negli Appunti e quindi incollarlo nella casella Certificato X.509.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

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

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad AlertOps.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **AlertOps**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **AlertOps**.

    ![Collegamento di AlertOps nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-alertops-test-user"></a>Creare l'utente di test di AlertOps

1. In un'altra finestra del browser accedere al sito aziendale di AlertOps come amministratore.

2. Fare clic su **Users** (Utenti) nel pannello di spostamento sinistro.

    ![Configurazione di AlertOps](./media/alertops-tutorial/user1.png)

3. Selezionare **Add User** (Aggiungi utente).

    ![Configurazione di AlertOps](./media/alertops-tutorial/user2.png)

4. Nella finestra di dialogo **Aggiungi utente** seguire questa procedura:

    ![Configurazione di AlertOps](./media/alertops-tutorial/user3.png)

    a. Nella casella di testo **Login User Name** (Nome utente di accesso) immettere il nome utente dell'utente, ad esempio **Brittasimon**.

    b. Nella casella di testo **Official Email** (Posta elettronica ufficiale) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **Brittasimon@contoso.com**.

    c. Nella casella di testo **First Name** (Nome) immettere il nome dell'utente, ad esempio **Britta**.

    d. Nella casella di testo **Last Name** (Cognome) immettere il cognome dell'utente, ad esempio **Simon**.

    e. Nell'elenco a discesa selezionare il valore di **Type** (Tipo) in base alle indicazioni dell'organizzazione.

    f. Nell'elenco a discesa selezionare il valore di **Role** (Ruolo) per l'utente in base alle indicazioni dell'organizzazione.

    g. Selezionare **Aggiungi**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di AlertOps nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione AlertOps per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
