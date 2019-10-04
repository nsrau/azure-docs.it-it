---
title: 'Esercitazione: Integrazione di Azure Active Directory con Sprinklr | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Sprinklr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 9e4025d040783bff1cd85fb46d571e3a89967892
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089652"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Esercitazione: Integrazione di Azure Active Directory con Sprinklr

Questa esercitazione descrive come integrare Sprinklr con Azure Active Directory (Azure AD).
L'integrazione di Sprinklr con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Sprinklr.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Sprinklr con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Sprinklr, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Sprinklr abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Sprinklr supporta l'accesso SSO avviato da **SP**

## <a name="adding-sprinklr-from-the-gallery"></a>Aggiunta di Sprinklr dalla raccolta

Per configurare l'integrazione di Sprinklr in Azure AD, è necessario aggiungere Sprinklr dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Sprinklr dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Sprinklr**, selezionare **Sprinklr** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Sprinklr nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Sprinklr usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Sprinklr.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Sprinklr, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Sprinklr](#configure-sprinklr-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Sprinklr](#create-sprinklr-test-user)** : per avere una controparte di Britta Simon in Sprinklr collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Sprinklr, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Sprinklr** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Sprinklr](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<subdomain>.sprinklr.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il modello seguente: `https://<subdomain>.sprinklr.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Sprinklr](https://www.sprinklr.com/contact-us/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Sprinklr** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-sprinklr-single-sign-on"></a>Configurare l'accesso Single Sign-On di Sprinklr

1. In un'altra finestra del Web browser accedere al sito aziendale di Sprinklr come amministratore.

1. Passare ad **Administration (Amministrazione) \> Settings (Impostazioni)** .

    ![Amministrazione](./media/sprinklr-tutorial/ic782907.png "Amministrazione")

1. Passare a **Manage Partner (Gestisci partner) \> Single Sign (Accesso singolo)** nel riquadro sinistro.

    ![Gestione partner](./media/sprinklr-tutorial/ic782908.png "Gestione partner")

1. Fare clic su **+ Add Single Sign Ons**.

    ![Accessi Single Sign-On](./media/sprinklr-tutorial/ic782909.png "Accessi Single Sign-On")

1. Nella pagina **Single Sign On** eseguire la procedura seguente:

    ![Accessi Single Sign-On](./media/sprinklr-tutorial/ic782910.png "Accessi Single Sign-On")

    a. Nella casella di testo **Name**, digitare un nome per la configurazione, ad esempio: *WAADSSOTest*).

    b. Selezionare **Enabled**.

    c. Selezionare **Use new SSO Certificate**.

    d. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **Certificato provider di identità**.

    e. Incollare il valore di **Identificatore Azure AD**, copiato dal portale di Azure, nella casella di testo **Entity Id** (ID entità).

    f. Incollare il valore di **URL di accesso**, copiato dal portale di Azure, nella casella di testo **Identity Provider Login URL** (URL di accesso provider di identità).

    g. Incollare il valore di **URL di disconnessione**, copiato dal portale di Azure, nella casella di testo **Identity Provider Logout URL** (URL di disconnessione provider di identità).

    h. In **SAML User ID Type** (Tipo ID utente SAML) selezionare **Assertion contains User's sprinklr.com username** (L'asserzione contiene il nome utente sprinklr.com dell'utente).

    i. In **SAML User ID Location** (Percorso ID utente SAML) selezionare **User ID is in the Name Identifier element of the Subject statement** (L'ID utente è nell'elemento identificatore nome dell'istruzione Subject).

    j. Fare clic su **Save**.

    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Sprinklr.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Sprinklr**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Sprinklr**.

    ![Collegamento di Sprinklr nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-sprinklr-test-user"></a>Creare l'utente di test di Sprinklr

1. Accedere al sito aziendale di Sprinklr come amministratore.

1. Passare ad **Administration (Amministrazione) \> Settings (Impostazioni)** .

    ![Amministrazione](./media/sprinklr-tutorial/ic782907.png "Amministrazione")

1. Passare a **Manage Client (Gestisci client) \> Users (Utenti)** dal riquadro di sinistra.

    ![Impostazioni](./media/sprinklr-tutorial/ic782914.png "Impostazioni")

1. Fare clic su **Add User**.

    ![Impostazioni](./media/sprinklr-tutorial/ic782915.png "Impostazioni")

1. Nella finestra di dialogo **Edit User** seguire la procedura seguente:

    ![Modifica degli utenti](./media/sprinklr-tutorial/ic782916.png "Modifica degli utenti")

    a. Nelle caselle di testo **Email** (E-mail), **First name** (Nome) e **Last name** (Cognome) digitare i dati di un account utente di Azure AD di cui eseguire il provisioning.

    b. Selezionare **Password disabled**.

    c. Selezionare **Language** (Lingua).

    d. Selezionare **User Type** (Tipo di utente).

    e. Fare clic su **Update**.

    > [!IMPORTANT]
    > **Password disabled** deve essere selezionata per consentire agli utenti di accedere tramite un provider di identità. 

1. Passare a **Role**ed eseguire la procedura seguente:

    ![Ruoli partner](./media/sprinklr-tutorial/ic782917.png "Ruoli partner")

    a. Nell'elenco **Global** (Globale) selezionare **ALL_Permissions** (TUTTE_autorizzazioni).  

    b. Fare clic su **Update**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento di creazione di account utente di Sprinklr o API fornita da Sprinklr per eseguire il provisioning degli account utente di Azure AD.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Sprinklr nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Sprinklr per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
