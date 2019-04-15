---
title: 'Esercitazione: Integrazione di Azure Active Directory con Syncplicity | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 041f1e9706c7d815dad1a33104e7dd15b2cc3893
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270235"
---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Esercitazione: Integrazione di Azure Active Directory con Syncplicity

Questa esercitazione descrive come integrare Syncplicity con Azure Active Directory (Azure AD).
L'integrazione di Syncplicity con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Syncplicity.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Syncplicity con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Syncplicity, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Syncplicity abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Syncplicity supporta l'accesso SSO avviato da **SP**

## <a name="adding-syncplicity-from-the-gallery"></a>Aggiunta di Syncplicity dalla raccolta

Per configurare l'integrazione di Syncplicity in Azure AD, è necessario aggiungere Syncplicity dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Syncplicity dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Syncplicity**, selezionare **Syncplicity** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Syncplicity nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Syncplicity usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Syncplicity.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Syncplicity, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Syncplicity](#configure-syncplicity-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Syncplicity](#create-syncplicity-test-user)**: per avere una controparte di Britta Simon in Syncplicity collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Syncplicity, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Syncplicity** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Syncplicity](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<companyname>.syncplicity.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Syncplicity](https://www.syncplicity.com/contact-us). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Syncplicity** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-syncplicity-single-sign-on"></a>Configurare l'accesso Single Sign-On di Syncplicity

1. Accedere al tenant **Syncplicity**.

1. Nel menu nella parte superiore fare clic su **admin** (amministrazione), selezionare **settings** (impostazioni) e quindi fare clic su **Custom domain and single sign-on** (Dominio personalizzato e Single Sign-On).

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Nella pagina finestra di dialogo **Single Sign-On (SSO)** eseguire la procedura seguente:

    ![Single Sign-On \(SSO\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. Nella casella di testo **Custom Domain** digitare il nome del dominio.
  
    b. Selezionare **Enabled** (Abilitato) come **Single Sign-On Status** (Stato Single Sign-On).

    c. Nella casella di testo **Entity ID** (ID entità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    d. Nella casella di testo **Sign-in page URL** (URL pagina di accesso) incollare l'**URL di accesso** copiato dal portale di Azure.

    e. Nella casella di testo **Logout page URL** (URL pagina di disconnessione) incollare l'**URL di disconnessione** copiato dal portale di Azure.

    f. In **Identity Provider Certificate** (Certificato provider di identità) fare clic su **Choose file** (Scegli file) e quindi caricare il certificato scaricato dal portale di Azure.

    g. Fare clic su **SAVE CHANGES** (SALVA MODIFICHE).

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

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Syncplicity.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Syncplicity**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Syncplicity**.

    ![Collegamento di Syncplicity nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.
    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-syncplicity-test-user"></a>Creare l'utente di test di Syncplicity

Per essere in grado di effettuare l'accesso, è necessario effettuare il provisioning degli utenti AAD all'applicazione Syncplicity. In questa sezione viene descritto come creare gli account utente AAD in Syncplicity.

**Per eseguire il provisioning di un account utente, eseguire la procedura seguente:**

1. Accedere al tenant **Syncplicity** (ad esempio: `https://company.Syncplicity.com`).

1. Fare clic su **admin** (amministrazione) e selezionare **user accounts** (account utente), quindi fare clic su **ADD A USER** (AGGIUNGI UN UTENTE).

    ![Gestione utenti](./media/syncplicity-tutorial/ic769764.png "Gestione utenti")

1. Digitare gli **indirizzi di posta elettronica** di un account Azure AD di cui effettuare il provisioning, selezionare **User** (Utente) come valore di **Role** (Ruolo) e quindi fare clic su **NEXT** (AVANTI).

    ![Informazioni sull'account](./media/syncplicity-tutorial/ic769765.png "Informazioni sull'account")

    > [!NOTE]
    > Il titolare dell'account AAD riceverà un messaggio di posta elettronica con un collegamento per confermare e attivare l'account.

1. Selezionare un gruppo nell'azienda a cui aggiungere il nuovo utente e quindi fare clic su **NEXT** (Avanti).

    ![Appartenenza al gruppo](./media/syncplicity-tutorial/ic769772.png "Appartenenza al gruppo")

    > [!NOTE]
    > Se non sono elencati gruppi, fare clic su **NEXT** (Avanti).

1. Selezionare le cartelle a cui si vuole applicare il controllo di Syncplicity nel computer dell'utente e quindi fare clic su **NEXT** (Avanti).

    ![Cartelle di Syncplicity](./media/syncplicity-tutorial/ic769773.png "Cartelle di Syncplicity")

> [!NOTE]
> È possibile usare qualsiasi altro strumento di creazione account utente o API fornita da Syncplicity per eseguire il provisioning degli account utente di AAD.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Syncplicity nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Syncplicity per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
