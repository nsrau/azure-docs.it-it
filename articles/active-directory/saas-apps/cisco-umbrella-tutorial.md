---
title: 'Esercitazione: Integrazione di Azure Active Directory con Cisco Umbrella | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Cisco Umbrella.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 675dca98-f119-4463-8350-d6a45d5601e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a1b0763e33607367939476ca155040295de864c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57837984"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella"></a>Esercitazione: Integrazione di Azure Active Directory con Cisco Umbrella

Questa esercitazione descrive come integrare Cisco Umbrella con Azure Active Directory (Azure AD).
L'integrazione di Cisco Umbrella con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Cisco Umbrella.
* È possibile abilitare gli utenti per l'accesso automatico a Cisco Umbrella (Single Sign-On) con gli account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Cisco Umbrella, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Una sottoscrizione a Cisco Umbrella abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Cisco Umbrella supporta il Single Sign-On avviato da **SP e IDP**

## <a name="adding-cisco-umbrella-from-the-gallery"></a>Aggiunta di Cisco Umbrella dalla raccolta

Per configurare l'integrazione di Cisco Umbrella in Azure AD, è necessario aggiungere Cisco Umbrella dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Cisco Umbrella dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Cisco Umbrella**, selezionare **Cisco Umbrella** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Cisco Umbrella nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con [Nome applicazione] usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in [Nome applicazione].

Per configurare e testare l'accesso Single Sign-On di Azure AD con [Nome applicazione], è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Cisco Umbrella](#configure-cisco-umbrella-single-sign-on)**: per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Cisco Umbrella](#create-cisco-umbrella-test-user)**: per avere una controparte di Britta Simon in Cisco Umbrella collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con [Nome applicazione], seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione [Cisco Umbrella](https://portal.azure.com/) del **portale di Azure** selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** l'utente non deve eseguire alcuna operazione perché l'app è già preintegrata in Azure.

    ![Informazioni su URL e dominio di Cisco Umbrella per l'accesso Single Sign-On](common/both-preintegrated-signon.png)

    a. Per configurare l'applicazione in modalità avviata da **SP**, seguire questa procedura:

    b. Fare clic su **Impostare URL aggiuntivi**.

    c. Nella casella di testo **URL di accesso** digitare l'URL: `https://login.umbrella.com/sso`

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **file XML dei metadati** definito dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura Cisco Umbrella** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-cisco-umbrella-single-sign-on"></a>Configurare Single Sign-On per Cisco Umbrella

1. In un'altra finestra del Web browser accedere al sito aziendale di Cisco Umbrella come amministratore.

2. Dal lato sinistro del menu, fare clic su **Amministratore** e passare a **Autenticazione**, quindi fare clic su **SAML**.

    ![Amministrazione](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_admin.png)

3. Scegliere **Altro** e fare clic su **AVANTI**.

    ![Il menu Altro](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_other.png)

4. Nella pagina **Metadati Cisco Umbrella** fare clic su **AVANTI**.

    ![I metadati](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_metadata.png)

5. Nella scheda **Carica metadati**, se è stato preconfigurato SAML, selezionare l'opzione **Fai clic qui per modificare** e attenersi ai passaggi seguenti.

    ![Il pulsante AVANTI](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_next.png)

6. In **Opzione A: Caricare il file XML**e caricare il file **XML dei metadati di federazione** scaricato dal portale di Azure. Dopo il caricamento dei metadati, i valori seguenti vengono popolati automaticamente. A questo punto, fare clic su **AVANTI**.

    ![Finestra di selezione file](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_choosefile.png)

7. Nella sezione **Convalida configurazione SAML** fare clic su **TESTA LA CONFIGURAZIONE SAML**.

    ![Test](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_test.png)

8. Fare clic su **SAVE**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon\@dominioaziendale.estensione**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Cisco Umbrella.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Cisco Umbrella**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **Cisco Umbrella**.

    ![Collegamento a Cisco Umbrella nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-cisco-umbrella-test-user"></a>Creare l'utente di test di Cisco Umbrella

Per consentire agli utenti di Azure AD di accedere a Cisco Umbrella, è necessario eseguirne il provisioning in Cisco Umbrella.  
Nel caso di Cisco Umbrella, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. In un'altra finestra del Web browser accedere al sito aziendale di Cisco Umbrella come amministratore.

2. Dal lato sinistro del menu, fare clic su **Amministratore** e passare a **Account**.

    ![Account](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_account.png)

3. Nella pagina **Account**, fare clic su **Aggiungi** in alto a destra e attenersi ai passaggi seguenti.

    ![Utente](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_createuser.png)

    a. Nel campo **Nome** immettere il nome, ad esempio **Britta**.

    b. Nel campo **Cognome** immettere il cognome, ad esempio **simon**.

    c. In **Scegli ruolo amministratore con delega** selezionare il proprio ruolo.
  
    d. Nel campo **Email address** (Indirizzo di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **brittasimon\@contoso.com**.

    e. Immettere la password nel campo **Password**.

    f. Reimmettere la password nel campo **Conferma password**.

    g. Fare clic su **Create** (Crea).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Cisco Umbrella nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Cisco Umbrella per cui è stato configurato l'accesso Single Sign-On. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
